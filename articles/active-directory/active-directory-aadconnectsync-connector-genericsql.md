<properties
   pageTitle="Azure AD Connect 同步處理：一般 SQL 連接器"
   description="本文說明如何設定 Microsoft 的一般 SQL 連接器。"
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="12/11/2015"
   ms.author="andkjell"/>

# 一般 SQL 連接器技術參考

本文說明一般 SQL 連接器。本文適用於下列產品：

- Microsoft Identity Manager 2016 (MIM2016)
- Forefront Identity Manager 2010 R2 (FIM2010R2)
    -   必須使用 Hotfix 4.1.3461.0 或更新版本 [KB2870703](https://support.microsoft.com/kb/2870703)。

對於 MIM2016 和 FIM2010R2，可以從 [Microsoft 下載中心](http://go.microsoft.com/fwlink/?LinkId=717495)下載此連接器。

## 一般 SQL 連接器概觀

一般 SQL 連接器可讓您整合同步處理服務與提供 ODBC 連線的資料庫系統。

目前的連接器版本大致支援下列功能：

| 功能 | 支援 |
| --- | --- |
| 連接的資料來源 | 此連接器支援所有 64 位元的 ODBC 驅動程式。此連接器已進行下列各項的測試：<li>Microsoft SQL Server & SQL Azure</li><li>IBM DB2 10.x</li><li>IBM DB2 9.x</li><li>Oracle 10 & 11g</li><li>MySQL 5.x</li>
| 案例 | <li>物件生命週期管理</li><li>密碼管理</li> |
| 作業 | <li>完整匯入和差異匯入、匯出</li><li>若為匯出：新增、更新、刪除和取代</li><li>設定密碼、變更密碼</li>
| 結構描述 | <li>動態探索物件和屬性</li>

### 必要條件

在您使用連接器之前，請確定除了任何先前提到的 Hotfix 以外，同步處理伺服器上還有下列項目：

- Microsoft .NET 4.5.2 Framework 或更新版本
- 64 位元的 ODBC 用戶端驅動程式

### 連接的資料來源權限

若要在一般 SQL 連接器中建立或執行任何支援的工作，您必須具備：

- db\_datareader
- db\_datawriter

### 連接埠和通訊協定

如需 ODBC 驅動程式運作所需的連接埠，請參閱資料庫廠商的說明文件。

## 建立新的連接器

若要建立一般 SQL 連接器，請在 [同步處理服務] 中選取 [管理代理程式] 和 [建立]。選取 **一般 SQL (Microsoft)** 連接器。

![CreateConnector](./media/active-directory-aadconnectsync-connector-genericsql/createconnector.png)

### 連線能力

連接器會使用 ODBC DSN 檔案進行連線。使用在 [系統管理工具] 下的開始功能表中找到的 [ODBC 資料來源]，建立 DSN 檔案。在系統管理工具中，建立 [檔案 DSN]，以便提供給連接器。

![CreateConnector](./media/active-directory-aadconnectsync-connector-genericsql/connectivity.png)

當您建立新的一般 SQL 連接器時，[連線能力] 是第一個畫面。您必須先提供下列資訊：

- DSN 檔案路徑
- 驗證
    - 使用者名稱
    - 密碼

資料庫應該支援下列其中一種驗證方法。

- **Windows 驗證**：驗證資料庫將使用 Windows 認證來驗證使用者。在此情況下，將使用同步處理服務所使用的服務帳戶。此帳戶需要資料庫的權限。
- **SQL 驗證**：驗證資料庫會使用 [連線能力] 畫面上定義的使用者名稱/密碼連接到資料庫。如果您在 DSN 檔案中儲存使用者名稱/密碼，則優先使用在 [連線能力] 畫面上提供的認證。
- **Azure SQL Database 驗證**：如需詳細資訊，請參閱[使用 Azure Active Directory 驗證連接到 SQL Database](sql-database-aad-authentication.md)。

**DN 是錨點**：如果您選取此選項，DN 也會做為錨點屬性。它可用於簡單實作，但也有下列限制：

-	連接器只支援 1 個物件類型。因此，所有參考屬性只能參考相同的物件類型。

**匯出類型：物件取代**：在匯出期間，只有一些屬性已變更時，包含所有屬性的整個物件將會匯出並將取代現有的物件。

### 結構描述 1 (偵測物件類型)

此頁面上，您將設定連接器要如何在資料庫中尋找不同的物件類型。

每個物件類型會顯示為一個資料分割，並且在 [設定資料分割和階層] 上進一步設定。

![schema1a](./media/active-directory-aadconnectsync-connector-genericsql/schema1a.png)

**物件類型偵測方法**：連接器支援下列物件類型偵測方法。

- **固定值**：您以逗號分隔的清單來提供物件類型清單。例如User,Group,Department。![schema1b](./media/active-directory-aadconnectsync-connector-genericsql/schema1b.png)
- **資料表/檢視/預存程序**：提供資料表/檢視/預存程序的名稱，然後提供資料行名稱以提供物件類型的清單。如果您使用預存程序，也需以下列格式提供其參數：**[名稱]:[方向]:[值]**。在個別一行上提供每個參數 (使用 Ctrl+Enter 來換行)。![schema1c](./media/active-directory-aadconnectsync-connector-genericsql/schema1c.png)
- **SQL 查詢**：此選項可讓您提供 SQL 查詢，以傳回包含物件類型的單一資料行，例如 `SELECT [Column Name] FROM TABLENAME`。傳回的資料行必須是字串類型 (varchar)。

### 結構描述 2 (偵測屬性類型)

在此頁面上，您將設定要如何偵測屬性名稱和類型。系統會針對在前一頁偵測到的每個物件類型，列出設定選項。

![schema2a](./media/active-directory-aadconnectsync-connector-genericsql/schema2a.png)

**屬性類型偵測方法**：連接器會以 [結構描述 1] 畫面中每個偵測到的物件類型，支援下列屬性類型偵測方法。

- **資料表/檢視/預存程序**：提供資料表/檢視/預存程序的名稱，以便用來尋找屬性名稱。如果您使用預存程序，也需以下列格式提供其參數：**[名稱]:[方向]:[值]**。在個別一行上提供每個參數 (使用 Ctrl+Enter 來換行)。若要偵測多重值屬性中的屬性名稱，請提供以逗號分隔的資料表或檢視清單。如果父和子資料表具有相同的資料行名稱，則不支援多重值案例。
- **SQL 查詢**：此選項可讓您提供 SQL 查詢，以傳回包含屬性名稱的單一資料行，例如 `SELECT [Column Name] FROM TABLENAME`。傳回的資料行必須是字串類型 (varchar)。

### 結構描述 3 (定義錨點和 DN)

此頁面可讓您為每個偵測到的物件類型設定錨點和 DN 屬性。您可以選取多個屬性，讓錨點變成唯一的。

![schema3a](./media/active-directory-aadconnectsync-connector-genericsql/schema3a.png)

- 不會列出多重值和布林值屬性。
- DN 和錨點無法使用相同的屬性，除非已在 [連線能力] 頁面上選取 [DN 是錨點]。
- 如果已在 [連線能力] 頁面上選取 [DN 是錨點]，此頁面只需要 DN 屬性。這個屬性也會做為錨點屬性。![schema3b](./media/active-directory-aadconnectsync-connector-genericsql/schema3b.png)

### 結構描述 4 (定義屬性類型、參考和方向)

此頁面可讓您設定每個屬性的屬性類型，如整數、參考、字串、二進位或布林值和方向。[結構描述 2] 頁面中的所有屬性都會列出，包括多重值屬性。

![schema4a](./media/active-directory-aadconnectsync-connector-genericsql/schema4a.png)

- **DataType**：用來將屬性類型對應至同步處理引擎所知的屬性類型。預設會使用在 SQL 結構描述中偵測到的相同類型，但 DateTime 和 Reference 不容易偵測。因此，您必須指定 **DateTime** 或 **Reference**。
- **方向**：您可以設定 Import、Export 或 ImportExport 的屬性方向。ImportExport 是預設值。![schema4b](./media/active-directory-aadconnectsync-connector-genericsql/schema4b.png)

注意：

- 如果連接器無法偵測屬性類型，則會使用字串資料類型。
- 可將**巢狀資料表**視為一個資料行的資料庫資料表。Oracle 不會以任何特定順序儲存巢狀資料表的資料列。不過，當您將巢狀資料表擷取至 PL/SQL 變數時，資料列便會有從 1 開始的連續下標。這可讓您取得個別資料列的類似陣列存取。
- 連接器不支援 **VARRYS**。

### 結構描述 5 (定義參考屬性的資料分割)

在此頁面上，您將為所有參考屬性設定屬性所參考的資料分割 (也就是物件類型)。

![schema5](./media/active-directory-aadconnectsync-connector-genericsql/schema5.png)

如果您使用 [DN 是錨點]，則必須使用相同的物件類型做為您參考的來源物件類型。您無法參考其他物件類型。

### 全域參數

[全域參數] 頁面用來設定差異匯入、日期/時間格式，以及密碼方法。

![globalparameters1](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters1.png)

一般 SQL 連接器支援使用下列差異匯入方法：

- **觸發程序**：請參閱[使用觸發程序產生差異檢視](https://technet.microsoft.com/library/cc708665.aspx)。
- **浮水印**：這是一般方法，可以用於任何資料庫。浮水印查詢會根據資料庫供應商預先填入。浮水印資料行必須出現在所用的每個資料表/檢視上。必須追蹤資料表的插入和更新，以及其相依 (多重值或子系) 資料表。同步處理服務與資料庫伺服器之間的時鐘必須同步。如果沒有同步，則可能會省略差異匯入中的某些項目。限制：
    - 浮水印策略不支援已刪除的物件。
- **快照** (僅適用於 Microsoft SQL Server) [使用快照產生差異檢視](https://technet.microsoft.com/library/cc720640.aspx)
- **變更追蹤** (僅適用於 Microsoft SQL Server) [關於變更追蹤](https://msdn.microsoft.com/library/bb933875.aspx) 限制：
    - 錨點和 DN 屬性必須屬於資料表中所選物件的主索引鍵。
    - 在使用變更追蹤的匯入和匯出期間內，不支援 SQL 查詢。

**其他參數**：指定 [資料庫伺服器時區]，以指出您的資料庫伺服器所在的位置。這個值用來支援各種格式的日期和時間屬性。

連接器一律會以 UTC 格式儲存日期和日期時間。若要能夠正確地轉換日期和時間，必須指定資料庫伺服器的時區以及所用的格式。格式應該是以 .Net 格式表示。

在匯出期間，必須器以 UTC 時間格式將每個日期時間屬性提供給連接器。

![globalparameters2](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters2.png)

**密碼設定**：連接器會提供密碼同步處理功能並支援設定和變更密碼。

連接器提供兩種方法來支援密碼同步處理：

- **預存程序**：此方法需要兩個預存程序，以支援設定和變更密碼。按照以下範例，分別在 [設定密碼 SP 參數] 和 [變更密碼 SP 參數] 中輸入新增和變更密碼作業的所有參數。![globalparameters3](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters3.png)
- **密碼延伸模組**：此方法需要密碼延伸模組 DLL (您必須提供實作 [IMAExtensible2Password](https://msdn.microsoft.com/library/microsoft.metadirectoryservices.imaextensible2password.aspx) 介面的延伸模組 DLL 名稱)。密碼延伸模組組件必須放在延伸模組資料夾中，連接器才可以在執行階段載入 DLL。![globalparameters4](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters4.png)

您也必須在 [設定延伸模組] 頁面上啟用密碼管理。![globalparameters5](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters5.png)

### 設定資料分割和階層

在資料分割和階層頁面上，選取所有物件類型。每個物件類型都在自己的資料分割中。

![partitions1](./media/active-directory-aadconnectsync-connector-genericsql/partitions1.png)

您也可以覆寫在 [連線能力] 或 [全域參數] 頁面上定義的值。

![partitions2](./media/active-directory-aadconnectsync-connector-genericsql/partitions2.png)

### 設定錨點

此頁面是唯讀頁面，因為已經定義錨點。選取的錨點屬性一律會附加物件類型，以確保它在所有物件類型中保持獨一無二。

![錨點](./media/active-directory-aadconnectsync-connector-genericsql/anchors.png)

## 設定執行步驟參數

這些步驟設定於連接器的執行設定檔。這些設定將會進行匯入和匯出資料的實際工作。

### 完整和差異匯入

一般 SQL 連接器支援使用下列方法的完整和差異匯入：

- 資料表
- 檢視
- 預存程序
- SQL 查詢

![runstep1](./media/active-directory-aadconnectsync-connector-genericsql/runstep1.png)

**資料表/檢視**

若要匯入物件的多重值屬性，您必須在 [多重資料表/檢視名稱] 中提供以逗號分隔的資料表/檢視名稱，以及在父資料表的 [聯結條件] 中提供各自的聯結條件。

範例：您想要匯入 [員工] 物件與其所有的多重值屬性。有兩個資料表：[員工] (主要資料表) 和 [部門] (多重值) 資料表。執行下列動作：

- 在 [資料表/檢視/SP] 中輸入 [員工]。
- 在 [多重資料表/檢視名稱] 中輸入 [部門]。
- 在 [聯結條件] 中輸入 [員工] 和 [部門] 之間的聯結條件，例如 `Employee.DEPTID=Department.DepartmentID`。![runstep2](./media/active-directory-aadconnectsync-connector-genericsql/runstep2.png)

**預存程序**

![runstep3](./media/active-directory-aadconnectsync-connector-genericsql/runstep3.png)

- 如果您有大量資料，建議實作預存程序的分頁。
- 若要讓預存程序支援分頁，您必須提供開始索引和結束索引。請參閱：[有效地進行大量資料的分頁](https://msdn.microsoft.com/library/bb445504.aspx)。
- 在執行時，將會以在 [設定步驟] 頁面上設定的各自頁面大小值取代 @StartIndex 和 @EndIndex。範例：如果連接器擷取第一頁且頁面大小設為 500，在這種情況下 @StartIndex 會是 1 而 @EndIndex 被視為 500，這些值會隨著連接器擷取後續頁面而增加並變更 @StartIndex 和 @EndIndex 值。
- 若要執行參數化預存程序，請以 `[Name]:[Direction]:[Value]` 格式提供參數。在個別一行上輸入每個參數 (使用 Ctrl + Enter 來換行)。
- 一般 SQL 連接器也支援從分散式環境的匯入作業，例如 Microsoft SQL Server 中連結的伺服器。如果要從連結的伺服器中的資料表擷取資訊，則會以格式提供資料表：`[ServerName].[Database].[Schema].[TableName]`
    - 在分散式環境中，連接器僅支援 Microsoft 連結的伺服器。
- 一般 SQL 連接器僅支援在執行步驟資訊和結構描述偵測之間具有類似結構 (包括別名和資料類型) 的物件。如果結構描述中選取的物件與在執行步驟提供的資訊不同，則 SQL 連接器將無法支援這類案例。

**SQL 查詢**

![runstep4](./media/active-directory-aadconnectsync-connector-genericsql/runstep4.png)

![runstep5](./media/active-directory-aadconnectsync-connector-genericsql/runstep5.png)

- 不支援多個結果集查詢。
- SQL 查詢支援分頁並提供開始索引和結束索引做為變數，以支援分頁。

### 差異匯入

![runstep6](./media/active-directory-aadconnectsync-connector-genericsql/runstep6.png)

差異匯入設定需要一些詳細的設定，以及其他支援的方法 (如完整匯入)。

- 如果使用者選擇 [觸發程序] 或 [快照] 方法來追蹤差異變更，使用者可以在 [歷程記錄資料表或快照資料庫名稱] 方塊中提供歷程記錄資料表或快照資料庫。
- 使用者也必須提供歷程記錄資枓表與父資料表之間的聯結條件。範例：`Employee.ID=History.EmployeeID`
- 從歷程記錄資料表追蹤父資料表上的交易，使用者必須提供包含新增/更新/刪除等作業資訊的資料行名稱。
- 如果使用者選擇 [浮水印] 來追蹤差異變更，則使用者必須在 [浮水印資料行名稱] 中提供包含作業資訊的資料行名稱。因此，連接器可以在執行差異匯入時考慮此資料行。
- 變更類型需要 [變更型別屬性] 資料行。此資料行會將主要資料表或多重值資料表中發生的變更，對應至差異檢視中的變更類型。此資料行可以包含適用於屬性層級變更的 Modify\_Attribute 變更類型，或適用於物件層級變更的 [新增]、[修改] 或 [刪除] 變更類型。如果除了預設值 [新增]、[修改] 或 [刪除] 以外，使用者可以使用此選項來定義這些值。

### 匯出

![runstep7](./media/active-directory-aadconnectsync-connector-genericsql/runstep7.png)

一般 SQL 連接器支援使用下列四種方法的匯出：

- 資料表
- 檢視
- 預存程序
- SQL 查詢

**資料表/檢視**

如果使用者選擇 [資料表/檢視] 選項，則連接器會產生各自的查詢並執行匯出。

**預存程序**

![runstep8](./media/active-directory-aadconnectsync-connector-genericsql/runstep8.png)

如果使用者選擇預存程序選項，則匯出需要 3 個不同的預存程序來執行各種插入/更新/刪除作業。

- **新增 SP 名稱**：如有任何物件來到連接器以便在各自的資料表中插入，就會執行此 SP。
- **更新 SP 名稱**：如有任何物件來到連接器以便在各自的資料表中更新，就會執行此 SP。
- **刪除 SP 名稱**：如有任何物件來到連接器以便在各自的資料表中刪除，就會執行此 SP。
- 從結構描述選取的屬性會做為預存程序的參數值。範例：EmployeeName: INPUT: @EmployeeName (EmployeeName 已在連接器結構描述中選取，而連接器會在執行匯出時取代各自的值)
- 執行參數化預存程序時，會以 `[Name]:[Direction]:[Value]` 格式輸入所有參數。在個別一行上輸入每個參數 (使用 Ctrl + Enter 來換行)。

**SQL 查詢**

![runstep9](./media/active-directory-aadconnectsync-connector-genericsql/runstep9.png)

如果使用者選擇 SQL 查詢選項，則匯出需要 3 個不同的查詢來執行各種插入/更新/刪除作業。

- **插入查詢**：如有任何物件來到連接器以便在各自的資料表中插入，就會執行此查詢。
- **更新查詢**：如有任何物件來到連接器以便在各自的資料表中更新，就會執行此查詢。
- **刪除查詢**：如有任何物件來到連接器以便在各自的資料表中刪除，就會執行此查詢。
- 從結構描述選取的屬性會做為查詢的參數值。範例：`Insert into Employee (ID, Name) Values (@ID, @EmployeeName)`

## 疑難排解

-	如需如何啟用記錄來疑難排解連接器的資訊，請參閱[如何啟用連接器的 ETW 追蹤](http://go.microsoft.com/fwlink/?LinkId=335731)。

<!---HONumber=AcomDC_1217_2015-->