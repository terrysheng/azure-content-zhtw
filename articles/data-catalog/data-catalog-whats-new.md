<properties
   pageTitle="Azure 資料目錄的新功能"
   description="概略介紹 Azure 資料目錄預覽版本中的新功能。"
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="09/11/2015"
   ms.author="maroche"/>

# Azure 資料目錄的新功能

**Azure 資料目錄**的更新會定期發行。並非所有發行版本都會包含供使用者使用的新功能，某些版本著重在後端服務功能。本頁將特別強調已加入 **Azure 資料目錄**服務的新使用者功能。


## 2015 年 9 月 11 日當週發行的新功能

自 2015 年 9 月 11 日當週起，**Azure 資料目錄**已加入下列功能：

- 支援透過 HTTP/HTTPS 註冊和探索 SQL Server Analysis Servics 物件。使用者現在可使用 URL (例如 https://servername/olap/msmdpump.dll) 連線至 SSAS 伺服器，而無須使用伺服器名稱，且可使用 Windows 驗證以外的「基本」驗證與「匿名」連接。如需 HTTP/HTTPS 連接至 SSAS 的其他資訊，請參閱[設定 Analysis Services 的 HTTP 存取](https://msdn.microsoft.com/library/gg492140.aspx)。
- 針對 HDInsight 提供 Hive 資料來源支援。使用者現可在 HDInsight 資料來源的 Hadoop 中，註冊並探索適用於 Apache Hive 的 Hive 資料表。如需有關 HDInsight 上 Hive 的其他資訊，請參閱 [HDInsight 文件中心](../hdinsight-use-hive/)。
- 支援註冊與探索 Oracle 資料庫及 HDFS 叢集以做為容器。註冊 Oracle 資料表和檢視或 HDFS 時，**Azure 資料目錄**會建立資料庫以及資料表和檢視的項目。您可使用 **Azure 資料目錄**入口網站探索資料庫，並為其加上註解。除了搜尋和篩選目錄內容之外，使用者亦可搜尋和篩選資料庫或叢集的內容。


> [AZURE.NOTE]針對 9 月 11 日前註冊的 Oracle 資料表和檢視以及 HDFS 檔案和目錄，必須在資料庫或叢集項目加入目錄之前，使用資料來源註冊工具重新加以註冊。重新註冊資料來源並不會影響使用者在 **Azure 資料目錄**入口網站所加入的任何註解。

## 2015 年 9 月 4 日當週發行的新功能

自 2015 年 9 月 4 日當週起，**Azure 資料目錄**已加入下列功能：

- 支援手動註冊未知的資料來源類型。使用者可透過 **Azure 資料目錄**入口網站手動輸入資料來源資訊，如此即可將資料來源註冊工具未明確支援的資料來源加上註解並加以探索。
- 支援註冊與探索 SQL Server 資料庫以做為容器。註冊 SQL Server 資料表和檢視時，**Azure 資料目錄**會針對資料庫以及資料表和檢視建立項目。您可使用 **Azure 資料目錄**入口網站探索資料庫，並為其加上註解。除了搜尋和篩選目錄的內容之外，使用者也可以搜尋和篩選資料庫的內容。

> [AZURE.NOTE]針對 9 月 4 日前註冊的 SQL Server 資料表和檢視，必須在資料庫項目加入目錄之前，使用資料來源註冊工具重新加以註冊。重新註冊資料來源並不會影響使用者在 **Azure 資料目錄**入口網站所加入的任何註解。

## 2015 年 8 月 28 日當週發行的新功能

自 2015 年 8 月 28 日當週起，**Azure 資料目錄**已加入下列功能：

- 支援 SQL Server 和 Oracle 資料來源的資料分析。在註冊 SQL Server 和 Oracle 資料表和檢視時，使用者可以選擇要包含正在註冊之物件的資料設定檔資訊。資料設定檔包含物件層級與資料行層級的統計資料。
- 支援 Hadoop HDFS 資料來源。使用者現在可以註冊並探索 HDFS 檔案和目錄。

## 2015 年 8 月 21 日當週發行的新功能

自 2015 年 8 月 21 日當週起，**Azure 資料目錄**已加入下列功能：

- 支援針對註冊的資料來源提供存取要求資訊。使用者現在可以針對任何已註冊的資料資產，提供要求存取權的指令 (包括電子郵件連結或 URL)，以輕鬆整合現有工具和程序。
- 標籤和專家的工具提示，讓您可以更輕鬆探索哪些使用者已為註冊的資料資產提供何種中繼資料。
- 我們已將新的「使用者」按鈕和功能表加入我們的上方導覽列中。此功能表可讓使用者查看用來登入 **Azure 資料目錄**，以及在必要時登出的帳戶。此功能表亦會顯示目錄名稱，這可為使用 **Azure 資料目錄** REST API 的開發人員提供實用資訊。
- 僅限標準版：現在將擁有者新增至資料資產時，**Azure 資料目錄**可同時支援以使用者帳戶和安全性群組做為擁有者。若要將安全性群組加入並使其成為所選資料資產的擁有者，您可以輸入群組的顯示名稱或群組的 UPN 電子郵件地址 (如果有的話)。
- 支援 Azure Blob 儲存體的資料來源。使用者現在可以註冊並探索 Azure 儲存體 Blob 和目錄。

<!---HONumber=Sept15_HO3-->