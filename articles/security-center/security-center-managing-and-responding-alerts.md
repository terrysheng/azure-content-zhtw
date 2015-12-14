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
   ms.date="12/01/2015"
   ms.author="yurid"/>

# 管理及回應 Azure 資訊安全中心的安全性警示
使用本逐步解說以了解如何使用「Azure 資訊安全中心」功能來管理及回應安全性警示。

> [AZURE.NOTE]本文件中的資訊適用於「Azure 資訊安全中心」的預覽版本。

## Azure 資訊安全中心如何管理安全性警示？
「Azure 資訊安全中心」會自動收集、分析及整合下列來源的記錄檔資料，以偵測真正的威脅並降低誤判：Azure 資源、網路，以及反惡意程式碼和防火牆等合作夥伴解決方案。一個已排定優先順序的警示清單會顯示成 [安全性警示]。

您可以查看 [安全性警示] 磚來檢閱目前的警示。請遵循下列步驟來查看有關每個警示的更多詳細資訊：

1. 從 [Azure 資訊安全中心] 儀表板，您會看到 [安全性警示] 磚。 

    ![啟用資料收集](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig1.png)

2.  按一下圖形中的安全性警示項目，將會開啟 [安全性警示] 刀鋒視窗，內含有關此警示的更多詳細資訊，如以下所示：
    
    ![啟用資料收集](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig2.png)

在此刀鋒視窗的下半部中，您會看到每個警示的詳細資料。您可以按一下所需的資料行來針對每個資料排序警示。每個資料行的定義如以下所示：

- **警示**：有關警示的簡短說明。
- **計數**：代表在特定一天偵測到的這個特定類型的所有警示數目。
- **偵測者**：負責觸發警示的服務。
- **日期**：事件發生的時間。
- **狀態**：顯示該警示的目前狀態。有三種類型的狀態：
    - **使用中**：已偵測到安全性警示。
    - **已解除**：安全性警示已被使用者解除。這個狀態通常用於已調查但發現並非真正攻擊的警示，或已調查並已降低影響的警示。

- **嚴重性**：顯示嚴重性層級，可以是高、中或低。
  

### 回應安全性警示
許多活動可能都代表您的組織可能遭受攻擊。例如，執行合法網路擷取的網路系統管理員可能與發動某種攻擊的人看起來類似。在其他情況下，設定不良的系統可能會在入侵偵測系統中導致一些誤判，而使得準確找出真正的事件變得更加困難。在您使用「Azure 資訊安全中心」檢閱安全性警示之後，您可以開始根據警示的嚴重性採取動作。

若要採取動作，請選取您想要回應的警示，這會在右邊開啟一個含有更多詳細資訊的新刀鋒視窗，如以下所示：

![啟用資料收集](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig3.png)

在此案例中，所觸發的警示是關於可疑的「遠端桌面通訊協定」(RDP) 活動。第一個資料行顯示哪些資源遭到攻擊，後面接著偵測到此攻擊的時間、警示的狀態及嚴重性。檢閱這項資訊之後，您應該按一下遭到攻擊的資源，這會開啟一個新的刀鋒視窗，內含更多您接下來應該採取的動作建議，如以下範例所示：

![啟用資料收集](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig4.png)
  
> [AZURE.NOTE]「Azure 資訊安全中心」所建議的補救措施會因安全性警示而異，而在某些情況下，您可能必須使用其他 Azure 功能才能實作建議補救措施。例如，此攻擊的補救措施是藉由使用[網路 ACL](virtual-networks-acl.md)或[網路安全性群組](virtual-networks-nsg.md)規則，將產生此攻擊的 IP 位址列入黑名單。

### 其他工作
您可以篩選根據日期、狀態及嚴重性來篩選警示。在 [安全性警示] 刀鋒視窗底下，按一下 [篩選] 並啟用您想要的選項，如以下所示：

![啟用資料收集](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig5.png)

如果您需要縮小在儀表板中的檢視範圍，篩選警示會相當有用。例如，您想要確認在過去 24 小時發生的安全性警示，因為您正在調查系統中可能的入侵。雖然大多數安全性警示都會有應該套用的建議，但在某些情況下，您可能需要忽略某個警示，因為對您的環境來說，該警示為誤判或是某個特定資源的預期行為。不論可能是哪一種情況，您都可以使用 [解除] 選項來隱藏特定資源的建議。

若要解除某個工作，請按一下要檢視其他資訊或套用建議組態的資源。在該工作上按一下滑鼠右鍵來解除它，您將會看到類似下面影像的 [解除] 選項：

![啟用資料收集](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig6.png)

### Alerts

在警示集合中，每個警示都會有非常明確的描述與補救措施。您在「Azure 資訊安全中心」看到的警示是根據攻擊案例。以下是由 Microsoft 引擎觸發的攻擊案例：

- **透過網路資料的暴力密碼破解偵測**：這些偵測是根據從網路流量資料學習而來的機器學習模型。 
- **透過端點資料的暴力密碼破解偵測**：這些偵測是根據「Azure 資訊安全中心」的電腦記錄檔查詢；可用來區分失敗和成功的嘗試。 
- **與惡意 IP 通訊的 VM**：這些偵測是根據「Azure 資訊安全中心」對遭到 Bot 入侵而與其「命令與控制 (C&C) 伺服器」通訊 (反之亦然) 之電腦的探索。 

## 後續步驟
在本文件中，您已了解如何在「Azure 資訊安全中心」設定安全性原則。若要深入了解「Azure 資訊安全中心」，請參閱下列主題：

- [Azure 資訊安全中心的安全性健康情況監視](security-center-monitoring.md) – 了解如何監視您 Azure 資源的健康情況
- [Azure 資訊安全中心常見問題集](security-center-faq.md) – 尋找有關使用此服務的常見問題
- [Azure 安全性部落格](http://blogs.msdn.com/b/azuresecurity/) – 尋找有關 Azure 安全性與法規遵循的部落格文章

<!---HONumber=AcomDC_1203_2015-->