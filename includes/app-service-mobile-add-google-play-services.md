1. 按一下 Android Studio 工具列上的圖示以開啟 Android SDK Manager，或按一下工能表上的 [工具] -> [Android] -> [SDK Manager]。尋找專案中使用之 Android SDK 的目標版本，開啟該版本，然後選擇 [Google API] (如果尚未安裝的話)。

2. 移至 [檔案]、[專案結構]。接著，啟用 [通知] 下的推播通知。

3. 開啟 **AndroidManifest.xml** 並將此標記加入至應用程式標記。

        <meta-data android:name="com.google.android.gms.version"
            android:value="@integer/google_play_services_version" />
 

<!---HONumber=AcomDC_1203_2015-->