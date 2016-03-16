<properties
   pageTitle="加密 Azure 虛擬機器 | Microsoft Azure"
   description="本文件可協助您在收到 Azure 資訊安全中心所發出的警示後，加密 Azure 虛擬機器。"
   services="security-center"
   documentationCenter="na"
   authors="TomShinder"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/03/2016"
   ms.author="tomsh"/>

# 加密 Azure 虛擬機器
Azure 資訊安全中心會在您有未加密的虛擬機器時對您發出警示。這些警示會顯示為高嚴重性，因此建議您加密這些虛擬機器。

![磁碟加密建議](./media/security-center-disk-encryption\security-center-disk-encryption-fig1.png)

> [AZURE.NOTE] 本文件中的資訊適用於「Azure 資訊安全中心」的預覽版本。

若要加密已由 Azure 資訊安全中心識別為需要加密的 Azure 虛擬機器，建議您執行下列步驟︰

- 安裝並設定 Azure PowerShell。這可讓您執行必要的 PowerShell 命令，以便設定用來加密 Azure 虛擬機器的必要先決條件。
- 取得並執行 Azure 磁碟加密先決條件 Azure PowerShell 指令碼
- 加密虛擬機器

本文件的目標是讓您可以加密虛擬機器，即使您只略為了解或完全不了解 Azure PowerShell。本文件假設您使用 Windows 10 做為用戶端電腦，並從中設定 Azure 磁碟加密。
 
若要設定先決條件以及設定 Azure 虛擬機器的加密功能，可以使用的方法很多。如果您已經很熟悉 Azure PowerShell 或 Azure CLI，可能會想要使用其他方法。

> [AZURE.NOTE] 若要深入了解其他可為 Azure 虛擬機器設定加密功能的方法，請參閱[適用於 Windows 和 Linux Azure 虛擬機器的 Azure 磁碟加密](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)。

## 安裝並設定 Azure PowerShell
您的電腦上需要安裝 Azure PowerShell 1.2.1 版或更新版本。[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md) 一文含有佈建電腦以使用 Azure PowerShell 的所有所需步驟。最直接的方法是使用該文章所述的 Web PI 安裝方法。即使您已安裝 Azure PowerShell，仍請使用 Web PI 方法再安裝一次，以便擁有最新的 Azure PowerShell 版本。


## 取得並執行 Azure 磁碟加密先決條件組態指令碼
Azure 磁碟加密先決條件組態指令碼會設定用來加密 Azure 虛擬機器所需的所有先決條件。

1.	移至有 [Azure 磁碟加密先決條件設定指令碼](https://github.com/Azure/azure-powershell/blob/dev/src/ResourceManager/Compute/Commands.Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1)的 GitHub 頁面。
2.	在 GibHub 頁面上，按一下 [RAW] 按鈕。
3.	使用 **CTRL-A** 選取頁面上的所有文字，然後使用 **CTRL-C** 將頁面上的所有文字複製到剪貼簿。
4.	開啟 [記事本] 並將複製的文字貼入 [記事本]。 
5.	在 C: 磁碟機上建立名為 **AzureADEScript** 的新資料夾。
6.	儲存 [記事本] 檔案，方法是依序按一下 [檔案] 和 [另存新檔]。在 [檔案名稱] 文字方塊中，輸入 **“ADEPrereqScript.ps1”** 並按一下 [儲存] (請務必在名稱前後加上引號，否則檔案將會儲存為 .txt 副檔名)。 

您已儲存了指令碼內容，接下來請在 PowerShell ISE 中開啟指令碼︰

1.	在 [開始] 功能表中，按一下 [Cortana]。在 Cortana 搜尋文字方塊中輸入 **PowerShell**，以向 **Cortana** 詢問 "PowerShell"。 
2.	用滑鼠右鍵按一下 [Windows PowerShell ISE]，然後按一下 [以系統管理員身分執行]。
3.	在 [系統管理員: Windows PowerShell ISE] 視窗中，依序按一下 [檢視] 和 [顯示指令碼窗格]。 
4.	如果您在視窗右側看到 [命令] 窗格，請按一下窗格右上角的 **"x"** 以將它關閉。如果文字太小以致您看不清楚，請使用 **CTRL+加** (「加」是指「+」號)。如果文字太大，請使用 **CTRL+減** (「減」是指「-」號)。 
5.	依序按一下 [檔案] 和 [開啟]。瀏覽至 **C:\\AzureADEScript** 資料夾，然後連按兩下 **ADEPrereqScript**。
6.	**ADEPrereqScript** 內容現在應該會出現在 PowerShell ISE 中，並且會加上色彩標示以幫助您更輕鬆地識別各種元件 (例如命令、參數和變數)。 

您現在應該會看到類似下圖的內容：

![PowerShell ISE 視窗](./media/security-center-disk-encryption\security-center-disk-encryption-fig2.png)

上方窗格稱為「指令碼窗格」，下方窗格則稱為「主控台」。本文稍後將會使用這些詞彙。

## 執行 Azure 磁碟加密先決條件 PowerShell 命令

在啟動 Azure 磁碟加密先決條件指令碼後，它會要求您提供下列資訊︰

- **資源群組名稱** - 您要用來放置金鑰保存庫的資源群組名稱。如果還未建立具有所輸入名稱的資源群組，則會以該名稱建立新的資源群組。如果此訂用帳戶中已有您想要使用的資源群組，則請輸入該資源群組的名稱。
- **金鑰保存庫名稱** - 要用來放置加密金鑰的金鑰保存庫名稱。如果您沒有此名稱的金鑰保存庫，則會以此名稱建立新的金鑰保存庫。如果您已有想要使用的金鑰保存庫，請輸入現有金鑰保存庫的名稱。
- **位置** - 金鑰保存庫的位置。請確定金鑰保存庫和要加密的 VM 位於相同位置。如果您不知道位置在哪，本文稍後會有示範如何尋找的步驟。 
- **Azure Active Directory 應用程式名稱** - 用來將密碼寫入金鑰保存庫之 Azure Active Directory 應用程式的名稱。如果不存在此名稱的應用程式，將會以此名稱建立新的應用程式。如果您已有想要使用的 Azure Active Directory 應用程式，請輸入該 Azure Active Directory 應用程式的名稱。

> [AZURE.NOTE] 如果您想知道為何需要建立 Azure Active Directory 應用程式，請參閱[開始使用 Azure 金鑰保存庫](../key-vault/key-vault-get-started.md)一文的*向 Azure Active Directory 註冊應用程式*章節。

執行下列步驟來加密 Azure 虛擬機器︰

1.	如果您已關閉 PowerShell ISE，請開啟已提升權限的 PowerShell ISE 執行個體。如果尚未開啟 PowerShell ISE，請遵循本文稍早的指示。如果您已關閉指令碼，請開啟 **ADEPrereqScript.ps1**，方法是依序按一下 [檔案] 和 [開啟]，然後選取 **c:\\AzureADEScript** 資料夾中的指令碼。如果您從一開始就遵循本文的指示進行，請直接移至下一個步驟。 
2.	在 PowerShell ISE 的主控台 (PowerShell ISE 的下方窗格) 中，將焦點變更至指令碼的本機位置，方法是輸入 **cd c:\\AzureADEScript**，然後按 **ENTER** 鍵。
3.	設定機器的執行原則以便可以執行指令碼。在主控台輸入 **Set-ExecutionPolicy Unrestricted**，然後按 ENTER 鍵。如果出現對話方塊指出執行原則變更所帶來的影響，請按一下 [全部都是] 或 [是] (如果有看到 [全部都是]，請選取該選項；如果沒看到 [全部都是]，則請按一下 [是])。 
4.	登入 Azure 帳戶。在主控台中輸入 **Login-AzureRmAccount**，然後按 **ENTER** 鍵。隨即會出現對話方塊供您輸入認證 (請確定您有變更虛擬機器的權限；如果沒有權限，就無法將其加密。如果不能確定，請詢問訂用帳戶擁有者或系統管理員)。您應該會看到關於**環境**、**帳戶**、**TenantId**、**SubscriptionId** 和 **CurrentStorageAccount** 的資訊。請將 **SubscriptionId** 複製到 [記事本]。步驟 6 會用到此資訊。
5.	尋找虛擬機器所隸屬的訂用帳戶以及虛擬機器的位置。移至 [https://portal.azure.com](ttps://portal.azure.com) 並登入。在頁面左側按一下 [虛擬機器]。您將會看到虛擬機器和其所屬訂用帳戶的清單。

	![虛擬機器](./media/security-center-disk-encryption\security-center-disk-encryption-fig3.png)

6.	返回 PowerShell ISE。設定用來執行指令碼的訂用帳戶內容。在主控台中輸入 **Select-AzureRmSubscription –SubscriptionId <your_subscription_Id>** (將 **< your_subscription_Id >** 更換為實際的訂用帳戶識別碼)，然後按 **ENTER** 鍵。您將會看到關於環境、**帳戶**、**TenantId**、**SubscriptionId** 和 **CurrentStorageAccount** 的資訊。
7.	您現在可以開始執行指令碼。按一下 [執行指令碼] 按鈕或按鍵盤上的 **F5** 鍵。

	![執行 PowerShell 指令碼](./media/security-center-disk-encryption\security-center-disk-encryption-fig4.png)

8.	指令碼會要求輸入 [resourceGroupName:] - 輸入想要使用的*資源群組*名稱，然後按 **ENTER** 鍵。如果您沒有資源群組，請輸入新的資源群組所要使用的名稱。如果您已有想要使用的*資源群組* (例如虛擬機器所在的資源群組)，請輸入現有資源群組的名稱。
9.	指令碼會要求輸入 [keyVaultName:] - 輸入想要使用的*金鑰保存庫*名稱，然後按 ENTER 鍵。如果您沒有資源群組，請輸入新的資源群組所要使用的名稱。如果您已有想要使用的金鑰保存庫，請輸入現有*金鑰保存庫*的名稱。
10.	指令碼會要求輸入 [位置:] - 輸入想要加密之 VM 所在位置的名稱，然後按 **ENTER** 鍵。如果不記得位置，請返回步驟 5。
11.	指令碼會要求輸入 [aadAppName:] - 輸入想要使用的 *Azure Active Directory* 應用程式名稱，然後按 **ENTER** 鍵。如果您沒有資源群組，請輸入新的資源群組所要使用的名稱。如果您已有想要使用的 *Azure Active Directory 應用程式*，請輸入現有 *Azure Active Directory 應用程式*的名稱。
12.	此時會出現登入對話方塊。請提供您的認證 (沒錯，您已登入過一次，但現在您需要再做一次)。
13.	指令碼隨即執行，並且會於完成時要求您複製 **aadClientID**、**aadClientSecret**、**diskEncryptionKeyVaultUrl** 和 **keyVaultResourceId** 的值。將這些值各自複製到剪貼簿並貼入 [記事本]。 
14.	返回 PowerShell ISE 並將游標放在最後一行結尾，然後按 **ENTER** 鍵。

指令碼的輸出看起來應該會像以下畫面︰

![PowerShell 輸出](./media/security-center-disk-encryption\security-center-disk-encryption-fig5.png)

## 加密 Azure 虛擬機器 

現在，您已準備好加密虛擬機器。如果虛擬機器和金鑰保存庫位於相同資源群組，您可以移至＜加密步驟＞一節。不過，如果虛擬機器和金鑰保存庫並非位在相同資源群組，則必須在 PowerShell ISE 的主控台中輸入下列命令︰

**$resourceGroupName = <’Virtual\_Machine\_RG’>**

請將 **< Virtual_Machine_RG >** 更換為虛擬機器所在資源群組的名稱，並以單引號括住。然後按 **ENTER** 鍵。若要確認輸入的資源群組名稱沒有錯誤，請在 PowerShell ISE 主控台中輸入下列命令︰

**$resourceGroupName**

按 **ENTER** 鍵。您應該會看到虛擬機器所在資源群組的名稱。例如：

![PowerShell 輸出](./media/security-center-disk-encryption\security-center-disk-encryption-fig6.png)

### 加密步驟

首先，您必須提供想要加密的虛擬機器名稱給 PowerShell。在主控台中輸入︰

**$vmName = <’your\_vm\_name’>**

將 **<’your\_vm\_name’>** 更換為 VM 的名稱 (請確定名稱已用單引號括住)，然後按 **ENTER** 鍵。

若要確認輸入的 VM 名稱沒有錯誤，請輸入︰

**$vmName**

按 **ENTER** 鍵。您應該會看到想要加密的虛擬機器名稱。例如：

![PowerShell 輸出](./media/security-center-disk-encryption\security-center-disk-encryption-fig7.png)

有兩種方法可供您執行加密命令以加密虛擬機器。第一個方法是在 PowerShell ISE 主控台中輸入下列命令︰

~~~
Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $resourceGroupName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId
~~~

在輸入此命令後，按 **ENTER** 鍵。

第二個方法是在指令碼窗格 (PowerShell ISE 的上方窗格) 中按一下，然後向下捲動到指令碼底部。反白顯示上面所列的命令並按一下滑鼠右鍵，然後按一下 [執行選取範圍] 或按鍵盤上的 **F8** 鍵。

![PowerShell ISE](./media/security-center-disk-encryption\security-center-disk-encryption-fig8.png)

不論您使用哪一種方法，都會出現對話方塊通知您，需要 10-15 分鐘才能完成作業。按一下 [是]。

加密程序進行時，您可以返回 Azure 入口網站，並查看虛擬機器的狀態。在頁面左側按一下 [虛擬機器]，然後在 [虛擬機器] 刀鋒視窗中按一下您要加密的虛擬機器名稱。在出現的刀鋒視窗中，您會發現 [狀態] 指出其 [正在更新]。下圖展示進行中的加密。

![更多關於 VM 的詳細資料](./media/security-center-disk-encryption\security-center-disk-encryption-fig9.png)

返回 PowerShell ISE。指令碼完成時，您會看到下圖中所顯示的內容。

![PowerShell 輸出](./media/security-center-disk-encryption\security-center-disk-encryption-fig10.png)

為了證明虛擬機器現已加密，請返回 Azure 入口網站，然後按一下頁面左側的 [虛擬機器]。按一下已加密之虛擬機器的名稱。在 [設定] 刀鋒視窗中，按一下 [磁碟]。

![設定選項](./media/security-center-disk-encryption\security-center-disk-encryption-fig11.png)

在 [磁碟] 刀鋒視窗中，您會看到 [加密] 是 [已啟用] 狀態。

![磁碟屬性](./media/security-center-disk-encryption\security-center-disk-encryption-fig12.png)


## 後續步驟

在本文件中，您已了解如何加密 Azure 虛擬機器。若要深入了解「Azure 資訊安全中心」，請參閱下列主題：

- [Azure 資訊安全中心的安全性健全狀況監視](security-center-monitoring.md) - 了解如何監視 Azure 資源的健全狀況
- [管理與回應 Azure 資訊安全中心的安全性警示](security-center-managing-and-responding-alerts.md) – 了解如何管理與回應安全性警示
- [Azure 安全性中心常見問題集](security-center-faq.md) - 尋找使用服務的常見問題
- [Azure 安全性部落格](http://blogs.msdn.com/b/azuresecurity/) – 尋找有關 Azure 安全性與相容性的部落格文章

<!---HONumber=AcomDC_0309_2016-->