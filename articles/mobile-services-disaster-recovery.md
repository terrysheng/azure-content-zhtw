<properties urlDisplayName="Recover your mobile service in the event of a disaster" pageTitle="發生災害時回復行動服務 - Azure 行動服務" metaKeywords="" description="Learn how to recover your mobile service in the event of a disaster." metaCanonical="" services="" documentationCenter="Mobile" title="Recover your mobile service in the event of a disaster" authors="brettsam" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="11/11/2014" ms.author="brettsam" />

# 發生災害時回復行動服務

使用 Azure 行動服務來部署應用程式時，您可以利用內建的功能，在發生可用性問題的情況下確保業務續航力，例如伺服器故障、網路中斷、資料遺失和大規模的設備損失。如果您是部署傳統的內部部署解決方案，則必須設計、實作和管理許多容錯和基礎結構功能，但使用 Azure 行動服務來部署應用程式，就能直接享受這些功能。Azure 可緩和絕大部分可能的失敗後果，且成本低廉。

<h2><a name="prepare"></a>對可能的災害預做準備</h2>

為了在發生可用性問題的情況下輕鬆回復，您可以事先預做準備： 

+ **備份 Azure 行動服務 SQL Database 中的資料**
	<br/>您的行動服務應用程式資料儲存在 Azure SQL Database。建議您依照 [Azure SQL Database 業務持續性]中的指示來備份資料。
+ **備份您的行動服務指令碼**
	<br/>建議您將行動服務指令碼儲存在原始檔控制系統中，例如 [Team Foundation Service] 或 [GitHub]，而不要只依賴行動服務本身中的複本。您可以透過 Azure 入口網站、使用行動服務[原始檔控制功能]，或[使用 Azure 命令列工具]來下載指令碼。請注意入口網站中標示為 [預覽] 的功能，因為不保證可回復這些指令碼，可能需要從您自己的原始檔控制來源中回復指令碼。
+ **保留次要行動服務**
	<br/>如果行動服務發生可用性問題，您可能必須將它重新部署到替代的 Azure 區域。為了確保容量可用 (例如在遺失整個區域的罕見情況下)，建議您在替代區域中建立次要行動服務，並將模式設定為與主要服務的模式相同或更高(如果主要服務處於共用模式，您可以設次要服務設為共用或保留。但是如果保留主要服務，則次要服務也必須保留)。


<h2><a name="watch"></a>監看問題的徵兆</h2>

這些情況指出可能需要進行回復作業的問題：

+ 連線至行動服務的應用程式已經很久沒有與服務進行通訊。
+ 行動服務狀態在 [Azure 入口網站]中會顯示為 [**狀況不良**]。
+ 在 Azure 入口網站中，行動服務的每個索引標籤頂端會顯示 [**狀況不良**] 旗幟，且管理作業會產生錯誤訊息。
+ [Azure 服務儀表板]指出發生可用性問題。

<h2><a name="recover"></a>從災害中回復</h2>

發生問題時，請利用 [服務儀表板] 來取得指引和最新消息。
 
如果服務儀表板出現提示，請執行下列步驟，在替代 Azure 區域中將行動服務還原為執行中狀態。如果您已事先建立次要服務，則其容量將用來還原主要服務。因為在回復之後，主要服務的 URL 和應用程式金鑰不變，您並不需要更新任何參考此服務的應用程式。 

在中斷之後回復行動服務：

1. 在 Azure 入口網站中，請確定所報告的服務狀態為 [**狀況不良**]。

2. 如果您已保留次要行動服務，則可以略過此步驟。

   如果您尚未保留次要行動服務，請立即在其他 Azure 區域中建立。將模式設定為與主要服務的模式相同或更高(如果主要服務處於共用模式，您可以設次要服務設為共用或保留。但是如果保留主要服務，則次要服務也必須保留)。

3. 依照[使用命令列工具將行動服務自動化]中的說明，設定 Azure 命令列工具來處理您的訂閱。

4. 現在您可以使用次要服務來回復主要服務。

    <div class="dev-callout"><b>重要事項</b>
	<p>執行此步驟中的命令時，將會刪除次要服務，所以其容量可用來回復主要服務。執行命令之前，建議您先備份指令碼和設定 (若想要保留的話)。</p>
    </div>

   準備好時，請執行此命令：

		azure mobile recover PrimaryService SecondaryService
		info:    Executing command mobile recover
		Warning: this action will use the capacity from the mobile service 'SecondaryService' and delete it. Do you want to recover the mobile service 'PrimaryService'? (y/n): y
		+ Performing recovery
		+ Cleaning up
		info:    Recovery complete
		info:    mobile recover command OK


	<div class="dev-callout"><b>注意</b>
	<p>指令完成後可能需要經過一些時間，才能在入口網站中看到變更。</p>
    </div>

5. 與原始檔控制中的原始版本比較，以確認所有指令碼都已正確回復。在大部分情況下，指令碼會自動回復而不會遺失資料，但如果您發現有不一致之處，則可以手動回復該指令碼。

6. 請確定回復後的服務開始與 Azure SQL Database 進行通訊。回復命令會回復行動服務，但會保留原始資料庫的連線。如果主要 Azure 區域中的問題也影響到資料庫，則已回復的服務可能仍然無法正確執行。您可以利用 Azure 服務儀表板來檢查特定區域的資料庫狀態。如果原始資料庫未執行時，您可以回復它：
	+ 依照 [Azure SQL Database 業務持續性]中的說明，將 Azure SQL Database 回復到您剛回復行動服務的 Azure 區域。
	+ 在 Azure 入口網站中，在行動服務的 [**設定**] 索引標籤上選擇 [變更資料庫]，然後選取剛回復的資料庫。

現在的狀態應該是行動服務已回復到新的 Azure 區域，且現在正使用其原始 URL 接收來自您的市集應用程式的流量。

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[SQL Database 業務持續性]: http://msdn.microsoft.com/zh-tw/library/windowsazure/hh852669.aspx
[Team Foundation Service]: http://tfs.visualstudio.com/

[原始檔控制功能]: http://www.windowsazure.com/zh-tw/develop/mobile/tutorials/store-scripts-in-source-control/
[使用 Azure 命令列工具]: http://www.windowsazure.com/zh-tw/develop/mobile/tutorials/command-line-administration/
[Azure 入口網站]: http://manage.windowsazure.com/
[Azure 服務儀表板]: http://www.windowsazure.com/zh-tw/support/service-dashboard/
[使用命令列工具將行動服務自動化]: http://www.windowsazure.com/zh-tw/develop/mobile/tutorials/command-line-administration/
