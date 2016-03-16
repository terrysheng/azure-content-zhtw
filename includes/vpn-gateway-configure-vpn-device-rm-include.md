
為了設定 VPN 裝置，您需要虛擬網路閘道的公用 IP 位址，以便設定內部部署 VPN 裝置。請向裝置製造商取得特定的組態資訊並設定您的裝置。如需能與 Azure 搭配使用之 VPN 裝置的相關詳細資訊，請參閱 [VPN 裝置](vpn-gateway-about-vpn-devices.md)。

若要使用 PowerShell 尋找虛擬網路閘道的公用 IP 位址，請使用下面範例：

	Get-AzureRmPublicIpAddress -Name GW1PublicIP -ResourceGroupName TestRG

您也可以使用 Azure 入口網站，來檢視您虛擬網路閘道的公用 IP 位址。瀏覽至 [虛擬網路閘道]，然後按一下閘道名稱。

<!---HONumber=AcomDC_0107_2016-->