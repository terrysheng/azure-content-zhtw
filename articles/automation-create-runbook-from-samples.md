<properties urlDisplayName="Get Started with Azure Automation" pageTitle="開始使用 Azure 自動化" metaKeywords="" description="了解如何在 Azure 中匯入和執行自動化工作。" metaCanonical="" services="automation" documentationCenter="" title="Get Started with Azure Automation" authors="bwren" solutions="" manager="stevenka" editor="" />

<tags ms.service="automation" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/21/2014" ms.author="bwren" />


# 開始使用 Azure 自動化

Microsoft Azure 自動化可讓開發人員將執行於雲端環境中的手動、長時間執行、易發生錯誤且重複性高的工作自動化。您可以使用 Runbook 來建立、監視、管理及部署 Azure 環境中的資源；而所謂的 Runbook，基本上就是 Windows PowerShell 工作流程。若要深入了解「自動化」，請參閱[自動化概觀指南](http://go.microsoft.com/fwlink/p/?LinkId=392861)。 

本教學課程將引導您逐步執行將範例 "Hello World" Runbook 匯入 Azure 自動化中、執行 Runbook 並檢視其輸出的步驟。

>[WACOM.NOTE] 若要了解如何使用 [Azure PowerShell Cmdlet](http://msdn.microsoft.com/zh-tw/library/jj156055.aspx) 將 Azure 作業自動化，請參閱 <a href="http://azure.microsoft.com/blog/2014/08/27/azure-automation-authenticating-to-azure-using-azure-active-directory/">Azure 自動化：使用 Azure Active Directory 向 Azure 進行驗證</a>。

## 範例和工具 Runbook

Azure 自動化團隊建立了多種 Runbook 範例，以協助您著手使用「自動化」。這些範例運用了基本自動化概念，旨在協助您了解如何自行撰寫 Runbook。  

自動化團隊也建立了工具 Runbook，可供您做為大型自動化工作的基礎。  

>[WACOM.NOTE] 最佳做法是撰寫小型、模組化、可重複使用的 Runbook。我們也強烈建議您在熟悉自動化之後，針對常用的案例建立您自己的公用程式 Runbook。  

您可以在[指令碼中心](http://go.microsoft.com/fwlink/p/?LinkId=393029)檢視並下載「自動化」團隊的範例和公用程式 Runbook，或直接從 [Runbook 庫](http://aka.ms/runbookgallery)匯入。 

## 自動化社群和意見反應

社群及其他 Microsoft 團隊所提供的 Runbook 也一併發佈在[指令碼中心](http://go.microsoft.com/fwlink/?LinkID=391681)和 [Runbook 庫](http://aka.ms/runbookgallery)。 

<strong>請不吝提供意見。</strong>  如果您要尋找「自動化」Runbook 方案或整合模組，請在「指令碼中心」提出「指令碼要求」。如果您對於「自動化」的新功能有任何構想，請在[使用者意見](http://feedback.windowsazure.com/forums/34192--general-feedback)中提出。

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

## 本教學課程的概略步驟

1. [建立自動化帳戶](#automationaccount)
2. [從 Runbook 庫匯入 Runbook](#importrunbook)
3. [發行 Runbook](#publishrunbook)
4. [啟動 Runbook](#startrunbook)


## <a name="automationaccount"></a>建立自動化帳戶

1.	登入 [Azure 管理入口網站](http://manage.windowsazure.com)。

2.	在「管理入口網站」中，按一下 [建立自動化帳戶]****。

	>[WACOM.NOTE] 如果您已經建立自動化帳戶，則可以跳到步驟 4。

	![Create Account](./media/automation/automation_01_CreateAccount.png)

3.	在 [加入新的自動化帳戶]**** 頁面上，輸入帳戶的名稱，然後按一下打勾記號。

	![Add New Account](./media/automation/automation_02_addnewautoacct.png)

## <a name="importrunbook"></a>從 Runbook 庫匯入 Runbook
 
4.	在 [自動化]**** 頁面上，按一下您剛剛建立的新帳戶。
 
	![New Account](./media/automation/automation_03_NewAutoAcct.png)

5.	按一下 [RUNBOOKS]****。

	![Runbooks Tab](./media/automation/automation_04_RunbooksTab.png)
  
6.	按一下 [新增]**** > [Runbook]**** > [從組件庫]****。

	![Runbook Gallery](./media/automation/automation_05_ImportGallery.png)

7.  選取 [教學課程]**** 類別，然後選取 [適用於 Azure 自動化的 Hello World]**** (Hello World for Azure Automation)。按一下向右箭頭按鈕。

	![Import Runbook](./media/automation/automation_06_ImportRunbook.png)

8.  檢閱 Runbook 的內容，然後按一下向右箭頭按鈕。

	![Runbook Definition](./media/automation/automation_07_RunbookDefinition.png)

8.	檢閱 Runbook 的詳細資料，然後按一下勾選記號按鈕。

	![Runbook Details](./media/automation/automation_08_RunbookDetails.png)

## <a name="publishrunbook"></a>發行 Runbook 

9.	當 Runbook 匯入完成時，按一下 [Write-HelloWorld]****。

	![Imported Runbook](./media/automation/automation_07_ImportedRunbook.png)

9.	按一下 [製作]****，然後按一下 [草稿]****。  

	您可以在 [草稿] 模式下修改 Runbook 的內容。對這個 Runbook 無須進行任何修改。

	![Author Draft](./media/automation/automation_08_AuthorDraft.png)  
 
10.	按一下 [發行]**** 以提升 Runbook，使其可供生產環境使用。

	![Publish](./media/automation/automation_085_Publish.png)
   
11.	提示您儲存並發行 Runbook 時，按一下 [是]****。
 
	![Save and Pub prompt](./media/automation/automation_09_SavePubPrompt.png)

## <a name="startrunbook"></a>啟動 Runbook

12.	在已開啟 **Write-HelloWorld** Runbook 的情況下，按一下 [啟動]****。

	![Published](./media/automation/automation_10_PublishStart.png)
 
13.	在 [指定 Runbook 參數值]**** 頁面上，輸入將做為 Write-HelloWorld.ps1 指令碼之輸入參數的 [名稱]****，然後按一下打勾記號。

	![Runbook Parameters](./media/automation/automation_11_RunbookParams.png)
  
14.	按一下 [工作]**** 以查看您剛剛啟動之 Runbook 工作的狀態，然後按一下 [工作啟動]**** 欄中的時間戳記，以檢視工作摘要。

	![Runbook Status](./media/automation/automation_12_RunbookStatus.png)

15.	在 [摘要]**** 頁面上，您可以查看工作的摘要、輸入參數及輸出。
 
	![Runbook Summary](./media/automation/automation_13_RunbookSummary_callouts.png)


# 從 Runbook 管理 Azure 服務 
上述範例示範未管理 Azure 服務的範例 Runbook。[Azure Cmdlet](http://msdn.microsoft.com/zh-tw/library/jj156055.aspx) 需要向 Azure 進行驗證。您可以遵循 [Azure 自動化：使用 Azure Active Directory 向 Azure 進行驗證](http://azure.microsoft.com/blog/2014/08/27/azure-automation-authenticating-to-azure-using-azure-active-directory/)中的指示，來設定透過「Azure 自動化」管理您的 Azure 訂用帳戶。

# 另請參閱

- [自動化概觀](http://go.microsoft.com/fwlink/p/?LinkId=392860)
- [Runbook 撰寫指南](http://go.microsoft.com/fwlink/p/?LinkID=301740)
- [自動化論壇](http://go.microsoft.com/fwlink/p/?LinkId=390561)
- [Azure 自動化：使用 Azure Active Directory 向 Azure 進行驗證](http://azure.microsoft.com/blog/2014/08/27/azure-automation-authenticating-to-azure-using-azure-active-directory/)

<!--HONumber=35.2-->
