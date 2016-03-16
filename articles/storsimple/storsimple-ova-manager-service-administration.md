<properties 
   pageTitle="StorSimple Manager Virtual Array 管理 | Microsoft Azure"
   description="了解如何使用 Azure 傳統入口網站中的 StorSimple Manager 服務管理 StorSimple 內部部署 Virtual Array。"
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/01/2016"
   ms.author="alkohli" />

# 使用 StorSimple Manager 服務來管理 StorSimple Virtual Array

![安裝程序流程](./media/storsimple-ova-manager-service-administration/manage4.png)

## 概觀

本文描述 StorSimple Manager 服務介面，包括如何與其連接、各種可用選項，以及提供可以透過此 UI 執行的特定工作流程連結。

閱讀本文之後，您將了解如何：

- 連接至 StorSimple Manager 服務
- 瀏覽 StorSimple Manager UI
- 透過 StorSimple Manager 服務管理 StorSimple Virtual Array

> [AZURE.NOTE] 若要檢視 StorSimple 8000 系列裝置可用的管理選項，請移至[使用 StorSimple Manager 服務管理 StorSimple 裝置](storsimple-manager-service-administration.md)。

## 連接至 StorSimple Manager 服務

StorSimple Manager 服務可在 Microsoft Azure 中執行，並且連接至多個 StorSimple Virtual Array。您可以使用在瀏覽器中執行的中央 Microsoft Azure 傳統入口網站來管理這些裝置。若要連接道 StorSimple Manager 服務，請執行下列動作。

#### 連接至此服務

1. 移至 [https://manage.windowsazure.com/](https://manage.windowsazure.com/)。

2. 使用您的 Microsoft 帳戶認證，登入 Microsoft Azure 傳統入口網站 (位於窗格右上角)。

3. 向下捲動左方瀏覽窗格以存取 StorSimple Manager 服務。

    ![捲動到服務](./media/storsimple-ova-manager-service-administration/admin-scroll.png)

## 瀏覽 StorSimple Manager 服務 UI

StorSimple Manager 服務 UI 的瀏覽階層顯示於下表。

- **StorSimple Manager** 登陸頁面將帶您到適用於服務內所有 Virtual Array 的 UI 服務層級頁面。

- [裝置] 頁面會將您導向適用於特定 Virtual Array 的裝置層級 UI 頁面。

#### StorSimple Manager 服務瀏覽階層

|登陸頁面|服務層級頁面|裝置層級頁面|
|---|---|---|
|StorSimple Manager 服務|儀表板 (服務)|儀表板 (裝置)|
||裝置 →|監視|
||備份類別目錄|共用 (檔案伺服器) 或</br>磁碟區 (iSCSI 伺服器)|
||設定 (服務)|設定 (裝置)|
||作業|維護|
||Alerts|

## 使用 StorSimple Manager 服務來執行管理工作

下表顯示所有一般管理工作和複雜工作流程 (可在 StorSimple Manager 服務 UI 內執行) 的摘要。這些工作會根據在其中啟動它們的 UI 頁面加以組織。

如需每個工作流程的詳細資訊，請按一下資料表中的適當程序。

#### StorSimple Manager 工作流程

|如果您想要執行此動作...|請移至此 UI 頁面...|使用此程序|
|---|---|---|
|建立服務</br>刪除服務</br>取得服務註冊金鑰</br>重新產生服務註冊金鑰|StorSimple Manager 服務|[部署 StorSimple Manager 服務](storsimple-ova-manage-service.md)|
|變更服務資料加密金鑰</br>檢視作業記錄檔|StorSimple Manager 服務 → 儀表板|[使用 StorSimple 服務儀表板](storsimple-ova-service-dashboard.md)|
|停用 Virtual Array</br>刪除 Virtual Array|StorSimple Manager 服務 → 裝置|[停用或刪除 Virtual Array](storsimple-ova-deactivate-and-delete-device.md)|
|災害復原和裝置容錯移轉</br>容錯移轉必要條件</br>容錯移轉至虛擬裝置</br>業務持續性災害復原 (BCDR)</br>災害復原時發生錯誤|StorSimple Manager 服務 → 裝置|[StorSimple Virtual Array 的災害復原和裝置容錯移轉](storsimple-ova-failover-dr.md)|
|備份共用和磁碟區</br>進行手動備份</br>變更備份排程</br>檢視現有的備份|StorSimple Manager 服務 → 備份目錄|[備份 StorSimple Virtual Array](storsimple-ova-backup.md)|
|從備份組還原共用</br>從備份組還原磁碟區</br>項目層級復原 (僅限檔案伺服器)|StorSimple Manager 服務 → 備份目錄|[從 StorSimple Virtual Array 備份中還原](storsimple-ova-restore.md)|
|有關儲存體帳戶</br>加入儲存體帳戶</br>編輯儲存體帳戶</br>刪除儲存體帳戶|StorSimple Manager 服務 → 設定|[管理 StorSimple Virtual Array 的儲存體帳戶](storsimple-ova-manage-storage-accounts.md)|
|關於存取控制記錄</br>加入或修改存取控制記錄</br>刪除存取控制記錄|StorSimple Manager 服務 → 設定|[管理 StorSimple Virtual Array 的存取控制記錄](storsimple-ova-manage-acrs.md)|
|檢視工作詳細資料|StorSimple Manager 服務 → 工作| [管理 StorSimple Virtual Array 作業](storsimple-ova-manage-jobs.md)|
|設定警示設定</br>接收警示通知</br>管理警示</br>檢閱警示|StorSimple Manager 服務 → 警示|[檢視和管理 StorSimple Virtual Array 的警示](storsimple-ova-manage-alerts.md)|
|修改裝置系統管理員密碼|StorSimple Manager 服務 → 裝置 → 設定|[變更 StorSimple Virtual Array 裝置系統管理員密碼](storsimple-ova-change-device-admin-password.md)|
|安裝軟體更新|StorSimple Manager 服務 → 裝置 → 維護|[更新 Virtual Array](storsimple-ova-update.md)|

>[AZURE.NOTE] 您必須使用[本機 Web UI](storsimple-ova-web-ui-admin.md) 以執行下列工作：
>
>- [擷取服務資料加密金鑰](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key)
>- [建立支援封裝](storsimple-ova-web-ui-admin.md#generate-a-log-package)
>- [停止和重新啟動 Virtual Array](storsimple-ova-web-ui-admin.md#shut-down-and-restart-your-device)

##後續步驟
如需 Web UI 及如何使用的詳細資訊，請移至[使用 StorSimple Web UI 管理 StorSimple Virtual Array](storsimple-ova-web-ui-admin.md)。

<!---HONumber=AcomDC_0302_2016-------->