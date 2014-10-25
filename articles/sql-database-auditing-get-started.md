<properties title="Get started with SQL database auditing" pageTitle="Get started with SQL database auditing | Azure" description="Get started with SQL database auditing" metaKeywords="" services="sql-database" solutions="data-management" documentationCenter="" authors="jeffreyg" videoId="" scriptId=""  />

<tags ms.service="sql-database" ms.workload="data-management" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jeffreyg"></tags>

# 開始使用 SQL Database 稽核

Azure SQL Database 稽核會追蹤資料庫事件，並將稽核事件寫入 Azure 儲存體帳戶中的稽核記錄。Basic、Standard 和 Premium 服務層的預覽版提供稽核功能。若要使用稽核，請[註冊預覽版][]。

稽核可協助您保持法規遵循、了解資料庫活動，以及深入了解可指出商務考量或疑似安全違規的不一致和異常。

稽核工具啟用及推動遵循法規標準，但不保證符合法規。如需有關支援標準法規的 Azure 程式詳細資訊，請參閱 [Azure 信任中心][]。

-   [Azure SQL Database 稽核基本概念][]
-   [設定資料庫的稽核][]
-   [分析稽核記錄和報告][]

## <span id="subheading-1"></span>Azure SQL Database 稽核基本概念</a>

您可以在 Azure 預覽入口網站中設定稽核，這和您是使用 Azure 入口網站或 Azure 預覽入口網站建立資料庫無關。SQL Database 稽核可讓您：

-   **保留**所選事件的稽核記錄。定義要記錄的資料庫動作和事件類別。
-   **報告**資料庫活動。使用預先設定的報告和儀表板，以便快速開始使用活動和事件報告。
-   **分析**報告。尋找可疑事件、異常活動及趨勢。

您可以稽核下列活動和事件：

-   **存取資料**
-   **結構描述變更 (DDL)**
-   **資料變更 (DML)**
-   **帳戶、角色和權限 (DCL)**
-   **安全性例外狀況**

如需已記錄活動和事件的進一步資訊，請參閱[稽核記錄格式參考 (doc 檔案下載)][]。

您也可以選擇將儲存稽核記錄的儲存體帳戶。

### 已啟用安全性的連接字串

設定稽核時，Azure 會為您提供已啟用安全性的資料庫連接字串。只有使用此連接字串的用戶端應用程式會被記錄其活動和事件，因此您必須更新現有的用戶端應用程式才能使用此新字串格式。

傳統的連接字串格式：\<*server name*\>.database.windows.net

已啟用安全性的連接字串：\<*server name*\>.database.**secure**.windows.net

## <span id="subheading-2"></span></a>設定資料庫的稽核

1.  [註冊稽核預覽版][註冊預覽版]。
2.  另請務必[註冊新服務層的預覽版][]，並建立 Basic、Standard 或 Premium 資料庫。
3.  啟動 [Azure 預覽入口網站][]，位址是 <https://portal.azure.com>。
4.  按一下您要稽核的資料庫，然後按一下 **[Auditing Preview]**，以啟用稽核預覽版並啟動稽核組態分頁。

    ![][]

5.  在稽核組態分頁中，選取將儲存記錄的 Azure 儲存體帳戶。**秘訣：** 在所有稽核的資料庫中使用相同的儲存體帳戶，以充分利用預先設定的報告範本。

    ![][1]

6.  在 **[Auditing Options]** 下，按一下 [全部] 以記錄所有事件，或選擇個別事件類型。

    ![][2]

7.  如果您要將這些設定套用到伺服器上的所有未來資料庫以及尚未設定稽核的資料庫，請勾選 **[Save this Configuration as Default]**。您稍後可以依照下列相同步驟，來為每個資料庫覆寫設定。

    ![][3]

8.  按一下 **[Show database connection strings]**，然後為您的應用程式複製或記下適當的已啟用安全性連接字串。在任何您想稽核其活動的用戶端應用程式中使用此字串。

    ![][4]

9.  按一下 **[確定]**。

## <span id="subheading-3"></span>分析稽核記錄和報告</a>

稽核記錄會在安裝期間所選擇的 Azure 儲存體帳戶中、名為 **AuditLogs** 的單一 Azure 儲存體資料表內進行彙總。您可以使用工具 (例如 [Azure 儲存體總管][]) 來檢視記錄檔。

預先設定的儀表板報告範本會以[可下載的 Excel 試算表][]形式提供，以協助您快速分析記錄資料。若要在稽核記錄上使用範本，您需要 Excel 2013 (或更新版本) 和 Power Query (您可從[此處][]下載)。

範本中包含虛構的範例資料，您可以設定 Power Query 直接從 Azure 儲存體帳戶匯入稽核記錄。

如需有關使用報告範本的詳細指示，請閱讀[作法 (doc 下載)][]。

![][5]

<!--Anchors-->
  [註冊預覽版]: http://go.microsoft.com/fwlink/?LinkId=404163
  [Azure 信任中心]: http://azure.microsoft.com/en-us/support/trust-center/compliance/
  [Azure SQL Database 稽核基本概念]: #subheading-1
  [設定資料庫的稽核]: #subheading-2
  [分析稽核記錄和報告]: #subheading-3
<!--Image references-->
  [稽核記錄格式參考 (doc 檔案下載)]: http://go.microsoft.com/fwlink/?LinkId=506733
  [註冊新服務層的預覽版]: https://account.windowsazure.com/PreviewFeatures?fid=premiumdb
  [Azure 預覽入口網站]: https://portal.azure.com
  []: ./media/sql-database-auditing-get-started/sql-database-get-started-auditingpreview.png
  [1]: ./media/sql-database-auditing-get-started/sql-database-get-started-storageaccount.png
  [2]: ./media/sql-database-auditing-get-started/sql-database-auditing-eventtype.png
  [3]: ./media/sql-database-auditing-get-started/sql-database-get-started-saveconfigasdefault.png
  [4]: ./media/sql-database-auditing-get-started/sql-database-get-started-connectionstring.png
  [Azure 儲存體總管]: http://azurestorageexplorer.codeplex.com/
  [可下載的 Excel 試算表]: http://go.microsoft.com/fwlink/?LinkId=403540
  [此處]: http://www.microsoft.com/en-us/download/details.aspx?id=39379
  [作法 (doc 下載)]: http://go.microsoft.com/fwlink/?LinkId=506731
  [5]: ./media/sql-database-auditing-get-started/sql-database-auditing-dashboard.png
<!--Link references-->