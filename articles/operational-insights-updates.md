<properties 
   pageTitle="使用系統更新來更新伺服器"
   description="了解如何使用 Microsoft Azure Operational Insights 中的系統更新智慧套件，協助您將遺失的更新套用到基礎結構中的伺服器。"
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/20/2015"
   ms.author="banders" />

# 使用系統更新來更新伺服器

您可以使用 Microsoft Azure Operational Insights 中的系統更新智慧套件，協助您將遺失的更新套用到基礎結構中的伺服器。您需要安裝智慧套件才能更新 Operational Insights 的 Operations Manager 代理程式和基底組態模組。更新資訊是在受監視伺服器上讀取，然後將更新資料傳送至雲端中的 Operational Insights 服務進行處理。會將邏輯套用至更新資料，且雲端服務會記錄資料。如果找到遺失的更新，它們會顯示在 [**更新**] 儀表板。您可以使用 [**更新**] 儀表板處理遺失的更新及開發計劃，將它們套用至所需要的伺服器。

## 使用系統更新來更新伺服器

在 Microsoft Azure Operational Insights 中使用系統更新之前，您必須安裝智慧套件。如需閱讀安裝智慧套件的更多資訊，請參閱[使用圖庫來新增或移除智慧套件](operational-insights-add-intelligence-packs.md). 安裝後，您可以檢視受監視伺服器缺少的更新，方法是在 Operational Insights 的 [**概觀**] 頁面上使用 [**系統更新評估**] 磚。 

![系統更新評估磚的影像](./media/operational-insights-updates/overview-update.png)

此磚會開啟 [**更新**] 儀表板，您可以在其中檢視遺失更新的整體摘要。此頁面詳細說明下列類別：

- 遺失安全性更新的伺服器

- 最近尚未更新的伺服器

- 應套用到特定伺服器的更新

- 遺失的更新類型

您可以在 [**搜尋**] 頁面中按一下任何磚或項目以檢視其詳細資料，即可取得遺失更新的詳細資訊。 

![更新儀表板的影像](./media/operational-insights-updates/gallery-sysupdate-01.png)

![更新儀表板的影像](./media/operational-insights-updates/gallery-sysupdate-02.png)

## 搜尋結果##
更新搜尋結果包括：

- 伺服器

- 更新標題

- 知識庫識別碼

- 更新適用的產品

- 更新嚴重性

- 發行日期

*伺服器* 搜尋結果包括：

- 伺服器名稱

- 作業系統版本名稱

- 自動更新啟用方法

- 上次更新後至今的天數

- Windows Update 代理程式版本

## 使用更新

1. 在 [**概觀**] 頁面上，按一下 [**系統更新評估**] 磚。

2. 在 [**更新**] 儀表板上，檢視更新類別並選擇其中一個要使用的類別。

3. 在 [**搜尋**] 頁面中，按一下磚或任何項目來檢視其詳細資訊。

4. 使用您已經找到的資訊，您可以建立計畫以套用遺失的更新。

<!--HONumber=52-->