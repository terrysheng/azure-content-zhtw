<properties
	pageTitle="適用於機器學習 Web 服務的 Excel 增益集 | Microsoft Azure"
	description="如何在 Excel 中直接存取 Azure 機器學習 Web 服務，而不需要撰寫任何程式碼。"
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
	ms.date="08/10/2015"
	ms.author="tedway;garye" />

# 適用於 Azure 機器學習 Web 服務的 Excel 增益集

Excel 可以讓您直接輕鬆呼叫 Web 服務，而不需要撰寫任何程式碼。

## 使用現有 Web 服務的步驟

1. 從這裡或從 Web 服務 [儀表板] 索引標籤開啟範例 Excel 檔案。
2. 按一下 Web 服務加以選擇，在此範例中為「鐵達尼號存活者預測工具 (Excel 增益集範例) [分數]」。

    ![選取 Web 服務][01]

3. 這將帶領您到 [預測] 區段。在 Excel 中選取資料格，然後按一下 [使用範例資料]。(選擇性) 編輯 Excel 中的這份資料。

	![預測區段][02]

4. 反白顯示此資料並按一下輸入的資料範圍圖示。
5. 在 [輸出] 輸入您想要輸出的資料格數目。
6. 按一下 [預測]。

## 加入新的 Web 服務的步驟

1. 發佈 Web 服務 ([本頁](machine-learning-walkthrough-5-publish-web-service.md) 會說明做法) 或尋找現有的 Web 服務。
2. 有了 Web 服務之後，請按一下 Machine Learning Studio 左窗格的 [Web 服務] 區段，然後選取要使用的 Web 服務。
3. 從該 Web 服務 [儀表板] 索引標籤上，複製 Web 服務的 API 金鑰。
4. 在 Excel 中，移至 [Web 服務] 區段 (如果您是在 [預測] 區段中，請按一下 [向後鍵] 前往 Web 服務的清單)。

	![Web 服務區段][03]

5. 按一下 [新增 Web 服務]。
6. 將此金鑰貼到標示為 [API 金鑰] 的文字方塊中。
7. 在 Web 服務的 [儀表板] 索引標籤上，按一下 [API 說明頁面] 連結。
8. 複製 [要求] 底下的要求 URI，並貼到標示為 [URL] 的文字方塊中。
9. 按一下 [新增]。

	![URL 和 API 金鑰][04]

10.	若要使用 Web 服務，請遵循上述指示「使用現有 Web 服務的步驟」。

## 共用活頁簿

如果您儲存您的活頁簿，則您加入的 Web 服務之 API 金鑰也會一併儲存。這表示您應該只與您信任的人共用活頁簿。


[01]: ./media/machine-learning-excel-add-in-for-web-services/image1.png
[02]: ./media/machine-learning-excel-add-in-for-web-services/image2.png
[03]: ./media/machine-learning-excel-add-in-for-web-services/image3.png
[04]: ./media/machine-learning-excel-add-in-for-web-services/image4.png

<!---HONumber=August15_HO8-->