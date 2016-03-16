<properties 
   pageTitle="使用 Azure 資源管理員設定適用於自訂探查的應用程式閘道 | Microsoft Azure"
   description="本頁面提供使用 Azure 資源管理員，在應用程式閘道設定自訂探查的指示。"
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
   ms.date="11/24/2015"
   ms.author="joaoma"/>

# 健全狀況監視和自訂探查 


Azure 應用程式閘道會監視其後端集區中所有資源的健康狀況，並自動從集區中移除任何被視為狀況不良的資源。

您可以使用兩種探查設定健全狀況監視：預設健全狀況探查和自訂探查。

## 預設的健全狀況探查

如果您沒有設定任何自訂探查組態，應用程式閘道就會自動設定預設健全狀況探查。監視行為的運作方式是對後端集區所設定的 IP 位址，以及針對應用程式閘道的後端 HTTP 設定所設定的連接埠，提出 HTTP 要求。

例如：您將應用程式閘道設定為使用 A、B 和 C 後端伺服器來接收連接埠 80 上的 HTTP 網路流量。預設健全狀況監視每 30 秒就會對三部伺服器進行一次測試，以取得狀況良好的 HTTP 回應。狀況良好的 HTTP 回應具有 200 到 399 之間的[狀態碼](https://msdn.microsoft.com/library/aa287675.aspx)。

如果伺服器 A 的預設探查檢查失敗，應用程式閘道就會將其從後端集區中移除，網路流量也不會再流向此伺服器。預設探查仍會繼續每 30 秒檢查一次伺服器 A。當伺服器 A 成功回應預設健全狀態探查所提出的要求時，就會變為狀況良好並重新回到後端集區中，而流量也會開始再次流向該伺服器。

預設探查只會使用 IP 位址來檢查狀態。如果您想要藉由測試對 URL 的連線能力來檢查健全狀況，您必須使用自訂探查。


## 自訂探查 

自訂探查可讓您更細微地控制健全狀況監視。使用自訂探查時，您可以設定探查間隔檢查、要測試的 URL 和路徑，以及在將後端集區執行個體標示為狀況不良前，可接受的失敗回應次數。


自訂探查設定：

- **探查間隔** - 設定探查間隔檢查。
- **探查逾時** - 定義 HTTP 要求檢查的探查逾時。
- **狀況不良臨界值** - 指定需要有多少失敗的要求才會將執行個體標示為狀況不良。  
- **主機名稱與路徑** - 如果您的網站或 Web 伺服器陣列沒有 IP 位址的 HTTP 回應，您需要針對有效狀況良好的 HTTP 回應設定探查主機名稱和路徑。例如：您有網站 http://contoso.com/，但是並未產生有效的 HTTP 回應。必須設定主機名稱和路徑以提供有效的狀況良好 HTTP 回應，來驗證 Web 伺服器執行個體的狀況是否良好。在此案例中，為 "http://contoso.com/path/custompath.htm" 設定自訂探查，以便讓探查檢查有成功的 HTTP 回應。 



>[AZURE.NOTE] 自訂探查只能使用 PowerShell 設定

<!---HONumber=AcomDC_0128_2016-->