<properties
	pageTitle="讓 VM 的 D 磁碟機成為資料磁碟 | Microsoft Azure"
	description="描述如何為使用傳統部署模型建立的 Windows VM 變更磁碟機代號，讓您能夠使用 D: 磁碟機做為資料磁碟機。"
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/03/2015"
	ms.author="cynthn"/>

# 使用 D 磁碟機做為 Windows VM 上的資料磁碟機 

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]資源管理員模型。


如果您需要使用 D 磁碟機來儲存資料，請遵循下列指示，針對暫存磁碟使用不同的磁碟機代號。切勿使用暫存磁碟儲存需要保留的資料。

## 連接資料磁碟

首先，您需要將資料磁碟連接到虛擬機器。若要連接到新的磁碟，請參閱[如何將資料磁碟連接至 Windows 虛擬機器][Attach]。

如果您想要使用現有的資料磁碟，請確定已將 VHD 上傳至儲存體帳戶。如需指示，請參閱[建立 Windows Server VHD 並上傳至 Azure][VHD] 中的步驟 3 和 4。


## 暫時將 pagefile.sys 移到 C 磁碟機

1. 連接至虛擬機器。 

2. 使用滑鼠右鍵按一下 [開始] 功能表，然後選取 [系統]。

3. 在左側功能表中，選取 [進階系統設定]。

4. 在 [效能] 區段中，選取 [設定]。

5. 選取 [進階] 索引標籤。

5. 在 [虛擬記憶體] 區段中，選取 [變更]。

6. 選取 **C** 磁碟機，然後依序按一下 [系統管理大小] 和 [設定]。

7. 選取 **D** 磁碟機，然後依序按一下 [沒有分頁檔] 和 [設定]。

8. 按一下 [套用]。您將會收到一則警告，表示電腦必須重新啟動，才能讓變更生效。

9. 重新啟動虛擬機器。




## 變更磁碟機代號 

1. 重新啟動 VM 之後，再次登入 VM。

2. 按一下 [開始] 功能表，然後輸入 **diskmgmt.msc** 並按下 Enter 鍵。隨即會啟動「磁碟管理」。

3. 使用滑鼠右鍵按一下 **D**、暫存磁碟機，然後選取 [變更磁碟機代號及路徑]。

4. 在磁碟機代號下方，選取磁碟機 **G**，然後按一下 [確定]。

5. 使用滑鼠右鍵按一下資料磁碟，然後選取 [變更磁碟機代號及路徑]。

6. 在磁碟機代號下方，選取磁碟機 **D**，然後按一下 [確定]。

7. 使用滑鼠右鍵按一下 **G**、暫存磁碟機，然後選取 [變更磁碟機代號及路徑]。

8. 在磁碟機代號下方，選取磁碟機 **E**，然後按一下 [確定]。

> [AZURE.NOTE]如果您的 VM 有其他磁碟或磁碟機，請使用相同的方法來為其他磁碟及磁碟機重新指派磁碟機代號。以下為您所需的磁碟設定：- C：作業系統磁碟 - D：資料磁碟 - E：暫存磁碟



## 將 pagefile.sys 移回暫存磁碟機 

1. 使用滑鼠右鍵按一下 [開始] 功能表，然後選取 [系統]

2. 在左側功能表中，選取 [進階系統設定]。

3. 在 [效能] 區段中，選取 [設定]。

4. 選取 [進階] 索引標籤。

5. 在 [虛擬記憶體] 區段中，選取 [變更]。

6. 選取作業系統磁碟機 **C**，然後依序按一下 [沒有分頁檔] 和 [設定]。

7. 選取暫存磁碟機 **E**，然後依序按一下 [系統管理大小] 和 [設定]。

8. 按一下 [Apply (套用)]。您將會收到一則警告，表示電腦必須重新啟動，才能讓變更生效。

9. 重新啟動虛擬機器。




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

<!---HONumber=Nov15_HO2-->