<properties 
   pageTitle="部署 StorSimple Virtual Array 的 StorSimple Manager 服務 | Microsoft Azure"
   description="說明如何在 Azure 傳統入口網站中建立和刪除 StorSimple Manager 服務，並說明如何管理服務註冊金鑰。"
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
   ms.date="01/26/2016"
   ms.author="alkohli" />

# 部署 StorSimple Virtual Array 的 StorSimple Manager 服務

## 概觀

StorSimple Manager 服務可在 Microsoft Azure 中執行，並且連接至多個 StorSimple 裝置。建立服務之後，您可以使用服務從在瀏覽器中執行的 Microsoft Azure 傳統入口網站管理這些裝置。這可讓您從單一集中位置監視連線至 StorSimple Manager 服務的所有裝置，所以可以盡可能降低管理負擔。

StorSimple Manager 登陸頁面會列出所有 StorSimple Manager 服務，您可用來管理您的 StorSimple 儲存體裝置。針對每個 StorSimple Manager 服務，下列資訊會顯示在 StorSimple Manager 頁面上：

- **名稱** – 在建立時指派給您的 StorSimple Manager 服務的名稱。建立服務之後，無法變更服務名稱。

- **狀態** – 服務的狀態，可以是 [**作用中**]、[**建立**] 或是 [**線上**]。

- **位置** – 部署 StorSimple 裝置所在的地理位置。

- **訂用帳戶** – 與您的服務相關聯的計費訂用帳戶。

可以透過 StorSimple Manager 頁面執行的一般工作包括：

- 建立服務
- 刪除服務
- 取得服務註冊金鑰。
- 重新產生服務註冊金鑰

本教學課程說明如何執行這些工作。

## 建立服務

如果您想要部署您的 StorSimple 裝置，使用 [**快速建立**] 選項來建立 StorSimple Manager 服務。若要建立服務，您需要：

- 具有企業合約的訂用帳戶
- 使用中的 Microsoft Azure 儲存體帳戶
- 用於存取管理的計費資訊

您也可以選擇在建立服務時產生預設儲存體帳戶。

單一服務可以管理多個裝置。不過，裝置不能跨越多個服務。大型企業可以擁有多個服務執行個體使用不同的訂用帳戶、組織或甚至是部署位置。

執行下列步驟來建立服務。

[AZURE.INCLUDE [storsimple-ova-create-new-service](../../includes/storsimple-ova-create-new-service.md)]

## 刪除服務

刪除服務之前，請確定沒有任何連接的裝置正在使用它。如果服務正在使用中，請停用連接的裝置。停用作業將會斷絕裝置與服務之間的連接，但是會保留雲端中的裝置資料。

> [AZURE.IMPORTANT] 刪除服務之後，就無法回復此作業。

執行下列步驟來刪除服務。

### 刪除服務

1. 在 [**StorSimple Manager 服務**] 頁面上，選取您要刪除的服務。

1. 按一下頁面底部的 [**刪除**]。

1. 在確認通知處按一下 [**是**]。刪除服務可能需要數分鐘的時間。

## 取得服務註冊金鑰。

在您成功建立服務之後，您必須在 StorSimple 裝置註冊服務。若要註冊您的第一個 StorSimple 裝置，您必須使用服務註冊金鑰。若要使用現有的 StorSimple 服務註冊其他裝置，您需要註冊金鑰和服務資料加密金鑰 (在註冊期間於第一個裝置上產生)。如需服務資料加密金鑰的詳細資訊，請參閱[從本機 Web UI 取得服務資料加密金鑰](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key)。

執行下列步驟以取得服務註冊金鑰。

[AZURE.INCLUDE [storsimple-ova-get-service-registration-key](../../includes/storsimple-ova-get-service-registration-key.md)]

將服務註冊金鑰保存在安全的位置。您需要這個金鑰，以及服務資料加密金鑰，才能對額外裝置註冊此服務。取得服務註冊金鑰之後，您必須透過 Windows PowerShell for StorSimple 介面設定裝置。

## 重新產生服務註冊金鑰

如果您必須執行金鑰替換或服務系統管理員的清單已變更，則您必須重新產生服務註冊金鑰。當您重新產生金鑰時，新的金鑰僅用於註冊後續裝置。已註冊的裝置不會受到此程序影響。

執行下列步驟以重新產生服務註冊金鑰。

### 重新產生服務註冊金鑰

1. 在 [**StorSimple Manager 服務**] 頁面上，按一下 [**註冊金鑰**]。

1. 在 [**服務註冊金鑰**] 對話方塊中，按一下 [**重新產生**]。

1. 您將會看見確認訊息。按一下 [**確定**] 繼續重新產生。

1. 新的服務註冊金鑰隨即顯示。

1. 複製這個金鑰並儲存，以對任何新的裝置註冊此服務。

1. 按一下核取圖示 ![核取圖示](./media/storsimple-ova-manage-service/image7.png) 以關閉此對話方塊。


## 後續步驟

- 深入了解如何[開始](storsimple-ova-deploy1-portal-prep.md)部署 StorSimple Virtual Array。
	
- 深入了解如何[使用 StorSimple Manager 服務管理 StorSimple 裝置](storsimple-ova-manager-service-administration.md)。

 

<!---HONumber=AcomDC_0128_2016-->