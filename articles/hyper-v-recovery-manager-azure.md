<properties 
	pageTitle="教學課程：設定內部部署 VMM 網站和 Azure 之間的保護" 
	description="Azure 站台復原可協調將位於內部部署 VMM 雲端中的 Hyper-V 虛擬機器複寫、容錯移轉及復原至 Azure 的作業。" 
	services="site-recovery" 
	documentationCenter="" 
	authors="raynew" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.workload="backup-recovery" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/18/2015" 
	ms.author="raynew"/>


# 開始使用 Azure Site Recovery：使用 Hyper-V 複寫進行內部部署 VMM 站台至 Azure 保護



<div class="dev-callout"> 

<p>Azure Site Recovery 可在一些部署案例中協調虛擬機器的複寫、容錯移轉及復原，為您的商務與工作負載持續性策略做出貢獻。<p>

<P>本教學課程說明如何使用 Hyper-V 複寫來部署 Azure Site Recovery，以協調內部部署 VMM 站台與 Azure 之間的保護。本教學課程將盡可能使用最快的部署路徑與預設設定。</P>

<UL>
<LI>如果您需要完整部署的詳細資訊，請參閱<a href="http://go.microsoft.com/fwlink/?LinkId=321294">規劃</a>及<a href="http://go.microsoft.com/fwlink/?LinkId=402679">部署</a>指南。</LI>
<LI>如需其他 Azure Site Recovery 部署案例的相關資訊，請參閱 <a href="http://go.microsoft.com/fwlink/?LinkId=518690">Azure Site Recovery 概觀</a>。</LI>
<LI>如果您在進行本教學課程期間發生問題，請檢閱 Wiki 文章 <a href="http://go.microsoft.com/fwlink/?LinkId=389879">Azure Site Recovery：常見錯誤案例和解決方法</a>，或在 <a href="http://go.microsoft.com/fwlink/?LinkId=313628">Azure 復原服務論壇</a>提出您的問題。</LI>
</UL>
</div>


<h2><a id="before"></a>必要條件</h2> 
<div class="dev-callout"> 
<P>開始教學課程之前，請確認一切就緒。</P>

<UL>
<LI><b>Azure 帳戶</b>-您將需要一個 Azure 帳戶。如果您沒有帳戶，請參閱 <a href="http://aka.ms/try-azure">Azure 免費試用</a>。您可以從 <a href="http://go.microsoft.com/fwlink/?LinkId=378268">Azure Site Recovery 管理員定價詳細資料</a>取得訂閱定價資訊。</LI>

<LI><b>Azure 儲存體帳戶</b>-您需要 Azure 儲存體帳戶才能將複寫的資料儲存至 Azure。此帳戶必須啟用異地複寫。應該與 Azure 站台復原服務位於相同的區或，且與相同的訂閱相關聯。若要深入了解設定 Azure 儲存體，請參閱 <a href="http://go.microsoft.com/fwlink/?LinkId=398704">Microsoft Azure 儲存體簡介</a>。</LI><LI><b>VMM 伺服器</b>-在 System Center 2012 R2 上執行的 VMM 伺服器。</LI>
<LI><b>VMM 雲端</b>-VMM 伺服器上至少一個雲端，此雲端應該包含：
	<UL>
	<LI>一或多個 VMM 主機群組</LI>
	<LI>每個主機群組中的一或多個 Hyper-V 主機伺服器或叢集。</LI>
	<li>位於雲端中來源 Hyper-V 伺服器上的一或多部虛擬機器。虛擬機器應該為第 1 代。</li>
		</UL></LI>	
<LI><b>虛擬機器</b>-您將需要符合 Azure 需求的虛擬機器。請參閱《規劃指南》中的<a href="http://go.microsoft.com/fwlink/?LinkId=402602">必要條件和支援</a>。</LI>
<LI>如需虛擬機器容錯移轉至 Azure 的支援需求完整清單，請閱讀  </LI>
</UL>


<h2><a id="tutorial"></a>教學課程步驟</h2> 

確認已達成先決條件之後，請執行下列動作：
<UL>

<LI><a href="#vault">步驟 1：建立保存庫</a>-建立 Azure 站台復原保存庫。</LI>
<LI><a href="#download">步驟 2：在 VMM 伺服器上安裝「提供者」應用程式</a>-在保存庫中產生註冊金鑰，及下載「提供者」安裝檔。您將在 VMM 伺服器上執行安裝程式來安裝提供者，並在保存庫中註冊伺服器。</LI>
<LI><a href="#storage">步驟 3：新增 Azure 儲存體帳戶</a>-如果您沒有帳戶，請建立一個。 </LI>
<LI><a href="#agent">步驟 4：安裝代理程式應用程式</a>-在您想要保護之 VMM 雲端中的每一部 Hyper-V 主機上，安裝「Microsoft Azure 復原服務」代理程式。</LI>
<LI><a href="#clouds">步驟 5：設定雲端保護</a>-為 VMM 雲端進行保護設定。</LI>
<LI><a href="#NetworkMapping">步驟 6：設定網路對應</a>-您可以選擇性地設定網路對應，將來源 VM 網路對應到目標 Azure 網路。</LI>
<LI><a href="#virtualmachines">步驟 7：對虛擬機器啟用保護</a>-對受保護 VMM 雲端中的虛擬機器啟用保護。</LI>
<LI><a href="#test">步驟 8：測試部署</a>-您可以為單一虛擬機器執行測試容錯移轉，或是建立復原方案，然後為該方案執行測試容錯移轉，以確定它可以運作。</LI>
</UL>



<a name="vault"></a> <h2>步驟 1：建立保存庫</h2>

1. 登入[管理入口網站](https://manage.windowsazure.com)。


2. 依序展開 [資料服務]<b></b>、[復原服務]<b></b>，然後按一下 [Site Recovery 保存庫]<b></b>。

3. 按一下 [新建]<b></b>，然後按一下 [快速建立]<b></b>。
	

4. 在 [名稱]<b></b> 中，輸入用於識別保存庫的易記名稱。

5. 在 [地區]<b></b> 中，選取保存庫的地理區域。可用的地理區域包括 [東亞]、[西歐]、[美國西部]、[美國東部]、[北歐] 和 [東南亞]。
6. 按一下 [建立保存庫]<b></b>。 

	![New Vault](./media/hyper-v-recovery-manager-configure-vault/SR_HvVault.png)

<P>檢查狀態列，以確認是否順利建立保存庫。保存庫將會在主要 [復原服務] 頁面上列為 [使用中]<b></b>。</P>





 <a name="download"></a> <h2>步驟 2：產生註冊金鑰並安裝 Azure 站台復原提供者</h2>
 

1. 在 [復原服務]<b></b> 頁面中，按一下保存庫以開啟 [快速入門] 頁面。您也可以使用圖示隨時開啟 [快速入門]。

	![Quick Start Icon](./media/hyper-v-recovery-manager-configure-vault/SR_QuickStartIcon.png)

2. 在下拉式清單中，選取 [在內部部署 Hyper-V 站台與 Microsoft Azure 之間]****。
3. 在 [準備 VMM 伺服器]**** 中，按一下 [產生註冊金鑰]**** 檔案。該金鑰在產生後會維持 5 天有效。將檔案複製到 VMM 伺服器。設定提供者時需要這個檔案。

	![Registration key](./media/hyper-v-recovery-manager-configure-vault/SR_E2ARegisterKey.png)

4. 在 [快速入門]<b></b> 頁面上的 [準備 VMM 伺服器]**** 中，按一下 [下載要在 VMM 伺服器上安裝的 Microsoft Azure Site Recovery 提供者]<b></b>，以取得最新版的提供者安裝檔案。

2. 在來源 VMM 伺服器上執行此檔案。


3. 在 [必要條件檢查]**** 中，選取停止 VMM 服務以開始執行提供者安裝程式。服務隨即停止，並將在安裝程式完成時自動重新啟動。

	![Prerequisites](./media/hyper-v-recovery-manager-configure-vault/SR_ProviderPrereq.png)

4. 在 [Microsoft Update]**** 中，您可以選擇要接收更新。啟用這項設定時，會根據您的 Microsoft Update 原則來安裝提供者更新。

	![Microsoft Updates](./media/hyper-v-recovery-manager-configure-vault/SR_ProviderUpdate.png)

安裝提供者之後，請繼續安裝以在保存庫中註冊伺服器。

5. 在 [網際網路連線]**** 中，指定 VMM 伺服器上執行的提供者連線到網際網路的方式。選取 [使用預設的系統 Proxy 設定]<b></b>，以使用在伺服器上設定的預設網際網路連線設定。

	![Internet Settings](./media/hyper-v-recovery-manager-configure-vault/SR_ProviderProxy.png)

6. 在 [註冊金鑰]**** 中，選取您已從 Azure Site Recovery 下載並複製到 VMM 伺服器的金鑰。
7. 在 [保存庫名稱]**** 中，確認將於其中註冊伺服器的保存庫名稱。
8. 在 [伺服器名稱]**** 中，指定用於識別保存庫中之 VMM 伺服器的易記名稱。

	
	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SR_ProviderRegKeyServerName.png)

8. 在 [初始雲端中繼資料同步]**** 中，選取您是否要將 VMM 伺服器上所有雲端的中繼資料與保存庫同步。這個動作只需要在每個伺服器上進行一次。如果不要同步所有雲端，您可以取消核取這項設定，再於 VMM 主控台的雲端屬性中個別同步每個雲端。


9. 在 [資料加密]**** 中，您可以指定一個位置來儲存自動為資料加密產生的 SSL 憑證。如果您在 Azure 站台復原入口網站中為 Azure 所保護的雲端啟用資料加密，則會使用此憑證。請保護此憑證的安全。當您執行至 Azure 的容錯移轉時，您將選取它來將加密的資料解密。 
如果您是從一個內部部署站台複寫到另一個內部部署站台，則與此選項無關。

	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SR_ProviderSyncEncrypt.png)

8. 按一下 [註冊]<b></b> 以完成此程序。註冊之後，Azure Site Recovery 即可從 VMM 伺服器抓取中繼資料。伺服器會顯示在保存庫中 [伺服器]**** 頁面上 [資源]<b></b> 索引標籤的結尾。

<h2><a id="storage"></a>步驟 3：建立 Azure 儲存體帳戶</h2>
如果您沒有 Azure 儲存體帳戶，請按一下 [新增 Azure 儲存體帳戶]****。此帳戶應啟用異地複寫。此帳戶應與 Azure 站台復原服務位於相同的區或，且與相同的訂閱相關聯。

<P>您可以使用這個教學課程，在內部部署 Azure 部署中設定 Azure Site Recovery 的快速概念證明。該教學課程盡可能使用最快的路徑和預設設定。您將會建立 Azure 站台復原保存庫、在來源 VMM 伺服器中安裝 Azure 站台復原提供者、在位於 VMM 雲端中的 Hyper-V 主機伺服器上安裝 Azure 復原服務代理程式、設定雲端保護設定、啟用虛擬機器的保護功能，以及測試您的部署。</P>

![Storage account](./media/hyper-v-recovery-manager-configure-vault/SR_E2AStorageAgent.png)

<h2><a id="agent"></a>步驟 4：在 Hyper-V 主機上安裝 Azure 復原服務代理程式</h2>

在 VMM 雲端中您要保護的每一個 Hyper-V 主機伺服器上，安裝 Azure 復原服務代理程式。

1. 在 [快速入門] 頁面上，按一下 [下載 Azure Site Recovery 服務代理程式並安裝於主機]<b></b>，取得最新版的代理程式安裝檔案。

	![Install Recovery Services Agent](./media/hyper-v-recovery-manager-configure-vault/SR_E2AInstallHyperVAgent.png)

2. 在 VMM 雲端中您要保護的每一個 Hyper-V 主機伺服器上執行安裝檔案。
3. 在 [檢查先決條件]**** 頁面上，按 [下一步]<b></b>。將自動安裝任何缺少的必要元件。

	![Prerequisites Recovery Services Agent](./media/hyper-v-recovery-manager-configure-vault/SR_AgentPrereqs.png)

4. 在 [安裝設定]**** 頁面上，指定您要安裝代理程式的位置，並選取將在其中安裝備份中繼資料的快取位置。然後按一下 [安裝]<b></b>。


<h2><a id="clouds"></a>步驟 5：設定雲端保護設定</h2>

註冊 VMM 伺服器之後，您就可以設定雲端保護設定。在您安裝提供者時，便啟用了 [將雲端資料與保存庫同步]**** 選項，因此 VMM 伺服器上的所有雲端都會出現在保存庫的 [受保護項目]<b></b> 索引標籤中。

![Published Cloud](./media/hyper-v-recovery-manager-configure-vault/SR_CloudsList.png)


1. 在 [快速入門] 頁面上，按一下 [設定 VMM 雲端的保護]****。
2. 在 [受保護項目]**** 索引標籤上，按一下要設定的雲端，然後前往 [設定]**** 索引標籤。
3. 在 [目標]<b></b> 中，選取 [Microsoft Azure]<b></b>。
4. 在 [儲存體帳戶]<b></b> 中，選取要用來儲存 Azure 虛擬機器的 Azure 儲存體。
5. 將 [加密儲存的資料]<b></b> 設為 [關閉]<b></b>。此設定指定應該將內部部署與 Azure 之間複寫的資料加密。
6. 保留 [複製頻率]<b></b> 的預設設定。這個值指定應在來源與目標位置之間同步處理資料的頻率。
7. 保留 [保留復原點的時間]<b></b> 中的預設設定。使用預設值 0 時，只會在複本主機伺服器上儲存主要虛擬機器的最新復原點。
8. 保留 [應用程式一致快照的頻率]<b></b> 中的預設設定。這個值指定建立快照的頻率。快照會使用「磁碟區陰影複製服務」(VSS) 來確保建立快照時，應用程式是處於一致狀態。如果您設定一個值，請確定此值小於您設定的其他復原點數目。
9. 在 [複寫開始時間]<b></b>中，指定初次將資料複寫至 Azure 的開始時間。將會使用 Hyper-V 主機伺服器的時區。建議您將初次複寫排定在離峰時段進行。 

	![Cloud replication settings](./media/hyper-v-recovery-manager-configure-vault/SR_CloudSettingsE2A.png)

儲存設定之後，會建立一個工作，並可在 [工作]<b></b> 索引標籤上進行監視。VMM 來源雲端中的所有 Hyper-V 主機伺服器會設定進行複寫。

儲存之後，可在 [設定]<b></b> 索引標籤上修改雲端設定。若要修改目標位置或目標儲存體，您需要移除雲端組態，然後重新設定雲端。請注意，如果您變更儲存體帳戶，則只會對修改儲存體帳戶之後才啟用保護的虛擬機器套用變更。現有的虛擬機器不會移轉至新的儲存體帳戶。</p>

<h2><a id="networkmapping"></a>步驟 6：設定網路對應</h2>

<p>本教學課程說明在測試環境中部署 Azure Site Recovery 的最簡單路徑。如果您想在進行本教學課程時設定網路對應，請閱讀《規劃指南》中的<a href="http://go.microsoft.com/fwlink/?LinkId=324817">準備網路對應</a>。若要設定對應，請遵循《部署指南》之<a href="http://go.microsoft.com/fwlink/?LinkId=402533">設定網路對應</a>中的步驟進行。</p>




<h2><a id="virtualmachines"></a>步驟 7：為虛擬機器啟用保護</h2>

正確設定伺服器、雲端和網路後，您就可以對雲端中的虛擬機器啟用保護。請注意：

- 虛擬機器必須符合 Azure 需求。請在《規劃指南》的<a href="http://go.microsoft.com/fwlink/?LinkId=402602">必要條件和支援</a>中查看這些需求。
- 若要啟用保護功能，您必須為虛擬機器設定作業系統和作業系統磁碟屬性。當您使用虛擬機器範本在 VMM 中建立虛擬機器時，您可以設定屬性。您也可以在虛擬機器屬性的 [一般]**** 和 [硬體組態]**** 索引標籤上，為現有的虛擬機器設定這些屬性。若未在 VMM 中設定這些屬性，您將可在 Azure 站台復原入口網站中加以設定。

![Create virtual machine](./media/hyper-v-recovery-manager-configure-vault/SR_EnableVMME2ANew.png)

![Modify virtual machine properties](./media/hyper-v-recovery-manager-configure-vault/SR_EnableVMME2AExisting.png)


1. 若要啟用保護，請在虛擬機器所在雲端中的 [虛擬機器]<b></b>索引標籤上，按一下 [啟用保護]<b></b>，然後選取 [新增虛擬機器]<b></b>
2. 從雲端中所有虛擬機器的清單，選取您要保護的虛擬機器。 

	![Enable virtual machine protection](./media/hyper-v-recovery-manager-configure-vault/SR_EnableVMME2ASelectVM.png)

3. 驗證虛擬機器屬性，並視需要加以修改。

	![Verify virtual machines](./media/hyper-v-recovery-manager-configure-vault/SR_EnableVME2AProps.png)

您可以在 [工作]**** 索引標籤中追蹤「啟用保護」動作的進度，包括初始複寫。執行「完成保護」工作之後，虛擬機器即準備好進行容錯移轉。啟用保護並複寫虛擬機器之後，您將能夠在 Azure 中檢視這些虛擬機器。


![Virtual machine protection job](./media/hyper-v-recovery-manager-configure-vault/SR_VMJobs.png)


<h2><a id="test"></a>步驟 8：測試部署</h2>
若要測試部署，您可以對單一虛擬機器執行測試容錯移轉，或者建立包含多部虛擬機器的復原方案，再對這個方案執行測試容錯移轉。測試容錯移轉會在隔離的網路中模擬您的容錯移轉與復原機制。請注意：
<UL>
<li>如果您在容錯移轉之後要使用遠端桌面連接到 Azure 中的虛擬機器，請先在虛擬機器上啟用遠端桌面連線，再執行測試容錯移轉。</li>
<li>容錯移轉之後，您將透過遠端桌面使用公用 IP 位址連接到 Azure 中的虛擬機器。如果想要這樣做，請確定沒有任何網域原則禁止您使用公用位址連接到虛擬機器。</li>
</UL>

1. 在 [復原方案]**** 索引標籤上，新增一個新方案。指定一個名稱、在 [來源類型]**** 中指定 [VMM]****、在 [來源]**** 中指定來源 VMM 伺服器，在 [目標] 中指定 Azure。

	![Create recovery plan](./media/hyper-v-recovery-manager-configure-vault/SRAzure_RP1.png)

2. 在 [確認測試容錯移轉]**** 頁面上，選取 [無]****。請注意，具有此設定的測試容錯移轉將會檢查虛擬機器是否正確地複寫至 Azure，但並不會檢查您的複寫網路組態。如果您想要使用指定的 Azure 網路來執行測試，請參閱 a href="http://go.microsoft.com/fwlink/?LinkId=522292">測試內部部署至 Azure 部署</a>。

	![No network](./media/hyper-v-recovery-manager-configure-vault/SRAzure_TestFailoverNoNetwork.png)


3. 當容錯移轉到達 [完成測試]**** 階段時，請按一下 [完成測試]**** 以完成測試容錯移轉。您可以向下切入至 [工作]**** 索引標籤，以追蹤容錯移轉進度和狀態，並執行任何所需的動作。
4. 在容錯移轉完成之後，請執行下列動作：
	- 確認虛擬機器成功啟動。
	- 按一下 [記事]****，記錄並儲存關於測試容錯移轉的任何觀察。
	- 按一下 [測試容錯移轉完成]****。清除測試環境，以自動關閉測試虛擬機器的電源及刪除測試 Azure 網路。
5. 在其容錯移轉之後，您將可以在 Azure 入口網站中看到該虛擬機器測試複本。如果您設定從內部部署網路存取虛擬機器，您可以初始化虛擬機器的「遠端桌面」連線。


<h3><a id="runtest"></a>監視活動</h3>
<p>您可以使用 [工作]<b></b> 索引標籤和 [儀表板]<b></b> 來檢視和監視 Azure Site Recovery 保存庫所執行的主要工作，包括設定雲端保護、啟用和停用虛擬機器保護、執行容錯移轉 (計劃性、非計劃性或測試)，以及認可非計劃性容錯移轉。</p>

<p>您可以從 [工作]<b></b> 索引標籤檢視工作、向下切入至工作詳細資料和錯誤、執行工作查詢以抓取符合特定條件的工作、將工作匯出至 Excel，以及重新啟動失敗的工作。</p>

<p>您可以從 [儀表板]<b></b> 下載最新版的提供者和代理程式安裝檔案、取得保存庫的設定資訊、查看由保存庫管理其保護的虛擬機器數目、查看最近的工作、管理保存庫憑證，以及重新同步虛擬機器。</p>

<p>如需與工作和儀表板互動的詳細資訊，請參閱<a href="http://go.microsoft.com/fwlink/?LinkId=398534">作業和監視指南</a>。</p>

<h2><a id="next"></a>後續步驟</h2>
<UL>
<LI>若要在完全生產環境中規劃及部署 Azure Site Recovery，請參閱 <a href="http://go.microsoft.com/fwlink/?LinkId=321294">Azure Site Recovery 的規劃指南</a>及 <a href="http://go.microsoft.com/fwlink/?LinkId=321295">Azure Site Recovery 的部署指南</a>。</LI>


<LI>若有任何問題，請造訪 <a href="http://go.microsoft.com/fwlink/?LinkId=313628">Azure 復原服務論壇</a>。</LI> 
</UL>

<!--HONumber=35.2-->

<!--HONumber=46--> 
