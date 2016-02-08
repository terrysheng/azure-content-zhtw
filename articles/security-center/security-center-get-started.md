<properties
   pageTitle="開始使用 Azure 資訊安全中心 | Microsoft Azure"
   description="本文帶領您認識安全性監視和原則管理元件，並連結至下一個步驟，協助您快速開始使用 Azure 資訊安全中心。"
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
   ms.date="12/10/2015"
   ms.author="terrylan"/>

# 開始使用 Azure 資訊安全中心

本文帶領您認識安全性監視和原則管理元件，並連結至下一個步驟，協助您快速開始使用 Azure 資訊安全中心。

> [AZURE.NOTE] 本文中的資訊適用於 Azure 資訊安全中心的預覽版本。這是使用範例部署的服務簡介。這不是逐步指南。

## 什麼是 Azure 資訊安全中心？
Azure 資訊安全中心利用加強對 Azure 資源的能見度及安全性控制權，以預防、偵測並回應威脅。它提供您訂用帳戶之間的整合式安全性監視和原則管理，協助您偵測可能會忽略的威脅，且適用於廣泛的安全性解決方案生態系統。

## 必要條件

若要開始使用 Azure 資訊安全中心，您必須有 Microsoft Azure 訂用帳戶。Azure 資訊安全中心已經由訂用帳戶啟用。如果您沒有訂用帳戶，可以註冊[免費試用](https://azure.microsoft.com/pricing/free-trial/)。

Azure 資訊安全中心是從 [Azure 入口網站](https://azure.microsoft.com/features/azure-portal/)存取。如需詳細資訊，請參閱[入口網站文件](https://azure.microsoft.com/documentation/services/azure-portal/)。


## 存取 Azure 資訊安全中心

在入口網站中，執行下列步驟來存取 Azure 資訊安全中心：

1. 選取 [瀏覽] 並捲動至 [資訊安全中心] 選項。![][1]

2. 選取 [資訊安全中心]。隨即開啟 [資訊安全中心] 刀鋒視窗。
3. 為方便日後快速存取 [資訊安全中心] 刀鋒視窗，請選取 [將刀鋒視窗釘選到儀表板] 選項 (右上方)。![][2]

## 使用 Azure 資訊安全中心

設定訂用帳戶的安全性**原則**：

4. 按一下 [資訊安全中心] 刀鋒視窗上的 [安全性原則] 磚。
5. 在 [安全性原則 – 定義每個訂用帳戶的原則] 刀鋒視窗中，選取訂用帳戶。
6. 在 [安全性原則] 刀鋒視窗中，開啟 [資料收集] 自動收集記錄檔。開啟 [資料收集] 也會將監視擴充功能佈建在訂用帳戶的所有目前和新 VM 上。
7. 按一下 [選擇儲存體帳戶]。為每個執行虛擬機器的區域選擇儲存體帳戶，儲存從這些虛擬機器收集到的資料。如不為每個區域選擇儲存體帳戶，系統會為您建立。基於安全性考量，收集到的資料在邏輯上會和其他客戶資料分開。
8. 開啟想要查看的 [建議]，當做安全性原則的一部分。範例：

  - 開啟 [系統更新] 會掃描所有支援的虛擬機器，尋找缺漏的作業系統更新。
  - 開啟 [基準規則] 會掃描支援的虛擬機器，識別任何可能讓虛擬機器更容易受到攻擊的作業系統設定。

![][3]

處理**建議**：

9. 返回 [資訊安全中心] 刀鋒視窗，然後按一下 [建議] 磚。
10.	Azure 資訊安全中心會定期分析 Azure 資源的安全性狀態。發現潛在的安全性弱點時，會在這裡顯示建議。
11.	按一下每個建議，檢視詳細資訊及/或採取行動以解決問題。

![][4]

透過 [資源健康狀態] 檢視資源的健康狀態及安全性狀態：

12.	返回 [資訊安全中心] 刀鋒視窗。
13.	[資源健康狀態] 磚包含**虛擬機器**、**網路**、**SQL** 和**應用程式**的安全性狀態指標。
14.	請選取 [虛擬機器] 檢視詳細資訊。
15.	[虛擬機器] 刀鋒視窗會顯示虛擬機器的狀態摘要，包括反惡意程式碼、系統更新、重新啟動與基準規則。
16.	在 [預防步驟] 下選取項目，檢視詳細資訊及/或採取行動以設定需要的控制項。
17.	向下鑽研以檢視特定虛擬機器的其他資訊。

![][5]

處理**安全性警示**：

19.	返回 [資訊安全中心] 刀鋒視窗，然後按一下 [安全性警示] 磚。
20.	[安全性警示] 刀鋒視窗中會顯示警示清單。Azure 資訊安全中心透過對安全性記錄檔和網路活動的分析，偵測到警示。也包含來自整合式合作夥伴解決方案的警示。![][6]

21.	按一下警示以檢視其他資訊。

  ![][7]

## 後續步驟
本文介紹了 Azure 資訊安全中心的安全性監視和原則管理元件。若要深入了解，請參閱下列文章：

- [設定 Azure 資訊安全中心的安全性原則](security-center-policies.md) – 了解如何設定安全性原則
- [管理 Azure 資訊安全中心的安全性建議](security-center-recommendations.md) – 了解建議如何協助您保護您的 Azure 資源
- [Azure 資訊安全中心的安全性健康狀態監視](security-center-monitoring.md) – 了解如何監視 Azure 資源的健康狀態
- [管理與回應 Azure 資訊安全中心的安全性警示](security-center-managing-and-responding-alerts.md) – 了解如何管理與回應安全性警示
- [Azure 安全性中心常見問題集](security-center-faq.md) – 尋找使用服務的常見問題
- [Azure 安全性部落格](http://blogs.msdn.com/b/azuresecurity/) – 取得最新的 Azure 安全性新聞和資訊

<!--Image references-->
[1]: ./media/security-center-get-started/security-tile.png
[2]: ./media/security-center-get-started/pin-blade.png
[3]: ./media/security-center-get-started/security-policy.png
[4]: ./media/security-center-get-started/recommendations.png
[5]: ./media/security-center-get-started/resources-health.png
[6]: ./media/security-center-get-started/security-alert.png
[7]: ./media/security-center-get-started/security-alert-detail.png

<!---HONumber=AcomDC_0128_2016-->