<properties
	pageTitle="適用於 C 的 Azure IoT 裝置 SDK - 序列化程式 | Microsoft Azure"
	description="深入了解在適用於 C 的 Azure IoT 裝置 SDK 中使用序列化程式程式庫"
	services="iot-hub"
	documentationCenter=""
	authors="MichelBarnett"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="cpp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/29/2015"
     ms.author="michelb"/>

# Microsoft Azure IoT 裝置 SDK for C – 深入了解序列化程式

本系列的[第一篇文章](iot-hub-device-sdk-c-intro.md)介紹 **Azure IoT 裝置 SDK for C**。下一篇文章提供 [**IoTHubClient**](iot-hub-device-sdk-c-iothubclient.md) 更詳細的描述。在本文中，我們將藉由提供最後這個元件：**序列化程式**程式庫的更詳細描述，來完成 SDK 的涵蓋範圍。

在本篇簡介文章中，我們會描述如何使用**序列化程式**程式庫將事件傳送至 IoT 中樞並接收來自 IoT 中樞的訊息。在本文中，我們將針對如何利用**序列化程式**巨集語言建立資料的模型提供更完整的說明以延伸討論。我們也會包含程式庫如何序列化訊息的更多詳細資料 (以及在某些情況下，如何控制序列化行為)。我們也將描述您可以修改以判斷您所建立之模型大小的某些參數。

我們會重新造訪先前文章中涵蓋的一些主題，例如訊息和屬性處理，來進行總結。但是我們將會發現，當您使用**序列化程式**程式庫時，這些功能的運作方式和它們對 **IoTHubClient** 程式庫的運作方式相同。

以下所述的所有內容都是根據**序列化程式** SDK 範例。所以如果您想要遵循這些內容，請參閱包含在 Azure IoT 裝置 SDK for C 中的 **simplesample\_amqp** 和 **simplesample\_http**。

## 模型化語言

本系列中的[簡介文章](iot-hub-device-sdk-c-intro.md)透過 **simplesample\_amqp** 應用程式提供的範例介紹 **Azure IoT 裝置 SDK for C** 模型化語言：

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

如您所見，模型化語言是以 C 巨集為基礎。您一定會以 **BEGIN\_NAMESPACE** 開始您的定義，而且永遠以 **END\_NAMESPACE** 做結。通常會為您的公司命名此命名空間，或如同此範例，為您正在使用的專案命名此命名空間。

在命名空間內部進行的是模型定義。在此情況下，有個適用於風速計的單一模型。同樣地，此模型可任意命名，但通常會命名為您想要與 IoT 中樞交換的裝置或資料類型。

模型包含您可以輸入到 IoT 中樞 (*資料*) 之事件的定義，以及您可以從 IoT 中樞接收的訊息 (*動作*)。如同您在範例中看到的，事件具有類型和名稱。動作會有一個名稱和選擇性的參數 (各有其類型)。

此範例中並未示範 SDK 支援的其他資料類型。我們將在稍後討論。

> 請注意，IoT 中樞會將裝置傳送給它的資料視為*事件*，而模型化語言則是將其視為*資料* (使用 **WITH\_DATA** 定義)。同樣地，IoT 中樞會將您傳送給裝置的資料視為*訊息*，而模型化語言則會將其視為*動作* (使用 **WITH\_ACTION** 定義)。請留意這些辭彙可能會在本文中交替使用。

### 支援的資料類型

利用**序列化程式**程式庫建立的模型支援下列資料類型：

| 類型 | 說明 |
|-------------------------|----------------------------------------|
| double | 雙精確度浮點數 |
| int | 32 位元整數 |
| float | 單精確度浮點數 |
| long | 長整數 |
| int8\_t | 8 位元整數 |
| int16\_t | 16 位元整數 |
| int32\_t | 32 位元整數 |
| int64\_t | 64 位元整數 |
| 布林 | 布林值 |
| ascii\_char\_ptr | ASCII 字串 |
| EDM\_DATE\_TIME\_OFFSET | 日期時間位移 |
| EDM\_GUID | GUID |
| EDM\_BINARY | binary |
| DECLARE\_STRUCT | 複雜資料類型 |

我們先從最後一個資料類型開始討論。**DECLARE\_STRUCT** 可讓您定義複雜資料類型-也就是其他基本類型的群組。這可讓我們定義看起來像這樣的模型：

```
DECLARE_STRUCT(TestType,
double, aDouble,
int, aInt,
float, aFloat,
long, aLong,
int8_t, aInt8,
uint8_t, auInt8,
int16_t, aInt16,
int32_t, aInt32,
int64_t, aInt64,
bool, aBool,
ascii_char_ptr, aAsciiCharPtr,
EDM_DATE_TIME_OFFSET, aDateTimeOffset,
EDM_GUID, aGuid,
EDM_BINARY, aBinary
);

DECLARE_MODEL(TestModel,
WITH_DATA(TestType, Test)
);
```

我們的模型包含 **TestType** 類型的單一資料事件。而 **準則** 是包含數個成員的複雜類型—這些成員會共同示範**序列化程式**模型化語言所支援的基本類型。

具備如同上面的模型，我們可以撰寫程式碼以將資料傳送到看起來像這樣的 IoT 中樞：

```
TestModel* testModel = CREATE_MODEL_INSTANCE(MyThermostat, TestModel);

testModel->Test.aDouble = 1.1;
testModel->Test.aInt = 2;
testModel->Test.aFloat = 3.0f;
testModel->Test.aLong = 4;
testModel->Test.aInt8 = 5;
testModel->Test.auInt8 = 6;
testModel->Test.aInt16 = 7;
testModel->Test.aInt32 = 8;
testModel->Test.aInt64 = 9;
testModel->Test.aBool = true;
testModel->Test.aAsciiCharPtr = "ascii string 1";

time_t now;
time(&now);
testModel->Test.aDateTimeOffset = GetDateTimeOffset(now);

EDM_GUID guid = { { 0x00, 0x01, 0x02, 0x03, 0x04, 0x05, 0x06, 0x07, 0x08, 0x09, 0x0A, 0x0B, 0x0C, 0x0D, 0x0E, 0x0F } };
testModel->Test.aGuid = guid;

unsigned char binaryArray[3] = { 0x01, 0x02, 0x03 };
EDM_BINARY binaryData = { sizeof(binaryArray), &binaryArray };
testModel->Test.aBinary = binaryData;

SendAsync(iotHubClientHandle, (const void*)&(testModel->Test));
```

基本上，我們將值指派給**測試**結構的每個成員，並呼叫 **SendAsync** 傳送**測試**資料事件至雲端。**SendAsync** 是將單一資料事件傳送到 IoT 中樞的協助程式函式：

```
void SendAsync(IOTHUB_CLIENT_LL_HANDLE iotHubClientHandle, const void *dataEvent)
{
	unsigned char* destination;
	size_t destinationSize;
	if (SERIALIZE(&destination, &destinationSize, *(const unsigned char*)dataEvent) ==
	{
		// null terminate the string
		char* destinationAsString = (char*)malloc(destinationSize + 1);
		if (destinationAsString != NULL)
		{
			memcpy(destinationAsString, destination, destinationSize);
			destinationAsString[destinationSize] = '\0';
			IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromString(destinationAsString);
			if (messageHandle != NULL)
			{
				IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)0);

				IoTHubMessage_Destroy(messageHandle);
			}
			free(destinationAsString);
		}
		free(destination);
	}
}
```

此函式會序列化指定的資料事件並使用 **IoTHubClient\_SendEventAsync** 將它傳送到 IoT 中樞。這是我們在先前的文章中看過的相同程式碼 (**SendAsync** 會將邏輯封裝入方便的函式)。

在上述程式碼片段中使用的另一個協助程式函式是 **GetDateTimeOffset**。此函式會將指定的時間轉換成 **EDM\_DATE\_TIME\_OFFSET** 類型的值：

```
EDM_DATE_TIME_OFFSET GetDateTimeOffset(time_t time)
{
	struct tm newTime;
	gmtime_s(&newTime, &time);
	EDM_DATE_TIME_OFFSET dateTimeOffset;
	dateTimeOffset.dateTime = newTime;
	dateTimeOffset.fractionalSecond = 0;
	dateTimeOffset.hasFractionalSecond = 0;
	dateTimeOffset.hasTimeZone = 0;
	dateTimeOffset.timeZoneHour = 0;
	dateTimeOffset.timeZoneMinute = 0;
	return dateTimeOffset;
}
```

如果我們執行上述程式碼，就會將下列訊息傳送到 IoT 中樞：

```
{"aDouble":1.100000000000000, "aInt":2, "aFloat":3.000000, "aLong":4, "aInt8":5, "auInt8":6, "aInt16":7, "aInt32":8, "aInt64":9, "aBool":true, "aAsciiCharPtr":"ascii string 1", "aDateTimeOffset":"2015-09-14T21:18:21Z", "aGuid":"00010203-0405-0607-0809-0A0B0C0D0E0F", "aBinary":"AQID"}
```

請注意，序列化為 JSON— 此為**序列化程式**程式庫所產生的格式。也請注意，序列化 JSON 物件的每個成員皆與我們在我們的模型中所定義的 **TestType** 成員相符。這些值也完全符合我們在程式碼中使用的值。但是，請注意二進位資料是以 base64 編碼—"AQID" 是 {0x01, 0x02, 0x03} 的 base64 編碼。

這個範例示範使用**序列化程式**程式庫的優點—它可讓我們將 JSON 傳送至雲端，而不需要明確地處理應用程式中的序列化。我們需要擔心的是在模型中設定資料事件的值，然後呼叫簡單的 API 將這些事件傳送至雲端。

我們可以使用上述資訊定義包含支援之資料類型範圍的模型—包括複雜類型 (以及我們甚至可以隨意願包含其他複雜類型內的複雜類型)。但是上述範例所產生的序列化 JSON 帶出了一個重點。我們*如何*利用**序列化程式**程式庫傳送資料，可完全決定 JSON 如何形成。此特點就是我們接下來要討論的內容。

## 深入了解序列化

上一節強調**序列化程式**程式庫所產生的輸出範例。在本節中，我們將著重於該主題— 說明程式庫如何將資料序列化，以及如何使用序列化 API 控制該行為。

若要進一步討論序列化，我們將使用以控溫器為基礎的新模型。但首先讓我們針對我們想要處理的案例提供一些背景...

我們想要建立可測量氣溫和溼度的控溫器模型。但是每一項資料會以不同的方式傳送至 IoT 中樞。根據預設，控溫器會每 2 分鐘輸入溫度事件一次。溼度事件會每 15 分鐘輸入一次。輸入任一事件時，必須包含表示測量相對應溫度或溼度之時間的時間戳記。

在這個案例中，我們將示範兩種模型化資料的方式，而且我們會說明該模型化對序列化輸出的影響。

### 模型 #1

以下是支援上述案例的第一版模型：

```
BEGIN_NAMESPACE(Contoso);

DECLARE_STRUCT(TemperatureEvent,
int, Temperature,
EDM_DATE_TIME_OFFSET, Time);

DECLARE_STRUCT(HumidityEvent,
int, Humidity,
EDM_DATE_TIME_OFFSET, Time);

DECLARE_MODEL(Thermostat,
WITH_DATA(TemperatureEvent, Temperature),
WITH_DATA(HumidityEvent, Humidity)
);

END_NAMESPACE(Contoso);
```

請注意，此模型包含兩個資料事件：**溫度**和**溼度**。但是與先前範例不同的是每個事件的類型都是使用 **DECLARE\_STRUCT** 定義的結構。**TemperatureEvent** 包含溫度測量和時間戳記。**HumidityEvent** 包含溼度測量和時間戳記。此模型可讓我們以自然的方式模型化上述案例的資料。當我們將事件傳送至雲端時，我們要傳送溫度/時間戳記或溼度/時間戳記組。

我們可以使用如下的程式碼將溫度事件傳送至雲端：

```
time_t now;
time(&now);
thermostat->Temperature.Temperature = 75;
thermostat->Temperature.Time = GetDateTimeOffset(now);

unsigned char* destination;
size_t destinationSize;
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

我們將在範例程式碼中對氣溫和溼度使用硬式編碼值- 但想像我們實際上正藉由在控溫器上取樣相對應的感應器來擷取這些值。

上述程式碼使用先前介紹的協助程式 **GetDateTimeOffset**。基於稍後才會明朗的原因，此程式碼會明確分隔序列化及傳送事件的工作。上述程式碼會將溫度事件序列化至緩衝區。**sendMessage** 是個協助程式函式 (包含在 **simplesample\_amqp** 中)，會將事件傳送至 IoT 中樞：

```
static void sendMessage(IOTHUB_CLIENT_HANDLE iotHubClientHandle, const unsigned char* buffer, size_t size)
{
    static unsigned int messageTrackingId;
    IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromByteArray(buffer, size);
    if (messageHandle != NULL)
    {
        IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)(uintptr_t)messageTrackingId);

        IoTHubMessage_Destroy(messageHandle);
    }
    free((void*)buffer);
}
```

此程式碼是前一節所述之 **SendAsync** 協助程式的子集，所以我們不會在此贅述。

我們執行先前的程式碼以傳送溫度事件時，事件的序列化形式會傳送到 IoT 中樞：

```
{"Temperature":75, "Time":"2015-09-17T18:45:56Z"}
```

我們要傳送屬於 **TemperatureEvent** 類型的溫度，以及包含**溫度**和**時間**成員的結構。這會直接反映在序列化資料中。毫無意外。

同樣地，我們可以利用此程式碼傳送溼度事件：

```
thermostat->Humidity.Humidity = 45;
thermostat->Humidity.Time = GetDateTimeOffset(now);
if (SERIALIZE(&destination, &destinationSize, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

且傳送到 IoT 中樞的序列化形式看起來像這樣：

```
{"Humidity":45, "Time":"2015-09-17T18:45:56Z"}
```

同樣地，全如預期一般。

您可以使用上述模型想像如何輕鬆地新增其他事件— 您只需使用 **DECLARE\_STRUCT** 定義較多結構，並使用 **WITH\_DATA** 將相對應的事件包含在模型中。

但是現在我們要修改模型，讓它包含相同的資料，但具有不同的結構。

### 模型 #2

請考慮上述模型的替代模型：

```
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

在此情況下，我們消除了 **DECLARE\_STRUCT** 巨集，並且只使用來自模型化語言的簡單類型定義來自案例中的資料項目。

現在讓我們忽略**時間**事件。忽略該事件之後，以下是要輸入**溫度**的程式碼：

```
time_t now;
time(&now);
thermostat->Temperature = 75;

unsigned char* destination;
size_t destinationSize;
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

此程式碼會將下列序列化事件傳送到 IoT 中樞：

```
{"Temperature":75}
```

且傳送溼度事件的程式碼看起來像這樣：

```
thermostat->Humidity = 45;
if (SERIALIZE(&destination, &destinationSize, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

此程式碼會將其傳送至 IoT 中樞：

```
{"Humidity":45}
```

到目前為止，仍毫無意外。但是讓我們變更「序列化」巨集的使用方式...

**SERIALIZE** 巨集可以將多個資料事件視為引數。這樣可以讓我們一併序列化**溫度**和**溼度**事件，並在單一呼叫中將其傳送給 IoT 中樞：

```
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

您或許已經猜到，此程式碼的結果是兩個資料事件都會傳送到 IoT 中樞：

[

{"Temperature":75},

{"Humidity":45}

]

換句話說，您可能會預期此程式碼在分別傳送**溫度**和**溼度**時是一樣的— 這樣就可以方便將兩個事件在相同的呼叫中傳遞到 **SERIALIZE**。不過，事實並非如此。上述程式碼會改為將這個單一資料事件傳送到 IoT 中樞：

{"Temperature":75, "Humidity":45}

這樣似乎很奇怪，因為我們的模型會將**溫度**和**溼度**定義為兩個*個別*事件：

```
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

此外，我們沒有模型化這些**溫度**和**溼度**位於相同結構的事件：

```
DECLARE_STRUCT(TemperatureAndHumidityEvent,
int, Temperature,
int, Humidity,
);

DECLARE_MODEL(Thermostat,
WITH_DATA(TemperatureAndHumidityEvent, TemperatureAndHumidity),
);
```

如果我們使用此模型，就可以更容易了解**溫度**和**溼度**如何在相同的序列化訊息中傳送。但是當您將使用模型 #2 將兩個資料事件都傳遞至 **SERIALIZE** 時，可能會無法理解其運作方式。

如果您知道**序列化程式**程式庫所做的假設，您可以更容易了解這種行為。若要了解這一點，讓我們回到我們的模型：

```
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

請以物件導向的觀點思考此模型。在此情況下，我們正在模型化一個實體裝置 (控溫器)，而該裝置包含**溫度**和**溼度**等屬性。

我們可以依照意願利用類似以下的程式碼傳送模型的整個狀態：

```
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature, thermostat->Humidity, thermostat->Time) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

假設已設定溫度、溼度和時間的值，我們會看到這樣的事件傳送到 IoT 中樞：

```
{"Temperature":75, "Humidity":45, "Time":"2015-09-17T18:45:56Z"}
```

但是在許多情形下，您可能只想要傳送模型的*部分*屬性到雲端 (尤其是當您的模型包含大量資料事件的時候)。此時像我們先前的範例一樣只傳送資料事件的子集就非常實用：

```
{"Temperature":75, "Time":"2015-09-17T18:45:56Z"}
```

這樣會產生完全相同的序列化事件，如同我們已利用**溫度**和**時間**成員定義 **TemperatureEvent**— 就像我們對模型 #1 的執行內容。但是在此情況下，我們可以使用不同的模型 (模型 #2) 產生完全相同的序列化事件，完全是因為我們以不同的方式呼叫 **SERIALIZE**。

這裡的重點是，如果您傳遞多個資料事件至 **SERIALIZE**，它會假設每個事件都是單一 JSON 物件中的一個屬性。

最好的方法取決於您和您對模型的思考。如果您的心態是您要傳送「事件」至雲端，且每個事件都包含一組定義的屬性，則第一種方法很實用。在此情況下，您會使用 **DECLARE\_STRUCT** 定義每個事件的結構，然後利用 **WITH\_DATA** 巨集將它們包含在模型中。然後您會依照我們在上述第一個範例中使用的方法來傳送每個事件。對於這種方法，您只會傳送單一資料事件至 **SERIALIZER**。

如果您以物件導向的方式思考模型，第二種方法可能比較適合您。在此情況下，使用 **WITH\_DATA** 定義的項目是物件的「屬性」。在此情況下，您可以隨意傳遞事件的任何子集至 **SERIALIZE**— 取決於您想要將什麼程度的「物件」狀態傳送至雲端。

沒有絕對正確或錯誤的方法。請務必了解**序列化程式**程式庫的運作方式，並挑選最適合您需求的模型化方法。

## 訊息處理

到目前為止，本文只討論傳送事件到 IoT 中樞，尚未處理接收訊息。這是因為我們對接收訊息所需要知道的大部分內容已在[先前的文章](iot-hub-device-sdk-c-intro.md)中討論過了。請從那篇文章回憶，您是藉由註冊訊息回呼函式來處理訊息：

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

**IoTHubMessage** 的實作會在模型中負責呼叫每個動作的特定函式。例如，如果您的模型定義這項動作：

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

並在訊息傳送到您的裝置時呼叫 **SetAirResistance**。

不過我們還沒說明序列化版本訊息的外觀。換句話說，如果您想要傳送 **SetAirResistance** 訊息至您的裝置，該訊息的外觀如何？

如果您要傳送訊息給裝置，您會透過 Azure IoT 服務 SDK 進行。但是您仍然需要知道要傳送哪個字串才能叫用特殊動作...傳送訊息的一般格式看起來像這樣：

```
{"Name" : "", "Parameters" : "" }
```

您要使用兩個屬性傳送序列化的 JSON 物件：**名稱**是動作 (也稱為訊息) 的名稱，而**參數**包含該動作的參數。

例如，若要叫用 **SetAirResistance**，您可以傳送下列訊息給裝置：

```
{"Name" : "SetAirResistance", "Parameters" : { "Position" : 5 }}
```

動作名稱必須完全符合您的模型中所定義的動作。同樣地，參數名稱也必須相符。也請注意，案例很重要。**名稱**和**參數**永遠是大寫。並確定符合模型中動作名稱和參數的大小寫。在此範例中，動作名稱是 "SetAirResistance"，而不是 "setairresistance"。

這篇文章說明您使用**序列化程式**程式庫傳送事件和接收訊息時需要知道的所有內容。但是在繼續討論之前，我們先討論一些您可以設定以控制模型大小的參數。

## 巨集組態

如果您使用**序列化程式**程式庫，以下是要注意的 SDK 重點：

> .\\c\\common\\tools\\macro\_utils\_h\_generator。

此資料夾包含名為 **macro\_utils\_h\_generator.sln** 的 Visual Studio 解決方案。

  ![](media/iot-hub-device-sdk-c-serializer/01-macro_utils_h_generator.PNG)

此解決方案中的程式會產生 **macro\_utils.h**，位於 .\\c\\common\\inc 目錄中。沒有預設 macro\_utils.h 檔案包含在 SDK 中。但是這個解決方案可讓您修改部分參數並根據這些參數重新建立標頭檔。

您需要考慮的兩個重要參數為 **nArithmetic** 和 **nMacroParameters**，定義於 macro\_utils.tt 中的這兩行：

```
<#int nArithmetic=1024;#>
<#int nMacroParameters=124;/*127 parameters in one macro deﬁnition in C99 in chapter 5.2.4.1 Translation limits*/#>

```

上述的值是包含在 SDK 中的預設參數。每個參數都具有下列意義：

-   nMacroParameters – 控制您在一個 DECLARE\_MODEL 巨集定義中所擁有的參數數目。

-   nArithmetic – 控制模型中允許的成員*總*數

這些參數很重要的原因是它們掌控您的模型大小。以此模型定義為例：

```
DECLARE_MODEL(MyModel,
WITH_DATA(int, MyData)
);
```

如先前所述，**DECLARE\_MODEL** 只是個 C 巨集。模型的名稱和 **WITH\_DATA** 陳述式 (另一個巨集) 是 **DECLARE\_MODEL** 的參數。**nMacroParameters** 會定義 **DECLARE\_MODEL** 中包含多少參數。實際上，這樣會定義您擁有多少資料事件與動作宣告。因此預設限制 124 表示您可以定義由大約 60 個動作和事件資料所組成的模型。如果您嘗試超過這個限制，您會得到如下所示的編譯器錯誤：

  ![](media/iot-hub-device-sdk-c-serializer/02-nMacroParametersCompilerErrors.PNG)

**nArithmetic** 參數與巨集語言內部運作的關聯勝過您的應用程式。但它只能大略控制您在模型中擁有的成員*總*數- 包括 **DECLARE\_STRUCT** 巨集。因此如果您開始看到這類的編譯器錯誤，您應該嘗試增加 **nArithmetic**：

   ![](media/iot-hub-device-sdk-c-serializer/03-nArithmeticCompilerErrors.PNG)

如果您想要變更這些參數，請修改 macro\_utils.tt 檔案中的值、重新編譯 macro\_utils\_h\_generator.sln 解決方案，然後執行已編譯的程式。當您這麼做時，新的 macro\_utils.h 檔案就會產生並放置在 .\\common\\inc 目錄中。

若要使用 macro\_utils.h 的新版本，您必須從您的解決方案移除 **序列化程式** NuGet 套件，並在它的位置包含 **序列化程式** Visual Studio 專案。這可讓您的程式碼針對序列化程式程式庫的原始程式碼 (包含更新的 macro\_utils.h) 進行編譯。假設您想要對 **simplesample\_amqp** 執行這項操作。您應該先從解決方案移除序列化程式程式庫的 NuGet 套件開始：

   ![](media/iot-hub-device-sdk-c-serializer/04-serializer-github-package.PNG)

然後將此專案加入至您的 Visual Studio 解決方案：

> .\\c\\serializer\\build\\windows\\serializer.vcxproj

完成時，解決方案應該如下所示：

   ![](media/iot-hub-device-sdk-c-serializer/05-serializer-project.PNG)

現在當您編譯您的解決方案時，更新的 macro\_utils.h 將會包含在您的二進位檔。

要注意的重點在於，增加這些值到足夠的數目可能會超出編譯器限制。此時 **nMacroParameters** 會成為要注意的主要參數。C99 規格指定巨集定義中允許最少 127 個參數。Microsoft 編譯器剛好會確實遵循此規格 (且限制為 127)，讓您無法將 **nMacroParameters** 增加到超過預設值。但其他編譯器可能會允許您這麼做 (例如 GNU 編譯器支援更高的限制)。

到目前為止，我們幾乎已經討論了如何使用**序列化程式**程式庫撰寫程式碼所必須知道的一切內容。在我們做結論之前，讓我們從先前的文章重新瀏覽一些您可能想知道的主題。

## 較低層級 API

我們在這篇文章中著重的範例應用程式是 **simplesample\_amqp**。這個範例會使用較高層級 (非 "LL") API 來傳送事件和接收訊息。如果您使用這些 API，會執行背景執行緒以處理事件傳送和訊息接收。不過，我們可以依照意願使用較低層級 (LL) API 來消除這個背景執行緒，並且在傳送事件或從雲端接收訊息時明確掌握控制權。

如[先前的文章](iot-hub-device-sdk-c-iothubclient.md)所述，有一組函式由較高層級 API 組成：

-   IoTHubClient\_CreateFromConnectionString

-   IoTHubClient\_SendEventAsync

-   IoTHubClient\_SetMessageCallback

-   IoTHubClient\_Destroy

這些 API 會在 **simplesample\_amqp** 中示範。

但是有一組類似的較低層級 API。

-   IoTHubClient\_LL\_CreateFromConnectionString

-   IoTHubClient\_LL\_SendEventAsync

-   IoTHubClient\_LL\_SetMessageCallback

-   IoTHubClient\_LL\_Destroy

其中的主要重點是較低層級 API 的運作和先前文章中所述的運作完全相同。如果您想要背景執行緒以處理事件傳送和訊息接收，您可以使用第一組 API。如果您想要在與 IoT 中樞之間傳送和接收資料時掌握明確控制權，您會使用第二組 API。任何一組 API 使用**序列化程式**程式庫的效果都相同。

如需較低層級 API 搭配**序列化程式**程式庫使用的範例，請參閱 **simplesample\_http** 應用程式。

## 其他主題

值得一提的其他幾個主題包括屬性處理、使用替代裝置認證和組態選項。這些都是[先前的文章](iot-hub-device-sdk-c-iothubclient.md)中所涵蓋的主題。這裡的重點在於，這些功能對**序列化程式**程式庫的運作方式和它們對 **IoTHubClient** 程式庫的運作方式相同。例如，如果您想要從您的模型將屬性附加至事件，您將以相同方式使用 **IoTHubMessage\_Properties** 和 **Map**\_**AddorUpdate**，如先前所述：

```
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

事件是從**序列化程式**程式庫產生，或使用 **IoTHubClient** 程式庫手動產生並不重要。

關於替代裝置認證，使用 **IoTHubClient\_LL\_Create** 配置 **IOTHUB\_CLIENT\_HANDLE** 的效果和使用 **IoTHubClient\_CreateFromConnectionString** 一樣好。

最後，如果您使用**序列化程式**程式庫，您可以使用 **IoTHubClient\_LL\_SetOption** 設定組態選項，就像您使用 **IoTHubClient** 程式庫時的執行內容一樣。

對**序列化程式**程式庫而言獨一無二的次要功能是初始化 API。在您開始使用此程式庫之前，您必須先呼叫 **serializer\_init**：

```
serializer_init(NULL);
```

此動作必須在您呼叫 **IoTHubClient\_CreateFromConnectionString** 之前完成。

同樣地，當您使用該程式庫完成動作時，您通常進行最後一次呼叫的對象是 **serializer\_deinit**：

```
serializer_deinit();
```

否則所有上面所列的其他功能對**序列化程式**程式庫的運作方式就會和其對 **IoTHubClient** 程式庫的運作方式相同。如需任何主題的詳細資料，請參閱本系列中的[前一篇文章](iot-hub-device-sdk-c-iothubclient.md)。

## 後續步驟

這篇文章會深入探討 **Azure IoT 裝置 SDK for C** 中包含之**序列化程式**程式庫的獨特層面。透過文中提供的資訊，您應該能充分了解如何使用模型來傳送事件和接收來自 IoT 中樞的訊息。

在此也將結束有關如何使用 **Azure IoT 裝置 SDK for C** 開發應用程式共三個部分的系列。這些資訊應該足以讓您入門並且能夠徹底了解 API 的運作方式。如果您仍想尋找其他詳細資料，有幾個 SDK 中的範例未涵蓋在上方的討論中。否則，[SDK 文件](https://github.com/Azure/azure-iot-sdks)是其他資訊的絕佳資源。

<!---HONumber=Nov15_HO1-->