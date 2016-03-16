<properties
   pageTitle="設定具名的驗證認證 | Microsoft Azure"
   description="了解如何提供 Visual Studio 可用來向 Azure 驗證要求的認證，以便將應用程式從 Visual Studio 發佈至 Azure，或用來監視現有的雲端服務。"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="12/18/2015"
   ms.author="tarcher" />

# 設定具名的驗證認證

若要將應用程式從 Visual Studio 發佈至 Azure，或若要監視現有的雲端服務，您必須提供 Visual Studio 可用來向 Azure 驗證要求的認證。您可以從 Visual Studio 中的幾個位置登入並提供這些認證。例如，從 [伺服器總管] 中，您可以開啟 [Azure] 節點的捷徑功能表，並選擇 [連接到 Azure]。登入時，您可以在 Visual Studio 中取得與 Azure 帳戶相關聯的訂用帳戶資訊，而且接下來您無需執行任何動作。

Azure Tools 也支援像以前一樣使用訂用帳戶檔案 (.publishsettings 檔案) 提供認證。本主題將說明此方法，Azure SDK 2.2 中仍支援此方法。

向 Azure 驗證需要下列項目：

- 訂用帳戶識別碼

- 有效的 X.509 v3 憑證

>[AZURE.NOTE]X.509 v3 憑證的金鑰長度必須至少為 2048 位元。Azure 將會拒絕任何不符合此需求或無效的憑證。

Visual Studio 會使用您的訂用帳戶識別碼連同憑證資料做為認證。包含憑證公開金鑰的訂用帳戶檔案 (.publishsettings 檔案) 會參考適當的認證。訂用帳戶檔案可以包含多個訂用帳戶的認證。

您可以在 [新增/編輯訂用帳戶] 對話方塊中編輯訂用帳戶資訊，本主題稍後會有說明。

如果您想要自行建立憑證，您可以參考[建立及上傳 Azure 的管理憑證](https://msdn.microsoft.com/library/windowsazure/gg551722.aspx)中的指示，然後手動將憑證上傳至管理入口網站。

>[AZURE.NOTE]Visual Studio 為管理雲端服務所需的這些認證，與針對 Azure 儲存體服務驗證要求所需的認證是不同的。

## 在 Visual Studio 中修改或匯出驗證認證

您也可以在 [新增訂用帳戶] 對話方塊中設定、修改或匯出您的驗證認證，此對話方塊會在您執行下列動作時出現：

- 在 [伺服器總管] 中，開啟 **Azure** 節點的捷徑功能表，選擇 [管理訂用帳戶]，再選擇 [憑證] 索引標籤，然後選擇 [新增] 或 [編輯] 按鈕。

- 當您從 [發佈 Azure 應用程式精靈] 發佈 Azure 雲端服務時，選擇 [選擇訂用帳戶] 清單中的 [管理]，再選擇 [憑證] 索引標籤，然後選擇 [新增] 或 [編輯] 按鈕。

下列程序假設 [新增訂用帳戶] 對話方塊已開啟。

### 在 Visual Studio 中設定驗證認證

1. 在驗證清單的 [選取現有憑證] 中選擇憑證。

1. 選擇 [複製完整路徑] 按鈕。憑證 (.cer 檔案) 的路徑便會複製到剪貼簿。

    >[AZURE.IMPORTANT]若要從 Visual Studio 發佈 Azure 應用程式，您必須將此憑證上傳至管理入口網站。

1. 將憑證上傳至 Azure 管理入口網站：

    1. 選擇 Azure 入口網站連結。

         [Azure 管理入口網站](http://go.microsoft.com/fwlink/?LinkID=213885) 隨即開啟。

    1. 使用您的 Microsoft 帳戶登入 Azure 管理入口網站，然後選擇 [雲端服務] 按鈕。

    1. 選擇您感興趣的雲端服務。

        該服務的頁面便會隨即開啟。

    1. 在 [憑證] 索引標籤上，選擇 [上傳] 按鈕。

    1. 貼上您剛才建立的 .cer 檔案的完整路徑，然後輸入您指定的密碼。

<!---HONumber=AcomDC_1223_2015-->