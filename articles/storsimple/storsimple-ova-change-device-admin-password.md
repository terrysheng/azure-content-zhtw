<properties 
   pageTitle="變更 StorSimple 虛擬裝置系統管理員密碼 | Microsoft Azure"
   description="描述如何使用 Azure 傳統入口網站或 StorSimple Virtual Array 的 Web UI 來變更裝置系統管理員密碼。"
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
   ms.date="01/15/2016"
   ms.author="alkohli" />

# 變更 StorSimple Virtual Array 裝置系統管理員密碼 (預覽)

## 概觀

當您使用 Windows PowerShell 介面來存取 StorSimple 虛擬裝置時，需要輸入裝置系統管理員密碼。StorSimple 裝置第一次佈建並啟動時，預設的密碼是 Password1。為了資料的安全性，您第一次登入時預設密碼便會過期，且系統會要求您變更密碼。

裝置部署到生產環境之後，您隨時可以使用本機 Web UI 或 Azure 傳統入口網站變更裝置系統管理員密碼。本文章說明上述各程序。

## 使用 Azure 傳統入口網站變更密碼

執行下列步驟來透過 Azure 傳統入口網站變更裝置系統管理員密碼。

#### 透過 Azure 傳統入口網站變更裝置系統管理員密碼

1. 在入口網站中，對您的裝置按一下 [裝置] > [組態]。

2. 向下捲動至 [**裝置系統管理員密碼**] 區段。提供含有 8 到 15 個字元的系統管理員密碼。密碼必須是大寫、小寫、數字和特殊字元的組合。

3. 確認密碼。

4. 按一下頁面底部的 [儲存]。

現在應該已更新裝置系統管理員密碼。您可以使用修改的密碼在本機存取該裝置。

## 使用 StorSimple Virtual Array 的 Web UI 變更密碼

執行下列步驟來透過本機 Web UI 變更裝置系統管理員密碼。

#### 透過本機 Web UI 變更裝置系統管理員密碼

1. 在本機 Web UI 中，對您的裝置按一下 [維護] > [變更密碼]。

    ![變更 password1](./media/storsimple-ova-change-device-admin-password/image40.png)

2. 輸入**目前的密碼**。

3. 提供**新密碼**。密碼長度必須至少為 8 個字元。密碼必須包含下列 4 項的其中 3 項：大寫、小寫、數字和特殊字元。

    請注意，您的密碼不能與最近的 24 個密碼相同。

3. 請重新輸入密碼來加以確認。

    ![變更 password2](./media/storsimple-ova-change-device-admin-password/image41.png)

4. 在頁面底部，按一下 [套用]。將會套用新密碼。如果變更密碼未成功，您將看到下列錯誤訊息。

    ![密碼錯誤](./media/storsimple-ova-change-device-admin-password/image42.png)

    成功變更密碼之後，您將會收到通知。您即可使用修改的密碼在本機存取該裝置。

## 後續步驟

深入了解 [administering your StorSimple Virtual Array (管理 StorSimple Virtual Array)](storsimple-ova-web-ui-admin.md)。

<!---HONumber=AcomDC_0121_2016-->