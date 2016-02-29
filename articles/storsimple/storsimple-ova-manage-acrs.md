<properties 
   pageTitle="管理 StorSimple Virtual Array 的存取控制記錄 | Microsoft Azure"
   description="說明如何使用存取控制記錄 (ACR) 以判斷哪些主機可以連接至 StorSimple Virtual Array 上的磁碟區。"
   services="storsimple"
   documentationCenter=""
   authors="SharS"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/12/2016"
   ms.author="v-sharos" />

# 針對 StorSimple Virtual Array使用 StorSimple Manager 服務管理存取控制記錄 (預覽)

## 概觀

存取控制記錄 (ACR) 可讓您指定哪些主機可連接至 StorSimple Virtual Array (也稱為 StorSimple 內部部署虛擬裝置) 上的磁碟區。ACR 設為特定的磁碟區，並且包含主機的 iSCSI 限定名稱 (IQN)。當主機嘗試連線到磁碟區時，裝置會檢查與該磁碟區相關聯的 ACR 的 IQN 名稱，如果相符，則會建立連接。[設定] 頁面上的 [存取控制記錄] 區段會顯示具有主機對應 IQN 的所有存取控制記錄。

本教學課程將說明下列常見 ACR 相關工作：

- 取得 IQN
- 加入存取控制記錄 
- 編輯存取控制記錄 
- 刪除存取控制記錄 

> [AZURE.IMPORTANT] 
> 
> - 將 ACR 指派到磁碟區時，請注意磁碟區並未被多個非叢集主機並行存取，因為這可能會損毀磁碟區。 
> - 從磁碟區刪除 ACR 時，請確定對應的主機未存取磁碟區，因為刪除作業可能會導致讀寫中斷。

## 取得 IQN

請執行下列步驟，以取得正在執行 Windows Server 2012 之 Windows 主機的 IQN。

[AZURE.INCLUDE [storsimple-get-iqn](../../includes/storsimple-get-iqn.md)]

## 加入 ACR

使用 StorSimple Manager 服務的 [組態] 頁面加入 ACR。一般而言，您會讓一個 ACR 與一個磁碟區產生關聯。

如需讓 ACR 與磁碟區產生關聯的詳細資訊，請移至[使用 StorSimple Manager 服務來管理 StorSimple Virtual Array 上的磁碟區](storsimple-ova-manage-volumes)。

>[AZURE.IMPORTANT] 
> 
>將 ACR 指派到磁碟區時，請注意磁碟區並未被多個非叢集主機並行存取，因為這可能會損毀磁碟區。
 
執行下列步驟以加入 ACR。

#### 加入 ACR

1. 在服務登陸頁面上，選取您的服務，連按兩下該服務名稱，然後按一下 [組態] 索引標籤。

    ![組態索引標籤](./media/storsimple-ova-manage-acrs/acr1.png)

2. 在 [**存取控制記錄**] 底下的表格式清單中，提供 ACR 的 [**名稱**]。

3. 在 [iSCSI 啟動器名稱] 下方，提供 Windows 主機的 IQN 名稱。

4. 按一下頁面底部的 [儲存] 以儲存新建立的 ACR。您會看到下列確認訊息。

    ![確認訊息](./media/storsimple-ova-manage-acrs/acr2.png)

5. 按一下核取圖示 ![核取圖示](./media/storsimple-ova-manage-acrs/check-icon.png)。表格式清單會更新以反映此新增。

## 編輯 ACR

您可以使用 Azure 傳統入口網站中的 [組態] 頁面編輯 ACR。

> [AZURE.NOTE] 您應該只修改目前未在使用中的 ACR。若要編輯與目前正在使用中的磁碟區相關聯的 ACR，您應該先讓磁碟區離線。

執行下列步驟以編輯 ACR。

#### 編輯 ACR

1. 在服務登陸頁面上，選取您的服務，連按兩下該服務名稱，然後按一下 [組態] 索引標籤。

2. 在存取控制記錄的表格式清單中，將滑鼠停留在您想要修改的 ACR 上。

3. 為 ACR 提供新的名稱和/或 IQN。

4. 按一下頁面底部的 [儲存] 以儲存修改過的 ACR。您將會看見確認訊息。

5. 按一下核取圖示 ![核取圖示](./media/storsimple-ova-manage-acrs/check-icon.png)。表格式清單會更新以反映此變更。

## 刪除存取控制記錄

您可以使用 Azure 傳統入口網站中的 [組態] 頁面刪除 ACR。

> [AZURE.NOTE] 
> 
> - 您應該只刪除目前未在使用中的 ACR。若要刪除與目前正在使用中的磁碟區相關聯的 ACR，您應該先讓磁碟區離線。
> - 從磁碟區刪除 ACR 時，請確定對應的主機未存取磁碟區，因為刪除作業可能會導致讀寫中斷。

執行下列步驟來刪除存取控制記錄。

#### 刪除存取控制記錄

1. 在服務登陸頁面上，選取您的服務，連按兩下該服務名稱，然後按一下 [組態] 索引標籤。

2. 在存取控制記錄 (ACR) 的表格式清單中，將滑鼠停留在您想要刪除的 ACR 上。

3. 刪除圖示 (**x**) 會出現在您選取之 ACR 資料行的最右邊。按一下 [**x**] 圖示，以刪除 ACR。您會看到下列確認訊息。

    ![確認訊息](./media/storsimple-ova-manage-acrs/acr3.png)

5. 按一下核取圖示 ![核取圖示](./media/storsimple-ova-manage-acrs/check-icon.png)。表格式清單會更新以反映刪除。

## 後續步驟

- 深入了解[加入磁碟區和設定 ACR](storsimple-ova-deploy3-iscsi-setup.md#step-3-add-a-volume)。

<!---HONumber=AcomDC_0218_2016-->