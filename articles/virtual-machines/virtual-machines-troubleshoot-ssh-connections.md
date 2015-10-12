<properties
	pageTitle="無法透過 SSH 連線到 Azure VM | Microsoft Azure"
	description="疑難排解執行 Linux 的 Azure 虛擬機器的安全殼層 (SSH) 連線"
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="top-support-issue,azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/25/2015"
	ms.author="dkshir"/>

# 疑難排解以 Linux 為基礎之 Azure 虛擬機器的安全殼層 (SSH) 連線

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]本文說明如何在以傳統部署模型，或以資源管理員部署模型建立的虛擬機器上疑難排解 SSH 連線。


有許多原因可能導致以 Linux 為基礎的 Azure 虛擬機器 SSH 失敗。本文將協助您找出原因並更正失敗。

> [AZURE.NOTE]本文僅適用於執行 Linux 之 Azure 虛擬機器。如需疑難排解執行 Windows 之 Azure 虛擬機器的連線，請參閱[這篇文章](virtual-machines-troubleshoot-remote-desktop-connections.md)。

## 連絡 Azure 客戶支援

如果在本文章中有任何需要協助的地方，您可以連絡 [MSDN Azure 和堆疊溢位論壇](http://azure.microsoft.com/support/forums/)上的 Azure 專員。

或者，您也可以提出 Azure 支援事件。請移至 [Azure 支援網站](http://azure.microsoft.com/support/options/)，然後按一下 [取得支援]。如需關於使用 Azure 支援的資訊，請參閱 [Microsoft Azure 支援常見問題集](http://azure.microsoft.com/support/faq/)。


## 基本步驟

請嘗試下列步驟來解決其他常見的 SSH 連線失敗：

1. 從 [Azure 入口網站](https://portal.azure.com)重設遠端存取。依序按一下 [全部瀏覽] > [虛擬機器 (傳統)] > 您的 Windows 虛擬機器 > [重設遠端存取]。

	![重設遠端存取](./media/virtual-machines-troubleshoot-ssh-connections/Portal-SSH-Reset-Windows.png)

2. 重新啟動虛擬機器。從 [Azure Preview 入口網站](https://portal.azure.com)依序按一下 [全部瀏覽] > [虛擬機器 (傳統)] > 您的 Windows 虛擬機器 > [重新啟動]。從 [Azure 管理入口網站](https://manage.windowsazure.com)開啟 [虛擬機器] > [執行個體]，按一下 [重新啟動]。

3. [調整虛擬機器的大小](https://msdn.microsoft.com/library/dn168976.aspx)。

4. 請遵循[如何為 Linux 虛擬機器重設密碼或 SSH](virtual-machines-linux-use-vmaccess-reset-password-or-ssh.md) 中關於虛擬機器的指示進行，以：

	- 重設密碼或 SSH 金鑰。
	- 建立新的 sudo 使用者帳戶。
	- 重設 SSH 組態。

## 詳細疑難排解

如果 SSH 用戶端仍然無法連線到虛擬機器上的 SSH 服務，可能有許多原因。下列為相關的元件。

![](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot1.png)

下列各節將協助您隔離失敗的來源，並找出解決方案或因應措施。

### 疑難排解之前的步驟

首先，在 Azure 入口網站中檢查虛擬機器的狀態。

在 [Azure 管理入口網站](https://manage.windowsazure.com)中：

1. 按一下 [**虛擬機器**] > [*VM 名稱*]。
2. 按一下 VM 的 [儀表板] 檢查其狀態。
3. 按一下 [**監視器**] 以查看計算、儲存體和網路資源的近期活動。
4. 按一下 [**端點**] 以確保有 SSH 流量的端點。

在 [Azure Preview 入口網站](https://portal.azure.com)中：

1. 按一下 [**瀏覽**] > [**虛擬機器**] > [*VM 名稱*]。如果是在 Azure 資源管理員中建立的虛擬機器，請按一下 [瀏覽] > [虛擬機器 (v2)] > [VM 名稱]。虛擬機器的狀態窗格應該會顯示為**執行中**。向下捲動以顯示計算、儲存體和網路資源的近期活動。
2. 按一下 [**設定**] 以檢查端點、IP 位址和其他設定。

若要確認網路連線，請檢查設定的端點，並判斷您是否可以透過另一個通訊協定 (例如 HTTP 或另一個服務) 連接到 VM。

在這些步驟之後，請再試一次 SSH 連線。


### 疑難排解步驟

您電腦上的 SSH 用戶端若不能連接 Azure 虛擬機器上的 SSH 服務，可能為下列問題或錯誤設定的來源所造成：

- SSH 用戶端電腦
- 組織邊緣裝置
- 雲端服務端點和存取控制清單 (ACL)
- 網路安全性群組
- 以 Linux 為基礎的 Azure 虛擬機器

#### 來源 1：SSH 用戶端電腦

若要排除您的電腦為失敗來源之可能性，請確認您的電腦可以建立 SSH 連線到另一部內部部署且以 Linux 為基礎的電腦。

![](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot2.png)

如果失敗，請在您的電腦上檢查下列項目：

- 正封鎖輸入或輸出 SSH 流量的本機防火牆設定 (TCP 22)
- 正阻止 SSH 連線的本機安裝用戶端 Proxy 軟體
- 正阻止 SSH 連線的本機安裝網路監視軟體
- 其他類型的安全性軟體，這會監視流量或允許/不允許特定類型的流量。

在所有這些情況下，請暫時停用此軟體，然後再嘗試 SSH 連線到內部部署的電腦，以找出原因。然後請和網路管理員合作，修正軟體設定以允許 SSH 連線。

如果您使用憑證驗證，請確認您在主目錄中擁有 ssh 資料夾的下列權限：

- Chmod 700 ~/.ssh
- Chmod 644 ~/.ssh/*.pub
- Chmod 600 ~/.ssh/id\_rsa (或您儲存私密金鑰的任何其他檔案)
- Chmod 644 ~/.ssh/known\_hosts (包含您已透過 SSH 連接的主機)

#### 來源 2：組織邊緣裝置

若要排除組織邊緣裝置為失敗來源之可能性，請確認直接連接到網際網路的電腦能 SSH 連線到您的 Azure VM。如果您透過網站間 VPN 或 ExpressRoute 連線存取 VM，請跳至[來源 4：網路安全性群組](#nsg)。

![](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot3.png)

如果您沒有直接連線到網際網路的電腦，則您只要將新的 Azure 虛擬機器建立於專屬的資源群組或雲端服務中並加以使用即可。如需詳細資訊，請參閱[在 Azure 中建立執行 Linux 的虛擬機器](virtual-machines-linux-tutorial.md)。當您完成測試後，請刪除此資源群組或虛擬機器及雲端服務。

如果您可以對直接連線到網際網路的電腦建立 SSH 連線，請檢查組織邊緣裝置之下列項目：

- 正封鎖網際網路 SSH 流量的內部防火牆。
- 正阻止 SSH 連線的 Proxy 伺服器。
- 正阻止 SSH 連線且在邊緣網路中的裝置上執行的入侵偵測或網路監視軟體。

請和網路管理員合作，修正組織邊緣裝置的設定，允許網際網路的 SSH 流量。

#### 來源 3：雲端服務端點和 ACL

若要排除雲端服務端點與 ACL 為失敗來源之可能性，針對使用[傳統部署模型](../resource-manager-deployment-model.md)建立的 VM，請確認同一虛擬網路中的其他 Azure VM 可以建立 SSH 連線至您的 VM。

![](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot4.png)

> [AZURE.NOTE]如果是使用資源管理員建立的虛擬機器，請跳到[來源 4：網路安全性群組](#nsg)。

如果在相同的虛擬網路中沒有另一部 VM，則您只要建立一部新的即可。如需詳細資訊，請參閱[在 Azure 中建立執行 Linux 的虛擬機器](virtual-machines-linux-tutorial.md)。當您完成測試後，請刪除額外的 VM。

如果您可以建立 SSH 連線到相同虛擬網路中的 VM，請檢查：

- 目標 VM 上的 SSH 流量端點組態。此端點的私用 TCP 連接埠應符合 VM 上 SSH 服務正在接聽的 TCP 連接埠，預設為 22。如果是在資源管理員部署模型中使用範本建立的 VM，請利用 [瀏覽] > [虛擬機器 (v2)] > [VM 名稱] > [設定] > [端點]，確認 Azure Preview 入口網站中的 SSH TCP 連接埠號碼。
- 目標虛擬機器上的 SSH 流量端點 ACL。ACL 讓您可指定要根據來源 IP 位址允許或拒絕來自網際網路的連入流量。設定錯誤的 ACL 會阻止送至端點的連入 SSH 流量。檢查您的 ACL，以確保允許來自您的 Proxy 或其他邊緣伺服器的公用 IP 位址之連入流量。如需詳細資訊，請參閱[關於網路存取控制清單 (ACL)](../virtual-network/virtual-networks-acl.md)。

若要排除端點為問題或錯誤設定來源之可能性，請移除目前的端點、建立一個新端點，再指定 **SSH** 名稱 (TCP 連接埠 22 做為公用及私用連接埠編號)。如需詳細資訊，請參閱[在 Azure 中設定虛擬機器的端點](virtual-machines-set-up-endpoints.md)。

<a id="nsg"></a>
#### 來源 4：網路安全性群組

網路安全性群組讓您更精確地控制受允許的輸入和輸出流量。您可以在 Azure 虛擬網路中建立跨越子網路和雲端服務的規則。請檢查您的網路安全性群組規則，以確保允許往來網際網路的 SSH 流量。如需詳細資訊，請參閱[關於網路安全性群組](../traffic-manager/virtual-networks-nsg.md)。

#### 來源 5：以 Linux 為基礎的 Azure 虛擬機器

最後一個可能的問題來源就是 Azure 虛擬機器本身。

![](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot5.png)

請在虛擬機器上遵循[如何為 Linux 虛擬機器重設密碼或 SSH](virtual-machines-linux-use-vmaccess-reset-password-or-ssh.md) 中的指示進行 (如果您還沒這麼做)。

再次嘗試從您的電腦連線。如果仍然失敗，下列為某些可能的原因：

- SSH 服務未在目標虛擬機器上執行。
- SSH 服務未在 TCP 連接埠 22 上接聽。若要測試這種情況，請在本機電腦上安裝 telnet 用戶端，並執行 "telnet *cloudServiceName*.cloudapp.net 22"。這樣可以判斷虛擬機器是否允許和 SSH 端點的輸入和輸出通訊。
- 目標虛擬機器上的本機防火牆具有防止輸入或輸出 SSH 流量的規則。
- 在 Azure 虛擬機器上執行的入侵偵測或網路監視軟體正在阻止 SSH 連線。


## 其他資源

[如何為 Linux 虛擬機器重設密碼或 SSH](virtual-machines-linux-use-vmaccess-reset-password-or-ssh.md)

[疑難排解以 Windows 為基礎之 Azure 虛擬機器的 Windows 遠端桌面連線](virtual-machines-troubleshoot-remote-desktop-connections.md)

[疑難排解存取在 Azure 虛擬機器上執行的應用程式](virtual-machines-troubleshoot-access-application.md)

<!---HONumber=Oct15_HO1-->