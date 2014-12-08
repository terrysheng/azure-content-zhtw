<properties title="Diagnose performance issues on a running website" pageTitle="在執行中的網站上診斷效能問題" description="Monitor a website's performance without re-deploying it." metaKeywords="analytics monitoring application insights" authors="awills"  manager="kamrani" />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="2014-09-24" ms.author="awills" />
 

# 立即監視即時網站的效能

*Application Insights 目前為預覽階段。*

Web 應用程式運作不正常嗎？快速診斷例外狀況和效能問題，不必重建或重新部署。在伺服器中安裝 Application Insights 代理程式，就能夠找出效能瓶頸並取得任何例外狀況的堆疊追蹤。 


#### 何時應該採用此方法來設定 Application Insights？

此方法主要是為了在即時 IIS 網站中快速診斷效能問題。

您只需要在伺服器中安裝代理程式，然後在 Application Insights 上查看效能資料。

- 您可以對託管於 IIS 伺服器的 ASP.NET 應用程式採取此作法。

- 您需要 [Microsoft Azure 帳戶](http://azure.com)，才能查看資料。

- 對於執行 Web 應用程式的伺服器，您需要有管理員存取權。 

- 您「*不*」需要 Web 應用程式的程式碼，也不必重建或重新部署應用程式。 

- 此方法會依 Web 應用程式的現況進行檢測。您不需要插入追蹤或記錄程式碼(但如果想要的話，後來也可以這樣做)。

如果您要插入記錄或診斷追蹤，請在這裡停住 - 改為[將 Application Insights 加入至專案][greenbrown]並重新部署。如需了解所有選項，請參閱 [Application Insights - 開始使用][start]。

## 在 Web 伺服器上安裝 Application Insights 代理程式

1. 在網頁瀏覽器中，以系統管理員認證登入。

2. 請確定您有 [Web Platform Installer](http://www.microsoft.com/web/downloads/platform.aspx) 5.0 版或更新版本。
3. 使用 Web Platform Installer 安裝 Application Insights 代理程式。

    ![](./media/appinsights/appinsights-031-wpi.png)
4. 在安裝精靈中，登入 Microsoft Azure。

    ![](./media/appinsights/appinsights-035-signin.png)
5. 挑選您要監視的已安裝的 Web 應用程式或網站，然後設定您在 Application Insights 入口網站中查看結果時想要使用的名稱。最後，按一下 [新增] 按鈕。

    ![](./media/appinsights/appinsights-036-configAIC.png)

    通常應該會選擇建立新的資源。

    假設您已設定網站的 [Web 測試][availability]，則您可能會使用現有的資源。  

6. 請注意，ApplicationInsights.config 已插入至您要監視的網站中。

    ![](./media/appinsights/appinsights-034-aiconfig.png)

   web.config 也有一些變動。

### 稍後再 (重新) 設定嗎？

完成精靈之後，您隨時都可以重新設定代理程式。如果已安裝代理程式，但初始設定有一些問題，則您也可以這樣做。

![Click the Application Insights icon on the task bar](./media/appinsights/appinsights-033-aicRunning.png)

## 檢視資料

在 Azure 中開啟您的帳戶，瀏覽 Application Insights 並開啟您已建立的資源。

![](./media/appinsights/appinsights-08openApp.png)

您在 [應用程式健全狀況] 下可以看到資料。

![](./media/appinsights/appinsights-037-results.png)

#### 沒有資料？

  * 使用您的網站來產生一些資料。
  * 等待幾分鐘讓資料到來。
  * 請確定伺服器防火牆允許連出流量從連接埠 443 送往 dc.services.visualstudio.com 

#### 「應用程式健全狀況」報告有何用途？
 * [了解資料並設定磚][perf]

## <a name="next"></a>後續步驟


[檢視資料][perf]

[搜尋診斷記錄 (英文)][diagnostic]

[Web 測試][availability]

[設定使用情況監視][start]

[疑難排解][qna]




[AZURE.INCLUDE [app-insights-learn-more](../includes/app-insights-learn-more.md)]



