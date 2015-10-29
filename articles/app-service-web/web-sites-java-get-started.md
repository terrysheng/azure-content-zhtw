<properties
	pageTitle="在 Azure App Service 中建立 Java Web 應用程式 | Microsoft Azure"
	description="本教學課程示範如何將 Java Web 應用程式部署至 Azure App Service。"
	services="app-service\web"
	documentationCenter="java"
	authors="rmcmurray"
	manager="wpickett"
	editor="jimbe"/>
<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="Java"
	ms.topic="hero-article"
	ms.date="08/31/2015"
	ms.author="robmcm"/>

# 在 Azure App Service 中建立 Java Web 應用程式

> [AZURE.SELECTOR]
- [.Net](web-sites-dotnet-get-started.md)
- [Node.js](web-sites-nodejs-develop-deploy-mac.md)
- [Java](web-sites-java-get-started.md)
- [PHP - Git](web-sites-php-mysql-deploy-use-git.md)
- [PHP - FTP](web-sites-php-mysql-deploy-use-ftp.md)
- [Python](web-sites-python-ptvs-django-mysql.md)

本教學課程說明如何使用 Azure Preview 入口網站[在 Azure App Service 中建立 Web 應用程式](http://go.microsoft.com/fwlink/?LinkId=529714)。您可以從 Azure Marketplace 選擇 Web 應用程式範本，或可對 Java 建立一般 Web 應用程式，並手動加以設定。

如果您不打算使用這些技術 (例如，如果您要自訂應用程式容器)，請參閱將[自訂的 Java Web 應用程式上傳至 Azure](web-sites-java-custom-upload.md)。

> [AZURE.NOTE]若要完成此教學課程，您需要 Microsoft Azure 帳戶。如果您沒有這類帳戶，可以[啟用自己的 MSDN 訂戶權益][]或是[申請免費試用][]。
>
> 如果您想要在註冊 Azure 帳戶之前先開始使用 Azure App Service，請移至[試用 App Service][]。您可以於該處，在 App Service 中立即建立短期的入門 Web app - 不需信用卡，不需任何承諾。

## 從 Azure Marketplace 選擇 Web 應用程式範本

本節說明如何使用 Azure Marketplace 來建立 Java Web 應用程式。

1. 登入 [Azure Preview 入口網站](https://portal.azure.com/)。

2. 按一下 [新增] > [Marketplace]。

	![](./media/web-sites-java-get-started/newmarketplace.png)

5. 按一下 [Web + 行動]。

	您可能必須向左捲動，才能看到 [Marketplace] 刀鋒視窗，在這裡您可以選取 [Web + 行動]。

6. 在 [搜尋] 文字方塊中輸入 Java 應用程式伺服器名稱，例如 **Apache Tomcat** 或 **Jetty**，然後按 Enter 鍵。

4. 在搜尋結果中，按一下 Java 應用程式伺服器。

	![](./media/web-sites-java-get-started/webmobilejetty.png)

5. 在第一個 [Apache Tomcat] 或 [Jetty] 刀鋒視窗中，按一下 [建立]。

	![](./media/web-sites-java-get-started/jettyblade.png)

4. 在下一個 [Apache Tomcat] 或 [Jetty] 刀鋒視窗中，於 [Web 應用程式] 方塊中輸入 Web 應用程式的名稱。

	此名稱在 azurewebsites.net 網域中必須是唯一的，因為 Web 應用程式的 URL 將是 {name}.azurewebsites.net。如果您輸入的名稱不是唯一的，紅色驚嘆號會出現在文字方塊中。

5. 選取 [資源群組] 或建立新的資源群組。

	如需資源群組的詳細資訊，請參閱[使用 Azure Preview 入口網站管理您的 Azure 資源](../resource-group-portal.md)。

5. 選取 [App Service 方案/位置]，或建立新的 App Service 方案/位置。

	如需 App Service 方案的詳細資訊，請參閱 [Azure App Service 方案概觀](../azure-web-sites-web-hosting-plans-in-depth-overview.md)

6. 按一下 [建立]。

	![](./media/web-sites-java-get-started/jettyportalcreate2.png)

	Azure 很快 (通常不到一分鐘) 就會完成建立新的 Web 應用程式。

7. 按一下 [Web 應用程式] > [{您的新 Web 應用程式}]。

8. 按一下 [URL] 瀏覽至新網站。

	![](./media/web-sites-java-get-started/jettyurl.png)

	如果選擇 Tomcat，您會看到類似下列範例的頁面。

	![使用 Apache Tomcat 的 Web 應用程式](./media/web-sites-java-get-started/tomcat.png)

	如果選擇 Jetty，您會看到類似下列範例的頁面。

	![使用 Jetty 的 Web 應用程式](./media/web-sites-java-get-started/jetty.png)

現在，您已建立含有應用程式容器的 Web 應用程式，如需如何將應用程式上傳至該 Web 應用程式的相關資訊，請參閱[後續步驟](#next-steps)一節。

## 對 Java 建立 Web 應用程式，並手動設定它

本節說明如何對 Java 建立 Web 應用程式，並手動設定它。

1. 登入 [Azure Preview 入口網站](https://portal.azure.com/)。

2. 按一下 [新增] > [Web + 行動]。

6. 按一下 [Web 應用程式]。

4. 在 [Web 應用程式] 方塊中，輸入 Web 應用程式的名稱。

	此名稱在 azurewebsites.net 網域中必須是唯一的，因為 Web 應用程式的 URL 將是 {name}.azurewebsites.net。如果您輸入的名稱不是唯一的，紅色驚嘆號會出現在文字方塊中。

5. 選取 [資源群組] 或建立新的資源群組。

	如需資源群組的詳細資訊，請參閱[使用 Azure Preview 入口網站管理您的 Azure 資源](../resource-group-portal.md)。

5. 選取 [App Service 方案/位置]，或建立新的 App Service 方案/位置。

	如需 App Service 方案的詳細資訊，請參閱 [Azure App Service 方案概觀](../azure-web-sites-web-hosting-plans-in-depth-overview.md)

6. 按一下 [建立]。
 
11. 建立 Web 應用程式後，按一下 [Web Apps] > [{您的 Web 應用程式}。
 
13. 在 [Web 應用程式] 刀鋒視窗中，按一下 [設定]。

12. 按一下 [應用程式設定]。

13. 選擇所需的 [Java 版本]。

14. 選擇所需的 [Web 容器]。

15. 按一下 [儲存]。

	幾分鐘內，您的 Web 應用程式即會變成 Java 型 Web 應用程式。

7. 按一下 [Web 應用程式] > [{您的新 Web 應用程式}]。

8. 按一下 [URL] 瀏覽至新網站。

	網頁會確認您已建立 Java 型 Web 應用程式。

## 後續步驟

此時，在 Azure App Service 中，您會擁有在 Web 應用程式中執行的 Java 應用程式伺服器。若要將您自己的程式碼部署至網頁，請參閱[將應用程式或網頁新增至 Java Web 應用程式](web-sites-java-add-app.md)。

如需在 Azure 中開發 Java 應用程式的詳細資訊，請參閱 [Java 開發人員中心](/develop/java/)。

<!-- External Links -->
[啟用自己的 MSDN 訂戶權益]: http://go.microsoft.com/fwlink/?LinkId=623901
[申請免費試用]: http://go.microsoft.com/fwlink/?LinkId=623901

[試用 App Service]: http://go.microsoft.com/fwlink/?LinkId=523751

<!---HONumber=Oct15_HO4-->