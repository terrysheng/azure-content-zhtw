<properties
   pageTitle="在 Windows Server 和 Linux 上使用 Azure Service Fabric 進行隨處部署 | Microsoft Azure"
   description="Service Fabric 叢集將會在 Windows Server 和 Linux 上執行，這表示您能夠在任何您可以執行 Windows Server 和 Linux 的環境中部署和裝載 Service Fabric 應用程式。"
   services="service-fabric"
   documentationCenter=".net"
   authors="kunalds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/19/2015"
   ms.author="kunalds"/>

# 使用 Service Fabric 在 Windows Server 或 Linux 上「隨處部署」
新增「隨處部署」後，Service Fabric 可允許在執行 Windows Server 或 Linux 的任何 VM 或電腦上建立 Service Fabric 叢集。這表示您能夠在任何環境中 (不論是在內部部署環境或是透過雲端提供者，您都有一組互連式 Windows Server 或 Linux 電腦) 部署和執行 Service Fabric 應用程式。

 Service Fabric 會提供一個安裝套件供您建立 Service Fabric 叢集。「隨處部署」功能的主要優點在於當您使用 Service Fabric 來建置應用程式時，不會受到廠商限制，因為是您選擇這些應用程式的執行位置。此功能也增加了您拓展客戶群的可能性，因為客戶對於要用來執行您應用程式的環境可能會有各種不同的需求。例如，醫療保健和金融業客戶的需求可能與汽車製造或旅遊業客戶的需求不同。

這項功能的技術預覽版預期會在 2016 年的第一季發行。

## 受支援的作業系統
您將可以在執行下列作業系統的 VM 或電腦上建立叢集：* Windows Server 2012 R2 * Windows Server 2016 * Linux

## 支援的程式設計語言
您將可以使用下列程式設計語言來撰寫 Service Fabric 應用程式：* C# * Java

## 叢集的建立與設定
Service Fabric 將會提供您可從 Microsoft 下載中心下載的安裝套件。下載此套件之後，您將必須變更組態檔來指定您叢集的設定。編輯叢集設定之後，您將執行一個安裝指令碼，此指令碼將建立涵蓋您在叢集設定中所指定之電腦的叢集。

我們將會在 2016 年第一季發行這項功能的預覽版時，分享確切的安裝程序詳細資訊。

## 雲端部署與內部部署之比較
在內部部署環境建立 Service Fabric 叢集的程序將會與在您所選擇、具有一組 VM 的任何雲端建立叢集的程序類似。佈建 VM 的初始步驟將取決於您要使用的雲端或內部部署環境。在您有一組彼此之間已啟用網路連線的 VM 之後，則安裝 Service Fabric 封裝、編輯叢集設定，以及執行叢集建立與管理指令碼的步驟將會類似。這可確保當您選擇以新裝載環境做為目標時，可將您操作和管理 Service Fabric 叢集方面的知識與經驗轉移過去。

## 隨處部署功能的優點
* 因為不會受到廠商限制，您可以選擇這些應用程式的執行位置。
* Service Fabric 應用程式一旦撰寫完成，只需進行最低限度的變更或不需任何變更，即可在多個裝載環境中執行。
* 建置 Service Fabric 應用程式的知識可以從一個裝載環境轉移到其他裝載環境。
* 執行 Service Fabric 叢集的操作經驗可以從從一個部署環境轉移到其他部署環境。
* 不受裝載環境條件約束束縛的廣大客戶群。
* 有一層額外的可靠性和保護可防止發生廣泛的中斷狀況，因為您可以在資料中心或雲端提供發生中斷時，將服務移到另一個部署環境。

## Azure 上的 Service Fabric 受管理叢集的優點勝於使用「隨處部署」建立及管理的 Service Fabric 叢集
在 Azure 上執行 Service Fabric 叢集可提供一些勝於使用「隨處部署」選項的優點，因此如果您對叢集的執行位置沒有特定的需求，則建議您將在 Azure 上執行叢集。在 Azure 中，我們與其他的 Azure 功能和服務整合，因此能輕鬆地操作與管理叢集：

* **Azure 入口網站：**Azure 入口網站能輕鬆建立和管理叢集。
* **Azure 資源管理員：**使用 Azure 資源管理員可輕鬆管理叢集做為單位使用的所有資源，並簡化成本追蹤與付費作業。
* **診斷：**在 Azure 上，我們提供與 Azure 診斷及 Operational Insights 的整合。
* **自動調整：**對於 Azure 上的叢集，我們會提供內建的自動調整功能。在其他使用「隨處部署」功能的環境中，您將必須建置您自己的自動調整規模功能，或使用 Service Fabric 針對調整叢集規模顯示的 API 來手動調整規模。

<!---HONumber=AcomDC_1223_2015-->