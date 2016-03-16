<properties
	pageTitle="Azure Active Directory Identity Protection | Microsoft Azure"
	description="了解 Azure AD Identity Protection 如何讓您限制攻擊者利用遭入侵的身分識別或裝置的能力，以及保護先前疑似或已知遭到入侵的身分識別或裝置。"
	services="active-directory"
	keywords="azure active directory identity protection, cloud app discovery, 管理應用程式, 安全性, 風險, 風險層級, 弱點, 安全性原則"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/02/2016"
	ms.author="markvi"/>

#Azure Active Directory Identity Protection 

Azure Active Directory Identity Protection 是一項安全性服務，可供整合檢視會影響組織身分識別的風險事件和潛在弱點。十多年來，Microsoft 一直保護雲端架構身分識別的安全，並透過 Azure AD Identity Protection 持續為企業客戶提供相同的保護系統。Identity Protection 利用現有 Azure AD 的異常偵測功能 (可透過 Azure AD 的異常活動報告取得)，並引進可即時偵測異常的新風險事件類型。

> [AZURE.NOTE] Identity Protection 預覽目前僅適用於在美國境內佈建的 Azure AD 租用戶。
 

大部分的安全性缺口出現於當攻擊者藉由竊取使用者的身分識別來取得環境的存取權時。攻擊者變得越來越擅於利用協力廠商缺考，以及使用複雜的網路釣魚攻擊。一旦攻擊者取得更低權限的使用者帳戶的存取權，他們即可透過橫向移動，相當容易地存取重要的公司資源。因此務必保護所有身分識別，當身分識別遭到入侵時，請主動防止遭入侵的身分識別被濫用。

探索遭入侵的身分識別並不容易。幸運的是，Identity Protection 有所幫助：Identity Protection 會使用調適性機器學習演算法和啟發學習法來偵測異常行為以及可能表示身分識別已遭入侵的風險事件。
 
Identity Protection 會使用此資料來產生報告和警示，讓您調查這些風險事件並採取適當的補救或緩和動作。
 
但是，Azure Active Directory Identity Protection 不只是監視和報告工具而已。Identity Protection 會根據風險事件，計算每位使用者的使用者風險層級，讓您設定以風險為基礎的原則來自動保護您組織的身分識別。除了 Azure Active Directory 與 EMS 所提供的其他條件式存取控制以外，這些以風險為基礎的原則可以自動封鎖或提供調適性補救動作，包括重設密碼以及強制 Multi-Factor Authentication。

####探索 Identity Protection 的功能 

**偵測風險事件和有風險的帳戶：**

- 使用機器學習服務和啟發式規則偵測 6 種風險事件類型 

- 計算使用者風險層級

- 提供自訂建議，藉由反白顯示弱點來改善整體安全性狀態

<br>

**調查風險事件：**

- 傳送風險事件的通知

- 使用相關和內容資訊來調查風險事件

- 提供基本工作流程來追蹤調查

- 讓您輕鬆存取補救動作，例如重設密碼

<br>

**以風險為基礎的條件式存取原則：**

- 此原則會藉由封鎖登入或要求 Multi-Factor Authentication 挑戰來緩和有風險的登入。

- 此原則會封鎖或保護有風險的使用者帳戶

- 此原則會要求註冊使用者以便進行 Multi-Factor Authentication


## 偵測和風險

### 風險事件

由 Identity Protection 標示為可疑的風險事件，表示身分識別可能已被入侵。如需風險事件的完整清單，請參閱[風險事件類型](#types-of-risk-events)。有些這些風險事件已可透過 Azure 管理入口網站中的 Azure AD 異常活動報告取得。下表列出各種風險事件類型和對應的 **Azure AD 異常活動**報告。Microsoft 將持續投入這個領域，並且計劃持續改善現有風險事件的偵測精確度，以及持續加入新的風險事件類型。



| Identity Protection 風險事件類型 | 對應的 Azure AD 異常活動報告 |
| :-- | :-- |
| 認證外洩 | 認證外洩的使用者 |
| 不可能到達非典型位置的移動 |	異常的登入活動 |
| 從受感染的裝置登入 | 從可能受感染的裝置登入 |
| 從匿名 IP 位址登入 | 從不明來源登入 |
| 從具有可疑活動的 IP 位址登入 |	從具有可疑活動的 IP 位址登入 |
| 從不熟悉的位置登入 | - | 
| 鎖定事件 (不在公開預覽中) | - |

下列 Azure AD 異常活動報告不會納入為 Azure AD Identity Protection 中的風險事件，因此不會透過 Identity Protection 提供。這些報告仍可在 Azure 管理入口網站中取得，不過將會在未來某個時候淘汰，因為它們正由 Identity Protection 中的風險事件所取代。

- 在多次失敗後登入
- 從多個地理區域登入

### 風險層級

風險事件的風險層級可表示風險事件的嚴重性 (高、中或低)。風險層級可協助 Identity Protection 使用者排定為了降低組織風險而必須採取之行動的優先順序。風險事件的嚴重性代表身分識別入侵 (結合它通常引發的雜訊) 預測的訊號強度。

- **高**：高信賴度和高嚴重性風險事件。這些事件強烈指出使用者的身分識別已遭入侵，而且應該立即補救任何受影響的使用者帳戶。

- **中**：高嚴重性，但信賴度較低的風險事件，或反之亦然。這些事件具有潛在風險，而且應該補救任何受影響的使用者帳戶。

- **低**：低信賴度和低嚴重性風險事件。此事件可能不需要採取立即行動，但與其他風險事件結合時，可能強烈指出身分識別遭到入侵。


![風險層級](./media/active-directory-identityprotection/01.png "風險層級")

 

風險事件會以**即時**方式，或在風險事件發生後的後處理中 (離線) 識別。目前 Identity Protection 中的大部分風險事件均為離線計算，並且會在 2-4 小時內顯示於 Identity Protection。進行即時評估時，即時風險事件會在 5-10 分鐘內顯示於 Identity Protection 主控台。

目前有數個舊版用戶端不支援即時風險事件偵測與防護。因此，無法即時偵測或預防從這些用戶端登入。

### 風險事件類型

這一節概述可用的風險事件類型

#### 認證外洩

Microsoft 安全性研究人員發現外洩的認證公開張貼於黑暗網路 (Dark Web)。這些認證通常在純文字中找到。它們會根據 Azure AD 認證進行檢查，如果有相符項目，則會在 Identity Protection 中回報為「認證外洩」。

認證外洩風險事件會被歸類為「高」嚴重性風險事件，因為它們清楚指出攻擊者可使用使用者名稱和密碼。

#### 不可能到達非典型位置的移動

此風險事件類型會識別來自距離遙遠的位置的兩次登入，而根據使用者過去的行為，其中至少有一個位置可能不尋常。此外，兩次登入之間的時間比使用者從第一個位置移到第二個位置所需的時間還要短，這表示有不同的使用者正在使用相同的認證。

這種機器學習演算法忽略明顯的「誤判」，以致發生不可能的移動情況，例如組織中的其他使用者定期使用的 VPN 和位置。系統有為期 14 天的初始學習期間，它會在這段期間了解新使用者的登入行為。

不可能的移動通常會明顯指出駭客已能夠成功登入。不過，當使用者使用新裝置或使用組織中其他使用者通常不會使用的 VPN 進行移動時，可能會發生誤判。另一個誤判來源是誤將伺服器 IP 當作用戶端 IP 傳遞的應用程式，其可能會導致從裝載應用程式後端的資料中心進行登入 (這些通常是 Microsoft 資料中心，其可能導致從 Microsoft 擁有的 IP 位址進行登入)。這些誤判以致此風險事件的風險層級為「中」。

####從受感染的裝置登入

此風險事件類型會識別從感染惡意程式碼的裝置登入，已知這類登入會主動與 Bot 伺服器通訊。讓使用者裝置的 IP 位址與聯繫 Bot 伺服器的 IP 位址相互關聯，即可判定此類型。

此風險事件可識別 IP 位址，而不是使用者裝置。如果單一 IP 位址背後有數個裝置，而只有某些裝置受 Bot 網路控制，則來自其他裝置的登入可能會不必要地觸發此事件，這就是將此風險事件歸類為「低」的原因。

建議您連絡使用者並掃描使用者的所有裝置。使用者的個人裝置也可能受到感染，或如前所述，可能是其他人從與使用者相同的 IP 位址使用受感染的裝置。受感染的裝置通常是受到防毒軟體尚未識別的惡意程式碼所感染，這也表示不良的使用者習慣可能會導致裝置受到感染。

如需如何處理惡意程式碼感染的詳細資訊，請參閱[惡意程式碼防護中心](http://go.microsoft.com/fwlink/?linkid=335773&clcid=0x409)。


#### 從匿名 IP 位址登入

此風險事件類型會識別從被視為匿名 Proxy IP 位址的 IP 位址成功登入的使用者。這些 Proxy 通常由想要隱藏其裝置 IP 位址的人員使用，而且可能用於惡意意圖。

我們建議您立即連絡使用者，確認他們是否使用匿名 IP 位址。此風險事件類型的風險層級為「中」，因為匿名 IP 本身並未強烈指出帳戶遭到入侵。

#### 從具有可疑活動的 IP 位址登入

此風險事件類型會識別在短期內透過多個使用者帳戶多次嘗試登入失敗的 IP 位址。這符合攻擊者所使用的 IP 位址流量模式，而且強烈指出帳戶已經或即將遭到入侵。這種機器學習演算法忽略明顯的「誤判」，例如組織中的其他使用者定期使用的 IP 位址。系統有為期 14 天的初始學習期間，它會在這段期間了解新使用者和新租用戶的登入行為。

我們建議您連絡使用者，確認他們是否實際從標示為可疑的 IP 位址進行登入。此事件類型的風險層級為「中」，因為相同 IP 位址背後可能有數個裝置，而只有某些裝置可能負責進行可疑的活動。


#### 從不熟悉的位置登入

此風險事件類型是一種即時登入評估機制，它會考量過去的登入位置 (IP、經緯度和 ASN) 以判斷新的 / 不熟悉的位置。系統會儲存有關使用者先前所用位置的資訊，並考量這些「熟悉的」位置。從不在熟悉位置清單中的位置登入時，甚至會觸發此風險。系統有為期 14 天的初始學習期間，在這段期間內，它不會將任何新位置標示為不熟悉的位置。系統也會忽略從熟悉的裝置以及地理上靠近熟悉位置的位置進行的登入。<br> 不熟悉的位置可以強烈指出攻擊者可嘗試使用遭竊的身分識別。當使用者正在移動，並試用新裝置或使用新的 VPN 時，可能會發生誤判。這些誤判以致此事件類型的風險層級為「中」。

### 弱點

弱點是您的環境中攻擊者可以利用的弱點。我們建議您處理這些弱點，以改善您組織的安全性狀態，並防止攻擊者利用這些弱點。

#### 未設定 Multi-Factor Authentication 註冊 

此弱點可協助您控制組織中部署的 Azure Multi-Factor Authentication。

Azure Multi-Factor Authentication 會為使用者驗證提供第二層安全性。這有助於保護對資料與應用程式的存取，同時可以滿足使用者對簡單登入程序的需求。它可以透過一些簡單的驗證選項 (例如電話、文字訊息，或行動應用程式通知或驗證代碼，以及第三方 OATH 權杖) 來提供強大的驗證功能。

建議您要求對使用者登入進行 Multi-Factor Authentication。在 Identity Protection 提供的以風險為基礎的條件式存取原則中，Multi-Factor Authentication 扮演關鍵角色。

如需詳細資訊，請參閱[什麼是 Azure Multi-Factor Authentication？](../multi-factor-authentication/multi-factor-authentication.md)


#### 未受管理的雲端應用程式

此弱點可協助您識別組織中未受管理的雲端應用程式。
 
在現代企業中，IT 部門通常不會知道組織中的使用者用於進行工作的所有雲端應用程式。很容易知道為什麼系統管理員必須對未經授權存取公司資料、可能的資料外洩和其他安全性風險有所顧慮。

我們建議您的組織部署 Cloud App Discovery 來探索未受管理的雲端應用程式，以及管理這些使用 Azure Active Directory 的應用程式。

如需詳細資訊，請參閱[使用 Cloud App Discovery 尋找未受管理的雲端應用程式](active-directory-cloudappdiscovery-whatis.md)。



####來自 Privileged Identity Management 的安全性警示

此弱點可協助您找出並解決有關您組織中特殊權限身分識別的警示。

若要讓使用者能夠執行特殊權限作業，組織必須賦予使用者在 Azure AD、Azure 或 Office 365 資源或其他 SaaS 應用程式中的暫時或永久特殊權限存取權。這些特殊權限的使用者會增加您組織的受攻擊面。此弱點可協助您識別具有非必要特殊權限存取權的使用者，並採取適當的行動來減少或消除其所造成的風險。

建議您的組織使用 Azure AD Privileged Identity Management 來管理、控制和監視特殊權限身分識別，以及其在 Azure AD 和其他 Microsoft 線上服務 (如 Office 365 或 Microsoft Intune) 中的資源存取權。

如需詳細資訊，請參閱 [Azure AD Privileged Identity Management](active-directory-privileged-identity-management-configure.md)。

## 調查
您通常會從 Identity Protection 儀表板開始使用 Identity Protection。

<br><br> ![補救](./media/active-directory-identityprotection/29.png "補救") <br>

儀表板可讓您存取：
 
- 報告，例如 [標示有風險的使用者]、[風險事件] 和 [弱點]
- 設定，例如 [安全性原則]、[通知] 和 [Multi-Factor Authentication 註冊] 的組態
 

這通常是調查的起點，而在調查過程中會檢閱風險事件相關活動、記錄檔和其他相關資訊，以決定是否需要採取補救或緩和步驟，了解身分識別如何遭到入侵，以及了解遭到入侵的身分識別如何被利用。


### 通知

Azure AD Identity Protection 會傳送兩種自動化通知電子郵件，協助您管理使用者風險和風險事件：

- 使用者遭到入侵的警示電子郵件

- 每週精選文章電子郵件

#### 使用者遭到入侵的警示電子郵件

當 Azure AD Identity Protection 發現帳戶遭到入侵時，就會產生使用者遭到入侵的電子郵件警示。此電子郵件包含 Identity Protection 主控台中「標示有風險的使用者」報告的連結。我們建議立即調查遭到入侵的使用者通知。


#### 每週精選文章電子郵件

每週精選文章電子郵件包含新風險事件的摘要。<br> 其中包括：
- 有風險的使用者
- 已報告的風險事件
- 偵測到的弱點
- Identity Protection 中相關報告的連結


<br> ![補救](./media/active-directory-identityprotection/400.png "補救") <br>

預設會將遭到入侵的使用者警示傳送給所有 Azure Active Directory 系統管理員。您可以自訂收件者：

- 遵循警示底部的管理收件者連結

- 按一下 Identity Protection 設定之下的 [通知]
 
<br> ![使用者風險](./media/active-directory-identityprotection/62.png "使用者風險") <br>
 



## 什麼是使用者風險層級？

使用者風險層級可指出使用者的身分識別遭到入侵的可能性 (高、中或低)。它會根據與使用者的身分識別相關聯的使用者風險事件進行計算。

風險事件的狀態為 [作用中] 或 [已關閉]。只有 [作用中] 的風險事件會納入使用者風險計算。

使用下列輸入來計算使用者風險層級：

- 影響使用者的作用中風險事件
- 這些事件的風險層級 
- 是否已採取任何補救動作 

<br> ![使用者風險](./media/active-directory-identityprotection/86.png "使用者風險") <br>



您可以使用使用者風險層級來建立條件式存取原則，以阻止有風險的使用者進行登入，或迫使他們安全地變更其密碼。


## 手動關閉風險事件

在大部分情況下，您將採取補救動作 (例如重設安全的密碼) 來自動關閉風險事件。但是，這不一定都可行。<br> 因為 [作用中] 的風險事件會納入使用者風險計算，所以您可能必須以手動方式降低風險層級，但不要手動關閉風險事件。<br> 在調查過程中，您可以選擇採取下列任何動作，以變更風險事件的狀態：

<br> ![動作](./media/active-directory-identityprotection/34.png "動作") <br>

- **解決** - 如果在調查風險事件之後，您在 Identity Protection 外部採取適當的補救動作，而且您認為應該將風險事件視為已關閉，請將事件標示為 [已解決]。解決的事件會將風險事件的狀態設定為 [已關閉]，而此風險事件便不再算是使用者風險。

- **標示為誤判** - 在某些情況下，您可能會調查某個風險事件並發現該事件被誤標為有風險。您可以將風險事件標示為誤判，以減少發生這種情況。這可協助機器學習演算法未來改善類似事件的分類。誤判事件的狀態為 [已關閉]，而且不再算是使用者風險。

- **忽略** - 如果您尚未採取任何補救動作，但希望風險事件從作用中清單中移除，您可忽略風險事件，且事件狀態將會是 [已關閉]。忽略的事件不算是使用者風險。這個選項只能用於不尋常的情況下。

- **重新啟用** - 可以重新啟用已手動關閉的風險事件 (藉由選擇 [解決]、[誤判] 或 [略過])，並將事件狀態設回 [作用中]。重新啟用的風險事件會納入使用者風險層級計算。無法重新啟用透過補救 (例如重設安全的密碼) 關閉的風險事件。



## 補救使用者風險事件

補救就是用來保護先前疑似或已知遭到入侵的身分識別或裝置的動作。補救動作可讓身分識別或裝置還原到安全的狀態，以及解決先前與身分識別或裝置相關聯的風險事件。

若要補救使用者風險事件，您可以：

- 重設安全的密碼，以便手動補救使用者風險事件 

- 設定使用者風險安全性原則，以自動緩和或補救使用者風險事件

- 重新安裝受感染的裝置映像


### 重設安全的密碼

重設安全的密碼是許多風險事件的有效補救動作，一旦執行，就會自動關閉這些風險事件並重新計算使用者風險層級。您可以使用 Identity Protection 主控台，為有風險的使用者起始密碼重設。

此主控台提供兩種重設密碼的方法：

**重設密碼** - 如果使用者已註冊 Multi-Factor Authentication，選取 [要求使用者重設密碼] 可讓使用者自行復原。在使用者下次登入期間，使用者必須成功解決 Multi-Factor Authentication 挑戰，且被迫變更密碼。如果使用者帳戶尚未註冊 Multi-Factor Authentication，則無法使用此選項。

**暫時密碼** - 選取 [產生暫時密碼]，立即讓現有的密碼失效，並且為使用者建立新的暫時密碼。將新的暫時密碼傳送到使用者的備用電子郵件地址，或傳送給使用者的經理。因為此密碼是暫時的，所以會提示使用者在登入時變更密碼。

<br> ![原則](./media/active-directory-identityprotection/71.png "原則") <br>



## 使用者風險安全性原則

使用者風險安全性原則是條件式存取原則，可評估特定使用者的風險層級，並根據預先定義的條件和規則來套用補救和緩和動作。<br><br> ![使用者風險原則](./media/active-directory-identityprotection/500.png "使用者風險原則") <br>

Azure AD Identity Protection 可讓您執行下列作業，以協助您管理標示有風險的使用者的緩和與補救動作：

- 設定要套用原則的使用者和群組<br><br> ![使用者風險原則](./media/active-directory-identityprotection/501.png "使用者風險原則") <br>

- 設定可觸發密碼變更的使用者風險層級臨界值 (低、中或高) <br><br> ![使用者風險原則](./media/active-directory-identityprotection/502.png "使用者風險原則") <br>

- 設定可觸發封鎖使用者的使用者風險層級臨界值 (低、中或高) <br><br> ![使用者風險原則](./media/active-directory-identityprotection/503.png "使用者風險原則") <br>

- 在啟用變更前檢閱和評估其影響 <br><br> ![使用者風險原則](./media/active-directory-identityprotection/504.png "使用者風險原則") <br>


選擇 [高] 臨界值可減少觸發原則的次數，並將對使用者的影響降至最低。不過，這會從原則中排除標示 [低] 和 [中] 度風險的使用者，而無法保護先前疑似或已知遭到入侵的身分識別或裝置。

設定原則時，

- 排除可能會產生大量誤判的使用者 (開發人員、安全性分析人員)

- 在啟用原則並不實用 (例如無法存取技術服務人員) 的地區設定中排除使用者

- 在原則推出初期，或如果您必須盡量減少使用者所看到的挑戰，請使用 [高] 臨界值。

- 如果您的組織需要更高的安全性，請使用 [低] 臨界值。選取 [低] 臨界值會帶來額外的使用者登入挑戰，並提高安全性。

大部分組織的建議預設值是設定 [中] 臨界值的規則，以取得可用性與安全性之間的平衡。



### 緩和使用者風險事件
系統管理員可以設定使用者風險安全性原則，以根據風險層級防止使用者登入。

封鎖登入：
 
- 避免對受影響的使用者產生新的使用者風險事件

- 可讓系統管理員補救會影響使用者身分識別的風險事件，並將它還原到安全的狀態





### 使用者風險補救和緩和流程  

下列各節針對使用者風險補救和緩和流程提供大致的使用者體驗。

#### 遭到入侵的帳戶復原流程

設定使用者風險安全性原則之後，符合原則中指定的使用者風險層級 (因而假定遭到入侵) 的使用者，必須先經歷使用者入侵復原流程，才可以登入。

使用者入侵復原流程有三個步驟：

1. 使用者獲知其帳戶安全性因為可疑活動或認證外洩而有風險。

<br> ![補救](./media/active-directory-identityprotection/101.png "補救") <br>

2.	使用者必須解決安全性挑戰以證明其身分識別。如果使用者已註冊 Multi-Factor Authentication，他們可以從損害中自行復原。他們必須回傳送至其電話號碼的安全碼。 

<br> ![補救](./media/active-directory-identityprotection/110.png "補救") <br>


3.	最後，使用者會被迫變更其密碼，因為其他人可能有其帳戶的存取權。這項體驗的螢幕擷取畫面如下。
 
<br> ![補救](./media/active-directory-identityprotection/111.png "補救") <br>







 
#### 重設密碼
如果使用者無法登入，系統管理員可以為其產生暫時密碼。他們必須在下次登入時變更密碼。變更密碼可為使用者補救並關閉大多數的風險事件類型。

<br> ![補救](./media/active-directory-identityprotection/160.png "補救") <br>


### 使用者風險層級緩和

### 遭到入侵的帳戶：已封鎖 

若要讓遭到使用者風險安全性原則封鎖的使用者解除封鎖，該使用者必須連絡系統管理員或技術服務人員。藉由解決 Multi-Factor Authentication 自行復原，不是此種情況的適用選項。

<br> ![補救](./media/active-directory-identityprotection/104.png "補救") <br>


## 緩和登入風險事件 
緩和動作可限制攻擊者利用遭到入侵的身分識別或裝置的能力，但不需將身分識別或裝置還原至安全的狀態。緩和並未解決先前與身分識別或裝置相關聯的登入風險事件。

您可以在 Azure AD Identity Protection 中使用條件式存取，以自動緩和登入風險事件。使用這些原則時，請考慮使用者或登入的風險層級，以封鎖有風險的登入或要求使用者執行 Multi-Factor Authentication。這些動作可防止攻擊者利用遭竊的身分識別而造成損害，而且會讓您有一些時間可保護身分識別。


## 登入風險安全性原則

登入風險原則是條件式存取原則，可評估特定登入的風險，並根據預先定義的條件和規則來套用緩和動作。<br><br> ![登入風險原則](./media/active-directory-identityprotection/700.png "登入風險原則") <br>

Azure AD Identity Protection 可讓您執行下列作業，以協助您管理有風險登入的緩和動作：

- 設定要套用原則的使用者和群組<br><br> ![登入風險原則](./media/active-directory-identityprotection/701.png "登入風險原則") <br>

- 設定可針對受影響的登入觸發 Multi-Factor Authentication 挑戰的登入風險層級臨界值 (低、中或高) <br><br> ![登入風險原則](./media/active-directory-identityprotection/702.png "登入風險原則") <br>

- 設定可封鎖受影響登入的登入風險層級臨界值 (低、中或高) <br><br> ![登入風險原則](./media/active-directory-identityprotection/703.png "登入風險原則") <br>

- 在啟用變更前檢閱和評估其影響 <br><br> ![登入風險原則](./media/active-directory-identityprotection/704.png "登入風險原則") <br>

 
選擇 [高] 臨界值可減少觸發原則的次數，並將對使用者的影響降至最低。<br> 不過，它會從原則中排除標示 [低] 和 [中] 度風險的登入，而無法阻止攻擊者利用遭到入侵的身分識別。

設定原則時，

- 排除不會 / 不能有 Multi-Factor Authentication 的使用者

- 在啟用原則並不實用 (例如無法存取技術服務人員) 的地區設定中排除使用者

- 排除可能會產生大量誤判的使用者 (開發人員、安全性分析人員)

- 在原則推出初期，或如果您必須盡量減少使用者所看到的挑戰，請使用 [高] 臨界值。

- 如果您的組織需要更高的安全性，請使用 [低] 臨界值。選取 [低] 臨界值會帶來額外的使用者登入挑戰，並提高安全性。

大部分組織的建議預設值是設定 [中] 臨界值的規則，以取得可用性與安全性之間的平衡。

 
登入風險原則：

- 會套用至所有使用新式驗證的瀏覽器流量和登入。
- 不會套用至使用較舊安全性通訊協定的應用程式，其做法是停用位於同盟 IDP (例如 ADFS) 的 WS-Trust 端點。

Identity Protection 主控台中的 [風險事件] 頁面會列出所有事件：

- 此原則已套用至
- 您可以檢閱活動並判斷動作是否適當 




## Multi-Factor Authentication 註冊原則

Multi-Factor Authentication 用來取得使用者身分識別的額外保證。<br> 幫您的組織準備防禦帳戶入侵並從中復原時，註冊 Multi-Factor Authentication 是關鍵步驟。<br> ![MFA 註冊](./media/active-directory-identityprotection/600.png "MFA 註冊") <br>

Azure AD Identity Protection 可讓您執行下列作業，以協助您管理首次 Multi-Factor Authentication 註冊：

- 設定要套用原則的使用者和群組<br><br> ![MFA 註冊](./media/active-directory-identityprotection/601.png "MFA 註冊") <br>

- 定義多久以後才能略過註冊 <br><br> ![MFA 註冊](./media/active-directory-identityprotection/602.png "MFA 註冊") <br>

- 檢視受影響使用者的目前註冊狀態。 <br><br> ![MFA 註冊](./media/active-directory-identityprotection/603.png "MFA 註冊") <br>


##登入風險緩和流程 

#### 有風險的登入復原流程

當系統管理員設定登入風險的原則後，受影響的使用者會在嘗試登入時收到通知。

有風險的登入流程有兩個步驟：

1. 使用者獲知偵測到不尋常的登入，例如從新的位置、裝置或應用程式登入 <br> ![補救](./media/active-directory-identityprotection/120.png "補救") <br>

2. 使用者必須解決安全性挑戰以證明其身分識別。如果使用者已註冊 Multi-Factor Authentication，他們必須回傳送至其電話號碼的安全碼。由於這只是有風險的登入，並不是遭入侵的帳戶，所以使用者不必在此流程中變更密碼。<br> ![補救](./media/active-directory-identityprotection/121.png "補救") <br>
 
#### 已封鎖有風險的登入
系統管理員也可以選擇設定登入風險原則，以根據風險層級防止使用者登入。若要解除封鎖，使用者必須連絡系統管理員或技術服務人員，或者嘗試從熟悉的位置或裝置登入。藉由解決 Multi-Factor Authentication 自行復原，不是此種情況的適用選項。

<br> ![補救](./media/active-directory-identityprotection/130.png "補救") <br>
 
#### Multi-Factor Authentication 註冊

在遭到入侵的帳戶復原流程和有風險的登入流程中，最佳的使用者體驗皆是使用者可以自行復原。如果使用者已註冊 Multi-Factor Authentication，他們便有與其帳戶相關聯的電話號碼可用來通過安全性挑戰。從帳戶入侵復原時，不需要技術服務人員或系統管理員介入。因此，強烈建議您讓使用者註冊 Multi-Factor Authentication。

系統管理員可以：

- 設定一個原則，要求使用者設定其帳戶進行其他安全性驗證。 
- 允許最多略過 Multi-Factor Authentication 註冊 30 天 (如果他們想給予使用者註冊前的寬限期)。

 
 <br> ![補救](./media/active-directory-identityprotection/140.png "補救") <br> <br> ![補救](./media/active-directory-identityprotection/141.png "補救") <br> <br> ![補救](./media/active-directory-identityprotection/142.png "補救") <br>

 

#### 在有風險的登入期間註冊 Multi-Factor Authentication

使用者務必註冊 Multi-Factor Authentication，以便他們做好準備並能夠通過安全性挑戰。如果使用者未註冊 Multi-Factor Authentication，但原則要求他們這麼做，則可能在有風險的登入期間要求他們進行註冊。這表示，攻擊者最後還是會被要求新增電話號碼，而不是成為好使用者。<br> 若要避免這種情況，請要求使用者盡快註冊 Multi-Factor Authentication，如此才能在遭到入侵時擁有與其帳戶相關聯的電話號碼。或者，系統管理員可以完全封鎖遭到入侵且未註冊 Multi-Factor Authentication 的使用者。

 <br> ![補救](./media/active-directory-identityprotection/150.png "補救") <br> <br> ![補救](./media/active-directory-identityprotection/151.png "補救") <br>






 
## 腳本

### 模擬風險事件

這一節將概述如何模擬下列風險事件類型：

- 從匿名 IP 位址登入 (容易)

- 從不熟悉的位置登入 (適中)

- 不可能到達非典型位置的移動 (困難)

無法以安全的方式模擬其他風險事件。


#### 從匿名 IP 位址登入

此風險事件類型會識別從被視為匿名 Proxy IP 位址的 IP 位址成功登入的使用者。這些 Proxy 通常由想要隱藏其裝置 IP 位址的人員使用，而且可能用於惡意意圖。

若要模擬從匿名 IP 登入，請遵循下列步驟：

1.	下載 [Tor 瀏覽器](https://www.torproject.org/projects/torbrowser.html.en)
2.	使用 Tor 瀏覽器，瀏覽至 https://myapps.microsoft.com   
3.	輸入您要顯示在「從匿名 IP 位址登入」報告中之帳戶的認證

大功告成！ 5 分鐘內應可在 Identity Protection 中看見此登入。


####從不熟悉的位置登入
不熟悉的位置風險是一種即時登入評估機制，它會考量過去的登入位置 (IP、經緯度和 ASN) 以判斷新的/不熟悉的位置。系統會儲存使用者先前的 IP、經緯度和 ASN，並將這些視為「熟悉的」位置。如果登入位置不符合任何現有的熟悉位置，此登入位置會被視為不熟悉。系統有為期 14 天的初始學習期間，在這段期間內，它不會將任何新位置標示為不熟悉的位置。系統也會忽略從熟悉的裝置以及地理上靠近現有熟悉位置的位置進行的登入。

若要模擬不熟悉的位置，您必須從帳戶未曾用來登入的位置和裝置進行登入。逐步解說：

1.	選擇至少有 14 天登入歷程記錄的帳戶 

2.	請執行下列其中一項：
	
    a.使用 VPN 時，請瀏覽至 [https://myapps.microsoft.com](https://myapps.microsoft.com)，然後輸入您要用於模擬風險事件之帳戶的認證

    b.要求不同位置的關聯以使用帳戶的認證進行登入 (不建議)

大功告成！ 5 分鐘內應可在 Identity Protection 中看見此登入。
 
#### 不可能到達非典型位置的移動
模擬不可能的移動情況相當困難，因為此演算法會使用機器學習服務來剔除誤判，例如不可能來自熟悉裝置的移動，或從目錄中其他使用者所用的 VPN 登入。此外，此演算法在開始產生風險事件之前，需要使用者 3 到 14 天的登入歷程記錄。

1.	使用標準瀏覽器，瀏覽至 [https://myapps.microsoft.com](https://myapps.microsoft.com)  

2.	輸入您想要對其產生不可能移動風險事件之帳戶的認證

3.	現在變更您的使用者代理程式。您可以在 Internet Explorer 中從 [開發人員工具] 變更使用者代理程式，或在 Firefox 或 Chrome 中使用使用者代理程式切換器附加元件來變更您的使用者代理程式。

4.	現在變更您的 IP 位址。使用 VPN、Tor 附加元件，或在不同資料中心於 Azure 中啟動新機器，即可變更您的 IP 位址。

5.	在前次登入的短短幾分鐘內，使用與之前相同的認證登入 [https://myapps.microsoft.com](https://myapps.microsoft.com)

2-4 小時內應可在 Identity Protection 中看見此登入。不過，因為牽涉到複雜的機器學習服務模型，所以可能達不到。針對多個 Azure AD 帳戶複製這些步驟，可能是個好主意。


#### 模擬弱點 
弱點是 Azure AD 環境中不良執行者可以利用的弱點。Azure AD Identity Protection 中目前顯示 3 種會運用其他 Azure AD 功能的弱點。一旦設定好這些功能，這些弱點就會自動顯示在 Identity Protection 中。

-	Azure AD [Multi-Factor Authentication？](../multi-factor-authentication/multi-factor-authentication.md)
-	Azure AD [Cloud App Discovery](active-directory-cloudappdiscovery-whatis.md)。
-	Azure AD [Privileged Identity Management](active-directory-privileged-identity-management-configure.md)。 



###以風險為基礎的條件式存取原則
####使用者入侵風險

**若要測試使用者入侵風險，請執行下列步驟**：

1.	使用租用戶的全域系統管理員認證來登入 [https://portal.azure.com](https://portal.azure.com)。

2.	瀏覽至 [Identity Protection]。

3.	在主要 Azure AD Identity Protection 刀鋒視窗上按一下 [設定]。

4.	在 [入口網站設定] 刀鋒視窗的 [安全性規則] 之下，按一下 [使用者入侵風險]。

5.	在 [登入風險] 刀鋒視窗上關閉 [啟用規則]，然後按一下 [儲存] 設定。

6.	對於指定的使用者帳戶，模擬不熟悉位置或匿名 IP 風險事件。這會將該使用者的使用者風險層級提升至 [中]。

7.	等候幾分鐘並確認使用者的使用者層級為 [中]。

8.	移至 [入口網站設定] 刀鋒視窗。

9.	在 [使用者入侵風險] 刀鋒視窗中，選取 [啟用規則] 之下的 [開啟]。

10.	選取下列其中一個選項：

    a.若要封鎖，請選取 [封鎖登入] 之下 [中]。

    b.若要強制執行安全的密碼變更，請選取 [需要 Multi-Factor Authentication] 之下的 [中]。

13.	按一下 [儲存]。

14. 您現在可以使用具有提高風險等級的使用者進行登入，以測試以風險為基礎的條件式存取。如果使用者風險為「中」，則視您所設定的原則而定，您的登入將被封鎖，或者會強制您變更密碼。<br><br> ![腳本](./media/active-directory-identityprotection/201.png "腳本") <br>

 
####登入風險

 


若要測試登入風險，請執行下列步驟：

1.	使用租用戶的全域系統管理員認證來登入 [https://portal.azure.com](https://portal.azure.com)。

2.	瀏覽至 [Identity Protection]。

3.	在主要 **Azure AD Identity Protection** 刀鋒視窗上按一下 [設定]。

4.	在 [入口網站設定] 刀鋒視窗的 [安全性規則] 之下，按一下 [登入風險]。

5.	在 [登入風險] 刀鋒視窗中，選取 [啟用規則] 之下的 [開啟]。

7.	選取下列其中一個選項：

    a.若要封鎖，請選取 [封鎖登入] 之下 [中]。

    b.若要強制執行安全的密碼變更，請選取 [需要 Multi-Factor Authentication] 之下的 [中]。

8.	若要封鎖，請選取 [封鎖登入] 之下 [中]。

9.	若要強制執行 Multi-Factor Authentication，請選取 [需要 Multi-Factor Authentication] 之下的 [中]。

10.	按一下 [**儲存**]。

11.	您現在可以藉由模擬不熟悉的位置或匿名 IP 風險事件 (這兩種會被視為 [中] 風險事件)，測試以風險為基礎的條件式存取。

<br> ![腳本](./media/active-directory-identityprotection/200.png "腳本") <br>


## 另請參閱

 - [Azure AD 和身分識別展示：Identity Protection 預覽](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)
 - [Identity Protection 詞彙](active-directory-identityprotection-glossary.md)

<!-----HONumber=AcomDC_0302_2016-------->