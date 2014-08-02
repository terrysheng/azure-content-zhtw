<properties linkid="manage-services-biztalk-state-chart" urlDisplayName="BizTalk Services: Service state chart" pageTitle="BizTalk Services: Service state chart | Azure" metaKeywords="" description="" metaCanonical="" services="biztalk-services" documentationCenter="" title="BizTalk Services: Service state chart" authors="mandia" solutions="" manager="paulettm" editor="cgronlun" />

BizTalk 服務：服務狀態圖
========================

視 BizTalk 服務的目前狀態而定，有些作業是可以或無法在 BizTalk 服務上執行。

例如，您在 Azure 管理入口網站中佈建新的 BizTalk 服務。順利完成時，BizTalk 服務會處於「作用中」狀態。在「作用中」狀態下，您可以停止 BizTalk 服務。如果順利停止，則 BizTalk 服務會進入「已停止」狀態。如果無法停止，則 BizTalk 服務會進入「停止失敗」狀態。在「停止失敗」狀態下，您可以重新啟動 BizTalk 服務。如果您嘗試不允許的作業，例如繼續 BizTalk 服務，則會發生下列錯誤：

**不允許此作業**

若要佈建 BizTalk 服務，請參閱＜[BizTalk 服務：使用 Azure 管理入口網站進行佈建](http://go.microsoft.com/fwlink/p/?LinkID=302280) (英文)。

下表列出當 BizTalk 服務處於特定狀態時可執行的作業。核取記號表示可在此狀態下執行作業。空白項目表示無法在此狀態下執行作業。

#### 啟動、停止、重新啟動、暫止、繼續和刪除作業

<table data-morhtml="true" border="1">
<tr data-morhtml="true">
<th data-morhtml="true" colspan="15">作業</th>
</tr>

<tr data-morhtml="true">
<th data-morhtml="true" rowspan="18">BizTalk 服務狀態</th>
</tr>
<tr data-morhtml="true" bgcolor="FAF9F9">
        <th data-morhtml="true"> </th>
<th data-morhtml="true">啟動</th>
<th data-morhtml="true">停止</th>
<th data-morhtml="true">重新啟動</th>
<th data-morhtml="true">暫止</th>
<th data-morhtml="true">繼續</th>
<th data-morhtml="true">刪除</th>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">Active</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">已停用</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">暫止</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">已停止</b></td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">服務更新失敗</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">停用失敗</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">啟用失敗</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">啟動失敗<br data-morhtml="true" /> 停止失敗<br data-morhtml="true" /> 重新啟動失敗</b></td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">暫止失敗<br data-morhtml="true" /> 繼續失敗</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">建立失敗<br data-morhtml="true" /> 還原失敗<br data-morhtml="true" /></b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">設定更新失敗</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">調整失敗</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
</tr>
</table>
<p><br data-morhtml="true" /></p>


#### 調整、更新組態和備份作業

<table data-morhtml="true" border="1">
<tr data-morhtml="true">
<th data-morhtml="true" colspan="15">作業</th>
</tr>

<tr data-morhtml="true">
<th data-morhtml="true" rowspan="18">BizTalk 服務狀態</th>
</tr>
<tr data-morhtml="true" bgcolor="FAF9F9">
        <th data-morhtml="true"> </th>
<th data-morhtml="true">調整</th>
<th data-morhtml="true">更新組態</th>
<th data-morhtml="true">備份</th>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">Active</b></td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">已停用</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">暫止</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">已停止</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">服務更新失敗</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">停用失敗</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">啟用失敗</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">啟動失敗<br data-morhtml="true" /> 停止失敗<br data-morhtml="true" /> 重新啟動失敗</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"> </td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">暫止失敗<br data-morhtml="true" /> 繼續失敗</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">建立失敗<br data-morhtml="true" /> 還原失敗<br data-morhtml="true" /></b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">設定更新失敗</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"> </td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">調整失敗</b></td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
</tr>
</table>

另請參閱
--------

-   [BizTalk 服務：使用 Azure 管理入口網站進行佈建](http://go.microsoft.com/fwlink/p/?LinkID=302280)
     (英文)
-   [BizTalk 服務：儀表板、監視器和調整索引標籤](http://go.microsoft.com/fwlink/p/?LinkID=302281)
     (英文)
-   [BizTalk 服務：開發人員、基本、標準和高級版本圖表](http://go.microsoft.com/fwlink/p/?LinkID=302279)
     (英文)
-   [BizTalk 服務：備份與還原](http://go.microsoft.com/fwlink/p/?LinkID=329873)
     (英文)
-   [BizTalk 服務：節流](http://go.microsoft.com/fwlink/p/?LinkID=302282)
     (英文)
-   [BizTalk 服務：簽發者名稱和簽發者金鑰](http://go.microsoft.com/fwlink/p/?LinkID=303941)
     (英文)
-   [如何開始使用 Azure BizTalk 服務 SDK](http://go.microsoft.com/fwlink/p/?LinkID=302335)

