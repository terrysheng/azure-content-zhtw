<properties 
    pageTitle="Operational Insights 智慧套件" 
    description="您可以使用智慧套件將其他功能加入 Operational Insights" 
    services="operational-insights" 
    documentationCenter="" 
    authors="bandersmsft" 
    manager="jwhit" 
    editor=""/>

<tags 
    ms.service="operational-insights" 
    ms.workload="operational-insights" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="03/20/2015" 
    ms.author="banders"/>

# Operational Insights 智慧套件

Microsoft Azure Operational Insights 含有基礎組態評估模組。然而透過從 [概觀] 頁面加入智慧套件，您可以取得額外的功能。

![image of intelligence packs icon](./media/operational-insights-add-intelligence-pack/overview-intelpacks.png)

加入智慧套件後，系統會從基礎結構中的伺服器收集資料，然後再傳送到 Operational Insights 服務。Operational Insights 服務的處理時間少則幾分鐘，多則達數小時之久。待服務處理完資料後，您可以在 Operational Insights 中予以檢視。

當您不再需要智慧套件時，您可以輕易地將它移除。移除智慧組件後，系統不會將資料傳送到 Operational Insights，因此可減少每日配額所使用的資料量。

## Microsoft Monitoring Agent 支援的智慧套件

在現階段，使用 Microsoft Monitoring Agent 直接連接 Microsoft Azure Operational Insights 的伺服器可使用目前可用的大多數智慧套件，包括：

- [系統更新](operational-insights-updates.md)

- [記錄檔管理](operational-insights-log-collection.md)

- [反惡意程式碼](operational-insights-antimalware.md)

- [變更追蹤](operational-insights-change-tracking.md)

- [SQL 和 Active Directory 評估](operational-insights-assessment.md)

然而，以下是 Microsoft Monitoring Agent  *不*支援的智慧套件

- [產能管理](operational-insights-capacity.md)

- [組態評估](operational-insights-configuration-assessment.md)

安裝以下作業系統的電腦支援 IIS 記錄檔收集：

- Windows Server 2012

- Windows Server 2012 R2

### 加入智慧套件


1. 在 Operational Insights 的 [概觀] 頁面中按一下 [**智慧套件**] 磚。


2. 在 [Operational Insights 智慧套件組件庫] 頁面中，您可以了解每個可用的智慧套件。按一下要加入 Operational Insights 之智慧套件的名稱。


3. 在所選智慧套件的頁面中，該智慧套件的詳細資料會顯示於此。按一下 [**加入**]。


4. 在確認頁面中，按一下 [**接受**] 以同意隱私權聲明和使用條款。


5. 加入智慧套件而新建立的磚會出現在 Operational Insights 的 [概觀] 頁面中；待 Operational Insights 服務處理資料後，您便可以開始使用智慧套件。




### 移除智慧套件



1. 在 Operational Insights 的 [概觀] 頁面中按一下 [**智慧套件**] 磚。


2. 在 [Operational Insights 智慧套件組件庫] 頁面中，於要移除的智慧套件下方按一下 [**移除**]。


3. 在確認頁面中按一下 [**是**] 以移除智慧套件。



<!--HONumber=52-->