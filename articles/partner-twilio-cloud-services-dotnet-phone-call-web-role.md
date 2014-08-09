<properties linkid="develop-net-how-to-twilio-phone-call" urlDisplayName="Twilio Phone Call" pageTitle="How to make a phone call from Twilio (.NET) - Azure" metaKeywords="Azure .NET Twilio, Azure Twilio, Azure phone calls, Azure twilio, Azure SMS, Azure SMS, Azure voice calls, azure voice calls, Azure text messages, Azure text messages, ASP.NET twilio Azure" description="Learn how to make a phone call and send a SMS message with the Twilio API service on Azure. Code samples written in .NET." metaCanonical="" services="" documentationCenter=".NET" title="How to make a phone call using Twilio in a web role on Azure" authors="" solutions="" manager="" editor="" />

如何在 Azure 上的 Web 角色中使用 Twilio 撥打電話
================================================

本指南將說明如何從 Azure 代管的網頁上使用 Twilio 撥打電話。產生的應用程式會提示使用者提供電話值，如下列螢幕擷取畫面所示。

![Azure call form using Twilio and ASP.NET](./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form.png)

內容目錄
--------

-   [必要條件](#twilio-prereqs)
-   [作法：建立用以撥打電話的 Web 表單](#howtocreateform)
-   [作法：建立用以撥打電話的程式碼](#howtocreatecode)
-   [後續步驟](#nextsteps)
-   [另請參閱](#seealso)

先決條件先決條件
----------------

您必須執行下列動作才能使用本主題中的程式碼：

1.  取得 Twilio 帳戶和驗證權杖。若要開始使用 Twilio，請在 [https://www.twilio.com/try-twilio](http://www.twilio.com/try-twilio) 上註冊。您可以在 <http://www.twilio.com/pricing> 上評估價格。如需 Twilio 所提供之 API 的相關資訊，請參閱 <http://www.twilio.com/voice/api>。
2.  向 Twilio 驗證您的電話號碼。如需如何驗證電話號碼的相關資訊，請參閱 <https://www.twilio.com/user/account/phone-numbers/verified#>。除了使用現有的電話號碼，您也可以購買 Twilio 電話號碼。
    在此範例中，您將會使用 Twilio 沙箱電話號碼傳送訊息至已驗證的電話號碼。您只能使用沙箱電話號碼對已驗證的電話號碼進行傳送。
3.  將 Twilio .NET 程式庫新增至您的 Web 角色。請參閱本主題稍後的「將 Twilio 程式庫新增至 Web 角色專案」。

您應知悉如何在 Azure 上建立基本 Web 角色。

建立用以撥打電話的 Web 表單作法：建立用以撥打電話的 Web 表單
------------------------------------------------------------

### 將 Twilio 程式庫新增至 Web 角色專案：

1.  在 Visual Studio 中開啟方案。
2.  以滑鼠右鍵按一下 **[參考]**。
3.  按一下 **[管理 NuGet 封裝]**。
4.  按一下 **[線上]**。
5.  在搜尋線上方塊中，輸入 *twilio*。
6.  在 Twilio 套件上按一下 **[安裝]**。

下列程式碼將說明如何建立 Web 表單，以擷取撥打電話所需的使用者資料。在此範例中，會建立名為 **TwilioCloud** 的 ASP.NET Web 角色。

    <%@ Page Title="Home Page" Language="C#" MasterPageFile="~/Site.master"
        AutoEventWireup="true" CodeBehind="Default.aspx.cs"
        Inherits="WebRole1._Default" %>

    <asp:Content ID="HeaderContent" runat="server" ContentPlaceHolderID="HeadContent">
    </asp:Content>
    <asp:Content ID="BodyContent" runat="server" ContentPlaceHolderID="MainContent">
        <div>
            <asp:BulletedList ID="varDisplay" runat="server" BulletStyle="NotSet">
            </asp:BulletedList>
        </div>
        <div>
            <p>Fill in all fields and click <b>Make this call</b>.</p>
            <div>
                To:<br /><asp:TextBox ID="toNumber" runat="server" /><br /><br />
                Message:<br /><asp:TextBox ID="message" runat="server" /><br /><br />
                <asp:Button ID="callpage" runat="server" Text="Make this call"
                    onclick="callpage_Click" />
            </div>
        </div>
    </asp:Content>

建立程式碼作法：建立用以撥打電話的程式碼
----------------------------------------

下列程式碼會在使用者完成表單時受到呼叫，可用來建立通話訊息及產生通話。在此範例中，程式碼會在表單按鈕的 onclick 事件處理常式中執行。(在下方的程式碼中，請使用您的 Twilio 帳戶和驗證權杖，而不要使用指派給 **accountSID** 和 **authToken** 的預留位置值。)

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using System.Web.UI;
    using System.Web.UI.WebControls;
    using Twilio;

    namespace WebRole1
    {
        public partial class _Default : System.Web.UI.Page
        {
            protected void Page_Load(object sender, EventArgs e)
            {

            }

            protected void callpage_Click(object sender, EventArgs e)
            {
                // Call porcessing happens here.

                // Use your account SID and authentication token instead of
                // the placeholders shown here.
                string accountSID = "ACNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN";
                string authToken =  "NNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN";

                // Instantiate an instance of the Twilio client.
                TwilioRestClient client;
                client = new TwilioRestClient(accountSID, authToken);

                // Retrieve the account, used later to retrieve the
                Twilio.Account account = client.GetAccount();
                string APIversuion = client.ApiVersion;
                string TwilioBaseURL = client.BaseUrl;

                this.varDisplay.Items.Clear();
                if (this.toNumber.Text == "" || this.message.Text == "")
                {
                    this.varDisplay.Items.Add(
                            "You must enter a phone number and a message.");
                }
                else
                {
                    // Retrieve the values entered by the user.
                    string to = this.toNumber.Text;
                    string myMessage = this.message.Text;

                    // Create a URL using the Twilio message and the user-entered
                    // text. You must replace spaces in the user's text with '%20'
                    // to make the text suitable for a URL.
                    String Url = "http://twimlets.com/message?Message%5B0%5D="
                            + myMessage.Replace(" ", "%20");

                    // Diplay the enpoint, API version, and the URL for the message.
                    this.varDisplay.Items.Add("Using Tilio endpoint "
                        + TwilioBaseURL);
                    this.varDisplay.Items.Add("Twilioclient API Version is "
                        + APIversuion);
                    this.varDisplay.Items.Add("The URL is " + Url);

                    // Instantiate the call options that are passed
                    // to the outbound call.
                    CallOptions options = new CallOptions();

                    // Set the call From, To, and URL values into a hash map.
                    // This sample uses the sandbox number provided by Twilio
                    // to make the call.
                    options.From = "+14155992671";
                    options.To = to;
                    options.Url = Url;

                    // Place the call.
                    var call = client.InitiateOutboundCall(options);
                    this.varDisplay.Items.Add("Call status: " + call.Status);
                }
            }
        }
    }

通話建立後，會顯示 Twilio 端點、API 版本和通話狀態。下列螢幕擷取畫面顯示執行範例的輸出。

![Azure call response using Twilio and ASP.NET](./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form_output.png)

如需 TwiML 的相關資訊，請參閱 <http://www.twilio.com/docs/api/twiml>。如需 &lt;Say\> 和其他 Twilio 動詞的詳細資訊，請參閱 <http://www.twilio.com/docs/api/twiml/say>。

後續步驟後續步驟
----------------

此程式可說明在 Azure 上的 ASP.NET Web 角色中使用 Twilio 的基本功能。在部署至生產環境中的 Azure 之前，您可以新增更多錯誤處理或其他功能。例如：

-   除了使用 Web 表單以外，您也可以使用 Azure Blob 儲存體或 Azure SQL Database 執行個體來儲存電話號碼和通話文字。如需在 Azure 中使用 Blob 的相關資訊，請參閱[如何在 .NET 中使用 Azure Blob 儲存體服務](https://www.windowsazure.com/en-us/develop/net/how-to-guides/blob-storage/)。如需使用 SQL Database 的相關資訊，請參閱[如何在 .NET 應用程式中使用 Azure SQL Database](https://www.windowsazure.com/en-us/develop/net/how-to-guides/sql-database/)。
-   您可以使用 RoleEnvironment.getConfigurationSettings，從部署的組態設定中擷取 Twilio 帳戶 ID 和驗證權杖，而不要在表單中進行值的硬式編碼。如需 RoleEnvironment 類別的相關資訊，請參閱 [Microsoft.WindowsAzure.ServiceRuntime 命名空間](http://msdn.microsoft.com/zh-tw/library/windowsazure/microsoft.windowsazure.serviceruntime.aspx)。
-   閱讀 [https://www.twilio.com/docs/security](http://www.twilio.com/docs/security) 上的 Twilio 安全性指引。
-   在 [https://www.twilio.com/docs](http://www.twilio.com/docs) 上深入了解 Twilio。

另請參閱另請參閱
----------------

-   [如何在 Web 角色中透過 Twilio 使用語音和簡訊功能](/en-us/develop/net/how-to-guides/twilio/)

