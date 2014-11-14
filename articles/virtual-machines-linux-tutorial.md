<properties urlDisplayName="Create a virtual machine" pageTitle="在 Azure 中建立執行 Linux 的虛擬機器" metaKeywords="Azure Linux vm, Linux vm" description="了解如何透過使用 Azure 的映像建立執行 Linux 的 Azure 虛擬機器 (VM)。 " metaCanonical="" services="virtual-machines" documentationCenter="" title="" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="10/9/2014" ms.author="kathydav" />

# 建立執行 Linux 的虛擬機器

您可以使用 Azure 管理入口網站中的映像庫，輕鬆地建立執行 Linux 的虛擬機器。本指南說明如何執行此作業，並假設您沒有使用 Azure 的經驗。

> [WACOM.NOTE] 即使您不需要使用 Azure VM 的任何經驗來完成此教學課程，但您的確需要 Azure 帳戶。只需要幾分鐘的時間，您就可以建立免費試用帳戶。如需詳細資料，請參閱＜[建立 Azure 帳戶][建立 Azure 帳戶]＞。

本教學課程涵蓋下列項目：

-   [關於 Azure 中的虛擬機器][關於 Azure 中的虛擬機器]
-   [如何建立虛擬機器][如何建立虛擬機器]
-   [如何在建立虛擬機器之後登入][如何在建立虛擬機器之後登入]
-   [如何將資料磁碟連接至新的虛擬機器][如何將資料磁碟連接至新的虛擬機器]

**重要事項**：本教學課程所建立的虛擬機器並未連線到虛擬網路。如果您想要讓虛擬機器使用虛擬網路，就必須在建立虛擬機器時指定虛擬網路。如需虛擬網路的詳細資訊，請參閱 [Azure 虛擬網路概觀][Azure 虛擬網路概觀]。

## <span id="virtualmachine"></span> </a>關於 Azure 中的虛擬機器

Azure 中的虛擬機器是您在雲端中可以控制和管理的伺服器。在 Azure 中建立虛擬機器之後，可隨時依需要刪除和重新建立，而存取虛擬機器就像存取公司裡的伺服器一樣。使用虛擬硬碟 (VHD) 檔案來建立虛擬機器。虛擬機器使用下列類型的 VHD：

-   **映像** - 做為範本以建立新虛擬機器的 VHD。映像只是範本，並沒有像執行的虛擬機器一樣有特定設定，例如電腦名稱及使用者帳戶設定。如果使用映像建立虛擬機器，則會自動為新的虛擬機器建立作業系統磁碟。
-   **磁碟** - 磁碟是指可開機並掛接為作業系統執行版本的 VHD。映像在佈建之後就成為磁碟。使用映像建立虛擬機器時一定會建立磁碟。任何連接至虛擬化硬體而且當做服務一部分執行的 VHD 都是磁碟

使用映像建立虛擬機器時有下列選項可用：

-   使用 Azure 管理入口網站的映像庫中提供的映像來建立虛擬機器。
-   建立含有映像的 .vhd 檔案並上傳至 Azure，然後利用此映像來建立虛擬機器。如需關於建立和上傳自訂映像的詳細資訊，請參閱[建立和上傳包含 Linux 作業系統的虛擬硬碟][建立和上傳包含 Linux 作業系統的虛擬硬碟]。

每一部虛擬機器都是位於雲端服務內，可能是單獨存在或與其他虛擬機器並存。您可以將虛擬機器放在相同的雲端服務內，使虛擬機器彼此相互通訊，讓虛擬機器之間的網路流量達到負載平衡，並維持機器的高可用性。如需關於雲端服務和虛擬機器的詳細資訊，請參閱＜[Azure 簡介][Azure 簡介]＞中的＜執行模型＞一節。

## <span id="custommachine"></span> </a>如何建立虛擬機器

本教學課程使用 [從組件庫] 方法來建立虛擬機器，因為它提供比 [快速建立] 方法還要多的選項。您可以選擇連接的資源、DNS 名稱和網路連線 (若有需要)。

[WACOM.INCLUDE [virtual-machines-create-LinuxVM](../includes/virtual-machines-create-LinuxVM.md)]

[WACOM.INCLUDE [virtual-machines-Linux-tutorial-log-on-attach-disk](../includes/virtual-machines-Linux-tutorial-log-on-attach-disk.md)]

## 後續步驟

若要深入了解 Azure 上的 Linux，請參閱下列文章：

-   [Azure 上的 Linux 簡介][Azure 上的 Linux 簡介]

-   [如何使用適用於 Mac 和 Linux 的 Azure 命令列工具][如何使用適用於 Mac 和 Linux 的 Azure 命令列工具]

-   [關於 Azure VM 組態設定][關於 Azure VM 組態設定]

  [建立 Azure 帳戶]: http://www.windowsazure.com/zh-tw/develop/php/tutorials/create-a-windows-azure-account/
  [關於 Azure 中的虛擬機器]: #virtualmachine
  [如何建立虛擬機器]: #custommachine
  [如何在建立虛擬機器之後登入]: #logon
  [如何將資料磁碟連接至新的虛擬機器]: #attachdisk
  [Azure 虛擬網路概觀]: http://go.microsoft.com/fwlink/p/?LinkID=294063
  [建立和上傳包含 Linux 作業系統的虛擬硬碟]: /zh-tw/manage/linux/common-tasks/upload-a-vhd/
  [Azure 簡介]: http://go.microsoft.com/fwlink/p/?LinkId=311926
  [Azure 上的 Linux 簡介]: http://www.windowsazure.com/zh-tw/documentation/articles/introduction-linux/
  [如何使用適用於 Mac 和 Linux 的 Azure 命令列工具]: http://www.windowsazure.com/zh-tw/documentation/articles/xplat-cli/
  [關於 Azure VM 組態設定]: http://msdn.microsoft.com/library/azure/dn763935.aspx
