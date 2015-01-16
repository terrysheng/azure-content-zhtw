<properties title="Step 6: Access the Azure Machine Learning web service" pageTitle="步驟 6：存取機器學習 Web 服務 | Azure" description="步驟 6：存取使用中的 Azure 機器學習 Web 服務" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/06/2014" ms.author="garye" />


這是逐步解說[使用 Azure ML 開發預測方案][develop]的最後一個步驟：

[develop]: ../machine-learning-walkthrough-develop-predictive-solution/


1.	[建立 ML 工作區][create-workspace]
2.	[上傳現有的資料][upload-data]
3.	[建立新的實驗][create-new]
4.	[訓練和評估模型][train-models]
5.	[發佈 Web 服務][publish]
6.	**存取 Web 服務**

[create-workspace]: ../machine-learning-walkthrough-1-create-ml-workspace/
[upload-data]: ../machine-learning-walkthrough-2-upload-data/
[create-new]: ../machine-learning-walkthrough-3-create-new-experiment/
[train-models]: ../machine-learning-walkthrough-4-train-and-evaluate-models/
[publish]: ../machine-learning-walkthrough-5-publish-web-service/
[access-ws]: ../machine-learning-walkthrough-6-access-web-service/

----------

# 步驟 6：存取 Azure 機器學習 Web 服務

為了像 Web 服務一樣實用，使用者必須能夠將資料傳送至服務並接收結果。此 Web 服務是一個可用兩種方式來傳送和傳回資料的 Azure Web 服務：  

-	**要求/回應** - 使用者以 HTTP 通訊協定將一組信用資料傳送給服務，然後服務回應一組結果。
-	**批次執行** - 使用者將 Azure BLOB (含有一或多列的信用資料) 的 URL 傳送給服務。服務會將結果儲存在另一個 BLOB 中，再傳回該容器的 URL。  

在 Web 服務的 [**儀表板**] 索引標籤上，有兩個資訊連結可協助開發人員撰寫程式碼來存取此服務。在 [**要求/回應**] 資料列按一下 [**API 說明頁面**] 連結，將會開啟一個頁面，其中包含範例程式碼來使用服務的要求/回應通訊協定。同樣地，[**批次執行**] 資料列也提供範例程式碼來向服務提出批次要求。  

API 說明頁面包含 R、C# 和 Python 程式設計語言的範例。例如，以下 R 程式碼可用來存取我們發行的 Web 服務 (您的範例程式碼中會顯示實際的服務 URL)：  

	library("RCurl")
	library("RJSONIO")
	
	h = basicTextGatherer()
	req = list(Id="score00001",
	 Instance=list(FeatureVector=list(
	    "row.names"= "0",
	    "Status of checking account"= "0",
	    "Duration in months"= "0",
	    "Credit history"= "0",
	    "Purpose"= "0",
	    "Credit amount"= "0",
	    "Savings account/bond"= "0",
	    "Present employment since"= "0",
	    "Installment rate in percentage of disposable income"= "0",
	    "Personal status and sex"= "0",
	    "Other debtors"= "0",
	    "Present residence since"= "0",
	    "Property"= "0",
	    "Age in years"= "0",
	    "Other installment plans"= "0",
	    "Housing"= "0",
	    "Number of existing credits"= "0",
	    "Job"= "0",
	    "Number of people providing maintenance for"= "0",
	    "Telephone"= "0",
	    "Foreign worker"= "0",
	    "Credit risk"= "0"
	 ),GlobalParameters=fromJSON('{}')))
	
	body = toJSON(req)
	api_key = "abc123" # You can obtain the API key from the publisher of the web service
	
	h$reset()
	curlPerform(url = "http://xxx.cloudapp.net/workspaces/xxx/services/xxx/score",
	            httpheader=c('Content-Type' = "application/json", 'Authorization' = "Bearer " + api_key),
	            postfields=body,
	            writefunction = h$update,
	            verbose = TRUE
	            )
	
	result = h$value()


