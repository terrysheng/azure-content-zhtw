<properties urlDisplayName="How to create a VSO project and setup Continuous Deployment" pageTitle="如何建立 Visual Studio Online 小組專案和設定連續部署 - Windows Azure" metaKeywords="Visual Studio Online 建立小組專案, 對 Azure 的連續部署" description="了解如何建立 Visual Studio Online 團隊專案，並設定該專案用於持續部署至 Windows Azure。" metaCanonical="" services="cloud-services, visual-studio-online" documentationCenter="" title="How to Create and Deploy a Cloud Service" authors="jimlamb" solutions="" writer="jimlamb" manager="kamrani" editor=""  />

<tags ms.service="visual-studio-online" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jimlamb" />

#建立 Visual Studio Online 專案並對 Windows Azure 設定連續部署 

[WACOM.INCLUDE [disclaimer](../includes/disclaimer.md)]

Windows Azure 管理入口網站可讓您在 Visual Studio Online 上建立小組專案，並設定 Web 應用程式以進行網站的連續部署。

##目錄##

* [如何建立小組專案](#create_team_project)
* [如何建立新的 Web 應用程式，並將它新增至 Git 版本控制](#create_web_app)
* [如何設定連續部署](#continuous_deployment)

## <a name="create_team_project"></a>如何建立小組專案

1. 登入管理入口網站。
2. 在左下角，按一下 [**新增**]。
3. 按一下 [**小組專案**]
4. 提供小組專案名稱。請注意，在專案建立之後，您便無法變更小組專案的名稱。
5. 選擇您要在專案中使用的版本控制類型。您可以選擇 Git (分散式版本控制系統) 或 Team Foundation 版本控制 (集中式版本控制系統)。不確定要使用哪個系統？在[此處](http://msdn.microsoft.com/zh-tw/library/ms181368.aspx)深入了解。
6. 選擇流程範本。如需流程範本的比較，請參閱[使用小組專案成品](http://msdn.microsoft.com/zh-tw/library/ms400752.aspx)。
7. 選擇要用來建立小組專案、新增使用者，及監控資源使用的 Visual Studio Online 帳戶。
8. 將 [**新增至面板**] 核取方塊保留為勾選狀態，如此一來，您的新小組專案便會自動出現在您的開始面板上。
9. 按一下 [**建立**]。

## <a name="create_web_app"></a>如何建立新的 Web 應用程式，並將它新增至 Git 版本控制

1. 在 [開始面板] 中，按一下您的新小組專案。
2. 在 [**程式碼**] 鏡頭的 [**儲存機制**] 組件中，按一下以您的小組專案命名的 Git 儲存機制。
3. 在儲存機制/分支分頁中，按一下 **Visual Studio** 分頁層級命令，以便在 Visual Studio 中開啟您的新儲存機制。您的網頁瀏覽器可能會提示您授權啟動 Visual Studio。
4. 在 Visual Studio 的 [Team Explorer] 工具視窗中，按一下 [**複製這個儲存機制**]，以在本機磁碟上設定新儲存機制的本機副本。
5. 在 [Team Explorer] 首頁的 [**解決方案**] 區段中按一下 [**新增...**]，以在您剛剛複製的儲存機制中建立新專案。
6. 在 [新增專案] 對話方塊中，展開 Visual Basic 或 Visual C# 節點 (取決於您的程式設計語言喜好設定)，然後選取 [**Web**]。
7. 按一下可用專案範本清單中的 [**ASP.NET Web 應用程式**]，並輸入您的 Web 應用程式名稱。
8. 按一下 [**確定**]。
9. 切換至 [Team Explorer] 工具視窗，瀏覽到 [變更] 頁面，並輸入認可訊息。
10. 按一下 [**認可**] 按鈕上的下拉式箭頭，並按一下 [**認可並同步處理**]，以認可變更並將認可推送至您先前複製的遠端儲存機制。

## <a name="continuous_deployment"></a>如何設定連續部署

1. 登入管理入口網站。
2. 在 [開始面板] 中，按一下您先前建立的小組專案。
3. 在 [**建置**] 鏡頭中，按一下 [**設定連續部署**] 組件。
4. 選取您要部署 Web 應用程式的目標網站。
5. 選取原始程式碼所在的儲存機制 (在您的小組專案中，目前應該只有一個儲存機制)。
6. 選取要組建的分支。Visual Studio Online 將掃描此分支上的最新認可內容，並尋找單一 Visual Studio 解決方案檔案 (*.sln)。如果找到檔案，它便會設定新的組建定義 (名稱會以 "_CD" 結尾) 以組建該解決方案。如果找不到或找到多個解決方案檔案，則它仍會設定新的組建定義，但會是停用狀態，您必須在 Visual Studio 中進行編輯，以指定要組建的解決方案。 
7. 按一下 [**建立**]。
8. Visual Studio Online 將建立新的組建定義，以建立並部署您的 Web 應用程式專案，並嘗試將該定義的新組建排入佇列。

###確認部署是否成功完成###

1. 在 [開始面板] 中，按一下您先前建立的小組專案。
2. 在 [**建置**] 鏡頭中，按一下 [**最後的組建**] 組件，以開啟組建分頁。
3. 在組建分頁中，按一下 [**部署**] 組件中的第一個項目，以開啟相關網站。
4. 在網站分頁中，按一下 [**瀏覽**] 分頁層級命令，以瀏覽網站並確認 Web 應用程式的部署。
