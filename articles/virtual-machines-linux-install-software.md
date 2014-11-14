<properties urlDisplayName="Install software on VM" pageTitle="在 Linux 虛擬機器上安裝軟體 - Azure" metaKeywords="" description="了解如何在 Azure 的 Linux 虛擬機器上安裝軟體，方法是使用 CentOS/Red Hat 或 Ubuntu。" metaCanonical="" services="virtual-machines" documentationCenter="" title="在 Azure 中的 Linux 虛擬機器上安裝軟體" authors="timlt" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="timlt" />

# 在 Azure 中的 Linux 虛擬機器上安裝軟體

Linux 散發套件傾向於使用軟體「套件」來安裝軟體。這些套件通常是使用一組命令 (例如 `apt` 或 `yum`) 來進行管理。您也可以在沒有套件的情況下安裝程式，例如利用原始程式碼的 *tarball*。

我們將會說明如何使用一些常見 Linux 散發套件的套件管理員。相關步驟會根據您所使用的 Linux 散發套件而有所不同。

**注意：** 視您的環境的設定方式而定，可能必須使用根權限來執行下列命令 (透過 `sudo`)。

## CentOS/Red Hat

CentOS 隨附於 `yum`，可用於管理套件。透過此工具，您可以安裝、解除安裝、更新、列出已安裝的套件等。這些命令的語法如下。

### 安裝

此命令可安裝套件以及其相依的套件。由於具有相依性，所以可能會安裝一個以上的套件。

    yum install [package name]

### 解除安裝

此命令會從您的機器解除安裝套件。請記住，不會移除任何相依性。

    yum remove [package name]

### 更新

此命令會將套件更新為最新版本。必須先安裝套件，才可以進行更新。

    yum update [package name]

### 列出已安裝的套件

此命令會顯示您的機器上所有已安裝的套件清單。

    yum list installed

## Ubuntu

Ubuntu 隨附於 `apt` (進階封裝工具)，可用於管理套件。透過此工具，您可以安裝、解除安裝、更新、列出已安裝的套件等。這些命令的語法如下。

### 安裝

此命令可安裝套件以及其相依的套件。由於具有相依性，所以可能會安裝一個以上的套件。

    apt-get install [package name]

### 解除安裝

此命令會從您的機器解除安裝套件。請記住，不會移除任何相依性。

    apt-get remove [package name]

### 更新/升級

若要升級為新版本，您必須使用兩個命令：其中一個可更新套件索引，而另一個可將套件升級。執行下列命令以更新套件索引：

    apt-get update

更新套件索引後，執行下列命令以將套件升級：

    apt-get upgrade
