<properties
    pageTitle="Azure RemoteApp 範本映像中有什麼內容？| Microsoft Azure"
    description="了解 Azure RemoteApp 隨附的範本映像。"
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="04/05/2016"
    ms.author="elizapo" />

# Azure RemoteApp 範本映像中有什麼內容？

Azure RemoteApp 訂閱包含三個範本映像：


- Windows Server 2012
- Microsoft Office 365 ProPlus (需訂閱 Office 365)
- Microsoft Office 2013 Professional Plus (僅限試用版)

> [AZURE.IMPORTANT]您的 Azure RemoteApp 訂用帳戶讓您可以存取映像中的軟體，除了需要個別訂閱的 Office 365 ProPlus，與不能用在生產環境中的 Office 2013 外。這表示您可以與使用者共用範本映像上的程式或應用程式。例如，如果您建立使用 Windows Server 2012 R2 映像的集合，則可以發佈 System Center Endpoint Protection，讓使用者透過 RemoteApp 存取。
>
> 如需詳細資訊，請查看 [RemoteApp 授權詳細資訊](remoteapp-licensing.md)。此外，如需 Office 授權資訊，請參閱[使用 Office 與 Azure RemoteApp 搭配](remoteapp-o365.md)。

閱讀每個映像包含之內容的詳細資訊。

## Windows Server 2012 R2 (「Vanilla 映像」)
此映像以 Microsoft Windows Server 2012 R2 Datacenter 作業系統為基礎，並已安裝下列角色和功能，以符合 Azure RemoteApp 範本映像的需求：


- .NET Framework 4.5、3.5.1、3.5
- 桌面體驗
- 筆跡和手寫服務
- 媒體基礎
- 遠端桌面工作階段主機
- Windows PowerShell 4.0
- Windows PowerShell ISE
- WoW64 支援

此映像也安裝了下列應用程式：

- Adobe Flash Player
- Microsoft Silverlight
- Microsoft System Center 2012 Endpoint Protection
- Microsoft Windows Media Player


## Microsoft Office 365 ProPlus (需要訂閱)
Office 365 是最常要求的應用程式，因此我們建立「 自訂 」映像供您使用。

此映像是 Vanilla 映像的延伸，而且除了 Windows Server 2012 R2 映像中描述的元件外，還安裝了下列 Microsoft Office 365 ProPlus 元件：


- Access
- Excel
- Lync
- OneNote
- OneDrive for Business
- Outlook
- PowerPoint
- Word
- Microsoft Office 校訂工具

映像也包含 Visio Pro 及 Project Pro。

以及下列應用程式：

- SQL 原生用戶端
- ODBC 驅動程式
- SQL Server 資料採礦用戶端
- MasterDataServices 用戶端
- Microsoft Publisher
- PowerQuery
- PowerMap


Office 365 ProPlus 應用程式的完整功能只適用於擁有 Office 365 ProPlus 方案的使用者。如需 Office 365 訂閱方案的詳細資訊，請參閱 [Office 365 服務方案](http://technet.microsoft.com/library/office-365-plan-options.aspx)。還有疑問嗎？ 請查看 [Office 365 + RemoteApp](remoteapp-o365.md) 資訊。同時請查看新的文章：[如何搭配 Azure RemoteApp 使用 Office 365 訂用帳戶](remoteapp-officesubscription.md)。

請注意您必須個別授權 Office 365 ProPlus、Visio Pro 和 Project Pro，他們都有自己的授權。

## Microsoft Office 2013 Professional Plus (僅限試用版)
在免費試用期間，您可以使用 Office 2013 映像測試服務。

此映像是 Vanilla 映像的延伸，而且除了 Windows Server 2012 R2 映像中描述的元件外，還安裝了下列 Microsoft Office 2013 Professional Plus 元件：


- Access
- Excel
- Lync
- OneNote
- OneDrive for Business
- Outlook
- PowerPoint
- 隨附此逐步解說的專案
- Visio
- Word
- Microsoft Office 校訂工具

> [AZURE.IMPORTANT]**重要的法律資訊：**此映像不包含 Microsoft Office 授權，且*無法用於生產環境*。Office 2013 Professional Plus 映像僅作為試用之用。如果您想要在 Azure RemoteApp 中使用 Office 應用程式作為生產之用，您必須使用 Office 365 ProPlus 映像。如需授權 Office 的詳細資訊，請參閱[使用 Office 365 與 Azure RemoteApp 搭配](remoteapp-o365.md)

<!---HONumber=AcomDC_0406_2016-->