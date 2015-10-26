<properties
    pageTitle="如何搭配使用 Azure 從屬外掛程式與 Jenkins 連續整合 | Microsoft Azure"
    description="說明如何搭配使用 Azure 從屬外掛程式與 Jenkins 連續整合。"
	services="storage"
	documentationCenter="java"
	authors="rmcmurray"
	manager="wpickett"
	editor="jimbe" />

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="Java"
	ms.topic="article"
	ms.date="09/01/2015"
    ms.author="v-dedomi"/>

# 如何搭配使用 Azure 從屬外掛程式與 Jenkins 連續整合

執行分散式組建時，您可使用適用於 Jenkins 的 Azure 從屬外掛程式，在 Azure 上佈建從屬節點。

## 安裝 Azure 從屬外掛程式
1. 在 Jenkins 儀表板中，按一下 [**管理 Jenkins**]。
2. 在 [管理 Jenkins] 頁面中，按一下 [管理外掛程式]。
3. 按一下 [Available] 索引標籤。
4. 在可用外掛程式清單上方的 [篩選] 欄位中輸入 **Azure**，讓清單只顯示相關外掛程式。

	如果您選擇以捲動的方式查看可用的外掛程式清單，您會在 [叢集管理和分散式組建] 區段下找到 Azure 從屬外掛程式。

5. 選取 [Azure 從屬外掛程式] 的核取方塊。
6. 按一下 [**直接安裝而不重新啟動**] 或 [**立即下載並於重新啟動後安裝**]。

現在外掛程式已安裝完畢，接下來的步驟是在 Azure 訂用帳戶設定檔中設定外掛程式，以及建立用來建立從屬節點虛擬機器的範本。


## 在 Azure 訂用帳戶設定檔中設定 Azure 從屬外掛程式

訂用帳戶設定檔也稱為發佈設定，它是一個 XML 檔案，內含要在開發環境中使用 Azure 所需具備的安全認證和一些額外資訊。若要設定 Azure 從屬外掛程式，您需要：

* 訂用帳戶識別碼
* 訂用帳戶的管理憑證

您可以在訂用帳戶設定檔中找到這些資訊。如果您沒有訂用帳戶設定檔，可以從[訂用帳戶網站](https://manage.windowsazure.com/publishsettings/Index?SchemaVersion=2.0)下載。以下是訂用帳戶設定檔的範例。

	<?xml version="1.0" encoding="utf-8"?>

		<PublishData>

  		<PublishProfile SchemaVersion="2.0" PublishMethod="AzureServiceManagementAPI">

    	<Subscription

      		ServiceManagementUrl="https://management.core.windows.net"

      		Id="<Subscription ID value>"

      		Name="Pay-As-You-Go"
			ManagementCertificate="<Management certificate value>" />

  		</PublishProfile>

	</PublishData>

有了訂用帳戶設定檔後，請遵循下列步驟來設定 Azure 從屬外掛程式：

1. 在 Jenkins 儀表板中，按一下 [**管理 Jenkins**]。
2. 按一下 [**設定系統**]。
3. 向下捲動頁面來找出 [**雲端**] 區段。
4. 按一下 [**新增雲端 > Microsoft Azure**]。

	![雲端區段](./media/azure-slave-plugin-for-jenkins/jenkins-cloud-section.png)

	此動作會顯示一些欄位，您必須在其中輸入訂用帳戶的詳細資料。

	![訂用帳戶組態](./media/azure-slave-plugin-for-jenkins/jenkins-account-configuration-fields.png)

5. 從訂用帳戶設定檔複製訂用帳戶識別碼和管理憑證值，並貼至適當欄位。

	在複製訂用帳戶識別碼和管理憑證時，請勿將括住值的引號也包括進來。

6. 按一下 [驗證組態]。
7. 驗證組態正確無誤後，按一下 [**儲存**]。

## 設定 Azure 從屬外掛程式的虛擬機器範本

虛擬機器範本可定義外掛程式用來在 Azure 上建立從屬節點的參數。在下列步驟中，我們將建立 Ubuntu 虛擬機器的範本。

1. 在 Jenkins 儀表板中，按一下 [**管理 Jenkins**]。
2. 按一下 [**設定系統**]。
3. 向下捲動頁面來找出 [**雲端**] 區段。

4. 在 [雲端] 區段內，找到 [新增 Azure 虛擬機器範本]，然後按一下 [新增]。

	![新增 VM 範本](./media/azure-slave-plugin-for-jenkins/jenkins-add-vm-template.png)

	此動作將會顯示一些欄位，供您輸入所要建立之範本的相關詳細資料。

	![空白的一般組態](./media/azure-slave-plugin-for-jenkins/jenkins-slave-template-general-configuration-blank.png)

5. 在 [名稱] 方塊中，輸入 Azure 雲端服務名稱。如果您輸入的名稱會參照現有雲端服務，便會在該服務中佈建虛擬機器。否則，Azure 會建立一個新的。

6. 在 [說明] 方塊中，輸入您要建立之範本的說明文字。此資訊僅供記錄之用，不會用於佈建虛擬機器。
7. [標籤] 方塊可用來識別您要建立的範本，而且後續建立 Jenkins 工作時也會用來參照範本。為了達到我們的目的，請在此方塊中輸入 **linux**。
8. 在 [區域] 清單中，按一下即將建立虛擬機器的區域。
9. 在 [虛擬機器大小] 清單中，按一下適當的大小。
10. 在 [儲存體帳戶名稱] 方塊中，指定即將建立虛擬機器的儲存體帳戶。請確定它與您將要使用的雲端服務位在相同的區域中。如果您想要建立新的儲存體，可以將此方塊保留空白。
11. 保留時間會指定 Jenkins 要等待幾分鐘的時間才將閒置的從屬節點刪除。請讓此欄位保持使用預設值 60。您也可以選擇關閉從屬節點，而不是在其閒置時將它刪除。若要關閉節點，請勾選 [在保留時間過後僅關閉 (不要刪除)] 核取方塊。
12. 在 [使用量] 清單中，按一下將使用此從屬節點的適當條件。現在只先按一下 [盡可能利用此節點] 即可。

	到目前為止，您的表單應該會與下圖類似：

	![檢查點一般範本組態](./media/azure-slave-plugin-for-jenkins/jenkins-slave-template-general-configuration.png)

	下一步是提供您想要在其中建立從屬節點之作業系統映像的詳細資料。

13. 在 [映像系列或識別碼] 方塊中，您必須指定要在虛擬機器上安裝的系統映像。您可以從映像系列清單中進行選取，或指定自訂映像。

	如果您想從映像系列清單中進行選取，請輸入映像系列名稱的第一個字元 (需區分大小寫)。例如，輸入 **U** 將會顯示 Ubuntu Server 系列的清單。從清單中進行選取後，Jenkins 會在佈建虛擬機器時，使用該系列中該系統映像的最新版本。

	![作業系統映像清單範例](./media/azure-slave-plugin-for-jenkins/jenkins-os-family-list-sample.png)

	如果您想要改用自訂映像，請輸入該自訂映像的名稱。清單中不會顯示自訂映像名稱，因此您必須確實輸入正確的名稱。

	在本教學課程中，請輸入 **U** 來顯示 Ubuntu 映像清單，然後按一下 [Ubuntu Server 14.04 LTS]。

14. 在 [啟動方法] 清單中，按一下 [SSH]。
15. 複製下方的指令碼，並貼到 [Init 指令碼] 方塊。

		# Install Java
		sudo apt-get -y update

		sudo apt-get install -y openjdk-7-jdk

		sudo apt-get -y update --fix-missing

		sudo apt-get install -y openjdk-7-jdk



		# Install git

		sudo apt-get install -y git



		#Install ant

		sudo apt-get install -y ant

		sudo apt-get -y update --fix-missing

		sudo apt-get install -y ant

	Init 指令碼就會在虛擬機器建立完成後執行。在此範例中，指令碼會安裝 Java、Git 和 ant。

16. 在 [使用者名稱] 和 [密碼] 方塊中，請為即將在虛擬機器上建立的系統管理員帳戶輸入偏好使用的值。
17. 按一下 [驗證範本] 以檢查指定的參數是否有效。
18. 按一下 [儲存]。


## 建立在 Azure 從屬節點上執行的 Jenkins 工作

在本節中，您將建立在 Azure 的從屬節點上執行的 Jenkins 工作。您必須在 GitHub 上啟動自己的專案才能繼續。

1. 在 Jenkins 儀表板中，按一下 [**新增項目**]。
2. 輸入要建立之工作的名稱。
3. 針對專案類型，請按一下 [自由樣式專案]。
4. 按一下 [**確定**]。
5. 在工作組態頁面中，選取 [**限制可以執行這個專案的位置**]。
6. 在 [標籤運算式] 方塊中，輸入 **linux**。上一節我們建立了名為 **linux** 的從屬範本，這就是我們要在這裡指定的項目。
7. 在 [**組件**] 區段中，按一下 [**新增組件步驟**]，然後選取 [**執行殼層**]。
8. 編輯下列指令碼，將 **(GitHub 帳戶名稱)**、**(專案名稱)** 和 **(專案目錄)** 替換為適當值，並在出現的文字區域中貼上編輯過的指令碼。

		# Clone from git repo

		currentDir="$PWD"

		if [ -e (your project directory) ]; then

  			cd (your project directory)

  			git pull origin master

		else

  			git clone https://github.com/(your GitHub account name)/(your project name).git

		fi

		# change directory to project

		cd $currentDir/(your project directory)



		#Execute build task

		ant

9. 按一下 [儲存]。
10. 在 Jenkins 儀表板中，將滑鼠指標移到您剛才建立的工作上，然後按一下向下箭頭以顯示工作選項。
11. 按一下 [立即建置]。

Jenkins 會隨即使用上一節建立的範本來建立從屬節點，並執行您在建置步驟中為這項工作指定的指令碼。

<!---HONumber=Oct15_HO3-->