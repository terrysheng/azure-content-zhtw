<properties urlDisplayName="Performance Profiling" pageTitle="在 Azure (.NET) 中使用效能計數器" metaKeywords="Azure performance counters, Azure performance profiling, Azure performance counters C#, Azure performance profiling C#" description="了解如何在 Azure 應用程式中啟用和收集效能計數器的資料。" metaCanonical="" services="cloud-services" documentationCenter=".NET" title="在 Azure 中使用效能計數器" authors="ryanwi" solutions="" manager="timlt" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="ryanwi" />

# 在 Azure 中使用效能計數器

您可以在 Azure 應用程式中使用效能計數器來收集資料，
以協助判斷系統瓶頸及微調系統和應用程式效能。
適用於 Windows Server 2008、Windows Server 2012、IIS 和 ASP.NET 的效能計數器可用來收集資料，以判斷 Azure 應用程式的健康情況。

在 Visual Studio 2012 或 Visual Studio 2013 內，您可以在部署之前或在執行階段使用 Azure SDK 2.0 或更新版本來設定效能計數器。如需詳細資訊，請參閱[設定 Azure 診斷功能][設定 Azure 診斷功能]。如需在應用程式中手動設定效能計數器的相關資訊，請參閱[如何設定效能計數器][如何設定效能計數器]。

如需建立記錄及追蹤策略，以及使用診斷和其他技術來疑難排解問題的其他深入指引，請參閱[開發 Azure 應用程式的疑難排解最佳作法][開發 Azure 應用程式的疑難排解最佳作法] (英文)。

## <a name="nextsteps"> </a>後續步驟

請參考下列連結，以了解如何實作更複雜的疑難排解案例。

-   [測試雲端服務的效能][測試雲端服務的效能]
-   [開發 Azure 應用程式的疑難排解最佳作法][開發 Azure 應用程式的疑難排解最佳作法]
-   [如何監視雲端服務][如何監視雲端服務]
-   [如何使用自動調整應用程式區塊][如何使用自動調整應用程式區塊]
-   [建置彈性、恢復力強的雲端應用程式][建置彈性、恢復力強的雲端應用程式]

## <a name="additional"> </a>其他資源

-   [啟用 Azure 中的診斷][啟用 Azure 中的診斷]
-   [使用 Azure 診斷收集記錄資料][使用 Azure 診斷收集記錄資料]
-   [偵錯 Azure 應用程式][偵錯 Azure 應用程式]

  [設定 Azure 診斷功能]: http://msdn.microsoft.com/zh-tw/library/windowsazure/dn186185.aspx
  [如何設定效能計數器]: http://msdn.microsoft.com/zh-tw/library/azure/dn535595.aspx
  [開發 Azure 應用程式的疑難排解最佳作法]: http://msdn.microsoft.com/zh-tw/library/windowsazure/hh771389.aspx
  [測試雲端服務的效能]: http://msdn.microsoft.com/zh-tw/library/azure/hh369930.aspx
  [如何監視雲端服務]: http://azure.microsoft.com/zh-tw/documentation/articles/cloud-services-how-to-monitor/
  [如何使用自動調整應用程式區塊]: http://azure.microsoft.com/zh-tw/documentation/articles/cloud-services-dotnet-autoscaling-application-block/
  [建置彈性、恢復力強的雲端應用程式]: http://msdn.microsoft.com/zh-tw/library/hh680949(PandP.50).aspx
  [啟用 Azure 中的診斷]: http://azure.microsoft.com/zh-tw/documentation/articles/cloud-services-dotnet-diagnostics/
  [使用 Azure 診斷收集記錄資料]: http://msdn.microsoft.com/zh-tw/library/windowsazure/gg433048.aspx
  [偵錯 Azure 應用程式]: http://msdn.microsoft.com/zh-tw/library/windowsazure/ee405479.aspx
