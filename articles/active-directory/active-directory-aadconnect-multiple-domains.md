<properties
	pageTitle="Azure AD Connect 多個網域"
	description="本文件說明如何使用 O365 與 Azure AD 安裝及設定多個最上層網域。"
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/14/2016"
	ms.author="billmath"/>

# 


## 




>[AZURE.NOTE]



![](./media/active-directory-multiple-domains/MsolDomainFederationSettings.png)



![網域](./media/active-directory-multiple-domains/domains.png)


  

![](./media/active-directory-multiple-domains/error.png)

### 


	
- `New-MsolFederatedDomain`
- `Convert-MsolDomaintoFederated`
- `Update-MsolFederatedDomain`



![](./media/active-directory-multiple-domains/convert.png)



![](./media/active-directory-multiple-domains/settings.png)







這會比對 Azure AD 組態，且驗證將會成功。



    c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type =   "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)", "http://${domain}/adfs/services/trust/"));


>[AZURE.IMPORTANT]


## 




![](./media/active-directory-multiple-domains/trust1.png)



![](./media/active-directory-multiple-domains/trust2.png)



![](./media/active-directory-multiple-domains/trust3.png)





2.   
2.  
3.  
1.    
2.  
2.  
4.  




1.    
2.  
2.  
3.  



1.	
2.	
3.	
4.	
5.	


### 


![](./media/active-directory-multiple-domains/MsolDomainFederationSettings.png)



![](./media/active-directory-multiple-domains/settings2.png)


##




### 




    c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, "^((.*)([.|@]))?(?<domain>[^.]*[.].*)$", "http://${domain}/adfs/services/trust/"));



1.	
2.	
3.	
4.	
    
	    c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)","http://${domain}/adfs/services/trust/"));
    	
	取代為
    
	    `c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, "^((.*)([.|@]))?(?<domain>[^.]*[.].*)$", "http://${domain}/adfs/services/trust/"));`
	
![](./media/active-directory-multiple-domains/sub2.png)
5.	按一下 [確定]。按一下 [確定]。關閉 [AD FS 管理]。

<!---HONumber=AcomDC_0316_2016-->