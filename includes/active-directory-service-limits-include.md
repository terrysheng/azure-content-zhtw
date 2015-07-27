以下是 Azure Active Directory 服務的使用條件約束和其他服務限制。

### 目錄

單一使用者最多只能與 20 個 Azure Active Directory 目錄相關聯。在下列任何範例中，可以達到這個限制：

- 單一使用者建立 20 個目錄。
- 單一使用者新增 20 個目錄作為成員。
- 單一使用者建立 10 個目錄，之後由其他使用者加入到 10 個不同的目錄。

### 物件

- Azure Active Directory Premium 或 Azure Active Directory Basic、Enterprise Mobility Suite、Office 365、Microsoft Intune 或任何依賴 Azure Active Directory 作為目錄服務之其他 Microsoft 線上服務的訂閱者沒有限制。
- 在免費版的 Azure Active Directory 中，最多可以在單一目錄中使用 500,000 個物件。
- 非系統管理員的使用者最多可以建立 250 個物件。

###結構描述延伸模組

可以使用「字串」類型或「二進位」類型單一值屬性來擴充目前的 “User”、“Group”、“TenantDetail”、“Device”、“Application” 和 “ServicePrincipal” 實體。其中包括下列限制：

- 字串類型延伸模組最多可有 256 個字元。
- 二進位類型延伸模組受限於 256 個位元組。
- 任何單一物件均可寫入 100 個延伸模組值 (所有類型和所有應用程式皆可)。
- 結構描述延伸模組僅適用於圖形 API 版本 1.21 預覽。應用程式必須取得寫入權限才能登錄延伸模組。

### [應用程式]

最多 10 個使用者可以成為單一應用程式的擁有者。

### 群組

- 最多 10 個使用者可以成為單一群組的擁有者。
- 任何數目的物件都可以是 Azure Active Directory 中的單一群組的成員。


> [AZURE.NOTE]
>
> 您可以從內部部署 Active Directory 同步至 Azure Active Directory 的物件數目沒有限制。
>
> - 如果您使用 DirSync，則限制為 15000 個使用者。
> - 如果您使用 Azure AD 連接，限制為 50000 個使用者。

<br/>
### 存取面板

- Azure AD Premium 或 Enterprise Mobility Suite 訂閱者端的每位使用者在存取面板中可以看到的應用程式數目沒有限制。
- 具有 Azure Active Directory 免費版或 Azure AD Basic 版的每位使用者在存取面板中最多可以看到 10 個應用程式磚 (例如：Box、Salesforce 或 Dropbox)。此限制不適用於系統管理員帳戶。

### 報告

在任何報告中，最多可以檢視或下載 1000 個資料列。任何其他資料會遭到截斷。

<!---HONumber=July15_HO3-->