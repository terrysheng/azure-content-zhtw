<properties 
	pageTitle="遠端進行 Azure 應用程式服務 API 應用程式的偵錯" 
	description="使用 Visual Studio，遠端進行 Azure 應用程式服務 API 應用程式的偵錯。" 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="bradygaster" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/19/2015" 
	ms.author="bradyg;tarcher"/>

# 遠端進行 Azure 應用程式服務 API 應用程式的偵錯 

## 概觀

Visual Studio 的遠端偵錯功能已經擴充，在 Azure 應用程式服務中納入 API 應用程式的支援。這表示您可以使用熟悉的偵錯工具，查看您的程式碼在 Azure 中即時執行。本主題示範如何使用 Visual Studio 的 **API 應用程式用戶端**，產生可呼叫已部署 API 應用程式的用戶端程式碼。然後您會同時偵錯用戶端應用程式和 API 應用程式，而 API 應用程式會在雲端即時執行 。

本教學課程是一系列教學課程中的最後一個：

1. 在[建立 API 應用程式](app-service-dotnet-create-api-app.md)中，您建立了 API 應用程式專案。 
* 在[部署 API 應用程式](app-service-dotnet-deploy-api-app.md)中，您已將 API 應用程式部署到您的 Azure 訂用。
* 在本教學課程中，您可以使用 Visual Studio，在程式碼於 Azure 中執行時進行遠端偵錯。

## 產生 API 應用程式用戶端 

Visual Studio 中的 API 應用程式工具可讓您輕鬆地產生 C# 程式碼，而該程式碼可從桌面、存放區和行動應用程式呼叫 Azure API 應用程式。 

在 Visual Studio 中，開啟含有[第一個](app-service-dotnet-create-api-app.md)教學課程中的 API 應用程式的方案。以滑鼠右鍵按一下此方案，然後選取 [**新增**] > [**新增專案**]。

![Add a new project](./media/app-service-dotnet-remotely-debug-api-app/01-add-new-project-v3.png)

選取 [**Windows 桌面**] 類別和 [**主控台應用程式**] 專案範本。

![Add a new project](./media/app-service-dotnet-remotely-debug-api-app/02-contact-list-console-project-v3.png)

以滑鼠右鍵按一下主控台應用程式專案，然後選取 [**新增**] > [**Azure API 應用程式用戶端**]。 

![Add a new Client](./media/app-service-dotnet-remotely-debug-api-app/03-add-azure-api-client-v3.png)
	
在對話方塊中，選取 [**下載**] 選項。從下拉式清單中，選取您先前建立的 API 應用程式。按一下 [**確定**]。 

![Generation Screen](./media/app-service-dotnet-remotely-debug-api-app/04-select-the-api-v3.png)

此精靈會下載 API 中繼資料檔案，並產生可供叫用 API 應用程式的具型別介面。

![Generation Happening](./media/app-service-dotnet-remotely-debug-api-app/05-metadata-downloading-v3.png)

程式碼產生完成之後，您會在 [方案總管] 中看到採用 API 應用程式名稱的新資料夾。此資料夾包含實作用戶端和資料模型的程式碼。 

![Generation Complete](./media/app-service-dotnet-remotely-debug-api-app/06-code-gen-output-v3.png)

從專案根目錄開啟 **Program.cs** 檔案，並使用下列程式碼取代 [**主要**] 方法： 

	static void Main(string[] args)
    {
        var client = new ContactsList();

        // Send GET request.
        var contacts = client.Contacts.Get();
        foreach (var c in contacts)
        {
            Console.WriteLine("{0}: {1} {2}",
                c.Id, c.Name, c.EmailAddress);
        }

        // Send POST request.
		client.Contacts.Post(new Models.Contact
	    {
	        EmailAddress = "lkahn@contoso.com",
	        Name = "Loretta Kahn",
	        Id = 4
	    });

        Console.WriteLine("Finished");
        Console.ReadLine();
    }

從 [**檢視**] 功能表選取 [**伺服器總管**]。在 [伺服器總管] 視窗中，展開 [**Azure**] > [**應用程式服務**] 節點。尋找您在部署 API 應用程式時所建立的資源群組。以滑鼠右鍵按一下 API 應用程式的節點，然後選取 [**附加偵錯工具**]。 

![Attaching debugger](./media/app-service-dotnet-remotely-debug-api-app/08-attach-debugger-v3.png)

遠端偵錯工具會嘗試連線。在某些情況下，您可能需要重試按一下 [**附加偵錯工具**] 才能建立連線，因此如果失敗，請再試一次。

![Attaching debugger](./media/app-service-dotnet-remotely-debug-api-app/09-attaching-v3.png)

建立連接之後，請開啟 API 應用程式專案中的 **ContactsController.cs** 檔案，然後在  `Get` 和  `Post` 方法新增中斷點。它們一開始可能不會顯示為作用中，但如果已附加遠端偵錯工具，就可以準備開始偵錯。 

![Applying breakpoints to controller](./media/app-service-dotnet-remotely-debug-api-app/10-breakpoints-v3.png)

若要偵錯，以滑鼠右鍵按一下 [方案總管] 中的主控台應用程式，然後選取 [**偵錯**] > [**啟動新的執行個體**]。您現在可以在遠端偵錯 API 應用程式和在本機偵錯用戶端應用程式，以及查看資料的整個流程。 

下列螢幕擷取畫面顯示達到  `Post` 方法的中斷點時的偵錯工具。您可以看到用戶端的連絡人資料已還原序列化為強型別  `Contact` 物件。 

![Debugging the local client to hit the API](./media/app-service-dotnet-remotely-debug-api-app/12-debugging-live-v3.png)

## 摘要

API 應用程式的遠端偵錯可讓您輕鬆地查看您的程式碼在 Azure 應用程式服務中執行的狀況。在 Visual Studio IDE 中可取得您遠端執行的 Azure API 應用程式的豐富診斷和偵錯資料。


<!--HONumber=49-->