<properties 
   pageTitle="搭配使用遠端桌面與 Azure 角色 | Microsoft Azure"
   description="搭配使用遠端桌面與 Azure 角色"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="multiple"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/18/2015"
   ms.author="tarcher" />

# 搭配使用遠端桌面與 Azure 角色

使用 Azure SDK 和遠端桌面服務，您可以存取 Azure 角色和由 Azure 裝載的虛擬機器。在 Visual Studio 中，您可以從 Azure 專案設定遠端桌面服務。若要啟用遠端桌面服務，您必須建立包含一或多個角色的工作專案並發佈至 Azure。

>[AZURE.IMPORTANT]您應該只基於疑難排解或開發目的來存取 Azure 角色。每個虛擬機器的用途是在 Azure 應用程式中執行特定角色，而不是執行其他用戶端應用程式。如果您想要使用 Azure 來裝載可做為任何用途的虛擬機器，請參閱「從伺服器總管存取 Azure 虛擬機器」。

## 啟用和使用遠端桌面來存取 Azure 角色

1. 在 [方案總管] 中，開啟專案的捷徑功能表並按一下 [發佈]。

    [發佈 Azure 應用程式] 精靈隨即出現。

    ![雲端服務專案的發佈命令](./media/vs-azure-tools-remote-desktop-roles/IC799161.png)

1. 在精靈的 [Microsoft Azure 發佈設定] 頁面底部，選取 [啟用所有角色的遠端桌面] 核取方塊。

    [遠端桌面組態] 對話方塊隨即出現。

1. 在 [遠端桌面組態] 對話方塊底部，選擇 [更多選項] 按鈕。
 
    這會顯示下拉式清單方塊，可讓您建立或選擇的憑證，以便您在透過遠端桌面連線時可以加密認證資訊。

1. 在下拉式清單中，選擇 [&lt;建立>]，或從選擇清單中現有的憑證。

    如果您選擇現有憑證，請略過下列步驟。

    >[AZURE.NOTE]遠端桌面連線所需的憑證不同於用於其他 Azure 作業的憑證。遠端存取憑證必須具有私密金鑰。

    [建立憑證] 對話方塊會隨即出現。

    1. 為新憑證提供易記的名稱，然後選擇 [確定] 按鈕。新的憑證會出現在下拉式清單方塊中。

    1. 在 [遠端桌面組態] 對話方塊中，提供使用者名稱和密碼。
    
        您無法使用現有的帳戶。請勿指定 Administrator 做為新帳戶的使用者名稱。

        >[AZURE.NOTE]如果密碼不符合複雜性需求，密碼文字方塊旁會出現紅色圖示。密碼必須包含大寫字母、小寫字母和數字或符號。

    1. 選擇帳戶的到期日期，在此日期之後，遠端桌面連線會遭到封鎖。

    1. 當您提供所有必要的資訊之後，選擇 [確定] 按鈕。
    
        啟用遠端存取服務的數個設定會加入至 .cscfg 和 .csdef 檔案。

1. 當您準備好要發佈雲端服務時，在 [Microsoft Azure 發佈設定] 精靈中選擇 [確定] 按鈕。

    如果您還沒準備好要發佈，請選擇 [取消] 按鈕。將會儲存組態設定，您可以稍後再發佈雲端服務。

## 使用遠端桌面連接到 Azure 角色

在 Azure 上發佈雲端服務之後，您可以使用 [伺服器總管] 來登入 Azure 所裝載的虛擬機器。

1. 在 [伺服器總管] 中展開 [Azure] 節點，然後展開某個雲端服務的節點及其中一個角色，以顯示執行個體的清單。

1. 開啟執行個體節點的捷徑功能表，然後選擇 [使用遠端桌面連接]。

    ![透過遠端桌面連接](./media/vs-azure-tools-remote-desktop-roles/IC799162.png)

1. 輸入您先前建立的使用者名稱和密碼。您現在已登入遠端工作階段。

<!---HONumber=AcomDC_1223_2015-->