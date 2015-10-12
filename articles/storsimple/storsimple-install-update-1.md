<properties 
   pageTitle="在 StorSimple 裝置上安裝 Update 1.2 | Microsoft Azure"
   description="說明如何在您的 StorSimple 8000 系列裝置上安裝 StorSimple 8000 系列更新 1.2。"
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="09/28/2015"
   ms.author="alkohli" />

# 在 StorSimple 裝置上安裝 Update 1.2

## 概觀

本教學課程說明如何在執行 Update 1 之前軟體版本的 StorSimple 裝置上安裝 Update 1.2。本教學課程也涵蓋了在 StorSimple 裝置之 DATA 0 以外的網路介面上設定閘道器時，進行更新所需的額外步驟。

Update 1.2 包括裝置軟體更新、LSI 驅動程式更新和磁碟韌體更新。此軟體和 LSI 驅動程式更新為非干擾性更新且可透過管理入口網站套用。磁碟韌體更新為干擾性更新，且只能透過裝置的 Windows PowerShell 介面套用。

根據正在執行的裝置版本，您可以判斷是否套用 Update 1.2。您可以藉由瀏覽至裝置**儀表板**的**快速瀏覽**區段，檢查裝置的軟體版本。

</br>

| 如果執行的軟體版本... | 在入口網站會發生什麼事？ |
|---------------------------------|--------------------------------------------------------------|
| Release - GA | 如果您正在執行 Release 版本 (GA)，請勿套用此更新。請[連絡 Microsoft 支援服務](storsimple-contact-microsoft-support.md)來更新您的裝置。|
| Update 0.1 | 入口網站套用 Update 1.2。 |
| Update 0.2 | 入口網站套用 Update 1.2。 |
| Update 0.3 | 入口網站套用 Update 1.2。 |
| Update 1 | 此更新將無法使用。 |
| Update 1.1 | 此更新將無法使用。 |

</br>

> [AZURE.IMPORTANT]
 
> -  您可能不會立即看到 Update 1.2，因為我們會分階段推出更新。請在數天內再次掃描更新，因為很快就會提供 Update。
> - 此更新包括一組手動和自動預先檢查，以根據硬體狀態和網路連線來判斷裝置健全狀況。這些預先檢查只會在您從 Azure 入口網站套用更新時執行。 
> - 建議您透過 Azure 管理入口網站安裝軟體和驅動程式更新。如果入口網站中的更新前閘道器檢查失敗，您應該只能進入裝置的 Windows PowerShell 介面 (若安裝更新)。更新可能需要 5-10 小時才能安裝 (包括 Windows Updates)。維護模式更新必須透過裝置的 Windows PowerShell 介面安裝。由於維護模式更新是干擾性更新，它們將會導致裝置的停機時間。

## 準備更新
您必須在掃描和套用更新之前執行下列步驟：


1. 擷取裝置資料的雲端快照。


1. 請確認您的控制器固定 IP 能夠路由，且可以連線到網際網路。這些固定 IP 將用於裝置的服務更新。您可以從裝置的 Windows PowerShell 介面，在每個控制站上執行下列 Cmdlet 以進行測試：

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

## 透過管理入口網站安裝 Update 1.2 

只有當您在裝置上的 DATA 0 網路介面上設定閘道器時才使用此程序。請執行下列步驟，以更新您的裝置。

[AZURE.INCLUDE [storsimple-install-update-via-portal](../../includes/storsimple-install-update-via-portal.md)]

## 在含有為非 DATA 0 網路介面設定之閘道器的裝置上安裝 Update 1.2 

嘗試透過管理入口網站安裝更新時，您應該只在無法通過閘道器檢查時才使用此程序。當您指派閘道器給非 DATA 0 網路介面且您的裝置正在執行早於 Update 1 的軟體版本時，檢查才會失敗。如果您的裝置在非 DATA 0 網路介面上沒有閘道器，您可以直接從管理入口網站更新您的裝置。請參閱[使用管理入口網站安裝 Update 1](#install-update-12-via-the-management-portal)。

可以使用這個方法升級的軟體版本為 Update 0.1、Update 0.2、和 Update 0.3。


> [AZURE.IMPORTANT]
> 
> - 如果您的裝置正在執行 Release (GA) 版本，請連絡 [Microsoft 支援服務](storsimple-contact-microsoft-support.md)以協助您進行更新。
> - 此程序僅需要執行一次，即可套用 Update 1.2。您可以使用 Azure 管理入口網站套用後續的更新。

如果您的裝置執行的是 Update 1.0 之前版本的軟體，而且有設定 DATA 0 以外的網路介面的閘道器，您可以下列兩種方式套用 Update 1.2：

- **選項 1**：下載更新，並從裝置的 Windows PowerShell 介面使用 `Start-HcsHotfix` Cmdlet 套用更新。這是建議的方法。**如果您的裝置正在執行 Update 1.0 或 Update 1.1，請勿使用這個方法來套用 Update 1.2。** 

- **選項 2**：直接從管理入口網站移除閘道器組態並安裝更新。


下列各節提供每個選項的詳細指示。

## 選項 1：使用 Windows PowerShell for StorSimple 套用 Update 1.2 做為 hotfix

嘗試透過管理入口網站安裝更新時，您應該只在執行 Update 0.1、0.2、0.3 時，以及無法通過閘道器檢查時才使用此程序。如果您正在執行 Release (GA) 軟體，請連絡 [Microsoft 支援服務](storsimple-contact-microsoft-support.md)以更新您的裝置。

使用此程序套用更新之前，請確認：

- 這兩個裝置控制器都在線上。

請執行下列步驟，以套用 Update 1.2。**更新可能需要大約 2 小時才能完成 (軟體需要約 30 分鐘、驅動程式需要約 30 分鐘、磁碟韌體需要約 45 分鐘)。**

[AZURE.INCLUDE [storsimple-install-update-option1](../../includes/storsimple-install-update-option1.md)]


## 選項 2：在移除閘道器組態之後使用 Azure 入口網站套用 Update 1.2

此程序僅適用於執行 Update 1 之前軟體版本，並在 DATA 0 以外的網路介面上設定閘道器的 StorSimple 裝置。您必須在套用更新之前清除閘道器設定。
 
更新可能需要花幾個小時才能完成。如果您的主機位於不同的子網路，移除 iSCSI 介面上的閘道器設定可能會導致停機。建議您為 iSCSI 流量設定 DATA 0，以減少停機。
 
執行下列步驟來透過閘道器停用網路介面，然後再套用更新。
 
[AZURE.INCLUDE [storsimple-install-update-option2](../../includes/storsimple-install-update-option2.md)]

## 更新失敗的疑難排解

**如果您看到升級前檢查失敗的通知，該怎麼辦？**

如果前置檢查失敗，請確定您已經查看頁面底部的詳細通知列。這會提供有關失敗的前置檢查的指引。下圖顯示這類出現的通知當中的執行個體。在此情況下，控制器健康狀況檢查和硬體元件健康狀況檢查失敗。在 [硬體狀態] 區段下，您可以看到**控制器 0** 及**控制器 1** 元件需要注意。
 
  ![前置檢查失敗](./media/storsimple-install-update-1/HCS_PreUpdateCheckFailed-include.png)

您必須確定兩個控制器是狀況良好且在線上。您也必須確定在 StorSimple 裝置中的所有硬體元件在 [維護] 頁面上都會顯示為狀況良好。接著，您可以嘗試安裝更新。如果您無法修正硬體元件問題，您將需要連絡 Microsoft 支援以了解後續步驟。

**如果您收到「無法安裝更新」錯誤訊息，且建議是參考更新疑難排解指南，以判斷失敗的原因，該怎麼辦？**

其中一個可能的原因是您沒有連線到 Microsoft Update 伺服器。這是需要執行的手動檢查。如果您失去更新伺服器的連線，更新作業將會失敗。您可以從 StorSimple 裝置的 Windows PowerShell 介面執行下列 Cmdlet 以檢查連線：

 `Test-Connection -Source <Fixed IP of your device controller> -Destination <Any IP or computer name outside of datacenter>`

在這兩個控制器上執行 Cmdlet。
 
如果您已經確認連線存在，而且您持續看到這個問題，請連絡 Microsoft 支援以了解後續步驟。


## 後續步驟

深入了解 [Update 1.2 版](storsimple-update1-release-notes.md)

<!---HONumber=Oct15_HO1-->