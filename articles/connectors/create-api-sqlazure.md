<properties
	pageTitle="在您的邏輯應用程式中新增 SQL Azure API | Microsoft Azure"
	description="搭配 REST API 參數來使用 SQL Azure API 的概觀"
	services=""
	documentationCenter="" 
	authors="MandiOhlinger"
	manager="erikre"
	editor=""
	tags="connectors"/>

<tags
   ms.service="multiple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="02/25/2016"
   ms.author="mandia"/>


# 開始使用 SQL Azure API
連線到 SQL Azure 來管理資料表和資料列，例如插入資料列、取得資料表等等。

您可以從下列應用程式使用 SQL Azure API：

- 邏輯應用程式 

>[AZURE.NOTE] 這一版的文章適用於邏輯應用程式 2015-08-01-preview 結構描述版本。對於 2014-12-01-preview 結構描述版本，請按一下 [SQL 連接器](../app-service-logic/app-service-logic-connector-sql.md)。

SQL Azure 可讓您：

- 根據您從 SQL Azure 所取得的資料，來建置您的商務流程。 
- 使用動作來取得資料列、插入資料列等等。這些動作會收到回應，然後輸出能讓其他動作使用的資料。舉例來說，您可以從 SQL Azure 取得一列資料，然後把該資料新增到 Excel 中。 

如果要在邏輯應用程式中新增作業，請參閱[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。


## 觸發程序及動作
SQL 包含下列動作，但不包含觸發程序。

觸發程序 | 動作
--- | ---
None | <ul><li>取得單一資料列</li><li>取得多個資料列</li><li>插入資料列</li><li>刪除資料列</li><li>取得資料表</li><li>更新資料列</li></ul>

所有 API 都支援 JSON 和 XML 格式的資料。

## 建立至 SQL 的連線
當您將這個 API 新增到邏輯應用程式時，請輸入下列的值：

|屬性| 必要|說明|
| ---|---|---|
|SQL 連接字串|是|輸入您的 SQL Azure 連接字串|

當您建立連線之後，請輸入 SQL 的屬性，例如資料表名稱。本主題的＜REST API 參考＞一節說明這些屬性。

>[AZURE.TIP] 您可以在其他的邏輯應用程式中使用這個連線。

## Swagger REST API 參考
適用的版本：1.0。

### 取得單一資料列 
擷取 SQL 資料表中的單一資料列。```GET: /datasets/default/tables/{table}/items/{id}```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|資料表|字串|yes|路徑|無|SQL 資料表名稱|
|id|字串|yes|路徑|無|要擷取之資料列的唯一識別碼|

#### Response
|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|


### 取得多個資料列 
擷取 SQL 資料表中的多個資料列。 ```GET: /datasets/default/tables/{table}/items```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|資料表|字串|yes|路徑|無|SQL 資料表名稱|
|$skip|integer|no|query|無|要略過的項目數目 (預設值 = 0)|
|$top|integer|no|query|無|要擷取的項目數目上限 (預設值 = 256)|
|$filter|字串|no|query|無|用來限制項目數目的 ODATA filter 查詢|
|$orderby|字串|no|query|無|用來指定項目順序的 ODATA orderBy 查詢|

#### Response
|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|


### 插入資料列 
在 SQL 資料表中插入新的資料列。```POST: /datasets/default/tables/{table}/items```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|資料表|字串|yes|路徑|無|SQL 資料表名稱|
|item|ItemInternalId：字串|yes|body|無|要在指定的 SQL 資料表中插入的資料列|

#### Response
|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|


### 刪除資料列 
刪除 SQL 資料表中的資料列。```DELETE: /datasets/default/tables/{table}/items/{id}```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|資料表|字串|yes|路徑|無|SQL 資料表名稱|
|id|字串|yes|路徑|無|要刪除的資料列的唯一識別碼|

#### Response
|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|


### 取得資料表 
擷取 SQL 資料庫中的資料表。```GET: /datasets/default/tables```

這個呼叫沒有參數。

#### Response
|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|


### 更新資料列 
更新 SQL 資料表中現有的資料列。```PATCH: /datasets/default/tables/{table}/items/{id}```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|資料表|字串|yes|路徑|無|SQL 資料表名稱|
|id|字串|yes|路徑|無|要更新的資料列的唯一識別碼|
|item|ItemInternalId：字串|yes|body|無|值已更新的資料列|

#### Response
|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|

## 物件定義

#### DataSetsMetadata

|屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|表格式|未定義|no|
|blob|未定義|no|

#### TabularDataSetsMetadata

|屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|來源|字串|no|
|displayName|字串|no|
|urlEncoding|字串|no|
|tableDisplayName|字串|no|
|tablePluralName|字串|no|

#### BlobDataSetsMetadata

|屬性名稱 | 資料類型 |必要 |
|---|---|---|
|來源|字串|no|
|displayName|字串|no|
|urlEncoding|字串|no|

#### TableMetadata

|屬性名稱 | 資料類型 |必要 |
|---|---|---|
|名稱|字串|no|
|title|字串|no|
|x-ms-permission|字串|no|
|結構描述|未定義|no|

#### DataSetsList

|屬性名稱 | 資料類型 |必要 |
|---|---|---|
|value|array|no|

#### DataSet

|屬性名稱 | 資料類型 |必要 |
|---|---|---|
|名稱|字串|no|
|DisplayName|字串|no|

#### 資料表

|屬性名稱 | 資料類型 |必要 |
|---|---|---|
|名稱|字串|no|
|DisplayName|字串|no|

#### 項目

|屬性名稱 | 資料類型 |必要 |
|---|---|---|
|ItemInternalId|字串|no|

#### ItemsList

|屬性名稱 | 資料類型 |必要 |
|---|---|---|
|value|array|no|

#### TablesList

|屬性名稱 | 資料類型 |必要 |
|---|---|---|
|value|array|no|


## 後續步驟

[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

<!---HONumber=AcomDC_0302_2016-->