透過 Azure 資源管理員，您可以定義在部署範本時想要指定之值的參數。此範本有一個 Parameters 區段，內含所有參數值。您應該為會隨著要部署的專案或要部署到的環境而變化的值定義參數。請不要為永遠保持不變的值定義參數。每個參數值都可在範本中用來定義所部署的資源。

我們將說明範本中的每個參數。

### gatewayName

閘道器的名稱。API 應用程式會向此閘道註冊。

    "gatewayName": {
      "type": "string"
    }

### apiAppName

要建立之 API 應用程式的名稱。名稱必須包含至少 8 個字元，而且不能多於 50 個字元。
    
    "apiAppName": {
      "type": "string"
    }

### apiAppSecret

API 應用程式的密碼。此值必須是 base64 編碼的字串。它應該是具有 64 個字元的隨機字串，且僅由整數和小寫字元所組成。

    "apiAppSecret": {
      "type": "securestring"
    }

### location

新 API 應用程式的位置。您可以透過執行 PowerShell 指令 `Get-AzureLocation` 或 Azure CLI 指令 `azure location list` 取得有效的位置。

    "location": {
      "type": "string"
    }

<!---HONumber=Oct15_HO3-->