<properties pageTitle="How to use Code First Migrations .NET backend (Mobile Services)" metaKeywords="" description="" metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Considerations for supporting multiple clients from a single mobile service" authors="glenga" solutions="mobile" writer="glenga" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# 如何對 .NET 後端行動服務進行資料模型變更

本主題說明如何使用 Entity Framework Code First 移轉對現有的 Azure SQL Database 進行資料模型變更，以避免遺失現有的資料。此程序假設您已將行動服務專案發佈至 Azure、您的資料庫中目前已有資料，且遠端與本機資料模型仍保持同步。本主題同時說明 Azure 行動服務實作的預設 Code First 初始設定式，開發期間將會用到這些初始設定式。當您不必保有現有的資料時，這些初始設定式可讓您輕鬆地進行結構描述變更，而不需要使用 Code First 移轉。

## 資料模型初始設定式

行動服務在 .NET 後端行動服務專案中，支援兩種資料模型初始設定式基底類別。這些初始設定式會在 Entity Framework 於您的 [DbContext][DbContext] 中偵測到資料模型變更時，捨棄並重新建立資料庫中的資料表。這些初始設定式設計成不論行動服務在本機電腦上執行，或裝載於 Azure，都能使用。這兩個初始設定式基底類別會從資料庫中，偵測行動服務所用結構描述中的所有資料表、檢視、函式和程序。

-   **ClearDatabaseSchemaIfModelChanges**
     只有在 Code First 偵測到資料模型變更時，才會刪除結構描述物件。從 [Azure 管理入口網站][Azure 管理入口網站]下載之 .NET 後端專案中的預設初始設定式便是繼承自這個基底類別。

-   **ClearDatabaseSchemaAlways**：
     每次存取資料模型都會刪除結構描述物件。這個基底類別可用來重設資料庫，而不需要進行資料模型變更。

在下載的快速入門專案中，Code First 初始設定式會在 WebApiConfig.cs 檔中定義。覆寫 **Seed** 方法可將開頭幾列資料加入新的資料表。如需植入資料的範例，請參閱[在移轉中植入資料][在移轉中植入資料]。在本機電腦上執行時，可使用其他 Code First 資料模型初始設定式。但是，由於使用者沒有可捨棄資料庫的權限，因此嘗試捨棄資料庫的初始設定式在 Azure 中會失敗，這是件好事。

您在行動服務的本機開發期間可繼續使用初始設定式，因此 .NET 後端教學課程假設您使用初始設定式。但如果您要進行資料模型變更，並保有資料庫中現有的資料，則必須使用 Code First 移轉。

> [WACOM.NOTE]對即時 Azure 服務開發及測試您的行動服務專案時，您應一律使用測試專用的行動服務執行個體。切勿對目前實際執行或由用戶端應用程式使用中的行動服務進行開發或測試。

## <a name="migrations"></a>啟用 Code First 移轉

「Code First 移轉」會使用快照方法，產生在執行時會對資料庫進行結構描述變更的程式碼。透過「移轉」，您將可對資料模型進行增量變更，並保有資料庫中現有的資料。

> [WACOM.NOTE]如果您已將 .NET 後端行動服務專案發佈至 Azure，而您的 SQL Database 資料表結構描述不符合專案目前的資料模型，則您必須使用初始設定式、手動捨棄資料表，或將結構描述和資料模型保持同步，再嘗試使用 Code First 移轉進行發佈。

下列步驟會開啟「移轉」，並且在專案、本機資料庫和 Azure 中套用資料模型變更。

1.  在 Visual Studio 的 [方案總管] 中，以滑鼠右鍵按一下行動服務專案，然後按一下 [設定為啟始專案]。

2.  在 [工具] 功能表中展開 [NuGet 封裝管理員]，然後按一下 [Package Manager Console]。

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

    此時，資料庫會與資料模型同步。如果您提供種子資料，您可以依序按一下 [Try it out]、[GET tables/todoitem]、[Try this out] 和 [傳送]，對資料進行驗證。如需詳細資訊，請參閱[在移轉中植入資料][在移轉中植入資料]。

8.  現在，請對資料模型進行變更 (例如，將新的 UserId 屬性新增至 TodoItem 類型、重新建置專案)，然後在封裝管理員中執行下列命令：

        PM> Add-Migration NewUserId

    這會建立名為 *NewUserId* 的新移轉。</cf><cf font="MS Gothic" complexscriptsfont="MS Gothic" asiantextfont="MS Gothic" fontcolor="000000">實作此變更的新程式碼檔案會新增至</cf><cf complexscriptsfont="Times New Roman" fontcolor="000000"> [</cf><cf font="MS Gothic" complexscriptsfont="MS Gothic" asiantextfont="MS Gothic" fontcolor="000000">移轉</cf><cf complexscriptsfont="Times New Roman" fontcolor="000000">] </cf><cf font="MS Gothic" complexscriptsfont="MS Gothic" asiantextfont="MS Gothic" fontcolor="000000">資料夾</cf><cf complexscriptsfont="Times New Roman" fontcolor="000000">

9.  再按一次 F5，在本機電腦上重新啟動行動服務專案。

    移轉會套用至資料庫，且資料庫會再次與資料模型同步。如果您提供種子資料，您可以依序按一下 [Try it out]、[GET tables/todoitem]、[Try this out] 和 [傳送]，對資料進行驗證。如需詳細資訊，請參閱[在移轉中植入資料][在移轉中植入資料]。

10. 將行動服務重新發佈至 Azure，然後執行用戶端應用程式以存取資料，並驗證資料確實載入且未發生錯誤。

11. (選用) 在 [Azure 管理入口網站][Azure 管理入口網站] 中選取您的行動服務，按一下 [設定] 索引標籤，然後按一下 [SQL Database] 連結。

    ![][0]

    這會導覽至您行動服務資料庫的 SQL Database 頁面。

12. (選用) 按一下 [管理]、登入您的 SQL Database 伺服器，然後按一下 [設計]，並驗證結構描述已在 Azure 中進行變更。

    ![][1]

## <a name="seeding"></a>在移轉中植入資料

您可以讓「移轉」在移轉執行時新增種子資料至資料庫。**組態**類別具有可覆寫的 **Seed** 方法，可用來插入或更新資料。在啟用「移轉」時，Configuration.cs 程式碼檔案會新增至 Migrations 資料夾。下列範例示範如何覆寫 [Seed][Seed] 方法，以將資料植入 **TodoItems** 資料表中。移轉至最新版本後，會呼叫 [Seed][Seed] 方法。

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

這個程式碼會呼叫 [AddOrUpdate][AddOrUpdate] 協助程式擴充方法，以將種子資料加入新的 UserId 資料行。使用 [AddOrUpdate][AddOrUpdate]，即不會建立重複的資料列。

<!-- Anchors --> 
<!-- Images --> 
<!-- URLs -->

  [DbContext]: http://msdn.microsoft.com/zh-tw/library/system.data.entity.dbcontext(v=vs.113).aspx
  [Azure 管理入口網站]: https://manage.windowsazure.com/
  [在移轉中植入資料]: #seeding
  [0]: ./media/mobile-services-dotnet-backend-how-to-use-code-first-migrations/navagate-to-sql-database.png
  [1]: ./media/mobile-services-dotnet-backend-how-to-use-code-first-migrations/manage-sql-database.png
  [Seed]: http://msdn.microsoft.com/zh-tw/library/hh829453(v=vs.113).aspx
  [AddOrUpdate]: http://msdn.microsoft.com/zh-tw/library/system.data.entity.migrations.idbsetextensions.addorupdate(v=vs.103).aspx
