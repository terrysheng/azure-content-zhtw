<properties
   pageTitle="從 Azure 將資料還原到 Windows Server 或 Windows 用戶端 | Microsoft Azure"
   description="了解如何從 Windows Server 或 Windows 用戶端進行還原。"
   services="backup"
   documentationCenter=""
   authors="aashishr"
   manager="jwhit"
   editor=""/>

<tags ms.service="backup" ms.workload="storage-backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/13/2015" ms.author="jimpark"; "aashishr"/>

# 將檔案還原到 Windows Server 或 Windows 用戶端電腦
本文涵蓋執行兩種類型還原作業所需的步驟：

- 將資料還原到進行備份的相同電腦。
- 將資料還原到任何其他電腦。

這兩種情況之下，都會從 Azure 備份保存庫擷取資料。

## 將資料還原到相同電腦
如果您不小心刪除檔案，而您想要將它還原到相同的電腦 (備份進行處)，下列步驟可協助您復原資料。

1. 開啟**Microsoft Azure 備份**嵌入式管理單元。
2. 按一下 [復原資料] 初始化工作流程。

    ![復原資料](./media/backup-azure-restore-windows-server/recover.png)

3. 選取 [這台伺服器 (*yourmachinename*)] 選項來於同一部電腦上還原備份檔案。

    ![相同電腦](./media/backup-azure-restore-windows-server/samemachine.png)

4. 選擇 [瀏覽檔案] 或 [搜尋檔案]。

    如果您打算還原路徑已知的一個或多個檔案，請保留預設選項。如果您不確定資料夾結構，但想要搜尋檔案，請挑選 [**搜尋檔案**] 選項。為了達成本章節的目的，我們將使用預設選項繼續進行。

    ![瀏覽檔案](./media/backup-azure-restore-windows-server/browseandsearch.png)

5. 選取您要還原檔案所在的磁碟區。

    您可以從任何時間點還原。日曆控制項中以**粗體**顯示的日期會指出還原點的可用性。選取日期之後，您可以根據您的備份排程 (以及成功的備份作業)，從 [時間] 下拉式清單選取時間點。

    ![磁碟區和日期](./media/backup-azure-restore-windows-server/volanddate.png)

6. 選取要復原的項目。您可以複選想要還原的資料夾/檔案。

    ![選取檔案](./media/backup-azure-restore-windows-server/selectfiles.png)

7. 指定復原參數。

    ![修復選項](./media/backup-azure-restore-windows-server/recoveroptions.png)

  - 您可以選擇還原至原始位置 (其中的檔案/資料夾可能會遭到覆寫) 或相同電腦中的其他位置。
  - 如果目標位置有您想要還原的檔案/資料夾存在，您可以建立複本 (相同檔案的兩個版本)、覆寫目標位置中的檔案，或略過修復目標位置已存在的檔案。
  - 強烈建議您針對正在復原檔案上的 ACL 保留預設還原選項。

8. 提供這些輸入之後，按一下 [下一步]。就會開始執行將檔案還原到這部電腦的復原工作流程。

## 還原至其他電腦
若您遺失整個伺服器，您仍然可從 Azure 備份將資料還原到其他電腦。下列步驟說明工作流程。

這些步驟中所使用的術語包含：

- 「來源電腦」 – 用來進行備份且目前無法使用的的原始電腦。
- 「目標電腦」 – 復原資料時的目標電腦。
- *範例保存庫* – *來源電腦*和*目標電腦*註冊的備份保存庫。<br/>

> [AZURE.NOTE]從電腦進行的備份無法在執行舊版作業系統的電腦上進行還原。例如，如果從 Windows 7 電腦進行備份，則可以在 Windows 8 或更新版電腦上進行還原。不過若情況相反，便無法進行還原。

1. 在「目標電腦」上開啟 [Microsoft Azure 備份] 嵌入式管理單元。
2. 確定「目標電腦」和「來源電腦」均已註冊到相同的備份保存庫。
3. 按一下 [復原資料] 初始化工作流程。

    ![復原資料](./media/backup-azure-restore-windows-server/recover.png)

4. 選取 [其他伺服器]

    ![其他伺服器](./media/backup-azure-restore-windows-server/anotherserver.png)

5. 提供與「範例保存庫」相對應的保存庫認證檔。如果保存庫認證檔無效 (或已過期)，請從 Azure 入口網站中的「範例保存庫」下載新的保存庫認證檔。一旦提供保存庫認證檔，即會顯示保存庫認證檔的備份保存庫。

6. 在顯示電腦的清單中選取 [來源電腦]。

    ![電腦清單](./media/backup-azure-restore-windows-server/machinelist.png)

7. 選取 [搜尋檔案] 或 [瀏覽檔案] 選項。為了達成本章節的目的，我們將使用 [搜尋檔案] 選項。

    ![Search](./media/backup-azure-restore-windows-server/search.png)

8. 在下一個畫面中選取磁碟區和日期。搜尋您想要還原的資料夾/檔案名稱。

    ![搜尋項目](./media/backup-azure-restore-windows-server/searchitems.png)

9. 選取需要還原之檔案的位置。

    ![還原位置](./media/backup-azure-restore-windows-server/restorelocation.png)

10. 提供「來源電腦」註冊至「範例保存庫」期間所提供的加密複雜密碼。

    ![加密](./media/backup-azure-restore-windows-server/encryption.png)

11. 一旦提供輸入，則按一下 [復原]，將會觸發還原備份檔案到所提供目的地的程序。

## 後續步驟
- [Azure 備份常見問題集](backup-azure-backup-faq.md)
- 造訪 [Azure 備份論壇](http://go.microsoft.com/fwlink/p/?LinkId=290933) (英文)。

## 詳細資訊
- [Azure 備份概觀](http://go.microsoft.com/fwlink/p/?LinkId=222425)
- [備份 Azure 虛擬機器](backup-azure-vms-introduction.md)
- [備份 Microsoft 工作負載](backup-azure-dpm-introduction.md)

<!---HONumber=Oct15_HO3-->