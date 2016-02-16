<properties
   pageTitle="管理及回應 Azure 資訊安全中心的安全性警示 | Microsoft Azure"
   description="本文件可協助您使用「Azure 資訊安全中心」功能來管理及回應安全性警示。"
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.topic="hero-article" 
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/09/2016"
   ms.author="yurid"/>

# 管理及回應 Azure 資訊安全中心的安全性警示
本文件可協助您使用 Azure 資訊安全中心的功能，來管理及回應安全性警示。

> [AZURE.NOTE] 本文件中的資訊適用於「Azure 資訊安全中心」的預覽版本。

## 什麼是 Azure 資訊安全中心？
 資訊安全中心利用加強對您 Azure 資源的能見度及安全性控制權，來協助您預防、偵測及回應威脅。它提供您訂用帳戶之間的整合式安全性監視和原則管理，協助您偵測可能會忽略的威脅，且適用於廣泛的安全性解決方案生態系統。

## 什麼是安全性警示：
「資訊安全中心」會自動收集、分析及整合 Azure 資源、網路，以及反惡意程式碼和防火牆的記錄檔資料，以偵測真正的威脅並降低誤判。「資訊安全中心」會顯示包括來自整合式合作夥伴解決方案的優先安全性警示，以及需要您快速調查的資訊，和如何修復受攻擊的建議。Microsoft 安全性研究人員持續不斷地分析全球新興的威脅，包括新的攻擊模式以及在消費者、企業產品及線上服務中發現的趨勢。如此一來，「資訊安全中心」能夠針對新的漏洞和入侵程式更新其偵測演算法，協助客戶防範各種威脅。「資訊安全中心」可以偵測的部分威脅類型範例如下：

- **暴力偵測網路資料**：使用熟知您應用程式傳統網路流量模式的機器學習模型，對非法使用者 (而非合法使用者) 執行的存取嘗試進行更有效率的偵測。
- **暴力偵測對端點資料**：以機器記錄分析為依據，區隔出失敗及成功的嘗試。
- **與惡意 IP 通訊的 VM**：將網路流量與 Microsoft 全球威脅智慧比較，找出遭到入侵、且與通訊操控伺服器 (C&C) 的電腦通訊的機器，反之亦然。
- **遭到入侵的 VM**：以機器記錄及與其他訊號相關的行為分析為依據，找出可能造成機器遭到入侵及利用的異常活動。

## 管理安全性警示 

您可以查看 [安全性警示] 磚來檢視目前的警示。請遵循下列步驟來查看有關每個警示的更多詳細資訊：

1. 您會在「資訊安全中心」的儀表板看到 [安全性警示] 磚。

    ![Azure 資訊安全中心的 [安全性警示] 磚](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig1-new.png)

2.  按一下磚，開啟 [安全性警示] 刀鋒視窗，其中包括與警示相關的詳細資料，如下所示。

    ![Azure 資訊安全中心的 [安全性警示] 刀鋒視窗](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig2-new.png)

這個刀鋒視窗的底部會顯示每個警示的詳細資料。如要為警示排序，請按一下您要做為排序依據的的資料行。下列為每個資料行的定義：

- **警示**：有關警示的簡短說明。
- **計數**：在特定一天所偵測到的特定類型警示總數。
- **偵測者**：負責觸發警示的服務。
- **日期**：事件發生的日期。
- **狀態**：警示目前的狀態。狀態有三種類型：
    - **活動中**：已偵測到的安全性警示。
    - **已解除**：使用者已經解除該安全性警示。這個狀態通常用於已經過調查但事件影響已減輕時，或是在調查後發現該事件並非真正攻擊時。

- **嚴重性**：嚴重性層級，分為高、中或低。

您可以根據日期、狀態及嚴重性來篩選警示。如果您需要縮小顯示的安全性警示檢視範圍，篩選警示會相當有用。例如，您可能想確認在過去 24 小時發生的安全性警示，因為您正在調查系統中可能的入侵行動。

1. 按一下 [安全性警示] 刀鋒視窗上的 [篩選]。即會開啟 [篩選] 刀鋒視窗，您可以選取想要查看的日期、狀態和嚴重值。

	![在 Azure 資訊安全中心篩選警示](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig5-new.png)

2. 	查閱安全性警示之後，您會可能會發現可以忽略的警示，因為這是對環境的誤判，或指出特定資源預期會有的行為。無論是哪種情況，如果您判斷安全性警示不適用，您可解除該警示，然後將它從您的檢視中篩除。解除安全性警示的方式有兩種。在警示上按一下滑鼠右鍵，然後選取 [解除]，或將滑鼠游標暫留在項目上，然後按一下出現在右邊的三個點，再選取 [解除]。您可以按一下 [篩選]，然後選取 [已解除]，來檢視已解除的安全性警示。

	![在 Azure 資訊安全中心篩選警示](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig6-new.png)

### 回應安全性警示
選取一個安全性警示以深入了解觸發警示的事件；如果發現項目，您需要進行一些步驟來阻止攻擊。安全性警示會依類型及日期區分。按一下安全性警示會開啟刀鋒視窗，其中包括已分組的警示清單。

![對於 Azure 資訊安全中心的安全性警示的回應](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig7.png)

在此案例中，所觸發的警示是關於可疑的遠端桌面通訊協定 (RDP) 活動。第一個資料行顯示哪些資源遭到攻擊；第二個資料行顯示偵測到該攻擊的時間；第三個資料行顯示警示的狀態；第四個資料行顯示攻擊的嚴重性。當您檢閱這項資訊後，請按一下遭到攻擊的資源。此時新的刀鋒視窗會開啟，提供您更多接下來該如何處理的建議，如下列範例所示。

![對於如何處理 Azure 資訊安全中心的安全性警示的建議](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig8.png)

> [AZURE.NOTE] 資訊安全中心會根據安全性警示，建議您不同的補救方法。在某些情況下，您可能必須使用其他的 Azure 功能來實作建議的補救方法。例如，這個攻擊的補救方法是使用[網路 ACL](virtual-networks-acl.md) 或[網路安全性群組](virtual-networks-nsg.md)規則，把產生此攻擊的 IP 位址列入黑名單。


## 後續步驟
在本文件中，您了解到如何在資訊安全中心設定安全性原則。如要深入了解資訊安全中心，請參閱下列主題：

- [Azure 資訊安全中心的安全性健康情況監視](security-center-monitoring.md) -- 了解如何監視 Azure 資源的健康情況。
- [Azure 資訊安全中心常見問題集](security-center-faq.md) -- 尋找在使用服務時經常會遇到的問題。
- [Azure 安全性部落格](http://blogs.msdn.com/b/azuresecurity/)：尋找有關 Azure 安全性與相容性的部落格文章。

<!---HONumber=AcomDC_0211_2016-->