<properties 
	pageTitle="在 5 分鐘內開始使用 Azure Blob、資料表和佇列" 
	description="了解如何利用 Azure 快速入門和 Visual Studio，快速掌握 Microsoft Azure Blob、資料表和佇列。" 
	services="storage" 
	documentationCenter=".net" 
	authors="Selcin" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="hero-article" 
	ms.date="02/18/2015" 
	ms.author="selcint"/>

# 在 5 分鐘內開始使用 Azure Blob、資料表和佇列 

本教學課程示範如何在 Visual Studio 中開發簡單的 Azure 應用程式，針對 **Azure 儲存體 Blob**、**資料表**和**佇列**快速進行程式設計。 

本教學課程包含快速掌握 Azure 儲存體的兩個主要案例：

- 在 Azure 儲存體模擬器上執行第一個 Azure 儲存體應用程式
- 在 Azure 儲存體服務上執行第一個 Azure 儲存體應用程式

如果您想要在一頭栽進程式碼前了解 Azure 儲存體，請參閱[後續步驟][]。

## 在 Azure 儲存體模擬器上執行第一個 Azure 儲存體應用程式

本節示範如何藉由開發可存取 [Azure 儲存體模擬器](https://msdn.microsoft.com/library/azure/hh403989.aspx)的範例應用程式，針對 **Azure 儲存體 Blob**、**資料表**和**佇列**進行程式設計。Microsoft Azure 儲存體模擬器提供了模擬 Azure Blob、佇列和資料表服務的本機環境，以供進行開發。使用儲存體模擬器，您可以針對儲存體服務在本機測試您的應用程式，而不會產生任何費用。

若要完成此區段，請務必先執行下列的必要工作：

1. 若要編譯及建置應用程式，您必須先在電腦上安裝 [Visual Studio](http://www.visualstudio.com/visual-studio-homepage-vs.aspx)。如果您未安裝 Visual Studio，則可以在安裝 [Azure SDK 2.5 for Visual Studio 2013](http://go.microsoft.com/fwlink/?linkid=324322&clcid=0x409) 或更新版本時安裝 Visual Studio Express for Web。 
2. 確定您已在電腦上安裝 [Azure SDK 2.5 for Visual Studio 2013](http://go.microsoft.com/fwlink/?linkid=324322&clcid=0x409) 或更新版本，因為其中含 Azure 快速入門範例專案和 [Azure Storage Client Library for .NET](https://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx)。  
3. 檢查您是否已在電腦上安裝 [.NET Framework 4.5](http://www.microsoft.com/download/details.aspx?id=30653)，因為 Azure 快速入門範例專案需要該元件。如果不確定您的電腦中安裝哪個版本的 .NET Framework，請參閱[作法：判斷安裝的 .NET Framework 版本](https://msdn.microsoft.com/vstudio/hh925568.aspx)。或者，按 [**開始**] 按鈕或 Windows 鍵，輸入 [**控制台**]。然後，按一下 [**程式**] > [**程式和功能**]。請查看 .NET Framework 4.5 是否列在所有已安裝的程式中。

現在，讓我們在 Visual Studio 中使用其中一個 Azure 快速入門範例專案，建立簡單的 Azure 儲存體應用程式。本教學課程著重於 **Azure Blob 儲存體**、**Azure 資料表儲存體**和 **Azure 儲存體佇列**範例專案。下列指示適用於每個範例專案，除非您在步驟 3.a 中選擇不同的範本：

1. 按 [**開始**] 按鈕或 Windows 鍵，輸入 Visual Studio 2013 或 VS Express 2013 for Web。按一下要啟動的程式。
2. 從 [檔案]**** 功能表，按一下 [新增專案]****。
3. 在 [**新增專案**] 對話方塊中，按一下 [**已安裝**] > [**範本**] > [**Visual C#**] > [**雲端**] > [**快速入門**] > [**資料服務**]。
	- 3.a.選擇下列其中一個範本：Azure Blob 儲存體、Azure 資料表儲存體或 Azure 儲存體佇列。 
	- 3.b.確定已選取 [**.NET Framework 4.5**] 作為目標架構。	
	- 3.c.根據您選擇的範本來命名應用程式，例如 **DataBlobStorage**、**DataTableStorage** 或 **DataStorageQueue**。按一下 [確定]****。這應會建立新的 Visual Studio 方案。請參閱下列螢幕擷取畫面作為範例：
	
	![Azure QuickStarts][Image1]

我們鼓勵您檢閱原始程式碼，以了解如何針對 Azure 儲存體進行程式設計，然後再執行應用程式。若要檢閱程式碼，請在 Visual Studio 中選取 [**檢視**] 功能表上的 [**方案總管**]。然後，按兩下 Program.cs 檔案。 

現在，使用隨著 Azure SDK 安裝的 [Azure 儲存體模擬器](https://msdn.microsoft.com/library/azure/hh403989.aspx)，執行範例應用程式：

1.	啟動 Azure 儲存體模擬器：按 [**開始**] 按鈕或 Windows 鍵，輸入 Azure 儲存體模擬器進行搜尋。從應用程式清單中進行選取即可啟動。
2.	在 Visual Studio 中，按一下 [**建置**] 功能表上的 [**建置方案**]。 
3.	在 [**偵錯**] 功能表上，按 **F11** 逐步執行方案，或按 **F5** 執行方案。

## 在 Azure 儲存體服務上執行第一個 Azure 儲存體應用程式
本節示範如何藉由開發可存取 [Azure 儲存體服務](http://azure.microsoft.com/documentation/services/storage/)的範例應用程式，針對 **Azure 儲存體 Blob**、**資料表**和**佇列**進行程式設計。

若要完成此區段，請務必先執行下列的必要工作：

1. 若要編譯及建置應用程式，您必須先在電腦上安裝 [Visual Studio](http://www.visualstudio.com/visual-studio-homepage-vs.aspx)。如果您未安裝 Visual Studio，則可以在安裝 [Azure SDK 2.5 for Visual Studio 2013](http://go.microsoft.com/fwlink/?linkid=324322&clcid=0x409) 或更新版本時安裝 Visual Studio Express for Web。 
2. 確定您已在電腦上安裝 [Azure SDK 2.5 for Visual Studio 2013](http://go.microsoft.com/fwlink/?linkid=324322&clcid=0x409) 或更新版本，因為其中含 Azure 快速入門範例專案和 [Azure Storage Client Library for .NET](https://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx)。  
3. 檢查您是否已在電腦上安裝 [.NET Framework 4.5](http://www.microsoft.com/download/details.aspx?id=30653)，因為 Azure 快速入門範例專案需要該元件。如果不確定您的電腦中安裝哪個版本的 .NET Framework，請參閱[作法：判斷安裝的 .NET Framework 版本](https://msdn.microsoft.com/vstudio/hh925568.aspx)。或者，按 [**開始**] 按鈕或 Windows 鍵，輸入 [**控制台**]。然後，按一下 [**程式**] > [**程式和功能**]。請查看 .NET Framework 4.5 是否列在所有已安裝的程式中。
4.	取得 Azure 訂用帳戶 (如果您尚未擁有) 並建立 [**標準儲存體**] 帳戶：
	- 若要取得 Azure 訂用帳戶，請參閱[免費試用版](http://azure.microsoft.com/pricing/free-trial/)、[購買選項](http://azure.microsoft.com/pricing/purchase-options/)和[會員優惠](http://azure.microsoft.com/pricing/member-offers/) (適用於 MSDN、Microsoft Partner Network、BizSpark 和其他 Microsoft 方案的成員)。
	- 若要在 Azure 中建立 [**標準儲存體**] 帳戶，請參閱[如何建立、管理或刪除儲存體帳戶](storage-create-storage-account.md)。**注意︰**在 Azure 中儲存體帳戶分為兩種類型：標準儲存體帳戶和進階儲存體帳戶。標準儲存體帳戶可供存取 Azure Blob、資料表和佇列儲存體。進階儲存體帳戶目前只能在 Azure 虛擬機器使用的磁碟上儲存資料。如需詳細資訊，請參閱[進階儲存體：Azure 虛擬機器工作負載適用的高效能儲存體](storage-premium-storage-preview-portal.md)。

現在，讓我們在 Visual Studio 中使用其中一個 Azure 快速入門範例專案，建立簡單的 Azure 儲存體應用程式。本教學課程著重於 **Azure Blob 儲存體**、**Azure 資料表儲存體**和 **Azure 儲存體佇列**範例專案。下列指示適用於每個範例專案，除非您在步驟 3.a 中選擇不同的範本：

1. 按 [**開始**] 按鈕或 Windows 鍵，輸入 Visual Studio 2013 或 VS Express 2013 for Web。按一下要啟動的程式。
2. 從 [檔案]**** 功能表，按一下 [新增專案]****。
3. 在 [**新增專案**] 對話方塊中，按一下 [**已安裝**] > [**範本**] > [**Visual C#**] > [**雲端**] > [**快速入門**] > [**資料服務**]。
	- 3.a.選擇下列其中一個範本：Azure Blob 儲存體、Azure 資料表儲存體或 Azure 儲存體佇列。 
	- 3.b.確定已選取 [**.NET Framework 4.5**] 作為目標架構。
	- 3.c.根據您選擇的範本來命名應用程式，例如 **DataBlobStorage**、**DataTableStorage** 或 **DataStorageQueue**。按一下 [確定]****。這應會建立新的 Visual Studio 方案。 

我們鼓勵您檢閱原始程式碼，以了解如何針對 Azure 儲存體進行程式設計，然後再執行應用程式。若要檢閱程式碼，請在 Visual Studio 中選取 [**檢視**] 功能表上的 [**方案總管**]。然後，按兩下 Program.cs 檔案。 

現在，執行範例應用程式：

1.	在 Visual Studio 中，選取 [**檢視**] 功能表上的 [**方案總管**]。按兩下 App.config 檔案並註解化 Azure SDK 儲存體模擬器的連接字串： 

	`<!--<add key="StorageConnectionString" value = "UseDevelopmentStorage=true;"/>-->`

2.	取消註解 Azure 儲存體服務的連接字串，並提供 App.config 檔案中的儲存體帳戶名稱和存取金鑰：
	`<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]"` 

	若要尋找儲存體帳戶名稱和存取金鑰，請參閱[什麼是儲存體帳戶](storage-whatis-account.md)。 

3.	提供 App.config 檔案中的儲存體帳戶名稱和存取金鑰之後，在 [**檔案**] 功能表上，按一下 [**全部儲存**] 以儲存所有的專案檔案。 
4.	在 [**建置**] 功能表上，按一下 [**建置方案**]。 
5.	在 [**偵錯**] 功能表上，按 **F11** 逐步執行方案，或按 **F5** 執行方案。


## 後續步驟
在本教學課程中，您已了解如何針對 Azure Blob 儲存體、Azure 資料表儲存體和 Azure 儲存體佇列進行程式設計。 

如果您想要深入了解相關資訊，請遵循下列連結：

* [Microsoft Azure 儲存體簡介](storage-introduction.md)
* [如何使用 .NET 的 Blob 儲存體](storage-dotnet-how-to-use-blobs.md)
* [如何使用 .NET 的資料表儲存體](storage-dotnet-how-to-use-tables.md)
* [如何使用 .NET 的佇列儲存體](storage-dotnet-how-to-use-queues.md)
* [Azure 儲存體文件](http://azure.microsoft.com/documentation/services/storage/)
* [Azure 儲存體 MSDN 參考](http://msdn.microsoft.com/library/azure/gg433040.aspx)
* [Azure 儲存體用戶端程式庫](https://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx)
* [Azure 儲存體 REST API](https://msdn.microsoft.com/library/azure/dd179355.aspx)

[Image1]: ./media/storage-getting-started-guide/QuickStart.png


<!--HONumber=47-->
