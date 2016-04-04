<properties
   pageTitle="管理 Azure 資訊安全中心的安全性建議 | Microsoft Azure"
   description="本文件將逐步引導您了解「Azure 資訊安全中心」的建議如何協助您保護 Azure 資源及遵守安全性原則。"
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="StevenPo"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/02/2016"
   ms.author="terrylan"/>

# 管理 Azure 資訊安全中心的安全性建議

本文件將逐步引導您了解如何使用「Azure 資訊安全中心」的建議來協助您保護 Azure 資源。

> [AZURE.NOTE] 本文中的資訊適用於 Azure 資訊安全中心的預覽版本。本文件將使用範例部署來介紹服務。這不是逐步指南。

## 什麼是 Azure 資訊安全中心？
Azure 資訊安全中心利用加強對 Azure 資源的能見度及安全性控制權，以預防、偵測並回應威脅。它提供您訂用帳戶之間的整合式安全性監視和原則管理，協助您偵測可能會忽略的威脅，且適用於廣泛的安全性解決方案生態系統。

## 有哪些安全性建議？
資訊安全中心會定期分析 Azure 資源的安全性狀態。當資訊安全中心識別潛在的安全性弱點時，它會建立建議。這些建議會引導您完成設定所需控制項的程序。

## 實作安全性建議

### 設定建議

在[在 Azure 資訊安全中心設定安全性原則](security-center-policies.md)中，您將了解如何：

- 設定安全性原則。
- 開啟資料收集。
- 選擇要在您的安全性原則中看見的建議。

目前的原則建議是以系統更新、基準規則、反惡意程式碼程式、[端點的 ACL](../virtual-machines/virtual-machines-windows-classic-setup-endpoints.md)、子網路與網路介面上的[網路安全性群組](../virtual-network/virtual-networks-nsg.md)、SQL Database 稽核、SQL Database 透明資料加密及 Web 應用程式防火牆為中心。[設定安全性原則](security-center-policies.md)提供每個建議選項的描述。

### 監視建議
設定安全性原則之後，「資訊安全中心」會分析您資源的安全性狀態，以識別潛在的弱點。[資訊安全中心] 刀鋒視窗上的 [建議] 磚可讓您知道資訊安全中心所識別出的建議總數量。

![][2]

查看每個建議的詳細資料：

1. 按一下 [資訊安全中心] 刀鋒視窗上的 [建議] 磚。將會開啟 [建議] 刀鋒視窗。

系統會以表格格式顯示建議，其中每一行代表一個特定的建議。這個表格的資料行包括：

- **描述**：說明建議及要如何處理它。
- **資源**：列出這個建議適用的資源。
- **狀態**：描述建議的目前狀態：
    - **未處理**：建議尚未處理。
    - **進行中**：正在將建議套用到資源，且您不需要採取任何動作。
    - **已解決**：建議已經完成 (在此情況下，該行會呈現灰色)。
- **嚴重性**：描述該特定建議的嚴重性：
    - **高**：某個有意義的資源 (例如應用程式、VM 或網路安全性群組) 有弱點存在，並且需要注意。
    - **中**：有弱點存在，需要非關鍵性步驟或其他步驟才能將其消除或完成程序。
    - **低**：應該處理但不需要立即注意的弱點存在。(預設不會顯示嚴重性低的建議，但是如果您要查看它們，則可以篩選嚴重性低的建議。)

請使用下表做為參考，以協助您了解可用的建議，以及如果套用建議，每一個建議將產生的作用。

> [AZURE.NOTE] 您會想要了解 Azure 資源的[傳統和資源管理員部署模型](../azure-classic-rm.md)。

|建議|說明|
|-----|-----|
|啟用訂用帳戶/虛擬機器的資料收集|建議您為每個訂用帳戶或特定 VM 開啟安全性原則中的 [資料收集]。|
|解決不相符基準規則|建議您讓作業系統組態符合建議的基準，例如不允許儲存密碼。|
|套用系統更新|建議您將遺漏的系統安全性與重大更新部署到 VM (僅適用於 Windows VM)。|
|設定端點的 ACL|建議您設定存取控制清單來限制對 VM 的輸入存取 (僅適用於傳統)。|
|[新增 Web 應用程式防火牆](security-center-add-web-application-firewall.md)|建議您為 Web 端點部署「Web 應用程式防火牆」(WAF)。您可以將這些應用程式加入現有的 WAF 部署，以保護資訊安全中心的多個 Web 應用程式。WAF 應用裝置 (使用資源管理員部署模型建立) 需要部署至不同的虛擬網路。WAF 應用裝置 (使用傳統部署模型所建立) 受限於只能使用網路安全性群組。這項支援在未來將會延伸至 WAF 應用裝置 (傳統) 的完全自訂部署。|
|完成 Web 應用程式防火牆設定|若要完成 WAF 組態，必須將流量重新路由至 WAF 設備。遵循這項建議會完成必要的設定變更。|
|[啟用反惡意程式碼](security-center-enable-antimalware.md)|建議您將反惡意程式碼程式佈建到 VM (僅適用於 Windows VM)。|
|啟用子網路/網路介面上的網路安全性群組|建議您啟用子網路和網路介面上的網路安全性群組 (NSG) (僅適用於「資源管理員 VM」)。|
|限制透過公用外部端點的存取|建議您為 NSG 設定輸入流量規則。|
|啟用伺服器 SQL 稽核|建議您針對 Azure SQL 伺服器開啟稽核 (僅適用於 Azure SQL 服務，不包括在您虛擬機器上執行的 SQL)。|
|啟用資料庫 SQL 稽核|建議您針對 Azure SQL 資料庫開啟稽核 (僅適用於 Azure SQL 服務，不包括在您虛擬機器上執行的 SQL)。|
|在 SQL 資料庫上啟用透明資料加密|建議您針對 SQL 資料庫啟用加密 (僅適用於 Azure SQL 服務)。|
|部署 VM 代理程式|可讓您查看哪些 VM 需要「VM 代理程式」。為了佈建修補程式掃描、基準掃描及反惡意程式碼程式，必須在 VM 上安裝「VM 代理程式」。預設會為從 Azure Marketplace 部署的 VM 安裝「VM 代理程式」。如需如何安裝 VM 代理程式的相關資訊，請參閱 [VM 代理程式和擴充功能 – 第 2 部分](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/)。|
| [套用磁碟加密](security-center-apply-disk-encryption.md) |建議您使用 Azure 磁碟加密來加密您的 VM 磁碟 (Windows 和 Linux VM)。建議您的 VM 上的作業系統和資料磁碟區都進行加密。|

您可以篩選並關閉建議。

1. 按一下 [建議] 刀鋒視窗上的 [篩選]。即會開啟 [篩選] 刀鋒視窗，您可以選取想要查看的嚴重性和狀態值。

    ![][3]

2. 如果您判斷建議不適用，您可解除該建議，然後將它從您的檢視中篩除。解除建議的方式有兩種。一種方式是在項目上按一下滑鼠右鍵，然後選取 [解除]。另一種方式是將滑鼠游標暫留在項目上，然後按一下出現在右邊的三個點，然後選取 [解除]。您可以按一下 [篩選]，然後選取 [已解除]，來檢視已解除的建議。

    ![][4]

### 套用建議
檢視所有建議之後，請決定應該先套用哪一個建議。建議您使用嚴重性分級做為主要參數，以評估應該先套用哪些建議。讓我們使用**啟用反惡意程式碼**建議，逐步解說有關如何套用建議的範例。

1. 在 [建議] 刀鋒視窗中，選取 [啟用反惡意程式碼]。![][5]

2. 在 [安裝反惡意程式碼] 刀鋒視窗中，從未啟用反惡意程式碼的虛擬機器清單中選取，然後按一下 [安裝反惡意程式碼]。
3. 即會開啟 [新增資源] 刀鋒視窗，讓您選取想要使用的反惡意程式碼解決方案。選取 [Microsoft Antimalware]。
4. 將會顯示反惡意程式碼解決方案的其他相關資訊。選取 [**建立**]。
5. 在 [加入擴充功能] 刀鋒視窗上輸入必要的組態設定，然後選取 [確定]。![][6]

[Microsoft Antimalware](../azure-security-antimalware.md) 現在已在選取的虛擬機器上使用。


## 後續步驟
在本文件中，已向您介紹「資訊安全中心」的安全性建議。如要深入了解資訊安全中心，請參閱下列主題：

- [在 Azure 資訊安全中心設定安全性原則](security-center-policies.md) --了解如何為您的 Azure 訂用帳戶及資源群組設定安全性原則。
- [Azure 資訊安全中心的安全性健全狀況監視](security-center-monitoring.md) -- 了解如何監視 Azure 資源的健全狀況。
- [管理與回應 Azure 資訊安全中心的安全性警示](security-center-managing-and-responding-alerts.md) -- 了解如何管理與回應安全性警示。
- [Azure 資訊安全中心常見問題集](security-center-faq.md) -- 尋找在使用服務時經常會遇到的問題。
- [Azure 安全性部落格](http://blogs.msdn.com/b/azuresecurity/) -- 尋找有關 Azure 安全性與法規遵循的部落格文章。

<!--Image references-->
[2]: ./media/security-center-recommendations/recommendations-tile.png
[3]: ./media/security-center-recommendations/filter-recommendations.png
[4]: ./media/security-center-recommendations/dismiss-recommendations.png
[5]: ./media/security-center-recommendations/select-enable-antimalware.png
[6]: ./media/security-center-recommendations/install-antimalware.png

<!---HONumber=AcomDC_0323_2016-->