<properties title="Planned maintenance for Azure virtual machines" pageTitle="Azure 虛擬機器的計劃性維護描述" description="Understand what Azure planned maintenance is and how it affects your virtual machines running in Azure." metaKeywords="" services="virtual-machines" solutions="" documentationCenter="" authors="kenazk" videoId="" scriptId="" manager="timlt" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-multiple" ms.devlang="na" ms.topic="article" ms.date="11/26/2014" ms.author="kenazk" />


# Azure 虛擬機器的計劃性維護

## Azure 執行計劃性維護的原因
<p> 為提升虛擬機器之基礎主機基礎結構的可靠性、效能和安全性，Microsoft Azure 會全球定期執行更新。執行許多這些更新時並不會對虛擬機器或雲端服務造成任何影響。不過，其中有些更新的確需要將虛擬機器重新開機，才能將必要更新套用至基礎結構。我們會在修補基礎結構時將虛擬機器關機，然後再將虛擬機器重新啟動。請注意，有兩種的維護會對虛擬機器的可用性造成影響：計劃性和非計劃性。本頁面說明 Microsoft Azure 會如何執行計劃性維護。如需非計劃性維護的詳細資訊，請參閱[了解計劃性與非計劃性維護]。

<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->

* [虛擬機器組態]  
* [多重執行個體更新]
* [單一執行個體更新]
* [電子郵件通知]


## 虛擬機器組態
虛擬機器組態有兩種：多重執行個體和單一執行個體。透過將完全相同的虛擬機器放入可用性設定組，您可以設定多重執行個體虛擬機器。多重執行個體組態可提供備援，建議您採用此組態以確保應用程式的可用性。可用性設定組中的所有虛擬機器應該要幾乎完全相同，並可對您的應用程式提供相同目的的服務。如需關於設定虛擬機器以達到高可用性的詳細資訊，請參閱<a href="http://azure.microsoft.com/zh-tw/documentation/articles/virtual-machines-manage-availability/">「管理虛擬機器的可用性」</a>。 

對照之下，單一執行個體虛擬機器是指未放入可用性設定組的獨立虛擬機器。單一執行個體虛擬機器本身並不符合服務等級協定 (SLA) 的資格，服務等級協定需要在同一個可用性設定組底下部署兩部或以上的虛擬機器。如需 SLA 的詳細資訊，請參閱[服務等級協定]中的「雲端服務、虛擬機器和虛擬網路」一節(../../support/legal/sla/)。


## 多重執行個體更新
在計劃性維護過程中，Azure 平台將首先更新在多重執行個體組態中主控一組虛擬機器的一組主機機器，這會造成這些虛擬機器重新開機。若是多重執行個體組態中的虛擬機器，則整個過程中將會透過保留可用性的方式更新虛擬機器，這裡假設每部機器提供與集合內其他機器類似的功能。基礎 Azure 平台會為可用性設定組中的每部虛擬機器指定一個更新網域 (UD) 和一個容錯網域 (FD)。每個 UD 是指一個虛擬機器群組，且群組內的虛擬機器會在相同的時間期間重新開機。每個 FD 是指一個虛擬機器群組，且群組內的虛擬機器會共用通用電源和網路開關。 

如需有關 UD 和 FD 的詳細資訊，請參閱<a href="http://azure.microsoft.com/zh-tw/documentation/articles/virtual-machines-manage-availability/#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy">「針對備援在可用性設定組中設定多部虛擬機器」</a>。

Microsoft Azure 保證計劃性維護事件不會造成兩個不同 UD 的虛擬機器同時進入離線狀態。執行維護的方法是將每台虛擬機器關機、在主機機器上套用更新、重新啟動虛擬機器，然後換執行下一個 UD。計劃性維護事件會在所有 UD 更新後結束。在計劃性維護過程中，可能不會循序將 UD 重新開機，而是一次只會重新開機一個 UD。現今的多重執行個體組態可提供虛擬機器沒有事先通知的計劃性維護。

在還原虛擬機器之後，以下是 Windows 事件檢視器可能會顯示的範例：

<!--Image reference-->
![][image2]

## 單一執行個體更新
完成多個執行個體更新後，Azure 將接著在即將主控單一執行個體虛擬機器的一組主機機器上執行更新。未在可用性設定組中執行的虛擬機器也會因為此更新而重新開機。請注意，即使您只有一個執行個體在可用性設定組中執行，Azure 平台仍會將它視為多個執行個體。若是在單一執行個體組態中的虛擬機器，則會透過將虛擬機器關機、在主機機器上套用更新、重新啟動虛擬機器的方式更新虛擬機器。區域中的所有虛擬機器會在單一維護期間執行這些更新。此計劃性維護事件將會對此虛擬機器組態類型的應用程式可用性造成影響。 
 
### 電子郵件通知
僅適用於單一執行個體組態虛擬機器，Azure 會至少在一個星期之前預先傳送電子郵件通訊，警告您有關即將到來的計劃性維護。此電子郵件將會傳送到訂閱所提供的主要電子郵件帳戶。以下是此類型電子郵件的範例：

<!--Image reference-->
![][image1]

<!--Anchors-->
[Azure 執行計劃性維護的原因]: #why-azure-performs-planned-maintenance
[虛擬機器組態]: #virtual-machine-configurations
[多重執行個體更新]: #multi-instance-update
[單一執行個體更新]: #single-instance-update
[電子郵件通知]: #email-notification
[image1]: ./media/virtual-machines-planned-maintenance/vmplanned1.png
[image2]: ./media/virtual-machines-planned-maintenance/EventViewerPostReboot.png


<!--Link references-->
[虛擬機器管理可用性]: ../virtual-machines-windows-tutorial/
[了解規劃與未規劃的維護]: ../virtual-machines-manage-availability/#Understand-planned-versus-unplanned-maintenance/ 

<!--HONumber=35.1-->
