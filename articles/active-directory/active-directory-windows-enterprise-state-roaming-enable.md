<properties
    pageTitle="在 Azure Active Directory 中啟用企業狀態漫遊 | Microsoft Azure"
    description="Windows 裝置中企業狀態漫遊設定的常見問題集。企業狀態漫遊提供使用者跨 Windows 裝置的一致體驗，並且減少設定新的裝置所需的時間。"
    services="active-directory"
    keywords="企業狀態漫遊, windows 雲端, 如何啟用企業狀態漫遊"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor="curtand"/>

<tags
    ms.service="active-directory"  
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/04/2016"
	ms.author="femila"/>

 

# 在 Azure Active Directory 中啟用企業狀態漫遊

企業狀態漫遊適用於具有 Premium Azure Active Directory (Azure AD) 訂用帳戶的任何組織。如需有關如何取得 Azure AD 訂用帳戶的詳細資訊，請參閱 [Azure AD 產品頁面](https://azure.microsoft.com/services/active-directory)。

當您啟用企業狀態漫遊時，貴組織將會自動獲得 Azure Rights Management 的免費訂用帳戶授權。此免費訂用帳戶僅限於加密和解密企業設定資料；您必須擁有付費的訂用帳戶，才能使用 Azure Rights Management 的完整功能。

取得 Azure AD 訂用帳戶之後，請依照下列步驟來啟用企業狀態漫遊：
 
1. 登入 Azure 傳統入口網站。 
2. 在左側選取 [ACTIVE DIRECTORY]，然後選取您要啟用企業狀態漫遊的目錄。![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming.png)	 
3. 移至最上層的 [設定] 索引標籤。![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-configure.png)
4.	向下捲動頁面，並且選取 [使用者可以同步設定及企業應用程式資料]，然後按一下 [儲存]。![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-select-all-sync-settings.png)

對於以企業狀態漫遊服務漫遊設定的 Windows 10 裝置，裝置必須使用 Azure AD 身分識別進行驗證。對於已加入 Azure AD 的裝置，使用者的主要登入是 Azure AD 身分識別，所以不需要額外組態。對於使用傳統內部部署 Active Directory 的裝置，IT 管理員必須使用 [Azure AD Connect](active-directory-aadconnect.md) 將內部部署 Active Directory 連接至 Azure AD，然後必須設定群組原則以強制用戶端裝置自動同步處理使用者資料與 Azure。

## 同步處理資料儲存體
企業狀態漫遊資料裝載於一或多個 [Azure 區域](https://azure.microsoft.com/regions/)，這些區域與 Azure AD 執行個體中設定的國家 (地區) 值有最佳的配對狀態。例如，將其國家 (地區) 值設為「法國」的客戶，其資料會被裝載於歐洲內的一或多個 Azure 區域，而將其國家 (地區) 值設為「美國」的客戶，其資料會被裝載於美國內的一或多個 Azure AD 區域。國家 (地區) 值是在 Azure AD 網域建立程序時建立，之後無法修改。

如果您需要資料儲存體位置的詳細資料，請向 [Azure 支援](https://azure.microsoft.com/support/options/)提出票證。

## 管理企業狀態漫遊
Azure AD 租用戶系統管理員可以在 Azure 傳統入口網站中啟用和停用企業狀態漫遊。![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-manage.png)

租用戶系統管理員也可以檢視每個使用者同步處理狀態報告，並且可以將設定同步處理限制為特定的安全性群組。

##資料保留
透過企業狀態漫遊同步處理至 Azure 的資料將會無限期保留，除非執行手動刪除作業，或有問題的資料被判斷為已過時。
 
- **手動刪除資料**：如果 Azure AD 系統管理員想要手動刪除設定資料，系統管理員可以向 [Azure 支援](https://azure.microsoft.com/support/options/)提出票證。
 
 - **使用者刪除**：當使用者在 Azure AD 中遭到刪除時，使用者帳戶會進入停用狀態 30 天。30 天之後，將會刪除帳戶，相關聯的設定資料也會刪除。
 - **租用戶 (目錄) 刪除**：在 Azure AD 中刪除整個目錄是即時的作業。與該目錄相關聯的所有設定資料也會刪除。 
 - **設定刪除**：如果 Azure AD 系統管理員想要立即刪除特定使用者的設定資料，系統管理員可以向 Azure 支援提出票證。 
- **過時的資料**：一年 (「保留期限」) 未被存取的資料會被視為過時，可能會從 Azure 中刪除。過時的資料可能是一組特定的 Windows/應用程式設定或使用者的所有設定。例如： 
 - 如果沒有任何裝置存取特定設定集合 (例如，從裝置移除應用程式，或針對所有使用者的裝置停用如「主題」的設定群組)，則該集合在保留期限之後就會變成過時，可能會遭到刪除。 
 - 如果使用者在其所有裝置上已關閉設定同步處理，則不會存取任何設定資料，而且該使用者的所有設定資料將會變成過時，而且可能在保留期限之後刪除。 
 - 如果 Azure AD 目錄管理員針對整個目錄關閉企業狀態漫遊，則該目錄中的所有使用者會停止同步處理設定，且所有使用者的所有設定資料會變成過時，並且可能在保留期限之後刪除。 

- **復原已刪除的資料**：無法設定資料保留期原則。一旦資料永久刪除，就無法復原。不過，務必記得設定資料只會從 Azure 中刪除，不會從使用者裝置中刪除。如果任何裝置稍後重新連線至企業狀態漫遊服務，設定會再次同步處理並儲存在 Azure 中。

## 相關主題
- [企業狀態漫遊概觀](active-directory-windows-enterprise-state-roaming-overview.md)
- [設定和資料漫遊常見問題集](active-directory-windows-enterprise-state-roaming-faqs.md)
- [設定同步處理的群組原則和 MDM 設定](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
- [Windows 10 漫遊設定參考](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)

<!---HONumber=AcomDC_0204_2016-->