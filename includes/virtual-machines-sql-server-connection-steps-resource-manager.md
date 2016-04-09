### 設定公用 IP 位址的 DNS 標籤

若要從網際網路連線到 SQL Server Database Engine，請先設定公用 IP 位址的 DNS 標籤。請注意，如果您只計畫連線到同一個虛擬網路中或只連線到本機的 SQL Server 執行個體，不需要執行此步驟。

若要建立 DNS 標籤，請先在入口網站選取 [虛擬機器]。選取 SQL Server VM 以顯示其屬性。

1. 在虛擬機器刀鋒視窗中，選取您的 [公用 IP 位址]。

	![公用 IP 位址](./media/virtual-machines-sql-server-connection-steps/rm-public-ip-address.png)

2. 在公用 IP 位址屬性中，展開 [組態]。

3. 輸入 DNS 標籤名稱。您可以使用此「A 紀錄」透過名稱 (而非直接透過 IP 位址) 連線到 SQL Server VM。

	![DNS 標籤](./media/virtual-machines-sql-server-connection-steps/rm-dns-label.png)

### 從另一部電腦連接 Database Engine
 
1. 在已連線網際網路的電腦上開啟 SQL Server Management Studio (SSMS)。

2. 在 [連接至伺服器] 或 [連接到 Database Engine] 對話方塊的 [伺服器名稱] 方塊中，輸入虛擬機器的完整 DNS 名稱 (在上一個工作中決定的)。

3. 在 [驗證] 方塊中，選取 [SQL Server 驗證]。

5. 在 [登入] 方塊中，輸入有效的 SQL 登入名稱。

6. 在 [密碼] 方塊中，輸入登入的密碼。

7. 按一下 [連接]。

	![SSMS 連線](./media/virtual-machines-sql-server-connection-steps/rm-ssms-connect.png)

<!---HONumber=AcomDC_0309_2016-->