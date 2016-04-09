<properties
	pageTitle="Azure Active Directory Identity Protection 流程| Microsoft Azure"
	description="當 Identity Protection 已降低或補救使用者時，或是原則需要 Multi-Factor Authentication 時，請提供使用者經驗的概觀。"
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
	ms.date="03/10/2016"
	ms.author="markvi"/>

#Azure Active Directory Identity Protection 流程

透過 Azure Active Directory Identity Protection，您可以：

- 要求使用者進行註冊，以進行 Multi-Factor Authentication

- 處理高風險的登入和遭到入侵的使用者

系統對這些問題的回應會影響使用者的登入體驗，因為只藉由提供使用者名稱和密碼直接登入已不再可行。需要有其他步驟，才能讓使用者安全返回工作。

本主題會針對可能發生的所有案例，為您提供使用者的登入體驗概觀。

**Multi-Factor Authentication**

- Multi-Factor Authentication 註冊



**有風險的登入**

- 有風險的登入復原

- 已封鎖有風險的登入

- 在有風險的登入期間註冊 Multi-Factor Authentication
 

**有風險的使用者**

- 遭到入侵的帳戶復原

- 已封鎖遭到入侵的帳戶




## Multi-Factor Authentication 註冊

在遭到入侵的帳戶復原流程和有風險的登入流程中，最佳的使用者體驗皆是使用者可以自行復原。如果使用者已註冊 Multi-Factor Authentication，他們便有與其帳戶相關聯的電話號碼可用來通過安全性挑戰。從帳戶入侵復原時，不需要技術服務人員或系統管理員介入。因此，強烈建議您讓使用者註冊 Multi-Factor Authentication。

系統管理員可以：

- 設定一個原則，要求使用者設定其帳戶進行其他安全性驗證。 
- 允許最多略過 Multi-Factor Authentication 註冊 30 天 (如果他們想給予使用者註冊前的寬限期)。

**Multi-Factor Authentication 註冊具有三個步驟：**

1. 在第一個步驟中，使用者會得到設定此帳戶進行 Multi-Factor Authentication 之需求的相關通知。<br><br> ![補救](./media/active-directory-identityprotection-flows/140.png "補救") <br>


2. 若要設定 Multi-Factor Authentication，您需要讓系統知道您要連線的方式。<br><br> ![補救](./media/active-directory-identityprotection-flows/141.png "補救") <br>
 
3. 系統會送出一項挑戰給您，而您需要回應。<br><br> ![補救](./media/active-directory-identityprotection-flows/142.png "補救") <br>

 



## 有風險的登入復原

當系統管理員設定登入風險的原則後，受影響的使用者會在嘗試登入時收到通知。

**有風險的登入流程有兩個步驟：**

1. 使用者獲知偵測到不尋常的登入，例如從新的位置、裝置或 app 登入。<br><br> ![補救](./media/active-directory-identityprotection-flows/120.png "補救") <br>

2. 使用者必須解決安全性挑戰以證明其身分識別。如果使用者已註冊 Multi-Factor Authentication，他們必須回傳送至其電話號碼的安全碼。由於這只是有風險的登入，並不是遭到入侵的帳戶，所以使用者不必在此流程中變更密碼。 <br><br> ![補救](./media/active-directory-identityprotection-flows/121.png "補救") <br>



 
## 已封鎖有風險的登入
系統管理員也可以選擇設定登入風險原則，以根據風險層級防止使用者登入。若要解除封鎖，使用者必須連絡系統管理員或技術服務人員，或者嘗試從熟悉的位置或裝置登入。藉由解決 Multi-Factor Authentication 自行復原，不是此種情況的適用選項。<br><br> ![補救](./media/active-directory-identityprotection-flows/200.png "補救") <br>



## 在有風險的登入期間註冊 Multi-Factor Authentication

使用者務必註冊 Multi-Factor Authentication，以便他們做好準備並能夠通過安全性挑戰。如果使用者未註冊 Multi-Factor Authentication，但原則要求他們這麼做，則可能在有風險的登入期間要求他們進行註冊。這表示，攻擊者最後還是會被要求新增電話號碼，而不是成為好使用者。

若要避免這種情況，請要求使用者盡快註冊 Multi-Factor Authentication，如此才能在遭到入侵時擁有與其帳戶相關聯的電話號碼。或者，系統管理員可以完全封鎖遭到入侵且未註冊 Multi-Factor Authentication 的使用者。

**在有風險的登入期間註冊 Multi-Factor Authentication 有兩個步驟：**

1. 使用者獲知帳戶有風險。<br><br> ![補救](./media/active-directory-identityprotection-flows/150.png "補救") <br>

2. 初始 Multi-Factor Authentication 註冊程序。<br><br> ![補救](./media/active-directory-identityprotection-flows/151.png "補救") <br>

如需後續步驟，請參閱 [Multi-Factor Authentication 註冊](#multi-factor-authentication-registration)




## 遭到入侵的帳戶復原

設定使用者風險安全性原則之後，符合原則中指定的使用者風險層級 (因而假定遭到入侵) 的使用者，必須先經歷使用者入侵復原流程，才可以登入。

**使用者入侵復原流程有三個步驟：**

1. 使用者獲知其帳戶安全性因為可疑活動或認證外洩而有風險。

<br> ![補救](./media/active-directory-identityprotection-flows/101.png "補救") <br>

2.	使用者必須解決安全性挑戰以證明其身分識別。如果使用者已註冊 Multi-Factor Authentication，他們可以從損害中自行復原。他們必須回傳送至其電話號碼的安全碼。 

<br> ![補救](./media/active-directory-identityprotection-flows/110.png "補救") <br>


3.	最後，使用者會被迫變更其密碼，因為其他人可能有其帳戶的存取權。這項體驗的螢幕擷取畫面如下。
 
<br> ![補救](./media/active-directory-identityprotection-flows/111.png "補救") <br>



## 已封鎖遭到入侵的帳戶 

若要讓遭到使用者風險安全性原則封鎖的使用者解除封鎖，該使用者必須連絡系統管理員或技術服務人員。藉由解決 Multi-Factor Authentication 自行復原，不是此種情況的適用選項。

<br> ![補救](./media/active-directory-identityprotection-flows/104.png "補救") <br>



 
## 重設密碼

如果遭到入侵的使用者已遭封鎖而無法登入，系統管理員可以為其產生暫時密碼。使用者必須在下次登入期間變更密碼。

<br> ![補救](./media/active-directory-identityprotection-flows/160.png "補救") <br>


 




 

## 另請參閱

- [Azure Active Directory Identity Protection](active-directory-identityprotection.md) 

<!---HONumber=AcomDC_0316_2016-->