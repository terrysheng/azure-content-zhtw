### 設定 VM 的網路安全性群組輸入規則

如果您想要透過網際網路連線到 SQL Server，您必須為 SQL Server 執行個體正在接聽的連接埠在網路安全性群組上設定輸入規則。根據預設，其為 TCP 連接埠 1433。

1. 在入口網站中，選取 [虛擬機器]，然後選取 SQL Server VM。

2. 按一下 [所有設定] 連結。

3. 展開 [網路介面]。

	![網路介面](./media/virtual-machines-sql-server-connection-steps/rm-network-interface.png)

4. 然後選取 VM 網路介面。

4. 按一下 [網路安全性群組] 連結。

	![網路介面](./media/virtual-machines-sql-server-connection-steps/rm-network-security-group.png)

6. 在網路安全性群組的屬性中，展開 [輸入安全性規則]。

5. 按一下 [新增] 按鈕。

6. 提供 SQLServerPublicTraffic 的 [名稱]。

7. 將 [通訊協定] 變更為 [TCP]。

8. 指定 [目的地連接埠範圍] 為 1433 (或您的 SQL Server 正在接聽的連接埠)。

9. 確認 [動作] 是設為 [允許]。安全性規則對話方塊看起來應該類似以下的螢幕擷取畫面。

	![網路安全性規則](./media/virtual-machines-sql-server-connection-steps/rm-network-security-rule.png)

9. 按一下 [確定] 以儲存 VM 的規則。

<!---HONumber=AcomDC_0107_2016-->