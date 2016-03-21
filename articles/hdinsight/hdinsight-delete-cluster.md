<properties
pageTitle="如何刪除 HDInsight 叢集 | Azure"
description="您可用來刪除 HDInsight 叢集的各種方式相關資訊。"
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="paulettm"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="03/07/2016"
ms.author="larryfr"/>

#如何刪除 HDInsight 叢集

HDInsight 叢集是以小時計費，因此不再使用時，應該一律刪除您的叢集。在本文件中，您將了解如何使用 Azure 入口網站、Azure PowerShell 和 Azure CLI 來刪除叢集。

> [AZURE.IMPORTANT] 刪除 HDInsight 叢集並不會刪除與叢集相關聯的 Azure 儲存體帳戶。這可讓您保留及重複使用叢集所儲存的任何資料。

##Azure 入口網站

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後選取您的 HDInsight 叢集。如果您的 HDInsight 叢集並未釘選於儀表板上，您可以使用導覽列右側的搜尋欄位 (放大鏡圖示) 依名稱搜尋它。

    ![入口網站搜尋](./media/hdinsight-delete-cluster/navbar.png)

2. 當叢集的刀鋒視窗開啟之後，選取 [刪除] 圖示。出現提示時，選取 [是] 以刪除叢集。

    ![刪除圖示](./media/hdinsight-delete-cluster/deletecluster.png)

##Azure PowerShell

> [AZURE.NOTE] 如果您尚未安裝和設定 Azure PowerShell，可使用[安裝和設定 Azure PowerShell](../powershell-install-configure.md) 文件中的步驟。

在 PowerShell 提示中，使用下列命令來刪除叢集︰

    Remove-AzureRmHDInsightCluster -ClusterName CLUSTERNAME

將 __CLUSTERNAME__ 取代為 HDInsight 叢集的名稱。

##Azure CLI

> [AZURE.NOTE] 如果您尚未安裝和設定 Azure CLI，可使用[安裝和設定 Azure CLI](../xplat-cli-install.md) 文件中的步驟。

在提示中，使用下列命令來刪除叢集︰

    azure hdinsight cluster delete CLUSTERNAME
    
將 __CLUSTERNAME__ 取代為 HDInsight 叢集的名稱。

<!---HONumber=AcomDC_0309_2016-->