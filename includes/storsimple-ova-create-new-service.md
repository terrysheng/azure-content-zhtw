#### 建立新服務

1.  使用您的 Microsoft 帳戶認證登入 Azure 傳統入口網站，URL 如下：[https://manage.windowsazure.com/](https://manage.windowsazure.com/)

2.  在入口網站中，按一下 [新增] > [資料服務] > [StorSimple Manager] > [快速建立]。

3.  在顯示的表單中，執行下列動作：

	1.  為服務提供唯一的 [名稱]。這是可以用來識別服務的易記名稱。名稱長度可介於 2 到 50 個字元之間，且可以是字母、數字和連字號。名稱必須以字母或數字為開頭或結尾。

	2.  對於要管理 StorSimple 虛擬裝置的服務，請從 [受管理的裝置類型] 下拉式清單中選擇 [虛擬裝置系列]。

	3.  提供服務的 [位置]。位置是指您要部署裝置的地理區域。

	 -   如果您在 Azure 中還有其他您想利用 StorSimple 裝置來部署的工作負載，建議您使用該資料中心。

   	 -   StorSimple Manager 和 Azure 儲存體可以分別位於兩個不同的位置。如此一來，您需要分別建立 StorSimple Manager 和 Azure 儲存體帳戶。若要建立 Azure 儲存體帳戶，請前往入口網站中的 [Azure 儲存體服務]，並依照[建立 Azure 儲存體帳戶](storage-create-storage-account.md#create-a-storage-account)中的步驟進行。建立帳戶之後，請依照[設定服務的新儲存體帳戶](#optional-step-configure-a-new-storage-account-for-the-service)中的步驟，將此帳戶新增到 StorSimple Manager 服務中。
   	 
   	 	
	1.  從下拉式清單選擇 [訂用帳戶]。訂用帳戶會連結到您的帳單帳戶。當您只有一個訂用帳戶時，這個欄位不會出現。

	1.  選取 [建立新的 Azure 儲存體帳戶]，以自動向此服務建立儲存體帳戶。這個儲存體帳戶將會有特殊的名稱，例如「storsimplebwv8c6dcnf」。如果您需要讓資料存放在不同的位置，請清除此核取方塊。

	1.  按一下 [建立 StorSimple Manager] 來建立服務。

		![](./media/storsimple-ova-create-new-service/image1m-include.png)

	系統會將您導向至 [服務] 登陸頁面。服務建立需要幾分鐘的時間。當帳戶成功建立之後，系統將會通知您。

	![](./media/storsimple-ova-create-new-service/image2-include.png)

	服務狀態會變更為 [使用中]。

<!---HONumber=AcomDC_0309_2016-->