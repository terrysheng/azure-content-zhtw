大部分的時間驗證錯誤都是因為不正確或不一致的組態設定所造成。以下是一些需檢查之項目的特定建議。

* 確定您在任何地方都沒有遺漏 [儲存] 按鈕。這通常很容易實行，結果是您會在入口網站頁面上看見正確的值，但是尚未將它們實際儲存在 Azure 環境或 Azure AD 應用程式中。
* 針對在 Azure 入口網站的 [應用程式設定] 刀鋒視窗中所做的設定，請確定在輸入設定值時所選取的 API 應用程式或 Web 應用程式正確無誤。另外請確定設定輸入為 [應用程式設定] 而非 [連接字串]，因為這兩個區段的格式類似。
* 若要驗證 JavaScript 前端，請重新下載資訊清單，確認 `oauth2AllowImplicitFlow` 已順利變更為 `true`。
* 確認您在設定 URL 的位置都是使用 HTTPS：

	* 在專案程式碼中
	* 在 CORS 中
	* 在每個 API 應用程式和 Web 應用程式的 Azure 環境應用程式設定中
	* 在 Azure AD 應用程式設定中。
	
	請注意，如果您從入口網站複製 API 應用程式的 URL，它通常會有 `http://`，您必須手動將它變更為 `https://`。

* 請確定已成功部署任何程式碼變更。例如，在多專案方案中有可能變更專案的程式碼，而在想要部署變更時不小心選擇其中一個其他變更。
* 請確定您在瀏覽器中是移至 HTTPS URL，而非 HTTP URL。根據預設，Visual Studio 會利用 HTTP URL 建立發佈設定檔，而那會是您部署專案之後在瀏覽器中開啟的項目。
* 若要驗證 JavaScript 前端，請確定 JavaScript 程式碼呼叫的 API 應用程式上已正確設定 CORS。如果不確定問題是否與 CORS 有關，請嘗試以 "*" 做為允許的原始 URL。
* 針對 JavaScript 前端，請開啟您瀏覽器的 [開發人員工具主控台] 索引標籤以取得詳細的錯誤訊息，並檢查網路上的 HTTP 要求。不過，主控台錯誤訊息可能會產生誤導。如果您收到一則訊息指出 CORS 錯誤時，真正的問題可能是驗證。您可以執行應用程式並暫時停用驗證，檢查是否是這種情況。
* 針對 .NET API 應用程式，請將 [customErrors 模式設定為 [關閉]](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remoteview)以確保您會在錯誤訊息中取得最詳盡的資訊。
* 針對 .NET API 應用程式，啟動[遠端偵錯工作階段](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug)，並檢查傳遞至使用 ADAL 來取得持有人權杖之程式碼，或傳遞至針對預期的服務主體識別碼檢查宣告之程式碼的變數值。請注意，程式碼可以從許多不同的來源取得設定值，因此透過這種方式可能可以找到意外結果。例如，如果您在設定 Azure App Service 環境設定時將 `ida:ClientId` 拼錯為 `ida:ClientID`，程式碼可能忽略 Azure App Service 設定的值，從 Web.config 檔案取得 `ida:ClientId` 值。 
* 如果無法在正常的 Internet Explorer 視窗中運作，現有的登入可能會產生干擾；請嘗試 InPrivate 並嘗試使用 Chrome 或 Firefox。

<!---HONumber=AcomDC_0309_2016-->