


使用**資源管理員**部署模型在 [Azure 入口網站](https://portal.azure.com)中建立虛擬機器時，入口網站會為虛擬機器建立公用 IP 資源。您可以使用此 IP 位址從遠端存取虛擬機器。雖然入口網站預設不會建立 [Fully qualified domain name (完整網域名稱)](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) 或簡稱 FQDN，不過在虛擬機器建立之後即可輕易地建立一個。這篇文章示範建立 DNS 名稱或 FQDN 的步驟。

本文假設您已登入入口網站中的訂用帳戶，並透過**資源管理員**使用可用的映像建立虛擬機器。一旦您的虛擬機器開始執行，請依照下列步驟。

1.  檢視入口網站上的虛擬機器設定，並按一下 [公用 IP 位址]。

    ![找出 IP 資源](./media/virtual-machines-common-portal-create-fqdn/locatePublicIP.PNG)

2.  請注意，公用 IP 的 DNS 名稱是空白的。按一下 [公用 IP] 刀鋒視窗的 [所有設定]。

    ![設定 IP](./media/virtual-machines-common-portal-create-fqdn/settingsIP.PNG)

3.  開啟 [公用 IP 設定] 中的 [設定] 索引標籤。輸入要使用的 DNS 名稱標籤，然後**儲存**此設定。

    ![輸入 DNS 名稱標籤](./media/virtual-machines-common-portal-create-fqdn/dnsNameLabel.PNG)

    公用 IP 資源隨即會在其刀鋒視窗上顯示這個新的 DNS 標籤。

4.  關閉 [公用 IP] 刀鋒視窗，然後返回入口網站中的虛擬機器刀鋒視窗。請確認 DNS 名稱/FQDN 顯示在公用 IP 資源的 IP 位址旁邊。

    ![FQDN 已建立](./media/virtual-machines-common-portal-create-fqdn/fqdnCreated.PNG)


    您可以立即使用這個 DNS 名稱從遠端連線到虛擬機器。例如，連接到具有完整網域名稱 `testdnslabel.centralus.cloudapp.azure.com` 和使用者名稱 `adminuser` 的 Linux 虛擬機器時使用 `SSH adminuser@testdnslabel.centralus.cloudapp.azure.com`。

<!---HONumber=AcomDC_0323_2016-->