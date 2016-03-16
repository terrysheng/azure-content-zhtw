> [AZURE.SELECTOR]
- [C on Windows](../articles/iot-suite/iot-suite-connecting-devices.md)
- [C on Linux](../articles/iot-suite/iot-suite-connecting-devices-linux.md)
- [C on mbed](../articles/iot-suite/iot-suite-connecting-devices-mbed.md)
- [Node.js](../articles/iot-suite/iot-suite-connecting-devices-node.md)

## 案例概觀

在此案例中，您將建立會傳送下列遙測資料到遠端監視[預設解決方案][lnk-what-are-preconfig-solutions]的裝置：

- 外部溫度
- 內部溫度
- 溼度

為了簡化起見，在裝置上的程式碼會產生範例值，但我們鼓勵您將實際的感應器連接到您的裝置並傳送實際的遙測資料來擴充此範例。

若要完成此教學課程，您將需要一個有效的 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用][lnk-free-trial]。

## 開始之前

在您為裝置撰寫任何程式碼之前，您必須先佈建遠端監視預先設定解決方案，然後在該解決方案中佈建裝置。

### 佈建遠端監視預先設定解決方案

您建立的裝置將會傳送資料到[遠端監視][lnk-remote-monitoring]預先設定解決方案的執行個體。請瀏覽[開始使用 Azure IoT 套件][lnk-getstarted]，以在您的 Azure 帳戶中佈建遠端監視預先設定解決方案：

1. 在 https://www.azureiotsuite.com/ 頁面上，按一下 [+] 以建立新解決方案。

2. 按一下 [遠端監視] 面板上的 [選取] 以建立新解決方案。

3. 在 [建立「遠端監視」解決方案] 頁面上，輸入您選擇的 [解決方案名稱]，選取您要在其中進行部署的 [區域]，然後選取您想要使用的 Azure 訂用帳戶。按一下 [建立解決方案]。

4. 等候佈建程序完成。

> [AZURE.WARNING] 預先設定的解決方案使用可計費的 Azure 服務。當您使用完預先設定的解決方案之後，請務必將它從您的訂用帳戶中移除，以避免任何不必要的費用。只要到 https://www.azureiotsuite.com/ 頁面，即可將預先設定的解決方案從您的訂用帳戶中完全移除。

佈建好遠端監視解決方案之後，請按一下 [啟動]，以在瀏覽器中開啟解決方案儀表板。

![][img-dashboard]

### 在遠端監視方案中佈建您的裝置

> [AZURE.NOTE] 如果您已經有在解決方案中佈建裝置，則可以略過此步驟。建立用戶端應用程式時您需要知道裝置認證。

對於連線到預先設定解決方案的裝置，該裝置必須能夠使用有效的認證識別自己。您可以從解決方案儀表板取得裝置認證，然後將它們包含在用戶端應用程式中。

若要在遠端監視解決方案中新增裝置，請在解決方案儀表板中完成下列步驟：

1.  在儀表板左下角，按一下 [新增裝置]。

    ![][1]

2.  在 [自訂裝置] 面板中按一下 [新增]。

    ![][2]

3.  選擇 [自行定義裝置識別碼]，輸入裝置識別碼，例如 **mydevice**，按一下 [檢查 ID] 驗證該名稱未被使用，然後按一下 [建立] 來佈建裝置。

    ![][3]

5. 記下裝置的認證 (裝置識別碼、IoT 中樞主機名稱和裝置機碼)，用戶端應用程式需要這些資訊才能將裝置連線至遠端監視解決方案。然後按一下 [完成]。

    ![][4]

6. 確定您的裝置在裝置區段上正確顯示。狀態會是 [暫止] 直到裝置與遠端監視解決方案建立連線。

    ![][5]

[img-dashboard]: ./media/iot-suite-selector-connecting/dashboard.png
[1]: ./media/iot-suite-selector-connecting/suite0.png
[2]: ./media/iot-suite-selector-connecting/suite1.png
[3]: ./media/iot-suite-selector-connecting/suite2.png
[4]: ./media/iot-suite-selector-connecting/suite3.png
[5]: ./media/iot-suite-selector-connecting/suite5.png

[lnk-getstarted]: https://www.azureiotsuite.com/
[lnk-what-are-preconfig-solutions]: ../articles/iot-suite/iot-suite-what-are-preconfigured-solutions.md
[lnk-remote-monitoring]: ../articles/iot-suite/iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

<!---HONumber=AcomDC_0211_2016-->