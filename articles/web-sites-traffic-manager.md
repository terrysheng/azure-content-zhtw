<properties 
	pageTitle="使用 Azure 流量管理員來控制 Azure 網站的流量" 
	description="本文提供 Azure 網站相關之 Azure 流量管理員的摘要資訊。" 
	services="web-sites" 
	documentationCenter="" 
	authors="cephalin" 
	writer="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/24/2014" 
	ms.author="cephalin"/>

# 使用 Azure 流量管理員來控制 Azure 網站的流量

> [AZURE.NOTE] 本文提供 Azure 網站相關之 Microsoft Azure 流量管理員的摘要資訊。如需 Azure 流量管理員本身的詳細資訊，請造訪本文結尾的連結。

## 簡介
您可以使用 Azure 流量管理員，控制如何將來自 Web 用戶端的要求分散給多個 Azure 網站。將 Azure 網站端點新增至 Azure 流量管理員設定檔時，Azure 流量管理員會持續追蹤您網站的狀態 (執行中、已停止或已刪除)，以便判定其中哪些端點應接收流量。

## 負載平衡方法
Azure 流量管理員使用三種不同的負載平衡方法。下列清單說明這些方法，因為它們專屬於 Azure 網站。 

* **容錯移轉**：如果您在不同地區有網站複本，您可以使用此方法將一個網站設定為服務所有 Web 用戶端流量；並在不同地區設定其他網站，以便在第一個網站無法使用時服務該流量。 
	
* **循環配置資源**：如果您在不同地區有網站複本，則可以使用此方法，將流量平均分散在不同地區的網站。 
	
* **效能**：「效能」方法可根據前往用戶端的最短來回時間來分散流量。「效能」方法可用於相同地區或不同地區內的網站。 

如需 Azure 流量管理員中負載平衡的詳細資訊，請參閱[關於 Traffic Manager 負載平衡方法](http://msdn.microsoft.com/zh-tw/library/windowsazure/dn339010.aspx)。

##Azure 網站和流量管理員設定檔 
若要設定以控制網站流量，可以在使用上述三種負載平衡方法的其中一種之 Azure 流量管理員中建立設定檔，然後將要控制其流量的端點 (在此情況下為網站) 新增到設定檔。系統會定期與設定檔溝通您的網站狀態 (執行中、已停止或已刪除)，讓 Azure 流量管理員可相應地導向流量。

搭配使用 Azure 流量管理員與 Azure 時，請牢記下列重點：

* 對於相同地區內的純網站部署，Azure 網站已提供與網站模式無關的容錯移轉和循環配置資源功能。

* 若是在搭配使用 Azure 網站與其他 Azure 雲端服務的相同地區中進行部署，您可以結合兩種端點類型來啟用混合案例。

* 您只能針對一個設定檔的每個地區指定一個網站。選取一個網站作為一個地區的端點時，將無法為該設定檔選擇使用該地區中的剩餘網站。

* 您在 Azure 流量管理員設定檔中指定的網站端點，將出現在設定檔中網站之 [設定] 頁面的 [網域名稱]**** 區段下方，但您無法在該處進行設定。

* 將網站新增至設定檔後，在網站之入口網站頁面的儀表板上，[網站 URL]**** 將顯示網站的自訂網域 URL (如果已設定一個)。否則會顯示流量管理員設定檔 URL (例如， `contoso.trafficmgr.com`)。網站的直接網域名稱和流量管理員 URL 二者，都會顯示在網站之 [設定] 頁面的 [網域名稱]**** 區段下方。

* 您的自訂網域名稱將如預期般運作，但除了將之新增至網站外，您還必須設定 DNS 對應以指向流量管理員 URL。如需有關如何設定 Azure 網站之自訂網域的詳細資訊，請參閱[設定 Azure 網站的自訂網域名稱](https://www.windowsazure.com/zh-tw/documentation/articles/web-sites-custom-domain-name/)。

* 您只能將「標準」模式的網站新增至 Azure 流量管理員設定檔。

## 後續步驟

如需 Azure 流量管理員的概念與技術概觀，請參閱[流量管理員概觀](http://msdn.microsoft.com/zh-tw/library/windowsazure/hh744833.aspx)。 

如需如何設定 Azure 流量管理員的詳細資訊，包括適用於 Azure 網站用途，請參閱[流量管理員設定工作](http://msdn.microsoft.com/zh-tw/library/windowsazure/hh744830.aspx)。

如需 Azure 流量管理員中負載平衡的詳細資訊，請參閱[關於 Traffic Manager 負載平衡方法](http://msdn.microsoft.com/zh-tw/library/windowsazure/dn339010.aspx)。

如需有關使用流量管理員搭配 Azure 網站的詳細資訊，請參閱部落格文章 
[使用 Azure 流量管理員搭配 Azure 網站](http://blogs.msdn.com/b/waws/archive/2014/03/18/using-windows-azure-traffic-manager-with-waws.aspx)與 [Azure 流量管理員現在可以與Azure 網站整合](http://azure.microsoft.com/blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/)。


<!--HONumber=42-->
