<properties urlDisplayName="Get started with Azure" pageTitle="開始使用使用 Java 的 Microsoft Azure 網站 " metaKeywords="" description="本教學課程會示範如何將 Java 網站部署至 Microsoft Azure。" metaCanonical="" services="web-sites" documentationCenter="Java" title="Get started with Azure and Java" videoId="" scriptId="" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="09/25/2014" ms.author="robmcm" />

# 開始使用 Azure 網站和 Java

本教學課程說明如何使用 Azure 應用程式組件庫或 Azure 網站組態 UI，在使用 Java 的 Microsoft Azure 上建立網站。 

如果您不打算使用這些技術 (例如，如果您要自訂您的應用程式容器)，請參閱[將自訂 Java 網站上傳至 Azure](../web-sites-java-custom-upload)。

> [WACOM.NOTE] 若要完成此教學課程，您需要 Microsoft Azure 帳戶。若您沒有帳戶，您可以 <a href="/zh-tw/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F" target="_blank">啟用您的 MSDN 訂閱者權益</a> 或 <a href="/zh-tw/pricing/free-trial/?WT.mc_id=A261C142F" target="_blank">註冊免費試用</a>。 
> 
> 若要在註冊帳戶之前先開始使用 Azure 網站，請移至 <a href="https://trywebsites.azurewebsites.net/?language=java">https://trywebsites.azurewebsites.net</a>，您可以免費立即在上述網址中於「Azure 網站」中建立暫時性 ASP.NET 起始網站。不需要信用卡，不提供任何承諾。

# 使用 Azure 應用程式組件庫來建立 Java 網站

本資訊說明如何使用 Azure 應用程式組件庫，來為您的網站選取 Java 應用程式容器 (Apache Tomcat 或 Jetty)。

下列內容說明從應用程式組件庫，使用 Tomcat 建置網站的方式會顯示如下：

![Web site using Apache Tomcat](./media/web-sites-java-get-started/tomcat.png)

下列內容說明從應用程式組件庫，使用 Jetty 建置網站的方式會顯示如下：

![Web site using Jetty](./media/web-sites-java-get-started/jetty.png)

1. 登入 Microsoft Azure 管理入口網站。
2. 依序按一下 [新增]****、[運算]****、[網站]****，然後按一下 [從組件庫]****。
3. 從應用程式清單中，選取其中一個 Java 應用程式伺服器，例如 **Apache Tomcat** 或 **Jetty**。
4. 按一下 [下一步]****。
5. 指定 URL 名稱。
6. 選取區域。例如 [美國西部]****。
7. 按一下 [完成]****。

幾分鐘內即可建立您的網站。若要檢視網站，請在 Azure 管理入口網站內部的 [網站]**** 檢視中，等待狀態顯示為 [執行中]****，然後按一下網站的 URL。

請注意，您已建立使用應用程式容器的網站，如需將應用程式上傳至網站的詳細資訊，請參閱＜後續步驟＞****一節。

# 使用 Azure 組態 UI 來建立 Java 網站

本資訊說明如何使用 Azure 組態 UI，來為您的網站選取 Java 應用程式容器 (Apache Tomcat 或 Jetty)。

1. 登入 Microsoft Azure 管理入口網站。
2. 依序按一下 [新增]****、[運算]****、[網站]****，然後按一下 [快速建立]****。
3. 指定 URL 名稱。
4. 選取區域。例如 [美國西部]****。
5. 按一下 [完成]****。幾分鐘內即可建立您的網站。若要檢視網站，請在 Azure 管理入口網站內部的 [網站]**** 檢視中，等待狀態顯示為 [執行中]****，然後按一下網站的 URL。
6. 在 Azure 管理入口網站的 [網站]**** 檢視中，按一下您的網站名稱以開啟 
儀表板。
7. 按一下 [設定]****。
8. 在 [一般]**** 區段中，按一下可用版本以啟用 **Java**。
9. 如此即會顯示 Web 容器的選項，例如 Tomcat 和 Jetty。選取要使用的 Web 容器。 
10. 按一下 [儲存]****。 

幾分鐘內，您的網站即會變成 Java 型網站。若要確認是否為 Java 型網站，請在 Azure 管理入口網站的 [網站]**** 檢視中，等待狀態顯示為 [執行中]**** 之後，接著按一下網站的 URL。請注意，頁面將提供文字，指出新網站會是 Java 型網站。

請注意，您已建立使用應用程式容器的網站，如需將應用程式上傳至網站的詳細資訊，請參閱＜後續步驟＞****一節。

# 後續步驟

此時，在 Azure 上，您會擁有作為 Java 網站執行的 Java 應用程式伺服器。若要加入自己的應用程式或網頁，請參閱[將應用程式或網頁新增至 Java 網站](../web-sites-java-add-app)。

<!--HONumber=35.2-->
