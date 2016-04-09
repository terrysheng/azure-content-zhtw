<properties
	pageTitle="將 SQL Server API 加入 PowerApps Enterprise 中 | Microsoft Azure"
	description="在貴組織的 App Service 環境中建立或設定新的 SQL Server API，並加入內部部署資料的連接"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="linhtranms"
	manager="dwrede"
	editor=""/>


<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="03/03/2016"
   ms.author="litran"/>


# 在 PowerApps Enterprise 中建立新的 SQL Server API

將 SQL Server API 加入您組織 (租用戶) 的 App Service 環境中。

## 在 Azure 入口網站中建立 API

1. 在 [Azure 入口網站](https://portal.azure.com/)中使用您的工作帳戶登入。例如，使用 yourUserName@*YourCompany*.com 登入。當您這樣做時，將會自動登入您的公司訂用帳戶。 
2. 選取工作列中的 [瀏覽]：![][14]  
3. 在清單中，您可以捲動以尋找 PowerApps 或輸入 *powerapps*：![][15]  
4. 在 **PowerApps** 中，選取 [管理 API]。
5. 在 [管理 API] 中，選取 [加入] 以加入新的 API。
6. 為您的 API 輸入描述性**名稱**。例如，您要將 SQL Server API 加入示範中，您可以將它命名為 *SQLServerDemo*。  	
7. 在 [來源] 中，選取 [可用 API] 選取預先建置的 API，然後選取 [SQL Server]。 
8. 選取 [確定] 以完成步驟。

完成時，新的 SQL Server API 會加入 App Service 環境中。

## 設定 SQL Server 內部部署的連線能力

您可以連接到 SQL Server 內部部署。若要建立這種混合式連接，您可以利用 Azure 現有的混合式網路功能解決方案，包括：

- [ExpressRoute](../expressroute/expressroute-introduction.md)
- [站對站 VPN](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
- [點對站連線能力](../vpn-gateway/vpn-gateway-point-to-site-create.md)  

	> [AZURE.NOTE]  每個 App Service 環境都有與其相關聯的虛擬網路。您可以建立這個虛擬網路的這種網路連線。  
- [混合式連線](../app-service-web/web-sites-hybrid-connection-get-started.md)  

	> [AZURE.NOTE]  App Service 環境中每個已註冊的 API 都有對應的 Web 應用程式。您可以從這個 Web 應用程式建立混合式連接，就像從任何其他 Web 應用程式建立連接一樣。
	
下列範例示範如何建立混合式連接：

1. 選取剛剛建立的 SQL Server API，並選取資源群組。在這個範例中，選取 [sqlconnectordemo] API，然後選取 [DedicatedAses] 資源群組：![資源群組](./media/powerapps-create-api-sqlserver/sqlapi.png)

2.  選取 [資源] 磚，然後選取與 SQL Server API 同名的 Web 應用程式。在這個範例中，選取 [sqlconnectordemo]：![SQL Web 應用程式](./media/powerapps-create-api-sqlserver/sqlwebapp.png)

3.  在 [設定] 中選取 [網路]。選取 [設定混合式連接端點]，然後依照[這些指示](../app-service-web/web-sites-hybrid-connection-get-started.md)建立混合式連接：![網路](./media/powerapps-create-api-sqlserver/network.png)

只要建立並連上混合式連接，就已連接到內部部署伺服器。接下來，建立資料連接並授與使用者存取權：![混合式連接](./media/powerapps-create-api-sqlserver/hybridconn.png)

## 建立 SQL Server API 連接

1. 在 Azure 入口網站中開啟 PowerApps，然後選取 [管理 API]。隨即顯示已設定的 API 清單：![](./media/powerapps-create-api-sqlserver/apilist.png)

2. 選取想要的 API。在這個範例中，依次選取 [SQLServerDemo] 和 [連接]。

3. 在 [連接] 中選取 [加入連接]：![](./media/powerapps-create-api-sqlserver/addconnection.png)

4. 輸入連接的名稱，然後輸入連接字串。輸入連接字串需要知道與要連接的服務有關的某些特定屬性。例如，如果要連接內部部署的 SQL Server，就需要知道使用者名稱、密碼和順利連接所需要的其他屬性。

5. 選取 [加入] 儲存變更。

## 摘要和後續步驟
在本主題中，您加入了 SQL Server API 以連接 SQL Server 內部部署。接下來，請授與使用者此 API 的存取權，讓使用者能夠在其應用程式中加入此 API：

[加入連接並授與使用者存取權](powerapps-manage-api-connection-user-access.md)


[14]: ./media/powerapps-create-api-sqlserver/browseall.png
[15]: ./media/powerapps-create-api-sqlserver/allresources.png

<!---HONumber=AcomDC_0309_2016-->