<properties
	pageTitle="變更暫存磁碟的磁碟機代號 | Microsoft Azure"
	description="變更隨傳統部署模型建立的 Windows 虛擬機器上的暫存磁碟的磁碟機代號"
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn
"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/27/2015"
	ms.author="cynthn"/>

#變更隨傳統部署模型建立的虛擬機器上的 Windows 暫存磁碟的磁碟機代號

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]本文涵蓋的內容包括以傳統部署模型建立資源。

如果您需要使用 D 磁碟機來儲存資料，請遵循下列指示，使用不同的磁碟機作為暫存磁碟。切勿使用暫存磁碟儲存需要保留的資料。

在開始之前，您需要將資料磁碟連接至虛擬機器，您才能在此程序期間儲存 Windows 分頁檔 (pagefile.sys)。如果您沒有資料磁碟，請參閱[如何將資料磁碟連接至 Windows 虛擬機器][Attach]。如需如何找出哪些磁碟已連接的相關指示，請參閱[如何從 Windows 虛擬機器卸離資料磁碟][Detach]中的＜尋找磁碟＞。

如果您要使用 D 磁碟機現有的資料磁碟，請確定已將 VHD 上傳至儲存體帳戶。如需指示，請參閱[建立 Windows Server VHD 並上傳至 Azure][VHD] 中的步驟 3 和 4。

> [AZURE.WARNING]如果您調整虛擬機器大小或「停止 (取消配置)」虛擬機器，這可能會導致虛擬機器被放置到新的 Hypervisor。已規劃或未規劃的維護事件也可能會觸發這個放置動作。在此案例中，暫存磁碟會重新指派給第一個可用的磁碟機代號。如果您的應用程式特別需要 "D" 磁碟機，請務必在移動分頁檔之後指派一個新的永久性磁碟並為它指派代號 D。Azure 不會收回代號 D。

> [AZURE.WARNING]如果您在明確地移動分頁檔之後調整虛擬機器大小，請注意，如果新的虛擬機器的暫存磁碟大小不足以包含原始 VM 大小的分頁檔，則開機時可能會發生錯誤。當 Azure 以下一個可用的磁碟機代號建立暫存磁碟機時，如果暫存磁碟機未設為下一個可用的磁碟機代號，導致 Windows 參照分頁檔設定中無效的磁碟機代號，也可能會發生這個錯誤。

##變更磁碟機代號

1. 登入虛擬機器。如需詳細資訊，請參閱[如何登入執行 Windows Server 的虛擬機器][Logon]。

2. 將 pagefile.sys 從 D 磁碟機移至另一個磁碟機。

3. 重新啟動虛擬機器。

4. 重新登入，並將磁碟機代號從 D 變更為 E。

5. 在 [Azure 入口網站](http://manage.windowsazure.com)中，連接現有的資料磁碟或空的資料磁碟。

6.	重新登入虛擬機器、初始化磁碟，並指定 D 作為剛連接磁碟的磁碟機代號。

7.	確認 E 已對應至暫存磁碟。

8.	將 pagefile.sys 從其他磁碟機移至 E 磁碟機。

9.	重新啟動虛擬機器。



## 其他資源
[如何登入執行 Windows Server 的虛擬機器][Logon]

[如何從 Windows 虛擬機器卸離資料磁碟][Detach]

[關於 Azure 儲存體帳戶][Storage]

<!--Link references-->
[Attach]: storage-windows-attach-disk.md



[VHD]: virtual-machines-create-upload-vhd-windows-server.md

[Logon]: virtual-machines-log-on-windows-server.md

[Detach]: storage-windows-detach-disk.md

[Storage]: ../storage-whatis-account.md

<!---HONumber=Oct15_HO1-->