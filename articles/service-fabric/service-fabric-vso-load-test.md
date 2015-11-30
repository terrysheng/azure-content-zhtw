<properties
    pageTitle="使用 Visual Studio Online 負載測試您的應用程式 | Microsoft Azure"
    description="了解如何使用 Visual Studio Online 對您的 Azure Service Fabric 應用程式進行壓力測試。"
    services="service-fabric"
    documentationCenter="na"
    authors="cawa"
    manager="timlt"
    editor="" />

<tags
    ms.service="multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="multiple"
    ms.date="10/28/2015"
    ms.author="cawa" />

# 使用 Visual Studio Online 負載測試您的應用程式

本文說明如何使用 Visual Studio 負載測試功能對應用程式進行壓力測試。它會使用 Service Fabric 具狀態服務後端和無狀態服務 Web 前端。以下使用的範例應用程式是飛機位置模擬器。您提供飛機識別碼、起飛和目的地位置。應用程式的後端會處理要求，前端會顯示地圖上與準則相符的飛機。

下圖說明您測試的 Service Fabric 應用程式。

![][0]

## 必要條件
開始之前，您需要執行下列動作。

- 取得 Visual Studio Online (VSO) 帳戶。您可以在 [Visual Studio Online](https://www.visualstudio.com) 取得一個免費帳戶。
- 取得並安裝 Visual Studio 2013 或 Visual Studio 2015。本文使用 Visual Studio 2015 Enterprise 版本，但是 Visual Studio 2013 和其他版本應該也同樣可以運作。
- 將您的應用程式部署至預備環境。如需相關詳細資訊，請參閱[如何使用 Visual Studio 將應用程式部署至遠端叢集](service-fabric-publish-app-remote-cluster.md)。
- 了解您的應用程式的使用模式。這項資訊是用來模擬負載模式。
- 了解您的負載測試的目標。這可協助您解譯和分析負載測試結果。

## 建立及執行 Web 效能和負載測試專案

### 建立 Web 效能和負載測試專案

1. 開啟 Visual Studio 2015。在功能表列上選擇 [檔案] > [新增] > [專案] 以開啟 [新增專案] 對話方塊。

1. 展開 [Visual C#] 節點並選擇 [測試] > [Web 效能和負載測試專案]。給予專案名稱，然後選擇 [確定] 按鈕。

    ![][1]

    您應該會看到新的 Web 效能和負載測試專案。

    ![][2]

### 記錄 Web 效能測試

1. 開啟 .webtest 專案。

1. 選擇 [加入錄製] 圖示以在瀏覽器中啟動錄製工作階段。

    ![][3]

    ![][4]

1. 瀏覽至 Service Fabric 應用程式。[錄製] 面板應該會顯示 Web 要求。

    ![][5]

1. 執行您預期使用者執行的一系列動作。這些動作會當做產生負載的模式。

1. 完成時，選擇 [停止] 按鈕以停止錄製。

    ![][6]

    Visual Studio 中的 .webtest 專案應該已擷取一系列的要求。會自動取代動態參數。此時，您可以刪除不屬於您的測試案例的任何額外、重複相依性要求。

1. 儲存專案，然後選擇 [執行測試] 命令以在本機執行 Web 效能測試，並且確認一切運作正常。

    ![][7]

### 參數化 Web 效能測試

您可以將 Web 效能測試轉換成 Web 效能測試程式碼，然後編輯程式碼，以參數化 Web 效能測試。或者，您可以將 Web 效能測試繫結至資料清單，以便測試逐一查看資料。請參閱[產生和執行 Web 效能測試程式碼](https://msdn.microsoft.com/library/ms182552.aspx)以取得如何將 Web 效能測試轉換成程式碼測試的詳細資訊，以及參閱[將資料來源新增至 Web 效能測試](https://msdn.microsoft.com/library/ms243142.aspx)以取得如何將資料繫結至 Web 效能測試的詳細資訊。

對於此範例，我們會將 Web 效能測試轉換成程式碼測試，您就可以使用產生的 GUID 取代飛機識別碼，並且加入更多要求以將航班傳送至不同位置。

### 建立負載測試專案

負載測試專案是由 Web 效能測試和單位測試所描述的一或多個案例，以及其他指定的負載測試設定組成。下列步驟說明如何建立負載測試專案。

1. 在您的 Web 效能和負載測試專案的捷徑功能表上選擇 [新增] > [負載測試]。在 [負載測試] 精靈中，選擇 [下一步] 按鈕以設定測試設定。

1. 在 [負載模式] 區段中，選擇要常數使用者負載或逐步執行負載，開頭為少數使用者並且隨著時間經過增加使用者。

    如果您對於使用者負載量有良好的評估並且想要查看目前系統的執行方式，請選擇 [常數負載]。如果您的目標是了解系統在不同負載量之下的執行是否一致，請選擇 [逐步執行負載]。

1. 在 [測試混合] 區段中，選擇 [新增] 按鈕，然後選取您要包含在負載測試的測試。您可以使用 [散發] 資料行指定每一項測試的測試執行總計百分比。

1. 在 [回合設定] 區段中，指定負載測試持續期間。
    >[AZURE.NOTE] [測試反覆項目] 選項只有當您使用 Visual Studio 在本機執行負載測試時才可以使用。

1. 在 [回合設定] 的 [位置] 區段中，指定產生負載測試要求的位置。精靈可能會提示您登入 VSO 帳戶。使用您的 VSO 帳戶登入，然後選擇地理位置。完成時，選擇 [完成] 按鈕。

1. 建立負載測試之後，開啟 .loadtest 專案並選擇目前回合設定，例如 [回合設定] > [回合設定1 [作用中]]。這會在 [屬性] 視窗中開啟回合設定。

1. 在 [回合設定] 屬性視窗中的 [結果] 區段中，[計時詳細資料儲存區] 設定的預設值應該為 [無]。將此值變更為 [所有個別細節] 以取得更多有關負載測試結果的資訊。請參閱[負載測試](https://www.visualstudio.com/load-testing.aspx)以取得有關如何連接至 Visual Studio Online 和執行負載測試的詳細資訊。

### 使用 Visual Studio Online 執行負載測試

選擇 [執行負載測試] 命令來啟動測試回合。

![][8]

## 檢視及分析負載測試結果

隨著負載測試進行，效能資訊會圖表化。您應該會看到類似下面的圖形。

![][9]

1. 選擇頁面頂端附近的 [下載報告] 連結。下載報告之後，選擇 [檢視報告] 按鈕。

    在 [圖形] 索引標籤上，您可以看到各種效能計數器的圖形。在 [摘要] 索引標籤上，會顯示整體測試結果。在 [資料表] 索引標籤上，會顯示成功和失敗負載測試的總數。

1. 在 [測試] > [失敗] 和 [錯誤] > [計數] 資料行上選擇數字連結，以查看錯誤詳細資料。

    [詳細資料] 索引標籤會顯示失敗要求的虛擬使用者和測試案例資訊。如果負載測試包含多個案例，此資料相當有用。

請參閱[在負載測試分析器的圖形檢視中分析負載測試結果](https://www.visualstudio.com/load-testing.aspx)以取得有關檢視負載測試結果的詳細資訊。

## 自動化您的負載測試

Visual Studio Online 負載測試提供 API，可讓您在 VSO 帳戶中管理負載測試和分析結果。如需詳細資訊，請參閱[雲端負載測試 Rest API](http://blogs.msdn.com/b/visualstudioalm/archive/2014/11/03/cloud-load-testing-rest-apis-are-here.aspx)。

## 後續步驟
- [監視和診斷本機開發設定中的服務](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[0]: ./media/service-fabric-vso-load-test/OverviewDiagram.png
[1]: ./media/service-fabric-vso-load-test/NewProjectDialog.png
[2]: ./media/service-fabric-vso-load-test/Project.png
[3]: ./media/service-fabric-vso-load-test/AddRecording.png
[4]: ./media/service-fabric-vso-load-test/AddRecording2.png
[5]: ./media/service-fabric-vso-load-test/ActionSequence.png
[6]: ./media/service-fabric-vso-load-test/StopRecording.png
[7]: ./media/service-fabric-vso-load-test/RunTest.png
[8]: ./media/service-fabric-vso-load-test/RunTest2.png
[9]: ./media/service-fabric-vso-load-test/Graph.png

<!---HONumber=Nov15_HO4-->