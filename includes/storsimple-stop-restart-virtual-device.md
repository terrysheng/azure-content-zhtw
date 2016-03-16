#### 停止和啟動虛擬裝置
若要停止虛擬裝置，請按一下其名稱，然後按一下 [關機]。當虛擬裝置正在關閉期間，其狀態會是 [正在停止]。當虛擬裝置停止之後，其狀態會是 [已停止]。

請使用下列 Cmdlet 來停止和啟動虛擬裝置。

`Stop-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`


`Start-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`
    
#### 重新啟動虛擬機器

若虛擬裝置正在執行，但您想將它重新啟動，請按一下其名稱，然後按一下 [重新啟動]。當虛擬裝置正在重新啟動期間，其狀態會是 [正在重新啟動]。若虛擬裝置已可供您使用，其狀態會是 [執行中]。

請使用下列 Cmdlet 來重新啟動虛擬裝置。

`Restart-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

<!---HONumber=AcomDC_1217_2015-->