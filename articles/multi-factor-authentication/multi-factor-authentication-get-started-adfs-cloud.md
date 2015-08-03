<properties 
	pageTitle="使用 Azure Multi-Factor Authentication 與 AD FS 保護雲端資源" 
	description="這是說明如何在雲端開始使用 Azure MFA 和 AD FS 的 Azure Multi-Factor Authentication 頁面。" 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="terrylan" 
	editor="bryanla"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/02/2015" 
	ms.author="billmath"/>

# 使用 Azure Multi-Factor Authentication 與 AD FS 保護雲端資源

如果您的組織與 Azure Active Directory 結盟，而且您有 Azure AD 所存取的資源，您可以使用 Azure Multi-factor Authentication 或 Active Directory Federation Services 來保護這些資源。使用下列程序可利用 Azure Multi-Factor Authentication 或 Active Directory Federation Services 來保護 Azure Active Directory 資源。

## 若要使用 AD FS 保護 Azure AD 資源，請執行下列作業： 



1. 使用[開啟 Multi-Factor Authentication](multi-factor-authentication-get-started-cloud/#turn-on-multi-factor-authentication-for-users) 中概述的步驟，讓使用者啟用帳戶。
2. 請使用下列程序來設定宣告規則：

![雲端](./media/multi-factor-authentication-get-started-adfs-cloud/adfs1.png)

- 	啟動 AD FS 管理主控台。
- 	瀏覽至 [信賴憑證者信任]，並以滑鼠右鍵按一下 [信賴憑證者信任]。選取 [編輯宣告規則...]
- 	按一下 [新增規則…]
- 	從下拉式清單中，選取 [使用自訂規則傳送宣告] 並按 [下一步]。
- 	輸入宣告規則的名稱。
- 	在自訂規則之下：新增下列各項：


		=> issue(Type = "http://schemas.microsoft.com/claims/authnmethodsreferences", Value = "http://schemas.microsoft.com/claims/multipleauthn");

	對應宣告：

		<saml:Attribute AttributeName="authnmethodsreferences" AttributeNamespace="http://schemas.microsoft.com/claims">
		<saml:AttributeValue>http://schemas.microsoft.com/claims/multipleauthn</saml:AttributeValue>
		</saml:Attribute>
- 按一下 [確定]。按一下 [完成] (Finish)。關閉 AD FS 管理主控台。

使用者便可以使用內部部署方法 (例如智慧卡) 完成登入。

## 同盟使用者的可信任 IP
信任的 IP 可讓系統管理員針對特定的 IP 位址，或針對從他們自己的內部網路發出要求的同盟使用者，略過多因素驗證。下列各節說明當要求是來自同盟使用者的內部網路時，如何設定同盟使用者的 Azure Multi-Factor Authentication 信任的 IP，以及略過多因素驗證。這是藉由設定 AD FS 使用「通過或篩選傳入宣告」範本與「位於公司網路之內」宣告類別來達成。此範例使用 Office 365 做為信賴憑證者信任。

### 設定 AD FS 宣告規則

我們要做的第一件事是設定 AD FS 宣告。我們將建立兩個宣告規則，一個用於「位於公司網路之內」宣告類型，另一個用於保持使用者登入。<ol>

<li>開啟 [AD FS 管理]。</li>
<li>在左側選取 [信賴憑證者信任]。</li>
<li>在中間，以滑鼠右鍵按一下 [Microsoft Office 365 識別身分平台]，然後選取 [**編輯宣告規則...**]。</li>

![雲端](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip1.png)

<li>在 [發佈轉換規則] 上按一下 [**新增規則**]。</li>

![雲端](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip2.png)

<li>在 [新增轉換宣告規則精靈] 上，從下拉式清單選取 [通過或篩選傳入宣告]，然後按 [下一步]。</li>

![雲端](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip3.png)

<li>在 [宣告規則名稱] 旁邊的方塊中，命名您的規則。例如：InsideCorpNet。</li>
<li>從 [連入宣告類型] 旁邊的下拉式清單中，選取 [位於公司網路之內]。</li>

![雲端](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip4.png)

<li>按一下 [完成] (Finish)。</li>
<li>在 [發佈轉換規則] 上，按一下 [**新增規則**]。</li>
<li>在 [新增轉換宣告規則精靈] 上，從下拉式清單選取 [使用自訂規則傳送宣告]，然後按 [下一步]。</li>
<li>在 [宣告規則名稱] 下的方塊中：輸入「保持使用者登入」。</li>
<li>在 [自訂規則] 方塊中輸入：c:[Type == "http://schemas.microsoft.com/2014/03/psso"] => issue(claim = c);
</li>

![雲端](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip5.png)

<li>按一下 [**完成**]。</li>
<li>按一下 [**套用**]。</li>
<li>按一下 [**確定**]。</li>

<li>關閉 [AD FS 管理]。</li>

### 搭配同盟使用者設定 Azure Multi-Factor Authentication 信任的 IP
既然已經有宣告，我們可以開始設定信任的 IP。<ol>

<li>登入 Azure 管理入口網站。</li>
<li>在左側按一下 [Active Directory]。</li>
<li>在 [目錄] 下，按一下要設定信任的 IP 的目錄。</li>
<li>在您選取的目錄上，按一下 [設定]。</li>
<li>在 Multi-Factor Authentication 區段中，按一下 [管理服務設定]。</li>
<li>在 [服務設定] 頁面的 [信任的 IP] 下，選取 [**適用於從我的內部網路產生的同盟使用者提出的要求**]</li>

![雲端](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip6.png)

<li>按一下 [儲存]。</li>
<li>套用更新之後，請按一下 [關閉]。</li>

這樣就大功告成了！ 現在，當宣告來自公司內部網路之外時，Office 365 使用者同盟只需要使用 MFA。

<!---HONumber=July15_HO4-->