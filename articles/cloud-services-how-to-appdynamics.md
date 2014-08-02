<properties linkid="manage-services-how-to-use-appdynamics" urlDisplayName="Monitor with AppDynamics" pageTitle="How to use AppDynamics with Azure" metaKeywords="" description="Learn how to use AppDynamics for Azure." metaCanonical="" services="cloud-services" documentationCenter="" title="How To Use AppDynamics for Azure" authors="ryanwi" solutions="" manager="" editor="" />

如何使用 AppDynamics for Azure
==============================

本主題說明如何開始使用 AppDynamics for Azure。

目錄
----

-   [什麼是 AppDynamics？](#what)
-   [必要條件](#prereq)
-   [註冊 AppDynamics 帳戶](#register)
-   [從 AppDynamics 下載 .NET 代理程式](#download)
-   [將 .NET 代理程式加入至 Azure 角色並修改啟動](#addagent)
-   [將 AppDynamics 檢測的應用程式發行至 Azure](#publish)
-   [監視應用程式](#monitor)

什麼是 AppDynamics？
--------------------

AppDynamics 是一套應用程式效能監視解決方案，可協助您：

-   識別生產環境中的問題，例如使用者要求緩慢和停頓，以及錯誤。

-   排解和查明這些問題的根本原因

AppDynamics 中有兩個元件：

-   應用程式伺服器代理程式：應用程式伺服器 .NET 代理程式會收集來自您伺服器的資料。在您想要監視的每一個角色執行個體上，您可以執行個別的代理程式。您可以從 AppDynamics 入口網站下載代理程式。

-   AppDynamics 控制器：代理程式會將資訊傳送至 Azure 上的 AppDynamics 控制器代管服務。您可以使用網頁瀏覽器主控台來登入控制器，以監視、分析和疑難排解您的應用程式。

    ![AppDynamics Diagram](./media/cloud-services-how-to-appdynamics/addiagram.png)

必要條件
--------

-   Visual Studio 2010 或更新版本
-   要監視的 Visual Studio 方案
-   Azure SDK
-   Azure 帳戶

註冊 AppDynamics 帳戶
---------------------

註冊 AppDynamics for Azure 帳戶：

1.  在 Azure 市集按一下 AppDynamics 的 **[免費試用]** 或 **[註冊]** (<https://datamarket.azure.com/browse/Applications>)。

    如果選擇 **[註冊]**，您會收到免費版本的 AppDynamics Pro for Azure，具備完整的功能，但在 30 天後會降級到免費版本的 AppDynamics Lite for Azure，功能就有所限制。選擇此選項不需要提供信用卡。您可以隨時升級到 AppDynamics Pro for Azure。

    如果選擇 **[免費試用]**，您會收到免費版本的 AppDynamics Pro for Azure，具備完整的功能。選擇此選項需要提供信用卡。30 天之後，除非取消訂閱，否則將開始以您的信用卡收費，以持續使用 AppDynamics Pro for Azure。

    您想要監視的每一個角色執行個體都需要有一個代理程式授權。例如，執行 2 個 Web 角色執行個體和 2 個背景工作角色執行個體的網站，就需要 4 個代理程式授權。

2.  在註冊頁面上，提供您的使用者資訊、密碼、電子郵件地址、公司名稱，以及您監視的應用程式名稱，您會將它發行至 Azure。

3.  按一下 **[立即註冊]**。

    根據您在註冊頁面提供的地址而寄給您的電子郵件中，您會收到指派給您的帳戶的 AppDynamics 認證和 AppDynamics 控制器 URL (主機和連接埠)。請儲存此資訊。

    如果您已有其他產品的 AppDynamics 認證，則可直接用來登入。

    您也會獲得一個 AppDynamics 帳戶首頁。

    您將登入至 AppDynamics 帳戶首頁。

    AppDynamics 帳戶首頁包含：

    -   控制器 URL：供您在 AppDynamics 控制器代管服務上登入帳戶

    -   AppDynamics 認證：帳戶名稱和存取金鑰

    -   AppDynamics 下載網站的連結：供您下載 AppDynamics .NET 代理程式

    -   Pro 試用版的剩餘天數

    -   AppDynamics 線上影片和文件的連結

    您隨時都可以在網頁瀏覽器中輸入 URL，並以您的 AppDynamics 認證登入，以進入 AppDynamics 帳戶首頁。

從 AppDynamics 下載 .NET 代理程式
---------------------------------

1.  瀏覽至 AppDynamics 下載網站。URL 在歡迎電子郵件中，也在 AppDynamics 帳戶首頁上。

2.  使用 AppDynamics 帳戶名稱和存取金鑰登入。

3.  下載名為 AppDynamicsdotNetAgentSetup64.msi 的檔案。請不要執行檔案。

將 .NET 代理程式加入至 Azure 角色並修改啟動
-------------------------------------------

此步驟會在 Visual Studio 方案中檢測由 AppDynamics 監視的角色。使用 AppDynamics for Azure 並不需要執行傳統的 Windows 精靈式安裝程序。

1.  在 Visual Studio 中建立新的 Azure 專案，或開啟現有的 Azure 專案。

2.  如果已建立新的專案，請將 Web 角色和/或背景工作角色專案加入至方案中。

3.  在您要監視的每一個 Web 角色和背景工作角色專案中，加入已下載的 .NET 代理程式 .msi 檔案。

    請注意，每一個*角色專案*都附加單一的 .NET 代理程式 .msi，而專案的每一個*角色執行個體*都需要個別的 .NET 代理程式授權。

4.  在您要監視的每一個 Web 角色和背景工作角色專案中，加入名為 startup.cmd 的文字檔，並將以下幾行貼上：

         if defined COR_PROFILER GOTO END 
        SETLOCAL EnableExtensions 
        REM Run the agent installer 
        AppDynamicsdotNetAgentSetup64.msi AD_Agent_Environment=Azure AD_Agent_ControllerHost=%1 AD_Agent_ControllerPort=%2 AD_Agent_AccountName=%3 AD_Agent_AccessKey=%4 AD_Agent_ControllerApplication=%5 /quiet /log d:\adInstall.log  
        SHUTDOWN /r /c "Rebooting the instance after the installation of AppDynamics Monitoring Agent" /t 0 
        GOTO END   
        :END

5.  在您要監視的每一個 Web 角色和背景工作角色中，設定 AppDynamics 代理程式 .msi 檔案和 startup.cmd file 檔案的 **[複製到輸出目錄]** 屬性設為 **[永遠複製]**。

    ![Copy Always](./media/cloud-services-how-to-appdynamics/adcopyalways.png)

6.  在 Azure 專案的 ServiceDefinition.csdef 檔案中，加入 Startup Task 元素，並指定每一個 WorkerRole 和 WebRole 元素的參數來叫用 startup.cmd。

    加入下列幾行：

         <Startup>
        <Task commandLine="startup.cmd [your_controller_host] [your_controller_port] [your_account_name] [your_access_key] [your_application_name]" executionContext="elevated" taskType="simple"/>
        </Startup>

    其中：

    -   *your controller host* 和 *your controller port* 是指派給您帳戶的控制器主機和連接埠，*your account name* 和 *your access key* 是 AppDynamics 指派給您的認證。此資訊在您向 AppDynamics 註冊時所寄出的電子郵件中，也在您的 AppDynamic 首頁上。請參閱[註冊 AppDynamics 帳戶](#register)。

    -   *your application name* 是您選擇的應用程式名稱。此名稱在 AppDynamics 控制器介面中用來識別應用程式。

    ServiceDefinition.csdef 檔案的內容如下：

    ![Service Definition](./media/cloud-services-how-to-appdynamics/adscreen.png)

將 AppDynamics 檢測的應用程式發行至 Azure
-----------------------------------------

對於 AppDynamics 檢測的每一個角色專案：

1.  在 Visual Studio 中，選取角色專案。

2.  選取 [發佈到 Azure]。

監視應用程式
------------

1.  使用歡迎電子郵件中及 AppDynamics 帳戶首頁上提供的 URL，登入 AppDynamics 控制器。

2.  傳送一些要求給您的應用程式以產生一些可監視的流量，並等待幾分鐘。

3.  在 AppDynamics 控制器中，選取您的應用程式。

4.  監視應用程式。

詳細資訊
--------

請參閱 AppDynamics 帳戶首頁上的文件和影片連結。

本文的 Wiki 版本中有最新內容，網址是 <http://docs.appdynamics.com/display/ADAZ/How+To+Use+AppDynamics+for+Windows+Azure>。

