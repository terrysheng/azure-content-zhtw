<properties urldisplayname="Team Foundation Service" headerexpose="" pageTitle="在本機的計算模擬器中分析雲端服務" metakeywords="" footerexpose="" description="" umbraconavihide="0" disquscomments="1" title="使用 Visual Studio 分析工具，在 Azure 計算模擬器中本機測試雲端服務的效能" authors="ghogen" manager="douge" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="ghogen" />

# 使用 Visual Studio 分析工具，在 Azure 計算模擬器中本機測試雲端服務的效能

各種工具和技術可用於測試雲端服務的效能。
當您將雲端服務發行至 Azure 時，可以讓 Visual Studio 收集分析資料，
然後在本機分析它 (如[測試雲端服務的效能][測試雲端服務的效能]中所述)。
您也可以使用診斷來追蹤各種效能
計數器 (如[在 Azure 中使用效能計數器][在 Azure 中使用效能計數器]中所述)。
您可能也想要先在計算模擬器中本機分析應用程式，再將之部署至雲端。

本文涵蓋進行分析的「CPU 取樣」方法，這可以在模擬器上本機完成。CPU 取樣不是非常侵入式的分析方法。分析工具會按指定的取樣間隔取得呼叫堆疊的快照集。會收集一段時間的資料，而且資料會顯示在報告中。此分析方法傾向指出在計算密集應用程式中的哪個位置完成大部分的 CPU 工作。這可讓您有機會聚焦在應用程式耗用最多時間的「最忙碌路徑」。

## 必要條件

只有在您有 Visual Studio Premium 或 Visual Studio Ultimate 的狀況下，才能在本機執行分析工具。

## 本文內容：

-   [步驟 1：設定 Visual Studio 進行分析][步驟 1：設定 Visual Studio 進行分析]

-   [步驟 2：連結至程序][步驟 2：連結至程序]

-   [步驟 3：檢視分析報告][步驟 3：檢視分析報告]

-   [步驟 4：進行變更與比較效能][步驟 4：進行變更與比較效能]

-   [疑難排解][疑難排解]

-   [後續步驟][後續步驟]

## <a name="step1"> </a>步驟 1：設定 Visual Studio 進行分析

首先，有些 Visual Studio 組態選項可能在進行分析時很實用。若要讓分析報告發揮作用，您需要應用程式的符號 (.pdb 檔案)，也需要系統庫的符號。您會希望確定參考可用的符號伺服器。若要這樣做，請在 Visual Studio 的 [工具] 功能表上，依序選擇 [選項]、[偵錯] 和 [符號]。請確定 Microsoft Symbol Servers 列在 [符號檔 (.pdb) 位置] 下方。您也可以參考 http://referencesource.microsoft.com/symbols，其中可能有其他符號檔案。

![][0]

如有需要，您可以設定 Just My Code 來簡化分析工具所產生的報告。啟用 Just My Code 之後，會簡化函數呼叫堆疊，因此報告中會隱藏整個是程式庫和 .NET Framework 的內部呼叫。在 [工具] 功能表上，選擇 [選項]。然後展開 [效能工具] 節點，並選擇 [一般]。選取 [啟用 Just My Code 以進行分析工具報告] 核取方塊。

![][1]

您可以將這些指示與現有專案或新的專案搭配使用。如果您建立新的專案來嘗試上面所述的技術，請選擇 C# [Azure 雲端服務] 專案，然後選取 [Web 角色] 和 [背景工作角色]。

![][2]

舉例來說，在專案中新增某個程式碼，而這個程式碼會耗用許多時間，並示範某個明顯的效能問題。
例如，將下列程式碼新增至背景工作角色專案：

    public class Concatenator
    {
        public static string Concatenate(int number)
        {
            int count;
            string s = ""
            for (count = 0; count < number; count++)
            {
                s += "\n" + count.ToString();
            }
            return s;
        }
    }

請在背景工作角色的 RoleEntryPoint 衍生類別中，從 Run 方法呼叫此程式碼。

    public override void Run()
    {
        while (true)
        {
            Concatenator.Concatenate(10000);
        }
    }

在方案組態設定為 [發行] 的情況下，在本機建置和執行雲端服務。這確保會建立在本機執行應用程式時所需的所有檔案和資料夾，並確保已啟動所有模擬器。

## <a name="step2"> </a>步驟 2：連結至程序

您必須將分析工具連結至執行中程序，而不是從 Visual Studio 2010 IDE 啟動應用程式來分析應用程式。

若要將分析工具連結至程序，請在 [分析] 功能表上選擇 [分析工具] 和 [連結/中斷連結]。

![][3]

若為背景工作角色，請尋找 WaWorkerHost.exe 程序。

![][4]

如果您的專案資料夾位於網路磁碟機上，則分析工具會要求您提供另一個位置來儲存分析報告。

您也可以藉由連結至 WaIISHost.exe 來連結至 Web 角色。
如果您的應用程式中有多個背景工作角色程序，則需使用 processID 進行區別。您可以存取 Process 物件，以透過程式設計方式查詢 processID。例如，如果您將此程式碼新增至角色中 RoleEntryPoint 衍生類別的 Run 方法，則可查看計算模擬器 UI 中的記錄，以得知要連線的程序。

    var process = System.Diagnostics.Process.GetCurrentProcess();
    var message = String.Format("Process ID: {0}", process.Id);
    Trace.WriteLine(message, "Information");

若要檢視記錄，請啟動計算模擬器 UI。

![][5]

按一下主控台視窗的標題列，以在計算模擬器 UI 中開啟背景工作角色記錄主控台視窗。您可以在記錄中看到程序 ID。

![][6]

連結之後，請在應用程式 UI 中執行步驟 (需要時) 來重現案例。

當您想要停止分析時，請選擇 [停止分析] 連結。

![][7]

## <a name="step3"> </a>步驟 3：檢視效能報告

會顯示您應用程式的效能報告。

此時，分析工具會停止執行、將資料儲存為 .vsp 檔案，並顯示可顯示此資料分析的報告。

![][8]

如果您在「最忙碌路徑」中看到 String.wstrcpy，請按一下 Just My Code 變更檢視，只顯示使用者程式碼。如果您看到 String.Concat，請嘗試按 [顯示所有程式碼] 按鈕。

您應該會發現 Concatenate 方法和 String.Concat 耗用了大部分的執行時間。

![][9]

如果您已在本文中新增字串串連碼，則應該會在 [工作清單] 中看到警告。您也可能會看到有大量記憶體回收的警告，原因是處置的字串數目。

![][10]

## <a name="step4"> </a>步驟 4：進行變更與比較效能

您也可以在變更程式碼之前和之後比較效能。請編輯程式碼，將字串串連作業取代為使用 StringBuilder：

    public static string Concatenate(int number)
    {
        int count;
        System.Text.StringBuilder builder = new System.Text.StringBuilder("");
        for (count = 0; count < number; count++)
        {
             builder.Append("\n" + count.ToString());
        }
        return builder.ToString();
    }

請進行另一個效能執行，然後比較效能。在 [效能總管] 中，如果這些執行都位於相同的工作階段中，則只能選取兩份報告，並開啟捷徑功能表，然後選擇 [比較效能報告]。如果您想要與另一個效能工作階段中的執行進行比較，請開啟 [分析] 功能表，然後選擇 [比較效能報告]。請在顯示的對話方塊中指定兩個檔案。

![][11]

報告會反白顯示兩個執行之間的差異。

![][12]

恭喜！您已開始使用分析工具。

## <a name="troubleshooting"> </a>疑難排解

-   確定您正在分析發行組建。

-   如果未在 [分析工具] 功能表上啟用 [連結/中斷連結] 選項，請執行 [效能精靈]。

-   使用計算模擬器 UI 檢視您應用程式的狀態。

-   如果您在模擬器中啟動應用程式或是連結分析工具時發生問題，請關閉並重新啟動計算模擬器。如果這樣未解決問題，請嘗試重新開機。如果您使用計算模擬器來暫停和移除執行中部署，則可能會發生此問題。

-   如果您已從命令列使用任何分析命令 (尤其是全域設定)，請確定已呼叫 VSPerfClrEnv /globaloff，並已關閉 VsPerfMon.exe。

-   取樣時，如果您看到訊息「PRF0025：未收集任何資料」，請確認您連結的程序具有 CPU 活動。未執行任何計算工作的應用程式可能不會產生任何取樣資料。在執行任何取樣之前，也可能已結束程序。請確認所分析角色的 Run 方法未終止。

## <a name="nextSteps"> </a>後續步驟

在 Visual Studio 2010 分析工具中，不支援在模擬器中檢測 Azure 二進位，但是，如果您想要測試記憶體配置，則可以在分析時選擇該選項。您也可以選擇並行分析來協助您判斷執行緒是否浪費時間來競爭鎖定，或選擇階層互動分析來協助您追蹤在應用程式階層之間互動時的效能問題 (最常發生在資料層與背景工作角色之間)。您可以檢視應用程式所產生的資料庫查詢，以及使用分析資料來提高資料庫的使用。如需階層互動分析的相關資訊，請參閱[逐步解說：在 Visual Studio Team System 2010 中使用階層互動分析][逐步解說：在 Visual Studio Team System 2010 中使用階層互動分析]。

  [測試雲端服務的效能]: http://msdn.microsoft.com/zh-tw/library/windowsazure/hh369930.aspx
  [在 Azure 中使用效能計數器]: http://www.windowsazure.com/zh-tw/develop/net/common-tasks/performance-profiling
  [步驟 1：設定 Visual Studio 進行分析]: #step1
  [步驟 2：連結至程序]: #step2
  [步驟 3：檢視分析報告]: #step3
  [步驟 4：進行變更與比較效能]: #step4
  [疑難排解]: #troubleshooting
  [後續步驟]: #nextSteps
  [0]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally09.png
  [1]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally08.png
  [2]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally10.png
  [3]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally02.png
  [4]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally05.png
  [5]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally010.png
  [6]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally07.png
  [7]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally06.png
  [8]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally03.png
  [9]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally011.png
  [10]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally04.png
  [11]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally013.png
  [12]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally012.png
  [逐步解說：在 Visual Studio Team System 2010 中使用階層互動分析]: http://blogs.msdn.com/b/habibh/archive/2009/06/30/walkthrough-using-the-tier-interaction-profiler-in-visual-studio-team-system-2010.aspx
