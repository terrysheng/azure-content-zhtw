<properties title="How to use diagnostics" pageTitle="如何使用診斷" description="Learn how to set up diagnostics for your resources in Azure." authors="stepsic" manager="kamrani" />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="2014-11-04" ms.author="stepsic" />

# 設定診斷功能

在 Azure 入口網站預覽中，您現在可以為您的 Windows 虛擬機器與儲存體帳戶，設定內容豐富且經常產生的監視與診斷資料。

## 收集來自虛擬機器的豐富資料
1. 在 [Azure 入口網站預覽](https://portal.azure.com/) 中，按一下 [**瀏覽**]，然後按一下 [**虛擬機器**]。選取您想要監視的虛擬機器。
2. [**監視**] 透鏡包含一些預設的度量，例如 [**CPU 百分比**]、[**磁碟讀取和寫入**] 和 [**進出網路**]。按一下其中任何一個部分，即會顯示 [**度量**] 分頁。  
    ![Monitoring lens](./media/insights-how-to-use-diagnostics/Insights_VMMonitoringLens.png)
3. [**度量**] 分頁會顯示所選度量的詳細資料。分頁頂端會有一個圖形，圖形下會有一個資料表顯示這些度量的彙總，例如平均值、最小值和最大值。該資料表下會列出您所定義的警示 (依照分頁上顯示的度量進行篩選)。  
    ![Metric blade](./media/insights-how-to-use-diagnostics/Insights_VMMetricBlade.png)
4. 若要啟用豐富的診斷功能，按一下 [**設定**] 按鈕，接著畫面就會顯示 [**診斷**] 分頁。選擇 [**開啟**]：  
    ![Diagnostics blade](./media/insights-how-to-use-diagnostics/Insights_VMDiagnosticsBlade.png)
    - **Basic metrics**：有關您的虛擬機器的健康情況度量，例如處理器與記憶體 
    - **Per disk metrics**：附加至您的虛擬機器的所有磁碟度量
    - **.NET metrics**：有關在您的虛擬機器上執行的 .NET 與 ASP.NET 應用程式度量
    - **Network metrics**：有關您的網路連線與 Web 服務的度量
    - **Windows event application logs**：傳送至應用程式通道的 Windows 事件
    - **Windows event system logs**：傳送至系統通道的 Windows 事件。此事件同時包含來自 [Microsoft 反惡意程式碼](http://go.microsoft.com/fwlink/?LinkID=404171&clcid=0x409) (英文) 的所有事件。 
    - **Windows event security logs**：傳送至安全性通道的 Windows 事件
    - **Diagnostics infrastructure logs**：記錄有關診斷收集基礎結構的資訊
    - **IIS 記錄**：有關 IIS 伺服器的記錄
    所有的度量與記錄都將以一分鐘的間隔來記錄，以便您隨時保有最新的機器相關資訊。

當您對儲存體帳戶啟用診斷資訊功能時，我們將向該帳戶收取正常儲存、交易及出口流量費用。不過，這些功能並不會產生大量的資料 (IIS 記錄是可能的例外情況)。為了有效降低出口流量費用，請在選取儲存體帳戶所在區域時，盡量與虛擬機器一致。

一旦您按一下 [**確定**]，您的儲存體帳戶在幾分鐘內就會開始出現資料。您無法在執行 Linux 的虛擬機器上啟用診斷功能，而且必須安裝客體代理程式以便啟用診斷功能。

## 收集來自儲存體帳戶的豐富資料

一直以來您都可以順利收集到來自儲存體帳戶的一些資料，不過現在隨著 Azure 入口網站預覽功能的問世，您可以將資料收集間隔縮短至一分鐘，以便真正了解您的儲存體帳戶內的一舉一動。啟用一分鐘度量的步驟，類似於虛擬機器上的設定步驟：

1. 您可以按一下 [**Storage account**] 分頁上的任何圖表，前往 [**度量**] 分頁。
2. 按一下命令列中的 [**診斷**] 按鈕。
3. 選取您要從儲存體帳戶中收集的資料類型：  
    ![Storage diagnostics](./media/insights-how-to-use-diagnostics/Insights_StorageDiagnostics.png)
4. 按一下 [**確定**]。資料初次顯示時，需要幾分鐘時間來完成。

## 視覺化診斷資料 

一旦您啟用了診斷功能，就可用滑鼠右鍵按一下任何一張圖表，並選取 [**編輯查詢**] 來查看完整的可用度量清單：

![Edit query](./media/insights-how-to-use-diagnostics/Insights_VMEditQuery.png)

您可以繪製這些度量，放大到 [**Past hour**]、縮小到 [**Past week**] 或甚至選擇 [**自訂**] 時間範圍：
 
![Custom timerange](./media/insights-how-to-use-diagnostics/Insights_VMCustomTime.png)

您會發現這些度量比先前提供的資料還要細膩，而且延遲時間非常短。

此時您無法繪製具有多重執行個體的度量，例如個別程序或個別磁碟度量。如需有關如何自訂您的監視圖表的詳細資訊，請參閱 [如何自訂監視](http://go.microsoft.com/fwlink/?LinkID=394523&clcid=0x409) (英文)。

## 對診斷資料發出警示通知

除了對度量進行視覺化處理之外，您還可以對入口網站預覽中的任何度量發出警示通知。首先，將頁面向下捲動至虛擬機器或儲存體分頁的 [**警示規則**] 部分，然後按一下 [**加入警示**]：

![Add alert](./media/insights-how-to-use-diagnostics/Insights_VMAlerts.png)

接著您就可以選取先前啟用的任何一項診斷度量：

![JIT alert](./media/insights-how-to-use-diagnostics/Insights_VMJITAlert.png)

該圖形會顯示您的警示臨界值與前一天度量相比較的預覽。按一下 [**儲存**] 之後，幾分鐘內您將收到所選的度量是否超出臨界值的通知。 

請注意，只在 [預覽入口網站] 上顯示的所有度量，無法在 [完整入口網站] 上提供警示通知。如此一來，來自預覽入口網站的特定警示規則，將無法顯示在完整入口網站上。
