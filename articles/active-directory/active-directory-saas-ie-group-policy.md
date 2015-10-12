<properties
   pageTitle="如何使用群組原則部署 Internet Explorer 的存取面板延伸模組 | Microsoft Azure"
   description="如何使用群組原則針對我的應用程式入口網站部署 Internet Explorer 附加元件。"
   services="active-directory"
   documentationCenter=""
   authors="liviodlc"
   manager="stevenpo"
   editor=""/>
<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/28/2015"
   ms.author="liviodlc"/>

#如何使用群組原則部署 Internet Explorer 的存取面板延伸模組

本教學課程示範如何使用群組原則，在您的使用者電腦上遠端安裝 Internet Explorer 的存取面板延伸模組。需要登入使用[密碼單一登入](active-directory-appssoaccess-whatis.md#password-based-single-sign-on)設定的應用程式的 Internet Explorer 使用者，都需要此延伸模組。

我們建議系統管理員自動化部署這個延伸模組。否則，使用者必須自行下載並安裝延伸模組，這樣很容易發生使用者錯誤，而且需要系統管理員權限。本教學課程涵蓋使用群組原則自動化軟體部署的一種方法。[深入了解群組原則。](https://technet.microsoft.com/windowsserver/bb310732.aspx)

存取面板延伸模組也可供 [Chrome](https://go.microsoft.com/fwLink/?LinkID=311859) 和 [Firefox](https://go.microsoft.com/fwLink/?LinkID=626998) 使用，兩者都不需要系統管理員權限即可安裝。

##必要條件

- 您已設定了 [Active Directory 網域服務](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx)，並且已將使用者的電腦加入網域。
- 您必須擁有 [編輯設定] 權限，才能編輯群組原則物件 (GPO)。根據預設，下列安全性群組的成員擁有此權限：網域系統管理員、企業系統管理員及群組原則建立者擁有者。[深入了解。](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx)

##步驟 1：建立發佈點

首先，您必須將安裝程式套件放在網路位置上，該位置可以從您要在上面遠端安裝延伸模組的所有電腦存取。若要這樣做，請遵循下列步驟：

1. 以系統管理員身分登入伺服器

2. 在 [伺服器管理員] 視窗中，移至 [檔案和存放服務]。

	![開啟檔案和存放服務](./media/active-directory-saas-ie-group-policy/files-services.png)

3. 移至 [共用] 索引標籤。然後按一下 [工作] > [新增共用...]

	![開啟檔案和存放服務](./media/active-directory-saas-ie-group-policy/shares.png)

4. 完成 [新增共用精靈] 並設定權限以確保可以從您的使用者電腦存取該精靈。[深入了解共用。](https://technet.microsoft.com/library/cc753175.aspx)

5. 下載下列 Microsoft Windows 安裝程式套件 (.msi file)：[Access Panel Extension.msi](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access Panel Extension.msi)

6. 將安裝程式套件複製到共用上想要的位置。

	![將 .msi 檔案複製到您的共用。](./media/active-directory-saas-ie-group-policy/copy-package.png)

8. 確認用戶端電腦能夠從共用存取安裝程式套件。

##步驟 2：建立群組原則物件

1. 登入裝載 Active Directory 網域服務 (AD DS) 安裝的伺服器。

2. 在 [伺服器管理員] 中，移至 [工具] > [群組原則管理]。

	![移至工具 > 群組原則管理](./media/active-directory-saas-ie-group-policy/tools-gpm.png)

3. 在 [群組原則管理] 視窗的左窗格中，檢視您的組織單位 (OU) 階層並決定您想要套用群組原則的範圍。例如，您可能決定針對測試挑選小型 OU 以部署到少數使用者，或者您可能會挑選最上層 OU 以部署到整個組織。

	> [AZURE.NOTE]如果您想要建立或編輯您的組織單位 (OU)，切換回 [伺服器管理員]，然後移至 [工具] > [Active Directory 使用者和電腦]。

4. 一旦您選取 OU，以滑鼠右鍵按一下它然後選取 [在這個網域中建立 GPO 並連結到...]。

	![建立新的 GPO](./media/active-directory-saas-ie-group-policy/create-gpo.png)

5. 在 [新增 GPO] 提示中，輸入新的群組原則物件的名稱。

	![命名新的 GPO](./media/active-directory-saas-ie-group-policy/name-gpo.png)

6. 以滑鼠右鍵按一下您剛才建立的群組原則物件，然後選取 [編輯]。

	![編輯新的 GPO](./media/active-directory-saas-ie-group-policy/edit-gpo.png)

##步驟 3：指派安裝套件

1. 決定您想要根據 [電腦設定] 或 [使用者設定] 部署延伸模組。當使用[電腦設定](https://technet.microsoft.com/library/cc736413%28v=ws.10%29.aspx)時，不論哪一個使用者登入電腦，都會在電腦上安裝延伸模組。相反地，使用[使用者設定](https://technet.microsoft.com/library/cc781953%28v=ws.10%29.aspx)時，無論使用者登入哪一台電腦，都會在該電腦上安裝延伸模組。

2. 在 [群組原則管理編輯器] 視窗的左窗格中，移至下列其中一個資料夾路徑，根據您選擇的設定類型而定：
	- `Computer Configuration/Policies/Software Settings/`
	- `User Configuration/Policies/Software Settings/`

3. 以滑鼠右鍵按一下 [軟體安裝]，然後選取 [新增] > [套件...]

	![建立新的軟體安裝套件](./media/active-directory-saas-ie-group-policy/new-package.png)

4. 移至共用資料夾，該資料夾包含[步驟 1：建立發佈點](#step-1-create-the-distribution-point)的安裝程式套件，選取 .msi 檔案，然後按一下 [開啟]。

	> [AZURE.IMPORTANT]如果共用位於相同的伺服器上，確認您是透過網路檔案路徑存取此 .msi，，而不是本機檔案路徑。

	![從共用資料夾中選取安裝套件。](./media/active-directory-saas-ie-group-policy/select-package.png)

5. 在 [部署軟體] 提示中，針對您的部署方法選取 [已指派]。然後按一下 [確定]。

	![選取 [已指派]，然後按一下 [確定]。](./media/active-directory-saas-ie-group-policy/deployment-method.png)

延伸模組現在已部署至您所選取的 OU。[深入了解群組原則軟體安裝。](https://technet.microsoft.com/library/cc738858%28v=ws.10%29.aspx)

##步驟 4：自動啟用 Internet Explorer 的延伸模組 

除了執行安裝程式之外，Internet Explorer 的每個延伸模組必須明確啟用才能使用。遵循下列步驟以使用群組原則啟用存取面板延伸模組：

1. 在 [群組原則管理編輯器] 視窗中，移至下列其中一個路徑，根據您在[步驟 3：指派安裝套件](#step-3-assign-the-installation-package)中選擇的設定類型而定：
	- `Computer Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`
	- `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`

2. 以滑鼠右鍵按一下 [附加元件清單]，然後選取 [編輯]。![編輯附加元件清單。](./media/active-directory-saas-ie-group-policy/edit-add-on-list.png)

3. 在 [附加元件清單] 視窗中，選取 [已啟用]。然後，在 [選項] 區段中，按一下 [顯示...]。

	![按一下 [啟用]，然後按一下 [顯示...]](./media/active-directory-saas-ie-group-policy/edit-add-on-list-window.png)

4. 在 [顯示內容] 視窗中，執行下列步驟：

	1. 對於第一個資料行 ([值名稱] 欄位)，複製和貼上以下類別識別碼：`{030E9A3F-7B18-4122-9A60-B87235E4F59E}`

	2. 對於第二個資料行 ([值] 欄位)，輸入下列值：`1`

	3. 按一下 [確定] 關閉 [顯示內容] 視窗。

	![填寫值，如上述步驟所指定。](./media/active-directory-saas-ie-group-policy/show-contents.png)

5. 按一下 [確定] 以套用變更並關閉 [附加元件清單] 視窗。

延伸模組現在應該已在所選 OU 中的機器啟用。[深入了解使用群組原則啟用或停用 Internet Explorer 附加元件。](https://technet.microsoft.com/library/dn454941.aspx)

##步驟 5：測試部署

請遵循下列步驟以確認延伸模組是否成功部署：

1. 如果您部署使用 [電腦設定]，請登入屬於您在[步驟 2：建立群組原則物件](#step-2-create-the-group-policy-object)中選取的 OU 的用戶端電腦。如果您部署使用 [使用者設定]，請務必以屬於該 OU 的使用者身分登入。

2. 可能要登入好幾次才能讓群組原則變更完全更新至此電腦。若要強制更新，開啟 [命令提示字元] 視窗，然後執行下列命令：`gpupdate /force`

3. 您必須重新啟動電腦才能進行安裝。安裝延伸模組時，開機可能需要比平常更多的時間。

4. 重新開機之後，開啟 **Internet Explorer**。在視窗的右上角按一下 [工具] (齒輪圖示)，然後選取 [管理附加元件]。

	![移至工具 > 管理附加元件](./media/active-directory-saas-ie-group-policy/manage-add-ons.png)

5. 在 [管理附加元件] 視窗中，確認 [存取面板延伸模組] 已安裝且其 [狀態] 已設為 [已啟用]。

	![確認存取面板延伸模組已安裝並啟用。](./media/active-directory-saas-ie-group-policy/verify-install.png)

[AZURE.INCLUDE [saas-toc](../../includes/active-directory-saas-toc.md)]

<!---HONumber=Oct15_HO1-->