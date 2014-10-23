<properties linkid="manage-services-how-to-configure-a-sqldb" urlDisplayName="How to configure" pageTitle="How to configure a SQL Database - Azure" metaKeywords="Azure creating SQL Server, Azure configuring SQL Server" description="Learn how to create and configure a logical server using SQL Server in Azure." metaCanonical="" services="sql-database" documentationCenter="" title="How to Create and Configure SQL Database" authors="Lori Clark," solutions="" manager="" editor="" />

<tags ms.service="sql-database" ms.workload="data-management" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="Lori Clark,"></tags>

# <span id="configLogical"></span></a>如何建立及設定 SQL Database

在本主題中，我們將引導您逐步完成邏輯伺服器的建立和設定。在新的 Azure (Preview) 管理入口網站中，經過修訂的工作流程可讓您先建立資料庫，然後再建立伺服器。

不過在本主題中，您將會先建立伺服器。如果您想要上傳現有的 SQL Server 資料庫，可能會偏好這個方法。

## 目錄

-   [作法：建立邏輯伺服器][]
-   [作法：設定邏輯伺服器的防火牆][]

## <span id="createLogical"></span></a>作法：建立邏輯伺服器

1.  登入[管理入口網站][]。

2.  按一下 [SQL Database]，再按一下 SQL Database 首頁的 [伺服器]。

3.  按一下頁面底部的 [新增]。

4.  在 [伺服器設定] 中，以一個字且不含空格的方式輸入系統管理員名稱。

    SQL Database 會針對加密連線使用 SQL 驗證。系統將使用您提供的名稱，建立指派給系統管理員 (sysadmin) 固定伺服器角色的新 SQL Server 驗證登入。

    登入不可以是電子郵件地址、Windows 使用者帳戶，也不得為 Windows Live ID。SQL Database 不支援 Claims 或 Windows 驗證。

5.  提供八個字元以上，使用大小寫值和數字或符號組合的強式密碼。

6.  選擇區域。區域可決定伺服器的地理位置。您無法輕易地切換區域，所以請選擇一個適合此伺服器的區域。選擇一個最靠近您的位置。將 Azure 應用程式和資料庫放在相同區域，可節省對外頻寬的成本並縮短資料延遲。

7.  請務必將 [Allow Services] 選項保持選取狀態，以便使用 SQL Database 適用的管理入口網站、儲存體服務和 Azure 上的其他服務連接此資料庫。

8.  完成時，請按一下頁面底部的核取記號。

請注意，您未指定伺服器名稱。SQL Database 會自動產生伺服器名稱，以確保沒有重複的 DNS 項目。伺服器名稱是十個字元的英數字元字串。您無法變更 SQL Database 伺服器的名稱。

在下一個步驟中，您將設定防火牆，以允許在網路上執行之應用程式的連線存取。

## <span id="configFWLogical"></span></a>作法：設定邏輯伺服器的防火牆

1.  在[管理入口網站][]中，依序按一下 [SQL Databases] 和 [伺服器]，然後按一下您剛剛建立的伺服器。

2.  按一下 [設定]。

3.  複製目前的用戶端 IP 位址。如果您從網路連接，這是路由器或 Proxy 伺服器正在接聽的 IP 位址。SQL Database 會偵測目前連線所使用的 IP 位址，因此您可以建立防火牆規則以接受來自此裝置的連線要求。

4.  將此 IP 位址貼入起始和結束範圍。稍後，如果您遇到連線錯誤，指出範圍太過狹窄，您可以編輯此規則將範圍擴大。

    如果用戶端電腦使用動態指派的 IP 位址，您指定的範圍必須足以容納指派給網路中電腦的 IP 位址。一開始請使用較狹窄的範圍，待需要時再將範圍延伸。

5.  為此防火牆規則輸入名稱，例如您的電腦或公司名稱。

6.  按一下核取記號以儲存規則。

7.  按一下頁面底部的 [儲存] 以完成此步驟。如果您沒有看到 [儲存]，請重新整理瀏覽器頁面。

您現在擁有邏輯伺服器、允許來自 IP 位址之輸入連線的防火牆規則及系統管理員登入資訊。在下一個步驟中，您將會切換本機電腦以完成剩餘的設定步驟。

**注意：**您剛剛建立的邏輯伺服器是暫時性的，它會動態地託管於資料中心內的實體伺服器上。在刪除伺服器之前，您應該知道這是無法復原的動作。請務必備份後續上傳至伺服器的所有資料庫。

  [作法：建立邏輯伺服器]: #createLogical
  [作法：設定邏輯伺服器的防火牆]: #configFWLogical
  [管理入口網站]: http://manage.windowsazure.com
