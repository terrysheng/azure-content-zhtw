<properties 
	pageTitle="Microsoft Azure 儲存體的用戶端加密 | Microsoft Azure" 
	description="適用於 .NET 的 Azure 儲存體用戶端程式庫預覽提供對於用戶端加密和與 Azure 金鑰保存庫整合的支援。用戶端加密為您的 Azure 儲存體應用程式提供最大安全性，因為您的存取金鑰永遠不會讓服務使用。用戶端加密適用於 Blob、佇列和資料表。" 
	services="storage" 
	documentationCenter=".net" 
	authors="tamram" 
	manager="carolz" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/10/2015" 
	ms.author="tamram"/>


# Microsoft Azure 儲存體 (預覽) 的用戶端加密

## 概觀

歡迎使用[適用於 .NET 的新 Azure 儲存體用戶端程式庫預覽](https://www.nuget.org/packages/WindowsAzure.Storage/4.4.1-preview)。此預覽程式庫包含新功能，可協助開發人員在將用戶端應用程式內的資料上傳至 Azure 儲存體之前，對資料進行加密，以及在下載時解密資料。預覽程式庫也針對儲存體帳戶金鑰管理，支援與 Azure [金鑰保存庫](http://azure.microsoft.com/services/key-vault/)的整合。

## 為何要使用用戶端加密？

用戶端加密提供重要的優點，透過伺服器端加密：您完全掌控您的帳戶存取金鑰。用戶端加密為您的應用程式提供最大安全性，因為 Azure 儲存體永遠不會看到您的金鑰，也永遠不會解密您的資料。預覽程式庫是在 [GitHub](https://github.com/Azure/azure-storage-net/tree/preview) 上開放取用，因此您可以看到程式庫如何加密您的資料以確保其符合您的標準。

## 為什麼我們會提供具有用戶端加密支援的程式庫？

雖然任何開發人員都可以在上傳資料之前於用戶端加密其資料，但是這麼做需要加密的專業知識。也需要針對效能和安全性做設計。不同的開發人員必須設計自己的加密解決方案，因為每個方案各不相同，所以無法搭配使用。

預覽程式庫設計來：

- 為您實作安全性最佳做法。
- 最大化效能。
- 讓通用案例簡單易用。
- 支援跨語言的互通性。使用 .NET 用戶端程式庫加密的資料將在未來由我們其他支援語言的用戶端程式庫解密，包括 Java、Node.js 和 C++ (反之亦然)。

## 現在有哪些項目可用？

預覽程式庫目前支援使用信封技巧加密 Blob、資料表和佇列。使用非對稱金鑰的加密和解密相當昂貴。因此，在信封技術中，資料本身不會直接使用這類金鑰加密，而是改為使用隨機的對稱內容加密金鑰來加密。然後使用公開金鑰來加密此內容加密金鑰。Azure 金鑰保存庫的支援可協助您有效地管理您的金鑰。

使用用戶端加密相當簡單。您可以用適當的加密原則 (Blob、佇列或資料表) 指定要求選項，並將它傳遞給資料上傳/下載 API。用戶端程式庫會在上傳至 Azure 儲存體時，自動加密用戶端上的資料，並且在擷取時解密資料。您可以在[開始使用 Microsoft Azure 儲存體用戶端加密](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/04/29/getting-started-with-client-side-encryption-for-microsoft-azure-storage.aspx)部落格文章中找到更多詳細資料和程式碼範例。

用戶端加密的一些其他詳細資料：

- **安全性**：加密的資料無法讀取，即使客戶儲存體帳戶金鑰被盜用。
- **固定負擔加密**：加密的資料會有根據原始大小的可預測大小。
- **獨立加密** 每個 Blob、資料表實體或佇列訊息會將所有加密資訊儲存在物件本身或其中繼資料中。唯一需要的外部值是您的加密金鑰。
- **金鑰替換**：使用者可以自行替換金鑰，在金鑰替換程序期間支援多個金鑰。
- **全新的升級路徑**：未來將會支援其他加密演算法和通訊協定版本，不需要大幅變更您的程式碼。
- **Blob 加密支援**：
	- **完整的 Blob 上傳**：您可以加密及上傳和檔案一樣的文件、相片和影片。
	- **完整或範圍型 Blob 下載**：您可以完整或範圍下載及解密 Blob。


>[AZURE.IMPORTANT]使用預覽程式庫時，請注意這些要點：
>
>- 請勿對實際執行資料使用預覽程式庫。未來對程式庫的變更會影響使用的結構描述。未來的版本並不保證可以使用預覽程式庫來解密已加密的資料。  
>- 當讀取或寫入加密的 Blob 時，使用完整的 Blob 上傳命令和範圍/完整 Blob 下載命令。避免使用通訊協定作業寫入加密的 Blob，例如放置區塊、放置區塊清單、撰寫頁面或清除頁面；否則您可能會損毀加密的 Blob，並使它無法讀取。
>- 對於資料表，存在類似的條件約束。請小心在未更新加密中繼資料時即更新加密的內容。
>- 如果您在加密 Blob 上設定中繼資料，您可能會覆寫解密所需的加密相關中繼資料，因為設定中繼資料不是附加的。這也適用於快照集 - 避免在建立加密的 Blob 快照集時指定中繼資料。

## 另請參閱

- [開始使用 Microsoft Azure 儲存體的用戶端加密](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/04/29/getting-started-with-client-side-encryption-for-microsoft-azure-storage.aspx)  
- [適用於 .NET NuGet 封裝的 Azure 儲存體用戶端程式庫 (預覽)](http://www.nuget.org/packages/WindowsAzure.Storage/4.4.0-preview)  
- [適用於 .NET 來源程式碼的 Azure 儲存體用戶端程式庫 (預覽)](https://github.com/Azure/azure-storage-net/tree/preview)
 

<!---HONumber=62-->