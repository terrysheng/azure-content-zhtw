<properties 
	pageTitle="將 Azure WebJobs 部署至 Azure 網站" 
	description="了解如何使用 Visual Studio 將 Azure WebJobs 部署到 Azure 網站。" 
	services="web-sites" 
	documentationCenter="" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="web" 
	ms.date="11/12/2014" 
	ms.author="tdykstra"/>

# 如何將 Azure WebJobs 部署至 Azure 網站

本主題說明如何使用 Visual Studio，將主控台應用程式專案作為 [Azure WebJob](http://go.microsoft.com/fwlink/?LinkId=390226) 部署至 Azure 網站。部署 WebJobs 的另一個方法是使用 Azure 管理入口網站；如需該方法的相關資訊，請參閱[使用 WebJob 在 Microsoft Azure 網站中執行背景工作](/zh-tw/documentation/articles/web-sites-create-web-jobs/).

## 目錄 ##

- [概觀](#overview)
- [先決條件](#prerequisites)
- [啟用現有主控台應用程式專案的 WebJobs 部署](#convert)
- [建立具有 WebJobs 功能的新專案](#create)
- [新增 Azure WebJob 對話方塊](#configure)
- [webjob-publish-settings.json 檔案](#publishsettings)
- [webjobs-list.json 檔案](#webjobslist)
- [部署 WebJobs 專案](#deploy)
- [後續步驟](#next-steps)

## <a id="overview"></a>概觀

當 Visual Studio 部署具有 WebJobs 功能的主控台應用程式專案時，它會執行兩個工作：

* 將執行階段檔案複製到 Azure 網站中的適當資料夾 (若是連續的 WebJobs，則是 *App_Data/jobs/continuous*，若是排程和隨選 WebJobs，則是  *App_Data/jobs/triggered*)。
* 為已排定在特定時間執行的 WebJobs 設定 [Azure 排程器工作](#scheduler) 。(無需為連續 WebJobs 執行此動作。)

具有 WebJobs 功能的專案會新增下列項目：

* [Microsoft.Web.WebJobs.Publish](http://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) NuGet 封裝。
* 包含部署和排程器設定的 [webjob-publish-settings.json](#publishsettings) 檔案。 

![Diagram showing what is added to a Console App to enable deployment as a WebJob](./media/websites-dotnet-deploy-webjobs/convert.png)

您可以將這些項目新增至現有的主控台應用程式專案，或使用範本建立具有 WebJobs 功能的新主控台應用程式專案。 

以 WebJob 的方式自我部署專案，或將專案連結到 Web 專案，因此每當您要部署 Web 專案時，專案便會自動部署。為了連結專案，Visual Studio 會在 Web 專案的 [webjobs-list.json](#webjobslist) 檔案中加上具有 WebJobs 功能的專案名稱。

![Diagram showing WebJob project linking to web project](./media/websites-dotnet-deploy-webjobs/link.png)
 


## 先決條件

安裝 Azure SDK 2.4 版或更新版本後，您便可在 Visual Studio 2013 中使用 WebJobs 部署功能：

* [Azure SDK for Visual Studio 2013](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409)。

[Visual Studio 2013 Update 3](http://go.microsoft.com/fwlink/?linkid=397827&clcid=0x409) 及更新版本的更新也會包含 WebJobs 部署功能。

## <a id="convert"></a>啟用現有主控台應用程式專案的 WebJobs 部署

您有兩個選擇：

* [透過 Web 專案啟用自動部署](#convertlink)。

	設定現有主控台應用程式專案，以便在您部署 Web 專案時，它會以 WebJob 的方式自動部署。當您要在與執行相關 Web 應用程式相同的網站中執行 WebJob 時，請使用此選項。

* [不透過 Web 專案啟用部署](#convertnolink)。

	設定現有主控台應用程式專案以 WebJob 的方式自我部署，且不具 Web 專案的連結。當您要在網站中自我執行 WebJob，且網站中沒有正在執行的 Web 應用程式，請使用此選項。為了調整不受 Web 應用程式資源影響的 WebJob 資源，您可能會想執行此動作。

### <a id="convertlink"></a> 透過 Web 專案啟用自動 WebJobs 部署
  
1. 以滑鼠右鍵按一下 [方案總管]**** 中的 Web 專案，然後依序按一下 [新增]**** > [做為 Azure WebJob 的現有專案]****。

	![Existing Project as Azure WebJob](./media/websites-dotnet-deploy-webjobs/eawj.png)
	
	The [Add Azure WebJob](#configure) dialog box appears.

1. 在 [專案名稱]**** 下拉式清單中，選取要新增為 WebJob 的主控台應用程式專案。

	![Selecting project in Add Azure WebJob dialog](./media/websites-dotnet-deploy-webjobs/aaw1.png)

2. 完成 [[新增 Azure WebJob]](#configure) 對話方塊，然後按一下 [確定]****。 

### <a id="convertnolink"></a> 不透過 Web 專案啟用 WebJobs 部署
  
1. 以滑鼠右鍵按一下 [方案總管]**** 中的主控台應用程式專案，然後按一下 [發行為 Azure WebJob]****。 

	![Publish as Azure WebJob](./media/websites-dotnet-deploy-webjobs/paw.png)
	
	[[新增 Azure WebJob]](#configure) 對話方塊隨即出現，而且 [專案名稱]**** 方塊中已選取此專案。

2.  完成 [[新增 Azure WebJob]](#configure) 對話方塊，然後按一下 [確定]****。

	此時會出現 [發行 Web]**** 精靈。如果您不打算立即發行，請關閉精靈。您所輸入的設定會被儲存下來，以便在[部署專案]時使用(#deploy)。

## <a id="create"></a>建立具有 WebJobs 功能的新專案

若要建立具有 WebJobs 功能的新專案，您可以使用主控台應用程式專案範本，並如[上一節]中所述來啟用 WebJobs 部署(#convert)。另一種方式是，您可以使用 WebJobs 新專案範本：

* [在獨立的 WebJob 中使用 WebJobs 新專案範本](#createnolink)

	建立專案，並將專案設定為以 WebJob 的方式自我部署，且不具 Web 專案的連結。當您要在網站中自我執行 WebJob，且網站中沒有正在執行的 Web 應用程式，請使用此選項。為了調整不受 Web 應用程式資源影響的 WebJob 資源，您可能會想執行此動作。

* [在連結至 Web 專案的 WebJob 中使用 WebJobs 新專案範本](#createlink)

	建立專案，並設定在針對位於相同解決方案中的 Web 專案進行部署時，會自動以 WebJob 的方式部署此專案。當您要在與執行相關 Web 應用程式相同的網站中執行 WebJob 時，請使用此選項。

在 SDK 2.4 版本中，WebJobs 新專案範本並不會比建立主控台應用程式專案並啟用 WebJobs 部署來得容易。日後，WebJobs 新專案範本將更有助於 [WebJobs SDK](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs) 開發，因為它會自動安裝適當的 WebJobs SDK NuGet 封裝。在那之前，您可以手動安裝此封裝來設定專案使用 WebJobs SDK，如 [WebJobs SDK 教學課程](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs)中所示。 


### <a id="createnolink"></a> 在獨立的 WebJob 中使用 WebJobs 新專案範本
  
1. 依序按一下 [檔案]**** > [新增專案]****，然後在 [新增專案]**** 對話方塊中，依序按一下 [雲端]**** > [Microsoft Azure WebJob]****。

	![New Project dialog showing WebJob template](./media/websites-dotnet-deploy-webjobs/np.png)
	
2. 依照稍早所示的指示進行，[將主控台應用程式專案設定成獨立的 WebJobs 專案](#convertnolink)。

### <a id="createlink"></a> 在連結至 Web 專案的 WebJob 中使用 WebJobs 新專案範本

1. 以滑鼠右鍵按一下 [方案總管]**** 中的 Web 專案，然後依序按一下 [新增]**** > [新增 Azure WebJob 專案]****。

	![New Azure WebJob Project menu entry](./media/websites-dotnet-deploy-webjobs/nawj.png)

	[[新增 Azure WebJob]](#configure) 對話方塊隨即出現。

2. 完成 [[新增 Azure WebJob]](#configure) 對話方塊，然後按一下 [確定]****。

## <a id="configure"></a>新增 Azure WebJob 對話方塊

[新增 Azure WebJob]**** 對話方塊可讓您輸入 WebJob 的名稱和排程設定。 

![Add Azure WebJob dialog](./media/websites-dotnet-deploy-webjobs/aaw2.png)

此對話方塊中的欄位會對應至 Azure 管理入口網站中 [新增工作]**** 對話方塊上的欄位。如需詳細資訊，請參閱[使用 WebJob 在 Azure 網站中執行背景工作](/zh-tw/documentation/articles/web-sites-create-web-jobs/).

若是排定的 WebJob (不適用於連續 WebJobs)，則 Visual Studio 會建立 [Azure 排程器](/zh-tw/services/scheduler/)工作集合 (如果尚未存在)，然後在集合中建立工作：

* 排程器工作集合會被命名為 *WebJobs-{regionname}*，其中 *{regionname}* 是指託管網站的區域。例如：WebJobs-WestUS。
* 排程器工作會被命名為 *{websitename}-{webjobname}*。例如：MyWebSite-MyWebJob。 
 
>[AZURE.NOTE]
> 
>* 如需命令列部署的相關資訊，請參閱[啟用 Azure WebJobs 的命令列或連續傳遞](/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/).
>* 如果設定了 [週期性工作]****，並將週期頻率設為分鐘數，則 Azure 排程器不是免費提供的服務。其他頻率 (時數、天數等) 可免費提供。
>* 如果部署 WebJob，並在稍後將執行模式從連續變更為非連續 (或相反情形)，則在您重新部署時，Visual Studio 會在 Azure 中建立新的 WebJob。如果您變更其他排程設定但保持執行模式不變，或在排程和隨選模式之間切換，則 Visual Studio 會更新現有的工作，而非建立新的工作。

## <a id="publishsettings"></a>webjob-publish-settings.json

當您設定 WebJobs 部署的主控台應用程式時，Visual Studio 會安裝 [Microsoft.Web.WebJobs.Publish](http://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) NuGet 封裝， 
並將排程資訊儲存在 WebJobs 專案中專案 *Properties* 資料夾的 *webjob-publish-settings.json* 檔案中。以下是該檔案的範例：

		{
		  "$schema": "http://schemastore.org/schemas/json/webjob-publish-settings.json",
		  "webJobName": "WebJob1",
		  "startTime": "2014-06-23T00:00:00-08:00",
		  "endTime": "2014-06-27T00:00:00-08:00",
		  "jobRecurrenceFrequency": "Minute",
		  "interval": 5,
		  "runMode": "Scheduled"
		}

您可以編輯此檔案目錄，而 Visual Studio 會提供 IntelliSense。檔案結構描述儲存在 [http://schemastore.org](http://schemastore.org/schemas/json/webjob-publish-settings.json) 中，您可以在那裡進行檢視。  

>[AZURE.NOTE]
>
>* 如果設定了 [週期性工作]****，並將週期頻率設為分鐘數，則 Azure 排程器不是免費提供的服務。其他頻率 (時數、天數等) 可免費提供。

## <a id="webjobslist"></a>webjobs-list.json

當您將具有 WebJobs 功能的專案連結到 Web 專案時，Visual Studio 會將 WebJobs 專案的名稱儲存在 Web 專案之 *Properties* 資料夾的 *webjobs-list.json* 檔案中。此清單可能包含多個 WebJobs 專案，如以下範例所示：

		{
		  "$schema": "http://schemastore.org/schemas/json/webjobs-list.json",
		  "WebJobs": [
		    {
		      "filePath": "../ConsoleApplication1/ConsoleApplication1.csproj"
		    },
		    {
		      "filePath": "../WebJob1/WebJob1.csproj"
		    }
		  ]
		}

您可以編輯此檔案目錄，而 Visual Studio 會提供 IntelliSense。檔案結構描述儲存在 [http://schemastore.org](http://schemastore.org/schemas/json/webjobs-list.json) 中，您可以在那裡進行檢視。
  
## <a id="deploy"></a>部署 WebJobs 專案

已連結到 Web 專案的 WebJobs 專案會透過 Web 專案自動部署。如需 Web 專案部署的相關資訊，請參閱[如何部署 Azure 網站](/zh-tw/documentation/articles/websites-dotnet-deploy/).

若要自我部署 WebJobs 專案，請以滑鼠右鍵按一下 [方案總管]**** 中的專案，然後按一下 [發行為 Azure WebJob]****。 

![Publish as Azure WebJob](./media/websites-dotnet-deploy-webjobs/paw.png)
	
若是獨立的 WebJob，則 Web 專案所使用的相同 [發行 Web]**** 精靈會隨即出現，但其中幾個設定可以變更。

## <a id="nextsteps"></a>後續步驟

如需如何在 Visual Studio 中使用連續傳遞程序部署 Azure WebJobs 的詳細資訊，請參閱 [Azure WebJobs - 建議資源 - 部署](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/azure-webjobs-recommended-resources#deploying)。


<!--HONumber=42-->
