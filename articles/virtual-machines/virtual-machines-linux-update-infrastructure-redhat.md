<properties
   pageTitle="適用於 Red Hat Enterprise Linux 映像的更新基礎結構 | Microsoft Azure"
   description="介紹適用於 Azure 中隨選 Red Hat Enterprise Linux 執行個體的 yum 更新服務"
   services="virtual-machines"
   documentationCenter=""
   authors="KylieLiang"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="01/13/2016"
   ms.author="kyliel"/>

# 適用於 Red Hat Enterprise Linux 映像的更新基礎結構

在 Azure 中使用 Red Hat Update Infrastructure (RHUI)，便可以管理 Azure Red Hat Enterprise Linux (RHEL) 的 yum 儲存機制內容。您從 Azure Marketplace 建立的隨選 RHEL 執行個體就能存取區域的 yum 儲存機制。這樣可讓 RHEL 執行個體接收累加式更新。

RHUI 所管理的 yum 儲存機至清單，會於佈建期間設定在您的 RHEL 執行個體中。因此您不需要進行任何額外設定，只需在 RHEL 執行個體開始執行之後執行 `yum update` 即可。

## RHUI 概觀
[Red Hat Update Infrastructure](https://access.redhat.com/products/red-hat-update-infrastructure) 提供調整性高的解決方案，可以針對裝載在 Red Hat 認證雲端提供者上的 Red Hat Enterprise Linux 雲端執行個體，管理 yum 儲存機制內容。根據上游 Pulp 專案，RHUI 可讓雲端提供者在本機建立 Red Hat 所裝載儲存機制內容的鏡像、以其內容建立自定儲存機制，並透過具流量負載平衡的內容傳遞系統，讓儲存機制可供大量使用者使用。

## 部署 RHUI 的區域
RHUI 部署在 [Azure 狀態儀表板](https://azure.microsoft.com/status/)中列出的所有公用區域。這表示在這些區域您可以取得 yum 更新服務而不需要任何額外費用。這些資訊未來將會更新。

## 從內部部署更新儲存機制 (如 Red Hat Network Satellite) 取得更新。

若要從內部部署儲存機制取得更新，您需要有 Red Hat Cloud Access 授權，以及現有的 Azure Red Hat 訂用帳戶。

接下來，您需要取消註冊 RHUI並向您的內部部署更新基礎結構註冊。以下是詳細的步驟。

1.	編輯 /etc/yum.repos.d/rh-cloud.repo 並將所有 `enabled=1` 變更為 `enabled=0`。例如：

        # sed -i 's/enabled=1/enabled=0/g' /etc/yum.repos.d/rh-cloud.repo

2.	編輯 /etc/yum/pluginconf.d/rhnplugin.conf 並將 `enabled=0` 變更為 `enabled=1`。
3.	接下來，註冊 Red Hat Network (RHN)。

        rhn_register

    或

        rhnreg_ks


> [AZURE.NOTE] 會針對傳出的資料收費 (請參閱[定價詳細資料](http://azure.microsoft.com/pricing/details/data-transfers/))。我們建議使用預設的 RHUI 來取得累加式更新，而不會產生額外費用。

## 後續步驟
既然您已了解 Azure 中的 RHUI，即可從 [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/redhat/) 建立 RHEL 映像並在您的 RHEL 執行個體中使用 `yum update`。

<!---HONumber=AcomDC_0224_2016-->