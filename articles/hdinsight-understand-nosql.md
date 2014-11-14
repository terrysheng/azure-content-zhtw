<properties urlDisplayName="Understand NoSQL technologies" pageTitle="了解 Azure 上的 NoSQL 技術 | Azure" metaKeywords="" description="了解 HDInsight 上的 NoSQL 技術如何協助您管理不適用於關聯式資料庫的資料，例如巨量資料集和 JSON 文件或圖形。" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="storage,hdinsight" documentationCenter="" title="資料管理：了解 Azure 上的 NoSQL 技術" authors="dchappell" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="dchappell" />

# 資料管理：了解 Azure 上的 NoSQL 技術

**摘要：** Azure 提供許多選項來處理 NoSQL 工作負載，但可能不容易決定何者適用於您。本文說明 Azure 上的 NoSQL 技術，以及如何選擇正確的服務來管理非關聯式資料，包括非結構化或特別大的資料集 (通常稱為「巨量資料」)。

關聯式技術長久以來都是主要的資料處理方法。不過，在處理非常大量的不同資料類型時，就不容易擴充到許多伺服器。另外，關聯式技術並非最適合用來管理某些資料類型，例如 JSON 文件或圖形。本指南逐步解說這些選項，說明每個選項的用途及為何需要使用它。

**目錄：**

-   Azure 上的資料：概觀
-   Azure 關聯式技術：快速概覽
-   Azure NoSQL 技術：作業資料

    -   索引鍵/值存放區
    -   資料行系列存放區
    -   文件存放區
    -   圖形資料庫
-   Azure NoSQL 技術：分析資料

    -   Hadoop MapReduce
    -   Oozie 協調器

**作者：** David Chappell, Principal of Chappell & Associates

**發行日期：**2013 年 11 月

**修訂：** 1.0

**下載：** [了解 Azure 上的 NoSQL 技術 PDF 檔案][了解 Azure 上的 NoSQL 技術 PDF 檔案]

  [了解 Azure 上的 NoSQL 技術 PDF 檔案]: http://go.microsoft.com/fwlink/p/?LinkId=330292
