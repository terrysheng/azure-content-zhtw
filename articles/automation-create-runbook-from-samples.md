<properties urlDisplayName="Get Started with Azure Automation" pageTitle="開始使用 Azure 自動化" metaKeywords="" description="了解如何在 Azure 中匯入和執行自動化工作。" metaCanonical="" services="automation" documentationCenter="" title="開始使用 Azure 自動化" authors="bwren" solutions="" manager="stevenka" editor="" />

<tags ms.service="automation" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="bwren" />

# 開始使用 Azure 自動化

Microsoft Azure 自動化可讓開發人員將執行於雲端環境中的手動、長時間執行、易發生錯誤且重複性高的工作自動化。您可以使用 Runbook 來建立、監視、管理及部署 Azure 環境中的資源；而所謂的 Runbook，基本上就是 Windows PowerShell 工作流程。若要深入了解自動化，請參閱[自動化概觀指南][自動化概觀指南] (英文)。

本教學課程將引導您逐步執行將範例 "Hello World" Runbook 匯入 Azure 自動化中、執行 Runbook 並檢視其輸出的步驟。

> [WACOM.NOTE] 若要了解如何使用 [Azure PowerShell Cmdlet][Azure PowerShell Cmdlet] 自動化 Azure 作業，請參閱 [Azure 自動化：使用 Azure Active Directory 對 Azure 進行驗證][Azure 自動化：使用 Azure Active Directory 對 Azure 進行驗證] (英文)。

## 範例和工具 Runbook

Azure 自動化團隊建立了多種 Runbook 範例，以協助您著手使用「自動化」。這些範例運用了基本自動化概念，旨在協助您了解如何自行撰寫 Runbook。

自動化團隊也建立了工具 Runbook，可供您做為大型自動化工作的基礎。

> [WACOM.NOTE] 最理想的作法是撰寫小型、模組化、可重複使用的 Runbook。我們也強烈建議您在熟悉自動化之後，針對常用的案例建立您自己的公用程式 Runbook。

您可以在[指令碼中心][指令碼中心]檢視並下載自動化團隊的範例和公用程式 Runbook，或直接從 [Runbook 組件庫][Runbook 組件庫] (英文) 下載。

## 自動化社群和意見反應

由社群和其他 Microsoft 團隊提供的 Runbook，也會發佈在[指令碼中心][1]和 [Runbook 組件庫][Runbook 組件庫] (英文)。

**請不吝提供意見。**如果您要尋找自動化 Runbook 解決方案或整合模組，請在指令碼中心提出「指令碼要求」。如果您對於自動化的新功能有任何構想，請將其發佈至[使用者意見][使用者意見] (英文)。

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

## 本教學課程的高階步驟

1.  [註冊自動化預覽版][註冊自動化預覽版]
2.  [從 Runbook 組件庫匯入 Runbook][從 Runbook 組件庫匯入 Runbook]
3.  [發行 Runbook][發行 Runbook]
4.  [啟動 Runbook][啟動 Runbook]

## <a name="preview"></a>註冊 Azure 自動化預覽版

若要開始使用自動化，您需要已啟用 Microsoft Azure 自動化預覽功能的作用中 Azure 訂用帳戶。

-   在 [預覽功能] 頁面上，按一下 [立即試用]。

    ![啟用預覽][啟用預覽]

## <a name="automationaccount"></a>建立自動化帳戶

1.  登入 [Azure 管理入口網站][Azure 管理入口網站]。

2.  在管理入口網站中，按一下 [Create an Automation Account]。

    > [WACOM.NOTE] 如果您已建立自動化帳戶，您可以跳至步驟 4。

    ![Create Account][Create Account]

3.  在 [Add a New Automation Account] 頁面上輸入帳戶的名稱，然後按一下核取記號。

    ![Add New Account][Add New Account]

## <a name="importrunbook"></a>從 Runbook 組件庫匯入 Runbook

1.  在 [自動化] 頁面上，按一下您剛剛建立的新帳戶。

    ![New Account][New Account]

2.  按一下 [Runbook]。

    ![Runbooks Tab][Runbooks Tab]

3.  按一下 [新增] \> [Runbook] \> [從組件庫]。

    ![Runbook Gallery][Runbook Gallery]

4.  選取 [教學課程] 類別，然後選取 [Hello World for Azure Automation]。按一下向右箭頭按鈕。

    ![Import Runbook][Import Runbook]

5.  檢閱 Runbook 的內容，然後按一下向右箭頭按鈕。

    ![Runbook Definition][Runbook Definition]

6.  檢閱 Runbook 的詳細資料，然後按一下勾選記號按鈕。

    ![Runbook Details][Runbook Details]

## <a name="publishrunbook"></a>發行 Runbook

1.  當 Runbook 匯入完成時，按一下 [Write-HelloWorld]。

    ![Imported Runbook][Imported Runbook]

2.  按一下 [作者]，然後按一下 [草稿]。

    您可以在 [草稿] 模式下修改 Runbook 的內容。對這個 Runbook 無須進行任何修改。

    ![Author Draft][Author Draft]

3.  按一下 [發佈] 提升 Runbook，使其可用於生產環境中。

    ![Publish][Publish]

4.  在出現儲存及發佈 Runbook 的提示時，按一下 [是]。

    ![Save and Pub prompt][Save and Pub prompt]

## <a name="startrunbook"></a>啟動 Runbook

1.  開啟 **Write-HelloWorld** Runbook 後，按一下 [啟動]。

    ![Published][Published]

2.  在 [Specify the runbook parameter values] 頁面上，輸入將做為 Write-HelloWorld.ps1 指令碼之輸入參數的 [名稱]，然後按一下核取記號。

    ![Runbook Parameters][Runbook Parameters]

3.  按一下 [JOBS] 以查看您剛剛啟動之 Runbook 工作的狀態，然後按一下 [工作啟動] 欄中的時間戳記，以檢視工作摘要。

    ![Runbook Status][Runbook Status]

4.  在 [摘要] 頁面上，您可以檢視工作的摘要、輸入參數和輸出。

    ![Runbook Summary][Runbook Summary]

# 從 Runbook 管理 Azure 服務

上述範例示範未管理 Azure 服務的範例 Runbook。[Azure Cmdlet][Azure PowerShell Cmdlet] 需要對 Azure 進行驗證。您可以遵循 [Azure 自動化：使用 Azure Active Directory 對 Azure 進行驗證][Azure 自動化：使用 Azure Active Directory 對 Azure 進行驗證] (英文) 上的指示設定 Azure 訂用帳戶，以透過 Azure 自動化進行管理。

# 另請參閱

-   [自動化概觀][自動化概觀]
-   [Runbook 編寫指南][Runbook 編寫指南]
-   [自動化論壇][自動化論壇]
-   [Azure 自動化：使用 Azure Active Directory 對 Azure 進行驗證][Azure 自動化：使用 Azure Active Directory 對 Azure 進行驗證] (英文)

  [自動化概觀指南]: http://go.microsoft.com/fwlink/p/?LinkId=392861
  [Azure PowerShell Cmdlet]: http://msdn.microsoft.com/zh-tw/library/jj156055.aspx
  [Azure 自動化：使用 Azure Active Directory 對 Azure 進行驗證]: http://azure.microsoft.com/blog/2014/08/27/azure-automation-authenticating-to-azure-using-azure-active-directory/
  [指令碼中心]: http://go.microsoft.com/fwlink/p/?LinkId=393029
  [Runbook 組件庫]: http://aka.ms/runbookgallery
  [1]: http://go.microsoft.com/fwlink/?LinkID=391681
  [使用者意見]: http://feedback.windowsazure.com/forums/34192--general-feedback
  [註冊自動化預覽版]: #automationaccount
  [從 Runbook 組件庫匯入 Runbook]: #importrunbook
  [發行 Runbook]: #publishrunbook
  [啟動 Runbook]: #startrunbook
  [啟用預覽]: ./media/automation/automation_00_EnablePreview.png
  [Azure 管理入口網站]: http://manage.windowsazure.com
  [Create Account]: ./media/automation/automation_01_CreateAccount.png
  [Add New Account]: ./media/automation/automation_02_addnewautoacct.png
  [New Account]: ./media/automation/automation_03_NewAutoAcct.png
  [Runbooks Tab]: ./media/automation/automation_04_RunbooksTab.png
  [Runbook Gallery]: ./media/automation/automation_05_ImportGallery.png
  [Import Runbook]: ./media/automation/automation_06_ImportRunbook.png
  [Runbook Definition]: ./media/automation/automation_07_RunbookDefinition.png
  [Runbook Details]: ./media/automation/automation_08_RunbookDetails.png
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
