<properties
	pageTitle="在 Logic Apps 中使用 SFTP 連接器 | Microsoft Azure App Service"
	description="如何建立並設定 SFTP 連接器或 API 應用程式，並在 Azure App Service 的邏輯應用程式中使用它"
	authors="anuragdalmia"
	manager="dwrede"
	editor=""
	services="app-service\logic"
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/23/2015"
	ms.author="sameerch"/>

# 開始使用 SFTP 連接器並將它加入您的邏輯應用程式
使用 SFTP 連接器可從 SFTP 伺服器移入/移出資料。它可以讓您下載、上傳檔案，或列出檔案清單 (從/到 SFTP 伺服器)。

邏輯應用程式可以根據各種資料來源觸發，並提供連接器以取得及處理屬於流程一部分的資料。您可以將 SFTP 連接器加入您的商務工作流程，就能在邏輯應用程式的該工作流程中處理資料。

## 建立邏輯應用程式的 SFTP 連接器 ##
連接器可以在邏輯應用程式內建立，或直接從 Azure Marketplace 建立。從 Marketplace 建立連接器：

1. 在 Azure 開始面板中，選取 [**Marketplace**]。
2. 搜尋「SFTP 連接器」，將其選取，然後選取 [建立]。
3. 設定 SFTP 連接器，如下所示：  
![][1]
	- **位置** - 選擇您要部署連接器的地理位置
	- **訂閱** - 選擇您要建立此連接器的訂閱
	- **資源群組** - 選取或建立連接器所在的資源群組
	- **Web 裝載方**案 - 選取或建立 Web 裝載方案
	- **定價層** - 選擇連接器的定價層
	- **名稱** - 提供 SFTP 連接器的名稱
	- **套裝設定**
		- **伺服器位址** - 指定 SFTP 伺服器名稱或 IP 位址
		- **接受任何 SSH 伺服器 HostKey** - 決定是否接受任何來自伺服器的 SSH 公開主機金鑰指紋。若設定為 false，主機金鑰會與「SSH 伺服器主機金鑰指紋」屬性所指定的金鑰比對。
		- **SSH 伺服器 HostKey** - 指定 SSH 伺服器的公開主機金鑰指紋 - *選擇性*。
		- **根資料夾** - 指定根資料夾路徑。如果空白，會預設為根。
		- **加密編碼器** - 指定加密編碼器 - *選擇性*。
		- **伺服器連接埠** - 指定 SFTP 伺服器連接埠號碼
4. 按一下 [建立]。將建立新的 SFTP 連接器。

5. 透過 [瀏覽] -> [API 應用程式] -> [<Name of the API App just created>] 瀏覽至剛建立的 API 應用程式，可以看到 [安全性] 元件尚未設定： 
![][2]
6. 按一下 [安全性] 元件來設定 SFTP 連接器的安全性 (使用者名稱、密碼、私密金鑰、PPK 檔案密碼)。選取安全性之下的 [密碼]、[私密金鑰]、[多因素] 授權索引標籤，並提供必要的屬性：  
![][3]  
![][4]  
![][5]  
6. 儲存安全性組態之後，您可以在相同的資源群組中建立邏輯應用程式，以便使用 SFTP 連接器。

## 在邏輯應用程式中使用 SFTP 連接器 ##
建立 API 應用程式之後，您現在可以使用 SFTP 連接器做為邏輯應用程式的觸發程序/動作。若要這樣做，您需要：

1.	建立新的邏輯應用程式，並選擇具有 SFTP 連接器的相同資源群組：  
![][6]
2.	開啟 [觸發程序和動作] 以開啟 Logic Apps 設計工具，並設定您的流程：  
![][7]
3.	SFTP 連接器就會出現在右側資源庫中的 [此資源群組中的 API Apps] 區段：  
![][8]
4.	您可以在 [SFTP 連接器] 上按一下來將 SFTP 連接器 API 應用程式置入編輯器。

5.	您現在便可以在流程中使用 SFTP 連接器。您可以在流程的其他動作中使用從 SFTP 觸發程序 ("TriggerOnFileAvailable") 所擷取的「檔案」。

	> [AZURE.IMPORTANT]SFTP 觸發程序 "TriggerOnFileAvailable" 會在處理檔案之後刪除擷取的檔案。

6.	設定 SFTP 觸發程序的輸入屬性，如下所示：

	- **資料夾路徑** - 指定需從中擷取檔案之資料夾的路徑。
	- **檔案類型：文字或二進位** - 選取檔案的類型。
	- **檔案遮罩** - 指定要套用以擷取檔案的檔案遮罩。' *' 會擷取指定之資料夾中的所有檔案。
	- **排除檔案遮罩** - 指定要套用以排除檔案的檔案遮罩。如果也有設定「檔案遮罩」屬性，會先套用「排除檔案遮罩」。


	![][9]  
	![][10]

7.	您可以採用類似方式在流程中使用 SFTP 動作。您可以使用「上傳檔案」動作將檔案上傳至 SFTP 伺服器。設定 [上傳檔案] 的輸入屬性，如下所示：

	- **內容** - 指定要上傳之檔案的內容
	- **內容轉移編碼** - 指定 [無] 或 [base64]
	- **檔案路徑** - 指定要上傳之檔案的檔案路徑
	- **覆寫** - 指定 "true" 在檔案已存在時將其覆寫
	- ****存在時附加** - 指定 "true" 或 "false"。設定為 "true" 時，若檔案存在則資料會附加至檔案。設定為 [false] 時，若檔案存在則會覆寫檔案。
	- **暫存資料夾** - 若提供，則配接器會將檔案上傳至「暫存資料夾路徑」，上傳完成後會將檔案移至「資料夾路徑」。「暫存資料夾路徑」應和「資料夾路徑」位在相同的實體磁碟上，以確保移動作業自動進行。只在啟用 [存在時附加] 屬性，才能使用暫存資料夾。

	![][11]  
	![][12]

## 進一步運用您的連接器
現在已建立連接器，您可以將它加入到使用邏輯應用程式的商務工作流程。請參閱[什麼是邏輯應用程式？](app-service-logic-what-are-logic-apps.md)。

檢視位於[連接器和 API Apps 參考](http://go.microsoft.com/fwlink/p/?LinkId=529766)的 Swagger REST API 參考。

您也可以檢閱連接器的效能統計資料及控制安全性。請參閱[管理和監視內建 API Apps 和連接器](app-service-logic-monitor-your-connectors.md)。


<!-- Image reference -->
[1]: ./media/app-service-logic-connector-sftp/img1.PNG
[2]: ./media/app-service-logic-connector-sftp/img2.PNG
[3]: ./media/app-service-logic-connector-sftp/img3.PNG
[4]: ./media/app-service-logic-connector-sftp/img4.PNG
[5]: ./media/app-service-logic-connector-sftp/img5.PNG
[6]: ./media/app-service-logic-connector-sftp/img6.PNG
[7]: ./media/app-service-logic-connector-sftp/img7.png
[8]: ./media/app-service-logic-connector-sftp/img8.png
[9]: ./media/app-service-logic-connector-sftp/img9.PNG
[10]: ./media/app-service-logic-connector-sftp/img10.PNG
[11]: ./media/app-service-logic-connector-sftp/img11.PNG
[12]: ./media/app-service-logic-connector-sftp/img12.PNG

<!-----HONumber=August15_HO9-->