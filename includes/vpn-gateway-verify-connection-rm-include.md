### 使用 Azure 入口網站驗證您的連線

您可以在 Azure 入口網站中驗證 VPN 連線，方法是瀏覽至 [虛擬網路閘道] -> **按一下您的閘道名稱** -> [設定] -> [連線]。您可以選取連線名稱，以在 [連線] 刀鋒視窗中檢視詳細資訊。

### 使用 PowerShell 驗證您的連線

您也可以使用 Get-AzureRmVirtualNetworkGatewayConnection –Debug 驗證您的連接是否成功。未來，我們將有用於執行此作業的 Cmdlet。您可以使用下列 cmdlet 範例，設定符合您自己的值。出現提示時，請選取 [A] 以執行 [全部]。

	Get-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Debug

 完成此 cmdlet 之後，請捲動以檢視值。在下列範例中，連接狀態會顯示為 [已連接]，且您可以看見輸入和輸出位元組。

	Body:
	{
	  "name": "localtovon",
	  "id":
	"/subscriptions/086cfaa0-0d1d-4b1c-9455-f8e3da2a0c7789/resourceGroups/testrg/providers/Microsoft.Network/connections/loca
	ltovon",
	  "properties": {
	    "provisioningState": "Succeeded",
	    "resourceGuid": "1c484f82-23ec-47e2-8cd8-231107450446b",
	    "virtualNetworkGateway1": {
	      "id":
	"/subscriptions/086cfaa0-0d1d-4b1c-9455-f8e3da2a0c7789/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworkGa
	teways/vnetgw1"
	    },
	    "localNetworkGateway2": {
	      "id":
	"/subscriptions/086cfaa0-0d1d-4b1c-9455-f8e3da2a0c7789/resourceGroups/testrg/providers/Microsoft.Network/localNetworkGate
	ways/LocalSite"
	    },
	    "connectionType": "IPsec",
	    "routingWeight": 10,
	    "sharedKey": "abc123",
	    "connectionStatus": "Connected",
	    "ingressBytesTransferred": 33509044,
	    "egressBytesTransferred": 4142431
	  }

<!---HONumber=AcomDC_0323_2016-->