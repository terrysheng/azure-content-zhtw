<properties
   pageTitle="Azure 資料目錄版本資訊"
	description="Azure 資料目錄 2015 年 8 月 28 日公開預覽版本的版本資訊。"
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
	ms.date="08/28/2015"
	ms.author="maroche"/>

# Azure 資料目錄版本資訊

## Azure 資料目錄 2015 年 8 月 28 日版本注意事項

### 某些已註冊資料資產之遺漏的資料設定檔

藉由在資料來源註冊工具中已選取的資料分析選項，用以註冊資料來源時，在下列情況下，資料設定檔資訊可能未包含在內：

* Azure SQL Database 資料表
* SQL Server 資料表和檢視，其中有多個物件，在不同的結構描述中名稱相同。
* SQL Server 資料表和檢視，其具有大於 118 個字元的資料行名稱。
* Oracle 資料表和檢視，其具有大於 20 個字元的資料行名稱。
* Oracle 資料表和檢視，其資料行名稱使用空格或多位元組字元。

這些限制起因於 8 月 28 日版本的已知問題，而且將在未來的 Azure 資料目錄更新中解決。

## Azure 資料目錄 2015 年 7 月 13 日版本注意事項

### 註冊並連接至 Oracle 資料庫

連接到 Oracle 資料庫資料來源時，使用者必須已安裝正確的 Oracle 驅動程式，符合所使用軟體的位元 (32 位元或 64 位元)。

-	在執行 32 位元 Windows 的電腦上註冊 Oracle 資料來源時，將使用 32 位元 Oracle 驅動程式
-	在執行 64 位元 Windows 的電腦上註冊 Oracle 資料來源時，將使用 64 位元 Oracle 驅動程式
-	在執行 32 位元版本 Microsoft Office 的電腦上使用 Excel 連接到 Oracle 資料來源時 (包括在 64 位元 Windows 上)，將使用 32 位元 Oracle 驅動程式
-	在執行 64 位元版本 Microsoft Office 的電腦上使用 Excel 連接到 Oracle 資料來源時，將使用 64 位元 Oracle 驅動程式

### 註冊及連接到 SQL Server Reporting Services

Azure 資料目錄初始預覽版本中對於 SQL Server Reporting Services (SSRS) 資料來源的支援，僅限於原生模式伺服器。未來版本將加入在 SharePoint 模式中支援 SSRS。

### 在 Excel 中開啟資料資產

從 Azure 資料目錄入口網站使用 Microsoft Excel 開啟資料資產時，使用者可能會看到 [Microsoft Excel 安全性注意事項] 對話方塊。這是標準的預期行為，使用者可以選取 [啟用] 以繼續。

如需詳細資訊，請參閱[啟用或停用可疑網站之連結和檔案的安全性警告](https://support.office.com/zh-TW/article/Enable-or-disable-security-alerts-about-links-and-files-from-suspicious-websites-A1AC6AE9-5C4A-4EB3-B3F8-143336039BBE)。

### 預覽中遺漏 BLOB 和 UDT 資料行

當您註冊的資料表和檢視包含二進位大型物件 (BLOB) 和使用者自訂資料型別 (UDT) 資料行，且選擇要包含資料資產的預覽時，預覽中將不會包含這些資料行。

### Proxy 與原則設定及資料來源註冊

使用者可能會遇到一種情況，他們可以登入 Azure 資料目錄入口網站，但在嘗試登入資料來源註冊工具時，卻遇到錯誤訊息，導致無法登入。

此問題行為有兩個可能的原因：

**原因 1：Active Directory Federation Services 設定**。資料來源註冊工具使用「表單驗證」，根據 Active Directory 驗證使用者登入。Active Directory 系統管理員必須在全域驗證原則中啟用表單驗證，登入才會成功。

在某些情況下，只有當使用者是在公司網路上，或只有當使用者從公司網路外部連接時，才會發生此錯誤行為。全域驗證原則允許分別為內部網路和外部網路連線啟用驗證方法。如果使用者連線的網路未啟用表單驗證，可能會發生登入錯誤。

如需詳細資訊，請參閱[針對不支援 WIA 的裝置設定內部網路表單型驗證](https://technet.microsoft.com/library/dn727110.aspx)。

**原因 2：網路 Proxy 設定**。如果公司網路使用 Proxy 伺服器，註冊工具可能無法透過 Proxy 連線到 Azure Active Directory。使用者可以編輯註冊工具的組態檔，將此區段加入至檔案，以確保註冊工具能夠連線：


	  <system.net>
	    <defaultProxy useDefaultCredentials="true" enabled="true">
	      <proxy usesystemdefault="True"
	                      proxyaddress="http://<your corporate network proxy url>"
	                      bypassonlocal="True"/>
	    </defaultProxy>
	  </system.net>


若要找出 RegistrationTool.exe.config 檔案，請啟動註冊工具，然後開啟 Windows 工作管理員公用程式。在工作管理員的 [詳細資料] 索引標籤，以滑鼠右鍵按一下 RegistrationTool.exe，再從快顯功能表中選擇 [開啟檔案位置]。

<!---HONumber=September15_HO1-->