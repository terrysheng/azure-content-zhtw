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
   ms.date="03/21/2016"
   ms.author="alkohli" />

# 在 StorSimple 裝置上安裝 Update 1.2

## 概觀

本教學課程說明如何在執行 Update 1 之前軟體版本的 StorSimple 裝置上安裝 Update 1.2。本教學課程也涵蓋了在 StorSimple 裝置之 DATA 0 以外的網路介面上設定閘道器時，進行更新所需的額外步驟。

Update 1.2 包括裝置軟體更新、LSI 驅動程式更新和磁碟韌體更新。此軟體和 LSI 驅動程式更新為非干擾性更新，且可透過 Azure 傳統入口網站套用。磁碟韌體更新為干擾性更新，且只能透過裝置的 Windows PowerShell 介面套用。

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
> - 此更新包括一組手動和自動預先檢查，以根據硬體狀態和網路連線來判斷裝置健全狀況。這些預先檢查只會在您從 Azure 傳統入口網站套用更新時執行。
> - 建議您透過 Azure 傳統入口網站安裝軟體和驅動程式更新。如果入口網站中的更新前閘道器檢查失敗，請移至裝置的 Windows PowerShell 介面安裝更新 (勿透過其他方式)。更新可能需要 5-10 小時才能安裝 (包括 Windows Updates)。維護模式更新必須透過裝置的 Windows PowerShell 介面安裝。由於維護模式更新是干擾性更新，它們將會導致裝置的停機時間。

[AZURE.INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## 透過 Azure 傳統入口網站安裝 Update 1.2

請執行下列步驟，以將您的裝置更新至 [Update 1.2](storsimple-update1-release-notes.md)。只有當您在裝置上的 DATA 0 網路介面上設定閘道器時才使用此程序。

[AZURE.INCLUDE [storsimple-install-update2-via-portal](../../includes/storsimple-install-update2-via-portal.md)]

12. 確認您的裝置是否正在執行 **StorSimple 8000 Series Update 1.2 (6.3.9600.17584)**。[**上次更新日期**] 應該也已修改。您也會看到有可用的維護模式更新 (此訊息可能會在您安裝更新之後繼續顯示長達 24 小時)。

    維護模式更新為干擾性更新，會導致裝置產生停機時間，且只能透過您裝置的 Windows PowerShell 介面加以套用。

    ![維護頁面](./media/storsimple-install-update-1/InstallUpdate12_10M.png "維護頁面")

13. 使用[下載 Hotfix](#to-download-hotfixes) 中列出的步驟，下載維護模式更新，以搜尋和下載 KB3063416，它會安裝磁碟韌體更新 (現在其他更新應該已安裝)。

13. 請遵循[安裝及驗證維護模式 Hotfix](#to-install-and-verify-maintenance-mode-hotfixes) 中列出的步驟安裝這些維護模式更新。

14. 在 Azure 傳統入口網站，瀏覽至 [維護] 頁面，然後在頁面底部，按一下 [掃描更新] 來檢查是否有任何 Windows 更新，然後按一下 [安裝更新]。成功安裝所有更新之後就完成了。



## 在含有為非 DATA 0 網路介面設定之閘道器的裝置上安裝 Update 1.2

請只在嘗試透過 Azure 傳統入口網站安裝更新，而無法通過閘道器檢查時再執行此程序。當您指派閘道器給非 DATA 0 網路介面且您的裝置正在執行早於 Update 1 的軟體版本時，檢查才會失敗。如果您的裝置在非 DATA 0 網路介面上沒有閘道器，您可以直接在 Azure 傳統入口網站中更新您的裝置。請參閱[透過 Azure 傳統入口網站安裝 Update 1.2](#install-update-1.2-via-the-azure-classic-portal)。

可以使用這個方法升級的軟體版本為 Update 0.1、Update 0.2、和 Update 0.3。


> [AZURE.IMPORTANT]
>
> - 如果您的裝置正在執行 Release (GA) 版本，請連絡 [Microsoft 支援服務](storsimple-contact-microsoft-support.md)以協助您進行更新。
> - 此程序僅需要執行一次，即可套用 Update 1.2。您可以使用 Azure 傳統入口網站套用後續的更新。

如果您的裝置執行的是 Update 1.0 之前版本的軟體，而且有設定 DATA 0 以外的網路介面的閘道器，您可以下列兩種方式套用 Update 1.2：

- **選項 1**：下載更新，並從裝置的 Windows PowerShell 介面使用 `Start-HcsHotfix` Cmdlet 套用更新。這是建議的方法。**如果您的裝置正在執行 Update 1.0 或 Update 1.1，請勿使用這個方法來套用 Update 1.2。**

- **選項 2**：直接從 Azure 傳統入口網站移除閘道器組態並安裝更新。


下列各節提供每個選項的詳細指示。

## 選項 1：使用 Windows PowerShell for StorSimple 套用 Update 1.2 做為 hotfix

請只在您執行的是 Update 0.1、0.2、0.3，且嘗試透過 Azure 傳統入口網站安裝更新，而無法通過閘道器檢查時再執行此程序。如果您正在執行 Release (GA) 軟體，請連絡 [Microsoft 支援服務](storsimple-contact-microsoft-support.md)以更新您的裝置。

若要將 Update 1.2 安裝為 Hotfix，您必須下載並安裝下列 Hotfix：

| 順序 | KB | 說明 | 更新類型 |
|--------|-----------|-------------------------|------------- |
| 1 | KB3063418 | 軟體更新 | 定期 |
| 2 | KB3043005 | LSI SAS 控制器更新 | 定期 |
| 3 | KB3063416 | 磁碟韌體 | 維護 |

使用此程序套用更新之前，請確認：

- 這兩個裝置控制器都在線上。

請執行下列步驟，以套用 Update 1.2。**更新可能需要大約 2 小時才能完成 (軟體需要約 30 分鐘、驅動程式需要約 30 分鐘、磁碟韌體需要約 45 分鐘)。**

[AZURE.INCLUDE [storsimple-install-update-option1](../../includes/storsimple-install-update-option1.md)]


## 選項 2：在移除閘道器組態之後使用 Azure 傳統入口網站套用 Update 1.2

此程序僅適用於執行 Update 1 之前軟體版本，並在 DATA 0 以外的網路介面上設定閘道器的 StorSimple 裝置。您必須在套用更新之前清除閘道器設定。

更新可能需要花幾個小時才能完成。如果您的主機位於不同的子網路，移除 iSCSI 介面上的閘道器設定可能會導致停機。建議您為 iSCSI 流量設定 DATA 0，以減少停機。

執行下列步驟來透過閘道器停用網路介面，然後再套用更新。

[AZURE.INCLUDE [storsimple-install-update-option2](../../includes/storsimple-install-update-option2.md)]

[AZURE.INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]


## 後續步驟

深入了解 [Update 1.2 版](storsimple-update1-release-notes.md)。

<!---HONumber=AcomDC_0323_2016-->