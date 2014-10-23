<properties linkid="develop-java-how-to-guides-web-sso" urlDisplayName="Web SSO" pageTitle="Single sign-on with Azure Active Directory (Java)" metaKeywords="Azure Java web app, Azure single sign-on, Azure Java Active Directory" description="Learn how to create a Java web application that uses single sign-on with Azure Active Directory." metaCanonical="" services="active-directory" documentationCenter="Java" title="Web Single Sign-On with Java and Azure Active Directory" authors="mbaldwin" solutions="" manager="mbaldwin" editor="" />

<tags ms.service="active-directory" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="mbaldwin"></tags>

# 利用 Java 和 Azure Active Directory 進行 Web 單一登入

## <a name="introduction"></a>簡介

本教學課程將對 Java 開發人員顯示如何運用 Azure Active Directory，對 Office 365 客戶的使用者啟用單一登入。您將了解如何：

-   在客戶的租用戶中佈建 Web 應用程式
-   使用 WS-同盟保護應用程式

### 必要條件

本教學課程使用特定應用程式伺服器，但是如果您是資深 Java 開發人員，下面描述的程序也可以套用至其他環境。本教學課程需要下列開發環境必要條件：

-   [Java Sample Code for Azure Active Directory][]
-   [Java Runtime Environment 1.6][]
-   [JBoss Application Server version 7.1.1.Final][]
-   [JBoss Developer Studio IDE][]
-   Internet Information Services (IIS) 7.5 (已啟用 SSL)
-   Windows PowerShell
-   [Office 365 PowerShell Commandlets][]

### 目錄

-   [簡介][]
-   [步驟 1：建立 Java 應用程式][]
-   [步驟 2：在公司的目錄租用戶中佈建應用程式][]
-   [步驟 3：使用 WS-同盟進行員工登入來保護應用程式][]
-   [摘要][]

## <a name="createapp"></a>步驟 1：建立 Java 應用程式

此步驟描述如何建立將代表受保護資源的簡單 Java 應用程式。將透過公司 STS 管理的結盟驗證，授與資源此的存取權，稍後在教學課程會有所描述。

1.  開啟新的 JBoss Developer Studio 執行個體。
2.  從 [檔案] 功能表中，按一下 [新增]，然後按一下 [專案...]。
3.  在 [新增專案] 對話方塊上，展開 [Maven] 資料夾、按一下 [Maven Project]，然後按 [下一步]。
4.  在 [New Maven Project] 對話方塊中，核取 [Create a simple project (skip archetype selection)]，然後按 [下一步]。
5.  在 [New Maven Project] 對話方塊的下一個頁面上，於 [群組 ID] 和 [成品 ID] 文字方塊中輸入 *sample*。然後，從 [包裝] 下拉式功能表中選取 **war**，然後按一下 [完成]。
6.  將建立新的 Maven 專案。在左邊的 [專案總管] 功能表中，展開 **sample** 專案、在 **pom.xml** 檔案上按一下滑鼠右鍵、按一下 [開啟檔案]，然後按一下 [文字編輯器]。
7.  在 **pom.xml** 檔案中，於 *\<project\>* 區段內新增下列 XML：

        <repositories>
            <repository>
                <id>jboss-public-repository-group</id>
                <name>JBoss Public Maven Repository Group</name>
                <url>https://repository.jboss.org/nexus/content/groups/public-jboss/</url>
                <layout>default</layout>
                <releases>
                    <enabled>true</enabled>
                    <updatePolicy>never</updatePolicy>
                </releases>
                <snapshots>
                    <enabled>true</enabled>
                    <updatePolicy>never</updatePolicy>
                </snapshots>
            </repository>
        </repositories>
        <pluginRepositories>
            <pluginRepository>
                <id>jboss-public-repository-group</id>
                <name>JBoss Public Maven Repository Group</name>
                <url>https://repository.jboss.org/nexus/content/groups/public-jboss/</url>
                <layout>default</layout>
                <releases>
                    <enabled>true</enabled>
                    <updatePolicy>always</updatePolicy>
                </releases>
                <snapshots>
                    <enabled>true</enabled>
                    <updatePolicy>always</updatePolicy>
                </snapshots>
            </pluginRepository>
        </pluginRepositories>
        <build>
            <plugins>
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-compiler-plugin</artifactId>
                    <version>2.0.2</version>
                    <configuration>
                        <source>1.6</source>
                        <target>1.6</target>
                    </configuration>
                </plugin>
            </plugins>
        </build> 

    在輸入了此 XML 後，請儲存 **pom.xml** 檔案。

8.  在 **sample** 專案上按一下滑鼠右鍵，並按一下 [Maven]，然後按一下 [Update Maven Project]。在 [Update Maven Project] 對話方塊中，按一下 [確定]。此步驟將利用 **pom.xml** 變更來更新您的專案。

9.  在 **sample** 專案上按一下滑鼠右鍵，按一下 [新增]，然後按一下 [JSP File]。

10. 在 [New JSP File] 對話方塊上，將新檔案的路徑變更為 *sample/src/main/webapp*。然後，將檔案命名為 **index.jsp**，然後按一下 [完成]。

11. 新的 **index.jsp** 檔案將自動開啟。將自動產生的程式碼取代為下列程式碼，然後儲存檔案：

        <%@ page language="java" contentType="text/html; charset=ISO-8859-1"  pageEncoding="ISO-8859-1"%>
        <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
        <html>
        <head>
            <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
            <title>Index</title>
        </head>
        <body>
            <h3>Index Page</h3>
        </body>
        </html> 

12. 在 **sample** 專案上按一下滑鼠右鍵，並按一下 [Run As]，然後按一下 [Run on Server]。

13. 在 [Run on Server] 對話方塊上，確定已選取 **JBoss Enterprise Application Platform 6.x**，然後按一下 [完成]。

## <a name="provisionapp"></a>步驟 2：在公司的目錄租用戶中佈建應用程式

此步驟描述 Azure Active Directory 客戶的系統管理員如何在其租用戶中佈建 Java 應用程式，並設定單一登入。在完成此步驟之後，公司的員工可以使用其 Office 365 帳戶進行 Web 應用程式驗證。

佈建程序開始先為應用程式建立新的服務主體。服務主體是由 Azure Active Directory 用來向目錄註冊並驗證應用程式。

1.  下載並安裝 Office 365 PowerShell Commandlets (如果尚未這樣做)。
2.  從 [開始] 功能表中，執行 [適用於 Windows PowerShell 的 Microsoft Online Services 模組] 主控台。此主控台提供命令行環境，用於設定 Office 365 租用戶的相關屬性，例如建立和修改服務主體。
3.  若要匯入必要的 **MSOnlineExtended** 模組，請輸入下列命令並按 Enter 鍵：

        Import-Module MSOnlineExtended -Force

4.  若要連接至 Office 365 目錄，您將需要提供公司的系統管理員認證。輸入下列命令並按 Enter 鍵，然後在提示中輸入您的認證：

        Connect-MsolService

5.  現在您將為應用程式建立新的服務主體。輸入下列命令並按 Enter 鍵：

        New-MsolServicePrincipal -ServicePrincipalNames @("javaSample/localhost") -DisplayName "Federation Sample Website" -Type Symmetric -Usage Verify -StartDate "12/01/2012" -EndDate "12/01/2013" 

    此步驟將輸出如下的資訊：

        The following symmetric key was created as one was not supplied qY+Drf20Zz+A4t2w e3PebCopoCugO76My+JMVsqNBFc=
        DisplayName           : Federation Sample Java Website
        ServicePrincipalNames : {javaSample/localhost}
        ObjectId              : 59cab09a-3f5d-4e86-999c-2e69f682d90d
        AppPrincipalId        : 7829c758-2bef-43df-a685-717089474505
        TrustedForDelegation  : False
        AccountEnabled        : True
        KeyType               : Symmetric
        KeyId                 : f1735cbe-aa46-421b-8a1c-03b8f9bb3565
        StartDate             : 12/01/2012 08:00:00 a.m.
        EndDate               : 12/01/2013 08:00:00 a.m.
        Usage                 : Verify 

    > [WACOM.NOTE]
    > 您應該儲存此輸出，尤其是產生的對稱金鑰。此金鑰只會在建立服務主體期間顯示給您，而且日後您將無法擷取它。需要其他值，才能使用圖形 API 來讀取和寫入目錄中的資訊。

6.  最終步驟為您的應用程式設定回覆 URL。回覆 URL 為嘗試驗證之後傳送回應的目的地。輸入下列命令並按 Enter 鍵：

        $replyUrl = New-MsolServicePrincipalAddresses -Address "https://localhost:8443/sample" 

        Set-MsolServicePrincipal -AppPrincipalId "7829c758-2bef-43df-a685-717089474505" -Addresses $replyUrl 

現在已在目錄中佈建 Web 應用程式，而且公司員工可以使用它，進行 Web 單一登入。

## <a name="protectapp"></a>步驟 3：使用 WS-同盟進行員工登入來保護應用程式

此步驟顯示如何使用 Windows Identity Foundation (WIF) ，以及您已下載作為必要條件中範例程式碼套件一部分的 **waad-federation** 程式庫，新增結盟登入的支援。您也將新增登入頁面，並在應用程式與目錄租用戶之間設定信任。

1.  在 JBoss Developer Studio 中，按一下 [檔案]，然後按一下 [匯入]。

2.  在 [匯入] 對話方塊上，展開 [Maven] 資料夾、按一下 [Existing Maven Projects]，然後按 [下一步]。

3.  在 [Import Maven Projects] 對話方塊上，將 [根目錄] 路徑設為範例程式碼中您下載 **waad-federation** 程式庫的位置。然後，從 **waad-federation** 專案中選取 **pom.xml** 檔案旁邊的核取方塊，然後按一下 [完成]。

4.  展開 **sample** 專案、在 **pom.xml** 檔案上按一下滑鼠右鍵，然後按一下 [開啟檔案]，然後按一下 [文字編輯器]。

5.  在 **pom.xml** 檔案中，於 *\<project\>* 區段內新增下列 XML，然後儲存檔案：

        <dependencies>
            <dependency>
                <groupId>javax.servlet</groupId>
                <artifactId>servlet-api</artifactId>
                <version>3.0-alpha-1</version>
            </dependency>
            <dependency>
                <groupId>com.microsoft.samples.waad.federation</groupId>
                <artifactId>waad-federation</artifactId>
                <version>0.0.1-SNAPSHOT</version>
            </dependency>
        </dependencies> 

6.  在 **sample** 專案上按一下滑鼠右鍵、按一下 [Maven]，然後按一下 [更新專案]。在 [Update Maven Project] 對話方塊中，按一下 [確定]。此步驟將利用 **pom.xml** 變更來更新您的專案。

7.  在 **sample** 專案上按一下滑鼠右鍵，按一下 [新增]，然後按一下 [篩選]。

8.  在 [建立篩選器] 對話方塊中，對 [類別名稱] 項目輸入 *FederationFilter*，然後按一下 [完成]。

9.  將開啟自動產生的 **FederationFilter.java** 檔案。將其程式碼取代為下列程式碼，並儲存檔案：

        import java.io.IOException;
        import javax.servlet.Filter;
        import javax.servlet.FilterChain;
        import javax.servlet.FilterConfig;
        import javax.servlet.ServletException;
        import javax.servlet.ServletRequest;
        import javax.servlet.ServletResponse;
        import javax.servlet.http.HttpServletRequest;
        import javax.servlet.http.HttpServletResponse;
        import java.util.regex.*;
        import com.microsoft.samples.federation.FederatedLoginManager; import com.microsoft.samples.federation.URLUTF8Encoder;

        public class FederationFilter implements Filter {
            private String loginPage;
            private String allowedRegex;
            public void init(FilterConfig config) throws ServletException {
                this.loginPage = config.getInitParameter("login-page-url");
                this.allowedRegex = config.getInitParameter("allowed-regex");
            }
            public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
                    if (request instanceof HttpServletRequest) { 
                        HttpServletRequest httpRequest = (HttpServletRequest) request;
                        if (!httpRequest.getRequestURL().toString().contains(this.loginPage)) {
                            FederatedLoginManager loginManager = FederatedLoginManager.fromRequest(httpRequest);
                            boolean allowedUrl = Pattern.compile(this.allowedRegex).matcher(httpRequest.getRequestURL().toString()).find();
                            if (!allowedUrl && !loginManager.isAuthenticated()) {
                                HttpServletResponse httpResponse = (HttpServletResponse) response;
                                String encodedReturnUrl = URLUTF8Encoder.encode(httpRequest.getRequestURL().toString());
                                httpResponse.setHeader("Location", this.loginPage + "?returnUrl=" + encodedReturnUrl);
                                httpResponse.setStatus(302);
                                return;
                            }
                        }
                    }
                chain.doFilter(request, response);
            }
            public void destroy() {
            }
        } 

10. 在 [專案總管] 中，依序展開 **src**、**main**、**webapp** 資料夾。在 **web.xml** 檔案上按一下滑鼠右鍵、按一下 [開啟檔案]，然後按一下 [文字編輯器]。

11. 在 **web.xml** 檔案中，您將新增篩選器，以處理安全及不安全的頁面，而且也將未經驗證的使用者重新導向至登入頁面 (指定為 **login-page-url** 篩選參數)。不過，如果 URL 符合 **allowed-regex** 篩選參數中指定的 regex，則將不會篩選它。在 *\<web-app\>* 區段內新增下列 XML，然後儲存 **web.xml** 檔案。

        <filter>
            <filter-name>FederationFilter</filter-name>
            <filter-class>FederationFilter</filter-class>
            <init-param>
                <param-name>login-page-url</param-name>
                <param-value>/sample/login.jsp</param-value>
            </init-param>
            <init-param>
                <param-name>allowed-regex</param-name>
                <param-value>(\/sample\/login.jsp|\/sample\/wsfed-saml|\/sample\/oauth)</param-value>
            </init-param>
        </filter>
        <filter-mapping>
            <filter-name>FederationFilter</filter-name>
            <url-pattern>/*</url-pattern>
        </filter-mapping> 

12. 若要建立登入頁面，請在 **sample** 專案上按一下滑鼠右鍵，按一下 [新增]，然後按一下 [JSP File]。

13. 在 [New JSP File] 對話方塊上，將新檔案的路徑變更為 *sample/src/main/webapp*。然後，將檔案命名為 **login.jsp**，然後按一下 [完成]。

14. 新的 **login.jsp** 檔案將自動開啟。將自動產生的程式碼取代為下列程式碼，然後儲存檔案：

        <%@ page language="java" contentType="text/html; charset=ISO-8859-1"  pageEncoding="ISO-8859-1"%>
        <%@ page import="com.microsoft.samples.federation.*"%>
        <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
        <html>
        <head>
            <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1"> 
            <title>Login Page</title>
        </head>
        <body>
            <h3>Login Page</h3>
            <a href="<%=FederatedLoginManager.getFederatedLoginUrl(request.getParameter("returnUrl"))%>"><%=FederatedConfiguration.getInstance().getStsFriendlyName()%></a>
        </body>
        </html> 

15. 在 [專案總管] 中展開 **sample** 資料夾的 **/src/main** 資料夾。在 **resources** 資料夾上按一下滑鼠右鍵、按一下 [新增]，然後按一下 [其他]。

16. 從 [新增] 對話方塊中，展開 **JBoss Tools Web** 資料夾、按一下 [內容檔]，然後按 [下一步]。

17. 在 [新檔案內容] 對話方塊上，將檔案命名為 **federation**，然後按一下 [完成]。

18. 在 [專案總管] 中展開 **sample** 資料夾的 **src/main/resources** 資料夾。在 **federation.properties** 檔案上按一下滑鼠右鍵、按一下 [開啟檔案]，然後按一下 [文字編輯器]。

19. 在 **federation.properties** 檔案中，包括下列組態項目，然後儲存檔案：

        federation.trustedissuers.issuer=https://accounts.accesscontrol.windows.net/v2/wsfederation
        federation.trustedissuers.thumbprint=qY+Drf20Zz+A4t2we3PebCopoCugO76My+JMVsqNBFc=
        federation.trustedissuers.friendlyname=Fabrikam
        federation.audienceuris=spn:7829c758-2bef-43df-a685-717089474505
        federation.realm=spn:7829c758-2bef-43df-a685-717089474505
        federation.reply=https://localhost:8443/sample/wsfed-saml 

    > [WACOM.NOTE]
    >  **audienceuris** 和 **realm** 值的前面必須加上 "spn:"。

20. 現在您將需要建立新的 Servlet。在 **sample** 專案上按一下滑鼠右鍵、按一下 [新增]、按一下 [其他]，然後按一下 [Servlet]。

21. 在 [Create Servlet] 對話方塊上,提供 *FederationServlet* 的 [類別名稱]，然後按一下 [完成]。

22. **FederationServlet.java** 檔案將自動開啟。將其內容取代為下列程式碼，然後儲存檔案：

        import java.io.IOException;
        import javax.servlet.ServletException;
        import javax.servlet.http.HttpServlet;
        import javax.servlet.http.HttpServletRequest;
        import javax.servlet.http.HttpServletResponse;
        import com.microsoft.samples.federation.FederatedAuthenticationListener;
        import com.microsoft.samples.federation.FederatedLoginManager;
        import com.microsoft.samples.federation.FederatedPrincipal;
        import com.microsoft.samples.federation.FederationException;

        public class FederationServlet extends HttpServlet {
            private static final long serialVersionUID = 1L;

            protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
                String token = request.getParameter("wresult").toString();

                if (token == null) {
                    response.sendError(400, "You were supposed to send a wresult parameter with a token");
                }
                FederatedLoginManager loginManager = FederatedLoginManager.fromRequest(request, new SampleAuthenticationListener());

                try {
                    loginManager.authenticate(token, response);
                } catch (FederationException e) {
                    response.sendError(500, "Oops! and error occurred.");
                }
            }

            private class SampleAuthenticationListener implements FederatedAuthenticationListener {
                @Override
                public void OnAuthenticationSucceed(FederatedPrincipal principal) {
                    // ***
                    // do whatever you want with the principal object that contains the token's claims
                    // ***
                }
            }
        } 

23. 在 [專案總管] 中，展開 **src/main/webapp/WEB-INF** 資料夾。在 **web.xml** 檔案上按一下滑鼠右鍵、按一下 [開啟檔案]，然後按一下 [文字編輯器]。

24. 在 **web.xml** 檔案中，將 *\<url-pattern\>* 區段中的 **/FederationServlet** 設定取代為 **/ws-saml**。例如：

        <servlet>
            <description></description>
            <display-name>FederationServlet</display-name>
            <servlet-name>FederationServlet</servlet-name>
            <servlet-class>FederationServlet</servlet-class>
        </servlet>
        <servlet-mapping>
            <servlet-name>FederationServlet</servlet-name>
            <url-pattern>/wsfed-saml</url-pattern>
        </servlet-mapping> 

25. 在 [專案總管] 中，展開 **src/main/webapp** 資料夾。在 **index.jsp** 檔案上按一下滑鼠右鍵、按一下 [開啟檔案]，然後按一下 [文字編輯器]。

26. 在 **index.jsp** 檔案中，將現有的程式碼取代為下列程式碼，然後儲存檔案：

        <%@ page language="java" contentType="text/html; charset=ISO-8859-1" pageEncoding="ISO-8859-1"%>
        <%@ page import="com.microsoft.samples.federation.*"%>
        <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
        <html>
        <head>
            <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
            <title>Index Page</title>
        </head>
        <body>
            <h3>Welcome <strong><%=FederatedLoginManager.fromRequest(request).getPrincipal().getName()%></strong>!</h3>
            <h2>Claim list:</h2>
            <ul> 
                <% for (Claim claim : FederatedLoginManager.fromRequest(request).getClaims()) { %>
                <li><%= claim.toString()%></li>
                <%  } %>
            </ul>
        </body>
        </html> 

27. 在 [專案總管] 中，展開 **src/main/webapp/WEB-INF** 資料夾。在 **web.xml** 檔案上按一下滑鼠右鍵、按一下 [開啟檔案]，然後按一下 [文字編輯器]。

28. 我們現在將對應用程式啟用 SSL。在 **web.xml** 檔案中，於 *\<web-app\>* 區段內插入下列 *\<security-constraint\>* 區段，然後儲存檔案：

        <security-constraint>
            <web-resource-collection>
                <web-resource-name>SSL Forwarding</web-resource-name>
                <url-pattern>/*</url-pattern>
                <http-method>POST</http-method>
                <http-method>GET</http-method>
            </web-resource-collection>
            <user-data-constraint>
                <transport-guarantee>CONFIDENTIAL</transport-guarantee>
            </user-data-constraint>
        </security-constraint> 

    > [WACOM.NOTE]
    > 在繼續進行之前，請確定 JBoss Server 已設定成支援 SSL。

29. 現在我們已準備好執行應用程式端對端。在 **sample** 專案上按一下滑鼠右鍵、按一下 [Run As]，然後按一下 [Run On Server]。接受您之前指定的值，然後按一下 [完成]。

30. JBoss 瀏覽器將開啟登入頁面。如果按一下 [Awesome Computers] 連結，您將被重新導向至 Office 365 身分識別提供者頁面，在此頁面中您可以使用目錄租用戶認證進行登入。例如，<*john.doe@fabrikam.onmicrosoft.com*>。

31. 在您登入後，將以已驗證的使用者身分被重新導向至安全頁面 (**sample/index.jsp**)。

## <a name="summary"></a>摘要

本教學課程已顯示如何建立和設定單一租用戶 Java 應用程式，來使用 Azure Active Directory 的單一登入功能。

  [Java Sample Code for Azure Active Directory]: https://github.com/WindowsAzure/azure-sdk-for-java-samples/tree/master/WAAD.WebSSO.JAVA
  [Java Runtime Environment 1.6]: http://www.oracle.com/technetwork/java/javase/downloads/index.html
  [JBoss Application Server version 7.1.1.Final]: http://www.jboss.org/jbossas/downloads/
  [JBoss Developer Studio IDE]: https://devstudio.jboss.com/earlyaccess/
  [Office 365 PowerShell Commandlets]: http://onlinehelp.microsoft.com/en-us/office365-enterprises/ff652560.aspx
  [簡介]: #introduction
  [步驟 1：建立 Java 應用程式]: #createapp
  [步驟 2：在公司的目錄租用戶中佈建應用程式]: #provisionapp
  [步驟 3：使用 WS-同盟進行員工登入來保護應用程式]: #protectapp
  [摘要]: #summary
