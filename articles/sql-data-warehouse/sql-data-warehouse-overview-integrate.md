<properties
   pageTitle="使用 SQL 資料倉儲建置整合式解決方案 | Microsoft Azure"
   description="工具以及提供可與 SQL 資料倉儲整合之解決方案的合作夥伴。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/22/2015"
   ms.author="lodipalm"/>

#整合服務和 SQL 資料倉儲
除了其核心功能，SQL 資料倉儲可讓使用者搭配運用許多其他 Azure 中的服務。具體而言，我們目前已經採取步驟來深入整合下列項目：

+ Power BI
+ Azure Data Factory
+ Azure Machine Learning
+ Azure 串流分析

除此之外，我們正在設法進一步結合跨 Azure 生態系統的許多其他服務。

##Power BI
Power BI 整合可讓使用者大量運用 SQL 資料倉儲的計算能力以及 Power BI 的動態報告和視覺效果。與 Power BI 的整合目前包含：

+ **直接連接**：針對 SQL 資料倉儲進行之邏輯下推的更進階連接。這樣可允許更快速且較大規模的分析。
+ **在 Power BI 中開啟**：[在 Power BI 中開啟] 按鈕會傳遞執行個體資訊給 Power BI，允許更順暢的連接。 

如需詳細資訊，請參閱[整合 Power BI](../sql-data-warehouse-integrate-power-bi.md) 或 [Power BI 文件](http://blogs.msdn.com/b/powerbi/archive/2015/06/24/exploring-azure-sql-data-warehouse-with-power-bi.aspx)。

##Azure Data Factory
Azure Data Factory 提供使用者一個受管理的平台，以建立複雜的擷取-轉換-載入管線。SQL 資料倉儲與 Azure Data Factory 的整合包含下列內容：

+ **資料移動**：排程資料會跨數個內部部署和 azure 服務傳輸。
+ **預存程序**：協調 SQL 資料倉儲上的預存程序執行。

如需詳細資訊，請參閱[與 Azure Data Factory 整合](../sql-data-warehouse-integrate-azure-data-factory.md)或 [Azure Data Factory 文件](https://azure.microsoft.com/documentation/services/data-factory/)。

##Azure Machine Learning
Azure Machine Learning 是完全受管理的分析服務，可讓使用者建立運用一組大型預測工具的複雜模型。SQL 資料倉儲可支援做為模型的來源和目的地，具備下列功能：

+ **讀取資料：**磁碟機模型大規模針對 SQL 資料倉儲使用 T-SQL。 
+ **寫入資料：**認可從任何模型回到 SQL 資料倉儲的變更。

如需詳細資訊，請參閱[與 Azure Machine Learning 整合](../sql-data-warehouse-integrate-azure-machine-learning.md)或 [Azure Machine Learning 文件](https://azure.microsoft.com/services/machine-learning/)。

##Azure 串流分析
Azure 串流分析是複雜、完全受管理的基礎結構，可處理和取用產生自 Azure 事件中樞的事件資料。與 SQL 資料倉儲的整合可讓串流資料有效地處理並與相關資料一起儲存以啟用更深入、更進階的分析。

+ **工作輸出：**將來自串流分析工作的輸出直接傳送到 SQL 資料倉儲。

如需詳細資料，請參閱[與 Azure 串流分析整合](../sql-data-warehouse-integrate-azure-stream-analytics.md)或 [Azure 串流分析文件](https://azure.microsoft.com/documentation/services/stream-analytics/)。

<!--Image references-->

<!--Article references-->
[development overview]: sql-data-warehouse-overview-develop/

[Azure Data Factory]: sql-data-warehouse-integrate-azure-data-factory.md
[Azure Machine Learning]: sql-data-warehouse-integrate-azure-machine-learning.md
[Azure Stream Analytics]: sql-data-warehouse-integrate-azure-stream-analytics.md
[Power BI]: sql-data-warehouse-integrate-power-bi.md
[Partners]: sql-data-warehouse-integrate-solution-partners.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=July15_HO3-->