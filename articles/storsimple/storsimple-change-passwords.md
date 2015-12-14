<properties 
   pageTitle="變更 StorSimple 密碼 | Microsoft Azure" 
   description="描述如何使用 StorSimple Manager 服務變更 StorSimple Snapshot Manager 與裝置系統管理員密碼。" 
   services="storsimple" 
   documentationCenter="NA" 
   authors="SharS" 
   manager="carolz" 
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD" 
   ms.date="12/02/2015"
   ms.author="v-sharos@microsoft.com"/>

# 使用 StorSimple Manager 服務變更 StorSimple 密碼

## 概觀 

Azure 傳統入口網站 [設定]頁面包含所有裝置參數，可讓您重新設定 StorSimple Manager 服務所管理的 StorSimple 裝置。本教學課程說明如何使用 [**設定**] 頁面變更 StorSimple Snapshot Manager 或裝置系統管理員密碼。

## 變更 StorSimple Snapshot Manager 密碼

StorSimple Snapshot Manager 軟體位於您的 Windows 主機上，而且可讓系統管理員以本機和雲端快照的形式管理 StorSimple 裝置的備份。

當您在 StorSimple Snapshot Manager 中設定裝置時，系統將提示您提供裝置 IP 位址和密碼來驗證您的儲存裝置。此密碼最初是透過 Windows PowerShell 介面來設定。如需詳細資訊，請參閱[部署您的內部部署 StorSimple 裝置](storsimple-deployment-walkthrough.md)中的[步驟 3：透過 Windows PowerShell for StorSimple 設定和註冊裝置](storsimple-deployment-walkthrough.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple)。

然後可以透過傳統入口網站，變更在註冊期間先透過 Windows PowerShell 介面設定的密碼。執行下列步驟來變更 StorSimple Snapshot Manager 密碼。

#### 若要變更 StorSimple Snapshot Manager 密碼：

1. 在傳統入口網站中，對您的裝置按一下 [裝置] > [設定]。

2. 向下捲動到 [**StorSimple Snapshot Manager**] 區段。輸入 14 或 15 個字元的密碼。請確定密碼包含大寫、小寫、數字和特殊字元的組合。

3. 確認密碼。

4. 按一下頁面底部的 [儲存]。

StorSimple Snapshot Manager 密碼現在應該已更新。
 
## 變更裝置系統管理員密碼

當您使用 Windows PowerShell 介面來存取 StorSimple 裝置時，需要輸入裝置系統管理員密碼。向服務註冊第一個 StorSimple 裝置時，此介面的預設密碼為 *Password1*。為了確保資料的安全性，您必須在註冊程序結束時變更此密碼。若未變更此密碼，您就無法結束註冊程序。如需詳細資訊，請參閱[部署您的內部部署 StorSimple 裝置](storsimple-deployment-walkthrough.md)中的[步驟 3：透過 Windows PowerShell for StorSimple 設定和註冊裝置](storsimple-deployment-walkthrough.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple)。

然後可以透過 Azure 傳統入口網站，變更在註冊期間先透過 Windows PowerShell 介面設定的密碼。執行下列步驟來變更裝置系統管理員密碼。

#### 若要變更裝置系統管理員密碼：

1. 在傳統入口網站中，對您的裝置按一下 [裝置] > [設定]。

2. 向下捲動至 [**裝置系統管理員密碼**] 區段。提供含有 8 到 15 個字元的系統管理員密碼。密碼必須是大寫、小寫、數字和特殊字元的組合。

3. 確認密碼。

4. 按一下頁面底部的 [儲存]。

現在應該已更新裝置系統管理員密碼。您可以使用此修改過的密碼來存取 Windows PowerShell 介面。

## 後續步驟

- 深入了解 [StorSimple 安全性](storsimple-security.md)。

- [深入了解修改您的裝置組態](storsimple-modify-device-config.md)。

- 深入了解[使用 StorSimple Manager 服務管理 StorSimple 裝置](storsimple-manager-service-administration.md)。

<!---HONumber=AcomDC_1203_2015-->