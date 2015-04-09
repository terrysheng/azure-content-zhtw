<properties 
	pageTitle="教學課程：設定內部部署 Hyper-V 站台和 Azure 之間的保護" 
	description="Azure Site Recovery 可協調內部部署 Hyper-V 站台和 Azure 之間虛擬機器的複寫、容錯移轉及復原作業。" 
	services="site-recovery" 
	documentationCenter="" 
	authors="raynew" 
	manager="jwhit" 
	editor="tysonn"/>

<tags 
	ms.service="site-recovery" 
	ms.workload="backup-recovery" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/18/2015" 
	ms.author="raynew"/>

# 教學課程：設定內部部署 Hyper-V 站台和 Azure 之間的保護


<h2><a id="overview" name="overview" href="#overview"></a>概觀</h2>
<p>Azure Site Recovery 可在一些部署案例中協調虛擬機器的複寫、容錯移轉及復原，為您的商務與工作負載持續性策略做出貢獻。<p>

本教學課程說明如何部署 Azure Site Recovery，以保護在內部部署站台中搭配 Windows Server 2012 R2 之 Hyper-V 伺服器上執行的工作負載。Hyper-V 伺服器上的虛擬機器會使用 Hyper-V 複寫來複寫到 Azure。如果您的辦公室或分公司有 Hyper-V 伺服器，但未部署 System Center VMM，此部署便特別有用。


<LI>本教學課程使用只需最少和預設設定的最快速部署途徑。
您可以在<a href="http://go.microsoft.com/fwlink/?LinkId=522087">規劃</a>及<a href=" http://go.microsoft.com/fwlink/?LinkId=522088">部署</a>指南閱讀完整的部署指示。</LI>
<LI>如需其他 Azure Site Recovery 部署案例的相關資訊，請參閱 <a href="http://go.microsoft.com/fwlink/?LinkId=518690">Azure Site Recovery 概觀</a>。</LI>
<LI>如果您在進行本教學課程期間發生問題，請檢閱 Wiki 文章 <a href="http://go.microsoft.com/fwlink/?LinkId=389879">Azure Site Recovery：常見錯誤案例和解決方法</a>，或在 <a href="http://go.microsoft.com/fwlink/?LinkId=313628">Azure 復原服務論壇</a>提出您的問題。</LI>
</UL>

</div>

<h2><a id="before" name="before" href="#before"></a>必要條件</h2>
<div class="dev-callout"> 
<P>開始之前，請確認您已經做好一切準備工作。</P>

<UL>
<LI><b>Azure 帳戶</b>-您將需要一個 Azure 帳戶。如果您沒有帳戶，請參閱 <a href="http://aka.ms/try-azure">Azure 免費試用</a>。您可以從 <a href="http://go.microsoft.com/fwlink/?LinkId=378268">Azure Site Recovery 管理員定價詳細資料</a> (英文) 取得定價資訊。</LI>
<LI><b>Hyper-V</b>-在來源內部部署站台中，您將至少要有一部執行已安裝 Hyper-V 角色之 Windows Server 2012 R2 的伺服器。Hyper-V 伺服器應該包含一個或多部虛擬機器。Hyper-V 伺服器應該直接或透過 Proxy 連線到網際網路。</LI>
<LI><b>虛擬機器</b>-您想要保護的虛擬機器應該要符合 Azure 對虛擬機器的必要條件。請參閱<a href="http://go.microsoft.com/fwlink/?LinkId=522287">虛擬機器支援</a>。</LI>

</UL>
<h2><a id="tutorial" name="tutorial" href="#tutorial"></a>教學課程步驟</h2>
確認已達成先決條件之後，請執行下列動作：
<UL>
<LI><a href="#vault">步驟 1：建立保存庫</a>-建立 Azure Site Recovery 保存庫。</LI>
<LI><a href="#site">步驟 2：建立 Hyper-V 站台</a>-為所有包含您要保護之虛擬機器的 Hyper-V 伺服器，建立 Hyper-V 站台來做為邏輯容器。</LI>
<LI><a href="#download">步驟 3：準備 Hyper-V 伺服器</a>-產生註冊金鑰並下載提供者安裝程式檔案。您將在站台中的每一部 Hyper-V 伺服器上執行該檔案，並選取該金鑰以在保存庫中註冊伺服器。</LI>
<LI><a href="#resources">步驟 4：準備資源</a>-建立 Azure 儲存體帳戶以儲存複寫的虛擬機器。</LI>
<LI><a href="#protectiongroup">步驟 5：建立和設定保護群組</a>-建立保護群組並對其套用保護設定。保護設定將套用至您新增到群組中的每一部虛擬機器。</LI>
<LI><a href="#enablevirtual">步驟 6：為虛擬機器啟用保護</a>-將虛擬機器新增至保護群組來為其啟用保護。</LI>
<LI><a href="#recovery plans">步驟 7：測試部署</a>-為虛擬機器執行測試容錯移轉。</LI>

</UL>




<a name="vault"></a> <h3>步驟 1：建立保存庫</h3>

1. 登入[管理入口網站](https://manage.windowsazure.com)。


2. 依序展開 [資料服務]<b></b>、[復原服務]<b></b>，然後按一下 [Site Recovery 保存庫]<b></b>。


3. 按一下 [新建]<b></b>，然後按一下 [快速建立]<b></b>。
	
4. 在 [名稱]<b></b> 中，輸入用於識別保存庫的易記名稱。

5. 在 [地區]<b></b> 中，選取保存庫的地理區域。若要查看支援的地區，請參閱 <a href="http://go.microsoft.com/fwlink/?LinkId=389880">Azure Site Recovery 定價詳細資料</a> (英文) 中的＜各地區上市情況＞。

6. 按一下 [建立保存庫]<b></b>。 

	![New Vault](./media/hyper-v-recovery-manager-configure-vault/SR_HvVault.png)

<P>檢查狀態列，以確認是否順利建立保存庫。保存庫將會在主要 [復原服務] 頁面上列為 [使用中]<b></b>。</P>

<a name="site"></a> <h3>步驟 2：建立 Hyper-V 站台</h3>

1. 在 [復原服務] 頁面中，按一下保存庫以開啟 [快速入門] 頁面。您也可以使用圖示隨時開啟 [快速入門]。

	![Hyper-V site scenario](./media/hyper-v-recovery-manager-configure-vault/SR_QuickStartIcon.png)

2. 在下拉式清單中，選取 [在內部部署 Hyper-V 站台與 Azure 之間]。

	![Hyper-V site scenario](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_SelectScenario.png)

3. 在 [建立 Hyper-V 站台] 中，按一下 [建立 Hyper-V 站台]。指定站台名稱並儲存。

	![Hyper-V site](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_CreateSite2.png)

<a name="download"></a> <h3>步驟 3：準備 Hyper-V 伺服器</h3>
	

1. 在 [準備 Hyper-V 伺服器] 中，按一下 [下載註冊金鑰] 檔案。
2. 在 [下載註冊金鑰] 頁面上，按一下站台旁邊的 [下載]。將金鑰下載到 Hyper-V 伺服器容易存取的安全位置。該金鑰在產生後會維持 5 天有效。

	![Registration key](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_DownloadKey2.png)

4. 按一下 [下載提供者]<b></b> 來取得最新版本。
5. 在您想要於保存庫中註冊的每一部 Hyper-V 伺服器上執行該檔案。該檔案會安裝兩個元件：
	- **Azure Site Recovery 提供者**-處理 Hyper-V 伺服器與 Azure Site Recovery 入口網站之間的通訊和協調流程。 
	- **Azure 復原服務代理程式**-處理在 Hyper-V 伺服器上執行之虛擬機器與 Azure 儲存體之間的資料傳輸。 
6. 在 [Microsoft Update] 中，您可以選擇要接收更新。啟用這項設定時，會根據您的 Microsoft Update 原則來安裝提供者和代理程式更新。

	![Microsoft Updates](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_Provider1.png)

7. 在 [安裝] 中，指定您要將提供者和代理程式安裝到 Hyper-V 伺服器上的哪個位置。

	![Install location](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_Provider2.png)

8. 安裝完成之後，請繼續設定以在保存庫中註冊伺服器。

	![Installation complete](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_Provider3.png)


9. 在 [網際網路連線] 頁面上，您將指定提供者連線到 Azure Site Recovery 的方式。選取 [使用預設的系統 Proxy 設定]<b></b>，以使用在伺服器上設定的預設網際網路連線設定。 

	![Internet Settings](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_Provider4.png)

9. 在 [保存庫設定] 頁面上，按一下 [瀏覽] 以選取金鑰檔案。指定 Azure Site Recovery 訂用帳戶、保存庫名稱，以及 Hyper-V 伺服器所屬的 Hyper-V 站台。

	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_SelectKey.png)


11. 註冊作業會開始在保存庫中註冊伺服器。

	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_Provider6.png)

11. 註冊完成之後，Azure Site Recovery 便會抓取來自 Hyper-V 伺服器的中繼資料，而該伺服器會顯示在保存庫中 [伺服器] 頁面的 [Hyper-V 站台] 索引標籤上 。

	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_Provider7.png)


<h3><a id="resources"></a>步驟 4：準備資源</h3>


1. 在 [快速入門] 頁面上，於 [**準備資源**] 中選取 [**建立儲存體帳戶**]，以建立 Azure 儲存體帳戶 (如果您沒有儲存體帳戶)。此帳戶應啟用異地複寫。此帳戶應與 Azure Site Recovery 保存庫位於相同的區或，且與相同的訂用帳戶關聯。

	![Create storage account](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_CreateResources1.png)



<h3><a id="protectiongroup"></a>步驟 5：建立和設定保護群組</h3>

<p>保護群組會將具有相同保護設定的虛擬機器聚集在一起。您只要將保護設定套用至保護群組，這些設定就會套用至您新增至該群組中的所有虛擬機器。</p>
1. 在 [建立和設定保護群組] 中，按一下 [建立保護群組]。如果有任何必要條件不符，系統就會發出訊息，您可以按一下 [檢視詳細資料] 來取得詳細資訊。

2. 在 [保護群組] 索引標籤上，新增保護群組。請指定名稱、來源 Hyper-V 站台、目標為 **Azure**、您的 Azure Site Recovery 訂用帳戶名稱，以及 Azure 儲存體帳戶。

	![Protection group](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_ProtectionGroupCreate.png)


2. 在 [複寫設定] 中，保留預設設定。

	![Protection group](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_ProtectionGroup2.png)


<h3><a id="enablevirtual"></a>步驟 6：啟用虛擬機器保護</h3>
<p>您將把虛擬機器新增至保護群組來為其啟用保護。</p>

1. 在保護群組的 [機器]<b></b> 索引標籤，按一下 [新增虛擬機器至保護群組以啟用保護]<b></b>。
2. 在 [啟用虛擬機器保護] 頁面上，選取您想要保護的虛擬機器。 

	![Enable virtual machine protection](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_AddVM1.png)

「啟用保護」工作隨即開始。您可以在 [工作] 索引標籤上追蹤進度。執行「完成保護」工作之後，虛擬機器即準備好進行容錯移轉。啟用保護且完成初始複寫之後，您就可以在 Azure 中檢視虛擬機器。

在 Azure Site Recovery 中，您可以在 [**受保護的項目**] > [**保護群組**]>  *protectiongroup_name* > [**虛擬機器**] 中檢視受保護的虛擬機器。 


<h3><a id="recoveryplans"></a>步驟 7：測試部署</h3>

測試您的部署，以在不會影響生產環境的隔離網路環境中，模擬您的容錯移轉和復原機制。若要這樣做，您將需要為受保護的虛擬機器執行測試容錯移轉。

1. [**受保護的項目**] > [**保護群組**] >  *protectiongroup_name* > [**虛擬機器**]，選取您要容錯移轉的虛擬機器，然後按一下 [**測試容錯移轉**]。
2. 在 [確認測試容錯移轉] 頁面上，選取 [無]。 

	![Recovery plan](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_TestFailoverNoNetwork.png)

3. 您可以在 [工作] 索引標籤上追蹤容錯移轉進度和狀態。
4. 當容錯移轉到達 [完成測試] 階段時，請依下列方式完成驗證：
	- 在容錯移轉之後，於 Azure 入口網站中檢視複本虛擬機器。確認虛擬機器成功啟動。
	- 如果您設定從內部部署網路存取虛擬機器，您可以初始化虛擬機器的「遠端桌面」連線。
	- 按一下 [完成測試] 來完成它。
	- 按一下 [記事]，記錄並儲存關於測試容錯移轉的任何觀察。
	- 按一下 [測試容錯移轉完成]。清除測試環境，以自動關閉電源及刪除測試虛擬機器。

<h2><a id="runtest" name="runtest" href="#runtest"></a>監視活動</h2>
<p>您可以使用 [工作]<b></b> 索引標籤和 [儀表板]<b></b> 來檢視和監視 Azure Site Recovery 保存庫所執行的主要工作，包括設定雲端保護、啟用和停用虛擬機器保護、執行容錯移轉 (計劃性、非計劃性或測試)，以及認可非計劃性容錯移轉。</p>

<p>您可以從 [工作]<b></b> 索引標籤檢視工作、向下切入至工作詳細資料和錯誤、執行工作查詢以抓取符合特定條件的工作、將工作匯出至 Excel，以及重新啟動失敗的工作。</p>

<p>您可以從 [儀表板]<b></b> 下載最新版的提供者和代理程式安裝檔案、取得保存庫的設定資訊、查看由保存庫管理其保護的虛擬機器數目、查看最近的工作、管理保存庫憑證，以及重新同步虛擬機器。</p>

<p>如需與工作和儀表板互動的詳細資訊，請參閱<a href="http://go.microsoft.com/fwlink/?LinkId=398534">作業和監視指南</a>。</p>

<h2><a id="next" name="next" href="#next"></a>後續步驟</h2>
<UL>
<LI>若要在完全生產環境中規劃及部署 Azure Site Recovery，請參閱 <a href="http://go.microsoft.com/fwlink/?LinkId=522087">Azure Site Recovery 的規劃指南</a>及 <a href=" http://go.microsoft.com/fwlink/?LinkId=522088">Azure Site Recovery 的部署指南</a>。</LI>
<LI>若有任何問題，請造訪 <a href="http://go.microsoft.com/fwlink/?LinkId=313628">Azure 復原服務論壇</a> (英文)。</LI> 
</UL>

<!--HONumber=49-->