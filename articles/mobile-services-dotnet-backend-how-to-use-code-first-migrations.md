<properties pageTitle="How to use Code First Migrations .NET backend (Mobile Services)" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="Considerations for supporting multiple clients from a single mobile service" authors="glenga" solutions="" writer="glenga" manager="dwrede" editor="" />

如何對 .NET 後端行動服務進行資料模型變更
========================================

在 .NET 後端行動服務專案中，預設的 Entity Framework Code First 資料庫初始設定式衍生自 [DropCreateDatabaseIfModelChanges] 類別。此初始設定式會在偵測到您的 [DbContext] 所公開的資料模型變更時，指示 Entity Framework 捨棄並重新建立資料庫。您在行動服務專案的本機開發期間應繼續使用此初始設定式，因此 .NET 後端教學課程假設您使用此初始設定式。但如果您要進行資料模型變更，並保有資料庫中現有的資料，則必須使用 Code First 移轉。此外，將資料模型變更發佈至 Azure 時也很適合使用 Code First 移轉，因為 SQL Database 是不可捨棄的。

本主題將說明如何使用 Code First 移轉對現有的 SQL Database 進行資料模型變更，同時保有現有資料。此程序假設您已將行動服務專案發佈至 Azure、您的資料庫中目前已有資料，且遠端與本機資料模型仍保持同步。

> [WACOM.NOTE]建議您先在本機電腦上盡可能完成資料模型開發，然後再發佈至 Azure。如果您已將 .NET 後端行動服務專案發佈至 Azure，而您的 SQL Database 資料表結構描述不符合專案目前的資料模型，則您必須先捨棄資料表，或以手動方式加以同步，再嘗試使用 Code First 移轉進行發佈。

在本機電腦上開發 .NET 後端行動服務專案時，最簡單的資料模型變更處理方式，是繼續使用預設初始設定式，因為這樣即可在偵測到資料模型變更時捨棄並重新建立資料庫。將專案重新發佈至 Azure 時，並不適用此方法。此初始設定式將會失敗，因為執行階段沒有權限可捨棄 Azure 中的 SQL Database；這是妥善的設定。

> [WACOM.NOTE]對即時 Azure 服務開發及測試您的行動服務專案時，您應一律使用測試專用的行動服務執行個體。切勿對目前實際執行或由用戶端應用程式使用中的行動服務進行開發或測試。

捨棄 SQL Database 中的資料表
----------------------------

您必須先在行動服務所使用的資料庫結構描述中手動捨棄任何現有的資料表，才能使用 Azure 對 SQL Database 執行「移轉」。請使用下列步驟，從您的 SQL Database 中捨棄現有資料表。如果您的資料庫結構描述已與目前的資料模型同步，您可以略過此步驟，直接開始執行[移轉](#migrations)。

1.  登入 [Azure 管理入口網站]、選取您的行動服務、按一下 **[設定]** 索引標籤，然後按一下 **[SQL Database]** 連結。

    ![](./media/mobile-services-dotnet-backend-how-to-use-code-first-migrations/navagate-to-sql-database.png)

    這會將您導向至行動服務所使用之資料庫的入口網站頁面。

2.  按一下 **[管理]** 按鈕，然後登入 SQL Database 伺服器。

    ![](./media/mobile-services-dotnet-backend-how-to-use-code-first-migrations/manage-sql-database.png)

3.  在 SQL Database 管理員中依序按一下 **[設計]** 和 **[資料表]**、選取行動服務結構描述中的資料表、按一下 **[捨棄資料表]**，然後按一下 **[確定]** 加以確認。

    ![](./media/mobile-services-dotnet-backend-how-to-use-code-first-migrations/sql-database-drop-tables.png)

4.  對行動服務結構描述中的每個資料表重複前述步驟。

    在現有的資料表移除後，「Code First 移轉」即可在 SQL Database 上初始化。不屬於行動服務結構描述的資料表並不會對您的行動服務造成影響，因此不應捨棄。

啟用 Code First 移轉
--------------------

「Code First 移轉」會使用快照方法，產生在執行時會對資料庫進行結構描述變更的程式碼。透過「移轉」，您將可對資料模型進行增量變更，並保有資料庫中現有的資料。下列步驟會開啟「移轉」，並且在專案、本機資料庫和 Azure 中套用資料模型變更。

1.  在 Visual Studio 的 [方案總管] 中，以滑鼠右鍵按一下行動服務專案，然後按一下 **[設定為啟始專案]**。

2.  在 **[工具]** 功能表中展開 **[NuGet 封裝管理員]**，然後按一下 **[Package Manager Console]**。

    這會顯示 [Package Manager Console]，以供您管理 Code First 移轉。

3.  在 [Package Manager Console] 中，輸入下列命令：

         PM> Enable-Migrations

    這會為您的專案開啟「Code First 移轉」。

4.  在主控台中，執行下列命令：

         PM> Add-Migration Initial

    這會建立名為*初始*的新移轉。移轉程式碼會儲存在 Migrations 專案資料夾中。

5.  展開 App\_Start 資料夾，開啟 WebApiConfig.cs 專案檔案，然後新增下列 **using** 陳述式：

         using System.Data.Entity.Migrations;
        using todolistService.Migrations;

    在前述程式碼中，您必須將 *todolistService* 字串取代為您專案的命名空間；就下載的快速入門專案而言，此命名空間為 *mobile\_service\_name*Service。

6.  在相同的程式碼檔案中，將 **Database.SetInitializer** 方法的呼叫註解化，並在其後新增下列程式碼：

         var migrator = new DbMigrator(new Configuration());
        migrator.Update();

    這會停用捨棄並重新建立資料庫的預設 Code First 資料庫初始設定式，並將其取代為要套用最新移轉的明確要求。此時，只要存取資料，任何資料模型變更都將導致 InvalidOperationException，除非您已建立資料模型的移轉。接下來，您的服務必須使用「Code First 移轉」，將資料模型變更移轉至資料庫。

7.  按 F5，在本機電腦上啟動行動服務專案。

    此時，資料庫會與資料模型同步。如果您提供種子資料，您可以依序按一下 **[Try it out]**、**[GET tables/todoitem]**、**[Try this out]** 和 **[傳送]**，對資料進行驗證。如需詳細資訊，請參閱[在移轉中植入資料](#seeding)。

8.  現在，請對資料模型進行變更 (例如，將新的 UserId 屬性新增至 TodoItem 類型、重新建置專案)，然後在封裝管理員中執行下列命令：

    PM\> Add-Migration NewUserId



    這會建立名為 *NewUserId* 的新移轉。實作此變更的新程式碼檔案會新增至 [移轉] 資料夾

1.  再按一次 F5，在本機電腦上重新啟動行動服務專案。

    移轉會套用至資料庫，且資料庫會再次與資料模型同步。如果您提供種子資料，您可以依序按一下 **[Try it out]**、**[GET tables/todoitem]**、**[Try this out]** 和 **[傳送]**，對資料進行驗證。如需詳細資訊，請參閱[在移轉中植入資料](#seeding)。

2.  將行動服務重新發佈至 Azure，然後執行用戶端應用程式以存取資料，並驗證資料確實載入且未發生錯誤。

3.  (選用) 在 [Azure 管理入口網站] 中選取您的行動服務、按一下 **[設定]** 索引標籤，然後按一下 **[SQL Database]** 連結。

    ![](./media/mobile-services-dotnet-backend-how-to-use-code-first-migrations/navagate-to-sql-database.png)

    這會導覽至您行動服務資料庫的 SQL Database 頁面。

4.  (選用) 按一下 **[管理]**、登入您的 SQL Database 伺服器，然後按一下 **[設計]**，並驗證結構描述已在 Azure 中進行變更。

    ![](./media/mobile-services-dotnet-backend-how-to-use-code-first-migrations/manage-sql-database.png)

在移轉中植入資料
----------------

您可以讓「移轉」在移轉執行時新增種子資料至資料庫。「組態」類別具有可覆寫的 Seed 方法，可用來插入或更新資料。在啟用「移轉」時，Configuration.cs 程式碼檔案會新增至 Migrations 資料夾。下列範例說明如何覆寫 [Seed] 方法，以將資料植入 **TodoItems** 資料表中。移轉至最新版本後，會呼叫 [Seed] 方法。

### 植入新資料表

下列程式碼會在 **TodoItems** 資料表中植入新的資料列：

        List<TodoItem> todoItems = new List<TodoItem>
        {
    new TodoItem { Id = "1", Text = "First item", Complete = false },
    new TodoItem { Id = "2", Text = "Second item", Complete = false },
        };

    foreach (TodoItem todoItem in todoItems)
        {
    context.Set<TodoItem>().Add(todoItem);
        }
    base.Seed(context);

### 在資料表中植入新資料行

下列程式碼只會植入 UserId 資料行：

        context.TodoItems.AddOrUpdate(
    t => t.UserId,
    new TodoItem { UserId = 1 },
    new TodoItem { UserId = 1 },
    new TodoItem { UserId = 2 }
            );
    base.Seed(context);

此程式碼會呼叫 [AddOrUpdate] 協助程式延伸方法，以將種子資料新增至新的 UserId 資料行。使用 [AddOrUpdate]，即不會建立重複的資料列。

[DropCreateDatabaseIfModelChanges]:http://msdn.microsoft.com/query/dev12.query?appId=Dev12IDEF1&l=EN-US&k=k("System.Data.Entity.DropCreateDatabaseIfModelChanges\`1");k(TargetFrameworkMoniker-.NETFramework,Version%3Dv4.5);k(DevLang-csharp)&rd=true 
[Seed]:http://msdn.microsoft.com/en-us/library/hh829453(v=vs.113).aspx 
[Azure Management Portal]:https://manage.windowsazure.com/ 
[DbContext]:http://msdn.microsoft.com/en-us/library/system.data.entity.dbcontext(v=vs.113).aspx 
[AddOrUpdate]:http://msdn.microsoft.com/en-us/library/system.data.entity.migrations.idbsetextensions.addorupdate(v=vs.103).aspx

