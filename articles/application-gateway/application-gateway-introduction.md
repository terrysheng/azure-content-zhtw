<properties 
   pageTitle="應用程式閘道簡介 | Microsoft Azure"
   description="此頁面提供第 7 層負載平衡的應用程式閘道服務的概觀，包括閘道大小、HTTP 負載平衡、以 Cookie 為基礎的工作階段同質，以及 SSL 卸載。"
   documentationCenter="na"
   services="application-gateway"
   authors="joaoma"
   manager="carmonm"
   editor="tysonn"/>
<tags 
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="11/09/2015"
   ms.author="joaoma"/>

# 什麼是應用程式閘道？


Microsoft Azure 應用程式閘道會根據第 7 層負載平衡，提供 Azure 管理的 HTTP 負載平衡解決方案。

應用程式負載平衡可讓 IT 系統管理員與開發人員建立以 HTTP 為基礎的網路流量路由規則。應用程式閘道服務高度可用且計量。如需 SLA 和價格的詳細資訊，請參閱 [SLA](http://azure.microsoft.com/support/legal/sla/) 和[價格](https://azure.microsoft.com/pricing/details/application-gateway/)頁面。

應用程式閘道目前支援下列的第 7 層應用程式傳遞：

- HTTP 負載平衡
- 以 Cookie 為基礎的工作階段同質
- SSL 卸載

![應用程式閘道](./media/application-gateway-introduction/appgateway1.png)

HTTP 第 7 層負載平衡可用於：

- 需要要求來自相同使用者/用戶端工作階段，才能到達相同後端 VM 的應用程式。此情況的範例為：購物車應用程式和網頁郵件伺服器。
- 要從 SSL 終止負荷釋出 Web 伺服器陣列的應用程式。
- CDN 之類的應用程式，需要將在相同的長時間執行 TCP 連接上的多個 HTTP 要求遞送/負載平衡到不同的後端伺服器。


## 閘道大小和執行個體

應用程式閘道目前提供 3 大小：小型、中型和大型。小型執行個體大小是針對開發和測試案例。

每一訂用帳戶您可以建立最多 50 個應用程式閘道，而且每一應用程式閘道最多可以有 10 個執行個體。應用程式閘道負載平衡為 Azure 管理服務，可允許將第 7 層負載平衡器佈建在 Azure 軟體負載平衡器之後。

下表顯示每個應用程式的閘道執行個體的平均效能輸送量：


| 後端頁面回應 | 小型 | 中型 | 大型|
|---|---|---|---|
| 6K | 7\.5 mbps | 13 mbps | 50 mbps |
|100k | 35 mbps | 100 mbps| 200 mbps |


>[AZURE.NOTE]這是應用程式閘道輸送量的大概指引。實際的輸送量會依據平均頁面大小、後端執行個體的位置、到伺服器的處理時間等等的各種環境詳細資料。

## 健康狀況監視
 

Azure 應用程式閘道會自動監視後端執行個體的健康狀況。請移至[探查和應用程式閘道健康狀況監視](application-gateway-probe-overview.md)以取得詳細資訊。

## 設定和管理

您可以藉由使用 REST API，以及使用 PowerShell Cmdlet 來建立和管理應用程式閘道。



## 後續步驟

建立應用程式閘道。請參閱[建立應用程式閘道](application-gateway-create-gateway.md)。

設定 SSL 卸載。請參閱[使用應用程式閘道設定 SSL 卸載](application-gateway-ssl.md)。

<!---HONumber=AcomDC_0107_2016-->