<properties 
	pageTitle="IIS 驗證與 Azure Multi-Factor Authentication Server"
	description="此 Azure Multi-Factor Authentication 頁面協助您部署 IIS 驗證與 Azure Multi-Factor Authentication Server。"
	services="multi-factor-authentication"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags 
	ms.service="multi-factor-authentication"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/24/2015"
	ms.author="billmath"/>

# IIS 驗證

Azure Multi-Factor Authentication Server 的 [IIS 驗證] 區段可讓您啟用及設定 IIS 驗證，以便與 Microsoft IIS Web 應用程式整合。Azure Multi-Factor Authentication Server 會安裝一個外掛程式，可以篩選對 IIS Web 伺服器提出的要求，以新增 Azure Multi-Factor Authentication。IIS 外掛程式支援表單架構驗證和整合式 Windows HTTP 驗證。信任的 IP 也可以設定為將內部 IP 位址免除雙因素驗證。


![IIS 驗證](./media/multi-factor-authentication-get-started-server-iis/iis.png)


## 搭配 Azure Multi-Factor Authentication Server 使用表單架構 IIS 驗證

為了保護使用表單架構驗證的 IIS Web 應用程式，請在 IIS Web 伺服器上安裝 Azure Multi-Factor Authentication Server，並依照下列程序設定伺服器。

1. 在 Azure Multi-Factor Authentication Server 內，按一下左功能表中的 [IIS 驗證] 圖示。
2. 按一下 [表單架構] 索引標籤。
3. 按一下 [新增...] 按鈕。
4. 若要自動偵測使用者名稱、密碼和網域變數，請在 [自動設定表單架構網站] 對話方塊中輸入 [登入 URL] (例如 https://localhost/contoso/auth/login.aspx)，然後按一下 [確定]。
5. 如果所有使用者都已經或將要匯入到「伺服器」，且必須接受多因素驗證，請核取 [需要進行 Multi-Factor Authentication 使用者比對] 方塊。如果有大量使用者尚未匯入伺服器及/或將免除多重要素驗證，請勿核取此方塊。
6. 如果無法自動偵測頁面變數，請按一下 [自動設定表單架構網站] 對話方塊中的 [手動指定...] 按鈕。
7. 在 [新增表單架構網站] 對話方塊中，於 [提交 URL] 欄位中輸入登入頁面的 URL，然後輸入 [應用程式名稱] (選擇性)。應用程式名稱會出現在 Azure Multi-Factor Authentication 報表中，而且可能顯示在簡訊或行動應用程式驗證訊息內。如需 [提交 URL] 的詳細資訊，請參閱說明檔。 
8. 選取正確的 [要求格式]。對於大多數 Web 應用程式，這都設定為 [POST 或 GET]。
9. 輸入 [使用者名稱變數]、[密碼變數] 和 [網域變數] (如果出現在登入頁面上)。您可能需要在網頁瀏覽器中瀏覽至登入頁面，然後在頁面上按一下滑鼠右鍵並選取 [檢視來源]，以尋找頁面內的輸入方塊名稱。
10. 如果所有使用者都已或將要匯入伺服器並進行 Multi-Factor Authentication，請核取 [需要進行 Azure Multi-Factor Authentication 使用者比對] 方塊。如果有大量使用者尚未匯入伺服器及/或將免除多重要素驗證，請勿核取此方塊。如需此功能的其他資訊，請參閱說明檔。
11.  按一下 [進階...] 按鈕以檢閱進階設定，包括能夠選取自訂拒絕頁面檔案、使用 Cookie 將網站的成功驗證快取一段時間，以及選擇是否向 Windows 網域、LDAP 目錄或 RADIUS 伺服器驗證主要認證。完成時，按一下 [確定] 按鈕以返回 [新增表單架構網站] 對話方塊。如需進階設定的詳細資訊，請參閱說明檔。
12. 按一下 [確定] 按鈕。
13. 偵測到或已輸入 URL 和頁面變數後，網站資料就會顯示在表單架構面板中。
14. 請參閱下方的＜啟用 Azure Multi-factor Authentication Server 的 IIS 外掛程式＞小節，以完成 IIS 驗證組態。 

## 搭配 Azure Multi-Factor Authentication Server 使用整合式 Windows 驗證

為了保護使用整合式 Windows HTTP 驗證的 IIS Web 應用程式，請在 IIS Web 伺服器上安裝 Azure Multi-Factor Authentication Server，並依照下列程序設定伺服器。

1. 在 Azure Multi-Factor Authentication Server 內，按一下左功能表中的 [IIS 驗證] 圖示。
2. 按一下 [HTTP] 索引標籤。按一下 [表單架構] 索引標籤。
3. 按一下 [新增...] 按鈕。
4. 在 [新增基底 URL] 對話方塊中，於 [基底 URL] 欄位中輸入執行 HTTP 驗證之網站的 URL (例如 http://localhost/owa)，然後輸入 [應用程式名稱] (選擇性)。應用程式名稱會出現在 Azure Multi-Factor Authentication 報表中，而且可能顯示在簡訊或行動應用程式驗證訊息內。
5. 調整 [閒置逾時] 和 [最長工作階段時間] (如果預設值不足夠)。
6. 如果所有使用者都已經或將要匯入到「伺服器」，且必須接受多因素驗證，請核取 [需要進行 Multi-Factor Authentication 使用者比對] 方塊。如果有大量使用者尚未匯入伺服器及/或將免除多重要素驗證，請勿核取此方塊。如需此功能的其他資訊，請參閱說明檔。 
7. 如有需要，請核取 Cookie 快取方塊。
8. 按一下 [確定] 按鈕。
9. 請參閱下方的[啟用 Azure Multi-Factor Authentication Server 的 IIS 外掛程式](#enable-iis-plug-ins-for-azure-multi-factor-authentication-server)小節，以完成 IIS 驗證組態。 


## 啟用 Azure Multi-Factor Authentication Server 的 IIS 外掛程式

一旦設定表單架構或 HTTP 驗證 URL 和設定，您必須在 IIS 中選取應該載入和啟用 Azure Multi-Factor Authentication IIS 外掛程式的位置。請使用下列程序：

1. 如果在 IIS 6 上執行，請按一下 [ISAPI] 索引標籤，然後選取執行 Web 應用程式的網站 (例如 [預設的網站])，以啟用該網站的 Azure Multi-Factor Authentication ISAPI 篩選外掛程式。
2. 如果在 IIS 7 或更高版本上執行，請按一下 [原生模組] 索引標籤，然後選取伺服器、網站或應用程式，在需要的層級上啟用 IIS 外掛程式。
3. 按一下畫面頂端的 [啟用 IIS 驗證] 方塊。Azure Multi-Factor Authentication 現在會保護選取的 IIS 應用程式。確定使用者已匯入到「伺服器」。如果您想要將內部 IP 位址列入白名單，以便從這些位置登入網站時不需進行雙因素驗證，請參閱以下「信任的 IP」一節。 


## 信任的 IP

信任的 IP 可讓使用者對源自特定 IP 位址或子網路的網站要求略過 Azure Multi-Factor Authentication。例如，您可以讓使用者從辦公室登入時免除 Azure Multi-Factor Authentication。為此，您可以將辦公室子網路指定為信任的 IP 項目。若要設定信任的 IP，請使用下列程序：

1. 在 [IIS 驗證] 區段中，按一下 [信任的 IP] 索引標籤。 
2. 按一下 [新增...] 按鈕。
3. 當 [新增信任的 IP] 對話方塊出現時，請選取 [單一 IP]、[IP 範圍] 或 [子網路] 選項按鈕。
4. 輸入應列入白名單中的 IP 位址、IP 位址範圍或子網路。如果輸入子網路，請選取適當的網路遮罩，然後按一下 [確定] 按鈕。現已加入白名單。

<!---HONumber=August15_HO9-->