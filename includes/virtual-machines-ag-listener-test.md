在此步驟中，您會使用在相同網路上執行的用戶端應用程式來測試可用性群組接聽程式。

針對用戶端連線能力，請注意下列需求：

- 對接聽程式的用戶端連接必須來自位於與裝載 AlwaysOn 可用性複本的機器不同雲端服務的機器。

- 如果 AlwaysOn 複本位於不同的子網路中，用戶端必須在連接字串中指定 "MultisubnetFailover=True"。這會導致對於不同子網路中的複本進行平行連接嘗試。請注意，此情況包含跨區域的 AlwaysOn 可用性群組部署。

其中一個範例是從相同 Azure VNet 中的其中一台 VM (但不是裝載複本的那台 VM) 連接到接聽程式。完成這項測試的簡單方法是嘗試將 SSMS 連接到可用性群組接聽程式。另一種簡單的方法是執行 [SQLCMD.exe](https://technet.microsoft.com/library/ms162773.aspx)，如下所示：

	sqlcmd -S "<ListenerName>,<EndpointPort>" -d "<DatabaseName>" -Q "select @@servername, db_name()" -l 15

> [AZURE.NOTE]如果 EndpointPort 值為 1433，則不需要在呼叫中指定。前一個呼叫也假設用戶端電腦已加入相同的網域，且呼叫者已使用 Windows 驗證被授與對資料庫的權限。

測試接聽程式時，請務必容錯移轉可用性群組，以確定用戶端可以跨容錯移轉連線至接聽程式。

<!---HONumber=August15_HO7-->