<properties
   pageTitle="建立 Marketplace 資料服務的指南 | Microsoft Azure"
   description="如何建立、認證和部署資料服務供他人在 Azure Marketplace 上購買的詳細指示。"
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

   <tags
      ms.service="marketplace"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="na"
      ms.date="01/04/2016"
      ms.author="hascipio; avikova" />

# 透過 CSDL 將現有的 Web 服務對應至 OData

本文提供如何使用 CSDL 來將現有的服務對應至 OData 相容服務的概觀。其中將說明如何建立對應文件 (CSDL)，透過服務呼叫轉換來自用戶端的輸入要求，並透過 OData 相容的摘要將輸出 (資料) 傳輸回用戶端。Microsoft Azure Marketplace 會使用 OData 通訊協定，向使用者公開服務。內容提供者 (資料擁有者) 所公開的服務會以各種不同形式 (例如 REST、 SOAP 等) 來公開。

## 什麼是 CSDL 及其結構？
CSDL (概念結構定義語言) 是一項規格，會定義如何使用常見的 XML 用語來說明對於 Azure Marketplace 的 Web 服務或資料庫服務。

**要求流程**的簡單概觀：

  `Client -> Azure Marketplace -> Content Provider’s Web Service (Get, Post, Delete, Put)`

**資料流程**的方向相反：

  `Client <- Azure Marketplace <- Content Provider’s WebService`

**圖 1** 圖解說明用戶端如何透過 Azure Marketplace，從內容提供者 (您的服務) 取得資料。對應/轉換元件會使用 CSDL 來處理內容提供者的服務和要求用戶端之間的要求和資料傳遞。

*圖 1：透過 Azure Marketplace，從要求用戶端到內容提供者的詳細流程*

  ![繪圖](media/marketplace-publishing-data-service-creation-odata-mapping/figure-1.png)

如需 Atom、Atom Pub 和 OData 通訊協定 (Azure Marketplace 延伸模組會據以建置) 的背景，請檢閱：[http://msdn.microsoft.com/library/ff478141.aspx](http://msdn.microsoft.com/library/ff478141.aspx)

摘錄自上述連結：*「開放式資料通訊協定 (以下稱為 OData) 的目的是根據公開為資料服務的資源，為 CRUD 樣式的操作 (建立、讀取、更新及刪除) 提供以 REST 為基礎的通訊協定。「資料服務」是一個端點，其中含有公開自一或多個「集合」的資料，每個集合均包含零到多個「項目」，這些項目是由具類型的具名值組所組成。OData 是 Microsoft 基於 OASIS (Organization for the Advancement of Structured Information Standards) 標準所發佈，因此，任何有需要的人都能建置伺服器、用戶端或工具，而沒有任何權利金或限制。」*

### 以下是三個必須由 CSDL 定義的關鍵部分：

- 服務提供者的**端點**。服務的 Web 位址 (URI)
- 當成輸入傳遞至服務提供者**資料參數**。傳送至內容提供者服務 (向下至資料類型) 的參數定義。
- 傳回要求服務之資料的**結構描述**。內容提供者服務所傳遞之資料的結構描述，包括容器、集合/資料表、變數/資料行，以及資料類型。

下圖顯示從用戶端輸入 OData 陳述式 (呼叫內容提供者的 Web 服務) 到取回結果/資料的流程概觀。

  ![繪圖](media/marketplace-publishing-data-service-creation-odata-mapping/figure-2.png)

### 步驟：

1. 用戶端透過使用 XML 中定義的輸入參數填入的服務呼叫，將要求傳送到 Azure Marketplace
2. CSDL 可用來驗證服務呼叫。
	- 格式化的服務呼叫接著會由 Azure Marketplace 傳送到內容提供者服務
3. WebService 會執行，並執行 Http 指令動詞 (例如 GET) 的動作。資料會傳回 Azure Marketplace，其中的要求資料 (如果有的話) 會使用 CSDL 中定義的對應，以 XML 格式公開至用戶端。
4. 用戶端會以 XML 或 JSON 格式傳送資料 (如果有的話)

## 定義

### OData ATOM Pub

ATOM Pub 的延伸模組，其中每個項目代表結果集的一個資料列。項目的內容部分已增強來包含資料列的值 - 做為索引鍵值組。您可以在此處找到詳細資訊：[https://www.odata.org/documentation/odata-version-3-0/atom-format/](https://www.odata.org/documentation/odata-version-3-0/atom-format/)

### CSDL - 概念結構定義語言

允許定義透過資料庫公開的函式 (SPROC) 和實體。您可以在此處找到詳細資訊：[http://msdn.microsoft.com/library/bb399292.aspx](http://msdn.microsoft.com/library/bb399292.aspx)

> [AZURE.TIP]如果您看不到該篇文章，請按一下 [其他版本] 下拉式清單並選取一個版本。

### EDM - 項目資料模型
- 概觀：[http://msdn.microsoft.com/library/vstudio/ee382825(v=vs.100).aspx][OverviewLink] [OverviewLink]:http://msdn.microsoft.com/library/vstudio/ee382825(v=vs.100).aspx
- 預覽：[http://msdn.microsoft.com/library/aa697428(v=vs.80).aspx][PreviewLink] [PreviewLink]:http://msdn.microsoft.com/library/aa697428(v=vs.80).aspx
- 資料類型：[http://msdn.microsoft.com/library/bb399548(v=VS.100).aspx][DataTypesLink] [DataTypesLink]:http://msdn.microsoft.com/library/bb399548(v=VS.100).aspx

下圖顯示從用戶端輸入 OData 陳述式 (呼叫內容提供者的 Web 服務) 到取回結果/資料的詳細流程 (從左至右)：

  ![繪圖](media/marketplace-publishing-data-service-creation-odata-mapping/figure-3.png)


## CSDL 基本概念

CSDL (概念結構定義語言) 是一項規格，會定義如何使用常見的 XML 用語來說明對於 Azure Marketplace 的 Web 服務或資料庫服務。CSDL 說明**盡可能將資料從資料來源傳遞至 Azure Marketplace** 的重要部分。 主要部分如下所示：

- 說明所有對外公開使用之函式 (FunctionImport 節點) 的介面資訊
- 適用於所有訊息要求 (輸入) 和訊息回應 (輸出) (EntityContainer/EntitySet/EntityType 節點) 的資料類型資訊
- 關於要使用之傳輸通訊協定 (標頭節點) 的繫結資訊
- 可用以尋找指定服務 (BaseURI 屬性) 的位址資訊

簡單來說，CSDL 表示在服務要求者和服務提供者之間與平台及語言無關的合約。使用 CSDL，用戶端可以找到 Web 服務/資料庫服務，並叫用任何其公開可用的函式。

### 將 CSDL 關聯至資料庫或集合
**CSDL 規格**

CSDL 是說明 Web 服務的 XML 文法。規格本身分成 4 個主要元素：EntitySet、FunctionImport、NameSpace 和 EntityType。

若要讓這個抽象層更容易了解，可讓 CSDL 關聯至資料表。

請記住；

  CSDL 表示在**服務要求者**和**服務提供者**之間與平台及語言無關的合約。使用 CSDL，**用戶端**可以找到 **Web 服務/資料庫服務**，並叫用任何其公開可用的**函式**。

針對資料服務，可從資料庫、資料表、資料行及預存程序等方面來理解 CSDL 的四個部分。

針對資料服務，可使用如下方式來關聯這些項目：

- EntityContainer ~= 資料庫
- EntitySet ~= 資料表
- EntityType ~= 資料行
- FunctionImport ~= 預存程序

**允許的 HTTP 動詞命令** - GET - 從 DB 傳回值 (傳回集合) - POST - 用來將資料傳遞至 DB 並選擇性地從 db 傳回值 (在集合中建立新項目，傳回識別碼/URI) - DELETE - 從 DB 刪除資料 (刪除集合) - PUT - 將資料更新至 DB (取代集合或建立新集合)

## 中繼資料/對應文件

中繼資料/對應文件可用來對應內容提供者的現有 Web 服務，讓它能夠透過 Azure Marketplace 系統公開為 OData Web 服務。它會以 CSDL 為基礎，並實作數個 CSDL 延伸模組，以滿足透過 Azure Marketplace 公開之以 REST 為基礎的 Web 服務需求。您可以在 [http://schemas.microsoft.com/dallas/2010/04](http://schemas.microsoft.com/dallas/2010/04) 命名空間中找到延伸模組。

CSDL 的範例如下：(複製下列範例 CSDL 並貼至 XML 編輯器，然後進行變更以符合您的服務。然後在 [Azure Marketplace 發佈入口網站](https://publish.windowsazure.com)中建立您的服務時，貼至 [DataService] 索引標籤底下的 CSDL 對應)。

**詞彙：**將 CSDL 詞彙關聯至[發佈入口網站](https://publish.windowsazure.com) UI (PPUI) 詞彙。- 將 PPUI 中的優惠「標題」關聯至 MyWebOffer - 將 PPUI 中的 MyCompany 關聯至 [Microsoft 開發人員中心](http://dev.windows.com/registration?accountprogram=azure) UI 中的**發行者顯示名稱** UI - 您的 API 會關聯至 Web 或資料服務 (PPUI 中的方案)

**階層：**公司 (內容提供者) 擁有具有方案 (即服務) 的優惠，可利用 API 來排列。

### WebService CSDL 範例

連接至公開 Web 應用程式端點 (例如 C# 應用程式) 的服務

        <?xml version="1.0" encoding="utf-8"?>
        <!-- The namespace attribute below is used by our system to generate C#. You can change “MyCompany.MyOffer” to something that makes sense for you, but change “MyOffer” consistently throughout the document. -->
        <Schema Namespace="MyCompany.MyWebOffer" Alias="MyOffer" xmlns="http://schemas.microsoft.com/ado/2009/08/edm" xmlns:d="http://schemas.microsoft.com/dallas/2010/04" >
        <!-- EntityContainer groups all the web service calls together into a single offering. Every web service call has a FunctionImport definition. -->
          <EntityContainer Name="MyOffer">
        <!-- EntitySet is defined for CSDL compatibility reasons, not required for ReturnType=”Raw”
        @Name is used as reference by FunctionImport @EntitySet. And is used in the customer facing UI as name of the Service.
        @EntityType is used to point at the type definition near the bottom of this file. -->
            <EntitySet Name="MyEntities" EntityType="MyOffer.MyEntityType" />
        <!-- Add a FunctionImport for every service method. Multiple FunctionImports can share a single return type (EntityType). -->
        <!-- ReturnType is either Raw() for a stream or Collection() for an Atom feed. Ex. of Raw: ReturnType=”Raw(text/plain)” -->
        <!—EntitySet is the entityset defined above, and is needed if ReturnType is not Raw -->
        <!-- BaseURI attribute defines the service call, replace & with the encode value (&amp;).
        In the input name value pairs {param} represents passed in value.
        Or the value can be hard coded as with AccountKey. -->
        <!-- AllowedHttpMethods optional (default = “GET”), allows the CSDL to specifically specify the verb of the service, “Get”, “Post”, “Put”, or “Delete”. -->
        <!-- EncodeParameterValues, True encodes the parameter values, false does not. -->
        <!-- BaseURI is translated into an URITemplate which defines how the web service call is exposed to marketplace customers.
        Ex. https://api.datamarket.azure.com/mycompany/MyOfferPlan?name={name}
        BaseURI is XML encoded, the {...} point to the parameters defined below.
        Marketplace will read the parameters from this URITemplate and fill the values into the corresponding parameters of the BaseUri or RequestBody (below) during calls to your service.  
        It is okay for @d:BaseUri to include information only for Marketplace consumption, it will not be exposed to end users. i.e. the hardcoded AccountKey in the above BaseURI does not show up in the client facing URITemplate. -->
            <FunctionImport Name="MyWebServiceMethod"
                            EntitySet="MyEntities"
                            ReturnType="Collection(MyOffer.MyEntityType)"
        d:AllowedHttpMethods="GET"
        d:EncodeParameterValues="true"
        d:BaseUri="http://services.organization.net/MyServicePath?name={name}&amp;AccountKey=22AC643">
        <!-- Definition of the RequestBody is only required for HTTP POST requests and is optional for HTTP GET requests. -->
        <d:RequestBody d:httpMethod="POST">
                <!-- Use {} for placeholders to insert parameters. -->
                <!-- This example uses SOAP formatting, but any POST body can be used. -->
        	<!-- This example shows how to pass userid and password via the header -->
                <![CDATA[<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:MyOffer="http://services.organization.net/MyServicePath">
                  <soapenv:Header/>
                  <soapenv:Body>
                    <MyOffer:ws_MyWebServiceMethod>
                      <myWebServiceMethodRequest>
                        <!--This information is not exposed to end users. -->
                        <UserId>userid</UserId>
                        <Password>password</Password>
                        <!-- {name} is replaced with the value read from @d:UriTemplate above -->
                        <Name>{name}</Name>
                        <!-- Parameters can be used more than once and are not limited to appearing as the value of an element -->
                        <CustomField Name="{name}" />
                        <MyField>Static content</MyField>
                      </myWebServiceMethodRequest>
                    </MyOffer:ws_MyWebServiceMethod>
                  </soapenv:Body>
                </soapenv:Envelope>      
              ]]>
        </d:RequestBody>
        <!-- Title, Rights and Description are optional and used to specify values to insert into the ATOM feed returned to the end user.  You can specify the element to contain a fixed message by providing a value for the element (this is the default value).  @d:Map is an XPath expression that points into the response returned by your service and is optional.  -->
        <d:Title d:Map="/MyResponse/Title">Default title.</d:Title>
        <d:Rights>© My copyright. This is a fixed response. It is okay to also add a d:Map attribute to override this text.</d:Rights>
        <d:Description d:Map="/MyResponse/Description"></d:Description>
        <d:Namespaces>
        <d:Namespace d:Prefix="p"  d:Uri="http://schemas.organization.net/2010/04/myNamespace" />
        <d:Namespace d:Prefix="p2" d:Uri="http://schemas.organization.net/2010/04/MyNamespace2" />
        </d:Namespaces>
        <!-- Parameters of the web service call:
        @Name should match exactly (case sensitive) the {…} placeholders in the @d:BaseUri, @d:UriTemplate, and d:RequestBody, i.e. “name” parameter in above BaseURI.
        @Mode is always "In", compatibility with CSDL
        @Type is the EDM.SimpleType of the parameter, see http://msdn.microsoft.com/library/bb399548(v=VS.100).aspx
        @d:Nullable indicates whether the parameter is required.
        @d:Regex - optional, attribute to describe the string, limiting unwanted input at the entry of the system
        @d:Description - optional, is used by Service Explorer as help information
        @d:SampleValues - optional, is used by Service Explorer as help information. Multiple Sample values are separated by '|', e.g. "804735132|234534224|23409823234"
        @d:Enum - optional for string type. Contains an enumeration of possible values separated by a '|', e.g. d:enum="english|metric|raw". Will be converted in a dropdown list in the Service Explorer.
        -->
        <Parameter name="name" Mode="In" Type="String" d:Nullable="false" d:Regex="^[a-zA-Z]*$" d:Description="A name that cannot contain any spaces or non-alpha non-English characters"
        d:Enum="George|John|Thomas|James"
        d:SampleValues="George"/>
        <Parameter Name=" AccountKey" Mode="In" Type="String" d:Nullable="false" />

        <!-- d:ErrorHandling is an optional element. Use it define standardized errors by evaluating the service response. -->
        <d:ErrorHandling>
        <!-- Any number of d:Condition elements are allowed, they are evaluated in the order listed.
        @d:Match is an Xpath query on the service response, it should return true or false where true indicates an error.
        @d:httpStatusCode is the error code to return if an response matches the error.
        @d:errorMessage is the user friendly message to return when an error occurs.
        -->
        <d:Condition d:Match="/Result/ErrorMessage[text()='Invalid token']" d:HttpStatusCode="403" d:ErrorMessage="User cannot connect to the service." />
        </d:ErrorHandling>
           </FunctionImport>

        	<!-- The EntityContainer defines the output data schema -->
        </EntityContainer>
        <!-- The EntityType @d:Map defines the repeating node (an XPath query) in the response (output data schema). -->
        <!-- If these nodes are outside a namespace, add the prefix in the xpath. -->
        <!--
        @Name - define your user readable name, will become an XML element in the ATOM feed, so comply with the XML element naming restrictions (no spaces or other illegal characters).
        @Type is the EDM.SimpleType of the parameter, see http://msdn.microsoft.com/library/bb399548(v=VS.100).aspx.
        @d:Map uses an Xpath query to point at the location to extract the content from your services response.
        The "." is relative to the repeating node in the EntityType @d:Map Xpath expression.
        -->
            <EntityType Name="MyEntityType" d:Map="/MyResponse/MyEntities">
        <Property Name="ID"	d:IsPrimaryKey="True" Type="Int32"	Nullable="false" d:Map="./Remaining[@Amount]"/>
        <Property Name="Amount"	Type="Double"	Nullable="false" d:Map="./Remaining[@Amount]"/>
        <Property Name="City"	Type="String"	Nullable="false" d:Map="./City"/>
        <Property Name="State"	Type="String"	Nullable="false" d:Map="./State"/>
        <Property Name="Zip"	Type="Int32"	Nullable="false" d:Map="./Zip"/>
        <Property Name="Updated"	Type="DateTime"	Nullable="false" d:Map="./Updated"/>
        <Property Name="AdditionalInfo" Type="String" Nullable="true"
        d:Map="./Info/More[1]"/>
            </EntityType>
        </Schema>

> [AZURE.TIP]在[透過 CSDL 將現有的 Web 服務對應至 OData 的範例](marketplace-publishing-data-service-creation-odata-mapping-examples.md)一文中，檢視更多 CSDL Web 服務範例。

###DataService CSDL 範例

連接到將資料庫資料表或檢視公開為端點的服務。以下範例顯示兩個以資料庫為基礎之 API CSDL 的 API (可以使用檢視，而不是資料表)。

        <?xml version="1.0"?>
        <!-- The namespace attribute below is used by our system to generate C#. You can change “MyCompany.MyOffer” to something that makes sense for you, but change “MyOffer” consistently throughout the document. -->
        <Schema Namespace="MyCompany.MyDataOffer" Alias="MyOffer" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ado/2009/08/edm">
        <!-- EntityContainer groups all the data service calls together into a single offering. Every web service call has a FunctionImport definition. -->
        <EntityContainer Name="MyOfferContainer">
        <!-- EntitySet is defined for CSDL compatibility reasons, not required for ReturnType=”Raw”
        	Think of the EntitySet as a Service
        @Name is used in the customer facing UI as name of the Service.
        @EntityType is used to point at the type definition (returned set of table columns). -->
        <EntitySet Name="CompanyInfoEntitySet" EntityType="MyOffer.CompanyInfo" />
        <EntitySet Name="ProductInfoEntitySet" EntityType="MyOffer.ProductInfo" />
        </EntityContainer>
        <!-- EntityType defines result (output); the table (or view) and columns to be returned by the data service.)
        	Map is the schema.tabel or schema.view
        	dals.TableName is the table Name
        	Name is the name identifier for the EntityType and the Name of the service exposed to the client via the UI.
        	dals:IsExposed determines if the table schema is exposed (generally true).
        	dals:IsView (optional) true if this is based on a view rather than a table
        	dals:TableSchema is the schema name of the table/view
        -->
        <EntityType
        Map="[dbo].[CompanyInfo]"
        dals:TableName="CompanyInfo"
        Name="CompanyInfo"
        dals:IsExposed="true"
        dals:IsView="false"
        dals:TableSchema="dbo"
        xmlns:dals="http://schemas.microsoft.com/dallas/2010/04">
        <!-- Property defines the column properties and the output of the service.
        	dals:ColumnName is the name of the column in the table /view.
        	Type is the emd.SimpleType
        	Nullable determines if NULL is a valid output value
        	dals.CharMaxLenght is the maximum length of the output value
        	Name is the name of the Property and is exposed to the client facing UI
        	dals:IsReturned is the Boolean that determines if the Service exposes this value to the client.
        	IsQueryable is the Boolean that determines if the column can be used in a database query
        	(For data Services: To improve Performance make sure that columns marked ISQueryable=”true” are in an index.)
        	dals:OrdinalPosition is the numerical position x in the table or the View, where x is from 1 to the number of columns in the table.
        	dals:DatabaseDataType is the data type of the column in the database, i.e. SQL data type dals:IsPrimaryKey indicates if the column is the Primary key in the table/view.  (The columns marked ISPrimaryKey are used in the Order by clause when returning data.)
        -->
        <Property dals:ColumnName="data" Type="String" Nullable="true" dals:CharMaxLength="-1" Name="data" dals:IsReturned="true" dals:IsQueryable="false" dals:IsPrimaryKey="false" dals:OrdinalPosition="3" dals:DatabaseDataType="nvarchar" />
        <Property dals:ColumnName="id" Type="Int32" Nullable="false" Name="id" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="true" dals:OrdinalPosition="1" dals:NumericPrecision="10" dals:DatabaseDataType="int" />
        <Property dals:ColumnName="ticker" Type="String" Nullable="true" dals:CharMaxLength="10" Name="ticker" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="false" dals:OrdinalPosition="2" dals:DatabaseDataType="nvarchar" />
        </EntityType>
        <EntityType Map="[dbo].[ProductInfo]" dals:TableName="ProductInfo" Name="ProductInfo" dals:IsExposed="true" dals:IsView="false" dals:TableSchema="dbo" xmlns:dals="http://schemas.microsoft.com/dallas/2010/04">
        <Property dals:ColumnName="companyid" Type="Int32" Nullable="true" Name="companyid" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="false" dals:OrdinalPosition="2" dals:NumericPrecision="10" dals:DatabaseDataType="int" />
        <Property dals:ColumnName="id" Type="Int32" Nullable="false" Name="id" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="true" dals:OrdinalPosition="1" dals:NumericPrecision="10" dals:DatabaseDataType="int" />
        <Property dals:ColumnName="product" Type="String" Nullable="true" dals:CharMaxLength="50" Name="product" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="false" dals:OrdinalPosition="3" dals:DatabaseDataType="nvarchar" />
        </EntityType>
        </Schema>

## 另請參閱
- 如果您有興趣學習和了解特定節點及其參數，請閱讀[資料服務 OData 對應節點](marketplace-publishing-data-service-creation-odata-mapping-nodes.md)一文，來了解定義和說明、範例，以及使用案例內容。
- 如果您有興趣檢閱範例，請閱讀[資料服務 OData 對應範例](marketplace-publishing-data-service-creation-odata-mapping-examples.md)一文，來查看範例程式碼，並了解程式碼語法與內容。
- 若要返回用於將資料服務發佈至 Azure Marketplace 的指定路徑，請閱讀[資料服務發佈指南](marketplace-publishing-data-service-creation.md)一文。

<!---HONumber=AcomDC_0114_2016-->