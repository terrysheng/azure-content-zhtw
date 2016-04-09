## 範例案例

為了要更清楚說明如何管理 NSG，本文使用下列案例。

![VNet 案例](./media/virtual-networks-create-nsg-scenario-include/figure1.png)

在這個案例中，您將在 **TestVNet** 虛擬網路的每個子網路中建立 NSG ，如下所述：

- **NSG-FrontEnd**。前端 NSG 將套用到 *FrontEnd* 子網路，且包含兩個規則：	
	- **rdp-rule**。此規則允許至 *FrontEnd* 子網路的 RDP 流量。
	- **web-rule**。此規則允許至 *FrontEnd* 子網路的 HTTP 流量。
- **NSG-BackEnd**。後端 NSG 將套用到 *BackEnd* 子網路，且包含兩個規則：	
	- **sql-rule**。此規則僅允許來自 *FrontEnd* 子網路的 SQL 流量。
	- **web-rule**。此規則會拒絕來自 *BackEnd* 子網路的所有網際網路繫結流量 。

這些規則的組合會建立類似 DMZ 的案例，其後端子網路只能接收來自前端子網路 SQL 流量的傳入流量，且無網際網路的存取權；而前端子網路則可與網際網路通訊，並接收傳入的 HTTP 要求。

若要部署上述案例，請依循[此連結](http://github.com/telmosampaio/azure-templates/tree/master/201-IaaS-WebFrontEnd-SQLBackEnd-NSG)，按一下 [部署至 Azure]，視情況取代預設參數值，再依循入口網站中的指示。在下列範例指示中，使用了範本來部署名為 **RG-NSG** 的資源群組。
 

<!---HONumber=AcomDC_0323_2016-->