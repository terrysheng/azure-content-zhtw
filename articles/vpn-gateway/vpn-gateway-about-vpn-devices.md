<properties 
   pageTitle="關於站對站 Azure 虛擬網路連線的 VPN 裝置 | Microsoft Azure"
   description="深入了解 VPN 裝置和 Azure 虛擬網路站對站 VPN 閘道連線的 IPsec 參數。"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carolz"
   editor="" />
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/27/2015"
   ms.author="cherylmc" />

# 關於站對站虛擬網路連線的 VPN 裝置

設定站對站 VPN 連線需要 VPN 裝置。站對站連線可以用來建立混合式雲端解決方案，或者用於您想要在內部部署網路與虛擬網路之間建立安全連線之時。這篇文章討論相容的 VPN 裝置和組態參數。

請務必知道，除了相容的 VPN 裝置，站對站連線也需要有公開的 IPv4 IP 位址。此外，您會想要選取將會支援您的方案的閘道器類型。並非所有的 VPN 裝置都支援所有的閘道器類型。請參閱〈[VPN 閘道](vpn-gateway-about-vpngateways.md)〉以確認建立您的方案所需的閘道器類型。



## VPN 裝置

我們已與裝置廠商合作驗證多個標準站對站 (S2S) VPN 裝置。如需已知與虛擬網路相容的 VPN 裝置清單，請參閱後文的〈[相容的 VPN 裝置](#compatible-vpn-devices)〉一節。此清單列出的裝置系列中的所有裝置應該都能與 Azure VPN 閘道搭配運作。為了協助設定您的 VPN 裝置，請參閱對應到適當裝置系列的裝置組態範本。

除非另有說明，否則高效能 VPN 閘道和動態路由 VPN 閘道的規格相同。例如，經驗證與 Azure 動態路由 VPN 閘道相容的 VPN 裝置，也會與新的 Azure 高效能 VPN 閘道相容。


### 相容的 VPN 裝置

我們與 VPN 裝置廠商合作，共同找出符合資格的特定 VPN 裝置系列。下一節的清單列出已知能與我們的 VPN 閘道搭配的所有裝置系列。所有屬於列出裝置系列的裝置皆能運作，除非有提及例外狀況。如果您的裝置沒有出現在清單上，請參閱〈[裝置不在相容清單上](#devices-not-on-the-compatible-list)〉。



如需 VPN 裝置的支援，請連絡裝置製造商。



| **廠商** | **裝置系列** | **作業系統最低版本** | **靜態路由** | **動態路由** |
|---------------------------------|----------------------------------------------------------|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Allied Telesis | AR 系列 VPN 路由器 | 2\.9.2 | 敬請期待 | 不相容 |
| Barracuda Networks, Inc. | Barracuda NG Firewall | Barracuda NG Firewall 5.4.3 | [Barracuda NG Firewall](https://techlib.barracuda.com/display/BNGV54/How%20to%20Configure%20an%20IPsec%20Site-to-Site%20VPN%20to%20a%20Windows%20Azure%20VPN%20Gateway)| 不相容 |
| Barracuda Networks, Inc. | Barracuda Firewall | Barracuda Firewall 6.5 | [Barracuda Firewall](https://techlib.barracuda.com/BFW/ConfigAzureVPNGateway) | 不相容 |
| Brocade | Vyatta 5400 vRouter | Virtual Router 6.6R3 GA | [組態指示](http://www1.brocade.com/downloads/documents/html_product_manuals/vyatta/vyatta_5400_manual/wwhelp/wwhimpl/js/html/wwhelp.htm#href=VPN_Site-to-Site%20IPsec%20VPN/Preface.1.1.html) | 不相容 |
| Check Point | Security Gateway | R75.40、R75.40VS | [組態指示](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) | [組態指示](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |
| Cisco | ASA | 8\.3 | [Cisco 範例](http://go.microsoft.com/fwlink/p/?LinkId=691833) | 不相容 |
| Cisco | ASR | IOS 15.1 (靜態)、IOS 15.2 (動態) | [Cisco 範例](http://go.microsoft.com/fwlink/p/?LinkId=691833) | [Cisco 範例](http://go.microsoft.com/fwlink/p/?LinkId=691833) |
| Cisco | ISR | IOS 15.0 (靜態)、IOS 15.1 (動態) | [Cisco 範例](http://go.microsoft.com/fwlink/p/?LinkId=691833) | [Cisco 範例](http://go.microsoft.com/fwlink/p/?LinkId=691833) |
| Citrix | CloudBridge MPX 裝置或 VPX 虛擬裝置 | N/A | [整合指示](https://www.citrix.com/welcome.html?resource=%2Fdownloads%2Fcloudbridge%2Fbetas-and-tech-previews%2Fcloudbridge-azure-integration) | 不相容 |
| Dell SonicWALL | TZ 系列、NSA 系列、SuperMassive 系列、E 級 NSA 系列 | SonicOS 5.8.x、SonicOS 5.9.x、SonicOS 6.x | [組態指示](https://www.sonicwall.com/app/projects/file_downloader/document_lib.php?t=TN&id=348) | 不相容 |
| F5 | BIG-IP 系列 | N/A | [組態指示](https://devcentral.f5.com/articles/connecting-to-windows-azure-with-the-big-ip) | 不相容 |
| Fortinet | FortiGate | FortiOS 5.0.7 | [組態指示](http://docs.fortinet.com/fortigate/admin-guides) | [組態指示](http://docs.fortinet.com/fortigate/admin-guides) |
| Internet Initiative Japan (IIJ) | SEIL 系列 | SEIL/X 4.60、SEIL/B1 4.60、SEIL/x86 3.20 | [組態指示](http://www.iij.ad.jp/biz/seil/ConfigAzureSEILVPN.pdf) | 不相容 |
| Juniper | SRX | JunOS 10.2 (靜態)、JunOS 11.4 (動態) | [Juniper 範例](http://go.microsoft.com/fwlink/p/?LinkId=691833) | [Juniper 範例](http://go.microsoft.com/fwlink/p/?LinkId=691833) |
| Juniper | J 系列 | JunOS 10.4r9 (靜態)、JunOS 11.4 (動態) | [Juniper 範例](http://go.microsoft.com/fwlink/p/?LinkId=691833) | [Juniper 範例](http://go.microsoft.com/fwlink/p/?LinkId=691833) |
| Juniper | ISG | ScreenOS 6.3 (靜態和動態) | [Juniper 範例](http://go.microsoft.com/fwlink/p/?LinkId=691833) | [Juniper 範例](http://go.microsoft.com/fwlink/p/?LinkId=691833) |
| Juniper | SSG | ScreenOS 6.2 (靜態和動態) | [Juniper 範例](http://go.microsoft.com/fwlink/p/?LinkId=691833) | [Juniper 範例](http://go.microsoft.com/fwlink/p/?LinkId=691833) |
| Microsoft | 路由及遠端存取服務 | Windows Server 2012 | 不相容 | [Microsoft 範例](http://go.microsoft.com/fwlink/p/?LinkId=691833) |
| Openswan | Openswan | 2\.6.32 | (敬請期待) | 不相容 |
| Palo Alto Networks | 所有執行 PAN-OS 5.0 或更新版本的裝置 | PAN-OS 5.x 或更新版本 | [Palo Alto Networks](https://support.paloaltonetworks.com/) | 不相容 |
| Watchguard | 全部 | Fireware XTM v11.x | [組態指示](http://customers.watchguard.com/articles/Article/Configure-a-VPN-connection-to-a-Windows-Azure-virtual-network/) | 不相容 |


### 裝置不在相容清單上


如果在已知的相容 VPN 裝置清單中沒看到您的裝置，但想要使用它進行 VPN 連線，則您需要確認其符合[閘道器需求](vpn-gateway-about-vpngateways.md#gateway-requirements)表中列出的最低需求。符合最低需求的裝置也應該適用於虛擬網路。如需額外支援和設定指示，請連絡裝置製造商。


## 編輯裝置組態範本

下載提供的 VPN 裝置組態範本之後，您將需要取代其中一些值，以反映您環境的設定。

**編輯範本：**

1. 使用 [記事本] 開啟範本。 
1. 搜尋並取代所有 <*文字*> 字串為適合您環境的值。請記得加上 < and >。當有指定名稱時，您選取的名稱應該是唯一名稱。如果命令無法運作，請參閱裝置製造商文件。

| **範本中的文字** | **變更為** |
|----------------------------------|----------------------------------------------------------------------------------------------------------------------|
| &lt;RP\_OnPremisesNetwork&gt; | 您為此物件選擇的名稱。例如：myOnPremisesNetwork |
| &lt;RP\_AzureNetwork&gt; | 您為此物件選擇的名稱。例如：myAzureNetwork |
| &lt;RP\_AccessList&gt; | 您為此物件選擇的名稱。例如：myAzureAccessList |
| &lt;RP\_IPSecTransformSet&gt; | 您為此物件選擇的名稱。例如：myIPSecTransformSet |
| &lt;RP\_IPSecCryptoMap&gt; | 您為此物件選擇的名稱。例如：myIPSecCryptoMap |
| &lt;SP\_AzureNetworkIpRange&gt; | 指定範圍。例如：192.168.0.0 |
| &lt;SP\_AzureNetworkSubnetMask&gt; | 指定子網路遮罩。例如：255.255.0.0 |
| &lt;SP\_OnPremisesNetworkIpRange&gt; | 指定內部部署範圍。例如：10.2.1.0 |
| &lt;SP\_OnPremisesNetworkSubnetMask&gt; | 指定內部部署子網路遮罩。例如：255.255.255.0 |
| &lt;SP\_AzureGatewayIpAddress&gt; | 此資訊專屬於您的虛擬網路，是 [管理入口網站] 中的 [**閘道器 IP 位址**]。 |
| &lt;SP\_PresharedKey&gt; | 此資訊專屬於您的虛擬網路，是 [管理入口網站] 中的管理金鑰。 |



## IPsec 參數

### IKE 階段 1 設定

| **屬性** | **靜態路由 VPN 閘道** | **動態路由 VPN 閘道和標準或高效能 VPN 閘道** |
|----------------------------------------------------|--------------------------------|------------------------------------------------------------------|
| IKE 版本 | IKEv1 | IKEv2 |
| Diffie-Hellman 群組 | 群組 2 (1024 位元) | 群組 2 (1024 位元) |
| 驗證方法 | 預先共用金鑰 | 預先共用金鑰 |
| 加密演算法 | AES256 AES128 3DES | AES256 3DES |
| 雜湊演算法 | SHA1(SHA128) | SHA1(SHA128) |
| 階段 1 安全性關聯 (SA) 存留期 (時間) | 28,800 秒 | 28,800 秒 |


### IKE 階段 2 設定

| **屬性** | **靜態路由 VPN 閘道** | **動態路由 VPN 閘道和標準或高效能 VPN 閘道** |
|--------------------------------------------------------------------------|------------------------------------------------|--------------------------------------------------------------------|
| IKE 版本 | IKEv1 | IKEv2 |
| 雜湊演算法 | SHA1(SHA128) | SHA1(SHA128) |
| 階段 2 安全性關聯 (SA) 存留期 (時間) | 3,600 秒 | - | | 階段 2 安全性關聯 (SA) 存留期 (輸送量) | 102,400,000 KB | - | | IPsec SA 加密及驗證提供項目 (依喜好順序) | 1.ESP-AES256 2.ESP-AES128 3.ESP-3DES 4.N/A | 請參閱動態路由閘道器 IPsec安全性關聯 (SA) 提供項目 | | 完整轉寄密碼 (PFS) | 否 | 是 (DH Group1) | | 停用的對等偵測 (DPD) | 不支援 | 支援 |

### 動態路由閘道器道 IPsec 安全性關聯 (SA) 提供項目

下表列出 IPsec SA 加密及驗證提供項目。提供項目是依其呈現或被接受的喜好設定順序而列出。

| **IPsec SA 加密及驗證提供項目** | **Azure 閘道器為啟動者** | Azure 閘道器為回應者 |
|---------------------------------------------------|--------------------------------------------------------------|--------------------------------------------------------------|
| 1 | ESP AES\_256 SHA | ESP AES\_128 SHA |
| 2 | ESP AES\_128 SHA | ESP 3\_DES MD5 |
| 3 | ESP 3\_DES MD5 | ESP 3\_DES SHA |
| 4 | ESP 3\_DES SHA | AH SHA1 與 ESP AES\_128 與 null HMAC |
| 5 | AH SHA1 與 ESP AES\_256 與 null HMAC | AH SHA1 與 ESP 3\_DES 與 null HMAC |
| 6 | AH SHA1 與 ESP AES\_128 與 null HMAC | AH MD5 與 ESP 3\_DES 與 null HMAC，未提議存留期 |
| 7 | AH SHA1 與 ESP 3\_DES 與 null HMAC | AH SHA1與 ESP 3\_DES SHA1，無存留期 |
| 8 | AH MD5 與 ESP 3\_DES 與 null HMAC，未提議存留期 | AH MD5 與 ESP 3\_DES MD5，無存留期 |
| 9 | AH SHA1與 ESP 3\_DES SHA1，無存留期 | ESP DES MD5 |
| 10 | AH MD5 與 ESP 3\_DES MD5，無存留期 | ESP DES SHA1，無存留期 |
| 11 | ESP DES MD5 | AH SHA1 與 ESP DES null HMAC，未提議存留期 |
| 12 | ESP DES SHA1，無存留期 | AH MD5 與 ESP DES null HMAC，未提議存留期 |
| 13 | AH SHA1 與 ESP DES null HMAC，未提議存留期 | AH SHA1與 ESP DES SHA1，無存留期 |
| 14 | AH MD5 與 ESP DES null HMAC，未提議存留期 | AH MD5 與 ESP DES MD5，無存留期 |
| 15 | AH SHA1與 ESP DES SHA1，無存留期 | ESP SHA，無存留期 |
| 16 | AH MD5 與 ESP DES MD5，無存留期 | ESP MD5，無存留期 |
| 17 | - | AH SHA，無存留期 | | 18 | - | AH MD5，無存留期 |




- 您可以使用動態路由和高效能 VPN 閘道來指定 IPsec ESP NULL 加密，以便用於 Azure 網路內的 VNet 對 VNet 連線。 

- 透過網際網路的跨單位連線，請使用含有加密和雜湊演算法的預設 Azure VPN 閘道設定 (如上表所列)，以確保重要通訊的安全性。

## 後續步驟


若要深入了解 VPN 閘道，請參閱〈[關於 VPN 閘道](vpn-gateway-about-vpngateways.md)〉。

若要設定站對站 VPN，請參閱〈[建立虛擬網路與站對站 VPN 連線](vpn-gateway-site-to-site-create.md)〉。

<!---HONumber=Nov15_HO1-->