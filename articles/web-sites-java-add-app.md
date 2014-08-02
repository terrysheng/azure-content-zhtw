<properties linkid="develop-java-tutorials-web-site-add-app" urlDisplayName="Add an application to your Java web site" pageTitle="Add an application to your Java web site" metaKeywords="" description="This tutorial shows you how to add a page or application to your Java web site on Microsoft Azure." metaCanonical="" services="web-sites" documentationCenter="Java" title="Add an application to your Java web site" videoId="" scriptId="" authors="waltpo" solutions="" manager="keboyd" editor="mollybos" />

在 Azure 上將應用程式新增至 Java 網站
=====================================

本教學課程說明如何將網頁或應用程式，加入先前使用 Azure 組件庫或 Azure 組態 UI 所建立的 Java 網站中。

請注意，您可以使用原始檔控制來上傳應用程式或網頁，這會納入連續整合案例。如需在網站中使用原始檔控制的指示，請參閱[從原始檔控制發佈至 Azure 網站](../web-sites-publish-source-control) (英文)。作為原始檔控制的替代方式，本文將說明如何使用 FTP 上傳應用程式。

本教學課程假設您已完成[開始使用 Microsoft Azure 網站和 Java](../web-sites-java-get-started) 中的步驟。

使用 FTP 自訂網站
=================

您必須確認您的 FTP 認證，並使用這些認證存取網站內容。接著，您可以修改內容以便執行應用程式。以下所示範例將會使用 [檔案總管] 來進行 FTP，但您也可以使用其他 FTP 技術。

使用 FTP 認證存取網站內容
-------------------------

1.  在 Microsoft Azure 管理入口網站中，瀏覽到 **[網站]** 檢視。
2.  在 **[網站]** 檢視中，按一下您網站的名稱。
3.  按一下 **[儀表板]**。
4.  在 **[儀表板]** 檢視的 **[快速瀏覽]** 下，按一下 **[Download the publish profile]**。在本機上儲存此檔案。請務必保障此檔案的安全，因為它包含允許發佈至網站 (以及從網站中複製內容) 的使用者名稱和密碼。
5.  使用文字編輯器開啟已下載的發佈設定檔案。在該檔案中，請記下 **userName** 和 **userPwd** 的值。他們分別代表可用來存取網站上檔案的使用者名稱和密碼。
6.  存取網站的檔案，並在出現提示時提供使用者名稱和密碼。本範例將從 Internet Explorer 內使用 FTP，但您也可以使用其他技術。若要繼續使用 FTP，請在 **[儀表板]** 檢視中，按一下列在 **[FTP 主機名稱]** 下的 URL。(您也可以從發佈設定檔案中確認 FTP 主機名稱，也就是指派給 **publishUrl** 的值。)
7.  當出現輸入使用者名稱和密碼的提示時，在 **userName** 和 **userPwd** 中使用在發佈設定檔案中所指定的值。
8.  在 Internet Explorer 中，若要切換成 [檔案總管] 檢視，請依序按一下 **[檢視]**、**[在檔案總管中開啟 FTP 站台]**。

存取網站的 webapps 資料夾
-------------------------

在 Azure 網站上的 [檔案總管] 檢視內，您現在可以自訂網站。您必須將應用程式複製到網站的 **webapps** 資料夾。該資料夾的導覽路徑會根據網站的設定方式而有所不同。

-   如果您透過使用 Azure 應用程式組件庫來設定網站，則在 [檔案總管] 內依序按兩下 **[網站]**、**wwwroot**、**bin**、網站中所使用的應用程式伺服器版本，接著按兩下 **webapps**。
-   如果您透過使用 Azure 組態 UI 來設定網站，則在 [檔案總管] 內依序按兩下 **[網站]**、**wwwroot**，接著按兩下 **webapps**。
-   如果您透過使用自訂上傳來設定網站，則視需要瀏覽到 **webapps** 資料夾。

若要使用 FTP 將 WAR 檔案新增至網站
----------------------------------

1.  使用適用於網站的技術瀏覽到 **webapps** 資料夾，如上所述。
2.  將 WAR 檔案複製到 **webapps** 資料夾。

應用程式伺服器將會偵測到您已新增 WAR 檔案，並會將其自動載入。您接著可以透過網站的 URL 及其附加的 WAR 檔案名稱，在瀏覽器中執行應用程式。

例如，瀏覽到 http://*mysitename*.azurewebsites.net/*mywar*，其中 *mysitename* 是指您為 URL 指定的名稱，而 *mywar* 是指您所複製 WAR 的區分大小寫名稱 (去掉結尾的 **.war**)。

若要使用 FTP 將網頁新增至網站
-----------------------------

1.  瀏覽到 **webapps** 資料夾。
2.  在 **webapps** 資料夾內建立新的資料夾。
3.  開啟新資料夾。
4.  將網頁新增至新資料夾。

應用程式伺服器將會偵測到您已新增資料夾和 Web 檔案，並會將其自動載入。接著，使用 URL 執行 JSP 檔案，URL 格式為 http://*mysitename*.azurewebsites.net/*myfolder*/*myfile.jsp*，其中 *mysitename* 是指您為 URL 指定的名稱，*myfolder* 是指您在 **webapps** 中所建立的資料夾，而 *myfile.jsp* 是指您所建立的 JSP 檔案名稱。

請注意，如果您將檔案 (WAR 檔案除外) 複製到根目錄，則在使用這些檔案之前，必須重新啟動應用程式伺服器。在 Azure 上執行的 Java 網站，其自動載入功能會視新增的 WAR 檔案或新增至 **webapps** 資料夾的新檔案或目錄而定。

