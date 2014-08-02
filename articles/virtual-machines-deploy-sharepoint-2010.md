<properties linkid="manage-windows-other-resource-sharepoint-wp" urlDisplayName="SharePoint on Azure" pageTitle="SharePoint 2010 Deployment on Azure Virtual Machines" metaKeywords="" description="Understand the supported scenarios for using SharePoint 2010 on Azure virtual machines." metaCanonical="" services="virtual-machines" documentationCenter="" title="SharePoint Deployment on Azure Virtual Machines" authors="" solutions="" manager="" editor="" />

Azure 가상 컴퓨터에 SharePoint 배포
===================================

요약
----

Microsoft SharePoint Server 2010은 조직에서 비즈니스 요구와 목표에 맞춰 올바른 배포 시나리오를 결정할 수 있도록 다양한 배포 유연성을 제공합니다. 클라우드에서 호스트되고 관리되는 Azure 가상 컴퓨터는 완전하고 신뢰할 수 있으며 사용 가능한 인프라를 제공하여 다양한 주문형 응용 프로그램 및 데이터베이스 작업(예: Microsoft SQL Server 및 SharePoint 배포)을 지원합니다.

Azure 가상 컴퓨터는 여러 작업을 지원하지만 이 문서에서는 SharePoint 배포에 중점을 둡니다. Azure 가상 컴퓨터를 통해 조직은 SharePoint 인프라를 만들고 관리하여 거의 모든 호스트를 빠르게 프로비전하고 해당 호스트에 액세스할 수 있습니다. 또한 SharePoint VM(가상 컴퓨터)의 프로세서, RAM, CPU 범위 및 기타 리소스에 대해 모든 권한을 갖고 관리할 수 있습니다.

Azure 가상 컴퓨터가 하드웨어에 대한 요구를 줄여주므로 조직은 높은 선행 투자 비용과 복잡성 처리에서 적정 규모의 인프라 빌드 및 관리로 관심사를 전환할 수 있습니다. 즉, 며칠 또는 몇 주가 걸리던 기존 배포를 몇 시간 내로 혁신하고 실험해 보고 반복할 수 있습니다.

### 이 문서의 대상

이 문서는 IT 전문가를 대상으로 합니다. 또한 시스템 설계자 및 관리자와 같은 기술 관련 의제 결정권자는 이 문서의 정보와 제공된 시나리오를 사용하여 Azure에서 가상화된 SharePoint 인프라를 계획하고 설계할 수 있습니다.

### 이 문서를 읽어야 하는 이유

이 문서에서는 조직이 Azure 가상 컴퓨터 내에서 SharePoint를 설정하고 배포할 수 있는 방법을 설명합니다. 또한 이런 유형의 배포가 다양한 규모의 조직에 유용할 수 있는 이유에 대해서도 논의합니다.

클라우드 컴퓨팅으로 이동
------------------------

Gartner에 따르면 클라우드 컴퓨팅은 "인터넷 기술을 사용하여 외부 고객에게 방대하게 확장 가능한 IT 사용 기능을 '서비스로' 제공하는 컴퓨팅 스타일"로 정의할 수 있습니다. 이 정의에서 중요한 단어는 확장 가능성, 서비스 및 인터넷입니다. 요약하면 클라우드 컴퓨팅은 **인터넷을 통해 배포 및 제공되며** **주문형으로 확장 가능한** IT 서비스로 정의할 수 있습니다.

명백하게 클라우드 컴퓨팅은 오늘날 IT의 중요한 변화를 나타냅니다. 얼마 전까지만 해도 통합과 비용이 주요 관심사였습니다. 그러나 오늘날의 담론은 클라우드 컴퓨팅에서 제공할 수 있는 새로운 종류의 혜택에 대한 것입니다. 즉, IT에서 새로운 유형의 기능을 활용하여 조직에 제공하는 방법을 완전히 바꾸는 것에 대해서만 얘기합니다. 클라우드 컴퓨팅은 IT 세계를 근본적으로 변화시켜 서비스 공급자와 시스템 설계자로부터 개발자와 최종 사용자에 이르기까지 모든 역할에 영향을 주고 있습니다.

연구 결과에 따르면 기민성, 집중성 및 경제성이 클라우드 채택의 세 가지 주요 요인입니다.

-   **기민성**: 클라우드 컴퓨팅은 새로운 기회를 활용하고 비즈니스 요구의 변화에 대응하는 조직의 능력을 가속화할 수 있습니다.

-   **집중성**: 클라우드 컴퓨팅은 IT 부서가 인프라 비용을 확연하게 줄일 수 있도록 합니다. 인프라를 추출하고 리소스를 풀링하므로 IT 부서가 복잡한 서비스와 시스템 모음이 아니라 유틸리티처럼 실행됩니다. 또한 IT 부서는 더 획기적이고 전략적인 역할로 전환할 수 있습니다.

-   **경제성**: 클라우드 컴퓨팅은 IT를 제공하는 비용을 낮추고 데이터 센터의 사용률과 효율성을 높입니다. 클라우드 컴퓨팅을 통해 응용 프로그램 및 리소스가 셀프 서비스 방식이 되고 이러한 리소스의 사용이 새롭고 정확한 방식으로 측정 가능하게 되므로 배달 비용이 줄어듭니다. 또한 이제는 인프라 리소스(저장, 계산 및 네트워크)를 풀링하고 간소화하므로 하드웨어 사용률도 증가합니다.

클라우드 서비스의 배달 모델
---------------------------

간단히 말하면 클라우드 컴퓨팅은 IT 서비스를 추출한 것입니다. 이러한 서비스의 범위는 기본 인프라에서 완전한 응용 프로그램까지 다양할 수 있습니다. 최종 사용자는 추출된 서비스를 구성하는 요소를 관리할(또는 요소에 대해 완벽하게 알) 필요없이 해당 서비스를 요청하고 사용합니다. 현재 업계에서는 클라우드 서비스로 다음과 같은 세 가지 배달 모델을 인정하며, 각 모델은 제어/유연성과 총비용 측면에서 뚜렷한 장단점을 제공합니다.

-   **IaaS(Infrastructure as a Service)**: 가상 컴퓨터와 대부분의 기존 응용 프로그램을 호스트하는 가상 인프라

-   **PaaS(Platform as a Service)**: 주문형 응용 프로그램 호스팅 환경을 제공하는 클라우드 응용 프로그램 인프라

-   **SaaS(Software as a Service)**: 응용 프로그램이 인터넷을 통해 제공되며 고객은 사용량에 따라 결제하는 클라우드 서비스 모델(예: Microsoft Office 365 또는 Microsoft CRM Online)

그림 1은 클라우드 서비스 분류법 및 IT 인프라의 구성 요소에 매핑하는 방법을 보여 줍니다. 온-프레미스 모델에서 고객은 네트워크 연결부터 응용 프로그램까지 전체 스택을 관리하는 책임이 있습니다. IaaS에서는 고객이 응용 프로그램을 통해 운영 체제를 관리하는 책임이 있지만 낮은 수준의 스택은 공급업체에서 관리합니다. PaaS에서는 플랫폼 공급업체가 런타임을 통해 네트워크 연결의 모든 요소를 제공하고 관리합니다. 고객은 응용 프로그램 및 데이터만 관리하면 됩니다. Azure 제공은 이 모델에 가장 적합합니다. 마지막으로 SaaS에서는 공급업체가 응용 프로그램을 제공하며 모든 기본 구성 요소의 모든 서비스를 추출합니다.

그림 1: 클라우드 서비스 분류법

![azure-sharepoint-wp-1](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-1.png)

Azure 가상 컴퓨터
-----------------

Azure 가상 컴퓨터는 광범위한 가상 네트워킹과 함께 VM에 대해 모든 권한을 갖고 관리할 수 있는 기능을 도입했습니다. 이러한 제공은 조직에 다음과 같은 강력한 혜택을 제공할 수 있습니다.

-   **관리**: 모든 권한을 갖고 클라우드의 VM을 중앙에서 관리하여 인프라를 구성하고 관리할 수 있습니다.

-   **응용 프로그램 이동성**: VHD(가상 하드 드라이브)를 온-프레미스와 클라우드 기반 환경 간에 서로 이동할 수 있습니다. 클라우드에서 응용 프로그램을 실행하기 위해 다시 빌드하지 않아도 됩니다.

-   **Microsoft 서버 응용 프로그램에 액세스**: Microsoft SQL Server, SharePoint Server, Windows Server 및 Active Directory를 포함하여 클라우드에서 동일한 온-프레미스 응용 프로그램 및 인프라를 실행할 수 있습니다.

Azure 가상 컴퓨터는 조직에서 Windows Server 및 Linux VM을 몇 분 내에 배포하여 실행할 수 있는 간편하고 유연하며 강력한 개방형 플랫폼입니다.

-   **간편성**: Azure 가상 컴퓨터를 통해 클라우드에서 VM을 쉽고 간단하게 빌드하고 마이그레이션하고 배포하고 관리할 수 있습니다. 조직은 기존 코드를 변경하지 않고도 작업을 Azure로 마이그레이션할 수 있으며 몇 번의 클릭만으로 Azure에서 새 VM을 설정할 수 있습니다. 또한 이 제공은 Azure의 IaaS 및 PaaS 기능을 통합하여 새 클라우드 응용 프로그램 개발을 위한 지원을 제공합니다.

-   **개방성 및 유연성**: Azure는 조직에 유연성을 제공하는 개방형 플랫폼입니다. 이미지 갤러리의 미리 빌드된 이미지에서 시작하거나, 사용자 지정된 온-프레미스 VHD를 만들어 사용하고 이미지 라이브러리로 업로드할 수 있습니다. Linux의 커뮤니티 및 상용 버전도 사용할 수 있습니다.

-   **강력함**: Azure는 클라우드에서 SQL Server, SharePoint Server 또는 Active Directory와 같은 응용 프로그램을 실행하는 기업에도 적합한 클라우드 플랫폼입니다. 조직은 Azure 데이터 센터와 자체 네트워크 간 VPN 연결을 사용하여 온-프레미스와 클라우드의 하이브리드 솔루션을 만들 수 있습니다.

Azure 가상 컴퓨터의 SharePoint
------------------------------

SharePoint 2010은 Azure 가상 컴퓨터 배포의 대부분 작업을 유연하게 지원합니다. Azure 가상 컴퓨터는 FIS(인터넷 사이트용 SharePoint Server) 및 개발 시나리오에 최적입니다. 마찬가지로 핵심 SharePoint 작업도 지원합니다. 클라우드에서 가상화를 위한 옵션을 활용하는 동안 자체 SharePoint 2010 구현을 관리하고 제어하려는 경우 Azure 가상 컴퓨터가 배포에 가장 적합합니다.

Azure 가상 컴퓨터 제공은 클라우드에서 호스트되고 관리됩니다. 따라서 배포 유연성이 확보되며 하드웨어 조달에 따른 자본 지출이 줄어들어 전반적인 비용이 감소됩니다. 또한 인프라 기민성이 늘어나 SharePoint Server를 며칠 또는 몇 주가 아닌 몇 시간 내에 배포할 수 있습니다. Azure 가상 컴퓨터를 통해 "종량제" 모델을 사용하여 클라우드에서 SharePoint 작업을 배포할 수도 있습니다. SharePoint 작업이 증가함에 따라 인프라를 빠르게 확장할 수 있으며, 이후 컴퓨팅 요구가 줄어들 경우 더 이상 필요하지 않은 리소스를 반환할 수 있으므로 사용하고 있는 리소스에 대해서만 결제할 수 있습니다.

### IT 중점의 이동

대부분의 조직은 SharePoint Server와 같은 중요 업무용 응용 프로그램의 제어를 유지 관리하면서 하드웨어, 운영 체제, 보안, 데이터 저장소 및 백업과 같은 IT 인프라 및 관리의 일반 구성 요소는 외부 업체에 맡기는 계약을 합니다. IT 플랫폼의 중요하지 않은 모든 업무용 서비스 계층을 가상 공급자에게 위임함으로써 핵심적인 중요 업무용 SharePoint 서비스로 IT 중점을 이동하여 인프라를 설정하는 데 많은 시간을 소비하는 대신 SharePoint 프로젝트로 비즈니스 가치를 제공할 수 있습니다.

### 신속한 배포

대규모 SharePoint 인프라를 지원하고 배포하는 작업은 비즈니스 요구 사항을 지원하기 위해 빠르게 이동하는 IT 기능을 제한할 수 있습니다. SharePoint 서버 및 팜을 빌드, 테스트 및 준비하고 프로덕션 환경에 배포하는 데 필요한 시간은 조직의 프로세스 및 제약 조건에 따라 몇 주 또는 심지어 몇 개월이 걸릴 수 있습니다. Azure 가상 컴퓨터를 통해 조직은 하드웨어에 대한 자본 지출 없이 SharePoint 작업을 빠르게 배포할 수 있습니다. 이런 방식으로 인프라 기민성을 활용하여 며칠 또는 몇 주가 아니라 몇 시간 내에 배포할 수 있습니다.

### 확장성

조직은 실제 SharePoint 서버 및 팜을 배포, 테스트 및 준비할 필요없이 컴퓨팅 용량을 즉시 주문형으로 확장하고 계약할 수 있습니다. SharePoint 작업 요구 사항이 증가함에 따라 클라우드에서 인프라를 빠르게 확장할 수 있습니다. 마찬가지로 컴퓨팅 요구가 감소하는 경우 리소스를 줄이고 사용하는 리소스에 대해서만 결제할 수 있습니다. Azure 가상 컴퓨터가 선행 투자 경비와 장기간 비용 투입을 줄여주므로 SharePoint 인프라를 적절한 규모로 빌드하고 관리할 수 있습니다. 다시 한 번 말하자면, 조직은 며칠 또는 몇 주가 아니라 몇 시간 내에 기존 배포를 혁신하고 실험해 보고 반복할 수 있습니다.

### 요금제 사용

Azure 가상 컴퓨터는 일반적으로 리소스 사용에 따라 가격이 책정되는 SharePoint 시나리오의 컴퓨팅 기능, 메모리 및 저장소를 제공합니다. 조직은 사용하는 항목에 대해서만 결제하며, 서비스는 SharePoint 인프라를 실행하는 데 필요한 모든 용량을 제공합니다. 가격 책정 및 대금 청구에 대한 자세한 내용을 보려면 [Azure 가격 책정 세부 정보](/en-us/pricing/details/)로 이동하십시오. 온-프레미스 네트워크에서 Azure 클라우드 외부로 이동하는 저장소 및 데이터에 대해서는 명목상의 요금만 청구합니다. 그러나 데이터 업로드 요금은 청구하지 않습니다.

### 유연성

Azure 가상 컴퓨터는 .NET, Node.js, Java 및 PHP에 대한 공식적인 지원을 통해 개발자에게 원하는 언어 또는 런타임 환경을 선택할 수 있는 유연성을 제공합니다. 또한 개발자는 Microsoft Visual Studio, WebMatrix, Eclipse 및 텍스트 편집기를 지원하는 자신의 도구를 선택할 수 있습니다. 더 나아가 Microsoft는 장치 및 위치를 통해 BI(비즈니스 인텔리전스)에 대한 액세스를 제공하여 클라우드에 저비용, 저위험 경로를 제공하며 클라우드 보고 요구를 위해 비용 효율적인 간편한 프로비전 및 배포를 지원합니다. 마지막으로 Azure 제공을 통해 사용자는 적절한 라이선스를 보유하고 있는 한 VHD를 클라우드로 이동할 수 있으며 VHD를 다시 복사하여 로컬에서 또는 다른 클라우드 공급자를 통해 실행할 수 있습니다.

프로비전 프로세스
-----------------

이 하위 섹션에서는 Azure의 기본적인 강점에 대해 논의합니다. Azure의 **이미지 갤러리**는 미리 구성된 사용 가능한 VM의 목록을 제공합니다. 사용자는 SharePoint Server, SQL Server, Windows Server 및 기타 ISO/VHD를 이미지 갤러리에 게시할 수 있습니다. VM 만들기를 간소화하기 위해 기본 이미지를 만들어 라이브러리에 게시했습니다. 권한 있는 사용자는 이러한 이미지를 사용하여 원하는 VM을 생성할 수 있습니다. 자세한 내용을 보려면 Azure 사이트의 [Windows Server 2008 R2를 실행하는 가상 컴퓨터 만들기](/en-us/manage/windows/tutorials/virtual-machine-from-gallery/)로 이동하십시오. 그림 2는 Azure 관리 포털에서 VM을 만드는 기본 단계를 보여 줍니다.

그림 2: VM을 만드는 단계 개요

![azure-sharepoint-wp-2](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-2.png)

사용자는 Azure 관리 포털에서 Sysprep을 실행한 이미지를 업로드할 수도 있습니다. 자세한 내용을 보려면 [가상 하드 디스크 만들기 및 업로드](/en-us/manage/windows/common-tasks/upload-a-vhd/)로 이동하십시오. 그림 3은 VM을 만들 이미지를 업로드하는 기본 단계를 보여 줍니다.

그림 3: 이미지 업로드 단계 개요

![azure-sharepoint-wp-3](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-3.png)

### Azure에서 SharePoint 2010 배포

다음 단계에 따라 Azure에서 SharePoint 2010을 배포할 수 있습니다.

1.  계정을 사용하여 [Azure 관리 포털](http://manage.windowsazure.com/)에 로그온합니다.
    -   Azure 계정이 없는 경우 [Azure 무료 평가판에 등록합니다](http://www.windowsazure.com/en-us/pricing/free-trial/).

2.  기본 운영 체제가 있는 VM 만들기: Azure 관리 포털에서 **+새로 만들기**를 클릭하고 **가상 컴퓨터**를 클릭한 다음 **갤러리에서**를 클릭합니다. ![azure-sharepoint-wp-4](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-4.png)
3.  **VM OS 선택** 대화 상자가 나타납니다. **플랫폼 이미지**를 클릭하고 **Windows Server 2008 R2 SP1** 플랫폼 이미지를 선택합니다. ![azure-sharepoint-wp-5](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-5.png)
4.  ***VM 구성*** 대화 상자가 나타납니다. 다음 정보를 지정합니다.
    -   **가상 컴퓨터 이름**을 입력합니다.
        -   이 가상 컴퓨터 이름은 전 세계적으로 고유해야 합니다.
    -   **새 사용자 이름** 상자의 Administrator를 그대로 둡니다.
    -   **새 암호** 상자에 강력한 암호를 입력합니다.
    -   **암호 확인** 상자에 암호를 다시 입력합니다.
    -   해당 **크기**를 선택합니다.
        -   프로덕션 환경(SharePoint 응용 프로그램 서버 및 데이터베이스)의 경우 대규모*(4 코어, 7GB 메모리)*를 사용하는 것이 좋습니다.

    ![azure-sharepoint-wp-6](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-6.png)
5.  ***VM 모드*** 대화 상자가 나타납니다. 다음 정보를 지정합니다.
    -   독립 실행형 가상 컴퓨터를 선택합니다.
    -   **DNS 이름** 상자에 선택한 DNS 이름의 첫 번째 부분을 입력합니다.
        -   이 부분이 MyService1.cloudapp.net 형식의 이름을 완성합니다.
    -   **저장소 계정** 상자에서 다음 중 하나를 선택합니다.
        -   VHD 파일이 저장된 저장소 계정을 선택합니다.
        -   저장소 계정을 자동으로 만들도록 선택합니다.
            -   영역당 하나의 저장소 계정만 자동으로 만들어집니다. 이 설정으로 만든 다른 모든 VM이 이 저장소 계정에 저장됩니다.
            -   20개의 저장소 계정으로 제한됩니다.
            -   자세한 내용을 보려면 [Azure에서 저장소 계정 만들기](/en-us/manage/windows/common-tasks/upload-a-vhd/#createstorage)로 이동하십시오.
    -   **지역/선호도 그룹/가상 네트워크** 상자에서 가상 이미지를 호스트할 영역을 선택합니다.

    ![azure-sharepoint-wp-7](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-7.png)
6.  ***VM 옵션*** 대화 상자가 나타납니다. 다음 정보를 지정합니다.
    -   **가용성 집합** 상자에서 (없음)을 선택합니다.
    -   약관을 읽고 동의합니다.
    -   확인 표시를 클릭하여 VM을 만듭니다.

    ![azure-sharepoint-wp-8](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-8.png)
7.  ***VM 인스턴스*** 페이지가 나타납니다. VM이 만들어졌는지 확인합니다. ![azure-sharepoint-wp-9](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-9.png)
8.  다음과 같이 VM 설정을 완료합니다.
    -   원격 데스크톱을 사용하여 VM을 엽니다.
    -   Azure 관리 포털에서 VM을 선택한 후 **대시보드** 페이지를 선택합니다.
    -   **연결**을 클릭합니다.

    ![azure-sharepoint-wp-10](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-10.png)
9.  다음 옵션 중 하나를 사용하여 SQL Server VM을 빌드합니다.
    -   위의 1 - 7단계에 따라(**3단계**는 예외) SQL Server 2012 VM을 만들어 Windows Server 2008 R2 SP1 이미지 대신 SQL Server 2012 이미지를 사용합니다. 자세한 내용을 보려면 [Azure에서 SQL Server 가상 컴퓨터 프로비전](/en-us/manage/windows/common-tasks/install-sql-server/)으로 이동하십시오.
        -   이 옵션을 선택한 경우 설치를 사용자 지정할 수 있도록 프로비전 프로세스에서 SQL Server 2012 설치 파일의 복사본을 *C:\\SQLServer\_11.0\_Full* 디렉터리 경로에 저장합니다. 예를 들어 SQL Server 2012의 평가판 설치를, 라이선스 키를 사용하여 사용이 허가된 버전으로 변환할 수 있습니다.
    -   SQL Server 시스템 준비 도구(SysPrep)를 사용하여 기본 운영 체제가 있는 VM(위의 1 - 7단계)에 SQL Server를 설치합니다. 자세한 내용을 보려면 [SysPrep을 사용하여 SQL Server 2012 설치](http://msdn.microsoft.com/en-us/library/ee210664.aspx)로 이동하십시오.
    -   명령 프롬프트를 사용하여 SQL Server를 설치합니다. 자세한 내용을 보려면 [명령 프롬프트에서 SQL Server 2012 설치](http://msdn.microsoft.com/en-us/library/ms144259.aspx#SysPrep)로 이동하십시오.
    -   지원되는 SQL Server 미디어와 라이선스 키를 사용하여 기본 운영 체제가 있는 VM(위의 1 - 7단계)에 SQL Server를 설치합니다.

10. 다음 하위 단계를 사용하여 SharePoint 팜을 빌드합니다.
    -   하위 단계 1: 스크립트 파일을 사용하여 Azure 구독을 구성합니다.
    -   하위 단계 2: 기본 운영 체제가 있는 다른 VM을 만들어(위의 1 - 7단계) SharePoint 서버를 프로비전합니다. 이 VM에서 SharePoint 서버를 빌드하려면 다음 옵션 중 하나를 선택합니다.
        -   SharePoint GUI를 사용하여 프로비전:
            -   SharePoint 팜을 만들어 프로비전하려면 [Microsoft SharePoint Server 팜 만들기](http://technet.microsoft.com/en-us/library/ee805948.aspx#CreateConfigure)로 이동하십시오.
            -   웹 서버 또는 응용 프로그램 서버를 팜에 추가하려면 [팜(SharePoint Server 2010)에 웹 서버 또는 응용 프로그램 서버 추가](http://technet.microsoft.com/en-us/library/cc261752.aspx)로 이동하십시오.
            -   데이터베이스 서버를 기존 팜에 추가하려면 [기존 팜에 데이터베이스 서버 추가](http://technet.microsoft.com/en-us/library/cc262781)로 이동하십시오.

                -   SharePoint 팜에 SQL Server 2012를 사용하려면 응용 프로그램을 설치하고 서버를 구성하지 않도록 선택한 후 SharePoint Server 2010 서비스 팩 1을 다운로드하여 설치해야 합니다. 자세한 내용을 보려면 [SharePoint Server 2010 서비스 팩 1](http://www.microsoft.com/en-us/download/details.aspx?id=26623)로 이동하십시오.
                -   SQL Server BI 기능을 활용하려면 독립 실행형 서버 대신 서버 팜으로 SharePoint Server를 설치하는 것이 좋습니다. 자세한 내용을 보려면 [SQL Server 2012 비즈니스 인텔리전스 기능 설치](http://technet.microsoft.com/en-us/library/hh231681(v=sql.110).aspx)로 이동하십시오.

        -   Microsoft Windows PowerShell을 사용하여 프로비전: Psconfig 명령줄 도구를 대체 인터페이스로 사용하여 SharePoint 2010 제품을 프로비전하는 방법을 제어하는 여러 작업을 수행할 수 있습니다. 자세한 내용을 보려면 [Psconfig 명령줄 참조](http://technet.microsoft.com/en-us/library/cc263093.aspx)로 이동하십시오.
    -   하위 단계 3: SharePoint를 구성합니다. 각 SharePoint VM이 준비 상태에 있으면 다음 옵션 중 하나를 사용하여 각 서버에서 SharePoint Server를 구성합니다.
        -   GUI에서 SharePoint를 구성합니다.
        -   Windows PowerShell을 사용하여 SharePoint를 구성합니다. 자세한 내용을 보려면 [Windows PowerShell을 사용하여 SharePoint Server 2010 설치](http://technet.microsoft.com/en-us/library/cc262839.aspx)로 이동하십시오.
            -   또한 Windows PowerShell 스크립트, XML 입력 파일 및 Microsoft Windows 표준 배치 파일로 구성된 CodePlex 프로젝트의 AutoSPInstaller를 사용할 수도 있습니다. AutoSPInstaller는 Windows PowerShell을 기반으로 하여 SharePoint 2010 설치 스크립트용 프레임워크를 제공합니다. 자세한 내용을 보려면 [CodePlex: AutoSPInstaller](http://autospinstaller.codeplex.com/)(영문)로 이동하십시오. **참고**: 관리 포털 끝점에서 보안을 구성하고 VM의 Windows 방화벽에 인바운드 포트를 설정하십시오. 그런 다음 관리자 자격 증명으로 Windows PowerShell 세션을 열어서 SharePoint 응용 프로그램 서버 중 하나에 대해 원격 Windows PowerShell 세션을 시작할 수 있는지 확인합니다.

11. 스크립트가 완료되면 VM 대시보드를 사용하여 VM에 연결합니다.
12. SharePoint 구성 확인: SharePoint 서버에 로그온한 후 중앙 관리를 사용하여 구성을 확인합니다.

### 가상 하드 디스크 만들기 및 업로드

또한 고유한 이미지를 만들어 Azure에 VHD 파일로 업로드할 수도 있습니다. Azure에서 VHD 파일을 만들어 업로드하려면 다음 단계를 따르십시오.

1.  Hyper-V 사용 이미지 만들기: Hyper-V 관리자를 사용하여 Hyper-V 사용 VHD를 만듭니다. 자세한 내용을 보려면 [가상 하드 디스크 만들기](http://technet.microsoft.com/en-us/library/cc742509)로 이동하십시오.
2.  Azure에서 저장소 계정 만들기: VM을 만드는 데 사용할 수 있는 VHD 파일을 업로드하려면 Azure 저장소 계정이 필요합니다. 이 계정은 Azure 관리 포털을 사용하여 만들 수 있습니다. 자세한 내용을 보려면 [Azure에서 저장소 계정 만들기](/en-us/manage/windows/common-tasks/upload-a-vhd/)로 이동하십시오.
3.  업로드할 이미지 준비: Azure에 이미지를 업로드하려면 먼저 SysPrep 명령을 사용하여 범용화해야 합니다. 자세한 내용을 보려면 [SysPrep의 사용법: 소개](http://technet.microsoft.com/en-us/library/bb457073.aspx)(영문)를 참조하십시오.
4.  Azure에 이미지 업로드: VHD 파일에 포함된 이미지를 업로드하려면 관리 인증서를 만들고 설치해야 합니다. 인증서의 지문 및 구독 ID를 확보합니다. 연결을 설정하고 CSUpload 명령줄 도구를 사용하여 VHD 파일을 업로드합니다. 자세한 내용을 보려면 [Azure에 이미지 업로드](/en-us/manage/windows/common-tasks/upload-a-vhd/)로 이동하십시오.

사용 시나리오
-------------

이 섹션에서는 Azure 가상 컴퓨터를 사용하여 SharePoint를 배포하는 주요 고객 시나리오에 대해 논의합니다. 각 시나리오는 시나리오에 대한 간략한 설명과 시작 단계의 두 부분으로 구성되어 있습니다.

### 시나리오 1: 단순한 SharePoint 개발 및 테스트 환경

#### 설명

조직들은 더욱 민첩하게 SharePoint 응용 프로그램을 만들고 국내/해외 개발 및 테스트를 위한 SharePoint 환경을 설정하는 방법을 찾고 있습니다. 기본적으로는 SharePoint 응용 프로그램 개발 프로젝트를 설정하는 데 필요한 시간을 단축하고, 자체 테스트 환경을 더 많이 사용하여 비용을 절감하고자 합니다. 예를 들어 조직에서 SharePoint Server에서 주문형 부하 테스트를 수행하고 여러 지역에 더 많은 동시 사용자를 분산한 상태로 UAT(사용자 수용 테스트)를 실행할 수 있습니다. 마찬가지로 국내 팀과 해외 팀의 통합은 현재 대부분의 조직에서 증가하고 있는 중요한 비즈니스 요구입니다.

이 시나리오에서는 조직이 개발 및 테스트 작업에 미리 구성된 SharePoint 팜을 사용하는 방법에 대해 설명합니다. SharePoint 배포 토폴로지는 온-프레미스에서 배포를 가상화한 것과 같은 모양과 느낌입니다. 기존 IT 기술이 1:1 배포를 Azure 가상 컴퓨터 배포로 변환하면서 거의 모든 비용이 자본 지출에서 운영 지출로 전환되는 혜택을 누릴 수 있습니다. 즉, 물리적 서버 구매에 비용을 선행 투자할 필요가 없습니다. 조직은 서버 하드웨어에 대한 자본 비용을 없앨 수 있으며, 테스트 및 개발 환경을 위해 SharePoint 팜을 만들거나 설정하거나 확장하는 데 필요한 프로비전 시간을 크게 줄여 유연성을 얻을 수 있습니다. IT 부서에서는 용량을 동적으로 추가하고 제거하여 테스트 및 개발의 변화하는 요구를 지원할 수 있습니다. 또한 SharePoint 프로젝트로 비즈니스 가치를 전달하는 데 더 집중하고 인프라를 관리하는 데에는 덜 집중할 수 있습니다.

부하 테스트 컴퓨터를 완벽하게 활용하기 위해 조직은 Azure에서 Windows Sever 2008 R2에 대한 운영 체제 지원으로 SharePoint가 가상화된 개발 및 테스트 컴퓨터를 구성할 수 있습니다. 이를 통해 개발 팀은 응용 프로그램을 만들고 테스트할 수 있으며, 코드 변경 없이 온-프레미스 또는 클라우드 프로덕션 환경으로 쉽게 마이그레이션할 수 있습니다. 온-프레미스 및 클라우드에서 동일한 프레임워크 및 도구 집합을 사용할 수 있으므로 분산된 팀에서 동일한 환경에 액세스할 수 있습니다. 또한 사용자는 VPN 직접 연결을 설정하여 온-프레미스 데이터 및 응용 프로그램에 액세스할 수 있습니다.

#### 시작하기

그림 4는 Azure VM의 SharePoint 개발 및 테스트 환경을 보여 줍니다. 이 배포를 빌드하려면 먼저 응용 프로그램을 개발하는 데 사용한 것과 동일한 온-프레미스 SharePoint 개발 및 테스트 환경을 사용합니다. 그런 다음 테스트 및 개발을 위한 Azure VM에 응용 프로그램을 업로드하여 배포합니다. 조직에서 응용 프로그램을 다시 온-프레미스로 이동하기로 한 경우 응용 프로그램을 수정하지 않고 이동할 수 있습니다.

그림 4: Azure 가상 컴퓨터의 SharePoint 개발 및 테스트 환경

![azure-sharepoint-wp-11](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-11.png)

#### 시나리오 환경 설정

Azure에서 SharePoint 개발 및 테스트 환경을 구현하려면 다음 단계를 따르십시오.

1.  *프로비전*: 먼저, Azure 가상 네트워크를 사용하여 온-프레미스와 Azure 간의 VPN 연결을 프로비전합니다. 여기에서는 Active Directory를 사용하지 않으므로 VPN 터널이 필요합니다. 자세한 내용을 보려면 [Azure 가상 네트워크(디자인 고려 사항 및 보안 연결 시나리오)](http://msdn.microsoft.com/en-us/library/windowsazure/jj156007.aspx)로 이동하십시오. 그런 다음 관리 포털을 사용하여 이미지 라이브러리의 기존 이미지를 사용해 새 VM을 프로비전합니다.
    -   온-프레미스 SharePoint 개발 및 테스트 VM을 Azure 저장소 계정으로 업로드하고, 이미지 라이브러리를 통해 해당 VM을 참조하여 필요한 환경을 빌드할 수 있습니다.
    -   Windows Server 2008 R2 SP1 이미지 대신 SQL Server 2012 이미지를 사용할 수 있습니다. 자세한 내용을 보려면 [Azure에서 SQL Server 가상 컴퓨터 프로비전](/en-us/manage/windows/common-tasks/install-sql-server/)으로 이동하십시오.

2.  *설치*: 원격 데스크톱 연결을 사용하여 VM에 SharePoint Server, Visual Studio 및 SQL Server를 설치합니다.
    -   SharePoint Server 설치 옵션을 다음과 같이 선택합니다.
        -   SharePoint 2010 간편 설치 스크립트를 사용하여 SharePoint 개발자 컴퓨터를 빌드합니다. 자세한 내용을 보려면 [SharePoint 2010 간편 설치 스크립트](http://www.microsoft.com/en-us/download/details.aspx?id=23415)(영문)로 이동하십시오.
        -   Windows PowerShell을 사용합니다. 자세한 내용을 보려면 [Windows PowerShell을 사용하여 SharePoint Server 2010 설치](http://technet.microsoft.com/en-us/library/cc262839.aspx)로 이동하십시오.
        -   CodePlex 프로젝트의 AutoSPInstaller를 사용합니다. 자세한 내용을 보려면 [CodePlex: AutoSPInstaller](http://autospinstaller.codeplex.com/)(영문)로 이동하십시오.
    -   Visual Studio를 설치합니다. 자세한 내용을 보려면 [Visual Studio 설치](http://msdn.microsoft.com/en-us/library/e2h7fzkw.aspx)로 이동하십시오.
    -   SQL Server를 설치합니다. 자세한 내용을 보려면 [SysPrep을 사용하여 SQL Server 설치](http://msdn.microsoft.com/en-us/library/ee210664.aspx)로 이동하십시오.
        -   SharePoint 팜 배포를 위해 SQL Server 2012를 만들어 구성하는 과정에 대해서는 실습 교육 [Azure에서 SharePoint에 대해 SQL Server 2012 구성](https://github.com/WindowsAzure-TrainingKit/HOL-DeployingSQLServerForSharePoint)(영문)을 참조하십시오.
        -   Active Directory를 구성하고 단일 SQL Server 데이터베이스를 사용하여 SharePoint 팜을 만드는 과정에 대해서는 실습 교육 [Azure 가상 컴퓨터로 SharePoint 팜 배포](https://github.com/WindowsAzure-TrainingKit/HOL-DeploySharePointVMs)(영문)를 참조하십시오.

3.  *응용 프로그램 및 데이터베이스용 배포 패키지 및 스크립트 개발*: 이미지 라이브러리에서 사용 가능한 VM을 사용하려는 경우 Azure 가상 컴퓨터에서 필요한 온-프레미스 응용 프로그램 및 데이터베이스를 배포할 수 있습니다.
    -   SQL Server Data Tools 및 Visual Studio를 사용하여 기존 온-프레미스 응용 프로그램 및 데이터베이스용 배포 패키지를 만듭니다.
    -   이러한 패키지를 사용하여 Azure 가상 컴퓨터에서 응용 프로그램 및 데이터베이스를 배포합니다.

4.  *SharePoint 응용 프로그램 및 데이터베이스 배포*:
    -   관리 포털 끝점에 보안을 구성하고 VM의 Windows 방화벽에 인바운드 포트를 설정합니다.
    -   3단계에서 만든 배포 패키지 및 스크립트를 사용하여 SharePoint 응용 프로그램 및 데이터베이스를 Azure 가상 컴퓨터에 배포합니다.
    -   배포한 응용 프로그램 및 데이터베이스를 테스트합니다.

5.  *VM 관리*:
    -   관리 포털을 사용하여 VM을 모니터링합니다.
    -   Visual Studio 및 SQL Server Management Studio를 사용하여 응용 프로그램을 모니터링합니다.
    -   Microsoft System Center - Operations Manager와 같은 온-프레미스 관리 소프트웨어를 사용하여 VM을 모니터링하고 관리할 수도 있습니다.

### 시나리오 2: 사용자 지정 공용 SharePoint 팜

#### 설명

조직들은 보통 클라우드에서 호스트되며 요구 및 수요에 따라 쉽게 확장할 수 있는 인터넷을 만들려고 합니다. 또한 공동 작업을 위해 파트너 엑스트라넷 웹 사이트를 만들고 웹 사이트 콘텐츠의 분산된 제작 및 승인을 위한 간편한 프로세스도 구현하려고 합니다. 마지막으로 이러한 조직들은 증가하는 로드를 처리하기 위해 웹 사이트에 주문형 용량을 제공하길 원합니다.

이 시나리오에서는 공용 웹 사이트를 호스트하기 위한 기반으로 SharePoint Server를 사용합니다. 이를 통해 조직은 안전하고 확장 가능한 클라우드 인프라에서 기업 웹 사이트를 빠르게 배포하고 사용자 지정하고 호스트할 수 있습니다. 조직은 Azure의 SharePoint 공용 웹 사이트를 사용하여 트래픽 증가에 맞춰 확장하고 사용하는 리소스에 대해서만 결제할 수 있습니다. 또한 Azure의 SharePoint를 사용하여 콘텐츠 제작, 워크플로 및 승인에 온-프레미스에서 사용하는 것과 비슷한 일반 도구를 사용할 수 있습니다.

더 나아가 Azure 가상 컴퓨터를 사용하는 조직은 VM에서 실행되는 스테이징 환경 및 프로덕션 환경을 쉽게 구성할 수 있습니다. Azure에서 만든 SharePoint 공용 VM을 가상 저장소로 백업할 수 있습니다. 또한 "지역에서 연속 복제" 기능을 통해 조직은 재해 복구를 위해 한 데이터 센터의 VM 작업을 멀리 떨어진 다른 데이터 센터로 자동으로 백업할 수 있습니다. 지역에서 복제에 대한 자세한 내용을 보려면 [Azure 저장소의 지역에서 복제 소개](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/introducing-geo-replication-for-windows-azure-storage.aspx)(영문)로 이동하십시오.

SQL Server 및 SharePoint Server와 같은 다른 Microsoft 제품으로 작업할 수 있도록 Azure 인프라의 VM을 확인하고 지원합니다. Azure와 SharePoint Server를 함께 사용하면 더 좋습니다. 둘 다 Microsoft 제품군의 일부이며 최적의 환경을 제공하도록 함께 철저히 통합, 지원 및 테스트되었기 때문입니다. 둘 다 SharePoint 응용 프로그램 및 Azure 인프라 지원을 위한 단일 지점이 있습니다.

#### 시작하기

이 시나리오에서는 추가 트래픽을 지원하기 위해 SharePoint Server용 프런트 엔드 서버를 여러 대 추가해야 합니다. 사용자 인증 및 권한 부여를 지원하려면 이러한 서버에 강화된 보안 및 Active Directory 도메인 서비스 도메인 컨트롤러가 필요합니다. 그림 5는 이 시나리오의 레이아웃을 보여 줍니다.

그림 5: 사용자 지정 공용 SharePoint 팜

![azure-sharepoint-wp-12](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-12.png)

#### 시나리오 환경 설정

Azure에서 공용 SharePoint 팜을 구현하려면 다음 단계를 따르십시오.

1.  *Active Directory 배포*: Azure 가상 컴퓨터에서 Active Directory를 배포하기 위해 충족해야 하는 기본 요구 사항은 온-프레미스 VM(및 어느 정도까지는 물리적 컴퓨터)에서 배포하는 경우와 유사하지만 "동일하지는 않습니다". 차이점, 지침 및 기타 고려 사항에 대한 자세한 내용을 보려면 [Azure 가상 컴퓨터에 Active Directory를 배포하기 위한 지침](http://msdn.microsoft.com/en-us/library/windowsazure/jj156090)으로 이동하십시오. Azure에서 Active Directory를 배포하려면 다음을 수행합니다.
    -   VM을 특정 서브넷에 할당할 수 있는 가상 네트워크를 정의하고 만듭니다. 자세한 내용을 보려면 [가상 네트워킹 구성](https://github.com/WindowsAzure-TrainingKit/HOL-DeployingActiveDirectory/blob/master/HOL.md)(영문)으로 이동하십시오.
    -   Azure에서 관리 포털을 사용하여 새 VM에서 도메인 컨트롤러를 만들고 배포합니다. 자세한 내용을 보려면 [도메인 컨트롤러 만들기 및 배포](https://github.com/WindowsAzure-TrainingKit/HOL-DeployingActiveDirectory/blob/master/HOL.md)(영문)로 이동하십시오.
        -   또한 Azure 가상 컴퓨터 및 가상 네트워크를 사용하는 클라우드에서 Windows PowerShell 스크립트를 참조하여 독립 실행형 도메인을 배포할 수도 있습니다. 자세한 내용을 보려면 [Azure에서 Active Directory 배포(Windows PowerShell)](https://github.com/WindowsAzure-TrainingKit/HOL-DeployingActiveDirectoryPS)(영문)로 이동하십시오.
        -   Azure 가상 네트워크의 VM에서 새 Active Directory 포리스트를 만드는 방법에 대한 자세한 내용을 보려면 [Azure에서 새 Active Directory 포리스트 설치](/en-us/manage/services/networking/active-directory-forest/)(영문)로 이동하십시오.

2.  *VM 프로비전*: 관리 포털을 사용하여 이미지 라이브러리의 기존 이미지에서 새 VM을 프로비전합니다.
3.  *SharePoint 팜 배포*:
    -   새로 프로비전한 VM을 사용하여 SharePoint를 설치하고 재사용 가능 이미지를 생성합니다. SharePoint Server 설치에 대한 자세한 내용을 보려면 [Windows PowerShell을 사용하여 SharePoint Server 2010 설치 및 구성](http://technet.microsoft.com/en-us/library/cc262839.aspx) 또는 [CodePlex: AutoSPInstaller](http://autospinstaller.codeplex.com/)(영문)로 이동하십시오.
    -   SharePoint VM을 구성하여 SharePoint 팜을 만들고 연결합니다.
    -   관리 포털을 사용하여 부하 분산을 구성합니다.
        -   VM 끝점을 구성하고 기존 끝점에서 트래픽 부하를 분산하는 옵션을 선택한 후 부하 분산된 VM의 이름을 지정합니다.
        -   다른 프런트 엔드 웹 VM을 추가 트래픽을 위해 기존 SharePoint 팜에 추가합니다.

4.  *VM 관리*:
    -   관리 포털을 사용하여 VM을 모니터링합니다.
    -   중앙 관리를 사용하여 SharePoint 팜을 모니터링합니다.

### 시나리오 3: 추가 BI 서비스를 위해 확장된 팜

#### 설명

비즈니스 인텔리전스는 핵심 내용을 이해하고 타당한 결정을 빠르게 내리는 데 매우 중요합니다. 조직은 온-프레미스 접근 방식에서 전환할 때 기존 BI 응용 프로그램을 클라우드에 배포하는 동안 BI 환경의 변화를 원하지 않습니다. 또한 유지 관리에 많은 시간과 예산을 사용하지 않으면서 BI 응용 프로그램에 대해 모든 권한을 유지한 상태로 지속성 및 가용성이 뛰어난 환경에서 SSAS(SQL Server Analysis Services) 또는 SSRS(SQL Server Reporting Services)의 보고서를 호스트하려고 합니다.

이 시나리오에서는 조직이 Azure 가상 컴퓨터를 사용하여 중요 업무용 BI 응용 프로그램을 호스트하는 방법에 대해 설명합니다. 조직은 Azure 가상 컴퓨터에서 SharePoint 팜을 배포하고 응용 프로그램 서버 VM의 BI 구성 요소(예: SSRS 또는 Excel Services)를 확장할 수 있습니다. 또한 클라우드에서 리소스를 많이 사용하는 구성 요소를 확장하여 특수한 작업을 훨씬 효율적으로 쉽게 지원할 수 있습니다. 작은 규모의 설치에서 매우 큰 규모의 설치에 이르기까지 SQL Server 인스턴스를 쉽게 확장할 수 있으므로 Azure 가상 컴퓨터의 SQL Server는 뛰어난 성능을 제공합니다. 따라서 즉각적인 작업 요구 사항에 따라 BI 인스턴스를 동적으로 프로비전(확장)하거나 프로비전을 해제(축소)할 수 있는 탄력성을 얻을 수 있습니다.

기존 BI 응용 프로그램을 Azure로 마이그레이션하면 규모를 더욱 쉽게 조정할 수 있습니다. 조직은 SSAS, SSRS 및 SharePoint Server의 기능을 사용하여 확장 또는 축소되는 강력한 BI 및 보고 응용 프로그램과 대시보드를 만들 수 있습니다. 또한 이러한 응용 프로그램 및 대시보드는 온-프레미스 데이터 및 응용 프로그램과 더욱 안전하게 통합될 수 있습니다. Azure는 데이터 센터가 ISO 27001에 대한 지원을 준수하도록 합니다. 자세한 내용을 보려면 [Azure 보안 센터](/en-us/support/trust-center/compliance/)로 이동하십시오.

#### 시작하기

BI 구성 요소 배포를 확장하려면 PowerPivot, Power View, Excel Services 또는 PerformancePoint Services와 같은 서비스를 제공하는 새 응용 프로그램 서버를 설치해야 합니다. 또는 추가적인 쿼리 처리를 지원하기 위해 SSAS 또는 SSRS와 같은 SQL Server BI 인스턴스를 기존 팜에 추가해야 합니다. SharePoint 2010 Server 또는 SQL Server를 설치한 새 Azure VM으로 서버를 추가할 수 있습니다. 그런 다음 해당 서버에서 BI 구성 요소를 설치하고 배포하고 구성할 수 있습니다(그림 6).

그림 6: 추가 BI 서비스를 위해 확장된 SharePoint

![azure-sharepoint-wp-13](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-13.png)

#### 시나리오 환경 설정

Azure에서 BI 환경을 확장하려면 다음 단계를 따르십시오.

1.  *프로비전*:
    -   Azure 가상 네트워크를 사용하여 온-프레미스와 Azure 간 VPN 연결을 프로비전합니다. 자세한 내용을 보려면 [Azure 가상 네트워크(디자인 고려 사항 및 보안 연결 시나리오)](http://msdn.microsoft.com/en-us/library/windowsazure/jj156007.aspx)로 이동하십시오.
    -   관리 포털을 사용하여 이미지 라이브러리의 기존 이미지에서 새 VM을 프로비전합니다.
        -   SharePoint Server 또는 SQL Server BI 작업 이미지를 이미지 갤러리에 업로드할 수 있으며, 권한 있는 사용자는 해당 BI 구성 요소 VM을 선택하여 확장된 환경을 빌드할 수 있습니다.

2.  *설치*: 조직에 SharePoint Server 또는 SQL Server BI 구성 요소의 미리 빌드된 이미지가 없는 경우 원격 데스크톱 연결을 사용하여 SharePoint Server 및 SQL Server를 VM에 설치합니다.
    -   SharePoint 설치에 대한 자세한 내용을 보려면 [Windows PowerShell을 사용하여 SharePoint Server 2010 설치](http://technet.microsoft.com/en-us/library/cc262839.aspx) 또는 [CodePlex: AutoSPInstaller](http://autospinstaller.codeplex.com/)(영문)로 이동하십시오.
    -   SQL Server를 설치하는 방법에 대한 자세한 내용을 보려면 [SysPrep을 사용하여 SQL Server 설치](http://msdn.microsoft.com/en-us/library/ee210664.aspx)로 이동하십시오.
    -   SharePoint 팜 배포를 위해 SQL Server 2012를 만들어 구성하는 과정에 대해서는 실습 교육 [Azure에서 SharePoint에 대해 SQL Server 2012 구성](https://github.com/WindowsAzure-TrainingKit/HOL-DeployingSQLServerForSharePoint)(영문)을 참조하십시오.
    -   Active Directory를 구성하고 단일 SQL Server 데이터베이스를 사용하여 SharePoint 팜을 만드는 과정에 대해서는 실습 교육 [Azure 가상 컴퓨터로 SharePoint 팜 배포](https://github.com/WindowsAzure-TrainingKit/HOL-DeploySharePointVMs)(영문)를 참조하십시오.

3.  *BI VM 추가*:
    -   관리 포털 끝점에 보안을 구성하고 VM의 Windows 방화벽에 인바운드 포트를 설정합니다.
    -   새로 만든 BI VM을 기존 SharePoint 또는 SQL Server 팜에 추가합니다.

4.  *VM 관리*:
    -   관리 포털을 사용하여 VM을 모니터링합니다.
    -   중앙 관리를 사용하여 SharePoint 팜을 모니터링합니다.
    -   Microsoft System Center - Operations Manager와 같은 온-프레미스 관리 소프트웨어를 사용하여 VM을 모니터링하고 관리합니다.

### 시나리오 4: 완전히 사용자 지정된 SharePoint 기반 웹 사이트

#### 설명

점점 더 많은 조직이 클라우드에서 완전히 사용자 지정된 SharePoint 웹 사이트를 만들길 원합니다. 이러한 조직들은 클라우드에서 실행되는 복잡한 응용 프로그램을 관리할 수 있는 모든 권한을 제공하면서 지속성과 가용성이 뛰어난 환경을 요구하지만, 많은 시간과 예산을 사용하지는 않으려고 합니다.

이 시나리오의 조직은 클라우드에서 전체 SharePoint 팜을 배포하고 동적으로 모든 구성 요소를 확장하여 추가 용량을 확보하거나, 온-프레미스 배포를 클라우드로 확장하여 필요한 경우에 용량을 늘리고 성능을 개선할 수 있습니다. 이 시나리오는 응용 프로그램 개발 및 엔터프라이즈 콘텐츠 관리를 위해 전체 "SharePoint 환경"을 원하는 조직에 초점을 맞추고 있습니다. 또한 더욱 복잡한 사이트에는 종단 간, 전체 기능에 대한 고급 보고, 파워 뷰, PerformancePoint, PowerPivot, 심층 차트, 그 외에 SharePoint 사이트 기능 중 대부분도 포함될 수 있습니다.

조직은 Azure 가상 컴퓨터를 사용하여 비용 효율적이며 보안이 강화된 클라우드 인프라에서 사용자 지정된 응용 프로그램 및 연결된 구성 요소를 호스트할 수 있습니다. 또한 온-프레미스 Microsoft System Center를 온-프레미스 및 클라우드 응용 프로그램의 일반 관리 도구로 사용할 수도 있습니다.

#### 시작하기

Azure에서 완전히 사용자 지정된 SharePoint 웹 사이트를 구현하기 위해 조직은 클라우드에서 Active Directory 도메인을 배포하고 새 VM을 이 도메인에 프로비전해야 합니다. 그런 다음 SQL Server 2012를 실행하는 VM을 SharePoint 팜의 일부로 만들어 구성해야 합니다. 마지막으로 SharePoint 팜을 만들고 부하를 분산하고 Active Directory 및 SQL Server에 연결해야 합니다(그림 7).

그림 7: 완전히 사용자 지정된 SharePoint 기반 웹 사이트

![azure-sharepoint-wp-14](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-14.png)

#### 시나리오 환경 설정

다음 단계는 이미지 라이브러리의 미리 빌드된 이미지에서 사용자 지정된 SharePoint 팜 환경을 만드는 방법을 보여 줍니다. 그러나 SharePoint 팜 VM을 이미지 라이브러리로 업로드할 수도 있으며, 권한 있는 사용자는 해당 VM을 선택하여 Azure에서 필요한 SharePoint 팜을 빌드할 수 있습니다.

1.  Active Directory 배포*: Azure 가상 컴퓨터에서 Active Directory를 배포하기 위해 충족해야 하는 기본 요구 사항은 온-프레미스 VM(및 어느 정도까지는 물리적 컴퓨터)에서 배포하는 경우와 유사하지만 "동일하지는 않습니다". 차이점, 지침 및 기타 고려 사항에 대한 자세한 내용을 보려면 [Azure 가상 컴퓨터에 Active Directory를 배포하기 위한 지침](http://msdn.microsoft.com/en-us/library/windowsazure/jj156090)으로 이동하십시오. Azure에서 Active Directory를 배포하려면 다음을 수행하십시오.*
    -   VM을 특정 서브넷에 할당할 수 있는 가상 네트워크를 정의하고 만듭니다. 자세한 내용을 보려면 [가상 네트워킹 구성](https://github.com/WindowsAzure-TrainingKit/HOL-DeployingActiveDirectory/blob/master/HOL.md)(영문)으로 이동하십시오.
    -   Azure에서 관리 포털을 사용하여 새 VM에서 도메인 컨트롤러를 만들고 배포합니다. 자세한 내용을 보려면 [도메인 컨트롤러 만들기 및 배포](https://github.com/WindowsAzure-TrainingKit/HOL-DeployingActiveDirectory/blob/master/HOL.md)(영문)로 이동하십시오.
        -   또한 Azure 가상 컴퓨터 및 가상 네트워크를 사용하는 클라우드에서 Windows PowerShell 스크립트를 참조하여 독립 실행형 도메인을 배포할 수도 있습니다. 자세한 내용을 보려면 [Azure에서 Active Directory 배포(Windows PowerShell)](https://github.com/WindowsAzure-TrainingKit/HOL-DeployingActiveDirectoryPS)(영문)로 이동하십시오.
        -   Azure 가상 네트워크의 VM에서 새 Active Directory 포리스트를 만드는 방법에 대한 자세한 내용을 보려면 [Azure에서 새 Active Directory 포리스트 설치](/en-us/manage/services/networking/active-directory-forest/)(영문)로 이동하십시오.

2.  *SQL Server 배포*:
    -   관리 포털을 사용하여 이미지 라이브러리의 기존 이미지에서 새 VM을 프로비전합니다.
    -   VM에서 SQL Server를 구성합니다. 자세한 내용을 보려면 [SysPrep을 사용하여 SQL Server 설치](http://msdn.microsoft.com/en-us/library/ee210664.aspx)로 이동하십시오.
    -   새로 만든 Active Directory 도메인에 VM을 가입합니다.

3.  *다중 서버 SharePoint 팜 배포*:
    -   가상 네트워크를 만듭니다. 자세한 내용을 보려면 [Azure 가상 네트워크(디자인 고려 사항 및 보안 연결 시나리오)](http://msdn.microsoft.com/en-us/library/windowsazure/jj156007.aspx)로 이동하십시오.
        -   SharePoint VM을 배포할 때 프로비전하는 동안 로컬 Active Directory 상자의 DNS 주소를 사용할 수 있도록 SharePoint Server에 입력한 서브넷을 기록해 놓아야 합니다.
    -   관리 포털을 사용하여 VM을 만듭니다.
    -   이 VM에 SharePoint Server를 설치하고 재사용 가능 이미지를 생성합니다. SharePoint Server 설치에 대한 자세한 내용을 보려면 [Windows PowerShell을 사용하여 SharePoint Server 2010 설치 및 구성](http://technet.microsoft.com/en-us/library/cc262839.aspx) 또는 [CodePlex: AutoSPInstaller](http://autospinstaller.codeplex.com/)(영문)로 이동하십시오.
    -   SharePoint VM을 구성하여 SharePoint 팜을 만들고 [Join-SharePointFarm](http://technet.microsoft.com/en-us/library/ff607979.aspx) 명령을 사용하여 연결합니다.
    -   다음과 같이 관리 포털을 사용하여 부하 분산을 구성합니다.
        -   VM 끝점을 구성하고 기존 끝점에서 트래픽 부하를 분산하는 옵션을 선택한 후 부하 분산된 VM의 이름을 지정합니다.
            -   Azure 가상 컴퓨터에서 SharePoint 팜 배포에 대한 자세한 내용을 알아보려면 [TechEd North America 2012 동영상](http://channel9.msdn.com/Events/TechEd/NorthAmerica/2012/AZR327)(영문)을 시청하십시오.

4.  *System Center를 통해 SharePoint 팜 관리*:
    -   Operations Manager 에이전트 및 새로운 Azure 통합 팩을 사용하여 온-프레미스 System Center를 Azure 가상 컴퓨터에 연결합니다.
    -   관리 기능을 위해 온-프레미스 App Controller 및 Orchestrator를 사용합니다.

결론
----

클라우드 컴퓨팅은 IT 부서가 조직에 서비스를 제공하는 방법을 바꾸고 있습니다. 클라우드 컴퓨팅은 IT 부서의 집중성, 민첩성 및 유연성 향상과 함께 상당한 비용 절감 효과를 비롯하여 새로운 차원의 이점을 활용할 수 있기 때문입니다. Azure는 쉽고 유연하며 강력한 개방형 가상 인프라를 제공하여 클라우드 컴퓨팅을 선도하고 있습니다. Azure 가상 컴퓨터가 하드웨어에 대한 요구를 줄여주므로 조직은 모든 권한을 갖고 효율적인 관리 방식을 사용하여 인프라를 적절한 규모로 빌드함으로써 비용과 복잡성을 줄일 수 있습니다.

Azure 가상 컴퓨터는 SharePoint 배포의 완전한 연속성을 제공합니다. Azure 가상 컴퓨터는 다른 Microsoft 응용 프로그램과의 최적의 환경을 제공하도록 완벽하게 지원되며 테스트되었습니다. 따라서 조직은 Azure에서 SharePoint Server를 쉽게 설치하고 배포하여 새 SharePoint 배포를 위한 인프라를 프로비전하거나 기존 인프라를 확장할 수 있습니다. 비즈니스 작업이 증가함에 따라 조직은 SharePoint 인프라를 빠르게 확장할 수 있습니다. 마찬가지로 작업 요구가 감소하면 조직은 주문형 리소스를 계약하여 사용하는 리소스에 대해서만 결제할 수 있습니다. 이 문서에서 논의한 네 가지 SharePoint 기반 시나리오에서 알 수 있듯이 Azure 가상 컴퓨터는 광범위한 비즈니스 요구 사항에 탁월한 인프라를 제공합니다.

Azure 가상 컴퓨터에서 SharePoint Server를 성공적으로 배포하려면 확실한 계획이 필요하며, 특히 중요 팜 아키텍처의 범위 및 배포 옵션을 고려해야 합니다. 이 문서에서 설명한 통찰력과 모범 사례가 SharePoint 배포를 구현하도록 결정하는 데 지침이 될 수 있습니다.

추가 리소스
-----------

-   Azure 트레이닝 키트: 실습 교육 및 프레젠테이션(영문)

    <http://windowsazure-trainingkit.github.com/labs.htm>

-   Azure PowerShell로 시작

    <http://msdn.microsoft.com/en-us/library/windowsazure/jj156055>

-   Azure 관리 Cmdlet(영문)

    <http://msdn.microsoft.com/en-us/library/windowsazure/jj152841>

-   여러 운영 체제용 명령줄 도구 및 PowerShell Cmdlet(영문)

    [https://www.windowsazure.com/en-us/manage/downloads/](/en-us/manage/downloads/)

-   방법 안내 가이드 및 모범 사례 설명서(영문)

    [https://www.windowsazure.com/en-us/manage/windows/](/en-us/manage/windows/)


