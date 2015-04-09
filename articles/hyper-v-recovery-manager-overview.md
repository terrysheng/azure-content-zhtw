<properties 
	pageTitle="Azure 站台復原概觀" 
	description="部署 Azure 站台復原，以將其他內部部署站台或 Azure 當做目的地保護內部部署虛擬機器。" 
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

# Azure 站台復原概觀

<div class="dev-callout"> 
<p>Azure Site Recovery 會在一些案例中協調複寫和容錯移轉：</p>


<ul>
<li>**利用 Hyper-V 複寫進行內部部署 Hyper-V 站台到 Azure 保護** -利用一或多個 Hyper-V 伺服器從內部部署站台協調複寫、容錯移轉和復原作業，但沒有 System Center VMM。虛擬機器資料會從來源 Hyper-V 主機伺服器複寫到 Azure。讀取<a href="http://go.microsoft.com/fwlink/?LinkId=522298">開始使用 Azure Site Recovery：利用 Hyper-V 複寫在內部部署 Hyper-V 站台及 Azure 之間進行保護</a>。</li>
<li>**利用 Hyper-V 複寫進行內部部署 VMM 站台對內部部署 VMM 站台保護** - 協調內部部署 VMM 站台之間的複寫、容錯移轉和復原作業。虛擬機器資料會從來源 Hyper-V 主機伺服器複寫到目標主機伺服器。讀取<a href="http://go.microsoft.com/fwlink/?LinkId=398765">開始使用 Azure Site Recovery：利用 Hyper-V 複寫進行兩個內部部署 VMM 站台之間的保護</a>。</li>

<li>**利用 SAN 複寫進行內部部署 VMM 站台對內部部署 VMM 站台保護** - 使用存放裝置陣列型，在裝載來源和目標內部部署站台中虛擬機器資料之 SAN 裝置之間，協調端對端複寫、容錯移轉和復原作業。讀取<a href="http://go.microsoft.com/fwlink/?LinkId=518683">開始使用 Azure Site Recovery：：利用 SAN 複寫進行兩個內部部署 VMM 站台之間的保護</a>。</li>

<li>**內部部署 VMM 站台到 Azure 保護** - 協調內部部署 VMM 站台和 Azure 之間的複寫、容錯移轉和復原作業。複寫的虛擬機器資料儲存在 Azure 儲存體中。讀取<a href="http://go.microsoft.com/fwlink/?LinkId=398764">開始使用 Azure Site Recovery：內部部署 VMM 站台與 Azure 之間的保護。</a></li>

<li>**利用 InMage 進行內部部署 VMWare 站台至內部部署 VMWare 站台** - InMage Scout 是 Microsoft 最近的併購項目，可提供內部部署 VMWare 站台之間的即時複寫。現在，InMage 可做為個別產品，透過訂閱 Azure Site Recovery 服務取得。讀取<a href="http://go.microsoft.com/fwlink/?LinkId=518684">開始使用 Azure Site Recovery：利用 InMage 進行內部部署 VMWare 站台之間的保護</a>。</li>
</ul>

<p>功能矩陣摘要說明這些案例。</p>

<table border="1">
<thead>
<tr>
	<th>功能</th><th>內部部署至 Azure</th>
	<th>內部部署至內部部署 (Hyper-V)</th>
	<th>內部部署至內部部署 (SAN)</th>
</tr>
</thead>

<tr>
<td>資料至 Azure 站台復原</td>
<td><p>雲端中繼資料會傳送至 Azure 站台復原。</p> <p>複寫的資料會儲存在 Azure 儲存體中。</p></td>
<td><p>雲端中繼資料會傳送至 Azure 站台復原。</p> <p>複寫資料儲存在目標 Hyper-V 伺服器所指定的位置。</p></td>
<td><p>雲端中繼資料會傳送至 Azure 站台復原。</p> <p>複寫資料儲存在目標陣列存放裝置中。</p></td>
</tr>

<tr>
<td>保存庫必要條件</td>
<td><p>您需要 Azure 帳戶才能設定 Azure Site Recovery 保存庫。請參閱 <a href="http://aka.ms/try-azure">Azure 免費試用</a>。您可以從 <a href="http://go.microsoft.com/fwlink/?LinkId=378268">Azure Site Recovery 管理員定價詳細資料</a> (英文) 取得定價資訊。</p></td>
<td><p>啟用 Azure Site Recovery 的 Azure 帳戶。</p>
</td><td><p>啟用 Azure Site Recovery 的 Azure 帳戶。</p></td>
</tr>

<tr>
<td>複寫</td>
<td>虛擬機器會從來源內部部署 Hyper-V 伺服器複寫至目標 Azure 儲存體。您可以設定反向複寫來複寫回來源位置。</td>
<td>虛擬機器會從一個來源內部部署 Hyper-V 伺服器複寫至另一個。您可以設定反向複寫來複寫回來源位置。</td>
<td>虛擬機器會從來源 SAN 存放裝置複寫至目標 SAN 裝置。您可以設定反向複寫來複寫回來源位置。</td>
</tr>

<tr>
<td>虛擬機器存放裝置</td>
<td>Azure 儲存體中儲存的虛擬機器硬碟</td>
<td>Hyper-V 主機上儲存的虛擬機器硬碟。</td>
<td>儲存在 SAN 存放裝置陣列上的虛擬機器硬碟。</td>
</tr>

<tr>
<td>Azure 儲存體</td>
<td><p>需要 Azure 儲存體帳戶。</p> <p>帳戶應該啟用異地複寫、與 Azure 站台復原服務位於相同的區或，且與相同的訂閱相關聯。</p></td>
<td>不適用</td>
<td>不適用</td>
</tr>

<tr>
<td>SAN 存放裝置陣列</td>
<td><p>不適用</p></td>
<td>不適用</td>
<td>SAN 存放裝置陣列必須可提供來源和目標站台使用，並由 VMM 管理。如需詳細資訊，請參閱<a href="http://go.microsoft.com/fwlink/?LinkId=518685">部署必要條件</a>。 </td>
</tr>

<tr>
<td>VMM 伺服器</td>
<td>只有來源站台需要 VMM 伺服器。VMM 伺服器必須有至少一個雲端包含至少一個 Hyper-V 主機伺服器或叢集。</td>
<td>需要來源和目標 VMM 伺服器，且各伺服器上至少有一個雲端，或兩個雲端共用單一 VMM 伺服器。雲端必須包含至少一個 Hyper-V 主機伺服器或叢集。</td>
<td>需要來源和目標 VMM 伺服器，且各伺服器上至少有一個雲端。雲端必須包含至少一個 Hyper-V 叢集。</td>
</tr>

<tr>
<td>VMM System Center 版本</td>
<td>System Center 2012 R2</td>
<td><p>System Center 2012 (含 SP1)</p><p>System Center 2012 R2</p></td>
<td><p>System Center 2012 R2 含 VMM 更新彙總套件 5.0 </p></td>
</tr>

<tr>
<td>VMM 設定</td>
<td><p>在來源和目標站台中設定雲端</p><p>在來源和目標站台中設定 VM 網路</p><p>在來源和目標站台中設定存放裝置分類 <p>在來源與目標 VMM 伺服器上安裝提供者</p></td>
<td><p>在來源站台中設定雲端</p><p>設定 SAN 存放裝置</p><p>在來源站台中設定 VM 網路</p><p>在來源 VMM 伺服器上安裝提供者</p><p>啟用虛擬機器保護</p></td>
<td><p>在來源和目標站台中設定雲端</p><p>在來源和目標站台中設定 VM 網路</p><p>在來源和目標 VMM 伺服器上設定提供者</p><p>啟用虛擬機器保護</p></td>
</tr>

<tr>
<td>Azure 站台復原提供者</td>
<td>安裝在來源 VMM 伺服器上</td>
<td>安裝在來源和目標 VMM 伺服器上</td>
<td>安裝在來源和目標 VMM 伺服器上</td>
</tr>

<tr>
<td>Azure 復原服務代理程式</td>
<td>安裝在 VMM 雲端中的 Hyper-V 主機伺服器上</td>
<td>不需要</td>
<td>不需要</td>
</tr>

<tr>
<td>虛擬機器復原點</td>
<td><p>依時間設定復原點。</p> <p>指定復原點的保留時間 (0-24 小時)。第一個小時期間會使用公式 90/copy_frequency 建立復原點，之後每小時建立一次。</p></td>
<td><p>依數量設定復原點。</p> <p>指定應該保留的其他復原點數目 (0-15)。依預設每小時建立一個復原點。設為零時，只有最後一個復原點會儲存在複本 Hyper-V 主機伺服器上。</p></td>
<td>在陣列存放裝置設定中進行設定。</td>
</tr>

<tr>
<td>網路對應</td>
<td><p>將 VM 網站對應至 Azure 網路。</p> <p>網路對應可確保在相同來源 VM 網路中容錯移轉的所有虛擬機器，在容錯移轉之後仍可連接。此外，如果目標 Azure 網路上有網路閘道，則虛擬機器可以連接到內部部署虛擬機器。 </p><p>如果未啟用對應，則只有相同復原計畫中容錯移轉的虛擬機器，才能在容錯移轉到 Azure 之後彼此連接。</p></td>
<td><p>將來源 VM 網路對應至目標 VM 網路。</p> <p>網路對應可用來將複寫的虛擬機器放在最佳的 Hyper-V 主機伺服器上，並確保與來源 VM 網路相關聯的虛擬機器，在容錯移轉之後會與對應的目標網路相關聯。 </p><p>如果未啟用對應，則複寫的虛擬機器不會連接至網路。</p></td>
<td><p>將來源 VM 網路對應至目標 VM 網路。</p> <p>網路對應可確保與來源 VM 網路相關聯的虛擬機器，在容錯移轉之後會與對應的目標網路相關聯。 </p><p>如果未啟用對應，則複寫的虛擬機器不會連接至網路。</p></td>
</tr>

<tr>
<td>儲存體對應</td>
<td>不適用</td>
<td><p>將來源 VM 伺服器上的存放裝置分類對應至目標 VMM 伺服器上的存放裝置分類。</p> <p>如果啟用對應，則來源存放裝置分類中的虛擬機器硬碟在容錯移轉之後，將位於目標存放裝置分類中。</p><p>如果未啟用儲存體對應，則複寫的虛擬硬碟會儲存在目標 Hyper-V 主機伺服器上的預設位置。</p></td>
<td><p>在主要和次要站台的存放裝置陣列與集區之間進行對應。</p></td>
</tr>

</table>

<p>若有任何問題，請造訪 <a href="http://go.microsoft.com/fwlink/?LinkId=313628">Azure 復原服務論壇</a> (英文)。</p> 

<!--HONumber=49-->