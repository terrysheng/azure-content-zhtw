<properties
	pageTitle="適用於機器學習 Web 服務的 Excel 增益集 | Microsoft Azure"
	description="如何在 Excel 中直接使用 Azure Machine Learning Web 服務，而不需要撰寫任何程式碼。"
	services="machine-learning"
	documentationCenter=""
	authors="tedway"
	manager="paulettm"
	editor="cgronlun"
	tags=""/>

<tags
	ms.service="machine-learning"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="data-services"
	ms.date="08/25/2015"
	ms.author="tedway;garye"/>

# 適用於 Azure Machine Learning Web 服務的 Excel 增益集

Excel 可以讓您直接輕鬆呼叫 Web 服務，而不需要撰寫任何程式碼。

## 使用活頁簿中現有 Web 服務的步驟

1. 開啟 [範例 Excel 檔案](http://aka.ms/amlexcel-sample-2)，其中包含 Excel 增益集和鐵達尼號乘客的相關資料。
2. 按一下 Web 服務加以選擇，在此範例中為「鐵達尼號存活者預測工具 (Excel 增益集範例) [分數]」。

    ![選取 Web 服務][01]

3. 這將帶領您到 [預測] 區段。此活頁簿已經包含範例資料，但若為空白的活頁簿，您也可在 Excel 中選取一個儲存格並按一下 [使用範例資料]。
4. 選取含有標頭的資料並按一下輸入資料範圍圖示。請確定已核取 [我的資料有標頭] 方塊。
5. 在 [輸出] 之下，輸入您想要輸出所在的儲存格編號，例如此觸的 "H1"。
6. 按一下 [預測]。

	![預測區段][02]

## 加入新的 Web 服務的步驟

1. 發佈 Web 服務 ([本頁面](machine-learning-walkthrough-5-publish-web-service.md) 會說明做法) 或尋找現有的 Web 服務。
2. 在 Excel 中，移至 [Web 服務] 區段 (如果您是在 [預測] 區段中，請按一下 [向後鍵] 前往 Web 服務的清單)。

	![移至 web 服務選取][03]

3. 按一下 [新增 Web 服務]。
4. 在 Machine Learning Studio 中，按一下左窗格中的 [WEB 服務] 區段，然後選取 web 服務。

	![Studio 選取 Web 服務][04]

5. 複製 web 服務的 API 金鑰。

	![Studio API 金鑰][05]

6. 將此 API 金鑰貼到標示為 **API 金鑰**的 Excel 增益集文字方塊中。
7. 在 web 服務的 [儀表板] 索引標籤上，按一下**要求/回應**連結。
8. 尋找 **OData 端點位址**一節。複製 URL 並貼到標示為 **URL** 的文字方塊中。
9. 按一下 [新增]。

	![URL 和 API 金鑰][06]

10.	若要使用 Web 服務，請遵循上述指示「使用現有 Web 服務的步驟」。

## 共用活頁簿

如果您儲存您的活頁簿，則您加入的 Web 服務之 API 金鑰也會一併儲存。這表示您應該只與您信任的人共用活頁簿。

請在下方或我們的[論壇](http://go.microsoft.com/fwlink/?LinkID=403669&clcid=0x409)上提出任何問題。

[01]: ./media/machine-learning-excel-add-in-for-web-services/image1.png
[02]: ./media/machine-learning-excel-add-in-for-web-services/image2.png
[03]: ./media/machine-learning-excel-add-in-for-web-services/image3.png
[04]: ./media/machine-learning-excel-add-in-for-web-services/image4.png
[05]: ./media/machine-learning-excel-add-in-for-web-services/image5.png
[06]: ./media/machine-learning-excel-add-in-for-web-services/image6.png

<!---HONumber=September15_HO1-->