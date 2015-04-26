<properties 
	pageTitle="教學課程：設定內部部署 VMM 網站之間的保護" 
	description="Azure Site Recovery 可協調內部部署 VMM 網站之間的 Hyper-V 虛擬機器複寫、容錯移轉及復原作業。" 
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


# 開始使用 Azure Site Recovery：使用 Hyper-V 複寫進行內部部署至內部部署 VMM 站台保護


<div class="dev-callout"> 

<p>Azure Site Recovery 可在一些部署案例中協調虛擬機器的複寫、容錯移轉及復原，為您的商務與工作負載持續性策略做出貢獻。<p>

<P>本教學課程說明如何使用 Hyper-V 複寫來部署 Azure Site Recovery，把針對在某個內部部署 VMM 站台上執行之工作負載提供的保護，透過協調及自動化移轉至另一個 VMM 內部部署站台。本教學課程將盡可能使用最快的部署路徑與預設設定。</P>

<UL>
<LI>您可以在<a href="http://go.microsoft.com/fwlink/?LinkId=321294">規劃</a>及<a href="http://go.microsoft.com/fwlink/?LinkId=321295">部署</a>指南閱讀完整的部署指示。</LI>
<LI>如需其他 Azure Site Recovery 部署案例的相關資訊，請參閱 <a href="http://go.microsoft.com/fwlink/?LinkId=518690">Azure Site Recovery 概觀</a>。</LI>
<LI>如果您在進行本教學課程期間發生問題，請檢閱 Wiki 文章 <a href="http://go.microsoft.com/fwlink/?LinkId=389879">Azure Site Recovery：常見錯誤案例和解決方法</a>，或在 <a href="http://go.microsoft.com/fwlink/?LinkId=313628">Azure 復原服務論壇</a>提出您的問題。</LI>
</UL>

</div>


<h2><a id="before"></a>必要條件</h2> 
<div class="dev-callout"> 
<P>開始教學課程之前，請確認一切就緒。</P>

<UL>
<LI><b>Azure 帳戶</b>-您將需要一個 Azure 帳戶。如果您沒有帳戶，請參閱 <a href="http://aka.ms/try-azure">Azure 免費試用</a>。您可以從 <a href="http://go.microsoft.com/fwlink/?LinkId=378268">Azure Site Recovery 管理員定價詳細資料</a>取得定價資訊。</LI>
<LI><b>VMM 伺服器</b>-您至少需要一部在 System Center 2012 SP1 或 System Center 2012 R2 上執行的 VMM 伺服器。</LI>
<LI><b>VMM 雲端</b>-您至少應該在要保護的來源 VMM 伺服器上具有一個雲端，並在目標 VMM 伺服器上具有一個雲端。如果您執行一部 VMM 伺服器，則需要兩個雲端。要保護的主要雲端必須包含下列項目：<UL>
	<LI>一或多個 VMM 主機群組</LI>
	<LI>每個主機群組中的一或多個 Hyper-V 主機伺服器或叢集。</LI>
	<li>位於雲端中來源 Hyper-V 伺服器上的一或多部虛擬機器。</li>
		</UL></LI>
<LI>**網路**-您可以視需要設定網路對應，以確保在容錯移轉後，會以最佳方式將複本虛擬機器置於 Hyper-V 主機伺服器上，並且這些虛擬機器可以連線到適當的 VM 網路。已啟用網路對應時，位於主要位置的虛擬機器將會連線至網路，且其位於目標位置的複本將會連線到其對應的網路。如果您沒有設定網路對應，虛擬機器將不會在容錯移轉後連線到 VM 網路。本教學課程所述為最簡單的逐步設定，因此不包括網路對應，但您可以從下列參考資料取得詳細資訊：</LI>
	<UL>
	<LI>《規劃指南》中的<a href="http://go.microsoft.com/fwlink/?LinkId=522289">網路對應</a></LI>
	<LI>《部署指南》中的<a href="http://go.microsoft.com/fwlink/?LinkId=522293">啟用網路對應</a></LI>
	</UL></LI>

</UL>



<h2><a id="tutorial"></a>教學課程步驟</h2> 

確認已達成先決條件之後，請執行下列動作：
<UL>
<LI><a href="#vault">步驟 1：建立保存庫</a>-建立 Azure Site Recovery 保存庫。</LI>
<LI><a href="#download">步驟 2：在每個 VMM 伺服器上安裝「提供者」應用程式</a>-在保存庫中產生註冊金鑰，及下載「提供者」安裝檔。您將在每部 VMM 伺服器上執行安裝程式來安裝提供者，並在保存庫中註冊伺服器。</LI>
<LI><a href="#clouds">步驟 3：設定雲端保護</a>-為 VMM 雲端進行保護設定。這些保護設定會套用至您啟用 Azure Site Recovery 保護功能之雲端中的所有虛擬機器。</LI>
<LI><a href="#storagemapping">步驟 4：設定儲存體對應</a>-如果您想要指定複寫資料的儲存位置，可以設定儲存體對應。這會將來源 VMM 伺服器上的儲存體分類對應至目標伺服器上的儲存體分類。</LI>
<LI><a href="#enablevirtual">步驟 5：對虛擬機器啟用保護</a>-對受保護 VMM 雲端中的虛擬機器啟用保護</LI>
<LI><a href="#recovery plans">步驟 6：設定並執行復原計畫</a>-建立復原方案，並為此方案執行測試容錯移轉，以確定它可以運作。</LI>

</UL>




<a name="vault"></a> <h2>步驟 1：建立保存庫</h2>

1. 登入[管理入口網站](https://manage.windowsazure.com)。


2. 依序展開 [資料服務]<b></b>、[復原服務]<b></b>，然後按一下 [Site Recovery 保存庫]<b></b>。


3. 按一下 [新建]<b></b>，然後按一下 [快速建立]<b></b>。
	
4. 在 [名稱]<b></b> 中，輸入用於識別保存庫的易記名稱。

5. 在 [地區]<b></b> 中，選取保存庫的地理區域。若要查看支援的地區，請參閱 <a href="http://go.microsoft.com/fwlink/?LinkId=389880">Azure Site Recovery 定價詳細資料</a>中的＜各地區上市情況＞。

6. 按一下 [建立保存庫]<b></b>。 

	![New Vault](./media/hyper-v-recovery-manager-configure-vault/SR_HvVault.png)

<P>檢查狀態列，以確認是否順利建立保存庫。保存庫將會在主要 [復原服務] 頁面上列為 [使用中]<b></b>。</P>

<a name="upload"></a> <h2>步驟 2：設定保存庫</h2>


1. 在 [復原服務]<b></b> 頁面中，按一下保存庫以開啟 [快速入門] 頁面。您也可以使用圖示隨時開啟 [快速入門]。

	![Quick Start Icon](./media/hyper-v-recovery-manager-configure-vault/SR_QuickStartIcon.png)

2. 在下拉式清單中，選取 [在兩個內部部署 Hyper-V 站台之間]****。
3. 在 [準備 VMM 伺服器]**** 中，按一下 [產生註冊金鑰]**** 檔案。該金鑰在產生後會維持 5 天有效。將檔案複製到 VMM 伺服器。設定提供者時需要這個檔案。

	![Registration key](./media/hyper-v-recovery-manager-configure-vault/SR_E2ERegisterKey.png)
	



<a name="download"></a> <h2>步驟 3：安裝 Azure Site Recovery 提供者</h2>
	

1. 在 [快速入門]<b></b> 頁面上的 [準備 VMM 伺服器]**** 中，按一下 [下載要在 VMM 伺服器上安裝的 Microsoft Azure Site Recovery 提供者]<b></b>，以取得最新版的提供者安裝檔案。

2. 在來源和目標 VMM 伺服器上執行這個檔案。

3. 在 [必要條件檢查]**** 中，選取停止 VMM 服務以開始執行提供者安裝程式。服務隨即停止，並將在安裝程式完成時自動重新啟動。 

	![Prerequisites](./media/hyper-v-recovery-manager-configure-vault/SR_ProviderPrereq.png)

4. 在 [Microsoft Update]**** 中，您可以選擇要接收更新。啟用這項設定時，會根據您的 Microsoft Update 原則來安裝提供者更新。

	![Microsoft Updates](./media/hyper-v-recovery-manager-configure-vault/SR_ProviderUpdate.png)

安裝提供者之後，請繼續安裝以在保存庫中註冊伺服器。

5. 在 [網際網路連線] 頁面上，指定在 VMM 伺服器上執行的提供者連線到網際網路的方式。若要使用伺服器上設定的預設網際網路連線設定，請選取 [使用預設系統 Proxy 設定]<b></b>。

	![Internet Settings](./media/hyper-v-recovery-manager-configure-vault/SR_ProviderProxy.png)

6. 在 [註冊金鑰]**** 中，選取您已從 Azure Site Recovery 下載並複製到 VMM 伺服器的金鑰。
7. 在 [保存庫名稱]**** 中，確認將於其中註冊伺服器的保存庫名稱。
8. 在 [伺服器名稱]**** 中，指定用於識別保存庫中之 VMM 伺服器的易記名稱。

	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SR_ProviderRegKeyServerName.png)


9. 在 [初始雲端中繼資料同步]**** 中，選取您是否要將 VMM 伺服器上所有雲端的中繼資料與保存庫同步。這個動作只需要在每個伺服器上進行一次。如果不要同步所有雲端，您可以取消核取這項設定，再於 VMM 主控台的雲端屬性中個別同步每個雲端。


7. 在 [資料加密]**** 中，您將產生要用來加密 Azure 中受保護資料的憑證。 
此選項與本教學課程中所述的案例無關。

	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SR_ProviderSyncEncrypt.png)

8. 按一下 [註冊]<b></b> 以完成此程序。註冊之後，Azure Site Recovery 即可從 VMM 伺服器抓取中繼資料。此伺服器會顯示在儲存庫中 [伺服器]**** 頁面上的 [資源]<b></b> 索引標籤上。




<h2><a id="clouds"></a>步驟 4：設定雲端保護設定</h2>

註冊 VMM 伺服器之後，您就可以設定雲端保護設定。在您安裝提供者時，便啟用了 [將雲端資料與保存庫同步]**** 選項，因此 VMM 伺服器上的所有雲端都會出現在保存庫的 [受保護項目]<b></b> 索引標籤中。

![Published Cloud](./media/hyper-v-recovery-manager-configure-vault/SR_CloudsList.png)

1. 在 [快速入門] 頁面上，按一下 [設定 VMM 雲端的保護]****。
2. 在 [受保護項目]**** 索引標籤上，選取您想要設定的雲端，然後前往 [設定]**** 索引標籤。請注意：
3. 在 [目標]<b></b> 中，選取 [VMM]<b></b>。
4. 在 [目標位置]<b></b> 中，選取管理要供復原使用之雲端的站上 VMM 伺服器。
4. 在 [目標雲端]<b></b> 中，選取要供來源雲端中虛擬機器容錯移轉使用的目標雲端。請注意：
	- 建議您，選取的目標雲端要符合所要保護之虛擬機器的復原需求。
	- 雲端只能當成單一雲端組中的主要雲端或目標雲端。

6. 保留 [複製頻率]<b></b> 的預設設定。這個值指定應在來源與目標位置之間同步處理資料的頻率。這只有在 Hyper-V 主機是執行 Windows Server 2012 R2 時才有重要性。對於其他伺服器，使用預設設定的 5 分鐘即可。
7. 保留 [額外的復原點]<b></b> 中的預設設定。這個值指定是否要建立其他復原點。預設值 0 指定只會在複本主機伺服器上儲存主要虛擬機器的最新復原點。
8. 保留 [應用程式一致快照的頻率]<b></b> 中的預設設定。這個值指定建立快照的頻率。快照會使用「磁碟區陰影複製服務」(VSS) 來確保建立快照時，應用程式是處於一致狀態。若要針對本教學課程的逐步解說設定這個值，請務必將它設定為小於您設定的其他復原點數目。
	![Configure protection settings](./media/hyper-v-recovery-manager-configure-vault/SR_CloudSettingsE2E.png)
9. 在 [資料傳輸壓縮]<b></b> 中，指定是否應該將傳輸的複寫資料壓縮。 
10. 在 [驗證]<b></b>中，指定如何驗證主要與復原 Hyper-V 主機伺服器之間的流量。除非您已設定可用的 Kerberos 環境，否則建議您針對這個逐步解說選取 HTTPS。Azure Site Recovery 會自動設定用於 HTTPS 驗證的憑證。不需要進行任何手動設定。請注意，這項設定只有在 Hyper-V 主機伺服器是執行於 Windows Server 2012 R2 時才有重要性。
11. 保留 [連接埠]<b></b>中的預設設定。這個值會設定來源與目標 Hyper-V 主機電腦接聽複寫流量的連接埠號碼。
12. 在 [複寫方法]<b></b> 中，指定在正常複寫開始前，初次將資料從來源複寫到目標位置時的處理方式。 
	- <b>透過網路</b>-透過網路複製資料可能既費時又耗資源。如果雲端中的虛擬機器使用相當小的虛擬硬碟，而且主要 VMM 伺服器透過寬頻連線來連接次要 VMM 伺服器，則建議您使用這個選項。您可以指定立即開始複製，或是選取開始複製的時間。如果您使用網路複寫，建議您排在離峰時段進行。
	- <b>離線</b>-這個方法指定要使用外部媒體執行初次複寫。如果您想要避免影響網路效能，或者複寫目的地位於很遙遠的地方，就適合使用這個方法。若要使用這個方法，您需指定來源雲端上的匯出位置，以及目標雲端上的匯入位置。對虛擬機器啟用保護時，虛擬硬碟會複製到指定的匯出位置。您需將它送到目標站台，再將它複製到匯入位置。系統會將匯入的資訊複製到複本虛擬機器。如需離線複寫必要條件的完整清單，請參閱 <a href="http://go.microsoft.com/fwlink/?LinkId=323469">步驟 3：設定 VMM 雲端的保護設定</a>。
13. 選取 [刪除複本虛擬機器]**** 以指定在停止保護虛擬機器 (在雲端屬性的 [虛擬機器] 索引標籤上選取 [刪除虛擬機器的保護]**** 選項) 時，應該刪除複本虛擬機器。如果啟用這項設定，當您停用保護時，便會從 Azure Site Recovery 中移除虛擬機器、在 VMM 主控台中移除虛擬機器的 Site Recovery 設定，並刪除複本。
	![Configure protection settings](./media/hyper-v-recovery-manager-configure-vault/SR_CloudSettingsE2ERep.png)

<p>儲存設定之後，會建立一個工作，並可在 [工作]<b></b> 索引標籤上進行監視。VMM 來源雲端中的所有 Hyper-V 主機伺服器會設定進行複寫。在 [設定]<b></b> 索引標籤上可以修改雲端設定。如果您要修改目標位置或目標雲端，則必須移除雲端組態，然後重新設定雲端。</p>

<h2><a id="storagemapping"></a>步驟 5：設定儲存體對應</h2>

<p>本教學課程說明在測試環境中部署 Azure Site Recovery 的最簡單路徑。如果您想在進行本教學課程時設定儲存體對應，請遵循《部署指南》之<a href="http://go.microsoft.com/fwlink/?LinkId=402535">設定儲存體對應</a>中的步驟進行。</p>


<h2><a id="enablevirtual"></a>步驟 6：啟用虛擬機器保護</h2>
<p>正確設定伺服器、雲端和網路後，您就可以對雲端中的虛擬機器啟用保護。</p>
<OL>
<li>在虛擬機器所在雲端中的 [虛擬機器]<b></b>索引標籤上，按一下 [啟用保護]<b></b>，然後選取 [新增虛擬機器]<b></b>。 </li>
<li>從雲端中所有虛擬機器的清單，選取您要保護的虛擬機器。</li> 
</OL>

![Enable virtual machine protection](./media/hyper-v-recovery-manager-configure-vault/SR_EnableProtectionVM.png)


<P>您可以在 [工作]**** 索引標籤中追蹤「啟用保護」動作的進度，包括初始複寫。執行「完成保護」工作之後，虛擬機器即準備好進行容錯移轉。啟用保護並複寫虛擬機器之後，您將能夠在 Azure 中檢視這些虛擬機器。</p>



![Virtual machine protection job](./media/hyper-v-recovery-manager-configure-vault/SR_VMJobs.png)


<h2><a id="recoveryplans"></a>步驟 7：測試部署</h2>

測試您的部署，以確定虛擬機器和資料容錯移轉如預期般運作。若要這樣做，您將必須選取複寫群組來建立復原方案。然後在方案上執行測試容錯移轉。

1. 在 [復原方案]**** 索引標籤上，按一下 [建立復原方案]****。
2. 指定復原方案的名稱，以及來源和目標 VMM 伺服器。來源伺服器必須有已啟用容錯移轉和復原功能的虛擬機器。請選取 [Hyper-V]****，以檢視為 Hyper-V 複寫設定的雲端。

	![Create recovery plan](./media/hyper-v-recovery-manager-configure-vault/SRE2E_RP1.png)

3. 在 [選取虛擬機器]**** 中，選取複寫群組。所有與複寫群組關聯的虛擬機器，將會被選取並新增至復原方案。這些虛擬機器會新增到復原方案預設群組 (群組 1)。您可以視需要新增更多群組。請注意，複寫之後，虛擬機器將會根據復原方案群組的順序來啟動。

	![Add virtual machines](./media/hyper-v-recovery-manager-configure-vault/SRE2E_RP2.png)	

4. 建立復原方案之後，它會出現在 [復原方案]**** 索引標籤上的清單中。 
5. 在 [復原方案]**** 索引標籤上，選取方案並按一下 [測試容錯移轉]****。
6. 在 [確認測試容錯移轉]**** 頁面上，選取 [無]****。請注意，啟用此選項時，容錯移轉複本虛擬機器將不會連線到任何網路。這將會測試虛擬機器是否依照預期執行容錯移轉，但是不會測試您的複寫網路環境。如果您想要執行更完整的測試容錯移轉，請參閱 <a href="http://go.microsoft.com/fwlink/?LinkId=522291">MSDN 上的測試內部部署</a>。

	![Select test network](./media/hyper-v-recovery-manager-configure-vault/SRSAN_TestFailover1.png)


7. 測試虛擬機器將建立在複本虛擬機器所在的相同主機上。它並不會新增至複本虛擬機器所在的雲端。
8. 複製之後，複本虛擬機器的 IP 位址會不同於主要虛擬機器的 IP 位址。如果您是從 DHCP 發出位址，便會自動更新 DNS。如果您不是使用 DHCP 而想要確定位址相同，您將需要執行數個指令碼。
9. 執行此範例指令碼以抓取 IP 位址。
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
