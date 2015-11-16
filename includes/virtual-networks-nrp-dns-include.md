## Azure DNS

Azure DNS 是 DNS 網域的主機服務，採用 Microsoft Azure 基礎結構提供名稱解析。


| 屬性 | 說明 | 範例值 |
|---|---|---|
| DNS 區域 | 託管特定網域 DNS 記錄的網域區域資訊 | /subscriptions/{guid}/.../providers/Microsoft.Network/dnszones/contoso.com"


### DNS 記錄集

DNS 區域擁有名為「記錄集」的子物件。記錄集是 DNS 區域依類型分類之主機記錄的集合。記錄類型有 A、AAAA、CNAME、MX、NS、SOA、SRV 和 TXT。

| 屬性 | 說明 | 範例值 |
|---|---|---|
| A | IPv4 記錄類型 | /subscriptions/{guid}/.../providers/Microsoft.Network/dnszones/contoso.com/A/www |
| AAAA | IPv6 記錄類型| /subscriptions/{guid}/.../providers/Microsoft.Network/dnszones/contoso.com/AAAA/hostrecord |
| CNAME | 正式名稱記錄類型 <sup>1</sup> | /subscriptions/{guid}/.../providers/Microsoft.Network/dnszones/contoso.com/CNAME/www |
| MX | 郵件記錄類型 | /subscriptions/{guid}/.../providers/Microsoft.Network/dnszones/contoso.com/MX/mail |
| NS | 名稱伺服器記錄類型 | /subscriptions/{guid}/.../providers/Microsoft.Network/dnszones/contoso.com/NS/ |
| SOA | 起始授權記錄類型 <sup>2</sup> | /subscriptions/{guid}/.../providers/Microsoft.Network/dnszones/contoso.com/SOA |
| SRV | 服務記錄類型 | /subscriptions/{guid}/.../providers/Microsoft.Network/dnszones/contoso.com/SRV |

<sup>1</sup> 每個記錄集僅允許一個值。

<sup>2</sup> 每個 DNS 區域僅允許一種記錄類型 SOA。

Json 格式的 DNS 區域範例：

	{
	  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
	  "contentVersion": "1.0.0.0",
	  "parameters": {
	    "newZoneName": {
	      "type": "String",
	      "metadata": {
	          "description": "The name of the DNS zone to be created."
	      }
	    },
	    "newRecordName": {
	      "type": "String",
	      "defaultValue": "www",
	      "metadata": {
	          "description": "The name of the DNS record to be created.  The name is relative to the zone, not the FQDN."
	      }
	    }
	  },
	  "resources": 
	  [
	    {
	      "type": "microsoft.network/dnszones",
	      "name": "[parameters('newZoneName')]",
	      "apiVersion": "2015-05-04-preview",
	      "location": "global",
	      "properties": {
	      }
	    },
	    {
	      "type": "microsoft.network/dnszones/a",
		  "name": "[concat(parameters('newZoneName'), concat('/', parameters('newRecordName')))]",
      	"apiVersion": "2015-05-04-preview",
      	"location": "global",
	  	"properties": 
	  	{
        	"TTL": 3600,
			"ARecords": 
			[
			    {
				    "ipv4Address": "1.2.3.4"
				},
				{
				    "ipv4Address": "1.2.3.5"
				}
			]
	  	},
	  	"dependsOn": [
        	"[concat('Microsoft.Network/dnszones/', parameters('newZoneName'))]"
      	]
    	}
	  	]
	}

## 其他資源

如需詳細資訊，請參閱[適用於 DNS 區域的 REST API 文件](https://msdn.microsoft.com/library/azure/mt130626.aspx)。

如需詳細資訊，請參閱[適用於 DNS 記錄集的 REST API 文件](https://msdn.microsoft.com/library/azure/mt130626.aspx)。

<!---HONumber=Nov15_HO2-->