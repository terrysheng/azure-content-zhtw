
<properties 
	pageTitle="自訂 Swashbuckle 產生的作業識別碼" 
	description="了解如何自訂 Swashbuckle 在 Azure 應用程式服務中為 API 應用程式所產生的 Swagger 作業識別碼。" 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="bradygaster" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/16/2015" 
	ms.author="bradyg"/>

# 自訂 Swashbuckle 產生的作業識別碼 

## 概觀

Swashbuckle 會藉由串連控制器名稱與方法名稱來產生 Swagger 作業識別碼。當一個方法的多個多載時，此模式會產生問題：Swashbuckle 會產生重複的作業識別碼，這是無效的 Swagger JSON。

例如，下列控制器程式碼會導致 Swashbuckle 產生三個 Contact_Get 作業識別碼。

![](./media/app-service-api-dotnet-swashbuckle-customize/multiplegetsincode.png)

![](./media/app-service-api-dotnet-swashbuckle-customize/multiplegetsinjson.png)

您可藉由提供唯一的方法名稱 (如下所示)，以手動方式解決此問題：

* 取得
* GetById
* GetPage

替代方法是擴充 Swashbuckle 使其自動產生唯一的作業識別碼。本文說明如何執行該作業。

## 擴充 Swashbuckle 

下列步驟示範如何使用 Visual Studio API 應用程式預覽專案範本包含在專案中的檔案 *SwaggerConfig.cs* 來自訂 Swashbuckle。您也可以在您設定以供部署為 API 應用程式的 Web API 專案中自訂 Swashbuckle。

1. 建立自訂 `IOperationFilter` 實作 

	`IOperationFilter` 介面為想要自訂 Swagger 中繼資料程序的各個層面的 Swashbuckle 使用者提供了擴充點。下列程式碼示範一種變更作業識別碼產生行為的方法。此程式碼會將參數名稱附加至作業識別碼名稱。

		using Swashbuckle.Swagger;
		using System.Web.Http.Description;
		
		namespace ContactsList
		{
		    public class MultipleOperationsWithSameVerbFilter : IOperationFilter
		    {
		        public void Apply(
		            Operation operation,
		            SchemaRegistry schemaRegistry,
		            ApiDescription apiDescription)
		        {
		            if (operation.parameters != null)
		            {
		                operation.operationId += "By";
		                foreach (var parm in operation.parameters)
		                {
		                    operation.operationId += string.Format("{0}",parm.name);
		                }
		            }
		        }
		    }
		}

2. 在 *App_Start\SwaggerConfig.cs* 檔案中，呼叫 `OperationFilter` 方法，讓 Swashbuckle 使用新的 `IOperationFilter` 實作。

		c.OperationFilter<MultipleOperationsWithSameVerbFilter>();

	![](./media/app-service-api-dotnet-swashbuckle-customize/usefilter.png)

	Swashbuckle NuGet 封裝所置放的 *SwaggerConfig.cs* 檔案包含許多擴充點註解排除範例。此處未顯示其他註解。

	當您進行這項變更之後，會使用 `IOperationFilter` 實作並產生唯一的作業識別碼。
 
	![](./media/app-service-api-dotnet-swashbuckle-customize/uniqueids.png)

## 後續步驟

本文已說明如何自訂 Swashbuckle 以產生唯一的作業識別碼。如需詳細資訊，請參閱 [GitHub 上的 Swashbuckle](https://github.com/domaindrivendev/Swashbuckle)。

<!---HONumber=58-->