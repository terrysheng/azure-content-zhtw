<properties 
	pageTitle="逐步解說：使用 Application Insights 監視 Microsoft Dynamics CRM"
	description="使用 Application Insights 取得 Microsoft Dynamics CRM Online 遙測。設定、取得資料、視覺化與匯出的逐步解說。"
	services="application-insights"
	documentationCenter=""
	authors="mazharmicrosoft"
	manager="douge"/>

<tags 
	ms.service="application-insights"
	ms.workload="tbd"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/04/2015"
	ms.author="awills"/>
 
# 逐步解說：使用 Application Insights 啟用Microsoft Dynamics CRM Online 遙測

本文說明如何使用 [Visual Studio Application Insights](https://azure.microsoft.com/services/application-insights/) 取得 [Microsoft Dynamics CRM Online](https://www.dynamics.com/) 遙測資料。我們會逐步解說將 Application Insights 指令碼加入至您的應用程式、擷取資料和資料視覺化的完整程序。

>[AZURE.NOTE] [Browse the sample solution](https://dynamicsandappinsights.codeplex.com/)。

## 將 Application Insights 加入至新的或現有的 CRM Online 執行個體 

若要監視您的應用程式，請將 Application Insights SDK 加入應用程式。所有 SDK 均會將遙測資料傳送至 [Application Insights 入口網站](https://portal.azure.com)，您可以在該入口網站中使用我們功能強大的分析與診斷工具，並將資料匯出至儲存體。

### 在 Azure 中建立 Application Insights 資源

1. 取得 [Microsoft Azure 帳戶](http://azure.com/pricing)。 
2. 登入 [Azure 入口網站](https://portal.azure.com)並加入新的 Application Insights 資源。這是處理與顯示您資料的位置。

    ![按一下 [+]、[開發人員服務]、[Application Insights]。](./media/app-insights-sample-mscrm/01.png)

    選擇 ASP.NET 做為應用程式類型。

3. 開啟 [快速入門] 索引標籤並開啟程式碼指令碼。

    ![](./media/app-insights-sample-mscrm/03.png)

當您於另一個瀏覽器視窗中執行下一個步驟時，**保持程式碼頁面開啟**。您很快就會需要程式碼。

### 在 Microsoft CRM 中建立 JavaScript Web 資源

1. 開啟您的 CRM Online 執行個體並使用系統管理員權限登入。
2. 開啟 [Microsoft Dynamics CDM 設定]、[自訂]、[系統]。

    ![](./media/app-insights-sample-mscrm/04.png)
    
    ![](./media/app-insights-sample-mscrm/05.png)


    ![](./media/app-insights-sample-mscrm/06.png)

3. 建立 JavaScript 資源。

    ![](./media/app-insights-sample-mscrm/07.png)

    命名資源、選取**指令碼 (JScript)** 並開啟文字編輯器。

    ![](./media/app-insights-sample-mscrm/08.png)
    
4. 從 Application Insights 複製程式碼。

    ![](./media/app-insights-sample-mscrm/09.png)

    程式碼包含用來識別您 Application insights 資源的檢測金鑰。

5. 儲存並發佈。

    ![](./media/app-insights-sample-mscrm/10.png)

### 檢測表單

1. 在 Microsoft CRM Online 中，開啟 [帳戶] 表單

    ![](./media/app-insights-sample-mscrm/11.png)

2. 開啟表單 [屬性]

    ![](./media/app-insights-sample-mscrm/12.png)

3. 加入您建立的 JavaScript Web 資源

    ![](./media/app-insights-sample-mscrm/13.png)

    ![](./media/app-insights-sample-mscrm/14.png)

4. 儲存並發佈您的表單自訂內容。


## 擷取的度量

您現在已設定遙測擷取表單。只要使用，資料就會傳送至 Application Insights 資源。

以下是您會看到的資料範例。

#### 應用程式健全狀況

![](./media/app-insights-sample-mscrm/15.png)

![](./media/app-insights-sample-mscrm/16.png)

瀏覽器例外狀況：

![](./media/app-insights-sample-mscrm/17.png)

按一下圖表以取得詳細資料：

![](./media/app-insights-sample-mscrm/18.png)

#### 使用量

![](./media/app-insights-sample-mscrm/19.png)

![](./media/app-insights-sample-mscrm/20.png)

![](./media/app-insights-sample-mscrm/21.png)

#### 瀏覽器

![](./media/app-insights-sample-mscrm/22.png)

![](./media/app-insights-sample-mscrm/23.png)

#### 地理位置

![](./media/app-insights-sample-mscrm/24.png)

![](./media/app-insights-sample-mscrm/25.png)

#### 深入了解頁面檢視要求

![](./media/app-insights-sample-mscrm/26.png)

![](./media/app-insights-sample-mscrm/27.png)

![](./media/app-insights-sample-mscrm/28.png)

![](./media/app-insights-sample-mscrm/29.png)

![](./media/app-insights-sample-mscrm/30.png)

## 範例程式碼

[取得範例程式碼](https://dynamicsandappinsights.codeplex.com/)。

## Power BI

如果您[將資料匯出到 Microsoft Power BI](app-insights-export-power-bi.md)，則可以進行更深入的分析。

## 範例 Dynamics CRM 解決方案

[以下是在 Dynamics CRM 中實作的範例解決方案](https://dynamicsandappinsights.codeplex.com/)。

## 詳細資訊

* [什麼是 Application Insights？](app-insights-overview.md)
* [適用於網頁的 Application Insights](app-insights-javascript.md)
* [更多範例和逐步解說](app-insights-code-samples.md)

 

<!---HONumber=September15_HO1-->