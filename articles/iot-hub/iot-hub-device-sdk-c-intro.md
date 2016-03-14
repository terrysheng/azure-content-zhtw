<properties
	pageTitle="使用適用於 C 的 Azure IoT 裝置 SDK | Microsoft Azure"
	description="深入了解並開始使用 Azure IoT 裝置 SDK (適用於 C) 中的範例程式碼。"
	services="iot-hub"
	documentationCenter=""
	authors="olivierbloch"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="cpp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="02/23/2016"
     ms.author="obloch"/>

# Azure IoT 裝置 SDK (適用於 C) 簡介

「Azure IoT 裝置 SDK」是一組程式庫，設計用來簡化從 **Azure IoT 中樞** 服務傳送事件和接收訊息的程序。有各種不同的 SDK，每個 SDK 都以特定的平台為目標，而本文將說明的是「Azure IoT 裝置 SDK (適用於 C)」。

Azure IoT 裝置 SDK (適用於 C) 是以 ANSI C (C99) 撰寫，以獲得最大可攜性。如此就很適合在一些平台和裝置上運作 - 尤其是在以將磁碟和記憶體使用量降至最低做為優先考量的情況下。

已經有許多平台經過 SDK 測試 (如需詳細資料，請參閱[平台和相容性清單](iot-hub-tested-configurations.md))。雖然本文包含在 Windows 平台上執行之範例程式碼的逐步解說，但請記住，本文所述的程式碼在各種支援的平台上都完全相同。

本文將介紹 Azure IoT 裝置 SDK (適用於 C) 的架構。我們將示範如何初始化裝置程式庫，將事件傳送到 IoT 中樞，以及從 IoT 中樞接收訊息。本文中的資訊應足以讓您開始使用 SDK，但也提供了可取得程式庫其他相關資訊的指標。

## SDK 架構

您可以在下列 GitHub 儲存機制中找到 **Azure IoT 裝置 SDK (適用於 C)**︰

[azure-iot-sdks](https://github.com/Azure/azure-iot-sdks)

在此儲存機制的 **master** 分支中可找到最新版的程式庫：

  ![](media/iot-hub-device-sdk-c-intro/01-MasterBranch.PNG)

此儲存機制包含整個系列的 Azure IoT 裝置 SDK。不過，本文是關於「Azure IoT 裝置 SDK (適用於 *C*)」(可在 **c** 資料夾中找到)。

  ![](media/iot-hub-device-sdk-c-intro/02-CFolder.PNG)

* SDK 核心實作可以在 [iothub\_client] 資料夾中找到，這個資料夾包含了 SDK 中最低 API 層級的實作：**IoTHubClient** 程式庫。**IoTHubClient** 程式庫包含了實作將訊息傳送至 IoT 中樞以及從 IoT 中樞接收訊息之原始傳訊的 API。使用此程式庫時，您必須負責實作訊息序列化 (最終會使用如下所述的序列化程式範例)，但會為您處理其他與 IoT 中樞通訊的詳細資料。
* [serializer] 資料夾包含 helper 函式和示範如何在資料傳送至 Azure IoT 中樞之前，使用用戶端程式庫將資料序列化的範例。請注意，使用序列化程式並非必要，而且只提供您方便使用。如果您使用 **serializer** 程式庫，您首先會定義一個模型，以指定您要傳送至 IoT 中樞的事件以及您期望從 IoT 中樞收到的訊息。一旦定義此模型之後，SDK 會提供您一個可讓您輕鬆處理事件與訊息的 API 介面，而不用擔心該程式庫所相依之其他使用數個通訊協定 (AMQP, MQTT) 實作傳輸之開放原始碼程式庫的序列化詳細資料。
* **IoTHubClient** 程式庫相依於其他開放原始碼程式庫：
   * [Azure C 共用公用程式](https://github.com/Azure/azure-c-shared-utility)程式庫，提供了跨越數個 Azure 相關 C SDK 所需要之基本工作 (例如字串、清單管理、IO 等...) 的常用功能
   * [Azure uAMQP](https://github.com/Azure/azure-uamqp-c) 程式庫，這是針對資源條件約束裝置最佳化的 AMQP 用戶端端實作。
   * [Azure uMQTT](https://github.com/Azure/azure-umqtt-c) 程式庫，這是實作 MQTT 通訊協定，並針對資源條件約束裝置最佳化的一般用途程式庫。

查看範例程式碼可以較容易了解這一切。下列各節將為您逐步解說 SDK 中包含的幾個範例應用程式。這應可讓您輕鬆了解 SDK 架構層的各種功能以及 API 運作方式的簡介。

## 執行範例之前

在您可以執行適用於 C 的 Azure IoT 裝置 SDK 中的範例之前，如果您還沒有該服務的執行個體，您必須在您的 Azure 訂用帳戶上建立一個並完成 2 項工作：
* 準備您的開發環境
* 取得裝置認證。

如果您需要在您的 Azure 訂用帳戶上建立一個 Azure IoT 中樞執行個體，請遵循[這裡](https://github.com/Azure/azure-iot-sdks/blob/master/doc/setup_iothub.md)的指示。

SDK 隨附的[讀我檔案](https://github.com/Azure/azure-iot-sdks/tree/master/c)提供了準備開發環境，並取得裝置認證所需的指示。下列各節包含有關這些指示的一些額外評論。

### 準備開發環境

針對某些平台 (例如適用於 Windows 的 NuGet 或適用於 Debian 和 Ubuntu 的 apt\_get) 提供封裝，且範例會在這些封裝可用時使用的同時，下面的指示會詳細說明如何直接從程式碼建置程式庫和範例。

首先，您必須從 GitHub 取得 SDK 的複本，然後建立來源。您應該從 [GitHub 儲存機制](https://github.com/Azure/azure-iot-sdks)的 **master** 分支取得一份原始檔複本：

下載來源的複本後，您必須完成 SDK 文章 [Prepare your development environment (準備開發環境)](https://github.com/Azure/azure-iot-sdks/blob/master/c/doc/devbox_setup.md) 中所述的步驟。


以下是一些秘訣，可幫助您完成準備指南中所描述的程序：

-   當您安裝 **CMake** 公用程式時，請選擇將 **CMake** 新增至**所有使用者**之系統 PATH 的選項 (新增至**目前使用者**也可行)︰

  ![](media/iot-hub-device-sdk-c-intro/08-CMake.PNG)


-   在您開啟 [VS2015 開發人員命令提示字元] 之前，請先安裝 Git 命令列工具。若要安裝這些工具，請完成下列步驟：

	1. 啟動 **Visual Studio 2015** 安裝程式 (或從 [程式和功能] 控制台選擇 **Microsoft Visual Studio 2015**，然後選取 [變更])。
	
	2. 確定在安裝程式中已選取 [Git for Windows] 功能，但您也可以核取 [Visual Studio 的 GitHub 擴充] 選項以提供整合式開發環境 (IDE) 整合：

  		![](media/iot-hub-device-sdk-c-intro/10-GitTools.PNG)

	3. 完成安裝精靈來安裝工具。

	4. 將 Git 工具 **bin** 目錄新增至系統 **PATH** 環境變數。在 Windows 上，畫面如下所示：

  		![](media/iot-hub-device-sdk-c-intro/11-GitToolsPath.PNG)


當您已經完成 [Prepare your development environment (準備開發環境)](https://github.com/Azure/azure-iot-sdks/blob/master/c/doc/devbox_setup.md) 頁面中所述的所有步驟，您就準備好編譯範例應用程式。

### 取得裝置認證

現在已設定好您的開發環境，下一件事就是取得一組裝置認證。若要讓裝置能夠存取 IoT 中樞，您必須先將該裝置新增至 IoT 中樞裝置登錄。當您加入您的裝置時，您會取得一組所需的裝置認證，以便裝置能夠連線到 IoT 中樞。我們在下一節中看到的範例應用程式預期這些認證的形式為**裝置連接字串**。

SDK 開放原始碼儲存機制中提供了一些工具，以協助管理 IoT 中樞。一個是稱為「裝置總管」的 Windows 應用程式，另一個是以 node.js 為基礎的跨平台 CLI 工具，稱為 iothub-explorer。您可以在[這裡](https://github.com/Azure/azure-iot-sdks/blob/master/doc/manage_iot_hub.md)深入了解這些工具。

如同本文中我們審查在 Windows 上執行這些範例一樣，我們就是使用「裝置總管」工具。但是如果您偏好使用 CLI 工具，您也可以使用 iothub-explorer。

[裝置總管](https://github.com/Azure/azure-iot-sdks/tree/master/tools/DeviceExplorer)工具會使用 Azure IoT 服務程式庫在 IoT 中樞上執行各種函式，包含新增裝置。如果您使用 [裝置總管] 來新增裝置，您將會得到對應的連接字串。您需要此連接字串才能執行範例應用程式。

萬一您不熟悉此程序，下列程序說明如何使用 [裝置總管] 來新增裝置和取得裝置連接字串。

您可以在 [SDK 版本頁面](https://github.com/Azure/azure-iot-sdks/releases)上尋找「裝置總管」工具的 Windows 安裝程式。但您也可以直接從其程式碼執行此工具，在 **Visual Studio 2015** 中開啟**[DeviceExplorer.sln](https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/DeviceExplorer.sln)** 並建置解決方案。

當您執行程式時，您會看到此介面：

  ![](media/iot-hub-device-sdk-c-intro/03-DeviceExplorer.PNG)

請在第一個欄位中輸入您的 [IoT 中樞連接字串]，然後按一下 [更新]。這可設定此工具，以便與 IoT 中樞通訊。

設定 IoT 中樞連接字串後，請按一下 [管理] 索引標籤：

  ![](media/iot-hub-device-sdk-c-intro/04-ManagementTab.PNG)

您將在其中管理在 IoT 中樞註冊的裝置。

按一下 [建立] 按鈕即可建立裝置。將會顯示一個已預先填入一組金鑰 (主要和次要) 的對話方塊。您只需要輸入 [裝置識別碼]，然後按一下 [建立] 即可。

  ![](media/iot-hub-device-sdk-c-intro/05-CreateDevice.PNG)

建立裝置後，就會以所有註冊的裝置 (包括您剛才建立的裝置) 更新 [裝置] 清單。如果您在新裝置上按一下滑鼠右鍵，您將看到此功能表︰

  ![](media/iot-hub-device-sdk-c-intro/06-RightClickDevice.PNG)

如果您選擇 [複製所選裝置的連接字串] 選項，您裝置的連接字串就會被複製到剪貼簿。請保留一份連接字串複本。在執行後續各節中所述的範例應用程式時，您將會需要它。

完成上述步驟後，您就可以開始執行一些程式碼。兩個範例在主要原始程式檔頂端都有一個常數，此常數可讓您輸入連接字串。例如，**iothub\_client\_sample\_amqp** 應用程式中的對應行如以下所示。

```
static const char* connectionString = "[device connection string]";
```

如果您想要遵循，請在此輸入您的裝置連接字串，重新編譯解決方案，您將能夠執行範例。

## IoTHubClient

在 azure-iot-sdks 儲存機制的 [iothub\_client] 資料夾中，有一個 [samples] 資料夾，當中包含名稱為 **iothub\_client\_sample\_amqp** 的應用程式。

Windows 版本的 **iothub\_client\_sample\_ampq** 應用程式包含下列 Visual Studio 解決方案：

  ![](media/iot-hub-device-sdk-c-intro/12-iothub-client-sample-amqp.PNG)

此解決方案內含單一專案：值得注意的是，此解決方案中安裝了四個 NuGet 套件：

  ![](media/iot-hub-device-sdk-c-intro/17-iothub-client-sample-amqp-githubpackages.PNG)

當您使用 SDK 時，您永遠需要 **Microsoft.Azure.C.SharedUtility** 封裝。由於此範例依賴 AMQP，因此您也必須納入 **Microsoft.Azure.uamqp** 和 **Microsoft.Azure.IoTHub.AmqpTransport** 套件 (HTTP 和 MQTT 有對等套件)。由於此範例使用 **IoTHubClient** 程式庫，因此您也必須在解決方案中納入 **Microsoft.Azure.IoTHub.IoTHubClient** 套件。

您可以在 **iothub\_client\_sample\_amqp.c** 原始程式檔中找到範例應用程式的實作。

我們將使用此範例應用程式來逐步解說使用 **IoTHubClient** 程式庫時所需的項目。

### 初始化程式庫

您必須先配置 IoT 中樞用戶端控制代碼，才可以開始使用程式庫︰

```
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

請注意，我們要將我們的裝置連接字串複本傳遞給此函式 (我們從 [裝置總管] 取得的函式)。我們也會指定我們想要使用的通訊協定。這個範例使用 AMQP，但 MQTT 與 HTTP 也是選項之一。

當您具有有效的 **IOTHUB\_CLIENT\_HANDLE** 時，您可以開始呼叫 API 來傳送事件和從 IoT 中樞接收訊息。我們會接著進行探討。

### 傳送事件

您必須完成下列步驟，才能將事件傳送到 IoT 中樞：

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

處理完訊息時，請注意對 **IoTHubMessage\_Destroy** 函式的呼叫。您必須進行此呼叫，才能釋放在建立訊息時所配置的資源。

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

請注意，您需使用 **IoTHubMessage\_GetByteArray** 函式來擷取訊息 (在此範例中是一個字串)。

### 解除初始化程式庫

當您完成事件傳送和訊息接收時，您可以將 IoT 程式庫解除初始化。若要這麼做，請發出下列函式呼叫：

```
IoTHubClient_Destroy(iotHubClientHandle);
```

這會釋放 **IoTHubClient\_CreateFromConnectionString** 函式先前所配置的資源。

如您所見，使用 **IoTHubClient** 程式庫可以輕鬆傳送事件和接收訊息。此程式庫會處理與 IoT 中樞進行的通訊細節，包括要使用哪個通訊協定 (從開發人員的觀點來看，這是一個簡單的設定選項)。

在如何對您裝置傳送到 IoT 中樞的事件進行序列化方面，**IoTHubClient** 程式庫也可提供精確的控制。在某些情況下，這是一項優點，但在其他情況下，這是您不想要參與的實作細節。如果是這樣，您可以考慮使用我們將在下一節中說明的「序列化程式」程式庫。

## 序列化程式

在概念上，**serializer** 程式庫位於 SDK 中的 **IoTHubClient** 程式庫之上。它會使用 **IoTHubClient** 程式庫與 IoT 中樞進行基礎通訊，但是會新增模型化功能，以減輕開發人員處理訊息序列化的負擔。此程式庫的運作方式最好是由範例示範。

在 azure-iot-sdks 儲存機制的 **serializer** 資料夾中，有一個包含 **simplesample\_amqp** 應用程式的 **samples** 資料夾。Windows 版本的這個範例包含下列 Visual Studio 解決方案：

  ![](media/iot-hub-device-sdk-c-intro/14-simplesample_amqp.PNG)

如同先前的範例，此範例也包含數個 NuGet 套件：

  ![](media/iot-hub-device-sdk-c-intro/18-simplesample_amqp-githubpackages.PNG)

這當中的大部分，我們在先前的範例中都已看過，但 **Microsoft.Azure.IoTHub.Serializer** 是新的。我們在使用 **serializer** 程式庫時將會用到。

您可以在 **simplesample\_amqp.c** 檔案中找到範例應用程式的實作。

下列各節將逐步解說此範例的重要部分。

### 初始化程式庫

您必須先呼叫初始化 API，才可以開始使用 **serializer** 程式庫︰

```
serializer_init(NULL);

IOTHUB_CLIENT_HANDLE iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);

ContosoAnemometer* myWeather = CREATE_MODEL_INSTANCE(WeatherStation, ContosoAnemometer);
```

對 **serializer\_init** 函式進行的呼叫是一個單次呼叫，可用來將基礎程式庫初始化。然後，您需呼叫 **IoTHubClient\_CreateFromConnectionString** 函式，這與 **IoTHubClient** 範例中的 API 相同。此呼叫會設定您的裝置連接字串 (這也可用來選擇您要使用的通訊協定)。請注意，此範例使用 AMQP 做為傳輸方式，但可能使用過 HTTP。

最後，呼叫 **CREATE\_MODEL\_INSTANCE** 函式。請注意，**WeatherStation** 是模型的命名空間，而 **ContosoAnemometer** 是模型的名稱。建立模型執行個體後，您便可以使用它來開始傳送事件和接收訊息。不過，請務必了解模型是什麼。

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

模型中定義的事件和動作可定義 API 介面，此介面可供您用來將事件傳送到 IoT 中樞，以及回應傳送至裝置的訊息。最好能透過範例了解相關情況。

### 傳送事件

此模型會定義您可以傳送到 IoT 中樞的事件。在此範例中，是指使用 **WITH\_DATA** 巨集來定義的兩個事件之一。例如，如果您想要將 **WindSpeed** 事件傳送到 IoT 中樞，就必須執行下列幾個步驟。第一個步驟是設定我們要傳送的資料：

```
myWeather->WindSpeed = 15;
```

我們先前定義的模型可讓我們透過設定 **struct** 的成員來達到這個目的。接著，我們要將想要傳送的事件序列化：

```
unsigned char* destination;
size_t destinationSize;

SERIALIZE(&destination, &destinationSize, myWeather->WindSpeed);
```

此程式碼會將此事件序列化至緩衝區 (由 **destination** 參考)。最後，我們會使用下列程式碼將事件傳送到 IoT 中樞︰

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

此程式碼非常類似於我們在 **iothub\_client\_sample\_amqp** 應用程式中看到的程式碼，其中我們是從位元組陣列建立了一則訊息，然後使用 **IoTHubClient\_SendEventAsync** 將它傳送到 IoT 中樞。之後，我們只需釋放我們先前配置的訊息控制代碼和已序列化的資料緩衝區。

**IoTHubClient\_SendEventAsync** 的倒數第二個參數可參考成功傳送資料時所呼叫的回呼函式。以下是回呼函式的範例︰

```
void sendCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    int messageTrackingId = (intptr_t)userContextCallback;

    (void)printf("Message Id: %d Received.\r\n", messageTrackingId);

    (void)printf("Result Call Back Called! Result is: %s \r\n", ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
}
```

第二個參數是使用者內容的指標，即我們傳遞至 **IoTHubClient\_SendEventAsync** 的相同指標。在此案例中，此內容是一個簡易計數器，但它可以是您想要的任何東西。

傳送事件就是這麼簡單。最後只剩下說明如何接收訊息。

### 接收訊息

接收訊息的方式類似於在 **IoTHubClient** 程式庫中使用訊息的方式。首先，您需登錄訊息回呼函式：

```
IoTHubClient_SetMessageCallback(iotHubClientHandle, IoTHubMessage, myWeather)
```

然後，撰寫在接收訊息時所叫用的回呼函式：

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

此程式碼會重複使用 - 對任何解決方案而言都一樣。此函式會接收訊息並透過呼叫 **EXECUTE\_COMMAND** 負責將它路由傳送至適當的函式。此時所呼叫的函式取決於模型中的動作定義。

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

請注意，函式的名稱會與模型中的動作名稱相符，而函式的參數則會與為此動作指定的參數相符。第一個參數是必要參數，含有我們的模型執行個體的指標。

當裝置收到符合此簽章的訊息時，就會呼叫對應的函式。因此，除了必須包含 **IoTHubMessage** 中重複使用的程式碼之外，接收訊息所涉及的只有為模型中定義的每個動作定義一個簡單的函式。

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

本文涵蓋使用「Azure IoT 裝置 SDK (適用於 C)」中程式庫的基本概念。這提供您足夠的資訊來了解 SDK 中包含什麼、其架構，以及如何開始使用 Windows 範例。下一篇文章藉由說明 [IoTHubClient 程式庫的相關資訊](iot-hub-device-sdk-c-iothubclient.md)來繼續說明 SDK。

<!---HONumber=AcomDC_0302_2016-->