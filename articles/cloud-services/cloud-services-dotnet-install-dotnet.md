<properties
   pageTitle="在雲端服務角色上安裝 .NET"
   description="本文說明如何在雲端服務 Web 和背景工作角色上手動安裝 .NET Framework。"
   services="cloud-services"
   documentationCenter=".net"
   authors="sbtron"
   manager="timlt"
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/17/2016"
   ms.author="saurabh"/>

# 在雲端服務角色上安裝 .NET 

本文說明如何在雲端服務 Web 和背景工作角色上安裝 .NET framework。您可以依照下列步驟，在 Azure 客體 OS 系列 4 上安裝 .NET Famework 4.6.1。如需最新的客體 OS 版本相關資訊，請參閱 [Azure 客體 OS 版本與 SDK 相容性比較表](cloud-services-guestos-update-matrix.md)。

在 Web 和背景工作角色上安裝 .NET 的程序會涉及在雲端專案中加入 .NET 安裝程式套件，並在角色的啟動工作過程中啟動安裝程式。

## 將 .NET 安裝程式加入至專案
- 針對您想要安裝的 .NET framework 下載 Web 安裝程式
	- [.NET 4.6.1 Web 安裝程式](http://go.microsoft.com/fwlink/?LinkId=671729)
- 若是 Web 角色
  1. 在 [**方案總管**] 中，於雲端服務專案中的 [**角色**] 下，以滑鼠右鍵按一下您的角色，然後依序選取 [**新增 > 新增資料夾**]。建立名為 *bin* 的資料夾
  2. 在 **bin** 資料夾上按一下滑鼠右鍵，並依序選取 [**新增 > 現有項目**]。選取 .NET 安裝程式，並將它加入至 bin 資料夾。
- 若是背景工作角色
  1. 以滑鼠右鍵按一下您的角色，然後依序選取 [**新增 > 現有項目**]。選取 .NET 安裝程式，並將它加入至角色。 

以此方式新增至角色內容資料夾的檔案會自動加入至雲端服務封裝，並部署至虛擬機器上的一致位置。為雲端服務中的所有 Web 和背景工作角色重複此程序，以便所有角色都有安裝程式副本。

> [AZURE.NOTE] 即使您的應用程式以 .NET 4.6 為目標，您還是應該在雲端服務角色上安裝.NET 4.6.1。Azure 客體 OS 包含更新 [3098779](https://support.microsoft.com/kb/3098779) 和 [3097997](https://support.microsoft.com/kb/3097997)。在這些更新上安裝 .NET 4.6，可能會讓您在執行 .NET 應用程式時發生問題，因此您應該要直接安裝 .NET 4.6.1，而不是 .NET 4.6。如需詳細資訊，請參閱 [KB 3118750](https://support.microsoft.com/kb/3118750)。

![安裝程式檔案的角色內容][1]

## 定義角色的啟動工作
啟動工作可讓您在啟動角色之前執行作業。將 .NET Framework 做為啟動工作的一部分安裝，可確保在執行任何應用程式程式碼之前已安裝好 Framework。如需有關啟動工作的詳細資訊，請參閱：[在 Azure 中執行啟動工作](cloud-services-startup-tasks.md)。

1. 將下列內容加入所有角色中 **WebRole** 或 **WorkerRole** 節點底下的 *ServiceDefinition.csdef* 檔案：
	
	```xml
	<LocalResources>
      <LocalStorage name="NETFXInstall" sizeInMB="1024" cleanOnRoleRecycle="false" />
    </LocalResources>    
	<Startup>
      <Task commandLine="install.cmd" executionContext="elevated" taskType="simple">
        <Environment>
          <Variable name="PathToNETFXInstall">
            <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='NETFXInstall']/@path" />
          </Variable>
          <Variable name="ComputeEmulatorRunning">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
        </Environment>
      </Task>
    </Startup>
	```

	上述組態會使用系統管理員權限來執行主控台命令 *install.cmd*，以便安裝 .NET Framework。此設定也會建立名稱為 *NETFXInstall* 的 LocalStorage。啟動指令碼會將暫存資料夾設定為使用此本機儲存資源，如此即可從此資源下載並及安裝 .NET Framework 安裝程式。請務必將此資源的大小設定為至少 1024 MB，以確保架構的安裝正確。如需有關啟動工作的詳細資訊，請參閱：[常見的雲端服務啟動工作](cloud-services-startup-tasks-common.md)。

2. 建立 **install.cmd**，並在該角色上按一下右鍵，依序選取 [新增] > [現有項目...]，將其新增至所有角色。因此，所有角色現在應該都有 .NET 安裝程式檔案，以及 install.cmd 檔案。
	
	![所有檔案的角色內容][2]

	> [AZURE.NOTE] 使用如 [記事本] 之類的簡單文字編輯器來建立這個檔案。如果使用 Visual Studio 來建立文字檔案，然後將它重新命名為 '.cmd'，則此檔案可能仍會包含 UTF-8 位元順序標記，並在執行第一行的指令碼時出現錯誤。如果您要使用 Visual Studio 來建立檔案，請在檔案的第一行保留加入 REM (備註)，以便在執行時將它忽略。

3. 將下列指令碼加入 **install.cmd** 檔案：

	```
	REM Set the value of netfx to install appropriate .NET Framework. 
	REM ***** To install .NET 4.5.2 set the variable netfx to "NDP452" *****
	REM ***** To install .NET 4.6 set the variable netfx to "NDP46" *****
	REM ***** To install .NET 4.6.1 set the variable netfx to "NDP461" *****
	set netfx="NDP461"
	
	
	REM ***** Set script start timestamp *****
	set timehour=%time:~0,2%
	set timestamp=%date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2%
	set "log=install.cmd started %timestamp%."
	
	REM ***** Exit script if running in Emulator *****
	if %ComputeEmulatorRunning%=="true" goto exit
	
	REM ***** Needed to correctly install .NET 4.6.1, otherwise you may see an out of disk space error *****
	set TMP=%PathToNETFXInstall%
	set TEMP=%PathToNETFXInstall%
	
	REM ***** Setup .NET filenames and registry keys *****
	if %netfx%=="NDP461" goto NDP461
	if %netfx%=="NDP46" goto NDP46
	    set "netfxinstallfile=NDP452-KB2901954-Web.exe"
	    set netfxregkey="0x5cbf5"
	    goto logtimestamp
	
	:NDP46
	set "netfxinstallfile=NDP46-KB3045560-Web.exe"
	set netfxregkey="0x60051"
	goto logtimestamp
	
	:NDP461
	set "netfxinstallfile=NDP461-KB3102438-Web.exe"
	set netfxregkey="0x6041f"
	
	:logtimestamp
	REM ***** Setup LogFile with timestamp *****
	md "%PathToNETFXInstall%\log"
	set startuptasklog="%PathToNETFXInstall%log\startuptasklog-%timestamp%.txt"
	set netfxinstallerlog="%PathToNETFXInstall%log\NetFXInstallerLog-%timestamp%"
	echo %log% >> %startuptasklog%
	echo Logfile generated at: %startuptasklog% >> %startuptasklog%
	echo TMP set to: %TMP% >> %startuptasklog%
	echo TEMP set to: %TEMP% >> %startuptasklog%
	
	REM ***** Check if .NET is installed *****
	echo Checking if .NET (%netfx%) is installed >> %startuptasklog%
	reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\NET Framework Setup\NDP\v4\Full" /v Release | Find %netfxregkey%
	if %ERRORLEVEL%== 0 goto installed
	
	REM ***** Installing .NET *****
	echo Installing .NET with commandline: start /wait %~dp0%netfxinstallfile% /q /serialdownload /log %netfxinstallerlog%  /chainingpackage "CloudService Startup Task" >> %startuptasklog%
	start /wait %~dp0%netfxinstallfile% /q /serialdownload /log %netfxinstallerlog% /chainingpackage "CloudService Startup Task" >> %startuptasklog% 2>>&1
	if %ERRORLEVEL%== 0 goto installed
		echo .NET installer exited with code %ERRORLEVEL% >> %startuptasklog%	
		if %ERRORLEVEL%== 3010 goto restart
		if %ERRORLEVEL%== 1641 goto restart
		echo .NET (%netfx%) install failed with Error Code %ERRORLEVEL%. Further logs can be found in %netfxinstallerlog% >> %startuptasklog%
	
	:restart
	echo Restarting to complete .NET (%netfx%) installation >> %startuptasklog%
	goto end
	
	:installed
	echo .NET (%netfx%) is installed >> %startuptasklog%
	
	:end
	echo install.cmd completed: %date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2% >> %startuptasklog%
	
	:exit
	EXIT /B 0
	```
		
	安裝指令碼會透過查詢登錄來檢查指定的 .NET framework 版本是否已在電腦上安裝。如果未安裝該 .NET 版本，則 .Net Web 安裝程式便會啟動。為協助疑難排解任何問題，該指令碼會將所有活動記錄到名為 *startuptasklog-(currentdatetime).txt* (儲存於 *InstallLogs* 本機儲存體) 的檔案。

	> [AZURE.NOTE] 指令碼仍會示範如何為版本連貫性來安裝 .NET 4.5.2 或 .NET 4.6 。您不必手動安裝 .NET 4.5.2，因為 Azure 客體 OS 上已經有 .NET 4.5.2。而由於 [KB 3118750](https://support.microsoft.com/kb/3118750)，您應該要直接安裝 .NET 4.6.1，而不是 .NET 4.6。
      

## 設定診斷來將啟動工作記錄檔傳輸到 Blob 儲存體 
如要簡化任何安裝問題的疑難排解步驟，您可以設定 Azure 診斷，來將啟動工作指令碼或 .NET 安裝程式所產生的所有記錄檔傳輸到 Blob 儲存體。透過這種方法，您可以從 Blob 儲存體直接下載記錄檔，而無需遠端桌面到角色，即可檢視記錄檔。

若要設定診斷，請開啟 *diagnostics.wadcfgx*，並在**目錄**節點下加入下列內容：

```xml 
<DataSources>
 <DirectoryConfiguration containerName="netfx-install">
  <LocalResource name="NETFXInstall" relativePath="log"/>
 </DirectoryConfiguration>
</DataSources>
```

這樣會將 Azure 診斷設定為將 *NETFXInstall* 資源下 *log* 目錄中的所有檔案，傳輸到 *netfx-install* Blob 容器中的診斷儲存體帳戶。

## 部署您的服務 
部署服務時，啟動工作將會執行並安裝 .NET Framework (如果尚未安裝)。安裝 Framework 時，您的角色將會處於忙碌狀態，而且甚至可能會重新啟動 (如果 Framework 安裝有此要求)。

## 其他資源

- [安裝 .NET Framework][]
- [作法：判斷安裝的 .NET Framework 版本][]
- [疑難排解 .NET Framework 安裝][]

[作法：判斷安裝的 .NET Framework 版本]: https://msdn.microsoft.com/library/hh925568.aspx
[安裝 .NET Framework]: https://msdn.microsoft.com/library/5a4x27ek.aspx
[疑難排解 .NET Framework 安裝]: https://msdn.microsoft.com/library/hh925569.aspx

<!--Image references-->
[1]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithinstallerfiles.png
[2]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithallfiles.png

 

<!---HONumber=AcomDC_0218_2016-->