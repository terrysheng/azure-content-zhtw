<properties
	pageTitle="讓 VM 的 D 磁碟機成為資料磁碟 | Microsoft Azure"
	description="說明如何為 Windows VM 變更磁碟機代號，讓您能夠使用 D: 磁碟機做為資料磁碟機。"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/10/2016"
	ms.author="cynthn"/>

# 使用 D 磁碟機做為 Windows VM 上的資料磁碟機 

如果您的應用程式需要使用 D 磁碟機來儲存資料，請遵循下列指示，針對暫存磁碟使用不同的磁碟機代號。切勿使用暫存磁碟儲存需要保留的資料。

如果您調整虛擬機器大小或**停止 (解除配置)** 虛擬機器，這可能會導致虛擬機器置於新的 Hypervisor。已規劃或未規劃的維護事件也可能會觸發這個放置動作。在此案例中，暫存磁碟會重新指派給第一個可用的磁碟機代號。如果您的應用程式特別需要 D: 磁碟機，必須依照這些步驟，暫時移動 pagefile.sys、連接新的資料磁碟並為其指派代號 D，然後將 pagefile.sys 移回暫存磁碟機。一旦完成後，如果 VM 移到不同的 Hypervisor，Azure 將不會收回 D:。

如需有關 Azure 如何使用暫存磁碟的詳細資訊，請參閱[了解 Microsoft Azure 虛擬機器上的暫存磁碟機](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## 連接資料磁碟

首先，您需要將資料磁碟連接到虛擬機器。

- 若要使用入口網站，請參閱[如何在 Azure 入口網站連接資料磁碟](virtual-machines-windows-attach-disk-portal.md)
- 若要使用傳統入口網站，請參閱[如何將資料磁碟連接至 Windows 虛擬機器](virtual-machines-windows-classic-attach-disk.md)。 


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

6. 在 [磁碟機代號] 下方，選取磁碟機 **D**，然後按一下 [確定]。

7. 使用滑鼠右鍵按一下 **G**、暫存磁碟機，然後選取 [變更磁碟機代號及路徑]。

8. 在磁碟機代號下方，選取磁碟機 **E**，然後按一下 [確定]。

> [AZURE.NOTE] 如果您的 VM 有其他磁碟或磁碟機，請使用相同的方法來為其他磁碟及磁碟機重新指派磁碟機代號。您希望磁碟組態為︰
>- C: 作業系統磁碟  
>- D: 資料磁碟  
>- E: 暫存磁碟



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




## 後續步驟
- 您可以[連接其他資料磁碟](virtual-machines-windows-attach-disk-portal.md)來增加虛擬機器可以使用的儲存空間。

<!---HONumber=AcomDC_0323_2016-->