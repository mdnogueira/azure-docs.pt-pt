---
title: "Tutorial de aplicação da loja Windows de transmissão em fluxo uniforme | Microsoft Docs"
description: "Saiba como utilizar Media Services do Azure para criar uma aplicação da loja Windows c# com o controlo de um MediaElement de XML para transmissão em fluxo uniforme reprodução conteúdo."
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: 0fa5d8c5-3d5f-4886-ae55-fb6de4f5256d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2017
ms.author: juliako
ms.openlocfilehash: b4f8855fe6480bc58acfbbb53819f6eabe362bdb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-build-a-smooth-streaming-windows-store-application"></a>Como criar uma aplicação da loja Windows de transmissão em fluxo de uniforme

O uniforme de transmissão em fluxo cliente SDK para Windows 8 permite aos programadores criarem aplicações da loja Windows que podem reproduzir o conteúdo a pedido e em direto transmissão em fluxo uniforme. Para além de reprodução básica de conteúdos de transmissão em fluxo uniforme, o SDK também fornece funcionalidades avançadas, como o Microsoft PlayReady proteção, restrição de nível de qualidade, em direto DVR, a sequência de áudio mudar, à escuta de atualizações de estado (por exemplo, alterações de nível de qualidade) e eventos de erro e assim sucessivamente. Para mais informações sobre as funcionalidades suportadas, consulte o [notas de versão](http://www.iis.net/learn/media/smooth-streaming/smooth-streaming-client-sdk-for-windows-8-release-notes). Para obter mais informações, consulte [Player Framework para o Windows 8](http://playerframework.codeplex.com/). 

Este tutorial contém quatro lições:

1. Criar uma aplicação de arquivo de transmissão em fluxo de uniforme básica
2. Adicionar uma barra do controlo de deslize para controlar o progresso de suporte de dados
3. Selecione os fluxos de transmissão em fluxo uniforme
4. Selecione a atividade de transmissão em fluxo uniforme

## <a name="prerequisites"></a>Pré-requisitos
> [!NOTE]
> Versão de projetos de loja Windows 8.1 e anterior não são suportadas no Visual Studio 2017.  Para obter mais informações, veja [Visual Studio 2017 Platform Targeting and Compatibility](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs) (Segmentação e Compatibilidade de Plataformas do Visual Studio 2017)

* Windows 8 de 32 bits ou 64 bits.
* Versões de Visual Studio 2012 através de 2015.
* [Uniforme Microsoft transmissão em fluxo cliente SDK para Windows 8](http://visualstudiogallery.msdn.microsoft.com/04423d13-3b3e-4741-a01c-1ae29e84fea6?SRC=Homehttp://visualstudiogallery.msdn.microsoft.com/04423d13-3b3e-4741-a01c-1ae29e84fea6?SRC=Home).

A solução concluída para cada lesson pode ser transferida a partir de exemplos de código do programador MSDN (galeria de códigos): 

* [Lesson 1](http://code.msdn.microsoft.com/Smooth-Streaming-Client-0bb1471f) - um uniforme simples Windows 8 Media Player, transmissão em fluxo 
* [Lesson 2](http://code.msdn.microsoft.com/A-simple-Windows-8-Smooth-ee98f63a) - um uniforme simples Windows 8 Media Player com uma barra do controlo de deslize de transmissão em fluxo controlo, 
* [Lesson 3](http://code.msdn.microsoft.com/A-Windows-8-Smooth-883c3b44) - um uniforme do Windows 8 leitor de multimédia com a seleção de fluxo, transmissão em fluxo  
* [Lesson 4](http://code.msdn.microsoft.com/A-Windows-8-Smooth-aa9e4907) - um uniforme do Windows 8 leitor de multimédia com a seleção de controlar de transmissão em fluxo.

## <a name="lesson-1-create-a-basic-smooth-streaming-store-application"></a>Lesson 1: Criar uma aplicação de arquivo de transmissão em fluxo de uniforme básica

Este lesson, criará uma aplicação da loja do Windows com um MediaElement o controlo de transmissão em fluxo uniforme de reproduzir conteúdo.  A aplicação em execução tem o seguinte:

![Exemplo de aplicação da loja de Windows de transmissão em fluxo uniforme][PlayerApplication]

Para obter mais informações sobre o desenvolvimento das aplicações da loja Windows, consulte [desenvolver excelente aplicações para Windows 8](http://msdn.microsoft.com/windows/apps/br229512.aspx). Este lesson contém os seguintes procedimentos:

1. Criar um projeto de loja Windows
2. Estruturar a interface de utilizador (XAML)
3. Modificar o ficheiro code-behind
4. Compilar e testar a aplicação

**Para criar um projeto de loja Windows**

1. Execute o Visual Studio; são suportadas versões 2012 através de 2015.
2. No menu **FICHEIRO**, clique em **Novo** e, em seguida, clique em **Projeto**.
3. Caixa de diálogo novo projeto, escreva ou selecione os seguintes valores:

| Nome | Valor |
| --- | --- |
| Grupo de modelo |Instalado/modelos/Visual c# / da loja Windows |
| Modelo |Aplicação em branco (XAML) |
| Nome |SSPlayer |
| Localização |C:\SSTutorials |
| Nome da solução |SSPlayer |
| Criar o diretório para a solução |(selecionado) |

1. Clique em **OK**.

**Para adicionar uma referência para o SDK do cliente de transmissão em fluxo uniforme**

1. A partir do Explorador de soluções, faça duplo clique **SSPlayer**e, em seguida, clique em **Adicionar referência**.
2. Escreva ou selecione os seguintes valores:

| Nome | Valor |
| --- | --- |
| Grupo de referência |Extensões do Windows |
| Referência |Selecione uniforme Microsoft cliente SDK transmissão em fluxo para o Windows 8 e o Microsoft Visual C++ Runtime pacote |

1. Clique em **OK**. 

Depois de adicionar as referências, tem de selecionar a plataforma especificada (x64 ou x86), adicionar referências não irão funcionar para a configuração de plataforma Any CPU.  No Explorador de soluções, verá amarelo aviso marca estas adicionada referências.

**Para estruturar a interface de utilizador de leitor**

1. No Explorador de soluções, faça duplo clique **MainPage.xaml** para abri-lo na vista de estrutura.
2. Localize o  **&lt;grelha&gt;**  e  **&lt;/Grid&gt;**  etiquetas o ficheiro XAML e cole o seguinte código entre duas etiquetas:

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
   
   O controlo de MediaElement é utilizado para suportes de dados de reprodução. O controlo de deslize com o nome sliderProgress será utilizado no lesson seguinte para controlar o progresso de suporte de dados.
3. Prima **CTRL + G** para guardar o ficheiro.

O controlo de MediaElement não suporta a transmissão em fluxo uniforme conteúdo out-of-box. Para ativar o suporte de transmissão em fluxo uniforme, tem de registar o processador de fluxo de bytes de transmissão em fluxo uniforme através da extensão de nome de ficheiro e tipo de MIME.  Para registar, pode utilizar o método MediaExtensionManager.RegisterByteStremHandler do espaço de nomes Windows.Media.

Neste ficheiro XAML, alguns processadores de eventos estão associados com os controlos.  Tem de definir os processadores de eventos.

**Para modificar o ficheiro code-behind**

1. A partir do Explorador de soluções, faça duplo clique **MainPage.xaml**e, em seguida, clique em **Vista código**.
2. Na parte superior do ficheiro, adicione o seguinte utilizando a instrução:
   
        using Windows.Media;
3. No início do **MainPage** classe, adicione o seguinte membro de dados:
   
         private MediaExtensionManager extensions = new MediaExtensionManager();
4. No final de **MainPage** construtor, adicione as seguintes duas linhas:
   
        extensions.RegisterByteStreamHandler("Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", ".ism", "text/xml");
        extensions.RegisterByteStreamHandler("Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", ".ism", "application/vnd.ms-sstr+xml");
5. No final de **MainPage** classe, cole o seguinte código:
   
         # region UI Button Click Events
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

O processador de eventos sliderProgress_PointerPressed é definido aqui.  Existem mais funciona para funcionar, que vai ser abordada o seguinte lesson deste tutorial.
6. Prima **CTRL + G** para guardar o ficheiro.

A terminar o ficheiro code-behind deverá ter o seguinte aspeto:

![Codeview no Visual Studio do uniforme de transmissão em fluxo aplicação da loja Windows][CodeViewPic]

**Para compilar e testar a aplicação**

1. Do **criar** menu, clique em **do Configuration Manager**.
2. Alteração **plataforma do Active Directory solução** para corresponder à sua plataforma de desenvolvimento.
3. Prima **F6** para compilar o projeto. 
4. Prima **F5** para executar a aplicação.
5. Na parte superior da aplicação, pode utilizar o URL de transmissão em fluxo uniforme predefinido ou introduza outro. 
6. Clique em **definir origem**. Porque **automática reproduzir** está ativada por predefinição, o suporte de dados deverá reproduzir automaticamente.  Pode controlar o suporte de dados utilizando o **reproduzir**, **pausa** e **parar** botões.  Pode controlar o volume de suporte de dados utilizando o controlo de deslize vertical.  No entanto o controlo de deslize horizontal para controlar o progresso de suporte de dados ainda não está totalmente implementado. 

Concluiu lesson1.  Este lesson, utiliza um controlo MediaElement para reproduzir o conteúdo de transmissão em fluxo uniforme.  Lesson seguinte, irá adicionar um controlo de deslize para controlar o progresso do conteúdo de transmissão em fluxo uniforme.

## <a name="lesson-2-add-a-slider-bar-to-control-the-media-progress"></a>Lesson 2: Adicionar uma barra do controlo de deslize para controlar o progresso de suporte de dados

Lesson 1, criou uma aplicação da loja do Windows com o controlo de um MediaElement XAML para reproduzir conteúdo de multimédia de transmissão em fluxo uniforme.  Se trata de algumas funções de suporte de dados básicas, como iniciar, parar e colocar em pausa.  Este lesson, irá adicionar um controlo de barra do controlo de deslize para a aplicação.

Neste tutorial, utilizaremos um temporizador para atualizar a posição do controlo de deslize com base na posição atual do controlo MediaElement.  O início de controlo de deslize e de fim tempo que também têm de ser atualizadas em caso de conteúdos em direto.  Isto pode ser melhor processado o evento de atualização de origem adaptável.

Origens de suportes de dados são objetos geram dados de suporte de dados.  A resolução de origem assuma uma sequência de URL ou byte e cria a origem de suporte de dados adequado para esse conteúdo.  A resolução de origem é a forma de padrão para as aplicações criar as origens de suportes de dados. 

Este lesson contém os seguintes procedimentos:

1. Registar o processador de transmissão em fluxo uniforme 
2. Adicionar os processadores de eventos ao nível do Gestor de origem adaptável
3. Adicionar os processadores de eventos ao nível da origem adaptável
4. Adicionar processadores de eventos de MediaElement
5. Adicionar controlo de deslize barra código relacionado
6. Compilar e testar a aplicação

**Para registar o processador de fluxo de bytes de transmissão em fluxo uniforme e passar o propertyset**

1. No Explorador de soluções, com o botão direito clique **MainPage.xaml**e, em seguida, clique em **Vista código**.
2. No início do ficheiro, adicione o seguinte utilizando a instrução:

        using Microsoft.Media.AdaptiveStreaming;
3. No início da classe MainPage, adicione os seguintes membros de dados:

         private Windows.Foundation.Collections.PropertySet propertySet = new Windows.Foundation.Collections.PropertySet();             
         private IAdaptiveSourceManager adaptiveSourceManager;
4. Dentro do **MainPage** construtor, adicione o seguinte código após o **isto. Inicializar Components();**  linha e o registo de código linhas escritas o lesson anterior:

        // Gets the default instance of AdaptiveSourceManager which manages Smooth 
        //Streaming media sources.
        adaptiveSourceManager = AdaptiveSourceManager.GetDefault();
        // Sets property key value to AdaptiveSourceManager default instance.
        // {A5CE1DE8-1D00-427B-ACEF-FB9A3C93DE2D}" must be hardcoded.
        propertySet["{A5CE1DE8-1D00-427B-ACEF-FB9A3C93DE2D}"] = adaptiveSourceManager;
5. Dentro do **MainPage** construtor, modifique os dois métodos de RegisterByteStreamHandler para adicionar o estabelecido parâmetros:

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
6. Prima **CTRL + G** para guardar o ficheiro.

**Para adicionar o processador de eventos ao nível do Gestor de origem adaptável**

1. No Explorador de soluções, com o botão direito clique **MainPage.xaml**e, em seguida, clique em **Vista código**.
2. Dentro do **MainPage** classe, adicione o seguinte membro de dados:
   
     privada AdaptiveSource adaptiveSource = null;
3. No final de **MainPage** classe, adicione o processador de eventos seguintes:
   
         # region Adaptive Source Manager Level Events
         private void mediaElement_AdaptiveSourceOpened(AdaptiveSource sender, AdaptiveSourceOpenedEventArgs args)
         {

            adaptiveSource = args.AdaptiveSource;
         }

         # endregion Adaptive Source Manager Level Events
4. No final de **MainPage** construtor, adicione a seguinte linha para subscrever o evento de abrir origem adaptável:
   
         adaptiveSourceManager.AdaptiveSourceOpenedEvent += 
           new AdaptiveSourceOpenedEventHandler(mediaElement_AdaptiveSourceOpened);
5. Prima **CTRL + G** para guardar o ficheiro.

**Para adicionar processadores de eventos ao nível de origem adaptável**

1. No Explorador de soluções, com o botão direito clique **MainPage.xaml**e, em seguida, clique em **Vista código**.
2. Dentro do **MainPage** classe, adicione o seguinte membro de dados:
   
     privada AdaptiveSourceStatusUpdatedEventArgs adaptiveSourceStatusUpdate;   privada manifestObject manifesto;
3. No final de **MainPage** classe, adicione os seguinte processadores de eventos:

         # region Adaptive Source Level Events
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

         # endregion Adaptive Source Level Events
4. No final de **mediaElement AdaptiveSourceOpened** método, adicione o seguinte código para subscrever os eventos:
   
         adaptiveSource.ManifestReadyEvent +=

                    mediaElement_ManifestReady;
         adaptiveSource.AdaptiveSourceStatusUpdatedEvent += 

            mediaElement_AdaptiveSourceStatusUpdated;
         adaptiveSource.AdaptiveSourceFailedEvent += 

            mediaElement_AdaptiveSourceFailed;
5. Prima **CTRL + G** para guardar o ficheiro.

Estão disponíveis os mesmos eventos no adaptável origem do Configuration Manager nível, que pode ser utilizada para processamento de funcionalidade comuns a todos os elementos de suporte de dados na aplicação. Cada AdaptiveSource inclui os seus próprios eventos e todos os eventos de AdaptiveSource irão ser cascaded em AdaptiveSourceManager.

**Para adicionar processadores de eventos do elemento de suporte de dados**

1. No Explorador de soluções, com o botão direito clique **MainPage.xaml**e, em seguida, clique em **Vista código**.
2. No final de **MainPage** classe, adicione os seguinte processadores de eventos:

         # region Media Element Event Handlers
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

         # endregion Media Element Event Handlers
3. No final de **MainPage** construtor, adicione o seguinte código ao índice para os eventos:

         mediaElement.MediaOpened += MediaOpened;
         mediaElement.MediaEnded += MediaEnded;
         mediaElement.MediaFailed += MediaFailed;
4. Prima **CTRL + G** para guardar o ficheiro.

**Para adicionar a barra do controlo de deslize relacionados com o código**

1. No Explorador de soluções, com o botão direito clique **MainPage.xaml**e, em seguida, clique em **Vista código**.
2. No início do ficheiro, adicione o seguinte utilizando a instrução:
      
        using Windows.UI.Core;
3. Dentro do **MainPage** classe, adicione os seguintes membros de dados:
   
         public static CoreDispatcher _dispatcher;
         private DispatcherTimer sliderPositionUpdateDispatcher;
4. No final de **MainPage** construtor, adicione o seguinte código:
   
         _dispatcher = Window.Current.Dispatcher;
         PointerEventHandler pointerpressedhandler = new PointerEventHandler(sliderProgress_PointerPressed);
         sliderProgress.AddHandler(Control.PointerPressedEvent, pointerpressedhandler, true);    
5. No final de **MainPage** classe, adicione o seguinte código:

         # region sliderMediaPlayer
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

         # endregion sliderMediaPlayer
      
>[!NOTE]
>CoreDispatcher é utilizada para efetuar alterações ao thread da IU a partir do Thread de IU não. Em caso de congestionamento no thread do emissor, o programador pode optar por utilizar dispatcher fornecida pelo elemento de IU he/she pretende atualizar.  Por exemplo:
   
         await sliderProgress.Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () => { TimeSpan 

         timespan = new TimeSpan(adaptiveSourceStatusUpdate.EndTime); 
         double absvalue  = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero); 

         sliderProgress.Maximum = absvalue; }); 
6. No final de **mediaElement_AdaptiveSourceStatusUpdated** método, adicione o seguinte código:

         setSliderStartTime(args.StartTime);
         setSliderEndTime(args.EndTime);
7. No final de **MediaOpened** método, adicione o seguinte código:

         sliderProgress.StepFrequency = SliderFrequency(mediaElement.NaturalDuration.TimeSpan);
         sliderProgress.Width = mediaElement.Width;
         setupTimer();
8. Prima **CTRL + G** para guardar o ficheiro.

**Para compilar e testar a aplicação**

1. Prima **F6** para compilar o projeto. 
2. Prima **F5** para executar a aplicação.
3. Na parte superior da aplicação, pode utilizar o URL de transmissão em fluxo uniforme predefinido ou introduza outro. 
4. Clique em **definir origem**. 
5. Teste a barra do controlo de deslize.

Concluiu lesson 2.  Este lesson adicionou um controlo de deslize para a aplicação. 

## <a name="lesson-3-select-smooth-streaming-streams"></a>Lesson 3: Selecione fluxos de transmissão em fluxo uniforme
Transmissão em fluxo uniforme é com capacidade para transmitir o conteúdo com vários controla áudio de idioma que podem ser selecionados, os visualizadores autorizados.  Este lesson, irá ativar visualizadores autorizados selecionar fluxos. Este lesson contém os seguintes procedimentos:

1. Modificar o ficheiro XAML
2. Modificar o ficheiro de behand de código
3. Compilar e testar a aplicação

**Para modificar o ficheiro XAML**

1. A partir do Explorador de soluções, faça duplo clique **MainPage.xaml**e, em seguida, clique em **estruturador de vistas**.
2. Localizar &lt;Grid.RowDefinitions&gt;e modificar o RowDefinitions, para que se pareça com:
   
         <Grid.RowDefinitions>            
            <RowDefinition Height="20"/>
            <RowDefinition Height="50"/>
            <RowDefinition Height="100"/>
            <RowDefinition Height="80"/>
            <RowDefinition Height="50"/>
         </Grid.RowDefinitions>
3. Dentro do &lt;grelha&gt;&lt;/Grid&gt; etiquetas, adicione o seguinte código para definir um controlo de listbox, pelo que os utilizadores podem ver a lista de fluxos disponíveis e selecione fluxos:

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
4. Prima **CTRL + G** para guardar as alterações.

**Para modificar o ficheiro code-behind**

1. A partir do Explorador de soluções, faça duplo clique **MainPage.xaml**e, em seguida, clique em **Vista código**.
2. No interior do espaço de nomes SSPlayer, adicione uma nova classe:
   
        #region class Stream
   
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
3. No início da classe MainPage, adicione as seguintes definições de variável:
   
         private List<Stream> availableStreams;
         private List<Stream> availableAudioStreams;
         private List<Stream> availableTextStreams;
         private List<Stream> availableVideoStreams;
4. Na classe MainPage, adicione a região seguinte:
   
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
5. Localizar o método mediaElement_ManifestReady, acrescentar o seguinte código no final da função:
   
        getStreams(manifestObject);
        refreshAvailableStreamsListBoxItemSource();
   
    Por isso, quando o manifesto MediaElement estiver pronto, o código obtém uma lista das sequências disponíveis e preenche a caixa de listagem de IU com a lista.
6. Na classe MainPage, localize a IU de botões de região de eventos de clique e, em seguida, adicione a seguinte definição de função:
   
        private void btnChangeStream_Click(object sender, RoutedEventArgs e)
        {
            List<IManifestStream> selectedStreams = new List<IManifestStream>();
   
            // Create a list of the selected streams
            createSelectedStreamsList(selectedStreams);
   
            // Change streams on the presentation
            changeStreams(selectedStreams);
        }

**Para compilar e testar a aplicação**

1. Prima **F6** para compilar o projeto. 
2. Prima **F5** para executar a aplicação.
3. Na parte superior da aplicação, pode utilizar o URL de transmissão em fluxo uniforme predefinido ou introduza outro. 
4. Clique em **definir origem**. 
5. O idioma predefinido é audio_eng. Tente alternar entre audio_eng e audio_es. Everytime, selecionar um novo fluxo, tem de clicar no botão de envio.

Concluiu lesson 3.  Este lesson, adicione a funcionalidade para escolher fluxos.

## <a name="lesson-4-select-smooth-streaming-tracks"></a>Lesson 4: Controla a transmissão em fluxo uniforme de selecione
Uma apresentação de transmissão em fluxo uniforme pode conter vários ficheiros de vídeo codificados com níveis diferentes de qualidade (bit taxas) e as resoluções. Este lesson, irá ativar os utilizadores selecionem controla. Este lesson contém os seguintes procedimentos:

1. Modificar o ficheiro XAML
2. Modificar o ficheiro de behand de código
3. Compilar e testar a aplicação

**Para modificar o ficheiro XAML**

1. A partir do Explorador de soluções, faça duplo clique **MainPage.xaml**e, em seguida, clique em **estruturador de vistas**.
2. Localize o &lt;grelha&gt; etiqueta com o nome **gridStreamAndBitrateSelection**, acrescentar o seguinte código no final da etiqueta:
   
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
3. Prima **CTRL + G** ao guardar as alterações de putador

**Para modificar o ficheiro code-behind**

1. A partir do Explorador de soluções, faça duplo clique **MainPage.xaml**e, em seguida, clique em **Vista código**.
2. No interior do espaço de nomes SSPlayer, adicione uma nova classe:
   
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
3. No início da classe MainPage, adicione as seguintes definições de variável:
   
        private List<Track> availableTracks;
4. Na classe MainPage, adicione a região seguinte:
   
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
5. Localizar o método mediaElement_ManifestReady, acrescentar o seguinte código no final da função:
   
         getTracks(manifestObject);
         refreshAvailableTracksListBoxItemSource();
6. Na classe MainPage, localize a IU de botões de região de eventos de clique e, em seguida, adicione a seguinte definição de função:
   
         private void btnChangeStream_Click(object sender, RoutedEventArgs e)
         {
            List<IManifestStream> selectedStreams = new List<IManifestStream>();

            // Create a list of the selected streams
            createSelectedStreamsList(selectedStreams);

            // Change streams on the presentation
            changeStreams(selectedStreams);
         }

**Para compilar e testar a aplicação**

1. Prima **F6** para compilar o projeto. 
2. Prima **F5** para executar a aplicação.
3. Na parte superior da aplicação, pode utilizar o URL de transmissão em fluxo uniforme predefinido ou introduza outro. 
4. Clique em **definir origem**. 
5. Por predefinição, todas as controla do fluxo de vídeo estão selecionadas. Para testar as alterações de taxa de bits, pode selecionar a taxa de bits mais baixa disponível e, em seguida, selecione a velocidade de bits mais elevada disponível. Tem de clicar em submeter após cada alteração.  Pode ver as alterações de qualidade do vídeo.

Concluiu lesson 4.  Este lesson, adicione a funcionalidade para escolher controla.

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="other-resources"></a>Outros recursos:
* [Como criar uma aplicação de uniforme JavaScript de 8 Windows de transmissão em fluxo com funcionalidades avançadas](http://blogs.iis.net/cenkd/archive/2012/08/10/how-to-build-a-smooth-streaming-windows-8-javascript-application-with-advanced-features.aspx)
* [Uniforme transmissão em fluxo descrição geral técnica do](http://www.iis.net/learn/media/on-demand-smooth-streaming/smooth-streaming-technical-overview)

[PlayerApplication]: ./media/media-services-build-smooth-streaming-apps/SSClientWin8-1.png
[CodeViewPic]: ./media/media-services-build-smooth-streaming-apps/SSClientWin8-2.png

