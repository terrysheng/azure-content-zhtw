<properties
	pageTitle="Azure AD Connect 同步處理︰Synchronization Service Manager UI | Microsoft Azure"
	description="了解 Azure AD Connect 的 Synchronization Service Manager 中的 [作業] 索引標籤。"
	services="active-directory"
	documentationCenter=""
	authors="andkjell"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/03/2016"
	ms.author="andkjell"/>


# Azure AD Connect 同步處理︰Synchronization Service Manager

| [作業](active-directory-aadconnectsync-service-manager-ui-operations.md) | [連接器](active-directory-aadconnectsync-service-manager-ui-connectors.md) | [Metaverse 設計工具](active-directory-aadconnectsync-service-manager-ui-mvdesigner.md) | [Metaverse 搜尋](active-directory-aadconnectsync-service-manager-ui-mvsearch.md) |
| --- | --- | --- | --- |

![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/operations.png)

[作業] 索引標籤顯示最新作業的結果。此索引標籤主要是用來了解及疑難排解問題。

## 了解 [作業] 索引標籤中顯示的資訊
上半部會以紀事輯順序來顯示所有執行。根據預設，作業記錄將保留最後 7 天的相關資訊，但是您可以利用[排程器](active-directory-aadconnectsync-feature-scheduler.md)來變更此設定。您想要尋找任何未顯示成功狀態的執行。您可以按一下標頭來變更排序。

[狀態] 資料行是最重要的資訊，並顯示最嚴重的執行問題。以下快速摘要依照優先順序來調查的最常見狀態 (其中 * 表示數個可能的錯誤字串)。

| 狀態 | 註解 |
| --- | --- |
| stopped-* | 執行無法完成。例如，如果遠端系統已關閉且無法聯繫。 |
| stopped-error-limit | 有 5,000 個以上的錯誤。執行因錯誤數量過多而自動停止。 |
| completed-*-errors | 執行已完成，但發生應進行調查的錯誤 (數量少於 5,000 個)。 | 
| completed-*-warnings | 執行完成，但某些資料並未處於預期的狀態。如果您遇到錯誤，則此訊息通常只是一個徵狀。在您解決錯誤之前，不應該調查警告。 |
| 成功 | 沒有問題。 |

當您選取某個資料列時，底部將更新以顯示該執行的詳細資料。在底部的最左邊，可能會有一份顯示「步驟 #」的清單。如果您的樹系中有多個網域，而每個網域都以一個步驟來代表，則只會顯示此清單。您可以在「分割區」的標題下方找到網域名稱。在 [同步處理統計資料] 下方，您可以找到關於已處理的變更次數詳細資訊。您可以按一下連結，以取得變更的物件清單。如果您有物件發生錯誤，則這些將顯示於 [同步處理錯誤] 下方。

## 疑難排解 [作業] 索引標籤中的錯誤
![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/errorsync.png) 當您遇到錯誤時，錯誤中的物件與錯誤本身都是連結，將能提供更多資訊。

一開始先按一下錯誤字串 (在 **sync-rule-error-function-triggered** 上方的圖片中)。您將會先看到物件的概觀。若要查看實際的錯誤，可按一下 [堆疊追蹤] 按鈕。這將會提供錯誤的偵錯層級資訊。

**秘訣︰**您可以使用滑鼠右鍵按一下 [呼叫堆疊資訊] 方塊、選擇 [全選]，然後按一下 [複製]。您接著可以複製堆疊，並在您喜好的編輯器 (例如 [記事本]) 中查看此錯誤。

- 如果錯誤來自 **SyncRulesEngine**，則呼叫堆疊資訊首先會具備物件上所有屬性的清單。向下捲動，直到您看到 **InnerException =>** 標題為止。![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/errorinnerexception.png) 下一行將會顯示錯誤。在上圖中，錯誤是來自所建立的自訂同步處理規則 Fabrikam。

如果錯誤本身未提供足夠的資訊，則請查看資料本身。您可以按一下物件識別碼的連結，然後[在整個系統中遵循物件及其資料](active-directory-aadconnectsync-service-manager-ui-connectors.md#follow-an-object-and-its-data-through-the-system)。

## 後續步驟
深入了解 [Azure AD Connect 同步](active-directory-aadconnectsync-whatis.md)組態。

深入了解[整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)。

<!----HONumber=AcomDC_0309_2016-->