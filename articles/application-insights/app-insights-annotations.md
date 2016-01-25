<properties
    pageTitle="Application Insights 的部署註解 |Microsoft Azure"
    description="在 Application Insights 中對計量瀏覽器新增部署或建置標記。"
    services="application-insights"
    documentationCenter=".net"
    authors="alancameronwills"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="article"
	ms.date="01/12/2016"
    ms.author="awills"/>

# Application Insights 中的發行註解

[計量瀏覽器](app-insights-metrics-explorer.md)圖表上的發行註解會顯示您在哪邊部署了新組建。註解可讓您輕鬆查看變更是否對應用程式的效能有任何影響。[Visual Studio Team Services 建置系統](https://www.visualstudio.com/zh-TW/get-started/build/build-your-app-vs)可自動建立註解。

![註解範例，其會顯示與伺服器回應時間的相互關聯](./media/app-insights-annotations/00.png)

發行註解是 Visual Studio Team Services 的雲端型組建和發行服務的功能。

## 安裝註解擴充功能 (一次)

若要能夠建立發行註解，必須安裝 Visual Studio Marketplace 中許多可用 Team Service 擴充功能的其中一個。

1. 登入 [Visual Studio Team Services](https://www.visualstudio.com/zh-TW/get-started/setup/sign-up-for-visual-studio-online) 專案。
2. 開啟 Visual Studio Marketplace，尋找 Application Insights 註解擴充功能，並將其新增至 Team Services 帳戶。

![在 Team Services 網頁右上角開啟 Marketplace。搜尋 Application Insights 註解並安裝在帳戶中。](./media/app-insights-annotations/10.png)

您只需要為 Visual Studio Team Services 帳戶執行一次。現在就能為帳戶中的任何專案設定發行註解。

## 從 Application Insights 取得 API 金鑰

您必須為每個想要建立發行註解的發行範本執行這項操作。


1. 登入 [Microsoft Azure 入口網站](https://portal.azure.com)並開啟負責監視應用程式的 Application Insights 資源。(或者[立即建立一個](app-insights-overview.md)，如果您還沒建立的話)。
2. 開啟 [設定]、[API 存取]，然後複製 **Application Insights 識別碼**。

    ![在 portal.azure.com 中，開啟您的 Application Insights 資源然後選擇 [設定]。開啟 [API 存取]。複製](./media/app-insights-annotations/20.png)

2. 在另一個瀏覽器視窗中，開啟 (或建立) 可從 Visual Studio Team Services 管理部署的發行範本。

    新增工作，然後從功能表中選取 Application Insights 發行註解工作。

    將您從 [API 存取] 刀鋒視窗複製的 **Application Insights 識別碼**貼上。

    ![在 Visual Studio Team Services 中，開啟 [發行]，選取一項發行定義，然後選擇 [編輯]。按一下 [新增工作] 然後選取 [Application Insights 發行註解]。貼上 Application Insights 識別碼。](./media/app-insights-annotations/30.png)

3. 將 [APIKey] 欄位設為變數 `$(ApiKey)`。

4. 回到 [API 存取] 刀鋒視窗，建立新的 API 金鑰並複製。

    ![在 [Azure] 視窗的 [API 存取] 刀鋒視窗中，按一下 [建立 API 金鑰]。提供註解，檢查 [寫入註解]，然後按一下 [產生金鑰]。複製新的金鑰。](./media/app-insights-annotations/40.png)

4. 開啟發行範本的 [設定] 索引標籤。

    建立 `ApiKey` 的變數定義。

    將您的 API 金鑰貼上至 ApiKey 變數定義。

    ![在 [Team Services] 視窗中，選取 [設定] 索引標籤然後按一下 [新增變數]。設定 ApiKey 的名稱並設定到 [值] 中，將剛剛產生的金鑰貼上。](./media/app-insights-annotations/50.png)


5. 最後，**儲存**發行定義。

## 部署註解

現在，每當您使用發行範本來部署新的發行，就會將註解傳送至 Application Insights。註解將會出現在計量瀏覽器的圖表上。

<!---HONumber=AcomDC_0114_2016-->