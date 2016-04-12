
<properties 
    pageTitle="Azure RemoteApp 的 Azure AD + Active Directory 需求 | Microsoft Azure" 
    description="了解如何設定 Active Directory 以使用 Azure RemoteApp。" 
    services="remoteapp" 
	documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="03/28/2016" 
    ms.author="elizapo" />



# Azure RemoteApp 的 Azure AD + Active Directory 需求



針對 Azure RemoteApp 混合式集合，或您要使用 AD Connect 建立同盟的雲端集合，您必須執行下列動作。

### 連接 Azure AD 和 Active Directory

如果您要連接 Azure AD 租用戶和內部部署的 Active Directory 環境，請使用 AD Connect。您只要[按 4 下](http://blogs.technet.com/b/ad/archive/2014/08/04/connecting-ad-and-azure-ad-only-4-clicks-with-azure-ad-connect.aspx)即可連接兩個目錄。

注意 - 混合式集合需使用目錄同步處理。

### 請確定您的 "@domain.com" 相符
在開始之前，請確定您在內部部署樹系的 UPN 符合您 Azure AD 網域的尾碼。

當您在 Azure AD 中設定 UPN 網域尾碼後，所有登入 Azure RemoteApp 的使用者都會以 “user@<the suffix you set up>” 的身分登入。請確定使用者也可以用相同的 user@suffix 登入到內部部署網域。在某些情況下，您可以在 Azure AD 中設定一個網域名稱，並同時為內部部署使用者指定不同的網域尾碼。在此情況下，您的使用者將無法透過 Azure RemoteApp，連接至任何已加入網域的電腦或資源。

例如，如果您在 AAD 中將 UPN 網域尾碼設定為 contoso.com，但內部部署/AD 的某些使用者是設為以 @contoso.uk 登入，則這些使用者將無法正確登入 ARA 集合。AAD 和 AD 中的使用者 UPN 必須相同才能登入。

### 建立 Azure RemoteApp 的物件
您也必須建立下列內部部署 Active Directory 物件：

- 服務帳戶，以透過將 RDSH 端點加入內部部署的網域，提供 RemoteApp 程式的網域資源存取權。
- 組織單位 (OU)，以包含 RemoteApp 電腦物件。建議您使用 OU (但非必要) 來隔離帳戶與將用於 RemoteApp 的原則。

當您建立 RemoteApp 集合時，您會需要這兩個物件，因此請務必先執行這些步驟。

<!---HONumber=AcomDC_0330_2016-->