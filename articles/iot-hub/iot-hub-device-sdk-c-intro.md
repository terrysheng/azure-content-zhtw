<properties
	pageTitle="Azure IoT 裝置 SDK (適用於 C) 簡介"
	description="深入了解並開始使用 Azure IoT 裝置 SDK (適用於 C) 中的範例程式碼"
	services="iot-hub"
	documentationCenter="na"
	authors="MichelBarnett"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="na"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="tbd"
     ms.date="09/30/2015"
     ms.author="michelb"/>

# Azure IoT 裝置 SDK (適用於 C) 簡介

「Azure IoT 裝置 SDK」是一組程式庫，設計用來簡化從 **Azure IoT 中樞** 服務傳送事件和接收訊息的程序。有各種不同的 SDK，而每個 SDK 會以特定平台為目標，但在這篇文章將著重於 **Azure IoT 裝置 SDK (適用於 C)**。

Azure IoT 裝置 SDK (適用於 C) 是以 ANSI C (C99) 撰寫，以獲得最大可攜性。如此就很適合在數個平台和裝置上運作 - 尤其是在優先將磁碟和記憶體使用量降至最低的情況下。

SDK 已在各種平台上進行測試 (如需詳細資訊，請參閱 [SDK 文件](https://github.com/Azure/azure-iot-sdks/tree/master/c))。雖然本文包含在 *Windows* 平台上執行範例程式碼上的逐步解說，但請記住，以下所述的程式碼在各種支援的平台上完全相同。

本文將介紹 Azure IoT 裝置 SDK (適用於 C) 的架構。我們將示範如何初始化裝置程式庫，將事件傳送到 IoT 中樞，以及從 IoT 中樞接收訊息。本文中的資訊應足以讓您開始使用 SDK，但也提供給您可以取得程式庫的其他資訊的指標。


## SDK 架構

您可以在下列 GitHub 儲存機制中找到 **Azure IoT 裝置 SDK (適用於 C)**︰

[azure-iot-sdks](https://github.com/Azure/azure-iot-sdks)

在此儲存機制的 **master** 分支中可找到最新版的程式庫：

  ![](media/iot-hub-device-sdk-c-intro/01-MasterBranch.PNG)

此儲存機制包含整個系列的 Azure IoT 裝置 SDK。但這篇文章是關於可在 **c** 資料夾中找到的 Azure IoT 裝置 SDK *for C*。

  ![](media/iot-hub-device-sdk-c-intro/02-CFolder.PNG)

在儲存機制的 **common**、**iothub\_client** 和 **serializer** 資料夾中可找到 SDK 的核心實作。**common** 資料夾包含所有程式庫使用的共用程式碼 (通常您不會直接使用 **common** 資料夾中的程式碼)。不過，**iothub\_client** 和 **serializer** 資料夾包含兩個您將使用於程式碼中的不同 SDK 層實作。

-   **IoTHubClient** - **iothub\_client** 資料夾包含 SDK (**IoTHubClient** 程式庫) 中最低 API 層的實作。**IoTHubClient** 程式庫包含用於將事件傳送到 IoT 中樞以及從 IoT 中樞接收訊息的 API。如果您使用此程式庫，則需負責實作訊息序列化，但會為您處理與 IoT 中樞通訊的其他細節。

-   **serializer** - **serializer** 資料夾包含 **serializer** 程式庫的實作。**serializer** 程式庫在 **IoTHubClient** 所提供的功能之上加入模型化功能。如果您使用 **serializer** 程式庫，您首先會定義一個模型，以指定您要傳送至 IoT 中樞的事件以及您期望從 IoT 中樞收到的訊息。但是定義此模型後，SDK 會提供 API 介面給您，讓您輕鬆地使用事件和訊息，而不需擔心序列化細節。

查看一些範例程式碼，即可輕易了解這一切。下列各節將為您逐步解說 SDK 中包含的幾個範例應用程式。這應可讓您輕鬆了解 SDK 架構層的各種功能以及 API 運作方式的簡介。

## 執行範例之前

您必須先完成兩項工作，才可以在 Azure IoT 裝置 SDK (適用於 C) 中執行範例：準備開發環境和取得裝置認證。SDK 隨附的[讀我檔案](https://github.com/Azure/azure-iot-sdks/tree/master/c)會提供這兩項工作的指示。但下列各節包含有關這些指示的一些額外評論。

### 準備開發環境

首先，您必須從 GitHub 取得 SDK 的複本，然後建立來源。您應從 GitHub 儲存機制的 **master** 分支擷取來源的複本：

<https://github.com/Azure/azure-iot-sdks>

當您下載來源的複本後，您需要先完成一些步驟才能執行您的程式碼。在標題為[準備開發環境](https://github.com/Azure/azure-iot-sdks/blob/master/c/doc/devbox_setup.md)的 SDK 文件中可找到這些步驟的摘要。您必須為 Windows 執行的大部分工作，都牽涉到[準備 QPID Proton 程式庫](https://github.com/Azure/azure-iot-sdks/blob/master/c/doc/devbox_setup.md#preparing-qpid-proton-libraries-in-windows)和[驗證您的環境](https://github.com/Azure/azure-iot-sdks/blob/master/c/doc/devbox_setup.md#verify-your-environment)。但以下有一些秘訣可幫助您完成準備指南中所描述的程序：

-   當您建立 **PROTON\_PATH** 環境變數時，讓它成為**系統**環境變數，如下所示：

  ![](media/iot-hub-device-sdk-c-intro/07-EnvironmentVariables.PNG)

-   當您安裝 **CMake** 公用程式時，請選擇將 **CMake** 新增至**所有使用者**之系統 PATH 的選項 (新增至**目前使用者**也可行)︰

  ![](media/iot-hub-device-sdk-c-intro/08-CMake.PNG)

-   務必安裝正確的 Python 版本。通常在 Windows 中，您應該安裝 x86 版本︰

  ![](media/iot-hub-device-sdk-c-intro/09-Python.PNG)

您可以使用安裝程式中的預設選項。

- 將 Python 目錄新增至系統 **PATH** 環境變數。在 Windows 上，畫面如下所示：![](media/iot-hub-device-sdk-c-intro/16-PythonPath.PNG)

-   在您開啟 [VS2015 的開發人員命令提示字元] 之前，請先安裝 Git 命令列工具。若要安裝這些工具，請完成下列步驟：

	1. 啟動 **Visual Studio 2015** 安裝程式 (或從 [程式和功能] 控制台選擇 **Microsoft Visual Studio 2015**，然後選取 [變更])。
	2. 請確定已在安裝程式中選取 [適用於 Windows 的 Git] 功能，但您也可以核取 [適用於 Visual Studio 的 GitHub 延伸模組] 選項，以提供 IDE 整合：![](media/iot-hub-device-sdk-c-intro/10-GitTools.PNG)
	3. 完成安裝精靈來安裝工具。
	4. 將 Git 工具 **bin** 目錄新增至系統 **PATH** 環境變數。在 Windows 上，畫面如下所示：![](media/iot-hub-device-sdk-c-intro/11-GitToolsPath.PNG)

當您從命令提示字元執行 **build\_proton.cmd** 指令碼時，此指令碼會從其 GitHub 儲存機制提取 Proton 來源，然後建立 Proton 程式庫。

如果您要執行 MQTT 範例，您可以依照 SDK 文件中的[建置 Apache MQTT 程式庫](https://github.com/Azure/azure-iot-sdks/blob/master/c/doc/devbox_setup.md#building-the-apache-mqtt-library-in-windows)的指示操作。無論如何，最後您都必須要執行 **build.cmd**。

完成上述步驟後，您就可以開始編譯範例應用程式。

### 取得裝置認證

現在已設定好您的開發環境，最後一件事就是取得一組裝置認證。若要讓裝置能存取 IoT 中樞，您必須先將此裝置加入至 IoT 中樞裝置登錄。當您加入您的裝置時，您會取得一組所需的裝置認證，以便裝置能夠連線到 IoT 中樞。我們在下一節中看到的範例應用程式預期這些認證的形式為**裝置連接字串**。

[裝置總管](https://github.com/Azure/azure-iot-sdks/tree/master/tools/DeviceExplorer)工具 (包括 Azure IoT 裝置 SDK) 會使用 Azure IoT 服務程式庫在 IoT 中樞執行各種功能 — 包括新增裝置。如果您使用 [裝置總管] 來新增裝置，您會得到對應的連接字串，而您需要此連接字串才能執行範例應用程式。

萬一您不熟悉此程序，下列程序說明如何使用 [裝置總管] 來新增裝置和取得裝置連接字串。

在 **Visual Studio 2015** 中開啟 **[DeviceExplorer.sln](https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/DeviceExplorer.sln)** 並建置解決方案。當您執行程式時，您會看到此介面：

  ![](media/iot-hub-device-sdk-c-intro/03-DeviceExplorer.PNG)

在第一個欄位中輸入您的 [IoT 中樞連接字串]，然後按一下 [更新] 按鈕。這可設定此工具，以便與 IoT 中樞通訊。

設定 IoT 中樞連接字串後，按一下 [管理] 索引標籤：

  ![](media/iot-hub-device-sdk-c-intro/04-ManagementTab.PNG)

您將在其中管理在 IoT 中樞註冊的裝置。

按一下 [建立] 按鈕即可建立裝置。隨即顯示已填妥一組金鑰 (主要和次要) 的對話方塊。您只需要輸入 [裝置識別碼]，然後按一下 [建立] 按鈕。

  ![](media/iot-hub-device-sdk-c-intro/05-CreateDevice.PNG)

一旦建立裝置，就會以所有註冊的裝置 (包括您剛才建立的裝置) 更新裝置清單。如果您在新裝置上按一下滑鼠右鍵，您就會看到此功能表︰

  ![](media/iot-hub-device-sdk-c-intro/06-RightClickDevice.PNG)

如果您選擇 [複製所選裝置的連接字串] 選項，您的裝置的連接字串就會複製到剪貼簿。保留一份複本。您在執行如下所述的範例應用程式時會需要它。

完成上述步驟後，您就可以開始執行一些程式碼。以下所述的兩個範例在主要原始程式檔之上有一個常數，可讓您輸入連接字串。例如，以下是 **iothub\_client\_sample\_amqp** 應用程式中的對應行。

```
static const char* connectionString = "[device connection string]";
```

如果您想要遵循，請在此輸入您的裝置連接字串，重新編譯解決方案，您將能夠執行範例。

## IoTHubClient

在 azure-iot-sdks 儲存機制的 **iothub\_client** 資料夾中，有一個包含 **iothub\_client\_sample\_amqp** 應用程式的 **samples** 資料夾。

Windows 版本的 **iothub\_client\_sample\_ampq** 應用程式包含下列 Visual Studio 解決方案：

  ![](media/iot-hub-device-sdk-c-intro/12-iothub-client-sample-amqp.PNG)

此解決方案內含單一專案：但值得注意的是，此此解決方案中安裝了四個 GitHub 套件：

  ![](media/iot-hub-device-sdk-c-intro/17-iothub-client-sample-amqp-githubpackages.PNG)

您在使用 SDK 時一律需要 **Microsoft.Azure.IoTHub.Common** 套件。由於此範例依賴 AMQP，因此我們也必須納入 **Apache.QPID.Proton.AzureIoT** 和 **Microsoft.Azure.IoTHub.AmqpTransport** 套件 (HTTP 有對等套件)。此外，此範例使用 **IoTHubClient** 程式庫，因此我們也必須在解決方案中納入 **Microsoft.Azure.IoTHub.IoTHubClient** 專案。

您可以在 **iothub\_client\_sample\_amqp.c** 原始程式檔中找到範例應用程式的實作：

  ![](media/iot-hub-device-sdk-c-intro/13-iothub_client_sample_amqp_c.PNG)

我們將使用此範例應用程式來逐步解說使用 **IoTHubClient** 程式庫時所需的項目。


### 初始化程式庫

您必須先配置 IoT 中樞用戶端控制代碼，才可以開始使用程式庫︰

```
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

請注意，我們正將我們的裝置連接字串複本傳遞至此函式 (我們從 [裝置總管] 取得的函式)。我們也會指定我們想要使用的通訊協定 — 我們在此範例中使用 AMQP，但也可以選取 HTTP。

當您具有有效的 **IOTHUB\_CLIENT\_HANDLE** 時，您可以開始呼叫 API 來傳送事件和從 IoT 中樞接收訊息。我們會接著進行探討。

### 傳送事件

將事件傳送到 IoT 中樞時，您需要完成下列步驟：

首先，建立一則訊息：

```
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Message_%d_From_IoTHubClient_Over_AMQP", i);
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText);
```

接著，傳送此訊息：

```
IoTHubClient_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message);
```

每次傳送訊息時，您會指定傳送資料時所叫用的回呼函式的參考：

```
static void SendConfirmationCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    EVENT_INSTANCE* eventInstance = (EVENT_INSTANCE*)userContextCallback;
    (void)printf("Confirmation[%d] received for message tracking id = %d with result = %s\r\n", callbackCounter, eventInstance->messageTrackingId, ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
    /* Some device specific action code goes here... */
    callbackCounter++;
    IoTHubMessage_Destroy(eventInstance->messageHandle);
}
```

請注意在您完成訊息時所呼叫的 **IoTHubMessage\_Destroy** 函式。您必須這麼做，才可以釋放在建立訊息時配置的資源。

### 接收訊息

接收訊息是非同步作業。首先，您會登錄在裝置接收訊息時所要叫用的回呼：

```
int receiveContext = 0;
IoTHubClient_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext);
```

最後一個參數是您所要項目的無效指標。在範例中，這是一個整數的指標，但可能是更複雜的資料結構的指標。這可讓回呼函式與此函式的呼叫者以共用狀態運作。

當裝置接收訊息時，會叫用登錄的回呼函式：

```
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    int* counter = (int*)userContextCallback;
    const char* buffer;
    size_t size;
    if (IoTHubMessage_GetByteArray(message, (const unsigned char**)&buffer, &size) == IOTHUB_MESSAGE_OK)
    {
        (void)printf("Received Message [%d] with Data: <<<%.*s>>> & Size=%d\r\n", *counter, (int)size, buffer, (int)size);
    }

    /* Some device specific action code goes here... */
    (*counter)++;
    return IOTHUBMESSAGE_ACCEPTED;
}
```

請注意您如何使用 **IoTHubMessage\_GetByteArray** 函式來擷取訊息 (在此範例中是一個字串)。

### 解除初始化程式庫

當您完成事件傳送和訊息接收時，您可以解除初始化 IoT 程式庫 — 使用下列函式呼叫即可完成：

```
IoTHubClient_Destroy(iotHubClientHandle);
```

這會釋放 **IoTHubClient\_CreateFromConnectionString** 函式先前所配置的資源。

如您所見，使用 **IoTHubClient** 程式庫可以輕鬆傳送事件和接收訊息。程式庫會負責與 IoT 中樞進行通訊的細節 — 包括要使用哪個通訊協定 (從開發人員的觀點來看，這是一個簡單的設定選項)。

**IoTHubClient** 程式庫也可讓您精確地控制如何序列化您的裝置傳送到 IoT 中樞的事件。在某些情況下，這是一項優點，但在其他情況下，這是您不想要參與的實作細節。如果是這樣，您可以考慮使用接下來我們將討論的 **serializer** 程式庫。

## 序列化程式

在概念上，**serializer** 程式庫位於 SDK 中的 **IoTHubClient** 程式庫之上。它會使用 **IoTHubClient** 程式庫與 IoT 中樞進行基礎通訊，但是會新增模型化功能，以減輕開發人員處理訊息序列化的負擔。此程式庫的運作方式最好是由範例示範。

在 azure-iot-sdks 儲存機制的 **serializer** 資料夾中，有一個包含 **simplesample\_amqp** 應用程式的 **samples** 資料夾。Windows 版本的這個範例包含下列 Visual Studio 解決方案：

  ![](media/iot-hub-device-sdk-c-intro/14-simplesample_amqp.PNG)

如同先前的範例，此範例也包含數個 NuGet 套件：

  ![](media/iot-hub-device-sdk-c-intro/18-simplesample_amqp-githubpackages.PNG)

這些我們在先前範例中大多都已看過，但 **Microsoft.Azure.IoTHub.Serializer** 是新的 - 我們在使用 **serializer** 程式庫時將會用到。

您可以在 **simplesample\_amqp.c** 程式碼檔案中找到範例應用程式的實作：

  ![](media/iot-hub-device-sdk-c-intro/15-simplesample_amqp_c.PNG)

下列各節將逐步解說此範例的重要部分。

### 初始化程式庫

您必須先呼叫初始化 API，才可以開始使用 **serializer** 程式庫︰

```
serializer_init(NULL);

IOTHUB_CLIENT_HANDLE iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);

ContosoAnemometer* myWeather = CREATE_MODEL_INSTANCE(WeatherStation, ContosoAnemometer);
```

呼叫 **serializer\_init** 函式是用來初始化基礎程式庫的一次性呼叫。然後您會呼叫 **IoTHubClient\_CreateFromConnectionString** 函式，這就是我們在 **IoTHubClient** 範例中看到的相同 API。此呼叫會設定您的裝置連接字串 (這也可用來選擇您要使用的通訊協定)。請注意，此範例使用 AMQP 做為傳輸方式，但我們也可以使用 HTTP。

最後，呼叫 **CREATE\_MODEL\_INSTANCE** 函式。請注意，**WeatherStation** 是模型的命名空間，而 **ContosoAnemometer** 是模型的名稱。建立模型執行個體後，您可以使用它來傳送事件和接收訊息。但首先，請務必了解模型是什麼。

### 定義模型

**serializer** 程式庫中的模型會定義您的裝置可傳送至 IoT 中樞的事件以及可接收的訊息 (在模組化語言中稱為*動作*)。您可使用如 **simplesample\_amqp** 範例應用程式中的一組 C 巨集來定義模型︰

```
BEGIN_NAMESPACE(WeatherStation);

DECLARE_MODEL(ContosoAnemometer,
WITH_DATA(ascii_char_ptr, DeviceId),
WITH_DATA(double, WindSpeed),
WITH_ACTION(TurnFanOn),
WITH_ACTION(TurnFanOff),
WITH_ACTION(SetAirResistance, int, Position)
);

END_NAMESPACE(WeatherStation);
```

**BEGIN\_NAMESPACE** 和 **END\_NAMESPACE** 這兩個巨集都會以模型的命名空間做為引數。介於這兩個巨集之間的項目應該就是您的模型的定義和模型所使用的資料結構。

在此範例中，有一個名為 **ContosoAnemometer** 的模型。此模型會定義您的裝置可以傳送到 IoT 中樞的兩個事件︰**DeviceId** 和 **WindSpeed**。它也會定義您的裝置可以接收的三個動作 (訊息)：**TurnFanOn**、**TurnFanOff** 和 **SetAirResistance**。每個事件都有類型，而每個動作都有名稱 (以及一組選擇性的參數)。

模型中定義事件和動作可定義 API 介面，以便用來將事件傳送到 IoT 中樞以及回應傳送至裝置的訊息。最好能透過範例了解相關情況。

### 傳送事件

此模型會定義您可以傳送到 IoT 中樞的事件。在此範例中，這表示使用 **WITH\_DATA** 巨集定義的兩個事件之一。例如，如果您要將 **WindSpeed** 事件傳送到 IoT 中樞，則必須執行下列幾個步驟。第一個步驟是設定我們要傳送的資料：

```
myWeather->WindSpeed = 15;
```

我們先前定義的模型讓我們只需設定結構的成員，即可這麼做。接著，我們必須將想要傳送的事件序列化：

```
unsigned char* destination;
size_t destinationSize;

SERIALIZE(&destination, &destinationSize, myWeather->WindSpeed);
```

此程式碼會將此事件序列化至緩衝區 (由 **destination** 參考)。最後，我們會使用此程式碼將事件傳送到 IoT 中樞︰

```
sendMessage(iotHubClientHandle, destination, destinationSize);
```

在範例應用程式中，這是將已序列化的事件傳送到 IoT 中樞的協助程式函式：

```
static void sendMessage(IOTHUB_CLIENT_HANDLE iotHubClientHandle, const unsigned char* buffer, size_t size)
{
    static unsigned int messageTrackingId;
    IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromByteArray(buffer, size);
    if (messageHandle != NULL)
    {
        if (IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)(uintptr_t)messageTrackingId) != IOTHUB_CLIENT_OK)
        {
            printf("failed to hand over the message to IoTHubClient");
        }
        else
        {
            printf("IoTHubClient accepted the message for delivery\r\n");
        }

        IoTHubMessage_Destroy(messageHandle);
    }
    free((void*)buffer);
    messageTrackingId++;
}
```

此程式碼非常類似我們在 **iothub\_client\_sample\_amqp** 應用程式中看到的程式碼，在其中我們從位元組陣列建立了一則訊息並使用 **IoTHubClient\_SendEventAsync** 將它傳送至 IoT 中樞。之後，我們只需釋放我們先前配置的訊息控制代碼和已序列化的資料緩衝區。

**IoTHubClient\_SendEventAsync** 的倒數第二個參數可參考成功傳送資料時所呼叫的回呼函式。以下是回呼函式的範例︰

```
void sendCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    int messageTrackingId = (intptr_t)userContextCallback;

    (void)printf("Message Id: %d Received.\r\n", messageTrackingId);

    (void)printf("Result Call Back Called! Result is: %s \r\n", ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
}
```

第二個參數是使用者內容的指標 — 我們傳遞至 **IoTHubClient\_SendEventAsync** 的相同指標。在此情況下，此內容是一個簡易計數器，但它可能是您想要的任何項目。

傳送事件就是這麼簡單。最後只剩下說明如何接收訊息。

### 接收訊息

接收訊息的方式類似於在 **IoTHubClient** 程式庫中使用訊息的方式。首先，登錄訊息回呼函式：

```
IoTHubClient_SetMessageCallback(iotHubClientHandle, IoTHubMessage, myWeather)
```

然後撰寫在接收訊息時所叫用的回呼函式：

```
static IOTHUBMESSAGE_DISPOSITION_RESULT IoTHubMessage(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    IOTHUBMESSAGE_DISPOSITION_RESULT result;
    const unsigned char* buffer;
    size_t size;
    if (IoTHubMessage_GetByteArray(message, &buffer, &size) != IOTHUB_MESSAGE_OK)
    {
        printf("unable to IoTHubMessage_GetByteArray\r\n");
        result = EXECUTE_COMMAND_ERROR;
    }
    else
    {
        /*buffer is not zero terminated*/
        char* temp = malloc(size + 1);
        if (temp == NULL)
        {
            printf("failed to malloc\r\n");
            result = EXECUTE_COMMAND_ERROR;
        }
        else
        {
            memcpy(temp, buffer, size);
            temp[size] = '\0';
            EXECUTE_COMMAND_RESULT executeCommandResult = EXECUTE_COMMAND(userContextCallback, temp);
            result =
                (executeCommandResult == EXECUTE_COMMAND_ERROR) ? IOTHUBMESSAGE_ABANDONED :
                (executeCommandResult == EXECUTE_COMMAND_SUCCESS) ? IOTHUBMESSAGE_ACCEPTED :
                IOTHUBMESSAGE_REJECTED;
            free(temp);
        }
    }
    return result;
}
```

此程式碼會重複使用 - 對任何解決方案而言都一樣。此函式會接收訊息並透過呼叫 **EXECUTE\_COMMAND** 負責將它路由傳送至適當的函式。基本上，此時會呼叫的函式取決於模型中的動作定義。

當您在模型中定義動作時，您必須實作在裝置接收對應的訊息時所呼叫的函式。例如，如果您的模型定義這項動作：

```
WITH_ACTION(SetAirResistance, int, Position)
```

您必須使用此簽章定義函式：

```
EXECUTE_COMMAND_RESULT SetAirResistance(ContosoAnemometer* device, int Position)
{
    (void)device;
    (void)printf("Setting Air Resistance Position to %d.\r\n", Position);
    return EXECUTE_COMMAND_SUCCESS;
}
```

請注意，函式的名稱會符合模型中的動作名稱，而函式的參數會符合為此動作指定的參數。第一個參數是必要參數，含有我們的模型執行個體的指標。

當裝置收到符合此簽章的訊息時，就會呼叫對應的函式。所以除了必須包含 **IoTHubMessage** 中重複使用的程式碼，接收訊息只需為模型中定義的每個動作定義簡單函式。

### 解除初始化程式庫

當您完成資料傳送和訊息接收時，您可以解除初始化 IoT 程式庫：

```
        DESTROY_MODEL_INSTANCE(myWeather);
    }
    IoTHubClient_Destroy(iotHubClientHandle);
}
serializer_deinit();
```

上述三個函式均符合先前所述的三個初始化函式。呼叫這些 API 可確保您釋放先前配置的資源。

## 後續步驟

本文涵蓋使用 **Azure IoT 裝置 SDK (適用於 C)** 中的程式庫的基本概念。這應可提供足夠的資訊，讓您了解 SDK 中包含什麼、其架構，以及如何開始使用 Windows 範例。下一篇文章藉由說明 [IoTHubClient 程式庫的相關資訊](iot-hub-device-sdk-c-iothubclient.md)來繼續說明 SDK。

<!---HONumber=Oct15_HO2-->