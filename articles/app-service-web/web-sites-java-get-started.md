<properties
	pageTitle="在 Azure App Service 中建立 Java Web 應用程式 | Microsoft Azure"
	description="本教學課程示範如何將 Java Web 應用程式部署至 Azure App Service。"
	services="app-service\web"
	documentationCenter="java"
	authors="rmcmurray"
	manager="wpickett"
	editor=""/>
<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="Java"
	ms.topic="hero-article"
	ms.date="03/04/2016"
	ms.author="robmcm"/>

# 在 Azure App Service 中建立 Java Web 應用程式

> [AZURE.SELECTOR]
- [.Net](web-sites-dotnet-get-started.md)
- [Node.js](web-sites-nodejs-develop-deploy-mac.md)
- [Java](web-sites-java-get-started.md)
- [PHP - Git](web-sites-php-mysql-deploy-use-git.md)
- [PHP - FTP](web-sites-php-mysql-deploy-use-ftp.md)
- [Python](web-sites-python-ptvs-django-mysql.md)

本教學課程說明如何利用 [Azure 入口網站](https://portal.azure.com/)[在 Azure App Service 中建立 Web 應用程式](http://go.microsoft.com/fwlink/?LinkId=529714)。Azure 入口網站是可用來管理 Azure 資源的 Web 介面。

> [AZURE.NOTE] 若要完成此教學課程，您需要 Microsoft Azure 帳戶。如果您沒有這類帳戶，可以[啟用自己的 Visual Studio 訂閱者權益]，或是[申請免費試用]。
>
> 如果您想要在註冊 Azure 帳戶之前先開始使用 Azure App Service，請移至[試用 App Service]。您可以於該處，在 App Service 中立即建立短期的入門 Web app - 不需信用卡，不需任何承諾。

## Java 應用程式選項

有數種方式可讓您在 App Service Web 應用程式中設定 Java 應用程式。

1. 建立應用程式，然後設定 [應用程式設定]。

	App Service 提供數個 Tomcat 或 Jetty 版本 (包含預設組態)。如果您將託管的應用程式會使用其中一個內建版本，這就是設定 Web 容器的最簡單方法，並且適合在您只想要將 WAR 檔案上傳到 Web 容器時使用。而這個方法就是您在 Azure 入口網站中建立應用程式，然後前往應用程式的 [應用程式設定] 刀鋒視窗來選擇您的 Java 版本及所需的 Java Web 容器。當您使用這個方法時，Java 和 Web 容器都會從程式檔案執行。其他方法則會將 Web 容器 (或許還有 JVM) 放在磁碟空間。當您使用此模型時，您沒有在這部分的檔案系統中編輯檔案的權限，這表示您不能進行像是設定 *server.xml* 檔案或將程式庫檔案放在 */lib* 資料夾中的作業。如需詳細資訊，請參閱本教學課程後面的[建立及設定 Java Web 應用程式](#appsettings)一節。
	
2. 使用來自 Azure Marketplace 的範本。

	Azure Marketplace 包含數個範本，其可使用 Tomcat 或 Jetty Web 容器自動建立及設定 Java Web 應用程式。設定範本的 Web 容器是可設定的。如需詳細資訊，請參閱本教學課程的[使用來自 Azure Marketplace 的 Java 範本](#marketplace)區段。
 
  
3. 建立應用程式，然後手動複製並編輯組態檔

	您可能想要裝載不會部署在 App Service 所提供的任何 Web 容器中的自訂 Java 應用程式。例如，以下是這麼做的一些原因：
	
	* Java 應用程式需要 App Service 未直接支援或資源庫不提供的 Tomcat 或 Jetty 版本。
	* Java 應用程式會接受 HTTP 要求，但不會以 WAR 形式部署到預先存在的 Web 容器中。
	* 您想要自行從頭設定 Web 容器。 
	* 您想要使用 App Service 所不支援的 Java 版本並自行將它上傳。

	對於這種案例來說，您可以使用 Azure 入口網站建立應用程式，然後以手動提供適當的執行階段檔案。在此情況下，會針對您的 App Service 計畫的儲存空間配額計算檔案。如需詳細資訊，請參閱[將自訂 Java Web 應用程式上傳至 Azure](web-sites-java-custom-upload.md)。

## <a name="portal"></a>建立及設定 Java Web 應用程式

本節說明如何建立 Web 應用程式，並使用入口網站的 [應用程式設定] 刀鋒視窗針對 Java 進行設定。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

2. 依序按一下 [新增] > [Web + 行動] > [Web 應用程式]。

	![](./media/web-sites-java-get-started/newwebapp.png)

4. 在 [Web 應用程式] 方塊中，輸入 Web 應用程式的名稱。

	此名稱在 azurewebsites.net 網域中必須是唯一的，因為 Web 應用程式的 URL 將是 {name}.azurewebsites.net。如果您輸入的名稱不是唯一的，紅色驚嘆號會出現在文字方塊中。

5. 選取 [資源群組]，或建立新的資源群組。

	如需資源群組的詳細資訊，請參閱[使用 Azure 入口網站管理您的 Azure 資源](../resource-group-portal.md)。

6. 選取 [App Service 方案/位置]，或建立新的 App Service 方案/位置。

	如需 App Service 方案的詳細資訊，請參閱 [Azure App Service 方案概觀](../azure-web-sites-web-hosting-plans-in-depth-overview.md)

7. 按一下 [建立]。

	![](./media/web-sites-java-get-started/newwebapp2.png)
 
8. 建立 Web 應用程式後，依序按一下 [Web Apps] > [{您的 Web 應用程式}]。
 
	![](./media/web-sites-java-get-started/selectwebapp.png)

9. 在 [Web 應用程式] 刀鋒視窗中，按一下 [設定]。

10. 按一下 [應用程式設定]。

11. 選擇所需的 [Java 版本]。

12. 選擇所需的 [Java 次要版本]。如果您選取 [最新]，您的應用程式會使用 App Service 可用的最新次要版本作為該 Java 主要版本。[最新] 項目是從 [應用程式設定] 建立之 Java 應用程式所獨有。如果您是從資源庫建立 Java 應用程式，則必須管理您自己的容器和 JVM 變更。

12. 選擇所需的 [Web 容器]。如果您選取的容器名稱開頭為 [最新]，您的應用程式將會保留在 App Service 中該 Web 容器主要版本可用的最新版本。

	![](./media/web-sites-java-get-started/versions.png)

13. 按一下 [儲存]。

	幾分鐘之後，您的 Web 應用程式就會變成 Java 型的 Web 應用程式，並已設定成使用您選取的 Web 容器。

14. 依序按一下 [Web 應用程式] > [{您的新 Web 應用程式}]。

15. 按一下 [URL] 來瀏覽至新網站。

	網頁會確認您已建立 Java 型 Web 應用程式。


## <a name="marketplace"></a>使用來自 Azure Marketplace 的 Java 範本

本節說明如何使用 Azure Marketplace 來建立 Java Web 應用程式。相同的一般流程也可以用來建立 Java 型行動或 API 應用程式。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

2. 依序按一下 [新增] > [Marketplace]。

	![](./media/web-sites-java-get-started/newmarketplace.png)

3. 按一下 [Web + 行動]。

	您可能必須向左捲動才能看到 [Marketplace] 刀鋒視窗，讓您能選取 [Web + 行動]。

4. 在搜尋文字方塊中輸入 Java 應用程式伺服器的名稱，例如 **Apache Tomcat** 或 **Jetty**，然後按下 Enter 鍵。

5. 在搜尋結果中，按一下 Java 應用程式伺服器。

	![](./media/web-sites-java-get-started/webmobilejetty.png)

6. 在第一個 [Apache Tomcat] 或 [Jetty] 刀鋒視窗中，按一下 [建立]。

	![](./media/web-sites-java-get-started/jettyblade.png)

7. 在下一個 [Apache Tomcat] 或 [Jetty] 刀鋒視窗中，於 [Web 應用程式] 方塊中輸入 Web 應用程式的名稱。

	此名稱在 azurewebsites.net 網域中必須是唯一的，因為 Web 應用程式的 URL 將是 {name}.azurewebsites.net。如果您輸入的名稱不是唯一的，紅色驚嘆號會出現在文字方塊中。

8. 選取 [資源群組]，或建立新的資源群組。

	如需資源群組的詳細資訊，請參閱[使用 Azure 入口網站管理您的 Azure 資源](../resource-group-portal.md)。

9. 選取 [App Service 方案/位置]，或建立新的 App Service 方案/位置。

	如需 App Service 方案的詳細資訊，請參閱 [Azure App Service 方案概觀](../azure-web-sites-web-hosting-plans-in-depth-overview.md)

10. 按一下 [建立]。

	![](./media/web-sites-java-get-started/jettyportalcreate2.png)

	Azure 很快 (通常不到一分鐘) 就會完成建立新的 Web 應用程式。

11. 依序按一下 [Web 應用程式] > [{您的新 Web 應用程式}]。

12. 按一下 [URL] 來瀏覽至新網站。

	![](./media/web-sites-java-get-started/jettyurl.png)

	Tomcat 會隨附一組預設頁面，所以如果選擇 Tomcat，您會看到類似下列範例的頁面。

	![使用 Apache Tomcat 的 Web 應用程式](./media/web-sites-java-get-started/tomcat.png)

	如果選擇 Jetty，您會看到類似下列範例的頁面。Jetty 沒有一組預設頁面，所以用於空白 Java 網站的相同 JSP 會在此重複使用。

	![使用 Jetty 的 Web 應用程式](./media/web-sites-java-get-started/jetty.png)

現在，您已建立含有應用程式容器的 Web 應用程式，如需了解如何將應用程式上傳至該 Web 應用程式，請參閱＜[後續步驟](#next-steps)＞一節。


## 後續步驟

此時，在 Azure App Service 中，您會擁有在 Web 應用程式中執行的 Java 應用程式伺服器。若要將您自己的程式碼部署至 Web 應用程式，請參閱[將應用程式或網頁新增至 Java Web 應用程式](web-sites-java-add-app.md)。

如需在 Azure 中開發 Java 應用程式的詳細資訊，請參閱 [Java 開發人員中心](/develop/java/)。

<!-- External Links -->
[啟用自己的 Visual Studio 訂閱者權益]: http://go.microsoft.com/fwlink/?LinkId=623901
[申請免費試用]: http://go.microsoft.com/fwlink/?LinkId=623901
[試用 App Service]: http://go.microsoft.com/fwlink/?LinkId=523751

<!---HONumber=AcomDC_0309_2016-->