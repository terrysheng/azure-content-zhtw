<properties urlDisplayName="Compute Intensive .NET Task" pageTitle="虛擬機器上需密集運算的 .NET 工作 - Azure" metaKeywords="deploying compute .NET application, vm .NET application, Service Bus queue monitoring, remote monitoring" description="了解如何在 Azure 虛擬機器上部署與執行需密集運算的 .NET 應用程式，並使用服務匯流排佇列來遠端監控進度。" metaCanonical="" services="virtual-machines" documentationCenter=".NET" title="如何在 Azure 虛擬機器上以 .NET 執行需密集運算的工作" authors="wpickett" solutions="" manager="wpickett" editor="mollybos" scriptId="" videoId="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="wpickett" />

# 如何在 Azure 虛擬機器上以 .NET 執行需密集運算的工作

透過 Azure，您可以使用虛擬機器來處理需密集運算的工作，例如：虛擬機器可以處理工作並將結果傳遞至用戶端電腦或行動應用程式。完成本指南的內容後，您將了解如何建立虛擬機器，以便執行需密集運算並可由其他 .NET 應用程式監控的 .NET 應用程式。

本教學課程假設您知道如何建立 .NET 主控台應用程式。未假設您具備任何 Azure 相關知識。

您將了解：

-   如何建立虛擬機器。
-   如何從遠端登入虛擬機器。
-   如何建立服務匯流排命名空間。
-   如何建立 .NET 應用程式，以便執行需密集運算的工作。
-   如何建立 .NET 應用程式，以便監控需密集運算之工作的進度。
-   如何執行 .NET 應用程式。
-   如何停止 .NET 應用程式。

本教學課程將使用旅行業務員問題 (Traveling Salesman Problem) 來進行大量運算工作。以下是執行需密集運算之工作的 .NET 應用程式範例：

![Traveling Salesman Problem solver][Traveling Salesman Problem solver]

以下是監控需密集運算之工作的 .NET 應用程式範例：

![Traveling Salesman Problem client][Traveling Salesman Problem client]

[WACOM.INCLUDE [create-account-and-vms-note](../includes/create-account-and-vms-note.md)]

## 建立虛擬機器

1.  登入 [Azure 管理入口網站][Azure 管理入口網站]。
2.  按一下 [新增]。
3.  按一下 [虛擬機器]。
4.  按一下 [快速建立]。
5.  在 [Create a virtual machine] 畫面中輸入 [DNS 名稱] 的值。
6.  從 [映像] 下拉式清單中選取映像，例如 **Windows Server 2012**。
7.  在 [使用者名稱] 欄位中輸入系統管理員的名稱。請記住即將輸入的名稱和密碼，因為當您從遠端登入此虛擬機器時將需要用到它們。
8.  在 [新增密碼] 欄位中輸入密碼，然後在 [確認] 欄位中再輸入一次。
9.  從 [位置] 下拉式清單中選取您的虛擬機器的資料中心位置。
10. 按一下 [Create virtual machine]。系統隨即會開始建立虛擬機器。您可以在管理入口網站的 [虛擬機器] 區段中監控狀態。當狀態顯示為 [作用中] 時，您即可登入該虛擬機器。

## 從遠端登入虛擬機器

1.  登入[管理入口網站][Azure 管理入口網站]。
2.  按一下 [虛擬機器]。
3.  按一下要登入的虛擬機器名稱。
4.  按一下 [連接]。
5.  視需要回應提示以連接虛擬機器。當要求提供系統管理員名稱和密碼的提示出現時，請使用在建立虛擬機器時提供的值。

## 如何建立服務匯流排命名空間

若要開始在 Azure 中使用服務匯流排佇列，
首先必須建立服務命名空間。服務命名空間提供範圍容器，
可在應用程式內定址服務匯流排資源。

建立服務命名空間：

1.  登入 [Azure 管理入口網站][Azure 管理入口網站]。
2.  在管理入口網站的左方瀏覽窗格中，按一下 [服務匯流排]。
3.  在管理入口網站的下方窗格中，按一下 [建立]。

    ![Create new service bus][Create new service bus]

4.  在 [Create a namespace] 對話方塊中，輸入命名空間名稱。系統會立即檢查此名稱是否可用，因為該名稱必須是唯一名稱。

    ![Create a namespace dialog][Create a namespace dialog]

5.  確定命名空間名稱可用之後，請選擇要代管命名空間的區域 (務必使用要代管虛擬機器的相同區域)。
    <div class="dev-callout">

    **重要事項**
    挑選您使用或打算使用於虛擬機器的**相同區域**。這樣可以獲得最佳效能。

    </div>

6.  如果您用以登入的帳戶有一個以上的 Azure 訂閱，請選取要用於命名空間的訂閱 (如果您用以登入的帳戶只有一個訂閱，您就不會看到含有訂閱的下拉式清單)。
7.  按一下核取記號。此時系統會建立並啟用服務命名空間。系統為帳戶提供資源時，您可能需要等幾分鐘。

    ![Click create screenshot][Click create screenshot]

然後，您建立的命名空間就會出現在管理入口網站中，稍待片刻就會生效。等到狀態變成 [作用中] 之後，再繼續進行下一步。

## 取得命名空間的預設管理認證

若要在新的命名空間上執行管理作業 (例如建立佇列)，
您必須取得命名空間的
管理認證。

1.  在左側瀏覽窗格中，按一下 [服務匯流排] 節點，以
    顯示可用的命名空間清單：
    ![[可用的命名空間] 螢幕擷取畫面][]
2.  從顯示的清單中，選取您剛建立的命名空間：
    ![[命名空間清單] 螢幕擷取畫面][]
3.  按一下 [存取金鑰]。
    ![[存取金鑰] 按鈕][]
4.  在此對話方塊中，找出 [Default Issuer] 和 [Default Key] 項目。請記下這些值，接下來會利用此資訊對命名空間執行作業。

## 如何建立 .NET 應用程式以便執行需密集運算的工作

1.  在您的部署機器 (不一定是您建立的虛擬機器) 上，下載 [Azure SDK for .NET][Azure SDK for .NET]。
2.  利用名為 **TSPSolver** 的專案建立 .NET 主控台應用程式。確定已針對 **NET Framework 4** (而非 **.NET Framework 4 用戶端設定檔**) 設定目標 Framework。執行下列動作，即可在您建立專案後設定此目標 Framework：在 Visual Studio 的功能表中，依序按一下 [專案]、[屬性]、[應用程式] 索引標籤，然後設定 [目標 Framework] 的值。
3.  加入 Microsoft ServiceBus 程式庫中。在 Visual Studio 方案總管中，於 [TSPSolver] 上按一下滑鼠右鍵，按一下 [新增參考]、按一下 [瀏覽] 索引標籤，瀏覽至 **C:\\Program Files\\Microsoft SDKs\\Windows Azure.NET SDK\\2012-06\\ref** 並選取 **Microsoft.ServiceBus.dll** 作為參考。
4.  加入 System Runtime Serialization 程式庫中。在 Visual Studio 方案總管中，於 [TSPSolver] 上按一下滑鼠右鍵，按一下 [新增參考]、按一下 [.NET] 索引標籤，然後選取 **System.Runtime.Serialization** 作為參考。
5.  將這一節結尾的範例程式碼使用於 **Program.cs** 的內容。
6.  請將 **your\_service\_bus\_namespace**、**your\_service\_bus\_owner** 及 **your\_service\_bus\_key** 預留位置，分別修改成使用您服務匯流排 [命名空間]、[Default Issuer] 及 [預設金鑰] 的值。
7.  編譯應用程式。這會在您專案的 **bin** 資料夾 (**bin\\release** 或 **bin\\debug**，視您的目標為版本或偵錯組建而定) 中建立 **TSPSolver.exe**。您稍後會將此可執行檔和 Microsoft.ServiceBus.dll 複製到您的虛擬機器。

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.IO;

    using Microsoft.ServiceBus;
    using Microsoft.ServiceBus.Messaging;

    namespace TSPSolver
    {
        class Program
        {
            // Value specifying how often to provide an update to the console.
            private static long loopCheck = 100000000;
            private static long nTimes = 0, nLoops = 0;

            private static double[,] distances;
            private static String[] cityNames;
            private static int[] bestOrder;
            private static double minDistance;

            private static NamespaceManager namespaceManager;
            private static QueueClient queueClient;
            private static String queueName = "TSPQueue";

            private static void BuildDistances(String fileLocation, int numCities)
            {

                try
                {
                    StreamReader sr = new StreamReader(fileLocation);
                    String[] sep1 = { ", " };

                    double[,] cityLocs = new double[numCities, 2];

                    for (int i = 0; i < numCities; i++)
                    {
                        String[] line = sr.ReadLine().Split(sep1, StringSplitOptions.None);
                        cityNames[i] = line[0];
                        cityLocs[i, 0] = Convert.ToDouble(line[1]);
                        cityLocs[i, 1] = Convert.ToDouble(line[2]);
                    }
                    sr.Close();

                    for (int i = 0; i < numCities; i++)
                    {
                        for (int j = i; j < numCities; j++)
                        {
                            distances[i, j] = hypot(Math.Abs(cityLocs[i, 0] - cityLocs[j, 0]), Math.Abs(cityLocs[i, 1] - cityLocs[j, 1]));
                            distances[j, i] = distances[i, j];
                        }
                    }
                }
                catch (Exception e)
                {
                    throw e;
                }
            }

            private static double hypot(double x, double y)
            {
                return Math.Sqrt(x * x + y * y);
            }

            private static void permutation(List<int> startCities, double distSoFar, List<int> restCities)
            {
                try
                {

                    nTimes++;
                    if (nTimes == loopCheck)
                    {
                        nLoops++;
                        nTimes = 0;
                        DateTime dateTime = DateTime.Now;
                        Console.Write("Current time is {0}.", dateTime);
                        Console.WriteLine(" Completed {0} iterations of size of {1}.", nLoops, loopCheck);
                    }

                    if ((restCities.Count == 1) && ((minDistance == -1) || (distSoFar + distances[restCities[0], startCities[0]] + distances[restCities[0], startCities[startCities.Count - 1]] < minDistance)))
                    {
                        startCities.Add(restCities[0]);
                        newBestDistance(startCities, distSoFar + distances[restCities[0], startCities[0]] + distances[restCities[0], startCities[startCities.Count - 2]]);
                        startCities.Remove(startCities[startCities.Count - 1]);
                    }
                    else
                    {
                        for (int i = 0; i < restCities.Count; i++)
                        {
                            startCities.Add(restCities[0]);
                            restCities.Remove(restCities[0]);
                            permutation(startCities, distSoFar + distances[startCities[startCities.Count - 1], startCities[startCities.Count - 2]], restCities);
                            restCities.Add(startCities[startCities.Count - 1]);
                            startCities.Remove(startCities[startCities.Count - 1]);
                        }
                    }
                }
                catch (Exception e)
                {
                    throw e;
                }
            }

            private static void newBestDistance(List<int> cities, double distance)
            {
                try
                {
                    minDistance = distance;
                    String cityList = "Shortest distance is " + minDistance + ", with route: ";

                    for (int i = 0; i < bestOrder.Length; i++)
                    {
                        bestOrder[i] = cities[i];
                        cityList += cityNames[bestOrder[i]];
                        if (i != bestOrder.Length - 1)
                            cityList += ", ";
                    }
                    Console.WriteLine(cityList);
                    queueClient.Send(new BrokeredMessage(cityList));
                }
                catch (Exception e)
                {
                    throw e;
                }
            }

            static void Main(string[] args)
            {
                try
                {

                    String serviceBusNamespace = "your_service_bus_namespace";
                    String issuer = "your_service_bus_owner";
                    String key = "your_service_bus_key";

                    String connectionString = @"Endpoint=sb://" +
                           serviceBusNamespace +
                           @".servicebus.windows.net/;SharedSecretIssuer=" +
                           issuer + @";SharedSecretValue=" + key;

                    int numCities = 10; // Use as the default, if no value is specified
                    // at the command line.
                    if (args.Count() != 0)
                    {

                        if (args[0].ToLower().CompareTo("createqueue") == 0)
                        {
                            // No processing to occur other than creating the queue.
                            namespaceManager = NamespaceManager.CreateFromConnectionString(connectionString);
                            namespaceManager.CreateQueue(queueName);
                            Console.WriteLine("Queue named {0} was created.", queueName);
                            Environment.Exit(0);
                        }

                        if (args[0].ToLower().CompareTo("deletequeue") == 0)
                        {
                            // No processing to occur other than deleting the queue.
                            namespaceManager = NamespaceManager.CreateFromConnectionString(connectionString);
                            namespaceManager.DeleteQueue("TSPQueue");
                            Console.WriteLine("Queue named {0} was deleted.", queueName);
                            Environment.Exit(0);
                        }

                        // Neither creating or deleting a queue.
                        // Assume the value passed in is the number of cities to solve.
                        numCities = Convert.ToInt32(args[0]);
                    }

                    Console.WriteLine("Running for {0} cities.", numCities);

                    queueClient = QueueClient.CreateFromConnectionString(connectionString, "TSPQueue");

                    List<int> startCities = new List<int>();
                    List<int> restCities = new List<int>();

                    startCities.Add(0);
                    for (int i = 1; i < numCities; i++)
                    {
                        restCities.Add(i);
                    }
                    distances = new double[numCities, numCities];
                    cityNames = new String[numCities];
                    BuildDistances(@"c:\tsp\cities.txt", numCities);
                    minDistance = -1;
                    bestOrder = new int[numCities];
                    permutation(startCities, 0, restCities);
                    Console.WriteLine("Final solution found!");
                    queueClient.Send(new BrokeredMessage("Complete"));

                    queueClient.Close();
                    Environment.Exit(0);

                }
                catch (ServerBusyException serverBusyException)
                {
                    Console.WriteLine("ServerBusyException encountered");
                    Console.WriteLine(serverBusyException.Message);
                    Console.WriteLine(serverBusyException.StackTrace);
                    Environment.Exit(-1);
                }
                catch (ServerErrorException serverErrorException)
                {
                    Console.WriteLine("ServerErrorException encountered");
                    Console.WriteLine(serverErrorException.Message);
                    Console.WriteLine(serverErrorException.StackTrace);
                    Environment.Exit(-1);
                }
                catch (Exception exception)
                {
                    Console.WriteLine("Exception encountered");
                    Console.WriteLine(exception.Message);
                    Console.WriteLine(exception.StackTrace);
                    Environment.Exit(-1);
                }
            }
        }
    }

## 如何建立 .NET 應用程式以便監控需密集運算之工作的進度

1.  在開發電腦上，以 **TSPClient** 作為專案名稱來建立 .NET 主控台應用程式。確定已針對 **NET Framework 4** (而非 **.NET Framework 4 用戶端設定檔**) 設定目標 Framework。執行下列動作，即可在您建立專案後設定此目標 Framework：在 Visual Studio 的功能表中，依序按一下 [專案]、[屬性]、[應用程式] 索引標籤，然後設定 [目標 Framework] 的值。
2.  加入 Microsoft ServiceBus 程式庫中。在 Visual Studio 方案總管中，於 [TSPSolver] 上按一下滑鼠右鍵，按一下 [新增參考]、按一下 [瀏覽] 索引標籤，瀏覽至 **C:\\Program Files\\Microsoft SDKs\\Windows Azure.NET SDK\\2012-06\\ref** 並選取 **Microsoft.ServiceBus.dll** 作為參考。
3.  加入 System Runtime Serialization 程式庫中。在 Visual Studio 方案總管中，於 [TSPClient] 上按一下滑鼠右鍵，按一下 [新增參考]、按一下 [.NET] 索引標籤，然後選取 **System.Runtime.Serialization** 作為參考。
4.  將這一節結尾的範例程式碼使用於 **Program.cs** 的內容。
5.  請將 **your\_service\_bus\_namespace**、**your\_service\_bus\_owner** 及 **your\_service\_bus\_key** 預留位置，分別修改成使用您服務匯流排 [命名空間]、[Default Issuer] 及 [預設金鑰] 的值。
6.  編譯應用程式。這會在您專案的 **bin** 資料夾 (**bin\\release** 或 **bin\\debug**，視您的目標為版本或偵錯組建而定) 中建立 **TSPClient.exe**。您可以從部署機器執行此程式碼，或將此可執行檔和 Microsoft.ServiceBus.dll 複製到將執行用戶端應用程式的機器 (不一定要在您的虛擬機器上)。

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.IO;

    using Microsoft.ServiceBus;
    using Microsoft.ServiceBus.Messaging;
    using System.Threading; // For Thread.Sleep

    namespace TSPClient
    {
        class Program
        {

            static void Main(string[] args)
            {

                try
                {

                    Console.WriteLine("Starting at {0}", DateTime.Now);

                    String serviceBusNamespace = "your_service_bus_namespace";
                    String issuer = "your_service_bus_owner";
                    String key = "your_service_bus_key";

                    String connectionString = @"Endpoint=sb://" +
                           serviceBusNamespace +
                           @".servicebus.windows.net/;SharedSecretIssuer=" +
                           issuer + @";SharedSecretValue=" + key;

                    QueueClient queueClient = QueueClient.CreateFromConnectionString(connectionString, "TSPQueue");

                    BrokeredMessage message;

                    int waitMinutes = 3;  // Use as the default, if no value
                    // is specified at command line.

                    if (0 != args.Length)
                    {
                        waitMinutes = Convert.ToInt16(args[0]);
                    }

                    String waitString;
                    waitString = (waitMinutes == 1) ? "minute" : waitMinutes.ToString() + " minutes";

                    while (true)
                    {
                        message = queueClient.Receive();

                        if (message != null)
                        {
                            try
                            {
                                string str = message.GetBody<string>();
                                Console.WriteLine(str);

                                // Remove message from queue
                                message.Complete();

                                if ("Complete" == str)
                                {
                                    Console.WriteLine("Finished at {0}.", DateTime.Now);
                                    break;
                                }
                            }
                            catch (Exception e)
                            {
                                // Indicates a problem. Unlock the message in the queue.
                                message.Abandon();
                                throw e;
                            }
                        }
                        else
                        {
                            // The queue is empty.
                            Console.WriteLine("Queue is empty. Sleeping for another {0}.", waitString);
                            System.Threading.Thread.Sleep(60000 * waitMinutes);
                        }
                    }
                    queueClient.Close();
                    Environment.Exit(0);
                }
                catch (ServerBusyException serverBusyException)
                {
                    Console.WriteLine("ServerBusyException encountered");
                    Console.WriteLine(serverBusyException.Message);
                    Console.WriteLine(serverBusyException.StackTrace);
                    Environment.Exit(-1);
                }
                catch (ServerErrorException serverErrorException)
                {
                    Console.WriteLine("ServerErrorException encountered");
                    Console.WriteLine(serverErrorException.Message);
                    Console.WriteLine(serverErrorException.StackTrace);
                    Environment.Exit(-1);
                }
                catch (Exception exception)
                {
                    Console.WriteLine("Exception encountered");
                    Console.WriteLine(exception.Message);
                    Console.WriteLine(exception.StackTrace);
                    Environment.Exit(-1);
                }
            }
        }
    }

## 如何執行 .NET 應用程式

請執行大量運算應用程式，先建立佇列，然後解決旅行業務員問題，這會將目前的最佳路徑加入服務匯流排佇列。在大量運算應用程式執行時 (或執行後)，請執行用戶端以顯示來自服務匯流排佇列的結果。

### 如何執行大量運算應用程式

1.  登入虛擬機器。
2.  建立名為 **c:\\TSP** 的資料夾。這就是您將執行應用程式的地方。
3.  將 TSPSolver.exe 和 Microsoft.ServiceBus.dll (兩者均位於 TSPSolver 專案的 **bin** 資料夾中) 複製到 **c:\\TSP**。
4.  建立一個含有下列內容且名為 **c:\\TSP\\cities.txt** 的檔案：

        City_1, 1002.81, -1841.35
        City_2, -953.55, -229.6
        City_3, -1363.11, -1027.72
        City_4, -1884.47, -1616.16
        City_5, 1603.08, -1030.03
        City_6, -1555.58, 218.58
        City_7, 578.8, -12.87
        City_8, 1350.76, 77.79
        City_9, 293.36, -1820.01
        City_10, 1883.14, 1637.28
        City_11, -1271.41, -1670.5
        City_12, 1475.99, 225.35
        City_13, 1250.78, 379.98
        City_14, 1305.77, 569.75
        City_15, 230.77, 231.58
        City_16, -822.63, -544.68
        City_17, -817.54, -81.92
        City_18, 303.99, -1823.43
        City_19, 239.95, 1007.91
        City_20, -1302.92, 150.39
        City_21, -116.11, 1933.01
        City_22, 382.64, 835.09
        City_23, -580.28, 1040.04
        City_24, 205.55, -264.23
        City_25, -238.81, -576.48
        City_26, -1722.9, -909.65
        City_27, 445.22, 1427.28
        City_28, 513.17, 1828.72
        City_29, 1750.68, -1668.1
        City_30, 1705.09, -309.35
        City_31, -167.34, 1003.76
        City_32, -1162.85, -1674.33
        City_33, 1490.32, 821.04
        City_34, 1208.32, 1523.3
        City_35, 18.04, 1857.11
        City_36, 1852.46, 1647.75
        City_37, -167.44, -336.39
        City_38, 115.4, 0.2
        City_39, -66.96, 917.73
        City_40, 915.96, 474.1
        City_41, 140.03, 725.22
        City_42, -1582.68, 1608.88
        City_43, -567.51, 1253.83
        City_44, 1956.36, 830.92
        City_45, -233.38, 909.93
        City_46, -1750.45, 1940.76
        City_47, 405.81, 421.84
        City_48, 363.68, 768.21
        City_49, -120.3, -463.13
        City_50, 588.51, 679.33

5.  在命令提示字元，將目錄切換至 c:\\TSP。
6.  您將需要在執行 TSP 求解器排列之前，先建立服務匯流排佇列。請執行下列命令來建立服務匯流排佇列：

        TSPSolver createqueue

7.  建立完佇列之後，您便可以執行 TSP 求解器排列。例如，執行下列命令來執行 8 個城市的求解器。

        TSPSolver 8

如果您未指定數字，此 solver 將會針對 10 個城市執行。當求解器找到目前最短的路徑時，將會把這些路徑加入佇列中。

求解器將會執行直到完成所有路徑檢查為止。

> [WACOM.NOTE]
> 您指定的數目越大，求解器就會花越長的時間執行。例如，針對 14 個城市執行可能需花數分鐘，而針對 15 個城市執行可能需花數小時。增加至 16 個或更多城市可能需花數天執行 (最終可能達數周、數月及數年)。這是因為隨著城市數目增加，求解器所評估的排列數目也隨之激增的緣故。

### 如何執行監視用戶端應用程式

1.  登入您將執行用戶端應用程式的電腦。這不一定要是執行 **TSPSolver** 應用程式的同一部電腦，但也可以是同一部電腦。
2.  建立將執行您應用程式的資料夾。例如 **c:\\TSP**。
3.  將 **TSPClient.exe** 和 Microsoft.ServiceBus.dll (兩者均位於 TSPClient 專案的 **bin** 資料夾中) 複製到 c:\\TSP 資料夾。
4.  在命令提示字元，將目錄切換至 c:\\TSP。
5.  執行以下命令：

        TSPClient

    您可以選擇是否要傳遞命令列引數來指定檢查佇列之間的睡眠分鐘數。檢查佇列的預設睡眠期間為 3 分鐘，若未將任何命令列引數傳遞至 **TSPClient**，便會使用預設值。如果您想要使用其他值 (例如 1 分鐘) 做為睡眠間隔，請執行：

        TSPClient 1

    用戶端將會執行直到看見 [完成] 佇列訊息為止。請注意，如果您在未執行用戶端的情況下執行多個求解器，您可能需要執行用戶端多次，才能完全清空佇列。或者，您也可以刪除佇列，然後重新建立它。若要刪除佇列，請執行下列 **TSPSolver** (而非 **TSPClient**) 命令：

        TSPSolver deletequeue

## 如何停止 .NET 應用程式

不論是求解器或用戶端應用程式，只要您想在正常完成之前就予以結束，都可以按 [Ctrl+C] 來結束應用程式。

## 使用 TSPSolver 建立和刪除佇列的替代方式

除了使用 TSPSolver 建立或刪除佇列，您也可以使用 [Azure 管理入口網站][Azure 管理入口網站]來建立或刪除佇列。請造訪管理入口網站的服務匯流排區段，存取用於建立或刪除佇列的使用者介面，以及擷取連接字串、發行者和存取金鑰。您也可以檢視服務匯流排佇列的儀表板，進而檢視連入和連出訊息的度量。

  [Traveling Salesman Problem solver]: ./media/virtual-machines-dotnet-run-compute-intensive-task/WA_dotNetTSPSolver.png
  [Traveling Salesman Problem client]: ./media/virtual-machines-dotnet-run-compute-intensive-task/WA_dotNetTSPClient.png
  [Azure 管理入口網站]: https://manage.windowsazure.com
  [Create new service bus]: ./media/virtual-machines-dotnet-run-compute-intensive-task/ServiceBusCreateNew.png
  [Create a namespace dialog]: ./media/virtual-machines-dotnet-run-compute-intensive-task/CreateNameSpaceDialog.png
  [Click create screenshot]: ./media/virtual-machines-dotnet-run-compute-intensive-task/ClickCreate.png
  [[可用的命名空間] 螢幕擷取畫面]: ./media/virtual-machines-dotnet-run-compute-intensive-task/AvailableNamespaces.png
  [[命名空間清單] 螢幕擷取畫面]: ./media/virtual-machines-dotnet-run-compute-intensive-task/NamespaceList.png
  [[存取金鑰] 按鈕]: ./media/virtual-machines-dotnet-run-compute-intensive-task/AccessKey.png
  [Azure SDK for .NET]: http://www.windowsazure.com/zh-tw/develop/net/
