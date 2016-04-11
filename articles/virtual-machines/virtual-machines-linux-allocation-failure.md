<properties
	pageTitle="Linux VM 配置失敗的疑難排解 | Microsoft Azure"
	description="在 Azure 中建立、重新啟動或調整 Linux VM 大小時，對配置失敗進行疑難排解"
	services="virtual-machines-linux, azure-resource-manager"
	documentationCenter=""
	authors="jiangchen79"
	manager="felixwu"
	editor=""
	tags="top-support-issue,azure-resourece-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="na"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/02/2016"
	ms.author="cjiang"/>

# 在 Azure 中建立、重新啟動或調整 Linux VM 大小時，對配置失敗進行疑難排解

建立 VM、重新啟動已停止 (已取消配置) 的 VM，或重新調整 VM 大小時，Microsoft Azure 會配置計算資源給您的訂用帳戶。當您執行這些作業時，即使您尚未達到 Azure 訂用帳戶的限制，也可能偶爾收到錯誤訊息。本文說明一些常見配置失敗的原因，並建議可能的補救方法。規劃服務的部署時，本資訊可能也很有用。

＜一般疑難排解步驟＞一節列出解決常見問題的步驟。＜詳細的疑難排解步驟＞一節依照特定的錯誤訊息提供解決問題的步驟。在您開始之前，請參考這裡提供的背景資訊，來了解配置的運作方式及配置失敗的原因。[當您在 Azure 中建立、重新啟動或調整 Windows VM 大小時，也可以對配置失敗進行疑難排解](virtual-machines-windows-allocation-failure.md)。


[AZURE.INCLUDE [virtual-machines-common-allocation-failure](../../includes/virtual-machines-common-allocation-failure.md)]

<!---HONumber=AcomDC_0330_2016-->