<properties 
   pageTitle="連絡 Microsoft 支援服務 | Microsoft Azure"
   description="了解如何建立支援要求和在 StorSimple 裝置上啟動支援工作階段。"
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carolz"
   editor="tysonn" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/24/2015"
   ms.author="alkohli" />

# 連絡 Microsoft 支援服務

如果您使用 Microsoft Azure StorSimple 解決方案時遇到任何問題，您可以向技術支援人員提出服務要求。在與支援工程師進行線上工作階段時，可能也需要在您的 StorSimple 裝置上啟動支援工作階段。本文將逐步引導您建立支援要求，以及在 StorSimple 裝置的 Windows PowerShell 介面中啟動支援工作階段。

## 建立支援要求

執行下列步驟來建立支援要求：

#### 建立支援要求

1. 您可以透過[管理入口網站](http://manage.windowsazure.com/)建立支援要求。在[管理入口網站](http://manage.windowsazure.com/)中，按一下您的 [**帳戶名稱**]，然後按一下 [**連絡 Microsoft 支援服務**]。

	![透過 ManagementPortal 連絡 MS 支援服務](./media/storsimple-contact-microsoft-support/IC777286.png)

1. 在 [**連絡 Microsoft 支援服務**] 對話方塊中：


	- 從下拉式清單中，選取與 StorSimple Manager 服務相關聯的目標**訂用帳戶**。指定 [**支援類型**] 為 [**技術**]。您必須已付費購買支援方案，才能啟用技術支援。

	1. 按一下 [**建立票證**] 的核取圖示 ![核取圖示](./media/storsimple-contact-microsoft-support/IC740895.png)。

1. 在 [**Microsoft 支援服務**] 視窗中，從 [**產品**] 下拉式清單中選擇 [**StorSimple**]。

	![連絡 Microsoft 支援服務 - 產品](./media/storsimple-contact-microsoft-support/IC777288.png)

1. 遵循螢幕上的指示，將您的要求正確分類，並清楚、明確地描述您的問題。

提交您的要求之後，支援工程師會儘速連絡您來處理您的要求。

## 在 Windows PowerShell for StorSimple 中啟動支援工作階段

若要對您使用 StorSimple 裝置時可能會遇到的任何問題進行疑難排解，您必須連絡 Microsoft 支援服務小組。Microsoft 支援服務可能需要使用支援工作階段來登入您的裝置。

執行下列步驟來啟動支援工作階段：

#### 啟動支援工作階段

1. 從遠端電腦使用序列主控台或透過 telnet 工作階段，直接存取裝置。如果要這樣做，請依照[使用 PuTTY 來連接至裝置序列主控台](storsimple-deployment-walkthrough.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple)中的步驟進行。

1. 在開啟的工作階段中，按 **Enter** 鍵取得命令提示字元。

1. 在序列主控台功能表中，選取選項 1 [使用完整存取權登入]。

1. 在提示字元中輸入下列密碼：

	`Password1`

1. 在出現提示時輸入下列命令：

	`Enable-HcsSupportAccess`

1. 您會看到加密的字串。將此字串複製到 [記事本] 之類的文字編輯器。

1. 儲存這個字串，並透過電子郵件傳送給 Microsoft 支援服務。

> [AZURE.IMPORTANT]您可以執行 `Disable-HcsSupportAccess` 停用支援存取。在起始工作階段之後 8 小時，StorSimple 裝置也會嘗試停用支援存取。最好在起始支援工作階段之後變更 StorSimple 裝置認證。

<!---HONumber=August15_HO6-->