<properties
   pageTitle="Azure AD Connect 同步：作業工作和考量 | Microsoft Azure"
   description="本主題描述 Azure AD Connect 同步處理的作業工作以及如何準備操作此元件。"
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="StevenPo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="01/21/2016"
   ms.author="andkjell"/>

# Azure AD Connect 同步處理：作業工作和考量
本主題的目標在於描述 Azure AD Connect 同步處理的作業工作。

## 預備模式
預備模式可以用於許多案例，包括：

-	高可用性。
-	測試和部署新的組態變更。
-	引進新的伺服器並解除委任舊伺服器。

利用預備模式中的伺服器，您可以在啟用伺服器之前變更組態並預覽變更。它也可以讓您執行完整的匯入和完整的同步處理，以在變更生產環境之前確認所有變更皆如預期。

您可以在安裝期間選取狀態為**預備模式**的伺服器。這樣會讓伺服器匯入和同步處理，但它不會執行任何匯出。預備模式的伺服器無法執行密碼同步處理或啟用密碼回寫，即使您選取這些功能也一樣。當您停用預備模式時，伺服器會開始匯出並啟用密碼同步處理和密碼回寫 (如果已啟用)。

預備模式的伺服器將繼續收到來自 Active Directory 和 Azure AD 的變更。這樣會永遠有最新的變更複本，並且可以非常快速地接取代另一部伺服器的責任。如果您對您的主要伺服器進行組態變更，則您有責任對預備模式的伺服器進行相同的變更。

對於具備較舊同步處理技術知識的人員，預備模式是不同的，因為伺服器有它自己的 SQL 資料庫。這樣可讓預備模式伺服器位於不同的資料中心。

### 驗證伺服器的組態
若要套用此方法，請遵循下列步驟：

1. 準備
2. 匯入和同步處理
3. Verify
4. 切換作用中的伺服器

**準備**

1. 安裝 Azure AD Connect、選取 [**預備模式**]，然後取消選取安裝精靈中最後一個頁面上的 [**啟動同步處理**]。這可讓我們手動執行同步處理引擎。![ReadyToConfigure](./media/active-directory-aadconnectsync-operations/readytoconfigure.png)
2. 登出/登入並從 [開始] 功能表中選取 [同步處理服務]。

**匯入和同步處理**

1. 選取 [連接器]，並選取第一個類型為 [Active Directory 網域服務] 的連接器。按一下 [執行]，選取 [完整匯入] 和 [確定]。對這種類型的所有連接器執行此動作。
2. 選取 [Azure Active Directory (Microsoft)] 類型的連接器。按一下 [執行]，選取 [完整匯入] 和 [確定]。
4. 確定仍然選取連接器，並針對每一個 [Active Directory 網域服務] 類型的連接器按一下 [執行]、選取 [差異同步處理] 和 [確定]。
5. 選取 **Azure Active Directory (Microsoft)** 類型的連接器。按一下 [執行]，選取 [差異同步處理]，然後選取 [確定]。

您現在已預備匯出變更至 Azure AD 和內部部署 AD (如果您正在使用 Exchange 混合部署)。接下來的步驟可讓您在實際開始匯出至目錄之前，檢查將要變更的項目。

**Verify**

1. 啟動 CMD 命令提示字元並移至 `%Program Files%\Microsoft Azure AD Sync\bin`
2. 執行：`csexport "Name of Connector" %temp%\export.xml /f:x`<BR/> 連接器名稱可以在同步處理服務中找到。它的名稱類似 Azure AD 的 "contoso.com – AAD"。
3. 執行：`CSExportAnalyzer %temp%\export.xml > %temp%\export.csv`
4. 現在您在 %temp% 中已經有名稱為 export.csv 的檔案，可在 Microsoft Excel 中加以檢查。此檔案包含將要匯出的所有變更。
5. 對資料或組態進行必要的變更並再次執行這些步驟 (匯入和同步處理和驗證)，直到要匯出的變更皆如預期進行。

**了解 export.csv 檔案**

大部分的檔案都簡單易懂。要了解內容所需的一些縮寫：

- OMODT – 物件修改類型。指出在物件層級的作業是否為新增、更新或刪除。
- AMODT – 屬性修改類型。指出在屬性層級的作業是否為新增、更新或刪除。

如果屬性值是多重值，則不會每個變更都顯示。只有新增和移除值的數目會顯示。

**切換作用中的伺服器**

1. 在目前作用中的伺服器上，關閉伺服器 (DirSync/FIM/Azure AD Sync) 讓它不會匯出至 Azure AD 或將它設為預備模式 (Azure AD Connect)。
2. 在「預備模式」的伺服器上執行安裝精靈並停用「預備模式」。![ReadyToConfigure](./media/active-directory-aadconnectsync-operations/additionaltasks.png)

## 災害復原
實作設計的一部分是規劃您在災害中失去同步處理伺服器時如何應對。有不同的模型可供使用，要使用哪一種取決於許多因素，包括：

-	您在 Azure AD 的停機期間無法變更物件的容錯能力如何？
-	如果您使用密碼同步化，使用者是否接受他們在變更其內部部署時必須在 Azure AD 中使用舊密碼？
-	您是否對即時作業有相依性，例如密碼回寫？

根據這些問題的解答和組織的原則，可實作下列其中一個策略：

-	必要時重建。
-	具有備用的待命伺服器，稱為「預備模式」。
-	使用虛擬機器。

因為 Azure AD Connect 同步處理對 SQL 資料庫有相依性，如果您不使用包含在 Azure AD Connect 中的 SQL Express，您也應該檢閱 SQL 高可用性一節。

### 必要時重建
必要時規劃伺服器重建為可行的策略。在許多情況下，安裝同步處理引擎並執行初始匯入，同步處理可以在幾個小時內完成。如果沒有可用的備用伺服器，則可以暫時使用網域控制站裝載同步處理引擎。

同步處理引擎伺服器不會儲存有關物件的任何狀態，因此可以從 Active Directory 與 Azure AD 中的資料重建資料庫。**sourceAnchor** 屬性可用來聯結來自內部部署和雲端的物件。如果您重建具有現有物件內部部署與雲端的伺服器，同步處理引擎的重新安裝會符合這些項目。您需要記錄和儲存的項目是對伺服器進行的組態變更，例如篩選和同步處理規則。這些項目必須在您開始同步處理之前重新套用。

### 具有備用的待命伺服器 - 預備模式
如果您有更複雜的環境，則建議使用一或多個待命伺服器。您可以在安裝期間啟用狀態為「預備模式」的伺服器。

如需詳細資料，請參閱[預備模式](#staging-mode)。

### 使用虛擬機器
一般和受支援的方法是在虛擬機器中執行同步處理引擎。如果主機有問題，可將內含同步處理引擎伺服器的映像移轉到另一部伺服器。

### SQL 高可用性
如果未使用隨附於 Azure AD Connect 的 SQL Server Express，也應該考慮 SQL Server 的高可用性。唯一受支援的高可用性解決方案是 SQL 叢集。不支援的解決方案包括鏡像和永遠開啟。

## 後續步驟
深入了解 [Azure AD Connect 同步](active-directory-aadconnectsync-whatis.md)組態。

深入了解[整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)。

<!---HONumber=AcomDC_0128_2016-->