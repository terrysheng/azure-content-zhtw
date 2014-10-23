<properties title="How to create web test" pageTitle="How to create web test" description="Learn how to create web tests in Azure." authors="awills" manager="kamrani" />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="2014-09-29" ms.author="awills"></tags>

# Web 測試

您的 Azure 網站是否仍可運作？它是否可正常回應，速度是否夠快？請設定 Web 測試，以定期測試您的網站。如果網站失效，或是回應速度緩慢或不正確，您將會收到電子郵件警示。此外，您也會收到顯示網站在一段時間內的可用性和回應性的圖形。

![Browse Hub][]

您可以為任何使用基本或標準方案的 Azure 網站設定可用性監視。您最多可以設定 3 項 Web 測試，且最多可從 3 個地理位置執行其中的各項測試。您無須以任何形式變更網站。

您也可以在部署或已知的運作中段期間暫停 Web 測試，使整體的可用性不受影響。整體可用性會從所有 Web 測試計算得出，包括選取的不同位置。

## 如何設定 Web 測試

1.  若要設定 Web 測試，請先確定您的網站是 [基本] 或 [標準] 的。
2.  接著，在 [網站] 分頁上選擇 [Web 測試] 組件：
    
	![設定 Web 測試][]
3.  在 [建立 Web 測試] 分頁中為 Web 測試命名，然後指定作為測試執行對象的 URL。
    
	![建立 Web 測試][]
4.  接著，從 8 個位置中選擇最多 3 個位置。
5.  指定成功的準則，包括 HTTP 狀態碼檢查，或網站本身包含的字串。
6.  然後選擇警示設定，包括敏感度和電子郵件的收件者。
    ![警示][]

    -   若選擇高敏感度，只要在 1 個位置中偵測到測試失敗，就會建立警示。
    -   若選擇中敏感度，則在 10 分鐘內至少要有半數的位置失敗，才會建立警示。
    -   若選擇低敏感度，則所有位置的測試都必須在 15 分鐘內失敗，才會建立警示。

完成設定後，請按一下 [建立] 按鈕。您的 Web 測試在建立之後，將會以 5 分鐘的間隔從指定的位置執行，因此資料可能需要一點時間才會顯示。

### 位置可透露哪些訊息？

我們從這些位置將要求傳送至網站，而使用者也將以相同方式從世界各地存取網站。如果您的網站在美國地區無法使用，但在歐洲地區仍可使用，您可以得知，這是網路問題，而不是您的伺服器有問題。

### 使用成功準則

一般情況下，我們希望測試出來的 HTTP 狀態碼會是 200，表示伺服器可辨識 URI，並傳回頁面。

在內容比對字串中不可使用萬用字元，但您可以測試任何純文字。

## 不妙 - 我的網站當掉了！

您的 Web 測試若未通過成功準則，將會被標示為失敗測試，因而降低網站的整體可用性。失敗測試 (以及成功測試) 會顯示在特定 Web 測試分頁上的散佈圖中。

![Failed Test][]

您可以分析失敗測試的失敗原因。請向下鑽研失敗的 Web 測試，並開啟 Visual Studio 的「Web 測試結果檔案」，以分析並了解測試的失敗原因。

![Open in VS][]

  [Browse Hub]: ./media/insights-create-web-tests/Inisghts_WebTestBlade.png
  [設定 Web 測試]: ./media/insights-create-web-tests/Insights_ConfigurePart.png
  [建立 Web 測試]: ./media/insights-create-web-tests/Insights_CreateTest.png
  [警示]: ./media/insights-create-web-tests/Inisghts_AlertCreation.png
  [Failed Test]: ./media/insights-create-web-tests/Insights_FailedWebTest.png
  [Open in VS]: ./media/insights-create-web-tests/Insights_OpenInVS.png
