<properties 
	pageTitle="下載 Azure SDK for Java" 
	description="了解如何下載 Azure SDK for Java，包括可供 Maven 專案使用的範例程式碼，以及 Azure Tookit for Eclipse 的基本安裝步驟。" 
	services="" 
	documentationCenter="java" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="multiple" 
	ms.workload="na" 
	ms.tgt_pltfrm="multiple" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="01/09/2016" 
	ms.author="robmcm"/>

# 下載 Azure SDK for Java #

本文包含下載及安裝 Azure Libraries for Java。

**注意：**Azure Libraries for Java 的散發係以 [Apache 授權 2.0 版][license]為依據。

## Azure Libraries for Java - 手動下載 ##

若要手動安裝 Azure Libraries for Java，請按一下 <http://go.microsoft.com/fwlink/?LinkId=690320>，以下載包含所有程式庫及所有相依性的 ZIP 檔案。

當將 zip 檔案下載到您的電腦之後，請將內容解壓縮，再使用下列選項之一，將 JAR 檔案加入您的專案中：

* 將 JAR 檔案匯入您在 Eclipse 的 Java 專案中。

* 為您在 Eclipe 中的專案設定**組建路徑**，以將路徑加入 JAR 檔案的路徑中。

如需在 Eclipse 中設定組建路徑的詳細資訊，請參閱 Eclipse 網站上的 [Java 組建路徑][]一文。

**注意：**如需授權與其他資訊， 請參閱 ZIP 中的 license.txt 與 ThirdPartyNotices.txt 檔案。

## Azure Libraries for Java - 使用 Maven 建置 ##

### 步驟 1 - 設定使用 Maven 建置專案 ###

若要在 Eclipse 中建立使用 Azure Libraries for Java 的 Maven 專案，請遵循[開始使用Azure Management Libraries for Java][maven-getting-started] 一文。

### 步驟 2 - 設定您的 Maven 設定必的相依性 ###

當設定好使用 Maven 建置專案之後，您便可使用類似下列範例所示的語法，將必要的相依性加入 pom.xml 檔案中。請注意，您無須加入下列範例中所列的每項相依性，而只需加入您專案所需的特定相依性即可。

    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-management</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-management-compute</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-management-network</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-management-sql</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-management-storage</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-management-websites</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-media</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-servicebus</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-serviceruntime</artifactId>
        <version>n.n.n</version>
    </dependency>

**注意：**在前述範例的每一個 `<version>` 元素中，以有效的版本號碼 (可從 [Maven 上的 Azure 程式庫存放庫][]取得) 取代此範例中的 "n.n.n" 預留位置。

## 安裝 Azure Toolkit for Eclipse ##

本節包含安裝 Azure Toolkit for Eclipse 的基本指示。如需詳細指示，請參閱[安裝 Azure Toolkit for Eclipse][]。

### 必要條件 ###

1. [Azure Toolkit for Eclipse 新功能][]一文中所列的 Windows 作業系統。
1. [Azure Toolkit for Eclipse 新功能][]一文中所列的 Macintosh 或 Linux 作業系統。
1. Eclipse IDE for Java EE Developers (Indigo 或更新版本)。這可透過 <http://www.eclipse.org/downloads/> 下載。

### 基本安裝步驟 ###

1. 在 Eclipse 中，從 [說明] 功能表選取 [安裝新軟體]。
1. 輸入網站位置 <http://dl.msopentech.com/eclipse>，然後按 **Enter** 鍵。
1. 選取要安裝的項目，然後按一下 [完成]。

Azure Toolkit for Eclipse 會使用最新版的 Azure sdk。您可以使用 Web Platform Installer (WebPI) 從 <http://go.microsoft.com/fwlink/?LinkID=252838> 下載此 SDK。不過，如果您沒有安裝此 SDK，則建立第一個 Azure 部署專案時，Azure Toolkit for Eclipse 會自動安裝適當版本的 Azure SDK。

## 另請參閱 ##

[適用於 Eclipse 的 Azure 工具組][]

[安裝 Azure Toolkit for Eclipse][]

[在 Eclipse 中為 Azure 建立 Hello World 應用程式][]

如需如何搭配使用 Azure 與 Java 的詳細資訊，請參閱 [Azure Java 開發人員中心][]。

<!-- URL List -->

[Azure Java 開發人員中心]: http://go.microsoft.com/fwlink/?LinkID=699547
[Maven 上的 Azure 程式庫存放庫]: http://go.microsoft.com/fwlink/?LinkID=286274
[適用於 Eclipse 的 Azure 工具組]: http://go.microsoft.com/fwlink/?LinkID=699529
[在 Eclipse 中為 Azure 建立 Hello World 應用程式]: http://go.microsoft.com/fwlink/?LinkID=699533
[安裝 Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Java 組建路徑]: http://help.eclipse.org/luna/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2Freference%2Fref-properties-build-path.htm
[license]: http://www.apache.org/licenses/LICENSE-2.0.html
[maven-getting-started]: http://go.microsoft.com/fwlink/?LinkID=622998
[zip-download]: http://go.microsoft.com/fwlink/?LinkId=690320
[Azure Toolkit for Eclipse 新功能]: http://go.microsoft.com/fwlink/?LinkId=690333

<!---HONumber=AcomDC_0114_2016-->