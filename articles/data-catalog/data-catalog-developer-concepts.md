<properties
   pageTitle="Azure 資料目錄開發人員概念"
   description="Azure 資料目錄概念模型的重要概念簡介，以透過目錄 REST API 的形式公開。"
   services="data-catalog"
   documentationCenter=""
   authors="dvana"
   manager="mblythe"
   editor=""
   tags=""/>
<tags 
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="02/03/2016"
   ms.author="derrickv"/>

# Azure 資料目錄開發人員概念

Microsoft **Azure 資料目錄**是全面管理的雲端服務，能夠進行資料來源探索，以及讓群眾外包資料來源中繼資料。開發人員可以透過 REST API 來使用此服務。開發人員必須了解服務中所實作的概念，才能成功地與 **Azure 資料目錄**整合。

## 重要概念

**Azure 資料目錄**概念模型根據四個主要概念：**目錄**、**使用者**、**資產**和**註解**。

![概念][1]

*圖 1 - Azure 資料目錄簡易概念模型*

### 目錄

**目錄**是組織用來儲存所有中繼資料的最上層容器。每個 Azure 帳戶只能一個**目錄**。目錄與 Azure 訂用帳戶密切相關，即使一個帳戶可以有多個訂用帳戶，但任一特定 Azure 帳戶只能建立一個**目錄**。

目錄包含**使用者**和**資產**。

### 使用者

使用者是有權在目錄中執行動作的安全性主體 (搜尋目錄、加入、編輯或移除項目等等...)。

一個使用者可能扮演多個不同的角色。如需角色的詳細資訊，請參閱＜角色和授權＞一節。

可加入個別的使用者和安全性群組。

Azure 資料目錄使用 Azure Active Directory 來管理身分識別和存取權。每個目錄使用者必須是帳戶 Active Directory 的成員。

### Assets

**目錄**包含資料資產。**資產**是由目錄管理的細微度單位。

資產的細微度隨資料來源而異。對於 SQL Server 或 Oracle 資料庫，資產可以是資料表或檢視。對於 SQL Server Analysis Services，資產可以是量值、維度或關鍵效能指標 (KPI)。對於 SQL Server Reporting Services，資產是報表。

**資產**是您在目錄中加入或移除的項目。它是您從**搜尋**取回的結果單位。

**資產**由其名稱、位置和類型，以及進一步描述它的註解所組成。

### 註解

註解是代表資產中繼資料的項目。

註解的例子包括描述、標記、結構描述、文件等等。＜資產物件模型＞一節中有資產類型和註解類型的完整清單。

## 群眾外包註解和使用者觀點 (多樣性意見)

Azure 資料目錄的重點在於如何支援由群眾外包系統中的中繼資料。與 Wiki 的作法不同 – 其中只有一個意見，以最後一個寫入者為準 – Azure 資料目錄模型允許多種意見並存於系統中。

這種作法反映出企業資料的真實情況，不同的使用者對特定的資產可以有不同的觀點：

-	資料庫管理員可以提供服務等級協定或可用於處理大量 ETL 作業的時段的相關資訊
-	資料負責人可以提供資產適用的商務程序或企業採用的分類辦法的相關資訊
-	財務分析師可以提供期末報告工作期間如何使用資料的相關資訊

為了支援這個例子，每位使用者 (DBA、資料負責人和分析師) 可以將描述加入至目錄中已註冊的資料表。所有描述會都保留在系統中，Azure 資料目錄入口網站會顯示所有的描述。

這種模式適用於物件模型中的大部分項目。這就是您原本以為 JSON 裝載中的物件類型只有一個屬性，但卻有大量屬性的原因。

例如，資產根目錄底下有大量的描述物件。此陣列屬性稱為 “descriptions”。描述物件有三個屬性：description、tags 及 friendlyName。模式是每個使用者輸入任何一個或多個這些屬性時，就會根據使用者提供的值建立一個描述物件。

然後 UX 可以選擇如何顯示組合。有三種不同的顯示模式。

-	最簡單的模式為 [全部顯示]。在此模式下，所有物件會以某種形式的清單檢視來顯示。這就是 Azure 資料目錄入口網站 UX 對於描述的作法。
-	另一種模式是「合併」。在此模式下，不同使用者提供的所有值會合併在一起，並移除重複項目。Azure 資料目錄入口網站 UX 中的 tags 和 experts 屬性，即為此模式的例子。
-	第三種模式為「最後寫入者為準」。在此模式下，只會顯示最後輸入的值。friendlyName 是這種模式的一個例子。

## 資產物件模型

＜重要概念＞一節介紹的 **Azure 資料目錄**物件模型所包含的項目，可以是資產或註解。項目具有選用或必要的屬性。某些屬性會套用至所有項目。某些屬性會套用至所有資產。某些屬性只套用至特定的資產類型。

### 通用屬性

這些屬性套用至所有根資產類型和所有註解類型。

> [AZURE.NOTE] 名稱開頭為雙底線的屬性是系統類型。

<table><tr><td><b>屬性名稱</b></td><td><b>資料類型</b></td><td><b>註解</b></td></tr><tr><td>modifiedTime</td><td>DateTime</td><td>上次修改根目錄的時間。這是由用戶端設定(伺服器不維護此值)。</td></tr><tr><td>__id</td><td>String</td><td>項目的識別碼 (唯讀)。此識別碼是目錄資產中的唯一識別碼。</td></tr><tr><td>__type</td><td>String</td><td>資產的類型 (唯讀)</td></tr><tr><td>__creatorId</td><td>String</td><td>由資產建立者用來唯一識別資產的字串。</td></tr></table>

### 通用根屬性

這些屬性套用至所有根資產類型。

<table><tr><td><b>屬性名稱</b></td><td><b>資料類型</b></td><td><b>註解</b></td></tr><tr><td>名稱</td><td>String</td><td>衍生自資料來源位置資訊的名稱</td></tr><tr><td>dsl</td><td>資料來源位置</td><td>可唯一描述資料來源，為資產的其中一個識別碼(請參閱＜雙重識別＞一節)。dsl 的結構隨來源類型而異。</td></tr><tr><td>dataSource</td><td>DataSourceInfo</td><td>資產類型的詳細資料。</td></tr><tr><td>lastRegisteredBy</td><td>SecurityPrincipal</td><td>描述最近註冊此資產的使用者。包含使用者的唯一識別碼 (upn) 和顯示名稱 (lastName 和 firstName)。</td></tr><tr><td>lastRegisteredTime</td><td>dateTime</td><td>此資產上次註冊在目錄中的時間。</td></tr><tr><td>containerId</td><td>String</td><td>資料來源的容器資產識別碼。容器類型不支援這個屬性。</td></tr></table>

### 根資產類型

根資產類型所指的類型代表可以註冊在目錄中的各種資料資產。

<table><tr><td><b>資產類型</b></td><td><b>其他屬性</b></td><td><b>資料類型</b></td><td><b>註解</b></td></tr><tr><td>資料表</td><td></td><td></td><td>資料表代表任何表格式資料。這包括 SQL 資料表、SQL 檢視、 Analysis Services 表格式資料表、Analysis Services 多維度的維度、Oracle 資料表等等...   </td></tr><tr><td>Measure</td><td></td><td></td><td>此類型代表 Analysis Services 量值。</td></tr><tr><td></td><td>Measure</td><td>欄</td><td>描述量值的中繼資料</td></tr><tr><td></td><td>isCalculated </td><td>Boolean</td><td>指定是否計算量值。</td></tr><tr><td></td><td>measureGroup</td><td>String</td><td>量值的實體容器</td></tr><tr><td></td><td>goalExpression</td><td>String</td><td>會傳回 KPI 目標值的 MDX 數值運算式或計算。</td></tr><tr><td></td><td>valueExpression</td><td>String</td><td>會傳回 KPI 實際值的 MDX 數值運算式。</td></tr><tr><td></td><td>statusExpression</td><td>String</td><td>代表指定時間點之 KPI 狀態的 MDX 運算式。</td></tr><tr><td></td><td>trendExpression</td><td>String</td><td>評估一段時間的 KPI 值的 MDX 運算式。趨勢可以是特定商務情況下適用的任何時間性準則。</td></tr><tr><td></td><td>measureGroup</td><td>String</td><td>量值的實體容器</td></tr><tr><td>報告</td><td></td><td></td><td>此類型代表 SQL Server Reporting Services 報表 </td></tr><tr><td></td><td>CreatedBy</td><td>String</td><td></td></tr><tr><td></td><td>CreatedDate</td><td>String</td><td></td></tr><tr><td>容器</td><td></td><td></td><td>此類型代表其他資產 (例如 SQL database、Azure Blob 容器或 Analysis Services 模型) 的容器 。</td></tr></table>

### 註解類型

註解類型代表可以指派給目錄內其他類型的中繼資料類型。

<table><tr><td><b>註解類型</b></td><td><b>其他屬性</b></td><td><b>資料類型</b></td><td><b>註解</b></td></tr><tr><td>說明</td><td></td><td></td><td>系統的每個使用者可以加入自己的描述和標記。只有該使用者可以編輯 Description 物件(系統管理員和資產擁有者可以刪除 Description 描述物件，但無法編輯它)。系統會個別維護這些物件。因此，每個資產上有一個描述陣列 (除了可能有一個描述包含衍生自資料來源的資訊，每一個已對資產貢獻知識的使用者都有一個描述)。</td></tr><tr><td></td><td>friendlyName</td><td>字串</td><td>易記名稱，可代替衍生自資料來源的名稱。這適合用於顯示和搜尋。</td></tr><tr><td></td><td>tags</td><td>string[]</td><td>資產的標記陣列</td></tr><tr><td></td><td>說明</td><td>字串</td><td>資產的簡短描述 (2-3 行)</td></tr><tr><td>結構描述</td><td></td><td></td><td>Schema 描述資料的結構。它會列出屬性 (也就是資料行、屬性、欄位等等..) 名稱、類型及其他中繼資料。此資訊完全衍生自資料來源。資產上通常有一個結構描述項目。</td></tr><tr><td></td><td>columns</td><td>Column[]</td><td>資料行物件的陣列。它們以衍生自資料來源的資訊來描述資料行。</td></tr><tr><td>SchemaDescription</td><td></td><td></td><td>這包含結構描述中定義的每個屬性的描述和標記集合。系統的每個使用者可以加入自己的描述和標記。只有該使用者可以編輯 Description 物件(系統管理員和資產擁有者可以刪除 SchemaDescription 物件，但無法編輯它)。系統會個別維護這些物件。因此，每個資產上有一個 SchemaDescription 物件陣列 (除了可能有一個描述包含衍生自資料來源的資訊，每一個已對屬性貢獻知識的使用者都有一個描述)。SchemaAttributes 與結構描述不緊密繫結，所以可能不會同步，亦即 SchemaDescription 描述的資料行可能已不存在於結構描述中，或無法參考最近加入的新資料行。由寫入者決定是否要保持同步。資料來源也可能有描述資訊。這是執行工具時建立的另一個 schemaDescription 物件。</td></tr><tr><td></td><td>columnDescriptions</td><td>ColumnDescription[]</td><td>描述結構描述中的資料行的 ColumnDescriptions 陣列。</td></tr><tr><td>專家</td><td></td><td></td><td>這包含可視為資料集專家的使用者清單。列出描述時，專家意見 (也就是描述) 會移至 UX 頂端。每個使用者可以指定自己的專家清單。只有該使用者可以編輯 Expert 物件(系統管理員和資產擁有者可以刪除 Expert 物件，但無法編輯它)。</td></tr><tr><td></td><td>experts</td><td>string[]</td><td>電子郵件地址的陣列。</td></tr><tr><td>預覽</td><td></td><td></td><td>預覽包含資產的前 20 列資料的快照集。預覽只對某些資產類型才有意義 (也就是，對資料表有意義，但對量值沒有意義)。</td></tr><tr><td></td><td>preview</td><td>object[]</td><td>代表資料行的物件陣列。每個物件都有屬性與資料行的對應，根據的是資料列在該資料行中的值。</td></tr>
<tr><td>AccessInstruction</td><td></td><td></td><td>這包含如何要求存取資料來源的資訊。這項資訊是目錄入口網站顯示的「 要求存取 」欄位。</td></tr>
<tr><td></td><td>mimeType</td><td>字串</td><td>內容的 mime 類型。</td></tr>
<tr><td></td><td>內容</td><td>字串</td><td>如何取得這項資料資產的指示。可能是一個 URL、電子郵件地址或一組指示。</td></tr>

<tr><td>TableDataProfile</td><td></td><td></td><td></td></tr>
<tr><td></td><td>numberOfRows</td></td><td>int</td><td>在此資料集的資料列數目。</td></tr>
<tr><td></td><td>size</td><td>long</td><td>以位元組為單位的資料集大小。 </td></tr>
<tr><td></td><td>schemaModifiedTime</td><td>字串</td><td>上次修改結構描述的時間。</td></tr>
<tr><td></td><td>dataModifiedTime</td><td>字串</td><td>上次修改此資料集 (加入、修改或刪除資料) 的時間。</td></tr>

<tr><td>ColumnsDataProfile</td><td></td><td></td><td></td></tr>
<tr><td></td><td>columns</td></td><td>ColumnDataProfile[]</td><td>在此資料集的資料列數目。</td></tr>

<tr><td>文件</td><td></td><td></td><td>指定的資產只能有一個相關聯的文件。</td></tr>
<tr><td></td><td>mimeType</td><td>字串</td><td>內容的 mime 類型。</td></tr>
<tr><td></td><td>內容</td><td>字串</td><td>文件內容。</td></tr>


</table>

### 常見的類型

常見類型可作為屬性的類型，而不是項目的類型。

<table><tr><td><b>常見類型</b></td><td><b>屬性</b></td><td><b>資料類型</b></td><td><b>註解</b></td></tr><tr><td>DataSourceInfo</td><td></td><td></td><td></td></tr><tr><td></td><td>sourceType</td><td>字串</td><td>描述資料來源的類型，也就是 SQL Server、Oracle 資料庫等等...  </td></tr><tr><td></td><td>objectType</td><td>字串</td><td>描述資料來源中的物件類型，也就是 SQL Server 的資料表、檢視。</td></tr><tr><td></td><td>formatType</td><td>字串</td><td>描述資料結構。目前的值為結構化或非結構化。</td></tr><tr><td>SecurityPrincipal</td><td></td><td></td><td></td></tr><tr><td></td><td>upn</td><td>字串</td><td>使用者的唯一電子郵件地址。</td></tr><tr><td></td><td>firstName</td><td>字串</td><td>使用者的名字 (用於顯示)。</td></tr><tr><td></td><td>lastName</td><td>字串</td><td>使用者的姓氏 (用於顯示)。</td></tr><tr><td>欄</td><td></td><td></td><td></td></tr><tr><td></td><td>名稱</td><td>字串</td><td>資料行或屬性的名稱。</td></tr><tr><td></td><td>類型</td><td>字串</td><td>資料行或屬性的資料類型。允許的類型取決於資產的資料 sourceType。僅支援一部分類型。</td></tr><tr><td></td><td>maxLength</td><td>int</td><td>資料行或屬性允許的長度上限。衍生自資料來源。只適用於某些來源類型。</td></tr><tr><td></td><td>Precision</td><td>byte</td><td>資料行或屬性的精確度。衍生自資料來源。只適用於某些來源類型。</td></tr><tr><td></td><td>isNullable</td><td>Boolean</td><td>是否允許資料行有 Null 值。衍生自資料來源。只適用於某些來源類型。</td></tr><tr><td></td><td>expression</td><td>字串</td><td>如果值是導出資料行，此欄位會包含表示此值的運算式。衍生自資料來源。只適用於某些來源類型。</td></tr><tr><td></td><td>defaultValue</td><td>物件</td><td>如果物件的 insert 陳述式中未指定任何值，則插入預設值。衍生自資料來源。只適用於某些來源類型。</td>

</tr><tr><td>ColumnDescription</td><td></td><td></td><td></td></tr>
<tr><td></td><td>tags</td><td>string[]</td><td>描述資料行的標記陣列。</td></tr>
<tr><td></td><td>說明</td><td>字串</td><td>用來描述資料行的描述。</td></tr><tr><td></td><td>columnName</td><td>字串</td><td>此資訊所參考的資料行名稱。</td></tr>

</tr><tr><td>ColumnDataProfile</td><td></td><td></td><td></td></tr>
<tr><td></td><td>columnName </td><td>字串</td><td>資料行的名稱。</td></tr>
<tr><td></td><td>類型 </td><td>字串</td><td>資料行的類型。</td></tr>
<tr><td></td><td>min </td><td>字串</td><td>在此資料集內的最小值。</td></tr>
<tr><td></td><td>max </td><td>字串</td><td>在此資料集內的最大值。</td></tr>
<tr><td></td><td>avg </td><td>double</td><td>在此資料集內的平均值。</td></tr>
<tr><td></td><td>stdev </td><td>double</td><td>此資料集的標準差。</td></tr>
<tr><td></td><td>nullCount </td><td>int</td><td>在此資料集內 null 值的計數。</td></tr>
<tr><td></td><td>distinctCount  </td><td>int</td><td>在此資料集內相異值的計數。</td></tr>



</table>

## 角色和授權

Microsoft Azure 資料目錄對資產和註解的 CRUD 作業提供授權功能。

## 重要概念

Azure 資料目錄使用兩種授權機制：

- 以角色為基礎的授權
- 以權限為基礎的授權

### 角色

有 3 個角色：**系統管理員**、**擁有者** 和 **參與者**。每個角色有其範圍和權限，於下表中摘要說明。

<table><tr><td><b>角色</b></td><td><b>範圍</b></td><td><b>權限</b></td></tr><tr><td>系統管理員</td><td>目錄 (也就是目錄中的所有資產/註解)</td><td>Read Delete ViewRoles ChangeOwnership ChangeVisibility ViewPermissions</td></tr><tr><td>擁有者</td><td>每個資產 (也就是根項目)</td><td>Read Delete ViewRoles ChangeOwnership ChangeVisibility ViewPermissions</td></tr><tr><td>參與者</td><td>每個個別的資產和註解</td><td>Read Update Delete ViewRoles 注意事項：如果撤銷 Contributor 在項目上的 Read 權限，則會撤鎖所有權限</td></tr></table>

> [AZURE.NOTE] **Read**、**Update**、**Delete**、**ViewRoles** 權限適用於任何項目 (資產或註解)，而 **TakeOwnership**、**ChangeOwnership**、**ChangeVisibility**、**ViewPermissions** 只適用於根資產。
>
>**Delete** 權限適用於項目及其底下的任何子項目或單一項目。例如，刪除資產時，也會刪除該資產的任何註解。

### 權限

權限是存取控制項目的清單。每個存取控制項目指派一組權限給安全性主體。權限只能在資產 (也就是根項目) 上指定，可套用至資產和任何子項目。

在 **Azure 資料目錄**預覽期間，僅支援權限清單中的 **Read** 權限，以便該情況下限制資產的可見性。

根據預設，任何已驗證的使用者對目錄中的任何項目都具有 **Read** 權限，除非權限中已限制只能看到某一組主體。

## REST API

**PUT** 和 **POST** 檢視項目要求可用於控制角色和權限：除了項目裝載，還可指定兩個系統屬性 **\_\_roles** 和 **\_\_permissions**。

> [AZURE.NOTE]
>
> **\_\_permissions** 僅適用於根項目。
>
> **擁有者** 角色僅適用於根項目。
>
> 根據預設，在目錄中建立項目時，其 **參與者** 會設定為目前已驗證的使用者。如果項目可以由每個人更新，則第一次發佈項目時，應該在 **\_\_roles** 屬性中，將 **參與者** 設定為 <Everyone> 特殊安全性主體 (請參閱下面範例)。**參與者** 無法變更，而且在項目存留期間都維持不變 (也就是即使 **系統管理員** 或 **擁有者** 都沒有權限變更 **參與者**)。明確設定 **參與者** 時，唯一支援的值是 <Everyone>：亦即 **參與者** 只能是建立項目的使用者，或是 <Everyone>。

###範例
**發佈項目時將參與者設定為 <Everyone>。** 特殊安全性主體 <Everyone> 具有 objectId "00000000-0000-0000-0000-000000000201"。**文章** https://123154bb...6aad6370ee14.datacatalog.azure.com/default/views/tables/?api-version=2015-07.1.0-Preview **內文**

	{
	    "__roles": [
	        {
	            "role": "Contributor",
	            "members": [
	                {
	                    "objectId": "00000000-0000-0000-0000-000000000201"
	                }
	            ]
	        }
	    ],
	    … other table properties
	}

**指派擁有者，並限制現有根項目的可見性** **PUT** https://123154bb...6aad6370ee14.datacatalog.azure.com/default/views/tables/042297b0...1be45ecd462a?api-version=2015-07.1.0-Preview

	{
	    "__roles": [
	        {
	            "role": "Owner",
	            "members": [
	                {
	                    "objectId": "c4159539-846a-45af-bdfb-58efd3772b43",
	                    "upn": "user1@contoso.com"
	                },
	                {
	                    "objectId": "fdabd95b-7c56-47d6-a6ba-a7c5f264533f",
	                    "upn": "user2@contoso.com"
	                }
	            ]
	        }
	    ],
	    "__permissions": [
	        {
	            "principal": {
	                "objectId": "27b9a0eb-bb71-4297-9f1f-c462dab7192a",
	                "upn": "user3@contoso.com"
	            },
	            "rights": [
	                {
	                    "right": "Read"
	                }
	            ]
	        },
	        {
	            "principal": {
	                "objectId": "4c8bc8ce-225c-4fcf-b09a-047030baab31",
	                "upn": "user4@contoso.com"
	            },
	            "rights": [
	                {
	                    "right": "Read"
	                }
	            ]
	        }
	    ]
	}

> [AZURE.NOTE] 在 PUT 中，不需要在內文中指定項目裝載：PUT 可以用來直接更新角色和 (或) 權限。

<!--Image references-->
[1]: ./media/data-catalog-developer-concepts/concept2.png

<!---HONumber=AcomDC_0211_2016-->