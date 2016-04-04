<properties
   pageTitle="Azure 流量管理員的效能考量 | Microsoft Azure"
   description="了解流量管理員的效能，以及如何在使用流量管理員時測試您的網站效能"
   services="traffic-manager"
   documentationCenter=""
   authors="kwill-MSFT"
   manager="adinah"
   editor="joaoma" />

<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/09/2016"
   ms.author="joaoma" />


# 流量管理員的效能考量

此頁面說明使用流量管理員的效能考量。例如案例：您有在美國區域中有網站，在亞洲有一個網站，其中一個區域的流量管理員探查健全狀況檢查失敗，所有使用者會被導向至狀況良好的區域，行為會顯示為效能問題，但是根據使用者要求的距離，可能是預期的行為。

  

## 流量管理員運作方式的重要事項

- 流量管理員基本上只會做一件事 – DNS 解析。這表示流量管理員可以在網站造成的唯一效能影響就是初始 DNS 查閱。
- 關於流量管理員 DNS 查閱的釐清點。流量管理員會根據您的原則和探查結果，填入並定期更新一般 Microsoft DNS 根伺服器。所以即使在初始 DNS 查閱期間，流量管理員還是沒有造成影響，因為 DNS 要求是由一般 Microsoft DNS 根伺服器處理的。如果流量管理員「故障」 (也就是說，執行原則探查和 DNS 更新的 VM 失敗)，也不會影響到您的流量管理員 DNS 名稱，因為 Microsoft DNS 伺服器中的這些項目仍然會保留 – 唯一的影響是根據原則的探查和更新將不會執行 (也就是說，如果主要網站故障，流量管理員將無法將 DNS 更新為指向您的容錯移轉網站)。
- 流量不會透過流量管理員流動。沒有流量管理員可以做為用戶端與 Azure 裝載服務之間的中介。一旦完成 DNS 查閱，流量管理員會完全從用戶端和伺服器之間的通訊移除。
- DNS 查閱速度非常快，而且可快取。初始 DNS 查閱將取決於用戶端和其設定的 DNS 伺服器，用戶端通常可以在 ~50 毫秒內完成 DNS 查閱 (請參閱 http://www.solvedns.com/dns-comparison/)。完成第一次查閱之後，隨即會快取 DNS TTL 的結果，對流量管理員的預設值是 300 秒。
- 您選擇的流量管理員原則 (效能、容錯移轉、循環配置資源) 並不會影響到 DNS 效能。您的效能原則可能會對使用者的體驗造成負面影響，例如，如果您將美國的使用者傳送至裝載於亞洲的服務時，這個效能問題並不是流量管理員所造成。

  

## 測試流量管理員效能

有幾個公開可用的網站，您可以用來判斷您的流量管理員效能和行為。這些網站可用來判斷 DNS 延遲，以及全世界的使用者將會被導向哪一個裝載服務。請記住，大部分的工具並不會快取 DNS 結果，所以多次執行測試可以顯示完整的 DNS 查閱，而連結到流量管理員端點的用戶端只會在 TTL 期間看到完整的 DNS 查閱效能影響一次。


## 測量效能的範例工具


其中一個最簡單的工具是 WebSitePulse。輸入 URL，您就會看到統計資料，例如 DNS 解析時間、第一個位元組、最後一個位元組，以及其他效能統計資料。您可以從三個不同的位置選擇要在其中測試您網站的位置。在本範例中，您會看到第一次執行顯示第一個 DNS 查閱所需時間為 0.204 秒。我們在相同的流量管理員端點第二次執行這項測試時，DNS 查閱所需時間為 0.002 秒，因為結果已經快取過了。

http://www.websitepulse.com/help/tools.php


![pulse1](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse.png)

已快取時的 DNS 所需時間：


![pulse2](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse2.png)



如果要同時從多個地理區域取得 DNS 解析時間，另一個真正實用的工具是 Watchmouse 的核取網站工具。輸入 URL，您就會看見 DNS 解析時間、連接時間，以及來自數個地理位置的速度。這樣也可以方便測試流量管理員效能原則，以查看全世界的不同使用者會傳送到哪個裝載服務。

http://www.watchmouse.com/en/checkit.php


![pulse1](./media/traffic-manager-performance-considerations/traffic-manager-web-site-watchmouse.png)

http://tools.pingdom.com/ - 這會測試網站，並在視覺圖形上提供頁面上每個項目的效能統計資料。如果您切換到 [頁面分析] 索引標籤，您就可以看到執行 DNS 查閱所花費的時間百分比。

 

http://www.whatsmydns.net/ – 這個網站將會從 20 個不同的地理位置執行 DNS 查閱，並將結果顯示在地圖上。這是絕佳的視覺表示法，可協助判斷您的用戶端會連結到哪個裝載服務。

 

http://www.digwebinterface.com – 與 watchmouse 站台類似，但此站台會顯示更多 DNS 的詳細資訊，包括 CNAME 和 A 記錄。請務必檢查選項下的 ‘Colorize output’ 與 ‘Stats’ 並選取 Nameservers 下的 'All'。

## 後續步驟


[關於流量管理員流量路由方法](traffic-manager-routing-methods.md)

[測試流量管理員設定](traffic-manager-testing-settings.md)

[流量管理員的相關作業 (REST API 參考)](http://go.microsoft.com/fwlink/?LinkId=313584)

[Azure 流量管理員 Cmdlet](http://go.microsoft.com/fwlink/p/?LinkId=400769)
 

<!---HONumber=AcomDC_0323_2016-->