<properties
	pageTitle="在 PowerApps Enterprise 或邏輯應用程式中加入 Office 365 Users API | Microsoft Azure"
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
ms.date="03/03/2016"
ms.author="deonhe"/>

# 開始使用 Office 365 使用者 API

連接至 Office 365 使用者，以取得設定檔、搜尋使用者等等。Office 365 使用者 API 可以從下列位置使用：

- 邏輯應用程式 
- PowerApps

> [AZURE.SELECTOR]
- [邏輯應用程式](../articles/connectors/create-api-office365-users.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-office365-users.md)

&nbsp;

>[AZURE.NOTE] 這一版的文章適用於邏輯應用程式 2015-08-01-preview 結構描述版本。如需 2014-12-01-preview 結構描述版本，請按一下 [Office 365 API](../app-service-logic/app-service-logic-connector-office365.md)。


您可以利用 Office 365 使用者來：

- 根據您從 Office 365 使用者所取得的資料，來建置您的商務流程。 
- 使用可取得直屬員工、取得管理員的使用者設定檔等等的動作。這些動作會收到回應，然後輸出能讓其他動作使用的資料。例如，取得某人的直屬員工，然後利用此資訊更新 SQL Azure 資料庫。 
- 新增 Office 365 使用者 API 至 PowerApps Enterprise。接著，您的使用者便能夠在自己的應用程式中使用這個 API。 

如需如何在 PowerApps Enterprise 中加入 API 的資訊，請移至[在 PowerApps 中註冊 API](../power-apps/powerapps-register-from-available-apis.md)。

如果要在邏輯應用程式中新增作業，請參閱[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

## 觸發程序及動作

Office 365 使用者 API 提供下列動作。無觸發程序。

| 觸發程序 | 動作|
| --- | --- |
|None | <ul><li>取得管理員</li><li>取得我的設定檔</li><li>取得直屬員工</li><li>取得使用者設定檔</li><li>搜尋使用者</li></ul>|

所有 API 皆支援 JSON 與 XML 格式的資料。


## 建立至 Office 365 使用者的連線

當您將這個 API 新增到邏輯應用程式時，您必須登入您的 Office 365 使用者帳戶，並允許邏輯應用程式連線到您的帳戶。

1. 登入您的 Office 365 使用者帳戶。
2. 允許您的邏輯應用程式連線並使用您的 Office 365 帳戶。 

連線建立之後，您需要輸入 Office 365 使用者屬性，像是使用者識別碼。本主題的＜REST API 參考＞一節會說明這些屬性。

>[AZURE.TIP] 您可以在其他的邏輯應用程式中，使用這個相同的 Office 365 使用者連線。


## Office 365 使用者 REST API 參考
適用的版本：1.0。

### 取得我的設定檔 
擷取目前使用者的設定檔。```GET: /users/me```

這個呼叫沒有參數。

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


### 取得使用者設定檔 
擷取特定的使用者設定檔。```GET: /users/{userId}```

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


### 取得管理員 
擷取指定使用者之管理員的使用者設定檔。```GET: /users/{userId}/manager```

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



### 取得直屬員工 
取得直屬員工。```GET: /users/{userId}/directReports```

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



### 搜尋使用者 
擷取使用者設定檔的搜尋結果。```GET: /users```

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



## 物件定義

#### User：使用者模型類別。

|屬性名稱 | 資料類型 |必要
|---|---|---|
|DisplayName|字串|no|
|GivenName|字串|no|
|Surname|字串|no|
|Mail|字串|no|
|MailNickname|字串|no|
|TelephoneNumber|字串|no|
|AccountEnabled|布林值|no|
|識別碼|字串|yes
|UserPrincipalName|字串|no|
|部門|字串|no|
|JobTitle|字串|no|
|mobilePhone|字串|no|


## 後續步驟

[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

返回 [API 清單](apis-list.md)。

<!--References-->
[5]: https://portal.azure.com
[7]: ./media/create-api-office365-users/aad-tenant-applications.PNG
[8]: ./media/create-api-office365-users/aad-tenant-applications-add-appinfo.PNG
[9]: ./media/create-api-office365-users/aad-tenant-applications-add-app-properties.PNG
[10]: ./media/create-api-office365-users/contoso-aad-app.PNG
[11]: ./media/create-api-office365-users/contoso-aad-app-configure.PNG

<!---HONumber=AcomDC_0309_2016-->