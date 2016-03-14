<properties
	pageTitle="如何在虛擬機器中使用 Azure 診斷 | Microsoft Azure"
	description="使用 Azure 診斷從 Azure 虛擬機器收集資料，以進行偵錯、測量效能、監視、流量分析等。"
	services="virtual-machines"
	documentationCenter=".net"
	authors="rboucher"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="02/20/2016"
	ms.author="robb"/>



# 在 Azure 虛擬機器中啟用診斷

如需有關 Azure 診斷的背景資訊，請參閱 [Azure 診斷概觀](azure-diagnostics.md)。

## 如何在虛擬機器中啟用診斷

本逐步解說說明如何從遠端的開發電腦將診斷安裝至 Azure 虛擬機器。您也將了解如何實作在該 Azure 虛擬機器上執行，並使用 .NET [EventSource 類別][]發出遙測資料的應用程式。Azure 診斷可用來收集遙測資料，並將資料儲存在 Azure 儲存體帳戶。

### 必要條件
本逐步解說假設您擁有 Azure 訂用帳戶，並且搭配 Azure SDK 使用 Visual Studio 2013。如果您沒有 Azure 訂用帳戶，可以註冊[免費試用版][]。請務必[安裝及設定 Azure PowerShell 0.8.7 版或更新版本][]。

### 步驟 1：建立虛擬機器
1.	在您的開發電腦上，啟動 Visual Studio 2013。
2.	在 Visual Studio [**伺服器總管**] 中展開 [**Azure**]，以滑鼠右鍵按一下 [**虛擬機器**]，然後選取 [**建立虛擬機器**]。
3.	在 [選擇訂用帳戶] 對話方塊中，選取您的 Azure 訂用帳戶，然後按 [下一步]。
4.	在 [**選取虛擬機器映像**] 對話方塊中，選取 [**Windows Server 2012 R2 Datacenter，2014 年 11 月**]，然後按 [**下一步**]。
5.	在 [**虛擬機器基本設定**] 中，將虛擬機器名稱設為 "wadexample"。設定您的系統管理員使用者名稱和密碼，然後按 [下一步]。
6.	在 [**雲端服務設定**] 對話方塊中，建立名為 "wadexampleVM" 的新雲端服務。建立名為 "wadexample" 的新儲存體帳戶，然後按 [**下一步**]。
7.	按一下 [建立]。

### 步驟 2：建立您的應用程式
1.	在您的開發電腦上，啟動 Visual Studio 2013。
2.	建立以 .NET Framework 4.5 為目標的新 Visual C# 主控台應用程式。將專案命名為 "WadExampleVM"。![CloudServices\_diag\_new\_project](./media/virtual-machines-dotnet-diagnostics/NewProject.png)
3.	以下列程式碼取代 Program.cs 的內容。**SampleEventSourceWriter** 類別實作四種記錄方法：**SendEnums**、**MessageMethod**、**SetOther** 和 **HighFreq**。傳遞至 WriteEvent 方法的第一個參數定義個別事件的識別碼。Run 方法實作一個無限迴圈，每 10 秒呼叫一次在 **SampleEventSourceWriter** 類別中實作的每種記錄方法。

		using System;
		using System.Diagnostics;
		using System.Diagnostics.Tracing;
		using System.Threading;

		namespace WadExampleVM
		{
    	sealed class SampleEventSourceWriter : EventSource
    	{
        	public static SampleEventSourceWriter Log = new SampleEventSourceWriter();
        	public void SendEnums(MyColor color, MyFlags flags) { if (IsEnabled())  WriteEvent(1, (int)color, (int)flags); }// Cast enums to int for efficient logging.
        	public void MessageMethod(string Message) { if (IsEnabled())  WriteEvent(2, Message); }
        	public void SetOther(bool flag, int myInt) { if (IsEnabled())  WriteEvent(3, flag, myInt); }
        	public void HighFreq(int value) { if (IsEnabled()) WriteEvent(4, value); }

    	}

    	enum MyColor
    	{
        	Red,
        	Blue,
        	Green
    	}

    	[Flags]
    	enum MyFlags
    	{
        	Flag1 = 1,
        	Flag2 = 2,
        	Flag3 = 4
    	}

    	class Program
    	{
        static void Main(string[] args)
        {
            Trace.TraceInformation("My application entry point called");

            int value = 0;

            while (true)
            {
                Thread.Sleep(10000);
                Trace.TraceInformation("Working");

                // Emit several events every time we go through the loop
                for (int i = 0; i < 6; i++)
                {
                    SampleEventSourceWriter.Log.SendEnums(MyColor.Blue, MyFlags.Flag2 | MyFlags.Flag3);
                }

                for (int i = 0; i < 3; i++)
                {
                    SampleEventSourceWriter.Log.MessageMethod("This is a message.");
                    SampleEventSourceWriter.Log.SetOther(true, 123456789);
                }

                if (value == int.MaxValue) value = 0;
                SampleEventSourceWriter.Log.HighFreq(value++);
            }

        }
    	}
		}


4.	儲存檔案，然後從 [建置] 功能表選取 [建置方案]，以建置您的程式碼。


### 步驟 3：部署應用程式
1.	在 [**方案總管**] 中以滑鼠右鍵按一下 **WadExampleVM** 專案，然後選擇 [**在檔案總管中開啟資料夾**]。
2.	瀏覽至 *bin\\Debug* 資料夾並複製所有檔案 (WadExampleVM.*)。
3.	在 [伺服器總管] 中，以滑鼠右鍵按一下虛擬機器並選擇 [使用遠端桌面連線]。
4.	連線至 VM 之後，建立名為 WadExampleVM 的資料夾，並將您的應用程式檔案貼到資料夾中。
5.	啟動 WadExampleVM.exe 應用程式。您應該會看見空白的主控台視窗。

### 步驟 4：建立您的診斷組態並安裝擴充功能
1.	執行下列 PowerShell 命令，將公用組態檔結構描述定義下載至您的開發電腦：

		(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File -Encoding utf8 -FilePath 'WadConfig.xsd'

2.	在已開啟專案的 Visual Studio 中，或在未開啟專案的 Visual Studio 執行個體中，開啟新的 XML 檔。在 Visual Studio 中，選取 [加入] -> [新增項目...] -> [Visual C# 項目] -> [資料] -> [XML 檔]。將檔案命名為 "WadExample.xml"
3.	將 WadConfig.xsd 與組態檔產生關聯。確定 WadExample.xml 編輯器視窗是使用中視窗。按 **F4** 鍵開啟 [屬性] 視窗。在 [屬性] 視窗中，按一下 [結構描述] 屬性。在 [結構描述] 屬性中，按一下 [...]。按一下 [**加入...**] 按鈕並瀏覽至您儲存 XSD 檔的位置，然後選取檔案 WadConfig.xsd。按一下 [確定]。
4.	以下列 XML 取代 WadExample.xml 組態檔的內容，然後儲存檔案。此組態檔可定義兩個要收集的效能計數器：一個用於 CPU 使用率，一個用於記憶體使用率。組態會接著定義四個事件，分別對應至 SampleEventSourceWriter 類別中的方法。

```
		<?xml version="1.0" encoding="utf-8"?>
		<PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  			<WadCfg>
    			<DiagnosticMonitorConfiguration overallQuotaInMB="25000">
      			<PerformanceCounters scheduledTransferPeriod="PT1M">
        			<PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT1M" unit="percent" />
        			<PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT1M" unit="bytes"/>
      				</PerformanceCounters>
      				<EtwProviders>
        				<EtwEventSourceProviderConfiguration provider="SampleEventSourceWriter" scheduledTransferPeriod="PT5M">
          					<Event id="1" eventDestination="EnumsTable"/>
          					<Event id="2" eventDestination="MessageTable"/>
          					<Event id="3" eventDestination="SetOtherTable"/>
          					<Event id="4" eventDestination="HighFreqTable"/>
          					<DefaultEvents eventDestination="DefaultTable" />
        				</EtwEventSourceProviderConfiguration>
      				</EtwProviders>
    			</DiagnosticMonitorConfiguration>
  			</WadCfg>
		</PublicConfig>
```

### 步驟 5：從遠端將診斷安裝至您的 Azure 虛擬機器上
用於管理 VM 上之診斷的 PowerShell Cmdlet 為：Set-AzureVMDiagnosticsExtension、Get-AzureVMDiagnosticsExtension 和 Remove-AzureVMDiagnosticsExtension。

1.	在您的開發電腦上，開啟 Azure PowerShell。
2.	執行指令碼，從遠端將診斷安裝至您的 VM (以 wadexamplevm 儲存體帳戶的儲存體帳戶金鑰取代 *StorageAccountKey*)：

		$storage_name = "wadexamplevm"
		$key = "<StorageAccountKey>"
		$config_path="c:\users<user>\documents\visual studio 2013\Projects\WadExampleVM\WadExampleVM\WadExample.xml"
		$service_name="wadexamplevm"
		$vm_name="WadExample"
		$storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key
		$VM1 = Get-AzureVM -ServiceName $service_name -Name $vm_name
		$VM2 = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $config_path -Version "1.*" -VM $VM1 -StorageContext $storageContext
		$VM3 = Update-AzureVM -ServiceName $service_name -Name $vm_name -VM $VM2.VM


### 步驟 6：查看您的遙測資料
在 Visual Studio 的 [伺服器總管] 中，瀏覽至 wadexample 儲存體帳戶。在 VM 執行約 5 分鐘之後，您應該會看到資料表 **WADEnumsTable**、**WADHighFreqTable**、**WADMessageTable**、**WADPerformanceCountersTable** 和 **WADSetOtherTable**。按兩下其中一個資料表以檢視收集的遙測資料。

![CloudServices\_diag\_wadexamplevm\_tables](./media/virtual-machines-dotnet-diagnostics/WadExampleVMTables.png)

## 組態檔結構描述

診斷組態檔定義當診斷代理程式啟動時，用來初始化診斷組態設定的值。如需相關的有效值和範例，請參閱[最新的結構描述參考](https://msdn.microsoft.com/library/azure/mt634524.aspx)。

## 疑難排解

如需詳細資訊，請參閱 [Azure 診斷的疑難排解](azure-diagnostics-troubleshooting.md)。


## 後續步驟
[請參閱虛擬機器相關的 Azure 診斷文章清單](azure-diagnostics.md#virtual-machines-using-azure-diagnostics)，以變更您收集的資料、進行問題疑難排解，或深入了解一般的診斷。


[EventSource 類別]: http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx

[Debugging an Azure Application]: http://msdn.microsoft.com/library/windowsazure/ee405479.aspx
[Collect Logging Data by Using Azure Diagnostics]: http://msdn.microsoft.com/library/windowsazure/gg433048.aspx
[免費試用版]: http://azure.microsoft.com/pricing/free-trial/
[安裝及設定 Azure PowerShell 0.8.7 版或更新版本]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/

<!---HONumber=AcomDC_0302_2016-------->