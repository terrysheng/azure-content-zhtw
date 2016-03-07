<properties
pageTitle="在 PowerApps Enterprise 中新增 Excel API | Microsoft Azure"
description="搭配 REST API 參數使用 Excel API 的概觀"
services=""	
documentationCenter="" 	
authors="msftman"	
manager="erikre"	
editor=""
tags="connectors"/>

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="na"
ms.date="02/23/2016"
ms.author="deonhe"/>

# 開始使用 Excel API

連接至 Excel 以插入資料列、刪除資料列等。

您可以從 PowerApps Enterprise 使用 Excel API。

Excel 可讓您：

- 將 Excel API 新增至 PowerApps Enterprise。接著，您的使用者便能夠在自己的應用程式中使用這個 API。 

如需有關如何在 PowerApps Enterprise 中新增 API 的資訊，請移至[在 PowerApps 中為 API 註冊](../power-apps/powerapps-register-from-available-apis.md)。

如果要在邏輯應用程式中新增作業，請參閱[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

## 觸發程序及動作
Excel 包含下列動作。無觸發程序。

|觸發程序|動作|
|--- | ---|
|None | <ul><li>取得資料列</li><li>插入資料列</li><li>刪除資料列</li><li>取得資料列</li><li>取得資料表</li><li>更新資料列</li></ul>

所有 API 皆支援 JSON 與 XML 格式的資料。

## Swagger REST API 參考
#### 本文件適用的版本：1.0


### 在 Excel 資料表中插入新的資料列
**```POST: /datasets/{dataset}/tables/{table}/items```**



| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|資料集|字串|yes|路徑|無|Excel 檔案名稱|
|資料表|字串|yes|路徑|無|Excel 資料表名稱|
|item| |yes|body|無|要在指定的 Excel 資料表中插入的資料列|


### Response

|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|
------



### 從 Excel 資料表擷取單一資料列
**```GET: /datasets/{dataset}/tables/{table}/items/{id}```**



| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|資料集|字串|yes|路徑|無|Excel 檔案名稱|
|資料表|字串|yes|路徑|無|Excel 資料表名稱|
|id|字串|yes|路徑|無|要擷取的資料列的唯一識別碼|


### Response

|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|
------



### 刪除 Excel 資料表中的資料列。
**```DELETE: /datasets/{dataset}/tables/{table}/items/{id}```**



| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|資料集|字串|yes|路徑|無|Excel 檔案名稱|
|資料表|字串|yes|路徑|無|Excel 資料表名稱|
|id|字串|yes|路徑|無|要刪除的資料列的唯一識別碼|


### Response

|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|
------



### 更新 Excel 資料表中的現有資料列
**```PATCH: /datasets/{dataset}/tables/{table}/items/{id}```**



| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|資料集|字串|yes|路徑|無|Excel 檔案名稱|
|資料表|字串|yes|路徑|無|Excel 資料表名稱|
|id|字串|yes|路徑|無|要更新的資料列的唯一識別碼|
|item| |yes|body|無|值已更新的資料列|


### Response

|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|
------



## 物件定義

#### DataSetsMetadata

| 名稱 | 資料類型 | 必要|
|---|---|---|
|表格式|未定義|no|
|blob|未定義|no|

#### TabularDataSetsMetadata

| 名稱 | 資料類型 |必要|
|---|---|---|
|來源|字串|no|
|displayName|字串|no|
|urlEncoding|字串|no|
|tableDisplayName|字串|no|
|tablePluralName|字串|no|

#### BlobDataSetsMetadata

| 名稱 | 資料類型 |必要|
|---|---|---|
|來源|字串|no|
|displayName|字串|no|
|urlEncoding|字串|no|

#### TableMetadata

| 名稱 | 資料類型 |必要|
|---|---|---|
|名稱|字串|no|
|title|字串|no|
|x-ms-permission|字串|no|
|結構描述|未定義|no|

#### DataSetsList

| 名稱 | 資料類型 |必要|
|---|---|---|
|value|array|no|

#### DataSet

| 名稱 | 資料類型 |必要|
|---|---|---|
|名稱|字串|no|
|DisplayName|字串|no|

#### 資料表

| 名稱 | 資料類型 |必要|
|---|---|---|
|名稱|字串|no|
|DisplayName|字串|no|

#### 項目

| 名稱 | 資料類型 |必要|
|---|---|---|
|ItemInternalId|字串|no|

#### TablesList

| 名稱 | 資料類型 |必要|
|---|---|---|
|value|array|no|

#### ItemsList

| 名稱 | 資料類型 |必要|
|---|---|---|
|value|array|no|


## 後續步驟
[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md) [建立 Power 應用程式](../power-apps/powerapps-get-started-azure-portal.md)

<!---HONumber=AcomDC_0224_2016-->