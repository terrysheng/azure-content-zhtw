<properties 
   pageTitle="保護 Azure 資料湖儲存區的資料 | Azure" 
   description="了解如何使用群組和存取控制清單來保護 Azure 資料湖儲存區中的資料" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="12/11/2015"
   ms.author="nitinme"/>

# 保護儲存在 Azure 資料湖儲存區中的資料

保護儲存在 Azure 資料湖儲存區中的資料，其方法有三步驟。

1. 首先，在 Azure Active Directory (AAD) 中建立安全性群組。這些安全性群組是用在 Azure 入口網站中實作角色型存取控制 (RBAC)。如需詳細資訊，請參閱 [Microsoft Azure 中的角色型存取控制](role-based-access-control-configure.md)。

2. 指派 AAD 安全性群組給 Azure 資料湖儲存區帳戶。這會控制從入口網站至資料湖儲存區帳戶，以及從入口網站或 API 至管理作業的存取。

3. 指派 AAD 安全性群組做為資料湖儲存區檔案系統上的存取控制清單 (ACL)。

本文提供有關如何使用 Azure 入口網站執行上述工作的詳細指示。

## 必要條件

開始進行本教學課程之前，您必須具備下列條件：

- **Azure 訂用帳戶**。請參閱[取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
- **Azure 資料湖儲存區帳戶**。如需有關如何建立帳戶的詳細指示，請參閱[開始使用 Azure 資料湖儲存區](data-lake-store-get-started-portal.md)

## 在 Azure Active Directory 中建立安全性群組

如需有關如何建立 AAD 安全性群組及如何新增使用者至群組的詳細指示，請參閱[管理 Azure Active Directory 中的安全性群組](active-directory-accessmanagement-manage-groups.md)。

## 指派使用者或安全性群組給 Azure 資料湖儲存區帳戶

當您指派使用者或安全性群組給 Azure 資料湖儲存區帳戶時，您可使用 Azure 入口網站和 Azure 資源管理員 API 來控制該帳戶的管理作業存取。

1. 開啟 Azure 資料湖儲存區帳戶。從左側窗格依序按一下 [瀏覽]、[資料湖儲存區]，然後從 [資料湖儲存區] 刀鋒視窗中，按一下您要指派使用者或安全性群組的目標帳戶名稱。

2. 在您的 [資料湖儲存區帳戶] 刀鋒視窗中，按一下 [使用者] 圖示。

	![指派安全性群組給 Azure 資料湖儲存區帳戶](./media/data-lake-store-secure-data/adl.select.user.icon.png "指派安全性群組給 Azure 資料湖儲存區帳戶")

3. 依預設，[使用者] 刀鋒視窗會將 [訂用帳戶管理員] 群組列為擁有者。

	![新增使用者和角色](./media/data-lake-store-secure-data/adl.add.group.roles.png "新增使用者和角色")
 
	有兩種方式可新增群組並指派相關的角色。

	* 新增使用者/群組至帳戶，然後指派角色，或
	* 新增角色，然後指派使用者/群組給角色。

	在本節中，我們將探討第一個方法，也就是新增群組，然後指派角色。您可以執行類似的步驟先選取角色，然後指派群組給該角色。
	
4. 在 [使用者] 刀鋒視窗中，按一下 [新增] 以開啟 [新增存取] 刀鋒視窗。在 [新增存取] 刀鋒視窗中，按一下 [選取角色]，然後選取使用者/群組的角色。

	 ![新增使用者的角色](./media/data-lake-store-secure-data/adl.add.user.1.png "新增使用者的角色")

	[擁有者] 和 [參與者] 角色會提供資料湖帳戶上各種不同管理功能的存取。針對與資料湖資料互動的使用者，您可以將它們新增至 [讀取器] 角色。這些角色的範圍僅限於與 Azure 資料湖儲存區帳戶相關的管理作業。

	針對資料作業，個別的檔案系統權限會定義使用者的使用範圍。因此，擁有 [讀取器] 角色的使用者僅可檢視與帳戶相關的管理設定，但是可依指派給該使用者的檔案系統權限來讀取和寫入資料。有關資料湖儲存區檔案系統權限的相關資訊都在[將安全性群組以 ACL 型式指派給 Azure 資料湖儲存區檔案系統](#filepermissions)。



5. 在 [新增存取] 刀鋒視窗中，按一下 [新增使用者] 以開啟 [新增使用者] 刀鋒視窗。在此刀鋒視窗中，搜尋您稍早在 Azure Active Directory 中建立的安全性群組。若您需要搜尋大量的群組，請使用頂端的文字方塊來篩選群組名稱。按一下 [選取]。

	![新增安全性群組](./media/data-lake-store-secure-data/adl.add.user.2.png "新增安全性群組")

	若要新增未列出的群組/使用者，您可使用 [邀請] 圖示並指定使用者/群組的電子郵件位址來邀請。

6. 按一下 [確定]。您會看見新增的安全性群組，如下所示。

	![已新增的安全性群組](./media/data-lake-store-secure-data/adl.add.user.3.png "已新增的安全性群組")

7. 您的使用者/安全性群組現在可以存取 Azure 資料湖儲存區帳戶。若要將存取給予特定的使用者，您可以將其新增至安全性群組。同樣地，若要撤銷使用者的存取，您可以將其從安全性群組中移除。您也可以將多個安全性群組指派給一個帳戶。

## <a name="filepermissions"></a>將使用者或安全性群組以 ACL 型式指派給 Azure 資料湖儲存區檔案系統

藉由指派使用者/安全性群組給 Azure 資料湖檔案系統，您可以針對儲存在 Azure 資料湖儲存區中的資料設定存取控制。在目前版本中，僅可在檔案系統的根節點設定 ACL。

1. 在您的 [資料湖儲存區帳戶] 刀鋒視窗中，按一下 [資料總管]。

	![在資料湖儲存區帳戶中建立目錄](./media/data-lake-store-secure-data/adl.start.data.explorer.png "在資料湖帳戶中建立目錄")

2. 在 [資料總管] 刀鋒視窗中，按一下帳戶的根目錄，然後在 [帳戶] 刀鋒視窗中，按一下 [存取] 圖示。

	![設定資料湖檔案系統上的 ACL](./media/data-lake-store-secure-data/adl.acl.1.png "設定資料湖檔案系統上的 ACL")

3. [存取] 刀鋒視窗會列出已指派至根的標準存取和自訂存取。按一下 [新增] 圖示以新增自訂層級的 ACL。

	![列出標準和自訂存取](./media/data-lake-store-secure-data/adl.acl.2.png "列出標準和自訂存取")

	* 標準存取為 UNIX 樣式存取，可讓您指定讀取、寫入、執行 (rwx) 三種不同的使用者類別：擁有者、群組和其他。
	* 自訂存取會對應至 POSIX ACL，讓您除了設定檔案擁有者或群組的權限外，還可以設定特定命名的使用者或群組的權限。
	
	如需詳細資訊，請參閱 [HDFS ACLs](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists)。

4. 按一下 [新增] 圖示，以開啟 [新增自訂存取] 刀鋒視窗。在此刀鋒視窗中，按一下 [選取使用者或群組]，然後在 [選取使用者或群組] 刀鋒視窗中，搜尋您稍早在 Azure Active Directory 中建立的安全性群組。若您需要搜尋大量的群組，請使用頂端的文字方塊來篩選群組名稱。按一下您要新增的群組，然後按一下 [選取]。

	![新增群組](./media/data-lake-store-secure-data/adl.acl.3.png "新增群組")

5. 按一下 [選取權限]，選取您要指派給該群組的權限，然後按一下 [確定]。

	![將權限指派至群組](./media/data-lake-store-secure-data/adl.acl.4.png "將權限指派至群組")


	>[AZURE.NOTE]您必須擁有「執行」權限才可列舉目錄，而且在提供資料唯讀存取給使用者或群組時也經常需要使用該權限。


6. 在 [新增自訂存取] 刀鋒視窗中，按一下 [確定]。具有相關權限的新增群組現在會列在 [存取] 刀鋒視窗中。

	![將權限指派至群組](./media/data-lake-store-secure-data/adl.acl.5.png "指派權限給群組")

	> [AZURE.IMPORTANT]在目前版本中，[自訂存取] 下僅可有 12 個項目。若要新增 12 位以上的使用者，您必須建立安全性群組、新增使用者至安全性群組，並且新增存取權限給該資料湖儲存區帳戶的安全性群組。

7. 如有需要，您也可以在新增群組之後修改存取權限。根據您是否要移除或指派該權限給安全性群組，選擇清除或選取每個權限類型的核取方塊 (讀取、寫入、執行)。按一下 [儲存] 儲存變更，或按一下 [捨棄] 復原變更。

## 移除 Azure 資料湖儲存區帳戶的安全性群組

當您從 Azure 資料湖儲存區帳戶移除安全性群組時，僅會變更使用 Azure 入口網站和 Azure 資源管理員 API 的帳戶上，其管理作業的存取。

1. 在您的 [資料湖儲存區帳戶] 刀鋒視窗中，按一下 [使用者] 圖示。

	![指派安全性群組給 Azure 資料湖帳戶](./media/data-lake-store-secure-data/adl.select.user.icon.png "指派安全性群組給 Azure 資料湖帳戶")

2. 在 [使用者] 刀鋒視窗中，按一下您要移除的安全性群組。

	![要移除的安全性群組](./media/data-lake-store-secure-data/adl.add.user.3.png "要移除的安全性群組")

3. 在安全性群組的刀鋒視窗中，按一下 [移除]。

	![已移除的安全性群組](./media/data-lake-store-secure-data/adl.remove.group.png "已移除的安全性群組")

## 從 Azure 資料湖儲存區檔案系統移除安全性群組 ACL

當您從 Azure 資料湖儲存區檔案系統中移除安全性群組 ACL 時，會變更資料湖儲存區資料的存取。

1. 在您的 [資料湖儲存區帳戶] 刀鋒視窗中，按一下 [資料總管]。

	![在資料湖帳戶中建立目錄](./media/data-lake-store-secure-data/adl.start.data.explorer.png "在資料湖帳戶中建立目錄")

2. 在 [資料總管] 刀鋒視窗中，按一下帳戶的根目錄，然後在 [帳戶] 刀鋒視窗中，按一下 [存取] 圖示。

	![設定資料湖檔案系統上的 ACL](./media/data-lake-store-secure-data/adl.acl.1.png "設定資料湖檔案系統上的 ACL")

3. 從 [存取] 刀鋒視窗中的 [自訂存取] 區段，按一下您要移除的安全性群組。在 [自訂存取] 刀鋒視窗中，按一下 [移除]，然後按一下 [確定]。

	![指派權限給群組](./media/data-lake-store-secure-data/adl.remove.acl.png "指派權限給群組")


## 另請參閱

- [Azure 資料湖儲存區概觀](data-lake-store-overview.md)
- [將資料從 Azure 儲存體 Blob 複製到資料湖存放區](data-lake-store-copy-data-azure-storage-blob.md)
- [搭配資料湖存放區使用 Azure 資料湖分析](data-lake-analytics-get-started-portal.md)
- [搭配資料湖存放區使用 Azure HDInsight](data-lake-store-hdinsight-hadoop-use-portal.md)
- [使用 PowerShell 開始使用資料湖存放區](data-lake-store-get-started-powershell.md)
- [使用 .NET SDK 開始使用資料湖存放區](data-lake-store-get-started-net-sdk.md)

<!---HONumber=AcomDC_1217_2015-->