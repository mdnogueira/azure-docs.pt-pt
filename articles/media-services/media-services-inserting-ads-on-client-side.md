---
title: "A inserir anúncios do lado do cliente | Microsoft Docs"
description: "Este tópico mostra como inserir anúncios do lado do cliente."
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: 65c9c747-128e-497e-afe0-3f92d2bf7972
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2016
ms.author: juliako
ms.openlocfilehash: 52ba731f88c630830560e3cf8406ba2e9613c8a5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="inserting-ads-on-the-client-side"></a>A inserir anúncios do lado do cliente
Este tópico contém informações sobre como introduzir vários tipos de anúncios do lado do cliente.

Para obter informações sobre o suporte de fechada captioning e ad no vídeos de transmissão em fluxo em direto, consulte [suportado fechado Captioning e normas de inserção de Ad](media-services-live-streaming-with-onprem-encoders.md#cc_and_ads).

> [!NOTE]
> Azure Media Player não suporta atualmente anúncios.
> 
> 

## <a id="insert_ads_into_media"></a>A inserir anúncios no suporte de dados
Media Services do Azure fornece suporte para inserção de ad através da plataforma de suporte de dados do Windows: estruturas do leitor. Estruturas do leitor com suporte ad estão disponíveis para dispositivos Windows 8, o Silverlight, o Windows Phone 8 e iOS. Cada framework player contém o código de exemplo que mostra como implementar uma aplicação do leitor. Existem três tipos diferentes de pode inserir a suporte de dados: lista de anúncios.

* **Linear** – total de anúncios de moldura que colocar em pausa o vídeo principal.
* **Nonlinear** – anúncios de sobreposição que são apresentados como reprodução de vídeo principal, normalmente, um logótipo ou outro image estático colocado num leitor de.
* **Complementar** – anúncios que são apresentados fora do leitor.

Anúncios podem ser colocados em qualquer momento na linha de tempo as vídeo principal. Tem dizer o leitor quando reproduzir o ad e os anúncios para reproduzir. Isto é feito utilizando um conjunto de ficheiros padrão baseado em XML: modelo de serviço de Ad de vídeo (VAST), Digital vídeo vários Ad listas de reprodução (VMAP), modelo de sequenciação Abstrato suporte de dados (MAST) e Digital vídeo Player Ad Interface definição (VPAID). Ficheiros VASTA especificar que anúncios para apresentar. Ficheiros VMAP Especifique quando reproduzir vários anúncios e conter XML grande. Os ficheiros MAST são outra forma de anúncios de sequência que também pode conter XML grande. Ficheiros VPAID definem uma interface entre o leitor de vídeo e o ad ou o servidor do ad.

Cada framework player funciona de forma diferente e cada vai ser abordada no seu próprio tópico. Este tópico descreve os mecanismos de básicos utilizados para introduzir anúncios. Aplicações do leitor de vídeo pedem anúncios de um servidor do ad. O servidor do ad pode responder de diversas formas:

* Devolve um ficheiro grande
* Devolver um ficheiro VMAP (com VAST incorporado)
* Devolver um ficheiro MAST (com VAST incorporado)
* Devolver um ficheiro grande com anúncios VPAID

### <a name="using-a-video-ad-service-template-vast-file"></a>Utilizando um ficheiro de modelo (VAST) de serviço do Ad de vídeo
Especifica um ficheiro grande que ad ou anúncios para apresentar. O seguinte XML é um exemplo de um ficheiro grande para um linear ad:

    <VAST version="2.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
      <Ad id="115571748">
        <InLine>
          <AdSystem version="2.0 alpha">Atlas</AdSystem>
          <AdTitle>Unknown</AdTitle>
          <Description>Unknown</Description>
          <Survey></Survey>
          <Error></Error>
          <Impression id="Atlas"><![CDATA[http://www.myserver.com/tracking-resource]]></Impression>
          <Creatives>
            <Creative id="video" sequence="0" AdID="">
              <Linear>
                <Duration>00:00:32</Duration>
                <TrackingEvents>
                  <Tracking event="start"><![CDATA[http://www.myserver.com/start-tracking-resource]]></Tracking>
                  <Tracking event="midpoint"><![CDATA[http://www.myserver.com/midpoint-tracking-resource]]></Tracking>
                  <Tracking event="complete"><![CDATA http://www.myserver.com/complete-tracking-resource]]></Tracking>
                  <Tracking event="expand"><![CDATA[http://www.myserver.com/expand-tracking-resource]]></Tracking>
                </TrackingEvents>
                <VideoClicks>
                  <ClickThrough id="Atlas Redirect"><![CDATA[http://www.myserver.com/click-resource]]></ClickThrough>
                  <ClickTracking id="Spare"></ClickTracking>
                </VideoClicks>
                <MediaFiles>
                  <MediaFile apiFramework="Windows Media" id="windows_progressive_200" maintainAspectRatio="true" scaleable="true"  delivery="progressive" bitrate="200" width="400" height="300" type="video/x-ms-wmv">
                    <![CDATA[http://www.myserver.com/media/myad_200_4x3.wmv]]>
                  </MediaFile>
                  <MediaFile apiFramework="Windows Media" id="windows_progressive_300" maintainAspectRatio="true" scaleable="true"  delivery="progressive" bitrate="300" width="400" height="300" type="video/x-ms-wmv">
                    <![CDATA[http://www.myserver.com/media/myad_300_4x3.wmv]]>
                  </MediaFile>
                </MediaFiles>
              </Linear>
            </Creative>
          </Creatives>
          <Extensions>
            <Extension type="Atlas">
            </Extension>
          </Extensions>
        </InLine>
      </Ad>
    </VAST>

O ad linear é descrito através de <**Linear**> elemento. Especifica a duração do ad, eventos de controlo, clique em através de, clique em de controlo e um número de **MediaFile** elementos. Registo de eventos são especificados a <**TrackingEvents**> elemento e permitir que um servidor do ad controlar vários eventos que ocorrem ao visualizar o ad. Neste caso o início, o ponto intermédio, concluído e expanda eventos são controlados. O evento de início ocorre quando é apresentado o ad. O evento ponto intermédio ocorre quando, pelo menos, visualizou 50% da linha cronológica do ad. O evento completa ocorre quando o ad foi executada até ao fim. O evento de expansão ocorre quando o utilizador expande o leitor de vídeo ao ecrã inteiro. Clickthroughs são especificados com um <**ClickThrough**> elemento dentro de uma <**VideoClicks**> elemento e especifica um URI para um recurso a apresentar quando o utilizador clica no ad. ClickTracking é especificado numa <**ClickTracking**> elemento, também dentro do <**VideoClicks**> elemento e especifica um recurso de controlo para o leitor pedir quando o utilizador clica no ad . O <**MediaFile**> elementos especificam informações sobre uma codificação de específica de um anúncio. Quando existe mais do que um <**MediaFile**> elemento, o leitor de vídeo pode escolher a codificação melhor para a plataforma. 

Anúncios lineares podem ser apresentados por uma ordem especificada. Para tal, adicione adicionais <Ad> elementos para o VAST de ficheiros e especificar a ordem utilizando o atributo de sequência. O exemplo a seguir ilustra este:

    <VAST version="2.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
      <Ad id="1" sequence="0">
        <InLine>
          <AdSystem version="2.0 alpha">Atlas</AdSystem>
          <AdTitle>Unknown</AdTitle>
          <Description>Unknown</Description>
          <Survey></Survey>
          <Error></Error>
          <Impression id="Atlas"><![CDATA[http://myserver.com/Impression/Ad1trackingResouce]]></Impression>
          <Creatives>
            <Creative id="video" sequence="0" AdID="">
              <Linear>
                <Duration>00:00:32</Duration>
                <MediaFiles>
                  <!-- ... -->
                </MediaFiles>
              </Linear>
            </Creative>
          </Creatives>
        </InLine>
      </Ad>
      <Ad id="2" sequence="1">
        <InLine>
          <AdSystem version="2.0 alpha">Atlas</AdSystem>
          <AdTitle>Unknown</AdTitle>
          <Description>Unknown</Description>
          <Survey></Survey>
          <Error></Error>
          <Impression id="Atlas"><![CDATA[http://myserver.com/Impression/Ad2trackingResouce]]></Impression>
          <Creatives>
            <Creative id="video" sequence="0" AdID="">
              <Linear>
                <Duration>00:00:30</Duration>
                <MediaFiles>
                  <!-- ... -->
                </MediaFiles>
              </Linear>
            </Creative>
          </Creatives>
        </InLine>
      </Ad>
    </VAST>

Anúncios nonlinear estão especificados num <Creative> elemento bem. O seguinte exemplo mostra um <Creative> elemento que descreve um nonlinear ad.

    <Creative id="video" sequence="1" AdID="">
      <NonLinearAds>
        <NonLinear width="216" height="121" minSuggestedDuration="00:00:15">
          <StaticResource creativeType="image/png"><![CDATA[http://myserver/images/image.png]]></StaticResource>
          <StaticResource creativeType="image/jpg"><![CDATA[http://myserver/images/image.jpg]]></StaticResource>
        </NonLinear>
        <TrackingEvents>
             <Tracking event="acceptInvitation"><![CDATA[http://myserver/tracking/trackingID]></Tracking>
             <Tracking event="collapse"><![CDATA[http://myserver/tracking/trackingID2]]></Tracking>
         </TrackingEvents>
       </NonLinearAds>
    </Creative>


O <**NonLinearAds**> elemento pode conter um ou mais <**NonLinear**> elementos, cada um dos quais pode descrevem um nonlinear ad. O <**NonLinear**> elemento Especifica o recurso para o ad nonlinear. O recurso pode ser uma <**StaticResouce**>, uma <**IFrameResource**>, ou uma <**HTMLResouce**>. <**StaticResource**> descreve um recurso não HTML e define um atributo de creativeType que especifica a forma como o recurso é apresentado:

Imagem/gif, imagem/jpeg, imagem/png – o recurso é apresentado num HTML <**img**> tag.

Application/x-javascript – o recurso é apresentado num HTML <**script**> tag.

Application/x-shockwave-flash – o recurso é apresentado num leitor de Flash.

**IFrameResource** descreve um recurso HTML que pode ser apresentado num IFrame. **HTMLResource** descreve um fragmento de código HTML que pode ser inserido uma página web. **TrackingEvents** especifique eventos de controlo e o URI de pedido quando o evento ocorre. Neste exemplo, são controlados os eventos acceptInvitation e fechar. Para mais informações sobre o **NonLinearAds** elemento e os respectivos valores secundários, consulte IAB.NET/VAST. Tenha em atenção que o **TrackingEvents** elemento está localizado dentro da **NonLinearAds** elemento em vez do **NonLinear** elemento.

Anúncios complementar são definidos dentro de um <CompanionAds> elemento. O <CompanionAds> elemento pode conter um ou mais <Companion> elementos. Cada <Companion> elemento descreve um ad complementar e pode conter um <StaticResource>, <IFrameResource>, ou <HTMLResource> que são especificados da mesma forma que um nonlinear ad. Um ficheiro grande pode conter vários anúncios de complementar e a aplicação de leitor pode escolher o ad mais adequado para apresentar. Para obter mais informações sobre VAST, consulte [3.0 VASTA](http://www.iab.net/media/file/VASTv3.0.pdf).

### <a name="using-a-digital-video-multiple-ad-playlist-vmap-file"></a>Utilizar um vídeo Digital vários ficheiros de lista de reprodução (VMAP) do Ad
Um ficheiro VMAP permite-lhe especificar quando ocorrem quebras de ad, é de cada quebra quanto, quantos anúncios podem ser apresentados dentro de uma quebra de, e que tipos de anúncios podem ser apresentado durante uma quebra de. O seguinte num ficheiro VMAP de exemplo que define uma quebra de ad único:

    <vmap:VMAP xmlns:vmap="http://www.iab.net/vmap-1.0" version="1.0">
      <vmap:AdBreak breakType="linear" breakId="mypre" timeOffset="start">
        <vmap:AdSource allowMultipleAds="true" followRedirects="true" id="1">
          <vmap:VASTData>
            <VAST version="2.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
              <Ad id="115571748">
                <InLine>
                  <AdSystem version="2.0 alpha">Atlas</AdSystem>
                  <AdTitle>Unknown</AdTitle>
                  <Description>Unknown</Description>
                  <Survey></Survey>
                  <Error></Error>
                  <Impression id="Atlas"><![CDATA[http://view.atdmt.com/000/sview/115571748/direct;ai.201582527;vt.2/01/634364885739970673]]></Impression>
                  <Creatives>
                    <Creative id="video" sequence="0" AdID="">
                      <Linear>
                        <Duration>00:00:32</Duration>
                        <MediaFiles>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_200" maintainAspectRatio="true" scaleable="true"  delivery="progressive" bitrate="200" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_1_000_200_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_300" maintainAspectRatio="true" scaleable="true"  delivery="progressive" bitrate="300" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_2_000_300_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_500" maintainAspectRatio="true" scaleable="true"  delivery="progressive" bitrate="500" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_1_000_500_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_700" maintainAspectRatio="true" scaleable="true" delivery="progressive" bitrate="700" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_2_000_700_4x3.wmv]]>
                          </MediaFile>
                        </MediaFiles>
                      </Linear>
                    </Creative>
                  </Creatives>
                </InLine>
              </Ad>
            </VAST>
          </vmap:VASTData>
        </vmap:AdSource>
        <vmap:TrackingEvents>
          <vmap:Tracking event="breakStart">
            http://MyServer.com/breakstart.gif
          </vmap:Tracking>
        </vmap:TrackingEvents>
      </vmap:AdBreak>
    </vmap:VMAP>

Um ficheiro VMAP começa com um <VMAP> elemento que contém um ou mais <AdBreak> elementos, definindo cada uma quebra de ad. Cada quebra ad Especifica um tipo de quebra, quebra ID e a compensação de tempo. O atributo breakType Especifica o tipo do ad que pode ser reproduzido durante a quebra: linear, nonlinear, ou a apresentar. Apresentar o mapa de anúncios para anúncios de grande complementar. Mais do que um tipo de ad pode ser especificado numa lista separada por vírgulas (sem espaços). O breakID é um identificador opcional para o ad. O timeOffset Especifica quando deve ser apresentado o ad. Pode ser especificado das seguintes formas:

1. Hora – no formato hh: mm: ou hh:mm:ss.mmm onde .mmm é em milissegundos. O valor deste atributo especifica o tempo desde o início da linha cronológica de vídeo no início de quebra do ad.
2. Percentagem – no formato n % em que n é a percentagem da linha cronológica de vídeo para reprodução antes de reproduzir do ad
3. Início/fim – Especifica que um anúncio deve ser apresentado antes ou depois do vídeo tenha sido apresentado
4. Posição – Especifica a ordem de quebras de ad de quando a temporização de quebras de ad é desconhecido, como a transmissão em fluxo em direto. A ordem de cada quebra ad é especificada no formato #n em que n é um número inteiro 1 ou superior. 1 significa que deve ser reproduzido ad na primeira oportunidade, 2 significa ad deve ser reproduzido na segunda oportunidade e assim sucessivamente.

Dentro do <**AdBreak**> existe elemento pode ser um <**AdSource**> elemento. O <**AdSource**> elemento contém os seguintes atributos:

1. ID – Especifica um identificador da origem do ad
2. allowMultipleAds – um valor booleano que especifica se a vários anúncios podem ser apresentados durante a quebra de ad
3. followRedirects – um valor booleano opcional que especifica se o leitor de vídeo deve honrar redireciona dentro de uma resposta do ad

O <**AdSource**> elemento fornece um leitor de uma resposta de ad inline ou uma referência a uma resposta do ad. Pode conter um dos seguintes elementos:

* <VASTAdData>indica que uma resposta de ad VASTA está incorporada no ficheiro VMAP
* <AdTagURI>URI que referencia uma resposta de ad de outro sistema
* <CustomAdData>-um arbitrários que respresents uma resposta não grande cadeia

Neste exemplo, uma resposta de ad na linha for especificada com um <VASTAdData> elemento que contenha uma resposta de ad grande. Para obter mais informações sobre os outros elementos, consulte [VMAP](http://www.iab.net/guidelines/508676/digitalvideo/vsuite/vmap).

O <**AdBreak**> elemento também pode conter uma <**TrackingEvents**> elemento. O <**TrackingEvents**> elemento permite-lhe controlar o início ou de fim de uma quebra de ad ou se Ocorreu um erro durante a quebra de ad. O <**TrackingEvents**> elemento contém um ou mais <**controlo**> elementos, cada um dos quais Especifica um evento de controlo e um URI de controlo. Os eventos de rastreio possíveis são:

1. breakStart – controla o início de uma quebra de ad
2. breakEnd – controlar a conclusão de uma quebra de ad
3. Erro – controla o erro ocorreu durante a quebra de ad

O exemplo seguinte mostra um ficheiro VMAP que especifica os eventos de controlo

    <vmap:VMAP xmlns:vmap="http://www.iab.net/vmap-1.0" version="1.0">
      <vmap:AdBreak breakType="linear" breakId="mypre" timeOffset="start">
        <vmap:AdSource allowMultipleAds="true" followRedirects="true" id="1">
          <vmap:VASTData>
            <!--Inline VAST -->
          </vmap:VASTData>
        </vmap:AdSource>
        <vmap:TrackingEvents>
          <vmap:Tracking event="breakStart">
            http://MyServer.com/breakstart.gif
          </vmap:Tracking>
          <vmap:Tracking event="breakend">
            http://MyServer.com/breakend.gif
          </vmap:Tracking>
          <vmap:Tracking event="error">
            http://MyServer.com/error.gif
          </vmap:Tracking>
        </vmap:TrackingEvents>
      </vmap:AdBreak>
    </vmap:VMAP>

Para mais informações sobre a <**TrackingEvents**> elemento e os respectivos valores secundários, consulte http://iab.org/VMAP.pdf

### <a name="using-a-media-abstract-sequencing-template-mast-file"></a>Utilizar um Abstrato de suporte de dados sequência do ficheiro de modelo (MAST)
Um ficheiro MAST permite-lhe especificar acionadores que definem quando é apresentado um anúncio. Segue-se um exemplo de ficheiro MAST que contém os acionadores para um ad de agregação de pré-instalação, uma agregação intermédio ad e ad uma pós agregação.

    <MAST xsi:schemaLocation="http://openvideoplayer.sf.net/mast http://openvideoplayer.sf.net/mast/mast.xsd" xmlns="http://openvideoplayer.sf.net/mast" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
      <triggers>
        <trigger id="preroll" description="preroll every item"  >
          <startConditions>
            <condition type="event" name="OnItemStart" />
          </startConditions>
          <sources>
            <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
              <sources />
            </source>
          </sources>
        </trigger>

        <trigger id="midroll" description="midroll at 15 sec."  >
          <startConditions>
            <condition type="property" name="Position" value="00:00:15.0" operator="GEQ" />
          </startConditions>
          <endConditions>
            <condition type="event" name="OnItemEnd"/>
            <!--This 'resets' the trigger for the next clip-->
          </endConditions>
          <sources>
            <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
              <sources />
            </source>
          </sources>
        </trigger>

        <trigger id="postroll" description="postroll"  >
          <startConditions>
            <condition type="event" name="OnItemEnd"/>
          </startConditions>
          <sources>
            <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
              <sources />
            </source>
          </sources>
        </trigger>
      </triggers>
    </MAST>



Um ficheiro MAST começa com um **MAST** elemento que contém um **acionadores** elemento. O <triggers> elemento contém um ou mais **acionador** elementos que definem quando deve ser reproduzido um anúncio. 

O **acionador** elemento contém um **startConditions** elemento que especificam quando um anúncio deve começar a reproduzir. O **startConditions** elemento contém um ou mais <condition> elementos. Quando cada <condition> avalia como VERDADEIRO, um acionador inicia ou revogado consoante se o <condition> está contido dentro de um **startConditions** ou **endConditions** elemento respetivamente. Quando vários <condition> elementos estiverem presentes, são tratadas como uma implícita ou, qualquer condição avaliar como verdadeiro fará com que o acionador iniciar. <condition>elementos podem ser aninhados. Quando subordinado <condition> elementos são a configuração predefinidos, são tratadas como uma e implícita, todas as condições tem de avaliar como verdadeiro para acionar a iniciar. O <condition> elemento contém os seguintes atributos que definem a condição: 

1. **tipo** – Especifica o tipo de condição, eventos ou propriedade
2. **nome** – o nome da propriedade ou evento a ser utilizada durante a avaliação
3. **valor** – o valor de uma propriedade que será avaliada relativamente
4. **operador** – a utilizar durante a avaliação da operação: EQ (igual), NEQ (não é igual), GTR (superior), GEQ (igual ou superior), LT (inferior), LEQ (menor ou igual), MOD (módulo)

**endConditions** também conter <condition> elementos. Quando a condição for avaliada como verdadeira o acionador é reposta. O <trigger> elemento também contém um <sources> elemento que contém um ou mais <source> elementos. O <source> elementos definem o URI de resposta de ad e o tipo de resposta do ad. Neste exemplo é dado um URI para uma VASTA resposta. 

    <trigger id="postroll" description="postroll"  >
      <startConditions>
        <condition/>
      </startConditions>
      <sources>
        <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
          <sources />
        </source>
      </sources>
    </trigger>


### <a name="using-video-player-ad-interface-definition-vpaid"></a>Utilizar a definição de Interface de Ad de leitor de vídeo (VPAID)
VPAID é uma API para ativar unidades ad executável comunicar com um leitor de vídeo. Isto permite experiências ad altamente interativo. O utilizador pode interagir com o ad e ad pode responder a ações executadas pelo Visualizador. Por exemplo um anúncio pode apresentar botões que permitem ao utilizador ver mais informações ou uma versão superior do ad. O leitor de vídeo tem de suportar a API de VPAID e o ad executável tem de implementar a API. Quando um leitor de pedidos de que um anúncio de um servidor de ad o servidor pode responder com uma resposta VASTA que contém um ad VPAID.

É criado um anúncio executável no código que tem de ser executado num ambiente de tempo de execução, tais como Adobe Flash™ ou JavaScript que pode ser executada num web browser. Quando um servidor do ad devolve uma resposta VASTA que contém um ad VPAID, o valor da apiFramework atributo no <MediaFile> elemento tem de ser "VPAID". Este atributo Especifica que o ad contido é um ad executável de VPAID. O atributo de tipo tem de ser definido para o tipo de MIME do executável, tal como "application/x-shockwave-flash" ou "application/x-javascript". O fragmento XML seguinte mostra o <MediaFile> elemento a partir de uma resposta VASTA que contém um ad executável de VPAID. 

    <MediaFiles>
       <MediaFile id="1" delivery="progressive" type=”application/x-shockwaveflash”
                  width=”640” height=”480” apiFramework=”VPAID”>
           <!-- CDATA wrapped URI to executable ad -->
       </MediaFile>
    </MediaFiles>


Uma executável ad pode ser inicializada com o <AdParameters> elemento o <Linear> ou <NonLinear> elementos numa VASTA resposta. Para mais informações sobre o <AdParameters> elemento, consulte [3.0 VASTA](http://www.iab.net/media/file/VASTv3.0.pdf). Para obter mais informações sobre a API de VPAID, consulte [VPAID 2.0](http://www.iab.net/media/file/VPAID_2.0_Final_04-10-2012.pdf).

## <a name="implementing-a-windows-or-windows-phone-8-player-with-ad-support"></a>Implementar um Windows ou Windows Phone 8 Player com suporte do Ad
A plataforma Microsoft Media: Player Framework para o Windows 8 e Windows Phone 8 contém uma coleção de aplicações de exemplo que mostram como implementar uma aplicação de leitor de vídeo utilizando a estrutura. Pode transferir a estrutura de leitor e os exemplos de [Player Framework para o Windows 8 e Windows Phone 8](https://playerframework.codeplex.com).

Quando abrir a solução de Microsoft.PlayerFramework.Xaml.Samples, verá um número de pastas no projeto. A pasta de publicidade contém o código de exemplo relevante para a criação de um leitor de vídeo com suporte do ad. Dentro de publicidade pasta é um número de ficheiros XAML/cs sempre que mostram como anúncios de inserção de forma diferente. A lista seguinte descreve cada:

* AdPodPage.xaml mostra como apresentar um pod ad.
* AdSchedulingPage.xaml mostra como agendar anúncios.
* FreeWheelPage.xaml mostra como utilizar o plug-in de FreeWheel agendar anúncios.
* MastPage.xaml mostra como agendar anúncios com um ficheiro MAST.
* ProgrammaticAdPage.xaml mostra como agendar programaticamente ads para um vídeo.
* ScheduleClipPage.xaml mostra como agendar um anúncio sem um ficheiro grande.
* VastLinearCompanionPage.xaml mostra como a inserir um linear e ad complementar.
* VastNonLinearPage.xaml mostra como a inserir um ad não linear.
* VmapPage.xaml mostra como especificar anúncios com um ficheiro VMAP.

Cada um destes exemplos utiliza a classe de MediaPlayer definida pelo framework leitor. A maioria dos exemplos utilizam Plug-ins que adicionar suporte para vários formatos de resposta do ad. O exemplo de ProgrammaticAdPage programaticamente interage com uma instância de MediaPlayer.

### <a name="adpodpage-sample"></a>Exemplo de AdPodPage
Este exemplo utiliza o AdSchedulerPlugin para definir a apresentar um anúncio. Neste exemplo, um anúncio de agregação de média dimensão que é agendado para ser reproduzido após 5 segundos. O pod ad (um grupo de anúncios a apresentar na ordem) está especificado num ficheiro VASTA devolvido a partir de um servidor do ad. O URI para o ficheiro grande é especificado no <RemoteAdSource> elemento.

    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">

        <mmppf:MediaPlayer.Plugins>
            <ads:AdSchedulerPlugin>
                <ads:AdSchedulerPlugin.Advertisements>

                    <ads:MidrollAdvertisement Time="00:00:05">
                        <ads:MidrollAdvertisement.Source>
                            <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_adpod.xml" Type="vast"/>
                        </ads:MidrollAdvertisement.Source>
                    </ads:MidrollAdvertisement>

                </ads:AdSchedulerPlugin.Advertisements>
            </ads:AdSchedulerPlugin>
            <ads:AdHandlerPlugin/>
        </mmppf:MediaPlayer.Plugins>
    </mmppf:MediaPlayer>

Para mais informações sobre o AdSchedulerPlugin, consulte [publicidade no Framework Player no Windows 8 e Windows Phone 8](http://playerframework.codeplex.com/wikipage?title=Advertising&referringTitle=Windows%208%20Player%20Documentation)

### <a name="adschedulingpage"></a>AdSchedulingPage
Este exemplo utiliza também o AdSchedulerPlugin. As agendas três anúncios, ad uma pré- agregação de, ad uma agregação intermédio e uma agregação posterior ao ad. O URI para VAST para cada anúncio é especificado num <RemoteAdSource> elemento.

    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>

                            <ads:PrerollAdvertisement>
                                <ads:PrerollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" Type="vast"/>
                                </ads:PrerollAdvertisement.Source>
                            </ads:PrerollAdvertisement>

                            <ads:MidrollAdvertisement Time="00:00:15">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" Type="vast"/>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>

                            <ads:PostrollAdvertisement>
                                <ads:PostrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" Type="vast"/>
                                </ads:PostrollAdvertisement.Source>
                            </ads:PostrollAdvertisement>

                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>


### <a name="freewheelpage"></a>FreeWheelPage
Este exemplo utiliza o FreeWheelPlugin que especifica um atributo de origem Especifica um URI que aponta para um ficheiro de SmartXML que especifica o conteúdo do ad, bem como informações de agendamento do ad.

    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:FreeWheelPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/freewheel.xml"/>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>

### <a name="mastpage"></a>MastPage
Este exemplo utiliza o MastSchedulerPlugin permite-lhe utilizar um ficheiro MAST. O atributo de origem Especifica a localização do ficheiro MAST.

    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:MastSchedulerPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/mast.xml" />
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>

### <a name="programmaticadpage"></a>ProgrammaticAdPage
Este exemplo programaticamente interage com o MediaPlayer. O ficheiro ProgrammaticAdPage.xaml para instanciar o MediaPlayer:

    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4"/>

O ficheiro ProgrammaticAdPage.xaml.cs cria um AdHandlerPlugin, adiciona um TimelineMarker para especificar quando um anúncio deve ser apresentado e, em seguida, adiciona um processador para o evento de MarkerReached que carrega um RemoteAdSource especificando um URI para um ficheiro grande e, em seguida, desempenha do ad.

    public sealed partial class ProgrammaticAdPage : Microsoft.PlayerFramework.Samples.Common.LayoutAwarePage
        {
            AdHandlerPlugin adHandler;

            public ProgrammaticAdPage()
            {
                this.InitializeComponent();
                adHandler = new AdHandlerPlugin();
                player.Plugins.Add(new AdHandlerPlugin());
                player.Markers.Add(new TimelineMarker() { Time = TimeSpan.FromSeconds(5), Type = "myAd" });
                player.MarkerReached += pf_MarkerReached;
            }

            async void pf_MarkerReached(object sender, TimelineMarkerRoutedEventArgs e)
            {
                if (e.Marker.Type == "myAd")
                {
                    var adSource = new RemoteAdSource() { Type = VastAdPayloadHandler.AdType, Uri = new Uri("http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml") };
                    //var adSource = new AdSource() { Type = DocumentAdPayloadHandler.AdType, Payload = SampleAdDocument };
                    var progress = new Progress<AdStatus>();
                    try
                    {
                        await player.PlayAd(adSource, progress, CancellationToken.None);
                    }
                    catch { /* ignore */ }
                }
            }

### <a name="scheduleclippage"></a>ScheduleClipPage
Este exemplo utiliza o AdSchedulerPlugin para agendar uma agregação intermédio ad ao especificar um ficheiro. wmv que contém o ad.

    <mmppf:MediaPlayer x:Name="player" Source="http://smf.cloudapp.net/html5/media/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>

                            <ads:MidrollAdvertisement Time="00:00:05">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:AdSource Type="clip">
                                        <ads:AdSource.Payload>
                                            <ads:ClipAdPayload MediaSource="http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_2_000_700_4x3.wmv" MimeType="video/x-ms-wmv" />
                                        </ads:AdSource.Payload>
                                    </ads:AdSource>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>

                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>

### <a name="vastlinearcompanionpage"></a>VastLinearCompanionPage
Este exemplo ilustra como utilizar o AdSchedulerPlugin para agendar uma ad linear do intermédio de agregação com um anúncio complementar. O <RemoteAdSource> elemento Especifica a localização do ficheiro grande.

    <mmppf:MediaPlayer Grid.Row="1"  x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>

                            <ads:MidrollAdvertisement Time="00:00:05">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear_companions.xml" Type="vast"/>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>

                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>

### <a name="vastlinearnonlinearpage"></a>VastLinearNonLinearPage
Este exemplo utiliza o AdSchedulerPlugin para agendar uma linear e um ad não linear. A localização do ficheiro grande é especificada com o <RemoteAdSource> elemento.

    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>

                            <ads:MidrollAdvertisement Time="00:00:05">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear_nonlinear.xml" Type="vast"/>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>

                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>

### <a name="vmappage"></a>VMAPPage
Este amostras utiliza o VmapSchedulerPlugin agendar anúncios utilizando um ficheiro VMAP. O URI para o ficheiro VMAP é especificado no atributo de origem a <VmapSchedulerPlugin> elemento.

    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:VmapSchedulerPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/vmap.xml"/>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>

## <a name="implementing-an-ios-video-player-with-ad-support"></a>Implementar um leitor de vídeo com suporte do Ad de iOS
A plataforma Microsoft Media: Arquitetura de leitor de iOS contém uma coleção de aplicações de exemplo que mostram como implementar uma aplicação de leitor de vídeo utilizando a estrutura. Pode transferir a estrutura de leitor e os exemplos de [Azure Media Player Framework](https://github.com/Azure/azure-media-player-framework). A página do github tem uma ligação para uma Wiki que contém informações adicionais sobre a arquitetura de leitor e uma introdução ao exemplo player: [Azure Media Player Wiki](https://github.com/Azure/azure-media-player-framework/wiki/How-to-use-Azure-media-player-framework).

### <a name="scheduling-ads-with-vmap"></a>Agendamento de anúncios com VMAP
O exemplo seguinte mostra como agendar anúncios utilizando um ficheiro VMAP.

    // How to schedule an Ad using VMAP.
    //First download the VMAP manifest

    if (![framework.adResolver downloadManifest:&manifest withURL:[NSURL URLWithString:@"http://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVMAP.xml"]])
            {
                [self logFrameworkError];
            }
            else
            {
                // Schedule a list of ads using the downloaded VMAP manifest
                if (![framework scheduleVMAPWithManifest:manifest])
                {
                    [self logFrameworkError];
                }          
            }

### <a name="scheduling-ads-with-vast"></a>Agendamento de anúncios com VAST
O exemplo seguinte mostra como agendar um ad VASTA de enlace dinâmico.

    //Example:3 How to schedule a late binding VAST ad.
    // set the start time for the ad
    adLinearTime.startTime = 13;
    adLinearTime.duration = 0;
    // Specify the URI of the VAST file
    NSString *vastAd1=@"http://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVAST.xml";
    // Create an AdInfo object
     AdInfo *vastAdInfo1 = [[[AdInfo alloc] init] autorelease];
    // set URL to VAST file
    vastAdInfo1.clipURL = [NSURL URLWithString:vastAd1];
    // set running time of ad
    vastAdInfo1.mediaTime = [[[MediaTime alloc] init] autorelease];
    vastAdInfo1.mediaTime.clipBeginMediaTime = 0;
    vastAdInfo1.mediaTime.clipEndMediaTime = 10;
    vastAdInfo1.policy = @"policy for late binding VAST";
    // specify ad type
    vastAdInfo1.type = AdType_Midroll;
    vastAdInfo1.appendTo=-1;
    adIndex = 0;
    // schedule ad
    if (![framework scheduleClip:vastAdInfo1 atTime:adLinearTime forType:PlaylistEntryType_VAST andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }

   O exemplo seguinte mostra como agendar um anúncio de grande de enlace antecipada.
Agenda de exemplo: 4 um antecipado enlace ad VASTA //Download o VAST ficheiro se (! [ framework.adResolver downloadManifest: & manifesto withURL: [nsurl que URLWithString:@"http://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVAST.xml"]]) {[logFrameworkError automática];} pessoa {adLinearTime.startTime = 7; adLinearTime.duration = 0;

        // Create AdInfo instance
        AdInfo *vastAdInfo2 = [[[AdInfo alloc] init] autorelease];
        vastAdInfo2.mediaTime = [[[MediaTime alloc] init] autorelease];
        vastAdInfo2.policy = @"policy for early binding VAST";
        // specify ad type
        vastAdInfo2.type = AdType_Midroll;
        vastAdInfo2.appendTo=-1;
        // schedule ad
        if (![framework scheduleVASTClip:vastAdInfo2 withManifest:manifest atTime:adLinearTime andGetClipId:&adIndex])
        {
            [self logFrameworkError];
        }
    }

O exemplo seguinte mostra como a inserir uma ad utilizando aproximada cortar Editar (RCE)

    //Example:1 How to use RCE.
    // specify manifest for ad content
    NSString *secondContent=@"http://wamsblureg001orig-hs.cloudapp.net/6651424c-a9d1-419b-895c-6993f0f48a26/The%20making%20of%20Microsoft%20Surface-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";

    // specify ad length
    mediaTime.currentPlaybackPosition = 0;
    mediaTime.clipBeginMediaTime = 0;
    mediaTime.clipEndMediaTime = 80;
    // append ad content
    if (![framework appendContentClip:[NSURL URLWithString:secondContent] withMediaTime:mediaTime andGetClipId:&clipId])
    {
        [self logFrameworkError];
    }

O exemplo seguinte mostra como agendar um pod ad.

    //Example:5 Schedule an ad Pod.
    // Set start time for ad
    adLinearTime.startTime = 23;
    adLinearTime.duration = 0;

    // Specify URL to content
    NSString *adpodSt1=@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/asset-e47b43fd-05dc-4587-ac87-5916439ad07f/Windows%208_%20Cliffjumpers.mp4?st=2012-11-28T16%3A31%3A57Z&se=2014-11-28T16%3A31%3A57Z&sr=c&si=2a6dbb1e-f906-4187-a3d3-7e517192cbd0&sig=qrXYZBekqlbbYKqwovxzaVZNLv9cgyINgMazSCbdrfU%3D";
    // Create an AdInfo instance
    AdInfo *adpodInfo1 = [[[AdInfo alloc] init] autorelease];
    // set URI to ad content
    adpodInfo1.clipURL = [NSURL URLWithString:adpodSt1];
    // Set ad running time
    adpodInfo1.mediaTime = [[[MediaTime alloc] init] autorelease];
    adpodInfo1.mediaTime.clipBeginMediaTime = 0;
    adpodInfo1.mediaTime.clipEndMediaTime = 17;
    adpodInfo1.policy = @"policy for ad pod 1";
    // Set ad type
    adpodInfo1.type = AdType_Midroll;
    adpodInfo1.appendTo=-1;
    adIndex = 0;
    // Schedule ad
    if (![framework scheduleClip:adpodInfo1 atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }

O exemplo seguinte mostra como agendar um ad de agregação de média dimensão que não temporária. Um ad não temporária apenas é reproduzido depois, independentemente de qualquer pesquisa executa o Visualizador.

    //Example:6 Schedule a single non sticky mid roll Ad
    // specify URL to content
    NSString *oneTimeAd=@"http://wamsblureg001orig-hs.cloudapp.net/5389c0c5-340f-48d7-90bc-0aab664e5f02/Windows%208_%20You%20and%20Me%20Together-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";

    // create an AdInfo instance
    AdInfo *oneTimeInfo = [[[AdInfo alloc] init] autorelease];
    // set URL of ad
    oneTimeInfo.clipURL = [NSURL URLWithString:oneTimeAd];
    oneTimeInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    oneTimeInfo.mediaTime.clipBeginMediaTime = 0;
    oneTimeInfo.mediaTime.clipEndMediaTime = -1;
    oneTimeInfo.policy = @"policy for one-time ad";
    // set ad start time
    adLinearTime.startTime = 43;
    adLinearTime.duration = 0;
    // set ad type
    oneTimeInfo.type = AdType_Midroll;
    // non sticky ad
    oneTimeInfo.deleteAfterPlayed = YES;
    // schedule ad
    if (![framework scheduleClip:oneTimeInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }

O exemplo seguinte mostra como agendar um ad roll intermédio temporária. Será apresentada uma ad temporária sempre que for atingido o ponto especificado na linha cronológica de vídeo.

    //Example:7 Schedule a single sticky mid roll Ad
    NSString *stickyAd=@"http://wamsblureg001orig-hs.cloudapp.net/2e4e7d1f-b72a-4994-a406-810c796fc4fc/The%20Surface%20Movement-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    // create AdInfo instance
    AdInfo *stickyAdInfo = [[[AdInfo alloc] init] autorelease];
    // set URI to ad
    stickyAdInfo.clipURL = [NSURL URLWithString:stickyAd];
    stickyAdInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    stickyAdInfo.mediaTime.clipBeginMediaTime = 0;
    stickyAdInfo.mediaTime.clipEndMediaTime = 15;
    stickyAdInfo.policy = @"policy for sticky mid-roll ad";
    // set ad start time
    adLinearTime.startTime = 64;
    adLinearTime.duration = 0;
    // set ad type
    stickyAdInfo.type = AdType_Midroll;
    stickyAdInfo.deleteAfterPlayed = NO;
    // schedule ad
    if (![framework scheduleClip:stickyAdInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }


O exemplo seguinte mostra como agendar um ad roll pós-implementação.

    //Example:8 Schedule Post Roll Ad
    NSString *postAdURLString=@"http://wamsblureg001orig-hs.cloudapp.net/aa152d7f-3c54-487b-ba07-a58e0e33280b/wp-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    // create AdInfo instance
    AdInfo *postAdInfo = [[[AdInfo alloc] init] autorelease];
    postAdInfo.clipURL = [NSURL URLWithString:postAdURLString];
    postAdInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    postAdInfo.mediaTime.clipBeginMediaTime = 0;
    // set ad length
    postAdInfo.mediaTime.clipEndMediaTime = 45;
    postAdInfo.policy = @"policy for post-roll ad";
    // set ad type
    postAdInfo.type = AdType_Postroll;
    adLinearTime.duration = 0;
    if (![framework scheduleClip:postAdInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }

O exemplo seguinte mostra como agendar um ad de pré-agregação.

    //Example:9 Schedule Pre Roll Ad
    NSString *adURLString = @"http://wamsblureg001orig-hs.cloudapp.net/2e4e7d1f-b72a-4994-a406-810c796fc4fc/The%20Surface%20Movement-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    AdInfo *adInfo = [[[AdInfo alloc] init] autorelease];
    adInfo.clipURL = [NSURL URLWithString:adURLString];
    adInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    adInfo.mediaTime.currentPlaybackPosition = 0;
    adInfo.mediaTime.clipBeginMediaTime = 40; //You could play a portion of an Ad. Yeh!
    adInfo.mediaTime.clipEndMediaTime = 59;
    adInfo.policy = @"policy for pre-roll ad";
    adInfo.appendTo = -1;
    adInfo.type = AdType_Preroll;
    adLinearTime.duration = 0;
    // schedule ad
    if (![framework scheduleClip:adInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }

O exemplo seguinte mostra como agendar um ad de sobreposição de agregação intermédio.

    // Example10: Schedule a Mid Roll overlay Ad
    NSString *adURLString = @"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/asset-e47b43fd-05dc-4587-ac87-5916439ad07f/Windows%208_%20Cliffjumpers.mp4?st=2012-11-28T16%3A31%3A57Z&se=2014-11-28T16%3A31%3A57Z&sr=c&si=2a6dbb1e-f906-4187-a3d3-7e517192cbd0&sig=qrXYZBekqlbbYKqwovxzaVZNLv9cgyINgMazSCbdrfU%3D";
    //Create AdInfo instance
    AdInfo *adInfo = [[[AdInfo alloc] init] autorelease];
    adInfo.clipURL = [NSURL URLWithString:adURLString];
    adInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    adInfo.mediaTime.currentPlaybackPosition = 0;
    adInfo.mediaTime.clipBeginMediaTime = 0;
    // specify ad length
    adInfo.mediaTime.clipEndMediaTime = 20;
    adInfo.policy = @"policy for mid-roll overlay ad";
    adInfo.appendTo = -1;
    // specify ad type
    adInfo.type = AdType_Midroll;
    // specify ad start time & duration
    adLinearTime.startTime = 300;
    adLinearTime.duration = 20;
    // schedule ad            if (![framework scheduleClip:adInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }



## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Veja Também
[Desenvolver aplicações de leitor de vídeo](media-services-develop-video-players.md)

