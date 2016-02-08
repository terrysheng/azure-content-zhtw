<properties 
   pageTitle="在 StorSimple 裝置上安裝 Update 2 | Microsoft Azure"
   description="說明如何在您的 StorSimple 8000 系列裝置上安裝 StorSimple 8000 系列 Update 2。"
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="01/27/2016"
   ms.author="alkohli" />

# 在 StorSimple 裝置上安裝 Update 2

## 概觀

本教學課程說明如何透過 Azure 傳統入口網站，在執行舊版軟體的 StorSimple 裝置上安裝 Update 2。本教學課程也涵蓋了當閘道器設定於 StorSimple 裝置之 DATA 0 以外的網路介面上，而您嘗試從 Update 1 以前的軟體版本更新時所需的步驟。

Update 2 包括裝置軟體更新、LSI 驅動程式更新和磁碟韌體更新。此裝置軟體和 LSI 更新為非干擾性更新，且可透過 Azure 傳統入口網站套用。磁碟韌體更新為干擾性更新，且只能透過裝置的 Windows PowerShell 介面套用。

> [AZURE.IMPORTANT]
 
> -  您可能不會立即看到 Update 2，因為我們會分階段推出更新。請在數天內再次掃描更新，因為很快就會提供 Update。
> - 安裝前會執行一組手動和自動預先檢查，以根據硬體狀態和網路連線來判斷裝置健全狀況。這些預先檢查只會在您從 Azure 傳統入口網站套用更新時執行。 
> - 建議您透過 Azure 傳統入口網站安裝軟體和驅動程式更新。如果入口網站中的更新前閘道器檢查失敗，請移至裝置的 Windows PowerShell 介面安裝更新 (勿透過其他方式)。更新可能需要 4-7 小時才能安裝 (包括 Windows Updates)。維護模式更新必須透過裝置的 Windows PowerShell 介面安裝。由於維護模式更新是干擾性更新，它們將會導致裝置的停機時間。
> - 如果執行選擇性的 StorSimple Snapshot Manager，更新裝置之前，請先將您的 Snapshot Manager 版本升級至 Update 2。

## 準備更新
您必須在掃描和套用更新之前執行下列步驟：


1. 擷取裝置資料的雲端快照。

2. 請確認您的控制器固定 IP 能夠路由，且可以連線到網際網路。這些固定 IP 將用於裝置的服務更新。您可以從裝置的 Windows PowerShell 介面，在每個控制站上執行下列 Cmdlet 以進行測試：

 	`Test-Connection -Source <Fixed IP of your device controller> -Destination <Any IP or computer name outside of datacenter network> `
 
	**當固定 IP 能連線到網際網路時測試連線的範例輸出**

	    
		Controller0>Test-Connection -Source 10.126.173.91 -Destination bing.com
	    
	    Source	  Destination 	IPV4Address      IPV6Address
	    ----------------- -----------  -----------
	    HCSNODE0  bing.com		204.79.197.200
	    HCSNODE0  bing.com		204.79.197.200
	    HCSNODE0  bing.com		204.79.197.200
	    HCSNODE0  bing.com		204.79.197.200
	
		Controller0>Test-Connection -Source 10.126.173.91 -Destination  204.79.197.200

	    Source	  Destination 	  IPV4Address    IPV6Address
	    ----------------- -----------  -----------
	    HCSNODE0  204.79.197.200  204.79.197.200
	    HCSNODE0  204.79.197.200  204.79.197.200
	    HCSNODE0  204.79.197.200  204.79.197.200
	    HCSNODE0  204.79.197.200  204.79.197.200

在您成功完成這些手動預先檢查之後，您就可以繼續掃描和安裝更新。

## 透過 Azure 傳統入口網站安裝 Update 2 

這是更新裝置的建議程序。請執行下列步驟：

[AZURE.INCLUDE [storsimple-install-update2-via-portal](../../includes/storsimple-install-update2-via-portal.md)]

## 以 Hotfix 的方式安裝 Update 2 

請只在嘗試透過 Azure 傳統入口網站安裝更新，而無法通過閘道器檢查時再執行此程序。當您指派閘道器給非 DATA 0 網路介面且您的裝置正在執行早於 Update 1 的軟體版本時，檢查才會失敗。

可以使用 Hotfix 方法升級的軟體版本為 Update 0.1、Update 0.2、Update 0.3、Update 1、Update 1.1 及 Update 1.2。Hotfix 方法涉及下列三個步驟：

- 從 Microsoft Update Catalog 下載 Hotfix。
- 安裝及驗證定期模式 Hotfix。
- 安裝及驗證維護模式 Hotfix。

下表顯示透過此方法套用的 Hotfix：

| 順序 | KB | 名稱 | 更新類型 |
|--------|-----------|-------------------------|------------- |
| 1 | KB3121901 | 軟體更新 | 定期 |
| 2 | KB3121900 | LSI 驅動程式 | 定期 |
| 3 | KB3080728 | Storport 修正程式 | 定期 |
| 4 | KB3090322 | Spaceport 修正程式 | 定期 |
| 5 | KB3121899 | 磁碟韌體 | 維護 |


> [AZURE.IMPORTANT] 
> 
> - 如果您的裝置正在執行 Release (GA) 版本，請連絡 [Microsoft 支援服務](storsimple-contact-microsoft-support.md)以協助您進行更新。
> - 此程序僅需要執行一次，即可套用 Update 2。您可以使用 Azure 傳統入口網站套用後續的更新。
> - 每個 Hotfix 安裝可能需要約 20 分鐘才能完成。總計的安裝時間接近 2 小時。 
> - 使用此程序套用更新之前，請確定兩個裝置控制器都在線上，而且所有硬體元件的狀況良好。

執行下列步驟，以 Hotfix 方式套用 Update 2。

[AZURE.INCLUDE [storsimple-install-update2-hotfix](../../includes/storsimple-install-update2-hotfix.md)]


## 更新失敗的疑難排解

**如果您看到升級前檢查失敗的通知，該怎麼辦？**

如果前置檢查失敗，請確定您已經查看頁面底部的詳細通知列。這會提供有關失敗的前置檢查的指引。下圖顯示這類出現的通知當中的執行個體。在此情況下，控制器健康狀況檢查和硬體元件健康狀況檢查失敗。在 [硬體狀態] 區段下，您可以看到**控制器 0** 及**控制器 1** 元件需要注意。
 
  ![前置檢查失敗](./media/storsimple-install-update-2/HCS_PreUpdateCheckFailed-include.png)

您必須確定兩個控制器是狀況良好且在線上。您也必須確定在 StorSimple 裝置中的所有硬體元件在 [維護] 頁面上都會顯示為狀況良好。接著，您可以嘗試安裝更新。如果您無法修正硬體元件問題，您將需要連絡 Microsoft 支援以了解後續步驟。

**如果您收到「無法安裝更新」錯誤訊息，且建議是參考更新疑難排解指南，以判斷失敗的原因，該怎麼辦？**

其中一個可能的原因是您沒有連線到 Microsoft Update 伺服器。這是需要執行的手動檢查。如果您失去更新伺服器的連線，更新作業將會失敗。您可以從 StorSimple 裝置的 Windows PowerShell 介面執行下列 Cmdlet 以檢查連線：

 `Test-Connection -Source <Fixed IP of your device controller> -Destination <Any IP or computer name outside of datacenter>`

在這兩個控制器上執行 Cmdlet。
 
如果您已經確認連線存在，而且您持續看到這個問題，請連絡 Microsoft 支援以了解後續步驟。


## 後續步驟

深入了解 [Update 2 版](storsimple-update2-release-notes.md)。

<!---HONumber=AcomDC_0128_2016-->