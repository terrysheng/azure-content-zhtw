<properties linkid="dotnet-visual-studio-2014" urlDisplayName="Visual Studio 14 CTP2" pageTitle="Installing the Azure SDK 2.4 for Visual Studio 14 CTP2" metaKeywords="Visual Studio, Azure SDK" description="Install Azure SDK 2.4 and Visual Studio 14 CTP2" metaCanonical="" services="" documentationCenter="" title="Installing Azure SDK 2.4 for Visual Studio 14 CTP2" authors="ghogen" solutions="" manager="" editor="" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="ghogen" />

## 安裝適用於 Visual Studio "14" CTP 的 Azure SDK 2.4

若要安裝適用於 Visual Studio "14" CTP 的 Azure SDK 2.4 for .NET，請依照下列步驟執行。此程序將安裝以 Visual Studio "14" CTP 進行 Azure 開發所需的 SDK、基本工具和擴充工具，不適用於 Visual Studio 的其他任何版本。

**注意**：Azure SDK 2.4 與 Visual Studio "14" CTP1 不相容。

若要安裝 Azure SDK 2.4 for .NET，請依照下列步驟執行：

1.  安裝最新的 [Visual Studio "14" CTP][Visual Studio "14" CTP]。

2.  使用下列清單中的連結，依序安裝 Azure SDK 的每一個元件。選擇下列每一個元件的 x86 或 x64 版本。

    -   Azure 製作工作：[WindowsAzureAuthoringTools-x86.msi][WindowsAzureAuthoringTools-x86.msi] 或 [WindowsAzureAuthoringTools-x64.msi][WindowsAzureAuthoringTools-x64.msi]。
    -   Azure 電腦模擬器：[WindowsAzureEmulator-x86.exe][WindowsAzureEmulator-x86.exe] 或 [WindowsAzureEmulator-x64.exe][WindowsAzureEmulator-x64.exe]。
    -   Azure 用戶端程式庫：[WindowsAzureLibsForNet-x86.msi][WindowsAzureLibsForNet-x86.msi] 或 [WindowsAzureLibsForNet-x64.msi][WindowsAzureLibsForNet-x64.msi]。
    -   儲存體模擬器。[WindowsAzureStorageEmulator.msi][WindowsAzureStorageEmulator.msi]。如果收到關於本機 SQL 資料庫的警告，請從[這裡][這裡]安裝 SQL Server LocalDB 11.0 的 x86 版本，或從[這裡][1]安裝 x64 版本。
    -   Azure Tools for Visual Studio：[WindowsAzureTools.vs140.exe][WindowsAzureTools.vs140.exe]。

    </p>

## 已知問題

1.  如果您將 Visual Studio "14" CTP2 安裝在已安裝 Visual Studio 2013 的電腦上，則無法在 Visual Studio "14" CTP2 中啟動行動服務。若要解決此問題，請在行動服務專案中加入下列組件的參考：

    -   packages/Microsoft.Data.OData.5.6.0/lib/net40/Microsoft.Data.OData.dll
    -   packages/Microsoft.Data.Edm.5.6.0/lib/net40/Microsoft.Data.Edm.dll

2.  Visual Studio "14" CTP2 中無法對 Azure 網站和行動服務進行遠端偵錯。

## 版本資訊

請閱讀 Azure SDK 2.4 的[版本資訊][版本資訊]。

  [Visual Studio "14" CTP]: http://go.microsoft.com/fwlink/p/?LinkId=400776
  [WindowsAzureAuthoringTools-x86.msi]: http://go.microsoft.com/fwlink/p/?LinkId=400892
  [WindowsAzureAuthoringTools-x64.msi]: http://go.microsoft.com/fwlink/p/?LinkId=400893
  [WindowsAzureEmulator-x86.exe]: http://go.microsoft.com/fwlink/p/?LinkId=400894
  [WindowsAzureEmulator-x64.exe]: http://go.microsoft.com/fwlink/p/?LinkId=400895
  [WindowsAzureLibsForNet-x86.msi]: http://go.microsoft.com/fwlink/p/?LinkId=400896
  [WindowsAzureLibsForNet-x64.msi]: http://go.microsoft.com/fwlink/p/?LinkId=400897
  [WindowsAzureStorageEmulator.msi]: http://go.microsoft.com/fwlink/p/?LinkId=400904
  [這裡]: http://go.microsoft.com/fwlink/p/?LinkId=400778
  [1]: http://go.microsoft.com/fwlink/p/?LinkId=400779
  [WindowsAzureTools.vs140.exe]: http://go.microsoft.com/fwlink/p/?LinkId=400903
  [版本資訊]: http://go.microsoft.com/fwlink/?LinkId=507517
