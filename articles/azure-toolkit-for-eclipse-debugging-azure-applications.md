<properties
    pageTitle="在 Eclipse 中偵錯 Azure 應用程式"
    description="使用 Eclipse 的 Azure 工具組深入了解偵錯 Azure 應用程式。"
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
    ms.date="11/30/2015" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690949.aspx -->

# 在 Eclipse 中偵錯 Azure 應用程式 #

使用 Azure Toolkit for Eclipse，無論您的應用程式是在 Azure 或計算模擬器 (如果您使用 Windows 做為作業系統) 中執行，您都可以進行偵錯。下列影像顯示用來啟用遠端偵錯的 [偵錯] 內容對話方塊：

![][ic719504]

本教學課程假設您已成功建立應用程式，而且熟悉計算模擬器和部署到 Azure 的操作。

我們將使用[在 JSP 中使用 Azure 服務執行階段程式庫][]教學課程中的應用程式做為本主題的起始點。如果您尚未建立該應用程式，請先建立應用程式後再繼續。

## 在應用程式於 Azure 中執行時偵錯 ##

>[AZURE.WARNING]此工具組目前支援遠端 Java 偵錯，主要是針對在 Azure 計算模擬器中執行的部署所提供。由於偵錯連線不安全，您不應在生產環境部署中啟用遠端偵錯。如果您需要偵錯在 Azure 雲端中執行的應用程式，請使用預備環境部署，但您必須了解如果有人知道您的雲端部署的 IP 位址，可能會在未經授權的情況下存取您的偵錯工作階段，即使是預備環境部署也一樣。

1. 在模擬器中建置要測試的專案：在 Eclipse 的專案總管中，以滑鼠右鍵按一下 [MyAzureProject]，依序按一下 [內容]、[Azure]，將 [建置目的]設為 [部署至雲端]。
1. 重建您的專案：從 Eclipse 功能表按一下 [專案]，然後按一下 [全部建置]。
1. 將應用程式部署至 Azure 中的 [預備]。
    >[AZURE.IMPORTANT]如前所述，在大部分情況下，強烈建議您在計算模擬器中偵錯，只有需要額外的偵錯時，才在預備環境中偵錯。建議您不要在生產環境中偵錯。
1. 當您在 Azure 的部署準備就緒後，請從 [Azure 管理入口網站][]取得部署的 DNS 名稱。預備環境部署的 DNS 名稱的格式如下：http://*&lt;guid&gt;*.cloudapp.net，其中 *&lt;guid&gt;* 是 Azure 指派的 GUID 值。
1. 在 Eclipse 的專案總管中，於 [WorkerRole1] 上按一下滑鼠右鍵，按一下 [Azure]，然後按一下 [偵錯]。
1. 在 [WorkerRole1 偵錯內容] 對話方塊中：
    1. 核取 [啟用這個角色的遠端偵錯]。
    1. 針對 [要使用的輸入端點]，使用 [偵錯 (public:8090, private:8090)]。
    1. 確定未核取 [將 JVM 啟動在暫停模式，等候偵錯工具連線]。
        >[AZURE.IMPORTANT] [將 JVM 啟動在暫停模式，等候偵錯工具連線] 選項僅適用於計算模擬器中的進階偵錯案例 (不適用於雲端部署)。如果使用 [將 JVM 啟動在暫停模式，等候偵錯工具連線] 選項，它會暫停伺服器的啟動程序，直到 Eclipse 偵錯工具連線到其 JVM 為止。雖然您可以在使用計算模擬器的偵錯工作階段使用此選項，但請勿將它使用在雲端部署的偵錯工作階段。伺服器的初始化是在 Azure 啟動工作中進行，而 Azure 雲端在啟動工作完成之前不會提供公用端點。因此，如果在雲端部署中啟用此選項，啟動程序將無法順利完成，因為它無法接收來自外部 Eclipse 用戶端的連線。
    1. 按一下 [建立偵錯組態]。
1. 在 [Azure 偵錯組態] 對話方塊中：
    1. 針對 [要偵錯的 Java 專案]，選取 [MyHelloWorld] 專案。
    1. 針對 [設定偵錯的對象]，核取 [Azure 雲端 (預備)]。
    1. 確定未核取 [Azure 計算模擬器]。
    1. 針對 [主機]，輸入預備部署的 DNS 名稱，但不含前面的 * ***http://**。例如 (使用您的 GUID 取代以下顯示的 GUID)：**4e616d65-6f6e-6d65-6973-526f62657274.cloudapp.net**
1. 按一下 [確定] 關閉 [Azure 偵錯組態] 對話方塊。
1. 按一下 [確定] 關閉 [WorkerRole1 偵錯內容] 對話方塊。
1. 如果您尚未在 index.jsp 中設定中斷點，請設定一個：
    1. 在 Eclipse 的專案總管中，依序展開 [MyHelloWorld]、[WebContent]，然後按兩下 [index.jsp]。
    1. 在 index.jsp 中，以滑鼠右鍵按一下 Java 程式碼左側的藍色列，按一下 [切換中斷點]，如下所示：![][ic551537]
1. 在 Eclipse 的功能表，按一下 [執行]，然後按一下 [偵錯組態]。
1. 在 [偵錯組態] 對話方塊中，展開左窗格中的 [遠端 Java 應用程式]，選取 [Azure 雲端 (WorkerRole1)]，然後按一下 [偵錯]。
1. 在瀏覽器中，執行預備應用程式 ****http://***&lt;guid&gt;***.cloudapp.net/MyHelloWorld**，以您的 DNS 名稱中的 GUID 取代 *&lt;guid&gt;*。如果 [確認檢視參數] 對話方塊提示，按一下 [是]。您的偵錯工作階段現在應該執行到設定中斷點的程式碼。

>[AZURE.NOTE]如果您嘗試和執行多個角色執行個體的部署開始一個遠端偵錯連線，您目前尚無法控制偵錯工具會先連線至哪一個執行個體，因為 Azure 負載平衡器會隨機選取執行個體。不過，一旦您與該執行個體連線後，您就會繼續偵錯相同的執行個體。另外請注意，如果閒置時間超過 4 分鐘 (例如，當您停在中斷點的時間太長時)，Azure 可能會關閉連線。

## 在多個執行個體的部署中偵錯特定的角色執行個體 ##

當您的部署在雲端中執行時，您很有可能在多個計算或角色執行個體中執行它。這可讓您充分利用 Azure 99.95% 的可用性保證，並擴充您的應用程式。

在這種情況下，您可能需要在特定的角色執行個體中遠端偵錯您的 Java 應用程式。不過，如果您只啟用一個一般輸入端點來偵錯，Azure 負載平衡器會讓您幾乎無法將偵錯工具連線到特定的角色執行個體。它會改將您連線至其隨機挑選的角色執行個體。

這是利用執行個體輸入端點來讓您更輕鬆地偵錯特定角色執行個體的案例類型。

例如，假設您計劃在您的部署中執行最多 5 個角色執行個體。使用角色內容對話方塊中的 [端點] 內容頁，建立一個執行個體輸入端點，並為其指派一個公用連接埠範圍，而不是單一連接埠號碼。例如，在 [公用連接埠] 輸入方塊中，指定 **81-85**。

使用這個執行個體端點部署您的應用程式之後，Azure 會從這個範圍指派一個唯一的連接埠號碼到每個角色執行個體。然後，為了找出執行個體被指派的連接埠號碼，您可以使用您的部署中的工具組自動設定的 *InstanceEndpointName***\_PUBLICPORT** 環境變數 (其中的 *InstanceEndpointName* 是您建立執行個體端點時所指派的名稱) (例如，藉由在網頁的頁尾傳回其值，當您瀏覽至它時就可以讀取)。

一旦您知道該執行個體被指派的公用連接埠號碼，您可以將它加到服務的主機名稱，以在 Eclipse 的偵錯組態中參考它。這會讓 Eclipse 偵錯工具連線至該特定執行個體，而不是任何其他的執行個體。

## 僅限 Windows：在計算模擬器中執行應用程式時偵錯 ##

>[AZURE.NOTE]Azure 模擬器只在 Windows 中提供。如果您使用 Windows 以外的作業系統，請略過本節。

1. 在模擬器中建置要測試的專案：在 Eclipse 的專案總管中，以滑鼠右鍵按一下 [MyAzureProject]，依序按一下 [內容]、[Azure]，將 [建置目的]設為 [在模擬器中測試]。
1. 重建您的專案：從 Eclipse 功能表按一下 [專案]，然後按一下 [全部建置]。
1. 在 Eclipse 的專案總管中，於 [WorkerRole1] 上按一下滑鼠右鍵，按一下 [Azure]，然後按一下 [偵錯]。
1. 在 [WorkerRole1 偵錯內容] 對話方塊中：
    1. 核取 [啟用這個角色的遠端偵錯]。
    1. 針對 [要使用的輸入端點]，使用工具組自動產生的預設端點，列為 [偵錯 (public:8090, private:8090)]。
    1. 確定未核取 [將 JVM 啟動在暫停模式，等候偵錯工具連線] 選項。
        >[AZURE.IMPORTANT] [將 JVM 啟動在暫停模式，等候偵錯工具連線] 選項僅適用於計算模擬器中的進階偵錯案例 (不適用於雲端部署)。如果使用 [將 JVM 啟動在暫停模式，等候偵錯工具連線] 選項，它會暫停伺服器的啟動程序，直到 Eclipse 偵錯工具連線到其 JVM 為止。雖然您可以在使用計算模擬器的偵錯工作階段使用此選項，但請勿將它使用在雲端部署的偵錯工作階段。伺服器的初始化是在 Azure 啟動工作中進行，而 Azure 雲端在啟動工作完成之前不會提供公用端點。因此，如果在雲端部署中啟用此選項，啟動程序將無法順利完成，因為它無法接收來自外部 Eclipse 用戶端的連線。
    1. 按一下 [建立偵錯組態]。
1. 在 [Azure 偵錯組態] 對話方塊中：
    1. 針對 [要偵錯的 Java 專案]，選取 [MyHelloWorld] 專案。
    1. 針對 [設定偵錯的對象]，核取 [Azure 計算模擬器]。
1. 按一下 [確定] 關閉 [Azure 偵錯組態] 對話方塊。
1. 按一下 [確定] 關閉 [WorkerRole1 偵錯內容] 對話方塊。
1. 在 index.jsp 中設定中斷點：
    1. 在 Eclipse 的專案總管中，依序展開 [MyHelloWorld]、[WebContent]，然後按兩下 [index.jsp]。
    1. 在 index.jsp 中，以滑鼠右鍵按一下 Java 程式碼左側的藍色列，按一下 [切換中斷點]**Toggle Breakpoints**，如下所示：![][ic551537]如果您在 Java 程式碼左側藍色列中看到一個中斷點圖示，表示已設定中斷點。
1. 按一下 Azure 工具列上的 [在 Azure 模擬器中執行] 按鈕，以在計算模擬器中啟動應用程式。
1. 在 Eclipse 的功能表，按一下 [執行]，然後按一下 [偵錯組態]。
1. 在 [偵錯組態] 對話方塊中，展開左窗格中的 [遠端 Java 應用程式]，選取 [Azure 模擬器 (WorkerRole1)]，然後按一下 [偵錯]。
1. 在計算模擬器指出應用程式正在執行後，在您的瀏覽器中執行 ****http://localhost:8080/MyHelloWorld**。如果 [確認檢視參數] 對話方塊提示，按一下 [是]。您的偵錯工作階段現在應該執行到設定中斷點的程式碼。

這會告訴您如何在計算模擬器中偵錯；下一節說明如何偵錯部署至 Azure 的應用程式。

## 偵錯注意事項 ##

* 偵錯之後，您可以透過依序按一下 Eclipse 的功能表、[視窗]、[開啟檢視]，然後選取您要使用的檢視，即可將檢視從 [偵錯] 切換成 [Java]。
* 若要在 GlassFish 中啟用遠端偵錯，請勿使用 Eclipse 的 Azure 工具組的遠端偵錯組態功能。請改為手動設定 GlassFish。由於 GlassFish 處理預先定義在環境變數中的 Java 選項的方式，此工具組的遠端偵錯組態功能無法正確搭配 GlassFish 使用。如果工具組的遠端偵錯組態功能已啟用，可能造成 GlassFish 無法啟動。

## 另請參閱 ##

[適用於 Eclipse 的 Azure 工具組][]

[在 Eclipse 中建立適用於 Azure 的 Hello World 應用程式][]

[安裝適用於 Eclipse 的 Azure 工具組][]

如需有關在 Azure 中使用 Java 的詳細資訊，請參閱 [Azure Java 開發人員中心][]。

<!-- URL List -->

[Azure Java 開發人員中心]: http://go.microsoft.com/fwlink/?LinkID=699547
[Azure 管理入口網站]: http://go.microsoft.com/fwlink/?LinkID=512959
[適用於 Eclipse 的 Azure 工具組]: http://go.microsoft.com/fwlink/?LinkID=699529
[在 Eclipse 中建立適用於 Azure 的 Hello World 應用程式]: http://go.microsoft.com/fwlink/?LinkID=699533
[安裝適用於 Eclipse 的 Azure 工具組]: http://go.microsoft.com/fwlink/?LinkId=699546
[在 JSP 中使用 Azure 服務執行階段程式庫]: http://go.microsoft.com/fwlink/?LinkID=699551

<!-- IMG List -->

[ic719504]: ./media/azure-toolkit-for-eclipse-debugging-azure-applications/ic719504.png
[ic551537]: ./media/azure-toolkit-for-eclipse-debugging-azure-applications/ic551537.png

<!---HONumber=AcomDC_1210_2015-->