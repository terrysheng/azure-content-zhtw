<properties 
	pageTitle="了解快照集產生費用的方式" 
	description="了解 Azure 儲存體 Blob 快照集計費的指南" 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/20/2015" 
	ms.author="tamram"/>

#了解快照集產生費用的方式

建立快照集 (即 Blob 的唯讀複本) 可能會為您的帳戶產生額外的資料儲存體費用。設計您的應用程式時，請務必留意可能產生這些費用的方式，以便將不必要的成本降至最低。

##重要的計費考量

下列清單包含建立快照集時要考量的重點。

- 無論唯一的區塊或分頁是在 Blob 或快照集中，都會產生費用。在您更新快照集所依據的 Blob 之前，您的帳戶不會針對與該 Blob 相關聯的快照集產生額外費用。一旦您更新基底 Blob 之後，它就會與其快照集分離，而系統將針對每個 Blob 或快照集中唯一的區塊或分頁向您收費。

- 當您取代區塊 Blob 內的某個區塊時，後續即會將該區塊視為唯一區塊進行收費。即使該區塊的區塊識別碼和資料與其在快照集中擁有的相同，也是如此。一旦再次認可該區塊之後，其就會與它在任何快照集的對應項目分離，而您將需支付其資料的費用。這同樣適用分頁 Blob 中以相同資料更新的頁面。

- 藉由呼叫 UploadFile、UploadText、UploadStream 或 UploadByteArray 方法來取代區塊 Blob，會取代該 Blob 中的所有區塊。如果您的快照集與該 Blob 相關聯，基底 Blob 和快照集中的所有區塊現在都會分離出來，而您將針對這兩個 Blob 的所有區塊支付費用。即使基底 Blob 和快照集中的資料保持一致，也是如此。

- Azure Blob 服務未提供方法來判斷兩個區塊是否包含相同資料。已上傳且認可的每個區塊都會被視為唯一，即使它具有相同資料和相同的區塊識別碼也一樣。由於費用是針對唯一區塊而產生，因此，請務必考量更新含有快照集的 Blob 將產生額外的唯一區塊及額外費用。

> [AZURE.NOTE] 最佳做法是要求您謹慎管理快照集，以避免產生額外費用。建議您以下列方式管理快照集：

> - 每當您更新 Blob 時，刪除並重新建立與該 Blob 相關聯的快照集，除非您的應用程式設計為需要維護快照集，否則即使您正以相同資料進行更新也一樣。藉由刪除並重新建立 Blob 的快照集，讓您可以確保該 Blob 和快照集不會分離開來。

> - 如果您正在維護 Blob 的快照集，請避免呼叫 UploadFile、UploadText、UploadStream 或 UploadByteArray 來更新 Blob，因為這些方法會取代 Blob 中的所有區塊。相反地，請使用 PutBlock 和 PutBlockList 方法來更新最少的可能區塊數目。


##快照集計費案例


下列案例示範如何針對區塊 Blob 及其快照集產生費用。 

在案例 1 中，基底 Blob 在擷取快照之後尚未更新，因此只會針對唯一區塊 1、2 和 3 產生費用。

![Azure Storage Resources](./media/storage-blob-snapshots-billing/storage-blob-snapshots-billing-scenario-1.png)

在案例 2 中，基底 Blob 已更新，但快照集並未更新。區塊 3 已更新，而且即使它包含相同資料及相同的識別碼，它還是與快照集中的區塊 3 不一樣。因此，此帳戶必須支付四個區塊的費用。

![Azure Storage Resources](./media/storage-blob-snapshots-billing/storage-blob-snapshots-billing-scenario-2.png)

在案例 3 中，基底 Blob 已更新，但快照集並未更新。區塊 3 已使用基底 Blob 中的區塊 4 來取代，但快照集仍然反映區塊 3。因此，此帳戶必須支付四個區塊的費用。
 
![Azure Storage Resources](./media/storage-blob-snapshots-billing/storage-blob-snapshots-billing-scenario-3.png)

在案例 4 中，基底 Blob 已完全更新，且未包含它的任何原始區塊。因此，此帳戶必須支付所有八個唯一區塊的費用。如果您使用像是 UploadFile、UploadText、UploadFromStream 或 UploadByteArray 的更新方法，就會發生這個案例，因為這些方法會取代 Blob 的所有內容。

![Azure Storage Resources](./media/storage-blob-snapshots-billing/storage-blob-snapshots-billing-scenario-4.png)
<!--HONumber=47-->
