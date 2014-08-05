<properties  linkid="configure-hyper-v-recovery-vault" urlDisplayName="configure-hyper-v-recovery-vault" pageTitle="Configure Hyper-V Recovery Manager to protect virtual machines in VMM clouds" metaKeywords="hyper-v recovery, VMM, clouds, disaster recovery" description="Azure Hyper-V Recovery Manager can help you protect applications and services by coordinating the replication and recovery of virtual machines located in System Center 2012 VMM private clouds." metaCanonical="" umbracoNaviHide="0" disqusComments="1" title="Configure Azure Hyper-V Recovery Manager" editor="jimbe" manager="cfreeman" authors="" />

# 設定 Azure Hyper-V 復原管理員
<div class="dev-callout"> 

<p>Azure Hyper-V 復原管理員會協調及管理私人雲端 (位於 System Center 2012 Service Pack 1 (SP1) 或 System Center 2012 R2 的 Virtual Machine Manager (VMM) 伺服器) 中 Hyper-V 虛擬機器的保護。Hyper-V 復原管理員會協調這些虛擬機器在內部部署 Hyper-V 主機伺服器之間的容錯移轉。Azure 中的 Hyper-V 復原管理員保存庫則用來儲存您的保護組態。</p>

<h2><a  id="about"></a>關於本教學課程</h2>
<p>本教學課程提供 Hyper-V 復原管理員的快速部署逐步解說。如需詳細資訊，請參閱：</p>
<ul>
<li><a  href="http://go.microsoft.com/fwlink/?LinkId=321294">Hyper-V 復原管理員的規劃指南</a>：詳細說明開始進行完整部署前，應先完成的規劃步驟。</li>
<li><a  href="http://go.microsoft.com/fwlink/?LinkId=321295">Hyper-V 復原管理員的部署指南</a>：提供進行完整部署的逐步指示。</li>
<li>如果您在照著本教學課程做時發生問題，請參閱 <a  href="http://go.microsoft.com/fwlink/?LinkId=389879">Hyper-V 復原管理員：常見錯誤案例及解決之道</a> (英文)，或在 <a  href="http://go.microsoft.com/fwlink/?LinkId=313628">Azure 復原服務論壇</a> (英文) 提出您的問題。</li>
</ul>
</div>

 <h2><a  id="before" ></a>開始之前</h2>
<div class="dev-callout"> 
<p>開始本教學課程之前，請確認已達到先決條件。</p>

<h3><a  id="HVRMPrereq"></a>Hyper-V 復原管理員先決條件</h3>

<ul>
<li>**Azure 帳戶**：您需要已啟用 Azure Recovery Services 功能的 Azure 帳戶。如果您沒有這類帳戶或尚未註冊該功能，請參閱 <a  href="http://aka.ms/try-azure">Azure 免費試用</a> (英文和 <a  href="http://go.microsoft.com/fwlink/?LinkId=378268">Hyper-V Recovery Manager 定價詳細資料</a>。</li>
<li>**憑證 (.cer)**：若要在 Hyper-V 復原管理員保存庫中註冊 VMM，您需要將含公用金鑰的管理憑證 (.cer) 上傳到保存庫。請注意：<ul>
	<li>基於教學課程目的，您可以使用自己以 Makecert.exe 工具建立的自我簽署憑證。對於完整部署，您可以使用符合規劃指南中<a  href=" http://go.microsoft.com/fwlink/?LinkId=386485">必要條件和支援</a>所述之憑證先決條件的任何有效 SSL 憑證。</li>
	<li>每個保存庫在任何時候都只有一個相關聯的 .cer 憑證。您可以視需要上傳新的憑證來覆寫現有憑證。</li>
</ul></li>
<li>**憑證 (.pfx 檔)**：.cer 憑證必須匯出為 .pfx 檔 (含私密金鑰)。您可以在內有虛擬機器要保護的每個 VMM 伺服器上匯入這個檔案。然後在部署期間，當您在每個 VMM 伺服器上安裝 Hyper-V 復原管理員提供者代理程式時，您可以選取 .pfx 檔，以便向保存庫註冊 VMM 伺服器。</li>

</ul>

<h3><a  id="VMMPrereq"></a>VMM 先決條件</h3>

<ul>
<li>至少有一個 VMM 伺服器執行於 System Center 2012 SP1 或 System Center 2012 R2 上。</li>
<li>VMM 私人雲端。如果您執行一個 VMM 伺服器，則需要設定兩個雲端。對於兩個以上的 VMM 伺服器，則至少需要有一個雲端在要保護的來源 VMM 伺服器上，並有一個雲端在復原要用的目的地 VMM 伺服器上。要保護的主要雲端必須包含下列項目：<ul>
	<li>一或多個 VMM 主機群組</li>
	<li>每個主機群組中的一或多個 Hyper-V 主機。</li>
		</ul></li>
<li>如果您要虛擬機器在容錯移轉後連線到 VM 網路，您可以在 Hyper-V 復原管理員中設定網路對應。開始部署前，請確認下列事項：<ul>
	<li>來源 VMM 伺服器上之雲端中的虛擬機器已連線到 VM 網路。該 VM 網路應該連結到與雲端相關聯的邏輯網路。</li>
	<li>目的地 VMM 伺服器上的目標雲端有相對應的 VM 網路。該 VM 網路應該連結到與目標雲端相關聯的相對應邏輯網路。</li>
	</ul></li>
	
	<p>若要深入了解網路對應，請閱讀規劃指南中的<a  href="http://go.microsoft.com/fwlink/?LinkId=324817">準備網路對應</a>。</p>
</ul>


<h2><a  id="tutorial"></a>教學課程步驟</h2> 

確認已達成先決條件之後，請執行下列動作：
<ul>
<li><a  href="#createcert">取得並設定憑證</a>：取得 .cer 憑證、將之匯出為 .pfx 檔，然後將 .pfx 檔匯入到 VMM 伺服器。</li>
<li><a  href="#vault">步驟 1：建立保存庫</a>：在 Azure 中建立 Hyper-V 復原管理員保存庫。</li>
<li><a  href="#upload">步驟 2：上傳憑證</a>：將管理憑證上傳到保存庫。</li>
<li><a  href="#download">步驟 3：下載並安裝提供者</a>：在要保護的 VMM 伺服器下載並安裝 Hyper-V 復原管理員提供者。然後向保存庫註冊 VMM 伺服器。</li>
<li><a  href="#clouds">步驟 4：設定雲端保護</a>：為 VMM 雲端進行保護設定。</li>
<li><a  href="#networks">步驟 5：設定網路對應</a>：將來源 VMM 伺服器中的 VM 網路對應到目標 VMM 伺服器。</li>
<li><a  href="#virtualmachines">步驟 6：對虛擬機器啟用保護</a>：對位於 VMM 雲端的 Hyper-V 虛擬機器啟用保護。</li>
<li><a  href="#recovery plans">步驟 7：設定並執行復原計畫</a>：建立並自訂將虛擬機器群組在一起進行容錯移轉的復原計畫。然後執行復原計畫。</li>
<li><a  href="#jobs">步驟 8：監視</a>：使用 **工作** 和 **儀表板** 索引標籤監視設定、狀態和進度。</li>
</ul>



<a name="createcert"></a> <h2>取得及設定憑證</h2>

取得及設定憑證，如下所述：
<ol>
<li><a  href="#obtaincert">取得照著逐步解說做時所需的憑證</a>：使用 MakeCert 工具取得憑證，或使用符合<a  href="http://go.microsoft.com/fwlink/?LinkId=321294">憑證必要條件</a>的現有憑證。</li>
<li><a  href="#exportcert">將憑證匯出為 .pfx 格式</a>：在憑證所在或建立時所在的伺服器上，將 .cer 檔匯出為 .pfx 檔 (含私密金鑰)。 </li>
<li><a  href="#importcert">將 .pfx 憑證匯入至 VMM 伺服器</a>：匯出完 .pfx 檔後，將它匯入到要向保存庫註冊之每個 VMM 伺服器上的個人憑證存放區。</li>
</ol>


<h3><a  id="obtaincert"></a>取得自我簽署憑證 (.cer)</h3>
<p>使用 MakeCert 工具建立符合所有憑證必要條件的 .cer x.509 憑證，如下所述：</p>
<ol>
<li>
在要執行 MakeCert 的電腦上，下載最新版 <a  href="http://go.microsoft.com/fwlink/?LinkId=378269">Windows SDK</a>。請注意，makecert.exe 命令屬於核心 Windows 軟體開發套件 (SDK) 的一部份，因此您不需要下載和安裝整個 SDK。</li>
<li>在 [指定位置] 頁面上，選取 **Install the Windows Software Development Kit for Windows 8.1 to this computer**。</li>
<li>在 [Join the Customer Experience Improvement Program (CEIP)] 頁面上，選取您偏好的設定。</li>
<li>在 [授權合約] 頁面上，按一下 **接受** 以接受條款。</li>
<li>在 [選取您要安裝的功能] 頁面上，清除 **Windows 軟體開發套件** 以外的所有選項。</li>
<li>安裝完成後，確認 makecert.exe 出現在資料夾 C:\ProgramFiles (x86)\Windows Kits\<i>WindowsVersion</i>\bin\x64 中。</li>
<li>使用系統管理員權限開啟命令提示字元 (cmd.exe)，瀏覽至 makecert.exe 資料夾。</li> 
<li>>執行下列命令建立自我簽署憑證。將 CertificateName 取代為要使用的憑證名稱，並在 -e 之後指定憑證的實際到期日：</li>
<code>makecert.exe -r -pe -n CN=CertificateName -ss my -sr localmachine -eku 1.3.6.1.5.5.7.3.2 -len 2048 -e 01/01/2016 CertificateName.cer</code>
</ol>
<p>憑證將建立並儲存於同一個資料夾中。您可以將它移到更方便存取的位置，以便在下一個步驟將它匯出。</p>

<h3><a  id="exportcert"></a>將憑證匯出為 .pfx 格式</h3>
<p>完成這個程序的步驟可將 .cer 檔匯出為 .pfx 格式。</p>
<ol>
<li>從 [開始] 畫面中，輸入 **mmc.exe** 啟動 Microsoft Management Console (MMC)。</li>
<li>在 **檔案** 功能表上，按一下 **新增/移除嵌入式管理單元**。**新增或移除嵌入式管理單元** 對話方塊隨即出現。</li>
<li>在 **可用的嵌入式管理單元** 中，按一下 **憑證**，然後按一下 **新增**。</li>
<li>選取 **電腦帳戶**，然後按 **下一步**。</li>
<li>選取**本機電腦**，然後按一下 **完成**。</li>
<li>在 MMC 的主控台樹狀目錄中，展開 **憑證**，然後展開 **個人**。</li>
<li>在詳細資料窗格中，按一下要管理的憑證。</li>
<li>在 **動作** 功能表上，指向 **所有工作**，然後按一下 **匯出**。[憑證匯出精靈] 隨即出現。按 **下一步**。</li>
<li>在 **匯出私密金鑰** 頁面上，按一下 **是** 匯出私密金鑰。按 **下一步**。請注意，只有在安裝後您要將私密金鑰匯出至其他伺服器時，才需要執行這個動作。</li>
<li>在 [匯出檔案格式] 頁面上，選取 **個人資訊交換 - PKCS #12 (.PFX)**。按 **下一步**。</li>
<li>在 **密碼** 頁面上，輸入並確認私密金鑰的加密密碼。按 **下一步**。</li>
<li>按照精靈各頁面上的指示將憑證匯出為 .pfx 格式。</li>
</ol>


<h3><a  id="importcert"></a>將 .pfx 憑證匯入至 VMM 伺服器</h3>
<p>匯出伺服器後，將它複製到每個要在保存庫中註冊的 VMM 伺服器，然後按照下列方式將它匯入。請注意，如果您在 VMM 伺服器執行 MakeCert.exe，就不需要在該伺服器匯入憑證。</p>
 
<ol>
<li>將私密金鑰 (.pfx) 憑證檔案複製到本機伺服器上的位置。</li>
<li>在 [憑證] MMC 嵌入式管理單元中，選取 **電腦帳戶**，然後按 [下一步]。</li>
<li>選取 [本機電腦]，並按一下 **完成**。在 **新增/移除嵌入式管理單元** 對話方塊中，按一下 **確定**。 </li>
<li>在 MMC 中，展開 **憑證**，以滑鼠右鍵按一下 **個人**，指向 [所有工作]，然後按一下 **匯入** 啟動憑證匯入精靈。</li>
<li>在憑證匯入精靈歡迎畫面上，按 **下一步**。</li>
<li>在 [匯入檔案] 頁面上，按一下 **瀏覽** 找出內有 .pfx 憑證檔案包含要匯入之憑證的資料夾。選取適當的檔案，然後按一下 **開啟**。</li>
<li>在 [密碼] 頁面的 **密碼** 方塊中，輸入在上一個程序中指定給私密金鑰檔案的密碼，然後按 **下一步**。</li>
<li>在 [憑證存放區] 頁面上，選取 **將所有憑證放入以下的存放區**，按一下 **瀏覽**，選取 **個人** 存放區，按一下 **確定**，然後按 [下一步]。</li>
<li>在 [完成憑證匯入精靈] 頁面上，按一下 **完成**。</li>
</ol>

完成這些步驟後，您將能夠選擇要上傳到保存庫的 .cer 憑證，並且在安裝提供者期間註冊 VMM 伺服器時選取 .pfx 憑證。
</div>

 <a name="vault"></a> <h2>步驟
1：建立保存庫</h2>

1.  登入[管理入口網站][1]。

2.  按一下 **資料服務**，然後按一下 **Recovery Services**。
    
    ![預覽程式](./media/hyper-v-recovery-manager-configure-vault/RS_PreviewProgram.png)

3.  按一下 **Recovery Services**，並按一下 **新建**，接著指向 **Hyper-V Recovery Manager Vault**，然後按一下 **快速建立**。
    
    ![新增保存庫](./media/hyper-v-recovery-manager-configure-vault/RS_hvvault.png)

4.  在 **名稱** 中，輸入保存庫的易記識別名稱。

5.  在 **地區** 中，選取保存庫的地理區域。可用的地理區域包括東南亞、**北歐和美國東部。**

6.  在 **訂閱** 中，輸入您的訂閱詳細資料。

7.  按一下 **建立保存庫**。

檢查入口網站底部的通知中顯示的保存庫狀態。將有則訊息確認已成功建立保存庫。該保存庫將在 [Recovery Services] 頁面中以 **線上** 狀態列出。


<a name="upload"></a> <h2>步驟
2：上傳憑證 (.cer)</h2>

1.  在 **Recovery Services** 頁面中，開啟所需的保存庫。
2.  按一下 [快速啟動] 圖示開啟 [快速啟動] 頁面。
    
    ![快速啟動圖示](./media/hyper-v-recovery-manager-configure-vault/RS_QuickStartIcon.png)

3.  按一下 **管理憑證**。
    
    ![快速啟動](./media/hyper-v-recovery-manager-configure-vault/RS_QuickStart.png)

4.  在 **管理憑證** 對話方塊中，按一下 **瀏覽檔案** 找出要上傳到保存庫的 .cer 檔案。
    
    ![管理憑證](./media/hyper-v-recovery-manager-configure-vault/RS_ManageCert.png)

<a name="download"></a> <h2>步驟 3：下載並安裝提供者</h2>在每個要在保存庫中註冊的 VMM 伺服器上安裝 Hyper-V 復原管理員提供者。最新版提供者安裝檔案儲存在 Azure 下載中心。當您在 VMM 伺服器上執行該檔案時，就會安裝提供者，並且向保存庫註冊 VMM 伺服器。

1.  在 **快速啟動** 頁面上，按一下 **Download Provider** 取得提供者的 .exe 安裝檔。在 VMM 伺服器上執行此檔案，以開始提供者安裝。
    
    ![下載代理程式](./media/hyper-v-recovery-manager-configure-vault/RS_installwiz.png)

2.  依照步驟完成提供者安裝。
    
    ![安裝完成](./media/hyper-v-recovery-manager-configure-vault/RS_SetupComplete.png)

3.  提供者安裝完成後，依照精靈步驟向保存庫註冊 VMM 伺服器。
4.  在 [網際網路連線] 頁面上，指定 VMM 伺服器上執行的提供者連線到網際網路的方式。提供者可使用伺服器上預設的網際網路連線設定，或者按一下 **Use a proxy server for Internet requests** 使用自訂設定。
    
    ![網際網路設定](./media/hyper-v-recovery-manager-configure-vault/RS_ProviderProxy.png)
    
    若要在這個逐步解說期間使用自訂設定，請閱讀部署指南的[步驟 2：安裝提供者及註冊 VMM 伺服器][2]中的資訊。

5.  在 [憑證註冊] 頁面上，選取與您上傳到保存庫的 .cer 相對應的 .pfx 檔案。
    
    ![憑證註冊](./media/hyper-v-recovery-manager-configure-vault/RS_CertReg1.png)
    
    ![憑證保存庫](./media/hyper-v-recovery-manager-configure-vault/RS_CertReg2.png)

6.  在 [VMM 伺服器] 頁面上，指定 VMM 伺服器的易記名稱。這個名稱將在 Hyper-V 復原管理員主控台中用來識別該伺服器。
7.  選取 **Synchronize cloud data with the vault** 以與 Hyper-V 復原管理員保存庫同步處理 VMM 伺服器上所有私人雲端的資料。這個動作只需要在每個伺服器上進行一次。如果不要同步處理所有雲端，您可以個別發佈每個雲端來同步處理該雲端，然後您就可以設定雲端保護設定。
8.  按一下 **註冊** 完成此程序。
    
    ![網際網路設定](./media/hyper-v-recovery-manager-configure-vault/RS_PublishCloudSetup.png)

到這個階段，Hyper-V 復原管理員已擷取 VMM 伺服器中的資料，以便協調容錯移轉和復原。在成功註冊伺服器之後，該伺服器的易記名稱將顯示在保存庫中 [伺服器] 頁面的 **資源** 索引標籤上。


<h2><a  id="clouds" ></a>步驟 4：設定雲端保護設定</h2>


註冊 VMM 伺服器之後，您就可以設定雲端保護設定。如果您在 VMM 伺服器上安裝提供者時，並未啟用 **Synchronize cloud data with the vault** 選項，您將需要從 VMM 主控抬將雲端發佈到 Hyper-V 復原管理員。Hyper-V 復原管理員偵測到雲端之後，您就可以設定保護設定。

<h3><a  id="publishclouds" ></a>發佈雲端</h3>


1.  在 VMM 主控台中，開啟 **VM 和服務** 工作區。
2.  在 **VM 和服務** 窗格中，開啟要發佈的雲端。
3.  在雲端內容的 **一般** 頁面上，選取 **Send configuration data about this cloud to the Azure Hyper-V Recovery Manager** 來發佈雲端。發佈雲端後，雲端就會顯示在保存庫中。
    
    ![雲端](./media/hyper-v-recovery-manager-configure-vault/RS_PublishCloud.png)
    
    ![發佈的雲端](./media/hyper-v-recovery-manager-configure-vault/RS_Clouds.png)

<h3><a  id="configureclouds" ></a>設定雲端</h3>


若要設定雲端的保護，請執行下列動作：

1.  在 **快速啟動] 頁面上，按一下 [Configure Protection Settings**。
2.  在 **受保護項目** 索引標籤上，選取要設定的雲端，並移至 **設定** 索引標籤。
    
    ![雲端設定](./media/hyper-v-recovery-manager-configure-vault/RS_CloudConfig.png)

3.  在 **目標位置** 中，指定負責對您要用來復原的雲端進行管理的 VMM 伺服器。
4.  在 **目標雲端** 中，指定要讓來源雲端中的虛擬機器容錯移轉到的目標雲端。
5.  在 **Copy Frequency** 中保留預設設定。這個值指定應在來源與目標位置之間同步處理資料的頻率。這只有在 Hyper-V 主機是執行 Windows Server 2012 R2 時才有重要性。對於其他伺服器，使用預設設定的 5 分鐘即可。
6.  在 **額外的復原點** 中保留預設設定。這個值指定是否要建立其他復原點。使用預設值 0 時，只會在複本主機伺服器上儲存主要虛擬機器的最新復原點。
7.  在 **Application-Consistent Snapshot Frequency** 中保留預設設定。這個值指定建立快照的頻率。快照會使用「磁碟區陰影複製服務」(VSS) 來確保建立快照時，應用程式是處於一致狀態。若要針對本教學課程的逐步解說設定這個值，請務必將它設定為小於您設定的其他復原點數目。
8.  在 **Data Transfer Compression** 中，指定是否應該將傳輸的已複寫資料壓縮。
9.  在 **驗證** 中，指定如何驗證主要與復原 Hyper-V 主機伺服器之間的流量。除非您已設定可用的 Kerberos 環境，否則建議您針對這個逐步解說選取 HTTPS。選取 HTTPS 之後，主機伺服器將使用伺服器憑證相互驗證，而且流量將加密。Hyper-V 復原管理員會自動設定要在 HTTPS 驗證中使用的憑證。不需要進行任何手動設定。請注意，這項設定只有在 Hyper-V 主機伺服器是執行於 Windows Server 2012 R2 時才有重要性。
10. 在 **連接埠** 中保留預設設定。這個值會設定來源與目標 Hyper-V 主機電腦接聽複寫流量的連接埠號碼。
11. 在 **Initial Replication Settings** 中，指定在正常複寫開始前，初次將資料從來源複寫到目標目的地時的處理方式。
    * **Over the network**：透過網路複製資料可能既費時又費資源。如果雲端中的虛擬機器使用相當小的虛擬硬碟，而且主要 VMM 伺服器透過寬頻連線來連接次要 VMM 伺服器，則建議您使用這個選項。您可以指定立即開始複製，或是選取開始複製的時間。如果您使用網路複寫，建議您排在離峰時段進行。
    * **離線**：這個方法指定要使用外部媒體執行初次複寫。如果您想要避免影響網路效能，或者複寫目的地位於很遙遠的地方，就適合使用這個方法。若要使用這個方法，您需指定來源雲端上的匯出位置，以及目標雲端上的匯入位置。對虛擬機器啟用保護時，虛擬硬碟會複製到指定的匯出位置。您需將它送到目標站台，再將它複製到匯入位置。系統會將匯入的資訊複製到複本虛擬機器。如需離線複寫先決條件的完整清單，請參閱部署指南的[步驟 3：設定 VMM 雲端的保護設定][3]。

<h4><a  id="cloudsettingd" ></a>設定保護後的設定</h4>


在您設定雲端後，來源與目標雲端中設定的所有叢集和主機伺服器都會被設定進行複寫。具體而言，會設定的項目如下：

* Hyper-V Replica 使用的防火牆規則，以便開啟複寫流量的連接埠。
* 安裝複寫所需的憑證。
* 設定 Hyper-V 複本設定。

在 **設定** 索引標籤上可修改雲端設定。請注意：

* 建議您，選取的目標雲端要符合所要保護之虛擬機器的復原需求。
* 雲端只能當成單一雲端組中的主要雲端或目標雲端。
* 儲存雲端組態之後，一個工作就會成形，並可在 **工作** 索引標籤上受到監視。儲存組態後，若要修改目標位置或目標雲端，您必須移除雲端組態，然後重新設定雲端。

<h2><a  id="networks" ></a>步驟 5：設定網路對應</h2>


在 **網路** 索引標籤上，您可以設定來源與目標 VMM 伺服器之間的 VM 網路對應。網路對應可確保在容錯移轉之後複本虛擬機器會連線到適當的網路，並確保複本虛擬機器會位在可存取 VM 網路的主機上。確認已達到規劃指南的[必要條件和支援](http://go.microsoft.com/fwlink/?LinkId=386485)所列的 VMM 網路需求。建議您按照下列方式對應網路：

* 將已設定來保護之每個雲端所用的網路進行對應。
* 先執行網路對應，再對虛擬機器啟用保護，這是因為對應是在放置複本虛擬機器期間使用。如果您先啟用保護再設定對應，對應仍可正常運作，但您可能需要移轉一些複本虛擬機器。

若要對應網路，請執行下列動作：

1.  在 **快速啟動] 頁面上，按一下 [Configure Network Mapping**。
2.  在 **來源位置** 中，選取來源 VMM 伺服器。
3.  在 **目標位置** 中，選取目標 VMM 伺服器。如果您正在以單一 VMM 伺服器部署 Hyper-V 復原管理員，則來源和目標將相同。如此會顯示來源 VM 網路與其相關聯目標 VM 網路的清單。對於未對應的網路，將顯示空白值。
4.  在來源和目標清單中選取未對應的項目，然後按一下 **對應**。服務會偵測目標伺服器上的 VM 網路並加以顯示。
    
    ![管理憑證](./media/hyper-v-recovery-manager-configure-vault/RS_networks.png)

5.  在 [Select a Target Network] 頁面上，選取要在目標 VMM 伺服器上使用的 VM 網路。

	![目標網路](./media/hyper-v-recovery-manager-configure-vault/RS_TargetNetwork.png)

6.  按一下來源和目標網路名稱旁邊的資訊圖示，以檢視每個網路的子網路與類型。
    
    當您選取目標網路時，使用來源網路的受保護雲端將顯示，雲端相關聯目標 VM 網路的可用性也將顯示。建議您選取對於所有用於保護之雲端都可用的目標網路。

7.  按一下打勾記號完成對應程序。隨即有個工作啟動來追蹤對應程序。在 **工作** 索引標籤上可以檢視這項工作。
    
    如此一來，與來源 VM 網路連線的虛擬機器，其任何相對應的現有複本虛擬機器都會連線到目標 VM 網路。此外，在與來源 VM 網路連線的虛擬機器上啟用複寫後才建立的新複本虛擬機器，也都會連線到目標 VM 網路。

<h3><a  id="modifynetworks" ></a>修改網路對應</h3>


在 **網路** 索引標籤上，可以修改或移除網路對應。請注意：

*  如果您將選取的對應取消對應，則對應會移除，且複本虛擬機器會與其因為對應而連線到的網路中斷連線。
*  當您修改選取的對應時，變更會受到更新，而複本虛擬機器會連線到新提供的網路設定。


<h2><a  id="virtualmachines" ></a>步驟 6：對虛擬機器啟用保護</h2>


正確設定伺服器、雲端和網路後，您就可以對雲端中的虛擬機器啟用保護。啟用保護的動作是在 VMM 主控台中進行，方法為以滑鼠右鍵按一下每個要保護的虛擬機器，然後選取 **啟用復原**。


啟用保護後，您就可以在雲端的虛擬機器清單中看見該虛擬機器。您可以在 **工作** 索引標籤中檢視啟用保護動作的進度。


![虛擬機器](./media/hyper-v-recovery-manager-configure-vault/RS_Clouds.png)

當您啟用虛擬機器保護時，有三個工作會建立。「啟用保護」工作將執行。在初始複寫完成後，另外兩個「完成保護」工作將執行。只有在這三個工作成功完成後，虛擬機器才算準備好進行容錯移轉。


<h2><a  id="recoveryplans" ></a>步驟 7：設定及執行復原計畫</h2>


復原計畫會將虛擬機器蒐集成群組以當單一單位處理，並會指定群組應該容錯移轉的順序。除了定義虛擬機器的群組，您還可以自訂復原計畫來執行自動指令碼，或等候手動動作的確認。若要建立復原計畫，請執行下列動作：

1.  在 **快速啟動] 頁面上，按一下 [Create Recovery Plan**。
2.  在 [Specify the Recovery Page Name and Target] 頁面上，輸入名稱以及來源與目標 VMM 伺服器。如果您正在以單一 VMM 伺服器部署 Hyper-V 復原管理員，則來源與目標伺服器將相同。來源 VMM 伺服器必須包含已啟用保護的虛擬機器。
    
    ![建立復原計畫](./media/hyper-v-recovery-manager-configure-vault/RS_RecoveryPlan1.png)

3.  在 [選取虛擬機器] 頁面上，選取要新增到復原計畫的虛擬機器。這些虛擬機器將新增到復原計畫的預設群組 (群組 1)。
4.  按一下打勾記號以建立復原計畫。若要刪除已建立的復原計畫，可以在 **Recovery Plans** 索引標籤上刪除。
    
    ![復原計畫
    VM](./media/hyper-v-recovery-manager-configure-vault/RS_RecoveryPlan2.png)

建立復原計畫後，您可以執行下列動作：

* 自訂計畫。您可以將新的群組新增到復原計畫，也可以將虛擬機器新增到群組。您可以以指令碼和手動動作形式，定義在指定的群組之前或之後完成的自訂動作。群組名稱會以數字遞增。在預設復原計畫群組 1 之後，您將建立群組 2、群組 3，依此類推。虛擬機器會按照群組順序進行容錯移轉。如需關於自訂復原計畫的詳細資訊，請參閱部署指南的[步驟 6：建立及自訂復原方案][4]。
* 執行測試容錯移轉。

<h3><a  id="run" ></a>執行復原計畫</h3>


復原計畫可以在主動測試或計劃性容錯移轉期間執行，也可以在非計劃性容錯移轉期間執行。這個逐步解說將說明如何執行測試容錯移轉。如需其他容錯移轉類型的詳細資訊，請閱讀[作業和監視指南](hhttp://go.microsoft.com/fwlink/?LinkId=378272)。

<h3><a  id="protect" ></a>執行計畫。</h3>


在測試環境中執行測試容錯移轉，以確認復原計畫沒問題。進行測試容錯移轉有助於確認您的復原計畫和虛擬機器容錯移轉策略是否會如預期進行。測試容錯移轉會在隔離的網路中模擬您的容錯移轉與復原機制。

<h5><a  id="networkrecommendations" ></a>開始之前</h5>


觸發了測試容錯移轉時，系統會要求您指定在發生容錯移轉後，測試虛擬網路應該如何連線到網路。

*  如果您要使用現有網路，建議您另外建立未正式使用的測試專用邏輯網路。
*  如果您選取自動建立測試 VM 網路的選項，則在測試容錯移轉完成後，暫時性的網路與測試虛擬機器將自動清除。
*  如果您使用 LAN (VLAN) 型邏輯網路，請確定您新增到邏輯網路的網站已與外界隔離。
*  如果您使用 Windows 網路虛擬化型邏輯網路，則 Hyper-V 復原管理員會自動建立隔離的 VM 網路。


<h5><a  id="runtest" ></a>執行測試容錯移轉</h5>


執行復原計畫的測試容錯移轉，如下所述：

1.   在 **Recovery Plans** 索引標籤上，選取所需的復原計畫。
2.   若要啟動容錯移轉，請按一下 **Test Failover** 按鈕。
3.   在 [Confirm Test Failover] 頁面上，指定在發生測試容錯移轉後，虛擬機器應該如何連線到網路，如下所述：
	*  **無**：選取這個設定將指定 VM 網路不應該用於進行這次的測試容錯移轉。如果您要測試個別的虛擬機器，而非您的網路組態，請使用這個選項。它也將提供測試容錯移轉功能運作方式的速覽。在發生容錯移轉後，測試虛擬機器將不會連線到網路。
	*  **使用現有的**：如果您已經建立並隔離 VM 網路來用於容錯移轉，請使用這個選項。在發生容錯移轉後，測試容錯移轉過程中使用的所有測試虛擬機器都將連線到 **VM 網路** 中指定的網路。
	*  **自動建立**：選取這個設定將指定 Hyper-V 復原管理員應該自動根據您在 [邏輯網路] 中指定的設定來建立 VM 網路和其相關網站。如果復原計畫使用多個 VM 網路，請使用這個選項。如果是 Windows 網路虛擬化網路，可以使用這個選項自動建立 VM 網路，並使其與複本虛擬機器之網路中的 VM 網路具有相同的設定 (子網路和 IP 位址集區) 。在測試容錯移轉完成後，這些 VM 網路將自動清除。



在測試容錯移轉完成後，請執行下列動作：


1.   確認虛擬機器成功啟動。
2.   確認虛擬機器成功啟動之後，完成測試容錯移轉以清除隔離的環境。如果您選取自動建立 VM 網路，請清除所有測試虛擬機器和測試網路。
3.   按一下 **記事** 記錄並儲存關於測試容錯移轉的任何觀察。
4.   對復原計畫啟動測試容錯移轉時，復原計畫的詳細資料頁面將顯示容錯移轉程序。您可以看見個別測試容錯移轉步驟及其狀態，並檢視或建立關於測試容錯移轉的記事。此外，只要看看 **工作** 索引標籤上的容錯移轉工作，就能看見容錯移轉工作的詳細資料。詳細資料將顯示容錯移轉的每個相關聯工作、工作狀態、工作開始的時間，以及工作的持續時間。您可以將容錯移轉清單中的工作匯出至 Excel 試算表。


請注意：

* 在 [Confirm Test Failover] 頁面上，會顯示將建立測試虛擬機器的 VMM 伺服器相關的詳細資料。
* 您可以在 **工作** 索引標籤追蹤測試容錯移轉工作的進度。

<h2><a  id="jobsdashboard" ></a>步驟 8：監視</h2>


<h3><a  id="jobsdashboard" ></a>使用 [工作] 索引標籤和儀表板</h3>


<h4><a  id="jobs" ></a>工作</h4>


**工作** 索引標籤會顯示 Hyper-V 復原管理員保存庫執行的主要工作，包括設定雲端的保護、啟用和停用虛擬機器的保護、執行容錯移轉
(計劃性、非計劃性或測試容錯移轉)、認可非計劃性容錯移轉，以及設定反轉複寫。

在 **工作** 索引標籤上，您可以執行下列工作：

* **Run a job query**：您可以執行查詢，以擷取符合指定條件的工作。您可以使用下列參數來篩選工作：

* 找出在特定 VMM 伺服器上執行的工作。
* 找出在雲端、虛擬機器、網路、雲端中的復原計畫或上述所有這些位置執行的工作。
* 找出在特定日期和時間範圍進行的工作。

請注意，查詢最多只能傳回 200 個工作，因此建議您縮小查詢參數範圍，以便傳回低於上限數目的工作。

* **取得工作詳細資料**：您可以按一下 **工作** 清單中的工作，以取得更多詳細資料。詳細資料包括工作名稱、工作狀態、工作開始的時間，以及工作持續的時間長度。在 [工作詳細資料] 索引標籤上，您可以將工作詳細資料匯出至 Excel 試算表，也可以嘗試重新啟動失敗、略過或取消的工作。
* **Export Jobs**：您可以將工作查詢的結果匯出至 Excel 試算表。
* **重新啟動**：您可以將失敗的工作重新啟動，嘗試再次執行。
* **錯誤詳細資料**：面對失敗的工作，您可以按一下 **錯誤詳細資料** 取得該工作的錯誤清單。如此即會顯示每個錯誤的可能原因和建議。您可以將錯誤說明複製到剪貼簿，以供疑難排解之用。

> **工作** 索引標籤會提供下列資訊：

*  **名稱**：工作名稱
*  **項目**：執行工作的雲端、復原計畫、VMM 伺服器或虛擬機器名稱。
*  **狀態**：工作狀態 - 已完成、已取消、失敗或其他。
*  **類型**：工作類型
*  **Started At**：工作開始時間
*  **Task Time**：工作持續時間


![[工作]索引標籤](./media/hyper-v-recovery-manager-configure-vault/RS_Jobs.png)

<h4><a  id="protect" ></a>執行工作查詢</h4>


您可以執行查詢來擷取符合指定條件的工作，包括：

*  在特定 VMM 伺服器上執行的工作。
*  在特定雲端、虛擬機器、網路、復原計畫或上述所有這些位置執行的工作。
*  在特定時間範圍進行的工作。


若要執行工作查詢，請執行下列動作：

1.   開啟 **工作** 索引標籤
2.   在 **伺服器** 中，指定執行工作的 VMM 伺服器
3.   在 **類型** 中，指定在雲端、虛擬機器、網路或雲端中的復原計畫執行的工作。
4.   在 **狀態** 中，指定工作狀態。
5.   在 **持續期間** 中，指定工作發生的時間範圍。


<h4><a  id="dashboard" ></a>儀表板</h4>


**儀表板** 索引標籤會提供貴組織中 Hyper-V 復原管理員使用情形的快速概觀。它提供一個集中的閘道來檢視 Hyper-V 復原管理員保存庫所保護的虛擬機器。[儀表板] 提供下列功能：


在 **儀表板** 索引標籤上，您可以執行下列工作：

* **Download Provider**：下載 Hyper-V 復原管理員提供者到 VMM 伺服器來安裝。
* **管理憑證**：修改保存庫相關聯憑證的設定。
* **刪除**：刪除保存庫。
* **Resynchronize virtual machines**：如果 Hyper-V 復原管理員偵測到有任何主要與復原虛擬機器未如預期同步處理，您可以從 [儀表板] 檢視相關虛擬機器的清單，並選取任何想要重試同步的虛擬機器。

**儀表板** 索引標籤會提供下列資訊：

*  **Quick Glance**：顯示復原服務與 Hyper-V 復原管理員保存庫的重要組態資訊。它將指出保存庫是否在線上、獲派哪個憑證、憑證何時到期，以及服務的訂閱詳細資料。
*  **最近的工作**：顯示過去 24 小時內成功或失敗的工作，以及進行中或等候動作的工作。
*  **問題**：儀表板會顯示關於 VMM 伺服器連線問題，以及虛擬機器複寫之雲端組態設定或同步處理問題的資訊。您可以取得問題的詳細資料、檢視問題的相關聯工作，或重試同步處理虛擬機器。


![儀表板](./media/hyper-v-recovery-manager-configure-vault/RS_Dashboard.png)

<h2><a  id="next" ></a>後續步驟</h2>


*  若要規劃在完全生產環境中部署 Hyper-V 復原管理員，請參閱 [Hyper-V 復原管理員的規劃指南][5] 及 [Hyper-V 復原管理員的部署指南][6]。
*  若有任何問題，請造訪 [Azure 復原服務論壇][7] (英文)。




[1]: https://manage.windowsazure.com
[2]: http://go.microsoft.com/fwlink/?LinkId=378266
[3]: http://go.microsoft.com/fwlink/?LinkId=323469
[4]: http://go.microsoft.com/fwlink/?LinkId=378271
[5]: http://go.microsoft.com/fwlink/?LinkId=321294
[6]: http://go.microsoft.com/fwlink/?LinkId=321295
[7]: http://go.microsoft.com/fwlink/?LinkId=313628