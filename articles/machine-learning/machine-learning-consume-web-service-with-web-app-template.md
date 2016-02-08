<properties
	pageTitle="使用 Machine Learning Web 服務與 Web 應用程式範本 | Microsoft Azure"
	description="使用 Azure Marketplace 中的 Web 應用程式範本以使用 Azure Machine Learning 中的預測 Web 服務。"
	keywords="Web 服務、operationalization、REST API、機器學習服務"
	services="machine-learning"
	documentationCenter=""
	authors="garyericson"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/26/2015"
	ms.author="garye;raymondl"/>

# 使用 Azure Machine Learning Web 服務與 Web 應用程式範本

一旦使用 Machine Learning Studio，或使用 R 或 Python 之類的工具開發預測模型並部署為 Azure Web 服務後，即可使用 REST API 存取實際運作模型。

有數種方法可以使用 REST API 和存取 Web 服務。例如，您可以使用當您部署 Web 服務時為您產生的範例程式碼，在 C#、R 或 Python 中寫入應用程式 (可以在 Machine Learning Studio 中 Web 服務儀表板中的 API 說明頁面上取得)。或者您可以使用為您建立的範例 Microsoft Excel 活頁簿 (也可以在 Studio 中的 Web 服務儀表板取得)。

但是最簡單快速存取 Web 服務的方式是透過 [Azure Web 應用程式 Marketplace](https://azure.microsoft.com/marketplace/web-applications/all/) 中提供的 Web 應用程式範本。

[AZURE.INCLUDE [電腦-學習-免費-試用](../../includes/machine-learning-free-trial.md)]

## Azure Machine Learning Web 應用程式範本

Azure Marketplace 中可用的 Web 應用程式範本可以建立自訂的 Web 應用程式，該應用程式知道您的 Web 服務的輸入資料和預期的結果。您所需要做的就是將您的 Web 服務和資料的存取權授與 Web 應用程式，範本會執行其餘部分。

兩個範本可供使用：

- [Azure ML 要求回應服務 Web 應用程式範本](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlaspnettemplateforrrs/)
- [Azure ML 批次執行服務 Web 應用程式範本](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/)

每個範本會建立範例 ASP.NET 應用程式，使用您的 Web 服務的 API URI 和金鑰，並做為網站部署至 Azure。要求回應服務 (RRS) 範本會建立 Web 應用程式，可讓您將資料的單一資料列傳送至 Web 服務以取得單一結果。批次執行服務 (BES) 範本會建立 Web 應用程式，可讓您傳送資料的許多資料列以取得多個結果。

不必撰寫程式碼就可以使用這些範本。您只需提供 API URI 和金鑰，範本就會為您建置應用程式。

## 如何使用要求回應服務 (RRS) 範本

一旦您部署您的 Web 服務，就可以遵循以下的步驟以使用 RRS Web 應用程式範本，如下圖所示。

![使用 RRS Web 範本的程序][image1]

1. 在 Machine Learning Studio 中，開啟 [Web 服務] 索引標籤，然後開啟您想要存取的 Web 服務。複製 [API 金鑰] 底下所列的金鑰並儲存。

	![API 金鑰][image3]

2. 開啟 [要求/回應] API 說明頁面。在說明頁面頂端的 [要求] 底下，複製 [要求 URI] 值並儲存。此值如下所示：

		https://ussouthcentral.services.azureml.net/workspaces/<workspace-id>/services/<service-id>/execute?api-version=2.0&details=true

	![要求 URI][image4]

3. 移至 [Azure 入口網站](https://portal.azure.com) 的 [登入]，按一下 [新增]，搜尋並選取 [Azure ML 要求回應服務 Web 應用程式]，然後按一下 [建立]。

	- 為您的 Web 應用程式提供唯一名稱。Web 應用程式的 URL 將是此名稱後面加上 `.azurewebsites.net.`。例如，`http://carprediction.azurewebsites.net.`

	- 選擇 Azure 訂用帳戶及您的 Web 服務在其下執行的服務。

	- 按一下 [建立]。

	![建立 Web 應用程式][image5]

4. 當 Azure 完成部署 Web 應用程式時，請按一下 Azure 中 Web 應用程式設定頁面上的 [URL]，或在網頁瀏覽器中輸入 URL。例如，`http://carprediction.azurewebsites.net.`

5. 當 Web 應用程式第一次執行時，它會要求您提供 **API 貼文 URL** 和 **API 金鑰**。輸入您先前儲存的值：
	- API 說明頁面針對 **API 貼文 URL** 的**要求 URI**
	- Web 服務儀表板針對 **API 金鑰**的 **API 金鑰**。

	按一下 [提交]。

	![輸入貼文 URI 和 API 金鑰][image6]

6. Web 應用程式以目前 Web 服務設定顯示其 [Web 應用程式組態] 頁面。您可以在這裡變更 Web 應用程式所使用的設定。

	> [AZURE.NOTE] 變更此處的設定只會變更此 Web 應用程式的設定。不會變更您的 Web 服務的預設設定。例如，如果您變更這裡的 [描述]，則不會變更在 Machine Learning Studio 中 Web 服務儀表板上顯示的描述。

	當您完成時，按一下 [儲存變更]，然後按一下 [到首頁]。

7. 您可以在首頁輸入要傳送至 Web 服務的值，按一下 [提交]，即會傳回結果。

如果您想要返回 [組態] 頁面上，請移至 Web 應用程式的 `setting.aspx` 頁面。例如：`http://carprediction.azurewebsites.net/setting.aspx.`。系統會提示您再次輸入 API 金鑰，您需要該金鑰以存取頁面和更新設定。

您可以在 Azure 傳統入口網站停止、重新啟動或刪除 Web 應用程式，就像任何其他 Web 應用程式一樣。只要它正在執行中，您就可以瀏覽至首頁網址並輸入新值。

## 如何使用批次執行服務 (BES) 範本

您可以使用與 RRS 範本相同的方式使用 BES Web 應用程式範本，不同的是建立的 Web 應用程式可讓您提交資料的多個資料列和接收多個結果。

批次執行 Web 服務的結果會儲存在 Azure 儲存體容器。輸入值可能來自 Azure 儲存體或本機檔案。因此，您需要 Azure 儲存體容器以存放 Web 應用程式所傳回的結果，您也必須準備好您的輸入資料。

![使用 BES Web 範本的程序][image2]

1. 遵循與 RRS 範本相同的程序來建立 BES Web 應用程式，不同之處為：
	- 從**批次執行** API 說明頁面取得 Web 服務的**要求 URI**。
	- 移至 [Azure ML Batch 執行服務 Web 應用程式範本](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/)以開啟 Azure Marketplace 上的 BES 範本，然後按一下 [建立 Web 應用程式]。

2. 若要指定您想要儲存結果的位置，請在 Web 應用程式的首頁上輸入目的地容器資訊。同時指定 Web 應用程式可以在哪裡取得輸入值，在本機檔案或 Azure 儲存體容器。按一下 [提交]。

	![儲存體資訊][image7]

Web 應用程式將會顯示具有工作狀態的頁面。工作完成時，系統會提供您 Azure Blob 儲存體中結果的位置。您也可以選擇將結果下載到本機檔案。

## 如需 Blob 的詳細資訊，

深入了解...

- 使用 Machine Learning Studio 建立機器學習服務實驗，請參閱[在 Azure Machine Learning Studio 中建立您的第一個實驗](machine-learning-create-experiment.md)

- 如何將您的機器學習服務實驗部署為 Web 服務，請參閱[部署 Azure Machine Learning Web 服務](machine-learning-publish-a-machine-learning-web-service.md)

- 關於存取您 Web 服務的其他方式，請參閱[如何使用 Azure Machine Learning Web 服務](machine-learning-consume-web-services.md)


[image1]: media\machine-learning-consume-web-service-with-web-app-template\rrs-web-template-flow.png
[image2]: media\machine-learning-consume-web-service-with-web-app-template\bes-web-template-flow.png
[image3]: media\machine-learning-consume-web-service-with-web-app-template\api-key.png
[image4]: media\machine-learning-consume-web-service-with-web-app-template\post-uri.png
[image5]: media\machine-learning-consume-web-service-with-web-app-template\create-web-app.png
[image6]: media\machine-learning-consume-web-service-with-web-app-template\web-service-info.png
[image7]: media\machine-learning-consume-web-service-with-web-app-template\storage.png

<!---HONumber=AcomDC_0128_2016-->