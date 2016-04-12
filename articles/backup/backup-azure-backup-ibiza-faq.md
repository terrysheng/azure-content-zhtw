<properties
   pageTitle="公開預覽版本的 Azure 備份常見問題集 | Microsoft Azure"
   description="此版本的常見問題集支援公開預覽版本的 Azure 備份服務。關於備份代理程式、備份和保留、復原、安全性，以及 Azure 備份解決方案其他常見問題的常見問題集答案。"
   services="backup"
   documentationCenter=""
   authors="markgalioto"
   manager="jwhit"
   editor=""
   keywords="備份解決方案；備份服務"/>

<tags
   ms.service="backup"
   ms.workload="storage-backup-recovery"
	 ms.tgt_pltfrm="na"
	 ms.devlang="na"
	 ms.topic="get-started-article"
	 ms.date="03/30/2016"
	 ms.author="trinadhk; markgal; jimpark;"/>

# 公開預覽版本的 Azure 備份服務 - 常見問題集

> [AZURE.SELECTOR]
- [傳統模式的備份常見問題集](backup-azure-backup-faq.md)
- [ARM 模式的備份常見問題集](backup-azure-backup-ibiza-faq.md)

本文提供 Azure 備份服務公開預覽版本的特定資訊。本文會在有新的常見問題時更新，並可補充 [Azure 備份常見問題集](backup-azure-backup-faq)。Azure 備份常見問題集提供一組關於 Azure 備份服務的完整問答。

您可以在本文件或相關文件的 Disqus 一節中詢問有關 Azure 備份的問題。您也可以在[論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup)中張貼有關 Azure 備份服務的問題。

## 公開預覽版本中有什麼？
公開預覽版本引進了在保護 Azure VM 時的復原服務保存庫和 ARM 支援。復原服務保存庫是新一代的保存庫。它是 Azure 備份和 Azure Site Recovery (ASR) 服務所使用的保存庫。您可以將它視為保存庫 2.0 版。

## 復原服務和備份保存庫

**Q1.復原服務保存庫是 2.0 版，那麼是否仍支援備份保存庫 (1.0 版)？** <br/> A1.是，仍然支援備份保存庫。在傳統入口網站中建立備份保存庫。在 Azure 入口網站中建立復原服務保存庫。

**Q2.是否可以將備份保存庫移轉至復原服務保存庫？** <br/> A2.很遺憾，不行，目前無法將備份保存庫的內容移轉至復原服務保存庫。我們正著手新增這項功能，但公開預覽中並未提供。

**Q3.復原服務保存庫支援 1.0 還是 2.0 版 VM？** <br/> A3.復原服務保存庫支援 1.0 和 2.0 版 VM。您可以將在傳統入口網站建立的 VM (這是 1.0 版) 或在 Azure 入口網站建立的 VM (這是 2.0 版) 備份至復原服務保存庫。


## Azure VM 的 ARM 支援

**Q1.Azure VM 的 ARM 支援是否有任何限制？** <br/> A1.目前並未提供適用於 ARM 的 PowerShell Cmdlet。您必須使用 Azure 入口網站 UI 將資源加入至資源群組。

<!---HONumber=AcomDC_0406_2016-->