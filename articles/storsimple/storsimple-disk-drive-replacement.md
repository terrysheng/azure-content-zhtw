<properties 
   pageTitle="更換 StorSimple 裝置上的磁碟機 | Microsoft Azure"
   description="說明如何更換 StorSimple 主要機箱或 EBOD 機箱上的磁碟機。"
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="03/28/2016"
   ms.author="alkohli" />

# 更換 StorSimple 裝置上的磁碟機

## 概觀

本教學課程說明如何取下和更換 Microsoft Azure StorSimple 裝置上無法運作或故障的硬碟。若要取下磁碟機，您必須：

- 打開防拆鎖

- 取下磁碟機

- 安裝更換磁碟機

>[AZURE.IMPORTANT] 取下及更換磁碟機之前，請閱讀 [StorSimple 硬體元件更換](storsimple-hardware-component-replacement.md)中的安全資訊。

## 打開防拆鎖

此程序說明在更換磁碟機時如何扣上或打開 StorSimple 裝置上的防拆鎖。防拆鎖固定在磁碟機的載具把手，而且它們是透過把手的閂鎖部件中的小孔來存取。提供磁碟機時已將鎖設定為鎖定的位置。

#### 若要解除鎖定防拆鎖

1. 小心地將鎖鑰匙 (Microsoft 提供的「防拆」T10 螺絲起子) 插入把手中的孔中，並插入其插座中。 

    >[AZURE.NOTE] 如果防拆鎖已啟動，則可在孔中看到紅色指示器。

    ![已鎖定磁碟機](./media/storsimple-disk-drive-replacement/IC741056.png)

    **圖 1** 防拆鎖已扣上

    |標籤|說明|
    |:----|:----------|
    |1|指示器孔|
    |2|防拆鎖|

2. 以逆時鐘方向旋轉鑰匙，直到在鑰匙上方的孔中看不到紅色指示器。

3. 取下鑰匙。

    ![已解除鎖定磁碟機](./media/storsimple-disk-drive-replacement/IC741057.png)

    **圖 2** 已解除鎖定磁碟機

4. 現在可以取下磁碟機。

請反向依照步驟來扣上鎖。

## 取下磁碟機

您的 StorSimple 裝置支援如 RAID 10 的儲存空間組態。這表示它可以在一個磁碟、固態硬碟 (SSD) 或硬碟 (HDD) 故障的情況下正常操作。

>[AZURE.IMPORTANT]
>
>- 如果您的系統有一個以上故障的磁碟，請勿於任何時間點從系統移除一個以上的 SSD 或 HDD。這樣做可能會導致資料遺失。
>
>- 請確定您將更換 SSD 放置在先前包含 SSD 的插槽中。同樣地，請將更換 HDD 放置在先前包含 HDD 的插槽中。
>
>- 在傳統入口網站中，插槽的編號從 0 – 11。因此，如果入口網站顯示插槽 2 中的磁碟故障，請在裝置上從左上角算起的第三個插槽中尋找故障的磁碟。

您可以在系統操作時取下和更換磁碟機。

#### 若要取下磁碟機

1. 若要識別故障的磁碟，請在 Azure 傳統入口網站中，移至 [裝置] > [維護] > [硬體狀態]。因為主要機箱和/或 EBOD 機箱 (如果您是使用 8600 機型) 中的磁碟可能故障，請查看 [**共用元件**] 和 [**EBOD 機箱共用元件**] 下的磁碟狀態。任一機箱中故障的磁碟將以紅色狀態顯示。

2. 找出主要機箱或 EBOD 機箱前面的磁碟機。

3. 如果磁碟已解除鎖定，請繼續下一個步驟。如果磁碟已鎖定，請遵循[打開防拆鎖](#disengage-the-antitamper-lock)中的程序，將它解除鎖定。

4. 按壓磁碟機載具模組上的黑色閂鎖，然後拉出磁碟機載具把手，並從底座前端移走。

    ![鬆開磁碟機把手](./media/storsimple-disk-drive-replacement/IC741051.png)

    **圖 3** 鬆開磁碟機把手

5. 當磁碟機載具把手完全伸展時，請將磁碟機載具滑出底座。

    ![將磁碟滑出磁碟機](./media/storsimple-disk-drive-replacement/IC741052.png)
    
    **圖 4** 將磁碟機滑出載具

## 安裝更換磁碟機

在 StorSimple 裝置中的磁碟機故障，而且您已取下它之後，請遵循此程序，將它更換為新的磁碟機。

#### 若要插入磁碟機

1. 確定磁碟機載具把手已完全伸展，如下圖所示。

    ![把手已伸展的磁碟機](./media/storsimple-disk-drive-replacement/IC741044.png)

    **圖 5** 把手已伸展的磁碟機

2. 將磁碟機載具完全滑入底座。

    ![將磁碟滑入磁碟機載具](./media/storsimple-disk-drive-replacement/IC741045.png)

    **圖 6** 將磁碟機載具滑入底座

3. 一旦插入磁碟機載具，請關閉磁碟機載具把手，同時繼續將磁碟機載具推入底座，直到磁碟機載具把手卡入鎖定的位置。

4. 使用 Microsoft 所提供的鎖鑰匙 (防拆 Torx 螺絲起子)，將鎖螺絲順時鐘方向旋轉四分之一來固定住載具把手。

5. 存取 Azure 傳統入口網站，並導覽至 [維護] > [硬體狀態]，來確認更換成功，而且磁碟機可運作。在 [**共用元件**] 或 [**EBOD 機箱共用元件**] 下，磁碟機狀態應該是綠色,表示狀況良好。

    >[AZURE.NOTE] 在更換之後，可能需要數小時，磁碟狀態才會變成綠色。

## 後續步驟

深入了解 [StorSimple 硬體元件更換](storsimple-hardware-component-replacement.md)。

<!---HONumber=AcomDC_0330_2016-->