<properties urlDisplayName="Browse and Edit Your Source Code" pageTitle="Browse and Edit Your Source Code | Azure" metaKeywords="Visual Studio Online, VSO, git, tfvc, edit, code, commit" description="Learn how to edit your source code." metaCanonical="" services="visual-studio-online" documentationCenter="" title="Browse and Edit Your Source Code" authors="ehollow" solutions="" manager="" editor="" />

<tags ms.service="visual-studio-online" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="ehollow" />

# 瀏覽與編輯原始程式碼

在 Microsoft Azure 入口網站預覽版中，您可以輕輕鬆鬆瀏覽與編輯r Visual Studio Online 小組專案原始程式碼。

在入口網站中完成建立小組專案後，無論是簽入 (Team Foundation 版本控制) 或已認可 ( git ) 程式碼，您都可以透過入口網站輕鬆存取與瀏覽程式碼。

1.  首先，開啟小組專案分頁，然後找到程式碼鏡頭。接著在來源部分，按一下您要瀏覽的儲存機制名稱 (在此案例中，我們稱它為 'BrowseCode')。注意：如果您有兩個以上的儲存機制，您可以按一下儲存機制編號 (在此案例中為 1) 以檢視完整清單。
    ![Code Lens][Code Lens]
2.  覝在，您的儲存機制分頁已開啟，您可以按一下程式碼部分，並開始瀏覽原始程式碼。
    ![Repository Blade][Repository Blade]
3.  提供整個專案的單獨樹狀檢視瀏覽分頁便會隨即顯示。您可以按一下節點以開啟資料夾，或按一下特定檔案 (在此案例中為 Index.cshtml) 以檢視或編輯檔案。
    ![Tree Navigation][Tree Navigation]
4.  若要編輯檔案，只需按一下命令列中的 [編輯] 按鈕即可。檔案內容會隨即進入編輯模式，您便可以進行變更。秘訣：按一下右上角的最大化按鈕以享受全螢幕編輯經驗。
    ![Edit Mode][Edit Mode]
5.  完成檔案編輯後，按一下命令列中的認可 (或簽入) 按鈕。這將啟動一個分頁，您可以在此處輸入認可訊息。完成時請按一下 [確定]，系統便會將檔案認可至儲存機制。
    ![Commit Code][Commit Code]
6.  分頁的頂端將顯示一則 Flash 訊息，指出已成功認可至儲存機制。
    ![Commit Success][Commit Success]

  [Code Lens]: ./media/visual-studio-online-browse-edit-source-code/Code-Lens.png
  [Repository Blade]: ./media/visual-studio-online-browse-edit-source-code/Repo-Blade.png
  [Tree Navigation]: ./media/visual-studio-online-browse-edit-source-code/Tree-Nav.png
  [Edit Mode]: ./media/visual-studio-online-browse-edit-source-code/Edit-Mode.png
  [Commit Code]: ./media/visual-studio-online-browse-edit-source-code/Commit-Code.png
  [Commit Success]: ./media/visual-studio-online-browse-edit-source-code/Commit-Success.png
