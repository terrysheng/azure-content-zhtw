以下是 Azure Active Directory 服務的使用條件約束和其他服務限制。

| 類別 | 限制 |
|---|---|
| 目錄 | 單一使用者僅能與最多 20 個 Azure Active Directory 目錄相關聯。<br />可能的組合範例：<ul> <li>單一使用者可建立 20 個目錄。</li><li>單一使用者可加入至 20 個目錄做為成員。</li><li>單一使用者可建立 10 個目錄，並於稍後由其他人員加入至 10 個不同的目錄。</li></ul> |  
| 物件 | <ul><li>免費版 Azure Active Directory 的使用者最多可在單一目錄中使用 500,000 個物件。</li><li>非系統管理員使用者不能建立超過 250 個物件。</li><li>您可以從內部部署 Active Directory 同步至 Azure Active Directory 的物件數目受限於使用 Azure Active Directory 目錄同步處理 (DirSync) 的 15K 使用者。</li><li>您可以使用 Azure AD Connect 從內部部署 Active Directory 同步至 Azure Active Directory 的物件數目受限於 50K 使用者。</li></ul> |
| 結構描述延伸模組 | <ul><li>字串類型延伸模組最多可有 256 個字元。</li><li>二進位類型延伸模組限制為 256 個位元組。</li><li> 100 個延伸模組值 (跨所有類型和所有應用程式) 可寫入至任何單一物件。</li><li>只有 “User”、“Group”、“TenantDetail”、“Device”、“Application” 和 “ServicePrincipal” 實體可以利用 「字串」類型或「二進位」類型單一值屬性延伸。</li><li>結構描述延伸模組只能在圖形 API 1.21 版預覽中使用。應用程式必須取得寫入權限才能登錄延伸模組。</li></ul> |
| [應用程式] | 最多 10 個使用者可以成為單一應用程式的擁有者。 |
| 群組 | <ul><li>最多 10 個使用者可以是單一群組的擁有者。</li><li>任何數目的物件都可以是 Azure Active Directory 中的單一群組的成員。</li></ul> |
| 存取面板 | <ul><li>每個使用者可在存取面板中看見的應用程式數目沒有任何限制，因為使用者會指派授權給 Azure AD Premium 或 Enterprise Mobility Suite。</li><li>每個使用者可在存取面板中看見最多 10 個應用程式磚 (範例：Box、Salesforce 或 Dropbox)，因為使用者會指派免費授權或 Azure AD Basic 版本的 Azure Active Directory。此限制不適用於系統管理員帳戶。</li></ul> |
| 報告 | 在任何報告中，最多可以檢視或下載 1000 個資料列。任何其他資料會遭到截斷。 |

<!---HONumber=August15_HO6-->