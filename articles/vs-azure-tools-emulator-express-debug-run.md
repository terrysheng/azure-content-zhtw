<properties
   pageTitle="使用 Emulator Express 在本機電腦上執行及偵錯雲端服務 | Microsoft Azure"
   description="使用 Emulator Express 在本機電腦上執行及偵錯雲端服務"
   services="visual-studio-online"
   documentationCenter="n/a"
   authors="TomArcher"
   manager="douge"
   editor="tlee" />
<tags
   ms.service="visual-studio-online"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="08/14/2015"
   ms.author="tarcher" />


# 使用 Emulator Express 在本機電腦上執行及偵錯雲端服務

使用 Emulator Express，您可以測試及偵錯雲端服務，而不需以系統管理員身分執行 Visual Studio。視您的雲端服務的需求而定，您可以進行專案設定以使用 Emulator Express 或完整模擬器。如需完整模擬器的詳細資訊，請參閱[在計算模擬器中執行 Azure 應用程式](../storage/storage-use-emulator.md)。Emulator Express 最初隨附在 Azure SDK 2.1 中，而從 Azure SDK 2.3 起，它成為預設模擬器。

## 在 Visual Studio IDE 中使用 Emulator Express

當您在 Azure SDK 2.3 或更新版本中建立新專案時，已經選取 Emulator Express。對於使用舊版 SDK 建立的現有專案，請遵循下列步驟以選取 Emulator Express。

### 設定專案使用 Emulator Express

1. 在 Azure 專案的捷徑功能表上，選擇 [屬性]，然後選擇 [Web] 索引標籤。

1. 在 [本機開發伺服器] 之下，選擇 [使用 IIS Express 選項] 按鈕。Emulator Express 與 IIS Web 伺服器不相容。

1. 在 [模擬器] 之下，選擇 [使用 Emulator Express] 選項按鈕。

    ![Emulator Express](./media/vs-azure-tools-emulator-express-debug-run/IC673363.gif)

## 在命令提示字元啟動 Emulator Express

在命令提示字元中，您可以使用 /useemulatorexpress 選項啟動 Azure 計算模擬器的精簡版 (csrun.exe)。

## 限制

使用 Emulator Express 之前，您應注意一些限制：

- Emulator Express 與 IIS Web 伺服器不相容。

- 您的雲端服務可以包含多個角色，但每個角色受限於一個執行個體。

- 您無法存取低於 1000 的連接埠號碼。例如，如果您使用的驗證提供者通常使用低於 1000 的連接埠，您可能需要將此值變更為高於 1000 的連接埠號碼。

- 任何適用於 Azure 計算模擬器的限制也適用於 Emulator Express。例如，每一個部署不能有 50 個以上的角色執行個體。請參閱[在計算模擬器中執行 Azure 應用程式](http://go.microsoft.com/fwlink/p/?LinkId=623050)

## 後續步驟

[偵錯雲端服務](https://msdn.microsoft.com/library/azure/ee405479.aspx)

<!---HONumber=Nov15_HO3-->