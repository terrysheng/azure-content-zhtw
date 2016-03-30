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
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/09/2016"
   ms.author="yurid"/>

#Azure 資訊安全中心的安全性健康情況監視
本文件可協助您使用 Azure 資訊安全中心內的監視功能，監視是否符合原則。

> [AZURE.NOTE] 本文中的資訊適用於 Azure 資訊安全中心的預覽版本。

## 什麼是 Azure 資訊安全中心？
資訊安全中心利用加強對您 Azure 資源的能見度及安全性控制權，來協助您預防、偵測及回應威脅。它提供您訂用帳戶之間的整合式安全性監視與原則管理，協助您偵測可能忽略掉的威脅，且適用於廣泛的安全性解決方案生態系統。

##什麼是安全性健康情況監視？
我們通常都將監視想像成監看及等候事件發生，以便對該情況作出反應。安全性監視是指擁有一個主動式策略來稽核您的資源，以識別出不符合組織標準或最佳做法的系統。

##監視安全性健全狀況
在您為訂用帳戶的資源啟用[安全性原則](security-center-policies.md)之後，資訊安全中心會分析您資源的安全性狀態，以找出潛在的弱點。網路組態的相關資訊立即可用，但虛擬機器組態的相關資訊 (例如安全性更新狀態與作業系統組態) 則可能需要花費一個小時或更久才能提供。您可以在 [資源安全性健康情況] 刀鋒視窗中，檢視資源的安全性狀態及任何問題。您也可以在 [建議] 刀鋒視窗上檢視這些問題的清單。

如需如何套用建議的詳細資訊，請參閱[實作 Azure 資訊安全中心的安全性建議](security-center-recommendations.md)。

您可在 [資源健全狀況] 磚上監視資源的安全性狀態。在以下範例中，您會看到一些需要注意的高度或中度嚴重性問題。已啟用的安全性原則將會影響受監督的控制項類型。

![資源健康情況](./media/security-center-monitoring/security-center-monitoring-fig1-new.png)

如果資訊安全中心找出必須處理的弱點 (例如遺漏安全性更新的 VM 或沒有[網路安全性群組](../virtual-network/virtual-networks-nsg.md)的子網路)，將會在這裡列出。

###監視虛擬機器
按一下 [資源健全狀況] 磚中的 [虛擬機器] 時，將會開啟 [虛擬機器] 刀鋒視窗，內含有關訓練與預防步驟的更多詳細資訊，以及受資訊安全中心監視的所有 VM 清單，如下所示。

![遺漏系統更新 (依 VM)](./media/security-center-monitoring/security-center-monitoring-fig2-2-new.png)

- 登入步驟
- 虛擬機器建議
- 虛擬機器

在每個區段中，您可以選取一個個別的選項，以查看有關解決該問題之建議步驟的更多詳細資訊。下列各節將詳細討論這方面的資訊。

####登入步驟
本節顯示已初始化進行資料收集的 VM 總數和其目前狀態。一旦所有 VM 都初始化資料收集，它們就可以接收資訊安全中心的安全性原則。當您按一下此項目時，就會開啟 [初始化資料收集] 刀鋒視窗，您會在 [安裝狀態] 資料行中看到 VM 名稱和資料收集的目前狀態，如下所示。

![初始化狀態](./media/security-center-monitoring/security-center-monitoring-fig3-new.png)


####虛擬機器建議
本節提供一組 Azure 資訊安全中心所監視的每個 VM 的建議。第一個資料行列出建議，第二個資料行列出受到該建議影響的 VM 總數，第三個資料行顯示問題的嚴重性，如下所示。

![VM 建議](./media/security-center-monitoring/security-center-monitoring-fig4-2-new.png)

每個建議會有一組可在您按一下它之後執行的動作。比方說，如果您按一下 [遺失的系統更新]，[遺失的系統更新] 刀鋒視窗會隨即開啟。它會列出遺失修補程式的 VM 和遺失更新的嚴重性，如下所示。

![遺失的系統更新](./media/security-center-monitoring/security-center-monitoring-fig5-new.png)

[遺失的系統更新] 刀鋒視窗會顯示含有下列資訊的資料表：

- **虛擬機器**：遺失更新之虛擬機器的名稱。
- **系統更新**：遺漏的系統更新數量。
- **上次掃描時間**：資訊安全中心上次掃描 VM 更新的時間。
- **狀態**：建議的目前狀態： 
	- **未處理**：建議尚未處理
	- **進行中**：正在將建議套用到這些資源，您不需要採取任何動作
	- **已解決**：建議已經完成 (問題已解決時，該項目會呈現灰色)。
- **嚴重性**：描述該特定建議的嚴重性： 
	- **高**：某個有意義的資源 (應用程式、VM、網路安全性群組) 有弱點存在，並且需要注意
	- **中**：完成某個程序或消除弱點時所需的非關鍵性步驟或其他步驟
	- **低**：應該處理但不需要立即注意的弱點。(預設不會顯示嚴重性低的建議，但是如果您要檢視它們，則可以篩選嚴重性低的建議)。

若要檢視建議的詳細資料，請按一下 VM 的名稱。將會開啟該 VM 的新刀鋒視窗，內含更新清單，如下所示。

![每個 VM 遺失的系統更新](./media/security-center-monitoring/security-center-monitoring-fig6-new.png)

> [AZURE.NOTE] 這裡的安全性建議與 [建議] 刀鋒視窗中的建議相同。如需解決建議的詳細資訊，請參閱[實作 Azure 資訊安全中心的安全性建議](security-center-recommendations.md)一文。這不只適用於 VM，也適用於 [資源健康情況] 磚中所有可用的資源。

####虛擬機器區段
虛擬機器區段提供所有 VM 和建議的概觀。每個資料行代表一組建議，如下所示：

![VM](./media/security-center-monitoring/security-center-monitoring-fig7-new.png)

每個建議底下出現的圖示，可協助您快速識別哪一個 VM 需要關注以及何種類型的建議。

在上述範例中，有一個 VM 具有與反惡意程式碼程式有關的重要建議。若要取得 VM 的相關詳細資訊，請按一下它。代表此 VM 的新刀鋒視窗會隨即開啟，如下所示。

![VM 安全性詳細資料](./media/security-center-monitoring/security-center-monitoring-fig8-new.png)

此刀鋒視窗中提供 VM 的安全性詳細資料。您可以在此刀鋒視窗底部看到建議的動作和每個問題的嚴重性。

###監視虛擬網路
網路的預防狀態區段會列出受到「資訊安全中心」監視的虛擬網路。當您按一下 [資源健全狀況] 磚中的 [網路] 時，將會開啟含有更多詳細資訊的 [網路] 刀鋒視窗，如下所示：

![網路](./media/security-center-monitoring/security-center-monitoring-fig9-new.png)

開啟此刀鋒視窗後，您會看到兩個區段：
- 網路功能的建議
- 網路
 
在每個區段中，您可以選取個別的選項，以取得建議的相關詳細資訊。下列各節將詳細討論這方面的資訊。

####網路功能的建議

類似於虛擬機器資源健康情況資訊，這個刀鋒視窗也在刀鋒視窗上半部提供問題的摘要清單，在下半部提供受監督的網路清單。

![端點](./media/security-center-monitoring/security-center-monitoring-fig10-new.png)

網路狀態明細區段會列出網路功能的狀態分解區段列出潛在的安全性問題，並提供建議。可能的問題包括：

- 未啟用[端點上的 ACL](../virtual-machines/virtual-machines-windows-classic-setup-endpoints.md)
- 未啟用[網路安全性群組](../virtual-network/virtual-networks-nsg.md)
- 列出 NSG 中狀況良好的子網路和不受限制的存取。 
 
當您按一下這些建議的其中之一時，會開啟新的刀鋒視窗，其中包含與建議相關的更多詳細資料，如下列範例所示。

![限制端點](./media/security-center-monitoring/security-center-monitoring-fig11-new.png)

在這個範例中，[透過公用外部端點限制存取] 刀鋒視窗提供屬於此警示之網路安全性群組 (NSG) 的清單、此 NSG 相關聯的子網路與網路，以及此建議的目前狀態和問題的嚴重性。如果您按一下 [網路安全性群組]，則會開啟另一個刀鋒視窗，如下所示。

此刀鋒視窗具有網路安全性群組的資訊和位置。它也提供目前已啟用的輸入規則清單。此刀鋒視窗的下半部提供與此網路安全性群組相關聯的 VM。如果您想要啟用輸入規則來封鎖目前已開啟但不想使用的連接埠，或變更目前輸入規則的來源，請按一下刀鋒視窗頂端的 [編輯輸入規則] 按鈕。

####網路區段

在 [網路] 區段中，包含資源群組、子網路和與 VM 相關聯之網路介面的階層式檢視，如下所示。

![網路樹狀結構](./media/security-center-monitoring/security-center-monitoring-fig121-new.png)

此區段會區分[以資源管理員為基礎的 VM 和傳統 VM](../resource-manager-deployment-model.md)。這可協助您快速識別 Azure 服務管理或 Azure 資源管理網路功能是否可供虛擬機器使用。如果您決定從這個位置存取網路介面卡內容，您必須展開子網路，然後按一下 VM 名稱。如果您針對以資源管理員為基礎的 VM 執行此動作，將會出現類似以下所示的新刀鋒視窗。

![網路樹狀結構](./media/security-center-monitoring/security-center-monitoring-fig13-new.png)

此刀鋒視窗提供網路介面卡與其目前建議的摘要。如果您選取的 VM 是傳統 VM，則會出現包含不同選項的新刀鋒視窗，如下所示。

![ACL](./media/security-center-monitoring/security-center-monitoring-fig14-new.png)

在此刀鋒視窗中，您可以變更公用和私人連接埠，並建立此 VM 的 ACL。

###監視 SQL 資源
當您按一下 [資源健全狀況] 圖格中的 [SQL] 時，將會開啟 [SQL] 刀鋒視窗，內含問題的建議，例如未啟用稽核、透明資料加密。它也具有資料庫的一般健全狀況狀態建議。

![SQL 資源健全狀況](./media/security-center-monitoring/security-center-monitoring-fig15-new.png)

您可以按一下這些建議中的任何一項來取得更多解決問題之進一步動作的相關詳細資訊。下列範例顯示**未啟用資料庫稽核**建議的擴充。

![SQL 資源健全狀況](./media/security-center-monitoring/security-center-monitoring-fig16-new.png)

[在 SQL 資料庫上啟用稽核] 刀鋒視窗具有下列資訊：

- SQL 資料庫的清單
- 其所在的伺服器
- 此設定是否繼承自伺服器，或此設定在此資料庫中是否為唯一的相關資訊
- 目前的狀態
- 問題的嚴重性

當您在資料庫上按一下以解決這項建議時，將會開啟 [稽核與威脅的偵測] 刀鋒視窗，如下所示。

![SQL 資源健全狀況](./media/security-center-monitoring/security-center-monitoring-fig17-new.png)

若要啟用稽核，只需選取 [稽核] 選項底下的 [開啟]，然後按一下 [儲存]。

###監視應用程式
如果您的 Azure 工作負載有應用程式位於具有公開 Web 連接埠 (TCP 連接埠 80 和 443) 的[資源管理員 VM](../resource-manager-deployment-model.md) 中，資訊安全中心即可監視這些應用程式以找出潛在的安全性問題，並建議補救步驟。當您按一下 [應用程式] 圖格時，將會開啟 [應用程式] 刀鋒視窗，其 [預防步驟] 區段中會有一系列的建議。同時也會顯示每一主機/虛擬 IP 的應用程式明細，如下所示。

![應用程式安全性健全狀況](./media/security-center-monitoring/security-center-monitoring-fig18-new.png)

就像您對其他建議的做法一樣，您也可以按一下它來查看有關問題的更多詳細資訊及其補救方式。下圖所示範例是識別為不安全的 Web 應用程式之應用程式。當您選取被視為不安全的應用程式時，會開啟另一個包含以下可用選項的刀鋒視窗：

![應用程式](./media/security-center-monitoring/security-center-monitoring-fig19-new.png)

[不安全的 Web 應用程式] 刀鋒視窗中會提供包含不被視為安全之應用程式的所有 VM 的清單。清單會顯示 VM 名稱、問題的目前狀態和問題的嚴重性。如果您按一下此 Web 應用程式，將會開啟 [加入 Web 應用程式防火牆] 刀鋒視窗，其中包含讓您安裝協力廠商 WAF (Web 應用程式防火牆) 的選項，如下所示。

![新增 WAF](./media/security-center-monitoring/security-center-monitoring-fig20-new.png)

## 後續步驟
在本文件中，您已來了解如何使用「Azure 資訊安全中心」的監視功能。若要深入了解「Azure 資訊安全中心」，請參閱下列主題：

- [在 Azure 資訊安全中心設定安全性原則](security-center-policies.md) - 了解如何在 Azure 資訊安全中心設定安全性設定
- [管理與回應 Azure 資訊安全中心的安全性警示](security-center-managing-and-responding-alerts.md) - 了解如何管理與回應安全性警示
- [Azure 安全性中心常見問題集](security-center-faq.md) - 尋找使用服務的常見問題
- [Azure 安全性部落格](http://blogs.msdn.com/b/azuresecurity/) – 尋找有關 Azure 安全性與相容性的部落格文章

<!---HONumber=AcomDC_0323_2016-->