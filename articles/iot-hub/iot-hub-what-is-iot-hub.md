<properties
 pageTitle="Azure IoT 中樞概觀 | Microsoft Azure"
 description="Azure IoT 中樞服務概觀，包括裝置連線性、通訊模式和服務輔助通訊模式"
 services="iot-hub"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="11/09/2015"
 ms.author="dobett"/>

# 何謂 Azure IoT 中樞？

歡迎使用 Azure IoT 中樞。本文提供 Azure IoT 中樞的概觀，並描述在實作 IoT 解決方案時，您會想使用此服務的原因。

Azure IoT 中樞是一項完全受管理的服務，可讓數百萬個 IoT 裝置和一個解決方案後端進行可靠且安全的雙向通訊。Azure IoT 中樞提供了可靠且可實際運用的裝置到雲端和雲端到裝置傳訊，使用各裝置安全性認證與存取控制啟用安全通訊，並包含最受歡迎的語言和平台的裝置程式庫。

![IoT 中樞做為雲端閘道器？][img-architecture]

## IoT 裝置連線能力面臨的挑戰

對於以可靠且安全的方法將裝置連線到解決方案後端的挑戰，IoT 中樞和裝置程式庫可以協助解決。IoT 裝置：

- 通常是無人操作的嵌入式系統。
- 可能位於實體存取會非常昂貴的遠端位置。
- 可能只能透過解決方案後端來存取。
- 能力和/或處理資源可能都有限。
- 網路連線能力可能不穩定、緩慢或昂貴。
- 可能需要使用專屬、自訂或業界特定的應用程式通訊協定。
- 可以使用大型的熱門硬體和軟體平台組來建立。

除了上述需求之外，任何 IoT 解決方案也必須提供調整、安全性和可靠性。使用傳統技術 (例如 Web 容器和傳訊代理程式) 實作連線需求的結果是困難且耗時的。

## 為何使用 Azure IoT 中樞

Azure IoT 中樞會以下列方式解決裝置連線面臨的挑戰：

-   **每個裝置的驗證和安全連線**。您可以提供每個裝置獨有的安全性金鑰讓它連線到 IoT 中樞。解決方案後端可將個別裝置加入白名單或黑名單，以達到完全控制裝置存取權。

-   **一組廣泛的裝置程式庫**。Azure IoT 裝置 SDK 可供各種語言和平台使用並受其支援：C 表示許多 Linux 散發套件、Windows 和 RTOS。Azure IoT 裝置 SDK 也支援 C#、Java 和 JavaScript 等 Managed 語言。

-   **IoT 通訊協定和擴充性**。如果您的解決方案不能使用裝置程式庫，Azure IoT 中樞會公開可讓裝置以原生方式使用 HTTP 1.1 和 AMQP 1.0 通訊協定的公用通訊協定。您也可以利用 [Azure IoT 通訊協定閘道器][protocol-gateway]開放原始碼元件擴充 IoT 中樞，以提供支援給 MQTT v3.1.1。您可以在雲端或在內部部署執行 Azure IoT 通訊協定閘道器，並將其擴充以支援自訂通訊協定。

-   **調整**。Azure IoT 中樞會調整為數百萬個同時連接的裝置，以及每秒數百萬個事件。

對於許多通訊模式這些優點都是通用的。IoT 中樞目前可讓您實作下列特定的通訊模式：

-   **以事件為基礎的裝置到雲端擷取。** IoT 中樞每秒能接收數百萬個來自裝置的事件且非常可靠， 並使用事件處理引擎在最忙碌路徑上處理它們，或將它們儲存在冷路徑供分析。IoT 中樞可保留最多 7 天的事件資料，以保證可靠的處理並吸收負載尖峰。

-   **可靠的雲端到裝置傳訊 (或命令)。** 解決方案後端可以使用 IoT 中樞傳送訊息給個別裝置 (含至少一次的傳遞保證)。每個訊息都有個別的存留時間設定，且後端可以要求傳遞和到期的回條，以確保雲端到裝置訊息的生命週期有完整可見度。這樣您就可以實作包含在裝置上執行之作業的商務邏輯。

利用 IoT 中樞裡的 IoT 特定功能 (例如一致的裝置身分識別管理、連線能力監視和調整)，您也可以實作其他常見的模式，像是上傳和下載檔案。

## IoT 中樞如何運作？

Azure IoT 中樞會在實作[服務輔助通訊][lnk-service-assisted-pattern]模式時調節您的裝置與解決方案後端之間的互動。服務輔助通訊的目標是要在 IoT 中樞等控制系統，與部署在不受信任實體空間中的特殊用途裝置之間，建立可信任的雙向通訊路徑。該模式會建立下列原則：

- 安全性的優先順序高於所有其他功能。
- 裝置不會接受未經要求的網路資訊。裝置會以僅限輸出方式建立所有連接和路由。若要讓裝置從後端接收命令，裝置必須定期初始連接以檢查有沒有任何暫止的命令要處理。
- 裝置只應連接至或建立路由至與它們對等的知名服務，例如 IoT 中樞服務執行個體。
- 裝置和服務或是裝置和閘道器之間的通訊路徑會在應用程式通訊協定層受到保護。
- 系統層級授權和驗證是根據每個裝置的身分識別，且讓存取認證和權限能近乎即時撤銷。
- 對於因為電源或連線能力而連線不穩定的裝置而言，可透過保留命令和通知直到裝置連線並接收它們，進而促進其雙向通訊。IoT 中樞會為傳送的命令維護裝置特定佇列。
- 針對透過閘道對特定服務的受保護傳輸，應用程式承載資料會個別受到保護。

服務輔助通訊模式已在行動產業大規模成功用來實作推播通知服務，例如 [Windows 通知服務](https://msdn.microsoft.com/library/windows/apps/mt187203.aspx)、[Google 雲端通訊](https://developers.google.com/cloud-messaging/)和 [Apple Push Notification Service](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW9)。

## 後續步驟

若要深入了解 Azure IoT 中樞，請參閱這些連結：

* [開始使用 IoT 中樞]
* [連接裝置]
* [處理裝置到雲端的訊息]

[開始使用 IoT 中樞]: iot-hub-csharp-csharp-getstarted.md
[連接裝置]: https://azure.microsoft.com/develop/iot/
[處理裝置到雲端的訊息]: iot-hub-csharp-csharp-process-d2c.md
[protocol-gateway]: https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md
[img-architecture]: media/iot-hub-what-is-iot-hub/hubarchitecture.png

[lnk-service-assisted-pattern]: http://blogs.msdn.com/b/clemensv/archive/2014/02/10/service-assisted-communication-for-connected-devices.aspx "服務輔助通訊，由 Clemens Vasters 撰寫的部落格文章"

<!---HONumber=Nov15_HO3-->