<properties 
   pageTitle="發佈 Azure 應用程式精靈"
   description="發佈 Azure 應用程式精靈"
   services="visual-studio-online"
   documentationCenter="na"
   authors="kempb"
   manager="douge"
   editor="tlee" />
<tags 
   ms.service="multiple"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/24/2015"
   ms.author="kempb" />

# 發佈 Azure 應用程式精靈

## 概觀

在 Visual Studio 中開發 web 應用程式之後，您可以使用**發佈 Azure 應用程式**精靈，更輕鬆地將應用程式發佈至 Azure 雲端服務。第一節說明您在使用精靈前必須完成的步驟，其他節說明精靈的功能。

>[AZURE.NOTE]本主題是關於部署到雲端服務，而不是網站。如需部署到網站的相關資訊，請參閱[如何部署到 Azure 網站](https://social.msdn.microsoft.com/Search/windowsazure?query=How%20to%20Deploy%20an%20Azure%20Web%20Site&Refinement=138&ac=4#refinementChanges=117&pageNumber=1&showMore=false)。

## 必要條件

發佈 web 應用程式到 Azure 之前，您必須有 Microsoft 帳戶和 Azure 訂用帳戶，而且您必須將 web 應用程式與 Azure 雲端服務產生關聯。如果您已經完成這些工作，您就可以跳至下一節。

1. 取得 Microsoft 帳戶和 Azure 訂用帳戶。您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)免費試用 Azure 訂用帳戶一個月

1. 在 Azure 上建立雲端服務和儲存體帳戶。您可以從 Visual Studio 中的 [伺服器總管] 執行此動作，或使用 [Azure 管理入口網站](http://go.microsoft.com/fwlink/?LinkID=213885)執行。如需有關如何設定 Azure 環境的詳細資訊，請參閱[設定從 Visual Studio 發佈雲端服務所需的服務](vs-azure-tools-publish-azure-application-wizard)。

1. 啟用 Azure 的 web 應用程式。若要啟用 web 應用程式以將其從 Visual Studio 發佈至 Azure，您必須將它與 Visual Studio 中的 Azure 雲端服務專案產生關聯。若要建立相關聯的雲端服務專案，請開啟 web 應用程式專案的捷徑功能表，然後選擇 [轉換]，**轉換成 Azure 雲端服務專案**。

1. 雲端服務專案加入至您的方案之後，請再次開啟相同的捷徑功能表並選擇 [發佈]。如需有關如何啟用 Azure 的應用程式之詳細資訊，請參閱[作法：將 Web 應用程式從 Visual Studio 移轉並發佈至 Azure 雲端服務](https://msdn.microsoft.com/library/azure/hh420322.aspx)。

>[AZURE.NOTE]請務必以系統管理員認證啟動 Visual Studio (以系統管理員身分執行)。

1. 當您準備好發佈應用程式時，開啟 Azure 雲端服務專案的捷徑功能表，然後選擇 [發佈]。下列步驟會顯示發佈 Azure 應用程式精靈。

## 選擇您的訂用帳戶。

### 選擇訂用帳戶

1. 第一次使用精靈之前，您必須登入。選擇**登入**連結。出現提示時登入 Azure 入口網站，並提供您的 Azure 使用者名稱和密碼。 

    ![這是其中一個發佈精靈畫面](./media/vs-azure-tools-publish-azure-application-wizard/IC799159.png)

    在訂用帳戶清單中填入與您的帳戶相關聯的訂用帳戶。您也可能會從您先前匯入的任何訂用帳戶檔案看到訂用帳戶。

1. 在 [選擇您的訂用帳戶] 清單中，選擇要用於這個部署的訂用帳戶。

   如果您選擇 **<管理...>**，[管理訂用帳戶] 對話方塊會隨即出現，而且您可以選擇您想要使用的訂用帳戶和使用者帳戶。[帳戶] 索引標籤會顯示您的所有帳戶，而 [訂用帳戶] 索引標籤會顯示與帳戶相關聯的所有訂用帳戶。您也可以選擇要從其中使用 Azure 資源的區域，並且從 Azure 入口網站建立或匯入訂用帳戶的憑證。如果您從訂用帳戶檔案匯入任何訂用帳戶，相關聯的憑證將會出現在 [憑證] 索引標籤下。完成時，請選擇 [關閉] 按鈕。

    ![Manage subscriptions](./media/vs-azure-tools-publish-azure-application-wizard/IC799160.png)

    >[AZURE.NOTE] A subscription file can contain more than one subscription.

1. 選擇 [下一步] 按鈕以繼續。 

    如果您的訂用帳戶中沒有任何雲端服務，您需要在 Azure 中建立雲端服務以裝載您的專案。[建立雲端服務和儲存體帳戶] 對話方塊會隨即出現。

    指定雲端服務的新名稱。名稱在 Azure 中必須是唯一的。然後指定接近您或大部分用戶端之資料中心的區域或同質群組。此名稱也用於 Azure 為雲端服務所建立的新儲存體帳戶。

1. 修改您想要用於這個部署的任何設定，然後選擇 [發佈] 按鈕將它發佈 (下一節會提供各種設定的詳細資料)。若要在發佈之前檢閱設定，請選擇 [下一步] 按鈕。

    >[AZURE.NOTE]如果您在此步驟中選擇 [發佈]，您可以在 Visual Studio 中監視此部署的狀態。

您可以使用 [發佈 Azure 應用程式] 精靈修改部署的一般及進階設定。例如，您可以選擇一項設定，在發佈應用程式前將其部署至測試環境。下圖顯示 Azure 部署的 [一般設定] 索引標籤。

![一般設定](./media/vs-azure-tools-publish-azure-application-wizard/IC749013.png)

## 設定您的發佈設定

### 設定發佈設定

1. 在 [雲端服務] 清單中，執行下列其中一組步驟：

   1. 在下拉式清單方塊中，選擇現有的雲端服務。服務的資料中心位置會隨即出現。您應該記下這個位置並確定您的儲存體帳戶位置位於相同的資料中心。

    1. 選擇 [建立新的] 來建立 Azure 裝載的雲端服務。在 [建立雲端服務] 對話方塊中，提供服務的名稱，然後指定區域或同質群組，以指定您想要裝載此雲端服務的資料中心位置。名稱在 Azure 中必須是唯一的。

1. 在 [環境] 清單中，選擇 [生產] 或 [預備]。如果您想要將應用程式部署至測試環境，請選擇預備環境。您可以在稍後將應用程式移至生產環境。

1. 在 [建置組態] 清單中，選擇 [偵錯] 或 [發行]。

1. 在 [服務組態] 清單中，選擇 [雲端] 或 [本機]。

    如果您想要從遠端連線到服務，請選取 [啟用所有角色的遠端桌面] 核取方塊。此選項主要用於疑難排解。當您選取此核取方塊，[遠端桌面組態] 對話方塊會隨即出現。選擇設定連結以變更組態。

    選取 [啟用所有 web 角色的 Web 部署] 核取方塊以啟用服務的 web 部署。您必須啟用遠端桌面以使用這項功能。如需詳細資訊，請參閱[[使用 Azure Tools 發佈雲端服務](https://msdn.microsoft.com/library/azure/ff683672.aspx)](https://msdn.microsoft.com/library/azure/ff683672.aspx)。如需 Web 部署的詳細資訊，請參閱[[使用 Azure Tools 發佈雲端服務](https://msdn.microsoft.com/library/azure/ff683672.aspx)](https://msdn.microsoft.com/library/azure/ff683672.aspx)。

1. 選擇 [進階設定] 所引標籤。在 [部署標籤] 欄位中，接受預設名稱，或輸入您所選擇的名稱。若要將日期附加至部署標籤，請選取核取方塊。

    ![發佈精靈的第三個畫面](./media/vs-azure-tools-publish-azure-application-wizard/IC749014.png)

1. 在 [儲存體帳戶] 清單中，選擇要用於這個部署的儲存體帳戶。比較雲端服務和儲存體帳戶的資料中心位置。在理想的情況下，這些位置應該相同。

    >[AZURE.NOTE]Azure 儲存體帳戶會儲存應用程式部署的封裝。部署應用程式之後，封裝會從儲存體帳戶中移除。

1. 如果您只想要部署更新的元件，請選取 [部署更新] 核取方塊。這種部署類型比完整部署更快速。選擇**設定**連結以開啟 [部署更新設定] 對話方塊，如下圖所示。

    ![部署設定](./media/vs-azure-tools-publish-azure-application-wizard/IC617060.png)

    您可以選擇更新部署的兩個選項之一，累加或同時。累加部署一次會更新一個已部署的執行個體，讓您的應用程式保持連線並可供使用者使用。同時部署會一次更新所有已部署的執行個體。同時更新的速度比累加更新還快，但如果您選擇此選項，您的應用程式可能無法在更新過程中使用。

    如果無法更新部署，您應該選取此核取方塊，如果您想要在更新部署失敗時自動更新完整部署，請執行完整部署。完整部署會重設雲端服務的虛擬 IP (VIP) 位址。如需詳細資訊，請參閱[作法：保持雲端服務的固定虛擬 IP 位址](https://msdn.microsoft.com/library/azure/jj614593.aspx)。


1. 若要偵錯您的服務，請選取 [啟用 IntelliTrace] 核取方塊，或者如果您要部署**偵錯**組態並且想要在 Azure 中偵錯雲端服務，請選取 [啟用所有角色的遠端偵錯工具] 核取方塊以部署遠端偵錯服務。

2. 若要分析應用程式，請選取 [啟用分析] 核取方塊，然後選擇**設定**連結以顯示分析選項。


    >[AZURE.NOTE]您必須使用 Visual Studio Ultimate 以啟用 IntelliTrace 或階層互動分析 (TIP)，而且兩者不能同時啟用。

    如需詳細資訊，請參閱[使用 IntelliTrace 和 Visual Studio 進行已發佈的雲端服務偵錯](https://msdn.microsoft.com/library/azure/ff683671.aspx)和[測試雲端服務的效能](https://msdn.microsoft.com/library/azure/hh369930.aspx)。

1. 選擇 [下一步] 以檢視應用程式的摘要頁面。

## 發佈您的應用程式

1. 您可以選擇從您所選擇的設定建立發佈設定檔。例如，您可能會建立一個設定檔用於測試環境，並建立另一個用於生產。若要儲存這個設定檔，請選擇**儲存**圖示。此精靈會建立設定檔並將它儲存在 Visual Studio 專案。若要修改設定檔名稱，請開啟 [目標設定檔] 清單，然後選擇 **< 管理...>**。

    ![發佈精靈的摘要畫面](./media/vs-azure-tools-publish-azure-application-wizard/IC749015.png)

    >[AZURE.NOTE]發佈設定檔會出現在 Visual Studio 的 [方案總管] 中，而且設定檔設定會寫入至副檔名為.azurePubxml 的檔案。設定會儲存為 XML 標記的屬性。

1. 選擇 [發佈] 以發佈應用程式。您可以在 Visual Studio 中監視 [輸出] 視窗中的處理序狀態。

## 另請參閱

[設定從 Visual Studio 發佈雲端服務所需的服務](https://msdn.microsoft.com/library/azure/ff683668.aspx)

[作法：從 Visual Studio 將 Web 應用程式移轉並發佈至 Azure 雲端服務](https://msdn.microsoft.com/library/azure/hh420322.aspx)

[使用 Azure Tools 發佈雲端服務](https://msdn.microsoft.com/library/azure/ff683672.aspx)

[使用 IntelliTrace 和 Visual Studio 偵錯發佈的雲端服務](https://msdn.microsoft.com/library/azure/ff683671.aspx)

[測試雲端服務的效能](https://msdn.microsoft.com/library/azure/hh369930.aspx)

<!---HONumber=Oct15_HO3-->