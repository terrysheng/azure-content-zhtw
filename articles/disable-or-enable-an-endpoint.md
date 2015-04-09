<properties
	   pageTitle="停用或啟用流量管理員端點"
	   description="本文將協助停用或啟用流量管理員設定檔端點。"
	   services="traffic-manager"
	   documentationCenter="na"
	   authors="cherylmc"
	   manager="adinah"
	   editor="tysonn" />
<tags 
	   ms.service="traffic-manager"
	   ms.devlang="na"
	   ms.topic="article"
	   ms.tgt_pltfrm="na"
	   ms.workload="infrastructure-services"
	   ms.date="02/23/2015"
	   ms.author="cherylmc" />

# 停用或啟用流量管理員端點

您也可以停用屬於流量管理員設定檔一部分的個別端點。端點包括雲端服務和網站。停用端點會將它保留為設定檔的一部分，但是設定檔的動作會像是端點並未包含在內一樣。這個動作非常適合暫時移除處於維護模式或重新部署的端點。端點一旦再次啟動並執行，即可予以啟用。

[AZURE.NOTE] **停用端點與端點在 Azure 中的部署狀態無關。健全的端點將會持續運作，並且即使在流量管理員中停用該服務時仍然可以接收流量。此外，停用一個設定檔中的端點並不會影響它在另一個設定檔中的狀態。**

## 停用端點

1. 在管理入口網站的 [流量管理員] 窗格中，找出包含您想要修改之端點設定的流量管理員設定檔，然後按一下設定檔名稱右側的箭號。這會開啟設定檔的 [設定] 頁面。
1. 在頁面頂端，按一下 [**端點**] 檢視組態中所含的端點。 
1. 按一下您想要停用的端點，然後按一下頁面底部的 [**停用**]。
1. 根據針對流量管理員網域名稱設定的 DNS 存留時間 (TTL)，流量將停止流向端點。您可以從流量管理員設定檔的 [組態] 頁面中變更 TTL。

## 啟用端點


1. 在管理入口網站的 [流量管理員] 窗格中，找出包含您想要修改之端點設定的流量管理員設定檔，然後按一下設定檔名稱右側的箭號。這會開啟設定檔的 [設定] 頁面。
1. 在頁面頂端，按一下 [**端點**] 檢視組態中所含的端點。
1. 按一下您想要啟用的端點，然後按一下頁面底部的 [**啟用**]。
1. 流量會如設定檔指定再次開始流向服務。

## 另請參閱

[流量管理員組態工作](https://msdn.microsoft.com/library/azure/hh744830.aspx)

[流量管理員概觀](traffic-manager.md)

[雲端服務](http://go.microsoft.com/fwlink/?LinkId=314074)

[網站](http://go.microsoft.com/fwlink/p/?LinkId=393327)


[流量管理員的相關作業 (REST API 參考)](http://go.microsoft.com/fwlink/?LinkId=313584)

<!--HONumber=49-->