<properties 
	pageTitle="將應用程式新增至 Java 網站" 
	description="此教學課程顯示如何將頁面或應用程式新增至 Microsoft Azure 上的 Java 網站。" 
	services="web-sites" 
	documentationCenter="java" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="09/25/2014" 
	ms.author="robmcm"/>

# 在 Azure 上將應用程式新增至 Java 網站

當您依照[開始使用 Microsoft Azure 網站和 Java](../web-sites-java-get-started) 所述初始化網站之後，就可以將 WAR 放在 **webapps** 資料夾中以便上傳您的應用程式。

**webapps** 資料夾的導覽路徑會根據網站的設定方式而有所不同。

- 如果您使用 Azure 應用程式組件庫來設定網站，則 **webapps** 資料夾的路徑會是 **d:\home\site\wwwroot\bin\application\_server\webapps** 的格式，其中 **application\_server** 會是您網站中生效的應用程式伺服器的名稱。 
- 如果您使用 Azure 組態 UI 來設定網站，則 **webapps** 資料夾的路徑會是 **d:\home\site\wwwroot\webapps** 的格式。 

請注意，您可以使用原始檔控制來上傳應用程式或網頁，這會納入連續整合案例。如需在網站中使用原始檔控制的指示，請參閱[從原始檔控制發佈至 Azure 網站](../web-sites-publish-source-control)。FTP 也是一個可上傳應用程或網頁的選項。

請注意 Tomcat 網站：將 WAR 檔案上傳至 **webapps** 資料夾之後，Tomcat 應用程式伺服器便會偵測到您已新增該檔案，並會將其自動載入。請注意，如果您將檔案 (WAR 檔案除外) 複製到根目錄，則在使用這些檔案之前，必須重新啟動應用程式伺服器。在 Azure 上執行的 Tomcat Java 網站，其自動載入功能會視新增的 WAR 檔案或新增至 **webapps** 資料夾的新檔案或目錄而定。 


<!--HONumber=42-->
