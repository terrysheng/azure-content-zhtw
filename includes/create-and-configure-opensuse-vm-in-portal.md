<properties writer="kathydav" editor="tysonn" manager="jeffreyg" />

**重要事項**：如果您想要讓虛擬機器使用虛擬網路，請務必在建立虛擬機器時指定虛擬網路。只有在建立虛擬機器時，才能將虛擬機器設定為加入虛擬網路。如需虛擬網路的詳細資訊，請參閱 [Azure 虛擬網路概觀][]。

1.  使用 Azure 帳戶，登入 [Azure 管理入口網站][]。

2.  在管理入口網站中，依序按一下網頁左下角的 [+新增]、[虛擬機器] 和 [從藝廊]。

    ![建立新的虛擬機器][]

3.  從 [平台映像] 中選取 OpenSUSE 虛擬機器映像，然後按一下頁面右下角的 [下一步] 箭頭。

4.  在 [虛擬機器組態] 頁面，提供下列資訊：

    -   提供 [虛擬機器名稱] (如 "testlinuxvm")。
    -   指定 [新使用者名稱] (如 "newuser")，此名稱將新增至 Sudoers 清單檔案。
    -   在 [新密碼] 方塊中，輸入[強式密碼][]。
    -   在 [確認密碼] 方塊中，重新輸入密碼。
    -   從 [大小] 下拉式清單中選取適當的大小。

    按 [下一步] 箭頭以繼續。

5.  在 [虛擬機器模式] 頁面，提供下列資訊：

    -   選取 [獨立虛擬機器]。
    -   在 [DNS 名稱] 方塊中，輸入有效的 DNS 位址。例如，"testlinuxvm"。
    -   在 [區域/同質群組/虛擬網路] 方塊中，選取這個虛擬映像將託管於的區域。

	按 [下一步] 箭頭以繼續。

1.  在 [虛擬機器選項] 頁面的 [可用性設定組] 方塊中，選取 [(無)]。按一下打勾記號繼續。

2.  等待 Azure 準備好您的虛擬機器。

## 設定端點

建立虛擬機器之後，您必須設定端點以便進行遠端連線。

1.  在 [管理入口網站] 中，按一下 [虛擬機器]，接著按一下新虛擬機器的名稱，再按一下 [端點]。

2.  按一下頁面底部的 [編輯端點]，並編輯 SSH 端點，使其 [公用連接埠] 為 22。

## 連線至虛擬機器

在佈建好虛擬機器並設定好端點之後，您可以使用 SSH 或 PuTTY 連線到該虛擬機器。

### 使用 SSH 進行連線

如果您是使用 linux 電腦，請使用 SSH 連線至 VM。在命令提示字元下，執行：

    $ ssh newuser@testlinuxvm.cloudapp.net -o ServerAliveInterval=180

輸入使用者的密碼。

### 使用 PuTTY 進行連線

如果您是使用 Windows 電腦，請使用 PuTTY 連線至 VM。PuTTY 可自 [PuTTY 下載頁面][]下載取得。

1.  下載 **putty.exe** 並將其儲存至您電腦上的目錄中。開啟命令提示字元，瀏覽至該資料夾，然後執行 **putty.exe**。

2.  在 [Host Name] 中輸入 "testlinuxvm.cloudapp.net"，並在 [Port] 中輸入 "22"。
    ![PuTTY Screen][]

## 更新虛擬機器 (選用)

1.  連線至虛擬機器之後，您可以選擇性地安裝系統更新和修補程式。請執行：

    `$ sudo zypper update`

2.  選取 [軟體]，然後選取 [線上更新]。更新清單隨即顯示。選取 [接受] 開始安裝，並套用您系統目前可用的所有新修補程式 (但選用的修補程式除外)。

3.  安裝完成之後，選取 [完成]。您的系統現在已是最新版本。

  [Azure 虛擬網路概觀]: http://go.microsoft.com/fwlink/p/?LinkID=294063
  [Azure 管理入口網站]: http://manage.windowsazure.com
  [建立新的虛擬機器]: ./media/create-and-configure-opensuse-vm-in-portal/CreateVM.png
  [強式密碼]: http://msdn.microsoft.com/en-us/library/ms161962.aspx
  [PuTTY 下載頁面]: http://www.puttyssh.org/download.html
  [PuTTY Screen]: ./media/create-and-configure-opensuse-vm-in-portal/putty.png
