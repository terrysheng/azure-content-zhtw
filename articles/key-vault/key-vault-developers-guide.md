<properties
   pageTitle="金鑰保存庫開發人員指南 | Microsoft Azure"
   description="開發人員可以使用 Azure 金鑰保存庫來管理 Microsoft Azure 環境中的密碼編譯金鑰。"
   services="key-vault"
   documentationCenter=""
   authors="BrucePerlerMS"
   manager="mbaldwin"
   editor="mbaldwin" />
<tags
   ms.service="key-vault"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/22/2015"
   ms.author="mbaldwin" />

# Azure 金鑰保存庫開發人員指南

> [AZURE.VIDEO azure-key-vault-developer-quick-start]

開發人員可以使用 Azure 金鑰保存庫來管理 Microsoft Azure 環境中的密碼編譯金鑰。金鑰保存庫支援多種金鑰類型和演算法，也可針對高價值的客戶金鑰搭配硬體安全性模組 (HSM) 使用。此外，您可以使用金鑰保存庫來安全地儲存密碼，也就是沒有特定語意且有大小限制的八位元物件。金鑰保存庫可以包含金鑰和密碼的混合。物件類型的存取控制會受到獨立管理。

已成功取得授權的使用者可以執行下列作業：

- 使用 [[建立]](https://msdn.microsoft.com/library/azure/dn903634.aspx)、[[匯入]](https://msdn.microsoft.com/library/azure/dn903626.aspx)、[[更新]](https://msdn.microsoft.com/library/azure/dn903616.aspx)、[[刪除]](https://msdn.microsoft.com/library/azure/dn903611.aspx) 和其他作業管理密碼編譯金鑰。

- 使用 [[取得]](https://msdn.microsoft.com/library/azure/dn903633.aspx)、[更新] (https://msdn.microsoft.com/library/azure/dn986818.aspx)、[[刪除]](https://msdn.microsoft.com/library/azure/dn903613.aspx) 和其他作業管理密碼。

- 以 [[簽章]](https://msdn.microsoft.com/library/azure/dn878096.aspx)/[[驗證]](https://msdn.microsoft.com/library/azure/dn878082.aspx)、[[包裝金鑰]](https://msdn.microsoft.com/library/azure/dn878066.aspx)/[[解除包裝金鑰]](https://msdn.microsoft.com/library/azure/dn878079.aspx) 和 [[加密]](https://msdn.microsoft.com/library/azure/dn878060.aspx)/[[解密]](https://msdn.microsoft.com/library/azure/dn878097.aspx) 作業使用密碼編譯金鑰。

針對金鑰保存庫的作業會使用 Azure Active Directory 驗證及授權。

## 金鑰保存庫的程式設計

程式設計人員的金鑰保存庫管理系統由幾個介面組成，並以 REST 做為基礎。REST 參考中的文章[有關金鑰和密碼](https://msdn.microsoft.com/library/azure/dn903623.aspx)提供核心概念

### REST

REST API 是以程式設計方式和金鑰保存庫互動的基礎。

金鑰保存庫有其自己的 REST 端點，如[金鑰保存庫 REST API 參考](https://msdn.microsoft.com/library/azure/dn903609.aspx)中所述。

### .NET

.NET API 是一組包裝函式，可透過 C# 程式設計模型實作，而不需要直接與 REST 端點互動。您可以在這裡找到 [Azure 金鑰保存庫.NET 用戶端 API 參考](https://msdn.microsoft.com/library/azure/dn903301.aspx)。

### Node.js

Node.js API 是一組包裝函式，可透過 node.js 程式設計模型實作，而不需要直接與 REST 端點互動。您可以在這裡找到 [Microsoft Azure SDK for Node.js - 金鑰保存庫管理](http://azure.github.io/azure-sdk-for-node/azure-arm-keyvault/latest/)。

## 管理金鑰保存庫

Azure 金鑰保存庫容器 (保存庫) 可以使用 PowerShell 或 CLI 管理，如下列文章中所述：

- [使用 PowerShell 建立和管理金鑰保存庫](key-vault-get-started.md)
- [使用 CLI 建立和管理金鑰保存庫](key-vault-manage-with-cli.md)


## 作法

下列文章提供工作的特定指引：

- [如何為 Azure 金鑰保存庫產生並傳輸受 HSM 保護的金鑰](key-vault-hsm-protected-keys.md)

## 範例

- 這個下載包含範例應用程式 HelloKeyVault 和 Azure Web 服務範例。[Azure 金鑰保存庫程式碼範例](http://www.microsoft.com/download/details.aspx?id=45343)

<!---HONumber=Oct15_HO3-->