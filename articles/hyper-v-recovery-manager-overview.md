<properties linkid="Azure Site Recovery Overview" urlDisplayName="Azure Site Recovery Overview" pageTitle="Azure Site Recovery Overview" metaKeywords="Azure Site Recovery, on-premises, clouds, Azure, VMM, Hyper-V" description="Deploy Azure Site Recovery to protect virtual machines on Hyper-V host servers that are located in VMM clouds. You can deploy from one on-premises site to another, or from an on-premises site to Azure." metaCanonical="" umbracoNaviHide="0" disqusComments="1" title="Azure Site Recovery Overview" editor="jimbe" manager="cfreeman" authors="raynew" />

<tags ms.service="site-recovery" ms.workload="backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="raynew"></tags>

# Azure 站台復原概觀

<div class="dev-callout">

部署 Azure 站台復原來保護在 System Center Virtual Machine Manager (VMM) 雲端中的 Hyper-V 主機伺服器上執行的虛擬機器。您有數種方式可部署 Azure 站台復原：

-   **內部部署至內部部署保護** - 將虛擬機器從一個內部部署站台複寫到另一個內部部署站台。您可以在 Azure 站台復原保存庫中設定和啟用保護設定。虛擬機器資料會從來源 Hyper-V 主機伺服器複寫到目標主機伺服器。Azure 站台復原會協調整個過程。如需此案例的教學課程，請參閱[開始使用 Azure 站台復原：內部部署至內部部署保護][]。
-   **內部部署至 Azure 保護** - 將虛擬機器從內部部署站台複寫到 Microsoft Azure。您可以在 Azure 站台復原保存庫中設定和啟用保護設定。Azure 站台復原會協調整個過程，而複寫的虛擬機器資料會儲存在 Azure 儲存體中。如需此案例的教學課程，請參閱[開始使用 Azure 站台復原：內部部署至 Azure 保護][]。

下表總結和比較這兩種部署選項。

<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">功能</th>
<th align="left">內部部署至 Azure</th>
<th align="left">內部部署至內部部署</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">資料至 Azure 站台復原</td>
<td align="left">雲端中繼資料會傳送至 Azure 站台復原。複寫的資料會儲存在 Azure 儲存體中。</td>
<td align="left">雲端中繼資料會傳送至 Azure 站台復原。複寫的資料會儲存在目標 Hyper-V 主機伺服器上。</td>
</tr>
<tr class="even">
<td align="left">保存庫必要條件</td>
<td align="left"><p>保存庫需要一個符合<a href="%20http://go.microsoft.com/fwlink/?LinkId=386485">必要條件和支援</a>的憑證。</p>
<p>保存庫金鑰會自動產生。此金鑰可確保 VMM 伺服器上的 Azure 站台復原提供者與 Azure 站台復原之間的流量完整性。</p></td>
<td align="left"><p>保存庫需要一個符合<a href="%20http://go.microsoft.com/fwlink/?LinkId=386485">必要條件和支援</a>的憑證。</p>
<p>保存庫金鑰會自動產生。此金鑰可確保 VMM 伺服器上的 Azure 站台復原提供者與 Azure 站台復原之間的流量完整性。</p></td>
</tr>
<tr class="odd">
<td align="left">複寫</td>
<td align="left">虛擬機器會從內部部署 Hyper-V 伺服器複寫到 Azure 儲存體。</td>
<td align="left">虛擬機器會從一個內部部署 Hyper-V 伺服器，複寫到另一個內部部署 Hyper-V 伺服器。</td>
</tr>
<tr class="even">
<td align="left">虛擬機器儲存體</td>
<td align="left">Azure 儲存體中儲存的虛擬機器硬碟</td>
<td align="left">Hyper-V 主機上儲存的虛擬機器硬碟。</td>
</tr>
<tr class="odd">
<td align="left">Azure 儲存體</td>
<td align="left"><p>需要 Azure 儲存體帳戶。</p>
<p>帳戶應該啟用異地複寫、與 Azure 站台復原服務位於相同的區或，且與相同的訂閱相關聯。</p></td>
<td align="left">不適用</td>
</tr>
<tr class="even">
<td align="left">VMM 伺服器</td>
<td align="left">只需要來源 VMM 伺服器</td>
<td align="left">需要來源和目標 VMM 伺服器，且各伺服器上至少有一個雲端，或兩個雲端共用單一 VMM 伺服器</td>
</tr>
<tr class="odd">
<td align="left">VMM 伺服器 System Center 版本</td>
<td align="left">System Center 2012 R2</td>
<td align="left"><p>System Center 2012 (含 SP1)</p>
<p>System Center 2012 R2</p></td>
</tr>
<tr class="even">
<td align="left">Azure 站台復原提供者</td>
<td align="left">安裝在來源 VMM 伺服器上</td>
<td align="left">安裝在來源和目標 VMM 伺服器上</td>
</tr>
<tr class="odd">
<td align="left">Azure 復原服務代理程式</td>
<td align="left">安裝在 VMM 雲端中的 Hyper-V 主機伺服器上</td>
<td align="left">不需要</td>
</tr>
<tr class="even">
<td align="left">虛擬機器復原點</td>
<td align="left"><p>依時間設定復原點。</p>
<p>指定復原點的保留時間 (0-24 小時)。第一個小時期間會使用公式 90/copy_frequency 建立復原點，之後每小時建立一次。</p></td>
<td align="left"><p>依數量設定復原點。</p>
<p>指定應該保留的其他復原點數目 (0-15)。依預設每小時建立一個復原點。設為零時，只有最後一個復原點會儲存在複本 Hyper-V 主機伺服器上。</p></td>
</tr>
<tr class="odd">
<td align="left">網路對應</td>
<td align="left"><p>將 VM 網站對應至 Azure 網路。</p>
<p>網路對應可確保在相同來源 VM 網路中容錯移轉的所有虛擬機器，在容錯移轉之後仍可連接。此外，如果目標 Azure 網路上有網路閘道，則虛擬機器可以連接到內部部署虛擬機器。</p>
<p>如果未啟用對應，則只有相同復原計畫中容錯移轉的虛擬機器，才能在容錯移轉到 Azure 之後彼此連接。</p></td>
<td align="left"><p>將來源 VM 網路對應至目標 VM 網路。</p>
<p>網路對應可用來將複寫的虛擬機器放在最佳的 Hyper-V 主機伺服器上，並確保與來源 VM 網路相關聯的虛擬機器，在容錯移轉之後會與對應的目標網路相關聯。</p>
<p>如果未啟用對應，則複寫的虛擬機器不會連接至網路。</p></td>
</tr>
<tr class="even">
<td align="left">儲存體對應</td>
<td align="left">不適用</td>
<td align="left"><p>將來源 VM 伺服器上的儲存體分類對應至目標 VMM 伺服器上的儲存體分類。</p>
<p>如果啟用對應，則來源儲存體分類中的虛擬機器硬碟在容錯移轉之後，將位於目標儲存體分類中。</p>
<p>如果未啟用儲存體對應，則複寫的虛擬硬碟會儲存在目標 Hyper-V 主機伺服器上的預設位置。</p></td>
</tr>
</tbody>
</table>

若有任何問題，請造訪 [Azure 復原服務論壇][] (英文)。

</div>

  [開始使用 Azure 站台復原：內部部署至內部部署保護]: http://go.microsoft.com/fwlink/?LinkId=398765
  [開始使用 Azure 站台復原：內部部署至 Azure 保護]: http://go.microsoft.com/fwlink/?LinkId=398764
  [必要條件和支援]: %20http://go.microsoft.com/fwlink/?LinkId=386485
  [Azure 復原服務論壇]: http://go.microsoft.com/fwlink/?LinkId=313628
