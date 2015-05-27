<properties 
	pageTitle="偵錯 Azure App Service 中的 API 應用程式" 
	description="了解如何在 API 應用程式於 Azure App Service 執行時使用 Visual Studio 進行偵錯。" 
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

# 偵錯 Azure App Service 中的 API 應用程式 

## 概觀

在本教學課程中，您將在 ASP.NET Web API 程式碼於 [Azure App Service](app-service-value-prop-what-is.md) 中的 [API 應用程式](app-service-api-apps-why-best-platform.md)執行時進行偵錯。本教學課程會使用您已在此系列的先前教學課程中[建立](app-service-dotnet-create-api-app.md)和[部署](app-service-dotnet-deploy-api-app.md)的 API 應用程式。

一開始您會使用 Visual Studio 的 **API 應用程式用戶端**功能，產生可呼叫已部署 API 應用程式的用戶端程式碼。然後，您會在 API 應用程式於雲端中即時執行的同時偵錯用戶端應用程式和 API 應用程式。

## 產生 API 應用程式用戶端 

Visual Studio 中的 API 應用程式工具可讓您輕鬆地產生 C# 程式碼，而該程式碼可從桌面、存放區和行動應用程式呼叫 Azure API 應用程式。

1. 在 Visual Studio 中，開啟方案，其中包含來自[建立 API 應用程式](app-service-dotnet-create-api-app.md)主題的 API 應用程式。 

2. 從 [**方案總管**中，以滑鼠右鍵按一下方案，然後選取 [**新增**] > [**新增專案**]。

	![新增專案](./media/app-service-dotnet-remotely-debug-api-app/01-add-new-project-v3.png)

3. 在 [**新增專案**] 對話方塊中，執行下列步驟：

	1. 選取 [**Windows 桌面**] 類別。
	
	2. 選取 [**主控台應用程式**] 專案範本。
	
	3. 命名專案。
	
	4. 按一下 [**確定**]，以在現有方案中產生新專案。
	
	![新增專案](./media/app-service-dotnet-remotely-debug-api-app/02-contact-list-console-project-v3.png)

4. 以滑鼠右鍵按一下剛建立的主控台應用程式專案，然後選取 [**新增**] > [**Azure API 應用程式用戶端**]。

	![新增用戶端](./media/app-service-dotnet-remotely-debug-api-app/03-add-azure-api-client-v3.png)
	
5. 在 [**新增 Microsoft Azure API 應用程式用戶端**] 對話方塊中，執行下列步驟：

	1. 選取 [**下載**] 選項。 
	
	2. 從下拉式清單中，選取您先前建立的 API 應用程式。
	
	3. 按一下 [確定]****。

	![產生畫面](./media/app-service-dotnet-remotely-debug-api-app/04-select-the-api-v3.png)

	此精靈會下載 API 中繼資料檔案，並產生可供叫用 API 應用程式的具型別介面。

	![產生中](./media/app-service-dotnet-remotely-debug-api-app/05-metadata-downloading-v3.png)

	程式碼產生完成之後，您會在 [方案總管] 中看到採用 API 應用程式名稱的新資料夾。此資料夾包含實作用戶端和資料模型的程式碼。

	![產生完成](./media/app-service-dotnet-remotely-debug-api-app/06-code-gen-output-v3.png)

6. 從專案根目錄開啟 **Program.cs** 檔案，並將 **Main** 方法取代為下列程式碼：

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

## 測試 API 應用程式用戶端

一旦編碼了 API 應用程式，現在是時候在瀏覽器中測試程式碼。

1. 開啟 [**方案總管**]。

2. 以滑鼠右鍵按一下您已在上一節中建立的主控台應用程式。

3. 從主控台應用程式的內容功能表中，選取 [**偵錯 > 啟動新的執行個體**]。

4. 主控台視窗應該開啟並顯示所有連絡人。

	![執行主控台應用程式](./media/app-service-dotnet-remotely-debug-api-app/running-console-app.png)

5. 按 **Enter** 鍵以關閉主控台視窗。

## 偵錯 API 應用程式 

既然已編碼並測試 API 應用程式和其用戶端，就讓我們了解如何進行偵錯。

1. 從 Visual Studio 的 [**檢視**] 功能表中，選取 [**伺服器總管**]。 

2. 在 [**伺服器總管**] 中，展開 [**Azure > App Service**] 節點。

3. 尋找您在部署 API 應用程式時所建立的資源群組。

4. 在資源群組下，以滑鼠右鍵按一下 API 應用程式的節點，然後選取 [**附加偵錯工具**]。

	![附加偵錯工具](./media/app-service-dotnet-remotely-debug-api-app/08-attach-debugger-v3.png)

	遠端偵錯工具會嘗試連線。在某些情況下，您可能需要重試按一下 [**附加偵錯工具**] 才能建立連線，因此如果失敗，請再試一次。

	![附加偵錯工具](./media/app-service-dotnet-remotely-debug-api-app/09-attaching-v3.png)

16. 建立連接之後，請開啟 API 應用程式專案中的 **ContactsController.cs** 檔案，然後在 `Get` 和 `Post` 方法新增中斷點。它們一開始可能不會顯示為作用中，但如果已附加遠端偵錯工具，就可以準備開始偵錯。

	![將中斷點套用至控制器](./media/app-service-dotnet-remotely-debug-api-app/10-breakpoints-v3.png)

17. 若要偵錯，以滑鼠右鍵按一下 [**方案總管**] 中的主控台應用程式，然後選取 [**偵錯**] > [**啟動新的執行個體**]。您現在可以在遠端偵錯 API 應用程式和在本機偵錯用戶端應用程式，以及查看資料的整個流程。

	下列螢幕擷取畫面顯示達到 `Post` 方法的中斷點時的偵錯工具。您可以看到用戶端的連絡人資料已還原序列化為強型別 `Contact` 物件。

	![偵錯本機用戶端以叫用 API](./media/app-service-dotnet-remotely-debug-api-app/12-debugging-live-v3.png)

## 後續步驟

API 應用程式的遠端偵錯可讓您輕鬆地查看您的程式碼在 Azure App Service 中執行的狀況。在 Visual Studio IDE 中，可以立即取得 Azure API 應用程式的豐富診斷和偵錯資料。

App Service API 應用程式是具有額外功能 (用於裝載 Web 服務) 的 App Service Web 應用程式，因此您可對 API 應用程式使用您對 Web 應用程式使用的相同偵錯和疑難排解工具。如需詳細資訊，請參閱[使用 Visual Studio 疑難排解 Azure App Service 中的 Web 應用程式](web-sites-dotnet-troubleshoot-visual-studio.md)。

任何人皆可公開呼叫您在此系列中所建立的 API 應用程式。如需如何保護 API 應用程式，以便只有已驗證的使用者可以呼叫它的相關資訊，請參閱[保護 API 應用程式：新增 Azure Active Directory 或社交提供者驗證](app-service-api-dotnet-add-authentication.md)。

<!--HONumber=54-->