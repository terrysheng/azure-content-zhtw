<properties
	pageTitle="Oracle WebLogic Server 和 Database VM |Microsoft Azure"
	description="使用資源管理員部署模型，建立在 Windows Server 2012 上執行的 Oracle WebLogic Server 12c 和 Oracle Database 12c Azure 映像。"
	services="virtual-machines-windows"
	authors="bbenz"
	documentationCenter=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="infrastructure-services"
	ms.date="06/22/2015"
	ms.author="bbenz" />

#在 Azure 中建立 Oracle WebLogic Server 12c 與 Oracle Database 12c 虛擬機器

本文說明如何在 Azure 中，根據 Microsoft 所提供且在 Windows Server 2012 上執行的 Oracle WebLogic Server 12c 與 Oracle Database 12c 映像來建立虛擬機器。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]傳統部署模型。


##在 Azure 中建立 Oracle WebLogic Server 12c 與 Oracle Database 12c 虛擬機器

1. 登入 [Azure 入口網站](https://ms.portal.azure.com/)。

2.	依序按一下 [Marketplace] 和 [運算]，然後在搜尋方塊中輸入 **Oracle**。

3.	選取 [Windows Server 2012 上的 Oracle Database 12c 與 WebLogic Server 12c Standard Edition] 或 [Windows Server 2012 上的 Oracle Database 12c 與 WebLogic Server 12c Enterprise Edition] 映像。檢閱與此映像有關的資訊 (例如建議大小的最小值)，然後按 [下一步]。

4.	指定虛擬機器的 [主機名稱]。

5.	指定虛擬機器的 [使用者名稱]。請注意，這個使用者名稱是從遠端登入至虛擬機器；這不是 Oracle 資料庫使用者名稱。

6.	指定並確認虛擬機器的密碼，或提供安全殼層 (SSH) 公用金鑰。

7.	選擇 [定價層]。請注意，依預設會顯示建議的定價層。若要查看所有組態選項，可按一下右上方的 [檢視全部]。

8. 視需要設定選用的組態。請遵循這些考量：

	a.讓 [儲存體帳戶] 保持不變，以使用虛擬機器名稱來建立新的儲存體帳戶。

	b.讓 [可用性集合] 保持為 [未設定]。

	c.此時請勿加入任何端點。

9.	選擇或建立資源群組。如需詳細資訊，請參閱[使用 Azure 入口網站管理 Azure 資源](../resource-group-portal.md)。

10. 選擇 [訂用帳戶]。

11. 選擇 [位置]。


##建立裝載在此虛擬機器中的資料庫

遵循[在 Azure 中建立 Oracle Database 12c 虛擬機器](virtual-machines-windows-classic-create-oracle-database.md)中的指示，從**在 Azure 中使用 Oracle Database 12c 虛擬機器建立您的資料庫**一節開始。

##設定裝載在此虛擬機器中的 Oracle WebLogic Server 12c
遵循[在 Azure 中建立 Oracle WebLogic Server 12c 虛擬機器](virtual-machines-windows-create-oracle-weblogic-server-12c.md)中的指示，從**在 Azrue 中設定 Oracle WebLogic Server 12c 虛擬機器**一節開始。

##其他資源
[針對 Oracle 虛擬機器映像的其他考量](virtual-machines-windows-classic-oracle-considerations.md)

[Oracle 虛擬機器映像清單](virtual-machines-linux-classic-oracle-images.md)

[從 Java 應用程式連線到 Oracle 資料庫](http://docs.oracle.com/cd/E11882_01/appdev.112/e12137/getconn.htm#TDPJD136)

[Microsoft Azure 上使用 Linux 的 Oracle WebLogic Server 12c](http://www.oracle.com/technetwork/middleware/weblogic/learnmore/oracle-weblogic-on-azure-wp-2020930.pdf)

[Oracle Database 2 Day DBA 12c Release 1](http://docs.oracle.com/cd/E16655_01/server.121/e17643/toc.htm)

<!---HONumber=AcomDC_0323_2016-->