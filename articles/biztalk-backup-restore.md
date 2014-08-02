<properties linkid="biztalk-backup-restore" urlDisplayName="BizTalk Services: Backup and Restore" pageTitle="BizTalk Services: Backup and Restore | Azure" metaKeywords="" description="BizTalk Services includes Backup and Restore capabilities. When creating a Backup, a snapshot of the BizTalk Services configuration is taken." metaCanonical="" services="" documentationCenter="" title="BizTalk Services: Backup and Restore" authors="" solutions="" writer="mandia" manager="paulettm" editor="cgronlun" />

BizTalk 服務：備份與還原
========================

Azure BizTalk 服務包含備份與還原功能。建立備份時會產生 Azure BizTalk 服務組態的快照。

請考慮下列情況：

-   Azure 管理入口網站提供兩種方法讓您備份 BizTalk 服務組態：依需要建立備份 (特定備份) 和執行排程備份。

-   備份內容可以還原至相同的 BizTalk 服務，或還原至新的 BizTalk 服務。若要使用相同名稱來還原 BizTalk 服務，必須刪除現有的 BizTalk 服務。否則還原會失敗。

-   BizTalk 服務可以還原至相同版本或更高的版本。從建立備份之後，不支援將 BizTalk 服務還原至較低版本。

    例如，使用基本版本的備份可以還原至高級版本。使用高級版本的備份不能還原至標準版本。

-   EDI 控制編號會備份以維持控制編號的連貫性。如果在上次備份之後處理訊息，則還原此備份內容會產生重複的控制編號。

-   BizTalk 服務開發人員版本中沒有備份與還原功能。

本主題說明如何使用 Azure 管理入口網站來備份和還原 BizTalk 服務。您也可以使用 REST API 來備份 BizTalk 服務。如需詳細資訊，請參閱＜[BizTalk 服務 REST API](http://msdn.microsoft.com/library/windowsazure/dn232347.aspx)＞(英文)。

[備份什麼項目？](#budata)

[建立備份](#createbu)

[還原](#restore)

備份什麼項目？
--------------

建立備份時會備份下列項目：

<table border="1"> 
<TR bgcolor="FAF9F9">
<th> </th>
<TH>備份的項目</TH> 
</TR> 
<TR>
<td colspan="2">
 <strong>Azure BizTalk 服務入口網站</strong></td>
</TR> 
<TR>
<TD>組態和執行階段</TD> 
<TD><bl>
<li>夥伴和設定檔詳細資料</li>
<li>夥伴協議</li>
<li>已部署的自訂組件</li>
<li>已部署的橋接器</li>
<li>憑證</li>
<li>已部署的轉換</li>
<li>管線</li>
<li>BizTalk 服務入口網站中建立和儲存的範本</li>
<li>X12 ST01 和 GS01 對應</li>
<li>控制編號 (EDI)</li>
<li>AS2 訊息 MIC 值</li>
</bl></TD>
</TR> 
 
<TR>
<td colspan="2">
 <strong>Azure BizTalk 服務</strong></td>
</TR> 
<TR>
<TD>SSL 憑證</TD> 
<TD>
<bl>
<li>SSL 憑證資料</li>
<li>SSL 憑證密碼</li>
</bl>
</TD>
</TR> 
<TR>
<TD>BizTalk 服務設定</TD> 
<TD>
<bl>
<li>調整單位計數</li>
<li>版本</li>
<li>產品版本</li>
<li>區域/資料中心</li>
<li>存取控制服務 (ACS) 命名空間和金鑰</li>
<li>追蹤資料庫連接字串</li>
<li>封存儲存體帳戶連接字串</li>
<li>監視儲存體帳戶連接字串</li>
</bl></TD>
</TR> 
<TR>
<td colspan="2">
 <strong>其他項目</strong></td>
</TR> 
<TR>
<TD>追蹤資料庫</TD> 
<TD>佈建 BizTalk 服務時需要輸入追蹤資料庫詳細資料，包括 Azure SQL Database 伺服器和追蹤資料庫名稱。不會自動備份追蹤資料庫。<br/><br/>
<strong>重要事項</strong><br/>
 如果不小心刪除追蹤資料庫，且需要復原資料庫，先前的備份必須存在。如果備份不存在，則無法復原追蹤資料庫及其資料。在此情況下，請以相同的資料庫名稱建立新的追蹤資料庫。建議採用地理複寫。</TD>
</TR> 
</table>

建立備份
--------

您隨時都可以建立備份，完全決由掌控。您可以從 Azure 管理入口網站或使用 BizTalk 服務 REST API 來建立備份。若要使用 BizTalk 服務 REST API 建立備份，請參閱＜[備份 BizTalk 服務](http://go.microsoft.com/fwlink/p/?LinkID=325584)＞(英文)

本節提供如何使用管理入口網站來建立備份的指示。您可以使用管理入口網站來建立特定備份，或以想要的間隔排定備份。

建立備份之前
------------

建立備份之前，請確定您遵守下列注意事項：

1.  執行特定備份之前，對於批次的作用中訊息，請先處理批次訊息。*如果*還原此備份，這將有助於防止訊息遺失。執行備份時絕不會儲存批次中的訊息。若是排程的備份，當備份開始時，您可能無法確定批次中沒有訊息。

    **注意**

    建立備份時，如果批次中有作用中的訊息，則不會備份這些訊息，將會遺失這些訊息。

2.  選擇性：在 BizTalk 服務入口網站中，停止任何管理作業。
3.  使用 REST API 提供的[備份 BizTalk 服務](http://go.microsoft.com/fwlink/p/?LinkID=325584)命令來建立備份給儲存體帳戶。

[特定備份](#backupnow)

[排定備份](#backupschedule)

### 特定備份

1.  從 Azure 管理入口網站，按一下 [BizTalk 服務]，然後按一下您要備份的 BizTalk 服務名稱。
2.  從 BizTalk 服務的 **[儀表板]** 索引標籤，按一下頁面底部的 **[備份]**。
3.  在 **[備份 BizTalk 服務]** 對話方塊中，提供備份名稱。
4.  選取 Blob 儲存體帳戶，然後按一下勾選記號開始備份。

備份完成時，儲存體帳戶下會以您指定的備份名稱建立一個容器。此容器包含 BizTalk 服務備份組態。

### 排定備份

1.  從 Azure 管理入口網站，按一下 [BizTalk 服務]，按一下您要排定自動備份的 BizTalk 服務名稱，然後按一下 **[設定]** 索引標籤。
2.  在 **[備份狀態]** 中，如果您不要排定自動備份，請選取 **[無]**。若要排定自動備份，請按一下 **[自動]**。
3.  在 **[儲存體帳戶]** 中，選取將於其中建立備份的 Azure 儲存體帳戶。
4.  在 **[頻率]** 中，指定第一個備份的開始日期和時間，以及執行備份的間隔 (以天為單位)。
5.  在 **[保留天數]** 中，指定備份的保留時間範圍 (以天為單位)。保留週期必須大於備份頻率。
6.  選取 **[Always keep at least one backup]** 核取方塊，以確保即時超過保留天數，也至少有一個備份可用。
7.  按一下 **[儲存]**。

排程的備份工作執行時會在您指定的儲存體帳戶中建立容器 (以儲存備份資料)。容器名稱的格式為 *BizTalk Service name-date-time*。

如果備份失敗，BizTalk 服務儀表板頁面會顯示備份狀態為 **[失敗]**。

![Last scheduled backup status](./media/biztalk-backup-restore/status-last-backup.png)

您可以按一下連結來移至 [管理服務作業記錄檔] 頁面，以深入了解失敗的情況。如需 BizTalk 服務作業記錄檔的詳細資訊，請參閱＜[BizTalk 服務：使用作業記錄檔進行疑難排解](http://go.microsoft.com/fwlink/?LinkId=391211)＞。

還原
----

您可以從 Azure 管理入口網站或使用 BizTalk 服務 REST API 來還原備份。本節提供如何使用管理入口網站進行還原的指示。若要使用 REST API 進行還原，請參閱＜[從備份還原 BizTalk 服務](http://go.microsoft.com/fwlink/p/?LinkID=325582)＞。

### 還原備份之前

還原備份時，請注意下列事項：

-   還原 BizTalk 服務時可以指定新的追蹤、封存和監視儲存區。

-   若要使用相同的名稱來還原 BizTalk 服務，請在開始還原之前刪除現有的 BizTalk 服務。否則還原會失敗。

-   將還原相同的 EDI Runtime 資料。EDI Runtime 備份中儲存控制編號。還原的控制編號從備份時間開始按順序編排。如果在上次備份之後處理訊息，則還原此備份內容會產生重複的控制編號。

#### 還原備份

1.	從 Azure 管理入口網站，按一下 **[新增]**，指向 **[應用程式服務]**、**[BizTalk 服務]**，然後按一下 **[還原]**。

    ![Restore a backup](./media/biztalk-backup-restore/restore-ui.png)

2.	在 [New BizTalk Service - Restore] 精靈中，在 **[Restore BizTalk Service]** 頁面按一下 **[Backup URL]** 文字方塊中的資料夾圖示，以開啟 **[Browse Cloud Storage]** 對話方塊。對話方塊會列出您的 Azure 儲存體帳戶。

    展開您在建立或排定備份時所指定的儲存體帳戶，然後選取您需要從中還原 BizTalk 服務組態的容器名稱。

    從右窗格中，選取對應至還原來源的 .txt 檔案，然後按一下 **[開啟]**。

3.	在 **[Restore BizTalk Service]** 頁面，提供下列資訊：
4.	提供 BizTalk 服務名稱。依預設會使用已備份的 BizTalk 服務的名稱。
5.	驗證要還原的 BizTalk 服務的網域 URL、版本和區域。
6.	選擇為追蹤資料庫建立新的 SQL 資料庫執行個體，然後按一下向右箭頭。

7.  在 **[指定資料庫設定]** 頁面，驗證 SQL 資料庫的名稱，指定將建立 SQL 資料庫的實體伺服器，以及該伺服器的使用者名稱/密碼。

    如果您要為 SQL 資料庫設定進階的設定，請選取 **[設定進階資料庫設定]** 核取方塊，然後按一下向右箭頭。

    如果不要設定進階的資料庫設定，請按一下向右箭頭，然後跳至以下的步驟 6。

8.  在 **[Advanced database settings]** 頁面，選取您要使用的資料庫版本 (**[Web]** 或 **[商務]**)，指定資料庫大小上限和定序規則。按一下向右箭頭。

9.  在 **[Specify monitoring/archiving settings]** 頁面，建立新的儲存體帳戶或指定現有的儲存體帳戶，以儲存 BizTalk 服務監視資訊。

10.  按一下勾選記號來開始執行還原程序&lt;/b\>。

11.  順利完成還原時，在 Azure 管理入口網站的 BizTalk 伺服器頁面上，新的 BizTalk 服務將以暫止狀態列出。

### 還原備份之後

BizTalk 服務永遠還原成**暫止**狀態。這樣可以在新的環境開始運作之前，讓您在 BizTalk 服務應用程式中依需要在應用程式中進行任何組態變更。以下就是在啟動剛還原的 BizTalk 服務之前，必須注意的幾項考量：

-   如果是使用 Azure BizTalk 服務 SDK 來建立 BizTalk 服務應用程式，您可能需要在這些應用程式中更新 ACS 認證，才能使用還原後的環境。

-   您可以還原 BizTalk 服務來複寫已正常運作的 BizTalk 服務環境。在此情況下，如果您在原始 BizTalk 服務入口網站中所設定的協議使用 FTP 共用做為來源，則您可能需要在剛還原的環境中更新協議，以使用其他來源或 FTP 共用。否則，可能會有兩個不同的協議都嘗試提取相同的訊息。

-   如果您已使用還原作業而產生多個 BizTalk 服務環境，則在使用 Visual Studio 應用程式、PowerShell Cmdlet、REST API 或交易夥伴管理 OM API 時，請確定目標環境正確。

-   在剛還原的 BizTalk 服務環境上，也建議設定自動備份。

符合以上或其他類似的考量之後，請移至 Azure 管理入口網站的 [BizTalk 服務] 頁面，選取剛還原且處於暫止狀態的 BizTalk 服務，然後按一下頁面底部的 **[繼續]** 來啟動服務。

下一步
------

若要在 Azure 管理入口網站中佈建 Azure BizTalk 服務，請移至 [BizTalk 服務：使用 Azure 管理入口網站進行佈建](http://go.microsoft.com/fwlink/p/?LinkID=302280) (英文)。若要開始建立應用程式，請移至 [Azure BizTalk 服務](http://go.microsoft.com/fwlink/p/?LinkID=235197) (英文)。

另請參閱
--------

-   [備份 BizTalk 服務](http://go.microsoft.com/fwlink/p/?LinkID=325584)
-   [從備份還原 BizTalk 服務](http://go.microsoft.com/fwlink/p/?LinkID=325582)
-   [BizTalk 服務：開發人員、基本、標準和高級版本圖表](http://go.microsoft.com/fwlink/p/?LinkID=302279) (英文)
-   [BizTalk 服務：使用 Azure 管理入口網站進行佈建](http://go.microsoft.com/fwlink/p/?LinkID=302280) (英文)
-   [BizTalk 服務：佈建狀態圖](http://go.microsoft.com/fwlink/p/?LinkID=329870) (英文)
-   [BizTalk 服務：儀表板、監視器和調整索引標籤](http://go.microsoft.com/fwlink/p/?LinkID=302281) (英文)
-   [BizTalk 服務：節流](http://go.microsoft.com/fwlink/p/?LinkID=302282) (英文)
-   [BizTalk 服務：簽發者名稱和簽發者金鑰](http://go.microsoft.com/fwlink/p/?LinkID=303941) (英文)
-   [如何開始使用 Azure BizTalk 服務 SDK](http://go.microsoft.com/fwlink/p/?LinkID=302335)

