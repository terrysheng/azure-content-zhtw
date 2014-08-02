<properties linkid="develop-media-services-tutorials-smooth-streaming-windows-store-app" urlDisplayName="Smooth Streaming Windows Store App" pageTitle="Smooth Streaming Windows Store App - Azure .NET tutorials" metaKeywords="" description="Learn how to use Azure Media Services to create a C# Windows Store application with a XML MediaElement control to playback Smooth Stream content." metaCanonical="" services="media-services" documentationCenter="" title="How to Build a Smooth Streaming Windows Store Application" authors="" solutions="" manager="" editor="" />

부드러운 스트리밍 Windows 스토어 응용 프로그램을 빌드하는 방법
==============================================================

Smooth Streaming Client SDK for Windows 8을 사용하면 개발자가 주문형 및 Live Smooth Streaming 콘텐츠를 재생할 수 있는 Windows 스토어 응용 프로그램을 빌드할 수 있습니다. 부드러운 스트리밍 콘텐츠의 기본 재생뿐 아니라 SDK는 Microsoft PlayReady 보호, 품질 수준 제한, Live DVR, 오디오 스트림 전환, 상태 업데이트(예: 품질 수준 변경) 수신 대기, 오류 이벤트 등의 풍부한 기능도 제공합니다. 지원되는 기능에 대한 자세한 내용은 [릴리스 정보](http://www.iis.net/learn/media/smooth-streaming/smooth-streaming-client-sdk-for-windows-8-release-notes)를 참조하십시오.

이 자습서에서는 API를 다룹니다. 플레이어를 개발하는 경우 [Player Framework for Windows 8](http://playerframework.codeplex.com/)을 사용하는 것이 좋습니다. 플레이어 프레임워크를 통해 응용 프로그램을 더 쉽게 빌드할 수 있으며 많은 추가 기능이 제공됩니다.

이 자습서에는 4개 단원이 포함되어 있습니다.

1.  기본 부드러운 스트리밍 스토어 응용 프로그램 만들기
2.  미디어 진행을 제어하는 슬라이더 막대 추가
3.  부드러운 스트리밍 스트림 선택
4.  부드러운 스트리밍 트랙 선택

필수 조건
=========

-   Windows 8 32비트 또는 64비트. MSDN에서 [Windows 8 Enterprise Evaluation](http://msdn.microsoft.com/en-us/evalcenter/jj554510.aspx)을 다운로드할 수 있습니다.
-   Windows 8에 설치된 Visual Studio 2012 또는 Visual Studio Express 2012 for Windows 8. [여기서](http://www.microsoft.com/visualstudio/11/en-us/downloads) 평가판을 다운로드할 수 있습니다.
-   [Microsoft Smooth Streaming Client SDK for Windows 8](http://visualstudiogallery.msdn.microsoft.com/04423d13-3b3e-4741-a01c-1ae29e84fea6?SRC=Homehttp://visualstudiogallery.msdn.microsoft.com/04423d13-3b3e-4741-a01c-1ae29e84fea6?SRC=Home)

각 단원에 대해 완성된 솔루션은 MSDN 개발자 코드 샘플(코드 갤러리)에서 다운로드할 수 있습니다. [단원 1](http://code.msdn.microsoft.com/Smooth-Streaming-Client-0bb1471f "간단한 Windows 8 부드러운 스트리밍 미디어 플레이어"), [단원 2](http://code.msdn.microsoft.com/A-simple-Windows-8-Smooth-ee98f63a "슬라이더 막대 컨트롤이 포함된 간단한 Windows 8 부드러운 스트리밍 미디어 플레이어"), [단원 3](http://code.msdn.microsoft.com/A-Windows-8-Smooth-883c3b44 "스트림 선택이 포함된 Windows 8 부드러운 스트리밍 미디어 플레이어") 및 [단원 4](http://code.msdn.microsoft.com/A-Windows-8-Smooth-aa9e4907 "트랙 선택이 포함된 Windows 8 부드러운 스트리밍 미디어 플레이어")

단원 1: 기본 부드러운 스트리밍 스토어 응용 프로그램 만들기
==========================================================

이 단원에서는 부드러운 스트림 콘텐츠를 재생하기 위해 MediaElement 컨트롤이 포함된 Windows 스토어 응용 프로그램을 만듭니다. 실행 중인 응용 프로그램은 다음과 같이 표시됩니다.

![부드러운 스트리밍 Windows 스토어 응용 프로그램 예](./media/media-services-build-smooth-streaming-apps/SSClientWin8-1.png)

Windows 스토어 응용 프로그램 개발에 대한 자세한 내용은 [유용한 Windows 8용 앱 개발](http://msdn.microsoft.com/en-us/windows/apps/br229512.aspx)을 참조하십시오. 이 단원에는 다음 절차가 포함되어 있습니다.

1.  Windows 스토어 프로젝트 만들기
2.  사용자 인터페이스 디자인(XAML)
3.  코드 숨김 파일 수정
4.  응용 프로그램 컴파일 및 테스트

**Windows 스토어 프로젝트를 만들려면**

1.  Visual Studio 2012를 실행합니다.
2.  **파일** 메뉴에서 **새로 만들기**를 클릭한 후 **프로젝트**를 클릭합니다.
3.  새 프로젝트 대화 상자에서 다음 값을 입력하거나 선택합니다.

	<table border="1">
	<tr>
		<th>이름</th>
		<th>값</th>
	</tr>
	<tr>
		<td>템플릿 그룹</td>
		<td>Installed/Templates/Visual C#/Windows Store</td>
	</tr>
	<tr>
		<td>템플릿</td>
		<td>새 응용 프로그램(XAML))</td>
	</tr>
	<tr>
		<td>이름</td>
		<td>SSPlayer</td>
	</tr>
	<tr>
		<td>위치</td>
		<td>C:\SSTutorials</td>
	</tr>
	<tr>
		<td>솔루션 이름</td>
		<td>SSPlayer</td>
	</tr>
	<tr>
		<td>솔루션용 디렉터리 만들기</td>
		<td>(선택됨)</td>
	</tr>
	</table>

4.  **확인**을 클릭합니다.

**부드러운 스트리밍 클라이언트 SDK에 대한 참조를 추가하려면**

1.  솔루션 탐색기에서 **SSPlayer**를 마우스 오른쪽 단추로 클릭하고 **참조 추가**를 클릭합니다.
2.  다음 값을 입력하거나 선택합니다.

	<table border="1">
	<tr>
		<th>이름</th>
		<th>값</th>
	</tr>
	<tr>
		<td>참조 그룹</td>
		<td>Windows/Extensions</td>
	</tr>
	<tr>
		<td>참조</td>
		<td>Microsoft Smooth Streaming Client SDK for Windows 8 및 Microsoft Visual C++ 런타임 패키지 선택 
		</td>
	</tr>
	</table>

3.  **확인**을 클릭합니다.

참조를 추가한 후 대상 플랫폼(x64 또는 x86)을 선택해야 합니다. 임의 CPU 플랫폼 구성에서는 참조 추가가 작동하지 않습니다. 솔루션 탐색기에서 추가된 이 참조에 대해 노란색 경고 표시가 나타납니다.

**플레이어 사용자 인터페이스를 디자인하려면**

1.  솔루션 탐색기에서 **MainPage.xaml**을 두 번 클릭하여 디자인 보기에서 엽니다.
2.  XAML 파일에서 **&lt;Grid\>** 및 **&lt;/Grid\>** 태그를 찾아 두 태그 사이에 다음 코드를 붙여넣습니다.

        <Grid.RowDefinitions>
            <RowDefinition Height="20"/>    <!-- spacer -->
            <RowDefinition Height="50"/>    <!-- media controls -->
            <RowDefinition Height="100*"/>  <!-- media element -->
            <RowDefinition Height="80*"/>   <!-- media stream and track selection -->
            <RowDefinition Height="50"/>    <!-- status bar -->
        </Grid.RowDefinitions>
            
        <StackPanel Name="spMediaControl" Grid.Row="1" Orientation="Horizontal">
            <TextBlock x:Name="tbSource" Text="Source :  " FontSize="16" FontWeight="Bold" VerticalAlignment="Center" />
            <TextBox x:Name="txtMediaSource" Text="http://ecn.channel9.msdn.com/o9/content/smf/smoothcontent/elephantsdream/Elephants_Dream_1024-h264-st-aac.ism/manifest" FontSize="10" Width="700" Margin="0,4,0,10" />
            <Button x:Name="btnSetSource" Content="Set Source" Width="111" Height="43" Click="btnSetSource_Click"/>
            <Button x:Name="btnPlay" Content="Play" Width="111" Height="43" Click="btnPlay_Click"/>
            <Button x:Name="btnPause" Content="Pause"  Width="111" Height="43" Click="btnPause_Click"/>
            <Button x:Name="btnStop" Content="Stop"  Width="111" Height="43" Click="btnStop_Click"/>
            <CheckBox x:Name="chkAutoPlay" Content="Auto Play" Height="55" Width="Auto" IsChecked="{Binding AutoPlay, ElementName=mediaElement, Mode=TwoWay}"/>
            <CheckBox x:Name="chkMute" Content="Mute" Height="55" Width="67" IsChecked="{Binding IsMuted, ElementName=mediaElement, Mode=TwoWay}"/>
        </StackPanel>

        <StackPanel Name="spMediaElement" Grid.Row="2" Height="435" Width="1072"
                    HorizontalAlignment="Center" VerticalAlignment="Center">
            <MediaElement x:Name="mediaElement" Height="356" Width="924" MinHeight="225"
                          HorizontalAlignment="Center" VerticalAlignment="Center" 
                          AudioCategory="BackgroundCapableMedia" />
            <StackPanel Orientation="Horizontal">
                <Slider x:Name="sliderProgress" Width="924" Height="44"
                        HorizontalAlignment="Center" VerticalAlignment="Center"
                        PointerPressed="sliderProgress_PointerPressed"/>
                <Slider x:Name="sliderVolume" 
                        HorizontalAlignment="Right" VerticalAlignment="Center" Orientation="Vertical" 
                        Height="79" Width="148" Minimum="0" Maximum="1" StepFrequency="0.1" 
                        Value="{Binding Volume, ElementName=mediaElement, Mode=TwoWay}" 
                        ToolTipService.ToolTip="{Binding Value, RelativeSource={RelativeSource Mode=Self}}"/>
            </StackPanel>
        </StackPanel>

        <StackPanel Name="spStatus" Grid.Row="4" Orientation="Horizontal">
            <TextBlock x:Name="tbStatus" Text="Status :  " 
               FontSize="16" FontWeight="Bold" VerticalAlignment="Center" HorizontalAlignment="Center" />
            <TextBox x:Name="txtStatus" FontSize="10" Width="700" VerticalAlignment="Center"/>
        </StackPanel>

    MediaElement 컨트롤은 미디어를 재생하는 데 사용됩니다. sliderProgress라는 슬라이더 컨트롤은 다음 단원에서 미디어 진행을 제어하는 데 사용됩니다.

3.  **Ctrl+S**를 눌러 파일을 저장합니다.

MediaElement 컨트롤은 기본적으로 부드러운 스트리밍 콘텐츠를 지원하지 않습니다. 부드러운 스트리밍 지원을 사용하도록 설정하려면 파일 이름 확장명과 MIME 형식으로 부드러운 스트리밍 바이트 스트림 처리기를 등록해야 합니다. 등록하려면 Windows.Media 네임스페이스의 MediaExtensionManager.RegisterByteStremHandler 메서드를 사용합니다.

이 XAML 파일에서는 일부 이벤트 처리기가 컨트롤과 연결되어 있습니다. 해당 이벤트 처리기를 정의해야 합니다.

**코드 숨김 파일을 수정하려면**

1.  솔루션 탐색기에서 **MainPage.xaml**을 마우스 오른쪽 단추로 클릭하고 **코드 보기**를 클릭합니다.
2.  파일 맨 위에 다음 using 문을 추가합니다.

        using Windows.Media;

3.  **MainPage** 클래스의 시작 부분에 다음 데이터 멤버를 추가합니다.

        private MediaExtensionManager extensions = new MediaExtensionManager();

4.  **MainPage** 생성자의 끝에 다음 두 줄을 추가합니다.

        extensions.RegisterByteStreamHandler("Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", ".ism", "text/xml");
        extensions.RegisterByteStreamHandler("Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", ".ism", "application/vnd.ms-sstr+xml");

5.  **MainPage** 클래스의 끝에 다음 코드를 붙여넣습니다.

        #region UI Button Click Events
        private void btnPlay_Click(object sender, RoutedEventArgs e)
        {
            mediaElement.Play();
            txtStatus.Text = "MediaElement is playing ...";
        }
            
        private void btnPause_Click(object sender, RoutedEventArgs e)
        {
            mediaElement.Pause();
            txtStatus.Text = "MediaElement is paused";
        }
            
        private void btnSetSource_Click(object sender, RoutedEventArgs e)
        {
            sliderProgress.Value = 0;
            mediaElement.Source = new Uri(txtMediaSource.Text);
            
            if (chkAutoPlay.IsChecked == true)
            {
                txtStatus.Text = "MediaElement is playing ...";
            }
            else
            {
                txtStatus.Text = "Click the Play button to play the media source.";
            }
        }
            
        private void btnStop_Click(object sender, RoutedEventArgs e)
        {
            mediaElement.Stop();
            txtStatus.Text = "MediaElement is stopped";
        }
            
        private void sliderProgress_PointerPressed(object sender, PointerRoutedEventArgs e)
        {
            txtStatus.Text = "Seek to position " + sliderProgress.Value;
            mediaElement.Position = new TimeSpan(0, 0, (int)(sliderProgress.Value));
        }
        # endregion

    여기에는 sliderProgress\_PointerPressed 이벤트 처리기가 정의되어 있습니다. 작동하는 데 필요한 추가 작업이 있으며, 이 자습서의 다음 단원에서 설명합니다.

6.  **Ctrl+S**를 눌러 파일을 저장합니다.

완성된 코드 숨김 파일은 다음과 같이 표시됩니다.

![부드러운 스트리밍 Windows 스토어 응용 프로그램에 대한 Visual Studio의 Codeview](./media/media-services-build-smooth-streaming-apps/SSClientWin8-2.png)

**응용 프로그램을 컴파일 및 테스트하려면**

1.  **빌드** 메뉴에서 **구성 관리자**를 클릭합니다.
2.  개발 플랫폼과 일치하도록 **활성 솔루션 플랫폼**을 변경합니다.
3.  **F6** 키를 눌러 프로젝트를 컴파일합니다.
4.  **F5** 키를 눌러 응용 프로그램을 실행합니다.
5.  응용 프로그램 맨 위에 기본 부드러운 스트리밍 URL을 사용하거나 다른 URL을 입력할 수 있습니다.
6.  **원본 설정**을 클릭합니다. 기본적으로 **자동 재생**이 사용되기 때문에 미디어가 자동으로 재생됩니다. **재생**, **일시 중지** 및 **중지** 단추를 사용하여 미디어를 제어할 수 있습니다. 세로 슬라이더를 사용하여 미디어 볼륨을 제어할 수 있습니다. 그러나 미디어 진행을 제어하기 위한 가로 슬라이더는 아직 완전히 구현되지 않았습니다.

lesson1을 완성했습니다. 이 단원에서는 MediaElement 컨트롤을 사용하여 부드러운 스트리밍 콘텐츠를 재생합니다. 다음 단원에서는 부드러운 스트리밍 콘텐츠 진행을 제어하는 슬라이더를 추가합니다.

단원 2: 미디어 진행을 제어하는 슬라이더 막대 추가
=================================================

단원 1에서는 부드러운 스트리밍 미디어 콘텐츠를 재생하기 위해 MediaElement XAML 컨트롤이 포함된 Windows 스토어 응용 프로그램을 만들었습니다. 시작, 중지, 일시 중지 등의 기본 미디어 기능이 제공됩니다. 이 단원에서는 응용 프로그램에 슬라이더 막대 컨트롤을 추가합니다.

이 자습서에서는 타이머를 사용하여 MediaElement 컨트롤의 현재 위치에 따라 슬라이더 위치를 업데이트합니다. 라이브 콘텐츠의 경우 슬라이더 시작 및 종료 시간도 업데이트해야 합니다. 이 작업은 적응 원본 업데이트 이벤트를 통해 더 효율적으로 처리할 수 있습니다.

미디어 원본은 미디어 데이터를 생성하는 개체입니다. 원본 확인자는 URL 또는 바이트 스트림을 사용하고 해당 콘텐츠에 적합한 미디어 원본을 만듭니다. 원본 확인자는 응용 프로그램에서 미디어 원본을 만드는 표준 방법입니다.

이 단원에는 다음 절차가 포함되어 있습니다.

1.  부드러운 스트리밍 처리기 등록
2.  적응 원본 관리자 수준 이벤트 처리기 추가
3.  적응 원본 수준 이벤트 처리기 추가
4.  MediaElement 이벤트 처리기 추가
5.  슬라이더 막대 관련 코드 추가
6.  응용 프로그램 컴파일 및 테스트

**부드러운 스트리밍 바이트 스트림 처리기를 등록하고 propertyset를 전달하려면**

1.  솔루션 탐색기에서 **MainPage.xaml**을 마우스 오른쪽 단추로 클릭하고 **코드 보기**를 클릭합니다.
2.  파일의 시작 부분에 다음 using 문을 추가합니다.

        using Microsoft.Media.AdaptiveStreaming;

3.  MainPage 클래스의 시작 부분에 다음 데이터 멤버를 추가합니다.

        private Windows.Foundation.Collections.PropertySet propertySet = new Windows.Foundation.Collections.PropertySet();             
        private IAdaptiveSourceManager adaptiveSourceManager;

4.  **MainPage** 생성자 내에서 **this.Initialize Components();** 줄과 이전 단원에서 작성된 등록 코드 줄 뒤에 다음 코드를 추가합니다.

        // Gets the default instance of AdaptiveSourceManager which manages Smooth 
        //Streaming media sources.
        adaptiveSourceManager = AdaptiveSourceManager.GetDefault();
        // Sets property key value to AdaptiveSourceManager default instance.
        // {A5CE1DE8-1D00-427B-ACEF-FB9A3C93DE2D}" must be hardcoded.
        propertySet["{A5CE1DE8-1D00-427B-ACEF-FB9A3C93DE2D}"] = adaptiveSourceManager;

5.  **MainPage** 생성자 내에서 RegisterByteStreamHandler 메서드 두 개를 수정하여 네 번째 매개 변수를 추가합니다.

        // Registers Smooth Streaming byte-stream handler for ".ism" extension and, 
        // "text/xml" and "application/vnd.ms-ss" mime-types and pass the propertyset. 
        // http://*.ism/manifest URI resources will be resolved by Byte-stream handler.
        extensions.RegisterByteStreamHandler(
            "Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", 
            ".ism", 
            "text/xml", 
            propertySet );
        extensions.RegisterByteStreamHandler(
            "Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", 
            ".ism", 
            "application/vnd.ms-sstr+xml", 
        propertySet);

6.  **Ctrl+S**를 눌러 파일을 저장합니다.

**적응 원본 관리자 수준 이벤트 처리기를 추가하려면**

1.  솔루션 탐색기에서 **MainPage.xaml**을 마우스 오른쪽 단추로 클릭하고 **코드 보기**를 클릭합니다.
2.  **MainPage** 클래스 내에 다음 데이터 멤버를 추가합니다.

        private AdaptiveSource adaptiveSource = null;

3.  **MainPage** 클래스의 끝에 다음 이벤트 처리기를 추가합니다.

        #region Adaptive Source Manager Level Events
        private void mediaElement_AdaptiveSourceOpened(AdaptiveSource sender, AdaptiveSourceOpenedEventArgs args)
        {
            adaptiveSource = args.AdaptiveSource;
        }
        #endregion Adaptive Source Manager Level Events

4.  **MainPage** 생성자의 끝에 다음 줄을 추가하여 적응 원본 열기 이벤트를 구독합니다.

    adaptiveSourceManager.AdaptiveSourceOpenedEvent += new AdaptiveSourceOpenedEventHandler(mediaElement\_AdaptiveSourceOpened);

5.  **Ctrl+S**를 눌러 파일을 저장합니다.

**적응 원본 수준 이벤트 처리기를 추가하려면**

1.  솔루션 탐색기에서 **MainPage.xaml**을 마우스 오른쪽 단추로 클릭하고 **코드 보기**를 클릭합니다.
2.  **MainPage** 클래스 내에 다음 데이터 멤버를 추가합니다.

        private AdaptiveSourceStatusUpdatedEventArgs adaptiveSourceStatusUpdate; 
        private Manifest manifestObject;

3.  **MainPage** 클래스의 끝에 다음 이벤트 처리기를 추가합니다.

        #region Adaptive Source Level Events
        private void mediaElement_ManifestReady(AdaptiveSource sender, ManifestReadyEventArgs args)
        {
            adaptiveSource = args.AdaptiveSource;
            manifestObject = args.AdaptiveSource.Manifest;
        }
            
        private void mediaElement_AdaptiveSourceStatusUpdated(AdaptiveSource sender, AdaptiveSourceStatusUpdatedEventArgs args)
        {
            adaptiveSourceStatusUpdate = args;
        }
            
        private void mediaElement_AdaptiveSourceFailed(AdaptiveSource sender, AdaptiveSourceFailedEventArgs args)
        {
            txtStatus.Text = "Error: " + args.HttpResponse;
        }
        #endregion Adaptive Source Level Events

4.  **mediaElement AdaptiveSourceOpened** 메서드의 끝에 다음 코드를 추가하여 이벤트를 구독합니다.

        adaptiveSource.ManifestReadyEvent +=
                    mediaElement_ManifestReady;
        adaptiveSource.AdaptiveSourceStatusUpdatedEvent += 
            mediaElement_AdaptiveSourceStatusUpdated;
        adaptiveSource.AdaptiveSourceFailedEvent += 
            mediaElement_AdaptiveSourceFailed;

5.  **Ctrl+S**를 눌러 파일을 저장합니다.

앱의 모든 미디어 요소에 공통된 기능을 처리하는 데 사용할 수 있는 적응 원본 관리자 수준에서도 동일한 이벤트를 사용할 수 있습니다. 각 AdaptiveSource에는 고유한 이벤트가 포함되어 있으며 모든 AdaptiveSource 이벤트가 AdaptiveSourceManager 아래에 계단식으로 표시됩니다.

**미디어 요소 이벤트 처리기를 추가하려면**

1.  솔루션 탐색기에서 **MainPage.xaml**을 마우스 오른쪽 단추로 클릭하고 **코드 보기**를 클릭합니다.
2.  **MainPage** 클래스의 끝에 다음 이벤트 처리기를 추가합니다.

        #region Media Element Event Handlers 
        private void MediaOpened(object sender, RoutedEventArgs e)
        {
            txtStatus.Text = "MediaElement opened";
        }
            
        private void MediaFailed(object sender, ExceptionRoutedEventArgs e)
        {
            txtStatus.Text= "MediaElement failed: " + e.ErrorMessage;
        }
            
        private void MediaEnded(object sender, RoutedEventArgs e)
        {
            txtStatus.Text ="MediaElement ended.";
        }
        #endregion Media Element Event Handlers

3.  **MainPage** 생성자의 끝에 다음 코드를 추가하여 이벤트를 구독합니다.

        mediaElement.MediaOpened += MediaOpened;
        mediaElement.MediaEnded += MediaEnded;
        mediaElement.MediaFailed += MediaFailed;

4.  **Ctrl+S**를 눌러 파일을 저장합니다.

**슬라이더 막대 관련 코드를 추가하려면**

1.  솔루션 탐색기에서 **MainPage.xaml**을 마우스 오른쪽 단추로 클릭하고 **코드 보기**를 클릭합니다.
2.  파일의 시작 부분에 다음 using 문을 추가합니다.

        using Windows.UI.Core;

3.  **MainPage** 클래스 내에 다음 데이터 멤버를 추가합니다.

        public static CoreDispatcher _dispatcher;
        private DispatcherTimer sliderPositionUpdateDispatcher;

4.  **MainPage** 생성자의 끝에 다음 코드를 추가합니다.

        _dispatcher = Window.Current.Dispatcher;
        PointerEventHandler pointerpressedhandler = new PointerEventHandler(sliderProgress_PointerPressed);
        sliderProgress.AddHandler(Control.PointerPressedEvent, pointerpressedhandler, true);    

5.  **MainPage** 클래스의 끝에 다음 코드를 추가합니다.

        #region sliderMediaPlayer
        private double SliderFrequency(TimeSpan timevalue)
        {
            long absvalue = 0;
            double stepfrequency = -1;
            
            if (manifestObject != null)
            {
                absvalue = manifestObject.Duration - (long)manifestObject.StartTime;
            }
            else
            {
                absvalue = mediaElement.NaturalDuration.TimeSpan.Ticks;
            }
            
            TimeSpan totalDVRDuration = new TimeSpan(absvalue);
            
            if (totalDVRDuration.TotalMinutes >= 10 && totalDVRDuration.TotalMinutes < 30)
            {
               stepfrequency = 10;
            }
            else if (totalDVRDuration.TotalMinutes >= 30 
                     && totalDVRDuration.TotalMinutes < 60)
            {
                stepfrequency = 30;
            }
            else if (totalDVRDuration.TotalHours >= 1)
            {
                stepfrequency = 60;
            }
            
            return stepfrequency;
        }
            
        void updateSliderPositionoNTicks(object sender, object e)
        {
            sliderProgress.Value = mediaElement.Position.TotalSeconds;
        }
            
        public void setupTimer()
        {
            sliderPositionUpdateDispatcher = new DispatcherTimer();
            sliderPositionUpdateDispatcher.Interval = new TimeSpan(0, 0, 0, 0, 300);
            startTimer();
        }

        public void startTimer()
        {
            sliderPositionUpdateDispatcher.Tick += updateSliderPositionoNTicks;
            sliderPositionUpdateDispatcher.Start();
        }
            
        // Slider start and end time must be updated in case of live content
        public async void setSliderStartTime(long startTime)
        {
            await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
            {
                TimeSpan timespan = new TimeSpan(adaptiveSourceStatusUpdate.StartTime);
                double absvalue = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero);
                sliderProgress.Minimum = absvalue;
            });
        }
            
        // Slider start and end time must be updated in case of live content
        public async void setSliderEndTime(long startTime)
        {
            await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
            {
                TimeSpan timespan = new TimeSpan(adaptiveSourceStatusUpdate.EndTime);
                double absvalue = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero);
                sliderProgress.Maximum = absvalue;
            });
        }
        #endregion sliderMediaPlayer

    **참고:** CoreDispatcher는 비UI 스레드에서 UI 스레드를 변경하는 데 사용됩니다. 디스패처 스레드에서 병목이 발생할 경우 개발자는 업데이트하려는 UI 요소에서 제공하는 디스패처를 사용할 수 있습니다. 예를 들면 다음과 같습니다.

        await sliderProgress.Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () => { TimeSpan 
          timespan = new TimeSpan(adaptiveSourceStatusUpdate.EndTime); 
        double absvalue  = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero); 
          sliderProgress.Maximum = absvalue; }); 

6.  **mediaElement\_AdaptiveSourceStatusUpdated** 메서드의 끝에 다음 코드를 추가합니다.

        setSliderStartTime(args.StartTime);
        setSliderEndTime(args.EndTime);

7.  **MediaOpened** 메서드의 끝에 다음 코드를 추가합니다.

    sliderProgress.StepFrequency = SliderFrequency(mediaElement.NaturalDuration.TimeSpan); sliderProgress.Width = mediaElement.Width; setupTimer();

8.  **Ctrl+S**를 눌러 파일을 저장합니다.

**응용 프로그램을 컴파일 및 테스트하려면**

1.  **F6** 키를 눌러 프로젝트를 컴파일합니다.
2.  **F5** 키를 눌러 응용 프로그램을 실행합니다.
3.  응용 프로그램 맨 위에 기본 부드러운 스트리밍 URL을 사용하거나 다른 URL을 입력할 수 있습니다.
4.  **원본 설정**을 클릭합니다.
5.  슬라이더 막대를 테스트합니다.

단원 2를 완료했습니다. 이 단원에서는 응용 프로그램에 슬라이더를 추가했습니다.

단원 3: 부드러운 스트리밍 스트림 선택
=====================================

부드러운 스트리밍은 사용자가 선택할 수 있는 여러 언어 오디오 트랙으로 콘텐츠를 스트림할 수 있습니다. 이 단원에서는 사용자가 스트림을 선택할 수 있도록 설정합니다. 이 단원에는 다음 절차가 포함되어 있습니다.

1.  XAML 파일 수정
2.  코드 숨김 파일 수정
3.  응용 프로그램 컴파일 및 테스트

**XAML 파일을 수정하려면**

1.  솔루션 탐색기에서 **MainPage.xaml**을 마우스 오른쪽 단추로 클릭하고 **디자이너 보기**를 클릭합니다.
2.  &lt;Grid.RowDefinitions\>를 찾은 후 RowDefinitions를 다음과 같이 수정합니다.

         <Grid.RowDefinitions>            
             <RowDefinition Height="20"/>
             <RowDefinition Height="50"/>
             <RowDefinition Height="100"/>
             <RowDefinition Height="80"/>
             <RowDefinition Height="50"/>
         </Grid.RowDefinitions>

3.  &lt;Grid\>&lt;/Grid\> 태그 내에 목록 상자 컨트롤을 정의하는 다음 코드를 추가하여 사용자가 사용 가능한 스트림 목록을 보고 스트림을 선택할 수 있게 합니다.

         <Grid Name="gridStreamAndBitrateSelection" Grid.Row="3">
             <Grid.RowDefinitions>
                 <RowDefinition Height="300"/>
             </Grid.RowDefinitions>
             <Grid.ColumnDefinitions>
                 <ColumnDefinition Width="250*"/>
                 <ColumnDefinition Width="250*"/>
             </Grid.ColumnDefinitions>
             <StackPanel Name="spStreamSelection" Grid.Row="1" Grid.Column="0">
                 <StackPanel Orientation="Horizontal">
                     <TextBlock Name="tbAvailableStreams" Text="Available Streams:" FontSize="16" VerticalAlignment="Center"></TextBlock>
                     <Button Name="btnChangeStreams" Content="Submit" Click="btnChangeStream_Click"/>
                 </StackPanel>
                 <ListBox x:Name="lbAvailableStreams" Height="200" Width="200" Background="Transparent" HorizontalAlignment="Left" 
                    ScrollViewer.VerticalScrollMode="Enabled" ScrollViewer.VerticalScrollBarVisibility="Visible">
                     <ListBox.ItemTemplate>
                         <DataTemplate>
                            <CheckBox Content="{Binding Name}" IsChecked="{Binding isChecked, Mode=TwoWay}" />
                         </DataTemplate>
                     </ListBox.ItemTemplate>
                 </ListBox>
             </StackPanel>
         </Grid>

4.  **Ctrl+S**를 눌러 변경 내용을 저장합니다.

**코드 숨김 파일을 수정하려면**

1.  솔루션 탐색기에서 **MainPage.xaml**을 마우스 오른쪽 단추로 클릭하고 **코드 보기**를 클릭합니다.
2.  SSPlayer 네임스페이스 내에 새 클래스를 추가합니다. \#region class Stream

         public class Stream
         {
             private IManifestStream stream;
             public bool isCheckedValue;
             public string name;

             public string Name
             {
                 get { return name; }
                 set { name = value; }
             }

             public IManifestStream ManifestStream
             {
                 get { return stream; }
                 set { stream = value; }
             }

             public bool isChecked
             {
                 get { return isCheckedValue; }
                 set
                 {
                     // mMke the video stream always checked.
                     if (stream.Type == MediaStreamType.Video)
                     {
                         isCheckedValue = true;
                     }
                     else
                     {
                         isCheckedValue = value;
                     }
                 }
             }

             public Stream(IManifestStream streamIn)
             {
                 stream = streamIn;
                 name = stream.Name;
             }
         }
         #endregion class Stream

3.  MainPage 클래스의 시작 부분에 다음 변수 정의를 추가합니다.

         private List<Stream> availableStreams;
         private List<Stream> availableAudioStreams;
         private List<Stream> availableTextStreams;
         private List<Stream> availableVideoStreams;

4.  MainPage 클래스 내에 다음 지역을 추가합니다.

         #region stream selection
         ///<summary>
         ///Functionality to select streams from IManifestStream available streams
         /// </summary>
            
         // This function is called from the mediaElement_ManifestReady event handler 
         // to retrieve the streams and populate them to the local data members.
         public void getStreams(Manifest manifestObject)
         {
             availableStreams = new List<Stream>();
             availableVideoStreams = new List<Stream>();
             availableAudioStreams = new List<Stream>();
             availableTextStreams = new List<Stream>();
            
             try
             {
                 for (int i = 0; i<manifestObject.AvailableStreams.Count; i++)
                 {
                     Stream newStream = new Stream(manifestObject.AvailableStreams[i]);
                     newStream.isChecked = false;
            
                     //populate the stream lists based on the types
                     availableStreams.Add(newStream);
            
                     switch (newStream.ManifestStream.Type)
                     {
                         case MediaStreamType.Video:
                             availableVideoStreams.Add(newStream);
                             break;
                         case MediaStreamType.Audio:
                             availableAudioStreams.Add(newStream);
                             break;
                         case MediaStreamType.Text:
                             availableTextStreams.Add(newStream);
                             break;
                     }
            
                     // Select the default selected streams from the manifest.
                     for (int j = 0; j<manifestObject.SelectedStreams.Count; j++)
                     {
                         string selectedStreamName = manifestObject.SelectedStreams[j].Name;
                         if (selectedStreamName.Equals(newStream.Name))
                         {
                             newStream.isChecked = true;
                             break;
                         }
                     }
                 }
             }
             catch (Exception e)
             {
                 txtStatus.Text = "Error: " + e.Message;
             }
         }
            
         // This function set the list box ItemSource
         private async void refreshAvailableStreamsListBoxItemSource()
         {
             try
             {
                 //update the stream check box list on the UI
                 await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, ()
                     => { lbAvailableStreams.ItemsSource = availableStreams; });
             }
             catch (Exception e)
             {
                 txtStatus.Text = "Error: " + e.Message;
             }
         }
            
         // This function creates a selected streams list
         private void createSelectedStreamsList(List<IManifestStream> selectedStreams)
         {
             bool isOneVideoSelected = false;
             bool isOneAudioSelected = false;
            
             // Only one video stream can be selected
             for (int j = 0; j<availableVideoStreams.Count; j++)
             {
                 if (availableVideoStreams[j].isChecked && (!isOneVideoSelected))
                 {
                     selectedStreams.Add(availableVideoStreams[j].ManifestStream);
                     isOneVideoSelected = true;
                 }
             }
            
             // Select the frist video stream from the list if no video stream is selected
             if (!isOneVideoSelected)
             {
                 availableVideoStreams[0].isChecked = true;
                 selectedStreams.Add(availableVideoStreams[0].ManifestStream);
             }
            
             // Only one audio stream can be selected
             for (int j = 0; j<availableAudioStreams.Count; j++)
             {
                 if (availableAudioStreams[j].isChecked && (!isOneAudioSelected))
                 {
                     selectedStreams.Add(availableAudioStreams[j].ManifestStream);
                     isOneAudioSelected = true;
                     txtStatus.Text = "The audio stream is changed to " + availableAudioStreams[j].ManifestStream.Name;
                 }
             }
            
             // Select the frist audio stream from the list if no audio steam is selected.
             if (!isOneAudioSelected)
             {
                 availableAudioStreams[0].isChecked = true;
                 selectedStreams.Add(availableAudioStreams[0].ManifestStream);
             }
            
             // Multiple text streams are supported.
             for (int j = 0; j < availableTextStreams.Count; j++)
             {
                 if (availableTextStreams[j].isChecked)
                 {
                     selectedStreams.Add(availableTextStreams[j].ManifestStream);
                 }
             }
         }
            
         // Change streams on a smooth streaming presentation with multiple video streams.
         private async void changeStreams(List<IManifestStream> selectStreams)
         {
             try
             {
                 IReadOnlyList<IStreamChangedResult> returnArgs =
                     await manifestObject.SelectStreamsAsync(selectStreams);
             }
             catch (Exception e)
             {
                 txtStatus.Text = "Error: " + e.Message;
             }
         }
         #endregion stream selection

5.  mediaElement\_ManifestReady 메서드를 찾은 후 함수의 끝에 다음 코드를 추가합니다.

         getStreams(manifestObject);
         refreshAvailableStreamsListBoxItemSource();

    따라서 MediaElement 매니페스트가 준비되면 코드가 사용 가능한 스트림 목록을 가져오고 UI 목록 상자에 목록을 채웁니다.

6.  MainPage 클래스 내에서 UI 단추 클릭 이벤트 지역을 찾은 후 다음 함수 정의를 추가합니다.

         private void btnChangeStream_Click(object sender, RoutedEventArgs e)
         {
             List<IManifestStream> selectedStreams = new List<IManifestStream>();

             // Create a list of the selected streams
             createSelectedStreamsList(selectedStreams);

             // Change streams on the presentation
             changeStreams(selectedStreams);
         }

**응용 프로그램을 컴파일 및 테스트하려면**

1.  **F6** 키를 눌러 프로젝트를 컴파일합니다.
2.  **F5** 키를 눌러 응용 프로그램을 실행합니다.
3.  응용 프로그램 맨 위에 기본 부드러운 스트리밍 URL을 사용하거나 다른 URL을 입력할 수 있습니다.
4.  **원본 설정**을 클릭합니다.
5.  기본 언어는 audio\_eng입니다. audio\_eng와 audio\_es 간에 전환해 보십시오. 새 스트림을 선택할 때마다 제출 단추를 클릭해야 합니다.

단원 3을 완료했습니다. 이 단원에서는 스트림 선택 기능을 추가했습니다.

단원 4: 부드러운 스트리밍 트랙 선택
===================================

부드러운 스트리밍 프레젠테이션에는 여러 품질 수준(비트 전송률) 및 해상도로 인코딩된 여러 비디오 파일이 포함될 수 있습니다. 이 단원에서는 사용자가 트랙을 선택할 수 있도록 설정합니다. 이 단원에는 다음 절차가 포함되어 있습니다.

1.  XAML 파일 수정
2.  코드 숨김 파일 수정
3.  응용 프로그램 컴파일 및 테스트

**XAML 파일을 수정하려면**

1.  솔루션 탐색기에서 **MainPage.xaml**을 마우스 오른쪽 단추로 클릭하고 **디자이너 보기**를 클릭합니다.
2.  이름이 **gridStreamAndBitrateSelection**인 &lt;Grid\> 태그를 찾은 후 태그의 끝에 다음 코드를 추가합니다.

         <StackPanel Name="spBitRateSelection" Grid.Row="1" Grid.Column="1">
          <StackPanel Orientation="Horizontal">
              <TextBlock Name="tbBitRate" Text="Available Bitrates:" FontSize="16" VerticalAlignment="Center"/>
              <Button Name="btnChangeTracks" Content="Submit" Click="btnChangeTrack_Click" />
          </StackPanel>
          <ListBox x:Name="lbAvailableVideoTracks" Height="200" Width="200" Background="Transparent" HorizontalAlignment="Left" 
                   ScrollViewer.VerticalScrollMode="Enabled" ScrollViewer.VerticalScrollBarVisibility="Visible">
              <ListBox.ItemTemplate>
                  <DataTemplate>
                      <CheckBox Content="{Binding Bitrate}" IsChecked="{Binding isChecked, Mode=TwoWay}"/>
                  </DataTemplate>
              </ListBox.ItemTemplate>
          </ListBox>
         </StackPanel>

3.  **Ctrl+S**를 눌러 변경 내용을 저장합니다.

**코드 숨김 파일을 수정하려면**

1.  솔루션 탐색기에서 **MainPage.xaml**을 마우스 오른쪽 단추로 클릭하고 **코드 보기**를 클릭합니다.
2.  SSPlayer 네임스페이스 내에 새 클래스를 추가합니다.

         #region class Track
         public class Track
         {
             private IManifestTrack trackInfo;
             public string _bitrate;
             public bool isCheckedValue;

             public IManifestTrack TrackInfo
             {
                 get { return trackInfo; }
                 set { trackInfo = value; }
             }

             public string Bitrate
             {
                 get { return _bitrate; }
                 set { _bitrate = value; }
             }

             public bool isChecked
             {
                 get { return isCheckedValue; }
                 set
                 {
                     isCheckedValue = value;
                 }
             }

             public Track(IManifestTrack trackInfoIn)
             {
                 trackInfo = trackInfoIn;
                 _bitrate = trackInfoIn.Bitrate.ToString();
             }
             //public Track() { }
         }
         #endregion class Track

3.  MainPage 클래스의 시작 부분에 다음 변수 정의를 추가합니다.

         private List<Track> availableTracks;

4.  MainPage 클래스 내에 다음 지역을 추가합니다.

         #region track selection
         /// <summary>
         /// Functionality to select video streams
         /// </summary>

         /// This Function gets the tracks for the selected video stream
         public void getTracks(Manifest manifestObject)
         {
             availableTracks = new List<Track>();

             IManifestStream videoStream = getVideoStream();
             IReadOnlyList<IManifestTrack> availableTracksLocal = videoStream.AvailableTracks;
             IReadOnlyList<IManifestTrack> selectedTracksLocal = videoStream.SelectedTracks;

             try
             {
                 for (int i = 0; i < availableTracksLocal.Count; i++)
                 {
                     Track thisTrack = new Track(availableTracksLocal[i]);
                     thisTrack.isChecked = true;

                     for (int j = 0; j < selectedTracksLocal.Count; j++)
                     {
                         string selectedTrackName = selectedTracksLocal[j].Bitrate.ToString();
                         if (selectedTrackName.Equals(thisTrack.Bitrate))
                         {
                             thisTrack.isChecked = true;
                             break;
                         }
                     }
                     availableTracks.Add(thisTrack);
                 }
             }
             catch (Exception e)
             {
                 txtStatus.Text = e.Message;
             }
         }

         // This function gets the video stream that is playing
         private IManifestStream getVideoStream()
         {
             IManifestStream videoStream = null;
             for (int i = 0; i < manifestObject.SelectedStreams.Count; i++)
             {
                 if (manifestObject.SelectedStreams[i].Type == MediaStreamType.Video)
                 {
                     videoStream = manifestObject.SelectedStreams[i];
                     break;
                 }
             }
             return videoStream;
         }

         // This function set the UI list box control ItemSource
         private async void refreshAvailableTracksListBoxItemSource()
         {
             try
             {
                 // Update the track check box list on the UI 
                 await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, ()
                     => { lbAvailableVideoTracks.ItemsSource = availableTracks; });
             }
             catch (Exception e)
             {
                 txtStatus.Text = "Error: " + e.Message;
             }        
         }

         // This function creates a list of the selected tracks.
         private void createSelectedTracksList(List<IManifestTrack> selectedTracks)
         {
             // Create a list of selected tracks
             for (int j = 0; j < availableTracks.Count; j++)
             {
                 if (availableTracks[j].isCheckedValue == true)
                 {
                     selectedTracks.Add(availableTracks[j].TrackInfo);
                 }
             }
         }

         // This function selects the tracks based on user selection 
         private void changeTracks(List<IManifestTrack> selectedTracks)
         {
             IManifestStream videoStream = getVideoStream();
             try
             {
                 videoStream.SelectTracks(selectedTracks);
             }
             catch (Exception ex)
             {
                 txtStatus.Text = ex.Message;
             }
         }
         #endregion track selection

5.  mediaElement\_ManifestReady 메서드를 찾은 후 함수의 끝에 다음 코드를 추가합니다.

         getTracks(manifestObject);
         refreshAvailableTracksListBoxItemSource();

6.  MainPage 클래스 내에서 UI 단추 클릭 이벤트 지역을 찾은 후 다음 함수 정의를 추가합니다.

         private void btnChangeStream_Click(object sender, RoutedEventArgs e)
         {
             List<IManifestStream> selectedStreams = new List<IManifestStream>();

             // Create a list of the selected streams
             createSelectedStreamsList(selectedStreams);

             // Change streams on the presentation
             changeStreams(selectedStreams);
         }

**응용 프로그램을 컴파일 및 테스트하려면**

1.  **F6** 키를 눌러 프로젝트를 컴파일합니다.
2.  **F5** 키를 눌러 응용 프로그램을 실행합니다.
3.  응용 프로그램 맨 위에 기본 부드러운 스트리밍 URL을 사용하거나 다른 URL을 입력할 수 있습니다.
4.  **원본 설정**을 클릭합니다.
5.  기본적으로 비디오 스트림의 모든 트랙이 선택됩니다. 비트 전송률 변경을 실험하려면 사용 가능한 가장 낮은 비트 전송률을 선택한 후 사용 가능한 가장 높은 비트 전송률을 선택합니다. 각 변경 후에 제출을 클릭해야 합니다. 비디오 품질 변경을 확인할 수 있습니다.

단원 4를 완료했습니다. 이 단원에서는 트랙 선택 기능을 추가했습니다.

기타 리소스:
============

-   [고급 기능이 포함된 부드러운 스트리밍 Windows 8 JavaScript 응용 프로그램을 빌드하는 방법](http://blogs.iis.net/cenkd/archive/2012/08/10/how-to-build-a-smooth-streaming-windows-8-javascript-application-with-advanced-features.aspx)
-   [부드러운 스트리밍 기술 개요](http://www.iis.net/learn/media/on-demand-smooth-streaming/smooth-streaming-technical-overview)

