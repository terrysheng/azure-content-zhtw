<properties pageTitle="Use Mobile Services to upload images to blob storage (Windows Store) | Mobile Services" metaKeywords="" description="Learn how to use Mobile Services to upload images to Azure Blob Storage and access the images from your Windows Store app." metaCanonical="" services="mobile-services,storage" documentationCenter="Mobile" title="Upload images to Azure Storage by using Mobile Services" authors="glenga" solutions="mobile" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# 使用行動服務將影像上傳到 Azure 儲存體

<div class="dev-center-tutorial-selector sublanding"><a href="/zh-tw/documentation/articles/mobile-services-windows-store-dotnet-upload-data-blob-storage" title="Windows 市集 C#" class="current">Windows 市集 C#</a><a href="/zh-tw/documentation/articles/mobile-services-windows-phone-upload-data-blob-storage" title="Windows Phone">Windows Phone</a></div>

<div class="dev-center-tutorial-subselector"><a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-upload-data-blob-storage" title=".NET 後端" >.NET 後端</a> | <a href="/zh-tw/documentation/articles/mobile-services-windows-store-dotnet-upload-data-blob-storage" title="JavaScript 後端" class="current">JavaScript 後端</a></div>

本主題說明如何使用 Azure 行動服務，讓您的應用程式能在 Azure 儲存體中上傳和存放使用者產生的影像。行動服務會使用 SQL Database 儲存資料。不過在 Azure Blob 儲存體服務中，二進位大型物件 (BLOB) 資料的儲存比較有效。

您無法將資料安全上傳至 Blob 儲存體服務所需的認證，安全地散佈給用戶端應用程式。您必須在您的行動服務中儲存這些認證，並將其用於產生共用存取簽章 (SAS)，以便上傳新的影像。行動服務會安全地將 SAS (到期時間很短的認證，在此例中為 5 分鐘) 傳回給用戶端應用程式。應用程式會使用此暫存認證來上傳影像。在此範例中，來自 Blob 服務的下載是公開的。

在此教學課程中，您會在行動服務快速入門應用程式中新增功能，以便使用行動服務所產生的 SAS 來拍照並將影像上傳到 Azure。本教學課程會逐步引導您完成更新行動服務快速入門的基本步驟，以將影像上傳到 Blob 儲存體服務：

1.  [安裝儲存體用戶端程式庫][]
2.  [更新插入指令碼以產生 SAS][]
3.  [更新用戶端應用程式以擷取影像][]
4.  [上傳影像以測試應用程式][]

本教學課程需要下列各項：

-   Microsoft Visual Studio 2012 Express for Windows 8 或更新版本
-   [Azure 儲存體帳戶][]
-   連接到電腦的相機或其他影像擷取裝置。

本教學課程會以行動服務快速入門為基礎。在開始本教學課程之前，您必須首先完成[開始使用行動服務][]。

## <a name="install-storage-client"></a>安裝適用於 Windows 市集應用程式的儲存體用戶端

若要使用 SAS 將影像上傳至 Blob 儲存體，您必須先新增 NuGet 封裝，以安裝適用於 Windows 市集應用程式的儲存體用戶端程式庫。

1.  在 Visual Studio 的 [方案總管] 中以滑鼠右鍵按一下專案名稱，然後選取 [管理 NuGet 封裝]。

2.  在左側窗格中選取 [線上] 類別，搜尋 `WindowsAzure.Storage`，按一下 [Azure 儲存體] 封裝上的 [安裝]，然後接受授權協定。

    ![][]

    這麼做會將 Azure 儲存體服務的用戶端程式庫新增至專案。

接著，您會更新快速入門應用程式以擷取和上傳影像。

## <a name="update-scripts"></a>在管理入口網站中更新已註冊的插入指令碼

[WACOM.INCLUDE [mobile-services-configure-blob-storage][]]

[WACOM.INCLUDE [mobile-services-windows-store-dotnet-upload-to-blob-storage][]]

## <a name="next-steps"> </a>後續步驟

您現在已藉由整合行動服務與 Blob 服務，而能夠安全地上傳影像，請參閱其他後端服務和整合主題：

-   [使用 SendGrid 從行動服務傳送電子郵件][]

   了解如何使用 SendGrid 電子郵件服務將電子郵件功能新增到行動服務本主題示範如何新增伺服器端指令碼，以使用 SendGrid 傳送電子郵件。

-   [在行動服務中排程後端工作][]

   了解如何使用行動服務工作排程器功能，定義可依您定義的排程執行的伺服器指令碼。

-   [行動服務伺服器指令碼參考][]

   參考使用伺服器指令碼來執行伺服器端工作以及與其他 Azure 元件和外部資源整合的主題。

-   [行動服務 .NET 作法概念性參考][]

   深入了解如何搭配使用行動服務與 .NET

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [Windows 市集 C#]: /zh-tw/documentation/articles/mobile-services-windows-store-dotnet-upload-data-blob-storage "Windows 市集 C#"
  [Windows Phone]: /zh-tw/documentation/articles/mobile-services-windows-phone-upload-data-blob-storage "Windows Phone"
  [.NET 後端]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-upload-data-blob-storage ".NET 後端"
  [JavaScript 後端]: /zh-tw/documentation/articles/mobile-services-windows-store-dotnet-upload-data-blob-storage "JavaScript 後端"
  [安裝儲存體用戶端程式庫]: #install-storage-client
  [更新插入指令碼以產生 SAS]: #update-scripts
  [更新用戶端應用程式以擷取影像]: #add-select-images
  [上傳影像以測試應用程式]: #test
  [Azure 儲存體帳戶]: /zh-tw/manage/services/storage/how-to-create-a-storage-account
  [開始使用行動服務]: /zh-tw/documentation/articles/mobile-services-windows-store-get-started
  []: ./media/mobile-services-windows-store-dotnet-upload-data-blob-storage/mobile-add-storage-nuget-package-dotnet.png
  [mobile-services-configure-blob-storage]: ../includes/mobile-services-configure-blob-storage.md
  [mobile-services-windows-store-dotnet-upload-to-blob-storage]: ../includes/mobile-services-windows-store-dotnet-upload-to-blob-storage.md
  [使用 SendGrid 從行動服務傳送電子郵件]: /zh-tw/develop/mobile/tutorials/send-email-with-sendgrid/
  [在行動服務中排程後端工作]: /zh-tw/documentation/articles/mobile-services-schedule-recurring-tasks
  [行動服務伺服器指令碼參考]: http://go.microsoft.com/fwlink/p/?LinkId=262293
  [行動服務 .NET 作法概念性參考]: /zh-tw/develop/mobile/how-to-guides/work-with-net-client-library
