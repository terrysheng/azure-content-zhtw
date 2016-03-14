# Azure 技術文件參與者指南

您已經找到發佈至 Azure 文件中心 [http://azure.microsoft.com/documentation](http://azure.microsoft.com/documentation) 之技術文件來源存放所在的 GitHub 儲存機制。

此儲存機制內也有相關指引，可協助您參與編輯我們的技術文件。如需參與者指南中的文章清單，請參閱[索引](https://github.com/Azure/azure-content/blob/master/contributor-guide/contributor-guide-index.md)。

## 參與編輯 Azure 文件

感謝您對於 Azure 文件感到興趣！

* [參與方法](#ways-to-contribute)
* [關於您對 Azure 內容的參與](#about-your-contributions-to-azure-content)
* [儲存機制組織方式](#repository-organization)
* [使用 GitHub、Git 和此儲存機制](#use-github-git-and-this-repository)
* [如何使用 Markdown 來格式化主題](#how-to-use-markdown-to-format-your-topic)
* [其他資源](#more-resources)
* [所有參與者指南文章的索引](./contributor-guide/contributor-guide-index.md) (會開啟新頁面)

## 參與方法

您可以透過以下幾種不同方法參與 [Azure 文件](http://azure.microsoft.com/documentation/)：

* 參與[論壇討論](http://social.msdn.microsoft.com/Forums/windowsazure/home)。
* 在文章最下方提交 Disqus 註解。
* 您可以在 GitHub 使用者介面輕鬆地參與技術文件。在此儲存機制中尋找文章，或瀏覽 [http://azure.microsoft.com/documentation](http://azure.microsoft.com/documentation) 上的文章，然後按一下文章中的連結來前往 GitHub 來源。
* 如果您要大幅變更現有文章、新增或變更圖片或是參與編輯新文章，您需要取用此儲存機制、安裝 Git Bash 和 MarkdownPad，並學習一些 git 命令。

##關於您對 Azure 內容的參與

###小幅度修正

您在此儲存機制中針對文件和程式碼範例所提交的小幅度修正或釐清，將受到 [Azure 網站使用規定 (ToU)](http://azure.microsoft.com/support/legal/website-terms-of-use/) 的約束。


###較大範圍的提交內容

如果您提交撤回要求是為了對文件和程式碼範例進行全新或大幅度的變更，而且您屬於下列其中一個團體，我們會在 GitHub 中傳送註解，要求您提交線上版的參與授權合約 (Contribution License Agreement, CLA)：

* Microsoft Open Technologies 團體的成員。
* 非任職於 Microsoft 的參與編輯者。

您必須先填寫線上表單，我們才會接受您的撤回要求。

如需完整詳細資料，請前往 [http://azure.github.io/guidelines.html#cla](http://azure.github.io/guidelines.html#cla)。

## 儲存機制組織方式

Azure 內容儲存機制中的內容會遵循 [Azure.Microsoft.com](http://azure.microsoft.com) 上之文件的組織方式。此儲存機制包含兩個根資料夾：

### \articles

*\articles* 資料夾包含格式化為 Markdown 檔的文件文章，其副檔名為 *.md*。

根目錄中的文章會發佈至 Azure.Microsoft.com 的 *http://azure.microsoft.com/documentation/articles/{article-name-without-md}/* 路徑中。

* **文章的檔名：**請參閱[檔案命名指引](./contributor-guide/file-names-and-locations.md)。

自有服務資料夾內的文章會發佈至 Azure.Microsoft.com 的 
*http://azure.microsoft.com/documentation/articles/service-folder/{article-name-without-md}/* 路徑中

* **媒體子資料夾：***\articles* 資料夾內有 *\media* 資料夾可供存放根目錄文章媒體檔，而在 media 資料夾內則是存有各文章內圖片的子資料夾。每個服務資料夾內都含有不同的媒體資料夾來存放文章。文章圖片資料夾的名稱和文章檔案的名稱相同，但不會有 *.md* 副檔名。

### \includes

您可以建立要加入到一個或多個文章中的可重複使用內容區段。請參閱[技術內容中使用的自訂延伸模組](./contributor-guide/custom-markdown-extensions.md)。

### \markdown templates

此資料夾包含標準的 Markdown 範本，而且此範本具有文章所需的基本 Markdown 格式。

### \contributor-guide

此資料夾包含屬於參與者指南的文章。

## 使用 GitHub、Git 和此儲存機制

如需如何參與、如何使用 GitHub UI 參與小型變更，以及如何取用和複製儲存機制以進行更大幅度參與的相關資訊，請參閱[在 GitHub 中安裝和設定編寫工具](./contributor-guide/tools-and-setup.md)。

如果您安裝 GitBash 並選擇在本機工作，[用來建立新文章或更新現有文章的 Git 命令](./contributor-guide/git-commands-for-master.md)中有列出用來建立新的本機工作分支、進行變更，以及將變更提交回主要分支的相關步驟。

### 分支

建議您建立目標鎖定在特定範圍之變更的本機工作分支。每個分支都應侷限在單一概念/文章，以簡化工作流程，並降低合併時發生衝突的可能性。以下是新分支適合的工作範圍：

* 新文章 (和相關圖片)
* 對文章的拼字和文法進行編輯。
* 對為數眾多的一組文章套用單一格式變更 (例如新的著作權頁尾)。

## 如何使用 Markdown 來格式化主題

此儲存機制中的所有文章都使用 GitHub 風格的 Markdown。其資源清單如下。

- [Markdown 基本概念](https://help.github.com/articles/markdown-basics/)

- [可列印的 Markdown 速查表](./contributor-guide/media/documents/markdown-cheatsheet.pdf?raw=true)

- 如需 Markdown 編輯器清單，請參閱[工具和安裝主題](./contributor-guide/tools-and-setup.md#install-a-markdown-editor)。

## 文章中繼資料

文章中繼資料可啟用 azure.microsoft.com 網站上的某些功能，例如作者屬性、參與者屬性、階層連結、文章說明和 SEO 最佳化，以及 Microsoft 用來評估內容成效的報告功能。因此，中繼資料非常重要！ [這裡有確認中繼資料的製作方式是否正確的指引](./contributor-guide/article-metadata.md)。

## 其他資源

如需所有指引主題，請參閱[參與者指南索引](./contributor-guide/contributor-guide-index.md)。

<!---HONumber=AcomDC_0307_2016-->