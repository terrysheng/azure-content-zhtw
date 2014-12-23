# 使用 Git 發佈至 Azure 網站

Azure 網站支援從原始程式碼控制和儲存機制工具 (如 BitBucket、CodePlex、Dropbox、Git、GitHub、Mercurial 和 TFS) 進行連續部署。您可以使用這些工具來維護網站的內容和程式碼，然後隨時都能快速輕鬆地將變更推播到網站。

在本文中，您將了解如何使用 Git 從本機電腦直接發佈到 Azure 網站 (在 Azure 中，這個發佈方法稱為「本機 Git」****)。您還能了解如何從儲存機制網站 (如 BitBucket、CodePlex、Dropbox、GitHub 或 Mercurial) 進行連續部署。如需有關使用 TFS 連續部署的詳細資訊，請參閱＜[使用 Visual Studio Online 持續傳遞至 Azure]＞(英文)。

> [WACOM.NOTE] [WACOM.NOTE] 本文中描述的許多 Git 命令會在使用<a href="/zh-tw/develop/nodejs/how-to-guides/command-line-tools/">適用於 Mac 和 Linux 的 Azure 命令列工具</a>建立網站時自動執行。

此工作包含下列步驟：

* [安裝 Git](#Step1)
* [建立本機儲存機制](#Step2)
* [新增網頁](#Step3)
* [啟用網站儲存機制](#Step4)
* [部署專案](#Step5)
	* [將本機檔案發佈至 Azure (本機 Git)](#Step6)
	* [部署儲存機制網站 (如 BitBucket、CodePlex、Dropbox、GitHub 或 Mercurial) 的檔案](#Step7)
	* [從 BitBucket、CodePlex、Dropbox、GitHub 或 Mercurial 部署 Visual Studio 方案](#Step75)
* [疑難排解](#Step8)

<h2><a id="Step2"></a>安裝 Git</h2>

安裝 Git 所需的步驟會因作業系統而有所不同。如需作業系統特定的發佈和安裝指引，請參閱[安裝 Git]。

> [WACOM.NOTE]在某些作業系統上，將提供 Git 的命令列和 GUI 兩種版本。本文提供的指示將使用命令列版本。

<h2><a id="Step2"></a>建立本機儲存機制</h2>

請執行下列工作以建立新的 Git 儲存機制。

1. 建立名為 MyGitRepository 的目錄，以包含您的 Git 儲存機制和網站檔案。

2. 開啟命令列，例如 **GitBash** (Windows) 或 **Bash** (Unix Shell)。在 OS X 系統上，您可以透過 **[終端機]** 應用程式來存取命令列。

3. 從命令列，切換至 MyGitRepository 目錄。

		cd MyGitRepository

4. 使用下列命令來初始化新的 Git 儲存機制：

		git init

	這應會傳回一則如下訊息：**已初始化 [path]** 中的空白 Git 儲存機制。

<h2><a id="Step3"></a>新增網頁</h2>

Azure 網站支援以各種程式設計語言建立的應用程式。以此範例為例，您將使用靜態 .html 檔案。如需以其他程式設計語言將網站發佈至 Azure 的相關資料，請參閱 [Azure 開發人員中心]。

1. 使用文字編輯器，在 Git 儲存機制的根目錄 (您稍早建立的 MyGitRepository 目錄) 中建立名為 **index.html** 的新檔案。

2. 新增下列文字作為 index.html 檔案的內容，然後儲存檔案。

		Hello Git!

3. 從命令列，驗證您位在 Git 儲存機制的根目錄。然後使用下列命令，將 **index.html** 檔案新增至儲存機制：

		git add index.html 

	> [WACOM.NOTE] 在任何 git 命令後輸入 -help 或 --help 即可尋找該命令的說明。例如，如需 add 命令的參數選項，請輸入 'git add -help' 以取得命令列說明，或輸入 'git add --help' 以取得更詳細的說明。

4. 接著，使用下列命令來認可對儲存機制的變更：

		git commit -m "Adding index.html to the repository"

	您應該會看到如下所示的輸出：

		[master (root-commit) 369a79c] Adding index.html to the repository
		 1 file changed, 1 insertion(+)
		 create mode 100644 index.html

<h2><a id="Step4"></a>啟用網站儲存機制</h2>

請執行下列步驟，使用 Azure 入口網站來啟用網站的 Git 儲存機制：

1. 登入 [Azure 入口網站]。

2. 按一下 [新增] 按鈕，以建立您將啟用儲存機制的新網站。

2. 請等待 [網站]**** 檢視中的網站建立程序完成，然後選取該網站。

	![An image displaying a selected web site][portal-select-website]

3. 選取 [儀表板]**** 索引標籤。

4. 在 [Quick Glance]**** 區段中，選取 [設定從原始檔控制進行部署]****。下列 [設定應用程式部署]**** 對話方塊隨即出現。

	![git-WhereIsYourSourceCode][git-WhereIsYourSourceCode]

4. 選擇 [本機 Git]****，然後按 [下一步]**** 箭號。

4. 如果這是您第一次在 Azure 中設定儲存機制，就需要為它建立登入認證。您將使用這些認證來登入 Azure 儲存機制，並推播來自您本機 Git 儲存機制的變更。 

	![](./media/publishing-with-git/git_credentials.png)
	
5. 在短暫延遲之後，您應該可以看到一則儲存機制已就緒的訊息。 

	![git-instructions][git-instructions]

<h2><a id="Step5"></a>部署專案</h2>

<h3><a id="Step6"></a>將本機檔案發佈至 Azure (本機 Git)</h3>

在此時，入口網站會顯示初始化本機儲存機制和新增檔案的指示。您已在本主題先前的步驟中完成此作業。但是，如果您不需設定部署認證，就必須返回入口網站中的 [儀表板]**** 索引標籤，然後按一下 [重設您的部署認證]****。

利用下列步驟，使用本機 Git 將網站發佈至 Azure：

1. 使用命令列，驗證您位在包含先前建立之 index.html 檔案的本機 Git 儲存機制的根目錄。

2. 複製入口網站所傳回指示的步驟 3 中所列的 git remote add 命令。它看起來類似如下命令：

		git remote add azure https://username@needsmoregit.scm.azurewebsites.net:443/NeedsMoreGit.git

    > [WACOM.NOTE] **remote** 命令會將指定的參考新增至遠端儲存機制。在此範例中，它會為您的 Azure 網站儲存機制建立名為 'azure' 的參考。

1. 在命令列中使用下列命令，將目前的儲存機制內容從本機儲存機制推送到 'azure' 遠端：

		git push azure master

	系統將會提示您輸入在入口網站中重設部署認證時所建立的密碼。輸入密碼 (請注意，當您輸入密碼時，Gitbash 不會對主控台回應星號)。您應該會看到如下所示的輸出：

		Counting objects: 6, done.
		Compressing objects: 100% (2/2), done.
		Writing objects: 100% (6/6), 486 bytes, done.
		Total 6 (delta 0), reused 0 (delta 0)
		remote: New deployment received.
		remote: Updating branch 'master'.
		remote: Preparing deployment for commit id '369a79c929'.
		remote: Preparing files for deployment.
		remote: Deployment successful.
		To https://username@needsmoregit.scm.azurewebsites.net:443/NeedsMoreGit.git
		* [new branch]		master -> master

	> [WACOM.NOTE] 為您的 Azure 網站建立的儲存機制，預期推播要求會選擇其儲存機制的 [主要]<strong></strong> 分支，然後將它用作網站內容。

2. 在入口網站中，按一下入口網站底部的 [瀏覽]**** 連結，以驗證 **index.html** 是否已部署完成。隨即會出現包含 'Hello Git!' 的頁面。

	![A webpage containing 'Hello Git!'][hello-git]

3. 使用文字編輯器，將 **index.html** 檔案內容變更成包含 'Yay!'，然後儲存檔案。

4. 在命令列中使用下列命令，來 [新增]**** 和 [認可]**** 變更，然後將變更 [推送]**** 到遠端儲存機制：

		git add index.html
		git commit -m "Celebration"
		git push azure master

	一旦完成 [推送]**** 命令之後，請重新整理瀏覽器 (您可能需要按 Ctrl+F5，瀏覽器才能正常重新整理)，並請注意頁面內容現已反映最新的認可變更。

	![A webpage containing 'Yay!'][yay]

<h3><a id="Step7"></a>部署來自儲存機制網站 (如 BitBucket、CodePlex、Dropbox、GitHub 或 Mercurial) 的檔案</h3>

使用本機 Git 將本機檔案推播至 Azure，可讓您將更新從本機專案手動推播至 Azure 網站，但若從 BitBucket、CodePlex、Dropbox、GitHub 或 Mercurial 部署則會轉變成連續部署程序，Azure 將從您的專案中提取最近的更新。

以上兩種方法的結果都是將專案部署到 Azure 網站，當您有多人同時執行專案，並想確保無論是誰做出最近更新都一定會發佈最新版本時，連續部署是非常有用的。如果您使用上述其中一個工具作為應用程式的中央儲存機制，連續部署也很有用。

無論是從 GitHub、CodePlex 或 BitBucket 部署檔案，都會要求您將本機專案發佈到這些服務的其中之一。如需將專案發佈到這些服務的詳細資訊，請參閱[建立儲存機制 (GitHub)]、[使用 Git 與 CodePlex]、[建立儲存機制 (BitBucket)]、[使用 Dropbox 分享 Git 儲存機制]或[快速入門 - Mercurial]。

1. 先將您的網站檔案放入將用於連續部署的所選儲存機制中。

2. 在網站的 Azure 入口網站中，移至 [儀表板]**** 索引標籤。在 [Quick Glance]**** 區段中，選取 [設定從原始檔控制進行部署]****。[設定部署]**** 對話方塊會隨即出現，詢問您 [您的原始程式碼在哪裡?]****。 

2. 選擇您要用於連續部署的原始檔控制方法。
	
3. 在系統出現提示時，為您選取的服務輸入認證。

4. 在您授權 Azure 存取您的帳戶之後，系統將會提示您一份儲存機制清單。 

	![git-ChooseARepositoryToDeploy][git-ChooseARepositoryToDeploy]
  
5. 選取您想要與 Azure 網站建立關聯的儲存機制。按一下核取記號以繼續。

	> [WACOM.NOTE] 啟用搭配 GitHub 或 BitBucket 的持續部署時，將會同時顯示公用和私人專案。

6. Azure 將與所選儲存機制建立關聯，然後從主要分支取得檔案。在此程序完成後，[部署]**** 頁面上的 [部署歷程記錄]**** 將出現一則如下所示的 [現用部署]**** 訊息：

	![git-githubdeployed][git-githubdeployed]

7. 此時，您已將專案從您選擇的儲存機制部署到 Azure 網站。若要驗證此網站是否正在使用中，請按一下入口網站底部的 [瀏覽]**** 連結。瀏覽器應會瀏覽至網站。

8. 若要確認是否會發生連續部署，請對您的專案進行變更，然後將此變更推播至與此網站相關聯的儲存機制。在推播至儲存機制完成後不久，您的網站應會進行更新以反映變更。您可以在網站的 [部署]**** 頁面上確認它是否已提取更新。

	![git-GitHubDeployed-Updated][git-GitHubDeployed-Updated]

<h3><a id="Step75"></a>從 BitBucket、CodePlex、Dropbox、GitHub 或 Mercurial 部署 Visual Studio 方案</h3>

將 Visual Studio 方案推播至 Azure 網站，就像推播簡單的 index.html 檔案一樣容易。Azure 網站部署程序會簡化所有細節，包含還原 NuGet 相依性，以及建置應用程式二進位檔。您可以遵循只在 Git 儲存機制中維護程式碼的原始檔控制最佳作法，然後讓 Azure 網站部署負責執行剩餘的部分。

將您的 Visual Studio 方案推播至 Azure 網站的步驟和[上一節](#Step7)的一樣，可提供您設定解決方案和儲存機制的步驟，如下：

-	在您的儲存機制根目錄中，新增 '.gitignore' 檔案，然後指定要從儲存機制排除的所有檔案和資料夾，例如 'Obj'、'Bin' 及 'packages' 資料夾 (如需詳細資訊，請參閱 [gitignore 文件](http://git-scm.com/docs/gitignore))。例如：

		[Oo]bj/
		[Bb]in/
		*.user
		/TestResults
		*.vspscc
		*.vssscc
		*.suo
		*.cache
		*.csproj.user
		packages/*
		App_Data/
		/apps
		msbuild.log
		_app/
		nuget.exe

	>[WACOM.NOTE] 如果您使用 GitHub，就能在建立儲存機制時選擇是否產生 Visual Studio 特有的 .gitignore 檔案，其中包含所有通用的暫存檔和建置結果等。接著，您可以自訂該檔案，以符合您的特定需求。

-	使用儲存機制根目錄中的 .sln 檔案，將整個解決方案的目錄樹狀結構新增至您的儲存機制。

-	在您的 Visual Studio 方案中，[啟用 NuGet 封裝還原](http://docs.nuget.org/docs/workflows/using-nuget-without-committing-packages)可讓 Visual Studio 自動還原遺失的封裝。

一旦您設定儲存機制 (如前所述)，並設定 Azure 網站以便從某一個線上 Git 儲存機制繼續進行發佈之後，就能夠在 Visual Studio 中本機開發 ASP.NET 應用程式，並且只需將變更推播至線上 Git 儲存機制，就能持續部署您的程式碼。

<h4>連續部署運作方式</h4>
連續部署的運作是透過提供 網站 [設定]**** 索引標籤中的 [部署]**** 區段中找到的「部署觸發程序 URL」****。

![git-DeploymentTrigger][git-DeploymentTrigger]

為您的儲存機制進行更新時，POST 要求會被傳送到此 URL，而您的 Azure 網站便會收到該儲存機制已更新的通知。此時，它會擷取更新並在您的網站上部署更新。

如需位於 Azure 網站之 Git 部署程序後方的引擎詳細資訊，請參閱[專案 Kudu](https://github.com/projectkudu/kudu/wiki) (英文)。

<h4>指定要使用的分支</h4>

啟用連續部署時，它會預設為儲存機制的 [主要]**** 分支。如果您要使用其他分支，請執行下列步驟：

1. 在入口網站中，選取您的網站，然後選取 [設定]****。

2. 在頁面的 [部署]**** 區段中，將您想要使用的分支輸入 [要部署的分支]**** 欄位，然後按 Enter 鍵。最後，按一下 [儲存]****。

	Azure 應會根據新分支的變更，立即開始進行更新。

<h4>停用連續部署</h4>

您可以從 Azure [儀表板]**** 中停用連續部署。在 [Quick Glance]**** 區段下，選擇要與正在使用的儲存機制中斷連線的選項：

![git-DisconnectFromGitHub][git-DisconnectFromGitHub]	

在對確認訊息回答 [是]**** 之後，如果您想要設定從其他原始檔進行發佈，您可以回到 [Quick Glance]**** 然後按一下 [設定從原始檔控制進行部署]****。

<h2><a id="Step8"></a>疑難排解</h2>

使用 Git 發佈至 Azure 網站時，經常會遇到下列錯誤或問題：

****

**徵兆**：無法存取 '[siteURL]'：無法連線至 [scmAddress]

**原因**：如果網站尚未啟動並執行，就會發生這個錯誤。

**解決方案**：在 Azure 入口網站中啟動網站。除非網站正在執行，則 Git 部署將無法運作。 


****

**徵兆**：無法解析主機 'hostname'

**原因**：如果建立 'azure' 遠端時所輸入的位址資訊不正確，便有可能發生此錯誤。

**解決方案**：使用 'git remote -v' 命令來列出所有遠端以及相關的 URL。驗證 'azure' 遠端的 URL 是否正確。如有需要，移除此遠端並使用正確的 URL 重新建立。

****

**徵兆**：通常沒有參考且沒有指定；不執行任何動作。或許您應該指定分支，例如 'master'。

**原因**：執行 git 推送操作時，如果您沒有指定分支，且沒有設定 Git 所使用的 push.default 值，便有可能發生此錯誤。

**解決方案**：指定主要分支，重新執行推送操作。例如：

	git push azure master

****

**徵兆**：src refspec [branchname] 沒有任何符合項目。

**原因**：如果您嘗試在 'azure' 遠端上推送至除了主要以外的分支，便有可能發生此錯誤。

**解決方案**：指定主要分支，重新執行推送操作。例如：

	git push azure master

****

**徵兆**：錯誤 - 對遠端儲存機制認可變更，但您的網站未更新。

**原因**：如果您打算部署包含 package.json 檔案的 Node.js 應用程式，但該檔案指出需要額外的模組，便有可能發生此錯誤。

**解決方案**：在發生此錯誤之前應該會有記錄其他包含 'npm ERR!' 的訊息，這些訊息可提供其他關於此失敗的內容。下列是此錯誤的已知原因及其對應的 'npm ERR!' 訊息：

* **格式錯誤的 package.json 檔案**：npm ERR!無法讀取相依性。

* **原生模組沒有適用於 Windows 的二進位檔發佈**：

	* npm ERR! \`cmd "/c" "node-gyp rebuild"\` failed with 1

		或

	* npm ERR! [modulename@version] preinstall: \`make || gmake\`


## 其他資源

* [如何使用適用於 Azure 的 PowerShell]
* [如何使用適用於 Mac 和 Linux 的 Azure 命令列工具]
* [Git 文件]
* [專案 Kudu](https://github.com/projectkudu/kudu/wiki)

[Azure 開發人員中心]: http://www.windowsazure.com/zh-tw/develop/overview/
[Azure 入口網站]: http://manage.windowsazure.com
[Git 網站]: http://git-scm.com
[安裝 Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[如何使用適用於 Azure 的 PowerShell]: http://www.windowsazure.com/zh-tw/develop/nodejs/how-to-guides/powershell-cmdlets/
[如何使用適用於 Mac 和 Linux 的 Azure 命令列工具]: /zh-tw/develop/nodejs/how-to-guides/command-line-tools/
[Git 文件]: http://git-scm.com/documentation

[portal-select-website]: ./media/publishing-with-git/git-select-website.png
[git-WhereIsYourSourceCode]: ./media/publishing-with-git/git-WhereIsYourSourceCode.png
[git-instructions]: ./media/publishing-with-git/git-instructions.png
[portal-deployment-credentials]: ./media/publishing-with-git/git-deployment-credentials.png

[git-ChooseARepositoryToDeploy]: ./media/publishing-with-git/git-ChooseARepositoryToDeploy.png
[hello-git]: ./media/publishing-with-git/git-hello-git.png
[yay]: ./media/publishing-with-git/git-yay.png
[git-githubdeployed]: ./media/publishing-with-git/git-GitHubDeployed.png
[git-GitHubDeployed-Updated]: ./media/publishing-with-git/git-GitHubDeployed-Updated.png
[git-DisconnectFromGitHub]: ./media/publishing-with-git/git-DisconnectFromGitHub.png
[git-DeploymentTrigger]: ./media/publishing-with-git/git-DeploymentTrigger.png
[建立儲存機制 (GitHub)]: https://help.github.com/articles/create-a-repo
[使用 Git 與 CodePlex]: http://codeplex.codeplex.com/wikipage?title=Using%20Git%20with%20CodePlex&referringTitle=Source%20control%20clients&ProjectName=codeplex
[建立儲存機制 (BitBucket)]: https://confluence.atlassian.com/display/BITBUCKET/Create+an+Account+and+a+Git+Repo
[快速入門 - Mercurial]: http://mercurial.selenic.com/wiki/QuickStart
[使用 Dropbox 分享 Git 儲存機制]: https://gist.github.com/trey/2722927
[使用 Visual Studio Online 連續傳遞至 Azure]: http://www.windowsazure.com/zh-tw/develop/net/common-tasks/publishing-with-tfs/
