## 在儀表板中檢視裝置遙測

遠端監視解決方案中的儀表板，可讓您檢視裝置傳送到 IoT 中樞的遙測。

1. 在瀏覽器中，返回遠端監視解決方案儀表板，按一下左面板中的 [裝置] 瀏覽至 [裝置清單]。

2. 在 [裝置清單] 中，您應該會看到裝置狀態現在是 [正在執行]。

    ![][18]

3. 在儀表板中，在 [要檢視的裝置] 下拉式清單中選取您的裝置，以檢視其遙測。範例應用程式的遙測是 50 個單位的內部溫度、 55 個單位的外部溫度，以及 50 個單位的濕度。請注意，儀表板預設只顯示溫度和溼度的值。

    ![][img-telemetry]

## 傳送命令至裝置

遠端監視解決方案中的儀表板，可讓您要求 IoT 中樞傳送命令至裝置。例如，在遠端監視解決方案中，您可以傳送命令來設定裝置的內部溫度。

1. 在遠端監視解決方案的儀表板中，按一下左面板中的 [裝置] 瀏覽至 [裝置清單]。

2. 在 [裝置清單] 中，按一下裝置的 [裝置識別碼]。

3. 在 [裝置詳細資料] 面板中，按一下 [命令]。

    ![][13]

4. 在 [命令] 下拉式清單中選取 [SetTemperature]，然後在 [溫度] 輸入新的溫度值。按一下 [傳送命令] 將命令傳送至裝置。

    ![][14]

    > [AZURE.NOTE]命令歷程記錄一開始會顯示命令狀態為 [暫止]。當裝置認可命令時，狀態就會變更為 [成功]。

5. 在儀表板上確認該裝置現在正傳送 75 為新的溫度值。

## 後續步驟

《[自訂預先設定的解決方案][lnk-customize]》一文描述可以擴充這個範例的一些方法。可能的延伸模組包括使用真實的感應器和實作其他命令。

[13]: ./media/iot-suite-visualize-connecting/suite4.png
[14]: ./media/iot-suite-visualize-connecting/suite7-1.png
[18]: ./media/iot-suite-visualize-connecting/suite10.png
[img-telemetry]: ./media/iot-suite-visualize-connecting/telemetry.png
[lnk-customize]: ../articles/iot-suite/iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-dev-messaging]: ../articles/iot-hub/iot-hub-devguide.md#messaging

<!---HONumber=AcomDC_1203_2015-->