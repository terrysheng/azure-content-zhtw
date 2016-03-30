<properties 
	pageTitle="在 Eclipse 中建立 Azure Hello World Web 應用程式" 
	description="本教學課程將示範如何使用適用於 Eclipse 的 Azure 工具組來建立 Azure Hello World Web 應用程式。" 
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
	ms.topic="article" 
	ms.date="03/07/2016" 
	ms.author="robmcm"/>

# 在 Eclipse 中建立 Azure Hello World Web 應用程式

本教學課程示範如何使用[適用於 Eclipse 的 Azure 工具組]，建立基本的 Hello World 應用程式，並部署到 Azure 做為 Web 應用程式。下文所示的基本 JSP 範例乃力求簡潔，不過只要與 Azure 部署相關，幾乎類似的步驟皆適用於 Java servlet。

當您完成本教學課程，在網頁瀏覽器中檢視您的應用程式時，看起來會如下圖所示：

![][01]
 
## 必要條件

* Java Developer Kit (JDK) 1.7 版或更新版本。
* Eclipse IDE for Java EE Developers (Indigo 或更新版本)。這可透過 <http://www.eclipse.org/downloads/> 下載。
* Java 型 Web 伺服器或應用程式伺服器的散發套件，例如 Apache Tomcat 或 Jetty。
* Azure 訂用帳戶，可從 <https://azure.microsoft.com/zh-TW/free/> 或 <http://azure.microsoft.com/pricing/purchase-options/> 取得。
* 適用於 Eclipse 的 Azure 工具組。如需詳細資訊，請參閱[安裝 Azure Toolkit for Eclipse]。

## 建立 Hello World 應用程式

首先，我們將從建立 Java 專案開始。

1. 啟動 Eclipse，於功能表上依序按一下 [檔案]、[新增] 及 [動態 Web 專案] \(如果在按一下 [File] \(檔案)、[New] \(新增) 後沒有看到 [Dynamic Web Project] \(動態 Web 專案) 列為可用的專案，請執行下列動作：依序按一下 [File] \(檔案)、[New] \(新增)、[Project...] \(專案)，展開 [Web]，按一下 [Dynamic Web Project] \(動態 Web 專案)，然後按一下 [Next] \(下一步)。)
1. 基於本教學課程的目的，將專案命名為 **MyHelloWorld** 會出現以下類似畫面：
   ![][02]
1. 按一下 [完成]。
1. 在 Eclipse 的 [專案總管] 檢視中，展開 [MyHelloWorld]。在 **WebContent** 上按一下滑鼠右鍵、按一下 [新增]，然後按一下 [JSP File]。
1. 在 [新增 JSP 檔案] 對話方塊中，將檔案命名為 **index.jsp**。將父資料夾保留為 **MyHelloWorld/WebContent**。
1. 在 [Select JSP Template] \(選取 JSP 範本) 對話方塊中，基於本教學課程的目的，選取 [New JSP File (html)] \(新增 JSP 檔案 (html))，然後按一下 [Finish] \(完成)。
1. 當 index.jsp 檔案在 Eclipse 中開啟時，於現有的 `<body>` 元素中加入文字以動態顯示 **Hello World!**。您已更新的 `<body>` 內容看起來應該與下列範例類似：
   `<body><b><% out.println("Hello World!"); %></b></body>` 
1. 儲存 index.jsp。

## 將應用程式部署至 Azure Web 應用程式容器

您有數種方式可以將 Java Web 應用程式部署至 Azure。本教學課程說明其中一個最簡單的方式：將您的應用程式部署至 Azure Web 應用程式容器，無需特殊的專案類型或額外的工具。Azure 會為您提供 JDK 及 Web 容器軟體，因此您不需要自己上傳；只需要您的 Java Web 應用程式。如此一來，您的應用程式發行程序只需數秒，連一分鐘都不用。

1. 在 Eclipse 的 [Project Explorer] \(專案總管) 中，用滑鼠右鍵按一下 [MyHelloWorld]。

1. 在操作功能表中，選取 [Azure]，然後按一下 [Publish as Azure Web App...] \(發佈為 Azure Web 應用程式)
   ![][03]
1. 如果您尚未從 Eclipse 登入 Azure，系統會提示您登入 Azure 帳戶：
   ![][04]
   注意：如果您有多個 Azure 帳戶，登入程序期間的某些提示可能會顯示多次，即使它們看起來似乎相同。發生此情況時，請遵循登入指示繼續。
1. 在您成功登入 Azure 帳戶後，[Manage Subscriptions] \(管理訂用帳戶) 對話方塊將會顯示與您的認證相關聯的訂用帳戶清單。如果列出多個訂用帳戶，而您只想使用其中幾個帳戶，您可以選擇取消選取要使用的訂用帳戶。當您選取訂用帳戶之後，按一下 [Close] \(關閉)。
   ![][05]
1. 當 [Deploy to Azure Web App Container] \(部署至 Azure Web 應用程式容器) 對話方塊出現時，它會顯示您先前建立的所有 Web 應用程式容器；如果您尚未建立任何容器，清單將會是空白的。   
   ![][06]
1. 如果您之前尚未建立 Azure Web 應用程式容器，或您想要將應用程式發佈到新的容器中，請使用下列步驟。否則，請選取現有的 Web 應用程式容器，並跳至以下的步驟 7。

  1. 按一下 [New...] \(新增)

  1. [New Web App Container] \(新增 Web 應用程式容器) 對話方塊會隨即顯示：

        ![][07]

  1. 為您的 Web 應用程式容器輸入 **DNS Label** (DNS 標籤)；這會為您在 Azure 中的 Web 應用程式構成主機 URL 的分葉 DNS 標籤。注意：名稱必須可用，且符合 Azure Web 應用程式命名需求。

  1. 在 [Web Container] \(Web 容器) 下拉式功能表中，為您的應用程式選取適當的軟體。

        目前，您可以從 Tomcat 8、Tomcat 7 或 Jetty 9 選擇。所選軟體最新發行的版本由 Azure 提供，會在最新發行的 JDK 8 (由 Oracle 建立並由 Azure 提供) 中運作。

  1. 在 [Subscription] \(訂用帳戶) 下拉式功能表中，選取您要用於此部署的訂用帳戶。

  1. 在 [Resource Group] \(資源群組) 下拉式功能表中，選取您要與 Web 應用程式相關聯的資源群組。

        注意: Azure 資源群組可讓您將相關的資源分在同一組，方便一次刪除。

        您可以選取現有的資源群組 (如果有)，並略過下方步驟 g，或使用以下步驟建立新的資源群組：

      * 按一下 [New...] \(新增)

      * [New Resource Group] \(新增資源群組) 對話方塊會隨即顯示：

            ![][08]

      * 在 [Name] \(名稱) 文字方塊中，為新的資源群組指定名稱。

      * 在 [Region] \(區域) 下拉式功能表中，為資源群組選取適當的 Azure 資料中心位置。

      * 按一下 [確定]。

  1. [App Service Plan] \(App Service 方案) 下拉式功能表會列出與您選取之資源群組相關聯的應用程式服務方案。

        注意: App Service 方案會指定特定資訊，例如您 Web 應用程式的位置、定價層以及計算執行個體大小。單一 App Service 方案可用於多個 Web Apps，這也就是要與特定 Web 應用程式部署分開維護的原因。

        您可以選取現有的 App Service 方案 (如果有)，並略過下方步驟 h，或使用以下步驟建立新的 App Service 方案：

      * 按一下 [New...] \(新增)

      * [New App Service Plan] \(新增 App Service 方案) 對話方塊會隨即顯示：

            ![][09]

      * 在 [Name] \(名稱) 文字方塊中，為新的 App Service 方案指定名稱。

      * 在 [Location] \(位置) 下拉式功能表中，為該方案選取適當的 Azure 資料中心位置。

      * 在 [Pricing Tier] \(定價層) 下拉式功能表中，為方案選取適當的價格。若為測試用途，您可以選擇 [Free] \(免費)。

      * 在 [Instance Size] \(執行個體大小) 下拉式功能表中，為方案選取適當的執行個體大小。若為測試用途，您可以選擇 [Small] \(小型)。

  1. 一旦您完成所有上述步驟之後，[New Web App Container]\(新增 Web 應用程式容器) 對話方塊看起來應該如下圖所示：

        ![][10]

  1. 按一下 [OK] \(確定) 來完成建立新的 Web 應用程式容器。

        等待數秒鐘，讓 Web 應用程式容器清單重新整理；接著，您應該會在清單中看到新建立的 Web 應用程式容器已被選取。

1. 您現在已經準備好，可以完成將 Web 應用程式部署至 Azure 的初始部署：

    ![][11]

    按一下 [OK] \(確定) 將您的 Java 應用程式部署至選取的 Web 應用程式容器。

    注意：根據預設，您的應用程式將會部署為應用程式伺服器的子目錄。如果您想要部署為根應用程式，請選取 [Deploy to root] \(部署到根目錄) 核取方塊，然後再按一下 [OK] \(確定)。

1. 接下來，您應該會看到 [Azure Activity Log] 檢視，它會指出 Web 應用程式的部署狀態。

    ![][12]

    將您的 Web 應用程式部署至 Azure 的程序，應該只需幾秒鐘即可完成。當您的應用程式就緒時，您會在 [Status] \(狀態) 欄中看到名為 **Published** 的連結。當您按一下連結時，就會將您帶至已部署的 Web 應用程式首頁。

## 更新 Web 應用程式

更新現有執行中的 Azure Web 應用程式是一項快速又簡單的程序，而且您有兩個更新選項：

* 您可以更新現有 Java Web 應用程式的部署。
* 您可以將其他的 Java 應用程式發佈到相同的 Web 應用程式容器。

在任一種情況中，程序都是相同的，而且只需幾秒鐘：

1. 在 Eclipse 專案總管中，以右鍵按一下您要更新或新增到現有 Web 應用程式容器的 Java 應用程式。

2. 操作功能表顯示時，選取 [Azure] 然後選取 [Publish as Azure Web App...] \(發佈為 Azure Web 應用程式)

3. 由於您之前已經登入，因此會看到您現有 Web 應用程式容器的清單。選取您要對其發佈或重新發佈 Java 應用程式的 Web 應用程式容器，然後按一下 [OK] \(確定)。

幾秒鐘之後，[Azure Activity Log] \(Azure 活動記錄檔) 檢視將會將您已更新的部署顯示為 [Published] \(已發佈)，而您將可以在網頁瀏覽器中確認已更新的應用程式。

## 停止現有的 Web 應用程式

若要停止現有的 Azure Web 應用程式容器 (包括其中所有已部署的 Java 應用程式)，您可以使用 [Azure Explorer] \(Azure 總管) 檢視。

如果 [Azure Explorer] \(Azure 總管) 檢視尚未開啟，您可以依序按一下 Eclipse 中的 [Window] \(視窗) 功能表、[Show View] \(顯示檢視)、[Other...] \(其他)、[Azure]，然後按一下 [Azure Explorer] \(Azure 總管) 來開啟。如果您之前尚未登入，系統將會提示您登入。

顯示 [Azure Explorer] \(Azure 總管) 之後，使用下列這些步驟來停止您的 Web 應用程式：

1. 展開 [Azure] 節點。
2. 展開 [Web Apps] \(Web 應用程式) 節點。 
3. 以滑鼠右鍵按一下所需的 Web 應用程式。
5. 操作功能表出現時，按一下 [Stop] \(停止)。
    ![][13]

## 後續步驟

如需詳細資訊，請參閱下列連結：

* [Java 開發人員中心](/develop/java/)。
* [Web Apps 概觀](app-service-web-overview.md)

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[Azure App Service]: http://go.microsoft.com/fwlink/?LinkId=529714
[適用於 Eclipse 的 Azure 工具組]: http://go.microsoft.com/fwlink/?LinkID=699529
[安裝 Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546

<!-- IMG List -->

[01]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/01-Web-Page.png
[02]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/02-Dynamic-Web-Project.png
[03]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/03-Context-Menu.png
[04]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/04-Log-In-Dialog.png
[05]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/05-Manage-Subscriptions-Dialog.png
[06]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/06-Deploy-To-Azure-Web-Container.png
[07]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/07-New-Web-App-Container-Dialog.png
[08]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/08-New-Resource-Group-Dialog.png
[09]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/09-New-Service-Plan-Dialog.png
[10]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/10-Completed-Web-App-Container-Dialog.png
[11]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/11-Completed-Deploy-Dialog.png
[12]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/12-Activity-Log-View.png
[13]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/13-Azure-Explorer-Web-App.png

<!---HONumber=AcomDC_0309_2016-->



