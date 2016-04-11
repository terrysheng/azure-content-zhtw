<properties
	pageTitle="在 Azure VM (資源管理員) 上設定 SQL Server 的 Azure 金鑰保存庫整合"
	description="了解如何自動化 SQL Server 加密的組態，以用於 Azure 金鑰保存庫。本主題說明如何搭配「資源管理員」建立之 SQL Server 虛擬機器使用 Azure 金鑰保存庫整合。"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="rothja"
	manager="jeffreyg"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="03/24/2016"
	ms.author="jroth"/>

# 在 Azure VM (資源管理員) 上設定 SQL Server 的 Azure 金鑰保存庫整合

> [AZURE.SELECTOR]
- [資源管理員](virtual-machines-windows-ps-sql-keyvault.md)
- [傳統](virtual-machines-windows-classic-ps-sql-keyvault.md)

## 概觀
有多個 SQL Server 加密功能，例如[透明資料加密 (TDE)](https://msdn.microsoft.com/library/bb934049.aspx)、[資料行層級加密 (CLE)](https://msdn.microsoft.com/library/ms173744.aspx) 和 [備份加密](https://msdn.microsoft.com/library/dn449489.aspx)。這些形式的加密需要您管理和儲存用來加密的密碼編譯金鑰。Azure 金鑰保存庫 (AKV) 服務是設計來改善這些金鑰在安全且高度可用位置的安全性和管理。[SQL Server 連接器](http://www.microsoft.com/download/details.aspx?id=45344)讓 SQL Server 可以從 Azure 金鑰保存庫使用這些金鑰。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]傳統部署模型。

如果您使用內部部署電腦執行 SQL Server，有[您可以遵循的步驟以從您的內部部署 SQL Server 電腦存取 Azure 金鑰保存庫](https://msdn.microsoft.com/library/dn198405.aspx)。但是對於 Azure VM 中的 SQL server，您可以使用 *Azure 金鑰保存庫整合*功能來節省時間。

啟用這項功能時，它會自動安裝 SQL Server 連接器、設定 EKM 提供者來存取 Azure 金鑰保存庫，並建立認證讓您存取您的保存庫。如果您看到先前提及的內部部署文件中的步驟，您可以發現這項功能會自動執行步驟 2 和 3。您唯一仍然需要手動進行的是建立金鑰保存庫和金鑰。從那裡開始，會自動化 SQL VM 的整個設定。這項功能完成此設定之後，您可以執行 T-SQL 陳述式以開始如往常一般加密您的資料庫或備份。

[AZURE.INCLUDE [準備 AKV 整合](../../includes/virtual-machines-sql-server-akv-prepare.md)]

## 啟用 AKV 整合
如果您是使用資源管理員佈建新的 SQL Server 虛擬機器，則 Azure 入口網站會提供一個步驟來啟用 Azure 金鑰保存庫整合。

![SQL ARM Azure 金鑰保存庫整合](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-arm-akv.png)

如需佈建的詳細逐步解說，請參閱[在 Azure 入口網站中佈建 SQL Server 虛擬機器](virtual-machines-windows-portal-sql-server-provision.md)。

如果需要在現有 VM 上啟用 AKV 整合，則您可以使用範本。如需詳細資訊，請參閱 [Azure 金鑰保存庫整合的 Azure 快速入門範本](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-keyvault-setup)。


[AZURE.INCLUDE [AKV 整合後續步驟](../../includes/virtual-machines-sql-server-akv-next-steps.md)]

<!---HONumber=AcomDC_0330_2016-->