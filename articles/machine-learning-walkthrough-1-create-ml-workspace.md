<properties 
	pageTitle="步驟 1：建立 Machine Learning 工作區 | Azure" 
	description="方案逐步解說步驟 1：建立新的 Azure Machine Learning Studio 工作區" 
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
	ms.date="01/29/2015" 
	ms.author="garye"/>


這是[使用 Azure ML 開發預測方案][develop]逐步解說的第一個步驟：

[develop]: ../machine-learning-walkthrough-develop-predictive-solution/

1.	**建立 ML 工作區**
2.	[上傳現有資料][upload-data]
3.	[建立新實驗][create-new]
4.	[訓練及評估模型][train-models]
5.	[發佈 Web 服務][publish]
6.	[存取 Web 服務][access-ws]

[create-workspace]: ../machine-learning-walkthrough-1-create-ml-workspace/
[upload-data]: ../machine-learning-walkthrough-2-upload-data/
[create-new]: ../machine-learning-walkthrough-3-create-new-experiment/
[train-models]: ../machine-learning-walkthrough-4-train-and-evaluate-models/
[publish]: ../machine-learning-walkthrough-5-publish-web-service/
[access-ws]: ../machine-learning-walkthrough-6-access-web-service/

----------

# 步驟 1：建立 Azure Machine Learning 工作區

若要使用 ML Studio，您必須具有 ML 工作區。此工作區包含您建立、管理及發行實驗所需的工具。  

## 建立工作區  

1.	登入您的 Microsoft Azure 帳戶。
2.	在 Microsoft Azure 服務面板中，按一下 [**MACHINE LEARNING**]。  
![Create workspace][1]

3.	按一下 [**建立 ML 工作區**]。
4.	在 [**快速建**] 頁面中輸入您的工作區資訊，然後按一下 [**建立 ML 工作區**]。

	>**注意** - [**工作區擁有者** 是您的 Microsoft 帳戶 (例如 name@outlook.com) 或組織帳戶。

ML 工作區建立後，您會看見它列示在 [**機器學習**] 頁面上。  

>**秘訣** - 您可以邀請他人到您的工作區，共用您正在執行的實驗。您可以在 ML Studio 中的 [**設定**] 頁面上執行此動作。您只需要每個使用者的 Microsoft 帳戶或組織帳戶。

----------

**接著：[上傳現有資料][upload-data]**

[1]: ./media/machine-learning-walkthrough-1-create-ml-workspace/create1.png

<!--HONumber=46--> 
