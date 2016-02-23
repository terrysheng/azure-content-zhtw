<properties
 pageTitle="開始在 Azure 入口網站中使用 Azure 排程器 | Microsoft Azure"
 description=""
 services="scheduler"
 documentationCenter=".NET"
 authors="krisragh"
 manager="dwrede"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="hero-article"
 ms.date="02/17/2016"
 ms.author="krisragh"/>

# 開始在 Azure 入口網站中使用 Azure 排程器

在 Azure 排程器中建立排程作業很簡單。在本教學課程中，您將了解如何建立作業。您也將學習排程器的監視和管理功能。

## 建立工作

1.  登入 [Azure 入口網站](https://portal.azure.com/)。  

2.  按一下 [+新增] > 在搜尋方塊中輸入_排程器_ > 在結果中選取 [排程器] > 按一下 [建立]。

   ![][marketplace-create]

3.  讓我們使用 GET 要求建立只要點擊 http://www.microsoft.com/ 的工作。在 [排程器作業] 畫面中，輸入下列資訊：

    1.  **名稱：** `getmicrosoft`  

    2.  **訂用帳戶：**您的 Azure 訂用帳戶

    3.  **作業集合：**選取現有的作業集合，或按一下 [建立新項目] > 輸入名稱。

4.  接下來，在 [動作設定] 中，定義下列值：

    1.  **動作類型：** ` HTTP`  

    2.  **方法：** `GET`

    3.  **URL：**` http://www.microsoft.com`

   ![][action-settings]

5.  最後，讓我們定義排程。作業可以定義為一次性的工作，但我們挑選週期性排程：

    1. **週期**：`Recurring`

    2. **開始**：今天的日期

    3. **重複頻率**：`12 Hours`

    4. **結束於**：今天日期的前兩天

   ![][recurrence-schedule]

6.  按一下 [建立]

## 管理和監視作業

建立作業之後，它會出現在主要 Azure 儀表板。按一下作業，即會開啟新視窗並顯示下列索引標籤：

1.  屬性  

2.  動作設定

3.  排程

4.  歷程記錄

5.  使用者

   ![][job-overview]

### 屬性

這些唯讀屬性說明排程器作業的管理中繼資料。

   ![][job-properties]


### 動作設定

按一下 [作業] 畫面中的作業，可讓您設定該作業。如果您沒有在快速建立精靈中進行進階設定，這可讓您設定它們。

對於所有的動作類型，您可以變更重試原則和錯誤動作。

對於 HTTP 和 HTTPS 工作動作類型，您可能會將方法變更為任何允許的 HTTP 動詞。您也可以新增、刪除或變更標頭和基本驗證資訊。

對於儲存體佇列動作類型，您可能變更儲存體帳戶、佇列名稱、SAS 權杖和主體。

對於服務匯流排動作類型，您可以變更命名空間、主題/佇列路徑、驗證設定、傳輸類型、訊息屬性和訊息本文。

   ![][job-action-settings]

### 排程

如果您想要變更您在快速建立精靈中建立的排程，這可讓您重新設定排程。

這是[在作業中建置複雜的排程和進階週期](scheduler-advanced-complexity.md)的機會

您可以變更開始日期和時間、週期排程，以及結束日期和時間 (如果工作重複發生。)

   ![][job-schedule]


### 歷程記錄

[歷程記錄] 索引標籤為選取的作業顯示系統中每次執行作業時選取的度量。這些度量提供有關排程器健全狀況的即時值：

1.  狀態  

2.  詳細資料

3.  重試次數

4.  發生次數：第 1 次、第 2 次、第 3 次等。

5.  執行開始時間

6.  執行結束時間

   ![][job-history]

您可以按一下執行來檢視 [記錄詳細資料]，包括每次執行的完整回應。此對話方塊也可讓您將回應複製到剪貼簿。

   ![][job-history-details]

### 使用者

Azure 角色型存取控制 (RBAC) 可以對 Azure 排程器進行更細緻的存取權管理。若要了解如何使用 [使用者] 索引標籤，請參閱 [Azure 角色型存取控制](../active-directory/role-based-access-control-configure.md)


## 另請參閱

 [排程器是什麼？](scheduler-intro.md)

 [排程器概念、術語及實體階層](scheduler-concepts-terms.md)

 [Azure 排程器的計劃和計費](scheduler-plans-billing.md)

 [如何使用 Azure 排程器建立複雜的排程和進階週期](scheduler-advanced-complexity.md)

 [排程器 REST API 參考](https://msdn.microsoft.com/library/mt629143)

 [排程器 PowerShell Cmdlet 參考](scheduler-powershell-reference.md)

 [排程器高可用性和可靠性](scheduler-high-availability-reliability.md)

 [排程器限制、預設值和錯誤碼](scheduler-limits-defaults-errors.md)

 [排程器輸出驗證](scheduler-outbound-authentication.md)


[marketplace-create]: ./media/scheduler-get-started-portal/scheduler-v2-portal-marketplace-create.png
[action-settings]: ./media/scheduler-get-started-portal/scheduler-v2-portal-action-settings.png
[recurrence-schedule]: ./media/scheduler-get-started-portal/scheduler-v2-portal-recurrence-schedule.png
[job-properties]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-properties.png
[job-overview]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-overview-1.png
[job-action-settings]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-action-settings.png
[job-schedule]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-schedule.png
[job-history]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-history.png
[job-history-details]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-history-details.png


[1]: ./media/scheduler-get-started-portal/scheduler-get-started-portal001.png
[2]: ./media/scheduler-get-started-portal/scheduler-get-started-portal002.png
[3]: ./media/scheduler-get-started-portal/scheduler-get-started-portal003.png
[4]: ./media/scheduler-get-started-portal/scheduler-get-started-portal004.png
[5]: ./media/scheduler-get-started-portal/scheduler-get-started-portal005.png
[6]: ./media/scheduler-get-started-portal/scheduler-get-started-portal006.png
[7]: ./media/scheduler-get-started-portal/scheduler-get-started-portal007.png
[8]: ./media/scheduler-get-started-portal/scheduler-get-started-portal008.png
[9]: ./media/scheduler-get-started-portal/scheduler-get-started-portal009.png
[10]: ./media/scheduler-get-started-portal/scheduler-get-started-portal010.png
[11]: ./media/scheduler-get-started-portal/scheduler-get-started-portal011.png
[12]: ./media/scheduler-get-started-portal/scheduler-get-started-portal012.png
[13]: ./media/scheduler-get-started-portal/scheduler-get-started-portal013.png
[14]: ./media/scheduler-get-started-portal/scheduler-get-started-portal014.png
[15]: ./media/scheduler-get-started-portal/scheduler-get-started-portal015.png

<!---HONumber=AcomDC_0218_2016-->