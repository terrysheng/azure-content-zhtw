<properties 
	pageTitle="適用於行動電話的 Azure Authenticator 應用程式" 
	description="了解如何升級至最新版的 Azure Authenticatior。" 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtland"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/24/2015" 
	ms.author="billmath"/>



# 移至新的 Azure Authenticator 應用程式

發行 Azure Authenticator 應用程式以供 [Windows Phone](http://www.windowsphone.com/zh-TW/store/app/azure-authenticator/03a5b2bf-6066-418f-b569-e8aecbc06e50)、[Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) 和 [IOS](https://itunes.apple.com/us/app/azure-authenticator/id983156458) 使用後，舊的 Multi-Factor Authentication 將會被取代。Multi-Factor Authentication 應用程式將會繼續運作，但您應該決定移至新的 Azure Authenticator 應用程式，這篇文章才對您有所幫助。


## 如何將移至新的 Azure Authenticator 應用程式 

**步驟 1：**安裝 Azure Authenticator。

![雲端](./media/multi-factor-authentication-azure-authenticator/home.png)

**步驟 2：**使用新的應用程式啟動您的帳戶

首先確定，您必須有 QR 代碼或程式碼和 URL，以便手動輸入您要加入至應用程式的帳戶。

> [AZURE.NOTE]不確定如何取得 QR 代碼？ 請連絡支援人員尋求協助。
> 
> 無法使用新的應用程式啟動您的帳戶？ 請連絡支援人員。
>


一旦您的面前有 QR 代碼，請啟動應用程式。按一下 +。


![Add Account](./media/multi-factor-authentication-azure-authenticator/addaccount.png)

這會啟動相機來掃描 QR 代碼。如果您無法掃描 QR 代碼，您永遠可以手動輸入。

若要確認已成功啟動帳戶，請確認新帳戶出現在 [帳戶] 頁面上。


針對您要移轉到新應用程式的所有帳戶執行此步驟。



**步驟 3：**從您的電話解除安裝舊版的 Multi-Factor Authentication 應用程式。

一旦將所有帳戶加入至新應用程式，請從您的電話解除安裝舊版的應用程式。

想知道如何移除舊應用程式中的個別帳戶？ 請點選該帳戶。您會取得 [刪除] 選項。

![移除帳戶](./media/multi-factor-authentication-azure-authenticator/remove.png)

## 如何使用條碼掃描器新增帳戶



- 首先，請移至您的安全性驗證設定頁面。如需如何進入此頁面的詳細資訊，請參閱[變更安全性設定](multi-factor-authentication-end-user-manage-settings.md)。

- 按一下 [設定] 按鈕。
 
![Add Account](./media/multi-factor-authentication-azure-authenticator/azureauthe.png)

- 這樣會顯示在上面具有條碼的螢幕。
  
![掃描條碼](./media/multi-factor-authentication-azure-authenticator/barcode2.png)

- 現在開啟 Azure 驗證器應用程式，您應該會進入 [帳戶] 頁面。您會在這裡看到您已設定的帳戶的清單。如果您想要新增新的帳戶，請按一下 + 符號。這會開啟掃描器。

![Add Account](./media/multi-factor-authentication-azure-authenticator/addaccount3.png)

- 掃描條碼。 

![Add Account](./media/multi-factor-authentication-azure-authenticator/scan.png)

- 請等候帳戶啟用。

- 就這麼簡單。您現在應該會在 [帳戶] 頁面上看到新的帳戶。

![Add Account](./media/multi-factor-authentication-azure-authenticator/addaccount2.png)


## 如何手動新增 Azure 帳戶

如果您想要手動新增帳戶，可以執行下列動作：

- 首先，請移至您的安全性驗證設定頁面。如需如何進入此頁面的詳細資訊，請參閱[變更安全性設定](multi-factor-authentication-end-user-manage-settings.md)。

- 按一下 [設定] 按鈕。
 
![Add Account](./media/multi-factor-authentication-azure-authenticator/azureauthe.png)

- 這樣會顯示在上面具有條碼的螢幕。請注意條碼底下的代碼和 URL。
  
![掃描條碼](./media/multi-factor-authentication-azure-authenticator/barcode2.png)

- 現在開啟 Azure 驗證器應用程式，您應該會進入 [帳戶] 頁面。您會在這裡看到您已設定的帳戶的清單。如果您想要新增新的帳戶，請按一下 + 符號。這會開啟掃描器。

![Add Account](./media/multi-factor-authentication-azure-authenticator/addaccount3.png)

- 按一下底部的 [手動輸入] 按鈕。

![Add Account](./media/multi-factor-authentication-azure-authenticator/scan.png)

- 輸入顯示條碼的同一頁所提供的代碼和 URL。此資料會填入行動應用程式中的代碼和 URL 方塊。隨即會開始啟用程序。

![Add Account](./media/multi-factor-authentication-azure-authenticator/manual.png)

- 請等候帳戶啟用。

- 就這麼簡單。您現在應該會在 [帳戶] 頁面上看到新的帳戶。

![Add Account](./media/multi-factor-authentication-azure-authenticator/addaccount2.png)

## 如何手動新增非 Azure 帳戶

如果您想要手動新增非 Azure 帳戶，例如您的 Microsoft 帳戶，可以執行下列動作：


- 首先，您必須從與帳戶相關聯的網站取得秘密金鑰。例如，在 Outlook.com 中移至您的帳戶設定、您的安全性設定，然後選取安裝驗證應用程式。您必須選取「我無法掃描條碼以取得密碼金鑰」。

![Add Account](./media/multi-factor-authentication-azure-authenticator/secretkey.png)

- 開啟 Azure 驗證器應用程式，您應該會進入 [帳戶] 頁面。您會在這裡看到您已設定的帳戶的清單。如果您想要新增新的帳戶，請按一下 + 符號。這會開啟掃描器。

![Add Account](./media/multi-factor-authentication-azure-authenticator/addaccount3.png)

- 按一下底部的 [手動輸入] 按鈕。

![Add Account](./media/multi-factor-authentication-azure-authenticator/scan.png)

- 輸入顯示條碼的同一頁所提供的帳戶名稱和秘密金鑰。此資料會填入行動應用程式中的代碼和 URL 方塊。隨即會開始啟用程序。

![Add Account](./media/multi-factor-authentication-azure-authenticator/manual.png)

- 請等候帳戶啟用。您現在應該會看到新的帳戶。

![Add Account](./media/multi-factor-authentication-azure-authenticator/msaccount.png)

- 完成處理程序，方法是輸入您的帳戶底下的代碼 (在此案例中是 875 756)，然後將代碼輸入您收到秘密金鑰的頁面上的方塊中，然後按 [下一步]。  

![Add Account](./media/multi-factor-authentication-azure-authenticator/verify.png)

## 如何使用 TouchID 新增帳戶
在 iOS 上的 Azure 驗證器行動應用程式支援 Touch ID。Azure Multi-Factor Authentication 可讓組織除了持有已註冊的裝置之外，還可以要求 PIN。使用這項新功能，裝置已啟用 Touch ID 的 iOS 使用者就不需要再輸入 PIN。設定完成後，使用者只要掃描其指紋即可，不需要輸入 PIN 並且點選核准。

設定 Touch ID 與 Azure 驗證器非常簡單。您剛剛完成 PIN 的一般驗證挑戰，如果您的裝置支援 Touch ID，我們會自動為您設定。

![Touch ID](./media/multi-factor-authentication-azure-authenticator/touchid1.png)

往後當您被要求驗證登入時，您只要點選收到的推播通知並且掃描您的指紋即可，不需要輸入您的 PIN。

![Touch ID](./media/multi-factor-authentication-azure-authenticator/touchid2.png)

<!---HONumber=Oct15_HO4-->