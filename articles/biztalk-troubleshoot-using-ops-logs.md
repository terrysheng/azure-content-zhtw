<properties linkid="biztalk-troubleshoot-using-ops-logs" urlDisplayName="BizTalk Services: Troubleshoot using operation logs" pageTitle="BizTalk Services: Troubleshoot using ops logs | Azure" metaKeywords="" description="BizTalk Services: Troubleshoot using ops logs" metaCanonical="" services="" documentationCenter="" title="BizTalk Services: Troubleshoot using ops logs" authors="mandia"  solutions="" writer="nitinme" manager="dwrede" editor="cgronlun"  />

<tags ms.service="biztalk-services" ms.workload="integration" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/10/2014" ms.author="mandia"></tags>

# BizTalk 服務：使用作業記錄檔進行疑難排解

作業記錄檔是 Azure 管理入口網站中的可用管理服務功能之一，可讓您對執行於 Azure 服務的作業檢視歷程記錄檔，包括 BizTalk 服務在內。它可讓您檢視與 BizTalk 服務訂閱的管理作業有關的歷程資料，最多可回溯 180 天。

<div class="dev-callout"><b>注意</b>
<p>這項功能只會在服務啟動、備份等期間，針對 BizTalk 服務的管理作業擷取記錄檔。此類作業無論是從 Azure 管理入口網站還是透過 <a href="http://msdn.microsoft.com/zh-TW/library/windowsazure/dn232347.aspx">BizTalk Service REST API</a> 來執行，都會受到追蹤。如需使用管理服務進行追蹤的完整作業清單，請參閱<a href="#bizops">使用 Azure 管理服務進行追蹤的作業</a>。</p>
<p>此功能不會對 BizTalk 服務執行階段的相關活動 (例如橋接器等項目所處理的訊息) 擷取記錄檔。若要檢視這些記錄檔，您必須使用 BizTalk 服務入口網站中的 [追蹤] 檢視。如需詳細資訊，請參閱<a HREF="http://msdn.microsoft.com/library/windowsazure/hh949805.aspx">追蹤訊息</a> (英文)。</p>
</div>

## <a name="viewlogs"></a>檢視 BizTalk 服務作業記錄檔

1.  在 Azure 管理入口網站中按一下 [管理服務]，然後按一下 [作業記錄檔] 索引標籤。
2.  您可以根據不同的參數來篩選記錄檔，例如訂閱、日期範圍、服務類型 (例如 BizTalk 服務)、服務名稱或狀態 (作業的狀態，例如「成功」、「失敗」)
3.  按一下核取記號以檢視篩選清單。下圖顯示 testbiztalkservice 的相關活動。<br/>
    ![檢視作業記錄檔][檢視作業記錄檔]
4.  若要檢視特定作業的詳細資訊，請選取資料列，然後在頁面底部按一下 [詳細資料]。

## <a name="bizops"></a>使用 Azure 管理服務進行追蹤的作業

下表列出可使用 Azure 管理服務進行追蹤的作業。
<table border="1" cellpadding="5">
<tr>
<td>CreateBizTalkService</td> 
<td align="left">建立新 BizTalk 服務的作業</td> 
</tr> 
<tr>
<td>DeleteBizTalkService</td> 
<td align="left">刪除 BizTalk 服務的作業</td>  
</tr> 
<tr>
<td>RestartBizTalkService</td> 
<td align="left">重新啟動 BizTalk 服務的作業</td> 
</tr>
<tr>
<td>StartBizTalkService</td> 
<td align="left">啟動 BizTalk 服務的作業</td> 
</tr>
<tr>
<td>StopBizTalkService</td> 
<td align="left">停止 BizTalk 服務的作業</td> 
</tr>
<tr>
<td>DisableBizTalkService</td> 
<td align="left">停用 BizTalk 服務的作業</td> 
</tr>
<tr>
<td>EnableBizTalkService</td> 
<td align="left">啟用 BizTalk 服務的作業</td> 
</tr>
<tr>
<td>BackupBizTalkService</td> 
<td align="left">備份 BizTalk 服務的作業</td> 
</tr>
<tr>
<td>RestoreBizTalkService</td> 
<td align="left">從指定的備份還原 BizTalk 服務的作業</td> 
</tr>
<tr>
<td>SuspendBizTalkService</td> 
<td align="left">暫停 BizTalk 服務的作業</td> 
</tr>
<tr>
<td>ResumeBizTalkService</td> 
<td align="left">繼續 BizTalk 服務的作業</td> 
</tr>
<tr>
<td>ScaleBizTalkService</td> 
<td align="left">擴大或縮小 BizTalk 服務的作業</td> 
</tr>
<tr>
<td>ConfigUpdateBizTalkService</td> 
<td align="left">更新 BizTalk 服務組態的作業</td> 
</tr>
<tr>
<td>ServiceUpdateBizTalkService</td> 
<td align="left">將 BizTalk 服務升級或降級為不同版本的作業</td> 
</tr>
<tr>
<td>PurgeBackupBizTalkService</td> 
<td align="left">清除超過保留週期的 BizTalk 服務備份的作業</td> 
</tr>
</table>
## 另請參閱

-   [備份 BizTalk 服務][備份 BizTalk 服務]
-   [從備份還原 BizTalk 服務][從備份還原 BizTalk 服務]
-   [BizTalk 服務：開發人員、基本、標準和高級版本圖表][BizTalk 服務：開發人員、基本、標準和高級版本圖表] (英文)
-   [BizTalk 服務：使用 Azure 管理入口網站進行佈建][BizTalk 服務：使用 Azure 管理入口網站進行佈建] (英文)
-   [BizTalk 服務：佈建狀態圖][BizTalk 服務：佈建狀態圖] (英文)
-   [BizTalk 服務：儀表板、監視器和調整索引標籤][BizTalk 服務：儀表板、監視器和調整索引標籤] (英文)
-   [BizTalk 服務：節流][BizTalk 服務：節流] (英文)
-   [BizTalk 服務：簽發者名稱和簽發者金鑰][BizTalk 服務：簽發者名稱和簽發者金鑰] (英文)
-   [如何開始使用 Azure BizTalk 服務 SDK][如何開始使用 Azure BizTalk 服務 SDK]

  [BizTalk Service REST API]: http://msdn.microsoft.com/zh-TW/library/windowsazure/dn232347.aspx
  [使用 Azure 管理服務進行追蹤的作業]: #bizops
  [追蹤訊息]: http://msdn.microsoft.com/library/windowsazure/hh949805.aspx
  [檢視作業記錄檔]: ./media/biztalk-troubleshoot-using-ops-logs/Operation-Logs.png
  [備份 BizTalk 服務]: http://go.microsoft.com/fwlink/p/?LinkID=325584
  [從備份還原 BizTalk 服務]: http://go.microsoft.com/fwlink/p/?LinkID=325582
  [BizTalk 服務：開發人員、基本、標準和高級版本圖表]: http://go.microsoft.com/fwlink/p/?LinkID=302279
  [BizTalk 服務：使用 Azure 管理入口網站進行佈建]: http://go.microsoft.com/fwlink/p/?LinkID=302280
  [BizTalk 服務：佈建狀態圖]: http://go.microsoft.com/fwlink/p/?LinkID=329870
  [BizTalk 服務：儀表板、監視器和調整索引標籤]: http://go.microsoft.com/fwlink/p/?LinkID=302281
  [BizTalk 服務：節流]: http://go.microsoft.com/fwlink/p/?LinkID=302282
  [BizTalk 服務：簽發者名稱和簽發者金鑰]: http://go.microsoft.com/fwlink/p/?LinkID=303941
  [如何開始使用 Azure BizTalk 服務 SDK]: http://go.microsoft.com/fwlink/p/?LinkID=302335
