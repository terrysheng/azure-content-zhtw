<properties 
   pageTitle="開始使用 SQL Database 動態資料遮罩 (Azure Preview 入口網站)" 
   description="如何開始在 Azure Preview 入口網站中使用 SQL 資料庫動態資料遮罩" 
   services="sql-database" 
   documentationCenter="" 
   authors="nadavhelfman"
   manager="jeffreyg" 
   editor="v-romcal"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services" 
   ms.date="07/30/2015"
   ms.author="nadavh; ronmat; v-romcal; sstein"/>

# 開始使用 SQL Database 動態資料遮罩 (Azure Preview 入口網站)

> [AZURE.SELECTOR]
- [Dynamic Data Masking - Azure portal](sql-database-dynamic-data-masking-get-started-portal.md)

## 概觀

SQL Database 動態資料遮罩可藉由遮罩處理，使不具權限的使用者無法看見機密資料。在 Azure SQL Database 的 V12 版中，動態資料遮罩是 Basic、Standard 和 Premium 服務層的預覽功能。

動態資料遮罩可讓客戶在應用程式層級受到最小影響的情況下指定要顯示多少機密資料，而協助防止未經授權者存取機密資料。它是以原則為基礎的安全性功能，可針對指定的資料庫欄位隱藏查詢結果集中的機密資料，而不變更資料庫中的資料。

例如，客服中心支援人員可透過數個身分證號碼的信用卡號碼的數字來識別來電者，但這些資料項目不應完全公開給支援人員。開發人員可以定義要套用到每個查詢結果的遮罩規則，針對結果集中任何身分證號碼或信用卡號碼的末四碼以外的所有數字進行遮罩處理。在另一個範例中，開發人員藉由使用適當的資料遮罩來保護個人識別資訊 (PII) 資料，而得以在生產環境中進行疑難排解用途的查詢，且不會違反法務規定。

## SQL Database 動態資料遮罩的基本概念

如需在 Azure Preview 入口網站中建立動態資料遮罩原則，請在您的 SQL Database 設定刀鋒視窗中，選取 [動態資料遮罩] 作業。在設定動態資料遮罩之前，請檢查您是否正在使用[「下層用戶端」](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md)。


### 動態資料遮罩權限

動態資料遮罩可由　Azure 資料庫管理員、伺服器管理員或安全性主管人員等角色來設定。

### 動態資料遮罩原則

* **特殊權限登入** - 在 SQL 查詢結果中可取得非遮罩資料的一組登入。
  
* **遮罩規則** - 一組規則，定義會遮罩處理的指定欄位和所將使用的遮罩函數。指定的欄位可使用資料庫資料表名稱和資料行名稱來定義。

* **遮罩函數** - 一組方法，可控制不同案例的資料顯示性。

| 遮罩函數 | 遮罩邏輯 |
|----------|---------------|
| **預設值** |**根據指定欄位的資料類型的完整遮罩**<br/><br/>• 針對字串資料類型 (nchar、ntext、nvarchar)，如果欄位的大小少於 8 個字元，則使用 XXXXXXXX 或較少 X。<br/>• 針對數值資料類型 (bigint、bit、decimal、int、money、numeric、smallint、smallmoney、tinyint、float、real) 使用零的值。<br/>• 針對日期/時間資料類型 (date、datetime2、datetime、datetimeoffset、smalldatetime、time) 使用 1900-01-01。<br/>• 針對 SQL 變異，會使用目前類型的預設值。<br/>• 針對 XML，會使用文件 <masked/>。<br/>• 針對特殊資料類型 (timestamp table、hierarchyid、GUID、binary、image、varbinary spatial types) 使用空值。
| **信用卡** |**遮罩方法會公開指定欄位的末四碼**，並新增常數字串做為信用卡格式的前置詞。<br/><br/>XXXX-XXXX-XXXX-1234|
| **身分證號碼** |**遮罩方法會公開指定欄位的末兩碼**，並新增常數字串做為美國社會安全碼格式的前置詞。<br/><br/>XXX-XX-XX12 |
| **電子郵件** | **遮罩方法會公開第一個字母並以 XXX.com 取代網域**，使用的常數字串前置詞會以電子郵件地址為格式。<br/><br/>aXX@XXXX.com |
| **隨機數字** | **遮罩方法會產生一個隨機數字**，其根據為選取的界限與實際資料類型。如果指定的邊界相等，則遮罩函數將是常數。<br/><br/>![導覽窗格](./media/sql-database-dynamic-data-masking-get-started/1_DDM_Random_number.png) |
| **自訂文字** | **遮罩方法會公開第一個和最後一個字元**，並在中間新增自訂填補字串。<br/>prefix[padding]suffix<br/><br/>![導覽窗格](./media/sql-database-dynamic-data-masking-get-started/2_DDM_Custom_text.png) |

  
<a name="Anchor1"></a>
### 已啟用安全性的連接字串

如果您在使用[「下層用戶端 」](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md)，則您必須更新現有的用戶端 (如應用程式)，才能使用修改過的連接字串格式。如需詳細資訊，請按一下[這裡](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md)。

## 使用 Azure Preview 入口網站為您的資料庫設定動態資料遮罩

1. 啟動 Azure Preview 入口網站，位址是 [https://portal.azure.com](https://portal.azure.com)。
	 
2. 導覽至您要遮罩處理的機密資料所在的資料庫組態刀鋒視窗。
	
3. 按一下 [**動態資料遮罩**] 磚，以啟動 [**動態資料遮罩**] 組態刀鋒視窗。

	* 或者，您可以向下捲動至 [**作業**] 區段，然後按一下 [**動態資料遮罩**]。
	 
	![導覽窗格](./media/sql-database-dynamic-data-masking-get-started/4_DDM_Activation.png)<br/><br/>

4. 在**動態資料遮罩**組態分頁按一下**加入遮罩**開啟**加入遮罩規則**組態刀鋒視窗。

	![導覽窗格](./media/sql-database-dynamic-data-masking-get-started/5_ddm_policy_tile.png)<br/><br/>

5. 請選取 [**資料表**] 和 [**資料行**] ，以定義將會遮罩處理的指定欄位。

6. 從機密資料遮罩類別清單中，選擇 [**遮罩欄位格式**]。

	![導覽窗格](./media/sql-database-dynamic-data-masking-get-started/7_DDM_Add_Masking_Rule.png)<br/><br/>

7. 按一下資料遮罩規則刀鋒視窗中的 [**儲存**]，以更新動態遮罩原則中的遮罩資料規則集。

8. 輸入有權存取非遮罩機密資料的特殊權限登入。
 
	![導覽窗格](./media/sql-database-dynamic-data-masking-get-started/6_DDM_Privileged_Logins.png)

	>[AZURE.TIP]若要讓應用程式層級可以對具有特殊權限的應用程式使用者顯示機密資料，請新增用於查詢應用程式和資料庫的 SQL 登入。強烈建議此清單應包含最少的登入數目，以儘可能減少公開的機密資料。

9. 按一下資料遮罩組態刀鋒視窗中的 [**儲存**]，以儲存新的或更新的遮罩原則。

10. 如果您在使用[「下層用戶端 」](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md)，則您必須更新現有的用戶端 (如應用程式)，才能使用修改過的連接字串格式。如需詳細資訊，請參閱「[舊版用戶端](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md)」(英文)。

## 使用 PowerShell Cmdlet 為您的資料庫設定動態資料遮罩

請參閱 [Azure SQL Database Cmdlet](https://msdn.microsoft.com/library/azure/mt163521.aspx)。


## 使用 REST API 為您的資料庫設定動態資料遮罩

請參閱 [Azure SQL Database 的作業](https://msdn.microsoft.com/library/dn505719.aspx)。

<!---HONumber=Oct15_HO3-->