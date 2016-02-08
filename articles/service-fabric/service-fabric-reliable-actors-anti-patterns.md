<properties
   pageTitle="部分的 Azure Service Fabric Actors 相反模式 | Microsoft Azure"
   description="正在學習 Azure Service Fabric Actor 的客戶可能會碰到的問題"
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/11/2015"
   ms.author="vturecek"/>

# Reliable Actors 設計模式：某些相反模式

我們為正在學習 Azure Service Fabric Reliable Actors 的客戶找出下列潛在問題：

* 將 Reliable Actors 視為交易式系統。Service Fabric Reliable Actors 不是提供 ACID (不可部分完成的作業 (Atomicity)、一致性 (Consistency)、隔離 (Isolation) 和持久性 (Durability)) 的雙階段認可式系統。如果您未實作選擇性的持續性，且動作項目執行所在的機器中止了，則其目前的狀態會隨之中止。動作項目會非常快地在另一個節點上產生，但除非您已實作支援的持續性，否則狀態將會消失。然而，利用重試次數、重複篩選、和/或等冪設計，仍可達到高階的可靠性與一致性。

* 區塊。您在 Reliable Actors 中所做的一切應該非同步。這並不難，因為現在 Microsoft 平台中的非同步化 API 很豐富。但如果您必須與僅提供封鎖中 API 的系統互動，您必須將該系統放在明確使用 .NET 執行緒集區的包裝函式中。

* 過度設計架構並讓環境運作。習慣擔心並行集合與鎖定，或是使用工具編譯 XML 物件的開發人員，很難僅對於將值指派給變數或排程工作等簡單事項的類別進行編碼。排定的工作為內建工作，不需要鎖定。狀態不是死敵。這會讓在大規模環境中進行很多伺服器端工作的開發人員漸漸習慣。

* 讓單一動作項目成為瓶頸。有數百萬個動作項目注入另一個動作項目的單一執行個體時，很容易被困住。請使用我們在[分散式運算設計模式](service-fabric-reliable-actors-pattern-distributed-computation.md)中示範的彙總方法。

* 盲目地對應實體模型。這適用於來自相關體系的開發人員，在該體系會使用實體及其關係建立問題的模型。雖然此方法對於了解主體網域仍然很有幫助，但應搭配以服務為導向的思考模式與行為。

<!---HONumber=AcomDC_0128_2016-->