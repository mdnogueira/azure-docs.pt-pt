---
title: "Plugin de transmissão em fluxo uniforme para a estrutura de suporte de dados de código aberto"
description: "Saiba como utilizar o plug-in Azure Media Services transmissão em fluxo uniforme para a estrutura de suporte de dados de origem aberto da Adobe."
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: 6068151f-b6b0-4507-9346-f03416d3d572
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2016
ms.author: juliako
ms.openlocfilehash: 9c764f176ae75085320882de3fb26d8e7d52daaf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-the-microsoft-smooth-streaming-plugin-for-the-adobe-open-source-media-framework"></a>Como utilizar o uniforme Microsoft Plug-in de transmissão em fluxo para a estrutura de suporte de dados de código aberto da Adobe
## <a name="overview"></a>Descrição geral
O plug-in Microsoft transmissão em fluxo uniforme para abrir origem de suporte de dados Framework 2.0 (SS para OSMF) expande as capacidades de predefinição de OSMF e adiciona a reprodução de conteúdo Microsoft transmissão em fluxo uniforme ao jogadores OSMF novas e existentes. O plug-in também adiciona capacidades de reprodução de transmissão em fluxo uniforme para reprodução de suporte de dados de Strobe (SMP).

SS para OSMF inclui duas versões do plug-in:

* Estático transmissão em fluxo uniforme Plug-in para OSMF (.swc)
* Dinâmica transmissão em fluxo uniforme Plug-in para OSMF (.swf)

Este documento parte do princípio de que o leitor tem um conhecimento prático geral de OSMF e OSMF plug-ins. Para obter mais informações sobre OSMF, consulte a documentação sobre o [site oficial do OSMF](http://osmf.org/).

### <a name="smooth-streaming-plugin-for-osmf-20"></a>Uniforme Plug-in de transmissão em fluxo para OSMF 2.0
O plug-in suporta carregamento e reprodução de conteúdos de transmissão em fluxo uniforme a pedido com as seguintes funcionalidades:

* Reprodução de transmissão em fluxo uniforme a pedido (Play, pare de pausa, procura)
* Reprodução de transmissão em fluxo uniforme em direto (Play)
* Funções DVR em direto (colocar em pausa, procura, reprodução DVR, aceda-to-Live)
* Suporte para as vídeo codecs - 264
* Suporte para áudio codecs - AAC
* Vários idiomas áudio mudar com APIs incorporadas OSMF
* Seleção de qualidade de reprodução de máximo com APIs incorporadas OSMF
* Sidecar fechado legendas com Plug-in de legendas OSMF
* Adobe&reg; Flash&reg; Player 11.4 ou superior.
* Esta versão suporta apenas OSMF 2.0.

## <a name="supported-features-and-known-issues"></a>Funcionalidades suportadas e problemas conhecidos
Para obter uma lista completa das funcionalidades suportadas, funcionalidades não suportadas e problemas conhecidos, consulte [neste documento](http://download.microsoft.com/download/3/1/B/31B63D97-574E-4A8D-BF8D-170744181724/Smooth_Streaming_Plugin_for_OSMF.pdf).

## <a name="loading-the-plugin"></a>Carregar o plug-in
É possível carregar o plug-ins de OSMF estaticamente (no momento da compilação) ou dinamicamente (em tempo de execução). O plug-in transmissão em fluxo uniforme para transferência OSMF inclui versões estáticas e dinâmicas.

* A carregar estática: carregar estaticamente, é necessário um ficheiro de biblioteca estática (SWC). Estáticos plug-ins são adicionados como uma referência para os projetos e intercalação dentro do ficheiro de resultado final o momento da compilação.
* A carregar dinâmica: carregar dinamicamente, é necessário um ficheiro de (SWF) pré-compilada. Dinâmicas plug-ins são carregadas em tempo de execução e não incluídos no resultado do projeto. (Compilada saída) Plug-ins dinâmica podem ser carregadas utilizando os protocolos HTTP e o ficheiro.

Para obter mais informações sobre a carregar estática e dinâmica, consulte oficial [página de plug-in OSMF](http://osmf.org/dev/osmf/OtherPDFs/osmf_plugin_dev_guide.pdf).

### <a name="ss-for-osmf-static-loading"></a>SS para carregamento estático OSMF
O fragmento de código abaixo mostra como carregar o plug-in de SS para OSMF estaticamente e reproduzir um vídeo básico utilizando OSMF MediaFactory classe. Antes de incluindo SS código OSMF, certifique-se que a referência de projecto inclui o plug-in de estático de "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swc".

```
package 
{

    import com.microsoft.azure.media.AdaptiveStreamingPluginInfo;

    import flash.display.*;
    import org.osmf.media.*;
    import org.osmf.containers.MediaContainer;
    import org.osmf.events.MediaErrorEvent;
    import org.osmf.events.MediaFactoryEvent;
    import org.osmf.events.MediaPlayerStateChangeEvent;
    import org.osmf.layout.*;



    [SWF(width="1024", height="768", backgroundColor='#405050', frameRate="25")]
    public class TestPlayer extends Sprite
    {        
        public var _container:MediaContainer;
        public var _mediaFactory:DefaultMediaFactory;
        private var _mediaPlayerSprite:MediaPlayerSprite;


        public function TestPlayer( )
        {
            stage.quality = StageQuality.HIGH;

            initMediaPlayer();

        }

        private function initMediaPlayer():void
        {

            // Create the container (sprite) for managing display and layout
            _mediaPlayerSprite = new MediaPlayerSprite();    
            _mediaPlayerSprite.addEventListener(MediaErrorEvent.MEDIA_ERROR, onPlayerFailed);
            _mediaPlayerSprite.addEventListener(MediaPlayerStateChangeEvent.MEDIA_PLAYER_STATE_CHANGE, onPlayerStateChange);
            _mediaPlayerSprite.scaleMode = ScaleMode.NONE;
            _mediaPlayerSprite.width = stage.stageWidth;
            _mediaPlayerSprite.height = stage.stageHeight;
            //Adds the container to the stage
            addChild(_mediaPlayerSprite);

            // Create a mediafactory instance
            _mediaFactory = new DefaultMediaFactory();

            // Add the listeners for PLUGIN_LOADING
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD,onPluginLoaded);
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD_ERROR, onPluginLoadFailed );

            // Load the plugin class 
            loadAdaptiveStreamingPlugin( );  

        }

        private function loadAdaptiveStreamingPlugin( ):void
        {
            var pluginResource:MediaResourceBase;

            pluginResource = new PluginInfoResource(new AdaptiveStreamingPluginInfo( )); 
            _mediaFactory.loadPlugin( pluginResource ); 
        }

        private function onPluginLoaded( event:MediaFactoryEvent ):void
        {
            // The plugin is loaded successfully.
            // Your web server needs to host a valid crossdomain.xml file to allow plugin to download Smooth Streaming files.
        loadMediaSource("http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest")

        }

        private function onPluginLoadFailed( event:MediaFactoryEvent ):void
        {
            // The plugin is failed to load ...
        }


        private function onPlayerStateChange(event:MediaPlayerStateChangeEvent) : void
        {
            var state:String;

            state =  event.state;

            switch (state)
            {
                case MediaPlayerState.LOADING: 

                    // A new source is started to load.

                    break;

                case  MediaPlayerState.READY :   
                    // Add code to deal with Player Ready when it is hit the first load after a source is loaded. 

                    break;

                case MediaPlayerState.BUFFERING :

                    break;

                case  MediaPlayerState.PAUSED :
                    break;      
                // other states ...          
            }
        }

        private function onPlayerFailed(event:MediaErrorEvent) : void
        {
            // Media Player is failed .           
        }

        private function loadMediaSource(sourceURL : String):void 
        {
            // Take an URL of SmoothStreamingSource's manifest and add it to the page.

            var resource:URLResource= new URLResource( sourceURL );

            var element:MediaElement = _mediaFactory.createMediaElement( resource );
            _mediaPlayerSprite.scaleMode = ScaleMode.LETTERBOX;
            _mediaPlayerSprite.width = stage.stageWidth;
            _mediaPlayerSprite.height = stage.stageHeight;

            // Add the media element
            _mediaPlayerSprite.media = element;
        }     

    }
}
```


### <a name="ss-for-osmf-dynamic-loading"></a>SS para carregamento dinâmico OSMF
O fragmento de código abaixo mostra como carregar o plug-in de SS para OSMF dinamicamente e reproduzir num nível básico vídeo de utilizar a classe de OSMF MediaFactory. Antes de incluindo SS código OSMF, copie o plug-in de dinâmico de "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf" para a pasta de projeto, se pretender carregar utilizando o protocolo de ficheiros ou copie num servidor web para a carga HTTP. Não é necessário incluir "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swc" em referências do projeto.

{de pacote

    import flash.display.*;
    import org.osmf.media.*;
    import org.osmf.containers.MediaContainer;
    import org.osmf.events.MediaErrorEvent;
    import org.osmf.events.MediaFactoryEvent;
    import org.osmf.events.MediaPlayerStateChangeEvent;
    import org.osmf.layout.*;
    import flash.events.Event;
    import flash.system.Capabilities;


    //Sets the size of the SWF

    [SWF(width="1024", height="768", backgroundColor='#405050', frameRate="25")]
    public class TestPlayer extends Sprite
    {        
        public var _container:MediaContainer;
        public var _mediaFactory:DefaultMediaFactory;
        private var _mediaPlayerSprite:MediaPlayerSprite;


        public function TestPlayer( )
        {
            stage.quality = StageQuality.HIGH;
            initMediaPlayer();
        }

        private function initMediaPlayer():void
        {

            // Create the container (sprite) for managing display and layout
            _mediaPlayerSprite = new MediaPlayerSprite();    
            _mediaPlayerSprite.addEventListener(MediaErrorEvent.MEDIA_ERROR, onPlayerFailed);
            _mediaPlayerSprite.addEventListener(MediaPlayerStateChangeEvent.MEDIA_PLAYER_STATE_CHANGE, onPlayerStateChange);

            //Adds the container to the stage
            addChild(_mediaPlayerSprite);

            // Create a mediafactory instance
            _mediaFactory = new DefaultMediaFactory();

            // Add the listeners for PLUGIN_LOADING
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD,onPluginLoaded);
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD_ERROR, onPluginLoadFailed );

            // Load the plugin class 
            loadAdaptiveStreamingPlugin( );  

        }

        private function loadAdaptiveStreamingPlugin( ):void
        {
            var pluginResource:MediaResourceBase;
            var adaptiveStreamingPluginUrl:String;

            // Your dynamic plugin web server needs to host a valid crossdomain.xml file to allow loading plugins.

            adaptiveStreamingPluginUrl = "http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf";
            pluginResource = new URLResource(adaptiveStreamingPluginUrl);
            _mediaFactory.loadPlugin( pluginResource ); 

        }

        private function onPluginLoaded( event:MediaFactoryEvent ):void
        {
            // The plugin is loaded successfully.

            // Your web server needs to host a valid crossdomain.xml file to allow plugin to download Smooth Streaming files.

    loadMediaSource("http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest")
        }

        private function onPluginLoadFailed( event:MediaFactoryEvent ):void
        {
            // The plugin is failed to load ...
        }


        private function onPlayerStateChange(event:MediaPlayerStateChangeEvent) : void
        {
            var state:String;

            state =  event.state;

            switch (state)
            {
                case MediaPlayerState.LOADING: 

                    // A new source is started to load.

                    break;

                case  MediaPlayerState.READY :   
                    // Add code to deal with Player Ready when it is hit the first load after a source is loaded. 

                    break;

                case MediaPlayerState.BUFFERING :

                    break;

                case  MediaPlayerState.PAUSED :
                    break;      
                // other states ...          
            }
        }

        private function onPlayerFailed(event:MediaErrorEvent) : void
        {
            // Media Player is failed .           
        }

        private function loadMediaSource(sourceURL : String):void 
        {
            // Take an URL of SmoothStreamingSource's manifest and add it to the page.

            var resource:URLResource= new URLResource( sourceURL );

            var element:MediaElement = _mediaFactory.createMediaElement( resource );
            _mediaPlayerSprite.scaleMode = ScaleMode.LETTERBOX;
            _mediaPlayerSprite.width = stage.stageWidth;
            _mediaPlayerSprite.height = stage.stageHeight;
            // Add the media element
            _mediaPlayerSprite.media = element;
        }     

    }
}

## <a name="strobe-media--playback-with-the-ss-odmf-dynamic-plugin"></a>Strobe reprodução de suporte de dados com o plug-in de dinâmica de SS ODMF
A transmissão em fluxo uniforme para OSMF dinâmica Plug-in é compatível com [reprodução de suporte de dados de Strobe (SMP)](http://osmf.org/strobe_mediaplayback.html). Pode utilizar o SS para o plug-in de OSMF para adicionar a reprodução de conteúdos de transmissão em fluxo uniforme a SMP. Para tal, copie "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf" num servidor web para a carga HTTP utilizando os seguintes passos:

1. Procurar o [página de configuração de reprodução de suporte de dados de Strobe](http://osmf.org/dev/2.0gm/setup.html). 
2. Definir o src a uma origem de transmissão em fluxo uniforme, (por exemplo, http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest) 
3. Efetue as alterações de configuração pretendida e clique em pré-visualização e a atualização.
   
   **Tenha em atenção** o servidor de conteúdo web tem um crossdomain.xml válido. 
4. Copie e cole o código para uma página HTML simple utilizando o editor de texto favorito, tal como no exemplo seguinte:

        <html>
        <body>
        <object width="920" height="640"> 
        <param name="movie" value="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf"></param>
        <param name="flashvars" value="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest &autoPlay=true"></param>
        <param name="allowFullScreen" value="true"></param>
        <param name="allowscriptaccess" value="always"></param>
        <param name="wmode" value="direct"></param>
        <embed src="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf" 
            type="application/x-shockwave-flash" 
            allowscriptaccess="always" 
            allowfullscreen="true" 
            wmode="direct" 
            width="920" 
            height="640" 
            flashvars=" src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true">
        </embed>
        </object>
        </body>
        </html>



1. Adicione o plug-in do OSMF de transmissão em fluxo uniforme o código de incorporação e guardar.
   
        <html>
        <object width="920" height="640"> 
        <param name="movie" value="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf"></param>
        <param name="flashvars" value="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true&plugin_AdaptiveStreamingPlugin=http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf&AdaptiveStreamingPlugin_retryLive=true&AdaptiveStreamingPlugin_retryInterval=10"></param>
        <param name="allowFullScreen" value="true"></param>
        <param name="allowscriptaccess" value="always"></param>
        <param name="wmode" value="direct"></param>
        <embed src="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf" 
            type="application/x-shockwave-flash" 
            allowscriptaccess="always" 
            allowfullscreen="true" 
            wmode="direct" 
            width="920" 
            height="640" 
            flashvars="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true&plugin_AdaptiveStreamingPlugin=http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf&AdaptiveStreamingPlugin_retryLive=true&AdaptiveStreamingPlugin_retryInterval=10">
        </embed>
        </object>
        </html>
2. Guarde a sua página HTML e publicar um servidor web. Navegue para a página web publicada utilizando o favorito Flash&reg; Player ativada browser da Internet (Internet Explorer, Chrome, Firefox, etc.).
3. Conteúdos de transmissão em fluxo uniforme dentro Adobe&reg; Flash&reg; leitor.

Para obter mais informações sobre o desenvolvimento de OSMF geral, consulte oficial [página de desenvolvimento OSMF](http://osmf.org/resources.html).

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Veja Também
[Microsoft adaptável de transmissão em fluxo Plug-in de atualização de OSMF](https://azure.microsoft.com/blog/2014/10/27/microsoft-adaptive-streaming-plugin-for-osmf-update/) 

