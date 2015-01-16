<properties pageTitle="使用行動服務將影像上傳至 Blob 儲存體 (Windows Phone) | 行動服務" metaKeywords="" description="了解如何使用行動服務將影像上傳至 Azure Blob 儲存體。" metaCanonical="" disqusComments="0" umbracoNaviHide="1" documentationCenter="Mobile" title="Upload images to Azure Storage by using Mobile Services" authors="glenga" writer="glenga" services="mobile-services, storage" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="10/08/2014" ms.author="glenga" />

# 使用行動服務將影像上傳到 Azure 儲存體

[WACOM.INCLUDE [mobile-services-selector-upload-data-blob-storage](../includes/mobile-services-selector-upload-data-blob-storage.md)]

本主題說明如何使用 Azure 行動服務，讓您的應用程式能在 Azure 儲存體中上傳和存放使用者產生的影像。行動服務會使用 SQL Database 儲存資料。不過在 Azure Blob 儲存體服務中，二進位大型物件 (BLOB) 資料的儲存比較有效。 

您無法將資料安全上傳至 Blob 儲存體服務所需的認證，安全地散佈給用戶端應用程式。相反地，您必須將認證儲存在行動服務中，並使用這些認證來產生用來上傳新影像的共用存取簽章 (SAS)。行動服務會安全地將 SAS (到期時間很短的認證，在此例中為 5 分鐘) 傳回給用戶端應用程式。&mdash;應用程式會使用此暫存認證來上傳影像。在此範例中，來自 Blob 服務的下載是公開的。

在此教學課程中，您會在 [GetStartedWithData 範例應用程式專案](/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/)中新增功能，以便使用行動服務所產生的 SAS 來拍照並將影像上傳到 Azure。本教學課程會逐步引導您完成更新簡單 TodoList 應用程式的基本步驟，以將影像上傳到 Blob 儲存體服務：

1. [安裝儲存體用戶端程式庫]
2. [更新用戶端應用程式以擷取影像]
3. [在行動服務專案中安裝儲存體用戶端]
4. [更新資料模型中的 TodoItem 定義]
5. [更新資料表控制器以產生 SAS]
6. [上傳影像以測試應用程式]

本教學課程需要下列各項：

+ Microsoft Visual Studio 2013 或更新版本
+ [Windows Phone SDK 8.0] 或更高版本
+ 已安裝適用於 Microsoft Visual Studio 的 Nuget Package Manager
+ [Azure 儲存體帳戶][如何建立儲存體帳戶]
+ 完成教學課程[將行動服務新增至現有的應用程式](/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/)  

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-blob-storage](../includes/mobile-services-dotnet-backend-configure-blob-storage.md)]

##<a name="install-storage-client"></a>安裝適用於 Windows 市集應用程式的儲存體用戶端

若要能夠使用 SAS 將影像從您的應用程式上傳至 Blob 儲存體，您必須先新增 NuGet 封裝，以安裝適用於 Windows 市集應用程式的儲存體用戶端程式庫。

1. 在 Visual Studio 的 [**方案總管**] 中，以滑鼠右鍵按一下用戶端應用程式專案，然後選取 [**管理 NuGet 封裝**]。

2. 在左窗格中，選取 [**線上**] 類別，選取 [**包括發行前版本**]，搜尋 **WindowsAzure.Storage-Preview**]，按一下 [**Azure Storage**] 封裝上的 [**安裝**]，然後接受授權合約。 

  	![][2]

  	這麼做會將 Azure 儲存體服務的用戶端程式庫新增至專案。

[WACOM.INCLUDE [mobile-services-windows-phone-upload-to-blob-storage](../includes/mobile-services-windows-phone-upload-to-blob-storage.md)]
 
<!-- Anchors. -->
[安裝儲存體用戶端程式庫]: #install-storage-client
[更新用戶端應用程式以擷取影像]: #add-select-images
[在行動服務專案中安裝儲存體用戶端]: #storage-client-server
[更新資料模型中的 TodoItem 定義]: #update-data-model
[更新資料表控制器以產生 SAS]: #update-scripts
[上傳影像以測試應用程式]: #test
[後續步驟]:#next-steps

<!-- Images. -->
[2]: ./media/mobile-services-dotnet-backend-windows-phone-upload-data-blob-storage/mobile-add-storage-nuget-package-dotnet.png

<!-- URLs. -->
[使用 SendGrid 從行動服務傳送電子郵件]: /zh-tw/documentation/articles/store-sendgrid-mobile-services-send-email-scripts/
[在行動服務中排程後端工作]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-schedule-recurring-tasks
[開始使用行動服務]: /zh-tw/documentation/articles/mobile-services-windows-phone-get-started

[Azure 管理入口網站]: https://manage.windowsazure.com/
[如何建立儲存體帳戶]: /zh-tw/documentation/articles/storage-create-storage-account/
[適用於市集應用程式的 Azure 儲存體用戶端程式庫]: http://go.microsoft.com/fwlink/p/?LinkId=276866 
[行動服務 .NET 作法概念性參考]: /zh-tw/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library
[Windows Phone SDK 8.0]: http://www.microsoft.com/zh-tw/download/details.aspx?id=35471


