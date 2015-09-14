<properties 
   pageTitle="建立 StorSimple 支援封裝 | Microsoft Azure"
	description="了解如何建立、解密和編輯 StorSimple 裝置的支援封裝。"
	services="storsimple"
	documentationCenter=""
	authors="alkohli"
	manager="carolz"
	editor=""/>
<tags 
   ms.service="storsimple"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="na"
	ms.date="08/31/2015"
	ms.author="alkohli"/>


# 建立及管理 StorSimple 支援封裝

## 概觀

本教學課程說明建立和管理封裝的各種相關工作。支援封裝包含經過加密和壓縮的所有相關記錄檔，用來協助 Microsoft 支援服務小組對任何 StorSimple 裝置問題進行疑難排解。

本教學課程包含使用下列方法建立和管理支援封裝的逐步指示：

- StorSimple Manager 服務 [**維護**] 頁面的 [**支援封裝**] 區段
- Windows PowerShell for StorSimple

閱讀本教學課程之後，您將能夠：

- 建立支援封裝
- 解密並編輯支援封裝


## 在管理入口網站中建立支援封裝

若要對您使用 StorSimple Manager 服務時可能遇到的任何問題進行疑難排解，您可以在管理入口網站中透過此服務的 [**維護**] 頁面，建立支援封裝並上傳至 Microsoft 支援服務網站。您必須提供支援密碼才能上傳。支援工程師透過電子郵件提供支援密碼給您。現在已建立未加密、已壓縮的支援封裝 (.cab 檔案)。然後，支援工程師只要從支援網站上出示密碼，即可擷取此封裝。

在管理入口網站中執行下列步驟，以建立支援封裝：

#### 在管理入口網站中建立支援封裝

1. 瀏覽至 **[裝置] > [維護]**。

2. 在 [**支援封裝**] 區段中，按一下 [**建立及上傳支援封裝**]。

3. 在 [**建立及上傳支援封裝**] 對話方塊中，執行下列動作：

	![建立支援封裝](./media/storsimple-create-manage-support-package/IC740923.png)
											
	- 提供 [**支援密碼**]。Microsoft 支援工程師會透過電子郵件將這個密碼傳送給您。
 	
	- 核取組合方塊以同意 [**自動上傳支援封裝至 Microsoft 支援網站**]。
 	
	- 按一下核取圖示 ![核取圖示](./media/storsimple-create-manage-support-package/IC740895.png)。


## 在 Windows PowerShell for StorSimple 中建立支援封裝

如果您在建立封裝之前需要編輯記錄檔，則必須透過 Windows PowerShell for StorSimple 建立您的封裝。

執行下列步驟在 Windows PowerShell for StorSimple 中建立支援封裝：


#### 在 Windows PowerShell for StorSimple 中建立支援封裝

1. 在用來連接至 StorSimple 裝置的遠端電腦上，以系統管理員身分輸入下列命令，以啟動 Windows PowerShell 工作階段：

	`Start PowerShell`

2. 在 Windows PowerShell 工作階段中，連接到裝置的 SSAdmin 主控台 Runspace：


	- 在命令提示字元中，輸入： 
			
		`$MS = New-PSSession -ComputerName <IP address for DATA 0> -Credential SSAdmin -ConfigurationName "SSAdminConsole"`
		
		
	1. 在開啟的對話方塊中，輸入您的裝置系統管理員密碼。預設密碼為：
	 
		`Password1`

		![PowerShell 工作階段至 SSAdminConsole Runspace](./media/storsimple-create-manage-support-package/IC740962.png)

	2. 按一下 [確定]。
	1. 在命令提示字元中，輸入： 
		
		`Enter-PSSession $MS`


3. 在開啟的工作階段中，輸入適當的命令。


	- 對於受密碼保護的網路共用，請輸入：

		`Export-HcsSupportPackage –PackageTag "MySupportPackage" –Credential "Username" -Force`

		將提示您輸入密碼、網路共用的資料夾路徑及加密複雜密碼 (因為支援封裝已加密)。提供這些資訊後，就會在指定的資料夾中建立支援封裝。
											

	- 對於開放式網路共用資料夾 (即沒有密碼保護)，您不需要使用 `-Credential` 參數。輸入下列命令：

		`Export-HcsSupportPackage –PackageTag "MySupportPackage" -Force`

		將會在指定的網路共用資料夾中，同時為兩個控制器建立支援封裝。這是加密的壓縮檔案，可傳送給 Microsoft 支援服務進行疑難排解。如需詳細資訊，請參閱[連絡 Microsoft 支援服務](storsimple-contact-microsoft-support.md)。


### Export-HcsSupportPackage Cmdlet 的詳細資訊
下表顯示 Export-HcsSupportPackage Cmdlet 可用的各種參數。

| 序號 | 參數 | 必要/選用 | 說明 |
|--------|----------------------|-------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1 | Path | 必要 | 用來提供將存放支援封裝的網路共用資料夾位置。 |
| 2 | EncryptionPassphrase | 必要 | 用來提供複雜密碼，以協助加密支援封裝。 |
| 3 | 認證 | 選用 | 使用這個參數來提供網路共用資料夾的存取認證。 |
| 4 | Force | 選用 | 用來略過加密複雜密碼確認步驟。 |
| 5 | PackageTag | 選用 | 用來指定 Path 下存放支援封裝的目錄。預設值是 [裝置名稱]-[目前日期和時間：yyyy-MM-dd-HH-mm-ss]。 |
| 6 | Scope | 選用 | 指定為 **Cluster** (預設值) 可以為兩個控制器建立支援封裝。如果您只想針對目前的控制器建立封裝，請指定 **Controller**。 |


## 編輯支援封裝

在產生支援封裝之後，您可能需要編輯封裝來移除記錄檔中的客戶特定資訊，例如磁碟區名稱、裝置 IP 位址和備份名稱。

> [AZURE.IMPORTANT]您只能編輯透過 Windows PowerShell for StorSimple 產生的支援封裝。您無法編輯在管理入口網站中以 StorSimple Manager 服務建立的封裝。

將支援封裝上傳至 Microsoft 支援網站之前，若要編輯支援封裝，您必須解密支援封裝、編輯檔案，然後再次加密。執行下列步驟來編輯支援封裝：

#### 在 Windows PowerShell for StorSimple 中編輯支援封裝

1. 建立支援封裝，如[在 Windows PowerShell for StorSimple 中建立支援封裝](#create-a-support-package-in-windows-powershell-for-storsimple)中所述。

2. 在用戶端本機[下載指令碼](http://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65)。

3. 匯入 Windows PowerShell 模組。您必須指定下載指令碼時的本機資料夾的路徑。若要匯入模組，請輸入：
 
	`Import-module <Path to the folder that contains the Windows PowerShell script>`

4. 開啟支援封裝資料夾。請注意，所有檔案都是已壓縮和加密的 *.aes* 檔案。開啟檔案。若要開啟檔案，請輸入：

	`Open-HcsSupportPackage <Path to the folder that contains support package files>`

	這樣會將檔案解壓縮並解密。您會看到所有檔案現在顯示實際的副檔名。
	
	![編輯支援封裝 3](./media/storsimple-create-manage-support-package/IC750706.png)


5. 當提示您輸入加密複雜密碼時，請輸入建立支援封裝時使用的複雜密碼。

    	cmdlet Open-HcsSupportPackage at command pipeline position 1
    
    	Supply values for the following parameters:EncryptionPassphrase: ****
	
6. 瀏覽至包含記錄檔的資料夾。因為記錄檔現在已解壓縮並解密，所以會顯示原始的副檔名。修改這些檔案來移除客戶特定資訊，例如磁碟區名稱和裝置 IP 位址，然後儲存檔案。

7. 關閉檔案。關閉檔案將會使用 Gzip 壓縮檔案，然後使用 AES-256 加密。這是為了透過網路傳輸支援封裝時提高安全性和速度。若要關閉檔案，請輸入：

	`Close-HcsSupportPackage <Path to the folder that contains support package files>`

	![編輯支援封裝 2](./media/storsimple-create-manage-support-package/IC750707.png)

8. 出現提示時，提供加密複雜密碼給修改過的支援封裝。

	    cmdlet Close-HcsSupportPackage at command pipeline position 1
    	Supply values for the following parameters:EncryptionPassphrase: ****

9. 記下新的複雜密碼，當接到要求時就能提供給 Microsoft 支援服務。


### 範例：在受密碼保護的共用中編輯支援封裝中的檔案

以下顯示的範例示範如何解密、編輯和重新加密支援封裝。

![編輯支援封裝 1](./media/storsimple-create-manage-support-package/IC750708.png)

    	PS C:\WINDOWS\system32> Import-module C:\Users\Default\StorSimple\SupportPackage\HCSSupportPackageTools.psm1
    
    	PS C:\WINDOWS\system32> Open-HcsSupportPackage \\hcsfs\Logs\TD48\TD48Logs\C0-A\etw
    
    	cmdlet Open-HcsSupportPackage at command pipeline position 1
    
    	Supply values for the following parameters:
    
    	EncryptionPassphrase: ****
    
    	PS C:\WINDOWS\system32> Close-HcsSupportPackage \\hcsfs\Logs\TD48\TD48Logs\C0-A\etw
    
    	cmdlet Close-HcsSupportPackage at command pipeline position 1
    
    	Supply values for the following parameters:
    
    	EncryptionPassphrase: ****
    
    	PS C:\WINDOWS\system32>

## 後續步驟

了解如何[使用支援封裝和裝置記錄對裝置部署進行疑難排解](storsimple-troubleshoot-deployment.md#support-packages-and-device-logs-available-for-troubleshooting)。

<!---HONumber=September15_HO1-->