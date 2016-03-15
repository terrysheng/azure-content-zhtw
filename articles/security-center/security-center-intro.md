<properties
   pageTitle="Azure 資訊安全中心簡介 | Microsoft Azure"
   description="了解 Azure 資訊安全中心、其主要功能及運作方式。"
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="StevenPo"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/02/2016"
   ms.author="terrylan"/>

# Azure 資訊安全中心簡介

了解 Azure 資訊安全中心、其主要功能及運作方式。

> [AZURE.NOTE] 本文中的資訊適用於 Azure 資訊安全中心的預覽版本。本文件將使用範例部署來介紹服務。

## 什麼是 Azure 資訊安全中心？
 資訊安全中心利用加強對您 Azure 資源的能見度及安全性控制權，來協助您預防、偵測及回應威脅。它提供您 Azure 訂用帳戶之間的整合式安全性監視和原則管理，協助您偵測可能會忽略的威脅，且適用於廣泛的安全性解決方案生態系統。

##	主要功能
 資訊安全中心，提供 Azure 內建的威脅預防、偵測及回應功能，簡單易用又有效。主要功能：

| | |
|----- |-----|
| 防止 | 監視 Azure 資源的安全性狀態 |
| | 根據貴公司的安全性需求、您使用的應用程式類型和資料的機密性，定義您的 Azure 訂用帳戶與資源群組原則 |
| | 使用原則導向的安全性建議，引導服務擁有者完成需要控制項的實作程序 |
| | 快速部署 Microsoft 和協力廠商的安全性服務和應用裝置 |
| 偵測 |自動收集和分析下列來源的安全性資料：Azure 資源、網路，以及反惡意程式碼程式和防火牆等夥伴解決方案 |
| | 利用 Microsoft 產品和服務、Microsoft 數位犯罪防治中心 (DCU)、Microsoft 安全性回應中心 (MSRC) 以及外部摘要的全域威脅智慧 |
| | 套用進階分析，包括機器學習服務和行為分析 |
| 回應 | 依優先順序提供安全性事件/警示 |
| | 提供對攻擊和受影響資源來源的深入見解 |
| | 建議停止目前攻擊及協助避免未來攻擊的方法 |

## 逐步解說介紹
 您可以從 [Azure 入口網站](https://azure.microsoft.com/features/azure-portal/)存取資訊安全中心。請[登入入口網站](https://portal.azure.com)，選取 [瀏覽]，然後捲動至 [資訊安全中心] 選項，或選取之前釘選到入口網站儀表板的 [資訊安全中心] 磚。

![Azure 入口網站中的安全性磚][1]

從資訊安全中心，您可以設定安全性原則、監視安全性組態，並檢視安全性警示。

### 安全性原則

您可以根據貴公司的安全性需求定義 Azure 訂用帳戶與資源群組的原則。您也可以根據您使用的應用程式類型或每個訂用帳戶中的資料機密性來修改這些原則。例如，用於開發或測試的資源與用於實際執行應用程式的資源，兩者的安全性需求可能不同。同樣地，具有規範資料 (例如 PII) 的應用程式可能需要較高層級的安全性。

> [AZURE.NOTE] 若要修改訂用帳戶等級或資源群組層級的安全性原則，您必須是該訂用帳戶的擁有者或參與者。

在 [資訊安全中心] 刀鋒視窗中，選取 [安全性原則] 磚來取得訂用帳戶和資源群組清單。

![[資訊安全中心] 刀鋒視窗][2]

在 [安全性原則] 刀鋒視窗中，選取訂用帳戶來檢視原則詳細資料。

![[安全性原則] 刀鋒視窗訂用帳戶][3]

**資料收集** (見上文) 可收集安全性原則的資料。若加以啟用可以：

- 每日掃描所有支援的虛擬機器，取得安全性監控與建議。
- 收集安全性事件，供分析和偵測威脅所用。

選擇每個區域的儲存體帳戶 (見上文) 能夠讓您針對每個有虛擬機器在其中執行的區域選擇儲存體帳戶，以儲存從這些虛擬機器收集到的資料。如不為每個區域選擇儲存體帳戶，系統會為您建立。基於安全性考量，收集到的資料在邏輯上會與其他客戶的資料隔離。

> [AZURE.NOTE] 在訂用帳戶層級設定資料收集，並選擇每個區域的儲存體帳戶。

顯示建議 (見上文) 可讓您選擇想要監視的安全性控制項，並根據訂用帳戶內的資源安全性需求提出建議。

接下來，選取要加以檢視原則詳細資訊的資源群組。

![[安全性原則] 刀鋒視窗資源群組][4]

繼承 (見上文) 可讓您將資源群組定義為︰

- 繼承 (預設) 表示此資源群組的所有安全性原則都繼承自訂用帳戶層級。
- 唯一則表示資源群組將具有自訂的安全性原則。您必須在顯示建議下進行變更。

> [AZURE.NOTE] 如果訂用帳戶層級原則與資源群組層級原則間有衝突，將會優先採用資源群組層級原則。

### 安全性建議

 資訊安全中心會分析 Azure 資源的安全性狀態，以識別潛在的安全性弱點。建議清單會引導您完成設定所需控制項的程序。範例包括：

- 佈建反惡意程式碼，以協助識別及移除惡意軟體
- 設定網路安全性群組與規則，以控制對虛擬機器的流量
- 佈建 Web 應用程式防火牆，以協助抵禦以 Web 應用程式為目標的攻擊
- 部署遺漏的系統更新
- 處理不符合建議基準的作業系統設定

按一下 [建議] 磚取得建議清單。按一下每個建議，檢視其他資訊或採取行動以解決問題。

![Azure 資訊安全中心的安全性建議][5]

### 資源健康情況

[資源健康狀態] 磚會依資源類型顯示環境的整體安全性狀態，包括虛擬機器、Web 應用程式和其他資源。

在 [資源健康狀態] 磚上選取資源類型來檢視詳細資訊，包括一份任何已識別潛在安全性弱點的清單。(在下面的範例已選取虛擬機器。)

![資源健康狀態磚][6]

### 安全性警示

 資訊安全中心會自動收集、分析和整合下列來源的記錄檔資料：Azure 資源、網路，以及反惡意程式碼程式和防火牆等夥伴解決方案。偵測到威脅時，會建立安全性警示。偵測範例包括：

- 已受到危害的虛擬機器與已知的惡意 IP 位址進行通訊
- 使用 Windows 錯誤報告偵測到進階的惡意程式碼
- 針對虛擬機器的暴力密碼破解攻擊
- 來自整合式反惡意程式碼程式和防火牆的安全性警示

按一下 [安全性警示] 磚會顯示已排定優先順序的警示清單。

![安全性警示][7]

選取警示可顯示攻擊的詳細資訊以及矯正方法建議。

![安全性警示詳細資料][8]

## 開始使用
若要開始使用資訊安全中心，您需要 Microsoft Azure 訂用帳戶。資訊安全中心已經由 Azure 訂用帳戶啟用。如果您沒有訂用帳戶，可以註冊[免費試用](https://azure.microsoft.com/pricing/free-trial/)。

 您可以從 [Azure 入口網站](https://azure.microsoft.com/features/azure-portal/)存取資訊安全中心。如需詳細資訊，請參閱[入口網站文件](https://azure.microsoft.com/documentation/services/azure-portal/)。

[開始使用 Azure 資訊安全中心](security-center-get-started.md)會快速引導您認識資訊安全中心的安全性監視和原則管理元件。

## 後續步驟
本文介紹了資訊安全中心、其重要功能和如何開始進行。若要深入了解，請參閱下列文章：

- [在 Azure 資訊安全中心內設定安全性原則](security-center-policies.md)--了解如何為您的 Azure 訂用帳戶及資源群組設定安全性原則。
- [在 Azure 資訊安全中心內管理安全性建議](security-center-recommendations.md)--了解建議如何協助您保護您的 Azure 資源。
- [在 Azure 資訊安全中心內進行安全性健康情況監視](security-center-monitoring.md)--了解如何監視 Azure 資源的健康情況
- [在 Azure 資訊安全中心內管理與回應安全性警示](security-center-managing-and-responding-alerts.md)--了解如何管理與回應安全性警示。
- [Azure 安全性中心常見問題集](security-center-faq.md)--尋找使用服務的常見問題。
- [Azure 安全性部落格](http://blogs.msdn.com/b/azuresecurity/)--取得最新的 Azure 安全性新聞和資訊。

<!--Image references-->
[1]: ./media/security-center-intro/security-tile.PNG
[2]: ./media/security-center-intro/security-center.png
[3]: ./media/security-center-intro/security-policy.png
[4]: ./media/security-center-intro/security-policy-blade.png
[5]: ./media/security-center-intro/recommendations.png
[6]: ./media/security-center-intro/resources-health.png
[7]: ./media/security-center-intro/security-alert.png
[8]: ./media/security-center-intro/security-alert-detail.png

<!---HONumber=AcomDC_0309_2016-->