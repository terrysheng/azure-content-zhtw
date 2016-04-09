<properties 
	pageTitle="車輛遙測分析解決方案腳本 | Microsoft Azure" 
	description="使用 Cortana Analytics 具備的強大功能，取得關於車輛健全狀態與駕駛習慣的即時預測情資。" 
	services="machine-learning" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/04/2016" 
	ms.author="bradsev" />


# 車輛遙測分析解決方案腳本

此**功能表**連結至此腳本的章節。

[AZURE.INCLUDE [cap-vehicle-telemetry-playbook-selector](../../includes/cap-vehicle-telemetry-playbook-selector.md)]

## 概觀
讓超級電腦步出實驗室並停駐在我們的車庫！ 這些新銳汽車內含大量感應器，每秒可追蹤和監控數以百萬計的事件。預計在 2020 年前，在上述新銳汽車中，絕大部分的車輛都能連線至網際網路。想像一下使用此豐富資料提供頂級安全性、可靠性和駕駛體驗的美好願景。Microsoft 透過 Cortana Analytics 讓此一美好想像成真。

Microsoft Cortana Analytics 可完整管理的巨量資料與進階分析套件，讓您將資料轉化為智慧行動。我們在此為您介紹「Cortana Analytics 車輛遙測分析解決方案範本」。本解決方案將示範汽車經銷商、汽車製造商和保險公司如何運用 Cortana Analytics 功能，取得車輛健全狀態與駕駛習慣的即時預測情資。

此解決方案是以 [lambda 架構模式](https://en.wikipedia.org/wiki/Lambda_architecture)實作，完全展現 Cortana Analytics 平台在即時批次處理方面的優異潛力。其中包含「車輛遠程資訊服務」模擬器，可運用「事件中樞」擷取數以百萬計的模擬車輛遙測事件至 Azure，然後使用「串流分析」取得車輛健全狀態的即時情資，同時以長期儲存的方式保存這些資料，供日後進行更豐富廣泛的批次分析之用。其可運用「機器學習服務」，即時進行異常偵測與批次處理以取得預測情資。您可運用 HDInsight 大規模轉換資料，而 Data Factory 可執行批次處理管線的協調、排程、資源管理和監控工作。最後，Power BI 為此解決方案提供具備即時資料與預測性分析視覺效果等豐富功能的儀表板。

## 架構

![](./media/cortana-analytics-playbook-vehicle-telemetry/fig1-vehicle-telemetry-annalytics-solution-architecture.png) *圖 1 – 車輛遙測分析解決方案架構*

此解決方案包含下列 **Cortana Analytics 元件**，並展示端對端整合功能


- 「**事件中樞**」可將數百萬計的車輛遙測事件擷取至 Azure。
- 「**串流分析**」可取得關於車輛健全狀態的即時情資，同時以長期儲存的方式保存這些資料，供日後進行更豐富廣泛的批次分析之用。
- 「**機器學習服務**」可即時進行異常偵測與批次處理以取得預測情資。
- **HDInsight** 用於大規模的資料轉換
- **Data Factory** 可執行批次處理管線的協調、排程、資源管理和監控工作。
- **Power BI** 為此解決方案提供具備即時資料與預測性分析視覺效果等豐富功能的儀表板。

此解決方案可存取兩種不同的**資料來源**：

- **模擬車輛訊號和診斷**：針對指定時點的車輛狀態與駕駛模式，車輛遠程資訊服務模擬器可發出與其對應的診斷資訊和訊號。 
- **車輛目錄**：包含 VIN 模型對應的參考資料集。

<!---HONumber=AcomDC_0309_2016-->