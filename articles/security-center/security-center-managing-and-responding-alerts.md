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
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/02/2016"
   ms.author="yurid"/>

# 管理及回應 Azure 資訊安全中心的安全性警示
本文件可協助您使用 Azure 資訊安全中心的功能，來管理及回應安全性警示。

> [AZURE.NOTE] 本文件中的資訊適用於「Azure 資訊安全中心」的預覽版本。

## 什麼是 Azure 資訊安全中心？
 資訊安全中心利用加強對您 Azure 資源的能見度及安全性控制權，來協助您預防、偵測及回應威脅。它提供您訂用帳戶之間的整合式安全性監視和原則管理，協助您偵測可能會忽略的威脅，且適用於廣泛的安全性解決方案生態系統。

## 什麼是安全性警示：
 資訊安全中心會自動收集、分析及整合您 Azure 資源、網路，及合作夥伴解決方案 (例如反惡意程式碼程式和防火牆等) 的記錄檔資料，來偵測真正的威脅，以及起減少誤判的機會。它還會顯示已排定優先順序的警示清單，來做為安全性警示。

您可以查看 [安全性警示] 磚來檢視目前的警示。請遵循下列步驟來查看有關每個警示的更多詳細資訊：

1. 您會在從資訊安全中心的儀表板看到 [安全性警示] 磚。

    ![Azure 資訊安全中心的 [安全性警示] 磚](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig1.png)

2.  按一下安全性警示發生次數圖示。[安全性警示] 刀鋒視窗會隨即開啟，顯示更多警示的詳細資料，如下所示。

    ![Azure 資訊安全中心的 [安全性警示] 刀鋒視窗](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig2.png)

這個刀鋒視窗的底部會顯示每個警示的詳細資料。如要為警示排序，請按一下您要做為排序依據的的資料行。下列為每個資料行的定義：

- **警示**：有關警示的簡短說明。
- **計數**：在特定一天所偵測到的特定類型警示總數。
- **偵測者**：負責觸發警示的服務。
- **日期**：事件發生的日期。
- **狀態**：警示目前的狀態。狀態有三種類型：
    - **使用中**：已偵測到安全性警示。
    - **已解除**：使用者已經解除該安全性警示。這個狀態通常用於已經過調查但事件影響已減輕時，或是在調查後發現該事件並非真正攻擊時。

- **嚴重性**：嚴重性層級，分為高、中或低。

### 回應安全性警示
代表您的組織可能遭到攻擊的活動有許多種。例如，執行合法網路擷取的網路系統管理員可能與發動某種攻擊的人看起來類似。在其他案例中，設定不良的系統可能會導致入侵偵測系統發生誤判，因此更難找出真正的攻擊事件。您在使用資訊安全中心檢閱安全性警示之後，就能開始根據警示的嚴重性採取動作。

如要採取動作，請選取您想要回應的警示，此時右側會開啟新的刀鋒視窗，其中有更多的詳細資料，如下所示：

![對於 Azure 資訊安全中心的安全性警示的回應](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig3.png)

在此案例中，所觸發的警示是關於可疑的遠端桌面通訊協定 (RDP) 活動。第一個資料行顯示哪些資源遭到攻擊；第二個資料行顯示偵測到該攻擊的時間；第三個資料行顯示警示的狀態；第四個資料行顯示攻擊的嚴重性。當您檢閱這項資訊後，請按一下遭到攻擊的資源。此時新的刀鋒視窗會開啟，提供您更多接下來該如何處理的建議，如下列範例所示。

![對於如何處理 Azure 資訊安全中心的安全性警示的建議](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig4.png)

> [AZURE.NOTE] 資訊安全中心會根據安全性警示，建議您不同的補救方法。在某些情況下，您可能必須使用其他的 Azure 功能來實作建議的補救方法。例如，這個攻擊的補救方法是使用[網路 ACL](virtual-networks-acl.md) 或[網路安全性群組](virtual-networks-nsg.md)規則，把產生此攻擊的 IP 位址列入黑名單。

### 管理安全性警示
您可以根據日期、狀態及嚴重性來篩選警示。請按一下 [安全性警示] 刀鋒視窗上的 [篩選]，然後啟用您想要的選項，如下所示：

![在 Azure 資訊安全中心篩選警示](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig5.png)

如果您需要縮小在儀表板中的檢視範圍，篩選警示會相當有用。例如，您可能想確認在過去 24 小時發生的安全性警示，因為您正在調查系統中可能的入侵行動。

在大多數的情況下，您應該要套用安全性警示中的建議。但在某些情況下，您可能需要忽略警示，因為這是對您環境的誤判，或指出特定資源預期會有的行為。不論哪一種情況，都可以使用 [解除] 選項來隱藏特定資源的建議。

如要解除某項工作，只要滑鼠右鍵按一下該工作，就能看到類似下列圖片的 [解除] 選項。

![Azure 資訊安全中心的 [解除] 選項](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig6.png)

在大量的警示中，每個警示都有非常明確的描述及補救方法。您在資訊安全中心所看到的警示基礎是攻擊情況。有關下列攻擊案例的警示，是由 Microsoft 引擎所觸發的：

- **透過網路資料的暴力密碼破解偵測方式**：以透過網路流量資料來學習的機器學習模型為基礎。
- **透過端點資料的暴力密碼破解偵測方式**：以電腦記錄檔的資訊安全中心查詢為基礎；可區分失敗及成功的攻擊嘗試。
- **與惡意 IP 通訊的 VM**：以資訊安全中心對於遭到 Bot 入侵，且在與其命令與控制 (C&C) 伺服器通訊 (反之亦然) 的電腦的發現為基礎。

## 後續步驟
在本文件中，您了解到如何在資訊安全中心設定安全性原則。如要深入了解資訊安全中心，請參閱下列主題：

- [Azure 資訊安全中心的安全性健康情況監視](security-center-monitoring.md)：了解如何監視 Azure 資源的健康情況。
- [Azure 資訊安全中心常見問題集](security-center-faq.md)：尋找在使用該服務時經常會遇到的問題。
- [Azure 安全性部落格](http://blogs.msdn.com/b/azuresecurity/)：尋找有關 Azure 安全性與相容性的部落格文章。

<!---HONumber=AcomDC_0204_2016-->