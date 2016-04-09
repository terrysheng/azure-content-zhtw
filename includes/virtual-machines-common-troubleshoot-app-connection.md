


如果您無法存取在 Azure 虛擬機器上執行的應用程式，本文將說明以井然有序的方式，釐清問題的來源並更正。

> [AZURE.NOTE]  如需連接到 Azure 虛擬機器的說明，請參閱[疑難排解以 Windows 為基礎之 Azure 虛擬機器的遠端桌面連線](virtual-machines-windows-troubleshoot-rdp-connection.md)或[疑難排解以 Linux 為基礎之 Azure 虛擬機器的安全殼層 (SSH) 連線](virtual-machines-linux-troubleshoot-ssh-connection.md)。

如果在本文章中有任何需要協助的地方，您可以連絡 [MSDN Azure 和 Stack Overflow 論壇](https://azure.microsoft.com/support/forums/)上的 Azure 專家。或者，您也可以提出 Azure 支援事件。請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後按一下**取得支援**。


有四個主要區域來疑難排解在 Azure 虛擬機器執行上之應用程式的存取。

![](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access1.png)

1.	在 Azure 虛擬機器上執行的應用程式。
2.	Azure 虛擬機器
3.	Azure 雲端服務的端點包含虛擬機器 (適用於傳統部署模型中的虛擬機器)、輸入 NAT 規則 (適用於資源管理員部署模型中的虛擬機器)，以及網路安全性群組。
4.	您的網際網路邊緣裝置。

針對正透過站對站 VPN 或 ExpressRoute 連線存取應用程式的用戶端電腦，可能造成問題的主要區域是應用程式和 Azure 虛擬機器。若要判斷問題的來源並更正，請遵循下列步驟。

## 步驟 1：您是否可以從目標 VM 存取應用程式？

嘗試使用適當的用戶端程式，從執行所在的 VM 存取應用程式。使用本機主機名稱、本機 IP 位址或迴路位址 (127.0.0.1)。

![](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access2.png)

例如，如果應用程式是網頁伺服器，則在 VM 上執行瀏覽器，並嘗試存取在 VM 上託管的網頁。

如果您可以存取應用程式，請移至[步驟 3](#step2)。

如果您無法存取應用程式，請檢查下列項目：

- 應用程式正在目標虛擬機器上執行。
- 應用程式正在接聽預期的 TCP 和 UDP 連接埠。

在 Windows 和 Linux 虛擬機器兩者上，使用 **netstat -a** 命令顯示作用中的接聽連接埠。檢查應用程式應該接聽之預期連接埠的輸出。重新啟動應用程式，或將它設定使用預期的連接埠 (如果有需要)。

## <a id="step2"></a>步驟 2：您可以從相同虛擬網路中的另一部虛擬機器存取應用程式嗎？

使用 VM 的主機名稱或其 Azure 指派的公用、私人或提供者 IP 位址，嘗試存取不同 VM，但相同虛擬網路中的應用程式。若為使用傳統部署模型建立的虛擬機器，請勿使用雲端服務的公用 IP 位址。

![](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access3.png)

例如，如果應用程式是網頁伺服器，請嘗試在相同虛擬網路中的其他 VM 上使用瀏覽器存取網頁。

如果您可以存取應用程式，請移至[步驟 3](#step3)。

如果您無法存取應用程式，請檢查下列項目：

- 目標 VM 上的主機防火牆允許輸入要求與輸出回應的流量。
- 在目標 VM 上執行的入侵偵測或網路監視軟體允許流量。
- 網路安全性群組允許流量。
- 在您的 VM 中，測試 VM 與 VM 間的路徑執行的個別元件 (例如負載平衡器或防火牆) 允許流量。

在 Windows 虛擬機器上，請使用「具有進階安全性的 Windows 防火牆」判斷防火牆規則是否排除了您應用程式的輸入與輸出流量。

## <a id="step3"></a>步驟 3：您可以從位於虛擬網路之外，但連接到與您電腦不同網路的電腦存取應用程式嗎？

請嘗試從位於虛擬網路之外執行應用程式所在 VM 的電腦存取應用程式，但該電腦與您原始的用戶端電腦在不同的網路上。

![](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access4.png)

例如，如果應用程式是 Web 伺服器，請嘗試從虛擬網路外的電腦執行瀏覽器來存取網頁。

如果您無法存取應用程式，請檢查下列項目：

- 若為使用傳統部署模型建立的 VM，其 VM 的端點組態允許連入流量，特別是通訊協定 (TCP 或 UDP) 和公用與私人連接埠號碼。如需詳細資訊，請參閱[如何設定虛擬機器的端點](virtual-machines-windows-classic-setup-endpoints.md)。
- 若為使用傳統部署模型建立的 VM，其端點上的存取控制清單 (ACL) 不會阻擋來自網際網路的連入流量。如需詳細資訊，請參閱[如何設定虛擬機器的端點](virtual-machines-windows-classic-setup-endpoints.md)。
- 若為使用資源管理員部署模型建立的 VM，其 VM 的輸入 NAT 規則組態允許連入流量，特別是通訊協定 (TCP 或 UDP) 和公用與私人連接埠號碼。
- 網路安全性群組允許輸入要求與輸出回應的流量。如需詳細資訊，請參閱[什麼是網路安全性群組 (NSG)？](../virtual-network/virtual-networks-nsg.md)。

如果虛擬機器或端點是負載平衡集的成員：

- 請確認探查通訊協定 (TCP 或 UDP) 和連接埠號碼正確。
- 如果探查通訊協定和連接埠與負載平衡集通訊協定和連接埠不同：
	- 請確認應用程式正在接聽探查通訊協定 (TCP 或 UDP) 和連接埠號碼 (在目標 VM 上使用 **netstat –a**)。
	- 目標 VM 上的主機防火牆允許輸入探查要求與輸出探查回應的流量。

如果您可以存取應用程式，請確定您的網際網路邊緣裝置允許：

- 從您的用戶端電腦輸出到 Azure 虛擬機器的應用程式要求流量。
- 來自 Azure 虛擬機器的輸入應用程式回應流量。

## 疑難排解端點連線能力問題

如果您在連接到端點時遇到問題，例如連接遠端桌面端點時，您可以嘗試依照下列一般疑難排解步驟執行：

- 重新啟動虛擬機器
- 重新建立端點
- 從其他位置連接
- 調整虛擬機器的大小
- 重新建立虛擬機器

如需詳細資訊，請參閱 [疑難排解端點連接能力 (RDP/SSH/HTTP 等失敗問題)](https://social.msdn.microsoft.com/Forums/azure/zh-TW/538a8f18-7c1f-4d6e-b81c-70c00e25c93d/troubleshooting-endpoint-connectivity-rdpsshhttp-etc-failures?forum=WAVirtualMachinesforWindows)。



## 其他資源

[疑難排解以 Windows 為基礎之 Azure 虛擬機器的遠端桌面連線](virtual-machines-windows-troubleshoot-rdp-connection.md)

[疑難排解以 Linux 為基礎之 Azure 虛擬機器的安全殼層 (SSH) 連線](virtual-machines-linux-troubleshoot-ssh-connection.md)

<!---HONumber=AcomDC_0323_2016-->