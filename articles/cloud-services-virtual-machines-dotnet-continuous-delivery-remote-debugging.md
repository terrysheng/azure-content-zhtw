<properties 
	pageTitle="對連續傳遞啟用遠端偵錯" 
	description="了解在使用連續傳遞來部署至 Azure 時如何啟用遠端偵錯" 
	services="cloud-services" 
	documentationCenter=".net" 
	authors="kempb" 
	manager="douge" 
	editor="tglee"/>

<tags 
	ms.service="cloud-services" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-multiple" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/18/2015" 
	ms.author="kempb"/>
# 使用連續傳遞來發行至 Azure 時啟用遠端偵錯

當您使用[連續傳遞](http://azure.microsoft.com/ documentation/articles/cloud-services-dotnet-continuous-delivery/)來發行至 Azure 時，您可以依照下列步驟在 Azure 中啟用遠端偵錯。

本主題內容：

[對雲端服務啟用遠端偵錯](#cloudservice)

[對虛擬機器啟用遠端偵錯](#virtualmachine)

<h2> <a name="cloudservice"></a>對雲端服務啟用遠端偵錯</h2>

1. 在組建代理程式上，設定 Azure 的初始環境，如 [Azure 的命令列建置](http://msdn.microsoft.com/library/hh535755.aspx)所述。
2. 因為封裝需要有遠端偵錯執行階段 (msvsmon.exe)，請安裝 [Remote Tools for Visual Studio 2013](http://www.microsoft.com/zh-tw/download/details.aspx?id=40781) (如果是使用 Visual Studio 2012，則安裝 [Remote Tools for Visual Studio 2012 Update 4](http://www.microsoft.com/zh-tw/download/details.aspx?id=38184))。或者，您也可以從已安裝 Visual Studio 的系統中複製遠端偵錯二進位檔案。
3. 建立憑證，如[建立 Azure 的服務憑證](http://msdn.microsoft.com/library/azure/gg432987.aspx)所述。保留 .pfx 和 RDP 憑證指紋，並將憑證上傳至目標雲端服務。
4. 在 MSBuild 命令列中使用下列選項，指定啟用遠端偵測來建置和封裝(根據您的系統和專案檔案來更新路徑)。

	/TARGET:PUBLISH /PROPERTY:Configuration=Debug;EnableRemoteDebugger=true;VSX64RemoteDebuggerPath="C:\Remote Debugger\x64\\";RemoteDebuggerConnectorCertificateThumbprint="56D7D1B25B472268E332F7FC0C87286458BFB6B2";RemoteDebuggerConnectorVersion="2.4" "C:\Users\yourusername\Documents\visual studio 2013\Projects\WindowsAzure1\WindowsAzure1.sln"

5. 使用前一個步驟中產生的封裝和 .cscfg 檔案來發行至目標雲端服務。
6. 將憑證 (.pfx 檔案) 匯入至已安裝 Visual Studio with Azure SDK 2.4 的電腦。

<h2> <a name="virtualmachine"></a>對虛擬機器啟用遠端偵錯</h2>

1. 建立 Azure 虛擬機器。請參閱[建立執行 Windows Server 的虛擬機器](http://azure.microsoft.com/ documentation/articles/virtual-machines-windows-tutorial/)或[在 Visual Studio 中建立 Azure 虛擬機器](http://msdn.microsoft.com/library/azure/dn569263.aspx)。
2. 在 [Azure 入口網站頁面](http://go.microsoft.com/fwlink/p/?LinkID=269851)，檢視虛擬機器儀表板來查看虛擬機器的「RDP 憑證指紋」。這用在延伸組態中的 ServerThumbprint 值。
3. 建立用戶端憑證，如[建立 Azure 的服務憑證](http://msdn.microsoft.com/library/azure/gg432987.aspx)所述 (保留 .pfx 和 RDP 憑證指紋)。
4. 從 Microsoft 下載中心安裝 [Azure Powershell](http://go.microsoft.com/?linkid=9811175&clcid=0x409) (0.7.4 版或更新版本)。
5. 執行下列指令碼來啟用 RemoteDebug 延伸模組。換成您自己的個人資料，例如訂閱名稱、服務名稱和指紋。(注意：此指令碼設定用於 Visual Studio 2013。如果您使用 Visual Studio 2012，請在 ReferenceName 和 ExtensionName 使用 "RemoteDebugVS2013")。

	<pre>
    Add-AzureAccount
    
    Select-AzureSubscription "My Microsoft Subscription"
    
    $vm = Get-AzureVM -ServiceName "mytestvm1" -Name "mytestvm1"
    
    $endpoints = @(
    ,@{Name="RDConnVS2013"; PublicPort=30400; PrivatePort=30398}
    ,@{Name="RDFwdrVS2013"; PublicPort=31400; PrivatePort=31398}
    )
    
    foreach($endpoint in $endpoints)
    {
    Add-AzureEndpoint -VM $vm -Name $endpoint.Name -Protocol tcp -PublicPort $endpoint.PublicPort -LocalPort $endpoint.PrivatePort
    }
    
    $referenceName = "Microsoft.VisualStudio.WindowsAzure.RemoteDebug.RemoteDebugVS2013"
    $publisher = "Microsoft.VisualStudio.WindowsAzure.RemoteDebug"
    $extensionName = "RemoteDebugVS2013"
    $version = "1.*"
    $publicConfiguration = "<PublicConfig><Connector.Enabled>true</Connector.Enabled><ClientThumbprint>56D7D1B25B472268E332F7FC0C87286458BFB6B2</ClientThumbprint><ServerThumbprint>E7DCB00CB916C468CC3228261D6E4EE45C8ED3C6</ServerThumbprint><ConnectorPort>30398</ConnectorPort><ForwarderPort>31398</ForwarderPort></PublicConfig>"
    
    $vm | Set-AzureVMExtension `
    -ReferenceName $referenceName `
    -Publisher $publisher `
    -ExtensionName $extensionName `
    -Version $version `
    -PublicConfiguration $publicConfiguration
    
    foreach($extension in $vm.VM.ResourceExtensionReferences)
    {   
    if(($extension.ReferenceName -eq $referenceName) `
    -and ($extension.Publisher -eq $publisher) `
    -and ($extension.Name -eq $extensionName) `
    -and ($extension.Version -eq $version))
    {
    $extension.ResourceExtensionParameterValues[0].Key = 'config.txt'
    break
    }
    }
    
    $vm | Update-AzureVM 
	</pre>
    
6. 將憑證 (.pfx) 匯入至已安裝 Visual Studio with Azure SDK for .NET 2.4 的電腦。
<!--HONumber=45--> 
