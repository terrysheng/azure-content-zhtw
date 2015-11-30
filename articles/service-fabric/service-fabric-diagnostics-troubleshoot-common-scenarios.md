<properties
   pageTitle="常見問題疑難排解 | Microsoft Azure"
   description="在 Microsoft Azure Service Fabric 上部署服務時最常遇到的問題。"
   services="service-fabric"
   documentationCenter=".net"
   authors="mattrowmsft"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/10/2015"
   ms.author="mattrow"/>


# 常見問題疑難排解
在開發人員機器上執行服務時，很容易使用 [Visual Studio 的偵錯工具](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)。就遠端叢集而言，[健康情況報告](service-fabric-view-entities-aggregated-health.md)一律是個很好的起點。存取這些報告的最簡單方式就是透過 PowerShell 或 [SFX](service-fabric-visualizing-your-cluster.md)。本文假設您正在對遠端叢集進行偵錯，並且有任何這些工具的基本使用常識。

##應用程式損毀
「分割低於目標複本或執行個體數目」報告是一個指出您服務正要損毀的絕佳指標。若要找出您服務發生損毀的位置，需要進行深入一點的調查。大規模執行時，一組深思熟慮的線索將會是您的良伴。建議您嘗試使用 [WAD](service-fabric-diagnostics-how-to-setup-wad-operational-insights.md) 來收集和檢視這些線索。

![SFX 資料分割健康情況](./media/service-fabric-diagnostics-troubleshoot-common-scenarios/crashNewApp.png)

###在服務或動作項目初始化期間
在服務類型初始化之前發生的任何例外狀況都將導致程序損毀。針對這些類型的損毀，「應用程式事件記錄檔」將會顯示從您服務發生的錯誤。這些是服務初始化之前，最常見的例外狀況。

| 錯誤 | 說明 |
| --- | --- |
| System.IO.FileNotFoundException | 這些通常是因為遺失組件相依性所致。請在 Visual Studio 或 GAC 中檢查該節點的 CopyLocal 屬性。
| 在 System.Fabric.Interop.NativeRuntime+IFabricRuntime.RegisterStatefulServiceFactory(IntPtr, IFabricStatefulServiceFactory) 發生的 System.Runtime.InteropServices.COMException|這表示已註冊的服務類型名稱與服務資訊清單不符。 |

您可以將 [WAD](service-fabric-diagnostics-how-to-setup-wad-operational-insights.md) 設定成自動為您的所有節點上傳「應用程式事件記錄檔」。

###RunAsync() 或 OnActivateAsync()
如果是在已註冊的服務類型或動作項目的初始化或執行期間發生損毀，Service Fabric 就會捕捉到該例外狀況。您可以從＜後續步驟＞所述的 EventSource 提供者檢視這些例外狀況。

## 後續步驟

深入了解 Service Fabric 所提供的現有診斷：

* [動作項目診斷](service-fabric-reliable-actors-diagnostics.md)
* [Reliable Services 診斷](service-fabric-reliable-services-diagnostics.md)

<!---HONumber=Nov15_HO4-->