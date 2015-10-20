<properties
   pageTitle="重設 Azure VPN 閘道 | Microsoft Azure"
   description="本文將引導您重設您的 Azure VPN 閘道。本文適用於使用傳統部署模型所建立的 VPN 閘道。"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carolz"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/16/2015"
   ms.author="cherylmc"/>

# 使用 PowerShell 重設 Azure VPN 閘道


本文將引導您使用 PowerShell cmdlet 重設 Azure VPN 閘道。這些指示適用於傳統部署模型。我們還沒有 cmdlet 或 REST API 為使用資源管理員部署模型建立的虛擬網路重設 VPN 閘道。這些都在進行中。您可以藉由在預覽入口網站中檢視您的虛擬網路，分辨您的 VPN 閘道是否使用傳統部署模型建立。使用傳統部署模型所建立的虛擬網路會顯示在預覽入口網站的虛擬網路 (傳統) 部分。

如果您遺失一或多個 S2S VPN 通道上的跨單位 VPN 連線，重設 Azure VPN 閘道會很有幫助。在此情況下，您的所有內部部署 VPN 裝置都會運作正常，但無法使用 Azure VPN 閘道建立 IPsec 通道。當您使用 *Reset-AzureVNetGateway* cmdlet 時，它會重新啟動閘道器，並將跨單位組態重新套用至閘道器。閘道器會保留它永遠擁有的公用 IP 位址。這表示您不需要利用 Azure VPN 閘道的新公用 IP 位址更新 VPN 路由器組態。


在您重設您的閘道器之前，請確認以下所列每個 IPsec 站對站 (S2S) VPN 通道的重要項目。任何項目的不相符都會導致 S2S VPN 通道中斷連線。驗證並更正內部部署和 Azure VPN 閘道的組態可為您避免不必要的重新開機，也避免閘道器上的其他作用中連線中斷。

重設您的閘道器之前請確認下列項目。

- Azure VPN 閘道與在內部部署 VPN 閘道的網際網路 IP 位址 (VIP) 已利用 Azure 和內部部署 VPN 原則正確設定。
- 預先共用的金鑰在 Azure 和內部部署 VPN 閘道上必須是相同的。
- 如果您套用特定的 IPsec/IKE 組態，例如加密、雜湊演算法和 PFS (完整轉寄密碼)，請確定 Azure 和內部部署 VPN 閘道都具有相同組態。


## 使用 PowerShell 重設 VPN 閘道

用來重設 Azure VPN 閘道的 PowerShell cmdlet 是 *Reset-AzureVNetGateway*。每個 Azure VPN 閘道都是由在作用中待命組態中執行的兩個 VM 執行個體組成。一旦發出此命令，會立即重新啟動 Azure VPN 閘道的目前作用中執行個體。從作用中執行個體 (正在重新啟動) 容錯移轉到待命執行個體期間，會有短暫的間隔。此間隔應該不超過一分鐘。

下列範例會重設稱為 "ContosoVNet" 的虛擬網路的 Azure VPN 閘道。
 
			D:\PS> Reset-AzureVNetGateway –VnetName “ContosoVNet” 

	 		Error          :
	 		HttpStatusCode : OK
	 		Id             : f1600632-c819-4b2f-ac0e-f4126bec1ff8
	 		Status         : Successful
			RequestId      : 9ca273de2c4d01e986480ce1ffa4d6d9
			StatusCode     : OK


如果連接未在第一次重新啟動之後儲存，請再次發出相同的命令來重新啟動第二個 VM 執行個體 (新的使用中閘道器)。如果接連要求兩次重新啟動，兩個 VM 執行個體 (作用中和待命) 的重新啟動時間還要更久一點。此情況會導致 VPN 連線上較長的間隔，要讓 VM 完成重新啟動，最多需要 2 到 4 分鐘。

在兩次重新啟動之後，如果仍有跨單位連線問題，請從 Azure 入口網站開啟支援票證以連絡 Microsoft Azure 支援服務。


## 後續步驟
	
如需這個 cmdlet 的詳細資訊，請參閱 [PowerShell 參考](https://msdn.microsoft.com/library/azure/mt270366.aspx)。

<!---HONumber=Oct15_HO3-->