<properties 
	pageTitle="使用行動服務將影像上傳至 Blob 儲存體 (Android) | 行動服務"
	description="了解如何使用行動服務將影像上傳至 Azure 儲存體，並從您的 Android 應用程式存取該影像。"
	services="mobile-services"
	documentationCenter="android"
	authors="RickSaling"
	manager="dwrede"
	editor=""/>

<tags 
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="09/02/2015"
	ms.author="ricksal"/>

# 將影像從 Android 裝置上傳至 Azure 儲存體

[AZURE.INCLUDE [mobile-services-selector-upload-data-blob-storage](../../includes/mobile-services-selector-upload-data-blob-storage.md)]

本主題說明如何啟用 Android Azure 行動服務應用程式，以便將影像上傳至 Azure 儲存體。

行動服務會使用 SQL Database 儲存資料。不過，將二進位大型物件 (BLOB) 資料儲存在 Azure 儲存體中會更有效率。在本教學課程中，您會了解如何啟用行動服務快速入門應用程式，以便使用 Android 相機拍照，並將影像上傳至 Azure 儲存體。


## 若要開始，您需要：

開始本教學課程之前，您必須先完成行動服務快速入門：[開始使用行動服務]。

本教學課程還需要下列各項：

+ [Azure 儲存體帳戶](../storage-create-storage-account.md)
+ 內建相機的 Android 裝置

## 應用程式的運作方式

上傳相片影像的程序有多個步驟：

- 您要先拍攝相片，並將 TodoItem 資料列插入 SQL Database 中，該資料庫含有 Azure 儲存體所使用的新中繼資料欄位。
- 新的行動服務 SQL **插入**指令碼會要求 Azure 儲存體提供共用存取簽章 (SAS)。
- 該指令碼會將 SAS 及 Blob 的 URI 傳回用戶端。
- 用戶端就能使用 SAS 及 Blob URI 上傳相片。

那麼，什麼是 SAS？

將上傳資料所需的認證儲存至用戶端應用程式中的 Azure 儲存體服務，是不安全的做法。您應該將這些認證儲存在您的行動服務中，並使用這些認證來產生共用存取簽章 (SAS)，以便提供上傳新影像的權限。行動服務會以安全的方式，將 SAS (5 分鐘後到期的認證) 傳回用戶端應用程式。應用程式會使用此暫存認證來上傳影像。如需詳細資訊，請參閱[共用存取簽章，第 1 部分：了解 SAS 模型](storage-dotnet-shared-access-signature-part-1.md)

>[AZURE.NOTE] [Here](https://github.com/Azure/mobile-services-samples/tree/master/UploadImages) 是此應用程式中已完成的用戶端原始程式碼部分。

## 在管理入口網站中更新已註冊的插入指令碼

[AZURE.INCLUDE [mobile-services-configure-blob-storage](../../includes/mobile-services-configure-blob-storage.md)]


## 更新快速入門應用程式，以拍攝及上傳影像。

### 參照 Azure 儲存體 Android 用戶端程式庫

1. 若要將參考加入至程式庫 (位於 [應用程式] > **build.gradle** 檔案)，請在 `dependencies` 區段中加入這一行：

		compile 'com.microsoft.azure.android:azure-storage-android:0.6.0@aar'


2. 將 `minSdkVersion` 值變更為 15 (相機 API 所需)。

3. 按一下 [同步處理專案與 Gradle 檔案] 圖示。

### 更新相機和儲存體的資訊清單檔案

1. 若要指定應用程式，必須有可用的相機，方法是在 **AndroidManifest.xml** 中加入這一行：

	    <uses-feature android:name="android.hardware.camera"
	        android:required="true" />

2. 若要指定應用程式，必須有寫入外部儲存體的權限，方法是在 **AndroidManifest.xml** 中加入這一行：

	    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />

	請注意，Android 外部儲存體不一定是 SD 記憶卡：如需詳細資訊，請參閱[儲存檔案](http://developer.android.com/training/basics/data-storage/files.html)。

### 更新新使用者介面的資源檔案

1. 在 [值] 目錄的 **strings.xml** 檔案中加入下列內容，以新增新按鈕的標題：

	    <string name="preview_button_text">Take Photo</string>
	    <string name="upload_button_text">Upload</string>

2. 在 **res = > layout** 資料夾的 **activity\_to\_do.xml** 檔案中，在 [新增] 按鈕的現有程式碼之前加入此程式碼。

         <Button
             android:id="@+id/buttonPreview"
             android:layout_width="120dip"
             android:layout_height="wrap_content"
             android:onClick="takePicture"
             android:text="@string/preview_button_text" />

3. 以下列程式碼取代 [新增] 按鈕項目：

         <Button
             android:id="@+id/buttonUpload"
             android:layout_width="100dip"
             android:layout_height="wrap_content"
             android:onClick="uploadPhoto"
             android:text="@string/upload_button_text" />


### 新增拍攝相片的程式碼

1. 在 **ToDoActivity.java** 中加入此程式碼，以建立具有唯一名稱的**檔案**物件。

		// Create a File object for storing the photo
	    private File createImageFile() throws IOException {
	        // Create an image file name
	        String timeStamp = new SimpleDateFormat("yyyyMMdd_HHmmss").format(new Date());
	        String imageFileName = "JPEG_" + timeStamp + "_";
	        File storageDir = Environment.getExternalStoragePublicDirectory(Environment.DIRECTORY_PICTURES);
	        File image = File.createTempFile(
	                imageFileName,  /* prefix */
	                ".jpg",         /* suffix */
	                storageDir      /* directory */
	        );
	        return image;
	    }

5. 加入此程式碼以啟動 Android 相機應用程式。接著您就可以拍照，確定照片令您滿意後，請按下 [儲存]，該照片就會儲存在您剛才建立的檔案中。

		// Run an Intent to start up the Android camera
	    static final int REQUEST_TAKE_PHOTO = 1;
	    public Uri mPhotoFileUri = null;
	    public File mPhotoFile = null;
		
	    public void takePicture(View view) {
	        Intent takePictureIntent = new Intent(MediaStore.ACTION_IMAGE_CAPTURE);
	        // Ensure that there's a camera activity to handle the intent
	        if (takePictureIntent.resolveActivity(getPackageManager()) != null) {
	            // Create the File where the photo should go
	            try {
	                mPhotoFile = createImageFile();
	            } catch (IOException ex) {
	                // Error occurred while creating the File
	                //
	            }
	            // Continue only if the File was successfully created
	            if (mPhotoFile != null) {
	                mPhotoFileUri = Uri.fromFile(mPhotoFile);
	                takePictureIntent.putExtra(MediaStore.EXTRA_OUTPUT, mPhotoFileUri);
	                startActivityForResult(takePictureIntent, REQUEST_TAKE_PHOTO);
	            }
	        }
	    }

### 加入程式碼以將相片檔案上傳至 Blob 儲存體


1. 首先，我們先將此程式碼加入至 **ToDoItem.java**，為 `ToDoItem` 物件新增一些新的中繼資料欄位。

		/**
	     *  imageUri - points to location in storage where photo will go
	     */
	    @com.google.gson.annotations.SerializedName("imageUri")
	    private String mImageUri;
	
	    /**
	     * Returns the item ImageUri
	     */
	    public String getImageUri() {
	        return mImageUri;
	    }
	
	    /**
	     * Sets the item ImageUri
	     *
	     * @param ImageUri
	     *            Uri to set
	     */
	    public final void setImageUri(String ImageUri) {
	        mImageUri = ImageUri;
	    }
	
	    /**
	     * ContainerName - like a directory, holds blobs
	     */
	    @com.google.gson.annotations.SerializedName("containerName")
	    private String mContainerName;
	
	    /**
	     * Returns the item ContainerName
	     */
	    public String getContainerName() {
	        return mContainerName;
	    }
	
	    /**
	     * Sets the item ContainerName
	     *
	     * @param ContainerName
	     *            Uri to set
	     */
	    public final void setContainerName(String ContainerName) {
	        mContainerName = ContainerName;
	    }
	
	    /**
	     *  ResourceName
	     */
	    @com.google.gson.annotations.SerializedName("resourceName")
	    private String mResourceName;
	
	    /**
	     * Returns the item ResourceName
	     */
	    public String getResourceName() {
	        return mResourceName;
	    }
	
	    /**
	     * Sets the item ResourceName
	     *
	     * @param ResourceName
	     *            Uri to set
	     */
	    public final void setResourceName(String ResourceName) {
	        mResourceName = ResourceName;
	    }
	
	    /**
	     *  SasQueryString - permission to write to storage
	     */
	    @com.google.gson.annotations.SerializedName("sasQueryString")
	    private String mSasQueryString;
	
	    /**
	     * Returns the item SasQueryString
	     */
	    public String getSasQueryString() {
	        return mSasQueryString;
	    }
	
	    /**
	     * Sets the item SasQueryString
	     *
	     * @param SasQueryString
	     *            Uri to set
	     */
	    public final void setSasQueryString(String SasQueryString) {
	        mSasQueryString = SasQueryString;
	    }

2. 以下列程式碼取代 0 參數建構函式：

	    /**
	     * ToDoItem constructor
	     */
	    public ToDoItem() {
	        mContainerName = "";
	        mResourceName = "";
	        mImageUri = "";
	        mSasQueryString = "";
	    }

3. 以下列程式碼取代多參數建構函式：

	    /**
	     * Initializes a new ToDoItem
	     *
	     * @param text
	     *            The item text
	     * @param id
	     *            The item id
	     */
	    public ToDoItem(String text,
	                    String id,
	                    String containerName,
	                    String resourceName,
	                    String imageUri,
	                    String sasQueryString) {
	        this.setText(text);
	        this.setId(id);
	        this.setContainerName(containerName);
	        this.setResourceName(resourceName);
	        this.setImageUri(imageUri);
	        this.setSasQueryString(sasQueryString);
	    }


4. 在 **ToDoActivity.java** 檔案中，以上傳影像的下列程式碼取代 **ToDoActivity.java** 中的 **addItem** 方法。

	    /**
	     * Add a new item
	     *
	     * @param view
	     *            The view that originated the call
	     */
	    public void uploadPhoto(View view) {
	        if (mClient == null) {
	            return;
	        }
	
	        // Create a new item
	        final ToDoItem item = new ToDoItem();
	
	        item.setText(mTextNewToDo.getText().toString());
	        item.setComplete(false);
	        item.setContainerName("todoitemimages");
	
	        // Use a unigue GUID to avoid collisions.
	        UUID uuid = UUID.randomUUID();
	        String uuidInString = uuid.toString();
	        item.setResourceName(uuidInString);
	
	        // Send the item to be inserted. When blob properties are set this
	        // generates an SAS in the response.
	        AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
	            @Override
	            protected Void doInBackground(Void... params) {
	                try {
		                    final ToDoItem entity = addItemInTable(item);
		
		                    // If we have a returned SAS, then upload the blob.
		                    if (entity.getSasQueryString() != null) {
		
	                       // Get the URI generated that contains the SAS
	                        // and extract the storage credentials.
	                        StorageCredentials cred = 
								new StorageCredentialsSharedAccessSignature(entity.getSasQueryString());
	                        URI imageUri = new URI(entity.getImageUri());
	
	                        // Upload the new image as a BLOB from a stream.
	                        CloudBlockBlob blobFromSASCredential =
	                                new CloudBlockBlob(imageUri, cred);
	
	                        blobFromSASCredential.uploadFromFile(mPhotoFileUri.getPath());
  	                    }
	
	                    runOnUiThread(new Runnable() {
	                        @Override
	                        public void run() {
	                            if(!entity.isComplete()){
	                                mAdapter.add(entity);
	                            }
	                        }
	                    });
	                } catch (final Exception e) {
	                    createAndShowDialogFromTask(e, "Error");
	                }
	                return null;
	            }
	        };
	
	        runAsyncTask(task);
	
	        mTextNewToDo.setText("");
	    }
	

此程式碼會將要求傳送給行動服務以插入新 TodoItem。回應內容包含 SAS，這能用來將影像從本機儲存體上傳至 Azure 儲存體中的 Blob 。


## 測試上傳影像 

1. 在 Android Studio 中按下 [執行]。在對話方塊中，選擇要使用的裝置。

2. 應用程式 UI 出現後，在標示為 [加入 ToDo 項目] 的文字方塊中輸入文字。

3. 按 [拍照]。相機應用程式啟動後，請拍攝相片。按下核取記號以接受拍攝的相片。

4. 按 [上傳]。如往常一樣注意 ToDoItem 加入至清單中的方式。

5. 在 Microsoft Azure 入口網站中，前往您的儲存體帳戶並按一下 [容器] 索引標籤，然後在清單中按一下您的容器名稱。

6. 已上傳的 Blob 檔案清單會隨即出現。選取任一項目，然後按 [下載]。

7. 瀏覽器視窗會隨即顯示您所上傳的影像。


## <a name="next-steps"> </a>後續步驟

您現在已藉由整合行動服務與 Blob 服務，而能夠安全地上傳影像，請參閱其他後端服務和整合主題：

+ [使用 SendGrid 從行動服務傳送電子郵件]
 
  了解如何使用 SendGrid 電子郵件服務將電子郵件功能新增到行動服務本主題示範如何新增伺服器端指令碼，以使用 SendGrid 傳送電子郵件。

+ [在行動服務中排程後端工作]

  了解如何使用行動服務工作排程器功能，定義可依您定義的排程執行的伺服器指令碼。

+ [行動服務伺服器指令碼參考]

  參考使用伺服器指令碼來執行伺服器端工作以及與其他 Azure 元件和外部資源整合的主題。
 
+ [行動服務 .NET 作法概念性參考]

  深入了解如何搭配使用行動服務與 .NET
  
 
<!-- Anchors. -->
[Install the Storage Client library]: #install-storage-client
[Update the client app to capture images]: #add-select-images
[Update the insert script to generate an SAS]: #update-scripts
[Upload images to test the app]: #test
[Next Steps]: #next-steps

<!-- Images. -->

[2]: ./media/mobile-services-windows-store-dotnet-upload-data-blob-storage/mobile-add-storage-nuget-package-dotnet.png


<!-- URLs. -->
[使用 SendGrid 從行動服務傳送電子郵件]: store-sendgrid-mobile-services-send-email-scripts.md
[在行動服務中排程後端工作]: mobile-services-schedule-recurring-tasks.md
[Send push notifications to Windows Store apps using Service Bus from a .NET back-end]: http://go.microsoft.com/fwlink/?LinkId=277073&clcid=0x409
[行動服務伺服器指令碼參考]: mobile-services-how-to-use-server-scripts.md
[開始使用行動服務]: mobile-services-javascript-backend-windows-store-dotnet-get-started.md

[Azure Management Portal]: https://manage.windowsazure.com/
[How To Create a Storage Account]: ../storage-create-storage-account.md
[Azure Storage Client library for Store apps]: http://go.microsoft.com/fwlink/p/?LinkId=276866
[行動服務 .NET 作法概念性參考]: mobile-services-windows-dotnet-how-to-use-client-library.md
[App settings]: http://msdn.microsoft.com/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7
 

<!---HONumber=September15_HO1-->