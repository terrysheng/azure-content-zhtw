
<properties 
    pageTitle="保護 Azure RemoteApp 的存取，且後續將會推出更多功能 | Microsoft Azure"
	description="了解如何透過使用 Azure Active Directory 中的條件式存取來安全存取 Azure RemoteApp"
	services="remoteapp"
	documentationCenter="" 
	authors="piotrci" 
	manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="01/12/2016" 
    ms.author="elizapo" />

# 保護 Azure RemoteApp 的存取，且後續將會推出更多功能

本文將概述系統管理員要如何設定安全存取通道，讓這條通道起自使用者、經過 Azure RemoteApp，並終止於 SQL 資料庫或另一個應用程式的後端等安全資源。其目標是要確定只有符合所需條件的授權使用者可以存取遠端應用程式，而且只能從受控制的 Azure RemoteApp 環境存取安全的後端，而無法從其他位置進行存取。

系統管理員需要注意下列 3 個重點：

![Azure RemoteApp 條件式存取的考量](./media/remoteapp-secureaccess/ra-conditionalenvironment.png)

請繼續閱讀後續內容以了解相關資訊和上述問題的解答。

## 誰可以存取集合？
系統管理員必須選擇可存取集合中的遠端應用程式的使用者。您可以使用 Azure Active Directory (Azure AD) 的工作或學校帳戶 (先前稱為「組織帳戶」) 或 Microsoft 帳戶 (例如 @outlook.com)。大多數企業案例使用 Azure AD 帳戶，此帳戶可讓您使用稍後會討論到的條件式存取功能，而且也是已加入網域之集合唯一能選擇的帳戶。本文其餘部分假設您使用 Azure AD 帳戶和 Azure RemoteApp。

**我們已完成的工作：**

使用 Azure AD 帳戶來控制 Azure RemoteApp 的存取有兩個好處：

1.	我們永遠會知道誰可以存取已發行的應用程式以及這些應用程式所連接到的後端。
2.	我們能控制基礎的 Azure AD，因此可以建立和刪除使用者帳戶、設定密碼原則，以及使用 Multi-Factor Authentication 等等。 

## 集合的存取方式為何？ 是從哪裡進行存取的？
系統管理員通常會想要定義原則，來規範公用網際網路面向環境 (例如 Azure RemoteApp) 的存取。比方說，他們想要確定從公司網路外部存取環境的使用者必須使用 Multi-Factor Authentication (MFA) 才能取得存取權，或者，應該全都遭到封鎖。

Azure RemoteApp 的系統管理員可以使用 Azure AD Premium 所提供的功能來設定其 Azure RemoteApp 環境的條件式存取原則。他們也可以使用豐富的報告和警示功能來監控環境受到存取的情形。

### 如何設定 Azure RemoteApp 的條件式存取
我們要逐步解說一個範例案例，在此案例中，Azure RemoteApp 的系統管理員想要封鎖從公司網路外部存取環境的使用者。

>[AZURE.NOTE]我們假設您已將 Azure AD 升級為「高階層」，並且您已建立至少一個 Azure RemoteApp 集合。

1.	在 Azure 入口網站中，按一下 [Active Directory] 索引標籤。然後，按一下您想要設定的目錄。

	請記住：條件式存取是目錄的屬性，而非 Azure RemoteApp 的屬性，因此所有組態都是在目錄層級進行設定。這也表示您必須是目錄的系統管理員才能進行這些變更。

2.	按一下 [應用程式]，然後按一下 [Microsoft Azure RemoteApp] 來設定條件式存取。請注意，您可以個別為目錄中的每個「軟體即服務」應用程式設定條件式存取。![設定 Azure RemoteApp 的條件式存取](./media/remoteapp-secureaccess/ra-conditionalaccessscreen.png)
 

3.	在 [設定] 索引標籤上，將 [啟用存取規則] 設為 [開啟]。![啟用 Azure RemoteApp 的存取規則](./media/remoteapp-secureaccess/ra-enableaccessrules.png)
 

4.	現在您可以設定不同的規則，並選擇要套用規則的人員：

	1. 選擇 [不工作時封鎖存取] 以完全防止使用者從您指定的網路環境外部存取 Azure RemoteApp。
	2. 按一下下面的選項可定義構成「受信任網路」的 IP 位址範圍。此範圍以外的所有位址將會遭到拒絕。

5.	從您指定的範圍之外的 IP 位址啟動 Azure RemoteApp 用戶端，來測試您的組態。在使用 Azure AD 認證登入之後，您應該會看到如下的訊息：

![拒絕 Azure RemoteApp 的存取](./media/remoteapp-secureaccess/ra-accessdenied.png)
 

### 未來的條件式存取功能 
Azure Active Directory 小組正著手開發條件式存取的新功能。系統管理員日後將可以建立網路位置型規則以外的新類型規則。我們應該很快就會推出新功能的公開預覽。

### 如何監視 Azure RemoteApp 的存取
Azure Active Directory Premium 報告功能是能搭配條件式存取來使用的絕佳功能。您可以使用報告來監視正在存取環境的人員，以及偵測任何可疑的活動。

比方說，您可以查看存取 Azure RemoteApp 之使用者的名稱、存取次數和存取時間。

1.	在 Azure 入口網站中，按一下 [Active Directory]，然後按一下您的目錄。

2.	移至 [報告] 索引標籤。

3.	從報告清單中選取 [整合式應用程式] 底下的 [應用程式使用情況]。

	您會看到 Azure RemoteApp 的一些彙總統計資料。![彙總的 Azure RemoteApp 存取統計資料](./media/remoteapp-secureaccess/ra-accessstats.png)
 
5.	按一下應用程式以顯示存取 Azure RemoteApp 之使用者的相關資訊。![Azure RemoteApp 的使用者存取統計資料](./media/remoteapp-secureaccess/ra-userstats.png)
 
### 摘要
使用 Azure Active Directory Premium，您就可以設定 Azure RemoteApp (以及其他可透過 Azure AD 取得的軟體即服務應用程式) 的存取規則。目前規則只有網路位置型原則，但未來將會延伸到其他企業管理層面。

Azure AD Premium 也提供相關報告和監視功能，可進一步延伸系統管理員對其 Azure RemoteApp 環境的控制能力。

## 如何確定我的安全資源只能從 Azure RemoteApp 環境進行存取？
本文前幾節著重在保護 Azure RemoteApp 環境的存取。透過選擇允許存取的使用者並設定存取規則以進一步控制使用者使用服務的方式，我們已經完成這個部分。

Azure RemoteApp 部署的常見案例是遠端應用程式需要與後端資源 (例如 SQL 資料庫) 進行通訊。此資源裝載在內部部署環境中 (例如公司網路) 或雲端中 (例如 Azure IaaS)。系統管理員通常會想要確定，只有透過 Azure RemoteApp 部署的應用程式能夠存取後端資源，但 (舉例來說) 直接在使用者電腦上執行並透過公用網際網路存取的應用程式則不行。我們經常會將 Azure RemoteApp 視為受到集中管理的安全環境，且使用者只應該透過此途徑來與後端資源互動。

解決方法是將 Azure RemoteApp 環境和安全資源放在相同的 Azure 虛擬網路 (VNET) 中。如果資源位於不同網站，您可以建立站對站 VPN 連線，以便 (舉例來說) 建立一個橫跨 Azure 資料中心與客戶內部部署環境的 VNET。

Azure RemoteApp 支援兩種集合部署類型，您可以在其中提供您自己的 VNET：

-	未加入網域：應用程式會有 VNET 中其他資源的「視線」。比方說，這可以用來將應用程式連接至使用 SQL 驗證的 SQL 資料庫 (應用程式直接對資料庫驗證使用者)

-	已加入網域：Azure RemoteApp 所使用的虛擬機器加入至 VNET 中的網域控制站。當應用程式需要對 Windows 網域控制站進行驗證以取得後端資源的存取權時，這非常有用。![Azure RemoteApp 中已加入網域的集合](./media/remoteapp-secureaccess/ra-domainjoined.png)
 
### 如何在 Azure 和內部部署環境之間建立安全連線
有數個組態選項可供用來連接 Azure 和內部部署環境。這裡有提供很好的選項概觀。

在使用 Azure RemoteApp 時，您必須先設定 VNet，然後在集合的建立程序期間使用。

## 完整解決方案
下圖顯示完整的解決方案，我們在其中建置了起自使用者、經過 Azure RemoteApp (ARA)，並於最後進入後端資源的安全存取通道。![保護 Azure RemoteApp](./media/remoteapp-secureaccess/ra-secureoverview.png)在階段 1 中，我們已選取使用者，並建立了控制 ARA 存取方式的存取規則。在下面的範例中，我們只允許從公司網路進行工作的使用者進行存取。不符合此規定的使用者將完全無法存取 ARA 環境。在「階段 2」中，我們公開了後端資源，但只能透過我們所控制的 VNet/VPN 組態來存取。Azure RemoteApp 已放置於相同的 VNet。最終結果是只能透過 ARA 環境存取資源。

<!---HONumber=AcomDC_0121_2016-->