## 案例

為了更清楚說明如何建立 VNet 和子網路，本文件會使用下列案例。

![VNet 案例](./media/virtual-networks-create-vnet-scenario-include/vnet-scenario.png)

在這個案例中，您將建立名為 **TestVNet** 的 VNet，並包含保留的 CIDR 區塊 **192.168.0.0./16**。VNet 會包含下列子網路：

- **FrontEnd**，使用 **192.168.1.0/24** 作為其 CIDR 區塊。
- **BackEnd**，使用 **192.168.2.0/24** 作為其 CIDR 區塊。

 

<!---HONumber=Oct15_HO3-->