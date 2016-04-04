<properties
   pageTitle="存取 VM 識別碼"
   description="說明如何存取和使用 Azure VM 的唯一識別碼"
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="kmouss"
   manager="drewm"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="02/08/2016"
   ms.author="kmouss"/>
   
# 存取和使用 Azure VM 的唯一識別碼

Azure VM 的唯一識別碼是在所有 Azure IaaS VM 的 SMBIOS 中編碼並儲存的一個 128 位元識別碼，而且目前可以使用平台的 BIOS 命令讀取。

Azure VM 的唯一識別碼是唯讀屬性。在重新開機關機 (計劃中或未計劃)、開始/停止解除配置、服務修復或就地還原之後，Azure 的唯一 VM 識別碼並不會變更。不過，如果 VM 是一個快照，而且是為了建立新的執行個體而複製，就會設定新的 Azure VM 識別碼。

> [AZURE.NOTE] 如果您自這項新功能推出 (2014 年 9 月 18 日) 之後，已經建立並執行較舊的 VM，請重新啟動您的 VM 以自動取得 Azure 的唯一識別碼。


從 VM 內存取 Azure 的唯一 VM 識別碼︰


## 建立 VM
 

如需詳細資訊，請參閱[建立虛擬機器](virtual-machines-linux-creation-choices.md)


## 連接至 VM
 

如需詳細資訊，請參閱 [Linux 中的 SSH](virtual-machines-linux-ssh-from-linux.md)


## 查詢 VM 的唯一識別碼

命令 (範例使用 **Ubuntu**)：

    sudo dmidecode | grep UUID
    
範例預期的結果：

    UUID: 090556DA-D4FA-764F-A9F1-63614EDA019A
    
由於 Big Endian 位元順序的緣故，此案例中實際的唯一 VM 識別碼將是︰

    DA 56 05 09 – FA D4 – 4f 76 - A9F1-63614EDA019A
    
    
不論 VM 是在 Azure 上還是內部部署執行，都可以在不同的案例中使用 Azure VM 的唯一識別碼，而且可以協助您在 Azure IaaS 部署中可能會有的授權、報告或一般追蹤需求。建置應用程式並在 Azure 上進行驗證的許多獨立軟體廠商可能需要在整個生命週期找出 Azure VM，並辨別 VM 是在 Azure、內部部署，或其他雲端提供者上執行。例如，此平台識別碼可協助偵測軟體是否獲得正確授權，或協助將任何 VM 資料及其來源相互關聯，以協助為適當的平台設定適當的計量，並在其他用途之間追蹤和相互關聯這些度量。

<!---HONumber=AcomDC_0323_2016-->