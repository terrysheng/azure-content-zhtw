<properties linkid="article" urlDisplayName="Use SSH" pageTitle="Use SSH to connect to Linux virtual machines in Azure" metaKeywords="Azure SSH keys Linux, Linux vm SSH" description="Learn how to generate and use SSH keys with a Linux virtual machine on Azure." metaCanonical="" services="virtual-machines" documentationCenter="" title="How to Use SSH with Linux on Azure" authors="" solutions="" manager="" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="" />

# 如何對 Azure 上的 Linux 使用 SSH

本主題說明產生與 Azure 相容的 SSH 金鑰所需的步驟。

目前版本的 Azure 管理入口網站僅接受封裝於 X509 憑證中的 SSH 公開金鑰。請遵循下列步驟，產生 SSH 金鑰並用於 Azure。

## 在 Linux 中產生 Windows Azure 的相容金鑰

1.  若有需要，請安裝 `openssl` 公用程式：

    **CentOS/Oracle Linux**

        `sudo yum install openssl`

    **Ubuntu**

        `sudo apt-get install openssl`

    **SLES 及 openSUSE**

        `sudo zypper install openssl`

2.  使用 `openssl`，以 2048 位元 RSA 金鑰組來產生 X509 憑證。請回答 `openssl` 提示的一些問題 (您也可以將這些問題留空)。平台並未使用這些欄位的內容：

            openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem

3.  變更私密金鑰的權限來保護私密金鑰。

            chmod 600 myPrivateKey.key

4.  建立 Linux 虛擬機器時上傳 `myCert.pem`。佈建程序會為虛擬機器中的指定使用者，自動將此憑證中的公開金鑰安裝至 `authorized_keys` 檔案中。

5.  如果您將直接使用 API，而不使用管理入口網站，請使用下列命令，將 `myCert.pem` 轉換為 `myCert.cer` (DER 編碼的 X509 憑證)：

            openssl  x509 -outform der -in myCert.pem -out myCert.cer

## 從現有 OpenSSH 相容金鑰產生金鑰

上一個範例說明如何建立用於 Windows Azure 的新金鑰。在某些情況下，使用者可能已經有現有的 OpenSSH 相容公用與私密金鑰組，而希望對 Windows Azure 使用相同的金鑰。

`openssl` 公用程式可以直接讀取 OpenSSH 私密金鑰。下列命令將使用現有的 SSH 私密金鑰 (在下例中為 id\_rsa)，並建立 Windows Azure 所需的 `.pem` 公開金鑰：

    # openssl req -x509 -key ~/.ssh/id_rsa -nodes -days 365 -newkey rsa:2048 -out myCert.pem

**myCert.pem** 檔案是之後可用來在 Windows Azure 上佈建 Linux 虛擬機器的公開金鑰。在佈建期間，`.pem` 檔案將轉譯為 `openssh` 相容的公開金鑰，並放在 `~/.ssh/authorized_keys` 中。

## 從 Linux 連線到 Windows Azure 虛擬機器

每個 Linux 虛擬機器均是使用 SSH 佈建在與標準連接埠不同的特定連接埠，因此您可以

1.  尋找從管理入口網站連接到 Linux 虛擬機器時將使用的連接埠。
2.  使用 `ssh` 來連線到 Linux 虛擬機器。第一次登入時，將提示您接受主機公開金鑰的指紋。

        ssh -i  myPrivateKey.key -p <port> username@servicename.cloudapp.net

3.  (選用) 您可以將 `myPrivateKey.key` 複製到 `~/.ssh/id_rsa`，以便 openssh 用戶端能夠自動選擇該金鑰，而無需使用 `-i` 選項。

## 在 Windows 上取得 OpenSSL

### 使用 msysgit

1.  從下列位置下載並安裝 msysgit：[][]<http://msysgit.github.com/></a>
2.  從安裝的目錄執行 `msys` (範例：c:\\msysgit\\msys.exe)
3.  透過輸入 `cd bin` 來變更至 `bin` 目錄。

### 使用 GitHub for Windows

1.  從下列位置下載並安裝 GitHub for Windows：[][1]<http://windows.github.com/></a>
2.  從 [開始] 功能表 \> [所有程式] \> [GitHub, Inc] 執行 Git Shell

### 使用 cygwin

1.  從下列位置下載並安裝 Cygwin：[][2]<http://cygwin.com/></a>
2.  確定已安裝 OpenSSL 封裝及其所有的相依性。
3.  執行 `cygwin`

## 在 Windows 上建立私密金鑰

1.  遵循以上的其中一組指示，以便執行 `openssl.exe`
2.  輸入以下命令：

        openssl.exe req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem

3.  畫面應該如下所示：

    ![linuxwelcomegit][linuxwelcomegit]

4.  回答提出的問題。
5.  它會建立兩個檔案：`myPrivateKey.key` 和 `myCert.pem`。
6.  如果您將直接使用 API，而不使用管理入口網站，請使用下列命令，將 `myCert.pem` 轉換為 `myCert.cer` (DER 編碼的 X509 憑證)：

        openssl.exe  x509 -outform der -in myCert.pem -out myCert.cer

## 建立 Putty 的 PPK

1.  從下列位置下載並安裝 puttygen：[][3]<http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html></a>
2.  執行 `puttygen.exe`
3.  按一下功能表：[File] \> [Load a Private Key]
4.  找出您的私密金鑰，名稱為 `myPrivateKey.key`。您將需要變更檔案篩選，才能顯示 [\*\*所有檔案 (\*.\*)\*\*]
5.  按一下 [開啟]。您將看見提示，看起來如下所示：

    ![linuxgoodforeignkey][linuxgoodforeignkey]

6.  按一下 [確定]。
7.  按一下以下螢幕擷取畫面強調顯示的 [Save Private Key]：

    ![linuxputtyprivatekey][linuxputtyprivatekey]

8.  將檔案儲存為 PPK。

## 使用 Putty 連線到 Linux 機器

1.  從下列位置下載並安裝 putty：[][3]<http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html></a>
2.  執行 putty.exe
3.  以來自管理入口網站的 IP 來填入主機名稱。

    ![linuxputtyconfig][linuxputtyconfig]

4.  選取 [Open] 之前，請按一下 [Connection] \> [SSH] \> [Auth] 索引標籤來選擇您的金鑰。請參閱以下螢幕擷取畫面，以了解要填入的欄位。

    ![linuxputtyprivatekey][4]

5.  按一下 [Open] 連線到虛擬機器。

  []: http://msysgit.github.com/
  [1]: http://windows.github.com/
  [2]: http://cygwin.com/
  [linuxwelcomegit]: ./media/virtual-machines-linux-use-ssh-key/linuxwelcomegit.png
  [3]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
  [linuxgoodforeignkey]: ./media/virtual-machines-linux-use-ssh-key/linuxgoodforeignkey.png
  [linuxputtyprivatekey]: ./media/virtual-machines-linux-use-ssh-key/linuxputtygenprivatekey.png
  [linuxputtyconfig]: ./media/virtual-machines-linux-use-ssh-key/linuxputtyconfig.png
  [4]: ./media/virtual-machines-linux-use-ssh-key/linuxputtyprivatekey.png
