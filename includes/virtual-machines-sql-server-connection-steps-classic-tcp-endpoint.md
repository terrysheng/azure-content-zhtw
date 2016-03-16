### 為虛擬機器建立 TCP 端點

若要從網際網路存取 SQL Server，虛擬機器必須具有端點才能接聽傳入 TCP 通訊。此 Azure 組態步驟能將傳入 TCP 連接埠流量導向虛擬機器可存取的 TCP 連接埠。

>[AZURE.NOTE]如果您在相同的雲端服務或虛擬網路內連接，則不需要建立可公開存取的端點在此情況下，您可以繼續進行下一個步驟。如需詳細資訊，請參閱[連接案例](../articles/virtual-machines/virtual-machines-sql-server-connectivity.md#connection-scenarios)。

1. 在 Azure 管理入口網站中按一下 [虛擬機器]。
	
2. 按一下新建立的虛擬機器。畫面上即會顯示您虛擬機器的相關資訊。
	
3. 選取頁面頂端附近的 [端點] 頁面，然後按一下頁面底部的 [新增]。
	
4. 在 [Add an Endpoint to a Virtual Machine] 頁面中，按一下 [Add a Stand-alone Endpoint]，然後按 [下一步] 箭頭以繼續操作。
	
5. 在 [Specify the details of the endpoint] 頁面中，提供下列資訊。

	- 在 [名稱] 方塊中，提供端點的名稱。
	- 在 [通訊協定] 方塊中，選取 [TCP]。您可以在 [公用連接埠] 方塊中輸入 **57500**。同樣地，您可以在 [私人連接埠] 方塊中輸入 SQL Server 的預設接聽連接埠 **1433**。請注意，有許多組織會藉由選取不同連接埠號碼來避免惡意的安全性攻擊。 

6. 按一下打勾記號繼續。系統隨即會建立端點。

<!---HONumber=AcomDC_0107_2016-->