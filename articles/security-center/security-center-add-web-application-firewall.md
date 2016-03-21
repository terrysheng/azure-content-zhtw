<properties
   pageTitle="在 Azure 資訊安全中心新增 Web 應用程式防火牆 | Microsoft Azure"
   description="本文件說明如何實作 Azure 資訊安全中心建議的「新增 Web 應用程式防火牆」。"
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="StevenPo"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/02/2016"
   ms.author="terrylan"/>

# 在 Azure 資訊安全中心新增 Web 應用程式防火牆

Azure 資訊安全中心可能會建議您從 Microsoft 合作夥伴新增 Web 應用程式防火牆 (WAF)，以保護您 Web 應用程式的安全。本文件逐步解說如何進行這項操作的範例。

> [AZURE.NOTE] 本文件中的資訊適用於「Azure 資訊安全中心」的預覽版本。本文件將使用範例部署來介紹服務。這不是逐步指南。

## 實作建議

1. 在 [建議] 刀鋒視窗中，選取 [使用 Web 應用程式防火牆保護 Web 應用程式]。![][1]

2. 在 [使用 Web 應用程式防火牆保護 Web 應用程式] 刀鋒視窗中，選取 Web 應用程式。[新增 Web 應用程式防火牆] 刀鋒視窗隨即開啟。![][2]
3. 您可以選擇使用現有的 Web 應用程式防火牆 (如果有的話)，或者您可以建立一個新的 Web 應用程式防火牆。此範例中沒有任何可用的現有 WAF，因此我們將建立一個新的 WAF。

4. 若要建立新的 WAF，請從整合式合作夥伴的清單中選取一個解決方案。在此範例中，我們將選取 [Barracuda Web 應用程式防火牆]。
5. [Barracuda Web 應用程式防火牆] 刀鋒視窗隨即開啟，為您提供合作夥伴解決方案的相關資訊。選取資訊刀鋒視窗中的 [建立]。![][3]

6. 即會開啟 [新增 Web 應用程式防火牆] 刀鋒視窗，您可以在此視窗中執行 [VM 組態] 步驟並提供 [WAF 資訊]。選取 [VM 組態]。

7. 在 [VM 組態] 刀鋒視窗中，輸入啟動將執行 WAF 的虛擬機器所需的資訊。![][4]
8. 返回 [新增 Web 應用程式防火牆] 刀鋒視窗，然後選取 [WAF 資訊]。在 [WAF 資訊] 刀鋒視窗中，設定 WAF 本身。步驟 7 可讓您設定將執行 WAF 的虛擬機器，而步驟 8 則可讓您佈建 WAF 本身。

9. 返回 [建議] 刀鋒視窗。在您建立 WAF 之後會產生一個新項目，稱為 [完成 Web 應用程式防火牆設定]。此項目可讓您知道您需要完成實際串聯起 Azure 虛擬網路內 WAF 的程序，讓它可以保護應用程式。![][5]

10. 選取 [完成 Web 應用程式防火牆設定]。此時會開啟新的分頁。您會看到有一個需要重新路由流量的 Web 應用程式。
11. 選取 Web 應用程式。將會開啟一個刀鋒視窗，其中提供完成 Web 應用程式防火牆設定的步驟。完成這些步驟，然後選取 [限制流量]。接著，資訊安全中心會為您進行串聯。![][6]

> [AZURE.NOTE] 您可以將這些應用程式加入現有的 WAF 部署，以保護資訊安全中心的多個 Web 應用程式。WAF 應用裝置 (使用資源管理員部署模型建立) 需要部署至不同的虛擬網路。WAF 應用裝置 (使用傳統部署模型建立) 受限於只能使用網路安全性群組。這項支援在未來將會延伸至 WAF 應用裝置 (傳統) 的完全自訂部署。深入了解 Azure 資源的[傳統和資源管理員部署模型](../azure-classic-rm.md)。

現在已將來自該 WAF 的記錄完全整合。「資訊安全中心」可以開始自動收集並分析記錄，以便對您顯示重要的安全性警示。

## 後續步驟

本文件說明如何實作資訊安全中心建議的「新增 Web 應用程式」。 若要深入了解如何設定 Web 應用程式防火牆，請參閱下列各項：

- [設定 App Service 環境的 Web 應用程式防火牆 (WAF)](../app-service-web/app-service-app-service-environment-web-application-firewall.md)

如要深入了解資訊安全中心，請參閱下列主題：

- [在 Azure 資訊安全中心內設定安全性原則](security-center-policies.md)：了解如何為您的 Azure 訂用帳戶及資源群組設定安全性原則。
- [Azure 資訊安全中心的安全性健全狀況監視](security-center-monitoring.md) -- 了解如何監視 Azure 資源的健全狀況。
- [管理與回應 Azure 資訊安全中心的安全性警示](security-center-managing-and-responding-alerts.md) -- 了解如何管理與回應安全性警示。
- [管理 Azure 資訊安全中心的安全性建議](security-center-recommendations.md) -- 了解建議如何協助您保護您的 Azure 資源。
- [Azure 資訊安全中心常見問題集](security-center-faq.md) -- 尋找在使用服務時經常會遇到的問題。
- [Azure 安全性部落格](http://blogs.msdn.com/b/azuresecurity/) -- 尋找有關 Azure 安全性與相容性的部落格文章。

<!--Image references-->
[1]: ./media/security-center-add-web-application-firewall/secure-web-application.png
[2]: ./media/security-center-add-web-application-firewall/add-a-waf.png
[3]: ./media/security-center-add-web-application-firewall/info-blade.png
[4]: ./media/security-center-add-web-application-firewall/select-vm-config.png
[5]: ./media/security-center-add-web-application-firewall/finalize-waf.png
[6]: ./media/security-center-add-web-application-firewall/restrict-traffic.png

<!---HONumber=AcomDC_0309_2016-->