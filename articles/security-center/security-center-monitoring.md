<properties
   pageTitle="Azure 資訊安全中心的安全性健康情況監視 | Microsoft Azure"
   description="本文件可協助您開始使用「Azure 資訊安全中心」的監視功能。"
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
   ms.date="12/16/2015"
   ms.author="yurid"/>

#Azure 資訊安全中心的安全性健康情況監視
本文件可協助您使用 Azure 資訊安全中心內的監視功能，監視是否符合原則。

> [AZURE.NOTE]本文中的資訊適用於 Azure 資訊安全中心的預覽版本。

## 什麼是 Azure 資訊安全中心？
Azure 資訊安全中心利用加強對 Azure 資源的能見度及安全性控制權，以預防、偵測並回應威脅。它提供您訂用帳戶之間的整合式安全性監視與原則管理，協助您偵測可能忽略掉的威脅，且適用於廣泛的安全性解決方案生態系統。

##什麼是安全性健康情況監視？
我們通常都將監視想像成監看及等候事件發生，以便對該情況作出反應。安全性監視是指擁有一個主動式策略來稽核您的資源，以識別出不符合組織標準或最佳做法的系統。

##監視安全性健全狀況
為訂用帳戶的資源啟用[安全性原則](security-center-policies.md)之後，Azure 資訊安全中心會分析您資源的安全性狀態，以找出潛在的弱點。雖然您網路組態的相關資訊是立即可用，但虛擬機器組態的相關資訊 (例如安全性更新狀態與作業系統組態) 則可能需要花費一個小時或更久才能提供。您可以在 [資源安全性健康情況] 刀鋒視窗中，檢視資源的安全性狀態及任何問題。您也可以在 [建議] 刀鋒視窗上檢視這些問題的清單。

如需如何套用建議的詳細資訊，請參閱[實作 Azure 資訊安全中心的安全性建議](security-center-recommendations.md)。

您可利用 [資源健全狀況] 磚，監視資源的安全性狀態。您會看到一些需要注意的高度或中度嚴重性問題，如下所示：

![資源健康情況](./media/security-center-monitoring/security-center-monitoring-fig1.png)

已啟用的安全性原則將會影響這裡監督的控制項類型。如果 Azure 資訊安全中心找出必須處理的弱點 (例如遺漏安全性更新的 VM 或沒有[網路安全性群組](virtual-networks-nsg.md)的子網路)，將會在這裡指出。

###虛擬機器
按一下 [資源健全狀況] 磚中的 [虛擬機器] 時，將會開啟 [虛擬機器] 刀鋒視窗，內含有關訓練與預防步驟的更多詳細資訊，以及受 Azure 資訊安全中心監視的所有 VM 清單，如下所示：

![遺漏系統更新 (依 VM)](./media/security-center-monitoring/security-center-monitoring-fig2.png)

開啟此刀鋒視窗後，您會看到三個區段。針對每個區段，您可以選取一個個別的選項，以查看有關解決該問題之建議步驟的更多詳細資訊。

例如，如果您按一下 [預防步驟] 下的 [遺漏系統更新]，將會開啟 [遺漏系統更新] 刀鋒視窗，內含遺漏修補程式的 VM 清單及每個 VM 的嚴重性層級，如下所示：

![遺漏系統更新預防](./media/security-center-monitoring/security-center-monitoring-fig3.png)

若要檢視建議的更新詳細資料，請按一下 VM 的名稱。將會開啟該 VM 的新刀鋒視窗，內含遺漏的更新清單：

![建議的系統更新 (依 VM)](./media/security-center-monitoring/security-center-monitoring-fig4.png)

> [AZURE.NOTE]這裡的安全性建議與 [建議] 刀鋒視窗中的建議相同。如需解決建議的詳細資訊，請參閱[實作 Azure 資訊安全中心的安全性建議](security-center-recommendations.md)一文。這不只適用於 VM，也適用於 [資源健康情況] 磚中所有可用的資源。

###網路
網路的預防狀態會顯示受到「Azure 資訊安全中心」監視的虛擬網路。當您按一下 [資源健全狀況] 磚中的 [網路] 時，將會開啟含有更多詳細資訊的 [網路] 刀鋒視窗，如下所示：

![建議的系統更新 (依 VM)](./media/security-center-monitoring/security-center-monitoring-fig5.png)

類似於虛擬機器資源健康情況資訊，這個刀鋒視窗也在刀鋒視窗上半部提供問題的摘要清單，在下半部提供受監督的網路清單。

在網路狀態明細區段中，會列出預防步驟，像是未啟用[端點上的 ACL](virtual-machines-set-up-endpoints.md)、未啟用[網路安全性群組](virtual-networks-nsg.md)，以及狀況良好的子網路。您可以按一下其中的任何一項來取得更多詳細資訊，並採取進一步的動作來解決問題。

**未啟用子網路上的 NSG** 警示即為一例。如果按一下此警示，將會開啟 [設定子網路遺漏的網路安全性群組] 刀鋒視窗。在這裡，您將會看到未啟用「網路安全性群組」的子網路描述，以及屬於此子網路的網路介面卡清單。請選取想要在其中套用安全性群組的子網路，然後按一下 [設定 NSG] 選項。隨即會開啟 [選擇網路安全性] 群組刀鋒視窗，如下所示：

![建議的系統更新 (依 VM)](./media/security-center-monitoring/security-center-monitoring-fig6.png)

###SQL
當您按一下 [資源健全狀況] 磚中的 [SQL] 時，將會開啟 [SQL] 刀鋒視窗，內含問題的相關建議，例如未啟用稽核、未啟用透明資料加密，以及資料庫的一般健全狀況的狀態。刀鋒視窗的下半部含有 SQL Server 明細及屬於每部伺服器的資料庫，如下所示：

![建議的系統更新 (依 VM)](./media/security-center-monitoring/security-center-monitoring-fig7.png)

您可以按一下這些建議中的任何一項來取得更多詳細資訊，並採取進一步的動作來解決問題。以下顯示的範例是 [在 SQL 伺服器上啟用稽核] 的展開情況。

![建議的系統更新 (依 VM)](./media/security-center-monitoring/security-center-monitoring-fig8.png)

###應用程式
如果您的 Azure 工作負載有應用程式位於具有公開 Web 連接埠 (TCP 連接埠 80 和 443) 的[資源管理員 VM](resource-manager-deployment-model.md) 中，Azure 資訊安全中心即可監視這些應用程式以找出潛在的安全性問題，並建議補救步驟。當您按一下 [應用程式] 磚時，將會開啟 [應用程式] 刀鋒視窗，其 [預防步驟] 區段中會有一系列的建議，同時也會顯示每一主機/虛擬 IP 的應用程式明細，如下所示：

![建議的系統更新 (依 VM)](./media/security-center-monitoring/security-center-monitoring-fig9.png)

就像先前資源的任何其他建議一樣，您也可以按一下它來查看有關問題的更多詳細資訊及其補救方式。下圖所示範例是識別為**不安全的 Web 應用程式**之應用程式。當您選取被視為不安全的應用程式時，將會開啟含有可用選項 (在此例中是啟用「Web 應用程式防火牆」) 的另一個刀鋒視窗。

![建議的系統更新 (依 VM)](./media/security-center-monitoring/security-center-monitoring-fig10.png)

## 後續步驟
在本文件中，您已來了解如何使用「Azure 資訊安全中心」的監視功能。若要深入了解「Azure 資訊安全中心」，請參閱下列主題：

- [在 Azure 資訊安全中心設定安全性原則](security-center-policies.md) - 了解如何在 Azure 資訊安全中心設定安全性設定
- [管理與回應 Azure 資訊安全中心的安全性警示](security-center-managing-and-responding-alerts.md) - 了解如何管理與回應安全性警示
- [Azure 安全性中心常見問題集](security-center-faq.md) - 尋找使用服務的常見問題
- [Azure 安全性部落格](http://blogs.msdn.com/b/azuresecurity/) – 尋找有關 Azure 安全性與相容性的部落格文章

<!---HONumber=AcomDC_1217_2015-->