<properties writer="kathydav" editor="tysonn" manager="jeffreyg" />

# 如何快速建立虛擬機器 (英文)

請在管理入口網站中使用 [快速建立] 方法來快速建立虛擬機器。建立此機器時，有一個對話方塊可讓您提供組態詳細資料。

**注意**：本文所建立的虛擬機器並未連線到虛擬網路。如果您想要讓虛擬機器使用虛擬網路，請改用 [從組件庫] 方法，並於建立虛擬機器時指定虛擬網路。如需虛擬網路的詳細資訊，請參閱 [Azure 虛擬網路概觀][Azure 虛擬網路概觀]。

1.  登入 [Azure 管理入口網站][Azure 管理入口網站]。

2.  按一下命令列上的 [新增]。

    ![建立新的虛擬機器][建立新的虛擬機器]

3.  按一下 [虛擬機器]，然後按一下 [快速建立]。

    ![快速建立新的虛擬機器][快速建立新的虛擬機器]

    [Create a New Virtual Machine] 對話方塊隨即出現。

4.  為新的虛擬機器輸入下列資訊：

    -   **DNS 名稱** - 此名稱用於所建立的虛擬機器和包含此虛擬機器的雲端服務。
    -   **映像** - 用來建立虛擬機器的平台映像。
    -   **使用者名稱** - 用來管理虛擬機器的帳戶名稱。
    -   **帳戶密碼** - 輸入並確認帳戶的增強式密碼。
    -   **位置** - 虛擬機器所在的區域。

5.  按一下核取記號以建立虛擬機器。

    **注意：**將自動建立儲存體帳戶來包含此虛擬機器。

    您會看到新的虛擬機器列在 [虛擬機器] 頁面上。

    ![成功建立虛擬機器][成功建立虛擬機器]

  [Azure 虛擬網路概觀]: http://go.microsoft.com/fwlink/p/?LinkID=294063
  [Azure 管理入口網站]: http://manage.windowsazure.com
  [建立新的虛擬機器]: ./media/howto-quick-create-vm/create.png
  [快速建立新的虛擬機器]: ./media/howto-quick-create-vm/createquick.png
  [成功建立虛擬機器]: ./media/howto-quick-create-vm/vmsuccesswindows.png
