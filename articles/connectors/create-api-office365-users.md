<properties
	pageTitle="將 Office 365 使用者 API 新增到邏輯應用程式 | Microsoft Azure"
	description="搭配 REST API 參數來使用 Office 365 使用者 API 的概觀"
	services=""	
	documentationCenter="" 	
	authors="msftman"	
	manager="erikre"	
	editor="" 
	tags="connectors" />

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="02/22/2016"
ms.author="deonhe"/>

# 開始使用 Office 365 使用者 API

Office 365 使用者連線提供者可讓您利用自己的 Office 365 帳戶，來存取您組織中使用者的設定檔。您可以執行各種動作，例如取得您的設定檔、某個使用者設定檔、某個使用者的管理員或直屬員工，還能讓您更新某個使用者設定檔。

>[AZURE.NOTE] 這一版的文章適用於邏輯應用程式 2015-08-01-preview 結構描述版本。對於 2014-12-01-preview 結構描述版本，請按一下 [Office 365 API](../app-service-logic/app-service-logic-connector-office365.md)。


您可以利用 Office 365 使用者來：

* 建置邏輯應用程式
* 建置 Power 應用程式

如需如何在 PowerApps Enterprise 中新增 API 的資訊，請前往[在 PowerApps 中為 API 註冊](../power-apps/powerapps-register-from-available-apis.md)。

如要在邏輯應用程式中新增作業，請參閱[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

## 我們來談談觸發程序及動作。

Office 365 使用者 API 可當做動作來使用；它沒有觸發程序。所有 API 都支援 JSON 和 XML 格式的資料。

 Office 365 使用者 API 提供下列動作及/或觸發程序：

### Office 365 使用者的動作
您可以採取下列動作：

|動作|說明|
|--- | ---|
|MyProfile|擷取目前使用者的設定檔|
|UserProfile|擷取特定的使用者設定檔|
|Manager|擷取指定使用者之管理員的使用者設定檔|
|DirectReports|取得直屬員工|
|SearchUser|擷取使用者設定檔的搜尋結果|
## 建立至 Office 365 使用者的連線
如要使用 Office 365 使用者 API，您必須先建立「連線」，再提供下列屬性的詳細資料：

|屬性| 必要|說明|
| ---|---|---|
|權杖|是|提供 Office 365 的認證|


>[AZURE.TIP] 您可以在其他的邏輯應用程式中使用這個連線。

## Office 365 使用者 REST API 參考
#### 本文件適用的版本：1.0


### 取得我的設定檔 


 擷取目前使用者的設定檔```GET: /users/me```

這個呼叫沒有參數
#### Response

|名稱|說明|
|---|---|
|200|作業已順利完成|
|202|作業已順利完成|
|400|BadRequest|
|401|未經授權|
|403|禁止|
|500|內部伺服器錯誤|
|預設值|作業失敗。|
------



### 取得使用者設定檔 


 擷取特定的使用者設定檔```GET: /users/{userId}```



| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|userId|字串|yes|路徑|無|使用者主體名稱或電子郵件識別碼|


#### Response

|名稱|說明|
|---|---|
|200|作業已順利完成|
|202|作業已順利完成|
|400|BadRequest|
|401|未經授權|
|403|禁止|
|500|內部伺服器錯誤|
|預設值|作業失敗。|
------



### 取得管理員 


 擷取指定使用者之管理員的使用者設定檔```GET: /users/{userId}/manager```



| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|userId|字串|yes|路徑|無|使用者主體名稱或電子郵件識別碼|


#### Response

|名稱|說明|
|---|---|
|200|作業已順利完成|
|202|作業已順利完成|
|400|BadRequest|
|401|未經授權|
|403|禁止|
|500|內部伺服器錯誤|
|預設值|作業失敗。|
------



### 取得直屬員工 


 取得直屬員工```GET: /users/{userId}/directReports```



| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|userId|字串|yes|路徑|無|使用者主體名稱或電子郵件識別碼|


#### Response

|名稱|說明|
|---|---|
|200|作業已順利完成|
|202|作業已順利完成|
|400|BadRequest|
|401|未經授權|
|403|禁止|
|500|內部伺服器錯誤|
|預設值|作業失敗。|
------



### 搜尋使用者 


 擷取使用者設定檔的搜尋結果```GET: /users```



| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|searchTerm|字串|no|query|無|搜尋字串 (適用於：顯示名稱、名字、姓氏、郵件、郵件暱稱，及使用者主體名稱)|


#### Response

|名稱|說明|
|---|---|
|200|作業已順利完成|
|202|作業已順利完成|
|400|BadRequest|
|401|未經授權|
|403|禁止|
|500|內部伺服器錯誤|
|預設值|作業失敗。|
------



## 物件定義： 

 **User**：使用者模型類別。

User 的必要屬性：

識別碼

**所有屬性**：


| 名稱 | 資料類型 |
|---|---|
|DisplayName|字串|
|GivenName|字串|
|Surname|字串|
|Mail|字串|
|MailNickname|字串|
|TelephoneNumber|字串|
|AccountEnabled|布林值|
|識別碼|字串|
|UserPrincipalName|字串|
|部門|字串|
|JobTitle|字串|


## 後續步驟
當您把 Office 365 API 新增到 PowerApps Enterprise 之後，[請授與使用者能使用自己應用程式中 API 的權限](../power-apps/powerapps-manage-api-connection-user-access.md)。

[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

<!---HONumber=AcomDC_0224_2016-->