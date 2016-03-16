<!--author=v-sharos last changed: 11/06/15-->

#### 若要新增 StorSimple 備份原則

1. 在裝置的 [**快速入門**] 頁面上，按一下 [**備份原則**] 索引標籤。這會將您帶到 [**備份原則**] 頁面。

2. 在頁面底部，按一下 [新增]，以啟動 [新增備份原則精靈]。

    ![新增備份原則 1](./media/storsimple-add-backup-policy-u2/AddBackupPolicy1.png)

3. 在 [新增備份原則] 對話方塊的 [定義備份原則]，執行下列動作：

    1. 指定包含 3 到 150 個字元的備份原則名稱。
    2. 按一下核取方塊，將一或多個磁碟區指派給此備份原則。請注意，您無法選取使用不同雲端服務提供者的磁碟區。如果您使用多個雲端服務提供者，清單將會依據您的第一個選擇，顯示只屬於該雲端服務提供者的磁碟區。這可讓您群組磁碟區只屬於快照集中的一個雲端服務提供者。
    3. 按一下箭頭圖示 ![箭號圖示](./media/storsimple-add-backup-policy-u2/HCS_ArrowIcon-include.png) 以移至下一頁。

     ![新增備份原則 2](./media/storsimple-add-backup-policy-u2/AddBackupPolicy2.png)

4. 在 [定義排程] 下執行下列動作：
    1. 在 [備份的類型] 方塊中，從下拉式清單中選取 [雲端快照集] 或 [本機快照集]。
    2. 指定備份的頻率 (指定數字)，然後從下拉式清單中選擇 [天] 或 [週] 。
    3. 輸入保留排程。
    4. 輸入備份原則的開始日期與時間。  
    6. 按一下核取圖示 ![核取圖示](./media/storsimple-add-backup-policy-u2/HCS_CheckIcon-include.png) 以儲存原則。

新加入的原則將會以表格式檢視顯示在 [**備份原則**] 頁面。
 

<!---HONumber=AcomDC_1217_2015-->