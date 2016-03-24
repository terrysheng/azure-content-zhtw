<properties
	pageTitle="Azure AD Connect 同步處理︰Synchronization Service Manager UI | Microsoft Azure"
	description="了解 Azure AD Connect 的 Synchronization Service Manager 中的 [連接器] 索引標籤。"
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

![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/connectors.png)

[連接器] 索引標籤可用來管理同步處理引擎連接的所有系統。

## 連接器動作

| 動作 | 註解 |
| --- | --- |
| 建立 | 請勿使用。若要連接到其他的 AD 樹系，請使用安裝精靈。 |
| 屬性 | 用於網域和 OU 篩選。 |
| 刪除 | 用來刪除連接器空間中的資料或刪除與樹系的連接。 |
| 更新執行設定檔 | 除了網域篩選，不會在此處進行任何設定。 |
| 執行 | 用來啟動設定檔的一次性執行。 |
| 停止 | 停止目前執行設定檔的連接器。 |
| 匯出連接器 | 請勿使用。 |
| 匯入連接器 | 請勿使用。 |
| 更新連接器 | 請勿使用。 |
| 重新整理結構描述 | 重新整理快取的結構描述。最好是改為在安裝精靈中使用此選項，因為其也會更新同步處理規則。
| 搜尋連接器空間 | 用來尋找物件，以及[在整個系統中遵循物件及其資料](#follow-an-object-and-its-data-through-the-system)。 |

### 刪除
刪除動作適用於兩個不同的用途。
![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/connectordelete.png)

[僅刪除連接器空間] 選項將移除所有資料，但會保留所有組態。

[刪除連接器和連接器空間] 選項將移除資料以及所有組態。當您不想再連接到樹系時可使用此選項。

這兩個選項將會同步處理所有物件，並更新 metaverse 物件。這是長時間執行的作業。

### 搜尋連接器空間
尋找物件和疑難排解資料問題時，搜尋連接器空間動作非常有用。

![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/cssearch.png)

一開始先選取**範圍**。您可以依據資料 (RDN、DN、錨點、子樹狀目錄) 或物件狀態 (所有其他選項) 進行搜尋。  
![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/cssearchscope.png)  
例如，如果您進行子樹狀目錄的搜尋，將會取得某一個 OU 中的所有物件。
![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/cssearchsubtree.png)
您可以從此處選取物件、選取 [屬性]，並[跟隨物件](#follow-an-object-and-its-data-through-the-system)，從來源連接器空間、通過 metaverse，然後到目標連接器空間。

## 在整個系統中遵循物件及其資料
當您疑難排解資料的問題時，遵循物件從來源連接器空間到 metaverse 以及到目標連接器空間是一個關鍵程序，以了解為什麼資料沒有預期的值。

### 連接器空間物件屬性
**匯入**  
當您開啟 cs 物件時，頂端會出現數個索引標籤。[匯入] 索引標籤會顯示匯入後暫存的資料。
![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/csimport.png)
[舊值] 顯示目前儲存在系統中的資料，而 [新值] 代表從來源系統接收到但尚未套用的資料。在此案例中，由於發生同步處理錯誤，因此無法套用變更。

**錯誤**  
錯誤頁面只有在出現有問題的物件時才會顯示。如需如何[疑難排解同步處理錯誤](active-directory-aadconnectsync-service-manager-ui-operations.md#troubleshoot-errors-in-operations-tab)的詳細資料，請參閱 [作業] 頁面上的詳細資訊。

**歷程** [歷程] 索引標籤會顯示連接器空間物件與 metaverse 物件關聯的方式。我們可以看到上次從連接的系統匯入變更的時間，以及套用哪些規則以便在 metaverse 中填入資料。
![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/cslineage.png)
在「動作」資料行中，我們可以看到有一個動作為「佈建」的「輸入」同步處理規則。這表示，只要此連接器空間物件存在，metaverse 物件將會保留。如果同步處理規則的清單改為顯示方向為「輸出」和「佈建」的同步處理規則，這表示刪除 metaverse 物件時，將會刪除此物件。
![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/cslineageout.png)
我們也會在 **PasswordSync** 資料行中看到輸入連接器空間可以提供密碼變更，因為有一個同步處理規則的值為 **True**。此密碼接著會透過輸出規則傳送至 Azure AD。

從 [歷程] 索引標籤，您可以按一下 [Metaverse 物件屬性](#metaverse-object-properties)，以移至 metaverse。

所有索引標籤的底部都有兩個按鈕︰[預覽] 和 [記錄]。

**預覽**  
[預覽] 頁面可用來同步處理某一個單一物件。如果您正在疑難排解某些客戶的同步處理規則，並且想要在單一物件上查看變更的影響，則此頁面非常有用。您可以在 [完整同步處理] 和 [差異同步處理] 之間進行選擇。您也可以在 [產生預覽] \(這只會在記憶體中保留變更) 和 [認可預覽] \(將暫存目標連接器空間的所有變更) 之間進行選擇。
![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/preview1.png)
您可以檢查物件，以及哪一個規則適用於特定的屬性流程。
![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/preview2.png)

**記錄**  
[記錄] 頁面可用來查看密碼同步處理狀態和歷程記錄，如需詳細資訊，請參閱[疑難排解密碼同步處理](active-directory-aadconnectsync-implement-password-synchronization.md#troubleshoot-password-synchronization)。

### Metaverse 物件屬性
**屬性**  
在 [屬性] 索引標籤中，您可以看到值，以及是由哪一個連接器提供它。
![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/mvattributes.png)
**連接器**  
[連接器] 索引標籤會顯示所有具有物件表示法的連接器空間。
![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/mvconnectors.png)
此索引標籤也可讓您瀏覽至[連接器空間物件](#connector-space-object-properties)。

## 後續步驟
深入了解 [Azure AD Connect 同步](active-directory-aadconnectsync-whatis.md)組態。

深入了解[整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)。

<!----HONumber=AcomDC_0309_2016-->

