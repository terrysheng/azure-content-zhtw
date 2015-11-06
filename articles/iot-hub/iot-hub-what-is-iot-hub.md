<properties
 pageTitle="Azure IoT 中樞概觀 | Microsoft Azure"
 description="Azure IoT 中樞服務概觀，包括裝置連線性、通訊模式和服務輔助通訊模式"
 services="iot-hub"
 documentationCenter=""
 authors="fsautomata"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/29/2015"
 ms.author="elioda"/>

# 何謂 Azure IoT 中心？

Azure IoT 中心是一項完全受管理的服務，可讓數百萬個 IoT 裝置和一個應用程式後端進行可靠且安全的雙向通訊。Azure IoT 中心提供了可靠的裝置到雲端和雲端到裝置超規模傳訊，使用各裝置安全性認證與存取控制啟用安全通訊，並包含最受歡迎的語言和平台的裝置程式庫。

![IoT 中心做為雲端閘道器？][img-architecture]

## IoT 裝置連線能力

IoT 專案所面臨的其中一個最大挑戰，就是如何可靠且安全地將裝置連線至應用程式後端。相較於其他用戶端，例如瀏覽器、其他伺服器和行動應用程式，IoT 裝置通常會有不同的特性：

-   它們通常是內嵌在沒有人為運算子的系統。

-   它們可以位於實體存取非常昂貴的遠端位置。

-   應用程式後端的連線可以是存取裝置的唯一方式。

-   它們的能力和/或處理資源可能都有限。

-   網路連線對裝置而言可能是間歇性、昂貴或很稀少的。

-   您可能需要使用專屬、自訂或業界特定的應用程式通訊協定。

-   有大型的熱門硬體和軟體平台組可用來建立裝置。

除了上述需求之外，任何 IoT 解決方案也必須提供調整、安全性和可靠性。這些層面會結合為一組連線需求，要使用傳統技術 (例如 web 容器和訊息代理程式) 實作這些需求，既困難且耗時。

## 為何使用 Azure IoT 中心

Azure IoT 中心會以下列方式解決裝置連線需求：

-   **每個裝置的驗證和安全連線**。每個裝置都可以使用自己的安全性金鑰來連接至 IoT 中心。應用程式後端就能夠個別將每個裝置列入允許清單和封鎖清單，對裝置存取啟用完全的控制。

-   **一組廣泛的裝置程式庫**。Azure IoT 裝置 SDK 可供各種語言和平台使用並受其支援：C 表示許多 Linux 散發套件、Windows 和 RTOS。Azure IoT 裝置 SDK 也支援 C#、Java 和 JavaScript 等 Managed 語言。

-   **IoT 通訊協定和擴充性**。如果您的解決方案不能使用裝置程式庫，Azure IoT 中樞會公開可讓裝置以原生方式使用 HTTP 1.1 和 AMQP 1.0 通訊協定的公用通訊協定。您也可以擴充 IoT 中樞以利用 Azure IoT 通訊協定閘道器開放原始碼元件提供支援給 MQTT v3.1.1。您可以在雲端或在內部部署執行 Azure IoT 通訊協定閘道器，並將其擴充以支援自訂通訊協定。

-   **調整**。Azure IoT 中心會調整為數百萬個同時連接的裝置，以及每秒數百萬個事件。

除了這些跨許多通訊模式常見的優點之外，IoT 中樞目前可讓您實作下列通訊模式：

-   **以事件為基礎的裝置到雲端擷取。** 裝置每秒可以可靠地傳送數百萬個事件，讓最忙碌路徑事件處理器引擎進行處理，或者儲存進行冷路徑分析。IoT 中樞可保留最多 7 天的事件資料，以保證可靠的處理並吸收負載尖峰。

-   **可靠的雲端到裝置傳訊 (或*命令*)。** 應用程式後端可以傳送可靠的訊息給個別裝置 (也就是至少一次的傳遞保證)。每個訊息都有個別的存留時間設定，且後端可以要求傳遞和到期的回條，以確保雲端到裝置訊息之生命週期的完整可見度。

除了這些通訊模式之外，您可以實作其他熱門的模式，例如利用 IoT 中樞裡的 IoT 特定功能上傳和下載檔案，例如一致的裝置身分識別管理、連線能力監視和調整。

## 服務輔助通訊模式

Azure IoT 中心會在實作[服務輔助通訊][lnk-service-assisted-pattern]模式時調節您的裝置與應用程式後端之間的互動。服務輔助通訊的目標是要在控制系統 (例如 IoT 中心) 以及特殊用途裝置 (部署在不可信任的實體空間中) 之間，建立可信任的雙向通訊路徑。因此，該模式會建立下列原則：

- 安全性更勝於其他所有功能。
- 裝置不會接受未經要求的網路資訊。所有連接和路由都是以僅限輸出方式從裝置建立的。若要讓裝置從後端接收命令，裝置必須定期初始連接以檢查有沒有任何暫止的命令要處理。
- 裝置通常只會連接至或建立路由至與它們對等的知名服務，例如 IoT 中樞服務執行個體。
- 裝置和服務或是裝置和閘道器之間的通訊路徑會在應用程式通訊協定層受到保護。
- 系統層級授權和驗證必須根據每個裝置的身分識別，且存取認證和權限必須能夠盡快撤銷。
- 對於偶爾因為能力或連線能力而連接的裝置而言，可透過保留對裝置的命令和通知直到它們連接以收取命令及通知，進而促進其雙向通訊。IoT 中樞會為了它傳送至裝置的命令而維護裝置特定佇列。
- 應用程式承載資料可能會因為經由閘道器至特定服務的受保護傳輸而個別受到保護。

服務輔助通訊模式已在行動產業大規模成功用來實作推播通知服務，例如 [Windows 通知服務](https://msdn.microsoft.com/library/windows/apps/mt187203.aspx)、[Google 雲端通訊](https://developers.google.com/cloud-messaging/)和 [Apple Push Notification Service](http://go.microsoft.com/fwlink/p/?linkid=272584&clcid=0x409)。

## 後續步驟

若要深入了解 Azure IoT 中樞，請參閱這些連結：

* [IoT 中心開發人員指南]
* [IoT 中心指引]
* [支援的裝置平台和語言]
* [Azure IoT 開發人員中心]
* [開始使用 IoT 中心]

[IoT Hub Overview]: iot-hub-what-is-iot-hub.md
[IoT 中心指引]: iot-hub-guidance.md
[IoT 中心開發人員指南]: iot-hub-devguide.md
[IoT Hub Supported Devices]: iot-hub-supported-devices.md
[開始使用 IoT 中心]: iot-hub-csharp-csharp-getstarted.md
[支援的裝置平台和語言]: iot-hub-sdks-summary.md#os-platforms-and-hardware-compatibility
[Azure IoT 開發人員中心]: https://azure.microsoft.com/develop/iot/

[img-why-use]: media/iot-hub-what-is-iot-hub/image1.png
[img-architecture]: media/iot-hub-what-is-iot-hub/hubarchitecture.png

[lnk-service-assisted-pattern]: http://blogs.msdn.com/b/clemensv/archive/2014/02/10/service-assisted-communication-for-connected-devices.aspx "服務輔助通訊，由 Clemens Vasters 撰寫的部落格文章"

<!----HONumber=Oct15_HO3-->