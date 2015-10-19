<properties
	pageTitle="比較：Apache Storm 與Azure 串流分析 | Microsoft Azure"
	description="了解如何使用串流分析來執行即時 Twitter 情感分析。從事件產生到即時儀表板資料的逐步指導。"
	keywords="real-time twitter,sentiment analysis,social media analysis,social media analytics tools"
	services="stream-analytics"
	documentationCenter=""
	authors="jeffstokes72"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="stream-analytics"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="big-data"
	ms.date="10/05/2015"
	ms.author="jeffstok"/>

# Apache Storm 和 Azure 串流分析的比較 #

## 簡介 ##

這份文件說明 Azure 串流分析和 Apache Storm 在 HDInsight 上做為受管理的服務時的置放位置。目標是要協助客戶了解這兩個服務的價值主張，並制定適合自己業務使用案例的決策。

雖然兩者都提供 PaaS 解決方案的優點，但還有幾個主要的不同功能區分這兩個服務。我們認為列出這兩個服務的功能及限制可協助客戶選擇能達成其目標所需的解決方案。

## 一般 ##
<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Azure 串流分析</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache Storm on HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>開放原始碼</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    否，Azure 串流分析是 Microsoft 的專屬服務。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    是，Apache Storm 是 Apache 的授權技術。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Microsoft 提供支援</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    是
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    是
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>硬體需求</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    沒有硬體需求。Azure 串流分析是一種 Azure 服務。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    沒有硬體需求。Apache Storm 是一種 Azure 服務。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>最高層級單位</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    客戶可藉由 Azure 串流分析部署和監視串流工作。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    透過 HDInsight 上的 Apache Storm，客戶可部署和監視整個叢集，該叢集可裝載多個 Storm 工作，以及其他工作負載 (包括批次)。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>價格</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    串流分析是依所處理的資料量，和需要的串流單位數量 (根據每小時執行的工作數量) 計費。
                </p>
                <p>
                    <a href="http://azure.microsoft.com/pricing/details/stream-analytics/">如需進一步的價格資訊，請參閱這裡</a>。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    針對 HDInsight 上的 Apache Storm，購買的單位是叢集，並根據叢集執行的時間收費，與部署的工作是各自獨立的。
                </p>
                <p>
                    <a href="http://azure.microsoft.com/pricing/details/hdinsight/">如需進一步的價格資訊，請參閱這裡</a>。
                </p>
            </td>
        </tr>
    </tbody>
</table>
## 編寫 ##
<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Azure 串流分析</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache Storm on HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>功能：SQL DSL</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    可以，支援一種容易使用的 SQL 語言。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    不行，使用者必須撰寫 Java C# 格式的程式碼，或使用 Trident API。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>功能：暫時運算子</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    提供現成可用的視窗彙總值和暫時聯結。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    暫時運算子必須由使用者實作。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>開發體驗</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    提供透過 Azure 入口網站使用範例資料進行互動式編寫和偵錯的體驗。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    我們提供 Visual Studio 的開發、偵錯和監視體驗給 .NET 使用者，而 Java 和其他語言的開發人員可能會使用他們選擇的 IDE。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>偵錯支援</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    串流分析透過提供基本的工作狀態和作業記錄檔的方式偵錯，但目前無法變更包含在記錄檔中的項目/數量，例如詳細資訊模式。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    我們會提供詳細的記錄檔供偵錯使用。使用者可以用兩種方式找出記錄檔，透過 Visual Studio，也可以 RDP 到叢集存取記錄檔。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>UDF (使用者定義函數) 支援</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    目前不支援 UDF。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    可以使用 C#、Java 或您選擇的語言撰寫 UDF。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>可延伸 - 自訂程式碼</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    串流分析不支援可延伸程式碼。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    可以，在 Storm 中可以使用 C#、Java 或其他支援的語言撰寫自訂程式碼。
                </p>
            </td>
        </tr>
    </tbody>
</table>
## 輸入和輸出 ##
<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Azure 串流分析</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache Storm on HDInsight</strong>
                </p>
            </td>
        </tr>
		<tr>
            <td width="174" valign="top">
				<p>
				 <strong>輸入資料來源</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>支援的輸入來源是 Azure 事件中樞和 Azure Blob。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    為事件中樞、服務匯流排、Kafka 等提供連接器。可透過自訂程式碼實作不支援的連接器。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>輸入資料格式</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    支援的輸入格式為 Avro、JSON、CSV。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    可透過自訂程式碼實作任何格式。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>輸出</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    串流工作可以有多個輸出。支援的輸出：Azure 事件中樞、Azure Blob 儲存體、Azure 資料表、Azure SQL DB 和 PowerBI。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    支援在拓撲中納入多個輸出，每個輸出可包含必須進行下游處理所需的自訂邏輯。現成可用的 Storm 包含 PowerBI、Azure 事件中樞、Azure Blob 存放區、Azure DocumentDB、SQL 和 HBase 的連接器。可透過自訂程式碼實作不支援的連接器。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>資料編碼格式</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    串流分析僅可利用 UTF-8 資料格式。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    可透過自訂程式碼實作所有資料編碼格式。
                </p>
            </td>
        </tr>
    </tbody>
</table>
## 管理和作業 ##
<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Azure 串流分析</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache Storm on HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>工作部署模型</strong>
                </p>
                <p>
                    - <strong>Azure 入口網站</strong>
                </p>
                <p>
                    - <strong>Visual Studio</strong>
                </p>
                <p>
                    - <strong>PowerShell</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    可透過 Azure 入口網站、PowerShell 和 REST API 實作部署。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    可透過 Azure 入口網站、PowerShell、Visual Studio 和 REST API 實作部署。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>監視 (統計資料、計數器等)</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    可透過 Azure 入口網站 和 REST API 實作監視。
                </p>
                <p>
                    使用者也可以設定 Azure 警示。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    可透過 Storm UI 和 REST API 實作監視。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>延展性</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    每個工作的串流單位數量。每個串流單位的處理量最多 1 MB/秒。預設上限為 50 個單位。可透過呼叫放寬限制。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    HDI Storm 叢集中的節點數量。節點數量沒有限制 (最高限制依您的 Azure 配額定義)。可透過呼叫放寬限制。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>資料處理限制</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    使用者可以透過相應增加或減少串流單位，來增加資料處理量，或將成本降至最低。
                </p>
                <p>
                    相應增加至 1 GB/秒
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    使用者可以相應增加或減少叢集大小以符合需求。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>停止/繼續</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    停止並從上次的停止處繼續。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    停止並根據水位線從上次的停止處繼續。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>服務和架構更新</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    可不停機自動修補。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    可不停機自動修補。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>透過具有 SLA 保證所保障的高可用性服務提供商務持續性</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    SLA 保證運作時間達 99.9%
                </p>
                <p>
                    可在失敗時自動復原
                </p>
                <p>
                    內建可設定狀態的暫時運算子復原功能。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    SLA 保證 Storm 叢集的運作時間達 99.9%。Apache Storm 是一個容錯串流平台，不過客戶有責任確保其串流工作的運作不中斷。
                </p>
            </td>
        </tr>
    </tbody>
</table>
## 進階功能 ##
<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Azure 串流分析</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache Storm on HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>事件延遲和順序錯誤的處理</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    內建可設定的原則，可重新排序、捨棄事件，或調整事件的時間。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    使用者必須實作邏輯來處理這種情況。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>參考資料</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    Azure Blob 提供的參考資料的記憶體內部查閱快取大小上限為 100 MB。由服務重新整理參考資料。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    資料大小沒有限制。為 HBase、DocumentDB、SQL Server 和 Azure 提供連接器。可透過自訂程式碼實作不支援的連接器。
                </p>
                <p>
                    必須透過自訂程式碼重新整理參考資料。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>與 Machine Learning 整合</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    是，在建立 ASA 工作期間，將 Azure Machine Learning 模型設定為函數。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    透過 Storm Bolt 提供。
                </p>
            </td>
        </tr>
    </tbody>
</table>

<!---HONumber=Oct15_HO2-->