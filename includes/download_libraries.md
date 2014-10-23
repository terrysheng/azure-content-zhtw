# 下載 Azure SDK for Java

## Azure Client Libraries for Java - 手動安裝

Azure Libraries for Java 依據 [Apache 授權 2.0 版][]發行。請按一下[這裡][]，以取得含程式庫及所有相依性的 ZIP 檔案。此檔案由 Microsoft Open Technologies, Inc. 提供。關於授權及其他資訊，請參閱 ZIP 內的 license.txt 和 ThirdPartyNotices.txt 檔案。

## Azure Libraries for Java - Maven

如果您的專案已設定為使用 Maven 來建置，請將下列相依性新增至 pom.xml 檔案。

    <dependency>
        <groupId>com.microsoft.windowsazure</groupId>
        <artifactId>microsoft-azure-api-management</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.windowsazure</groupId>
        <artifactId>microsoft-azure-api-management-compute</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.windowsazure</groupId>
        <artifactId>microsoft-azure-api-management-network</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.windowsazure</groupId>
        <artifactId>microsoft-azure-api-management-sql</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.windowsazure</groupId>
        <artifactId>microsoft-azure-api-management-storage</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.windowsazure</groupId>
        <artifactId>microsoft-azure-api-management-websites</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.windowsazure</groupId>
        <artifactId>microsoft-azure-api-media</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.windowsazure</groupId>
        <artifactId>microsoft-azure-api-servicebus</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.windowsazure</groupId>
        <artifactId>microsoft-azure-api-serviceruntime</artifactId>
        <version>n.n.n</version>
    </dependency>

在 `<version>` 元素內，請將 *n.n.n* 換成有效的版本號碼，可從 [Azure Libraries Repository on Maven][] 取得。

  [Apache 授權 2.0 版]: http://www.apache.org/licenses/LICENSE-2.0.html
  [這裡]: http://go.microsoft.com/fwlink/?LinkId=253887
  [Azure Libraries Repository on Maven]: http://go.microsoft.com/fwlink/?LinkID=286274
