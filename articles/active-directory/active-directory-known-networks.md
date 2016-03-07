<properties 
	pageTitle="已知網路 | Microsoft Azure" 
	description="藉由設定已知的網路，您可以避免貴組織擁有的 IP 位址包含在「從多個地理區域登入」和「從具有可疑活動的 IP 位址登入」報告中。" 
	services="active-directory" 
	documentationCenter="" 
	authors="markusvi" 
	manager="stevenpo"  
	editor=""/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/18/2016" 
	ms.author="markvi"/>

# 已知的網路


您可以使用 Azure Active Directory 的存取和使用情況報告來了解貴組織的目錄完整性和安全性。利用此資訊，目錄管理員更能夠判斷可能發生安全性風險的位置，以便適當地規劃來減輕這些風險。

「*從多個地理區域登入*」和「*從具有可疑活動的 IP 位址登入*」可能會不正確地報告貴組織實際擁有的旗標 IP 位址。

比方說，這可以發生在下列情形：

- 在波士頓辦公室的使用者已從遠端登入您在舊金山的資料中心，觸發了「從多個地理區域登入」的報告 

- 貴組織的使用者多次嘗試以不正確的密碼登入，觸發了「從具有可疑活動的 IP 位址登入」的報告

若要避免這些情況下產生誤導的安全性報告，您應該在貴組織的公用 IP 位址清單中新增已知的 IP 位址範圍。


###若要新增貴組織的公用 IP 位址範圍，請執行下列步驟： 

1.	登入 [Azure 管理入口網站](https://manage.windowsazure.com)。

2.	在左窗格中，按一下 [Active Directory]。<br><br>![Cloud App Discovery 的運作方式](./media/active-directory-known-networks/known-netwoks-01.png)

3.	在 [目錄] 索引標籤上，選取您的目錄。

4.	在頂端的功能表中，按一下 [組態]。<br><br>![Cloud App Discovery 的運作方式](./media/active-directory-known-networks/known-netwoks-02.png)

5.	在 [組態] 索引標籤上，移至 [貴組織公用 IP 位址範圍] <br><br>![Cloud App Discovery 的運作方式](./media/active-directory-known-networks/known-netwoks-03.png)

6.	按一下 [新增已知的 IP 位址範圍[。

7.	在出現的對話方塊中新增位址範圍，然後在完成時按一下核取按鈕。<br><br>![Cloud App Discovery 的運作方式](./media/active-directory-known-networks/known-netwoks-04.png)


**其他資源**


* [檢視存取和使用情況報告](active-directory-view-access-usage-reports.md)
* [從具有可疑活動的 IP 位址登入](active-directory-reporting-sign-ins-from-ip-addresses-with-suspicious-activity.md)
* [從多個地理區域登入](active-directory-reporting-sign-ins-from-multiple-geographies.md)

<!---HONumber=AcomDC_0224_2016-->