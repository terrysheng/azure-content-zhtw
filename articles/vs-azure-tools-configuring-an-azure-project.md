<properties
   pageTitle="使用 Visual Studio 設定 Azure 雲端服務專案 | Microsoft Azure"
   description="了解如何在 Visual Studio 中根據 Azure 雲端服務專案的需求來設定專案。"
   services="visual-studio-online"
   documentationCenter="na"
   authors="kempb"
   manager="douge"
   editor="tglee" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="09/29/2015"
   ms.author="kempb" />

# 使用 Visual Studio 設定 Azure 雲端服務專案

您可以根據 Azure 雲端服務專案的需求來設定專案。您可以設定下列類別的專案的屬性：

- **將雲端服務發佈至 Azure**

  您可以設定屬性以確保部署至 Azure 的現有雲端服務不會被意外刪除。

- **執行或偵錯本機電腦上的雲端服務**

  您可以選取一個要使用的服務組態，並指示是否要啟動 Azure 儲存體模擬器。

- **在建立雲端服務封裝時進行驗證**

  您可以決定將任何警告均視為錯誤，這樣便能確保雲端服務封裝部署時不會發生錯誤。若您部署後發現有失敗情形發生，這會減少等候時間。

下圖顯示如何選取在本機執行或偵錯雲端服務時要使用的組態。如圖所示，您可以從此視窗中設定您需要的任一專案屬性。

![設定 Microsoft Azure 專案](./media/vs-azure-tools-configuring-an-azure-project/IC713462.png)

## 設定 Azure 雲端服務專案

1. 若要從 [方案總管] 設定雲端服務專案，請開啟雲端服務專案的捷徑功能表，然後選擇 [屬性]。

  Visual Studio 編輯器中會顯示具有該雲端服務專案名稱的頁面。

1. 選擇 [開發] 索引標籤。

1. 為確保您不會意外刪除 Azure 中現有的部署，請在 [刪除現有部署前先提示] 清單中選擇 **True**。

1. 如果要選取在本機執行或偵錯雲端服務時要使用的服務組態，請在 [服務組態] 清單中選擇該服務組態。

  >[AZURE.NOTE]如果您想建立一個服務組態來使用，請參閱＜如何管理服務組態和設定檔＞。如果您想要修改某個角色的服務組態，請參閱[如何使用 Visual Studio 設定 Azure 雲端服務的角色](vs-azure-tools-configure-roles-for-cloud-service.md)。

1. 如果要在您於本機執行或偵錯雲端服務時啟動 Azure 儲存體模擬器，請在 [啟動 Azure 儲存體模擬器] 中選擇 **True**。

1. 為確保發生封裝驗證錯誤時無法發佈，請在 [將警告視為錯誤] 中選擇 **True**。

1. 為確保您的 Web 角色每次於 IIS Express 本機啟動時均使用相同的連接埠，請在 [使用 Web 專案連接埠] 中選擇 **True**。如果要為特定 Web 專案使用特定的連接埠，請開啟該 Web 專案的捷徑功能表，依序選擇 [屬性] 索引標籤、[Web] 索引標籤，然後在 [IIS Express] 區段的 [專案 URL] 設定中變更連接埠號碼。例如，輸入 `http://localhost:14020` 做為專案 URL。

1. 如果要儲存您對雲端服務專案屬性所做的任何變更，請選擇工具列上的 [儲存] 按鈕。

## 後續步驟

若要深入了解如何在 Visual Studio 中設定 Azure 雲端服務專案，請參閱[使用多個服務組態設定 Azure 專案](vs-azure-tools-multiple-services-project-configurations.md)。

<!---HONumber=Oct15_HO3-->