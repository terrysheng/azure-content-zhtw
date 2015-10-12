<properties
   pageTitle="在 Logic Apps 中使用 SugarCRM 連接器 | Microsoft Azure App Service"
   description="如何建立並設定 SugarCRM 連接器或 API 應用程式，並在 Azure App Service 的邏輯應用程式中使用它"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="anuragdalmia"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="08/23/2015"
   ms.author="sameerch"/>


# 開始使用 SugarCRM 連接器並將它加入您的邏輯應用程式
SugarCRM 連接器可讓您建立和修改不同的實體，例如帳戶、潛在客戶、連絡人等。以下是涉及 SugarCRM 的典型整合案例：

- 在 SugarCRM 和 ERP 系統 (例如 SAP) 間同步帳戶
- 在 Marketo 和 SugarCRM 間同步帳戶、連絡人和潛在客戶
- 從 SugarCRM 到 ERP 系統的「下單至收款」流程

屬於連接器封裝設定的一部分，您可以選擇連接器可以管理的實體，而系統便會以動態方式填入動作、輸入和輸出參數。

邏輯應用程式可以根據各種資料來源觸發，並提供連接器以取得及處理屬於流程一部分的資料。您可以將 SugarCRM 連接器加入您的商務工作流程，就能在邏輯應用程式的該工作流程中處理資料。



## SugarCRM 連接器動作
以下是 SugarCRM 連接器中可使用的不同動作：

- 建立模組 - 使用此動作建立 SugarCRM 模組的新記錄，例如帳戶、潛在客戶、連絡人。

- 更新模組 - 使用此動作更新 SugarCRM 模組的現有記錄。

- 刪除模組 - 使用此動作刪除 SugarCRM 模組的現有記錄。

- 清單模組 - 使用此動作列出篩選的記錄。若未指定查詢將傳回所有記錄。

- 取得模組 - 使用此動作抓取指定模組中的單一記錄。

- 取得記錄計數 - 使用此動作取得模組中符合查詢的記錄之數目。若未指定查詢將傳回模組中所有記錄的數目。

- 檢查重複模組 - 使用此動作檢查模組中重複的記錄。

*注意*：如需查詢中支援的引數之詳細資訊，請參閱 [SugarCRM REST API](https://msdn.microsoft.com/library/dn705870) 文件。

## 建立 SugarCRM 連接器 API 應用程式
1.	瀏覽至 portal.azure.com。使用 Azure 入口網站左上角的 [+新增] 選項開啟 Azure Marketplace。
2.	瀏覽至 [Marketplace] > [所有項目]，然後搜尋 "SugarCRM"。
3.	提供 App Service 方案、資源群組的詳細資料，並選取 API 應用程式的名稱，設定 SugarCRM 連接器。
4. 設定 SugarCRM 連接器封裝設定。下列是建立連接器所需提供的套件設定：

	名稱 | 必要 | 說明
--- | --- | ---
網站 URL | 是 | 輸入 SugarCRM 執行個體的 URL。例如，輸入 https://abcde1234.sugarcrm.com。
用戶端識別碼 | 是 | 輸入 SugarCRM 中 OAUTH 2.0 金鑰的取用者金鑰。 
用戶端密碼 | 是 | 輸入 OAUTH 的取用者密碼。
使用者名稱 | 是 | 輸入 SugarCRM 使用者的使用者名稱。
密碼 | 是 | 輸入 SugarCRM 使用者的密碼
模組名稱 | 是 | 輸入您要執行作業的 SugarCRM 模組 (例如帳戶、連絡人、產品)。<br><br>例如︰帳戶、潛在客戶、連絡人  
  
![][9]



## 建立邏輯應用程式
讓我們建立一個簡單的邏輯應用程式，它會在 SugarCRM 建立帳戶，並更新相同帳戶的帳單寄送地址之詳細資料。

1.	登入 Azure 入口網站，並按一下 [新增] -> [Web + 行動] -> [邏輯應用程式]：  
	![][1]

2.	在 [建立邏輯應用程式] 頁面中，請提供必要的詳細資料，例如名稱、應用程式服務方案和位置：  
	![][2]

3.	按一下 [觸發程序及動作]，邏輯應用程式編輯器畫面便會隨即出現。選取 [手動執行此邏輯]，這表示僅能夠以手動方式叫用此邏輯應用程式。

4.	展開資源庫中的 [此資源群組中的 API 應用程式] 來查看所有可用的 API 應用程式。從資源庫選取 SugarCRM，然後 SugarCRM 連接器就會加入流程：  
	![][3]

5.	選取 [建立帳戶] 動作，而輸入參數便會隨即顯示：  
	![][4]

6.	提供名稱為「Microsoft 帳戶」，然後按一下 ✓：  
	![][5]

7.	從資源庫的 [最近使用的] 區段中選取[ SugarCRM 連接器]，即會新增新的 SugarCRM 動作。

8.	從動作清單中選取 更新帳戶 (在進階動作 [...] 之下)，就會顯示 [更新帳戶] 動作的輸入參數：  
	![][6]

9.	按一下 [記錄識別碼] 旁邊的 [...]，並從 [建立帳戶] 動作的輸出中挑選 [識別碼] 值：  
	![][7]

10.	提供帳單寄送地址的值，然後按一下 ✓：  
	![][8]

11. 按一下邏輯應用程式編輯器畫面上的 [確定]，然後按一下 [建立]。完成建立大約需要 30 秒的時間。

12. 瀏覽剛建立的邏輯應用程式，然後按一下 [立即執行] 以啟動該應用程式。

13. 您可以查看名為「Microsoft 帳戶」的新帳戶已建立於您的 SugarCRM 帳戶，且同一個帳戶也跟新了帳單寄送地址資訊。

## 進一步運用您的連接器
現在已建立連接器，您可以將它加入到使用邏輯應用程式的商務工作流程。請參閱[什麼是邏輯應用程式？](app-service-logic-what-are-logic-apps.md)。

>[AZURE.NOTE]如果您想在註冊 Azure 帳戶前開始使用 Azure Logic Apps，請移至[試用 Logic App](https://tryappservice.azure.com/?appservice=logic)，即可在 App Service 中立即建立短期入門邏輯應用程式。不需要信用卡，無需承諾。

檢視位於[連接器和 API Apps 參考](http://go.microsoft.com/fwlink/p/?LinkId=529766)的 Swagger REST API 參考。

您也可以檢閱連接器的效能統計資料及控制安全性。請參閱[管理和監視內建 API Apps 和連接器](app-service-logic-monitor-your-connectors.md)。

<!--Image references-->
[1]: ./media/app-service-logic-connector-sugarcrm/1_New_Logic_App.png
[2]: ./media/app-service-logic-connector-sugarcrm/2_Logic_App_Settings.png
[3]: ./media/app-service-logic-connector-sugarcrm/3_Select_SugarCRM_Gallery.png
[4]: ./media/app-service-logic-connector-sugarcrm/4_SugarCRM_Create_Account.png
[5]: ./media/app-service-logic-connector-sugarcrm/5_Create_Account_OK.png
[6]: ./media/app-service-logic-connector-sugarcrm/6_SugarCRM_Update_Account.png
[7]: ./media/app-service-logic-connector-sugarcrm/7_Record_ID_from_Create.png
[8]: ./media/app-service-logic-connector-sugarcrm/8_Update_Account_Address.png
[9]: ./media/app-service-logic-connector-sugarcrm/9_Create_new_SugarCRM_connector.png

<!---HONumber=Oct15_HO1-->