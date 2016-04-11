<properties
	pageTitle="在伺服器之間、訂用帳戶之間移動資料庫，或者將資料庫移入或移出 Azure。"
	description="複製、移動和移轉 Azure SQL Database 中的資料和資料庫的簡單步驟。"
	services="sql-database"
	documentationCenter=""
	authors="v-shysun"
	manager="msmets"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/28/2016"
	ms.author="v-shysun"/>

# 在伺服器之間、訂用帳戶之間移動資料庫，或者將資料庫移入或移出 Azure
##若要將資料庫移動至相同訂用帳戶的不同伺服器
- 在 [Azure 入口網站](https://portal.azure.com)中，按一下 [SQL Database]、從清單中選取資料庫，然後按一下 [複製]。如需詳細資訊，請參閱[複製 Azure SQL Database](sql-database-copy.md)。

## 若要在訂用帳戶之間移動資料庫
- 在 [Azure 入口網站](https://portal.azure.com)中，按一下 [SQL Server]，然後從清單中選取裝載您資料庫的伺服器。按一下 [移動]，然後挑選要移動的資源以及要移入的訂用帳戶。

## 若要將 SQL Database 移轉到 Azure
- 判斷資料庫相容性，然後根據您的需求，挑選正確的移轉方法。請依照[移轉 SQL Server 資料庫](../virtual-machines/virtual-machines-windows-classic-migrate-sql.md)中的指導方針和選項進行。

## 若要建立資料庫複本以供 Azure 外部使用
- [匯出 BACPAC 檔案。](sql-database-export.md)

<!---HONumber=AcomDC_0330_2016-->