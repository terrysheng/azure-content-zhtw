<properties
	pageTitle="Azure Active Directory 網域服務預覽：開始使用 | Microsoft Azure"
	description="開始使用 Azure Active Directory 網域服務"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="udayh"
	editor="inhenk"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/12/2015"
	ms.author="maheshu"/>

# Azure AD 網域服務 (預覽) - 開始使用

## 步驟 3：啟用 Azure AD 網域服務
在此步驟中，您可以針對目錄啟用 Azure AD 網域服務。執行下列組態步驟，針對您的目錄啟用 Azure AD 網域服務。

1. 登入 **Azure 管理入口網站** ([https://manage.windowsazure.com](https://manage.windowsazure.com))。
2. 在左窗格中，選取 [Active Directory] 節點。
3. 選取您要啟用 Azure AD 網域服務的 Azure AD 租用戶 (目錄)。

    ![選取 Azure AD 目錄](./media/active-directory-domain-services-getting-started/select-aad-directory.png)

4. 按一下 [設定] 索引標籤。

    ![設定目錄的索引標籤](./media/active-directory-domain-services-getting-started/configure-tab.png)

5. 向下捲動到標題為**網域服務**的區段。

    ![網域服務組態區段](./media/active-directory-domain-services-getting-started/domain-services-configuration.png)

6. 將標題為**啟用此目錄的網域服務**的選項切換至 [是]。您將會注意到頁面上出現了更多適用於 Azure AD 網域服務的組態選項。

    ![啟用網域服務](./media/active-directory-domain-services-getting-started/enable-domain-services.png)

    > [AZURE.NOTE]當您針對租用戶啟用 Azure AD 網域服務時，Azure AD 將會產生並儲存驗證使用者所需的 Kerberos 和 NTLM 認證雜湊。

7. 指定**網域服務的 DNS 網域名稱**。
   - 根據預設，將會選取目錄的預設網域名稱 (亦即以 **.onmicrosoft.com** 網域尾碼為結束)。
   - 下拉式清單會列出所有已針對 Azure AD 目錄設定的網域 – 包括您在 [網域] 索引標籤中設定的已驗證以及未驗證的網域。
   - 此外，您也可以在這個可編輯的下拉式清單中，藉由輸入網域名稱來指定自訂網域名稱。

8. 下一個步驟是選取您想要在其中使用 Azure AD 網域服務的虛擬網路。在標題為**將網域服務連接到此虛擬網路**的下拉式清單中，選取您剛建立的虛擬網路。
9. 完成選取上述選項時，請按一下頁面底部之工作窗格中的 [儲存]，來啟用 Azure AD 網域服務。
10. 針對您的目錄啟用 Azure AD 網域服務時，頁面將會顯示 [擱置中...] 狀態。

    ![啟用網域服務 - 擱置中狀態](./media/active-directory-domain-services-getting-started/enable-domain-services-pendingstate.png)

    > [AZURE.NOTE]Azure AD 網域服務可針對您的受管理網域提供高可用性。當您第一次針對網域啟用 Azure AD 網域服務時，您會注意到可在虛擬網路上使用網域服務的 IP 位址會一個接著一個顯示。一旦服務針對您的網域啟用高可用性之後，第二個 IP 位址將會立即顯示。在針對您的網域設定高可用性並使其成為作用中狀態時，您應該會在 [設定] 索引標籤的 [網域服務] 區段中看到兩個 IP 位址。

11. 大約 20-30 分鐘後，您會在 [設定] 頁面的 [IP 位址] 欄位中看見可在您虛擬網路上使用網域服務的第一個 IP 位址。

    ![網域服務已啟用 - 已佈建第一個 IP](./media/active-directory-domain-services-getting-started/domain-services-enabled-firstdc-available.png)

12. 若高可用性會針對您的網域進行運作，則您將會在頁面上看見兩個 IP 位址。這些都是可在您所選虛擬網路上使用 Azure AD 網域服務的 IP 位址。記下這些 IP 位址，以更新您虛擬網路的 DNS 設定。這個步驟會讓虛擬網路上的虛擬機器連線到網域，以進行像是加入網域等作業。

    ![網域服務已啟用 - 已佈建兩個 IP](./media/active-directory-domain-services-getting-started/domain-services-enabled-bothdcs-available.png)

  >[AZURE.NOTE]根據您的 Azure AD 目錄大小而定 (使用者的數目、群組等)，它需要一些時間，才能在 Azure AD 網域服務中取得目錄的內容。這個同步處理程序會在背景執行。對於具有成千上萬個物件的大型目錄，可能需要一天或兩天的時間，在 Azure AD 網域服務中同步處理所有使用者、群組成員資格和認證並加以使用。


---
[**下一個步驟 - 更新 Azure 虛擬網路的 DNS 設定。**](active-directory-ds-getting-started-dns.md)

<!---HONumber=Oct15_HO3-->