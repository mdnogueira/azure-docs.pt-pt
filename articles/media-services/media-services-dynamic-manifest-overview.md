---
title: "Filtros e manifestos dinâmicos | Microsoft Docs"
description: "Este tópico descreve como criar filtros para que o cliente possa utilizar a secções específicas do fluxo de um fluxo. Os Media Services cria manifestos dinâmicos para arquivar este seletiva de transmissão em fluxo."
services: media-services
documentationcenter: 
author: cenkdin
manager: cfowler
editor: 
ms.assetid: ff102765-8cee-4c08-a6da-b603db9e2054
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 06/29/2017
ms.author: cenkd;juliako
ms.openlocfilehash: 4034fd0aa64627c107a43208dcca766f7f44d5d4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="filters-and-dynamic-manifests"></a>Filtros e manifestos dinâmicos
A partir da versão 2.11, os Media Services permite-lhe definir os filtros para os elementos. Estes filtros são as regras do lado do servidor que irão permitir que os seus clientes de optar por efetuar ações como: reprodução apenas uma secção de um vídeo (em vez de reproduzir o vídeo todo), ou especifique apenas um subconjunto de áudio e vídeos renditions que pode processar o dispositivo do cliente (em vez de todos os renditions que estão associados com o elemento). Esta filtragem dos seus ativos é arquivado através de **manifesto dinâmica**s que são criados após o pedido do cliente para um vídeo de fluxo com base em filtros especificados.

Este tópicos descreve cenários comuns nos quais pode utilizar filtros seria muito vantajoso para os clientes e ligações para tópicos que demonstram como criar filtros programaticamente (atualmente, pode criar filtros com REST APIs apenas).

## <a name="overview"></a>Descrição geral
Quando distribui conteúdo aos clientes (transmissão em fluxo de eventos em direto ou as vídeo a pedido) o seu objetivo é fornecer um vídeo de alta qualidade para vários dispositivos em condições de rede diferentes. Para alcançar esta proceda de objetivo do seguinte modo:

* Codificar a sua transmissão para transmissão múltipla ([velocidade de transmissão adaptável](http://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)) de transmissão (isto tratará de condições de qualidade e de rede) e 
* utilizar os Media Services [empacotamento dinâmico](media-services-dynamic-packaging-overview.md) para dinamicamente o pacote novamente sua transmissão em fluxo para protocolos diferentes (isto tratará de transmissão em fluxo em dispositivos diferentes). Os Media Services suportam fornecimento da velocidade de transmissão adaptável seguinte tecnologias de transmissão em fluxo: HTTP Live Streaming (HLS), transmissão em fluxo uniforme e MPEG DASH. 

### <a name="manifest-files"></a>Ficheiros de manifesto
Quando codificar um elemento de transmissão em fluxo de velocidade de transmissão adaptável, um **manifesto** é criado (lista de reprodução) ficheiro (o ficheiro é baseado em texto ou baseado em XML). O **manifesto** ficheiro inclui de transmissão em fluxo metadados, tais como: controlar o tipo (áudio, vídeo, texto ou), controlar o nome, hora de início e de fim, velocidade de transmissão (qualities), controlar idiomas, a janela de apresentação (janela deslizante de duração fixa), (codec de vídeo FourCC). Também dá instruções ao leitor de obter o fragmento seguinte, fornecendo informações sobre os seguintes playable vídeos fragmentos disponíveis e a respetiva localização. Fragmentos (ou segmentos) são os reais "segmentos" de um conteúdo de vídeo.

Eis um exemplo de um ficheiro de manifesto: 

    <?xml version="1.0" encoding="UTF-8"?>    
    <SmoothStreamingMedia MajorVersion="2" MinorVersion="0" Duration="330187755" TimeScale="10000000">

    <StreamIndex Chunks="17" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="8">
    <QualityLevel Index="0" Bitrate="5860941" FourCC="H264" MaxWidth="1920" MaxHeight="1080" CodecPrivateData="0000000167640028AC2CA501E0089F97015202020280000003008000001931300016E360000E4E1FF8C7076850A4580000000168E9093525" />
    <QualityLevel Index="1" Bitrate="4602724" FourCC="H264" MaxWidth="1920" MaxHeight="1080" CodecPrivateData="0000000167640028AC2CA501E0089F97015202020280000003008000001931100011EDC00002CD29FF8C7076850A45800000000168E9093525" />
    <QualityLevel Index="2" Bitrate="3319311" FourCC="H264" MaxWidth="1280" MaxHeight="720" CodecPrivateData="000000016764001FAC2CA5014016EC054808080A00000300020000030064C0800067C28000103667F8C7076850A4580000000168E9093525" />
    <QualityLevel Index="3" Bitrate="2195119" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FAC2CA503C045FBC054808080A000000300200000064C1000044AA0000ABA9FE31C1DA14291600000000168E9093525" />
    <QualityLevel Index="4" Bitrate="1469881" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FAC2CA503C045FBC054808080A000000300200000064C04000B71A0000E4E1FF8C7076850A4580000000168E9093525" />
    <QualityLevel Index="5" Bitrate="978815" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EAC2CA50280BFE5C0548303032000000300200000064C08001E8480004C4B7F8C7076850A45800000000168E9093525" />
    <QualityLevel Index="6" Bitrate="638374" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EAC2CA50280BFE5C0548303032000000300200000064C080013D60000C65DFE31C1DA1429160000000168E9093525" />
    <QualityLevel Index="7" Bitrate="388851" FourCC="H264" MaxWidth="320" MaxHeight="180" CodecPrivateData="000000016764000DAC2CA505067E7C054830303200000300020000030064C040030D40003D093F8C7076850A45800000000168E9093525" />

    <c t="0" d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="9600000"/>
    </StreamIndex>


    <StreamIndex Chunks="17" Type="audio" Url="QualityLevels({bitrate})/Fragments(AAC_und_ch2_128kbps={start time})" QualityLevels="1" Name="AAC_und_ch2_128kbps">
    <QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="125658" FourCC="AACL" CodecPrivateData="1210" Channels="2" PacketSize="4" SamplingRate="44100" />

    <c t="0" d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="6965987" /></StreamIndex>


    <StreamIndex Chunks="17" Type="audio" Url="QualityLevels({bitrate})/Fragments(AAC_und_ch2_56kbps={start time})" QualityLevels="1" Name="AAC_und_ch2_56kbps">
    <QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="53655" FourCC="AACL" CodecPrivateData="1210" Channels="2" PacketSize="4" SamplingRate="44100" />

    <c t="0" d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="6965987" /></StreamIndex>

    </SmoothStreamingMedia>

### <a name="dynamic-manifests"></a>Manifestos dinâmicos
Existem [cenários](media-services-dynamic-manifest-overview.md#scenarios) quando o cliente tem mais flexibilidade do que o que é descrito no ficheiro de manifesto o elemento predefinido. Por exemplo:

* Dispositivo específico: fornecer apenas o e\ou renditions especificado especificado controla de idioma que é suportadas pelo dispositivo que é utilizado para reproduzir o conteúdo ("rendition filtragem"). 
* Reduza o manifesto para mostrar clip secundária de um evento em direto ("clip secundárias filtragem").
* Compactar o início de um vídeo ("trimming um vídeo").
* Ajuste a janela de apresentação (DVR) para fornecer um período limitado da janela DVR no leitor de ("janela de apresentação ajustar").

Para alcançar esta flexibilidade, os Media Services oferece **manifestos dinâmica** com base no previamente definido [filtros](media-services-dynamic-manifest-overview.md#filters).  Depois de definir os filtros, os clientes podem utilizá-los para transmitir um rendition específico ou secundárias aplicações de clips da vídeo. Se especificar filtros no URL de transmissão em fluxo. Foi possível aplicar os filtros para a velocidade de transmissão adaptável em fluxo protocolos suportados pelo [empacotamento dinâmico](media-services-dynamic-packaging-overview.md): HLS, MPEG DASH e transmissão em fluxo uniforme. Por exemplo:

MPEG DASH URL com filtro

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf,filter=MyLocalFilter)

URL de transmissão em fluxo uniforme com filtro

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyLocalFilter)


Para obter mais informações sobre como fornecer conteúdo e compilação URLs de transmissão em fluxo, consulte [fornecimento de descrição geral do conteúdo](media-services-deliver-content-overview.md).

> [!NOTE]
> Tenha em atenção que manifestos dinâmico não alterar o elemento e o manifesto predefinido para esse recurso. O cliente pode optar por uma sequência com ou sem os filtros de pedidos. 
> 
> 

### <a id="filters"></a>Filtros
Existem dois tipos de filtros de recurso: 

* Filtros global (podem ser aplicadas a qualquer recurso na conta de Media Services do Azure, têm uma duração da conta) e 
* Filtros locais (apenas pode ser aplicado a um recurso com o qual o filtro estava associado a após a criação, têm uma duração de elemento). 

Os tipos de filtro global e local ter exatamente as mesmas propriedades. A principal diferença entre os dois é para os cenários que tipo de um ficheiros é mais adequado. Filtros globais são geralmente adequados para perfis de dispositivo (rendition filtragem) onde os filtros locais pode ser utilizados para compactar um recurso específico.

## <a id="scenarios"></a>Cenários comuns
Tal como foi mencionado antes, quando distribui conteúdo aos clientes (transmissão em fluxo de eventos em direto ou as vídeo a pedido) o seu objetivo é fornecer um vídeo de alta qualidade para vários dispositivos em condições de rede diferentes. Além disso, o seu pode ter outros requisitos que envolvem os elementos de filtragem e a utilização de **manifesto dinâmica**s. As secções seguintes fornecem uma descrição geral curta dos diferentes cenários de filtragem.

* Especifique apenas um subconjunto de áudio e vídeos renditions que podem processar determinados dispositivos (em vez de todos os renditions que estão associados com o elemento). 
* Reproduzir volta apenas uma secção de um vídeo (em vez de reproduzir o vídeo todo).
* Ajuste a janela de apresentação DVR.

## <a name="rendition-filtering"></a>Filtragem de rendition
Pode optar por codificar o elemento para vários perfis de codificação (264 linha de base, 264 elevada, AACL, AACH, Dolby Digital Plus) e múltiplos de qualidade de forma. No entanto, nem todos os dispositivos de cliente irão suportar todos os seu elemento perfis e forma. Por exemplo, dispositivos Android anteriores só suporta 264 da linha de base + AACL. Enviar mais elevada de forma a um dispositivo que não é possível obter as vantagens, gasta cálculo de largura de banda e o dispositivo. Esses dispositivos têm de descodificar todas as informações fornecidas, apenas a dimensionar-a para baixo para apresentação.

Com o manifesto dinâmico, pode criar perfis de dispositivo, como mobile, consola, HD/SD, etc. e incluir o controla e qualities que pretende fazer parte de cada perfil.

![Exemplo de filtragem de rendition][renditions2]

No exemplo seguinte, um codificador foi utilizado para codificar um elemento de mezanino para sete ISO MP4s renditions vídeos (a partir de 180p para 1080p). O elemento codificado pode ser compactado dinamicamente em qualquer um dos seguintes protocolos de transmissão em fluxo: HLS, uniforme e MPEG DASH.  Na parte superior do diagrama, é apresentado o manifesto HLS para o elemento com nenhuma filtros (que contém todos os sete renditions).  Na parte inferior esquerda, é apresentado o manifesto HLS ao qual foi aplicado um filtro com o nome "ott". Especifica o filtro "ott" para remover todos os de forma abaixo 1 Mbps, o que resultou nos níveis de qualidade dois inferior //<Server>/ na resposta.  Na parte inferior direita, é apresentado o manifesto HLS ao qual foi aplicado um filtro com o nome "móvel". Especifica o filtro "móvel" para remover renditions em que a resolução é maior do que 720p, o que resultou em dois renditions 1080p que está a ser repartidas.

![Filtragem de rendition][renditions1]

## <a name="removing-language-tracks"></a>Remover idioma controla
Os recursos podem incluir várias linguagens de áudio, tais como o inglês, espanhol, francês, etc. Normalmente, os gestores de leitor SDK predefinido seleção de áudio controlar e áudio disponível controla por seleção do utilizador. É difícil desenvolver essas SDKs Player, requer diferentes implementações em específicos do dispositivo-estruturas do leitor. Além disso, em algumas plataformas, APIs de leitor são limitadas e não incluem a funcionalidade de seleção de áudio onde os utilizadores não é possível selecionar ou alterar a controlar de áudio predefinido. Com filtros de recurso, pode controlar o comportamento ao criar filtros que incluem apenas os idiomas de áudio pretendidos.

![Controla idioma filtragem][language_filter]

## <a name="trimming-start-of-an-asset"></a>Corte de início de um ativo
Na maior parte dos eventos de transmissão em fluxo em direto, os operadores executam alguns testes antes do evento real. Por exemplo, podem incluir uma ardósia como esta antes do início do evento: "O programa vai começar momentaneamente". Se o programa é arquivo, o teste ardósia dados também estão arquivados e serão incluídos na apresentação. No entanto, estas informações não devem ser apresentadas aos clientes. Com o manifesto dinâmico, pode criar um filtro de hora de início e remova os dados indesejáveis do manifesto.

![Corte início][trim_filter]

## <a name="creating-sub-clips-views-from-a-live-archive"></a>Criar aplicações de clips secundárias (vistas) de um arquivo em direto
Muitos eventos em direto são execução longos e arquivo em direto poderão incluir vários eventos. Depois do evento em direto extremidades broadcasters poderão pretender dividir o arquivo em direto no início do programa lógico e parar sequências. Em seguida, publica separadamente estes programas virtuais sem post processamento arquivo em direto e não criar recursos separados (o que não irão receber a vantagem de fragmentos de cache existentes nos CDNs). Exemplos de tais programas virtuais (aplicações de clips secundárias) são trimestres de um futebol ou jogos de basquetebol, innings no baseball ou eventos individuais de um afternoon programa Olympics.

Com o manifesto dinâmico, pode criar filtros utilizando a hora de início/fim e criar vistas virtuais através da parte superior do seu arquivo em direto. 

![Filtro de subclip][subclip_filter]

Asset filtrado:

![Skiing][skiing]

## <a name="adjusting-presentation-window-dvr"></a>Ajustar a janela de apresentação (DVR)
Atualmente, os Media Services do Azure oferece circular arquivo onde pode ser configurada a duração entre 5 minutos - 25 horas. Filtragem de manifesto pode ser utilizado para criar uma janela DVR graduais ao longo da parte superior do arquivo, sem eliminar o suporte de dados. Existem muitos cenários em que pretende fornecer uma janela DVR limitada que move com o limite em direto e ao mesmo tempo manter uma janela de arquivo maior broadcasters. Um broadcaster poderá querer utilizar os dados que está a janela DVR para realçar clips ou he\she poderá pretender fornecer windows DVR diferentes para diferentes dispositivos. Por exemplo, a maioria dos dispositivos móveis não processa grandes windows DVR (pode ter uma janela DVR de 2 minutos, para dispositivos móveis e de 1 hora para clientes de ambiente de trabalho).

![Janela DVR][dvr_filter]

## <a name="adjusting-livebackoff-live-position"></a>Ajustar LiveBackoff (posição em direto)
Filtragem de manifesto pode ser utilizado para remover o contorno em direto de um programa em direto a vários segundos. Isto permite broadcasters ver a apresentação no ponto de publicação de pré-visualização e criar pontos de inserção de anúncio antes dos visualizadores recebem o fluxo (normalmente, de segurança-desativado por 30 segundos). Broadcasters, em seguida, pode emitir estes anúncios para as estruturas de cliente no tempo para que estes recebido e processar as informações antes da oportunidade de anúncio.

Para além de suporte anúncio, LiveBackoff pode ser utilizado para ajustar a posição de transferência em direto de cliente para que quando os clientes que se desviam e atingiu o limite em direto, podem obter ainda fragmentos do servidor em vez de a obter erros HTTP 404 ou 412.

![livebackoff_filter][livebackoff_filter]

## <a name="combining-multiple-rules-in-a-single-filter"></a>Combinar várias regras num único filtro
Pode combinar várias regras de filtragem de um único filtro. Por exemplo, pode definir uma regra de intervalo para remover ardósia de um arquivo em direto e também filtrar disponível de forma. Para múltiplas regras de filtragem, o resultado final é a composição (apenas intersecção), as regras.

![regras de vários][multiple-rules]

## <a name="create-filters-programmatically"></a>Criar filtros através de programação
O tópico seguinte descreve as entidades de serviços de suporte de dados que estão relacionados com os filtros. O tópico também mostra como programaticamente criar filtros.  

[Criar filtros com REST APIs](media-services-rest-dynamic-manifest.md).

## <a name="combining-multiple-filters-filter-composition"></a>Combinar vários filtros (filtro composição)
Também pode combinar vários filtros num URL único. 

O cenário seguinte demonstra por que razão poderá pretender combinar os filtros:

1. Tem de filtrar as vídeos qualities para dispositivos móveis, como Android ou iPAD (para limitar as vídeos qualities). Para remover os qualities indesejáveis, poderá criar um filtro global que é adequado para perfis de dispositivo. Tal como mencionado acima, podem ser utilizados filtros globais para todos os seus recursos sob a mesma conta de media services sem outras associação. 
2. Também pretende compactar a hora de início e de fim de um ativo. Para atingir esse objetivo, teria de criar um filtro local e definir a hora de início/fim. 
3. Pretende combinar ambos estes filtros (sem combinação que terá de adicionar a filtragem de qualidade de mensagens em fila para o filtro de corte que fará com que a utilização de filtro difícil).

Combinar os filtros, tem de definir os nomes de filtro para a manifesto/listas de reprodução URL com ponto e vírgula delimitada. Vamos supor que tem um filtro com o nome *MyMobileDevice* que filtra qualities e tiver outro denominado *MyStartTime* para definir a hora de início específica. Pode combiná-los como esta:

    http://teststreaming.streaming.mediaservices.windows.net/3d56a4d-b71d-489b-854f-1d67c0596966/64ff1f89-b430-43f8-87dd-56c87b7bd9e2.ism/Manifest(filter=MyMobileDevice;MyStartTime)

Pode combinar até 3 filtros. 

Para obter mais informações consulte [isto](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) blogue.

## <a name="know-issues-and-limitations"></a>Problemas e limitações
* O manifesto dinâmico funciona no GOP limites (chave de Frames), por conseguinte, corte tem precisão GOP. 
* Pode utilizar o mesmo nome de filtro para filtros locais e global. Tenha em atenção esse filtro local tem precedência superior e substituirão filtros global.
* Se atualizar um filtro, pode demorar até 2 minutos para o ponto final de transmissão em fluxo atualizar as regras. Se o conteúdo tiver sido servido com alguns filtros (e colocados em cache no proxies e CDN caches), atualizar estes filtros pode resultar em falhas de leitor. É Recomendamos para limpar a cache depois de atualizar o filtro. Se esta opção não for possível, considere utilizar um filtro diferente.

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Veja Também
[Entrega de conteúdos para clientes descrição-geral](media-services-deliver-content-overview.md)

[renditions1]: ./media/media-services-dynamic-manifest-overview/media-services-rendition-filter.png
[renditions2]: ./media/media-services-dynamic-manifest-overview/media-services-rendition-filter2.png

[rendered_subclip]: ./media/media-services-dynamic-manifests/media-services-rendered-subclip.png
[timeline_trim_event]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-event.png
[timeline_trim_subclip]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-subclip.png

[multiple-rules]:./media/media-services-dynamic-manifest-overview/media-services-multiple-rules-filters.png

[subclip_filter]: ./media/media-services-dynamic-manifest-overview/media-services-subclips-filter.png
[trim_event]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-event.png
[trim_subclip]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-subclip.png
[trim_filter]: ./media/media-services-dynamic-manifest-overview/media-services-trim-filter.png
[redered_subclip]: ./media/media-services-dynamic-manifests/media-services-rendered-subclip.png
[livebackoff_filter]: ./media/media-services-dynamic-manifest-overview/media-services-livebackoff-filter.png
[language_filter]: ./media/media-services-dynamic-manifest-overview/media-services-language-filter.png
[dvr_filter]: ./media/media-services-dynamic-manifest-overview/media-services-dvr-filter.png
[skiing]: ./media/media-services-dynamic-manifest-overview/media-services-skiing.png
