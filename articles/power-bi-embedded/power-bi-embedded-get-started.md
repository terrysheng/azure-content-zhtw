<properties
   pageTitle="開始使用 Microsoft Power BI Embedded 預覽"
   description=""
   services="power-bi-embedded"
   documentationCenter=""
   authors="dvana"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="03/29/2016"
   ms.author="derrickv"/>

# 開始使用 Microsoft Power BI Embedded 預覽

**Microsoft Power BI Embedded** 是一項 Azure 服務，可讓應用程式開發人員將互動式 Power BI 報告加入至自己的應用程式。**Power BI Embedded** 會與現有的應用程式一同運作，而不需要重新設計這些應用程式，或變更其使用者登入的方式。

若要深入了解 Power BI Embedded，請參閱[什麼是 Power BI Embedded](power-bi-embedded-what-is-power-bi-embedded.md)。

如[什麼是 Power BI Embedded](power-bi-embedded-what-is-power-bi-embedded.md) 中所述，**Microsoft Power BI Embedded** 的資源是透過 [Azure ARM API](https://msdn.microsoft.com/library/mt712306.aspx) 佈建。在此情況下，您可以佈建的資源是 **Power BI 工作區集合**。下一節將說明如何建立工作區集合。

![](media\power-bi-embedded-get-started\introduction.png)

## 建立工作區集合
**工作區集合**是最上層的 Azure 資源，而內容的容器將會內嵌在您的應用程式中。建立**工作區集合**的方式有兩種︰

   -	以手動方式使用 Azure 入口網站
   -	以程式設計方式使用 Azure Resource Manager (ARM) API

讓我們逐步解說使用 Azure 入口網站建立**工作區集合**的步驟。

   1.	開啟並登入 **Azure 入口網站**：[http://portal.azure.com](http://portal.azure.com)。

   2.	按一下頂端面板上的 [+ 新增]。

       ![](media\power-bi-embedded-get-started\create-workspace-1.png)

   3.	按一下 [資料 + 分析] 之下的 [Power BI Embedded]。
   4.	在 [建立刀鋒視窗] 上輸入必要資訊。如需**價格**，請參閱 [Power BI Embedded 價格](http://go.microsoft.com/fwlink/?LinkID=760527)。

       ![](media\power-bi-embedded-get-started\create-workspace-2.png)

   5. 按一下 [建立]。

**工作區集合**會花費一些時間來佈建。完成後，您將會進入 [工作區集合刀鋒視窗]。

   ![](media\power-bi-embedded-get-started\create-workspace-3.png)

[建立刀鋒視窗] 包含呼叫 API 所需的資訊，以建立工作區並將內容部署到這些工作區。

下一節說明**存取金鑰**如何用來產生**應用程式權杖**，而這些權杖用來驗證 API 要求。

<a name="view-access-keys"/>
## 檢視 Power BI API 存取金鑰

呼叫 Power BI REST API 所需的其中一項最重要資訊是**存取金鑰**。這些存取金鑰用來產生**應用程式權杖**，而這些權杖用來驗證 API 要求。若要檢視您的**存取金鑰**，請按一下 [設定刀鋒視窗] 上的 [存取金鑰]。如需**應用程式權杖**的詳細資訊，請參閱[應用程式權杖流程的運作方式](power-bi-embedded-get-started-sample.md#key-flow)。

   ![](media\power-bi-embedded-get-started\access-keys.png)

您會發現您有兩個金鑰。

   ![](media\power-bi-embedded-get-started\access-keys-2.png)

複製這些金鑰並將它們安全地儲存在您的應用程式中。請務必如同密碼一樣處理這些金鑰，因為這些金鑰可供存取您的**工作區集合**中的所有內容。

雖已列出兩個金鑰，但一次只需要一個金鑰。系統會提供第二個金鑰，以便您定期重新產生金鑰，而不需中斷對服務的存取。

您現在已有應用程式的 Power BI 執行個體以及**存取金鑰**，您可以將報告匯入自己的應用程式中。在了解如何匯入報告之前，下一節說明如何建立要內嵌到應用程式中的 Power BI 資料集和報告。

## 建立要內嵌到應用程式中的 Power BI 資料集和報告

您現已為您的應用程式建立 Power BI 執行個體，而且有 **存取金鑰**，您必須建立想要內嵌的 Power BI 資料集和報告。使用 **Power BI Desktop** 可以建立資料集和報告。您可以下載[免費的 Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)。或者，若要快速開始，您可以下載[分析銷售資料 PBIX 範例](http://download.microsoft.com/download/1/4/E/14EDED28-6C58-4055-A65C-23B4DA81C4DE/Analyzing_Sales_Data.pbix)。若要深入了解如何使用 **Power BI Desktop**，請參閱[開始使用 Power BI Desktop](https://powerbi.microsoft.com/en-us/guided-learning/powerbi-learning-0-2-get-started-power-bi-desktop)。

> [AZURE.NOTE] 若要深入了解如何建立「分析銷售資料 PBIX」，請參閱[教學課程︰分析來自 Excel 和 OData 摘要的銷售資料](https://powerbi.microsoft.com/en-us/documentation/powerbi-desktop-tutorial-analyzing-sales-data-from-excel-and-an-odata-feed)。

透過 **Power BI Desktop**，將資料複本匯入 **Power BI Desktop** 或使用 **DirectQuery** 直接連接到資料來源，即可連接到資料來源。

以下是使用 **Import** 與 **DirectQuery** 之間的差異。

|Import | DirectQuery
|---|---
|資料表、資料行和資料會匯入或複製到 **Power BI Desktop** 中。當您使用視覺效果時，**Power BI Desktop** 會查詢資料的複本。若要查看基礎資料所發生的任何變更，您必須重新整理或再次匯入完整的目前資料集。|只有資料表和資料行會匯入或複製到 **Power BI Desktop** 中。當您使用視覺效果時，**Power BI Desktop** 會查詢基礎資料來源，這表示您一直在檢視目前的資料。

如需有關連接到資料來源的詳細資訊，請參閱[連接到資料來源](power-bi-embedded-connect-datasource.md)。

在 **Power BI Desktop** 中儲存您的工作之後，會建立一個 PBIX 檔案。此檔案包含您的報告。此外，如果您匯入資料，則 PBIX 會包含完整的資料集，或如果您使用 **DirectQuery**，則 PBIX 只包含資料集結構描述。您會使用 [Power BI Import API](https://msdn.microsoft.com/library/mt711504.aspx)，以程式設計方式將 PBIX 部署到您工作區。

> [AZURE.NOTE] **Power BI Embedded** 有其他 API 可變更您的資料集所指向的伺服器和資料庫，以及設定資料集將用來連接到您的資料庫的服務帳戶認證。請參閱 [Post SetAllConnections](https://msdn.microsoft.com/library/mt711505.aspx) 和 [Patch 閘道資料來源](https://msdn.microsoft.com/library/mt711498.aspx)。

## 後續步驟
在先前步驟中，您建立了一個工作區集合以及第一個報告和資料集。現在即可了解如何為 **Power BI Embedded** 撰寫程式碼。為了協助您開始著手，我們建立了一個範例 Web 應用程式︰[開始使用範例](power-bi-embedded-get-started-sample.md)。此範例示範如何：

  -	佈建內容
      - 建立工作區
      - 匯入 PBIX 檔案
      - 更新連接字串並設定您的資料集的認證。

  -	安全地內嵌報告

## 另請參閱
- [開始使用範例](power-bi-embedded-get-started-sample.md)
- [什麼是 Power BI Embedded](power-bi-embedded-what-is-power-bi-embedded.md)
- [開始使用 Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started)
- [Power BI 桌面](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)
- [Power BI Embedded 價格](http://go.microsoft.com/fwlink/?LinkID=760527)

<!---HONumber=AcomDC_0406_2016-->