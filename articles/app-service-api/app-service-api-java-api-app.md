<properties
	pageTitle="在 Azure App Service 中建置和部署 Java API 應用程式"
	description="了解如何建立 Java 應用程式封裝並將其部署至 Azure App Service。"
	services="app-service\api"
	documentationCenter="java"
	authors="bradygaster"
	manager="mohisri" 
	editor="tdykstra"/>

<tags
	ms.service="app-service-api"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="java"
	ms.topic="get-started-article"
	ms.date="11/27/2015"
	ms.author="bradygaster"/>

# 在 Azure App Service 中建置和部署 Java API 應用程式

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

本教學課程說明如何建立 Java 應用程式，並使用 [Git](http://git-scm.com) 將其部署至 Azure App Service API 應用程式。本教學課程中的指示可運用在任何足以執行 Java 應用程式的作業系統上。本教學課程中的程式碼，是使用 [Maven](https://maven.apache.org/) 建置的。我們使用[Jax RS](https://jax-rs-spec.java.net/) 來建立 RESTful 服務，而這些 Jax RS 是根據 [Swagger](http://swagger.io) 中繼資料規格使用[Swagger 編輯器](http://editor.swagger.io/)所產生的。

## 必要條件

1. [Java Developer Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) (或更新版本)
1. 付費或[免費試用](https://azure.microsoft.com/pricing/free-trial/)的 [Microsoft Azure](https://azure.microsoft.com/) 訂用帳戶
1. 安裝在您開發電腦上的 [Maven](https://maven.apache.org/)
1. 安裝在您開發電腦上的 Git 

## 使用 Swagger.IO 來建立 API 結構。
您可以使用 swagger.io 線上編輯器，來輸入代表您 API 結構的 Swagger JSON 或 YAML 程式碼。當您完成設計 API 介面區時，就可以依照各種不同平台和架構來匯出程式碼。在下一節中，我們將修改已建立結構的程式碼來包含模擬的功能。

這個示範將從會貼到 swagger.io 編輯器的 Swagger JSON 主體開始，然後我們要用該編輯器來產生會利用 JAX RS 來存取 REST API 端點的程式碼。然後，您將編輯已建立結構的程式碼來傳回模擬資料，依照資料永續性機制來模擬 REST API 組建。

1. 將下列 Swagger JSON 程式碼複製到剪貼簿中

		{
			"swagger": "2.0",
			"info": {
				"version": "v1",
				"title": "Contact List",
				"description": "A Contact list API based on Swagger and built using Java"
			},
			"host": "localhost",
			"schemes": [
				"http",
				"https"
			],
			"basePath": "/api",
			"paths": {
				"/contacts": {
					"get": {
						"tags": [
							"Contact"
						],
						"operationId": "contacts_get",
						"consumes": [],
						"produces": [
							"application/json",
							"text/json"
						],
						"responses": {
							"200": {
								"description": "OK",
								"schema": {
									"type": "array",
									"items": {
										"$ref": "#/definitions/Contact"
									}
								}
							}
						},
						"deprecated": false
					}
				},
				"/contacts/{id}": {
					"get": {
						"tags": [
							"Contact"
						],
						"operationId": "contacts_getById",
						"consumes": [],
						"produces": [
							"application/json",
							"text/json"
						],
						"parameters": [
							{
								"name": "id",
								"in": "path",
								"required": true,
								"type": "integer",
								"format": "int32"
							}
						],
						"responses": {
							"200": {
								"description": "OK",
								"schema": {
									"type": "array",
									"items": {
										"$ref": "#/definitions/Contact"
									}
								}
							}
						},
						"deprecated": false
					}
				}
			},
			"definitions": {
				"Contact": {
					"type": "object",
					"properties": {
						"Id": {
							"format": "int32",
							"type": "integer"
						},
						"Name": {
							"type": "string"
						},
						"EmailAddress": {
							"type": "string"
						}
					}
				}
			}
		}
		
1. 瀏覽至[線上 Swagger 編輯器](http://editor.swagger.io/)。抵達之後，依序按一下 [File] (檔案) -> [Paste JSON] (貼上 JSON) 功能表項目 。

    ![貼上 Json](media/app-service-api-java-api-app/paste-json.png)

1. 貼上您之前複製的連絡人清單 API Swagger JSON 程式碼。

    ![已貼上的 Swagger](media/app-service-api-java-api-app/pasted-swagger.png)

1. 檢視編輯器顯示的文件頁面和 API 摘要。

    ![檢視 Swagger 產生的文件](media/app-service-api-java-api-app/view-swagger-generated-docs.png)

1. 依序選取 [Generate Server] (產生伺服器) -> [JAX RS] 功能表選項，來建立伺服器端的程式碼結構，讓您稍後能拿來新增模擬實作。

    ![產生程式碼功能表項目](media/app-service-api-java-api-app/generate-code-menu-item.png)
	
	程式碼產生之後，編輯器會提供 ZIP 檔案來讓您下載。這個檔案包含 Swagger 程式碼產生器所建構的程式碼，以及所有相關連的組建指令碼。將整個程式庫解壓縮到您的開發工作站上的某個目錄。

## 編輯程式碼來新增 API 實作
在本節中，您將使用您自訂的程式碼，來取代編輯器所產生程式碼的伺服器端實作。新的程式碼會把連絡人實體的 ArrayList 傳回給呼叫中的用戶端。

1. 使用 [Visual Studio Code](https://code.visualstudio.com) 或您偏好的文字編輯器，來開啟 *Contact.java* 模型檔案 (位於 *src/gen/java/io/swagger/model* 資料夾)。 

    ![開啟連絡人模型檔案](media/app-service-api-java-api-app/open-contact-model-file.png)

1. 將下列建構函式新增到 **Contact** 類別。

        public Contact(Integer id, String name, String email) 
		{
			this.id = id;
			this.name = name;
			this.emailAddress = email;
		}

1. 使用 [Visual Studio Code](https://code.visualstudio.com) 或您偏好的文字編輯器，來開啟 *ContactsApiServiceImpl.java* 服務實作檔案 (位於 *src/main/java/io/swagger/api/impl* 資料夾)。

    ![開啟連絡人服務程式碼](media/app-service-api-java-api-app/open-contact-service-code-file.png)

1. 用新的程式碼覆寫檔案中的程式碼，來將模擬實作新增至服務程式碼。

        package io.swagger.api.impl;

        import io.swagger.api.*;
        import io.swagger.model.*;
        import com.sun.jersey.multipart.FormDataParam;
        import io.swagger.model.Contact;
        import java.util.*;
        import io.swagger.api.NotFoundException;
        import java.io.InputStream;
        import com.sun.jersey.core.header.FormDataContentDisposition;
        import com.sun.jersey.multipart.FormDataParam;
        import javax.ws.rs.core.Response;

        @javax.annotation.Generated(value = "class io.swagger.codegen.languages.JaxRSServerCodegen", date = "2015-11-24T21:54:11.648Z")
        public class ContactsApiServiceImpl extends ContactsApiService {
  
            private ArrayList<Contact> loadContacts()
            {
                ArrayList<Contact> list = new ArrayList<Contact>();
                list.add(new Contact(1, "Barney Poland", "barney@contoso.com"));
                list.add(new Contact(2, "Lacy Barrera", "lacy@contoso.com"));
                list.add(new Contact(3, "Lora Riggs", "lora@contoso.com"));
                return list;
            }
  
            @Override
            public Response contactsGet()
            throws NotFoundException {
                ArrayList<Contact> list = loadContacts();
                return Response.ok().entity(list).build();
                }
  
            @Override
            public Response contactsGetById(Integer id)
            throws NotFoundException {
                ArrayList<Contact> list = loadContacts();
                Contact ret = null;
            
                for(int i=0; i<list.size(); i++)
                {
                    if(list.get(i).getId() == id)
                    {
                        ret = list.get(i);
                    }
                }
                return Response.ok().entity(ret).build();
            }
        }

1. 開啟命令提示字元。

1. 執行下列 Maven 指令來建置程式碼，並在本機使用 Jetty 應用程式伺服器來執行。

		mvn package jetty:run
		
1. 您應該會在命令視窗看到 Jetty 已經在連接埠 8080 啟動您的程式碼。

	![開啟連絡人服務程式碼](media/app-service-api-java-api-app/run-jetty-war.png)
	
1. 使用 [Postman](https://www.getpostman.com/) 來對「取得所有連絡人」API 方法 (位於 http://localhost:8080/api/contacts) 提出要求。

	![呼叫連絡人 API](media/app-service-api-java-api-app/calling-contacts-api.png)
	
1. 使用 [Postman](https://www.getpostman.com/) 來對「取得特定連絡人」API 方法 (位於 http://localhost:8080/api/contacts/2) 提出要求。

	![呼叫連絡人 API](media/app-service-api-java-api-app/calling-specific-contact-api.png)
	
1. 最後，在您的主控台執行下列 Maven 命令來建立 Java WAR (網頁封存) 檔案。

		mvn package war:war
		
	當 WAR 檔案建立之後，將會放入 [target] 資料夾。瀏覽至 [target] 資料夾，然後將 WAR 檔案重新命名為「ROOT.war」(請確定字母的大小寫符合這個格式)。
	
		rename swagger-jaxrs-server-1.0.0.war ROOT.war
		
	最後，執行下列指令來建立 [deploy] 資料夾，以便用來將 WAR 檔案部署至 Azure。
	
		mkdir deploy
		mkdir deploy\webapps
		copy target\ROOT.war deploy\webapps
		cd deploy
	
## 將輸出資料發佈至 Azure App Service
在本節中，您將了解如何使用 Azure 入口網站來建立新的 API 應用程式、準備該 API 應用程式來託管 Java 應用程式，以及將新建立的 WAR 檔案部署至 Azure App Service 來執行您的新 API 應用程式。

1. 在 [Azure 入口網站](https://portal.azure.com/)建立新的 API 應用程式，方法是依序按一下 [新增] -> [Web + 行動] -> [API 應用程式] 功能表項目。
	
	![建立新的 API 應用程式](media/app-service-api-java-api-app/create-api-app.png)

1. 按一下 API 應用程式的 [設定] 刀鋒視窗中的 [應用程式設定] 選項。接著，在 [Java 版本] 功能表選取最新的 Java 版本，並在 [Web 容器] 功能表選取最新的 Tomcat。

	![在 API 應用程式刀鋒視窗中設定 Java](media/app-service-api-java-api-app/set-up-java.png)

1. 按一下 [部署認證] 設定功能表項目，並提供您要用來將檔案發佈至 API 應用程式的使用者名稱和密碼。

	![設定部署認證](media/app-service-api-java-api-app/deployment-credentials.png)

1. 按一下 [連續部署] 設定功能表項目。抵達之後，按一下 [選擇來源] 按鈕，然後選取 [本機 Git 儲存機制] 選項。這會建立在 Azure 中執行的 Git 儲存機制，而它與您的 API 應用程式有關聯。每次您將程式碼交附給 Git 儲存機制的「主要」分支時，您的程式碼就會發佈到您執行中的 API 應用程式執行個體。

	![設定新的本機 Git 儲存機制](media/app-service-api-java-api-app/select-git-repo.png)

1. 將新的 Git 儲存機制 URL 複製到剪貼簿中。請務必保存這個網址，因為它在稍後會很重要。

	![為您的應用程式設定新的 Git 儲存機制](media/app-service-api-java-api-app/copy-git-repo-url.png)

1. Git 會把 WAR 檔案推送至線上儲存機制。方法是瀏覽至您先前建立的 [deploy] 資料，讓您能輕鬆地把程式碼交付給在您 App Service 中執行的儲存機制。當您抵達主控台視窗，並瀏覽至 [webapps] 資料夾所在的資料夾之後，請發出下列的 Git 命令來啟動處理程序並開始部署。

		git init
		git add .
		git commit -m "initial commit"
		git remote add azure [YOUR GIT URL]		
		git push azure master
		
	當您發出「推送」要求之後，系統會詢問您之前為部署認證所建立的密碼。如果您輸入密碼，應該就會看到您的入口網站顯示更新已經抵達，且已經部署。
		
1. 如果您再次使用 Postman 來叫用您剛部署，且在 Azure App Service 中執行的新 API 應用程式，就會看到行為是一致的，且現在它會如預期般傳回連絡人資料，還會對以 Swagger.io 建構的 Java 程式碼進行簡單的程式碼變更。

	![在 Azure 中即時使用您的 Java 連絡人 REST API](media/app-service-api-java-api-app/postman-calling-azure-contacts.png)
	
## 後續步驟
在這篇文章中，您從 Swagger JSON 檔案和一些利用 Swagger.io 編輯器建構的 Java 程式碼開始，然後您做的簡單變更和 Git 部署程序，讓您得到以 Java 撰寫的實用 API 應用程式。API Apps 入門系列中的下一個教學課程示範如何[使用 CORS 從 JavaScript 用戶端取用 API 應用程式](app-service-api-cors-consume-javascript.md)。

為了根據此範例進行建置，您可以深入了解 [Storage SDK for Java](../storage/storage-java-how-to-use-blob-storage.md) 來保存 JSON blob。或者，您可以使用 [Document DB Java SDK](../documentdb/documentdb-java-application.md) 來將您的連絡人資料儲存到 Azure Document DB。

如需有關在 Azure 中使用 Java 的詳細資訊，請參閱 [ 開發人員中心](/develop/java/)。

<!---HONumber=AcomDC_0204_2016-->