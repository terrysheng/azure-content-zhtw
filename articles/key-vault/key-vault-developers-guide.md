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
   ms.date="11/06/2015"
   ms.author="bruceper" />

# Azure 金鑰保存庫開發人員指南

> [AZURE.VIDEO azure-key-vault-developer-quick-start]

開發人員可以使用 Azure 金鑰保存庫來管理 Microsoft Azure 環境中的密碼編譯金鑰。金鑰保存庫支援多種金鑰類型和演算法，也可針對高價值的客戶金鑰搭配硬體安全性模組 (HSM) 使用。此外，您可以使用金鑰保存庫來安全地儲存密碼，也就是沒有特定語意且有大小限制的八位元物件。金鑰保存庫可以包含金鑰和密碼的混合。物件類型的存取控制會受到獨立管理。

已成功取得授權的使用者可以執行下列作業：

- 使用 [[建立]](https://msdn.microsoft.com/library/azure/dn903634.aspx)、[[匯入]](https://msdn.microsoft.com/library/azure/dn903626.aspx)、[[更新]](https://msdn.microsoft.com/library/azure/dn903616.aspx)、[[刪除]](https://msdn.microsoft.com/library/azure/dn903611.aspx) 和其他作業管理密碼編譯金鑰。

- 使用 [[取得]](https://msdn.microsoft.com/library/azure/dn903633.aspx)、[更新] (https://msdn.microsoft.com/library/azure/dn986818.aspx)、[[刪除]](https://msdn.microsoft.com/library/azure/dn903613.aspx) 和其他作業管理密碼。

- 以 [[簽章]](https://msdn.microsoft.com/library/azure/dn878096.aspx)/[[驗證]](https://msdn.microsoft.com/library/azure/dn878082.aspx)、[[包裝金鑰]](https://msdn.microsoft.com/library/azure/dn878066.aspx)/[[解除包裝金鑰]](https://msdn.microsoft.com/library/azure/dn878079.aspx) 和 [[加密]](https://msdn.microsoft.com/library/azure/dn878060.aspx)/[[解密]](https://msdn.microsoft.com/library/azure/dn878097.aspx) 作業使用密碼編譯金鑰。

針對金鑰保存庫的作業會使用 Azure Active Directory 驗證及授權。

## 金鑰保存庫的程式設計

程式設計人員的金鑰保存庫管理系統由幾個介面組成，並以 REST 做為基礎。[金鑰保存庫 REST API 參考](https://msdn.microsoft.com/library/azure/dn903609.aspx)

|[![.NET](./media/key-vault-developers-guide/net.png)](https://msdn.microsoft.com/library/azure/dn903301.aspx)|[![Node.js](./media/key-vault-developers-guide/nodejs.png)](http://azure.github.io/azure-sdk-for-node/azure-arm-keyvault/latest)
|:--:|:--:|
|[.NET](https://msdn.microsoft.com/library/azure/dn903301.aspx)|[Node.js](http://azure.github.io/azure-sdk-for-node/azure-arm-keyvault/latest)

## 管理金鑰保存庫

Azure 金鑰保存庫容器 (保存庫) 可以使用 REST、PowerShell 或 CLI 管理，如下列文章中所述：

- [使用 REST 建立和管理金鑰保存庫](https://msdn.microsoft.com/library/azure/mt620024.aspx)
- [使用 PowerShell 建立和管理金鑰保存庫](key-vault-get-started.md)
- [使用 CLI 建立和管理金鑰保存庫](key-vault-manage-with-cli.md)


## 作法

下列文章提供工作的特定指引：

- [如何為 Azure 金鑰保存庫產生並傳輸受 HSM 保護的金鑰](key-vault-hsm-protected-keys.md)

## 範例

- 這個下載包含範例應用程式 HelloKeyVault 和 Azure Web 服務範例。[Azure 金鑰保存庫程式碼範例](http://www.microsoft.com/download/details.aspx?id=45343)
- 使用此教學課程來幫助您了解如何從 Azure 中的 Web 應用程式使用 Azure 金鑰保存庫。[從 Web 應用程式使用 Azure 金鑰保存庫](key-vault-use-from-web-application.md)

## 支援程式庫

- [Microsoft Azure 金鑰保存庫核心程式庫](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core/1.0.0)提供 IKey 和 IKeyResolver 介面，以從識別碼尋找金鑰和使用金鑰執行作業。

- [Microsoft Azure 金鑰保存庫延伸模組](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions/1.0.0)提供 Azure 金鑰保存庫的擴充功能。

<!---HONumber=Nov15_HO3-->