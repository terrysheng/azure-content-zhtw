<properties 
   pageTitle="使用 Visual Studio 的加入已連接服務加入 Azure 儲存體 | Microsoft Azure"
   description="使用 Visual Studio 的加入已連接服務對話方塊加入 Azure 儲存體"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="tlee" />
<tags 
   ms.service="visual-studio-online"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="mobile"
   ms.date="08/12/2015"
   ms.author="tarcher" />

# 使用 Visual Studio 的已連接服務加入 Azure 儲存體

## 概觀

透過 Visual Studio 2015，您可以使用 [加入已連接服務] 對話方塊，將任何 C# 雲端服務、.NET 後端行動服務、ASP.NET 網站或服務、ASP.NET 5 服務或 Azure WebJob 服務連接至 Azure 儲存體。已連接服務功能會加入所有必要的參考和連接程式碼，並且適當地修改組態檔。對話方塊也會帶您前往說明後續步驟的文件，以啟動 Blob 儲存體、佇列和資料表。

## 支援的專案類型

您可以使用 [已連接服務] 對話方塊來連接到 Azure 儲存體中的下列專案類型。

- ASP.NET Web 專案

- ASP.NET 5 專案

- .NET 雲端服務 Web 角色和背景工作角色專案

- .NET 行動服務專案

- Azure WebJob 專案


## 使用已連接服務對話方塊連接到 Azure 儲存體

1. 確定您具有 Azure 帳戶。如果您沒有 Azure 帳戶，您可以註冊[免費試用](http://go.microsoft.com/fwlink/?LinkId=518146)。一旦您有 Azure 帳戶，即可建立儲存體帳戶、建立行動服務並設定 Azure Active Directory。

1. 在 Visual Studio 中開啟您的專案，開啟 [方案總管] 中 [參考] 節點的內容功能表，然後選擇 [加入已連接服務]。

    ![加入已連接服務](./media/vs-azure-tools-connected-services-storage/IC796702.png)

1. 在 [加入已連接服務] 對話方塊中，選擇 [Azure 儲存體]，然後選擇 [設定] 按鈕。如果您尚未登入 Azure，系統可能會提示您這麼做。

    ![加入已連接服務對話方塊 - 儲存體](./media/vs-azure-tools-connected-services-storage/IC796703.png)

1. 在 [Azure 儲存體] 對話方塊方塊中，選擇現有的儲存體帳戶，然後按一下 [新增]。

    如果您需要建立新的儲存體帳戶，請移至下一個步驟。否則，跳到步驟 6。

    ![Azure 儲存體對話方塊](./media/vs-azure-tools-connected-services-storage/IC796704.png)

1. 若要建立新的儲存體帳戶：

    1. 選擇 [Azure 儲存體] 對話方塊底部的 [建立新的儲存體帳戶] 按鈕。

    1. 填寫 [建立儲存體帳戶] 對話方塊，然後選擇 [建立] 按鈕。
    
        ![Azure 儲存體對話方塊](./media/vs-azure-tools-connected-services-storage/create-storage-account.png)

        當您回到 [Azure 儲存體] 對話方塊時，新的儲存體會出現在清單中。

    1. 選取清單中的新儲存體，然後按一下 [新增]。

1. 儲存體的已連接服務會出現在 WebJob 專案的 [服務參考] 節點之下。

    ![Web 工作專案中的 Azure 儲存體](./media/vs-azure-tools-connected-services-storage/IC796705.png)

1. 檢閱所顯示的 [開始使用] 頁面並了解您的專案修改方式。每當您加入已連接服務時，您的瀏覽器就會出現 [開始使用] 頁面。您可以檢閱建議的後續步驟和程式碼範例，或切換到 [發生什麼情形] 頁面以查看哪些參考已加入至您的專案，以及您的程式碼和組態檔的修改方式。

## 您的專案修改方式

當您完成對話方塊時，Visual Studio 會加入參考並修改某些組態檔。特定變更視專案類型而定。

 - 若為 ASP.NET 專案，請參閱[發生什麼情形 – ASP.NET 專案](http://go.microsoft.com/fwlink/p/?LinkId=513126)。 
 - 若為 ASP.NET 5 專案，請參閱[發生什麼情形 – ASP.NET 5 專案](http://go.microsoft.com/fwlink/p/?LinkId=513124)。 
 - 若為雲端服務專案 (Web 角色和背景工作角色)，請參閱[發生什麼情形 – 雲端服務專案](http://go.microsoft.com/fwlink/p/?LinkId=516965)。 
 - 若為 WebJob 專案，請參閱[發生什麼情形 – WebJob 專案](vs-storage-webjobs-what-happened/)。

## 後續步驟

1. 使用 [開始使用] 程式碼範例做為指南，建立您要的儲存體類型，然後開始撰寫程式碼以存取您的儲存體帳戶！

1. 提出問題並取得協助
     - [MSDN 論壇︰Azure 儲存體](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata)

     - [Azure 儲存體團隊部落格](http://blogs.msdn.com/b/windowsazurestorage/)

     - [azure.microsoft.com 上的儲存體](http://azure.microsoft.com/services/storage)

     - [azure.microsoft.com 上的儲存體文件](http://azure.microsoft.com/documentation/services/storage/)

<!---HONumber=Nov15_HO3-->