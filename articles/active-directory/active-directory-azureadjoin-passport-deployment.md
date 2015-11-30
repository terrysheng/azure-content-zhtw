<properties 
	pageTitle="在組織中啟用 Microsoft Passport for Work | Microsoft Azure" 
	description="在組織中啟用 Microsoft Passport 的部署指示。" 
	services="active-directory" 
	documentationCenter="" 
	authors="femila" 
	manager="stevenpo" 
	editor=""
	tags="azure-classic-portal"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/17/2015" 
	ms.author="femila"/>

# 啟用 Microsoft Passport 並在組織中運用

將加入 Windows 10 網域的裝置與 Azure AD 連接後，請依下列方式在組織中啟用 Microsoft Passport for Work。

## 部署 System Center Configuration Manager 1509 版 (Technical Preview)。
如果要根據 Microsoft Passport 金鑰部署使用者憑證，您需要下列項目：

- **System Center Configuration Manager 1509 版 (Technical Preview)**。如需詳細資訊，請參閱 [Microsoft System Center Configuration Manager Technical Preview](https://technet.microsoft.com/library/dn965439.aspx#BKMK_TP3Update) 和 [System Center Configuration Manager 小組部落格](http://blogs.technet.com/b/configmgrteam/archive/2015/09/23/now-available-update-for-system-center-config-manager-tp3.aspx)。
- **PKI 基礎結構**：若要使用使用者憑證啟用 Microsoft Passport for Work，您必須備妥 PKI 基礎結構。如果您沒有或不想為了使用者憑證而使用它，您可以部署新版 Windows Server 的網域控制站 (DC)：
 - **部署新版的 Windows Server 的 DC**：在全新的 Windows Server 組建 10551 或更新版本上 (ISO 可至 [Signiant 媒體交換](https://datatransfer.microsoft.com/signiant_media_exchange/spring/main?sdkAccessible=true)下載)，遵循步驟以[在現有網域中安裝複本 DC](https://technet.microsoft.com/zh-TW/library/jj574134.aspx) 或[如果要建立全新的環境，請安裝新的 Active Directory 樹系](https://technet.microsoft.com/zh-TW/library/jj574134.aspx)。

## 透過 Active Directory 中的群組原則設定 Microsoft Passport for Work

 您可以使用 Active Directory 群組原則，設定加入 Windows 10 網域的裝置在使用者登入 Windows 時佈建使用者 Microsoft Passport 認證：

1. 	開啟 [伺服器管理員] 並瀏覽至 [工具] > [群組原則管理]。
2.	從 [群組原則管理]，瀏覽至與您想要啟用 [加入 Azure AD] 的網域相對應的網域節點。
3.	以滑鼠右鍵按一下 [群組原則物件]，選取 [新增]。指定群組原則物件的名稱，例如「啟用 Microsoft Passport」。按一下 [確定]。
4.	以滑鼠右鍵按一下新的群組原則物件，然後選取 [編輯]。
5.	瀏覽至 [電腦設定] > [原則] > [系統管理範本] > [Windows 元件] > [Passport for Work]。
6.	以滑鼠右鍵按一下 [啟用 Passport for Work]，然後選取 [編輯]。
7.	選取 [啟用] 選項按鈕，然後按一下 [套用]。按一下 [確定]。
8.	您現在可以將群組原則物件連結到您所選擇的位置。若要對組織中所有加入網域的 Windows 10 裝置啟用此原則，請將群組原則連結至網域。例如：
 - AD 中將放置加入網域的 Windows 10 電腦的特定組織單位 (OU)。
 - 加入網域而會向 Azure AD 自動註冊的 Windows 10 電腦所屬的特定安全性群組。

## 透過 Configuration Manager 部署 PowerShell 以設定 Microsoft Passport for Work 

執行下列 PowerShell 命令：

    powershell.exe -ExecutionPolicy Bypass -NoLogo -NoProfile -Command "& {New-ItemProperty "HKLM:\Software\Policies\Microsoft\PassportForWork" -Name "Enabled" -Value 1 -PropertyType "DWord" -Force}"

## 設定憑證設定檔以在 Configuration Manager 中註冊 "Passport for Work" 註冊憑證
若要使用 Passport for Work 憑證式登入/Microsoft Hello，請設定憑證設定檔 ([資產和相容性] -> [相容性設定] -> [公司資源存取權] -> [憑證設定檔])，選取一個含有智慧卡登入 EKU 的範本。

##設定一個在 Passport for Work 容器啟用時觸發並要求評估憑證的排定工作。
這是一項短期修正，系統管理員必須建立排定的工作來接聽 Passport for Work 容器的建立和要求評估憑證。這會減少設定容器和 PIN 的延遲，並且能夠在下次登入時使用。

**若要建立排定的工作，請使用下列命令或使用 UI** schtasks /create /xml %0..< EnrollCertificate.xml > /tn <Task Name>

範例 XML 如下：

    <?xml version="1.0" encoding="UTF-16"?>
    <Task version="1.2" xmlns="http://schemas.microsoft.com/windows/2004/02/mit/task">
       <RegistrationInfo>
         <Date>2015-09-04T17:48:45.9973761</Date>
          <Author><DomainName/UserName></Author>
        <URI>\Enroll Certificates</URI>
      </RegistrationInfo>
      <Triggers>
        <EventTrigger>
          <Enabled>true</Enabled>
          <Subscription>&lt;QueryList&gt;&lt;Query Id="0" Path="Microsoft-Windows-User Device Registration/Admin"&gt;&lt;Select Path="Microsoft-Windows-User Device Registration/Admin"&gt;*[System[Provider[@Name='Microsoft-Windows-User Device Registration'] and EventID=300]]&lt;/Select&gt;&lt;/Query&gt;&lt;/QueryList&gt;</Subscription>
        </EventTrigger>
      </Triggers>
      <Principals>
      </Principals>
      <Settings>
        <MultipleInstancesPolicy>IgnoreNew</MultipleInstancesPolicy>
        <DisallowStartIfOnBatteries>false</DisallowStartIfOnBatteries>
        <StopIfGoingOnBatteries>true</StopIfGoingOnBatteries>
        <AllowHardTerminate>true</AllowHardTerminate>
        <StartWhenAvailable>true</StartWhenAvailable>
        <RunOnlyIfNetworkAvailable>false</RunOnlyIfNetworkAvailable>
        <IdleSettings>
          <StopOnIdleEnd>true</StopOnIdleEnd>
          <RestartOnIdle>false</RestartOnIdle>
        </IdleSettings>
        <AllowStartOnDemand>true</AllowStartOnDemand>
        <Enabled>true</Enabled>
        <Hidden>false</Hidden>
        <RunOnlyIfIdle>false</RunOnlyIfIdle>
        <WakeToRun>false</WakeToRun>
        <ExecutionTimeLimit>PT1H</ExecutionTimeLimit>
        <Priority>7</Priority>
        <RestartOnFailure>
          <Interval>PT1M</Interval>
          <Count>3</Count>
        </RestartOnFailure>
      </Settings>
      <Actions Context="Author">
        <Exec>
          <Command>wmic</Command>
          <Arguments>/namespace:\\root\ccm\dcm path SMS_DesiredConfiguration call TriggerEvaluation 1,0,"ScopeId_73F3BB5E-5EDC-4928-87BD-4E75EB4BBC34/ConfigurationPolicy_db89c51e-1d1b-4a17-8e42-a5459b742ccd",8</Arguments>
        </Exec>
      </Actions>
    </Task>

其中 ScopeId\_73F3BB5E-5EDC-4928-87BD-4E75EB4BBC34/ConfigurationPolicy\_db89c51e-1d1b-4a17-8e42-a5459b742ccd 是在 [設定憑證設定檔以在 Configuration Manager 中註冊 "Passport for Work" 註冊憑證] 步驟中建立的憑證設定檔的識別碼，<8> 是版本。

## 其他資訊
* [適合企業使用的 Windows 10：使用裝置工作的方式](active-directory-azureadjoin-windows10-devices-overview.md)
* [透過 Azure Active Directory Join 擴充 Windows 10 裝置的雲端功能](active-directory-azureadjoin-user-upgrade.md)
* [透過 Microsoft Passport 不需要密碼就能驗證身分識別](active-directory-azureadjoin-passport.md)
* [了解適用於 Azure AD Join 的使用案例](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [將已加入網域裝置連接到 Azure AD 以體驗 Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [設定 Azure AD Join](active-directory-azureadjoin-setup.md)

<!---HONumber=Nov15_HO4-->