<properties
	pageTitle="教學課程 - 開始使用適用於 .NET 的 Azure 批次應用程式庫"
	description="了解 Azure 批次應用程式，以及如何使用簡單的案例進行開發的基本概念"
	services="batch"
	documentationCenter=".net"
	authors="yidingzhou"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.devlang="dotnet"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="big-compute"
	ms.date="07/21/2015"
	ms.author="yidingz"/>




# 開始使用適用於 .NET 的 Azure 批次應用程式庫
本教學課程說明您如何在 Azure 批次上，使用批次應用程式服務來執行平行計算工作負載。

批次應用程式是Azure 批次的一項功能，提供以應用程式為中心的方法來管理和執行批次工作負載。使用批次應用程式 SDK 可讓您建立封裝，以批次啟用應用程式，並將這些封裝部署在您自己的帳戶中，或提供給其他批次使用者使用。批次也提供資料管理、作業監督、內建的診斷和記錄，以及工作之間相依性的支援。此外，它還包含一個管理入口網站，可讓您管理作業、 檢視記錄檔和下載輸出，而不需要撰寫自己的用戶端。

在批次應用程式案例中，您可以使用批次應用程式雲端 SDK 來撰寫程式碼，將作業分割成平行工作、描述這些工作之間的任何相依性，以及指定如何執行每個工作。此程式碼會部署到批次帳戶。然後，用戶端只要指定作業類型和 REST API 的輸入檔，就可以執行作業。

>[AZURE.NOTE]若要完成此教學課程，您需要 Azure 帳戶。只需要幾分鐘的時間，您就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/)。您可以使用 NuGet 來取得<a href="http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps.Cloud/">批次應用程式雲端</a>組件和<a href="http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps/">批次應用程式用戶端</a>組件。在 Visual Studio 中建立專案之後，以滑鼠右鍵按一下 [**方案總管**] 中的專案，然後選擇 [**管理 NuGet 封裝**]。您也可以在<a href="https://visualstudiogallery.msdn.microsoft.com/8b294850-a0a5-43b0-acde-57a07f17826a">這裡</a>，或在 Visual Studio 中透過 [擴充功能和更新] 功能表項目搜尋「**批次應用程式**」，下載 Visual Studio Extension for Batch Apps，其中包含專案範本可啟用應用程式的雲端功能及部署應用程式。您也可以在 MSDN 上找到<a href="https://go.microsoft.com/fwLink/?LinkID=512183&clcid=0x409">端對端範例</a>。
>

###Azure 批次應用程式的基本概念
批次主要是用來處理現有的大量計算應用程式。它會利用現有的應用程式碼，並在動態、虛擬化、一般用途的環境中執行它。若要讓應用程式使用批次應用程式，有一些必須完成的事項：

1.	準備您現有應用程式可執行檔 (在傳統伺服器陣列或叢集中執行的相同可執行檔) 的 zip 檔案，以及它需要的任何支援檔案。然後使用管理入口網站或 REST API，將此 zip 檔案上傳到您的批次帳戶。
2.	建立「雲端組件」(將您的工作負載分派至應用程式) 的 zip 檔案，並透過管理入口網站或 REST API 上傳它。雲端組件包含兩個以雲端 SDK 建置的元件：
	1.	作業分割器 – 將工作細分成可以獨立處理的工作。比方說，在動畫案例中，作業分割器可將影片作業細分成個別的畫面。
	2.	工作處理器 – 叫用特定工作的應用程式可執行檔。例如，在動畫案例中，工作處理器會叫用呈現程式，以呈現手邊工作所指定的單一畫面。
3.	提供方法將作業提交至 Azure 中啟用的應用程式。這可能是您的應用程式 UI 或 Web 入口網站中的外掛程式，或甚至是執行管線中的自動服務。請參閱 MSDN 上的範例，以取得相關<a href="https://go.microsoft.com/fwLink/?LinkID=512183&clcid=0x409">範例</a>。



###批次應用程式的重要概念
批次應用程式的程式設計和使用方式模型以下列重要概念為中心：

####作業
**作業**是指由使用者提交的一件工作。當提交作業時，使用者將指定作業的類型、這項作業的任何設定，以及作業所需的資料。啟用的實作可以代替使用者決定這些詳細資料，或者，在某些情況下，使用者可以透過用戶端明確地提供這項資訊。作業會傳回結果。每個作業都有主要輸出，可能還有預覽輸出。如有需要，作業也可以傳回額外的輸出。

####工作
**工作**是指作業中需要完成的一件工作。當使用者提交作業時，它會細分為較小的工作。然後，服務會處理這些個別的工作，再將工作結果組合成完整的作業輸出。工作的本質視作業類型而定。作業分割器會根據應用程式主要處理的工作部分，定義如何將作業細分為工作。也可以下載個別工作輸出，在某些情況下可能很有用，例如使用者想要從動畫作業中下載個別的工作。

####合併工作
**合併工作**是一種特殊的工作，可將個別工作的結果組合成最終作業結果。在影片呈現作業中，合併工作可能將呈現的畫面組合成影片，或將所有呈現的畫面壓縮成單一檔案。每個作業即使實際上不需要「合併」，也都會有合併工作。

####檔案
**檔案**是指輸入到作業中的資料。作業可能沒有相關聯的輸入檔，也可能有一或多個輸入檔。相同的檔案也可用於多個作業中，例如在影片呈現作業中，檔案可能是材質、模型等。在資料分析作業中，檔案可能是一組觀察值或測量值。

###啟用雲端應用程式
您的應用程式必須包含靜態欄位或屬性，其中含有應用程式的所有詳細資料。它指定應用程式的名稱，以及應用程式所處理的作業類型。這是在使用 SDK 中的範本時提供，您可以透過 Visual Studio 組件庫下載範本。

平行工作負載的雲端應用程式宣告的範例如下：

	public static class ApplicationDefinition
	{
	    public static readonly CloudApplication App = new ParallelCloudApplication
	    {
	        ApplicationName = "ApplicationName",
	        JobType = "ApplicationJobType",
	        JobSplitterType = typeof(MyJobSplitter),
	        TaskProcessorType = typeof(MyTaskProcessor),
	    };
	}

####實作作業分割器和工作處理器
對於不易平行的工作負載，您必須實作作業分割器和工作處理器。

####實作 JobSplitter.SplitIntoTasks
SplitIntoTasks 實作必須傳回一連串的工作。每個工作代表一件個別的工作，將加入佇列中由計算節點處理。每一項工作必須獨立，而且必須有工作處理器所需的一切資訊。

作業分割器指定的工作以 TaskSpecifier 物件表示。TaskSpecifier 有一些屬性可讓您在傳回工作之前設定。


-	TaskIndex 會被忽略，但可供工作處理器使用。您可以利用此屬性將索引傳遞給工作處理器。如果您不需要傳遞索引，則不需要設定此屬性。
-	Parameters 預設為空集合。您可以將項目加入它，或取代為新的集合。您可以使用 WithJobParameters 或 WithAllJobParameters 方法，從作業參數集合中複製項目。  


RequiredFiles 預設為空集合。您可以將項目加入它，或取代為新的集合。您可以使用RequiringJobFiles 或 RequiringAllJobFiles 方法，從作業檔案集合中複製項目。

您可以指定相依於另一個特定工作的工作。若要這樣做，請設定 TaskSpecifier.DependsOn 屬性，並傳遞它相依的工作的識別碼：

	new TaskSpecifier {
	    DependsOn = TaskDependency.OnId(5)
	}

工作必須等到相依的工作產生輸出之後才會執行。

您也可以指定整組工作必須等到另一組工作完成之後才開始處理。在此情況下，您可以設定 TaskSpecifier.Stage 屬性。具有給定 Stage 值的工作必須等到 Stage 值更小的所有工作完成之後，才會開始處理。例如，必須等到 Stage 0、 1 或 2 的所有工作完成之後，具有 Stage 3 的工作才會開始處理。Stage 必須從 0 開始，各個 Stage 的順序必須沒有間隔，並且 SplitIntoTasks 必須依 Stage 順序傳回工作：例如，在傳回 Stage 為 1 的工作之後傳回 Stage 為 0 的工作，即為錯誤。

每個平行作業以一項特殊工作來結束，稱為合併工作。合併工作的任務是將平行處理工作的結果組合成最後的結果。批次應用程式會自動為您建立合併工作。

在罕見的情況下，您可能想要明確地控制合併工作，例如自訂其參數。在此情況下，您可以傳回 TaskSpecifier 並將其 IsMerge 屬性設定為 true，以指定合併工作。這會覆寫自動合併工作。如果您要建立明確的合併工作：

-	您可以只建立一個明確的合併工作
-	它必須是序列中的最後一個工作
-	它必須將 IsMerge 設定為 true，而且每個其他工作必須將 IsMerge 設為 false  


但請記住，通常您不需要明確地建立合併工作。

下列程式碼示範 SplitIntoTasks 的簡單實作。

	protected override IEnumerable<TaskSpecifier> SplitIntoTasks(
	    IJob job,
	    JobSplitSettings settings)
	{
	    int start = Int32.Parse(job.Parameters["startIndex"]);
	    int end = Int32.Parse(job.Parameters["endIndex"]);
	    int count = (end - start) + 1;

	    // Processing tasks
	    for (int i = 0; i < count; ++i)
	    {
	        yield return new TaskSpecifier
	        {
	            TaskIndex = start + i,
	        }.RequiringAllJobFiles();
	    }
	}
####實作 ParallelTaskProcessor.RunExternalTaskProcess

從作業分割器傳回的每個非合併工作都呼叫 RunExternalTaskProcess。它應該使用適當的引數來叫用您的應用程式，並傳回需要保留以供稍後使用的輸出集合。

下列片段示範如何呼叫名為 application.exe 的程式。請注意，您可以使用 ExecutablePath helper 方法來建立絕對檔案路徑。

雲端 SDK 中的ExternalProcess 類別提供實用的 helper 邏輯來執行您的應用程式可執行檔。ExternalProcess 可以處理取消作業、將結束代碼轉譯為例外狀況、擷取標準輸出和標準錯誤，以及設定環境變數。想要的話，您也可以直接使用 .NET Process 類別來執行您的程式。

RunExternalTaskProcess 方法會傳回 TaskProcessResult，其中包含輸出檔案的清單。至少必須包含合併所需的所有檔案。您可以選擇性地傳回記錄檔、預覽檔案和中繼檔 (例如工作失敗時進行診斷)。請注意，您的方法會傳回檔案路徑，而不是檔案內容。

每個檔案都必須以它所包含之輸出的類型 (output (也就是最終作業輸出的一部分)、preview、log 或 intermediate) 來識別。這些值來自 TaskOutputFileKind 列舉。下列片段會傳回單一工作輸出，但沒有預覽或記錄檔。TaskProcessResult.FromExternalProcessResult 方法可以簡化從命令列程式中擷取結束代碼、處理器輸出和輸出檔案的常見案例：

下列程式碼示範 ParallelTaskProcessor.RunExternalTaskProcess 的簡單實作。

	protected override TaskProcessResult RunExternalTaskProcess(
	    ITask task,
	    TaskExecutionSettings settings)
	{
	    var inputFile = LocalPath(task.RequiredFiles[0].Name);
	    var outputFile = LocalPath(task.TaskId.ToString() + ".jpg");

	    var exePath = ExecutablePath(@"application\application.exe");
	    var arguments = String.Format("-in:{0} -out:{1}", inputFile, outputFile);

	    var result = new ExternalProcess {
	        CommandPath = exePath,
	        Arguments = arguments,
	        WorkingDirectory = LocalStoragePath,
	        CancellationToken = settings.CancellationToken
	    }.Run();

	    return TaskProcessResult.FromExternalProcessResult(result, outputFile);
	}
####實作 ParallelTaskProcessor.RunExternalMergeProcess

這是在合併工作中呼叫。它應該叫用應用程式來結合先前工作的輸出 (以適合您應用程式的任何方式)，並傳回結合的輸出。

RunExternalMergeProcess 的實作非常類似於 RunExternalTaskProcess，除了：

-	RunExternalMergeProcess 會取用先前工作的輸出，而不是使用者輸入檔。因此，您應該根據工作識別碼來決定您想要處理的檔案名稱，而非從 Task.RequiredFiles 集合來判斷。
-	RunExternalMergeProcess 傳回 JobOutput 檔案，以及選擇性地 JobPreview 檔案。


下列程式碼示範 ParallelTaskProcessor.RunExternalMergeProcess 的簡單實作。

	protected override JobResult RunExternalMergeProcess(
	    ITask mergeTask,
	    TaskExecutionSettings settings)
	{
	    var outputFile =  "output.zip";

	    var exePath =  ExecutablePath(@"application\application.exe");
	    var arguments = String.Format("a -application {0} *.jpg", outputFile);

	    new ExternalProcess {
	        CommandPath = exePath,
	        Arguments = arguments,
	        WorkingDirectory = LocalStoragePath
	    }.Run();

	    return new JobResult {
	        OutputFile = outputFile
	    };
	}

###提交作業給批次應用程式
作業描述要執行的工作負載，而且必須包含工作負載的所有資訊 (檔案內容除外)。例如，作業包含從用戶端流向作業分割器，再流向工作的組態設定。MSDN 上提供的範例示範如何提交工作，並提供多個用戶端，包括 Web 入口網站和命令列用戶端。

<!---HONumber=Oct15_HO3-->