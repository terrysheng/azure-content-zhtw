<properties 
	pageTitle="共用存取簽章：了解 SAS 模型 | Microsoft Azure" 
	description="了解如何使用共用存取簽章來委派對 Blob、佇列和資料表資源的存取" 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/10/2014" 
	ms.author="tamram"/>



# 共用存取簽章，第 1 部分：了解 SAS 模型

若要在無需提供您帳戶金鑰的情況下，將儲存體帳戶中 Blob、資料表和佇列的限制存取授與其他用戶端，則使用共用存取簽章 (SAS) 會是個佷有效的方式。在本教學課程有關共用存取簽章的第 1 部分中，我們將提供 SAS 模型的概觀並檢閱 SAS 最佳做法。[第 2 部分](../storage-dotnet-shared-access-signature-part-2/) (在本教學課程中) 將逐步引導您使用 Blob 服務來完成建立共用存取簽章的程序。

## 共用存取簽章為何？ ##

共用存取簽章可提供您儲存體帳戶中資源的委派存取。這表示您可以在無需分享您帳戶存取金鑰的情況下，將您 Blob、佇列或資料表的有限權限授與用戶端，該用戶端便可在指定的時間期間內及使用指定的權限集來進行存取。SAS 是一種 URI，URI 會在其查詢參數中包含通過驗證存取儲存體資源的所有必要資訊。若要使用 SAS 存取儲存體資源，用戶端只需在適當的建構函式或方法中傳入 SAS 即可。

## 使用共用存取簽章的時機？ ##

當您想要將儲存體帳戶中的資源存取權提供給無法放心託付帳戶金鑰的用戶端時，您可以使用 SAS。您的儲存體帳戶金鑰包括主要和次要金鑰，兩者皆可授與帳戶及帳戶內所有資源的系統管理存取權。提供任一帳戶金鑰都會讓您的帳戶受到惡意或粗心使用的可能性。共用存取簽章提供一個安全的替代方式，無需帳戶金鑰便可讓其他用戶端根據他們被授與的權限，來讀取、寫入及刪除儲存體帳戶中的資料。

證明 SAS 非常有用的一個常見案例，就是使用者在您的儲存體帳戶中讀取和寫入自己的資料。在儲存體帳戶儲存使用者資料的案例中，典型的設計模式有兩種：


1\. 用戶端通過前端 Proxy 服務 (執行驗證) 來上傳與下載資料。此前端 Proxy 服務有個好處，那就是允許商務規則的驗證，但在大量資料或大量交易的情況下，建立可調整以符合需求的服務可能十分昂貴或困難。

![sas-storage-fe-proxy-service][sas-storage-fe-proxy-service]

2\.	輕量型服務可視需要驗證用戶端，然後產生 SAS。在用戶端收到 SAS 之後，他們可以使用 SAS 所定義的權限，並在 SAS 允許的間隔內直接存取帳戶資源。SAS 可減輕透過前端 Proxy 服務路由所有資料的需求。

![sas-storage-provider-service][sas-storage-provider-service]

視所涉及的案例而定，許多實際服務可能會混合運用這兩種方法，在部分資料透過前端 Proxy 進行處理與驗證時，其他資料會使用 SAS 直接儲存與/或讀取。

## 共用存取簽章的運作方式 ##

共用存取簽章是 URI，可指向儲存體資源並包括指出用戶端可以如何存取資源的一組特殊查詢參數。簽章是這些參數的其中一個，根據 SAS 參數所建構並使用帳戶金鑰進行簽署。Azure 儲存體會使用此簽章來驗證 SAS。

共用存取簽章包含下列定義共用存取簽章的限制，每項限制都會以 URI 上的參數表示：

- **儲存體資源。**您可以為其委派存取權的儲存體資源，包括容器、Blob、佇列、資料表及各種資料表實體。
- **開始時間。**這是 SAS 生效的時間。您可以選擇是否指定共用存取簽章的開始時間；如果省略，SAS 會立即生效。
- **到期時間。**這是 SAS 失效的時間。最佳做法是建議您為 SAS 指定一個到期時間，或將它與預存的存取原則建立關聯 (請參閱下方的詳細說明)。
- **權限。**在 SAS 上指定的權限會指出用戶端可以使用 SAS 對儲存體資源執行哪些作業。

以下是提供讀取和寫入 Blob 權限的 SAS URI 範例。此資料表會細分 URI 的每一部分，以了解它會如何影響 SAS：

https://myaccount.blob.core.windows.net/sascontainer/sasblob.txt?sv=2012-02-12&st=2013-04-29T22%3A18%3A26Z&se=2013-04-30T02%3A23%3A26Z&sr=b&sp=rw&sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D

<table border="1" cellpadding="0" cellspacing="0">
    <tbody>
        <tr>
            <td valign="top" width="213">
                <p>
                    Blob URI
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    https://myaccount.blob.core.windows.net/sascontainer/sasblob.txt
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    Blob 的位址。請注意，我們強烈建議您使用 HTTPS。
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top" width="213">
                <p>
                    儲存體服務版本
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    sv=2012-02-12
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    若是儲存體服務版本 2012-02-12 和更新版本，此參數表示要使用的版本。
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top" width="213">
                <p>
                    開始時間
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    st=2013-04-29T22%3A18%3A26Z
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    指定採用 ISO 8061 格式。如果您想要 SAS 立即生效，請略過開始時間。
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top" width="213">
                <p>
                    過期時間
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    se=2013-04-30T02%3A23%3A26Z
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    指定採用 ISO 8061 格式。
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top" width="213">
                <p>
                    資源
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    sr=b
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    此資源是 Blob。
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top" width="213">
                <p>
                    權限
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    sp=rw
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    SAS 所授與的權限包括讀取 (r) 和寫入 (w)。
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top" width="213">
                <p>
                    簽章
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    用來驗證對 Blob 的存取權。此簽章是 HMAC 根據要簽署字串和金鑰，使用 SHA256 演算法進行計算，然後使用 Base64 方式進行編碼而來的。
                </p>
            </td>
        </tr>
    </tbody>
</table>


## 使用預存存取原則來控制共用存取簽章 ##

共用存取簽章可以採用下列其中一種形式：

- **臨機操作 SAS：**當您建立臨機操作 SAS 時，SAS 的開始時間、到期時間及權限都是在 SAS URI 上指定 (如果省略開始時間，則視為隱含)。您可以在容器、Blob、資料表或佇列上建立此類型的 SAS。
- **具有預存存取原則的 SAS：**預存存取原則是在資源容器 (Blob 容器、資料表或佇列) 上定義，並可用來管理一或多個共用存取簽章的條件約束。當您將 SAS 與預存存取原則建立關聯時，SAS 會繼承為預存存取原則定義的條件約束 (開始時間、到期時間及權限)。

這兩種格式間的差異對於以下這一個重要案例而言相當重要：撤銷。SAS 是一種 URL，因此取得 SAS 的任何人都可以使用它，無論起先要求的人是誰。如果是公開發佈 SAS，則全世界的人都可以使用此 SAS。散佈的 SAS 在發生以下四個情況其中之一之前都會持續有效：

1.	已到達 SAS 上指定的過期時間。
2.	已到達在 SAS 所參考之預存存取原則上所指定的過期時間 (如果參考的是預存存取原則，而且如果此預存存取原則指定了過期時間)。發生的原因有可能是因為已經超過指定的間隔時間，或因為您已修改預存存取原則，將過期時間設定為過去的日期，這是撤銷 SAS 的一種方法。
3.	已刪除 SAS 所參考之預存存取原則，這是撤銷 SAS 的另外一種方法。請注意，如果您使用完全相同的名稱來重新建立預存存取原則，則現有的所有 SAS 權杖會根據與該預存存取原則有關的權限再次有效 (假設 SAS 上的過期時間尚未過去)。如果您打算撤銷 SAS，且如果您要使用未來的過期時間來重新建立存取原則，則務必使用不同的名稱。
4.	系統會重新產生用來建立 SAS 的帳戶金鑰。請注意，這麼做將會導致所有使用該帳戶金鑰的應用程式元件無法進行驗證，直到他們已更新為使用其他有效帳戶金鑰或重新產生帳戶金鑰為止。
 
## 使用共用存取簽章的最佳做法 ##

當您在應用程式中使用共用存取簽章時，您必須留意兩個潛在風險：

- 如果 SAS 洩漏出去，則取得該 SAS 的任何人都可以使用它，這有可能會洩露您的儲存體帳戶。
- 如果提供給用戶端應用程式的 SAS 已過期，且此應用程式無法從您的服務擷取新的 SAS，那麼該應用程式的功能可能會受到影響。  

下列關於使用共用存取簽章的建議將協助您平衡這些風險：

1. **一律 HTTPS** 來建立 SAS 或散佈 SAS。如果透過 HTTP 來傳遞 SAS 並被攔截，執行攔截式攻擊的攻擊者將能夠讀取並使用 SAS (就如同預期使用者執行般)，這有可能會洩露敏感資料或允許惡意使用者損毀資料。
2. **儘可能參考預存存取原則。**預存存取原則可讓您選擇撤銷權限但不重新產生儲存體帳戶金鑰。請在這些原則設定很長 (或無限期) 的到期時間，並確定它會定期更新以便更長久使用它。
3. **在臨機操作 SAS 上使用短期的到期時間。**如此一來，即使 SAS 在無意中外洩，也只會在短時間內可行。如果您無法參考預存存取原則，此做法就特別重要。此做法也可藉由限制對 Blob 進行上傳時的可用時間，協助限制可寫入 Blob 的資料多寡。
4. **視需要讓用戶端自動更新 SAS。**用戶端應該在預期的到期時間之前更新 SAS，以便在提供 SAS 的服務無法供使用時，能夠有時間重試。如果您的 SAS 是要用於少量且立即的短期作業，亦即預期會在指定的到期時間內完成，可能就不需要這麼做，因為預期不需更新 SAS。不過，如果您有用戶端會定期透過 SAS 提出要求，就有可能發生到期的情況。主要的考量是要在下列兩種需求之間取得平衡：短期 SAS 需求 (如上所述)，以及確保用戶端要求更新的時間夠早 (以避免因 SAS 在順利更新之前即已到期而導致中斷) 的需求。
5. **謹慎使用 SAS 開始時間。**如果您將 SAS 的開始時間設定為「現在」，則由於時鐘誤差 (因不同的電腦而產生的目前時間差異)，在前幾分鐘可能會觀察到一些間歇性的失敗。一般而言，請將開始時間至少設定為 15 分鐘前，或完全不設定，這會讓它在所有情況下都立即生效。同樣的做法通常也適用於到期時間，還記得您可能會在任何要求的任一方向觀察到最多 15 分鐘的時鐘誤差。請注意，如果用戶端使用的 REST　版本早於 2012-02-12，則未參考預存存取原則之 SAS 的最長持續時間為 1 小時，所有指定期間超過此值的原則都將失敗。
6.	**具體指定要存取的資源。**一般最佳的安全性做法是為使用者提供最低限度的必要權限。如果使用者只需要單一實體的讀取權限，就授與他們該單一實體的讀取權限，而不授與他們所有實體的讀取/寫入/刪除權限。這也有助於降低　SAS 遭到盜用的威脅，因為 SAS 在入侵者的手中已較不具威力。
7.	**了解您的帳戶將必須為任何使用付費，包括以 SAS 進行的使用。**如果您提供 Blob 的寫入權限，使用者可能選擇上傳 200GB 的 Blob。如果您也提供讀取權限給他們，他們可能會選擇下載該 Blob 10 次，導致為您產生 2TB 的資料外送流量費用。同樣地，提供有限的權限可協助降低潛在惡意使用者的風險。您可以使用短期 SAS 來降低此威脅 (但請留意結束時間的時鐘誤差)。
8.	**驗證使用 SAS 寫入的資料。**當用戶端應用程式將資料寫入您的儲存體帳戶時，請記住該資料可能會有問題。如果您的應用程式要求該資料必須經過驗證或授權後才可使用，您就應該在資料被寫入後且您應用程式使用它之前，執行這項驗證。此做法也可防止使用者 (不論是正確取得 SAS 的使用者，還是惡意探索洩漏之 SAS 的使用者) 將損毀或惡意的資料寫入您的帳戶。
9. **不要一律使用 SAS。**有時，與您儲存體帳戶的特定作業關聯的風險會大於 SAS 的好處。針對這類作業，請建立中介層服務，此服務會在執行商務規則驗證、驗證及稽核之後，才寫入儲存體帳戶。此外，有時以其他方式管理存取會比較簡單。例如，如果您想要將容器中的所有 Blob 都設定為可公開讀取，您可以將容器設定為「公用」，而不是將 SAS 提供給每個用戶端來進行存取。
10.	**使用儲存體分析來監視您的應用程式。**您可以使用記錄功能和度量，來觀察任何因 SAS 提供者服務中斷或不小心移除預存存取原則而造成的驗證失敗暴增情況。如需其他資訊，請參閱 [Azure 儲存體團隊部落格](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/08/03/windows-azure-storage-logging-using-logs-to-track-storage-requests.aspx)。

## 結論 ##

若要提供您儲存體帳戶的有限權限給沒有帳戶金鑰的用戶端，則共用存取簽章是非常有用的方式。因此，對於任何使用 Azure 儲存體的應用程式而言，共用存取簽章是安全性模型不可或缺的一部分。如果您依照此處所列的最佳做法進行，則您可以使用 SAS 來提供更大的彈性以存取儲存體帳戶中的資源，且不會影響應用程式的安全性。

## Next Steps ##

[Shared Access Signatures, Part 2: Create and Use a SAS with the Blob Service](../storage-dotnet-shared-access-signature-part-2/)

[Manage Access to Azure Storage Resources](http://msdn.microsoft.com/library/windowsazure/ee393343.aspx)

[Delegating Access with a Shared Access Signature (REST API)](http://msdn.microsoft.com/library/windowsazure/ee395415.aspx)

[Introducing Table and Queue SAS](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx)
[sas-storage-fe-proxy-service]: ./media/storage-dotnet-shared-access-signature-part-1/sas-storage-fe-proxy-service.png
[sas-storage-provider-service]: ./media/storage-dotnet-shared-access-signature-part-1/sas-storage-provider-service.png


<!--HONumber=42-->
