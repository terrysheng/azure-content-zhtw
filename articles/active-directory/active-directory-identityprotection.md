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
	ms.date="03/09/2016"
	ms.author="markvi"/>

#Azure Active Directory Identity Protection 

Azure Active Directory Identity Protection 是一項安全性服務，可供整合檢視會影響組織身分識別的風險事件和潛在弱點。十多年來，Microsoft 一直保護雲端架構身分識別的安全，並透過 Azure AD Identity Protection 持續為企業客戶提供相同的保護系統。Identity Protection 利用現有 Azure AD 的異常偵測功能 (可透過 Azure AD 的異常活動報告取得)，並引進可即時偵測異常的新風險事件類型。

## 目前的預覽限制
本節列出適用於 Azure Active Directory Identity Protection 的目前預覽限制。

### 國家或區域限制

Azure Active Directory Identity Protection 目前僅適用於 [國家或區域]值為 [美國]的目錄。<br><br> ![補救](./media/active-directory-identityprotection/222.png "補救")


### 身分識別保護和同盟網域

搭配同盟網域預覽 Azure Active Directory Identity Protection 時有下列限制︰

- 安全性原則不適用於同盟網域

- 未偵測到同盟網域的風險事件

##開始使用

大部分的安全性缺口出現於當攻擊者藉由竊取使用者的身分識別來取得環境的存取權時。攻擊者變得越來越擅於利用協力廠商缺考，以及使用複雜的網路釣魚攻擊。一旦攻擊者取得更低權限的使用者帳戶的存取權，他們即可透過橫向移動，相當容易地存取重要的公司資源。因此務必保護所有身分識別，當身分識別遭到入侵時，請主動防止遭入侵的身分識別被濫用。

探索遭入侵的身分識別並不容易。幸運的是，Identity Protection 有所幫助：Identity Protection 會使用調適性機器學習演算法和啟發學習法來偵測異常行為以及可能表示身分識別已遭入侵的風險事件。
 
Identity Protection 會使用此資料來產生報告和警示，讓您調查這些風險事件並採取適當的補救或緩和動作。
 
但是，Azure Active Directory Identity Protection 不只是監視和報告工具而已。Identity Protection 會根據風險事件，計算每位使用者的使用者風險層級，讓您設定以風險為基礎的原則來自動保護您組織的身分識別。除了 Azure Active Directory 與 EMS 所提供的其他條件式存取控制以外，這些以風險為基礎的原則可以自動封鎖或提供調適性補救動作，包括重設密碼以及強制 Multi-Factor Authentication。

####探索 Identity Protection 的功能 

偵測風險事件和有風險的帳戶：

- 使用機器學習服務和啟發式規則偵測 6 種風險事件類型 

- 計算使用者風險層級

- 提供自訂建議，藉由反白顯示弱點來改善整體安全性狀態

<br>

調查風險事件：

- 傳送風險事件的通知

- 使用相關和內容資訊來調查風險事件

- 提供基本工作流程來追蹤調查

- 讓您輕鬆存取補救動作，例如重設密碼

<br>

以風險為基礎的條件式存取原則：

- 此原則會藉由封鎖登入或要求 Multi-Factor Authentication 挑戰來緩和有風險的登入。

- 此原則會封鎖或保護有風險的使用者帳戶

- 此原則會要求使用者註冊以便進行 Multi-Factor Authentication


## 偵測和風險

### 風險事件

由 Identity Protection 標示為可疑的風險事件，表示身分識別可能已被入侵。如需風險事件的完整清單，請參閱 [Azure Active Directory Identity Protection 偵測到的風險事件類型](active-directory-identityprotection-risk-events-types.md)。

有些這些風險事件已可透過 Azure 管理入口網站中的 Azure AD 異常活動報告取得。下表列出各種風險事件類型和對應的 Azure AD 異常活動報告。Microsoft 將持續投入這個領域，並且計劃持續改善現有風險事件的偵測精確度，以及持續加入新的風險事件類型。



| Identity Protection 風險事件類型 | 對應的 Azure AD 異常活動報告 |
| :-- | :-- |
| 認證外洩 | 認證外洩的使用者 |
| 不可能到達非典型位置的移動 |	異常的登入活動 |
| 從受感染的裝置登入 | 從可能受感染的裝置登入 |
| 從匿名 IP 位址登入 | 從不明來源登入 |
| 從具有可疑活動的 IP 位址登入 |	從具有可疑活動的 IP 位址登入 |
| 從不熟悉的位置登入 | - | | 鎖定事件 (不在公開預覽中) | - |

下列 Azure AD 異常活動報告不會納入為 Azure AD Identity Protection 中的風險事件，因此不會透過 Identity Protection 提供。這些報告仍可在 Azure 管理入口網站中取得，不過將會在未來某個時候淘汰，因為它們正由 Identity Protection 中的風險事件所取代。

- 在多次失敗後登入
- 從多個地理區域登入

### 風險層級

風險事件的風險層級可表示風險事件的嚴重性 (高、中或低)。風險層級可協助 Identity Protection 使用者排定為了降低組織風險而必須採取之行動的優先順序。風險事件的嚴重性代表身分識別入侵 (結合它通常引發的雜訊) 預測的訊號強度。

- 高：高信賴度和高嚴重性風險事件。這些事件強烈指出使用者的身分識別已遭入侵，而且應該立即補救任何受影響的使用者帳戶。

- 中：高嚴重性，但信賴度較低的風險事件，或反之亦然。這些事件具有潛在風險，而且應該補救任何受影響的使用者帳戶。

- 低：低信賴度和低嚴重性風險事件。此事件可能不需要採取立即行動，但與其他風險事件結合時，可能強烈指出身分識別遭到入侵。


![風險層級](./media/active-directory-identityprotection/01.png "風險層級")

 

風險事件會以即時方式，或在風險事件發生後的後處理中 (離線) 識別。目前 Identity Protection 中的大部分風險事件均為離線計算，並且會在 2-4 小時內顯示於 Identity Protection。進行即時評估時，即時風險事件會在 5-10 分鐘內顯示於 Identity Protection 主控台。

目前有數個舊版用戶端不支援即時風險事件偵測與防護。因此，無法即時偵測或預防從這些用戶端登入。


## 調查
您通常會從 Identity Protection 儀表板開始使用 Identity Protection。

<br><br> ![補救](./media/active-directory-identityprotection/29.png "補救") <br>

儀表板可讓您存取：
 
- 
- 設定，例如 [安全性原則]、[通知] 和 [Multi-Factor Authentication 註冊] 的組態
 

這通常是調查的起點，而在調查過程中會檢閱風險事件相關活動、記錄檔和其他相關資訊，以決定是否需要採取補救或緩和步驟，了解身分識別如何遭到入侵，以及了解遭到入侵的身分識別如何被利用。

您可以將調查活動繫結至 Azure Active Directory Protection 傳送的每封電子郵件[通知](active-directory-identityprotection-notifications.md) 。

下列各節提供更多詳細資料以及調查的相關步驟。



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

在大部分情況下，您將採取補救動作 (例如重設安全的密碼) 來自動關閉風險事件。但是，這不一定都可行。<br>比方說，在下列情況下即是如此︰

- 已刪除具有作用中風險事件的使用者
- 調查顯示合法的使用者已執行報告的風險事件

因為 [作用中] 的風險事件會納入使用者風險計算，所以您可能必須以手動方式降低風險層級，但不要手動關閉風險事件。<br>在調查過程中，您可以選擇採取下列任何動作，以變更風險事件的狀態：

<br> ![動作](./media/active-directory-identityprotection/34.png "動作") <br>

- 解決 - 如果在調查風險事件之後，您在 Identity Protection 外部採取適當的補救動作，而且您認為應該將風險事件視為已關閉，請將事件標示為 [已解決]。解決的事件會將風險事件的狀態設定為 [已關閉]，而此風險事件便不再算是使用者風險。

- 標示為誤判 - 在某些情況下，您可能會調查某個風險事件並發現該事件被誤標為有風險。您可以將風險事件標示為誤判，以減少發生這種情況。這可協助機器學習演算法未來改善類似事件的分類。誤判事件的狀態為 [已關閉]，而且不再算是使用者風險。

- 忽略 - 如果您尚未採取任何補救動作，但希望風險事件從作用中清單中移除，您可忽略風險事件，且事件狀態將會是 [已關閉]。忽略的事件不算是使用者風險。這個選項只能用於不尋常的情況下。

- 重新啟用 - 可以重新啟用已手動關閉的風險事件 (藉由選擇 [解決]、[誤判] 或 [略過])，並將事件狀態設回 [作用中]。重新啟用的風險事件會納入使用者風險層級計算。無法重新啟用透過補救 (例如重設安全的密碼) 關閉的風險事件。

開啟相關的組態對話方塊：

1. 在 [Azure AD Identity Protection] 刀鋒視窗上，按一下 [標示為危險的使用者]。<br><br> ![手動重設密碼](./media/active-directory-identityprotection/408.png "手動重設密碼") <br>

2. 以滑鼠右鍵按一下受影響的使用者。 <br><br> ![手動重設密碼](./media/active-directory-identityprotection/437.png "手動重設密碼") <br>

## 補救使用者風險事件

補救就是用來保護先前疑似或已知遭到入侵的身分識別或裝置的動作。補救動作可讓身分識別或裝置還原到安全的狀態，以及解決先前與身分識別或裝置相關聯的風險事件。

若要補救使用者風險事件，您可以：

- 手動重設安全密碼，以補救使用者風險事件 

- 設定使用者風險安全性原則，以自動緩和或補救使用者風險事件

- 重新製作受感染裝置的映像


### 手動重設安全密碼

重設安全的密碼是許多風險事件的有效補救動作，一旦執行，就會自動關閉這些風險事件並重新計算使用者風險層級。您可以使用 Identity Protection 儀表板，為有風險的使用者起始密碼重設。

相關的對話方塊提供兩個不同的方法，可以將密碼重設為︰

重設密碼 - 如果使用者已註冊 Multi-Factor Authentication，選取 [要求使用者重設密碼] 可讓使用者自行復原。在使用者下次登入期間，使用者必須成功解決 Multi-Factor Authentication 挑戰，且被迫變更密碼。如果使用者帳戶尚未註冊 Multi-Factor Authentication，則無法使用此選項。

暫時密碼 - 選取 [產生暫時密碼]，立即讓現有的密碼失效，並且為使用者建立新的暫時密碼。將新的暫時密碼傳送到使用者的備用電子郵件地址，或傳送給使用者的經理。因為此密碼是暫時的，所以會提示使用者在登入時變更密碼。

<br> ![原則](./media/active-directory-identityprotection/71.png "原則") <br>

開啟相關的組態對話方塊：

1. 在 [Azure AD Identity Protection] 刀鋒視窗上，按一下 [標示為危險的使用者]。<br><br> ![手動重設密碼](./media/active-directory-identityprotection/408.png "手動重設密碼") <br>

2. 按一下受影響的使用者 <br><br> ![手動重設密碼](./media/active-directory-identityprotection/404.png "手動重設密碼") <br>

2. 按一下 [重設密碼] <br><br> ![手動重設密碼](./media/active-directory-identityprotection/420.png "手動重設密碼") <br>





## 使用者風險安全性原則

使用者風險安全性原則是條件式存取原則，可評估特定使用者的風險層級，並根據預先定義的條件和規則來套用補救和緩和動作。<br><br> ![使用者風險原則](./media/active-directory-identityprotection/500.png "使用者風險原則") <br>

Azure AD Identity Protection 可讓您執行下列作業，以協助您管理標示有風險的使用者的緩和與補救動作：

- 設定要套用原則的使用者和群組：<br><br> ![使用者風險原則](./media/active-directory-identityprotection/501.png "使用者風險原則") <br>

- 設定可觸發密碼變更的使用者風險層級臨界值 (低、中或高)：<br><br> ![使用者風險原則](./media/active-directory-identityprotection/502.png "使用者風險原則") <br>

- 設定可觸發封鎖使用者的使用者風險層級臨界值 (低、中或高)：<br><br> ![使用者風險原則](./media/active-directory-identityprotection/503.png "使用者風險原則") <br>

- 切換原則的狀態：<br><br> ![MFA 註冊](./media/active-directory-identityprotection/403.png "MFA 註冊") <br>

- 在啟用變更前檢閱和評估其影響：<br><br> ![使用者風險原則](./media/active-directory-identityprotection/504.png "使用者風險原則") <br>


選擇 [高] 臨界值可減少觸發原則的次數，並將對使用者的影響降至最低。不過，這會從原則中排除標示 [低] 和 [中] 度風險的使用者，而無法保護先前疑似或已知遭到入侵的身分識別或裝置。

設定原則時，

- 排除可能會產生大量誤判的使用者 (開發人員、安全性分析人員)

- 在啟用原則並不實用 (例如無法存取技術服務人員) 的地區設定中排除使用者

- 在原則推出初期，或如果您必須盡量減少使用者所看到的挑戰，請使用 [高] 臨界值。

- 如果您的組織需要更高的安全性，請使用 [低] 臨界值。選取 [低] 臨界值會帶來額外的使用者登入挑戰，並提高安全性。

大部分組織的建議預設值是設定 [中] 臨界值的規則，以取得可用性與安全性之間的平衡。

如需相關的使用者經驗概觀，請參閱︰

- [遭到入侵的帳戶復原流程](active-directory-identityprotection-flows.md#compromised-account-recovery)。  

- [遭到入侵的帳戶封鎖流程](active-directory-identityprotection-flows.md#compromised-account-blocked)。


開啟相關的組態對話方塊：

1. 在 [Azure AD Identity Protection] 刀鋒視窗上，按一下 [設定]。<br><br> ![使用者風險原則](./media/active-directory-identityprotection/401.png "使用者風險原則") <br>

2. 在 [安全性原則] 區段中，按一下 [使用者風險]。<br><br> ![使用者風險原則](./media/active-directory-identityprotection/500.png "使用者風險原則") <br>






## 緩和使用者風險事件
系統管理員可以設定使用者風險安全性原則，以根據風險層級防止使用者登入。

封鎖登入：
 
- 避免對受影響的使用者產生新的使用者風險事件

- 可讓系統管理員手動地補救會影響使用者身分識別的風險事件，並將它還原到安全的狀態



## 登入風險層級為何？

登入風險層級指出特定登入 (其他人正嘗試使用使用者的身分識別進行驗證) 的可能性 (高、中或低)。登入風險層級是在登入時間評估，並即時針對該特定登入考量偵測到的風險事件和標記。

## 緩和登入風險事件 
緩和動作可限制攻擊者利用遭到入侵的身分識別或裝置的能力，但不需將身分識別或裝置還原至安全的狀態。緩和並未解決先前與身分識別或裝置相關聯的登入風險事件。

您可以在 Azure AD Identity Protection 中使用條件式存取，以自動緩和登入風險事件。使用這些原則時，請考慮使用者或登入的風險層級，以封鎖有風險的登入或要求使用者執行 Multi-Factor Authentication。這些動作可防止攻擊者利用遭竊的身分識別而造成損害，而且會讓您有一些時間可保護身分識別。


## 登入風險安全性原則

登入風險原則是條件式存取原則，可評估特定登入的風險，並根據預先定義的條件和規則來套用緩和動作。<br><br> ![登入風險原則](./media/active-directory-identityprotection/700.png "登入風險原則") <br>

Azure AD Identity Protection 可讓您執行下列作業，以協助您管理有風險登入的緩和動作：

- 設定要套用原則的使用者和群組：<br><br> ![登入風險原則](./media/active-directory-identityprotection/701.png "登入風險原則") <br>

- 設定可針對受影響的登入觸發 Multi-Factor Authentication 挑戰的登入風險層級臨界值 (低、中或高)：<br><br> ![登入風險原則](./media/active-directory-identityprotection/702.png "登入風險原則") <br>

- 設定可封鎖受影響登入的登入風險層級臨界值 (低、中或高)：<br><br> ![登入風險原則](./media/active-directory-identityprotection/703.png "登入風險原則") <br>

- 切換原則的狀態：<br><br> ![MFA 註冊](./media/active-directory-identityprotection/403.png "MFA 註冊") <br>

- 在啟用變更前檢閱和評估其影響：<br><br> ![登入風險原則](./media/active-directory-identityprotection/704.png "登入風險原則") <br>

 
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

如需相關的使用者經驗概觀，請參閱︰

- [有風險的登入復原](active-directory-identityprotection-flows.md#risky-sign-in-recovery) 

- [已封鎖有風險的登入](active-directory-identityprotection-flows.md#risky-sign-in-blocked)

- [在有風險的登入期間註冊 Multi-Factor Authentication](active-directory-identityprotection-flows.md#multi-factor-authentication-registration-during-a-risky-sign-in)





開啟相關的組態對話方塊：

1. 在 [Azure AD Identity Protection] 刀鋒視窗上，按一下 [設定]。<br><br> ![MFA 註冊](./media/active-directory-identityprotection/401.png "MFA 註冊") <br>

1. 在 [安全性原則] 區段中，按一下 [登入風險]。<br><br> ![MFA 註冊](./media/active-directory-identityprotection/700.png "MFA 註冊") <br>




## Multi-Factor Authentication 註冊原則

Azure Multi-Factor Authentication 是除了使用使用者名稱與密碼之外，需要再利用其他方法驗證您的身份的驗證方法。它可以為使用者登入和交易提供第二層安全性。<br>建議您要求對使用者登入進行 Azure Multi-Factor Authentication，因為它：

- 提供增強式驗證與一系列簡單的驗證選項

- 扮演著舉足輕重的角色，可讓您的組織防護帳戶遭竊及從中復原

如需詳細資訊，請參閱[什麼是 Azure Multi-Factor Authentication？](../multi-factor-authentication/multi-factor-authentication.md)


Azure AD Identity Protection 可讓您設定原則來執行下列作業，以協助您管理首次 Multi-Factor Authentication 註冊：

- 檢視目前註冊狀態：<br><br> ![MFA 註冊](./media/active-directory-identityprotection/603.png "MFA 註冊") <br>

- 設定要套用原則的使用者和群組：<br><br> ![MFA 註冊](./media/active-directory-identityprotection/601.png "MFA 註冊") <br>

- 定義多久以後才能略過註冊：<br><br> ![MFA 註冊](./media/active-directory-identityprotection/602.png "MFA 註冊") <br>

- 切換原則的狀態：<br><br> ![MFA 註冊](./media/active-directory-identityprotection/403.png "MFA 註冊") <br>

如需相關的使用者經驗概觀，請參閱︰

- [Multi-Factor Authentication 註冊流程](active-directory-identityprotection-flows.md#multi-factor-authentication-registration)。  

- [在有風險的登入期間註冊 Multi-Factor Authentication](active-directory-identityprotection-flows.md#multi-factor-authentication-registration-during-a-risky-sign-in)。





開啟相關的組態對話方塊：

1. 在 [Azure AD Identity Protection] 刀鋒視窗上，按一下 [設定]。<br><br> ![MFA 註冊](./media/active-directory-identityprotection/401.png "MFA 註冊") <br>

2. 在 [Multi-Factor Authentication] 區段中，按一下 [註冊]。<br><br> ![MFA 註冊](./media/active-directory-identityprotection/402.png "MFA 註冊") <br>




## 另請參閱

 - [第 9 頻道：Azure AD 和身分識別展示：Identity Protection 預覽](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)
 - [Azure Active Directory Identity Protection 偵測到的風險事件類型](active-directory-identityprotection-risk-events-types.md)
 - [Azure Active Directory Identity Protection 偵測到的弱點](active-directory-identityprotection-vulnerabilities.md)
 - [Azure Active Directory Identity Protection 通知](active-directory-identityprotection-notifications.md)
 - [Azure Active Directory Identity Protection 流程](active-directory-identityprotection-flows.md)
 - [Azure Active Directory Identity Protection 腳本](active-directory-identityprotection-playbook.md)
 - [Azure Active Directory Identity Protection 詞彙](active-directory-identityprotection-glossary.md)

<!---HONumber=AcomDC_0316_2016-->