<properties
   pageTitle="具狀態 Reliable Services 診斷 | Microsoft Azure"
   description="具狀態 Reliable Services 診斷功能"
   services="service-fabric"
   documentationCenter=".net"
   authors="AlanWarwick"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/03/2015"
   ms.author="alanwar"/>

# 具狀態 Reliable Services 診斷功能
具狀態 Reliable Services StatefulServiceBase 類別會發出 [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) 事件，可用於偵錯服務、提供執行階段運作情形的深入了解，以及協助疑難排解。

## EventSource 事件
具狀態 Reliable Services 之 StatefulServiceBase 類別的 EventSource 名稱是 "Microsoft-ServiceFabric-Services"。當服務[在 Visual Studio 中偵錯](service-fabric-debugging-your-application.md)時，此事件來源的事件會出現在[診斷事件](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio)視窗中。

可協助您收集和/或檢視 EventSource 事件之工具和技術的範例包括 [PerfView](http://www.microsoft.com/download/details.aspx?id=28567)、[Microsoft Azure 診斷](../cloud-services/cloud-services-dotnet-diagnostics.md)和 [Microsoft TraceEvent 程式庫](http://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent)。

## 事件

|事件名稱|事件識別碼|Level|事件說明|
|----------|--------|-----|-----------------|
|StatefulRunAsyncInvocation|1|資訊|啟動服務 RunAsync 工作時發出|
|StatefulRunAsyncCancellation|2|資訊|取消服務 RunAsync 工作時發出|
|StatefulRunAsyncCompletion|3|資訊|完成服務 RunAsync 工作時發出|
|StatefulRunAsyncSlowCancellation|4|警告|服務 RunAsync 工作花太多時間才能完成取消時發出|
|StatefulRunAsyncFailure|5|錯誤|服務 RunAsync 工作擲回例外狀況時發出|

## 解譯事件

StatefulRunAsyncInvocation、StatefulRunAsyncCompletion，和 StatefulRunAsyncCancellation 事件可讓服務撰寫者了解服務的生命週期--以及服務啟動、取消或完成的時機。這可能適用於偵錯服務問題或了解服務生命週期。

服務撰寫者應密切注意 StatefulRunAsyncSlowCancellation 及 StatefulRunAsyncFailure 事件，因為它們表示服務的問題。

只要服務 RunAsync() 工作擲回例外狀況，就會發出 StatefulRunAsyncFailure。通常，擲回例外狀況表示服務中的錯誤。此外，例外狀況會導致服務失敗，因此它會移至另一個節點。當服務移動時，這會是代價高昂的作業，且會延遲傳入的要求。服務撰寫者應該判斷例外狀況的原因，並盡可能減少這種情況。

只要 RunAsync 工作的取消要求所花費的時間超過四秒，就會發出 StatefulRunAsyncSlowCancellation。當服務完成取消所花費的時間太久，它會影響服務快速在另一個節點上重新啟動的能力。這可能會影響服務的整體可用性。

<!---HONumber=AcomDC_0211_2016-->