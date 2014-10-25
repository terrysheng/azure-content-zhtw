<properties linkid="automation-create-runbook-from-samples" urlDisplayName="Get Started with Azure Automation" pageTitle="Get Started with Azure Automation" metaKeywords="" description="Learn how to import and run an automation job in Azure." metaCanonical="" services="automation" documentationCenter="" title="Get Started with Azure Automation" authors="bwren" solutions="" manager="stevenka" editor="" />

<tags ms.service="automation" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="bwren"></tags>

# 開始使用 Azure 自動化

Microsoft Azure 自動化可讓開發人員將執行於雲端環境中的手動、長時間執行、易發生錯誤且重複性高的工作自動化。您可以使用 Runbook 來建立、監視、管理及部署 Azure 環境中的資源；而所謂的 Runbook，基本上就是 Windows PowerShell 工作流程。若要深入了解自動化，請參閱[自動化概觀指南][] (英文)。

本教學課程將引導您逐步執行將範例 "Hello World" Runbook 匯入 Azure 自動化中、執行 Runbook 並檢視其輸出的步驟。

> [WACOM.NOTE] 若要了解如何使用 [Azure PowerShell Cmdlet][] 自動化 Azure 作業，請參閱 [Azure 自動化：使用 Azure Active Directory 對 Azure 進行驗證][] (英文)。

## 範例和工具 Runbook

Azure 自動化團隊建立了多種 Runbook 範例，以協助您著手使用「自動化」。這些範例運用了基本自動化概念，旨在協助您了解如何自行撰寫 Runbook。

自動化團隊也建立了工具 Runbook，可供您做為大型自動化工作的基礎。

> [WACOM.NOTE] 最理想的作法是撰寫小型、模組化、可重複使用的 Runbook。我們也強烈建議您在熟悉自動化之後，針對常用的案例建立您自己的公用程式 Runbook。

您可以在[指令碼中心][]檢視及下載自動化團隊的範例和公用程式 Runbook。

## 自動化社群和意見反應

由社群和其他 Microsoft 團隊提供的 Runbook，也會發佈在[指令碼中心][1]上。

**請不吝提供意見。**如果您要尋找自動化 Runbook 解決方案或整合模組，請在指令碼中心提出「指令碼要求」。如果您對於自動化的新功能有任何構想，請將其發佈至[使用者意見][] (英文)。

[WACOM.INCLUDE [create-account-note][]]

## 本教學課程的高階步驟

1.  [註冊自動化預覽版][]
2.  [下載範例 Runbook][]
3.  [匯入、執行及檢視範例 Runbook 的輸出][]

## <a name="preview"></a>註冊 Azure 自動化預覽版

若要開始使用自動化，您需要已啟用 Microsoft Azure 自動化預覽功能的作用中 Azure 訂用帳戶。

-   在 [預覽功能] 頁面上，按一下 [立即試用]。

    ![啟用預覽][]

## <a name="download-sample"></a>從指令碼中心下載範例 Runbook

1.  移至[指令碼中心][]，然後按一下 [Hello World for Azure Automation]。

2.  在 [下載] 旁按一下檔案名稱 **Write-HelloWorld.ps1**，然後將檔案儲存至您的電腦。

## <a name="import-sample"></a>在 Azure 自動化中匯入、執行及檢視範例 Runbook

1.  登入 [Azure 管理入口網站][]。

2.  在管理入口網站中，按一下 [Create an Automation Account]。

    > [WACOM.NOTE] 如果您已建立自動化帳戶，您可以跳至步驟 4。

    ![Create Account][]

3.  在 [Add a New Automation Account] 頁面上輸入帳戶的名稱，然後按一下核取記號。

    ![Add New Account][]

4.  在 [自動化] 頁面上，按一下您剛剛建立的新帳戶。

    ![New Account][]

5.  按一下 [Runbook]。

    ![Runbooks Tab][]

6.  按一下 [匯入]。

    ![Import][]

7.  瀏覽至您所下載的 **Write-HelloWorld.ps1** 指令碼，然後按一下核取記號。

    ![Browse][]

8.  按一下 **Write-HelloWorld**。

    ![Imported Runbook][]

9.  按一下 [作者]，然後按一下 [草稿]。對這個 Runbook 無須進行任何修改。

    現在，您會看見 **Write-HelloWorld.ps1** 的內容。您可以在 [草稿] 模式下修改 Runbook 的內容。

    ![Author Draft][]

10. 按一下 [發佈] 提升 Runbook，使其可用於生產環境中。

    ![Publish][]

11. 在出現儲存及發佈 Runbook 的提示時，按一下 [是]。

    ![Save and Pub prompt][]

12. 按一下 [已發佈]，然後按一下 [啟動]。

    ![Published][]

13. 在 [Specify the runbook parameter values] 頁面上，輸入將做為 Write-HelloWorld.ps1 指令碼之輸入參數的 [名稱]，然後按一下核取記號。

    ![Runbook Parameters][]

14. 按一下 [JOBS] 以查看您剛剛啟動之 Runbook 工作的狀態，然後按一下 [工作啟動] 欄中的時間戳記，以檢視工作摘要。

    ![Runbook Status][]

15. 在 [摘要] 頁面上，您可以檢視工作的摘要、輸入參數和輸出。

    ![Runbook Summary][]

# 從 Runbook 管理 Azure 服務

上述範例示範未管理 Azure 服務的範例 Runbook。[Azure Cmdlet][Azure PowerShell Cmdlet] 需要對 Azure 進行驗證。您可以遵循 [Azure 自動化：使用 Azure Active Directory 對 Azure 進行驗證][] (英文) 上的指示設定 Azure 訂用帳戶，以透過 Azure 自動化進行管理。

# 另請參閱

-   [自動化概觀][]
-   [Runbook 編寫指南][]
-   [自動化論壇][]
-   [Azure 自動化：使用 Azure Active Directory 對 Azure 進行驗證][] (英文)

  [自動化概觀指南]: http://go.microsoft.com/fwlink/p/?LinkId=392861
  [Azure PowerShell Cmdlet]: http://msdn.microsoft.com/zh-tw/library/jj156055.aspx
  [Azure 自動化：使用 Azure Active Directory 對 Azure 進行驗證]: http://azure.microsoft.com/blog/2014/08/27/azure-automation-authenticating-to-azure-using-azure-active-directory/
  [指令碼中心]: http://go.microsoft.com/fwlink/p/?LinkId=393029
  [1]: http://go.microsoft.com/fwlink/?LinkID=391681
  [使用者意見]: http://feedback.windowsazure.com/forums/34192--general-feedback
  [create-account-note]: ../includes/create-account-note.md
  [註冊自動化預覽版]: #preview
  [下載範例 Runbook]: #download-sample
  [匯入、執行及檢視範例 Runbook 的輸出]: #import-sample
  [啟用預覽]: ./media/automation/automation_00_EnablePreview.png
  [Azure 管理入口網站]: http://manage.windowsazure.com
  [Create Account]: ./media/automation/automation_01_CreateAccount.png
  [Add New Account]: ./media/automation/automation_02_addnewautoacct.png
  [New Account]: ./media/automation/automation_03_NewAutoAcct.png
  [Runbooks Tab]: ./media/automation/automation_04_RunbooksTab.png
  [Import]: ./media/automation/automation_05_Import.png
  [Browse]: ./media/automation/automation_06_Browse.png
  [Imported Runbook]: ./media/automation/automation_07_ImportedRunbook.png
  [Author Draft]: ./media/automation/automation_08_AuthorDraft.png
  [Publish]: ./media/automation/automation_085_Publish.png
  [Save and Pub prompt]: ./media/automation/automation_09_SavePubPrompt.png
  [Published]: ./media/automation/automation_10_PublishStart.png
  [Runbook Parameters]: ./media/automation/automation_11_RunbookParams.png
  [Runbook Status]: ./media/automation/automation_12_RunbookStatus.png
  [Runbook Summary]: ./media/automation/automation_13_RunbookSummary_callouts.png
  [自動化概觀]: http://go.microsoft.com/fwlink/p/?LinkId=392860
  [Runbook 編寫指南]: http://go.microsoft.com/fwlink/p/?LinkID=301740
  [自動化論壇]: http://go.microsoft.com/fwlink/p/?LinkId=390561
