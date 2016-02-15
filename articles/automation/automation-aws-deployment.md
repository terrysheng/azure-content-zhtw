<properties
   pageTitle="Azure 自動化和混合式雲端管理：自動化 Amazon Web 服務中 VM 的部署"
   description="Azure 自動化和混合式雲端管理：自動化 Amazon Web 服務中 VM 的部署"
   services="automation"
   documentationCenter=""
   authors="tiander"
   manager="stevenka"
   editor="" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/01/2016"
   ms.author="tiandert; bwren" />

# Azure 自動化和混合式雲端管理：自動化 Amazon Web 服務中 VM 的部署

在我曾介紹 Azure 自動化的[雲端巡迴研討會](https://microsoftcloudroadshow.com/cities)活動期間，有一些 Azure 自動化的相關問題僅針對 Azure。幸運的是，我有一些示範將會在此部落格文章中討論，但是關鍵的答案是 **Azure 自動化不僅適用於 Azure，它還適用於內部部署資源，*並且*也適用於管理混合式雲端環境！** 因此，例如，如果您是 Amazon Web Services (AWS) 客戶，則 Azure 自動化也適合您！

## 目標

在本文中，我將討論您可以如何利用 Azure 自動化在 AWS 中佈建 VM，並且給予 VM 特定名稱 – 在 AWS 世界中稱為「標記」VM。

## 假設

基於本文的目的，我假設您已設定 Azure 自動化帳戶，而且您也擁有 AWS 訂用帳戶。

如需有關設定 Azure 自動化帳戶的詳細資訊，請參閱我們的[文件頁面](automation-configuring.md)。

## 準備：儲存 AWS 認證

若要讓 Azure 自動化與 AWS「交談」，您必須擷取 AWS 認證，並將它們儲存為 Azure 自動化中的資產。

當您登入 AWS 入口網站時，按一下您的名稱下的小三角形，然後按一下 [安全性認證]。

![AWS 安全性認證](./media/automation-aws-deployment/73522ff9-30b5-431c-a3b6-5a33167827ab.png "AWS 安全性認證")

按一下 [便捷鍵]。

![AWS 便捷鍵](./media/automation-aws-deployment/215d76d0-6e7f-4f55-9128-ba618d4514be.jpg "AWS 便捷鍵")

複製您的便捷鍵和密碼便捷鍵 (選擇性地下載您的金鑰檔案以將其儲存在其他安全的位置)。

![建立 AWS 便捷鍵](./media/automation-aws-deployment/81ca1d36-2814-478b-858f-8346f2a28211.png "建立 AWS 便捷鍵")

## Azure 自動化中的 AWS 資產

您在上一個步驟中複製並儲存您的 AWS **便捷鍵 ID** 和**密碼便捷鍵**。現在我們要將其儲存在 Azure 自動化中。

瀏覽至您的自動化帳戶，然後選取 [資產] - [認證] 並加入新的認證，並將它命名為 **AWScred**。

![image](./media/automation-aws-deployment/19c3669d-5259-4566-9479-5dbebb8ac733.png "image")

提供選擇性描述，並確定您在 [使用者名稱] 欄位中輸入 [存取 ID]，且欄位中的 [密碼便捷鍵] 標示為 [密碼]。儲存 AWS 認證。

## Amazon Web Services PowerShell 模組

我們的 VM 佈建 Runbook 將會利用 AWS PowerShell 模組來執行其工作。AWS PowerShell 模組隨附於 [PowerShell 資源庫](http://www.powershellgallery.com/packages/AWSPowerShell/)，並且可以透過 [部署至 Azure 自動化] 按鈕，輕鬆地新增至自動化帳戶。

![AWS PS 模組匯入 AA](./media/automation-aws-deployment/daa07d22-0464-4ec2-8c85-35525f95e5e4.png "AWS PS 模組匯入 AA")

當您按一下 [部署至 Azure 自動化]，會帶您前往 Azure 登入頁面，在您提供您的認證之後，您會看到下列畫面。

![匯入 AWS 模組精靈](./media/automation-aws-deployment/575cd4d9-2ca5-4540-869d-3919f91294af.png "匯入 AWS 模組精靈")

選取新的或現有的自動化帳戶。請注意，現有的帳戶沒有下拉式方塊，因此請確定未輸入錯誤，且您的自動化帳戶的區域和資源群組正確。逐一完成其他步驟以完成組態，然後按一下 [建立]。

如果您瀏覽至您所選的自動化帳戶，並瀏覽至 [資產] -[模組]，您現在會看到 AWS 模組有 1427 個活動，好極了！

>[AZURE.NOTE] 將 PowerShell 模組匯入至 Azure 自動化包含兩個步驟：
>
> 1.  匯入模組
> 2.  擷取 Cmdlet
>
>活動在模組完成匯入和擷取 Cmdlet 之前不會顯示，這可能要花幾分鐘的時間。

## 佈建 AWS VM Runbook

我們已備妥所有必要條件，現在可以撰寫我們的 Runbook，在 AWS 中佈建 VM。我們也會向您展示，您可以在 Azure 自動化中利用原生 PowerShell 指令碼，而不是 PowerShell 工作流程。

原生 PowerShell 和 PowerShell 工作流程之間的差異的詳細資訊可以在[這裡](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)找到。圖形化 Runbook 製作的相關資訊可以在[這裡](https://azure.microsoft.com/blog/azure-automation-graphical-and-textual-runbook-authoring/)找到。

我們的 Runbook 的 PowerShell 指令碼可以從 [PowerShell 資源庫](https://www.powershellgallery.com/packages/New-AwsVM/DisplayScript)下載。

您可以藉由開啟 PowerShell 工作階段並且輸入：**Save-Script -Name New-AwsVM -Path <path>**，下載指令碼

當您已儲存 PowerShell 指令碼，您可以執行下列動作，將其新增至 Azure 自動化做為 Runbook：

在您的 [自動化帳戶] 中，按一下 [Runbook] ，然後選取 [加入 Runbook]。選取 [快速建立] (建立新的 Runbook)，提供您的 Runbook 的名稱，並在 [Runbook 類型] 底下選取 [PowerShell]。然後按一下 [建立]。

![建立 AWS Runbook](./media/automation-aws-deployment/4759ad00-f800-44ba-94be-307cf034a9df.png "建立 AWS Runbook")

當 Runbook 編輯器開啟時，將 PowerShell 指令碼複製並貼上到 Runbook 製作區域。

![image](./media/automation-aws-deployment/7e13addf-cae3-49a5-9d6c-28aa0b7263f4.png "image")

使用已下載的 PowerShell 指令碼時請注意下列事項：

-   Runbook 包含一些預設的參數值，如 #ToDo 章節中所述。請評估所有預設值，並且視需要更新。
-   請確定您在 [資產] 底下儲存您的 AWS 認證，稱為 **AWScred**。
-   您將使用的區域取決於您的 AWS 訂用帳戶。在 AWS 入口網站中，您也必須查閱安全性認證，按一下帳戶旁邊的箭號來驗證您的區域。

![AWS 區域](./media/automation-aws-deployment/5a789953-7329-4f0e-9501-e2d3347a8730.png "AWS 區域")

-   找出您必須在您的 Runbook 提供[哪一個區域](http://docs.aws.amazon.com/powershell/latest/userguide/pstools-installing-specifying-region.html)。例如，我的區域是美國西部 (奧勒岡)，會轉譯為 us-west-2。

![AWS 區域](./media/automation-aws-deployment/3ee8065d-0480-4c78-a2e3-6062653e9cb6.png "AWS 區域")

-   當您使用 PowerShell ISE、匯入模組、在您的 ISE 環境中將 **Get-AutomationPSCredential** 取代為 **$AwsCred = Get-Credential** 以驗證 AWS 時，您會取得映像名稱的清單。 這將會提示您輸入認證，您可以提供您的**便捷鍵 ID** 和**密碼便捷鍵**做為使用者名稱和密碼。請參閱下面的範例：


		#Sample to get the AWS VM available images
		#Please provide the path where you have downloaded the AWS PowerShell module
		Import-Module AWSPowerShell
		$AWSRegion = "us-west-2"
		$AwsCred = Get-Credential
		$AwsAccessKeyId = $AwsCred.UserName
		$AwsSecretKey = $AwsCred.GetNetworkCredential().Password
		
		# Set up the environment to access AWS
		Set-AWSCredentials -AccessKey $AwsAccessKeyId -SecretKey $AwsSecretKey -StoreAs AWSProfile
		Set-DefaultAWSRegion -Region $AWSRegion
		
		Get-EC2ImageByName -ProfileName AWSProfile 


您會取得以下輸出：

![取得 AWS 映像](./media/automation-aws-deployment/1b1b0e9f-1af0-471c-9f5e-43f4bb29f4ed.png "取得 AWS 映像")

在自動化變數中複製並貼上您喜好的映像名稱，在 Runbook 中參考為 **$InstanceType**。由於我使用免費的 AWS 層，所以我在我的 Runbook 中選取 **t2.micro**。

在[這裡](https://aws.amazon.com/ec2/instance-types/)深入了解 Amazon EC2 執行個體類型。

## 測試佈建 AWS VM Runbook

預檢檢查清單：

-   驗證 AWS 的資產已建立並命名為 **AWScred**
-   AWS PowerShell 模組已匯入 Azure 自動化
-   已建立新的 Runbook，已驗證參數值並且視需要更新
-   Runbook 設定底下的 [記錄詳細資訊記錄] 和選擇性的 [記錄進度記錄] 已設為 [開啟]

讓我們從提供 **VMname** 開始我們的新 Runbook。

![啟動 New-AwsVM Runbook](./media/automation-aws-deployment/31cd9d1d-208e-4e96-897f-05e37bf5ee7d.png "啟動 New-AwsVM Runbook")

由於我們已啟用 [記錄詳細資訊記錄] 和 [記錄進度記錄]，我們可以在 [所有記錄檔] 底下看到我們的輸出 [串流]。

![串流輸出](./media/automation-aws-deployment/77191c69-37fa-4598-82d8-cf5dbadfaff5.png "串流輸出")

讓我們看看 AWS：

![AWS 主控台部署 VM](./media/automation-aws-deployment/c5a16bd4-6c54-40d6-9811-7d8e6c5ec74d.png "AWS 主控台部署 VM")

太棒了！

在本文中，我提供如何使用 Azure 自動化管理混合式雲端的範例，例如在 Amazon Web 服務中建立 VM，然後套用自訂標記，以給予其名稱。

使用自動化愉快，下回見！

Tiander。

<!---HONumber=AcomDC_0204_2016-->