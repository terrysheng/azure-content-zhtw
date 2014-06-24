### 透過編輯器安裝

1.  [安裝 Git][1]。
    
    <div  class="dev-callout" markdown="1"></div>
	
**注意**
在 Windows 中，您也需要將 Git 可執行檔新增至 PATH 環境變數。


    


2.  在專案的根目錄中建立名為 **composer.json** 的檔案，並新增下列程式碼：
    
         {
        "require": {
        "microsoft/windowsazure": "*"
             },			
        "repositories": [
                 {
        "type":"pear",
        "url":"http://pear.php.net"
                 }
             ],
        "minimum-stability":"dev"
         }

3.  將 **[composer.phar][2]** 下載到專案根目錄中。

4.  開啟命令提示字元，在專案根目錄中執行此命令
    
         php composer.phar install
### 手動安裝

若要手動下載和安裝 PHP Client Libraries for Azure，請依照下列步驟進行：

1.  從 [GitHub][3] 下載含有程式庫的 .zip 封存檔。或者，分岔儲存機制並複製到本機電腦(後面這個選項需要有 GitHub
    帳戶並在本機安裝 Git)。
    
    <div  class="dev-callout" markdown="1">
 	</div>
**注意**
PHP Client Libraries for Azure 相依於 [HTTP\_Request2][4]、[Mail\_mime][5] 和
[Mail\_mimeDecode][6] PEAR 封裝。建議使用 [PEAR
封裝管理員][7]來安裝這些封裝，以解決這些相依性。


   


2.  將已下載之封存檔的 `WindowsAzure` 目錄複製到應用程式目錄結構中。

如需關於安裝 PHP Client Libraries for Azure 的詳細資訊 (包括關於以 PEAR
封裝來安裝的詳細資訊)，請參閱[下載 Azure SDK for PHP](../php-download-sdk/)(英文)。



[1]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[2]: http://getcomposer.org/composer.phar
[3]: http://go.microsoft.com/fwlink/?LinkId=252719
[4]: http://pear.php.net/package/HTTP_Request2
[5]: http://pear.php.net/package/Mail_mime
[6]: http://pear.php.net/package/Mail_mimeDecode
[7]: http://pear.php.net/manual/en/installation.php
