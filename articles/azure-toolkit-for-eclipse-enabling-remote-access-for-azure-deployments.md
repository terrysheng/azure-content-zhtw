<properties
    pageTitle="在 Eclipse 中啟用 Azure 部署的遠端存取"
    description="了解如何使用適用於 Eclipse 的 Azure 工具組來啟用 Azure 部署的遠端存取。"
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="11/30/2015" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690951.aspx -->

# 在 Eclipse 中啟用 Azure 部署的遠端存取 #

為協助疑難排解您的部署，您可以啟用並使用「遠端存取」以連線到裝載您的部署的虛擬機器。遠端存取功能需要遠端桌面通訊協定 (RDP)。您可以先將您的部署發行到 Azure 之後再設定遠端存取，或如果您使用 Eclipse 與 Windows 作業系統，則可先設定遠端存取，再將它發行到 Azure。請注意，要連線到您在 Azure 的部署的虛擬機器，您需要相容於您的作業系統的遠端桌面用戶端。

## 如何在部署到 Azure 之前先啟用遠端存取 ##

>[AZURE.NOTE]若要在將您的應用程式部署到 Azure 之前先啟用遠端存取，您需要在 Windows 上執行 Eclipse。

下列影像顯示用來啟用遠端存取的 [遠端存取] 內容對話方塊。

![][ic719494]

有兩種方式來顯示 [遠端存取] 內容對話方塊：

* 按一下 [發行到 Azure] 對話方塊的 [遠端存取] 區段中的 [進階] 連結。
* 開啟您的 Azure 專案的 [內容] 對話方塊。

當您建立新的 Azure 部署專案時，專案預設不會啟用遠端存取。不過，您可以在 [發行到 Azure] 對話方塊指定使用者名稱和密碼，就能輕鬆地啟用遠端存取。遠端存取密碼使用 X.509 憑證加密。如果您未使用它來提供您自己的憑證，加密是依賴隨附於 Azure Plugin for Eclipse 的自我簽署憑證。這個自我簽署的憑證在您的 Azure 專案的 **cert** 資料夾中，同時儲存為一個公開憑證檔案 (SampleRemoteAccessPublic.cer) 和一個個人資訊交換 (PFX) 憑證檔案 (SampleRemoteAccessPrivate.pfx)。後者包含憑證的私密金鑰，而且具有預設密碼 **Password1**。不過，由於此密碼是公開資訊，預設的憑證應僅供教學使用，不適用於實際執行部署。因此，除教學的用途外，當您想為您的部署啟用遠端工作階段時，您應該按一下 [發行到 Azure] 對話方塊中的 [進階] 連結來指定您自己的憑證。請注意，您必須將憑證的 PFX 版本上傳至 Azure 管理入口網站中您裝載的服務，這樣 Azure 才可以解密使用者密碼。

其餘的教學課程示範如何為一開始建立時停用遠端存取的 Azure 部署專案啟用遠端存取。基於本教學課程的目的，我們將建立一個新的自我簽署憑證，而其 .pfx 檔案將包含您選擇的密碼。您也可以選擇使用憑證授權單位所核發的憑證。

## 如何在部署到 Azure 之後啟用遠端存取 ##

若要在部署到 Azure 之後啟用遠端存取，請使用下列步驟：

1. 使用您的 Azure 帳戶登入 Azure 管理入口網站
1. 在 [雲端服務] 清單中，選取您部署的雲端服務
1. 在雲端服務 Web 頁面上，按一下 [設定] 連結
1. 在 [組態] 頁面底部，按一下 [遠端] 連結
1. 當快顯對話方塊出現時：
    1. 指定您想要為其啟用遠端存取的角色
    1. 按一下以選取 [啟用遠端桌面] 核取方塊
    1. 指定您想要用於遠端存取的使用者名稱和密碼
    1. 選取要使用的憑證
1. 按一下 [確定]。 

您會看到訊息指出您的組態變更正在進行，可能需要幾分鐘才能完成。完成組態變更之後，請依照本文稍後的＜從遠端登入＞一節中的步驟執行。
	
## 如何在您的封裝中啟用遠端存取 ##

* 在 Eclipse 的專案總管窗格中，於您的 Azure 專案上按一下滑鼠右鍵，並按一下 [內容]。
* 在 [內容] 對話方塊中，展開左窗格中的 [Azure]，按一下 [遠端存取]。
* 在 [遠端存取] 對話方塊中，確定已核取 [讓所有角色接受使用這些登入認證的遠端桌面連線]。
* 指定遠端桌面連線的使用者名稱。
* 指定並確認使用者的密碼。當您建立遠端桌面連線時，將使用在此對話方塊中設定的使用者名稱和密碼值。(請注意，這個密碼不同於您的 PFX 密碼)。
* 指定使用者帳戶的到期日。
* 按一下 [新增] 來建立新的自我簽署憑證。(或者，您可以透過 [工作區] 或 [檔案系統] 按鈕，分別從您的工作區或檔案系統選取憑證，但基於本教學課程目的，我們將建立新的憑證。)
* 在 [新憑證] 對話方塊中，指定並確認將使用於 PFX 檔案的密碼。
* 接受為 [名稱 (CN)] 提供的值，或使用自訂的名稱。
* 指定儲存新憑證的路徑和檔案名稱 (.cer 格式)。針對這一步和下一步，您可以使用您的 Azure 專案的 **cert** 資料夾，但您也可以選擇其他位置。基於本教學課程的目的，我們將使用 **c:\\mycert\\mycert.cer**。(請先建立 **c:\\mycert** 資料夾後再繼續，或視需要使用現有的資料夾。)
* 指定儲存新憑證和其私密金鑰的路徑和檔案名稱 (私密金鑰為 .pfx 格式)。基於本教學課程的目的，我們將使用 **c:\\mycert\\mycert.pfx**。您的 [新憑證] 對話方塊看起來應該和下面類似 (如果您不是使用 **c:\\mycert**，請更新資料夾路徑)：
    ![][ic712275]
* 按一下 [確定] 關閉 [新憑證] 對話方塊。
* 您的 [遠端存取] 對話方塊看起來應該和下面類似：</p>
    ![][ic719495]
* 按一下 [確定] 關閉 [遠端存取] 對話方塊。
	
使用為部署到雲端所設定的組建，重新建立您的應用程式。

## 從遠端登入 ##

您的角色執行個體準備就緒後，您可以從遠端登入裝載您的應用程式的虛擬機器。

* 如果使用 Eclipse 和 Windows，而且您在部署到 Azure 的期間選取 [部署時啟動遠端桌面] 選項，當您的部署開始時會提供一個 [遠端桌面連線] 登入畫面。當系統提示您輸入使用者名稱和密碼時，輸入您為遠端使用者指定的值就能登入。
* 從遠端登入的另一種方式是透過 <a href="http://go.microsoft.com/fwlink/?LinkID=512959">Azure 管理入口網站</a>：
    * 在 Azure 管理入口網站的 [雲端服務] 檢視中，按一下 [執行個體]，按一下特定的執行個體，再按一下 [連線] 按鈕。命令列中會顯示如下的 [連線] 按鈕：
    ![][ic659273]  
    >[AZURE.NOTE]如果您是在非 Windows 作業系統上，則必須使用與作業系統相容的遠端桌面用戶端，並使用您下載的 RDP 檔案中的設定，依照步驟來設定該用戶端。
    * 按一下 [連線] 按鈕後，將會提示您開啟 RDP 檔案。開啟檔案，並依照提示執行。(您可以也將此檔案儲存到您的本機電腦，然後按兩下執行該檔案以遠端登入您的虛擬機器，而不需要先前往管理入口網站。)
    * 當系統提示您輸入使用者名稱和密碼時，輸入您為遠端使用者指定的值就能登入。

## 另請參閱 ##

[適用於 Eclipse 的 Azure 工具組][]

[在 Eclipse 中建立適用於 Azure 的 Hello World 應用程式][]

[安裝適用於 Eclipse 的 Azure 工具組][]

如需有關在 Azure 中使用 Java 的詳細資訊，請參閱 [Azure Java 開發人員中心][]。

<!-- URL List -->

[Azure Java 開發人員中心]: http://go.microsoft.com/fwlink/?LinkID=699547
[Azure Management Portal]: http://go.microsoft.com/fwlink/?LinkID=512959
[適用於 Eclipse 的 Azure 工具組]: http://go.microsoft.com/fwlink/?LinkID=699529
[在 Eclipse 中建立適用於 Azure 的 Hello World 應用程式]: http://go.microsoft.com/fwlink/?LinkID=699533
[安裝適用於 Eclipse 的 Azure 工具組]: http://go.microsoft.com/fwlink/?LinkId=699546

<!-- IMG List -->

[ic712275]: ./media/azure-toolkit-for-eclipse-enabling-remote-access-for-azure-deployments/ic712275.png
[ic719495]: ./media/azure-toolkit-for-eclipse-enabling-remote-access-for-azure-deployments/ic719495.png
[ic719494]: ./media/azure-toolkit-for-eclipse-enabling-remote-access-for-azure-deployments/ic719494.png
[ic659273]: ./media/azure-toolkit-for-eclipse-enabling-remote-access-for-azure-deployments/ic659273.png

<!-------HONumber=AcomDC_1210_2015--->
