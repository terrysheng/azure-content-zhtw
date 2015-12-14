<properties
	pageTitle="在 PowerApps 中加入或建立新的 API 以及授與使用者權限 |Microsoft Azure"
	description="加入、建立和設定新的 API、連接或連線設定檔，以及在 Azure 入口網站中授與使用者存取的權限和權利"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="MandiOhlinger"
	manager="dwrede"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="11/25/2015"
   ms.author="litran"/>


# 加入新的 API、加入連接和授與使用者存取權

API 存在於 [App Service 環境](powerapps-get-started-azure-portal.md)內。您可以從可用的 PowerApps 的 API、從 App Service 環境代管的 API Apps，或從 Swagger 2.0 建立 API。有許多預先建置的 API 可讓您輕鬆加入 PowerApps。您也可以上傳 JSON 格式或 Swagger 2.0 的 API。

此主題：

- 列出將 API 加入 PowerApps 中的步驟，並授與貴公司使用者使用 API 的權限，包括變更其屬性。
- 列出將連接加入 API 中的步驟，並授與貴公司使用者使用連接的權限。


#### 開始使用的必要條件

- 啟用 [Azure 訂用帳戶的 PowerApps](powerapps-get-started-azure-portal.md)。
- 建立 [App Service 環境](powerapps-get-started-azure-portal.md)。
- 使用下列任一方法建立 API：  
	- 建立 [Microsoft 管理的 API 或 IT 管理的 API](powerapps-register-from-available-apis.md)。
	- 建立 [App Service 環境](powerapps-register-api-hosted-in-app-service.md)代管的 API。
	- 使用 [Swagger 2.0 API 定義](powerapps-register-existing-api-from-api-definition.md)來建立。


## 讓使用者存取 API
既然 API 已建立並加入 App Service 環境中，是時候授與貴公司的使用者使用權限了。

1. 在 PowerApps 中，選取 [管理 API]，然後選取 API。例如，如果您建立了 *MS Power BI* API，請選取它以開啟其刀鋒視窗。選取 [API 使用者存取]：![][1]  

2. 選取 [加入] 加入使用者，並選取權限。完成時，選取 [加入] 儲存變更。[API 使用者存取] 視窗的使用者或群組計數會增加。


## 將新的連接加入 API 中
下一個步驟是建立 API「連接」，有點像連接字串。這可讓 API 順利連接到您的「後端」系統。PowerApps Enterprise 公開預覽版本中，只能加入和設定 SQL Server 連接。日後會加入更多。

- [建立 SQL Server 的連接](powerapps-create-api-sqlserver.md)

## 授與使用者連接的執行階段存取權
現在，授與貴公司使用者使用連接的權限。

1. 開啟 API，選取 [連接]，然後選取特定的連接。這會開啟新的刀鋒視窗，在上方列出您的連接名稱。 
2. 在這個新的刀鋒視窗中，選取 [連接使用者存取]。下例中選取了 [混合式通道] 連接。新的刀鋒視窗隨即開啟，請在這裡選取 [連接使用者存取]：![][2]
  
3. 在 [連接使用者存取] 中選取 [加入]，再選取要授與的權限：![][3]
  
4. 加入使用者或群組。選取 [加入] 儲存變更。

現在使用者已有 API 及其連接的權限，使用者就可以將這些 API 加入自己在 PowerApps 建立的應用程式中。具體而言：

- 使用者可以看到 API 列在 PowerApps 的 [可用連接] 下。
- 使用者可以在 PowerApps 的 [我的連接] 下看到連接。


## 刪除 API
您也可以刪除先前加入的 API。在 PowerApps 中，依次選取 [管理 API]、API 和 [刪除]：![][4]


## 摘要和後續步驟
您在本主題中：

- 加入了 API 並授與貴公司使用者使用權限。您也可以使用這些步驟隨時管理執行階段存取。例如，如果使用者 A 離職，您可以使用 Azure 入口網站輕鬆移除這位使用者的權限。如果使用者 B 加入貴公司，則為相同案例。
- 加入連接 (類似於連接字串)。這個步驟讓 Azure 代管的 API 連接到您的系統，像是內部部署 SQL Server。您也授與了使用者貴公司的連接使用權限。 
- 視工作而定，您使用不同的刀鋒視窗。若要加入連接，請開啟 API 並使用其刀鋒視窗。若要授與使用者存取，請開啟 API 或連接，視授與的存取而定。 
- 您也可以刪除在 App Service 環境中建立的任一 API。

接下來，您可以[管理和監視 PowerApps](powerapps-manage-monitor-usage.md)。

[1]: ./media/powerapps-manage-api-connection-user-access/apiuseraccess.png
[2]: ./media/powerapps-manage-api-connection-user-access/connectionuseraccess.png
[3]: ./media/powerapps-manage-api-connection-user-access/selectpermission.png
[4]: ./media/powerapps-manage-api-connection-user-access/deleteapi.png

<!---HONumber=AcomDC_1203_2015-->