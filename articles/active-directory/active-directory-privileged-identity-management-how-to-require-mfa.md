<properties
   pageTitle="Azure 特殊權限身分識別管理：如何強制啟用 MFA"
   description="了解如何利用 Azure 特殊權限身分識別管理擴充功能，為特殊權限身分強制啟用 MFA (多重要素驗證)。"
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="01/21/2016"
   ms.author="kgremban"/>

# Azure Privileged Identity Management：如何強制啟用 MFA

強烈建議您要求所有系統管理員強制啟用多重要素驗證。

##在 Azure 特殊權限身分識別管理中強制啟用 MFA
以 PIM 系統管理員的身分登入時，您會收到警示訊息，建議您要求特殊權限的帳戶強制啟用多重要素驗證 (MFA)。按一下 PIM 儀表板中的安全性警示，新的刀鋒視窗會隨即開啟，並顯示應啟用 MFA 的系統管理員帳戶清單。您可以選取多個角色，然後按一下 [修正] 按鈕，以強制啟用 MFA；或者您也可以按一下個別角色旁邊的省略符號，然後按一下 [修正] 按鈕。

此外，您也可以變更特定角色的 MFA 要求，方法是在儀表板的 [角色] 區段中按一下此角色，然後在角色的刀鋒視窗中按一下 [設定]，並在多重要素驗證底下選取 [啟用]，即可為該角色啟用 MFA。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 後續步驟
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0204_2016-->