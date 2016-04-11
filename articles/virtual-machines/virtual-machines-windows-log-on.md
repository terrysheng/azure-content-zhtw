<properties
	pageTitle="登入 Windows VM | Microsoft Azure"
	description="使用 Azure 入口網站來登入以資源管理員部署模型建立的 Windows 虛擬機器。"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/03/2016"
	ms.author="cynthn"/>


# 使用 Azure 入口網站登入 Windows 虛擬機器


在 Azure 入口網站中，使用 [連接] 按鈕來啟動遠端桌面工作階段，並登入 Windows VM。


[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-windows-classic-connect-logon.md)。


## 連接至虛擬機器

1. 登入 Azure 入口網站。

2. 按一下 [虛擬機器]，然後選取虛擬機器。

3. 在頁面底部的命令列中，按一下 [連接]。

	![登入虛擬機器](./media/arm_log_on_windows_vm/rm_windows_connect.png)
	

4. 按一下 [連接] 建立並下載 [遠端桌面通訊協定] 檔案 (.rdp file)。按一下 [開啟] 使用這個檔案。

	![登入虛擬機器](./media/arm_log_on_windows_vm/rm_connect_rdp_file.png)
	
5. 在 [遠端桌面] 視窗中按一下 [連接] 以繼續。

	![繼續連接](./media/arm_log_on_windows_vm/rm_connect_unknown.png)

	
6. 在 [Windows 安全性] 視窗中，輸入虛擬機器上帳戶的認證，然後按一下 [確定]。

 	在大多數案例中，認證是您在建立虛擬機器時所指定的本機帳戶使用者名稱和密碼。虛擬機器的名稱是網域，而輸入格式為 *vmname*&#92;*username*。
	
	如果虛擬機器隸屬於貴公司的網域，請確定使用者名稱會包含該網域的名稱且格式為 *Domain*&#92;*Username*。此帳戶必須屬於系統管理員群組，或者已授與遠端存取 VM 的權限。
	
	如果虛擬機器是網域控制站，請輸入該網域的網域系統管理員帳戶的使用者名稱和密碼。

7.	按一下 [是] 來確認虛擬機器的身分識別，並完成登入。

	![驗證機器的身分識別](./media/arm_log_on_windows_vm/rm_connect_cert.png)

## 後續步驟

如果嘗試連線時遇到問題，請參閱[疑難排解執行 Windows 之 Azure 虛擬機器的遠端桌面連線](virtual-machines-windows-troubleshoot-rdp-connection.md)。

<!---HONumber=AcomDC_0330_2016-->