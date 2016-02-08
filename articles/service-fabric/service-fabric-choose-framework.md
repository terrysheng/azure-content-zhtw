<properties
   pageTitle="Service Fabric 程式撰寫模型 | Microsoft Azure"
   description="Service Fabric 提供兩種架構來建置服務：動作項目架構和服務架構。它們在簡化與控制中提供不同的取捨。"
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="01/26/2016"
   ms.author="seanmck"/>

# 選擇您服務的架構

Azure Service Fabric 提供兩個高階架構來建置服務：Reliable Services API 和 Reliable Actors API。雖然兩者都建置於相同的 Service Fabric 核心，但會在並行處理、資料分割以及通訊方面的簡易性和彈性之間進行不同的權衡。了解這兩個模型相當實用，以便您可以在應用程式中選擇特定服務的適當架構。

## 比較 Reliable Actors API 和 Reliable Services API

|**選擇 Reliable Actors API 的時機**|**選擇 Reliable Services API 的時機**|
|-----------------------|--------------------------|
|您的問題領域涉及大量 (1000 個以上) 小型的獨立狀態和邏輯單位。|您需要跨越多個元件維護邏輯。|
|您想要使用不需要大量外部互動的單一執行緒物件。|您想要使用 Reliable Collections (例如 .NET Reliable Dictionary 和 Reliable Queue) 來儲存和管理您的狀態。|
|您想要讓平台為您管理通訊。|您想要管理通訊和控制您服務的資料分割配置。|

請記住，在您的應用程式中針對不同的服務使用不同的架構相當合理。例如，您可能擁有可設定狀態的服務，用來彙總由一些動作項目所產生的資料。

## 後續步驟

- [深入了解 Reliable Actors API](service-fabric-reliable-actors-introduction.md)
- [深入了解 Reliable Services API](../Service-Fabric/service-fabric-reliable-services-introduction.md)

<!---HONumber=AcomDC_0128_2016-->