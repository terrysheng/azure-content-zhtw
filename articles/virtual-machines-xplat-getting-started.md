<properties
   pageTitle="如何使用 xplat-cli 建立 Azure 虛擬機器"
   description="本主題描述如何在任何平台上安裝 xplat-cli、如何使用它來連接到您的 Azure 帳戶，以及如何從 xplat-cli 建立 VM。"
   services="virtual-machines"
   documentationCenter="virtual-machines"
   authors="squillace"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="command-line-interface"
   ms.workload="infrastructure-services"
   ms.date="02/20/2015"
   ms.author="rasquill"/>

# 使用 Azure 跨平台命令列介面 (xplat-cli) 建立 VM
Xplat-cli 是從任何平台管理 Azure 基礎結構的好方法。

您必須安裝 xplat-cli 且擁有 Azure 訂用帳戶才能建立 VM，現在讓我們來處理這些步驟。如果您沒有 Azure 帳戶，請[取得一個免費帳戶](http://azure.microsoft.com/pricing/free-trial/)。

## 安裝 xplat-cli

請遵循這些指示來安裝 [xplat-cli](http://azure.microsoft.com/documentation/articles/xplat-cli/#install)。

## 使用 xplat-cli 連接到 Azure

您可以使用個人的 Azure 帳戶，或是使用工作或學校的 Azure 帳戶，來連接 xplat-cli 安裝。若要了解差異並加以選擇，請參閱[如何連接到您的 Azure 訂用帳戶](http://azure.microsoft.com/documentation/articles/xplat-cli/#configure)。

## 建立及連接到 Azure 中的 VM

建立 VM 時，一開始是選擇 (或上傳) 映像並使用 `azure vm create` 命令。

1. 若要從命令列選擇映像，您可以使用命令 `azure vm image list` 來列出可用的 VM 映像。由於映像為數眾多，因此您會想要使用 `more` 來將結果分頁，或是使用 `grep` (Linux) 或 `findstr` (Windows) 來篩選。例如，如果您想要尋找 Linux 上的 Ubuntu 映像，可使用如下的命令：

        azure vm image list | grep Ubuntu

    這仍會產生一份很長的映像清單，而您可依版本進一步縮小清單：

        azure vm image list | grep Ubuntu-14_10

    您可以從這裡選擇映像，並使用 `show` 命令，更詳細地檢視其屬性：

        azure vm image show b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_10-amd64-server-20150202-zh-tw-30GB

2. 建立您的 VM。

    一旦已選擇 VM 映像之後，就可使用 `vm create` 命令來建立映像。此命令有許多選項，您可以使用說明命令來列出這些選項：

        vm create --help

    除了來自步驟 1 的映像，您建立 VM 所需的重要引數為位置、DNS 名稱及使用者名稱。

    基於驗證目的，您可以選擇指定密碼 (在命令列上或以互動方式)，或使用憑證進行驗證。如果您選擇密碼，則它必須至少是 8 個字元、同時包含大寫與小寫字母，而且包含特殊字元 (例如，! @# $%^ & = 的其中一個)。如果您要在命令列上傳遞密碼，最好將密碼放在引號和逸出特殊字元之中。

    若要選擇位置，您可以使用 `vm location list` 命令來挑選鄰近地區。

  您選擇的 DNS 名稱必須是唯一 (它會對應至 `dnsname.cloudapp.net`)，而且如果您未在命令列上個別指定電腦名稱，則此名稱會與電腦名稱相同。  

    下列 Linux 範例會在 West US 中建立一個 VM、開啟預設的 SSH 連接埠 22 (-e 引數)，並建立名為 `myadminuser` 的使用者：

        azure vm create -e -l "West US"  my-new-cli-vm b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_10-amd64-server-20150202-zh-tw-30GB "myadminuser" "myAdm1n@passwd"

## 後續步驟

讓我們運用您的 VM 來執行動作。 

由於上述範例會開啟預設的 SSH 連接埠，因此，在它啟動並執行之後，連接到 VM 就會變得很簡單。從 Linux 命令列：

    ssh myadminuser@my-new-cli-vm.cloudapp.net

如需查看更多使用 xplat-cli 管理 Azure 基礎結構的範例，在[適用於 Mac 和 Linux 的 Azure 命令列工具](http://azure.microsoft.com/documentation/articles/virtual-machines-command-line-tools/)中有豐富的相關資訊

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png

<!--HONumber=47-->
