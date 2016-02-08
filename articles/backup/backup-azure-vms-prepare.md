<properties
	pageTitle="準備環境以備份 Azure 虛擬機器 | Microsoft Azure"
	description="確認在 Azure 中備份虛擬機器的環境已準備就緒"
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor=""
	keywords="備份；備份；"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/22/2016"
	ms.author="trinadhk; jimpark; markgal;"/>


# 準備環境以備份 Azure 虛擬機器

備份 Azure 虛擬機器 (VM) 之前，您需要完成這些必要條件。如果您已經準備您的環境以進行備份，您就可以開始[備份您的 VM](backup-azure-vms.md)。否則，請繼續進行下列所有步驟，以確保您的環境準備就緒。

## 1\.備份保存庫

![備份保存庫](./media/backup-azure-vms-prepare/step1.png)

若要開始備份 Azure 虛擬機器，首先您必須建立備份保存庫。保存庫是一個實體，會儲存所有時間以來建立的所有備份和復原點。保存庫也包含備份虛擬機器時將套用的備份原則。

此影像顯示各種「Azure 備份」實體之間的關係：![「Azure 備份」實體及其關係](./media/backup-azure-vms-prepare/vault-policy-vm.png)

若要建立備份保存庫：

1. 登入 [Azure 入口網站](http://manage.windowsazure.com/)。

2. 按一下 [**新增**] > [**資料服務**] > [**復原服務**] > [**備份保存庫**] > [**快速建立**]。如果您有多個與組織帳戶相關聯的訂用帳戶，請選擇與備份保存庫相關聯的正確訂用帳戶。每個 Azure 訂用帳戶都可以擁有多個備份保存庫，用以組織受保護的虛擬機器。

3. 在 [**名稱**] 中，輸入保存庫的易記識別名稱。每個訂用帳戶皆需為唯一名稱。

4. 在 [**區域**] 中，選取保存庫的地理區域。保存庫必須與您想要保護的虛擬機器位於相同區域。如果您的虛擬機器位於各個不同區域，請在每個區域都建立保存庫。儲存備份資料時，不需要指定儲存體帳戶，備份保存庫和「Azure 備份」服務會自動處理此作業。

    ![建立備份保存庫](./media/backup-azure-vms-prepare/backup_vaultcreate.png)

5. 按一下 [建立保存庫]。要等備份保存庫建立好，可能需要一些時間。監視位於入口網站底部的狀態通知。

    ![建立保存庫快顯通知](./media/backup-azure-vms-prepare/creating-vault.png)

6. 將會顯示一則確認已順利建立保存庫的訊息。該保存庫將會在 [復原服務] 頁面中以 [使用中] 狀態列出。建立保存庫之後，請務必立即選擇適當的儲存體備援選項。進一步了解[在備份保存庫中設定儲存體備援選項](backup-configure-vault.md#azure-backup---storage-redundancy-options)。

    ![備份保存庫的清單](./media/backup-azure-vms-prepare/backup_vaultslist.png)

7. 按一下備份保存庫以前往 [快速入門] 頁面，此頁面會顯示備份 Azure 虛擬機器的相關指示。

    ![「儀表板」頁面上的虛擬機器備份指示](./media/backup-azure-vms-prepare/vmbackup-instructions.png)



## 2\.網路連線

![網路連線](./media/backup-azure-vms-prepare/step2.png)

備份擴充功能必須連線至 Azure 公用 IP 位址才能正常運作，因為它會傳送命令給「Azure 儲存體」端點 (HTTP URL) 以管理 VM 的快照。若無適當的網際網路連線，這些來自 VM 的 HTTP 要求將會逾時，而備份作業將會失敗。

### NSG 的網路限制

如果您的部署具有適當的存取限制 (例如，透過網路安全性群組 (NSG))，您就必須採取額外的步驟來確保傳送至備份保存庫的備份流量不受影響。

有兩種方式可提供備份流量的路徑：

1. 將 [Azure 資料中心 IP 範圍](http://www.microsoft.com/zh-TW/download/details.aspx?id=41653)列入允許清單。
2. 部署 HTTP Proxy 來路由傳送流量。

管理能力、精確控制和成本之間必須有所取捨。

|選項|優點|缺點|
|------|----------|-------------|
|選項 1：將 IP 範圍列入允許清單| 沒有額外的成本。<br><br>如需在某個 NSG 中開啟存取權，請使用 <i>Set-AzureNetworkSecurityRule</i> Cmdlet。 | 由於受影響的 IP 範圍會隨著時間改變，因此難以管理。<br>提供整個 Azure 的存取權，而不只是「儲存體」的存取權。|
|選項 2：HTTP Proxy| 可在 Proxy 中精確控制允許的儲存體 URL。<br>為 VM 提供單一網際網路存取點。<br>不受 Azure IP 位址變更影響。| 使用 Proxy 軟體執行 VM 時的額外成本。|

### 使用 HTTP Proxy 進行 VM 備份
備份 VM 時，會使用 HTTPS API 將快照管理命令從備份擴充功能傳送到 Azure 儲存體。此流量必須透過 Proxy 從擴充功能路由傳送，因為只有 Proxy 會被設定為具有公用網際網路存取權。

>[AZURE.NOTE] 對於應該使用什麼 Proxy 軟體，並無任何建議。請務必挑選與下面設定步驟相容的 Proxy。

在下面的範例中，必須將「應用程式 VM」設定為針對前往公用網際網路的所有 HTTP 流量使用 Proxy VM。必須將 Proxy VM 設定為允許來自虛擬網路中 VM 的連入流量。最後，NSG (名為 *NSG-lockdown*) 需要新的安全性規則，以允許來自 Proxy VM 的輸出網際網路流量。

![包含 HTTP Proxy 部署圖表的 NSG](./media/backup-azure-vms-prepare/nsg-with-http-proxy.png)

**A) 允許連出網路連線：**

1. 若為 Windows 電腦，請在提高權限的命令提示字元中執行下列命令：

	```
	netsh winhttp set proxy http://<proxy IP>:<proxy port>
	```

	這會設定一個整部機器的 Proxy 設定，並用於任何連出 HTTP/HTTPS 流量。

2. 針對 Linux 電腦，請將下面一行新增至 ```/etc/environment``` 檔案：

 	```
 	http_proxy=http://<proxy IP>:<proxy port>
 	```

	將下列幾行新增至 ```/etc/waagent.conf``` 檔案：

	```
HttpProxy.Host=<proxy IP>
HttpProxy.Port=<proxy port>
```

**B) 在 Proxy 伺服器上允許連入連線：**

1. 在 Proxy 伺服器上開啟「Windows 防火牆」。在 [輸入規則] 上按一下滑鼠右鍵，然後按一下 [新增規則]。

	![開啟防火牆](./media/backup-azure-vms-prepare/firewall-01.png)

	![建立新的規則](./media/backup-azure-vms-prepare/firewall-02.png)
2. 在 [新增輸入規則精靈] 中，針對 [規則類型] 選擇 [自訂] 選項，然後按 [下一步]。在選取 [程式] 的頁面上，選擇 [所有程式]，然後按 [下一步]。

3. 在 [通訊協定及連接埠] 頁面上，使用下表中的輸入值，然後按 [下一步]：

	![建立新的規則](./media/backup-azure-vms-prepare/firewall-03.png)

| 輸入欄位 | 值 |
| --- | --- |
| 通訊協定類型 | TCP |
| 本機連接埠 | 從下拉式清單中，選取 [特定連接埠]。在文字方塊中，輸入已設定的 ```<Proxy Port>```。 |
| 遠端連接埠 | 從下拉式清單中，選取 [所有連接埠]。 |

在精靈的其餘部分，按一下直到結束為止並指定此規則的名稱。

**C) 新增 NSG 例外規則：**

在 Azure PowerShell 命令提示字元中，輸入下列命令：

```
Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
```

此命令會新增 NSG 例外，以允許從 10.0.0.5 上任何連接埠傳輸至 80 (HTTP) 或 443 (HTTPS) 連接埠上任何網際網路位址的 TCP 流量。如果您需要叫用公用網際網路中的特定連接埠，請務必一併將該連接埠新增至 ```-DestinationPortRange```。

*務必以適合您的部署的詳細資料取代範例中的名稱。*

## 3\.VM 代理程式

![VM 代理程式](./media/backup-azure-vms-prepare/step3.png)

備份 Azure 虛擬機器之前，您應該先確定虛擬機器上已正確安裝 Azure VM 代理程式。由於 VM 代理程式在虛擬機器建立時為選擇性元件，因此佈建虛擬機器之前，請先確定已選取 VM 代理程式的核取方塊。

### 手動安裝和更新

從 Azure 資源庫建立的 VM 中已經有 VM 代理程式。不過，從內部部署資料中心移轉的虛擬機器不會安裝 VM 代理程式。對於這類 VM，必須明確安裝 VM 代理程式。深入了解[在現有 VM 上安裝 VM 代理程式](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx)。

| **作業** | **Windows** | **Linux** |
| --- | --- | --- |
| 安裝 VM 代理程式 | <li>下載並安裝[代理程式 MSI](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)。您需要有系統管理員權限，才能完成安裝。<li>[更新 VM 屬性](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx)以表示已安裝代理程式。 | <li>從 GitHub 安裝最新的 [Linux 代理程式](https://github.com/Azure/WALinuxAgent)。您需要有系統管理員權限，才能完成安裝。<li> [更新 VM 屬性](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx)以表示已安裝代理程式。 |
| 更新 VM 代理程式 | 更新 VM 代理程式與重新安裝 [VM 代理程式二進位檔](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)一樣簡單。<br><br>確定在更新 VM 代理程式時，沒有任何執行中的備份作業。 | 請遵循[更新 Linux VM 代理程式](../virtual-machines-linux-update-agent.md)上的指示。<br><br>確定在更新 VM 代理程式時，沒有任何執行中的備份作業。 |
| 驗證 VM 代理程式安裝 | <li>瀏覽至 Azure VM 中的 *C:\\WindowsAzure\\Packages* 資料夾。<li>您應該會發現有 WaAppAgent.exe 檔案。<li> 在該檔案上按一下滑鼠右鍵，移至 [屬性]，然後選取 [詳細資料] 索引標籤。[產品版本] 欄位應為 2.6.1198.718 或更新的版本。| - |


深入了解 [VM 代理程式](https://go.microsoft.com/fwLink/?LinkID=390493&clcid=0x409)和[如何安裝](https://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/)。

### 備份擴充功能

為了備份虛擬機器，「Azure 備份」服務會安裝 VM 代理程式的擴充功能。Azure 備份服務無需使用者介入，即可順暢地升級和修補備份擴充功能。

如果 VM 正在執行，表示已安裝備份擴充功能。執行中的 VM 也提供了取得應用程式一致復原點的絕佳機會。不過，即使 VM 已關閉而無法安裝擴充功能 (亦稱為離線 VM)，「Azure 備份」服務仍會繼續備份 VM。在此情況下，復原點將如前面所述為「當機時保持一致」。


## 限制

- 不支援備份以「Azure 資源管理員」為基礎 (亦稱為 IaaS V2) 的虛擬機器。
- 不支援備份具有 16 個以上資料磁碟的虛擬機器。
- 不支援使用進階儲存體來備份虛擬機器。
- 不支援備份具有保留的 IP 且沒有已定義之端點的虛擬機器。
- 不支援在還原期間取代現有的虛擬機器。先刪除現有的虛擬機器及任何相關聯的磁碟，然後從備份還原資料。
- 不支援跨區域備份和還原。
- Azure 的所有公用區域皆支援使用「Azure 備份」服務來備份虛擬機器 (請參閱支援的區域[檢查清單](https://azure.microsoft.com/regions/#services))。如果您尋找的區域目前不受支援，在建立保存庫期間，該區域就不會顯示在下拉式清單中。
- 只有特定的作業系統版本才支援使用「Azure 備份」服務來備份虛擬機器：
  - **Linux**：請參閱[經 Azure 背書的散發套件清單](../virtual-machines/virtual-machines-linux-endorsed-distributions.md)。只要虛擬機器上有 VM 代理程式，其他「攜帶您自己的 Linux」散發套件應該也可以運作。
  - **Windows Server**：不支援比 Windows Server 2008 R2 更舊的版本。
- 只有透過 PowerShell 才支援還原屬於多網域控制站 (DC) 組態的 DC VM。進一步了解[還原多 DC 網域控制站](backup-azure-restore-vms.md#restoring-domain-controller-vms)。
- 僅支援透過 PowerShell 還原具有以下特殊網路組態的虛擬機器。藉由使用 UI 中的還原工作流程來建立的 VM 在還原作業完成之後，將不會具有這些網路組態。若要深入了解，請參閱[還原具有特殊網路組態的 VM](backup-azure-restore-vms.md#restoring-vms-with-special-netwrok-configurations)。
	- 負載平衡器組態下的虛擬機器 (內部與外部)
	- 具有多個保留的 IP 位址的虛擬機器
	- 具有多個網路介面卡的虛擬機器

## 有疑問嗎？
如果您有問題，或希望我們加入任何功能，請[傳送意見反應給我們](http://aka.ms/azurebackup_feedback)。

## 後續步驟

- [規劃 VM 備份基礎結構](backup-azure-vms-introduction.md)
- [備份虛擬機器](backup-azure-vms.md)
- [管理虛擬機器備份](backup-azure-manage-vms.md)

<!---HONumber=AcomDC_0128_2016-->