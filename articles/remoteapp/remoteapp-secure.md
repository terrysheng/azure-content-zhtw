
<properties 
    pageTitle="保護 Azure RemoteApp 中的應用程式和資源"
    description="了解如何鎖定 Azure RemoteApp 中的應用程式和資源" 
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
    ms.date="08/12/2015" 
    ms.author="elizapo" />



# 保護 Azure RemoteApp 中的應用程式和資源

Azure RemoteApp 可讓使用者存取集中管理的 Windows 應用程式，這樣可讓您控制使用者可以或不可以執行的動作。當使用者從未受管理的裝置連接 (例如其個人 Macbook)，而且您想要控制使用者存取權或體驗時，這特別有用。
 
例如，如果您使用 Active Directory 進行使用者驗證，而且您想要防止使用者複製應用程式的資料，您可以設定遠端桌面群組原則來禁止使用者複製資料。
 
另一個例子是您想要對集合中的特定應用程式，封鎖網際網路存取。當您建立集合的映像時，您可以建立 Windows 防火牆規則來封鎖存取。

## 實作選項

  以下是重要的實作選項，可以視需要個別使用或一起使用：

1.	如果您的 RemoteApp 集合已加入網域，您可以強制執行任何[群組原則](https://technet.microsoft.com/library/cc725828.aspx) ([這裡](../azure-subscription-service-limits.md)所述的閒置和中斷連線逾時原則除外)。
2.	除了群組原則 (如果您的集合未加入網域，或您在 AD 中沒有正確的權限)，您也可以在範本映像中設定[本機原則](https://technet.microsoft.com/library/cc775702.aspx)。請注意，當群組原則與本機原則發生衝突時，以群組原則為準。
3.	某些 OS/應用程式設定無法透過原則設定，但可以在設定範本映像時，使用 [RegEdit 工具](./remoteapp-hybridtrouble.md)透過登錄機碼來設定。
4.	您可以使用 [Windows 防火牆](http://windows.microsoft.com/zh-TW/windows-8/Windows-Firewall-from-start-to-finish)來控制從網路進出執行應用程式的電腦。只要確定沒有封鎖這裡所定義的 URL 和連接埠即可。
5.	您可以使用 [AppLocker](https://technet.microsoft.com/library/hh831440.aspx)來控制使用者可以執行的應用程式和檔案。比方說，聰明的使用者可能在您用來建立集合的映像中，想出方法來執行您未發佈的應用程式 - AppLocker 可以排除這種情況。
 
## 詳細資訊

- 下列 RDS 原則可能最有用： 
	- [裝置及資源重新導向](https://technet.microsoft.com/library/ee791794.aspx)
	- [印表機重新導向](https://technet.microsoft.com/library/ee791784.aspx)
	- [設定檔](https://technet.microsoft.com/library/ee791865.aspx)。
- 請注意，透過 RemoteApp PowerShell 模組設定重新導向 (如[這裡](./remoteapp-redirection.md)所示) 需要依賴用戶端機器強制執行原則，因此，如果安全性是主要目標，則您會想透過範本映像本機原則或群組原則來強制執行原則。
- [Windows Server 2012 R2 原則](https://technet.microsoft.com/library/hh831791.aspx)。
- [Office 2013 原則](https://technet.microsoft.com/library/cc178969.aspx) (包括[如何自訂 Office 工具列](https://technet.microsoft.com/library/cc179143.aspx))。
 

<!---HONumber=Oct15_HO3-->