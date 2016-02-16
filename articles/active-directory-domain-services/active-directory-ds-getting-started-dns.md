<properties pageTitle="Azure Active Directory 網域服務預覽：開始使用 | Microsoft Azure" description="開始使用 Azure Active Directory Domain Services" services="active-directory-ds" documentationCenter="" authors="mahesh-unnikrishnan" manager="stevenpo editor="curtand"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="01/26/2016"
	ms.author="maheshu"/>

# Azure AD 網域服務 (預覽) - 開始使用

## 步驟 4 - 更新 Azure 虛擬網路的 DNS 設定
現在您已成功啟用目錄的 Azure AD 網域服務，下一個步驟就是確保虛擬網路內的電腦可以連接並取用這些服務。若要這樣做，您必須更新虛擬網路的 DNS 伺服器設定，以指向虛擬網路上可以使用 Azure AD 網域服務的 IP 位址。

> [AZURE.NOTE] 在您已啟用目錄的 Azure AD 網域服務之後，請記下在您目錄的 [設定] 索引標籤上顯示之 Azure AD 網域服務的 IP 位址。

執行下列組態步驟，以便為已啟用 Azure AD 網域服務的虛擬網路更新 DNS 伺服器設定。

1. 瀏覽至 **Azure 管理入口網站** ([https://manage.windowsazure.com](https://manage.windowsazure.com))。
2. 在左窗格上選取 [網路] 節點。

    ![虛擬網路節點](./media/active-directory-domain-services-getting-started/virtual-network-select.png)

3. 在 [虛擬網路] 索引標籤中，選取已啟用 Azure AD 網域服務的虛擬網路，以檢視其內容。
4. 按一下 [設定] 索引標籤。

    ![虛擬網路節點](./media/active-directory-domain-services-getting-started/virtual-network-configure-tab.png)

5. 在 [DNS 伺服器] 區段中，輸入 Azure AD 網域服務的 IP 位址。
6. 確定您同時輸入已在 [網域服務] 區段 (位於您目錄的 [設定] 索引標籤上) 中顯示的 IP 位址。
7. 按一下頁面底部的工作窗格上的 [儲存]，以便儲存此虛擬網路的 DNS 伺服器設定。

   ![更新虛擬網路的 DNS 伺服器設定。](./media/active-directory-domain-services-getting-started/update-dns.png)

> [AZURE.NOTE] 在更新虛擬網路的 DNS 伺服器設定之後，可能需要一段時間，網路上的虛擬機器才能取得已更新的 DNS 組態。如果虛擬機器無法連線到網域，您可以清除虛擬機器上的 DNS 快取 (例如，ipconfig /flushdns)，以便強制重新整理虛擬機器上的 DNS 設定。

---
[**下一步 - 啟用 Azure AD 網域服務的密碼同步處理。**](active-directory-ds-getting-started-password-sync.md)

<!---HONumber=AcomDC_0211_2016-->