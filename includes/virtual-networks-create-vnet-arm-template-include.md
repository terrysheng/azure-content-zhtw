## 下載 ARM 範本並了解使用方式

您可以下載現有的 ARM 範本，以透過 Github 建立 VNet 和兩個子網路，然後進行任何需要的變更，並重複使用該範本。若要這樣做，請依照下列步驟執行。

1. 瀏覽至[範例範本頁面](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets)。
2. 依序按一下 [azuredeploy.json] 和 [RAW]。
3. 將檔案儲存至您電腦上的本機資料夾。
4. 如果您熟悉 ARM 範本的使用方式，請跳至步驟 7。
5. 開啟您剛儲存的檔案，並查看第 5 行中 **parameters** 下方的內容。ARM 範本的參數提供值的預留位置，可以在部署期間填寫。

	| 參數 | 說明 |
	|---|---|
	| **位置** | 將會在當中建立 VNet 的 Azure 區域 |
	| **vnetName** | 新 VNet 的名稱 |
	| **addressPrefix** | 以 CIDR 格式表示的 VNet 位址空間 |
	| **subnet1Name** | 第一個 VNet 的名稱 |
	| **subnet1Prefix** | 第一個子網路的 CIDR 區塊 |
	| **subnet2Name** | 第二個 VNet 的名稱 |
	| **subnet2Prefix** | 第二個子網路的 CIDR 區塊 |

	>[AZURE.IMPORTANT] Github 所管理的 ARM 範本內容可能會隨著時間改變。使用範本前，請務必先檢查當中的內容。
	
6. 檢查 **resources** 下方的內容，並注意以下項目：

	- **type**。範本所建立的資源類型。在此案例中為 **Microsoft.Network/virtualNetworks**，代表 VNet。
	- **名稱**。資源的名稱。請查看使用 **[parameters('vnetName')]** 的項目，這表示此名稱是在部署期間由使用者的輸入內容，或參數檔案提供。
	- **屬性**。資源屬性的清單。此範本在 VNet 建立期間會使用位址空間和子網路屬性。

7. 瀏覽回[範例範本頁面](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets)。
8. 依序按一下 [azuredeploy-paremeters.json] 和 [RAW]。
9. 將檔案儲存至您電腦上的本機資料夾。
10. 開啟您剛儲存的檔案，編輯參數的值。使用下列值部署本文案例所述的 VNet。

		{
		  "location": {
		    "value": "Central US"
		  },
		  "vnetName": {
		      "value": "TestVNet"
		  },
		  "addressPrefix": {
		      "value": "192.168.0.0/16"
		  },
		  "subnet1Name": {
		      "value": "FrontEnd"
		  },
		  "subnet1Prefix": {
		    "value": "192.168.1.0/24"
		  },
		  "subnet2Name": {
		      "value": "BackEnd"
		  },
		  "subnet2Prefix": {
		      "value": "192.168.2.0/24"
		  }
		}

11. 儲存檔案。
  

<!---HONumber=AcomDC_0211_2016-->