
<properties 
    pageTitle="疑難排解混合式集合 - 建立"
    description="了解如何疑難排解 RemoteApp 混合式集合建立失敗" 
    services="remoteapp" 
    solutions="" documentationCenter="" 
    authors="vkbucha" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="02/20/2015" 
    ms.author="vikbucha" />



# 疑難排解混合式集合 - 建立

您必須先了解建立混合式集合的方式，才能在混合式集合建立期間疑難排解失敗。混合式集合需要已加入網域的 RemoteApp 執行個體 - 您可以在集合建立期間執行這項操作。啟動集合建立程序時，系統會在 VNET 中建立您所上傳範本映像的複本，並透過站台對站台 VPN 通道，將其加入於 VNET 建立期間指定之 DNS IP 記錄所解析的網域。

Azure 管理入口網站中的常見錯誤：

	DNS server could not be reached

	Could not join the domain. Unable to reach the domain.

如果您看到上述其中一個錯誤，請檢查下列事項：

- 請確認 DNS IP 組態是否正確
- 請確定 DNS IP 記錄是公開 IP 記錄，或是屬於您於 VNET 建立期間指定的「本機位址空間」
- 請確認 VNET 通道，以確保它是否處於作用中或已連接狀態 
- 請確定 VPN 連線的內部部署端未封鎖網路流量。您可以透過查看本機 VPN 裝置或軟體的記錄檔來檢查此項目。
- 請確定您在集合建立期間所指定的網域已啟動並執行。

	ProvisioningTimeout


如果您看到此錯誤，請檢查下列事項：

- 請確定 VPN 連線的內部部署端未封鎖網路流量。您可以透過查看本機 VPN 裝置或軟體的記錄檔來檢查此項目。
- 請確定您所上傳的 RemoteApp 範本映像已正確地執行過 Sysprep。您可以在這裡檢查 RemoteApp 映像需求: http://azure.microsoft.com/documentation/articles/remoteapp-create-custom-image/ 
- 請嘗試使用您所上傳的範本映像來建立 VM，並確保它已開機並按下列任一需求正常執行 (a) 在本機的 Hyper-V 伺服器上 (b) 藉由在您的 Azure 訂閱上建立 Azure IAAS VM。如果無法建立或無法啟動 VM，這通常表示未正確地準備範本映像，而且您必須修正這個問題。

	DomainJoinFailed_InvalidUserNameOrPassword

	DomainJoinFailed_InvalidParameter

如果您看到上述其中一個錯誤，請檢查下列事項：

- 請確認所輸入的網域加入認證是否正確
- 請確認網域加入認證是否正確，或是否具備適當的網域加入權限
- 請確認組織單位 (OU) 的格式是否正確，並且存在於 Active directory 中。


<!--HONumber=52-->