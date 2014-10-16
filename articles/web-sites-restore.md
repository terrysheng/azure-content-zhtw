<properties linkid="web-sites-restore" urlDisplayName="Restore a Microsoft Azure web site" pageTitle="Restore a Microsoft Azure web site" metaKeywords="Azure Web Sites, Restore, restoring" description="Learn how to restore your Azure web sites from backup." metaCanonical="" services="web-sites" documentationCenter="" title="Restore a Microsoft Azure web site" authors="timamm" solutions="" writer="timamm" manager="paulettm" editor="mollybos" />

Microsoft Azure 웹 사이트 복원
==============================

이 문서에서는 Azure 웹 사이트 백업 기능을 사용하여 이전에 백업한 웹 사이트를 복원하는 방법을 보여 줍니다. 자세한 내용은 [Microsoft Azure 웹 사이트 백업](http://www.windowsazure.com/zh-tw/documentation/articles/web-sites-backup/)을 참조하십시오.

Azure 웹 사이트 복원 기능을 통해 요청 시 웹 사이트를 이전 상태로 복원하거나 원본 사이트 백업 중 하나를 기반으로 새 웹 사이트를 만들 수 있습니다. 최신 버전과 병행하여 실행되는 웹 사이트를 새로 만드는 것이 A/B를 테스트하는 데 유용할 수 있습니다.

Azure 웹 사이트 포털의 백업 탭에서 사용할 수 있는 복원 기능은 표준 모드에서만 사용할 수 있습니다.

이 문서에서는 다음을 수행합니다.
--------------------------------

-   [이전에 만든 백업에서 Azure 웹 사이트를 복원하려면](#PreviousBackup)
-   [저장소 계정에서 바로 Azure 웹 사이트를 복원하려면](#StorageAccount)
-   [웹 사이트 복원 설정 선택 및 복원 작업 시작](#RestoreSettings)
-   [작업 로그 보기](#OperationLogs)

## 이전에 만든 백업에서 Azure 웹 사이트를 복원하려면

1.  **백업** 탭에서, 포털 페이지의 맨 아래에 있는 명령 모음에서 **지금 복원**을 클릭합니다. **지금 복원** 대화 상자가 나타납니다.

    ![백업 원본 선택](./media/web-sites-restore/01ChooseBackupSource.png)

2.  **백업 원본 선택**에서 **이 웹 사이트의 이전 백업**을 선택합니다.
3.  복원할 백업 날짜를 선택한 다음 오른쪽 화살표를 클릭하여 계속합니다.
4.  이 문서의 뒷부분에 나오는 [웹 사이트 복원 설정 선택](#RestoreSettings) 섹션의 단계를 따릅니다.

## 저장소 계정에서 바로 Azure 웹 사이트를 복원하려면

1.  **백업** 탭에서, 포털 페이지의 맨 아래에 있는 명령 모음에서 **지금 복원**을 클릭합니다. **지금 복원** 대화 상자가 나타납니다.

    ![백업 원본 선택](./media/web-sites-restore/01ChooseBackupSource.png)

2.  **백업 원본 선택**에서 **저장소 계정 파일**을 선택합니다. 여기서 저장소 계정 파일의 URL을 바로 지정하거나 폴더 아이콘을 클릭하여 Blob 저장소를 찾아 백업 파일을 지정할 수 있습니다. 이 예제에서는 폴더 아이콘을 선택합니다.

    ![저장소 계정 파일](./media/web-sites-restore/02StorageAccountFile.png)

3.  폴더 아이콘을 클릭하여 **클라우드 저장소 찾아보기** 대화 상자를 엽니다.

    ![클라우드 저장소 찾아보기](./media/web-sites-restore/03BrowseCloudStorage.png)

4.  사용할 저장소 계정의 이름을 확장한 후 백업이 포함된 **websitebackups**를 선택합니다.
5.  복원할 백업이 포함된 zip 파일을 선택한 다음 **열기**를 클릭합니다.
6.  저장소 계정 파일이 선택된 채 저장소 계정 상자에 표시됩니다. 오른쪽 화살표를 클릭하여 계속합니다.

    ![선택된 저장소 계정 파일](./media/web-sites-restore/04StorageAccountFileSelected.png)

7.  다음에 나오는 섹션인 [웹 사이트 복원 설정 선택 및 복원 작업 시작](#RestoreSettings)을 계속합니다.

## 웹 사이트 복원 설정 선택 및 복원 작업 시작
1. **웹 사이트 복원 설정 선택**의 **복원 대상**에서 **현재 웹 사이트** 또는 **새 웹 사이트 인스턴스**를 선택합니다.

    ![Choose your web site restore settings][ChooseRestoreSettings]

    **현재 웹 사이트**를 선택하면 선택한 백업으로 기존 웹 사이트를 덮어씁니다(파괴 복원). 선택한 백업 시간 이후 웹 사이트의 모든 변경 내용이 영구적으로 삭제되며 복원 작업을 취소할 수 없습니다. 복원 작업을 하는 동안 현재 웹 사이트는 일시적으로 사용할 수 없게 되며 이와 관련하여 경고 메시지를 받습니다.

    **새 웹 사이트 인스턴스**를 선택하면 지정한 이름과 동일한 지역에 새 웹 사이트가 만들어집니다. (기본적으로 새 이름은 **restored-** *oldWebSiteName*입니다.) 

    복원하는 사이트에는 원본 사이트의 포털에서 만든 것과 동일한 내용과 구성이 포함됩니다. 또한 다음 단계에서 포함하도록 선택하는 데이터베이스도 포함됩니다.

2.  웹 사이트와 함께 데이터베이스를 복원하려면 **포함된 데이터베이스**에서 **복원 대상** 아래에 있는 드롭다운을 사용하여 데이터베이스를 복원할 데이터베이스 서버의 이름을 선택합니다. 또한 복원할 데이터베이스 서버를 새로 만들도록 선택하거나 **복원 안 함**을 선택하여 기본값인 데이터베이스를 복원하지 않을 수도 있습니다.

    서버 이름을 선택한 후에는 **데이터베이스 이름** 상자에서 복원용 데이터베이스의 이름을 지정합니다.

    복원에 하나 이상의 데이터베이스가 포함되면 **연결 문자열 자동 조정**을 선택하여 백업에 저장된 연결 문자열을 새 데이터베이스 또는 데이터베이스 서버를 가리키도록 업데이트합니다. 복원이 완료된 후 데이터베이스와 관련된 모든 기능이 예상대로 작동하는지 확인해야 합니다.

    ![데이터베이스 서버 호스트 선택](./media/web-sites-restore/06ChooseDBServer.png)

    > [WACOM.NOTE] 이름이 동일한 SQL 데이터베이스를 동일한 SQL 서버로 복원할 수 없습니다. 데이터베이스를 복원하려면 다른 데이터베이스 이름이나 다른 SQL 서버 호스트 중 하나를 선택해야 합니다.

    > [WACOM.NOTE] 이름이 동일한 MySQL 데이터베이스를 동일한 서버로 복원할 수는 있지만 MySQL 데이터베이스에 저장된 기존 콘텐츠는 지워집니다.

3.  기존 데이터베이스를 복원하도록 선택한 경우에는 사용자 이름과 암호를 제공해야 합니다. 새 데이터베이스로 복원하도록 선택한 경우에는 새 데이터베이스 이름을 제공해야 합니다.

    ![새 SQL 데이터베이스로 복원](./media/web-sites-restore/07RestoreToNewSQLDB.png)

    오른쪽 화살표를 클릭하여 계속합니다.

4.  새 데이터베이스를 만들도록 선택한 경우 다음 대화 상자에서 데이터베이스에 대한 자격 증명 및 다른 초기 구성 정보를 제공해야 합니다. 아래 예제에서는 새 SQL 데이터베이스를 보여 줍니다. (새 MySQL 데이터베이스의 옵션은 약간 다릅니다.)

   	![새 SQL 데이터베이스 설정](./media/web-sites-restore/08NewSQLDBConfig.png)

5.  확인 표시를 클릭하여 복원 작업을 시작합니다. 복원 작업이 완료되면 포털의 웹 사이트 목록에 새 웹 사이트 인스턴스(선택한 복원 옵션인 경우)가 표시됩니다.

    ![복원된 Contoso 웹 사이트](./media/web-sites-restore/09RestoredContosoWebSite.png)

## 작업 로그 보기

1.  웹 사이트 복원 작업의 성공 또는 실패에 대한 세부 정보를 보려면 웹 사이트의 대시보드 탭으로 이동하십시오. **간략 상태** 섹션의 **관리 서비스**에서 **작업 로그**를 클릭합니다.

    ![대시보드 - 작업 로그 링크](./media/web-sites-restore/10DashboardOperationLogsLink.png)

2.  관리 서비스 포털 **작업 로그** 페이지로 이동되며, 이 페이지의 작업 로그 목록에서 복원 작업에 대한 로그를 볼 수 있습니다.

    ![관리 서비스 작업 로그 페이지](./media/web-sites-restore/11ManagementServicesOperationLogsList.png)

3.  작업에 대한 세부 정보를 보려면 목록에서 해당 작업을 선택한 후 명령 모음에서 **세부 정보** 단추를 클릭합니다.

    ![세부 정보 단추](./media/web-sites-restore/12DetailsButton.png)

    이렇게 하면 **작업 세부 정보** 창이 열리고 로그 파일의 복사 가능한 내용이 표시됩니다.

    ![작업 세부 정보](./media/web-sites-restore/13OperationDetails.png)


<!-- IMAGES -->
[ChooseBackupSource]: ./media/web-sites-restore/01ChooseBackupSource.png
[StorageAccountFile]: ./media/web-sites-restore/02StorageAccountFile.png
[BrowseCloudStorage]: ./media/web-sites-restore/03BrowseCloudStorage.png
[StorageAccountFileSelected]: ./media/web-sites-restore/04StorageAccountFileSelected.png
[ChooseRestoreSettings]: ./media/web-sites-restore/05ChooseRestoreSettings.png
[ChooseDBServer]: ./media/web-sites-restore/06ChooseDBServer.png
[RestoreToNewSQLDB]: ./media/web-sites-restore/07RestoreToNewSQLDB.png
[NewSQLDBConfig]: ./media/web-sites-restore/08NewSQLDBConfig.png
[RestoredContosoWebSite]: ./media/web-sites-restore/09RestoredContosoWebSite.png
[DashboardOperationLogsLink]: ./media/web-sites-restore/10DashboardOperationLogsLink.png
[ManagementServicesOperationLogsList]: ./media/web-sites-restore/11ManagementServicesOperationLogsList.png
[DetailsButton]: ./media/web-sites-restore/12DetailsButton.png
[OperationDetails]: ./media/web-sites-restore/13OperationDetails.png