<properties 
   pageTitle="使用 IntelliTrace 和 Visual Studio 偵錯發佈的雲端服務"
   description="使用 IntelliTrace 和 Visual Studio 偵錯發佈的雲端服務"
   services="visual-studio-online"
   documentationCenter="n/a"
   authors="patshea123"
   manager="douge"
   editor="tlee" />
<tags 
   ms.service="visual-studio-online"
   ms.devlang="multiple"
   ms.topic="article"
   ms.workload="na"
   ms.tgt_pltfrm="na"
   ms.date="08/12/2015"
   ms.author="patshea" />

# 使用 IntelliTrace 和 Visual Studio 偵錯發佈的雲端服務

##概觀

有了 IntelliTrace，您可以於角色執行個體在 Azure 中執行時，記錄其廣泛的偵錯資訊。如果您需要找出問題的原因，您可以從 Visual Studio 使用 IntelliTrace 記錄檔來瀏覽程式碼，如同它是在 Azure 中執行。實際上，Azure 應用程式在 Azure 中以雲端服務形式執行時，IntelliTrace 會記錄主要執行程式碼和環境資料，並且可讓您從 Visual Studio 重新播放記錄的資料。或者，您可以使用遠端偵錯以直接附加到在 Azure 中執行的雲端服務。請參閱[偵錯雲端服務](http://go.microsoft.com/fwlink/p/?LinkId=623041)。

>[AZURE.IMPORTANT]IntelliTrace 僅適用於偵錯，並且不應該用於生產環境部署。

>[AZURE.NOTE]如果您有安裝 Visual Studio Enterprise，而您的 Azure 應用程式以 .NET Framework 4 或更新版本為目標，則可以使用 IntelliTrace。IntelliTrace 會收集 Azure 角色的資訊。這些角色的虛擬機器一律會執行 64 位元作業系統。

## 為 IntelliTrace 設定 Azure 應用程式

若要為 Azure 應用程式啟用 IntelliTrace，您必須從 Visual Studio Azure 專案建立並發佈應用程式。發佈至 Azure 之前，您必須您的 Azure 應用程式設定 IntelliTrace。如果您發佈應用程式但未設定 IntelliTrace，之後又想要這麼做，您必須從 Visual Studio 重新發佈應用程式。如需詳細資訊，請參閱[使用 Azure Tools 發佈雲端服務](http://go.microsoft.com/fwlink/p/?LinkId=623012)。

1. 準備好要部署 Azure 應用程式時，請確認專案建置目標是設定為 [偵錯]。

1. 在 [方案總管] 中開啟 Azure 專案的捷徑功能表並選擇 [發佈]。[發佈 Azure 應用程式] 精靈隨即出現。

1. 若要在發佈至雲端時收集您的應用程式的 IntelliTrace 記錄檔，請選取 [啟用 IntelliTrace] 核取方塊。

    >[AZURE.NOTE]發佈 Azure 應用程式時，您可以啟用 IntelliTrace 或分析。您不能同時啟用。

1. 若要自訂基本的 IntelliTrace 組態，請選擇 [設定] 超連結。

    [IntelliTrace 設定] 對話方塊隨即出現，如下圖所示。您可以指定要記錄的事件、是否要收集呼叫資訊、收集的記錄的模組和處理序，以及配置多少空間來存放記錄。如需有關 IntelliTrace 的詳細資訊，請參閱[使用 IntelliTrace 進行偵錯](http://go.microsoft.com/fwlink/?LinkId=214468)。

    ![VST\_IntelliTraceSettings](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/IC519063.png)

IntelliTrace 記錄檔是循環記錄檔，大小上限是在 IntelliTrace 設定中指定 (預設大小為 250 MB)。IntelliTrace 記錄檔會收集到虛擬機器的檔案系統中的檔案。要求記錄檔時，會擷取該時間點的快照，並下載到您的本機電腦。

將 Azure 應用程式發佈至 Azure 之後，您可以從伺服器總管中的 [Azure 運算] 節點判斷是否已啟用 IntelliTrace，如下圖所示：

![VST\_DeployComputeNode](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/IC744134.png)

## 下載角色執行個體的 IntelliTrace 記錄檔

您可以在 [伺服器總管] 的 [雲端服務] 節點下載角色執行個體的 IntelliTrace 記錄檔。展開 [雲端服務] 節點，直到您找到您感興趣的執行個體，開啟此執行個體的捷徑功能表，然後選擇 [檢視 IntelliTrace 記錄檔]。IntelliTrace 記錄檔會下載到本機電腦的目錄中的檔案。每當您要求 IntelliTrace 記錄檔，即會建立新的快照。

下載記錄檔時，Visual Studio 會在 [Azure 活動記錄檔] 視窗中顯示作業的進度。如下圖所示，您可以展開作業的行項目，以查看詳細資料。

![VST\_IntelliTraceDownloadProgress](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/IC745551.png)

下載 IntelliTrace 記錄檔時，您可以繼續使用 Visual Studio。當記錄檔完成下載時，它會自動在 Visual Studio 中開啟。

>[AZURE.NOTE]IntelliTrace 記錄檔可能包含架構產生和後續處理的例外狀況。內部架構程式碼會在正常啟動角色時產生這些例外狀況，因此您可以放心忽略。

## 後續步驟

[偵錯雲端服務](http://go.microsoft.com/fwlink/p/?LinkID=62304)

<!---HONumber=August15_HO8-->