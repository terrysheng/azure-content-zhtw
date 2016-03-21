<properties 
   pageTitle="管理 StorSimple 儲存體帳戶 | Microsoft Azure"
   description="說明如何使用 StorSimple Manager 的 [設定] 頁面，來為與 StorSimple Virtual Array 相關聯的儲存體帳戶新增、編輯、刪除或替換安全性金鑰。"
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
   ms.date="03/02/2016"
   ms.author="alkohli" />

# 使用 StorSimple Manager 服務管理儲存體帳戶

## 概觀

[設定] 頁面會顯示可在 StorSimple Manager 服務中建立的全域服務參數。這些參數可以套用到與該服務連線的所有裝置，還包括：

- 儲存體帳戶 
- 存取控制記錄 

本教學課程說明如何使用 [設定] 頁面，來為您的 StorSimple Virtual Array 新增、編輯或刪除儲存體帳戶。本教學課程中的資訊僅適用於執行 2016 年 3 月 GA 版軟體的 StorSimple Virtual Array。

 ![[設定] 頁面](./media/storsimple-ova-manage-storage-accounts/configure_service_page.png)

儲存體帳戶包含的認證可供裝置用來存取雲端服務提供者的儲存體帳戶。對於 Microsoft Azure 儲存體帳戶，像是帳戶名稱與主要存取金鑰就屬於這些認證。

在 [設定] 頁面上，為訂用帳戶計費而建立的所有儲存體帳戶都會以表格顯示，其中包含下列資訊：

- **名稱** – 帳戶建立時獲指派的唯一名稱。
- **啟用 SSL** – 是否已啟用 SSL 並透過安全通道進行裝置對雲端的通訊。

最常見可在 [設定] 頁面上執行與儲存體帳戶相關的工作如下：

- 新增儲存體帳戶 
- 編輯儲存體帳戶 
- 刪除儲存體帳戶 


## 儲存體帳戶類型

有三種儲存體帳戶類型能與 StorSimple 裝置搭配使用。

- **自動產生的儲存體帳戶** – 正如其名，這類型的儲存體帳戶是在初次建立服務時自動產生。如要深入了解此儲存體帳戶的建立方式，請參閱[建立新的服務](storsimple-ova-manage-service.md#create-a-service)。 
- **服務訂用帳戶中的儲存體帳戶** – 這些是與相同服務訂用帳戶相關聯的 Azure 儲存體帳戶。若要深入了解如何建立這些儲存體帳戶，請參閱[關於 Azure 儲存體帳戶](../storage/storage-create-storage-account.md)。 
- **服務訂用帳戶外的儲存體帳戶** – 這些是與服務毫無關聯的 Azure 儲存體帳戶，而且可能在服務建立之前便已存在 。

## 新增儲存體帳戶

您可以在 StorSimple Manager 服務組態新增儲存體帳戶，方法是提供唯一的易記名稱，以及與儲存體帳戶相關的存取認證。您也能選擇啟用安全通訊端層 (SSL) 模式，建立裝置與雲端之間網路通訊的安全通道。

您可以為指定的雲端服務提供者建立多個帳戶。儲存體帳戶在儲存時，服務會嘗試與您的雲端服務提供者通訊。此時會驗證您提供的認證與存取資料。只有當驗證成功時，才會建立儲存體帳戶。如果驗證失敗，則會顯示適當的錯誤訊息。

新增儲存體帳戶的程序詳述如下。

[AZURE.INCLUDE [add-a-storage-account](../../includes/storsimple-ova-configure-new-storage-account.md)]

## 編輯儲存體帳戶

您可以編輯您裝置使用的儲存體帳戶。如果您編輯的儲存體帳戶目前正在使用中，您就只能修改該儲存體帳戶的存取金鑰和 SSL 模式欄位。您可以提供新的儲存體存取金鑰，或是修改 [啟用 SSL 模式] 的選取項目，並儲存已更新的設定。

#### 若要編輯儲存體帳戶

1. 在服務登陸頁面上，選取您的服務，連按兩下該服務名稱，然後按一下 [設定]。

2. 按一下 [新增/編輯儲存體帳戶]。

3. 在 [新增/編輯儲存體帳戶] 對話方塊中：

  1. 在 [儲存體帳戶] 下拉式清單中，選擇您想要修改的現有帳戶。
  2. 如有必要，您可以修改 [啟用 SSL 模式] 選項。
  3. 您可以選擇重新產生儲存體帳戶的存取金鑰。如需詳細資訊，請參閱[重新產生儲存體帳戶金鑰](storage-create-storage-account.md#manage-your-storage-access-keys)。請提供新的儲存體帳戶金鑰。而每個 Azure 儲存體帳戶，都有主要存取金鑰。 
  4. 按一下核取圖示 ![核取圖示](./media/storsimple-ova-manage-storage-accounts/checkicon.png) 以儲存設定。[設定] 頁面上的設定隨即更新。 
  5. 按一下頁面底部的 [儲存]，來儲存剛才更新的設定。 

     ![編輯儲存體帳戶](./media/storsimple-ova-manage-storage-accounts/modifyexistingstorageaccount.png)
  
## 刪除儲存體帳戶

> [AZURE.IMPORTANT] 您只能刪除非使用中的儲存體帳戶。如果您要刪除的儲存體帳戶正在使用中，系統會通知您。

#### 若要刪除儲存體帳戶

1. 在 StorSimple Manager 服務登陸頁面上，選取您的服務，連按兩下該服務名稱，然後按一下 [設定]。

2. 在儲存體帳戶的表格式清單中，將滑鼠停留在您想要刪除的帳戶上方。

3. 刪除圖示 (**x**) 會出現在該儲存體帳戶資料行的最右邊。按一下 [x] 圖示，以刪除認證。

4. 當系統提示您確認時，按一下 [是] 繼續進行刪除。表格式清單會更新以反映所做的變更。

5. 按一下頁面底部的 [儲存]，來儲存剛才更新的設定。


## 後續步驟

- 了解如何[管理 StorSimple Virtual Array](storsimple-ova-web-ui-admin.md)。

<!---HONumber=AcomDC_0309_2016-->