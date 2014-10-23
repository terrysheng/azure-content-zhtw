<properties linkid="manage-linux-howto-capture-an-image" urlDisplayName="Capture an image" pageTitle="Capture an image of a virtual machine running Linux" metaKeywords="Azure Linux vm, Linux vm" description="Learn how to capture an image of an Azure virtual machine (VM) running Linux. " metaCanonical="" services="virtual-machines" documentationCenter="" title="How to Capture an Image of a Virtual Machine Running Linux" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav"></tags>

# 如何擷取 Linux 虛擬機器以做為範本

本文說明如何擷取 Linux 虛擬機器，以便用它做為範本建立其他虛擬機器。這個虛擬機器範本包括 OS 磁碟和任何連接虛擬機器的資料磁碟。它不包含網路組態，因此您將需要在建立使用該範本的其他虛擬機器時加以設定。

擷取虛擬機器之後，它會出現在您建立虛擬機器時的 [My Images] 下。映像檔會在您的儲存體帳戶中儲存為 VHD。如需映像的詳細資訊，請參閱[管理磁碟和映像][] (英文)。

## 開始之前

這些步驟假設您已建立 Azure 虛擬機器且設定好作業系統，包括連接任何資料磁碟。如果您還沒這麼做，請參閱下列指示：

-   [如何建立自訂虛擬機器 (英文)][]
-   [如何將資料磁碟附加至虛擬機器][]

## 擷取虛擬機器

1.  按一下命令列上的 [連線]，連線到虛擬機器。如需詳細資訊，請參閱[如何登入執行 Linux 的虛擬機器][]。

2.  在 SSH 視窗中，輸入下列命令，然後輸入您在虛擬機器上建立之帳戶的密碼。請注意，不同版本的 `waagent` 公用程式可能會有稍微不同的輸出：

    `sudo waagent -deprovision`

    ![Deprovision the virtual machine][]

3.  輸入 **y** 繼續。

    ![Deprovision of virtual machine successful][]

4.  輸入 **Exit** 關閉 SSH 用戶端。

5.  在[管理入口網站][]中，選取虛擬機器，然後按一下 [關閉]。

6.  當虛擬機器停止時，按一下命令列上的 [擷取]，開啟 [擷取虛擬機器] 對話方塊。

7.  在 [映像名稱] 中輸入新映像的名稱。

8.  所有 Linux 映像都必須已「取消佈建」，方法是執行加上 `-deprovision` 選項的 `waagent` 命令。按一下 [我已在虛擬機器上執行 de-provision 命令]，表示作業系統已準備好要成為映像。

9.  按一下打勾記號以擷取映像。

    新映像現在會出現在 [映像] 下。擷取此映像後，虛擬機器便會遭刪除。

    ![Image capture successful][]

## 後續步驟

映像已可用來做為範本，建立虛擬機器。若要這麼做，您將需要使用 [從組件庫] 方法並選取您剛建立的映像，建立自訂的虛擬機器。如需指示，請參閱[如何建立自訂虛擬機器][如何建立自訂虛擬機器 (英文)]。

  [管理磁碟和映像]: http://go.microsoft.com/fwlink/p/?LinkId=397536
  [如何建立自訂虛擬機器 (英文)]: ../virtual-machines-create-custom/
  [如何將資料磁碟附加至虛擬機器]: ../storage-windows-attach-disk/
  [如何登入執行 Linux 的虛擬機器]: ../virtual-machines-linux-how-to-log-on
  [Deprovision the virtual machine]: ./media/virtual-machines-linux-capture-image/LinuxDeprovision.png
  [Deprovision of virtual machine successful]: ./media/virtual-machines-linux-capture-image/LinuxDeprovision2.png
  [管理入口網站]: http://manage.windowsazure.com
  [Image capture successful]: ./media/virtual-machines-linux-capture-image/VMCapturedImageAvailable.png
