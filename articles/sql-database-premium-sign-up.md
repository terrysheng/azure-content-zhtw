<properties linkid="manage-services-sql-databases-premium" urlDisplayName="Premium SQL Database" pageTitle="Sign up for Azure Premium for SQL Database" metaKeywords="" description="Describes how to sign up for the Premium for SQL Database preview, request your Premium database quota, and then upgrade a database to Premium in Azure SQL Database." metaCanonical="" services="cloud-services" documentationCenter="" title="Sign up for the preview of Premium for Azure SQL Database" authors="karaman" solutions="" manager="" editor="tysonn" />

註冊 Premium for Azure SQL Database 預覽版
==========================================

在本教學課程中，您將了解參與 Premium for SQL Database 預覽版試用的必要步驟。

Azure SQL Database 發行了新服務 Premium for SQL Database 的限額預覽版。Premium 資料庫提供保留資源，以利雲端應用程式維持可預測的效能。

[本主題中說明的功能僅適用於預覽版。本主題為發行前文件，其內容將在未來的版本中變更。]

目錄
----

-   [步驟 1：註冊 Premium for SQL Database 預覽版](#SignUp)
-   [步驟 2：申請 Premium 資料庫配額](#Quota)
-   [步驟 3：建立 Premium 資料庫](#Upgrade)

步驟 1：註冊 Premium for SQL Database 預覽版
--------------------------------------------

運用這項功能的第一個步驟，是註冊 Premium for SQL Database 預覽版的訂閱。

1.  使用您的 Microsoft 帳戶登入 [Azure 預覽功能頁面](http://account.windowsazure.com/PreviewFeatures)。

    **注意** - 只有 Azure 帳戶管理員可存取帳戶入口網站。如果您不是訂閱的帳戶管理員，請要求管理員為您完成訂閱的註冊程序。如需 Azure 帳戶和訂閱的詳細資訊，請參閱[購買選項](http://account.windowsazure.com/PreviewFeatures)。

    ![Image1](./media/sql-database-premium-sign-up/AccountSignup-Figure1.png)

2.  在預覽功能清單中找出 **Premium for SQL Database** 項目，然後按一下與該項目相關聯的 **[立即試用]** 按鈕。

    ![Image2](./media/sql-database-premium-sign-up/AccountSignupButton-Figure2.png)

3.  選擇您要為預覽註冊的訂閱。

    ![Image3](./media/sql-database-premium-sign-up/Subscription-Figure3.png)

    只有已付費的有效 Azure 訂閱，才符合預覽的條件。您可以註冊多個訂閱，但每個訂閱只能註冊一次。

    為預覽註冊訂閱並不會產生額外費用，但一旦啟用並授與 Premium 配額後，Premium 資料庫的建立或升級及必須依據 [SQL Database 定價頁面](http://www.windowsazure.com/en-us/pricing/details/sql-database/)中列載的訂價收費。

    註冊要求的現行狀態會反映在預覽功能清單中。

    ![Image4](./media/sql-database-premium-sign-up/Status-Figure4.png)

4.  我們會根據目前的容量和需求審核申請。您的訂閱可能需要 2 天才能啟用，若所需時間更久，表示申請量較大，或公用預覽容量已滿。

5.  您的預覽訂閱啟用後，您將會收到電子郵件通知。

步驟 2：申請 Premium 資料庫配額
-------------------------------

您的預覽訂閱啟用後，您必須為預定要建立 Premium 資料庫的每部伺服器申請 Premium 資料庫配額。由於容量有限，請為您預定要建立 Premium 資料庫的伺服器申請配額即可，並取消任何非必要的擱置申請。

1.  使用您的 Microsoft 帳戶，登入 [Azure 管理入口網站](https://manage.windowsazure.com)。

    **注意** - 帳戶管理員、服務管理員以及訂閱的共同管理員可在註冊預覽的訂閱後申請配額。

2.  導覽至 **[SQL Databases]** 延伸中的 **[伺服器]** 清單。
3.  選取您要為其申請 Premium 資料庫配額的伺服器。
4.  按一下頂層導覽列中的閃電圖示，以導覽至所選伺服器的 **[快速啟動]**。
5.  按一下 **[Premium 資料庫]** 區段中Request Premium Database Quota的 **[Request Premium Database Quota]**。

    ![Image6](./media/sql-database-premium-sign-up/RequestQuota-Figure6.png)

    在提交申請後，您最多可能要等待 5 天才能獲得配額。若等待時間更長，表示申請量大，或預覽容量已滿。

    申請 Premium 資料庫配額時還須注意若干事項：

    -   配額並不提供給使用免費試用訂閱的客戶。
    -   配額最初會有限制，我們會根據目前的需求和可用容量來審核申請。
    -   Microsoft 可在配額未使用達 15 天後收回配額。
    -   對於訂閱中的每個邏輯伺服器，只能提交一個配額申請。
    -   最初，配額會限定為每個邏輯伺服器一個資料庫。
    -   資料庫配額申請是免費的，但是，建立 Premium 版資料庫或將現有的 Web 或 Business 版資料庫升級至 Premium，將會增加資料庫的成本。

6.  您可以在伺服器的 **[快速啟動]** 頁面上檢視配額申請的狀態。

    ![Image7](./media/sql-database-premium-sign-up/PendingApproval-Figure7.png)

7.  在您的 Premium 資料庫配額獲准且配額可供使用後，您將會收到電子郵件通知。
8.  獲准之後，您可以在伺服器的 **[快速啟動]** 或 **[儀表板]** 索引標籤上，檢視伺服器剩餘的 Premium 資料庫配額。

    ![Image8](./media/sql-database-premium-sign-up/QuotaApproved-Figure8.png)

步驟 3：建立 Premium 資料庫
---------------------------

在您獲得配額後，您可以建立新的 Premium 版資料庫，或將現有的 Web 或 Business 版資料庫升級至 Premium，以使用保留容量和更可預期的效能。

![Image9](./media/sql-database-premium-sign-up/SpecifyDBSettings-Figure9.png)

![Image10](./media/sql-database-premium-sign-up/PremiumDBSettings-Figure10.png)

如需詳細資訊，請參閱[管理 Premium 資料庫](http://go.microsoft.com/fwlink/p/?LinkID=311927)。

**注意：**在每 24 小時的期間內，您只能變更一次資料庫的 Premium 狀態或保留大小。

後續步驟
--------

如需關於 Premium 資料庫的其他資訊，請參閱：

-   [管理 Premium 資料庫](http://go.microsoft.com/fwlink/p/?LinkID=311927)
-   [Premium for SQL Database 指引](http://go.microsoft.com/fwlink/p/?LinkId=313650)

