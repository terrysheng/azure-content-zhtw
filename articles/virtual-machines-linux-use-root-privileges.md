<properties urlDisplayName="Use root privileges" pageTitle="在 Azure 中的 Linux 虛擬機器上使用根權限" metaKeywords="" description="Learn how to use root privileges on a Linux virtual machine in Azure." metaCanonical="" services="virtual-machines" documentationCenter="" title="Using root privileges on Linux virtual machines in Azure" authors="szarkos" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="11/18/2014" ms.author="szark" />




# 在 Azure 中的 Linux 虛擬機器上使用根權限

在 Azure 中的 Linux 虛擬機器上，依預設會停用 `root` 使用者。使用者使用 `sudo` 命令，以升級的權限執行命令。不過，根據系統如何佈建而定，操作過程可能不同。

1. **SSH 金鑰和密碼，或僅密碼** - 虛擬機器是以憑證 (`.CER` 檔案) 或 SSH 金鑰和密碼來佈建，或只以使用者名稱和密碼來佈建。在此情況下，執行命令之前，`sudo` 會提示輸入使用者的密碼。

2. **僅 SSH 金鑰** - 虛擬機器以憑證 (`.cer` 或 `.pem` 檔案) 或 SSH 金鑰佈建，未使用密碼。在此情況下，執行命令之前，`sudo` **不會**提示輸入使用者的密碼。


## SSH 金鑰和密碼，或僅密碼

使用 SSH 金鑰或密碼驗證登入 Linux 虛擬機器，然後使用 'sudo' 來執行命令，例如：

	# sudo <command>
	[sudo] password for azureuser:

在此例子中，將會提示使用者輸入密碼。輸入密碼之後，'sudo' 會以 'root' 權限來執行命令。


## 僅 SSH 金鑰

使用 SSH 金鑰驗證登入 Linux 虛擬機器，然後使用 'sudo' 來執行命令，例如：

	# sudo <command>

在此例子中，將**不會**提示使用者輸入密碼。按 `<enter>` 後，'sudo' 會以 'root' 權限來執行命令。


<!--HONumber=35_1-->
