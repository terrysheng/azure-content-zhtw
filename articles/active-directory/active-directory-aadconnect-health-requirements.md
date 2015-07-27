<properties 
	pageTitle="Azure AD Connect Health 需求。" 
	description="這是 Azure AD Connect Health 頁面，其中說明需求與代理程式安裝。" 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/12/2015" 
	ms.author="billmath"/>

# Azure Active Directory Connect Health 需求
下列文件是開始使用 Azure AD Connect Health 之前必須符合的需求清單。



## Azure AD Premium 授權

Azure AD Connect Health 是 Azure AD Premium 的一個功能，而且需要 Azure AD Premium 授權。若要取得授權，請參閱「開始使用 Azure AD Premium」。
 

## 您必須是 Azure AD 租用戶的全域管理員。

根據預設，全域系統管理員可以存取由 Azure AD Connect Health 所提供的資訊。如果您不是與您的內部部署 Active Directory 同盟的 Azure AD 租用戶的全域系統管理員，您就無法建立 Azure AD Connect Health 的服務執行個體。請確定您是全域管理員。如需其他資訊，請參閱[管理您的 Azure AD 目錄](https://msdn.microsoft.com/library/azure/hh967611.aspx)。
 

## Azure AD Connect Health 代理程式安裝在每部目標伺服器上

Azure AD Connect Health 要求在目標伺服器上安裝代理程式，以提供在 Azure AD Connect Health 入口網站中檢視的資料。也就是說，若要取得 AD FS 內部部署基礎結構的相關資料，代理程式必須安裝在 AD FS 伺服器上。這包括 AD FS Proxy 伺服器和 Web 應用程式 Proxy 伺服器。如需安裝 Azure AD Connect Health 代理程式的相關資訊，請參閱「Azure AD Connect Health 代理程式安裝步驟」。


## Azure AD Connect Health 代理程式需求

下列各節說明 Azure AD Connect Health 代理程式的特定需求。
 

### 下載 Azure AD Connect Health 代理程式

若要開始使用 Azure AD Connect Health，您可以在此下載最新版的代理程式：[下載 Azure AD Connect Health 代理程式](http://go.microsoft.com/fwlink/?LinkID=518973)。 請確定您已從 Marketplace 中加入服務，才可安裝代理程式。

 
### Azure 服務端點的輸出連線
在安裝期間和執行階段，代理程式需要連線至以下列出的 Azure AD Connect Health 服務端點。如果您封鎖輸出連線，請確定在允許清單中加入下列內容：

- *.servicebus.windows.net - Port: 5671 - https://*.adhybridhealth.azure.com/
- https://*.table.core.windows.net/
- https://policykeyservice.dc.ad.msft.net/
- https://login.windows.net
- https://login.microsoftonline.com
- https://secure.aadcdn.microsoftonline-p.com 

## 如果啟用 IE 增強式安全性，則允許下列網站
如果在即將安裝代理程式的伺服器上啟用 IE 增強式安全性，則必須允許下列網站。

- https://login.microsoftonline.com 
- https://secure.aadcdn.microsoftonline-p.com
- https://login.windows.net
- Azure Active Directory 所信任的您組織的同盟伺服器，例如：https://sts.contoso.com 


### 若是 AD FS，必須啟用 AD FS 稽核，才能使用使用情況分析

若要讓使用情況分析功能收集並分析資料，Azure AD Connect Health 代理程式需要 AD FS 稽核記錄檔中的資訊。預設不會啟用這些記錄檔。這只適用於 AD FS 同盟伺服器。您不需要在 AD FS Proxy 伺服器或 Web 應用程式 Proxy 伺服器上啟用稽核。使用下列程序啟用 AD FS 稽核，並找出 AD FS 稽核記錄檔。

#### 啟用 AD FS 2.0 的稽核

1. 按一下 [開始]，依序指向 [程式集] 和 [系統管理工具]，然後按一下 [本機安全性原則]。
1. 瀏覽至 **Security Settings\\Local Policies\\User Rights Management** 資料夾，然後按兩下 [產生安全性稽核]。
1. 在 [本機安全性設定] 索引標籤上，確認已列出 AD FS 2.0 服務帳戶。如果不存在，按一下 [新增使用者或群組]，並將其新增至清單中，然後按一下 [確定]。
1. 使用提高的權限開啟命令提示字元，然後執行下列命令以啟用稽核。`auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable`
1. 關閉 [本機安全性原則]，然後開啟 [管理嵌入式管理單元]。若要開啟 [管理嵌入式管理單元]，按一下 [開始]，依序指向 [程式集] 和 [系統管理工具]，然後按一下 [AD FS 2.0 管理]。
1. 在 [動作] 窗格中，按一下 [編輯同盟服務屬性]。
1. 在 [同盟服務屬性] 對話方塊中，按一下 [事件] 索引標籤。
1. 選取 [成功稽核] 和 [失敗稽核] 核取方塊。
1. 按一下 [確定]。

#### 在 Windows Server 2012 R2 上啟用 AD FS 的稽核

1. 在 [開始] 畫面上開啟 [伺服器管理員]，或在桌面上的工作列中開啟 [伺服器管理員]，以開啟 [本機安全性原則]，然後按一下 [工具/本機安全性原則]。
1. 瀏覽至 **Security Settings\\Local Policies\\User Rights Assignment** 資料夾，然後按兩下 [產生安全性稽核]。
1. 在 [本機安全性設定] 索引標籤上，確認已列出 AD FS 服務帳戶。如果不存在，按一下 [新增使用者或群組]，並將其新增至清單中，然後按一下 [確定]。
1. 使用提高的權限開啟命令提示字元，然後執行下列命令以啟用稽核：`auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable.`
1. 關閉 [本機安全性原則]，然後開啟 **AD FS 管理**嵌入式管理單元 (在 [伺服器管理員] 中，按一下 [工具]，然後選取 [AD FS 管理])。
1. 在 [動作] 窗格中，按一下 [編輯同盟服務屬性]。
1. 在 [同盟服務屬性] 對話方塊中，按一下 [事件] 索引標籤。
1. 選取 [成功稽核] 和 [失敗稽核] 核取方塊，然後按一下 [確定]。






#### 找出 AD FS 稽核記錄檔


1. 開啟 [事件檢視器]。</li>
1. 移至 [Windows 記錄]，然後選取 [安全性]。
1. 按一下右側的 [篩選目前的記錄檔]。
1. 在 [事件來源] 下，選取 [AD FS 稽核]。

![AD FS 稽核記錄](./media/active-directory-aadconnect-health-requirements/adfsaudit.png)

> [AZURE.WARNING]如果您有即將停用 AD FS 稽核的群組原則，則 Azure AD Connect Health 代理程式將無法收集資訊。請確定您沒有可能即將停用稽核的群組原則。


### Windows Server 2008 R2 伺服器上的代理程式安裝

若是 Windows Server 2008 R2 伺服器，請執行以下操作：

1. 請確定伺服器在 Service Pack 1 或更高版本上執行。
1. 關閉 IE ESC 以安裝代理程式：
1. 在安裝 AD Health 代理程式之前，先在每部伺服器上安裝 Windows PowerShell 4.0。安裝 Windows PowerShell 4.0：
 - 使用下列連結下載離線安裝程式，以安裝 [Microsoft.NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=40779)。
 - 安裝 PowerShell ISE (從 Windows 功能)
 - 安裝 [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855)。
 - 在伺服器上安裝 Internet Explorer 10 或更新版本。這是 Health 服務所必需，才能使用 Azure 系統管理員認證進行驗證。
1. 如需有關在 Windows Server 2008 R2 上安裝 Windows PowerShell 4.0 的其他資訊，請參閱[這裡](http://social.technet.microsoft.com/wiki/contents/articles/20623.step-by-step-upgrading-the-powershell-version-4-on-2008-r2.aspx)的 Wiki 文章。

## Azure AD Connect Health 代理程式部署
本節將逐步引導您在伺服器上安裝和設定 Azure AD Connect Health 代理程式。請記住，在 Azure AD Connect Health 執行個體中出現任何資料之前，您必須先在目標伺服器上安裝 Azure AD Connect Health 代理程式。請務必在安裝代理程式之前，先完成上述需求。您可以使用上述連結下載代理程式，然後依照以下的步驟進行。


按兩下您所下載的 .exe 檔案。在第一個畫面上，按一下 [安裝]。

![驗證 Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install1.png)

一旦安裝完成之後，按一下 [立即設定]。

![驗證 Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install2.png)

這將會啟動命令提示字元，然後再啟動將執行 Register-AzureADConnectHealthADFSAgent 的特定 PowerShell。系統將提示您登入 Azure。繼續進行並登入。

![驗證 Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install2.png)

登入後，PowerShell 將會繼續。一旦完成之後，您就可以關閉 PowerShell，且設定已完成。

![驗證 Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install3.png)

此時，應該會自動啟動服務，且代理程式現在將會監視並收集資料。請注意，如果您還未符合前幾節所述的所有必要條件，您將會在 PowerShell 視窗中看到警告。以下螢幕擷取畫面為範例。

![驗證 Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install4.png)

若要確認已安裝代理程式，請開啟服務，並尋找下列項目。如果您已完成設定，這些服務應該正在執行。否則，他們將不會啟動，直到設定完成為止。

- Azure AD Connect Health AD FS 診斷服務
- Azure AD Connect Health AD FS Insights 服務
- Azure AD Connect Health AD FS 監視服務
 
![驗證 Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install5.png)

<!---HONumber=July15_HO3-->