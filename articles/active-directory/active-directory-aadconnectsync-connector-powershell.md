<properties
   pageTitle="Azure AD Connect 同步處理：PowerShell 連接器 | Microsoft Azure"
   description="本文說明如何設定 Microsoft 的 Windows PowerShell 連接器。"
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
   ms.date="03/16/2016"
   ms.author="andkjell"/>

# WIndows PowerShell 連接器技術參考

本文說明 Windows PowerShell 連接器。本文適用於下列產品：

- Microsoft Identity Manager 2016 (MIM2016)
- Forefront Identity Manager 2010 R2 (FIM2010R2)
    -   必須使用 Hotfix 4.1.3671.0 或更新版本 [KB3092178](https://support.microsoft.com/kb/3092178)。

對於 MIM2016 和 FIM2010R2，可以從 [Microsoft 下載中心](http://go.microsoft.com/fwlink/?LinkId=717495)下載此連接器。

## PowerShell 連接器概觀

PowerShell 連接器可讓您整合同步處理服務與可提供 Windows PowerShell 型應用程式開發介面 (API) 的外部系統。此連接器會在呼叫型可延伸連線管理代理程式 2 (ECMA2) 架構和 Windows PowerShell 的功能之間提供橋樑。如需 ECMA 架構的詳細資訊，請參閱[可延伸的連線 2.2 管理代理程式參考](https://msdn.microsoft.com/library/windows/desktop/hh859557.aspx)。

### 必要條件

在您使用連接器之前，請確定除了任何先前提到的 Hotfix 以外，同步處理伺服器上還有下列項目：

- Microsoft .NET 4.5.2 Framework 或更新版本
- Windows PowerShell 2.0、3.0 或 4.0

同步處理服務伺服器上的執行原則必須設定為允許連接器執行 Windows PowerShell 指令碼。除非連接器將要執行的指令碼是經過數位簽署的，否則請執行下列命令來設定執行原則：

`Set-ExecutionPolicy -ExecutionPolicy RemoteSigned`

## 建立新的連接器

若要在同步處理服務中建立 Windows PowerShell 連接器，您必須提供一系列 Windows PowerShell 指令碼來執行同步處理服務所要求的步驟。根據所要連接到的資料來源和所需的功能，您必須實作的指令碼將會不同。本節概述可以實作的每個指令碼以及何時需要用到它們。

Windows PowerShell 連接器是設計用來儲存同步處理服務資料庫內的每個指令碼。雖然您可以執行儲存在檔案系統的指令碼，但將每個指令碼的主體直接插入連接器組態會更為容易。

若要建立 PowerShell 連接器，請在 [同步處理服務] 中選取 [管理代理程式] 和 [建立]。選取 [PowerShell (Microsoft)] 連接器。

![建立連接器](./media/active-directory-aadconnectsync-connector-powershell/createconnector.png)

### 連線能力

接下來，您可以提供用來連接至遠端系統的組態參數。這些參數會由同步處理服務妥善保存，並在連接器執行時提供給 Windows PowerShell 指令碼。

![連線能力](./media/active-directory-aadconnectsync-connector-powershell/connectivity.png)

您可以設定下列連線參數：

**連線能力**

| 參數 | 預設值 | 目的 |
| --- | --- | --- |
| 伺服器 | <Blank> | 連接器應該連接到的伺服器名稱。 |
| 網域 | <Blank> | 連接器執行時，要儲存起來以供使用的認證網域。 |
| 使用者 | <Blank> | 連接器執行時，要儲存起來以供使用的認證使用者名稱。 |
| 密碼 | <Blank> | 連接器執行時，要儲存起來以供使用的認證密碼。 |
| 模擬連接器帳戶 | False | 如果為 true，同步處理服務將在上面提供的認證內容中執行 Windows PowerShell 指令碼。如果可能的話，建議您使用傳遞至每個指令碼的 $Credentials 參數，來代替模擬。如需使用此參數時必須具備之其他權限的詳細資訊，請參閱「模擬的其他組態」。 |
| 模擬時載入使用者設定檔 | False | 指示 Windows 在模擬期間載入連接器認證的使用者設定檔。如果要模擬的使用者具有漫遊設定檔，連接器就不會載入漫遊設定檔。如需使用此參數時必須具備之其他權限的詳細資訊，請參閱「模擬的其他組態」。 |
| 模擬時的登入類型 | None | 模擬期間的登入類型。如需詳細資訊，請參閱 [dwLogonType][dw] 文件。 |
| 僅限已簽署的指令碼 | False | 如果為 true，Windows PowerShell 連接器會驗證每個指令碼是否具有有效的數位簽章。如果為 false，請確定同步處理服務伺服器的 Windows PowerShell 執行原則是 RemoteSigned 或不受限制。 |

**一般模組**

連接器可讓系統管理員在組態中儲存共用的 Windows PowerShell 模組。連接器在執行指令碼時，會將 Windows PowerShell 模組擷取到檔案系統，使其可供每個指令碼匯入。

針對匯入、匯出和密碼同步處理指令碼，會將一般模組擷取到連接器的 MAData 資料夾。針對結構描述、驗證、階層和資料分割探索指令碼，則會將一般模組擷取到 %TEMP% 資料夾。在這兩種情況下，所擷取的一般模組指令碼會根據一般模組指令碼名稱設定進行命名。

若要從 MAData 資料夾中載入名稱為 FIMPowerShellConnectorModule.psm1 的模組，請使用下列陳述式：`Import-Module (Join-Path -Path [Microsoft.MetadirectoryServices.MAUtils]::MAFolder -ChildPath "FIMPowerShellConnectorModule.psm1")`

若要從 %TEMP% 資料夾中載入名稱為 FIMPowerShellConnectorModule.psm1 的模組，請使用下列陳述式：`Import-Module (Join-Path -Path $env:TEMP -ChildPath "FIMPowerShellConnectorModule.psm1")`

**參數驗證**

驗證指令碼是選擇性的 Windows PowerShell 指令碼，可用來確保系統管理員所提供的連接器組態參數有效。驗證伺服器和連接認證以及連線參數是驗證指令碼的常見用途。下列索引標籤和對話方塊若有修改，就會呼叫驗證指令碼：

- 連線能力
- 全域參數
- 資料分割組態

驗證指令碼會接收連接器中的下列參數：

| 名稱 | 資料類型 | 說明 |
| --- | --- | --- |
| ConfigParameterPage | [ConfigParameterPage][cpp] | 觸發驗證要求的組態索引標籤或對話方塊。 |
| ConfigParameters | [KeyedCollection][keyk] [string, [ConfigParameter][cp]] | 連接器組態參數的資料表。 |
| 認證 | [PSCredential][pscred] | 包含系統管理員在 [連線] 索引標籤上輸入的任何認證。 |

驗證指令碼應該將單一 ParameterValidationResult 物件傳回至管線中。

**結構描述探索**

結構描述探索指令碼是必要項目。此指令碼會傳回物件類型和屬性，以及同步處理服務在設定屬性流程規則時會使用的屬性條件約束。結構描述探索指令碼會在連接器建立期間執行，並且將會在同步處理服務管理員中填入連接器的結構描述和後續的重新整理結構描述函數。

結構描述探索指令碼會接收連接器中的下列參數：

| 名稱 | 資料類型 | 說明 |
| --- | --- | --- |
| ConfigParameters | [KeyedCollection][keyk] [string, [ConfigParameter][cp]] | 連接器組態參數的資料表。 |
| 認證 | [PSCredential][pscred] | 包含系統管理員在 [連線] 索引標籤上輸入的任何認證。 |

指令碼必須傳回單一[結構描述][schema]物件到管線中。結構描述物件是由代表物件類型 (例如使用者、群組等) 的 [SchemaType][schemaT] 物件所組成。SchemaType 物件會保存代表該類型屬性 (例如名字、姓氏、郵寄地址等) 的 [SchemaAttribute][schemaA] 物件的集合。

**其他參數**

除了到目前為止所討論的標準組態設定，您還可以定義連接器執行個體特定的其他自訂組態設定。這些參數可以指定於連接器、資料分割或執行步驟層級，並從相關的 Windows PowerShell 指令碼進行存取。自訂組態設定可以純文字格式儲存在同步處理服務資料庫中，或可以進行加密。同步處理服務會在必要時自動加密和解密安全的組態設定。

若要指定自訂組態設定，請使用逗號 (,) 分隔每個參數的名稱。

若要從指令碼中存取自訂組態設定，您必須在名稱後面加上底線 (\_) 和參數範圍 (全域、資料分割或 RunStep)。例如，若要存取全域檔案名稱參數，請使用此程式碼片段：`$ConfigurationParameters["FileName_Global"].Value`

### 功能

Management Agent Designer 的 [功能] 索引標籤會定義連接器的行為和功能。在建立連接器之後，就無法修改在此索引標籤上所做的選擇。下表列出每個功能設定。

![功能](./media/active-directory-aadconnectsync-connector-powershell/capabilities.png)

| 功能 | 說明 |
| --- | --- |
| [辨別名稱樣式][dnstyle] | 指出連接器是否會支援辨別名稱，如果會，其樣式為何。 |
| [匯出類型][exportT] | 決定要對匯出指令碼顯示的物件類型。<li>AttributeReplace – 包含屬性變更時的一組完整多重值屬性值。</li><li>AttributeUpdate – 只包含屬性變更時的多重值屬性差異。</li><li>MultivaluedReferenceAttributeUpdate - 包含一組完整的非參考多重值屬性值，並僅包含多重值參考屬性的差異。</li><li>ObjectReplace – 包含任何屬性變更時的所有物件屬性</li> |
| [資料正規化][DataNorm] | 指示同步處理服務先將錨點屬性正規化再提供給指令碼。 |
| [物件確認][oconf] | 在同步處理服務中設定擱置匯入行為。<li>標準 – 預期所有匯出的變更會透過匯入來確認的預設行為</li><li>NoDeleteConfirmation – 刪除物件時，不會產生任何擱置匯入。</li><li>NoAddAndDeleteConfirmation – 建立或刪除物件時，不會產生任何擱置匯入。</li>
| 使用 DN 做為錨點 | 如果 [辨別名稱樣式] 設定為 LDAP，則連接器空間的錨點屬性也是辨別名稱。 |
| 數個連接器並行作業 | 核取時，可以同時執行多個 Windows PowerShell 連接器。 |
| 分割數 | 核取時，連接器可支援多個資料分割和資料分割探索。 |
| 階層 | 核取時，連接器可支援 LDAP 樣式的階層結構。 |
| 啟用匯入 | 核取時，連接器會透過匯入指令碼匯入資料。 |
| 啟用差異匯入 | 核取時，連接器可以要求匯入指令碼的差異。 |
| 啟用匯出 | 核取時，連接器會透過匯出指令碼匯出資料。 |
| 啟用完整匯出 | 核取時，匯出指令碼可支援匯出整個連接器空間。若要使用此選項，也必須核取 [啟用匯出]。|
| 第一個匯出階段沒有參考值 | 核取時，會在第二個匯出階段匯出參考屬性。 |
| 啟用物件重新命名 | 核取時，您可以修改辨別名稱。 |
| 以刪除新增做為取代 | 核取時，會將刪除新增作業匯出為單一取代。 |
| 啟用密碼作業 | 核取時，可支援密碼同步處理指令碼。 |
| 在第一個階段啟用匯出密碼 | 核取時，會在建立物件時匯出佈建期間所設定的密碼。 |

### 全域參數

Management Agent Designer 中的 [全域參數] 索引標籤可讓系統管理員設定連接器將會執行的每個 Windows PowerShell 指令碼，以及 [連線] 索引標籤上定義的自訂組態設定的全域值。

**資料分割探索**

資料分割是一個共用結構描述內的個別命名空間。例如在 Active Directory 中，每個網域就是一個樹系內的資料分割。資料分割是匯入和匯出作業的邏輯群組。匯入和匯出將資料分割做為內容，而且所有作業都必須在此內容中發生。資料分割應該代表 LDAP 中的階層。匯入作業會使用資料分割的辨別名稱，以確認所有傳回的物件都在資料分割的範圍內。在從 Metaverse 佈建到連接器空間的期間，也會使用資料分割辨別名稱來判斷物件應該在匯出期間與哪個資料分割相關聯。

資料分割探索指令碼會接收連接器中的下列參數：

| 名稱 | 資料類型 | 說明 |
| --- | --- | --- |
| ConfigParameters | [KeyedCollection][keyk][string, [ConfigParameter][cp]] | 連接器組態參數的資料表。
| 認證 | [PSCredential][pscred] | 包含系統管理員在 [連線] 索引標籤上輸入的任何認證。 |

指令碼必須傳回單一[資料分割][part]物件或資料分割物件的 List[T] 到管線中。

**階層探索**

只有在辨別名稱樣式功能是 LDAP 時，才會使用階層探索指令碼。此指令碼可用來讓系統管理員瀏覽並選取一組會被視為在匯入和匯出作業範圍之內或之外的容器。此指令碼只應提供所提供給指令碼之根節點直接子系的節點清單。

階層探索指令碼會接收連接器中的下列參數：

| 名稱 | 資料類型 | 說明 |
| --- | --- | --- |
| ConfigParameters | [KeyedCollection][keyk][string, [ConfigParameter][cp]] | 連接器組態參數的資料表。 |
| 認證 | [PSCredential][pscred] | 包含系統管理員在 [連線] 索引標籤上輸入的任何認證。 |
| ParentNode | [HierarchyNode][hn] | 指令碼應將直接子系傳回到之階層的根節點。 |

指令碼必須傳回單一 HierarchyNode 子物件或 HierarchyNode 子物件 List[T] 至管線中。

#### Import

支援匯入作業的連接器必須實作三個指令碼。

**開始匯入**

開始匯入指令碼會在匯入執行步驟開始時執行。在此步驟中，您可以連接到來源系統，並進行任何預備步驟，再從連接的系統匯入資料。

開始匯入指令碼會接收連接器中的下列參數：

| 名稱 | 資料類型 | 說明 |
| --- | --- | --- |
| ConfigParameters | [KeyedCollection][keyk][string, [ConfigParameter][cp]] | 連接器組態參數的資料表。 |
| 認證 | [PSCredential][pscred] | 包含系統管理員在 [連線] 索引標籤上輸入的任何認證。 |
| OpenImportConnectionRunStep | [OpenImportConnectionRunStep][oicrs] | 告知指令碼匯入執行的類型 (差異或完整)、資料分割、階層、浮水印及預期的頁面大小。
| 類型 | [結構描述][schema] | 將匯入的連接器空間結構描述。 |

指令碼必須傳回單一 [OpenImportConnectionResults][oicres] 物件到管線中。下列範例程式碼示範如何將 OpenImportConnectionResults 物件傳回至管線：

`Write-Output (New-Object Microsoft.MetadirectoryServices.OpenImportConnectionResults)`

**匯入資料**

連接器會呼叫匯入資料指令碼，直到指令碼指出已沒有資料要匯入，而且同步處理服務不需要在差異匯入期間要求任何完整的物件匯入。Windows PowerShell 連接器的頁面大小為 9,999 個物件。如果您的指令碼在匯入時會傳回超過 9,999 個物件，您必須支援分頁功能。連接器會公開自訂資料屬性供您用來儲存浮水印，以便在每次呼叫匯入資料指令碼時，指令碼會繼續從中斷處開始匯入物件。

匯入資料指令碼會接收連接器中的下列參數：

| 名稱 | 資料類型 | 說明 |
| --- | --- | --- |
| ConfigParameters | [KeyedCollection][keyk][string, [ConfigParameter][cp]] | 連接器組態參數的資料表。 |
| 認證 | [PSCredential][pscred] | 包含系統管理員在 [連線] 索引標籤上輸入的任何認證。 |
| GetImportEntriesRunStep | [ImportRunStep][irs] | 保留可在分頁匯入與差異匯入期間使用的浮水印 (CustomData)。 |
| OpenImportConnectionRunStep | [OpenImportConnectionRunStep][oicrs] | 告知指令碼匯入執行的類型 (差異或完整)、資料分割、階層、浮水印及預期的頁面大小。 |
| 類型 | [結構描述][schema] | 將匯入的連接器空間結構描述。 |

匯入資料指令碼必須將 List[[CSEntryChange][csec]] 物件寫入到管線中。這個集合是由代表每個所匯入物件的 CSEntryChange 屬性所組成。在執行完整匯入時，這個集合應該有一組完整的 CSEntryChange 物件，而這些物件擁有每個個別物件的所有屬性。在差異匯入期間，CSEntryChange 物件應該包含要匯入之每個物件的屬性層級差異，或已變更之物件的完整表示 (取代模式)。

**結束匯入**

在匯入執行結束時，便會執行結束匯入指令碼。此指令碼應該會執行任何必要的清除工作 (例如中斷系統連線、回應失敗等等)。

結束匯入指令碼會接收連接器中的下列參數：

| 名稱 | 資料類型 | 說明 |
| --- | --- | --- |
| ConfigParameters | [KeyedCollection][keyk][string, [ConfigParameter][cp]] | 連接器組態參數的資料表。 |
| 認證 | [PSCredential][pscred] | 包含系統管理員在 [連線] 索引標籤上輸入的任何認證。 |
| OpenImportConnectionRunStep | [OpenImportConnectionRunStep][oicrs] | 告知指令碼匯入執行的類型 (差異或完整)、資料分割、階層、浮水印及預期的頁面大小。 |
| CloseImportConnectionRunStep | [CloseImportConnectionRunStep][cecrs] | 告知指令碼匯入結束的原因。 |

指令碼必須傳回單一 [CloseImportConnectionResults][cicres] 物件到管線中。下列範例程式碼示範如何將 CloseImportConnectionResults 物件傳回至管線：`Write-Output (New-Object Microsoft.MetadirectoryServices.CloseImportConnectionResults)`

#### 匯出

與連接器的匯入架構相同，支援匯出的連接器必須實作三個指令碼。

**開始匯出**

開始匯出指令碼會在匯出執行步驟開始時執行。在此步驟中，您可以連接到來源系統，並進行任何預備步驟，再從連接的系統匯出資料。

開始匯出指令碼會接收連接器中的下列參數：

| 名稱 | 資料類型 | 說明 |
| --- | --- | --- |
| ConfigParameters | [KeyedCollection][keyk][string, [ConfigParameter][cp]] | 連接器組態參數的資料表。 |
| 認證 | [PSCredential][pscred] | 包含系統管理員在 [連線] 索引標籤上輸入的任何認證。 |
| OpenExportConnectionRunStep | [OpenExportConnectionRunStep][oecrs] | 告知指令碼匯出執行的類型 (差異或完整)、資料分割、階層及預期的頁面大小。 |
| 類型 | [結構描述][schema] | 將匯出的連接器空間結構描述。 |

指令碼不應傳回任何輸出到管線。

**匯出資料**

同步處理服務將會一直呼叫匯出資料指令碼，直到能夠處理完所有擱置中的匯出。依據連接器空間是否有比連接器頁面大小還多的擱置中匯出、是否有參考屬性或密碼，可能會呼叫匯出資料指令碼多次，並可能針對相同物件呼叫多次。

匯出資料指令碼會接收連接器中的下列參數：

| 名稱 | 資料類型 | 說明 |
| --- | --- | --- |
| ConfigParameters | [KeyedCollection][keyk][string, [ConfigParameter][cp]] | 連接器組態參數的資料表。|
| 認證 | [PSCredential][pscred] | 包含系統管理員在 [連線] 索引標籤上輸入的任何認證。|
| CSEntries | IList[CSEntryChange][csec] | 具有要在此階段期間處理之擱置中匯出的所有連接器空間物件清單。 |
| OpenExportConnectionRunStep | [OpenExportConnectionRunStep][oecrs] | 告知指令碼匯出執行的類型 (差異或完整)、資料分割、階層及預期的頁面大小。 |
| 類型 | [結構描述][schema] | 將匯出的連接器空間結構描述。 |

匯出資料指令碼必須傳回 [PutExportEntriesResults][peeres] 物件到管線中。此物件不需要包含每個匯出連接器的結果資訊，除非發生錨點屬性錯誤或變更。

下列範例程式碼示範如何將 PutExportEntriesResults 物件傳回至管線：`Write-Output (New-Object Microsoft.MetadirectoryServices.PutExportEntriesResults)`

**結束匯出**

在匯出執行結束時，便會執行結束匯出指令碼。此指令碼應該會執行任何必要的清除工作 (例如中斷系統連線、回應失敗等等)。

結束匯出指令碼會接收連接器中的下列參數：

| 名稱 | 資料類型 | 說明 |
| --- | --- | --- |
| ConfigParameters | [KeyedCollection][keyk][string, [ConfigParameter][cp]] | 連接器組態參數的資料表。 |
| 認證 | [PSCredential][pscred] | 包含系統管理員在 [連線] 索引標籤上輸入的任何認證。 |
| OpenExportConnectionRunStep | [OpenExportConnectionRunStep][oecrs] | 告知指令碼匯出執行的類型 (差異或完整)、資料分割、階層及預期的頁面大小。 |
| CloseExportConnectionRunStep | [CloseExportConnectionRunStep][cecrs] | 告知指令碼匯出結束的原因。 |

指令碼不應傳回任何輸出到管線。

#### 密碼同步處理

Windows PowerShell 連接器可以做為密碼變更/重設的目標。

密碼指令碼會接收連接器中的下列參數：

| 名稱 | 資料類型 | 說明 |
| --- | --- | --- |
| ConfigParameters | [KeyedCollection][keyk][string, [ConfigParameter][cp]] | 連接器組態參數的資料表。 |
| 認證 | [PSCredential][pscred] | 包含系統管理員在 [連線] 索引標籤上輸入的任何認證。 |
| 資料分割 | [資料分割][part] | CSEntry 所在的目錄資料分割。 |
| CSEntry | [CSEntry][cse] | 接收密碼變更或重設之物件的連接器空間項目。 |
| OperationType | String | 指出作業是重設 (**SetPassword**) 還是變更 (**ChangePassword**)。 |
| PasswordOptions | [PasswordOptions][pwdopt] | 指定想要之密碼重設行為的旗標。只有在 OperationType 是 **SetPassword** 時才可以使用此參數。 |
| OldPassword | String | 填入物件的舊密碼以進行密碼變更。只有在 OperationType 是 **ChangePassword** 時才可以使用此參數。 |
| NewPassword | String | 填入指令碼應該設定的物件新密碼。 |

密碼指令碼預期不會傳回任何結果到 Windows PowerShell 管線。如果密碼指令碼中發生錯誤，指令碼應該會擲回下列其中一個例外狀況，以告知同步處理服務此問題：

- [PasswordPolicyViolationException][pwdex1] – 如果密碼不符合所連接系統中的密碼原則便會擲回。
- [PasswordIllFormedException][pwdex2] – 所連接系統不接受密碼時便會擲回。
- [PasswordExtension][pwdex3] – 密碼指令碼中發生其他所有錯誤時便會擲回。

## 範例連接器

如需可用範例連接器的完整概觀，請參閱 [Windows PowerShell 連接器範例連接器集合][samp]。

## 其他注意事項

### 適用於模擬的其他組態

對要模擬的使用者授與同步處理服務伺服器上的下列權限：

下列登錄機碼的讀取權限：

- HKEY\_USERS\\[SynchronizationServiceServiceAccountSID]\\Software\\Microsoft\\PowerShell
- HKEY\_USERS\\[SynchronizationServiceServiceAccountSID]\\Environment

若要判斷同步處理服務之服務帳戶的安全性識別碼 (SID)，請執行下列 PowerShell 命令：

```
$account = New-Object System.Security.Principal.NTAccount "<domain><username>"
$account.Translate([System.Security.Principal.SecurityIdentifier]).Value
```

下列檔案系統資料夾的讀取權限：

- %ProgramFiles%\\Microsoft Forefront Identity Manager\\2010\\Synchronization Service\\Extensions
- %ProgramFiles%\\Microsoft Forefront Identity Manager\\2010\\Synchronization Service\\ExtensionsCache
- %ProgramFiles%\\Microsoft Forefront Identity Manager\\2010\\Synchronization Service\\MaData<ConnectorName>

用 Windows PowerShell 連接器的名稱替換<ConnectorName>預留位置。

## 疑難排解

-	如需如何啟用記錄來疑難排解連接器的資訊，請參閱[如何啟用連接器的 ETW 追蹤](http://go.microsoft.com/fwlink/?LinkId=335731)。

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[cpp]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.configparameterpage.aspx
[keyk]: https://msdn.microsoft.com/library/ms132438.aspx
[cp]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.configparameter.aspx
[pscred]: https://msdn.microsoft.com/library/system.management.automation.pscredential.aspx
[schema]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.schema.aspx
[schemaT]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.schematype.aspx
[schemaA]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.schemaattribute.aspx
[dnstyle]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.madistinguishednamestyle.aspx
[exportT]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.maexporttype.aspx
[DataNorm]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.manormalizations.aspx
[oconf]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.maobjectconfirmation.aspx
[dw]: https://msdn.microsoft.com/library/windows/desktop/aa378184.aspx
[part]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.partition.aspx
[hn]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.hierarchynode.aspx
[oicrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.openimportconnectionrunstep.aspx
[cecrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.closeexportconnectionrunstep.aspx
[oicres]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.openimportconnectionresults.aspx
[cecrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.closeexportconnectionrunstep.aspx
[cicres]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.closeimportconnectionresults.aspx
[oecrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.openexportconnectionrunstep.aspx
[irs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.importrunstep.aspx
[cse]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.csentry.aspx
[csec]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.csentrychange.aspx
[peeres]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.putexportentriesresults.aspx
[pwdopt]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordoptions.aspx
[pwdex1]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordpolicyviolationexception.aspx
[pwdex2]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordillformedexception.aspx
[pwdex3]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordextensionexception.aspx
[samp]: http://go.microsoft.com/fwlink/?LinkId=394291

<!---HONumber=AcomDC_0323_2016-->