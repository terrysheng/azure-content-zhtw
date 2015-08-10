#### 透過 Windows PowerShell for StorSimple 安裝一般 Hotfix

1. 連接到裝置序列主控台。如需詳細資訊，請參閱[連接到序列主控台](#connect-to-the-serial-console)。

2. 在序列主控台功能表中，選取選項 1 [使用完整存取權登入]。輸入密碼。預設密碼為 **Password1**。

3. 在命令提示字元中，輸入：

    `Start-HcsHotfix`

       >[AZURE.IMPORTANT]> > - 這個命令只適用於一般的 Hotfix。您只需在某一個控制器上執行此命令，就能同時更新這兩個控制器。> - 您可能會在更新程序期間注意到控制器容錯移轉。不過，容錯移轉並不會影響系統的可用性或運作。

4. 出現提示時，請提供包含 Hotfix 檔案的網路共用資料夾所在路徑。

5. 系統將提示您進行確認。輸入 **Y** 繼續安裝 Hotfix。

<!---HONumber=July15_HO5-->