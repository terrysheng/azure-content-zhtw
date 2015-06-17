<properties 
	pageTitle="開始使用 SQL Database 稽核 | Azure" 
	description="開始使用 SQL Database 稽核" 
	services="sql-database" 
	documentationCenter="" 
	authors="jeffgoll" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/23/2015" 
	ms.author="jeffreyg"/>
 
# 開始使用 SQL Database 稽核 
<p> Azure SQL Database 稽核會追蹤資料庫事件，並將稽核事件寫入 Azure 儲存體帳戶中的稽核記錄。Basic、Standard 和 Premium 服務層皆提供稽核功能。

稽核可協助您保持法規遵循、了解資料庫活動，以及深入了解可指出商務考量或疑似安全違規的不一致和異常。 

稽核工具啟用及推動遵循法規標準，但不保證符合法規。如需有關支援標準法規的 Azure 程式詳細資訊，請參閱 <a href="http://azure.microsoft.com/support/trust-center/compliance/" target="_blank">Azure 信任中心</a>。

+ [Azure SQL Database 稽核基本概念] 
+ [設定資料庫的稽核]
+ [分析稽核記錄和報告]

##<a id="subheading-1"></a>Azure SQL Database 稽核基本概念

您可以在 Azure 預覽入口網站中設定稽核，這和您是使用 Azure 入口網站或 Azure 預覽入口網站建立資料庫無關。SQL Database 稽核可讓您：

- **保留**所選事件的稽核記錄。定義要記錄的資料庫動作和事件類別。
- **報告**資料庫活動。使用預先設定的報告和儀表板，以便快速開始使用活動和事件報告。
- **分析**報告。尋找可疑事件、異常活動及趨勢。

您可以稽核下列活動和事件：

- **存取資料**
- **結構描述變更 (DDL)**
- **資料變更 (DML)**
- **帳戶、角色和權限 (DCL)**
- **安全性例外狀況**

如需記錄的活動和事件相關的詳細資訊，請參閱 <a href="http://go.microsoft.com/fwlink/?LinkId=506733" target="_blank">稽核記錄格式參照 (doc 檔案下載)</a>。 

您也可以選擇將儲存稽核記錄的儲存體帳戶。

###已啟用安全性的連接字串
設定稽核時，Azure 會為您提供已啟用安全性的資料庫連接字串。只有使用此連接字串的用戶端應用程式會被記錄其活動和事件，因此您必須更新現有的用戶端應用程式才能使用此新字串格式。

傳統的連接字串格式：<*伺服器名稱*>.database.windows.net

已啟用安全性的連接字串：<*伺服器名稱*>.database.**secure**.windows.net


##<a id="subheading-2"></a>設定資料庫的稽核

1. 啟動 <a href="https://portal.azure.com" target="_blank">Azure 預覽入口網站</a> (https://portal.azure.com)。另外，您也可以啟動 <a href= "https://manage.windowsazure.com/" target="_bank">傳統的 Azure 入口網站</a> (https://manage.windowsazure.com/)。請參閱下列詳細資訊。
2. 瀏覽至您要稽核的資料庫組態分頁。向下捲動至 [**作業**] 區段，然後按一下 [**稽核**] 啟用稽核並啟動稽核組態分頁。

	![][1]

3. 在稽核組態分頁中，選取將儲存記錄的 Azure 儲存體帳戶。**提示：**在所有稽核的資料庫中使用相同的儲存體帳戶，以充分利用預先設定的報告範本。

	![][2]

4. 在 [**稽核選項**] 下，按一下 [**全部**] 以記錄所有事件，或選擇個別事件類型。

	![][3]

5. 如果您要將這些設定套用到伺服器上的所有未來資料庫以及尚未設定稽核的資料庫，請勾選 [**Save these settings as server default**]。您稍後可以依照下列相同步驟，來為每個資料庫覆寫設定。 

6. 按一下 [**Show database connection strings**]，然後為您的應用程式複製或記下適當的已啟用安全性連接字串。在任何您想稽核其活動的用戶端應用程式中使用此字串。

	![][5]

7. 按一下 [**確定**]。



##<a id="subheading-3">分析稽核記錄和報告</a>

稽核記錄會在安裝期間所選擇的 Azure 儲存體帳戶中、名為 **AuditLogs** 的單一 Azure 儲存體資料表內進行彙總。您可以使用工具來檢視記錄檔，例如 <a href="http://azurestorageexplorer.codeplex.com/" target="_blank">Azure 儲存體總管</a>。

預先設定的儀表板報告範本為 <a href="http://go.microsoft.com/fwlink/?LinkId=403540" target="_blank">可下載的 Excel 試算表，</a> 能夠讓您快速分析記錄檔資料。若要在稽核記錄上使用範本，您需要 Excel 2013 (或更新版本) 和 Power Query (您可從 <a href="http://www.microsoft.com/download/details.aspx?id=39379">此處下載)</a>。 

範本中包含虛構的範例資料，您可以設定 Power Query 直接從 Azure 儲存體帳戶匯入稽核記錄。 

如需有關使用報告範本的詳細指示，請閱讀 <a href="http://go.microsoft.com/fwlink/?LinkId=506731">作法 (doc 下載)</a>。

![][6]


##<a id="subheading-4"></a>使用傳統的 Azure 入口網站設定資料庫的稽核

1. 啟動 <a href= "https://manage.windowsazure.com/" target="_bank">傳統的 Azure 入口網站</a> (https://manage.windowsazure.com/)。 
2. 按一下要稽核的資料庫，然後按一下 [**Auditing & Secuirity Preview**] 索引標籤。
3. 在稽核區段中，按一下 [啟用]。

![][7]

4. 視需要編輯 [**EVENT TYPE**]。

![][8]

5. 選取 [**STORAGE ACCOUNT**]。
6. 按一下 [**儲存**]。
7. 針對連接字串按一下 [**Show secured connection string**]。


##<a id="subheading-3">生產環境中的實務使用方式</a>
本節的說明與以上的畫面截圖有關。無論是 <a href="https://portal.azure.com" target="_blank">Azure 預覽入口網站</a> 或 <a href= "https://manage.windowsazure.com/" target="_bank">傳統的 Azure 入口網站</a> 均可使用。
 

##<a id="subheading-4"></a>啟用安全性的存取

在生產中，您可能需要稽核從所有應用程式和工具到資料庫的所有流量。為此，請將 [**啟用安全性的存取**] 從 *Optional* 修改為 *Required*，並儲存該原則。設定為 *Required* 後，便無法選擇透過原始連線字串存取資料庫，只能透過啟用安全性的連線字串。


![][9]


##<a id="subheading-4"></a>儲存體金鑰產生

在生產中，您可能會定期重新整理儲存體金鑰。稽核服務不會保留您的儲存體帳戶金鑰。儲存時，會對於稽核資料表產生僅寫入共用存取簽章 (SAS) 金鑰 (只有客戶能讀取稽核記錄)。因此，重新整理金鑰時，您需要重新儲存該原則。程序如下：


1. 在稽核組態分頁中 (如以上的設定稽核小節所述)，將 [**儲存體存取金鑰**] 從 *Primary* 切換為 *Secondary*，並按 [**儲存**]。
![][10]
2. 進入儲存體組態分頁，並**重新產生** *Primary Access Key*。

3. 返回稽核組態分頁，並且將 [**儲存體存取金鑰**] 從 *Secondary* 切換為 *Primary*，然後按 [**儲存**]。

4. 返回儲存體 UI，並**重新產生** *Secondary Access Key* (為了下一個金鑰重新整理週期做準備。
  
##<a id="subheading-4"></a>自動化
有數個 PowerShell 指令程式可用來設定 Azure SQL Database 中的稽核。若要存取稽核 Cmdlet，您必須在 [Azure 資源管理員] 模式中執行 PowerShell。

> [AZURE.NOTE] AzureResourceManager 模組目前只能預覽。它可能沒有如 Azure 模組所提供的相同管理功能。

 [Azure 資源管理員](https://msdn.microsoft.com/library/dn654592.aspx) 模式可透過執行 Switch-AzureMode Cmdlet (`Switch-AzureMode AzureResourceManager`) 進行存取。在 [Azure 資源管理員] 模式中，您可以執行 `Get-Command *AzureSql*` 來列出可用的 Cmdlet。







<!--Anchors-->
[Azure SQL Database 稽核基本概念]: #subheading-1
[設定資料庫的稽核]: #subheading-2
[分析稽核記錄和報告]: #subheading-3
[使用傳統的 Azure 入口網站設定資料庫的稽核]: #subheading-4


<!--Image references-->
[1]: ./media/sql-database-auditing-get-started/sql-database-get-started-auditingpreview.png
[2]: ./media/sql-database-auditing-get-started/sql-database-get-started-storageaccount.png
[3]: ./media/sql-database-auditing-get-started/sql-database-auditing-eventtype.png
[5]: ./media/sql-database-auditing-get-started/sql-database-get-started-connectionstring.png
[6]: ./media/sql-database-auditing-get-started/sql-database-auditing-dashboard.png
[7]: ./media/sql-database-auditing-get-started/sql-database-auditing-classic-portal-enable.png
[8]: ./media/sql-database-auditing-get-started/sql-database-auditing-classic-portal-configure.png
[9]: ./media/sql-database-auditing-get-started/sql-database-auditing-security-enabled-access.png
[10]: ./media/sql-database-auditing-get-started/sql-database-auditing-storage-account.png






<!--Link references-->
[其他 azure.microsoft.com 文件主題的連結 1]: ../virtual-machines-windows-tutorial/
[其他 azure.microsoft.com 文件主題的連結 2]: ../web-sites-custom-domain-name/
[其他 azure.microsoft.com 文件主題的連結 3]: ../storage-whatis-account/


<!--HONumber=47-->
 