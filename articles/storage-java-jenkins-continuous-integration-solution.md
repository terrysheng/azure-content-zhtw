<properties linkid="develop-java-tutorials-jenkins-continuous-integration" urlDisplayName="Jenkins Continuous Integration" pageTitle="Using Azure Storage with a Jenkins Continuous Integration Solution | Microsoft Azure" metaKeywords="" description="This tutorial show how to use the Azure blob service as a repository for build artifacts created by a Jenkins continuous integration solution." metaCanonical="" services="storage" documentationCenter="Java" title="Using Azure Storage with a Jenkins Continuous Integration solution" authors="waltpo" solutions="" manager="bjsmith" editor="mollybos" />

使用 Azure 儲存體搭配 Jenkins 連續整合解決方案
==============================================

*作者：[Microsoft Open Technologies Inc.](http://msopentech.com) (英文)*

下列資訊示範如何使用 Azure Blob 服務作為 Jenkins 連續整合 (CI) 解決方案所建立之組建成品的存放庫。您會發現這很實用的其中一種情況就是，當您在敏捷式開發環境中編寫程式碼 (使用 Java 或其他語言) 時，組建是根據連續整合來執行，而您需要一個存放庫來存放組建成品，以便能夠將這些成品分享給其他組織成員或客戶，或是維護封存等等。

在本教學課程中，您將使用由 Microsoft Open Technologies, Inc. 提供的適用於 Jenkins CI 的 Azure 儲存體外掛程式。

目錄
----

-   [Jenkins 概觀](#overview)
-   [使用 Blob 服務的優點](#benefits)
-   [必要條件](#prerequisites)
-   [如何使用 Blob 服務搭配 Jenkins CI](#howtouse)
-   [如何安裝 Azure 儲存體外掛程式](#howtoinstall)
-   [如何設定 Azure 儲存體外掛程式來使用您的儲存體帳戶](#howtoconfigure)
-   [如何建立會將您的組建成品上傳至您儲存體帳戶的建置後動作](#howtocreatepostbuild)
-   [Blob 服務所使用的元件](#components)

概觀Jenkins 概觀
----------------

Jenkins 透過讓開發人員輕鬆整合自己的程式碼變更，並會以自動且頻繁的方式產生組建，來實現軟體專案的連續整合，從而提升開發人員產能。組建會分版本存在，而組建成品可以上傳至各種存放庫。本主題將示範如何使用 Azure Blob 儲存體作為組建成品的存放庫。

如需 Jenkins 的詳細資訊，請參閱[認識 Jenkins](https://wiki.jenkins-ci.org/display/JENKINS/Meet+Jenkins) (英文)。

優點使用 Blob 服務的優點
------------------------

使用 Blob 服務來裝載您敏捷式開發組建成品的優點包括：

-   讓您的組建成品具有高可用性。
-   在您的 Jenkins CI 解決方案上傳組建成品時提供良好的效能。
-   在您的客戶和合作夥伴下載組建成品時提供良好的效能。
-   提供使用者存取原則控制，可以選擇匿名存取、期限型共用存取簽章存取、私用存取等。

必要條件必要條件
----------------

若要使用 Blob 服務搭配 Jenkins CI 解決方案，您將需要下列項目：

-   一套 Jenkins 連續整合解決方案。

    如果您目前沒有 Jenkins CI 解決方案，可以透過下列步驟執行 Jenkins CI 解決方案：

    1.  在已啟用 Java 的電腦上，從 <http://jenkins-ci.org> (英文) 下載 jenkins.war。
    2.  在命令提示字元中切換至包含 jenkins.war 的資料夾，然後執行：

        `java -jar jenkins.war`

    3.  在瀏覽器中，開啟 `http://localhost:8080/`。這樣會開啟 Jenkins 儀表板，您將使用此儀表板來安裝及設定 Azure 儲存體外掛程式。

        雖然一般 Jenkins CI 解決方案會設定成以服務的形式執行，但對本教學課程來說，在命令列執行 Jenkins war 已經足夠。

-   一個 Azure 帳戶。您可以在 <http://www.windowsazure.com> 註冊 Azure 帳戶。

-   一個 Azure 儲存體帳戶。如果您還沒有儲存體帳戶，可以使用[如何建立儲存體帳戶](http://go.microsoft.com/fwlink/?LinkId=279823)中的步驟建立一個帳戶。

-   建議您熟悉 Jenkins CI 解決方案，但這並非必要，因為下列內容將使用一個基本範例來示範使用 Blob 服務作為 Jenkins CI 組建成品的存放庫時所需的步驟。

如何使用 Blob 服務如何使用 Blob 服務搭配 Jenkins CI
---------------------------------------------------

若要使用 Blob 服務搭配 Jenkins，您將要安裝 Azure 儲存體外掛程式、設定外掛程式來使用您的儲存體帳戶，然後建立一個會將您的組建成品上傳至您儲存體帳戶的建置後動作。這些步驟將於下列各節中說明。

如何安裝如何安裝 Azure 儲存體外掛程式
-------------------------------------

1.  在 Jenkins 儀表板中，按一下 **[Manage Jenkins]**。
2.  在 **[Manage Jenkins]** 頁面中，按一下 **[Manage Plugins]**。
3.  按一下 **[Available]** 索引標籤。
4.  在 **[Artifact Uploaders]** 區段中，核取 **[Azure Storage plugin]**。
5.  按一下 **[Install without restart]** 或 **[Download now and install after restart]**。
6.  重新啟動 Jenkins。

如何設定如何設定 Azure 儲存體外掛程式來使用您的儲存體帳戶
---------------------------------------------------------

1.  在 Jenkins 儀表板中，按一下 **[Manage Jenkins]**。
2.  在 **[Manage Jenkins]** 頁面中，按一下 **[Configure System]**。
3.  在 **[Azure Storage Account Configuration]** 區段中：
    1.  輸入您的儲存體帳戶名稱，這項資訊可從 Azure 入口網站 <https://manage.windowsazure.com> 取得。
    2.  輸入您的儲存體帳戶金鑰，這項資訊也可以從 Azure 入口網站取得。
    3.  如果您使用的是公用 Azure 雲端，請在 **[Blob Service Endpoint URL]** 使用預設值。如果您使用的是不同的 Azure 雲端，請使用 Azure 管理入口網站中為您儲存體帳戶指定的端點。
    4.  按一下 **[Validate Storage Credentials]** 驗證您的儲存體帳戶。
    5.  [選用] 如果您有其他儲存體帳戶要提供給 Jenkins CI 使用，請按一下 **[Add more Storage Accounts]**。
    6.  按一下 **[Save]** 儲存您的設定。

如何建立建置後動作如何建立會將您的組建成品上傳至您儲存體帳戶的建置後動作
------------------------------------------------------------------------

為了方便說明，首先，我們需要建立一個會建立數個檔案的工作，然後新增一個會將這些檔案上傳至您儲存體帳戶的建置後動作。

1.  在 Jenkins 儀表板中，按一下 **[New Job]**。
2.  將工作命名為 **MyJob**，按一下 **[Build a free-style software project]**，然後按 **[OK]**。
3.  在工作組態的 **[Build]** 區段中，按一下 **[Add build step]** 並選擇 **[Execute Windows batch command]**。
4.  在 **[Command]** 中，使用下列命令：

        md text
        cd text
        echo Hello Azure Storage from Jenkins > hello.txt
        date /t > date.txt
        time /t >> date.txt

5.  在工作組態的 **[Post-build Actions]** 區段中，按一下 **[Add post-build action]** 並選擇 **[Upload artifacts to Azure Blob storage]**。
6.  在 **[Storage Account Name]** 中，選取要使用的儲存體帳戶。
7.  在 **[Container Name]** 中，指定容器名稱。(如果上傳組建成品時該容器尚未存在，將會建立該容器。) 您可以使用環境變數，就這個範例而言，請輸入 **${JOB\_NAME}** 作為容器名稱。

    **秘訣**

    在您為 **[Execute Windows batch command]** 輸入指令碼的 **[Command]** 區段底下有一個 Jenkins 所辨識環境變數的連結。按一下該連結即可了解各環境變數名稱和描述。請注意，含有特殊字元的環境變數 (例如 **BUILD\_URL** 環境變數) 不能當做容器名稱或共同虛擬路徑。

8.  就這個範例而言，按一下 **[Make container public]**。(如果您想要使用私用容器，則需要建立共用存取簽章來允許存取。這已超出本主題的範圍。若要深入了解共用存取簽章，請參閱[建立共用存取簽章](http://go.microsoft.com/fwlink/?LinkId=279889)。)
9.  在 **[List of Artifacts to upload]**，輸入 **text/\*.txt**。
10. 在 **[Common virtual path for uploaded artifacts]**，輸入 **${BUILD\_ID}/${BUILD\_NUMBER}**。
11. 按一下 **[Save]** 儲存您的設定。
12. 在 Jenkins 儀表板中，按一下 **[Build Now]** 執行 **MyJob**。檢查主控台輸出中的狀態。當建置後動作開始上傳組建成品時，主控台輸出中將會包含 Azure 儲存體的狀態訊息。
13. 順利完成作業時，您就可以開啟公用 Blob 來檢查組建成品。
    1.  登入 Azure 管理入口網站 <https://manage.windowsazure.com>。
    2.  按一下 **[儲存體]**。
    3.  按一下用於 Jenkins 的儲存體帳戶名稱。
    4.  按一下 **[容器]**。
    5.  按一下名為 **myjob** 的容器，這是您建立 Jenkins 工作時所指派之工作名稱的小寫版本。在 Azure 儲存體中，容器名稱和 Blob 名稱皆為小寫 (並且區分大小寫)。在名為 **myjob** 之容器的 Blob 清單中，您應該會看到 **hello.txt** 和 **date.txt**。請複製這些項目中任何一項的 URL，然後在瀏覽器中開啟它。您會看到文字檔已上傳作為組建成品。

Blob 服務元件Blob 服務所使用的元件
----------------------------------

以下提供 Blob 服務元件的概觀。

-   **儲存體帳戶**：所有對 Azure 儲存體的存取都是透過儲存體帳戶進行。這是存取 blob 用的最高等級的命名空間。帳戶可以包含不限數目的容器，只要它們的大小總計低於 100TB 即可。
-   **容器**：容器提供一組 Blob 的群組。所有 Blob 都必須放在容器中。一個帳戶可以包含的容器不限數量。容器可以儲存無限制的 Blob。
-   **Blob**：一個可屬於任何類型和大小的檔案。Azure 儲存中可以儲存兩種 Blob：區塊和分頁 Blob。大部分檔案都是區塊 Blob。單一區塊 Blob 的大小上限為 200GB。本教學課程使用區塊 Blob。分頁 Blob (另一種 Blob 類型) 的大小上限為 1TB，當檔案中的位元組範圍經常修改時，分頁 Blob 的效率較高。如需關於 Blob 的詳細資訊，請參閱[了解區塊 Blob 和分頁 Blob](http://msdn.microsoft.com/zh-tw/library/windowsazure/ee691964.aspx)。
-   **URL 格式**：可利用下列 URL 格式來定址 Blob：

    `http://storageaccount.blob.core.windows.net/container_name/blob_name`

    (上述格式適用於公用 Azure 雲端。如果您使用不同的 Azure 雲端，請使用 Azure 管理入口網站中的端點來判斷您的 URL 端點。)

    在上述格式中，`storageaccount` 代表您儲存體帳戶的名稱，`container_name` 代表您容器的名稱，而 `blob_name` 則代表您 Blob 的名稱。容器名稱中可以有多個路徑，這些路徑彼此以正斜線 **/** 分隔。本教學課程中的範例容器名稱為 **MyJob**，使用的共同虛擬路徑則是 **${BUILD\_ID}/${BUILD\_NUMBER}**，產生的 Blob URL 格式如下：

    `http://example.blob.core.windows.net/myjob/2013-01-28_15-00-35/2/hello.txt`


