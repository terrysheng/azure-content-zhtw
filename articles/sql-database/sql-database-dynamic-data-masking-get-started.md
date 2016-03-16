<properties
   pageTitle="開始使用 SQL Database 動態資料遮罩 (Azure 入口網站)"
   description="如何開始在 Azure 入口網站中使用 SQL 資料庫動態資料遮罩"
   services="sql-database"
   documentationCenter=""
   authors="ronitr"
   manager="jeffreyg"
   editor="v-romcal"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="12/01/2015"
   ms.author="ronitr; ronmat; v-romcal; sstein"/>


# 開始使用 SQL Database 動態資料遮罩 (Azure 入口網站)

> [AZURE.SELECTOR]
- [Dynamic Data Masking - Azure Classic Portal](sql-database-dynamic-data-masking-get-started-portal.md)

## 概觀

SQL Database 動態資料遮罩可藉由遮罩處理，使不具權限的使用者無法看見機密資料。Azure SQL Database V12 版可支援動態資料遮罩。

動態資料遮罩可讓客戶在應用程式層級受到最小影響的情況下指定要顯示多少機密資料，而協助防止未經授權者存取機密資料。它是以原則為基礎的安全性功能，可針對指定的資料庫欄位隱藏查詢結果集中的機密資料，而不變更資料庫中的資料。

例如，客服中心服務代表可透過數個社會安全號碼或信用卡號碼的數字來識別來電者，但這些資料項目不應完全公開給服務代表。可以定義遮罩規則，以針對任何查詢的結果集中任何社會安全號碼或信用卡號碼的末四碼以外的所有數字進行遮罩處理。在另一個範例中，可以定義適當的資料遮罩來保護個人識別資訊 (PII) 資料，以便開發人員在生產環境中進行疑難排解用途的查詢，且不會違反法務規定。

## SQL Database 動態資料遮罩的基本概念

如需在 Azure 入口網站中建立動態資料遮罩原則，請在您的 SQL Database 設定刀鋒視窗或設定刀鋒視窗中，選取 [動態資料遮罩] 作業。


### 動態資料遮罩權限

動態資料遮罩可由　Azure 資料庫管理員、伺服器管理員或安全性主管人員等角色來設定。

### 動態資料遮罩原則

* **從遮罩處理中排除的 SQL 使用者** - 一組 SQL 使用者或 AAD 身分識別，將在 SQL 查詢結果中取得未經遮罩處理的資料。請注意，具有系統管理員權限的使用者永遠會從遮罩處理中排除，而且將會看到沒有任何遮罩的原始資料。

* **遮罩規則** - 一組規則，定義會遮罩處理的指定欄位和所將使用的遮罩函數。指定的欄位可使用資料庫結構描述名稱、資料表名稱和資料行名稱來定義。

* **遮罩函數** - 一組方法，可控制不同案例的資料顯示性。

| 遮罩函數 | 遮罩邏輯 |
|----------|---------------|
| **預設值** |**根據指定欄位的資料類型的完整遮罩**<br/><br/>• 針對字串資料類型 (nchar、ntext、nvarchar)，如果欄位的大小少於 4 個字元，則使用 XXXX 或較少 X。<br/>• 針對數值資料類型 (bigint、bit、decimal、int、money、numeric、smallint、smallmoney、tinyint、float、real) 使用零的值。<br/>• 針對日期/時間資料類型 (date、datetime2、datetime、datetimeoffset、smalldatetime、time) 使用 1900-01-01。<br/>• 針對 SQL 變異，會使用目前類型的預設值。<br/>• 針對 XML，會使用文件 <masked/>。<br/>• 針對特殊資料類型 (timestamp table、hierarchyid、GUID、binary、image、varbinary spatial types) 使用空值。
| **信用卡** |**遮罩方法會公開指定欄位的末四碼**，並新增常數字串做為信用卡格式的前置詞。<br/><br/>XXXX-XXXX-XXXX-1234|
| **社會安全號碼** |**遮罩方法會公開指定欄位的末四碼**，並新增常數字串做為美國社會安全號碼格式的前置詞。<br/><br/>XXX-XX-1234 |
| **電子郵件** | **遮罩方法會公開第一個字母並以 XXX.com 取代網域**，使用的常數字串前置詞會以電子郵件地址為格式。<br/><br/>aXX@XXXX.com |
| **隨機數字** | **遮罩方法會產生一個隨機數字**，其根據為選取的界限與實際資料類型。如果指定的邊界相等，則遮罩函數將是常數。<br/><br/>![導覽窗格](./media/sql-database-dynamic-data-masking-get-started/1_DDM_Random_number.png) |
| **自訂文字** | **遮罩方法會公開第一個和最後一個字元**，並在中間新增自訂填補字串。如果原始字串小於公開的前置詞和後置詞，則只會使用填補字串。<br/>prefix[padding]suffix<br/><br/>![導覽窗格](./media/sql-database-dynamic-data-masking-get-started/2_DDM_Custom_text.png) |


<a name="Anchor1"></a>
### 要遮罩處理的建議欄位

DDM 建議引擎會將您資料庫中的特定欄位標示為潛在敏感性欄位，而這類欄位可能適合進行遮罩處理。在入口網站的 [動態資料遮罩] 刀鋒視窗中，您會看到您的資料庫的建議資料行。您只需要對一或多個資料行按一下 [新增遮罩]，然後按一下 [儲存]，以便對這些欄位套用遮罩。

## 使用 Azure 入口網站為您的資料庫設定動態資料遮罩

1. 啟動 Azure 入口網站，位址是 [https://portal.azure.com](https://portal.azure.com)。

2. 導覽至您要遮罩處理的敏感性資料所在資料庫的設定刀鋒視窗。

3. 按一下 [動態資料遮罩] 磚，以啟動 [動態資料遮罩] 組態刀鋒視窗。

	* 或者，您可以向下捲動至 [作業] 區段，然後按一下 [動態資料遮罩]。

    ![導覽窗格](./media/sql-database-dynamic-data-masking-get-started/4_ddm_settings_tile.png)<br/><br/>


4. 在 [動態資料遮罩] 設定刀鋒視窗中，您可能會看到建議引擎已標示要進行遮罩處理的某些資料庫資料行。若要接受建議，只要對一或多個資料行按一下 [新增遮罩]，就會根據此資料行的預設類型建立遮罩。您可按一下遮罩規則並編輯遮罩欄位格式，使其成為您選擇的不同格式，即可變更遮罩函數。務必按一下 [儲存] 以儲存您的設定。

    ![導覽窗格](./media/sql-database-dynamic-data-masking-get-started/5_ddm_recommendations.png)<br/><br/>


5. 若要為資料庫中的任何資料行新增遮罩，請在 [動態資料遮罩] 設定刀鋒視窗的頂端，按一下 [新增遮罩] 以開啟 [新增遮罩規則] 設定刀鋒視窗。

    ![導覽窗格](./media/sql-database-dynamic-data-masking-get-started/6_ddm_add_mask.png)<br/><br/>

6. 請選取 [結構描述]、[資料表] 和 [資料行]，以定義將會遮罩處理的指定欄位。

7. 從敏感性資料遮罩類別清單中，選擇 [遮罩欄位格式]。

    ![導覽窗格](./media/sql-database-dynamic-data-masking-get-started/7_ddm_mask_field_format.png)<br/><br/>

8. 按一下資料遮罩規則刀鋒視窗中的 [儲存]，以更新動態遮罩原則中的遮罩資料規則集。

9. 輸入應從遮罩處理中排除，並可存取未經遮罩處理之敏感性資料的 SQL 使用者或 AAD 身分識別。這應該是以分號分隔的使用者清單。請注意，具有系統管理員權限的使用者永遠都有未經遮罩處理之原始資料的存取權。

    ![導覽窗格](./media/sql-database-dynamic-data-masking-get-started/8_ddm_excluded_users.png)

	>[AZURE.TIP] 若要讓應用程式層級可以對具有特殊權限的應用程式使用者顯示敏感性資料，請新增應用程式用於查詢資料庫的 SQL 使用者或 ADD 身分識別。強烈建議此清單應包含最少的特殊權限使用者數目，以儘可能減少公開的敏感性資料。

10. 按一下資料遮罩組態刀鋒視窗中的 [儲存]，以儲存新的或更新的遮罩原則。

## 使用 PowerShell Cmdlet 為您的資料庫設定動態資料遮罩

請參閱 [Azure SQL Database Cmdlet](https://msdn.microsoft.com/library/azure/mt574084.aspx)。


## 使用 REST API 為您的資料庫設定動態資料遮罩

請參閱 [Azure SQL Database 的作業](https://msdn.microsoft.com/library/dn505719.aspx)。

<!---HONumber=AcomDC_0128_2016-->