<properties 
   pageTitle="修改 StorSimple 裝置上的 DATA 0 網路介面設定"
   description="了解如何重新設定 StorSimple 裝置上的 DATA 0 網路介面"
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carolz"
   editor="tysonn" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/30/2015"
   ms.author="alkohli" />

# 修改 StorSimple 裝置上的 DATA 0 網路介面設定

## 概觀
Microsoft Azure StorSimple 裝置有 6 個網路介面，從 DATA 0 至 DATA 5。DATA 0 介面一律透過 Windows PowerShell 介面或序列主控台設定，且自動以雲端啟用。DATA 0 介面會在初始部署 StorSimple 裝置期間透過安裝精靈第一次設定。當裝置處於操作模式時，您可能需要重新進行 DATA 0 設定。本教學課程提供兩種方法修改 DATA 0 網路設定，兩者皆透過 Windows PowerShell for StorSimple。

閱讀本教學課程之後，您將能夠：

- 透過安裝精靈修改 DATA 0 網路設定
- 透過 `Set-HcsNetInterface` cmdlet 修改 DATA 0 網路設定


## 透過安裝精靈修改 DATA 0 網路設定
您可以連接至 StorSimple 裝置的 Windows PowerShell 介面並啟動安裝精靈工作階段以重新設定 DATA 0 網路設定。執行下列步驟以修改 DATA 0 設定：

#### 透過安裝精靈修改 DATA 0 網路設定

1. 在序列主控台功能表中，選擇選項 1 [使用完整存取權登入]。出現提示時，提供**裝置系統管理員密碼**。預設密碼為 `Password1`。

1. 在命令提示字元中，輸入：


	`Invoke-HcsSetupWizard`

1. 安裝精靈將會出現以協助您設定裝置的 DATA 0 介面。提供 IP 位址、閘道器和網路遮罩的新值。

> [AZURE.NOTE]固定控制器 IP 必須在 Azure 管理入口網站透過 StorSimple 裝置的 [設定] 頁面重新設定。如需詳細資訊，請移至[透過設定 (裝置) 頁面修改網路介面](storsimple-modify-device-config.md#modify-network-interfaces)。


## 透過 Set-HcsNetInterface cmdlet 修改 DATA 0 網路設定
重新設定 DATA 0 的替代方式為透過使用 `Set-HcsNetInterface` cmdlet。cmdlet 是從 StorSimple 裝置的 Windows PowerShell 介面執行。執行下列步驟以修改 DATA 0 設定：

#### 透過 Set-HcsNetInterface cmdlet 修改 DATA 0 網路設定

1. 在序列主控台功能表中，選擇選項 1 [使用完整存取權登入]。出現提示時，提供**裝置系統管理員密碼**。預設密碼為 `Password1`。

1. 在命令提示字元中，輸入：

	`Set-HCSNetInterface -InterfaceAlias Data0 -IPv4Address <> -IPv4Netmask <> -IPv4Gateway <> -Controller0IPv4Address <> -Controller1IPv4Address <> -IsiScsiEnabled 1 -IsCloudEnabled 1`

	如果您使用 IPv6 位址，請使用下列命令：

	`Set-HCSNetInterface -InterfaceAlias Data0 -IPv6Address <> -IPv6Netmask <> -IPv6Gateway <> -Controller0IPv6Address <> -Controller1IPv6Address <> -IsiScsiEnabled 1 -IsCloudEnabled 1`

1. 為下列項目在角括弧 (< >) 中輸入 DATA 0 的值：
											
	- IPv4/IPv6 位址
	
	- IPv4/IPv6 閘道器
	
	- IPv4/IPv6 子網路遮罩
	
	- 控制器 0 的固定的 IPv4/IPv6 位址

	- 控制器 1 的固定的 IPv4/IPv6 位址

## 後續步驟
如果您在設定您的網路介面時遇到任何問題，請參閱[疑難排解部署問題](storsimple-troubleshoot-deployment.md)。

<!---HONumber=July15_HO5-->