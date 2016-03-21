<properties
   pageTitle="金鑰保存庫開發人員指南 | Microsoft Azure"
   description="開發人員可以使用 Azure 金鑰保存庫來管理 Microsoft Azure 環境中的密碼編譯金鑰。"
   services="key-vault"
   documentationCenter=""
   authors="BrucePerlerMS"
   manager="mbaldwin"
   editor="bruceper" />
<tags
   ms.service="key-vault"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="03/07/2016"
   ms.author="bruceper" />

# Azure 金鑰保存庫開發人員指南

> [AZURE.VIDEO azure-key-vault-developer-quick-start]

透過金鑰保存庫，您可以從應用程式內安全地存取機密資訊，進而達到以下效果︰

- 不需要自行撰寫程式碼即可保護金鑰和密碼，而且也能輕易地從應用程式加以使用。
- 可以讓客戶擁有及管理他們自己的金鑰，因此您可以致力於提供核心軟體功能。透過這種方式，您的應用程式將不需要對客戶的租用戶金鑰和密碼負起責任或潛在責任。
- 您的應用程式能使用金鑰進行簽署和加密，卻也能在應用程式外部管理金鑰，如此一來此解決方案便適用於位於不同地點的應用程式。

如需 Azure 金鑰保存庫的一般詳細資訊，請參閱[什麼是金鑰保存庫？](key-vault-whatis.md)

## 建立及管理金鑰保存庫

在程式碼中使用 Azure 金鑰保存庫之前，您可以透過 REST、資源管理員範本、PowerShell 或 CLI 來建立及管理保存庫，如以下文章所述︰

- [使用 REST 建立和管理金鑰保存庫](https://msdn.microsoft.com/library/azure/mt620024.aspx)
- [使用 PowerShell 建立和管理金鑰保存庫](key-vault-get-started.md)
- [使用 CLI 建立和管理金鑰保存庫](key-vault-manage-with-cli.md)
- [建立金鑰保存庫並透過 ARM 範本新增密碼](../resource-manager-template-keyvault.md)

>[AZURE.NOTE] 涉及金鑰保存庫的作業乃透過 AAD 進行驗證，以及透過金鑰保存庫自己的存取原則 (每個保存庫有各自的定義) 進行授權。

## 撰寫金鑰保存庫的程式碼

程式設計人員的金鑰保存庫管理系統由幾個介面組成，並以 REST 做為基礎 ([金鑰保存庫 REST API 參考](https://msdn.microsoft.com/library/azure/dn903609.aspx))。

已成功取得授權的使用者可以執行下列作業：

- 使用 [[建立]](https://msdn.microsoft.com/library/azure/dn903634.aspx)、[[匯入]](https://msdn.microsoft.com/library/azure/dn903626.aspx)、[[更新]](https://msdn.microsoft.com/library/azure/dn903616.aspx)、[[刪除]](https://msdn.microsoft.com/library/azure/dn903611.aspx) 和其他作業管理密碼編譯金鑰。

- 使用 [Get](https://msdn.microsoft.com/library/azure/dn903633.aspx)、[Update](https://msdn.microsoft.com/library/azure/dn986818.aspx)、[Delete](https://msdn.microsoft.com/library/azure/dn903613.aspx) 和其他作業管理密碼。

- 以 [[簽章]](https://msdn.microsoft.com/library/azure/dn878096.aspx)/[[驗證]](https://msdn.microsoft.com/library/azure/dn878082.aspx)、[[包裝金鑰]](https://msdn.microsoft.com/library/azure/dn878066.aspx)/[[解除包裝金鑰]](https://msdn.microsoft.com/library/azure/dn878079.aspx) 和 [[加密]](https://msdn.microsoft.com/library/azure/dn878060.aspx)/[[解密]](https://msdn.microsoft.com/library/azure/dn878097.aspx) 作業使用密碼編譯金鑰。

以下 SDK 適用於操作金鑰保存庫︰

|[![.NET](./media/key-vault-developers-guide/net.png)](https://msdn.microsoft.com/library/azure/dn903301.aspx)|[![Node.js](./media/key-vault-developers-guide/nodejs.png)](http://azure.github.io/azure-sdk-for-node/azure-arm-keyvault/latest)
|:--:|:--:|
|[.NET SDK 文件](https://msdn.microsoft.com/library/azure/dn903301.aspx)|[Node.js SDK 文件](http://azure.github.io/azure-sdk-for-node/azure-arm-keyvault/latest)|
|[.NET SDK 封裝](https://azure.microsoft.com/documentation/api/)|[Node.js SDK 封裝](https://www.npmjs.com/package/azure-keyvault)|


如需搭配使用金鑰保存庫和應用程式的完整範例，請參閱︰

- .NET 範例應用程式 HelloKeyVault 和 Azure Web 服務範例。[Azure 金鑰保存庫程式碼範例](http://www.microsoft.com/download/details.aspx?id=45343)
- 幫助您了解如何從 Azure 之 Web 應用程式使用 Azure 金鑰保存庫的教學課程。[從 Web 應用程式使用 Azure 金鑰保存庫](key-vault-use-from-web-application.md)

## 作法

下列文章和案例提供工作的特定指引：

- [如何為 Azure 金鑰保存庫產生並傳輸受 HSM 保護的金鑰](key-vault-hsm-protected-keys.md)
- [在部署期間傳遞安全值 (如密碼)](../resource-manager-keyvault-parameter.md)。
- 如需整合金鑰保存庫和 Azure 及搭配使用的工作特定指引，請參閱 [Ryan Jones 金鑰保存庫 ARM 範本範例](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples)

## 支援程式庫

- [Microsoft Azure 金鑰保存庫核心程式庫](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core/1.0.0)提供 `IKey` 和 `IKeyResolver` 介面，以從識別碼尋找金鑰和使用金鑰執行作業。

- [Microsoft Azure 金鑰保存庫延伸模組](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions/1.0.0)提供 Azure 金鑰保存庫的擴充功能。

<!---HONumber=AcomDC_0309_2016-->