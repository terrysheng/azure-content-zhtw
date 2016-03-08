<properties
   pageTitle="在 Azure RemoteApp 集合中發佈應用程式給個別使用者 (預覽) | Microsoft Azure"
   description="了解如何在 Azure RemoteApp 中發佈應用程式給個別使用者，而非以群組為單位來發佈。"
   services="remoteapp-preview"
   documentationCenter=""
   authors="piotrci"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="03/01/2016"
   ms.author="piotrci"/>

# 在 Azure RemoteApp 集合中發佈應用程式給個別使用者 (預覽)

本文說明如何在 Azure RemoteApp 集合中發佈應用程式給個別使用者。這是 Azure RemoteApp 的新功能，目前還是「私人預覽」狀態，僅提供給選出的早期採用者進行評估。

Azure RemoteApp 最初只啟用一種「發佈」應用程式的方式：系統管理員會從映像發佈應用程式，而且集合中的所有使用者都能看見。

常見的案例是在單一映像中包含許多應用程式並部署一個集合，以降低管理成本。但有時候並非所有應用程式都與所有使用者有關，因此系統管理員會想要將應用程式發佈給個別使用者，讓使用者不會在應用程式摘要中看到不必要的應用程式。

現在 Azure RemoteApp 已提供這項功能，但目前為受限制的預覽功能。以下是新功能的簡短摘要：

1. 集合可以設定成兩種模式之一：
 
  - 原始的「集合模式」，在此模式中，集合內的所有使用者都可以看到所有已發佈的應用程式。這是預設模式。
  - 新推出的「應用程式模式」，在此模式中，使用者只能看見明確指派給他們的應用程式。

2. 應用程式模式目前只能使用 Azure RemoteApp PowerShell Cmdlet 來啟用。

  - 設定為應用程式模式時，將無法透過 Azure 入口網站管理集合中的使用者指派。您必須透過 PowerShell Cmdlet 管理使用者指派。

3. 使用者只會看到直接發佈給他們的應用程式。不過，使用者若想啟動映像上的其他可用應用程式，仍可藉由在作業系統中直接進行存取來達成。
  - 此功能只是限制應用程式摘要中的可見性，並不提供應用程式的安全鎖定。
  - 如果您需要讓使用者與應用程式隔離開來，您必須使用不同的集合。

## 如何取得 Azure RemoteApp PowerShell Cmdlet

若要嘗試新的預覽功能，您必須使用 Azure PowerShell Cmdlet。目前還無法使用 Azure 管理入口網站來啟用新的應用程式發佈模式。

首先請確定您已安裝 [Azure PowerShell 模組](../powershell-install-configure.md)。

然後以系統管理員模式啟動 PowerShell 主控台，並執行下列 Cmdlet：

		Add-AzureAccount

它會提示您輸入 Azure 使用者名稱和密碼。登入後，您就可以針對 Azure 訂用帳戶執行 Azure RemoteApp Cmdlet。

## 如何確認集合目前是什麼模式

執行下列 Cmdlet：

		Get-AzureRemoteAppCollection <collectionName>

![查看集合模式](./media/remoteapp-perapp/araacllelvel.png)

AclLevel 屬性的可能值如下：

- 集合：原始發佈模式。所有使用者都能看到所有已發佈的應用程式。
- 應用程式：新的發佈模式。使用者只會看到直接發佈給他們的應用程式。

## 如何切換為應用程式發佈模式

執行下列 Cmdlet：

		Set-AzureRemoteAppCollection -CollectionName -AclLevel Application

應用程式發佈狀態將會保留下來：一開始所有使用者會看到所有原本已發佈的應用程式。

## 如何列出可以看到特定應用程式的使用者

執行下列 Cmdlet：

		Get-AzureRemoteAppUser -CollectionName <collectionName> -Alias <appAlias>

這會列出所有能看到應用程式的使用者。

附註：執行 Get-AzureRemoteAppProgram -CollectionName 就能看到應用程式別名 (在上述語法中稱為 "app alias") <collectionName>。

## 如何指派應用程式給使用者

執行下列 Cmdlet：

		Add-AzureRemoteAppUser -CollectionName <collectionName> -UserUpn <user@domain.com> -Type <OrgId|MicrosoftAccount> -Alias <appAlias>

使用者現在將會看到 Azure RemoteApp 用戶端中的應用程式，並且能夠與其連接。

## 如何移除使用者的應用程式

執行下列 Cmdlet：

		Remove-AzureRemoteAppUser -CollectionName <collectionName> -UserUpn <user@domain.com> -Type <OrgId|MicrosoftAccount> -Alias <appAlias>

## 提供意見反應
歡迎您提供有關這項預覽功能的意見反應和建議。請填寫[問卷](http://www.instant.ly/s/FDdrb)，以讓我們知道您的想法。

## 還沒有機會試用預覽功能嗎？
如果您還未參與使用預覽功能，請使用本[問卷](http://www.instant.ly/s/AY83p)來要求存取權。

<!---HONumber=AcomDC_0302_2016-->