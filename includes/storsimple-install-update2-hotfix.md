<!--author=alkohli last changed: 01/12/15-->

#### 下載 Hofix

請執行下列步驟來從 Microsoft Update Catalog 下載軟體更新。

1. 啟動 Internet Explorer 並瀏覽至 [http://catalog.update.microsoft.com/v7/site/Home.aspx](http://catalog.update.microsoft.com/v7/site/Home.aspx)。

2. 如果您是第一次使用者，系統會提示您安裝 Microsoft Update Catalog。按一下 [Install]。
    
   	![安裝目錄](./media/storsimple-install-update2-hotfix/HCS_InstallCatalog-include.png)

3. 您會看到目錄搜尋畫面。在搜尋方塊中輸入 **3121901**，然後按一下 [搜尋]。

    ![搜尋目錄](./media/storsimple-install-update2-hotfix/HCS_SearchCatalog1-include.png)

4. 您將會看到**適用於 StorSimple 8000 系列的累積軟體套件組合更新 2.0**。按一下 [新增]。更新將會加入到購物籃中。

5. 按一下 [**檢視購物籃**]。
 
6. 按一下 [下載]。指定或**瀏覽**至您想要儲存下載項目的本機位置。更新將會下載到所選位置的資料夾 (名稱與更新相同) 中。資料夾也可以複製到裝置可連線的網路共用位置。
       
	> [AZURE.NOTE]
	> 
	> - 您也需要下載 **LSI 驅動程式更新** (適用於 StorSimple 8000 系列的 SAS 控制器更新 2.0 - KB3121900)、**Storport 更新** (適用於 Windows Server 2012 R2 x64 版本的 Hotfix - KB3080728)、**Spaceport 更新** (適用於 Windows Server 2012 R2 x64 版本的 Hotfix - KB3090322)，以及**磁碟韌體更新** (適用於 StorSimple 8000 系列的累積磁碟韌體更新 2.0 - KB3121899)，並複製到相同的共用資料夾。
	> - Hotfix 必須可同時從兩個控制器偵測任何來自對等控制器的潛在錯誤訊息。

#### 安裝及驗證一般模式 Hotfix

執行下列步驟來安裝及驗證一般 Hotfix。

1. 若要安裝 Hotfix，請存取 StorSimple 裝置序列主控台上的 Windows PowerShell 介面。請依照[使用 PuTTy 連接到序列主控台](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console)中的詳細指示執行作業。在命令提示字元中，按 **Enter** 鍵。

4. 選取 [**選項 1**] 以使用完整的存取權限登入裝置。

5. 若要安裝 Hotfix，請在命令提示字元中輸入：

    `Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`

    在上述命令的共用路徑中使用 IP 而不是 DNS。只有當您要存取已驗證的共用位置時，才會用到認證參數。

	我們建議您使用認證參數來存取共用項目。即使是開放給「所有人」的共用項目，通常也不會開放給未經驗證的使用者。

    下方顯示一項範例輸出。

        ````
        Controller0>Start-HcsHotfix -Path \\10.100.100.100\share
        \hcsmdssoftwareupdate.exe -Credential contoso\John
      
        Confirm

        This operation starts the hotfix installation and could reboot one or
        both of the controllers. If the device is serving I/Os, these will not 
        be disrupted. Are you sure you want to continue?
        [Y] Yes [N] No [?] Help (default is "Y"): Y

        ````
 
6. 當系統提示您確認 Hotfix 安裝時，請輸入 **Y**。

7. 使用 `Get-HcsUpdateStatus` Cmdlet 來監視更新。

    下列範例輸出顯示更新進行中。更新正在進行中時，`RunInprogress` 會是 `True`。

        ````
        Controller0>Get-HcsUpdateStatus
        RunInprogress       : True
        LastHotfixTimestamp : 12/21/2015 10:36:13 PM
        LastUpdateTimestamp : 12/21/2015 10:35:25 PM
        Controller0Events   :
        Controller1Events   : 
        ````
 
     下列範例輸出指出更新已完成。更新完成時，`RunInProgress` 將會是 `False`。

        ````
        Controller1>Get-HcsUpdateStatus

        RunInprogress       : False
        LastHotfixTimestamp : 12/21/2015 10:59:13 PM
        LastUpdateTimestamp : 12/21/2015 10:35:25 PM
        Controller0Events   :
        Controller1Events   :

        ````
		

	> [AZURE.NOTE]有時在更新進行期間，Cmdlet 會回報 `False`。若要確保此 Hotfix 已完成，請等待幾分鐘的時間、重新執行此命令並確認 `RunInProgress` 為 `False`。如果的確為 False 的話，則 Hotfix 已完成。
	
8. 軟體更新完成之後，請重複步驟 3-5 來使用 `CisMdsAgentUpdateBundle.exe` 安裝及監視 SaaS 代理程式和 MDS 代理程式。確定您是先安裝 `HcsMdsSoftwareUpdate.exe`，然後再安裝 `CisMdsAgentUpdateBundle.exe`。

9. 驗證系統軟體版本。輸入：

    `Get-HcsSystem`

    您應該會看見下列版本：

    - HcsSoftwareVersion：6.3.9600.17673
    - CisAgentVersion：1.0.9150.0
    - MdsAgentVersion：30.0.4698.13 
    
	如果在套用更新後版本號碼並未變更，則表示此 Hotfix 未成功套用。若您看到這種情況，請連絡 [Microsoft 支援](storsimple-contact-microsoft-support.md)以取得進一步的協助。
    
9. 重複步驟 3-5 來安裝及監視剩餘的一般 Hotfix。

	- 使用 `HcsLsiUpdate.exe` 封裝 (KB3121900) 的 LSI 驅動程式。
	- 使用 `Storport-KB3080728-x64.msu` 封裝 (KB3080728) 的 Storport 修正程式。
	- 使用 `spaceport-KB3090322-x64.msu` 封裝 (KB3090322) 的 Spaceport 修正程式。

#### 安裝及驗證維護模式 Hotfix

使用 `DiskFirmwarePackage.exe` 封裝 (KB3121899) 來安裝磁碟韌體更新。這些是干擾性更新，且需要約 30 分鐘來完成。您可以藉由連接至裝置序列主控台，以選擇在預計的維護視窗中安裝這些更新。若要安裝磁碟韌體更新，請依照下面的指示執行。

1. 使裝置處於維護模式。請注意，連線至處於維護模式的裝置時，您不應該使用 Windows PowerShell 遠端執行功能。透過裝置序列主控台連線時，您需要在裝置控制器上執行此 Cmdlet。輸入：
		
	`Enter-HcsMaintenanceMode`

	下方顯示一項範例輸出。

		Controller0>Enter-HcsMaintenanceMode
		Checking device state...

		In maintenance mode, your device will not service IOs and will be disconnected from the Microsoft Azure StorSimple Manager service. Entering maintenance mode will end the current session and reboot both controllers, which takes a few minutes to complete. Are you sure you want to enter maintenance mode?
		[Y] Yes [N] No (Default is "Y"): Y

		-----------------------MAINTENANCE MODE------------------------
		Microsoft Azure StorSimple Appliance Model 8100
		Name: Update2-8100-SHG0997879L76YD
		Software Version: 6.3.9600.17664
		Copyright (C) 2014 Microsoft Corporation. All rights reserved.
		You are connected to Controller0 - Passive
		---------------------------------------------------------------

		Serial Console Menu
		[1] Log in with full access
		[2] Log into peer controller with full access
		[3] Connect with limited access
		[4] Change language
		Please enter your choice>

	兩個控制器都會重新開機。重新開機完成之後，兩個控制器都會處於維護模式。

3. 若要安裝磁碟韌體更新，請輸入：

	`Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`

	下方顯示一項範例輸出。

        Controller1>Start-HcsHotfix -Path \\10.100.100.100\share\DiskFirmwarePackage.exe -Credential contoso\john
		Enter Password:
		WARNING: In maintenance mode, hotfixes should be installed on each controller sequentially. After the hotfix is installed on this controller, install it on the peer controller.
		Confirm
		This operation starts a hotfix installation and could reboot one or both of the controllers. By installing new updates you agree to, and accept any additional terms associated with, the new functionality listed in the release notes (https://go.microsoft.com/fwLink/?LinkID=613790). Are you sure you want to continue?
		[Y] Yes [N] No (Default is "Y"): Y
		WARNING: Installation is currently in progress. This operation can take several minutes to complete.
	

1.  使用 `Get-HcsUpdateStatus` 命令監視安裝進度。當 `RunInProgress` 變成 `False` 時，代表更新完成。
 
2.  安裝完成之後，維護模式 Hotfix 安裝所在的控制器將會重新開機。以具有完整存取權的選項 1 登入，並驗證磁碟韌體版本。輸入：
	
	`Get-HcsFirmwareVersion`
  
	預期的磁碟韌體版本為：

	`XMGG, XGEG, KZ50, F6C2, VR08`

	下方顯示一項範例輸出。


        -----------------------MAINTENANCE MODE------------------------
    	Microsoft Azure StorSimple Appliance Model 8100
    	Name: Update2-8100-SHG0997879L76YD
    	Software Version: 6.3.9600.17664
    	Copyright (C) 2014 Microsoft Corporation. All rights reserved.
    	You are connected to Controller1
    	---------------------------------------------------------------
    	
    	Controller1>Get-HcsFirmwareVersion
    	
    	
    	Controller0 : TalladegaFirmware
    	  ActiveBIOS:0.45.0006
    	  BackupBIOS:0.45.0008
    	  MainCPLD:17.0.0005
    	  ActiveBMCRoot:2.0.000E
    	  BackupBMCRoot:2.0.000E
    	  BMCBoot:2.0.0001
    	  LsiFirmware:19.00.00.00
    	  LsiBios:07.37.00.00
    	  Battery1Firmware:06.29
    	  Battery2Firmware:06.29
    	  DomFirmware:X231600
    	  CanisterFirmware:3.5.0.32
    	  CanisterBootloader:5.03
    	  CanisterConfigCRC:0xD1B030A4
    	  CanisterVPDStructure:0x06
    	  CanisterGEMCPLD:0x17
    	  CanisterVPDCRC:0xEE3504B4
    	  MidplaneVPDStructure:0x0C
    	  MidplaneVPDCRC:0xA6BD4F64
    	  MidplaneCPLD:0x10
    	  PCM1Firmware:1.00|1.05
    	  PCM1VPDStructure:0x05
    	  PCM1VPDCRC:0x41BEF99C
    	  PCM2Firmware:1.00|1.05
    	  PCM2VPDStructure:0x05
    	  PCM2VPDCRC:0x41BEF99C
    	
    	  DisksFirmware
    	  SEAGATE:ST400FM0073:XGEG
    	  SEAGATE:ST400FM0073:XGEG
    	  SEAGATE:ST400FM0073:XGEG
    	  SEAGATE:ST400FM0073:XGEG
    	  SEAGATE:ST4000NM0023:XMGG
    	  SEAGATE:ST4000NM0023:XMGG
    	  SEAGATE:ST4000NM0023:XMGG
    	  SEAGATE:ST4000NM0023:XMGG
    	  SEAGATE:ST4000NM0023:XMGG
    	  SEAGATE:ST4000NM0023:XMGG
    	  SEAGATE:ST4000NM0023:XMGG
    	  SEAGATE:ST4000NM0023:XMGG

	 在第二個控制器上執行 `Get-HcsFirmwareVersion` 命令來驗證軟體版本已經更新。然後您就可以結束維護模式。針對每個裝置控制器輸入以下命令：

    `Exit-HcsMaintenanceMode`
     
1. 控制器將會在您結束維護模式時重新開機。在磁碟韌體更新已成功套用且裝置已結束維護模式後，返回 Azure 傳統入口網站。維護模式更新在 24 小時內不會更新至入口網站。






 
 

<!---HONumber=AcomDC_0114_2016-->