### 決定虛擬機器的 DNS 名稱

若要從另一部電腦連接 SQL Server Database Engine，您必須知道虛擬機器的網域名稱系統 (DNS) 名稱。(這是網際網路用來識別虛擬機器的名稱。您可以使用 IP 位址，不過當 Azure 因備援或維護而移動資源時，IP 位址可能會改變。DNS 名稱是穩定的，因為它可以重新導向新的 IP 位址。)

1. 在 Azure 管理入口網站 (或前一個步驟) 中選取 [虛擬機器]。 

2. 在 [虛擬機器執行個體] 頁面的 [快速概覽] 欄下方，尋找並複製虛擬機器的 DNS 名稱。

	![DNS 名稱](./media/virtual-machines-sql-server-connection-steps/sql-vm-dns-name.png)
	

### 從另一部電腦連接 Database Engine
 
1. 在連接網際網路的電腦上開啟 SQL Server Management Studio。
2. 在 [連接到伺服器] 或 [連接到 Database Engine] 對話方塊的 [伺服器名稱]**** 方塊中，輸入虛擬機器的 DNS 名稱 (於上一個工作中決定) 和 *DNSName,portnumber* 格式的公用端點連接埠名稱 (例如 **tutorialtestVM.cloudapp.net,57500**)。若要取得連接埠號碼，請登入 Azure 管理入口網站並尋找虛擬機器。在儀表板上，按一下 [端點]，然後使用指派給 [MSSQL] 的 [公用連接埠]。![公用連接埠](./media/virtual-machines-sql-server-connection-steps/sql-vm-port-number.png)
3. 在 [驗證] 方塊中，選取 [SQL Server 驗證]。
5. 在 [登入] 方塊中，輸入於先前工作中建立之登入的名稱。
6. 在 [密碼] 方塊中，輸入於先前工作中建立之登入的密碼。
7. 按一下 [連接]。

	![使用 SSMS 進行連線](./media/virtual-machines-sql-server-connection-steps/33Connect-SSMS.png)

<!---HONumber=AcomDC_0107_2016-->