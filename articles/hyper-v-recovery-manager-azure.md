<properties linkid="configure-hyper-v-recovery-vault" urlDisplayName="configure-Azure-Site-Recovery" pageTitle="Configure Azure Site Recovery to protect virtual machines on Hyper-V server located in VMM clouds" metaKeywords="Azure Site Recovery, VMM, clouds, disaster recovery" description="Azure Site Recovery coordinates the replication, failover and recovery of Hyper-V virtual machines located in VMM clouds from one on-premises site to another. Azure Site Recovery can also replicate, failover, and recover Hyper-V virtual machine data between VMM clouds and Microsoft Azure." metaCanonical="" umbracoNaviHide="0" disqusComments="1" title="Getting Started with Azure Site Recovery: On-Premises to Azure Protection" editor="jimbe" manager="cfreeman" authors="" />

<tags ms.service="site-recovery" ms.workload="backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author></tags>

# 開始使用 Azure 站台復原：內部部署至 Azure 保護

<div class="dev-callout"> 

<p>使用 Azure 站台復原來保護在 System Center Virtual Machine Manager (VMM) 雲端中的 Hyper-V 主機上執行的虛擬機器。您可以設定：</p>

<ul>
<li><b>內部部署至 Azure 保護</b> - 將 VMM 雲端中的 Hyper-V 主機伺服器上的內部部署虛擬機器複寫到 Azure。您可以在 Azure 站台復原保存庫中設定和啟用保護設定。虛擬機器資料會從內部部署 Hyper-V 伺服器複寫到 Azure 儲存體。</li>

<li><b>內部部署至內部部署保護</b> - 將 VMM 雲端中的 Hyper-V 主機伺服器上的虛擬機器，從一個內部部署站台複寫到另一個內部部署站台。您可以在 Azure 站台復原保存庫中設定和啟用保護設定。虛擬機器資料會從一個內部部署 Hyper-V 伺服器，複寫到另一個內部部署 Hyper-V 伺服器。Azure 站台復原只是協調整個過程。
若要了解此案例，請參閱<a href="http://go.microsoft.com/fwlink/?LinkId=398765">開始使用 Azure 站台復原：內部部署至內部部署保護</a>。</li>

</ul>
 
<h2><a id="about"></a>關於本教學課程</h2>


<P>本教學課程將協助您部署 Azure 站台復原，以快速獲得概念證明。其中會儘可能採取最快的途徑和預設設定。步驟包括：
<ul>
<li>設定 Azure 站台復原保存庫 - 將憑證上傳至保存庫並在來源 VMM 伺服器上設定，然後產生保存庫金鑰。 </li>
<li>設定來源 VMM 伺服器和 Hyper-V 主機伺服器 - 將 Azure 站台復原提供者安裝在來源 VMM 伺服器，並將 Azure 復原服務代理程式安裝在 Hyper-V 主機伺服器。</li>
<li>設定 VMM 雲端 - 在來源 VMM 伺服器上設定雲端的保護設定。</li>
<li>啟用虛擬機器 - 對虛擬機器啟用保護</li>
<li>執行容錯移轉 - 建立復原計畫並執行測試容錯移轉。</li>
</ul>


如需完整部署的相關資訊，請參閱：</P>

<UL>
<LI><a href="http://go.microsoft.com/fwlink/?LinkId=321294">規劃 Azure 站台復原部署</a> - 描述在開始完整部署之前應該完成的規劃步驟。</LI>
<LI><a href="http://go.microsoft.com/fwlink/?LinkId=402679">部署 Azure 站台復原：內部部署至 Azure 保護</a> - 提供完整部署的逐步指示。</LI>
<LI><a href="http://go.microsoft.com/fwlink/?LinkId=378272">管理及監控 Azure 站台復原</a> - 描述如何執行容錯移轉，以及管理和監控您的部署。</LI>
</UL>
<P>如果在本教學課程中遇到問題，請檢閱 Wiki 文章 <a href="http://go.microsoft.com/fwlink/?LinkId=389879">Azure 站台復原：常見錯誤案例及解決之道</a> (英文)，或在 <a href="http://go.microsoft.com/fwlink/?LinkId=313628">Azure 復原服務論壇</a>提出您的問題。</P>

</div>

## <span id="before"></span></a>開始之前

<div class="dev-callout"> 
<P>開始本教學課程之前，請檢查先決條件。</P>

<h3><a id="HVRMPrereq"></a>Azure 先決條件</h3>

<UL>
<LI><b>Azure 帳戶</b> - 您需要有 Azure 帳戶。如果沒有，請參閱 <a href="http://aka.ms/try-azure">Azure 免費試用</a>。如需定價資訊，請參閱 <a href="http://go.microsoft.com/fwlink/?LinkId=378268">Azure 站台復原管理員定價詳細資料</a> (英文)。</LI>
<LI><b>憑證</b> - 您需要將含有公開金鑰的管理憑證 (.cer) 上傳至保存庫。您需要將此憑證匯出為 .pfx 檔案 (含私人金鑰)，並匯入至您要在保存庫中註冊的每一個 VMM 伺服器。在本教學課程中，您將使用自我簽署憑證。在完整部署中，您可以使用符合規劃指南所述之<a href="http://go.microsoft.com/fwlink/?LinkId=321294">憑證必要條件</a>的有效 SSL 憑證</a>。 </LI>


</LI>

<LI><b>Azure 儲存體帳戶</b> - 您需要 Azure 儲存體帳戶才能將複寫的資料儲存至 Azure。此帳戶必須啟用異地複寫。應該與 Azure 站台復原服務位於相同的區或，且與相同的訂閱相關聯。若要深入了解設定 Azure 儲存體，請參閱 <a href="http://go.microsoft.com/fwlink/?LinkId=398704">Microsoft Azure 儲存體簡介</a>。</LI>
</UL>

<h3><a id="VMMPrereq"></a>VMM 先決條件</h3>

<UL>
<LI><b>VMM 伺服器</b> - 在 System Center 2012 R2 上執行 VMM 伺服器。</LI>
<LI><b>VMM 雲端</b> - VMM 伺服器上至少一個雲端，此雲端應該包含：
    <UL>
<LI>一或多個 VMM 主機群組</LI>
<LI>每個主機群組中的一或多個 Hyper-V 主機伺服器。</LI>
<li>雲端中來源 Hyper-V 伺服器上的一或多個虛擬機器。虛擬機器應該為第 1 代。</li>
        </UL></LI>  
</UL>

<h3><a id="VMPrereq"></a>虛擬機器先決條件</h3>

<UL>
<LI><b>世代</b> - Azure 僅支援第 1 代虛擬機器。</LI>
<LI>如需容錯移轉至 Azure 的虛擬機器支援需求的完整清單，請參閱《規劃指南》中的＜<a href="http://go.microsoft.com/fwlink/?LinkId=402602">必要條件和支援</a>＞。 </LI>  
</UL>

<h2><a id="tutorial"></a>教學課程步驟</h2> 

確認已達成先決條件之後，請執行下列動作：
<UL>
<LI><a href="#createcert">步驟 1：取得並設定憑證</a> - 取得 .cer 憑證、將之匯出為 .pfx 檔，然後將 .pfx 檔匯入到 VMM 伺服器。</LI>
<LI><a href="#vault">步驟 2：建立保存庫</a> - 建立 Azure 站台復原保存庫。</LI>
<LI><a href="#upload">步驟 3：設定保存庫</a> - 將管理憑證上傳至保存庫，並產生保存庫的金鑰。此金鑰用來確保 VMM 伺服器上的提供者只執行 Azure 站台復原所傳送的命令。</LI>
<LI><a href="#download">步驟 4：安裝提供者應用程式</a> - 在 VMM 伺服器上執行 Microsoft Azure 站台復原提供者應用程式。這會安裝提供者並在保存庫中註冊 VMM 伺服器。</LI>
<LI><a href="#agent">步驟 5：安裝代理程式應用程式</a> - 在每一個 Hyper-V 主機上安裝 Microsoft Azure 復原服務代理程式。</LI>
<LI><a href="#clouds">步驟 6：設定雲端保護</a> - 為 VMM 雲端進行保護設定。</LI>
<LI><a href="#NetworkMapping">步驟 7：設定網路對應</a> - 您可以選擇性地設定網路對應，將來源 VM 網路對應到目標 Azure 網路。</LI>
<LI><a href="#virtualmachines">步驟 8：對虛擬機器啟用保護</a> - 對受保護 VMM 雲端中的虛擬機器啟用保護。</LI>
<LI><a href="#recovery plans">步驟 9：設定並執行復原計畫</a> - 建立復原計畫並執行計劃的測試容錯移轉。</LI>
</UL>



<a name="createcert"></a> <h2>步驟 1：取得及設定憑證</h2>

取得及設定憑證，如下所述：
<OL>
<LI><a href="#obtaincert">取得逐步解說所需的自我簽署憑證</a> - 使用 MakeCert 工具取得憑證。</LI>
<LI><a href="#exportcert">將憑證匯出為 .pfx 格式</a> - 在您建立憑證的伺服器上，將 .cer 檔匯出為 .pfx 檔 (含私密金鑰)。 </LI>
<LI><a href="#importcert">將 .pfx 憑證匯入到 VMM 伺服器</a> - 匯出之後，將 .pfx 檔案匯入到您要向保存庫註冊的 VMM 伺服器上，[本機電腦] 存放區的 [個人] 資料夾。</LI>
</OL>


<h3><a id="obtaincert"></a>取得自我簽署憑證 (.cer)</h3>
<P>建立符合所有憑證必要條件的 .cer x.509 憑證：</P>
<ol>
<LI>
在要執行 MakeCert 的電腦上，下載最新版 <a href="http://go.microsoft.com/fwlink/?LinkId=378269">Windows SDK</a>。您不需要安裝整個 SDK。</LI>
<ol>
<LI>在 [指定位置] 頁面上，選取 [Install the Windows Software Development Kit for Windows 8.1 to this computer]<b></b>。</LI>
<LI>在 [選取您要安裝的功能] 頁面上，清除 [Windows 軟體開發套件]<b></b> 以外的所有選項。</LI>
<LI>安裝完成後，確認 makecert.exe 出現在資料夾 C:\ProgramFiles (x86)\Windows Kits\<i>WindowsVersion</i>\bin\x64 中。</LI>
<LI>使用系統管理員權限開啟命令提示字元 (cmd.exe)，瀏覽至 makecert.exe 資料夾。</LI> 
<LI>執行下列命令建立自我簽署憑證。將 CertificateName 取代為要使用的憑證名稱，並在 -e 之後指定憑證的實際到期日：</LI>
<code>
makecert.exe -r -pe -n CN=CertificateName -ss my -sr localmachine -eku 1.3.6.1.5.5.7.3.2 -len 2048 -e 01/01/2016 CertificateName.cer</code>
</ol>
<P>成功狀態表示已建立憑證。它儲存在與 makecert.exe 相同的資料夾中。您可以將它移至更方便存取的位置以便匯出。</P>

<h3><a id="exportcert"></a>將憑證匯出為 .pfx 格式</h3>
<P>完成這個程序的步驟可將 .cer 檔匯出為 .pfx 格式。</P>
<ol>
<li>成功狀態表示已建立憑證。它儲存在與 makecert.exe 相同的資料夾中。您可以將它移至更方便存取的位置以便匯出。</li>
<li>在詳細資料窗格中，按一下要管理的憑證。</li>
<li>在 [動作]<b></b> 功能表上，指向 [所有工作]<b></b>，然後按一下 [匯出]<b></b>。[憑證匯出精靈] 隨即出現。按 [下一步]<b></b>。</li>
<li>在 [匯出私密金鑰]<b></b> 頁面上，按一下 [是]<b></b> 匯出私密金鑰。按 [下一步]<b></b>。請注意，只有在安裝後您要將私密金鑰匯出至其他伺服器時，才需要執行這個動作。</li>
<li>在 [匯出檔案格式] 頁面上，選取 [個人資訊交換 - PKCS #12 (.PFX)]<b></b>。按 [下一步]<b></b>。</li>
<li>在 [密碼]<b></b> 頁面上，輸入並確認私密金鑰的加密密碼。按 [下一步]<b></b>。</li>
<li>按照精靈各頁面上的指示將憑證匯出為 .pfx 格式。</li>
</ol>

<h3><a id="importcert"></a>將 .pfx 憑證匯入至 VMM 伺服器</h3>
<p>匯出之後，將 .pfx 複製到 VMM 伺服器，然後匯入它。請注意，如果您在 VMM 伺服器執行 MakeCert.exe，就不需要在該伺服器匯入憑證。</p>
 
<ol>
<li>將私密金鑰 (.pfx) 憑證檔案複製到本機伺服器上的位置。</li>
<li>在 [憑證] MMC 嵌入式管理單元中，選取 [電腦帳戶]<b></b>，然後按 [下一步]<b></b>。</li>
<li>選取 [本機電腦]<b></b>，並按一下 [完成]<b></b>。</li>
<li>在 MMC 中，展開 [憑證]<b></b>，以滑鼠右鍵按一下 [個人]<b></b>，指向 [所有工作]<b></b>，然後按一下 [匯入]<b></b> 啟動憑證匯入精靈。</li>
<li>在 [匯入檔案] 頁面上，按一下 [瀏覽]<b></b>，找出內有您要匯入之憑證的 .pfx 憑證檔案所在的資料夾。選取適當的檔案，然後按一下 [開啟舊檔]<b></b>。</li>
<li>在 [密碼] 頁面上，在 [密碼]<b></b> 方塊中輸入私密金鑰 .pfx 檔案的密碼，然後按 [下一步]<b></b>。</li>
<li>在 [憑證存放區] 頁面，選取 [將所有憑證放入以下的存放區]<b></b>，按一下 [瀏覽]<b></b>，選取 [個人]<b></b> 存放區，按一下 [確定]<b></b>，然後按 [下一步]<b></b>。完成精靈</li>
</ol>

完成這些步驟後，您將能夠在設定保存庫時選擇要上傳的 .cer 憑證，並且在安裝提供者期間註冊 VMM 伺服器時選取 .pfx 憑證。
</div>

<a name="vault"></a>

## 步驟 2：建立保存庫

</p>
1.  登入[管理入口網站][管理入口網站]。

2.  展開 [資料服務]，展開 [復原服務]，然後按一下 [Site Recovery Vault]。

3.  按一下 [建立新的保存庫]，然後按一下 [快速建立]。

4.  在 [名稱] 中，輸入保存庫的易記識別名稱。

5.  在 [地區] 中，選取保存庫的地理區域。可用的地理區域包括 [東亞]、[西歐]、[美國西部]、[美國東部]、[北歐] 和 [東南亞]。
6.  按一下 [建立保存庫]。

    ![新增保存庫][新增保存庫]

檢查狀態列來確定已成功建立保存庫。保存庫在主要復原服務頁面上會列為 [作用中] 。

<a name="upload"></a>

## 步驟 3：設定保存庫


1.  在 [復原服務] 頁面中，按一下保存庫開啟 [快速入門] 頁面。隨時都可以利用此圖示來開啟快速入門。

     ![快速啟動圖示][快速啟動圖示]

2.  在 [Setup Recovery] 下拉式清單中，選取 [Between an on-premises site and Microsoft Azure]。
3.  若要將憑證 (.cer) 上傳至保存庫，請按一下 [管理憑證] 。

    ![快速啟動][快速啟動]

4.  在 [管理憑證] 對話方塊中，按一下 [瀏覽檔案] 並選取 .cer 檔案。

    ![管理憑證][管理憑證]

5.  若要產生保存庫的金鑰，請按一下 [Get the vault key]。自動會產生金鑰。如果重新產生金鑰，則會覆寫前一個金鑰。請注意，稍後在 VMM 伺服器上安裝 Azure 站台復原提供者時需要此金鑰。

    ![管理憑證][1]

<a name="download"></a>

## 步驟 4：安裝 Azure 站台復原提供者

1. 在 [快速入門] 頁面上，按一下 [Download Provider]，取得最新版的提供者安裝檔案。

    ![Download Provider File][Download Provider File]

2. 在來源 VMM 伺服器上執行此檔案。

    ![下載代理程式][下載代理程式]

3. 安裝提供者之後，繼續執行安裝程式，在保存庫中註冊伺服器。
    ![Setup Complete][Setup Complete]
4. 在 [網際網路連線] 頁面上，指定 VMM 伺服器上執行的提供者連線到網際網路的方式。按 [下一步]，使用伺服器上設定的預設網際網路連線設定。
    ![Internet Settings][Internet Settings]
5. 在 [Vault Registration] 頁面上，執行下列動作：

    -   選取您已匯入到 VMM 伺服器的私密金鑰 (.pfx)。
    -   選取您要在其中註冊伺服器的保存庫。
    -   指定保存庫金鑰。這是您先前產生的保存庫金鑰。從 [快速入門] 頁面剪貼金鑰值。

    ![憑證註冊][憑證註冊]

6. 在 [資料加密] 頁面上，指定在特定雲端的複寫期間是否允許資料加密的選項。如果選取此選項，將會自動產生 SSL 憑證。執行容錯移轉時，您需要選取此憑證。啟用此設定之後，您可以在 Azure 站台復原入口網站中對雲端啟用或停用資料加密。在本教學課程中，請保留預設設定，然後按 [下一步]。

    ![憑證保存庫][憑證保存庫]

7. 在 [VMM 伺服器] 頁面上，執行下列動作：

    -   指定 VMM 伺服器的易記名稱。這個名稱將在 Azure 站台復原主控台中用來識別該伺服器。
    -   選取 [Synchronize cloud metadata with the vault]，同步處理 VMM 雲端的相關資訊與 Azure 站台復原保存庫。這個動作只需要在每個伺服器上進行一次。如果不要同步處理所有雲端，您可以個別發佈每個雲端來同步處理該雲端，然後您就可以設定雲端保護設定。

8. 按一下 [註冊] 完成此程序。

    ![PublishCloud][PublishCloud]

在成功註冊伺服器之後，該伺服器的易記名稱將顯示在保存庫中 [伺服器] 頁面的 [資源]索引標籤上。

## <span id="storage"></span></a>步驟 5：安裝 Azure 復原服務代理程式

在 VMM 雲端中您要保護的每一個 Hyper-V 主機伺服器上，安裝 Azure 復原服務代理程式。

1.  在 [快速入門] 頁面上，按一下 [Download Azure Site Recovery Services Agent and install on hosts] ，取得最新版的代理程式安裝檔案。

    ![Install Recovery Services Agent][Install Recovery Services Agent]

2.  在 VMM 雲端中您要保護的每一個 Hyper-V 主機伺服器上執行安裝檔案。
3.  在 [檢查先決條件] 頁面上，按 [下一步]。將自動安裝任何缺少的必要元件。

![Prerequisites Recovery Services Agent][Prerequisites Recovery Services Agent]

1.  在 [安裝設定] 頁面上，指定您要安裝代理程式的位置，並選取將在其中安裝備份中繼資料的快取位置。然後按一下 [安裝]。

在 [網際網路連線] 頁面上，指定 VMM 伺服器上執行的提供者連線到網際網路的方式。按 [下一步]，使用伺服器上設定的預設網際網路連線設定![Recovery Services Agent location][Recovery Services Agent location]

## <span id="clouds"></span></a>步驟 6：設定雲端保護設定

註冊 VMM 伺服器之後，您就可以設定雲端保護設定。您已在安裝提供者時啟用 [Synchronize cloud data with the vault] 選項，所以 VMM 伺服器上的所有雲端會出現在保存庫的 [受保護項目] 索引標籤中。

![發佈的雲端][發佈的雲端]

如下所示進行保護設定：

1.  在 [快速入門] 頁面上，按一下 [Set up protection for VMM clouds]。

![Enable cloud protection][Enable cloud protection]

1.  在 [受保護項目] 索引標籤上，按一下要設定的雲端，並移至 [設定] 索引標籤。
2.  在 [目標] 中，選取 [Microsoft Azure] 。
3.  在 [儲存體帳戶] 中，選取要用來儲存 Azure 虛擬機器的 Azure 儲存體。
4.  將 [Encrypt stored data] 設為 [關閉]。此設定指定應該將內部部署與 Azure 之間複寫的資料加密。
5.  在 [Copy frequency] 中保留預設設定。這個值指定應在來源與目標位置之間同步處理資料的頻率。
6.  在 [Retain recovery points for] 中保留預設設定。使用預設值 0 時，只會在複本主機伺服器上儲存主要虛擬機器的最新復原點。
7.  在 [Frequency of application-consistent snapshots]中保留預設設定。這個值指定建立快照的頻率。快照會使用「磁碟區陰影複製服務」(VSS) 來確保建立快照時，應用程式是處於一致狀態。如果您設定一個值，請確定此值小於您設定的其他復原點數目。

    ![雲端設定][雲端設定]

8.  在 [Replication start time] 中，指定初次將資料複寫至 Azure 的開始時間。將會使用 Hyper-V 主機伺服器的時區。建議您將初次複寫排定在離峰時段進行。

    ![Cloud replication settings][Cloud replication settings]

儲存設定之後，將會建立工作，可在 [工作] 索引標籤上監視此工作。將會對 VMM 雲端中的所有 Hyper-V 主機伺服器設定複寫。 儲存之後，可在 [設定] 索引標籤上修改雲端設定。若要修改目標位置或目標儲存體，您需要移除雲端組態，然後重新設定雲端。請注意，如果您變更儲存體帳戶，則只會對修改儲存體帳戶之後才啟用保護的虛擬機器套用變更。現有的虛擬機器不會移轉至新的儲存體帳戶。

## <span id="networkmapping"></span></a>步驟 7：設定網路對應

您可以選擇性地啟用網路對應，將來源 VM 網路對應到目標 Azure 虛擬網路。如果您未建立網路對應，則只有相同復原計畫中容錯移轉的虛擬機器才能在 Azure 中彼此連接。如果您建立網路對應，則在相同網路上容錯移轉的所有虛擬機器可以彼此連接，而不論它們所屬的復原計畫。此外，如果目標 Azure 網路上已設定網路閘道，則虛擬機器可以連接到內部部署虛擬機器。如果您要在本教學課程中設定網路對應，請參閱部署指南中的[設定網路對應][設定網路對應]。

## <span id="virtualmachines"></span></a>步驟 8：對虛擬機器啟用保護

正確設定伺服器、雲端和網路後，您就可以對雲端中的虛擬機器啟用保護。

在對虛擬機器啟用保護之後，請驗證設定，必要的話更新設定。例如，虛擬機器上的客體作業系統必須是 Windows Server 2008 或更新版本，或是 Linux。虛擬機器僅限為第 1 代。如需 Azure 站台復原必要條件的完整清單，請參閱《規劃指南》中的＜[必要條件和支援][必要條件和支援]＞。

在 VMM 主控台中，檢查並更新設定。您可以在虛擬機器屬性的 [一般] 頁面中，修改虛擬機器的作業系統設定。您可以在 [硬體設定] 頁面中更新作業系統磁碟設定。

![Modify virtual machine properties][Modify virtual machine properties]

![Modify virtual machine properties][2]

1.  若要啟用保護，請在虛擬機器所在雲端中的 [虛擬機器] 索引標籤上，按一下 [啟用保護]，然後選取 [Add virtual machines] 。
2.  從雲端中的虛擬機器清單中，選取您要保護的虛擬機器。

![Enable virtual machine protection][Enable virtual machine protection]

啟用保護之後會建立兩個工作。「啟用保護」工作將執行。在初始複寫完成後，接著會執行「完成保護」工作。只有在這些工作成功完成之後，虛擬機器才能進行容錯移轉。您可以在 [工作] 索引標籤上監視進度。

![Virtual machine protection job][Virtual machine protection job]

## <span id="recoveryplans"></span></a>步驟 9：設定並執行復原計畫

復原計劃會將虛擬機器聚集成群組，以便能夠形成單一單位來進行容錯移轉。若要建立復原計畫，請執行下列動作：

1.  在 [Recovery Plans] 索引標籤上，按一下 [建立]。
2.  在 [Specify the Recovery Page Name and Target] 頁面上，選取來源 VMM 伺服器和 Azure 當作目標。

    ![建立復原計畫][建立復原計畫]

3.  在 [選取虛擬機器] 頁面上，選取要新增到復原計畫的虛擬機器。只會顯示已啟用保護的虛擬機器。這些虛擬機器將新增到復原計畫的預設群組 (群組 1)。
4.  按一下打勾記號以建立復原計畫。

    ![復原計畫 VM][復原計畫 VM]

### <span id="run"></span></a>測試容錯移轉

復原計畫可以在主動測試或計劃性容錯移轉期間執行，也可以在非計劃性容錯移轉期間執行。本逐步解說描述如何測試 VMM 至 Azure 的容錯移轉，以確認容錯移轉策略如預期運作。測試容錯移轉會在隔離的網路中模擬您的容錯移轉與復原機制。請注意：

-   如果您在容錯移轉之後要使用遠端桌面連接到 Azure 中的虛擬機器，請先在虛擬機器上啟用遠端桌面連線，再執行測試容錯移轉。
-   容錯移轉之後，您將透過遠端桌面使用公用 IP 位址連接到 Azure 中的虛擬機器。如果想要這樣做，請確定沒有任何網域原則禁止您使用公用位址連接到虛擬機器。

</p>
#### 

<p>
<span id="runtest"></span></a>執行容錯移轉

</h5>
執行復原計畫的測試容錯移轉，如下所述：

</p>
1.  在執行復原計畫之前，您可以驗證計畫中的虛擬機器設定。若要這樣做，請開啟雲端的屬性頁面，按一下虛擬機器。在 [Source and Target Properties for Failover] 頁面上，驗證設定。尤其要驗證 Azure 中對目標虛擬機器建議的大小，以及網路設定都正確。如需虛擬機器先決條件的完整清單，請參閱[必要條件和支援][3]。

![Virtual Machine Properties][Virtual Machine Properties]

1.  在 [Recovery Plans] 索引標籤上，選取所需的復原計畫。
2.  若要啟動容錯移轉，請按一下 [測試容錯移轉] 按鈕。
3.  在 [Confirm Test Failover] 頁面上，選取虛擬機器在容錯移轉之後將連接的 Azure 網路。選擇性地，您可以選取 [沒有網路]。如果選取此設定，則容錯移轉之後，虛擬機器不會連接到網路。

![Test Failover][Test Failover]

您可以在 [工作] 索引標籤追蹤測試容錯移轉工作的進度。在測試容錯移轉完成後，請執行下列動作：

1.  在 Azure 中確認虛擬機器成功啟動。
2.  按一下 [記事] 記錄並儲存關於測試容錯移轉的任何觀察。
3.  除了 [工作] 索引標籤上的詳細資料，當您執行復原計畫的測試容錯移轉時，程序會顯示在復原計劃詳細資料頁面上。您可以檢視容錯移轉步驟和狀態，並檢視或建立測試容錯移轉的記事。

#### 

<p>
<span id="runtest"></span></a>監視活動

</h5>

您可以使用 [工作]索引標籤和 [儀表板]，以檢視和監視 Azure 站台復原保存庫所執行的主要工作，包括設定雲端的保護、啟用和停用虛擬機器的保護、執行容錯移轉 (計劃性、非計劃性或測試)，以及認可非計劃性容錯移轉。

</p>
從 [工作] 索引標籤中，您可以檢視工作、深入了解工作詳細資料和錯誤、執行工作查詢來擷取符合特定條件的工作、將工作匯出到 Excel，以及重新啟動失敗的工作。

從 [儀表板] 中，您可以下載最新版的提供者和代理程式安裝檔案、取得保存庫的組態資訊、查看由保存庫負責保護的虛擬機器數目、查看最近的工作、管理保存庫憑證，以及重新同步處理虛擬機器。

如需有關與工作和儀表板互動的詳細資訊，請參閱[作業和監視指南][作業和監視指南]。

## <span id="next"></span></a>後續步驟

-   若要在完整生產環境中規劃和部署 Azure 站台復原，請參閱 [Azure 站台復原的規劃指南][規劃 Azure 站台復原部署]和 [Azure 站台復原的部署指南][Azure 站台復原的部署指南]。
-   若有任何問題，請造訪 [Azure 復原服務論壇][Azure 復原服務論壇] (英文)。

  [開始使用 Azure 站台復原：內部部署至內部部署保護]: http://go.microsoft.com/fwlink/?LinkId=398765
  [規劃 Azure 站台復原部署]: http://go.microsoft.com/fwlink/?LinkId=321294
  [部署 Azure 站台復原：內部部署至 Azure 保護]: http://go.microsoft.com/fwlink/?LinkId=402679
  [管理及監控 Azure 站台復原]: http://go.microsoft.com/fwlink/?LinkId=378272
  [Azure 站台復原：常見錯誤案例及解決之道]: http://go.microsoft.com/fwlink/?LinkId=389879
  [Azure 復原服務論壇]: http://go.microsoft.com/fwlink/?LinkId=313628
  [Azure 免費試用]: http://aka.ms/try-azure
  [Azure 站台復原管理員定價詳細資料]: http://go.microsoft.com/fwlink/?LinkId=378268
  [Microsoft Azure 儲存體簡介]: http://go.microsoft.com/fwlink/?LinkId=398704
  [必要條件和支援]: http://go.microsoft.com/fwlink/?LinkId=402602
  [步驟 1：取得並設定憑證]: #createcert
  [步驟 2：建立保存庫]: #vault
  [步驟 3：設定保存庫]: #upload
  [步驟 4：安裝提供者應用程式]: #download
  [步驟 5：安裝代理程式應用程式]: #agent
  [步驟 6：設定雲端保護]: #clouds
  [步驟 7：設定網路對應]: #NetworkMapping
  [步驟 8：對虛擬機器啟用保護]: #virtualmachines
  [步驟 9：設定並執行復原計畫]: #recovery%20plans
  [取得逐步解說所需的自我簽署憑證]: #obtaincert
  [將憑證匯出為 .pfx 格式]: #exportcert
  [將 .pfx 憑證匯入到 VMM 伺服器]: #importcert
  [Windows SDK]: http://go.microsoft.com/fwlink/?LinkId=378269
  [管理入口網站]: https://manage.windowsazure.com
  [新增保存庫]: ./media/hyper-v-recovery-manager-configure-vault/SR_HvVault.png
  [快速啟動圖示]: ./media/hyper-v-recovery-manager-configure-vault/SR_QuickStartIcon.png
  [快速啟動]: ./media/hyper-v-recovery-manager-configure-vault/SR_QuickStart.png
  [管理憑證]: ./media/hyper-v-recovery-manager-configure-vault/SR_ManageCert.png
  [1]: ./media/hyper-v-recovery-manager-configure-vault/SR_VaultKey.png
  [Download Provider File]: ./media/hyper-v-recovery-manager-configure-vault/SR_DownloadProviderFile.png
  [下載代理程式]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderInstall1.png
  [Setup Complete]: ./media/hyper-v-recovery-manager-configure-vault/SR_InstallWiz.png
  [Internet Settings]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderProxy.png
  [憑證註冊]: ./media/hyper-v-recovery-manager-configure-vault/SR_CertReg1.png
  [憑證保存庫]: ./media/hyper-v-recovery-manager-configure-vault/SR_CertReg2.png
  [PublishCloud]: ./media/hyper-v-recovery-manager-configure-vault/SR_VMMServerName.png
  [Install Recovery Services Agent]: ./media/hyper-v-recovery-manager-configure-vault/SR_HyperVAgent.png
  [Prerequisites Recovery Services Agent]: ./media/hyper-v-recovery-manager-configure-vault/SR_AgentPrereqs.png
  [Recovery Services Agent location]: ./media/hyper-v-recovery-manager-configure-vault/SR_AgentInstallSettings.png
  [發佈的雲端]: ./media/hyper-v-recovery-manager-configure-vault/SR_Clouds.png
  [Enable cloud protection]: ./media/hyper-v-recovery-manager-configure-vault/SR_EnableCloudProtection.png
  [雲端設定]: ./media/hyper-v-recovery-manager-configure-vault/SR_CloudConfigureE2A1.png
  [Cloud replication settings]: ./media/hyper-v-recovery-manager-configure-vault/SR_PublishCloudSetup2.png
  [設定網路對應]: http://go.microsoft.com/fwlink/?LinkId=402533
  [Modify virtual machine properties]: ./media/hyper-v-recovery-manager-configure-vault/SR_VMMVMPropsGeneral.png
  [2]: ./media/hyper-v-recovery-manager-configure-vault/SR_VMMVMPropsHW.png
  [Enable virtual machine protection]: ./media/hyper-v-recovery-manager-configure-vault/SR_EnableProtectionVM.png
  [Virtual machine protection job]: ./media/hyper-v-recovery-manager-configure-vault/SR_VMJobs.png
  [建立復原計畫]: ./media/hyper-v-recovery-manager-configure-vault/SR_RecoveryPlan1.png
  [復原計畫 VM]: ./media/hyper-v-recovery-manager-configure-vault/SR_RecoveryPlan2.png
  [3]: http://go.microsoft.com/fwlink/?LinkId=402676
  [Virtual Machine Properties]: ./media/hyper-v-recovery-manager-configure-vault/SR_VMPropertiesE2A.png
  [Test Failover]: ./media/hyper-v-recovery-manager-configure-vault/SR_TestFailover.png
  [作業和監視指南]: http://go.microsoft.com/fwlink/?LinkId=398534
  [Azure 站台復原的部署指南]: http://go.microsoft.com/fwlink/?LinkId=321295
