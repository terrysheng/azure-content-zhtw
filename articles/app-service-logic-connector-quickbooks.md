<properties 
   pageTitle="QuickBooks 連接器" 
   description="如何使用 QuickBooks 連接器" 
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
   ms.date="04/01/2015"
   ms.author="vagarw"/>


# 在邏輯應用程式中使用 QuickBooks 連接器#

邏輯應用程式可以根據各種資料來源觸發，並提供連接器以取得及處理屬於流程一部分的資料。QuickBooks 連接器可讓您建立和修改不同的 QuickBooks 實體。以下清單是 QuickBooks 連接器支援的 QuickBooks 實體。

<Table>
<TR><TD><B>實體</TD><TD><B>說明</TR>
<TR>	<TD>	Account	</TD>	<TD>	Account 是組成 Chart Of Accounts 的元件，而且也是 Ledger 的一部分。用來記錄配置給特定用途的貨幣總數	</TD>	</TR>
<TR>	<TD>	CreditMemo	</TD>	<TD>	CreditMemo 是代表退貨或信用卡付款，或已售出的商品或服務之部分付款的財務交易。	</TD>	</TR>
<TR>	<TD>	Customer	</TD>	<TD>	Customer 是您公司提供服務或產品的客戶。	</TD>	</TR>
<TR>	<TD>	Estimate	</TD>	<TD>	Estimate 代表公司對客戶提議之販售商品或服務的商業交易 (包括提議的售價)。	</TD>	</TR>
<TR>	<TD>	Invoice	</TD>	<TD>	Invoice 代表客戶之後必須針對產品或服務付款的銷售表單。Invoice 資料模型的其他資訊可參考此處。	</TD>	</TR>
<TR>	<TD>	Item	</TD>	<TD>	Item 是您公司購買、銷售，或重新銷售的項目，例如產品、運送與處理費用、折扣及營業稅 (如果適用)。Item 會在發票或其他銷售表單上列為一行。	</TD>	</TR>
<TR>	<TD>	SalesReceipt	</TD>	<TD>	此實體代表提供給客戶的銷售收據。	</TD>	</TR>
</Table>


## QuickBooks 動作 ##
以下是 QuickBooks 連接器中可使用的不同動作。
	<table>
	<tbody>
		<tr><td>
		<strong>動作</strong>
		</td>
		<td>
		<strong>說明</strong>
		</td>
		</tr>
		<tr>
		<td>
		讀取實體
		</td>
		<td>
		讀取實體物件。
		</td>
		</tr>
		<tr>
		<td>
		建立或更新實體
		</td>
		<td>
		建立或更新實體物件。如果物件已存在會更新物件，若不存在則會建立新物件。
		</td>
		</tr>
		<tr>
		<td>
		刪除
		</td>
		<td>
		此動作會從選取的實體刪除指定的物件。
		</td>
		</tr>
		<tr>	
		<td>
		查詢
		</td>
		<td>
		查詢作業是針對實體建立引導示查詢的方法。
		</td>
		</tr>
	</tbody>
	</table>

## 建立 QuickBooks 連接器 API 應用程式##
1.	使用 Azure 入口網站右下方的 [+新增] 選項開啟 Azure Marketplace。
2.	瀏覽至 [Web 與行動] > [API 應用程式]，並搜尋「QuickBooks 連接器」。
3.	設定 QuickBooks 連接器，提供「主控方案」及資源群組的詳細資料，並選取 API 應用程式的名稱。

	![][13]
4. 設定 '套件設定' 中，您想要讀取/寫入的 QuickBooks 實體。

完成上述步驟後，您現在即可建立 QuickBooks 連接器 API 應用程式。


## 建立邏輯應用程式##
讓我們建立一個簡單的邏輯應用程式，它會在 QuickBooks 建立帳戶，並更新同一個帳戶的 '類別類型'。

1.	登入 Azure 入口網站，並按一下 '新增 -> Web + 行動 -> 邏輯應用程式'

	![][1]

2.	在 '建立邏輯應用程式' 頁面中，提供必要的詳細資料例如名稱、應用程式服務方案及位置。

	![][2]

3.	按一下 '觸發程序及動作'，邏輯應用程式編輯器畫面便會隨即出現。

	![][3]

4.	選取 '手動執行此邏輯'，這表示僅能夠以手動方式叫用此邏輯應用程式。


5.	展開程式庫中 '此資源群組中的 API 應用程式' 來查看可用的 API 應用程式。從程式庫選取 'QuickBooks 連接器'，然後 'QuickBooks 連接器' 就會加入流程。


6.	如果 QuickBooks 在線上，您必須驗證和授權邏輯應用程式以代表您執行作業。若要開始授權，請按一下 QuickBooks 連接器上的 [授權]。

	![][4]

7.	按一下 [授權] 會開啟 QuickBooks 的驗證對話方塊。提供您要執行作業之 QuickBooks 帳戶的登入詳細資料。

	![][5]

8. 按一下同意對話方塊中的 [授權]，來授與邏輯應用程式存取您帳戶的權限，以代表您執行作業。

	![][6]

9.	授權完成後，即會顯示所有的動作。

	![][7]

10.	選取 '建立或更新帳戶' 動作便會顯示輸入參數。

	![][8]

11.	提供 '名稱' 和 '帳戶類型'，然後按一下 ✓。 

	![][9]

12.	從組件庫的 '最近使用的' 區段中選取 'QuickBooks 連接器'，即會新增新的 QuickBooks 動作。

13.	從動作清單中選取 '建立或更新帳戶' 便會顯示動作的輸入參數。

	![][10]

14.	按一下 '識別碼' 旁邊的 '+'，並從 [建立帳戶] 動作的輸出中挑選識別碼值。 

	![][11]

15.	提供帳戶類型新的值，然後按一下 ✓。

	![][12]

16. 按一下邏輯應用程式編輯器上的 [確定]，然後按一下 '建立'。完成建立大約需要 30 秒的時間。

17. 瀏覽剛建立的邏輯應用程式，然後按一下 '執行' 以啟動該應用程式。

18. 您可以檢查 QuickBooks 帳戶中是否已建立名為 'Contoso' 的新帳戶。

<!--Image references-->
[1]: ./media/app-service-logic-connector-quickbooks/1_New_Logic_App.png
[2]: ./media/app-service-logic-connector-quickbooks/2_Logic_App_Settings.png
[3]: ./media/app-service-logic-connector-quickbooks/3_Logic_App_Editor.png
[4]: ./media/app-service-logic-connector-quickbooks/4_QuickBooks_Authorize.png
[5]: ./media/app-service-logic-connector-quickbooks/5_QuickBooks_Login.png
[6]: ./media/app-service-logic-connector-quickbooks/6_QuickBooks_User_Consent.png
[7]: ./media/app-service-logic-connector-quickbooks/7_QuickBooks_Actions.png
[8]: ./media/app-service-logic-connector-quickbooks/8_QuickBooks_Create_Account.png
[9]: ./media/app-service-logic-connector-quickbooks/9_Create_Account_OK.png
[10]: ./media/app-service-logic-connector-quickbooks/10_QuickBooks_Update_Account.png
[11]: ./media/app-service-logic-connector-quickbooks/11_Record_ID_from_Create.png
[12]: ./media/app-service-logic-connector-quickbooks/12_Update_Account_Address.png
[13]: ./media/app-service-logic-connector-quickbooks/13_Create_new_quickbooks_connector.png



<!--HONumber=52-->