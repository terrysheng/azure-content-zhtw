<properties
   pageTitle="Azure 特殊權限身分識別管理：安全性精靈"
   description="第一次使用 Azure Privileged Identity Management 擴充功能時，您將會看到安全性精靈。本文說明使用精靈的步驟。"
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

# Azure Privileged Identity Management 安全性精靈

第一次執行 Azure Privileged Identity Management (PIM) 時，您將會看到精靈。此精靈可協助您了解特殊權限身分識別的安全性風險，以及如何使用特殊權限身分識別管理來降低風險。

您需要檢閱下列三個區段：[您的管理員可能讓您身處風險之中]、[管理您的管理員所造成的受管理攻擊面]，以及 [定義暫時性管理設定]。每個區段都會提供相關概念的概觀，以及某些要採取之動作的說明。

首先，您的所有全域管理員都將是永久性的。當您按一下 [您的管理員可能讓您身處風險之中] 時，將會顯示一份全域管理員角色清單，以及您目前所擁有的數量。

按一下 [管理您的管理員所造成的受管理攻擊面]，讓您有機會可以將管理員變更為暫時性、讓他們保持永久性狀態，或者從角色中一併移除他們。

[定義暫時性管理設定] 可讓您要求 Multi-Factor Authentication、啟用通知，以及判斷暫時性管理員將擁有權限的期間長度。

## 將全域管理員角色變更為暫時性或永久性

您有三個選項可用來變更全域管理員的時間範圍：

1.  按一下 [讓全部變成暫時性] 按鈕，讓所有全域管理員變成暫時性的。

2.  按一下 [讓全部變成永久性] 按鈕，讓所有全域管理員變成永久性的。

3.  針對每位全域管理員選取 [保持永久性]、[成為暫時性] 或 [從角色中移除]。

## 變更全域管理員角色的啟用期間。

有兩個方法可為全域管理員設定啟用期間。

1.  向左或向右移動 [啟用期間] 滑桿，以增加或減少啟用期間。啟用期間最多可以是 72 小時。

2.  在滑桿右邊的 [小時] 欄位中輸入時數。

## 啟用通知

為了讓管理員可以在角色成為作用中時接收郵件，可按一下 [啟用] 按鈕來啟用通知。您也可以稍後停用此功能。

## 要求 Multi-Factor Authentication

如果您想要求管理員使用 MFA 登入其帳戶，並要求其角色的擴充功能，請按一下 [啟用] 按鈕來啟用 MFA。您也可以稍後停用此功能。

<!--For more information about MFA and PIM, click here. PLACEHOLDER: NEED LINK TO MFA DOC.-->

選取將套用這些設定的角色。按一下 [確定]。

> [AZURE.WARNING] 請注意，您目前有一個以上的安全性管理員。如果只有一個安全性管理員未設定為永久管理員，而且尚未設定 MFA，則在角色指派到期之後，使用者將完全無法管理 PIM。

完成時，請按一下 [確定]。

進行變更之後，將不會再次顯示精靈。不過，您可以按一下 [管理身分識別] 下方的 [精靈] 按鈕，再次存取它。

## 後續步驟
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0204_2016-->