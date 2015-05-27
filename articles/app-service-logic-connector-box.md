<properties 
   pageTitle="在邏輯應用程式中使用 Box 連接器" 
   description="在邏輯應用程式中使用 Box 連接器" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="rajeshramabathiran" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="03/20/2015"
   ms.author="vagarw"/>

# **在邏輯應用程式中使用 Box 連接器**

 

邏輯應用程式可以根據各種資料來源和提供項目連接器觸發，以在流程中取得和處理資料。您有時候可能需要使用 Box，讓您與任何人安全地共用資料 – 即使這些人位於您的防火牆之外。

 

Box 組件庫應用程式會提供觸發程序和動作，以做為與 Box 互動的機制：

 

1. **在檔案可用時** **觸發**：如果您要在 Box 資料夾加入檔案時起始邏輯應用程式，請使用此選項。觸發程序會依設定的頻率檢查指定的 Box 資料夾，並且在指定的資料夾中有可用的檔案時觸發流程。此流程會傳回檔案的內容與屬性，而且在成功地傳遞至下一個邏輯應用程式步驟之後，檔案就會刪除。


	<table>
  <tr>
    <td><b>參數名稱</b></td>
    <td><b>說明</b></td>
    <td><b>必要</b></td>
  </tr>
  <tr>
    <td>檔案路徑</td>
    <td>檔案所在的資料夾路徑。</td>
    <td>是</td>
  </tr>
  <tr>
    <td>檔案類型</td>
    <td>指定檔案是文字或二進位檔。</td>
    <td>否</td>
  </tr>
  <tr>
    <td>頻率</td>
    <td>指定頻率類型，請從列出的類型中選取一個類型。它可以是下列其中一個：年、月、週、天、小時、分鐘或秒鐘</td>
    <td>是</td>
  </tr>
  <tr>
    <td>間隔</td>
    <td>指定頻率單位。</td>
    <td>是</td>
  </tr>
</table>


 

2. **動作**：動作可讓您在使用邏輯應用程式設定的 Box 帳戶上執行預先定義的動作。以下是可使用 Box 連接器在 Box 帳戶上執行的動作：

	a.*列出檔案：*這項作業會傳回資料夾中所有檔案的資訊。以下是動作所需的參數清單：

	<table>
  <tr>
    <td><b>參數名稱</b></td>
    <td><b>說明</b></td>
    <td><b>必要</b></td>
  </tr>
  <tr>
    <td>資料夾路徑</td>
    <td>清單所在的資料夾路徑。</td>
    <td>是</td>
  </tr>
</table>*注意：它不會傳回任何檔案內容。*

 

    b.*取得檔案：*這項作業會擷取檔案 (包括其內容和屬性)。以下是動作所需的參數清單：

	<table>
  <tr>
    <td><b>參數名稱</b></td>
    <td><b>說明</b></td>
    <td><b>必要</b></td>
  </tr>
  <tr>
    <td>檔案路徑</td>
    <td>檔案所在的資料夾路徑。</td>
    <td>是</td>
  </tr>
  <tr>
    <td>檔案類型</td>
    <td>指定檔案是文字或二進位檔。</td>
    <td>否</td>
  </tr>
</table>*注意：這項作業不會在讀取檔案後加以刪除。*

 

    c.上傳檔案：正如其名建議，動作將檔案上傳至 Box 帳戶。如果檔案已存在，則不會加以覆寫，而且會擲回錯誤。以下是動作所需的參數清單：

	<table>
  <tr>
    <td><b>參數名稱</b></td>
    <td><b>說明</b></td>
    <td><b>必要</b></td>
  </tr>
  <tr>
    <td>檔案路徑</td>
    <td>檔案的路徑。</td>
    <td>是</td>
  </tr>
  <tr>
    <td>檔案內容</td>
    <td>即將上傳的檔案內容。</td>
    <td>是</td>
  </tr>
  <tr>
    <td>內容轉移編碼</td>
    <td>內容的編碼類型，可以是 [Base64] 或 [無]。</td>
    <td> </td>
  </tr>
</table>d.刪除檔案：此動作會從資料夾中刪除指定的檔案。如果找不到檔案/資料夾，則會擲回例外狀況。以下是動作所需的參數清單：

 	<table>
  <tr>
    <td><b>參數名稱</b></td>
    <td><b>說明</b></td>
    <td><b>必要</b></td>
  </tr>
  <tr>
    <td>檔案路徑</td>
    <td>檔案所在的資料夾路徑。</td>
    <td>是</td>
  </tr>
</table>


 

## **建立邏輯應用程式的 Box 連接器** ##

若要使用 Box 連接器，您必須先建立 Box 連接器 API 應用程式的執行個體。如下所示，完成此作業：

1. 使用 Azure 入口網站右下方的 [+新增] 選項開啟 Azure Marketplace。

2. 瀏覽至 [Web 與行動] > [API 應用程式]，並搜尋 [Box 連接器]。

3. 設定 Box 連接器，然後按一下 [建立]：

	![][1]

4. 完成後，您現在即可在相同的資源群組中建立邏輯應用程式，以使用 Box 連接器。


## **在邏輯應用程式中使用 Box 連接器** ##

建立 API 應用程式之後，您現在可以使用 Box 連接器做為邏輯應用程式的觸發程序或動作 。若要這麼做，您需要：


1. 建立新的邏輯應用程式，並選擇具有 Box 連接器的相同資源群組。

2. 開啟 [觸發程序和動作] 以開啟邏輯應用程式設計工具，並設定您的流程。Box 連接器就會出現在右邊組件庫的 [最近使用] 區段中。選取該項目。

3. 如果在邏輯應用程式的開頭選取 Box 連接器，它的作用就像在使用此連接器的 Box 帳戶上採取的其他觸發程序動作。

4. 第一個步驟是驗證及授權邏輯應用程式，以代表您執行作業。若要開始授權，請按一下 Box 連接器上的 [授權]。

	![][2]

5. 按一下 [授權] 會開啟 Box 的驗證對話方塊。提供您要執行作業之 Box 帳戶的登入詳細資料。

	![][3]

6. 對您的帳戶授與邏輯應用程式存取權限，以便代表您執行作業。

	![][4]

7. 如果 Box 連接器已設定為 [觸發程序]，則觸發程序會顯示且其他動作的清單隨即顯示，而您可以選擇您想要執行的適當作業。

	![][5]


<!--Image references-->
[1]: ./media/app-service-logic-connector-box/image_0.jpg
[2]: ./media/app-service-logic-connector-box/image_1.jpg
[3]: ./media/app-service-logic-connector-box/image_2.jpg
[4]: ./media/app-service-logic-connector-box/image_3.jpg
[5]: ./media/app-service-logic-connector-box/image_4.jpg


<!--HONumber=54-->