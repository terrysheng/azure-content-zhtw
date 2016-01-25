## 案例

此文件將逐步說明使用與虛擬機器 (VM) 關聯之靜態公用 IP 位址的部署。在此案例中，您擁有具有自己的靜態公用 IP 位址的單一 VM。VM 是名為 **FrontEnd** 之子網路的一部分，而且也在該子網路中擁有靜態私人 IP 位址 (**192.168.1.101**)。

您可能需要一個靜態 IP 位址以供需要 SSL 連線 (SSL 憑證是連結至 IP 位址) 的 Web 伺服器使用。

![影像說明](./media/virtual-network-deploy-static-pip-scenario-include/figure1.png)

您可以依照下面的步驟部署上圖中顯示的環境。

<!---HONumber=AcomDC_0114_2016-->