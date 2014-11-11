<properties linkid="manage-linux-howto-custom-create-vm" urlDisplayName="Create a custom VM" pageTitle=" Create a custom virtual machine running Linux in Azure" metaKeywords="Azure custom vm, creating custom vm" description="Learn how to create a custom virtual machine in Azure." metaCanonical="http://www.windowsazure.com/zh-tw/manage/windows/how-to-guides/custom-create-a-vm/" services="virtual-machines" documentationCenter="" title="" authors="kathydav" solutions="" manager="dongill" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav" />

# 如何建立自訂虛擬機器 (英文)

*自訂*虛擬機器是指您使用 [從組件庫] 選項建立的虛擬機器，因為您可以有比 [快速建立] 選項更多的組態選擇。其中包含：

-   更多映像選擇來建立虛擬機器 (VM)
-   將 VM 連線到虛擬網路
-   安裝 VM 代理程式和擴充功能，例如反惡意程式碼
-   將 VM 加入現有的雲端服務
-   將 VM 加入可用性設定組

**重要事項**：如果要讓虛擬機器使用虛擬網路，以便依主機名稱來連接虛擬機器，或設定跨單位連線，則必須在建立虛擬機器時指定虛擬網路。只有在建立虛擬機器時，才能將虛擬機器設定為加入虛擬網路。如需虛擬網路的詳細資訊，請參閱 [Azure 虛擬網路概觀][Azure 虛擬網路概觀]。

[WACOM.INCLUDE [virtual-machines-create-WindowsVM](../includes/virtual-machines-create-WindowsVM.md)]

  [Azure 虛擬網路概觀]: http://go.microsoft.com/fwlink/p/?LinkID=294063
  [virtual-machines-create-WindowsVM]: ../includes/virtual-machines-create-WindowsVM.md
