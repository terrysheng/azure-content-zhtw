<properties
   pageTitle="在 Logic Apps 中使用 Azure 機器學習服務連接器 | Microsoft Azure App Service"
   description="如何建立並設定 Azure 機器學習服務連接器，並在 Azure App Service 的邏輯應用程式中使用它"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="erikre"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="02/11/2016"
   ms.author="jehollan"/>
   
# 概觀
>[AZURE.NOTE] 這一版文章適用於邏輯應用程式 2014-12-01-preview 結構描述版本。

適用於 Logic Apps 的 Azure ML 連接器可讓您針對批次評分 (批次執行服務) 和重新定型呼叫 Azure ML API。結合這些功能與邏輯應用程式觸發程序，可用來排程批次作業以及設定排定的模型重新定型。

 ![][1]
 
## 開始使用 Azure 機器學習服務連接器，並將它加入您的邏輯應用程式
若要開始，在 Azure ML Studio 中建立實驗，然後設定和部署 Web 服務。您便可以使用 API URL 和在批次強徵說明頁面上找到的 BES Post URL 的索引鍵。([機器學習服務逐步解說](../machine-learning/machine-learning-walkthrough-5-publish-web-service.md))

若要使用連接器執行 BES 作業，請將 Azure ML 連接器加入邏輯應用程式。然後輸入所需的資訊 (詳細說明請參閱下文)。如果要設定重新定型，請新增第二個 Azure ML 連接器並提供輸入參數 (請參閱[設定模型以進行重新定型](../machine-learning/machine-learning-retrain-models-programmatically.md))。

## 執行 Azure ML 批次執行作業
Azure ML 連接器提供下列四個執行批次執行 (BES) 作業的選項：1.有輸入和輸出的批次作業：實驗有 Web 服務輸入和輸出模組。2.沒有輸入和輸出的批次作業：實驗沒有 Web 服務輸入或輸出模組 (例如，使用讀取器和寫入器模組)。3.只輸入的批次作業：實驗有 Web 服務輸入模組，但是沒有 Web 服務輸出模組 (例如，使用寫入器模組)。4.只輸出的批次作業：實驗沒有 Web 服務輸入模組，但有 Web 服務輸出模組 (例如使用讀取器模組)。請注意，BES 是非同步的要求，可能需要較長時間才能完成，視資料大小和模型複雜度而定。當作業完成時，連接器會傳回輸出結果。

### 執行批次執行：有輸入和輸出
如果 Studio 實驗有 Web 服務輸入和輸出模組，您必須[提供 blob 儲存體帳戶和位置的資訊](../machine-learning/machine-learning-consume-web-services.md)。此外，如果您的實驗中有設定全域 (Web 服務) 參數，您便可以加入這些參數 ([機器學習 Web 服務參數](../machine-learning/machine-learning-web-service-parameters.md))。

![][2]

按一下省略符號可顯示和隱藏 [全域參數] 欄位。這可讓您用逗號分隔的欄位和值清單提供一個或多個全域 (Web 服務) 參數。

![][3]

BES 作業的其他變化，例如沒有 Web 服務輸入或輸出的作業，也可透過連接器使用。

## 設定重新定型

使用 [設定重新定型] 動作設定一次性或排程的 ML 模型重新定型。結合連接器建立的批次執行作業，您可以完成定型和更新 Web 服務模型的步驟。在此工作流程中，您會使用連接器兩次。1.第一個連接器用來執行 BES 作業，重新定型您的模型並傳回輸出。此執行的輸出將會有新模型的 URL (.ilearner)。它可以是選擇性的 – 如果您已在實驗中設定它 – 傳回評估模組的輸出的 URL，是 csv 檔案。在下一個步驟中，您可以使用評估模組輸出中的資料，來決定是否要取代 Web 服務中的模型 (例如，如果準確度 > 0.85)。1.第二個連接器用來設定重新定型。它會使用第一個連接器的輸出中的參數，選擇性檢查更新模型的條件，並以新定型的模型更新 Web 服務。 * 舉例來說，您可以在 [重新定型模型 URL] 欄位中輸入 `@{body('besconnector').Results.output2.FullURL}`，使用新定型模型的 URL 來存取 BES 作業的輸出。這裡假設訓練實驗中的 Web 服務輸出叫做 "output2"。 * 針對 [資源名稱]，使用在｢預測實驗｣中儲存的已定型模型的完整名稱，例如 "MyTrainedModel [trained model]"。 * 在 [評估結果索引鍵] 欄位中，您可以輸入訓練實驗之評估模組的輸出中的任何傳回參數 (如果您已包含它)。您可以在 Azure ML Studio 中將訓練實驗中評估模組的結果視覺化程現，即可查看可用參數清單。在分類實驗中，這些會包含準確度、精確度、回收、F 分數、AUC、平均記錄遺失和定型記錄遺失。

![][4]
 
### 排定的重新訓練
 
使用邏輯應用程式觸發程序，您可以設定連接器執行排程。這可以定期在新資料到達時重新定型模型。BES 作業會重新定型模型，重新定型完成後，重新定型動作會更新模型。
 
![][5]
 
## 連接器的輸出 
 
**BES**：批次作業成功完成之後，連接器的輸出會有每個 Web 服務輸出的以下資訊。
 
 ![][6]
 
請注意，如果您並未包含 Web 服務輸出 (例如，您在 Studio 中使用寫入器模組寫入實驗的資料庫)，則不會得到這些輸出。

**重新定型**：重新定型成功完成之後，輸出就會有下列資訊。

![][7]

## 摘要

使用適用於 Logic Apps 的 Azure ML 連接器，您可以執行批次評分和重新定型作業，並按隨選或排定的週期性排程執行。兩個動作會自動組合，可以評分資料，以及重新定型、評估及更新 Web 服務的模型，而不需要撰寫任何程式碼。

 <!--Image references-->
[1]: ./media/app-service-logic-connector-azureml/img1.png
[2]: ./media/app-service-logic-connector-azureml/img2.png
[3]: ./media/app-service-logic-connector-azureml/img3.png
[4]: ./media/app-service-logic-connector-azureml/img4.png
[5]: ./media/app-service-logic-connector-azureml/img5.png
[6]: ./media/app-service-logic-connector-azureml/img6.png
[7]: ./media/app-service-logic-connector-azureml/img7.png

<!---HONumber=AcomDC_0224_2016-->