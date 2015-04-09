<properties 
	pageTitle="教學課程：利用 SAN 設定內部部署 VMM 站台之間的保護" 
	description="Azure Site Recovery 可使用 SAN 複寫，以協調內部部署站台之間的 Hyper-V 虛擬機器複寫、容錯移轉及復原作業。" 
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

# 教學課程：利用 SAN 設定內部部署 VMM 站台之間的保護



<h2><a id="overview" name="overview" href="#overview"></a>概觀</h2>
<p>Azure Site Recovery 可在一些部署案例中協調虛擬機器的複寫、容錯移轉及復原，為您的商務與工作負載持續性策略做出貢獻。<p>

<P>本教學課程說明如何使用存放裝置陣列型 (SAN) 複寫來部署 Azure Site Recovery，把針對在某個內部部署站台中受 System Center VMM 管理之 Hyper-V 伺服器上執行的工作負載提供的保護，透過協調及自動化移轉至另一個 VMM 內部部署站台。本教學課程將盡可能使用最快的部署路徑與預設設定。在這個案例中：</P>

<ul>
	<li>您將利用現有的 SAN 基礎結構，來保護部署在 Hyper-V 叢集中的關鍵任務應用程式。</li>
	<li>SAN 複寫將支援客體叢集，並可藉由同步和非同步複寫 (視存放裝置陣列功能而定) 確保不同層應用程式的複寫一致性。</li>
	<li>您將在 VMM 和 Azure Site Recovery 保存庫中設定並啟用複寫。您將探索 VMM 中的 SAN 存放裝置並加以分類、佈建 LUN，以及將存放裝置配置給 Hyper-V 叢集。Azure Site Recovery 會將複寫自動化並協調容錯移轉。 </li>
	</ul>

<UL>
<LI>您可以在<a href="http://go.microsoft.com/fwlink/?LinkId=321294">規劃</a>及<a href="http://go.microsoft.com/fwlink/?LinkId=519251">部署</a>指南中閱讀完整的部署指示。</LI>
<LI>如需其他 Azure Site Recovery 部署案例的相關資訊，請參閱 <a href="http://go.microsoft.com/fwlink/?LinkId=518690">Azure Site Recovery 概觀</a>。</LI>
<LI>如果您在進行本教學課程期間發生問題，請檢閱 Wiki 文章 <a href="http://go.microsoft.com/fwlink/?LinkId=389879">Azure Site Recovery：常見錯誤案例和解決方法</a> (英文)，或在 <a href="http://go.microsoft.com/fwlink/?LinkId=313628">Azure 復原服務論壇</a>提出您的問題。</LI>
</UL>




<h2><a id="before" name="before" href="#before"></a>之前</h2>
<div class="dev-callout"> 
<P>開始進行逐步解說之前，請確認您已經做好一切準備工作。</P>

<UL>
<LI><b>Azure 帳戶</b>-您將需要一個 Azure 帳戶。如果您沒有帳戶，請參閱 <a href="http://aka.ms/try-azure">Azure 免費試用</a>。您可以從 <a href="http://go.microsoft.com/fwlink/?LinkId=378268">Azure Site Recovery 管理員定價詳細資料</a> (英文) 取得定價資訊。</LI>
<LI>如果您想要知道在進行 Azure Site Recovery 作業時會收集、處理或傳輸什麼資訊，請參閱 MSDN 上的<a href="http://go.microsoft.com/fwlink/?LinkId=513016">隱私權規定</a>。</LI>
<LI><b>VMM 伺服器</b>-您將需要在每個內部部署站台上都設置一部 VMM 伺服器，此伺服器必須在 System Center 2012 R2 (含 <a href="http://go.microsoft.com/fwlink/?LinkId=517707">VMM 更新彙總套件 5.0 預覽</a>) 上執行，並部署為實體或虛擬獨立伺服器，或部署為虛擬叢集。</LI>
<LI>您將需要一個部署在主要和次要站台中的 Hyper-V 主機叢集，且其應至少執行含有最新更新的 Windows Server 2012。</LI>
<LI><b>VMM 雲端</b>-在您想要保護的主要 VMM 伺服器上，以及在次要 VMM 伺服器上，應該都至少要有一個雲端。要保護的主要雲端必須包含下列項目：<UL>
	<LI>一或多個 VMM 主機群組</LI>
	<LI>每個主機群組中的一或多個 Hyper-V 叢集。</LI>
	<li>位於雲端中來源 Hyper-V 伺服器上的一或多部虛擬機器。</li>
		</UL>若要深入了解設定 VMM 雲端，請參閱《規劃指南》中的<a href="http://go.microsoft.com/fwlink/?LinkId=513015">規劃 VMM 基礎結構</a>。</LI>
<LI><b>SAN 存放裝置</b>-您可以使用 SAN 複寫，藉由 iSCSI 或光纖通道存放裝置，或藉由使用共用虛擬硬碟 (vhdx)，複寫客體叢集化虛擬機器。SAN 的必要條件如下：</LI>
	<UL>
	<LI>您將需要設定兩個 SAN 陣列，一個在主要站台中，一個在次要站台中。</LI>
	<LI>應該在陣列之間設定網路基礎結構。應該設定對等互連和複寫。應該根據存放裝置陣列需求設定複寫授權。</LI>
	<LI>應該在 Hyper-V 主機伺服器與存放裝置陣列之間設定網路功能，讓主機可以使用 ISCSI 或光纖通道與存放裝置 LUN 進行通訊。</LI>
	<LI>在 <a href="http://go.microsoft.com/fwlink/?LinkId=518669">使用 VMM 和 SAN 部署 Azure Site Recovery - 支援的存放裝置陣列</a>中，檢視支援的存放裝置陣列清單。</LI>
	
	<LI>應該安裝 EMC 和 NetApp 所提供的 SMI-S 提供者，並且應該由此提供者管理 SAN 陣列。請根據它們的文件說明，設定此提供者。</LI>
	<LI>確定陣列的 SMI-S 提供者所在的伺服器，是 VMM 伺服器可透過網路以 IP 位址或 FQDN 存取的伺服器。</LI>
	<LI>每個 SAN 陣列都應該要有一或多個存放集區可供在此部署中使用。</LI>
	<LI>主要站台上的 VMM 伺服器將需要管理主要陣列，而次要 VMM 伺服器將管理次要陣列。</LI>
	</UL>
<LI><b>網路</b>-您可以視需要設定網路對應，以確保在容錯移轉後，會以最佳方式將複本虛擬機器置於 Hyper-V 主機伺服器上，並且這些虛擬機器可以連線到適當的 VM 網路。已啟用網路對應時，位於主要位置的虛擬機器將會連線至網路，且其位於目標位置的複本將會連線到其對應的網路。如果您沒有設定網路對應，虛擬機器將不會在容錯移轉後連線到 VM 網路。本教學課程所述為最簡單的逐步設定，因此不包括網路對應，但您可以從下列參考資料取得詳細資訊：</LI>
	<UL>
	<LI>《規劃指南》中的<a href="http://go.microsoft.com/fwlink/?LinkId=522289">網路對應</a>。</LI>
	<LI>SAN 部署指南中的<a href="http://go.microsoft.com/fwlink/?LinkId=522290">啟用網路對應</a>。</LI>
	</UL>
</UL>
</div>

<h2><a id="tutorial" name="tutorial" href="#tutorial"></a>教學課程步驟</h2>
確認已達成先決條件之後，請執行下列動作：

1. <a href="#VMM">步驟 1：準備 VMM 基礎結構</a>
	- 將 VMM 中的 SAN 存放裝置整合並分類。
	- 佈建邏輯單元 (LUN) 和存放集區，並配置存放裝置給 Hyper-V 叢集。
	- 為應該一起進行複寫的 LUN 建立複寫群組。 
2. <a href="#vault">步驟 2：建立保存庫</a>
3. <a href="#download">步驟 3：在 VMM 伺服器上安裝提供者</a>在保存庫中產生註冊金鑰及下載「提供者」安裝檔。您將在 VMM 伺服器上執行安裝程式來安裝提供者，並在保存庫中註冊伺服器。
4. <a href="#storage">步驟 4：對應存放裝置陣列和集區</a>對應陣列，以指定哪一個次要存放集區要從主要集區接收複寫資料。請在設定雲端保護之前先對應存放裝置，因為在您為複寫群組啟用保護時， 會使用此對應資訊。
5. <a href="#clouds">步驟 5：設定雲端保護</a>-為 VMM 雲端進行保護設定。
7. <a href="#replication">步驟 6：啟用複寫群組</a>-您必須先為存放裝置複寫群組啟用複寫，才能為虛擬機器啟用保護。
8. <a href="#enablevirtual">步驟 7：為虛擬機器啟用保護</a>-在複寫群組正在進行複寫之後，請為虛擬機器啟用保護。
9. <a href="#recovery plans">步驟 8：測試部署</a>-您可以藉由執行測試容錯移轉，測試虛擬機器和資料是否依照預期的方式容錯移轉。 


<a name="VMM"></a> <h3>步驟 1：準備 VMM 基礎結構</h3>

<a name="SAN"></a> <h4>將 VMM 中的 SAN 存放裝置整合並分類。</h4>


1. 在 [光纖] 工作區中，按一下 [存放裝置]。按一下 [首頁] > [新增資源] > [存放裝置] 以啟動 [新增存放裝置精靈]。
2. 在 [選取存放裝置提供者類型] 頁面上，選取 [由 SMI-S 提供者探索到及管理的 SAN 和 NAS 裝置]。

	![Provider type](./media/hyper-v-recovery-manager-configure-vault/SRSAN_Providertype.png)

3. 在 [請指定儲存 SMI-S 提供者的通訊協定與位址] 頁面上，選取 [SMI-S CIMXML] 並指定用來連線到提供者的設定。
4. 在 [提供者 IP 位址或 FQDN] 和 [TCP/IP 連接埠] 中，指定用來連線到提供者的設定。您只能針對 SMI-S CIMXML 使用 SSL 連線。

	![Provider connect](./media/hyper-v-recovery-manager-configure-vault/SRSAN_ConnectSettings.png)

5. 在 [執行身分帳戶] 中指定一個可以存取提供者的 VMM 執行身分帳戶，或建立一個新帳戶。
6. 在 [收集資訊] 頁面上，VMM 會自動嘗試探索並匯入存放裝置資訊。若要重新嘗試探索，請按一下 [掃描提供者]。如果探索程序成功，頁面上就會列出探索到的存放裝置陣列、存放集區、製造商、型號及容量。

	![Discover storage](./media/hyper-v-recovery-manager-configure-vault/SRSAN_Discover.png)

7. 在 [選取要列入管理的存放集區並指派分類] 中，選取 VMM 將管理的存放集區並為它們指派分類。LUN 資訊將會從存放集區匯入。請根據您需要保護的應用程式、其容量需求，以及您對於哪些項目要一起複寫的需求，建立 LUN。

	![Classify storage](./media/hyper-v-recovery-manager-configure-vault/SRSAN_Classify.png)

<a name="LUNs"></a> <h4>建立 LUN 並配置存放裝置</h4>


1. 將 SAN 存放裝置整合到 VMM 之後，您將建立 (佈建) LUN。如需詳細資料，請參閱：
	
	- <a href="http://go.microsoft.com/fwlink/?LinkId=518490">如何選取要在 VMM 中建立邏輯單元的方法</a>
	- <a href="http://go.microsoft.com/fwlink/?LinkId=518491">如何在 VMM 中佈建存放裝置邏輯單元</a>
2. 接著，配置儲存容量給 Hyper-V 主機叢集，讓 VMM 可以將虛擬機器資料部署到已佈建的存放裝置上： 
	- 在配置存放裝置給叢集之前，您必須先配置存放裝置給叢集所在的 VMM 主機群組。請參閱<a href="http://go.microsoft.com/fwlink/?LinkId=518493">如何將存放裝置邏輯單元配置到主機群組</a>及<a href="http://go.microsoft.com/fwlink/?LinkId=518492">如何將儲存集區配置到主機群組</a>。
	- 依照<a href="http://go.microsoft.com/fwlink/?LinkId=513017">如何在 VMM 中設定 Hyper-V 主機叢集上的存放裝置</a>中所述，配置儲存容量到叢集。
	

<a name="RGs"></a> <h4>建立複寫群組</h4>

1. 建立複寫群組，其中包含所有需要一起進行複寫的 LUN。
2. 在 VMM 主控台中，開啟存放裝置陣列內容的 [複寫群組] 索引標籤，然後按一下 [新增]。
	![SAN replication group](./media/hyper-v-recovery-manager-configure-vault/SRSAN_RepGroup.png)



<a name="vault"></a> <h3>步驟 2：建立保存庫</h3>
在驗證部署必要條件之後，建立一個 Azure Site Recovery 保存庫。或者，您也可以使用現有的保存庫並設定 SAN 複寫。

1. 登入[管理入口網站](https://manage.windowsazure.com)。


2. 依序展開 [資料服務]<b></b>、[復原服務]<b></b>，然後按一下 [Site Recovery 保存庫]<b></b>。


3. 按一下 [新建]<b></b>，然後按一下 [快速建立]<b></b>。
	
4. 在 [名稱]<b></b> 中，輸入用於識別保存庫的易記名稱。

5. 在 [地區]<b></b> 中，選取保存庫的地理區域。若要查看支援的地區，請參閱 <a href="http://go.microsoft.com/fwlink/?LinkId=389880">Azure Site Recovery 定價詳細資料</a> (英文) 中的＜各地區上市情況＞

6. 按一下 [建立保存庫]<b></b>。 

	![New Vault](./media/hyper-v-recovery-manager-configure-vault/SRSAN_HvVault.png)

<P>檢查狀態列，以確認是否順利建立保存庫。保存庫將會在主要 [復原服務] 頁面上列為 [使用中]<b></b>。</P>

<a name="upload"></a> <h2>步驟 3：設定保存庫</h3>


1. 在 [復原服務]<b></b> 頁面中，按一下保存庫以開啟 [快速入門] 頁面。您也可以使用圖示隨時開啟 [快速入門]。

	![Quick Start Icon](./media/hyper-v-recovery-manager-configure-vault/SRSAN_QuickStartIcon.png)

2. 在下拉式清單中，選取 [在使用陣列複寫的 Hyper-V 內部部署站台之間]。


<a name="download"></a> <h3>步驟 3：安裝 Azure Site Recovery 提供者</h3>
在您建立保存庫之後，請產生包含註冊金鑰的登錄檔。在您安裝 Azure Site Recovery 提供者時，將選取此檔案。 

1. 在 [快速入門]<b></b> 頁面上的 [準備 VMM 伺服器] 中，按一下 [產生註冊金鑰] 檔案。該金鑰在產生後會維持 5 天有效。您應該將此檔案下載到 VMM 伺服器可以存取的安全位置。例如，下載到某個共用。在您設定提供者時，將需要選取此檔案。

	![Registration key](./media/hyper-v-recovery-manager-configure-vault/SRSAN_QuickStartRegKey.png)


2. 在 [<b>快速入門</b>] 頁面上的 [**準備 VMM 伺服器**] 中，按一下 [<b>下載要在 VMM 伺服器上安裝的 Microsoft Azure Site Recovery 提供者</b>]，以取得最新版的提供者安裝檔案。
3. 在來源和目標 VMM 伺服器上執行這個檔案。
4. 在 [**必要條件檢查**] 中，選取停止 VMM 服務以開始執行提供者安裝程式。服務隨即停止，並將在安裝程式完成時自動重新啟動。 

	![Prerequisites](./media/hyper-v-recovery-manager-configure-vault/SRSAN_ProviderPrereq.png)

5. 在 [Microsoft Update] 中，您可以選擇要接收更新。啟用這項設定時，會根據您的 Microsoft Update 原則來安裝提供者更新。

安裝提供者之後，請繼續安裝以在保存庫中註冊伺服器。

6. 在 [網際網路連線] 頁面上，指定在 VMM 伺服器上執行的提供者連線到網際網路的方式。若要使用伺服器上設定的預設網際網路連線設定，請選取 [使用預設系統 Proxy 設定]<b></b>。

	![Internet Settings](./media/hyper-v-recovery-manager-configure-vault/SRSAN_ProviderProxy.png)

7. 在 [註冊金鑰] 中，選取您已從 Azure Site Recovery 下載並複製到 VMM 伺服器的金鑰。
8. 在 [伺服器名稱] 中，指定用於識別保存庫中之 VMM 伺服器的易記名稱。

	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SRSAN_ProviderRegKeyServerName.png)

9. 在 [初始雲端中繼資料同步] 中，選取您是否要將 VMM 伺服器上所有雲端的中繼資料與保存庫同步。這個動作只需要在每個伺服器上進行一次。如果不要同步所有雲端，您可以取消核取這項設定，
10. 
11. 再於 VMM 主控台的雲端屬性中個別同步每個雲端。

10. [資料加密] 選項與此案例無關。 

	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SRSAN_ProviderSyncEncrypt.png)

11. 按一下 [註冊]<b></b> 以完成此程序。註冊之後，Azure Site Recovery 即可從 VMM 伺服器抓取中繼資料。此伺服器會顯示在儲存庫中 [伺服器] 頁面上的 [資源]<b></b> 索引標籤上。安裝完提供者之後，您可在 VMM 主控台中修改提供者設定。

 
<h3><a id="storage"></a>步驟 4：對應存放裝置陣列和集區</h3>

您將需要在主要和次要站台的存放裝置陣列與集區之間建立對應。

在開始之前，請先確定雲端有出現在保存庫中。偵測雲端的方式有兩種：在安裝提供者時選取同步所有雲端；或在 VMM 主控台中雲端內容的 [一般] 索引標籤上，選取同步某個特定的雲端。然後，依下列方式對應存放裝置陣列：

1. 按一下 [資源] > [伺服器存放裝置] > [對應來源和目標陣列]。
	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SRSAN_StorageMap.png)
2. 選取主要站台上的存放裝置陣列，然後將它們對應至次要站台上的存放裝置陣列。
3.  對應陣列內的來源和目標存放集區。若要這樣做，請在 [存放集區] 中，選取來源和目標存放集區來進行對應。

	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SRSAN_StorageMapPool.png)




<h3><a id="clouds"></a>步驟 5：設定雲端保護設定</h3>

註冊 VMM 伺服器之後，您就可以設定雲端保護設定。在您安裝提供者時，便啟用了 [將雲端資料與保存庫同步] 選項，因此 VMM 伺服器上的所有雲端都會出現在保存庫的 [受保護項目]<b></b> 索引標籤中。

	![Published Cloud](./media/hyper-v-recovery-manager-configure-vault/SRSAN_CloudsList.png)

1. 在 [快速入門] 頁面上，按一下 [設定 VMM 雲端的保護]。
2. 在 [受保護項目] 索引標籤上，選取您想要設定的雲端，然後前往 [設定] 索引標籤。請注意：
3. 在 [目標]<b></b> 中，選取 [VMM]<b></b>。
4. 在 [<b>目標位置</b>] 中，選取管理要供復原使用之雲端的站上 VMM 伺服器。
5. 在 [目標雲端]<b></b> 中，選取要供來源雲端中虛擬機器容錯移轉使用的目標雲端。請注意：
	- 建議您，選取的目標雲端要符合所要保護之虛擬機器的復原需求。
	- 雲端只能當成單一雲端組中的主要雲端或目標雲端。
6. Azure Site Recovery 會確認雲端能夠存取具備 SAN 複寫功能的存放裝置，並確認存放裝置陣列已對等互連。將會顯示參與的陣列對等項。
7. 如果驗證成功，請在 [複寫類型] 中，選取 [SAN]。

<p>儲存設定之後，會建立一個工作，並可在 [工作]<b></b> 索引標籤上進行監視。在 [設定]<b></b> 索引標籤上可以修改雲端設定。如果您要修改目標位置或目標雲端，則必須移除雲端組態，然後重新設定雲端。</p>


<h3><a id="replication"></a>步驟 6：為複寫群組啟用複寫</h3>

您必須先為存放裝置複寫群組啟用複寫，才能為虛擬機器啟用保護。 

1. 在 Azure Site Recovery 入口網站之主要雲端的內容頁面中，開啟 [虛擬機器] 索引標籤。按一下 [新增複寫群組]。
2. 選取一個或多個與雲端關聯的 VMM 複寫群組、確認來源和目標陣列，然後指定複寫頻率。

<P>當此操作完成時，Azure Site Recovery 會與 VMM 和 SMI-S 提供者一起佈建目標站台存放裝置 LUN，並啟用存放裝置複寫。如果複寫群組已經被複寫，Azure Site Recovery 便會重複使用現有的複寫關係，然後更新 Azure Site Recovery 中的資訊。</P>

<h2><a id="enablevirtual"></a>步驟 7：啟用虛擬機器保護</h2>
<p>在存放裝置群組正在進行複寫之後，請在 VMM 主控台中，使用下列其中一個方法為虛擬機器啟用保護：</p>



- **新增虛擬機器**-在 VMM 主控台中，當您建立新虛擬機器時，請啟用 Azure Site Recovery 保護，並將該虛擬機器與複寫群組建立關聯。
使用此選項時，VMM 會使用智慧型放置，以最佳方式將虛擬機器存放裝置放置在複寫群組的 LUN 上。Azure Site Recovery 會在次要網站上協調建立影子虛擬機器並配置容量，以便能夠在容錯移轉後啟動複本虛擬機器。
- **現有的虛擬機器**-如果 VMM 中已經部署虛擬機器，您便可以啟用 Azure Site Recovery 保護，並進行移轉至複寫群組的存放裝置移轉。完成之後，VMM 和 Azure Site Recovery 會偵測新的虛擬機器，並開始在 Azure Site Recovery 中管理它以進行保護。建立影子虛擬機器時，會在次要站台上建立並配置容量，以便能夠在容錯移轉後啟動複本虛擬機器。


	![Enable protection](./media/hyper-v-recovery-manager-configure-vault/SRSAN_EnableProtection.png)
	

<P>為虛擬機器啟用保護之後，它們就會出現在 Azure Site Recovery 主控台中。您可以檢視虛擬機器內容、追蹤狀態，以及讓包含多個虛擬機器的複寫群組進行容錯移轉。請注意，在 SAN 複寫中，與複寫群組關聯的所有虛擬機器必須都一起進行容錯移轉。這是因為容錯移轉會先發生在儲存層。請務必正確地組成您的複寫群組，只將相關聯的虛擬機器放在一起。</P>

您可以在 [工作] 索引標籤中追蹤「啟用保護」動作的進度，包括初始複寫。執行「完成保護」工作之後，虛擬機器即準備好進行容錯移轉。 
	![Virtual machine protection job](./media/hyper-v-recovery-manager-configure-vault/SRSAN_JobPropertiesTab.png)

<h3><a id="recoveryplans"></a>步驟 8：測試部署</h3>



測試您的部署，以確定虛擬機器和資料容錯移轉如預期般運作。若要這樣做，您將必須選取複寫群組來建立復原方案。然後在方案上執行測試容錯移轉。

1. 在 [復原方案] 索引標籤上，按一下 [建立復原方案]。
2. 指定復原方案的名稱，以及來源和目標 VMM 伺服器。來源伺服器必須有已啟用容錯移轉和復原功能的虛擬機器。請選取 [SAN]，以檢視為 SAN 複寫設定的雲端。

	![Create recovery plan](./media/hyper-v-recovery-manager-configure-vault/SRSAN_RPlan.png)

4. 在 [選取虛擬機器] 中，選取複寫群組。所有與複寫群組關聯的虛擬機器，將會被選取並新增至復原方案。這些虛擬機器會新增到復原方案預設群組 (群組 1)。您可以視需要新增更多群組。請注意，複寫之後，虛擬機器將會根據復原方案群組的順序來啟動。

	![Add virtual machines](./media/hyper-v-recovery-manager-configure-vault/SRSAN_RPlanVM.png)	
5. 建立復原方案之後，它會出現在 [復原方案] 索引標籤上的清單中。 
6. 在 [復原方案] 索引標籤上，選取方案並按一下 [測試容錯移轉]。
7. 在 [確認測試容錯移轉] 頁面上，選取 [無]。請注意，啟用此選項時，容錯移轉複本虛擬機器將不會連線到任何網路。這將會測試虛擬機器是否依照預期執行容錯移轉，但是不會測試您的複寫網路環境。如果您想要執行更完整的測試容錯移轉，請參閱 <a href="http://go.microsoft.com/fwlink/?LinkId=522291">MSDN 上的測試內部部署</a>。

	![Select test network](./media/hyper-v-recovery-manager-configure-vault/SRSAN_TestFailover1.png)


8. 測試虛擬機器將建立在複本虛擬機器所在的相同主機上。它並不會新增至複本虛擬機器所在的雲端。
9. 複製之後，複本虛擬機器的 IP 位址會不同於主要虛擬機器的 IP 位址。如果您是從 DHCP 發出位址，便會自動更新位址。如果您不是使用 DHCP 而想要確定位址相同，您將需要執行數個指令碼。
10. 執行此範例指令碼以抓取 IP 位址。
    **$vm = Get-SCVirtualMachine -Name <VM_NAME>
	$na = $vm[0].VirtualNetworkAdapters>
	$ip = Get-SCIPAddress -GrantToObjectID $na[0].id
	$ip.address**  
11. 執行此範例指令碼來更新 DNS (使用上一個範例指令碼抓取到的 IP 位址)。

	**[string]$Zone,
	[string]$name,
	[string]$IP
	)
	$Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
	$newrecord = $record.clone()
	$newrecord.RecordData[0].IPv4Address  =  $IP
	Set-DnsServerResourceRecord -zonename com -OldInputObject $record -NewInputObject $Newrecord**

<h2><a id="runtest" name="runtest" href="#runtest"></a>監視活動</h2>
<p>您可以使用 [工作]<b></b> 索引標籤和 [儀表板]<b></b> 來檢視和監視 Azure Site Recovery 保存庫所執行的主要工作，包括設定雲端保護、啟用和停用虛擬機器保護、執行容錯移轉 (計劃性、非計劃性或測試)，以及認可非計劃性容錯移轉。</p>

<p>您可以從 [工作]<b></b> 索引標籤檢視工作、向下切入至工作詳細資料和錯誤、執行工作查詢以抓取符合特定條件的工作、將工作匯出至 Excel，以及重新啟動失敗的工作。</p>

<p>您可以從 [儀表板]<b></b> 下載最新版的提供者和代理程式安裝檔案、取得保存庫的設定資訊、查看由保存庫管理其保護的虛擬機器數目、查看最近的工作、管理保存庫憑證，以及重新同步虛擬機器。</p>

<p>如需與工作和儀表板互動的詳細資訊，請參閱<a href="http://go.microsoft.com/fwlink/?LinkId=398534">作業和監視指南</a>。</p>
	
<h2><a id="next" name="next" href="#next"></a>後續步驟</h2>
<UL>
<LI>若要在完全生產環境中規劃及部署 Azure Site Recovery，請參閱 <a href="http://go.microsoft.com/fwlink/?LinkId=321294">Azure Site Recovery 的規劃指南</a>及 <a href="http://go.microsoft.com/fwlink/?LinkId=321295">Azure Site Recovery 的部署指南</a>。</LI>
<LI>若有任何問題，請造訪 <a href="http://go.microsoft.com/fwlink/?LinkId=313628">Azure 復原服務論壇</a> (英文)。</LI> 
</UL>

<!--HONumber=49-->