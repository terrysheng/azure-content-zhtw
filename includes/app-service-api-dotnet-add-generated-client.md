3. 在 [**方案總管**] 中，以滑鼠右鍵按一下專案 (而非方案)，並依序選取 [**加入 > Azure API 應用程式用戶端**]。 

	![](./media/app-service-api-dotnet-add-generated-client/03-add-azure-api-client-v3.png)
	
3. 在 [**加入 Azure API 應用程式用戶端**] 對話方塊中，按一下 [**從 Azure API 應用程式下載**]。

5. 在下拉式清單中，選取要呼叫的 API 應用程式。

7. 按一下 [確定]。

	![產生畫面](./media/app-service-api-dotnet-add-generated-client/04-select-the-api-v3.png)

	精靈下載 API 中繼資料檔案，並產生具型別的介面來呼叫 API 應用程式。

	![產生中](./media/app-service-api-dotnet-add-generated-client/05-metadata-downloading-v3.png)

	程式碼產生完成之後，您會在 [**方案總管**] 中看到該 API 應用程式名稱的新資料夾。此資料夾包含實作用戶端類別和資料模型的程式碼。

	![產生完成](./media/app-service-api-dotnet-add-generated-client/06-code-gen-output-v3.png)

<!---HONumber=Oct15_HO3-->