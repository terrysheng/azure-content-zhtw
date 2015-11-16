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
   ms.date="10/30/2015"
   ms.author="maroche"/>

# Azure 資料目錄的新功能

**Azure 資料目錄**的更新會定期發行。並非所有發行版本都會包含供使用者使用的新功能，某些版本著重在後端服務功能。本頁將特別強調已加入 **Azure 資料目錄**服務的新使用者功能。

## 2015 年 10 月 30 日當週發行的新功能

自 2015 年 10 月 30 日當週起，**Azure Data Catalog** 已新增下列功能：

- 支援已註冊資料來源的資料預覽和資料設定檔的靜止狀態加密。Azure Data Catalog 會通透地加密已向服務註冊的資料來源的任何預覽記錄和資料設定檔，不需要目錄系統管理員管理金鑰。

## 2015 年 10 月 23 日當週發行的新功能

自 2015 年 10 月 23 日當週起，**Azure Data Catalog** 已新增下列功能：

- 支援 Teradata 資料來源。使用者現在可以註冊並探索 Teradata 資料表和檢視。

> [AZURE.NOTE]目前版本中，僅支援 Teradata TD2 驗證。在未來發行的版本中將支援更多驗證機制。

## 2015 年 10 月 16 日當週發行的新功能

自 2015 年 10 月 16 日當週起，**Azure Data Catalog** 已新增下列功能：

- 支援內部部署 Hive 資料來源。使用者現可在內部部署資料來源的 Hadoop 中，註冊並探索適用於 Apache Hive 的 Hive 資料表。
- 支援 **Azure Data Catalog** 入口網站中已儲存的搜尋。使用者可以儲存的搜尋詞彙和篩選器選取項目，而輕鬆地重複先前的搜尋，及定義目錄內容的實用檢視。使用者也可以將已儲存的搜尋標示為其預設搜尋。當使用者從 **Azure Data Catalog** 入口網站首頁或從 [入門] 頁面中按一下「放大鏡」搜尋圖示時，使用者將會直接進入標示為預設值的已儲存搜尋。


## 2015 年 10 月 9 日當週發行的新功能

自 2015 年 10 月 9 日當週起，**Azure Data Catalog** 已新增下列功能：

- 支援 Azure Data Catalog 入口網站中已註冊之資料資產和容器的 RTF 文件。對於標籤和描述不足的案例，使用者現在可以提供資料資產的文件，例如資料表、檢視和報告，以及提供容器的文件，例如資料庫和模型。

## 2015 年 10 月 2 日當週發行的新功能

自 2015 年 10 月 2 日當週起，**Azure Data Catalog** 已新增下列功能：

- 支援手動註冊已知的資料來源類型。對於 **Azure Data Catalog** 支援的所有資料來源類型，使用者可以使用 **Azure Data Catalog** 入口網站手動輸入資料來源資訊。
- 支援授權 Azure Active Directory 安全性群組。目錄系統管理員可以讓您存取安全性群組以及使用者帳戶的目錄，使得您易於管理 **Azure Data Catalog** 的存取。
- 支援透過 **Azure Data Catalog** 入口網站在 Excel 中開啟 Hive 資料來源。

> [AZURE.NOTE]若要對 Hive 資料來源使用「在 Excel 中開啟」功能，使用者必須已安裝 Hive 適用的 ODBC 驅動程式。

## 2015 年 9 月 25 日當週發行的新功能

自 2015 年 9 月 25 日當週起，**Azure Data Catalog** 已新增下列功能：

- 在登錄 Hive 資料來源時納入資料設定檔的支援。
- 支援以程式設計方式探索目錄 API，以便應用程式與 **Azure Data Catalog** 整合。

## 2015 年 9 月 18 日當週發行的新功能

自 2015 年 9 月 18 日當週起，**Azure Data Catalog** 已新增下列功能：

- 在 **Azure Data Catalog** 入口網站中新的「入門」資料來源探索體驗。當使用者進入 **Azure Data Catalog** 入口網站的「探索」頁面而未輸入搜尋詞彙時，它們將會使用目錄內容的概觀來呈現，包括最常使用的標記，以及專家、資料來源類型及物件類型。
- 支援註冊與探索 Azure SQL 資料倉儲物件和資料庫。如需 Azure SQL 資料倉儲的詳細資訊，請參閱 [SQL 資料倉儲](http://azure.microsoft.com/services/sql-data-warehouse/)。
- 支援將 SQL Server Analysis Services 模型和 SQL Server Reporting Services 伺服器做為容器來進行註冊與探索。註冊 SSAS 和 SSRS 物件時，**Azure Data Catalog** 將針對 SSAS 模型和 SSRS 伺服器以及報告和其他物件建立項目。您可以使用 **Azure Data Catalog** 入口網站來探索容器，並為其加上註解。除了搜尋和篩選目錄的內容之外，使用者也可以搜尋和篩選模型或伺服器的內容。

> [AZURE.NOTE]針對已在 9 月 18 日前註冊的 SSAS 和 SSRS 物件，必須在模型或伺服器項目加入目錄之前，使用資料來源註冊工具重新加以註冊。重新註冊資料來源並不會影響使用者在 **Azure Data Catalog** 入口網站所新增的任何註解。

## 2015 年 9 月 11 日當週發行的新功能

自 2015 年 9 月 11 日當週起，**Azure Data Catalog** 已新增下列功能：

- 支援透過 HTTP/HTTPS 註冊和探索 SQL Server Analysis Servics 物件。使用者現在可以使用 URL (例如 https://servername/olap/msmdpump.dll) 連線至 SSAS 伺服器，而無需使用伺服器名稱，且可使用 Windows 驗證以外的基本驗證與匿名連線。如需 HTTP/HTTPS 連線至 SSAS 的其他資訊，請參閱[設定 Analysis Services 的 HTTP 存取](https://msdn.microsoft.com/library/gg492140.aspx)。
- 針對 HDInsight 提供 Hive 資料來源支援。使用者現可在 HDInsight 資料來源的 Hadoop 中，註冊並探索適用於 Apache Hive 的 Hive 資料表。如需 HDInsight 上 Hive 的其他資訊，請參閱 [HDInsight 文件中心](../hdinsight-use-hive/)。
- 支援註冊與探索 Oracle 資料庫及 HDFS 叢集以做為容器。註冊 Oracle 資料表和檢視或 HDFS 時，**Azure Data Catalog** 將會建立資料庫以及資料表和檢視的項目。您可以使用 **Azure Data Catalog** 入口網站來探索資料庫，並為其加上註解。除了搜尋和篩選目錄內容之外，使用者亦可搜尋和篩選資料庫或叢集的內容。


> [AZURE.NOTE]針對 9 月 11 日前註冊的 Oracle 資料表和檢視以及 HDFS 檔案和目錄，必須在資料庫或叢集項目加入目錄之前，使用資料來源註冊工具重新加以註冊。重新註冊資料來源並不會影響使用者在 **Azure Data Catalog** 入口網站所加入的任何註解。

## 2015 年 9 月 4 日當週發行的新功能

自 2015 年 9 月 4 日當週起，**Azure Data Catalog** 已新增下列功能：

- 支援手動註冊未知的資料來源類型。使用者可透過 **Azure Data Catalog** 入口網站手動輸入資料來源資訊，如此即可為資料來源註冊工具未明確支援的資料來源加上註解並加以探索。
- 支援註冊與探索 SQL Server 資料庫以做為容器。註冊 SQL Server 資料表和檢視時，**Azure Data Catalog** 會針對資料庫以及資料表和檢視建立項目。您可以使用 **Azure Data Catalog** 入口網站來探索資料庫，並為其加上註解。除了搜尋和篩選目錄的內容之外，使用者也可以搜尋和篩選資料庫的內容。

> [AZURE.NOTE]針對 9 月 4 日前註冊的 SQL Server 資料表和檢視，必須在資料庫項目加入目錄之前，使用資料來源註冊工具重新加以註冊。重新註冊資料來源並不會影響使用者在 **Azure Data Catalog** 入口網站所新增的任何註解。

## 2015 年 8 月 28 日當週發行的新功能

自 2015 年 8 月 28 日當週起，**Azure Data Catalog** 已新增下列功能：

- 支援 SQL Server 和 Oracle 資料來源的資料分析。在註冊 SQL Server 和 Oracle 資料表和檢視時，使用者可以選擇要包含正在註冊之物件的資料設定檔資訊。資料設定檔包含物件層級與資料行層級的統計資料。
- 支援 Hadoop HDFS 資料來源。使用者現在可以註冊並探索 HDFS 檔案和目錄。

## 2015 年 8 月 21 日當週發行的新功能

自 2015 年 8 月 21 日當週起，**Azure Data Catalog** 已新增下列功能：

- 支援針對註冊的資料來源提供存取要求資訊。使用者現在可以針對任何已註冊的資料資產，提供要求存取權的指令 (包括電子郵件連結或 URL)，以輕鬆整合現有工具和程序。
- 標籤和專家的工具提示，讓您可以更輕鬆探索哪些使用者已為註冊的資料資產提供何種中繼資料。
- 我們已將新的「使用者」按鈕和功能表加入我們的上方導覽列中。此功能表可讓使用者查看用來登入 **Azure Data Catalog** 的帳戶，以及在必要時登出。此功能表也會顯示目錄名稱，這可為使用 **Azure Data Catalog** REST API 的開發人員提供實用資訊。
- 僅限標準版：在將擁有者新增至資料資產時，**Azure Data Catalog** 現在可支援同時讓使用者帳戶和安全性群組做為擁有者。若要將安全性群組加入並使其成為所選資料資產的擁有者，您可以輸入群組的顯示名稱或群組的 UPN 電子郵件地址 (如果有的話)。
- 支援 Azure Blob 儲存體的資料來源。使用者現在可以註冊並探索 Azure 儲存體 Blob 和目錄。

<!---HONumber=Nov15_HO2-->