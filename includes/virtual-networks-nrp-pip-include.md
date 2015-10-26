## 公用 IP 位址
公用 IP 位址資源提供保留或動態的網際網路的 IP 位址。雖然您可以將公用 IP 位址建立為獨立物件，但您必須將其關聯到另一個物件，才能實際使用該位址。您可以將公用 IP 位址與負載平衡器、應用程式閘道或 NIC 建立關聯，提供以網際網路存取這些資源的能力。

|屬性|說明|範例值|
|---|---|---|
|**publicIPAllocationMethod**|定義 IP 位址是否為「靜態」或「動態」。|static、dynamic|
|**idleTimeoutInMinutes**|定義閒置逾時，預設值為 4 分鐘。如果在此時間內不再收到指定工作階段的封包，即終止工作階段。|介於 4 到 30 之間的任意值|
|**ipAddress**|IP 位址已指派給物件。這是唯讀屬性。|104\.42.233.77|

### DNS 設定
公用 IP 位址有名為 **dnsSettings** 的子物件，其包含下列屬性：

|屬性|說明|範例值|
|---|---|---|
|**domainNameLabel**|用於名稱解析的主機名稱。|www、ftp、vm1|
|**fqdn**|公用 IP 的完整名稱。|www.westus.cloudapp.azure.com|
|**reverseFqdn**|完整網域名稱，會解析成 IP 位址並在 DNS 中登錄為 PTR 記錄。|www.contoso.com。|

JSON 格式的範例公用 IP 位址：

	{
	   "name": "PIP01",
	   "location": "North US",
	   "tags": { "key": "value" },
	   "properties": {
	      "publicIPAllocationMethod": "Static",
	      "idleTimeoutInMinutes": 4,
		  "ipAddress": "104.42.233.77",
	      "dnsSettings": {
	         "domainNameLabel": "mylabel",
			 "fqdn": "mylabel.westus.cloudapp.azure.com",
	         "reverseFqdn": "contoso.com."
	      }
	   }
	} 

### 其他資源

- 取得[公用 IP 位址](virtual-networks-reserved-public-ip.md)的詳細資訊。
- 了解[執行個體層級公用 IP 位址](virtual-networks-instance-level-public-ip.md)。
- 讀取公用 IP 位址的 [REST API 參考文件](https://msdn.microsoft.com/library/azure/mt163638.aspx)。

<!---HONumber=Oct15_HO3-->