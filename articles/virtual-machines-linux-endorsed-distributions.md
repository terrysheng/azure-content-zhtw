<properties 
	pageTitle="在 Azure 中 Linux 的背書散發套件" 
	description="了解在 Azure 背書散發套件上的 Linux，包括 Ubuntu、OpenLogic 和 SUSE 的準則。" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="szarkos" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/05/2014" 
	ms.author="szark"/>





#經 Azure 背書之配送映像上的 Linux

Azure 映像庫上的配送映像是由下列合作夥伴提供，而我們仍繼續與不同的 Linux 社群合作來提供更多經過背書的配送映像。同時，針對映像庫中未提供的散發套件，您可以隨時依照[本頁](virtual-machines-linux-create-upload-vhd.md)上的方針，來使用您自己的 Linux。

## Canonical ##
 
[http://www.ubuntu.com/cloud/azure](http://www.ubuntu.com/cloud/azure)

Canonical 對工程與開放社群的治理推動了 Ubuntu 在用戶端、伺服器和雲端運算 (包括消費者的個人雲端服務) 方面的成功。Canonical 對 Ubuntu 的願景是，透過對手機、平板電腦、電視和桌上型電腦而言皆風格一致的一系列介面，成為從手機到雲端皆適用的統一免費平台，讓 Ubuntu 成為從公用雲端供應商到消費性電子產品製造商等各種機構眼中的首選，並成為個人技術人員的愛用平台。

Canonical 在全球各地皆有開發人員和工程中心，此獨特的地位使其能和硬體製造商、內容供應商和軟體開發商合作，將 Ubuntu 解決方案帶到從電腦、伺服器到掌上型裝置等等的市場。

## OpenLogic ##
 
[http://www.openlogic.com/azure](http://www.openlogic.com/azure)

就雲端與資料中心而言，OpenLogic 是企業開放原始碼解決方案的領導供應商。OpenLogic 協助了各行各業數百家頂尖企業來安全取得、支援和控制開放原始碼軟體。OpenLogic 以 OpenLogic 專家社群為後盾，為 600 個開放原始碼套件提供商業級技術支援與保障，包括提供 CentOS 的企業級支援，以及成為在 Azure 上提供 CentOS 型映像的產品發佈合作夥伴。

## Oracle##
[http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html](http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)

Oracle 的策略是提供公用與私人雲端適用的各種解決方案，同時讓客戶能夠靈活選擇在 Oracle 雲端和其他雲端中部署 Oracle 軟體的方式。Oracle 與 Microsoft 的合作，讓客戶能夠在安心知道已經過認證和會受到 Oracle 支援的情況下，於 Microsoft 公用與私人雲端中部署 Oracle 軟體。Oracle 對 Oracle 公用與私人雲端解決方案的承諾與投資沒有改變。

##SUSE##
 
[http://www.suse.com/suse-linux-enterprise-server-on-azure](http://www.suse.com/suse-linux-enterprise-server-on-azure)

Azure 上的 SUSE Linux Enterprise Server 是一個經證實可為雲端運算提供優異可靠性與安全性的平台。SUSE 彈性的 Linux 平台可與 Azure 雲端服務緊密整合，提供容易管理的雲端環境。隨著 SUSE Linux Enterprise Server 有 1,800 多家獨立軟體廠商提供 9,200 多個認證應用程式，SUSE 可確保資料中心內所執行受支援的工作負載可以安心部署於 Azure 上。

## 支援的版本 ##

下表顯示不同的配送版本、Linux Integration Services (LIS) 驅動程式，以及經測試過可在 Azure 上運作的 Azure Linux 代理程式版本。依預設，LIS 驅動程式會內建於散發套件的核心，或您可以自[此處](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409)取得。Linux 代理程式版本可從散發套件的封裝儲存機制或在 [Github](https://github.com/azure/walinuxagent) 上取得。

有些散發套件/核心版本需要有 [Linux 核心相容性修補程式](http://go.microsoft.com/fwlink/?LinkID=403027&clcid=0x409)才能在 Azure 中適當運作，在此情況下，表中也包含這類修補程式的連結。

<table border="1" width="600">
  <tr bgcolor="#E9E7E7">
		<th>配送映像</th>		
	    <th>版本</th>
	    <th>驅動程式</th>
		<th>核心相容性修補程式</th>
		<th>代理程式</th>
			</tr>
	<tr>
		<th>  Canonical Ubuntu </th>
		<td> Ubuntu 12.04.1+、14.04 和 14.10 </td>
		<td>在核心中</td>
		<td><a href="http://go.microsoft.com/fwlink/?LinkID=275152&amp;clcid=0x409">只有 12.04 或 12.04.01 才需要</a></td>
		<td>套件：在 walinuxagent 下的封裝儲存機制中  <br />
			來源:<a href="http://go.microsoft.com/fwlink/p/?LinkID=250998">GitHub</a></td>
			</tr>
	<tr>
		<th> OpenLogic 的 CentOS </th>
		<td> CentOS 6.3+</td>
	    <td>CentOS 6.3：<a href="http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409">LIS 驅動程式</a>; CentOS 6.4+ 驅動程式：在核心中</td>
		<td><a href="http://go.microsoft.com/fwlink/?LinkID=275153&amp;clcid=0x409">只有 6.3 才需要</a></td>
		<td>套件：在 walinuxagent 下的 <a href="http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/">Open Logic 封裝儲存機制 </a><br />
			來源:<a href="http://go.microsoft.com/fwlink/p/?LinkID=250998">GitHub</a></td>
 		

	</tr>
	<tr>
		<th> <a href="https://coreos.com/docs/running-coreos/cloud-providers/azure/">CoreOS</a> </th>
		<td> 494.4.0+ </td>
        <td> 在核心中 </td>
		<td> N/A </td>
		<td> 來源:<a href="https://github.com/coreos/coreos-overlay/tree/master/app-emulation/wa-linux-agent">GitHub</a></td>
		
	</tr>
	<tr>
		<th> Oracle Linux </th>
		<td> 6.4+</td>
        <td>在核心中</td>
		<td>N/A</td>
		<td>套件：在存放庫中，名稱：WALinuxAgent<br />
			來源:<a href="http://go.microsoft.com/fwlink/p/?LinkID=250998">GitHub</a></td>
		
	</tr>
	<tr>
		<th> SUSE Linux Enterprise </th>
		<td> SLES 11 SP3+</td>
        <td>在核心中</td>
		<td>N/A</td>
		<td>套件：在 <a href="https://build.opensuse.org/project/show/Cloud:Tools" >雲端：工具</a> 儲存機制，名稱：WALinuxAgent<br />
			原始程式碼： <a href="http://go.microsoft.com/fwlink/p/?LinkID=250998">GitHub</a></td>
		
	</tr>
	<tr>
		<th> openSUSE </th>
		<td> openSUSE 13.1+</td>
		<td>在核心中</td>
		<td>N/A</td>
		<td>套件：在 <a href="https://build.opensuse.org/project/show/Cloud:Tools" >雲端：工具</a> 儲存機制，名稱：WALinuxAgent<br />
			原始程式碼：<a href="http://go.microsoft.com/fwlink/p/?LinkID=250998">GitHub</a></td>
		
	</tr>
</table>



<!--HONumber=42-->
