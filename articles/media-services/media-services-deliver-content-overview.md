---
title: "Entrega de conteúdos para clientes | Microsoft Docs"
description: "Este tópico fornece uma descrição geral do que esteja envolvido na entrega dos seus conteúdos com Media Services do Azure."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 89ede54a-6a9c-4814-9858-dcfbb5f4fed5
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/28/2017
ms.author: juliako
ms.openlocfilehash: 815aae57af93b0e4870bd9f61da248e4be328db4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="deliver-content-to-customers"></a>Fornecer conteúdos aos clientes
Quando estiver a entrega dos seus conteúdos de vídeo a pedido ou de transmissão em fluxo para os clientes, o seu objetivo é fornecer as vídeo de alta qualidade para vários dispositivos em condições de rede diferentes.

Para atingir este objetivo, pode:

* Codificar a sua transmissão em fluxo de vídeo de transmissão múltipla (velocidade de transmissão adaptável). Isto tratará de condições de qualidade e de rede.
* Utilizar os Media Services do Microsoft Azure [empacotamento dinâmico](media-services-dynamic-packaging-overview.md) para dinamicamente o pacote novamente sua transmissão em fluxo para protocolos diferentes. Isto tratará de transmissão em fluxo em dispositivos diferentes. Os Media Services suportam fornecimento da velocidade de transmissão adaptável seguinte tecnologias de transmissão em fluxo: <br/>
    * **HTTP Live Streaming** (HLS) - adicionar "(formato = m3u8-aapl)" caminho para a parte "/ Manifest" do URL para indicam ao servidor de origem de transmissão em fluxo a devolver back HLS conteúdo para consumo em **Apple iOS** dispositivos nativos (para obter detalhes, consulte [localizadores](#locators) e [URLs](#URLs)),
    * **MPEG-DASH** -adicionar "(formato = mpd-time-csf)" caminho para a parte "/ Manifest" do URL para saber se o servidor de origem de transmissão em fluxo para devolver fazer uma cópia de MPEG-DASH (para obter mais informações, consulte [localizadores](#locators) e [URLs](#URLs) ),
    * **Transmissão em fluxo uniforme**.

>[!NOTE]
>Quando a sua conta AMS é criada, é adicionado um ponto final de transmissão em fluxo **predefinido** à sua conta no estado **Parado**. Para começar a transmitir o seu conteúdo em fluxo e a tirar partido do empacotamento e encriptação dinâmicos, o ponto final de transmissão em fluxo a partir do qual quer transmitir conteúdo tem de estar no estado **Em execução**. 

Este artigo fornece uma descrição geral dos conceitos de entrega de conteúdos importantes.

Para verificar os problemas conhecidos, consulte [problemas conhecidos](media-services-deliver-content-overview.md#known-issues).

## <a name="dynamic-packaging"></a>Empacotamento dinâmico
Com o empacotamento dinâmico que os Media Services fornecem, que pode fornecer o conteúdo de velocidade de transmissão adaptável MP4 ou transmissão em fluxo uniforme codificado na transmissão em fluxo formatos suportados pelos Media Services (MPEG-DASH, HLS, transmissão em fluxo uniforme,) sem ter de novamente o pacote para estes formatos de transmissão em fluxo. Recomendamos a entrega dos seus conteúdos com o empacotamento dinâmico.

Para tirar partido do empacotamento dinâmico, terá de codificar o (origem) mezanino para um conjunto de ficheiros MP4 de velocidade de transmissão adaptável ou ficheiros de transmissão em fluxo uniforme de velocidade de transmissão adaptável.

Com o empacotamento dinâmico, armazenar e pagar pelos ficheiros num único formato de armazenamento. Os Media Services irão compilar e disponibilizar a resposta adequada com base nos seus pedidos.

Empacotamento dinâmico está disponível para standard e premium pontos finais de transmissão em fluxo. 

Para obter mais informações, consulte [empacotamento dinâmico](media-services-dynamic-packaging-overview.md).

## <a name="filters-and-dynamic-manifests"></a>Filtros e manifestos dinâmicos
Pode definir os filtros para os elementos com os Media Services. Estes filtros são regras de lado do servidor que ajudam os seus clientes fazer coisas como reproduzir uma secção específica de um vídeo ou especifique um subconjunto de áudio e vídeos renditions que pode processar o dispositivo do cliente (em vez de todos os renditions que estão associados com o elemento). Esta filtragem é efetuada através de *manifestos dinâmicos* que são criados quando o cliente pede para transmitir um vídeo com base num ou mais filtros de especificado.

Para obter mais informações, consulte [filtros e manifestos dinâmicos](media-services-dynamic-manifest-overview.md).

## <a name="a-idlocatorslocators"></a><a id="locators"/>Localizadores
Fornecer aos seus utilizadores com um URL que pode ser utilizado para transmitir ou transferir o conteúdo, primeiro tem de publicar o elemento criando um localizador. Um localizador fornece um ponto de entrada para aceder aos ficheiros contidos num recurso. Os Media Services suportam dois tipos de localizadores:

* Localizadores OnDemandOrigin. Estes são utilizados para transmitir multimédia (por exemplo, MPEG-DASH, HLS ou transmissão em fluxo uniforme) ou transferir progressivamente os ficheiros.
* Localizadores de URL de assinatura (SAS) de acesso partilhado. Estes são utilizados para transferir ficheiros de suporte de dados para o seu computador local.

Um *política de acesso* é utilizado para definir as permissões (por exemplo, leitura, escrita e lista) e a duração para a qual um cliente tem acesso para um recurso específico. Tenha em atenção que a permissão de lista (AccessPermissions.List) não deve ser utilizada na criação de um localizador OrDemandOrigin.

Os localizadores têm datas de expiração. O portal do Azure define uma data de expiração de 100 anos no futuro para localizadores.

> [!NOTE]
> Se utilizou o portal do Azure para criar localizadores antes de Março de 2015, os localizadores foram definidos para expirar após dois anos.
> 
> 

Para atualizar uma data de expiração num localizador, utilize as APIs [REST](https://docs.microsoft.com/rest/api/media/operations/locator#update_a_locator) ou [.NET](http://go.microsoft.com/fwlink/?LinkID=533259). Observe que, ao atualizar a data de expiração de um localizador SAS, o URL é alterado.

Os localizadores não são concebidos para gerir o controlo de acesso por utilizador. É possível conceder direitos de acesso diferentes para utilizadores individuais utilizando soluções de gestão de direitos digitais (DRM). Para obter mais informações, consulte [proteger suporte de dados](http://msdn.microsoft.com/library/azure/dn282272.aspx).

Quando criar um localizador, poderá haver um atraso de 30 segundo devido ao armazenamento necessário e processos de propagação no armazenamento do Azure.

## <a name="adaptive-streaming"></a>Transmissão em fluxo adaptável
Tecnologias de velocidade de transmissão adaptável permitem aplicações de leitor de vídeo determinar as condições de rede e selecionar a partir de forma várias. Quando degrada a comunicação de rede, o cliente pode selecionar uma velocidade de transmissão inferior para que reproduzir pode continuar com a qualidade do vídeo inferior. Como melhorar a condições de rede, o cliente pode mudar para uma velocidade de transmissão superior com qualidade do vídeo melhorada. Media Services do Azure suporta as seguintes tecnologias de velocidade de transmissão adaptável: HTTP Live Streaming (HLS), transmissão em fluxo uniforme e MPEG-DASH.

Para fornecer aos utilizadores com os URLs de transmissão em fluxo, primeiro tem de criar um localizador OnDemandOrigin. Criar o localizador dá-lhe o caminho de base para o elemento que contenha o conteúdo que pretende transmitir em fluxo. No entanto, para conseguir transmitir este conteúdo, terá de modificar ainda mais este caminho. Para construir um URL completo para o ficheiro de manifesto de transmissão em fluxo, tem de concatenar o valor do localizador de caminho e o manifesto (filename.ism) o nome de ficheiro. Em seguida, anexe **/manifesto** e um formato adequado (se necessário) para o caminho de localizador.

> [!NOTE]
> Também pode transmitir o seu conteúdo através de uma ligação SSL. Para tal, certifique-se que os URLs de transmissão em fluxo começar a utilizar HTTPS. Tenha em atenção que, atualmente, AMS não suporta SSL com domínios personalizados.  
> 

Apenas pode transmitir através de SSL se o ponto de final transmissão em fluxo a partir da qual a fornecer o conteúdo foi criado depois de 10 de Setembro de 2014. Se os URLs de transmissão em fluxo baseiam-se nos pontos finais de transmissão em fluxo criados após 10 de Setembro de 2014, o URL contém "streaming.mediaservices.windows.net." URL de transmissão em fluxo que contenham "origin.mediaservices.windows.net" (o formato antigo) não suportam SSL. Se o seu URL tem um formato antigo e pretender ser capaz de transmitir através de SSL, crie um novo ponto de final de transmissão em fluxo. Utilize URLs com base no novo ponto de final de transmissão em fluxo para transmitir o seu conteúdo através de SSL.

## <a name="a-idurlsstreaming-url-formats"></a><a id="URLs"/>Formatos de URL de transmissão em fluxo

### <a name="mpeg-dash-format"></a>Formato de MPEG-DASH
{de transmissão em fluxo ponto final dos serviços de suporte de dados de nome de conta name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

http://testendpoint-testaccount.Streaming.mediaservices.Windows.NET/fecebb23-46F6-490d-8B70-203e86b0df58/BigBuckBunny.ISM/MANIFEST(Format=mpd-Time-CSF)

### <a name="apple-http-live-streaming-hls-v4-format"></a>Formato de V4 Apple HTTP Live Streaming (HLS)
{de transmissão em fluxo ponto final dos serviços de suporte de dados de nome de conta name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

http://testendpoint-testaccount.Streaming.mediaservices.Windows.NET/fecebb23-46F6-490d-8B70-203e86b0df58/BigBuckBunny.ISM/MANIFEST(Format=m3u8-aapl)

### <a name="apple-http-live-streaming-hls-v3-format"></a>Formato do Apple HTTP Live Streaming (HLS) V3
{de transmissão em fluxo ponto final dos serviços de suporte de dados de nome de conta name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl-v3)

http://testendpoint-testaccount.Streaming.mediaservices.Windows.NET/fecebb23-46F6-490d-8B70-203e86b0df58/BigBuckBunny.ISM/MANIFEST(Format=m3u8-aapl-v3)

### <a name="apple-http-live-streaming-hls-format-with-audio-only-filter"></a>Formato de Apple HTTP Live Streaming (HLS) com o filtro só de áudio
Por predefinição, controla apenas de áudio está incluídas no HLS manifesto. Isto é necessário para certificação da Apple Store para redes celular. Neste caso, se um cliente não tem largura de banda suficiente ou está ligado através de uma ligação de 2G, reprodução muda para só de áudio. Isto ajuda a manter a transmissão em fluxo conteúdo sem memória intermédia, mas não existe nenhum vídeo. Em alguns cenários, poderá ser preferencial player colocação em memória intermédia através de só de áudio. Se pretender remover o registo apenas de áudio, adicione **só de áudio = false** para o URL.

http://testendpoint-testaccount.Streaming.mediaservices.Windows.NET/fecebb23-46F6-490d-8B70-203e86b0df58/BigBuckBunny.ISM/MANIFEST(Format=m3u8-aapl-v3,Audio-Only=FALSE)

Para obter mais informações, consulte [funcionalidades adicionais de saída do suporte de composição de manifesto dinâmica e HLS](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).

### <a name="smooth-streaming-format"></a>Formato de transmissão em fluxo uniforme
{nome da conta do nome dos serviços multimédia do ponto final de transmissão em fluxo}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Exemplo:

http://testendpoint-testaccount.Streaming.mediaservices.Windows.NET/fecebb23-46F6-490d-8B70-203e86b0df58/BigBuckBunny.ISM/manifest

### <a id="fmp4_v20"></a>Manifesto 2.0 de transmissão em fluxo uniforme (manifesto legado)
Por predefinição, o formato de manifesto de transmissão em fluxo uniforme contém a tag de repetição (tag de r). No entanto, algumas jogadores não suportam a tag de r. Os clientes com estes jogadores podem utilizar um formato que desativa a tag de r:

{de transmissão em fluxo ponto final dos serviços de suporte de dados de nome de conta name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=fmp4-v20)

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=fmp4-v20)

## <a name="progressive-download"></a>Transferência progressiva
Com a transferência progressiva, pode começar a reproduzir o suporte de dados antes do ficheiro completo tenha sido transferido. Não é possível transferir progressivamente ISM * (ismv, isma, ismt ou ismc) ficheiros.

Para transferir progressivamente os conteúdos, utilize o tipo de OnDemandOrigin de localizador. O exemplo seguinte mostra o URL que se baseia no tipo de localizador OnDemandOrigin:

    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

Tem a desencriptar quaisquer recursos encriptados de armazenamento que pretende que seja transmitido em fluxo do serviço de origem para transferência progressiva.

## <a name="download"></a>Transferência
Para transferir o conteúdo para um dispositivo cliente, tem de criar um localizador SAS. O localizador SAS dá-lhe acesso ao contentor de armazenamento do Azure onde o ficheiro está localizado. Para criar o URL de transferência, tem de incorporar o nome de ficheiro entre o anfitrião e a assinatura SAS.

O exemplo seguinte mostra o URL que se baseia o localizador SAS:

    https://test001.blob.core.windows.net/asset-ca7a4c3f-9eb5-4fd8-a898-459cb17761bd/BigBuckBunny.mp4?sv=2012-02-12&se=2014-05-03T01%3A23%3A50Z&sr=c&si=7c093e7c-7dab-45b4-beb4-2bfdff764bb5&sig=msEHP90c6JHXEOtTyIWqD7xio91GtVg0UIzjdpFscHk%3D

Aplicam as seguintes considerações:

* Tem a desencriptar quaisquer recursos encriptados de armazenamento que pretende que seja transmitido em fluxo do serviço de origem para transferência progressiva.
* Transferência de um que não foi concluído dentro de 12 horas irá falhar.

## <a name="streaming-endpoints"></a>Pontos finais de transmissões em fluxo

Um ponto final de transmissão em fluxo representa um serviço de transmissão em fluxo que pode proporcionar o conteúdo diretamente a uma aplicação de leitor de cliente ou a uma rede de entrega de conteúdos (CDN) para uma maior distribuição. O fluxo de saída de um serviço de ponto final de transmissão em fluxo pode ser uma transmissão em fluxo em direto ou um elemento de vídeo a pedido na sua conta de Media Services. Existem dois tipos de pontos finais, transmissão em fluxo **padrão** e **premium**. Para obter mais informações, consulte [descrição geral de pontos finais de transmissão em fluxo](media-services-streaming-endpoints-overview.md).

>[!NOTE]
>Quando a sua conta AMS é criada, é adicionado um ponto final de transmissão em fluxo **predefinido** à sua conta no estado **Parado**. Para começar a transmitir o seu conteúdo em fluxo e a tirar partido do empacotamento e encriptação dinâmicos, o ponto final de transmissão em fluxo a partir do qual quer transmitir conteúdo tem de estar no estado **Em execução**. 

## <a name="known-issues"></a>Problemas conhecidos
### <a name="changes-to-smooth-streaming-manifest-version"></a>As alterações a transmissão em fluxo uniforme manifestam versão
Antes da versão do serviço de Julho de 2016 - quando ativos produzido pelo codificador de multimédia Standard, o fluxo de trabalho do suporte de dados codificador Premium ou o codificador de multimédia do Azure anteriores foram transmissão em fluxo utilizando o empacotamento dinâmico – a transmissão em fluxo uniforme manifesto devolvido seria em conformidade com a versão 2.0. Na versão 2.0, as durações de fragmento não utilizar as tags de repetir so-called ('r'). Por exemplo:

<?xml version="1.0" encoding="UTF-8"?>
    <SmoothStreamingMedia MajorVersion="2" MinorVersion="0" Duration="8000" TimeScale="1000">
        <StreamIndex Chunks="4" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="3" Subtype="" Name="video" TimeScale="1000">
            <QualityLevel Index="0" Bitrate="1000000" FourCC="AVC1" MaxWidth="640" MaxHeight="360" CodecPrivateData="00000001674D4029965201405FF2E02A100000030010000003032E0A000F42400040167F18E3050007A12000200B3F8C70ED0B16890000000168EB7352" />
            <c t="0" d="2000" n="0" />
            <c d="2000" />
            <c d="2000" />
            <c d="2000" />
        </StreamIndex>
    </SmoothStreamingMedia>

Na versão de serviço de Julho de 2016, o manifesto de transmissão em fluxo uniforme gerado está em conformidade com a versão 2.2, com durações de fragmento utilizando tags de repetições. Por exemplo:

    <?xml version="1.0" encoding="UTF-8"?>
    <SmoothStreamingMedia MajorVersion="2" MinorVersion="2" Duration="8000" TimeScale="1000">
        <StreamIndex Chunks="4" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="3" Subtype="" Name="video" TimeScale="1000">
            <QualityLevel Index="0" Bitrate="1000000" FourCC="AVC1" MaxWidth="640" MaxHeight="360" CodecPrivateData="00000001674D4029965201405FF2E02A100000030010000003032E0A000F42400040167F18E3050007A12000200B3F8C70ED0B16890000000168EB7352" />
            <c t="0" d="2000" r="4" />
        </StreamIndex>
    </SmoothStreamingMedia>

Alguns dos clientes legados de transmissão em fluxo uniforme podem não suportar as etiquetas de repetições e irão falhar ao carregar o manifesto. Para atenuar este problema, pode utilizar o parâmetro de formato de manifesto legado **(formato = fmp4 v20)** ou atualizar o cliente para a versão mais recente, o qual suporta etiquetas de repetições. Para obter mais informações, consulte [2.0 de transmissão em fluxo uniforme](media-services-deliver-content-overview.md#fmp4_v20).

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Tópicos relacionados
[Atualizar localizadores de serviços de suporte de dados depois de implementar chaves de armazenamento](media-services-roll-storage-access-keys.md)

