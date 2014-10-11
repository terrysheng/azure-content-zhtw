<properties linkid="manage-services-mediaservices-scale-media-service" urlDisplayName="How to scale" pageTitle="How to Scale a media service | Azure Documentation" metaKeywords="" description="Learn how to scale Media Services by specifying the number of On-Demand Streaming Reserved Units and Encoding Reserved Units that you would like your account to be provisioned with." metaCanonical="" services="media-services" documentationCenter="" title="How to Scale a Media Service" authors="migree" solutions="" manager="" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="migree"></tags>

# 如何調整媒體服務

[WACOM.INCLUDE [disclaimer][]]

您可以指定要佈建給帳戶的**隨選串流保留單元**和**編碼保留單元**來調整媒體服務。

## 隨選串流保留單元

隨選串流保留單元為您提供專用的流出容量 (以 200 Mbps 為增量來購買) 和其他功能 (目前包括[動態封裝功能][])。依預設，隨選串流是以共用執行個體模型來設定，可與其他所有使用者共用伺服器資源 (例如，運算、流出容量等)。若要改進隨選串流輸送量，建議購買隨選串流保留單元。

若要變更隨選串流保留單元的數目，請執行下列動作：

1.  在[管理入口網站][]中，按一下 [媒體服務]。接著，按一下媒體服務的名稱。

2.  選取 [ORIGINS] 頁面。然後，按一下您要修改的原始來源。

    ![Origin page][]

3.  若要指定保留單元數目，請選取 [調整] 索引標籤，然後移動 [reserved capacity] 滑動軸。

    ![Scale page][]

4.  按 [儲存] 以儲存您的變更。

    任何新的隨選串流單元大約需要 20 分鐘才能配置完成。

    **注意：**目前，從隨選串流單元的任何正值回到無，可能會使隨選串流停用長達一小時。

    **注意：**計算成本時會使用 24 小時內指定的最大單元數。如需定價詳細資料的相關資訊，請參閱＜[媒體服務定價詳細資料][]＞。

## 編碼保留單元

佈建的編碼保留單元數目等於給定帳戶中可同時處理的媒體工作數目。例如，如果帳戶有 5 個保留單元，則只要有工作需要處理，就會同時執行 5 個媒體工作。剩餘的工作會在佇列中等待，且隨著執行中的工作完成，就立即循序地挑選來開始處理。如果帳戶未佈建任何保留單元，則會循序地挑選工作。在此情況下，一件工作完成與下一件工作開始之間的等待時間，視系統中的資源可用性而定。

若要變更編碼保留單元的數目，請執行下列動作：

1.  在[管理入口網站][]中，按一下 [媒體服務]。接著，按一下媒體服務的名稱。

2.  選取 [處理器] 頁面。

    ![Processors page][]

3.  按 [儲存] 以儲存您的變更。

    新的編碼保留單元幾乎是立即配置。

    **注意：**計算成本時會使用 24 小時內指定的最大單元數。

## 建立支援票證

依預設，每一個媒體服務帳戶可調整為最多 25 個編碼保留單元和 5 個隨選串流保留單元。您可以建立支援票證來要求更高的限制。

若要建立支援票證，請執行下列動作：

1.  在[管理入口網站][1]登入您的 Azure 帳戶。
2.  移至[支援][]。
3.  按一下 [取得支援]。
4.  選取您的訂用帳戶。
5.  在支援類型下，選取 [技術]。
6.  按一下 [建立票證]。
7.  在下一頁顯示的產品清單中，選取 [Azure 媒體服務]。
8.  在 [問題類型] 中選取 [媒體處理]，然後在類別下選取 [保留單元]。
9.  按一下 [繼續]。
10. 依照下一頁的指示進行，然後輸入您需要多少編碼保留單元或隨選串流保留單元的詳細資料。
11. 按一下 [提交] 來建立票證。

  [disclaimer]: ../includes/disclaimer.md
  [動態封裝功能]: http://go.microsoft.com/fwlink/?LinkId=276874
  [管理入口網站]: https://manage.windowsazure.com/
  [Origin page]: ./media/media-services-how-to-scale/media-services-origin-page.png
  [Scale page]: ./media/media-services-how-to-scale/media-services-origin-scale.png
  [媒體服務定價詳細資料]: http://go.microsoft.com/fwlink/?LinkId=275107
  [Processors page]: ./media/media-services-how-to-scale/media-services-encoding-scale.png
  [1]: http://manage.windowsazure.com
  [支援]: http://www.windowsazure.com/en-us/support/contact/
