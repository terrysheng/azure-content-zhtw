## 流量管理員設定檔

流量管理員和它的子端點資源讓 DNS 路由可路由至 Azure 內部和 Azure 外部端點。這類流量分配是由路由原則方法所管理。流量管理員也可監視端點健全狀況，並根據端點健全狀況適當地轉向流量。

| 屬性 | 說明 |
|---|---|
|**流量路由方法**| 可能的值為 *Performance*、*Weighted* 和 *Priority* | 
| **DNS 設定** | 設定檔的 FQDN | 
| **通訊協定** | 監視通訊協定，可能的值為 *HTTP* 和 *HTTPS*|
| **連接埠** | 監視連接埠 |  
| **路徑** | 監視路徑 |
| **Endpoints** | 端點資源的容器 | 

### 端點 

端點是流量管理員設定檔的子資源。它代表服務或 Web 端點，並根據流量管理員設定檔資源中所設定的原則，將使用者流量散發到此服務或 Web 端點。

| 屬性 | 說明 | 
|---|---| 
| **類型** | 端點的類型，可能的值為 *Azure End point*、*External Endpoint* 和 *Nested Endpoint* | 
| **目標資源識別碼** | 服務或 Web 端點的公用 IP 位址。這可以是 Azure 或外部端點。 | 
| **重量** | 用於流量管理的端點加權。 | 
| **優先順序** | 端點的優先順序，用來定義容錯移轉動作。 |

JSON 格式的流量管理員範例：


        {
            "apiVersion": "[variables('tmApiVersion')]",
            "type": "Microsoft.Network/trafficManagerProfiles",
            "name": "VMEndpointExample",
            "location": "global",
            "dependsOn": [
                "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'), '0')]",
                "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'), '1')]",
                "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'), '2')]",
            ],
            "properties": {
                "profileStatus": "Enabled",
                "trafficRoutingMethod": "Weighted",
                "dnsConfig": {
                    "relativeName": "[parameters('dnsname')]",
                    "ttl": 30
                },
                "monitorConfig": {
                    "protocol": "http",
                    "port": 80,
                    "path": "/"
                },
                "endpoints": [
                    {
                        "name": "endpoint0",
                        "type": "Microsoft.Network/trafficManagerProfiles/azureEndpoints",
                        "properties": {
                            "targetResourceId": "[resourceId('Microsoft.Network/publicIPAddresses',concat(variables('publicIPAddressName'), 0))]",
                            "endpointStatus": "Enabled",
                            "weight": 1
                        }
                    },
                    {
                        "name": "endpoint1",
                        "type": "Microsoft.Network/trafficManagerProfiles/azureEndpoints",
                        "properties": {
                            "targetResourceId": "[resourceId('Microsoft.Network/publicIPAddresses',concat(variables('publicIPAddressName'), 1))]",
                            "endpointStatus": "Enabled",
                            "weight": 1
                        }
                    },
                    {
                        "name": "endpoint2",
                        "type": "Microsoft.Network/trafficManagerProfiles/azureEndpoints",
                        "properties": {
                            "targetResourceId": "[resourceId('Microsoft.Network/publicIPAddresses',concat(variables('publicIPAddressName'), 2))]",
                            "endpointStatus": "Enabled",
                            "weight": 1
                        }
                    }
                ]
            }
        }

 
## 其他資源

如需詳細資訊，請閱讀[流量管理員的 REST API 文件](https://msdn.microsoft.com/library/azure/mt163664.aspx)。

<!---HONumber=Nov15_HO3-->