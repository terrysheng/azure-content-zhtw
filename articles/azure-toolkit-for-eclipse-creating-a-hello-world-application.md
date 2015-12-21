<properties
    pageTitle="在 Eclipse 中建立適用於 Azure 的 Hello World 應用程式"
    description="了解如何使用適用於 Eclipse 的 Azure 工具組建立簡單的 Hello World 應用程式。"
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="11/19/2015" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690944.aspx -->

# 在 Eclipse 中建立適用於 Azure 的 Hello World 應用程式 #

下列步驟示範如何使用適用於 Eclipse 的 Azure 工具組，在 Azure 中建立及部署基本 JSP 應用程式。下文所示的 JSP 範例乃力求簡潔，不過只要與 Azure 部署相關，幾乎類似的步驟皆適用於 Java servlet。

應用程式會顯示如下：

![][ic600360]

## 必要條件 ##

* Java Developer Kit (JDK) 1.7 版或更新版本。
* Eclipse IDE for Java EE Developers (Indigo 或更新版本)。這可透過 <http://www.eclipse.org/downloads/> 下載。
* Java 型 Web 伺服器或應用程式伺服器的散發套件，例如 Apache Tomcat、GlassFish、JBoss Application Server、Jetty 或 IBM® WebSphere® Application Server Liberty Core。
* Azure 訂用帳戶，可從 <http://azure.microsoft.com/pricing/purchase-options/> 取得。
* 適用於 Eclipse 的 Azure 工具組。如需詳細資訊，請參閱[安裝適用於 Eclipse 的 Azure 工具組][]。

## 建立 Hello World 應用程式 ##

首先，我們將從建立 Java 專案開始。

*  啟動 Eclipse，於功能表上依序按一下 [檔案]、[新增] 及 [動態 Web 專案] 如果在依序按一下 [檔案] 和 [新增] 後沒有看到 [動態 Web 專案] 列為可用的專案，請執行下列動作：依序按一下 [檔案]、[新增] 及 [專案...]，展開 [Web]，再依序按一下 [動態 Web 專案] 及 [下一步]。
*  基於本教學課程的目的，將專案命名為 **MyHelloWorld** (請確定您使用這個名稱，本教學課程的後續步驟預期 WAR 檔案命名為 MyHelloWorld)。會出現以下類似畫面：![][ic589576]
* 按一下 [完成]。
* 在 Eclipse 的 [專案總管] 檢視中，展開 [MyHelloWorld]。在 **WebContent** 上按一下滑鼠右鍵、按一下 [新增]，然後按一下 [JSP File]。
* 在 [New JSP File] 對話方塊中，將檔案命名為 **index.jsp**。保留父資料夾為 **MyHelloWorld/WebContent**，如下列所示：![][ic659262]
* 在 [選取 JSP 範本] 對話方塊中，基於此教學課程的目的，請選取 [新增 JSP 檔案 (html)] 並按一下 [完成]。
* 當 index.jsp 檔案在 Eclipse 中開啟時，於現有的 `<body>` 元素中加入文字以動態顯示 **Hello World!**。更新的 `<body>` 內容應該如下所示：```
    <body>
    <b><% out.println("Hello World!"); %></b>
    </body>
```
* 儲存 index.jsp。

## 將應用程式部署至 Azure 之最快速及最簡單的方法 ##

只要備妥要測試的 Java Web 應用程式，就可以使用下列捷徑直接在 Azure 雲端上進行試用。

1. 在 Eclipse 的 [專案總管] 中，按一下 [MyHelloWorld]。
1. 在 Eclipse 的工具列中，按一下 [發佈至 Azure 雲端] 按鈕 ![][ic710882]。
1. 如果您是第一次將此應用程式發佈至 Azure，而且未曾建立此應用程式的 Azure 部署專案，Azure 部署專案會自動建立。您應該會看到下列提示，它也會列出為了執行應用程式而會自動部署的 JDK 封裝和應用程式伺服器。![][ic789598] 這個快捷方法可讓您在 Azure 中快速且輕鬆測試應用程式，而不需要設定與預設值不同的特定伺服器或 JDK。如果您滿意預設值，可以按一下 [確定] 繼續進行下列步驟。不過，如果您想要變更用於應用程式的 JDK 或應用程式伺服器，您稍後可以這麼做，方法是編輯自動建立的 Azure 部署專案，或者立即按一下 [取消] 並閱讀本教學課程的＜**關於 Azure 部署專案**＞一節。
1. 在 [發佈至 Azure] 對話方塊中：
    1. 如果 [訂用帳戶] 清單中還沒有可供選取的訂用帳戶，請遵循下列步驟來匯入您的訂用帳戶資訊：
        1. 按一下 [從 PUBLISH-SETTINGS 檔案匯入]。
        1. 在 [匯入訂用帳戶資訊] 對話方塊中，按一下 [下載 PUBLISH-SETTINGS 檔案]。如果您尚未登入 Azure 帳戶，系統會提示您登入。系統接著會提示您儲存 Azure 發佈設定檔。請將其儲存至本機電腦。
        1. 同樣在 [匯入訂用帳戶資訊] 對話方塊中，按一下 [瀏覽] 按鈕，選取您在上一個步驟中儲存的本機發佈設定檔，然後按一下 [開啟]。您的畫面看起來應該像下面這樣：![][ic644267]
        1. 按一下 [確定]。
    1. 針對 [訂用帳戶]，選取您要使用於部署的訂用帳戶。
    1. 針對 [儲存體帳戶]，選取您想要使用的儲存體帳戶，或按一下 [新增] 來建立新的儲存體帳戶。
    1. 針對 [服務名稱]，選取您想要使用的雲端服務，或按一下 [新增] 來建立新的雲端服務。
    1. 針對 [目標作業系統]，選取您想要用於部署的作業系統版本。
    1. 針對 [目標環境]，基於本教學課程的目的，請選取 [預備] (當您準備好部署至生產網站時，您會將此選項變更為 [生產])。
    1. 選用：若要讓新部署自動覆寫先前的部署，請確實核取 [覆寫先前部署]。當您啟用此選項時，發佈至相同的位置時不會出現「409 衝突」問題。請注意，[發佈至 Azure] 對話方塊包含 [遠端存取] 的區段。根據預設，不會啟用遠端存取，而且我們不會針對此範例啟用它。若要啟用遠端存取，您需要在遠端登入時輸入要使用的使用者名稱和密碼。如需有關遠端存取的詳細資訊，請參閱[在 Eclipse 中啟用 Azure 部署的遠端存取][]。將會出現類似下面的 [發佈至 Azure] 對話方塊：![][ic719488]
1. 按一下 [發佈] 發佈至預備環境。當系統提示您執行完整建置時，請按一下 [是]。第一次建置時可能需要幾分鐘的時間。[Azure 活動記錄檔] 會顯示在 Eclipse 索引標籤式的檢視區段。![][ic719489] 您可以使用這個記錄檔，以及 [主控台] 檢視，以查看部署進度。替代方法是登入 [Azure 管理入口網站][]，並使用 [雲端服務] 區段以監視狀態。
1. 成功部署後，[Azure 活動記錄檔] 將顯示 [已發佈] 狀態。按一下 [已發佈] (如下圖所示)，瀏覽器隨即會開啟部署的執行個體。![][ic719490]

因為這是部署到預備環境，DNS 名稱將會採用 http://&lt;*guid*&gt;.cloudapp.net 格式，且 URL 將包含 DNS 名稱再加上您應用程式的尾碼。例如，http://447564652c20426f6220526f636b7321.cloudapp.net/MyHelloWorld。(**MyHelloWorld** 部分會區分大小寫)。 如果您按一下 Azure 平台管理入口網站 (在管理入口網站的 [雲端服務] 部分中) 中的部署名稱，您也會看到 DNS 名稱。

雖然本逐步解說適用於部署至預備環境，部署至生產環境仍適用相同的步驟，但在 [發佈至 Azure] 對話方塊中，必須為 [目標環境] 選取 [生產] 而非 [預備]。生產環境的部署會根據您選擇的 DNS 名稱 (而非預備部署所使用的 GUID) 產生 URL。

>[AZURE.WARNING]您現已將 Azure 應用程式部署至雲端了。但在繼續之前請注意，應用程式部署後即使未執行，也會繼續累算訂用帳戶的計費時間。因此，請務必從 Azure 訂用帳戶刪除任何不需要的部署。

## 關於 Azure 部署專案 ##

為了將一個或多個 Java 應用程式部署至 Azure，您需要 Azure 部署專案。它扮演「封裝」角色，應用程式需要包裝成封裝才能在 Azure 上發佈。

除了您的應用程式的相關資訊，Azure 部署專案也包含部署的其他重要元件資訊，最重要的是：用來執行 Web 應用程式和 Java 執行階段的應用程式伺服器容器。Azure 支援大範圍的 Java 執行階段和 Java 應用程式伺服器，讓您可以從中選擇。

雖然這裡使用的範例已針對教育目的大幅簡化，但 Azure 部署專案也可以包含其他重要組態資訊，讓您能夠使用應用程式建立幾乎各種複雜、可調整、高可用性、多層的雲端服務。您可以啟用 [工作階段親和性 (「黏性工作階段」)]、[快速快取]、[遠端偵錯]、[SSL 卸載]、[防火牆/連接埠路由]、[遠端存取] 和一些其他強大功能。

如果您已經完成本教學課程的上一節 (＜將應用程式部署至 Azure 之最快速及最簡單的方法＞)，您將會在 [專案總管] 中看到為您自動產生的新 Azure 部署專案 (名為 **MyHelloWorld\_onAzure**)。

您也可以先自行建立空白的 Azure 部署專案，然後將應用程式新增至該專案來開始本教學課程。這個程序較為冗長，但能讓您從頭開始更充分掌控初始組態。

若要從頭開始建立新的 Azure 部署專案，請按一下 [新增 Azure 部署專案] 按鈕 ![][ic710876]。

無論您是使用現有的 Azure 部署專案，或是從頭建立的專案，您都可以同樣輕鬆地隨時變更其組態設定和元件，例如 JDK 或應用程式伺服器。

若要變更現有 Azure 部署專案中的 JDK、應用程式伺服器或應用程式清單：

1. 在 [專案總管] 中展開專案節點 (例如 **MyHelloWorld\_onAzure**)
2. 以滑鼠右鍵按一下 [WorkerRole1]
3. 展開內容功能表中的 [Azure] 子功能表
4. 按一下 [伺服器組態]

不論您啟動這些伺服器組態步驟的方式為何 (如上所示編輯現有的 Azure 部署專案，還是從頭開始建立新專案)，您都會看到相同類型的對話方塊，可讓您設定您的 JDK、伺服器和應用程式元件。若要深入了解如何在這些對話方塊中變更設定，例如若要變更 JDK、應用程式伺服器和加入或移除部署中的應用程式，請參閱[伺服器組態屬性][]文章。

## 僅限 Windows：將應用程式部署至計算模擬器 ##

>[AZURE.NOTE]只能在 Windows 上使用 Azure 模擬器。如果您使用 Windows 以外的作業系統，請略過本節。

如果您已依照稍早的步驟建立新的 Azure 部署專案 (也就是以隱含方式將應用程式發佈至 Azure)，則已為雲端設定 JDK 和應用程式伺服器，但不會針對本機模擬設定。若要準備用於本機模擬器的測試專案，請遵循下列步驟：

1. 在 Eclipse 的 [專案總管] 中，按一下 [MyHelloWorld\_onAzure]。
1. 以滑鼠右鍵按一下 [WorkerRole1]。
1. 展開內容功能表中的 [Azure] 子功能表。
1. 按一下 [伺服器組態]。
1. 在 [JDK] 索引標籤上，檢查此工具組是否已為您預先設定預設本機 JDK。如果沒有，或您想要變更預設的預設值，請務必勾選 [使用此檔案路徑中的 JDK 以進行本機測試] 核取方塊，並指定您想要使用的 JDK 安裝位置。如果您想要變更它，請按一下 [瀏覽] 按鈕，然後使用瀏覽控制項，選取要使用的 JDK 目錄位置。
1. 按一下 [伺服器] 索引標籤。
1. 在對話方塊底部的 [本機伺服器路徑] 文字方塊中，輸入本機安裝伺服器的路徑 (須符合在對話方塊頂端的 [部署此類型的伺服器] 核取方塊下所選取之伺服器的類型和主要版本號碼)。如果您想要使用不同類型或主要版本的應用程式伺服器，請先變更該核取方塊底下的選取項目。
1. 按一下 [確定]。
1. 在 Eclipse 工具列中，按一下 [在 Azure 模擬器中執行] 按鈕 ![][ic710879]。如果 [在 Azure 模擬器中執行] 按鈕未啟用，請確定在 Eclipse 的 [專案總管] 中選取 [MyHelloWorld\_onAzure]，並確定 Eclipse 的 [專案總管] 是目前的視窗。這會第一次開始完整建置您的專案，然後在計算模擬器中啟動您的 Java Web 應用程式 (請注意，根據您的電腦效能特性，第一次建置可能需要幾秒鐘到幾分鐘，但後續建置會更快)。 完成第一次建置步驟之後，Windows 使用者帳戶控制 (UAC) 會提示您允許此命令對電腦進行變更。按一下 [是]。

>[AZURE.IMPORTANT]如果看不到 UAC 提示，請檢查 Windows 工作列的 UAC 圖示，然後先按一下它。有時候 UAC 提示不會顯示在最上層視窗，而只會顯示為工作列圖示。

1. 檢查計算模擬器 UI 的輸出，以判斷專案是否有任何問題。根據您的部署內容，可能需要幾分鐘，您的應用程式才能完全在計算模擬器中啟動。
1. 啟動您的瀏覽器，並將 URL `http://localhost:8080/MyHelloWorld` 做為位址 (URL 的 `MyHelloWorld` 部分會區分大小寫)。您應該會看到類似下圖的 MyHelloWorld 應用程式 (index.jsp 的輸出)：![][ic589579]

當您準備好停止計算模擬器中正在執行的應用程式時，請在 Eclipse 工具列中按一下 [重設 Azure 模擬器] 按鈕 ![][ic710880]。

## 刪除您的部署 ##

若要在適用於 Eclipse 的 Azure 工具組中刪除部署，請確定在 Eclipse 的 [專案總管] 中選取 [MyHelloWorld\_onAzure]，確定 Eclipse 的 [專案總管] 是目前的視窗，然後按一下 Eclipse 工具列中的 [取消發佈] 按鈕 ![][ic710883] (您可以在 Eclipse 的 [專案總管] 中以滑鼠右鍵按一下 [MyHelloWorld\_onAzure]，再依序按一下 [Azure] 及 [從 Azure 雲端解除部署] 以執行相同的作業)。 這會顯示 [取消發佈 Azure 專案] 對話方塊。

![][ic719491]

選取包含部署的訂用帳戶和雲端服務，選取想要刪除的部署，然後按一下 [取消發佈]

(另有一個使用工具組來刪除部署的替代方案，那就是使用 Azure 管理入口網站的 [雲端服務] 區段：瀏覽至您的部署，加以選取，然後按一下 [刪除] 按鈕。這樣就會停止並刪除部署。如果您只是想停止部署而不要加以刪除，請按一下 [停止] 按鈕而不要按 [刪除] 按鈕 (但如前所述，若未刪除部署，即便部署已停止也會繼續累算費用)。

## 另請參閱 ##

[適用於 Eclipse 的 Azure 工具組][]

[安裝適用於 Eclipse 的 Azure 工具組][]

[適用於 Eclipse 的 Azure 工具組的新功能][]

如需如何搭配使用 Azure 與 Java 的詳細資訊，請參閱 [Azure Java 開發人員中心][]。

<!-- URL List -->

[Azure Java 開發人員中心]: http://go.microsoft.com/fwlink/?LinkID=699547
[Azure 管理入口網站]: http://go.microsoft.com/fwlink/?LinkID=512959
[Azure Role Properties]: http://go.microsoft.com/fwlink/?LinkID=699525
[適用於 Eclipse 的 Azure 工具組]: http://go.microsoft.com/fwlink/?LinkID=699529
[在 Eclipse 中啟用 Azure 部署的遠端存取]: http://go.microsoft.com/fwlink/?LinkID=699538
[安裝適用於 Eclipse 的 Azure 工具組]: http://go.microsoft.com/fwlink/?LinkId=699546
[伺服器組態屬性]: http://go.microsoft.com/fwlink/?LinkID=699525#server_configuration_properties
[適用於 Eclipse 的 Azure 工具組的新功能]: http://go.microsoft.com/fwlink/?LinkID=699552

<!-- IMG List -->

[ic589576]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic589576.png
[ic589579]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic589579.png
[ic600360]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic600360.png
[ic644267]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic644267.png
[ic659262]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic659262.png
[ic710876]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710876.png
[ic710879]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710879.png
[ic710880]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710880.png
[ic710882]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710882.png
[ic710883]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710883.png
[ic719488]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic719488.png
[ic719489]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic719489.png
[ic719490]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic719490.png
[ic719491]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic719491.png
[ic789598]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic789598.png

<!-------HONumber=AcomDC_1210_2015--->