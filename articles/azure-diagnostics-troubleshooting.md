<properties
	pageTitle="針對 Azure 診斷疑難排解"
	description="針對在 Azure 雲端服務、虛擬機器中使用 Azure 診斷時出現的問題進行疑難排解"
	services="multiple"
	documentationCenter=".net"
	authors="rboucher"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="multiple"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="02/20/2016"
	ms.author="robb"/>


# Azure 診斷疑難排解
有關使用 Azure 診斷的疑難排解資訊。如需有關 Azure 診斷的詳細資訊，請參閱 [Azure 診斷概觀](azure-diagnostics.md#cloud-services)。

## Azure 診斷未啟動
診斷是由兩個元件所組成：客體代理程式外掛程式和監視代理程式。

在雲端服務角色中，客體代理程式外掛程式的記錄檔位於下列檔案中：

	*%SystemDrive%\ WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics<DiagnosticsVersion>*\CommandExecution.log

在 Azure 虛擬機器中，客體代理程式外掛程式的記錄檔位於下列檔案中：

		C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics<DiagnosticsVersion>\CommandExecution.log

外掛程式會傳回下列錯誤碼：

結束代碼|說明
---|---
0|成功。
-1|一般錯誤。
-2|無法載入 rcf 檔。<p>這是內部錯誤，只有當不正確地在 VM 上以手動方式叫用客體代理程式外掛程式啟動器時才會發生。
-3|無法載入診斷組態檔。<p><p>解決方法：這是組態檔未通過結構描述驗證的結果。解決方法是提供以結構描述編譯的組態檔。
-4|診斷之監視代理程式的另一個執行個體已在使用本機資源目錄。<p><p>解決方法：為 **LocalResourceDirectory** 指定其他值。
-6|客體代理程式外掛程式啟動器嘗試使用無效的命令列來啟動診斷。<p><p>這是內部錯誤，只有當不正確地在 VM 上以手動方式叫用客體代理程式外掛程式啟動器時才會發生。
-10|診斷外掛程式結束並發生未處理的例外狀況。
-11|客體代理程式無法建立負責啟動及監視監視代理程式的處理序。<p><p>解決方法：確認有足夠的系統資源，可啟動新的處理序。<p>
-101|呼叫診斷外掛程式時的引數無效。<p><p>這是內部錯誤，只有當不正確地在 VM 上以手動方式叫用客體代理程式外掛程式啟動器時才會發生。
-102|外掛程式處理序無法對本身進行初始化。<p><p>解決方法：確認有足夠的系統資源，可啟動新的處理序。
-103|外掛程式處理序無法對本身進行初始化。具體而言，其無法建立記錄器物件。<p><p>解決方法：確認有足夠的系統資源，可啟動新的處理序。
-104|無法載入客體代理程式提供的 rcf 檔。<p><p>這是內部錯誤，只有當不正確地在 VM 上以手動方式叫用客體代理程式外掛程式啟動器時才會發生。
-105|診斷外掛程式無法開啟診斷組態檔。<p><p>這是內部錯誤，只有當不正確地在 VM 上以手動方式叫用診斷外掛程式時才會發生。
-106|無法讀取診斷組態檔。<p><p>解決方法：這是組態檔未通過結構描述驗證的結果。因此，解決方法是提供以結構描述編譯的組態檔。您可以在 VM 上的 *%SystemDrive%\\WindowsAzure\\Config* 資料夾中，找到傳送給診斷擴充功能的 XML。開啟適當的 XML 檔並搜尋 **Microsoft.Azure.Diagnostics**，然後搜尋 [xmlCfg] 欄位。該資料是以 base64 編碼，因此您將需要[進行解碼](http://www.bing.com/search?q=base64+decoder)，才能查看診斷載入的 XML。<p>
-107|傳遞給監視代理程式的資源目錄無效。<p><p>這是內部錯誤，只有當不正確地在 VM 上以手動方式叫用監視代理程式時才會發生。</p>
-108 |無法將診斷組態檔轉換成監視代理程式組態檔。<p><p>這是內部錯誤，只有當使用無效的組態檔以手動方式叫用診斷外掛程式時才會發生。
-110|一般診斷組態錯誤。<p><p>這是內部錯誤，只有當使用無效的組態檔以手動方式叫用診斷外掛程式時才會發生。
-111|無法啟動監視代理程式。<p><p>解決方法：確認有足夠的系統資源可用。
-112|一般錯誤


## 診斷資料未記錄至 Azure 儲存體
Azure 診斷會將所有資料儲存至 Azure 儲存體。

遺漏事件資料的最常見原因是不正確地定義儲存體帳戶資訊。

解決方法：更正 Diagnostics 組態檔，並重新安裝 Diagnostics。如果在重新安裝診斷擴充功能後仍有問題，則您可能要進一步偵錯，做法為：仔細檢查監視代理程式的任何錯誤。將事件資料上傳至您的儲存體帳戶之前，資料是儲存在 LocalResourceDirectory 中。

針對雲端服務角色，LocalResourceDirectory 為：

	C:\Resources\Directory<CloudServiceDeploymentID>.<RoleName>.DiagnosticStore\WAD<DiagnosticsMajorandMinorVersion>\Tables

針對虛擬機器，LocalResourceDirectory 為：

	C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics<DiagnosticsVersion>\WAD<DiagnosticsMajorandMinorVersion>\Tables

如果 LocalResourceDirectory 資料夾中沒有檔案，則監視代理程式將無法啟動。這種情況通常是由於無效的組態檔所造成，即應在 CommandExecution.log 中回報的事件。

如果監視代理程式成功收集事件資料，您將看到組態檔中所定義之每個事件的 .tsf 檔案。監視代理程式會在 MaEventTable.tsf 檔案中記錄錯誤。若要檢查此檔案的內容，您可以使用 tabel2csv 應用程式，將 .tsf 檔案轉換為逗號分隔值 (.csv) 檔案：

在雲端服務角色上：

	%SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics<DiagnosticsVersion>\Monitor\x64\table2csv maeventtable.tsf

雲端服務角色上的 *%SystemDrive%* 通常為是 D：

在虛擬機器上：

	C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics<DiagnosticsVersion>\Monitor\x64\table2csv maeventtable.tsf

上述命令會產生記錄檔 *maeventtable.csv*，您可以開啟檔案並檢查失敗的訊息。


## 找不到診斷資料資料表
在內含 Azure 診斷資料的 Azure 儲存體中，資料表是以下列程式碼命名：

		if (String.IsNullOrEmpty(eventDestination)) {
		    if (e == "DefaultEvents")
		        tableName = "WADDefault" + MD5(provider);
		    else
		        tableName = "WADEvent" + MD5(provider) + eventId;
		}
		else
		    tableName = "WAD" + eventDestination;

下列是一個範例：

		<EtwEventSourceProviderConfiguration provider=”prov1”>
		  <Event id=”1” />
		  <Event id=”2” eventDestination=”dest1” />
		  <DefaultEvents />
		</EtwEventSourceProviderConfiguration>
		<EtwEventSourceProviderConfiguration provider=”prov2”>
		  <DefaultEvents eventDestination=”dest2” />
		</EtwEventSourceProviderConfiguration>

這會產生 4 個資料表：

事件|資料表名稱
---|---
provider=”prov1” &lt;Event id=”1” /&gt;|WADEvent+MD5(“prov1”)+”1”
provider=”prov1” &lt;Event id=”2” eventDestination=”dest1” /&gt;|WADdest1
provider=”prov1” &lt;DefaultEvents /&gt;|WADDefault+MD5(“prov1”)
provider=”prov2” &lt;DefaultEvents eventDestination=”dest2” /&gt;|WADdest2

<!---HONumber=AcomDC_0302_2016-------->