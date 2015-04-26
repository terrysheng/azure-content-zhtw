<properties 
	pageTitle="安裝適用於 Visual Studio 14 CTP2 的 Azure SDK 2.4" 
	description="安裝 Azure SDK 2.4 及 Visual Studio 14 CTP2" 
	services="visual-studio-online" 
	documentationCenter=".net" 
	authors="kempb" 
	manager="douge" 
	editor="tglee"/>

<tags 
	ms.service="visual-studio-online" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/02/2015" 
	ms.author="kempb"/>

# 安裝適用於 Visual Studio "14" CTP 的 Azure SDK 2.4

若要安裝適用於 Visual Studio "14" CTP 的 Azure SDK 2.4 for .NET，請依照下列步驟執行。此程序將安裝以 Visual Studio "14" CTP 進行 Azure 開發所需的 SDK、基本工具和擴充工具，不適用於 Visual Studio 的其他任何版本。

**注意**：Azure SDK 2.4 與 Visual Studio "14" CTP1 不相容。

若要安裝 Azure SDK 2.4 for .NET，請依照下列步驟執行：

1. 安裝最新的 [Visual Studio "14" CTP](http://go.microsoft.com/fwlink/p/?LinkId=400776)。

2. 使用下列清單中的連結，依序安裝 Azure SDK 的每一個元件。選擇下列每一個元件的 x86 或 x64 版本。

       <ul>
        <li>Azure 製作工具：<a href="http://go.microsoft.com/fwlink/p/?LinkId=400892">MicrosoftAzureAuthoringTools-x86.msi</a> 或 <a href="http://go.microsoft.com/fwlink/p/?LinkId=400893">MicrosoftAzureAuthoringTools-x64.msi</a>。</li>
       <li>Azure 計算模擬器：<a href="http://go.microsoft.com/fwlink/p/?LinkId=400894">MicrosoftAzureEmulator-x86.exe</a> 或 <a href="http://go.microsoft.com/fwlink/p/?LinkId=400895">MicrosoftAzureEmulator-x64.exe</a>。</li>
       <li>Azure 用戶端程式庫：<a href="http://go.microsoft.com/fwlink/p/?LinkId=400896">MicrosoftAzureLibsForNet-x86.msi</a> 或 <a href="http://go.microsoft.com/fwlink/p/?LinkId=400897">MicrosoftAzureLibsForNet-x64.msi</a>。</li>
       <li>儲存體模擬器：<a href="http://go.microsoft.com/fwlink/p/?LinkId=400904">MicrosoftAzureStorageEmulator.msi</a>.                            如果收到關於本機 SQL 資料庫的警告，請從<a href="http://go.microsoft.com/fwlink/p/?LinkId=400778">這裡</a>安裝 SQL Server LocalDB 11.0 的 x86 版本，或從<a href="http://go.microsoft.com/fwlink/p/?LinkId=400779">這裡</a>安裝 x64 版本。</li><li>Azure Tools for Visual Studio：<a href="http://go.microsoft.com/fwlink/p/?LinkId=400903">WindowsAzureTools.vs140.exe</a>。</li></ul>

## 已知問題

1. 如果您將 Visual Studio "14" CTP2 安裝在已安裝 Visual Studio 2013 的電腦上，則無法在 Visual Studio "14" CTP2 中啟動行動服務。若要解決此問題，請在行動服務專案中加入下列組件的  參考：

	* packages/Microsoft.Data.OData.5.6.0/lib/net40/Microsoft.Data.OData.dll
	* packages/Microsoft.Data.Edm.5.6.0/lib/net40/Microsoft.Data.Edm.dll

2. Visual Studio "14" CTP2 中無法對 Azure 網站和行動服務進行遠端偵錯。

## 版本資訊

請閱讀 Azure SDK 2.4 的 [版本資訊](http://go.microsoft.com/fwlink/?LinkId=507517)。

<!--HONumber=46--> 
