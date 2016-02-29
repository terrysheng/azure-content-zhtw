<properties
	pageTitle="執行 Site Recovery 的 Hyper-V 容量規劃工具 | Microsoft Azure"
	description="本文包含有關使用 Azure Site Recovery 的 Hyper-V 容量規劃工具的指示"
	services="site-recovery"
	documentationCenter="na"
	authors="rayne-wiselman"
	manager="jwhit"
	editor="" />
<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery"
	ms.date="02/15/2016"
	ms.author="raynew" />

# 執行 Site Recovery 的 Hyper-V 容量規劃工具

在 Azure Site Recovery 部署的過程中，您需要找出複寫和頻寬需求。Site Recovery 的 Hyper-V 容量規劃工具可協助您找出 Hyper-V 虛擬機器複寫的複寫和頻寬需求。


本文說明如何執行 Hyper-V 容量規劃工具。此工具應該搭配其他容量規劃工具及 [Site Recovery 的容量規劃](site-recovery-capacity-planner.md)中說明的相關資訊一起使用。


## 開始之前

您會在主要網站中的 Hyper-V 伺服器或叢集節點上執行工具。若要執行 Hyper-V 主機伺服器需要的工具：

- 作業系統：Windows Server® 2012 或 Windows Server® 2012 R2
- 記憶體：20 MB (最低要求)
- CPU：5% 額外負荷 (最低要求)
- 磁碟空間：5 MB (最低要求)

執行工具之前，您必須準備主要網站。如果您在兩個內部部署網站之間複寫，且想要檢查頻寬，您也必須準備複本伺服器。


## 步驟 1：準備主要網站
1. 在主要網站上製作您要複寫的所有 Hyper-V 虛擬機器，和它們所在的 Hyper-V 主機/叢集清單。此工具每次可針對多個獨立主機或單一叢集執行，但是不能同時執行。也必須針對每個作業系統分別執行，因此您應該收集並記下 Hyper-V 伺服器，如下所示：

  - Windows Server ® 2012 獨立伺服器
  - Windows Server ® 2012 叢集
  - Windows Server ® 2012 R2 獨立伺服器
  - Windows Server ® 2012 R2 叢集

3. 在所有 Hyper-V 主機和叢集上啟用 WMI 遠端存取。在每個伺服器/叢集上執行此命令，確定已設定防火牆規則和使用者權限：

        netsh firewall set service RemoteAdmin enable

5. 在伺服器和叢集上啟用效能監視，如下所示：

  - 開啟 [Windows 防火牆] 與 [進階安全性] 嵌入式管理單元，然後啟用下列輸入規則：**COM+ 網路存取 (DCOM-IN)**，以及「遠端事件記錄檔管理」群組中的所有規則。

## 步驟 2：準備複本伺服器 (內部部署至內部部署複寫)

如果您要複寫至 Azure，就不需要執行這項操作。

我們建議您設定一部 Hyper-V 主機做為復原伺服器，以便虛擬 VM 可以複寫到這部主機以檢查頻寬。您可以略過這個步驟，但是如果不執行這項作業，就法測量頻寬。

1. 如果您想要使用叢集節點做為複本，請設定 Hyper-V 複本代理人：

	- 在 [**伺服器管理員**] 中，開啟 [**容錯移轉叢集管理員**]。
	- 連接到叢集，反白顯示叢集名稱，然後按一下 [動作] > [設定角色] 以開啟 [高可用性] 精靈。
	- 在 [選取角色] 中，選取 [Hyper-V 複本代理人]。在精靈中提供 [NetBIOS 名稱] 和 [IP 位址] 做為叢集的連接點 (稱為用戶端存取點)。會設定 [Hyper-V 複本代理人]，產生您應該記下的用戶端存取點名稱。
	- 確認 Hyper-V 複本代理人角色順利連線，而且可以進行叢集所有節點之間的容錯移轉。若要這樣做，請以滑鼠右鍵按一下角色，指向 [**移動**]，然後按一下 [**選取節點**]。選取節點 > [**確定**]。
	- 如果您使用憑證型驗證，請確定每個叢集節點與用戶端存取點都有安裝憑證。
2.  啟用複本伺服器：

	- 對於叢集開啟 [失敗叢集管理員]，連線到叢集，然後按一下 [角色] > 選取角色 > [複寫設定] > [啟用此叢集做為複本伺服器]。請注意，如果使用叢集做為複本，您也必須在主要網站的叢集上顯示 Hyper-V 複本代理人角色。
	- 若是獨立伺服器，請開啟 [Hyper-V 管理員]。在 [動作] 窗格中，按一下您想要啟用的伺服器的 [Hyper-V 設定]，然後在 [複寫組態] 中按一下 [啟用這台電腦做為複本伺服器]。
3. 設定驗證：

	- 在 [驗證和連接埠] 中選取如何驗證主要伺服器及驗證連接埠。如果您使用憑證，按一下 [選取憑證] 以選取其中一個。如果主要和復原 Hyper-V 主機都位於相同網域或信任的網域，則使用 Kerberos。針對不同的網域或工作群組部署使用憑證。
	- 在 [授權與存放裝置] 區段中，允許**任何**驗證 (主要) 伺服器將複寫資料傳送到這個複本伺服器。按一下 [**確定**] 或 [**套用**]。

	![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image1.png)

	- 執行 **netsh http show servicestate** 來檢查接聽程式是否針對您指定的通訊協定/連接埠執行：  
4. 設定防火牆：Hyper-V 安裝期間會建立防火牆規則，以允許預設連接埠的流量 (443 上的 HTTPS，80 上的 Kerberos)。啟用這些規則，如下所示：

		- Certificate authentication on cluster (443): **Get-ClusterNode | ForEach-Object {Invoke-command -computername \$\_.name -scriptblock {Enable-Netfirewallrule -displayname "Hyper-V Replica HTTPS Listener (TCP-In)"}}**
		- Kerberos authentication on cluster (80): **Get-ClusterNode | ForEach-Object {Invoke-command -computername \$\_.name -scriptblock {Enable-Netfirewallrule -displayname "Hyper-V Replica HTTP Listener (TCP-In)"}}**
		- Certificate authentication on standalone server: **Enable-Netfirewallrule -displayname "Hyper-V Replica HTTPS Listener (TCP-In)"**
		- Kerberos authentication on standalone server: **Enable-Netfirewallrule -displayname "Hyper-V Replica HTTP Listener (TCP-In)"**

## 步驟 3：執行容量規劃工具

在準備好您的主要網站並設定復原伺服器之後，就可以執行此工具。

1. 從 Microsoft 下載中心[下載](https://www.microsoft.com/download/details.aspx?id=39057)工具。
2. 從其中一個主要伺服器 (或主要叢集的其中一個節點) 執行工具。以滑鼠右鍵按一下 .exe 檔案，然後選擇 [ **以系統管理員身分執行**]。
3. 在 [開始之前] 指定您要收集資料的時間長度。建議在實際執行期間執行工具，以確保資料具有代表性。如果您只想要驗證網路連線，可以只收集一分鐘的資料。

	![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image2.png)

4. 在 [主要網站詳細資料] 中，針對獨立主機指定伺服器名稱或 FQDN，或針對叢集指定用戶端接受點的 FQDN、叢集名稱或叢集中的任何節點，然後按 [下一步]。此工具會自動偵測它執行的伺服器名稱。此工具會挑選可以監視指定伺服器的 VM。

	![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image3.png)

5. 在 [複本網站詳細資料] 中，如果您要複寫至 Azure 或次要資料中心，且尚未設定複本伺服器，請選取 [略過複本網站的相關測試]。如果您要複寫到次要資料中心，而且已經設定好複本，在 [伺服器名稱 (或) Hyper-V 複本代理人 CAP] 中輸入獨立伺服器的 FQDN，或叢集的用戶端存取點。

	![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image4.png)

6. 在 [擴充複本詳細資料] 中啟用 [略過擴充複本網站的相關測試]。它們不受 Site Recovery 支援。
7. 在 [選擇要複寫的 VM] 中，工具會根據您在 [主要網站詳細資料] 頁面上指定的設定，連接到伺服器或叢集，並且顯示主要伺服器上執行的 VM 和磁碟。請注意，已針對複寫啟用 VM，不會顯示未執行的項目。選取您想要收集度量的 VM。選取 VHD 也會自動收集 VM 的資料。
9. 如果您已設定複本伺服器或叢集，請在 [網路資訊] 中指定您認為將會用於主要和複本網站之間的近似 WAN 頻寬，如果您已設定憑證驗證，則選取憑證。

	![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image5.png)

10. 在 [摘要] 中檢查設定，然後按 [下一步] 以開始收集度量。工具的進度和狀態會顯示在 [計算容量] 頁面。此工具完成執行時，請按一下 [檢視報告] 以檢閱輸出。根據預設，報告和記錄檔會儲存在 **%systemdrive%\\Users\\Public\\Documents\\Capacity Planner**。

	![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image6.png)


## 步驟 4：解譯結果
以下是重要度量。您可以忽略未列在這裡的度量。它們與 Site Recovery 不相關。

### 內部部署至內部部署複寫
  - 主要主機計算、記憶體上複寫的影響
  - 主要、復原主機儲存體磁碟空間、IOPS 上複寫的影響
  - 差異複寫所需的總頻寬 (Mbps)
  - 主要主機和復原主機之間觀察到的網路頻寬 (Mbps)
  - 兩個主機/叢集之間作用中平行傳輸理想數目的建議

### 內部部署至 Azure 複寫
  - 主要主機計算、記憶體上複寫的影響
  - 主要主機儲存體磁碟空間、IOPS 上複寫的影響
  - 差異複寫所需的總頻寬 (Mbps)

## 其他資源

- 如需工具的詳細資訊，請參閱隨工具下載的文件。
- 觀看 Keith Mayer 的 [TechNet 部落格](http://blogs.technet.com/b/keithmayer/archive/2014/02/27/guided-hands-on-lab-capacity-planner-for-windows-server-2012-hyper-v-replica.aspx)上的工具的逐步解說。
- 取得內部部署至內部部署 Hyper-V 複寫的效能測試[結果](site-recovery-performance-and-scaling-testing-on-premises-to-on-premises.md)



## 後續步驟

容量規劃完成之後，您就可以開始部署 Site Recovery：

- [設定內部部署 VMM 網站和 Azure 之間的保護](site-recovery-vmm-to-azure.md)
- [設定內部部署 Hyper-V 網站和 Azure 之間的保護](site-recovery-hyper-v-site-to-azure.md)
- [設定兩個內部部署 VMM 站台之間的保護](site-recovery-vmm-to-vmm.md)
- [利用 SAN 設定兩個內部部署 VMM 網站之間的保護](site-recovery-vmm-san.md)
- [利用單一 VMM 伺服器設定保護](site-recovery-single-vmm.md)

<!---HONumber=AcomDC_0218_2016-->