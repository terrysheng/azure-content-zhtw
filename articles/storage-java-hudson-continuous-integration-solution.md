<properties linkid="develop-java-how-to-hudson-ci" urlDisplayName="Hudson Continuous Integration" pageTitle="How to use Hudson with the Azure Blob service | Microsoft Azure" metaKeywords="Hudson, Azure storage, Azure Blob service, Azure storage, Azure hudson" description="Describes how to use Hudson with Azure Blob storage as a repository for build artifacts." metaCanonical="" services="storage" documentationCenter="Java" title="Using Azure Storage with a Hudson Continuous Integration solution" authors="waltpo" solutions="" manager="" editor="mollybos" />

使用 Azure 儲存體與 Hudson 連續整合解決方案
===========================================

*作者：[Microsoft Open Technologies Inc.](http://msopentech.com)*

下列資訊說明如何使用 Azure Blob 服務，作為 Hudson 連續整合 (CI) 解決方案所建立的組建成品儲存機制。您可能會在下列其中一個情況下，當在便捷的部署環境 (使用 Java 或其他語言) 中進行編碼、根據連續整合執行組建，以及需要組建成品的儲存機制時，發現這一點很有用，因此，例如，您可以將他們與其他組織成員、客戶分享或維護封存檔。

在本教學課程中，您將使用由 Microsoft Open Technologies, Inc. 所提供的 Azure Storage Plugin for Hudson CI。

目錄
----

-   [Hudson 概觀](#overview)
-   [使用 Blob 服務的好處](#benefits)
-   [必要條件](#prerequisites)
-   [如何使用 Blob 服務與 Hudson CI](#howtouse)
-   [如何安裝 Azure 儲存體外掛程式](#howtoinstall)
-   [如何設定 Azure 儲存體外掛程式使用您的儲存體帳戶](#howtoconfigure)
-   [如何建立可將組建成品上傳至儲存體帳戶的建置後動作](#howtocreatepostbuild)
-   [由 Blob 服務所使用的元件](#components)

概觀Hudson 概觀
---------------

Hudson 提供軟體專案的連續整合，方法是允許開發人員輕易整合其程式碼變更，並可自動和頻繁地產生組建，進而提高開發人員的產能。組建已建立版本，且您可以將組建成品上傳至不同的儲存機制。本主題將說明如何使用 Azure Blob 儲存體作為組建成品的儲存機制。

您可以在[認識 Hudson](http://wiki.eclipse.org/Hudson-ci/Meet_Hudson) (英文) 中找到有關 Hudson 的詳細資訊。

好處使用 Blob 服務的好處
------------------------

使用 Blob 服務來主控便捷開發組建成品的好處包括：

-   組建成品的高可用性。
-   Hudson CI 解決方案上傳組建成品時的效能。
-   客戶和合作夥伴下載組建成品時的效能。
-   透過使用者存取原則進行控制，選擇包括匿名存取、到期日型共用存取簽章存取、私人存取等。

必要條件必要條件
----------------

您將需要下列項目來使用 Blob 服務與 Hudson CI 解決方案：

-   Hudson 連續整合解決方案。

    如果您目前沒有 Hudson CI 解決方案，您可以使用下列技巧來執行 Hudson CI 解決方案：

    1.  在已啟用 Java 的機器上，從 <http://hudson-ci.org/> (英文) 下載 Hudson WAR。
    2.  在命令提示字元中，會開啟至包含 Hudson WAR 的資料夾，並執行 Hudson WAR。例如，如果您下載版本 3.0.1：

        `java -jar hudson-3.0.1.war`

    3.  在您的瀏覽器中，開啟 `http://localhost:8080/`。這將會開啟 Hudson 儀表板。

    4.  第一次使用 Hudson 時，請在 `http://localhost:8080/` 中完成初始設定。

    5.  在完成初始設定之後，取消執行中的 Hudson WAR 執行個體，重新啟動 Hudson WAR，並重新開啟 Hudson 儀表板：`http://localhost:8080/`，您可以使用此儀表板來安裝與設定 Azure 儲存體外掛程式。

        雖然典型的 Hudson CI 解決方案可設定作為服務執行，但在本教學課程中，您可以直接在命令列中執行 Hudson war。

-   Azure 帳戶。您可以註冊 Azure 帳戶，網址是 <http://www.windowsazure.com> (英文)。

-   Azure 儲存體帳戶。如果您還沒有儲存體帳戶，您可以使用[如何建立儲存體帳戶](http://go.microsoft.com/fwlink/?LinkId=279823) (英文) 上的步驟建立一個儲存體帳戶。

-   建議學習 Hudson CI 解決方案，但這不是必須的，因為下列內容會使用基本範例來說明使用 Blob 服務作為 Hudson CI 組建成品的儲存機制時的所需步驟。

如何使用 Blob 服務如何使用 Blob 服務與 Hudson CI
------------------------------------------------

若要使用 Blob 服務與 Hudson，您必須安裝 Azure 儲存體外掛程式，設定此外掛程式使用您的儲存體帳戶，然後建立可將組建成品上傳至儲存體帳戶的建置後動作。這些步驟將在後續章節中進行說明。

如何安裝如何安裝 Azure 儲存體外掛程式
-------------------------------------

1.  在 Hudson 儀表板中，按一下 **[Manage Hudson]**。
2.  在 **[Manage Hudson]** 頁面中，按一下 **[管理外掛程式]**。
3.  按一下 **[可用]** 索引標籤。
4.  按一下 **[其他]**。
5.  在 **[Artifact Uploaders]** 區段中，勾選 **[Azure Storage plugin]**。
6.  按一下 **[Install]**。
7.  在安裝完成後，請重新啟動 Hudson。

如何設定如何設定 Azure 儲存體外掛程式使用您的儲存體帳戶
-------------------------------------------------------

1.  在 Hudson 儀表板中，按一下 **[Manage Hudson]**。
2.  在 **[Manage Hudson]** 頁面中，按一下 **[設定系統]**。
3.  在 **[Azure Storage Account Configuration]** 區段中：
    1.  輸入您可從 Azure 入口網站 <https://manage.windowsazure.com> (英文) 中取得的儲存體帳戶名稱。
    2.  輸入應也可從 Azure 入口網站中取得的儲存體帳戶金鑰。
    3.  如果使用公用 Azure 雲端，在 **[Blob Service Endpoint URL]** 中請使用預設值。如果使用不同的 Azure 雲端，請使用您的儲存體帳戶在 Azure 管理入口網站中所指定的端點。
    4.  按一下 **[Validate Storage Credentials]** 以驗證您的儲存體帳戶。
    5.  [選用] 如果您有其他可提供 Hudson CI 使用的儲存體帳戶，請按一下 **[Add more Storage Accounts]**。
    6.  按一下 **[儲存]** 以儲存您的設定。

如何建立建置後動作如何建立可將組建成品上傳至儲存體帳戶的建置後動作
------------------------------------------------------------------

為了方便說明，我們首先必須建立一個將建立數個檔案的工作，然後加入建置後動作來將檔案上傳至您的儲存體帳戶。

1.  在 Hudson 儀表板中，按一下 **[新增工作]**。
2.  將此工作命名為 **MyJob**，按一下 **[Build a free-style software job]**，然後按一下 **[確定]**。
3.  在工作組態的 **[Build]** 區段中，按一下 **[Add build step]**，並選擇 **[Execute Windows batch command]**。
4.  在 **[命令]** 中，請使用下列命令：

        md text
        cd text
        echo Hello Azure Storage from Hudson > hello.txt
        date /t > date.txt
        time /t >> date.txt

5.  在工作組態的 **[Post-build Actions]** 區段中，按一下 **[Upload artifacts to Azure Blob storage]**。
6.  在 **[儲存體帳戶名稱]** 中選取要使用的儲存體帳戶。
7.  在 **[容器名稱]** 中指定容器名稱。(上傳組建成品時，如果容器尚未存在，則系統會建立容器。)您可以使用環境變數，因此，在此範例中，請輸入 **${JOB\_NAME}** 作為容器名稱。

    **秘訣**

    在您為 **[Execute Windows batch command]** 輸入指令碼的 **[命令]** 區段下方是個連結，可連結到 Hudson 認可的環境變數。按一下該連結以了解環境變數名稱和說明。請注意，容器名稱或常見虛擬路徑中不允許包含特殊字元的環境變數，例如 **BUILD\_URL** 環境變數。

8.  在此範例中，請按一下 **[Make container public]**。(若要使用私人容器，您必須建立共用存取簽章以允許存取。這超出了本主題的範圍。您可以參閱[建立共用存取簽章](http://go.microsoft.com/fwlink/?LinkId=279889) (英文) 以深入了解共用存取簽章。)
9.  在 **[List of Artifacts to upload]** 中輸入 **text/\*.txt**。
10. 在 **[Common virtual path for uploaded artifacts]** 中輸入 **${BUILD\_ID}/${BUILD\_NUMBER}**。
11. 按一下 **[儲存]** 以儲存您的設定。
12. 在 Hudson 儀表板中，按一下 **[Build Now]** 以執行 **MyJob**。檢查主控台輸出以取得狀態資訊。當建置後動作開始上傳組建成品時，主控台輸出將會包含 Azure 儲存體的狀態訊息。
13. 順利完成工作時，您可以透過開啟公用 Blob 來檢查組建成品。
    1.  登入 Azure 管理入口網站 <https://manage.windowsazure.com> (英文)。
    2.  按一下 **[儲存體]**。
    3.  按一下您在 Hudson 中使用的儲存體帳戶名稱。
    4.  按一下 **[容器]**。
    5.  按一下名為 **myjob** 的容器，這是您在建立 Hudson 工作時所指定工作名稱的小寫版本。在 Azure 儲存體中，容器名稱和 Blob 名稱為小寫 (且區分大小寫)。在名為 **myjob** 的容器 Blob 清單中，您應會看到 **hello.txt** 和 **date.txt**。複製這些項目的任一 URL，並在瀏覽器中將其開啟。您將會看到作為組建成品上傳的文字檔。

Blob 服務元件用於 Blob 服務的元件
---------------------------------

下列提供了 Blob 服務元件的概觀。

-   **儲存體帳戶**：一律透過儲存體帳戶來存取 Azure 儲存體。這是存取 blob 用的最高等級的命名空間。帳戶可以包含不限數目的容器，只要它們的大小總計低於 100TB 即可。
-   **容器**：容器提供一組 Blob 的群組。所有 Blob 都必須放在容器中。一個帳戶可以包含的容器不限數量。容器可以儲存無限制的 Blob。
-   **Blob**：任何類型和大小的檔案。Azure 儲存中可以儲存兩種 Blob：區塊和分頁 Blob。大部分檔案都是區塊 Blob。單一區塊 Blob 的大小上限為 200GB。本教學課程使用區塊 Blob。分頁 Blob (另一種 Blob 類型) 的大小上限為 1TB，當檔案中的位元組範圍經常修改時，分頁 Blob 的效率較高。如需關於 Blob 的詳細資訊，請參閱[了解區塊 Blob 和分頁 Blob](http://msdn.microsoft.com/zh-tw/library/windowsazure/ee691964.aspx)。
-   **URL 格式**：可利用下列 URL 格式來定址 Blob：

    `http://storageaccount.blob.core.windows.net/container_name/blob_name`

    (上述格式適用於公用 Azure 雲端。如果使用其他 Azure 雲端，請使用 Azure 管理入口網站內的端點來判斷您的 URL 端點。)

    在上述格式中，`storageaccount` 代表您的儲存體帳戶名稱，`container_name` 代表您的容器名稱，而 `blob_name` 代表您的 Blob 名稱。在容器名稱中，您可以包含多個路徑 (以正斜線 **/** 分隔)。本教學課程中的範例容器名稱為 **MyJob**，並在常見虛擬路徑中使用 **${BUILD\_ID}/${BUILD\_NUMBER}**，這會產生包含下列形式 URL 的 Blob：

    `http://example.blob.core.windows.net/myjob/2013-06-06_11-56-22/1/hello.txt`


