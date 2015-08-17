<properties
    pageTitle="設定工作區和管理設定"
    description="了解如何在 Microsoft Azure Operational Insights 中設定工作區和管理設定"
    services="operational-insights"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="operational-insights"
    ms.workload="operational-insights"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/05/2015"
    ms.author="banders"/>

# 設定工作區和管理設定

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

若要建立新的 Microsoft Azure Operational Insights 工作區，您要選擇工作區名稱、將工作區與您的帳戶關聯，然後選擇地理位置。Operational Insights 工作區本質上是一個容器，包含帳戶資訊和帳戶的簡單組態資訊。您或組織的其他成員可能會使用多個 Operational Insights 工作區來管理從所有或部份 IT 基礎結構收集而來的不同資料。

工作區建立之後，您就可以使用工作區執行其他工作，例如管理 Operational Insights、新增解決方案、連接資料來源、新增記錄檔、選取儲存體帳戶、在儀表板中檢視您的使用量資料，以及可以為每個工作區管理設定。

[在幾分鐘內上架](./operational-insights-onboard-in-minutes.md)文章會向您說明如何快速啟動和執行，本文章的其餘部分則會更詳細地描述您在開始使用並管理您的工作區時需要完成的一些動作。

在下列各節中，我們將會說明您使用的所有常用工作：

1. 新增解決方案
2. 連接資料來源
3. 新增和管理記錄檔
4. 管理帳戶和使用者

![步驟](./media/operational-insights-setup-workspace/steps.png)
## 1 新增解決方案

Microsoft Azure Operational Insights 含有基底組態評估功能，因此您不需要安裝解決方案來啟動它。不過，您可以透過從 [設定] 或 [解決方案資源庫] 頁面新增解決方案來取得其他功能。

加入解決方案後，系統會從基礎結構中的伺服器收集資料，然後再傳送到 Operational Insights 服務。Operational Insights 服務的處理時間少則幾分鐘，多則達數小時之久。待服務處理完資料後，您可以在 Operational Insights 中予以檢視。

當您不再需要解決方案時，您可以輕易地將它移除。移除解決方案後，系統不會將資料傳送到 Operational Insights，因此可減少每日配額所使用的資料量。

### Microsoft Monitoring Agent 支援的解決方案

在現階段，使用 Microsoft Monitoring Agent 直接連接 Microsoft Azure Operational Insights 的伺服器可使用目前可用的大多數解決方案，包括：

- [系統更新](operational-insights-updates.md)
- [反惡意程式碼](operational-insights-antimalware.md)
- [變更追蹤](operational-insights-change-tracking.md)
- [SQL 和 Active Directory 評估](operational-insights-assessment.md)

然而，Microsoft Monitoring Agent 並「不」支援下列解決方案，且需要 System Center Operations Manager (SCOM)。

- [產能管理](operational-insights-capacity.md)
- [警示管理](operational-insights-alerts.md)
- [組態評估](operational-insights-solutions.md#configuration-assessment)

如需搭配使用這些解決方案和 Operations Manager 的指引，請參閱 [Operational Insights 的 Operations Manager 考量](operational-insights-operations-manager.md)。

安裝以下作業系統的電腦支援 IIS 記錄檔收集：

- Windows Server 2012
- Windows Server 2012 R2

### 使用 [設定] 頁面新增解決方案

- 選取要新增的解決方案，然後按一下 [新增選取的解決方案]。並非所有可用的解決方案都會顯示在這裡。如果您想新增的解決方案並未列出，請使用下一個程序。![新增解決方案](./media/operational-insights-setup-workspace/settings-add-sol.png)

### 使用 [解決方案資源庫] 新增解決方案

1. 在 Operational Insights 的 [概觀] 頁面中按一下 [解決方案資源庫] 磚。![解決方案圖示的影像](./media/operational-insights-setup-workspace/sol-gallery.png)
2. 在 [Operational Insights 解決方案資源庫] 頁面中，您可以了解每個可用的解決方案。按一下要加入 Operational Insights 之解決方案的名稱。
3. 在所選解決方案的頁面中，該解決方案的詳細資料會顯示於此。按一下 [新增]。
4. 在確認頁面中，按一下 [**接受**] 以同意隱私權聲明和使用條款。
5. 加入解決方案而新建立的磚會出現在 Operational Insights 的 [概觀] 頁面中；待 Operational Insights 服務處理資料後，您便可以開始使用解決方案。

### 使用 [解決方案資源庫] 移除解決方案

1. 在 Operational Insights 的 [概觀] 頁面中按一下 [**解決方案資源庫**] 磚。
2. 在 [Operational Insights 解決方案資源庫] 頁面中，於要移除的解決方案下方按一下 [**移除**]。
3. 在確認頁面中按一下 [**是**] 以移除解決方案。

## 2 連接資料來源

有三種方式可連接資料來源：

- 直接將電腦連接至 Operational Insights。如需詳細資訊，請參閱[直接將電腦連接至 Operational Insights](./operational-insights-direct-agent.md)。![直接連接](./media/operational-insights-setup-workspace/attach-directly.png)
- 連接 Operations Manager 管理群組。如需詳細資訊，請參閱[從 System Center Operations Manager 連接到 Operational Insights](./operational-insights-connect-scom.md)。![連接 Operations Manager](./media/operational-insights-setup-workspace/attach-om.png)
- 連接 Azure 儲存體帳戶。如需詳細資訊，請參閱[在 Microsoft Azure 中分析來自伺服器的資料](./operational-insights-analyze-data-azure.md)。![連接 Azure](./media/operational-insights-setup-workspace/attach-azure.png)

## 3 新增和管理記錄檔

新增記錄檔之前，您需要安裝會使用記錄資料的解決方案。接著您可以加入新的記錄檔來收集事件，並選擇要針對記錄檔收集哪些事件層級或嚴重性。您可以收集：

- Windows 事件記錄檔
- IIS 記錄檔
- 您已新增的其他記錄檔

![新增記錄檔](./media/operational-insights-setup-workspace/collect-logs.png)

### IIS 記錄檔格式

目前支援的唯一 IIS 記錄檔格式是 W3C。別擔心，它是最常見的格式，而且是 IIS 7 和 IIS 8 中的預設格式。因此，如果您登入 NCSA 或 IIS 原生格式，Operational Insights 完全不會收取這些記錄檔。即使是 W3C 格式，預設也不會記錄所有欄位。您可以在 [選取要記錄的 W3C 欄位至記錄檔 (IIS 7)] 閱讀更多有關格式的資訊 (https://technet.microsoft.com/library/cc754702(v=WS.10).aspx)。


> [AZURE.TIP]如需最佳的記錄搜尋經驗，建議您使用 IIS 中的 [記錄]，為每個網站選取所有記錄欄位。我們也建議您將新記錄檔的 [記錄檔變換] 排程變更為 [每小時]，讓較小的檔案可上傳至雲端來節省頻寬。


### 從 Operations Manager 或直接連接的代理程式收集 Windows 事件記錄檔

1. 在 [概觀] 頁面上，按一下 [設定] 磚，然後按一下 [記錄] 索引標籤。
2. 輸入您想要從中收集資訊之事件記錄檔的名稱。如果您不確定要使用的名稱，請在 [事件檢視器] 中選取 Windows 事件記錄檔的屬性，在 [全名] 欄位中複製名稱，並在 [從下列事件記錄檔收集事件] 方塊中貼上名稱。
3. 按一下 [**+**] 加入記錄檔。
4. 選取要針對記錄檔收集哪些事件層級或嚴重性。這個版本不支援 [稽核成功] 和 [稽核失敗] 事件。
5. 針對您要從中收集資訊的每個記錄檔重複上述步驟，然後按一下 [儲存]。
6. Operational Insights 應該在幾分鐘內顯示事件，接著您就可以搜尋資料。

### 從 Operations Manager 或直接連接的代理程式收集 IIS 記錄檔

1. 在 [概觀] 頁面上，按一下 [設定] 磚，然後按一下 [記錄] 索引標籤。
2. 在 [記錄] 索引標籤上的 [事件記錄] 底下選取 [從 Operations Manager 收集記錄檔]。


### 從 Azure 診斷收集 IIS 記錄檔和/或 Windows 事件
這可從 Azure 管理入口網站中設定，而不必在 Operational Insights 入口網站中設定，方法是在工作區下，移至 [儲存體] 索引標籤，即可從該儲存體帳戶啟用記錄檔收集。

### 設定記錄檔收集之後
設定記錄檔收集之後，您的記錄檔收集原則會傳送至代理程式或透過管理群組傳送至代理程式，服務就會開始收集事件。

您可以藉由檢視 [使用量] 頁面，存取從受監視伺服器收集之記錄檔事件的一些初始細項。

![使用量頁面磚的影像](./media/operational-insights-setup-workspace/usage.png)


## 4 管理帳戶和使用者
您可以使用 [設定] 頁面中的 [帳戶] 索引標籤來管理帳戶和使用者。您可以在其中執行下列工作。

![帳戶索引標籤](./media/operational-insights-setup-workspace/manage-users.png)

## 新增使用者到現有的工作區


使用下列步驟新增使用者或群組到 Operational Insights 工作區。使用者或群組就能夠檢視與此工作區關聯的所有警示並處理警示。

>[AZURE.NOTE]如果您想要從 Azure Active Directory 組織帳戶新增使用者或群組，您必須先確定您已經將 Operational Insights 帳戶與 Active Directory 網域關聯。請參閱[新增 Azure Active Directory 組織到現有的工作區](#)。

### 新增使用者到現有的工作區
1. 在 Operational Insights 中，按一下 [設定] 磚。
2. 按一下 [帳戶] 索引標籤。
3. 在 [管理使用者] 區段中，選擇要新增的帳戶類型：[組織帳戶] 或 [Microsoft 帳戶]。
    - 如果您選擇 [Microsoft 帳戶]，請輸入與該 Microsoft 帳戶相關聯的使用者電子郵件地址。
    - 如果您選擇 [組織帳戶]，您可以輸入使用者或群組的部分名稱或電子郵件別名，系統將會顯示使用者和群組的清單。選取使用者或群組。
        >[AZURE.NOTE]為了獲得最佳的效能結果，請將與單一 Operational Insights 帳戶關聯的 Active Directory 群組數目限制為兩個，一個給管理員，一個給使用者。使用太多群組可能會影響 Operational Insights 的效能。
7. 選擇要新增的使用者或群組類型：[系統管理員] 或 [使用者]。  
8. 按一下 [新增]。

  如果您新增 Microsoft 帳戶，系統將會傳送一封加入工作區的邀請至您提供的電子郵件。使用者依照邀請中的指示加入 Operational Insights 之後，使用者就可以檢視此 Operational Insights 帳戶的警示和帳戶資訊，而且您將能夠在 [設定] 頁面上的 [帳戶] 索引標籤中檢視使用者資訊。如果您新增組織帳戶，使用者就能夠立即存取 Operational Insights。![邀請](./media/operational-insights-setup-workspace/manage-users04.png)


### 我需要多少工作區？
在 Azure 管理入口網站中，一個工作區被視為是一項 Azure 資源。

您可以建立新的工作區，或連結到您先前使用 System Center Operations Manager 所開啟的現有工作區，但是您尚未與 Azure 訂用帳戶關聯 (必須關聯才能計費)。工作區代表資料在 Operational Insights 入口網站中進行收集、彙總、分析以及呈現的層級。您可以選擇有多個工作區，區分來自不同環境和系統的資料；每個 Operations Manager 管理群組 (和所有其代理程式) 或個別 VM/代理程式可以分別只與一個工作區連線。

每個工作區可以有多個相關聯的使用者帳戶，而每個使用者帳戶 (Microsoft 帳戶或組織帳戶) 可以存取多個 Operational Insights 工作區。根據預設，用來建立工作區的 Microsoft 帳戶或組織帳戶會變成工作區的管理員。然後管理員可以邀請其他 Microsoft 帳戶或從其 Azure Active Directory 挑選使用者。

## 將現有的工作區連結到 Azure 訂用帳戶

您可以從 [microsoft.com/oms](https://microsoft.com/oms) 建立工作區。不過，這些工作區有某些限制，如果您使用免費帳戶，最明顯的限制是一天最多上傳 500MB 的資料。若要對此工作區進行變更，您必須**將您現有的工作區連結到 Azure 訂用帳戶**。

>[AZURE.IMPORTANT]如果要連結工作區，您的 Azure 帳戶必須已經能夠存取您想要連結的工作區。換句話說，您用來存取 Azure 入口網站的帳戶必須與您用來存取 Operational Insights 工作區的帳戶**相同**。如果不是，請參閱[新增使用者到現有的工作區](#add-an-azure-active-directory-organization-to-an-existing-workspace)。

1. 登入 Azure 管理入口網站。
2. 在入口網站左下方按一下 [+ 新增]。
3. 按一下 [應用程式服務]，捲動到 [Operational Insights] 並加以選取。
4. 按一下 [快速建立]。
5. 在 [帳戶] 清單中，您應該會看到*尚未*連結到您 Azure 訂用帳戶的現有工作區清單。選取帳戶。

  >[AZURE.NOTE]如果您在這裡沒有看到想要連結的工作區，這表示您的 Azure 訂用帳戶沒有 Operational Insights 工作區的存取權。您必須從您 Operational Insights 工作區內部授與存取權給此帳戶。若要這樣做，請參閱[新增使用者到現有的工作區](#add-a-user-to-an-existing-workspace)。

  ![連結帳戶](./media/operational-insights-setup-workspace/link-account.png) <p> 6.填寫剩下的欄位，然後選取 [建立工作區]。

## 升級工作區為付費資料方案

Operational Insights 有三種工作區資料方案類型：**免費**、**標準**和**高級**。如果您使用*免費*方案，有可能會達到 500MB 的資料容量。此時您將需要將工作區升級為 '**隨用隨付方案**'，才能收集超過此限制的資料。您隨時都可以轉換您的方案類型。如需 Operational Insights 定價的詳細資訊，請參閱[定價詳細資料](http://azure.microsoft.com/pricing/operational-insights/)

>[AZURE.IMPORTANT]工作區方案只有在*連結*到 Azure 訂用帳戶時才能變更。如果您在 Azure 中建立了工作區，或者如果您*已經*連結了工作區，可以忽略此訊息。如果您是從 [opinsights.azure.com](http://opinsights.azure.com) 建立工作區，就必須依照[連結現有的工作區到 Azure 訂用帳戶](#link-an-existing-workspace-to-an-Azure-subscription)的步驟。

### 變更方案類型

在 Azure 管理入口網站中，瀏覽到您想要升級的 Operational Insights 工作區：

![級別](./media/operational-insights-setup-workspace/find-workspace.png)

選取此工作區，然後從畫面頂端的索引標籤選取 [級別]

![選取級別](./media/operational-insights-setup-workspace/select-scale.png)

最後，選擇您想要升級的方案，然後按一下 [儲存]。您會在入口網站中看到反映的變更，現在可以收集超過「免費」資料容量的資料。

![選取方案](./media/operational-insights-setup-workspace/plan-select.png)

## 新增 Azure Active Directory 組織到現有的工作區

您可以將您的 Operational Insights 工作區與 Azure Active Directory 網域關聯。這樣可讓您直接從 Active Directory 新增使用者到您的 Operational Insights 工作區，不需要另外有 Microsoft 帳戶。

### 新增 Azure Active Directory 組織到現有的工作區

1. 在 Operational Insights 的 [設定] 頁面上，按一下 [帳戶]，然後按一下 [新增組織]。![邀請](./media/operational-insights-setup-workspace/add-org.png)
2. 複查有關組織帳戶的資訊，然後按 [下一步]。
3. 輸入您 Azure Active Directory 網域之管理員的識別資訊，然後按一下 [登入]。
4. 按一下 [授與存取權] 讓 Operational Insights 使用您 Active Directory 網域中的識別資訊。![已連結](./media/operational-insights-setup-workspace/ad-existing01.png)


## 編輯現有的使用者類型

您可以為與您的 Operational Insights 帳戶關聯的使用者變更帳戶角色。您有下列角色選項：

 - *管理員*：可以管理使用者、檢視和處理所有警示，以及新增和移除伺服器

 - *使用者*：可以檢視和處理所有警示，以及新增和移除伺服器

### 編輯帳戶
1. 在 Operational Insights 中 [帳戶] 索引標籤的 [設定] 頁面上，為使用者選取您要變更的角色。
2. 按一下 [確定]。

## 從 Operational Insights 工作區移除使用者

使用下列步驟從 Operational Insights 工作區移除使用者。請注意，這不會關閉使用者的工作區，而會移除使用者與工作區之間的關聯。如果使用者與多個工作區關聯，該使用者還是能夠登入 Operational Insights。

### 從工作區移除使用者

1. 在 Operational Insights 之 [帳戶] 索引標籤的 [設定] 頁面上，按一下您想移除之使用者名稱旁邊的 [移除]。
2. 按一下 [確定] 確認您要移除該使用者。

## 關閉 Operational Insights 工作區

當您關閉 Operational Insights 工作區時，與您的工作區相關的所有資料，在關閉工作區之後的 30 天內，都會從 Operational Insights 服務刪除。

如果您是管理員，而且有多位使用者與工作區關聯，則這些使用者與工作區之間的關聯將會中斷。如果使用者與其他工作區關聯，則他們可以繼續使用 Operational Insights 的其他工作區。不過，如果使用者沒有與其他工作區關聯，則他們必須建立新的工作區才能使用 Operational Insights。

### 關閉 Operational Insights 工作區

1. 在 Operational Insights 之 [帳戶] 索引標籤中的 [設定] 頁面上，按一下 [關閉工作區]。

2. 選取其中一個關閉工作區的原因，或者在文字方塊中輸入其他原因。

3. 按一下 [關閉工作區]。

## 其他資源
- [Azure Operational Insights 中的 IIS 記錄檔格式需求](http://blogs.technet.com/b/momteam/archive/2014/09/19/iis-log-format-requirements-in-system-center-advisor.aspx)
- 請在[意見反應論壇](http://feedback.azure.com/forums/267889-azure-operational-insights/category/88086-log-management-and-log-collection-policy)中，參閱社群要求我們實作的記錄檔的其他資料來源和類型

<!---HONumber=August15_HO6-->