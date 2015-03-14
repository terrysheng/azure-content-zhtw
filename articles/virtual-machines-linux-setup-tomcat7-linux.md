<properties 
	pageTitle="如何透過 Microsoft Azure 在 Linux 虛擬機器上設定 Tomcat7" 
	description="學習如何透過 Microsoft Azure 使用執行 Linux 的 Azure 虛擬機器 (VM) 設定 Tomcat7。" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="NingKuang" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/27/2014" 
	ms.author="ningk"/>

#如何透過 Microsoft Azure 在 Linux 虛擬機器上設定 Tomcat7 

Apache Tomcat (或簡稱 Tomcat，先前也稱為 Jakarta Tomcat) 是由 Apache Software Foundation (ASF) 開發的開放原始碼網頁伺服器與 Servlet 容器。Tomcat 實作 Sun Microsystems 的 Java Servlet 與 JavaServer Pages (JSP) 規格，並提供執行 Java 程式碼的純 Java HTTP Web 伺服器環境。在最簡單的組態中，Tomcat 會在單一作業系統處理序中執行。此程序會執行 Java 虛擬機器 (JVM)。每個從瀏覽器對 Tomcat 發出的 HTTP 要求會由 Tomcat 處理序中個別的執行緒處理。  

在本指南中，您將在 Linux 映像上安裝 Tomcat7，並將它部署在 Microsoft Azure。  

您將了解：  

-	如何在 Azure 中建立虛擬機器。
-	如何準備用於 Tomcat7 的虛擬機器。
-	如何安裝 Tomcat7。

假設讀者已具備 Azure 訂用帳戶。如果沒有，您可以在 [http://azure.microsoft.com](http://azure.microsoft.com) 註冊免費試用。如果您擁有 MSDN 訂閱，請參閱 [Microsoft Azure 特價：MSDN、MPN、和 Bizspark 優惠](http://azure.microsoft.com/pricing/member-offers/msdn-benefits/?c=14-39)。若要深入了解 Azure，請參閱[什麼是 Azure？](http://azure.microsoft.com/overview/what-is-azure/)。

本主題假設您已具備使用 Tomcat 和 Linux 的基本知識。  

##第 1 階段：建立映像
在這個階段，您將在 Azure 中使用 Linux 映像建立虛擬機器。  

###步驟 1：產生 SSH 驗證金鑰
SSH 對系統管理員而言是很重要的工具。不過，根據由人類決定的密碼來設定存取安全性並不是最佳做法。惡意使用者能夠根據使用者名稱和弱勢密碼，強行進入您的系統。

好消息是，確實有可以保持遠端存取開啟，且不需擔心密碼的方法。方法是由使用非對稱密碼編譯的驗證所組成。使用者的私密金鑰就是一種授與驗證的金鑰。您甚至可以鎖定使用者帳戶完全不允許密碼驗證。 

此方法的另一項優點是您不需要不同的密碼來登入到不同的伺服器。您可以在所有伺服器上使用個人私密金鑰進行驗證，這樣您就不需記憶多個密碼。 

您也可以透過此方法要求密碼以進行登入。

依照下列步驟來產生 SSH 驗證金鑰。

1.	從下列位置下載並安裝 puttygen：[http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) 
2.	執行 PUTTYGEN.EXE。
3.	按一下 [Generate] (產生) 來產生金鑰。在這個程序中，您可以在視窗中的空白區域移動滑鼠來提高隨機性。  
![][1]
4.	在產生程序之後，Puttygen.exe 會顯示產生的金鑰。例如：  
![][2]
5.	選取並複製 [金鑰] (Key) 中的公開金鑰，並將它儲存在名為 publicKey.pem 的檔案中。不要按 [Save public key] (儲存公開金鑰)，因為儲存的公開金鑰檔案格式與我們想要的公開金鑰不同。
6.	按一下 [Save private key] (儲存私密金鑰)，並將它儲存在名為 privateKey.ppk 的檔案。 

###步驟 2：在 Azure Preview 入口網站中建立映像。
在 [Azure Preview 入口網站](https://portal.azure.com/)中，按一下工作列中的 [新增]，根據您的需求選擇 Linux 映像來建立映像。下列範例使用 Ubuntu 14.04 映像。 
![][3]
 
對於 [主機名稱]，指定您和網際網路用戶端將用來存取此虛擬機器的 URL 名稱。定義 DNS 名稱的最後一個部分，例如 tomcatdemo，然後 Azure 會產生如 tomcatdemo.cloudapp.net 的 URL。  

對於 [SSH 驗證金鑰]，從 **publicKey.pem** 檔案複製金鑰值，其中包含 puttygen 所產生的公開金鑰。  
![][4]
  
視需要設定其他設定，然後按一下 [建立。  

##第 2 階段：準備用於 Tomcat7 的虛擬機器
在這個階段，您將設定 Tomcat 流量的端點，然後連線到新的虛擬機器。
###步驟 1：開啟要允許 Web 存取的 HTTP 連接埠
Azure 中的端點是由一種通訊協定 (TCP 或 UDP) 以及公用和私用連接埠所組成。私用連接埠是服務接聽虛擬機器的連接埠。公用連接埠是 Azure 雲端服務接聽外部連入 (網際網路流量) 的連接埠。  

TCP 連接埠 8080 是 Tomcat 接聽的預設連接埠號碼。在 Azure 端點開啟這個連接埠，可讓您和其他網際網路用戶端存取 Tomcat 頁面。  

1.	在 Azure Preview 入口網站中，按一下 [瀏覽] -> [虛擬機器]，然後按一下您所建立的虛擬機器。  
![][5]
2.	若要將端點新增至虛擬機器，請按一下 [端點] 方塊。
![][6] 
3.	按一下 [新增]。  
	1.	針對「端點」，在 [端點] 中輸入端點的名稱，然後在 [公用連接埠] 輸入 80。  
	  
		如果設定為 80，不需要在 URL 中包含連接埠號碼就可讓您存取 Tomcat。例如，http://tomcatdemo.cloudapp.net。    

		如果您將它設定為另一個值 (例如 81)，您就必須將此連接埠號碼加入 URL 才能存取 Tomcat。例如，http://tomcatdemo.cloudapp.net:81/。
	2.	在 [私用連接埠] 輸入 8080。根據預設，Tomcat 會接聽 TCP 通訊埠 8080。如果您變更了 Tomcat 的預設接聽連接埠，則必須更新私用連接埠與 Tomcat 接聽連接埠相同。  
	![][7]
 
4.	按一下 [確定] 將端點加入您的虛擬機器。



###步驟 2：連線到您所建立的映像。
您可以選擇任何 SSH 工具來連線到您的虛擬機器。在此範例中，我們使用 Putty。  

首先，從 Azure Preview 入口網站取得您虛擬機器的 DNS 名稱。**按一下 [瀏覽]** -> [虛擬機器] -> 您的虛擬機器名稱 -> [內容]，然後查看 [內容] 磚的 [網域名稱] 欄位。  

從 [SSH] 欄位取得 SSH 連線的連接埠號碼。下列是一個範例。  
![][8]
 
在[此處](http://www.putty.org/)下載 Putty。 。  

下載之後，按一下可執行檔 PUTTY.EXE。使用來自虛擬機器內容的主機名稱和連接埠號碼設定基本選項。下列是一個範例：  
![][9]
 
在左窗格中，按一下 [連線] -> [SSH] -> [驗證]，然後按一下 [瀏覽] 指定 **privateKey.ppk** 檔案的位置，其中包含 puttygen 在「第 1 階段：建立映像」所產生的私用金鑰。下列是一個範例：  
![][10]
 
按一下 [開啟]。您可能會收到警告訊息方塊。如果您已正確設定 DNS 名稱和連接埠號碼，按一下 [是]。
![][11]  


您應該會看見下列內容：  
![][12]

輸入您在「第 1 階段：建立映像」建立虛擬機器時指定的使用者名稱。您會看到類似下列的畫面：  
![][13] 





##第 3 階段：安裝軟體
在此階段中，您會安裝 Java 執行階段環境、Tomcat 及其他 Tomcat 元件。  

###Java 執行階段環境
Tomcat 是以 Java 撰寫。有兩種類型的 Java 開發套件 (JDK) (OpenJDK 和 Oracle JDK)，您可以選擇您要使用的那一個。  

>AZURE.NOTE: 這兩種 JDK 具備之 Java API 中的類別程式碼幾乎相同，但虛擬機器的程式碼卻不一樣。對於程式庫，OpenJDK 傾向使用開放程式庫，而 Oracle 傾向使用封閉的。但是 Oracle JDK 有更多類別和一些錯誤修正，且 Oracle JDK 比 OpenJDK 穩定。 

下列命令會下載不同的 JDK。  

open-jdk   

	sudo apt-get update  
	sudo apt-get install openjdk-7-jre  

oracle-jdk  

-	從 Oracle 網站下載 JDK：  

		wget --header "Cookie: oraclelicense=accept-securebackup-cookie" http://download.oracle.com/otn-pub/java/jdk/8u5-b13/jdk-8u5-linux-x64.tar.gz  

-	建立包含 JDK 檔案的目錄：  

		sudo mkdir /usr/lib/jvm  

-	將 JDK 檔案解壓縮到 /usr/sbin lib/jvm/目錄：  

		sudo tar -zxf jdk-8u5-linux-x64.tar.gz  -C /usr/lib/jvm/  

-	設定 Oracle JDK 為預設的 JVM：  

		sudo update-alternatives --install /usr/bin/java java /usr/lib/jvm/jdk1.8.0_05/bin/java 100  
		sudo update-alternatives --install /usr/bin/javac javac /usr/lib/jvm/jdk1.8.0_05/bin/javac 100  

####測試：
您可以使用類似下列的命令，測試是否已正確安裝 Java 執行階段環境：  

	java -version  

如果您安裝 open-jdk，您應該會看到類似下列的訊息：
![][14] 

如果您安裝 oracle-jdk，您應該會看到類似下列的訊息：
![][15] 

###Tomcat7
使用下列命令安裝 Tomcat7：  

	sudo apt-get install tomcat7  

如果您不是使用Tomcat7，請使用此命令其他適當的用法。  

####測試：

若要檢查是否已成功安裝 Tomcat7，瀏覽至 Tomcat 伺服器的 DNS 名稱 (http://tomcatexample.cloudapp.net/ is the example URL in this article)。如果您看到類似下列的頁面，表示您已經成功安裝 Tomcat7。
![][16] 


###安裝其他 Tomcat 元件
還有其他您也可以安裝的 Tomcat 元件。  

使用 **sudo apt-cache search tomcat7** 命令來查看所有可用的元件。下列命令是安裝一些有用組件的範例。  

	sudo apt-get install tomcat7-admin      #admin web applications
	sudo apt-get install tomcat7-user         #tools to create user instances  

##第 4 階段：設定 Tomcat
在這個階段，您將管理 tomcat。
###啟動和停止 Tomcat7
當您安裝 Tomcat7 伺服器時它會自動啟動。您也可以使用下列命令自行啟動它：   

	sudo /etc/init.d/tomcat7 start

停止 Tomcat7：  

	sudo /etc/init.d/tomcat7 stop 

檢視 Tomcat7 狀態：  

	sudo /etc/init.d/tomcat7 status

重新啟動 Tomcat 服務：  

	sudo /etc/init.d/tomcat7 restart

###Tomcat 管理
您可以使用下列命令編輯 Tomcat 使用者組態檔案來設定您的系統管理員認證：  

	sudo vi  /etc/tomcat7/tomcat-users.xml   

下列是一個範例：  
![][17]  

>AZURE.NOTE: 為系統管理員使用者名稱建立強式密碼。  

編輯此檔案之後, 您應該使用下列命令重新啟動 Tomcat7 服務，以確保所做的變更生效：  

	sudo /etc/init.d/tomcat7 restart  

開啟您的瀏覽器，並輸入 URL **http://<your tomcat server DNS name>/manager/html**。在這篇文章中的範例，URL 是 http://tomcatexample.cloudapp.net/manager/html。  

連線之後，您應該會看到類似下列的內容：  
![][18]
 
##常見問題

###無法從網際網路存取使用 Tomcat 和 Moodle 的虛擬機器

-	**徵兆**
Tomcat 正在執行，但是使用瀏覽器無法看到 Tomcat 預設頁面。
-	**可能的根本原因**
	1.	Tomcat 接聽連接埠與您虛擬機器針對 Tomcat 流量的端點私用連接埠不同。  
	
		檢查您的公用連接埠和私用連接埠端點設定，確定私用連接埠與 Tomcat 接聽連接埠相同。請參閱「第 1 階段：建立映像」對設定虛擬機器端點的指示。  

		若要判斷 Tomcat 接聽連接埠，請開啟 /etc/httpd/conf/httpd.conf (Red Hat 版本) 或 /etc/tomcat7/server.xml (Debian 版本)。根據預設，Tomcat 接聽連接埠是 8080。下列是一個範例：  

			<Connector port="8080" protocol="HTTP/1.1"  connectionTimeout="20000"  URIEncoding="UTF-8"            redirectPort="8443" />  

		您正在使用像是 Debian 或 Ubuntu 的虛擬機器，您希望變更預設的 Tomcat 接聽連接埠 (例如 8081)，您也應該開啟作業系統的連接埠。首先，開啟「設定檔」：  

			sudo vi /etc/default/tomcat7  

		然後移除最後一行的註解，並將「否」變更為「是」。

			AUTHBIND=yes

	2.	防火牆已停用 Tomcat 的接聽連接埠。

		如果您只能從本機主機看到 Tomcat 預設頁面，則問題很可能是 Tomcat 接聽的連接埠被防火牆封鎖。您可以使用 w3m 工具瀏覽網頁。下列命令會安裝 w3m，並瀏覽至 Tomcat 預設頁面：  

			sudo yum  install w3m w3m-img
			w3m http://localhost:8080  

-	**解決方法** 
	1. 如果 Tomcat 接聽連接埠，與端點傳送流量到虛擬機器的私用連接埠不同，您需要將端點的私用連接埠變更為與 Tomcat 接聽連接埠相同。   
	
	2.	如果問題是防火牆/iptables 造成，請在 /etc/sysconfig/iptables 中加入下列幾行：  
	
			-A INPUT -p tcp -m tcp --dport 80 -j ACCEPT
			-A INPUT -p tcp -m tcp --dport 443 -j ACCEPT  

		請注意，如為 https 流量才需要第二行。  

		請確定這幾行位於會全域限制存取的任何程式行上方，例如下列這一行：  

			-A INPUT -j REJECT --reject-with icmp-host-prohibited  

		若要重新載入 iptables，請執行下列命令：  

			service iptables restart  

		這在 CentOS 6.3 上已經過測試。

###上傳您的專案檔案到 /var/lib/tomcat7/webapps/ 時權限遭拒  

-	**徵兆**
當您使用任何 SFTP 用戶端 (例如 FileZilla) 連線到虛擬機器，並瀏覽到 /var/lib/tomcat7/webapps/ 發佈網站時，您收到類似下列的錯誤訊息：  

		status:	Listing directory /var/lib/tomcat7/webapps
		Command:	put "C:\Users\liang\Desktop\info.jsp" "info.jsp"
		Error:	/var/lib/tomcat7/webapps/info.jsp: open for write: permission denied
		Error:	File transfer failed

-	**可能的根本原因**
您沒有存取 /var/lib/tomcat7/webapps 資料夾的權限。  
-	**解決方法**
您需要從 root 帳戶取得權限。您可以從 root 將該資料夾的擁有權變更為佈建機器時所使用的使用者名稱。下列是一個 azureuser 帳戶名稱的範例：  

		sudo chown azureuser -R /var/lib/tomcat7/webapps

	使用-R 選項對目錄內的所有檔案也一併套用權限。  

	請注意，此命令也適用於目錄。-R 選項會變更目錄內所有檔案和目錄的權限。下列是一個範例：  

		sudo chown -R username:group directory  

	此命令會變更目錄內所有檔案和目錄以及目錄本身的擁有權 (使用者和群組兩者)。  

	下列命令只會變更資料夾目錄的權限，卻不會變更目錄內檔案和資料夾的權限。  

		sudo chown username:group directory



[1]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-01.png
[2]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-02.png
[3]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-03.png
[4]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-04.png
[5]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-05.png
[6]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-06.png
[7]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-07.png
[8]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-08.png
[9]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-09.png
[10]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-10.png
[11]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-11.png
[12]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-12.png
[13]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-13.png
[14]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-14.png
[15]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-15.png
[16]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-16.png
[17]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-17.png
[18]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-18.png

<!--HONumber=42-->
