1. 瀏覽回到容錯移轉叢集管理員。展開 [角色]，然後反白顯示您的可用性群組。在 [資源] 索引標籤上，以滑鼠右鍵按一下接聽程式名稱，然後按一下 [屬性]。

1. 按一下 [相依性] 索引標籤。如果列出多個資源，請確認 IP 位址具有 OR 相依性，而非 AND。按一下 [確定]。

1. 以滑鼠右鍵按一下接聽程式名稱，然後按一下 [線上工作]。

1. 一旦接聽程式在線上，從 [資源] 索引標籤，以滑鼠右鍵按一下可用性群組，再按一下 [屬性]。

	![設定可用性群組資源](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678772.gif)

1. 建立對接聽程式名稱資源 (非 IP 位址資源名稱) 的相依性。按一下 [確定]。

	![加入對接聽程式名稱的相依性](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678773.gif)

1. 啟動 [SQL Server Management Studio]，然後連接到主要複本。

1. 瀏覽至 [AlwaysOn 高可用性] | [可用性群組] | **<AvailabilityGroupName>** | [可用性群組接聽程式]。

3. 您現在應該會看到在容錯移轉叢集管理員中建立的接聽程式名稱。以滑鼠右鍵按一下接聽程式名稱，然後按一下 [屬性]。

1. 在 [連接埠] 方塊中，使用您稍早所用的 $EndpointPort，指定可用性群組接聽程式的連接埠號碼 (在本教學課程中，預設值是 1433)，然後按一下 [確定]。

<!---HONumber=August15_HO8-->