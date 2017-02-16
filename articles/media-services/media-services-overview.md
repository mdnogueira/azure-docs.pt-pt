---
title: "Descrição geral e cenários comuns dos Serviços de Multimédia do Azure | Microsoft Docs"
description: "Este tópico fornece uma descrição geral dos Media Services do Azure"
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 7a5e9723-c379-446b-b4d6-d0e41bd7d31f
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 01/05/2017
ms.author: juliako;anilmur
translationtype: Human Translation
ms.sourcegitcommit: e126076717eac275914cb438ffe14667aad6f7c8
ms.openlocfilehash: f22b87fc5bdfe2db5de39adaafe9c71d8c32b26a


---
# <a name="azure-media-services-overview-and-common-scenarios"></a>Descrição geral e cenários comuns dos Serviços de Multimédia do Azure

Os Media Services do Microsoft Azure são uma plataforma baseado na nuvem extensível que permite aos programadores compilar uma gestão de multimédia dimensionável e aplicações de entrega. Os Media Services são baseados nas APIs REST, que permitem carregar, armazenar, codificar e empacotar de forma segura conteúdos de vídeo ou áudio, para o envio de transmissão em fluxo tanto a pedido como em direto para vários clientes (por exemplo, TV, PC e dispositivos móveis).

Pode compilar fluxos de trabalho ponto-a-ponto utilizando inteiramente os Media Services. Pode também optar por utilizar componentes de terceiros para algumas partes do seu fluxo de trabalho. Por exemplo, codificar utilizando um codificador de terceiros. Em seguida, carregue, proteja, empacote e entregue através dos Media Services.

Pode também optar por transmitir os seus conteúdos em direto ou enviar conteúdos a pedido. Este tópico mostra cenários comuns para a entrega dos seus conteúdos [em direto](media-services-overview.md#live_scenarios) ou [a pedido](media-services-overview.md#vod_scenarios). O tópico também está ligado a outros tópicos relevantes.

## <a name="sdks-and-tools"></a>SDKs e ferramentas

Para compilar soluções de Media Services, pode utilizar:

* [API REST dos Serviços de Multimédia](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference)
* Um dos SDKs do Cliente disponíveis:
    * [SDK do .NET dos Media Services do Azure](https://github.com/Azure/azure-sdk-for-media-services),
    * [Azure SDK para Java](https://github.com/Azure/azure-sdk-for-java),
    * [SDK do PHP do Azure](https://github.com/Azure/azure-sdk-for-php),
    * [Media Services do Azure para Node.js](https://github.com/michelle-becker/node-ams-sdk/blob/master/lib/request.js) (Esta é uma versão não Microsoft de um SDK Node.js. Esta é mantida por uma comunidade e não tem atualmente uma cobertura de 100% das APIs do AMS).
* Ferramentas existentes:
    * [Portal do Azure](https://portal.azure.com/)
    * [Explorador de Media Services do Azure](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE – Azure Media Services Explorer, uma aplicação Winforms/C# para Windows)

A imagem seguinte mostra alguns objetos mais frequentemente utilizados ao desenvolver contra o modelo OData dos Serviços de Multimédia.

Clique na imagem para visualizá-lo tamanho completo.  

<a href="./media/media-services-overview/media-services-overview-object-model.png" target="_blank"><img src="./media/media-services-overview/media-services-overview-object-model-small.png"></a> 

Pode ver o modelo de todo [aqui](https://media.windows.net/API/$metadata?api-version=2.15).  


## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
Pode ver percurso de aprendizagem do AMS aqui:

* [Fluxo de Trabalho de Transmissão em Direto do AMS](https://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
* [Fluxo de Trabalho de Transmissão a Pedido do AMS](https://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)

## <a name="prerequisites"></a>Pré-requisitos

Para começar a utilizar os Media Services do Azure, deve ter o seguinte:

1. Uma conta do Azure. Se não tiver uma conta, pode criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com).
2. Uma conta de Media Services do Azure Utilize o Portal do Azure, o .NET ou a API REST para criar a conta dos Serviços de Multimédia do Azure. Para obter mais informações, consulte [Criar Conta](media-services-portal-create-account.md).
3. (Opcional) Configurar o ambiente de desenvolvimento. Escolha .NET ou a API REST para o ambiente de desenvolvimento. Para obter mais informações, consulte [Configurar o ambiente](media-services-dotnet-how-to-use.md).

    Além disso, saiba como [se ligar através de programação](media-services-dotnet-connect-programmatically.md).
4. Um ponto final de transmissão em fluxo standard ou premium no estado iniciado.  Para obter mais informações, consulte [Gerir pontos finais de transmissão em fluxo](https://docs.microsoft.com/en-us/azure/media-services/media-services-portal-manage-streaming-endpoints)

## <a name="concepts-and-overview"></a>Conceitos e descrição geral
Para saber mais sobre os conceitos de Media Services do Azure, consulte [Conceitos](media-services-concepts.md).

Para obter um conjunto de procedimentos que apresente todos os componentes principais dos Media Services do Azure, consulte [Tutorial Passo-a-Passo dos Media Services do Azure](https://docs.com/fukushima-shigeyuki/3439/english-azure-media-services-step-by-step-series). Este conjunto possui uma excelente descrição geral dos conceitos e utiliza a ferramenta AMSE para demonstrar as tarefas AMS. Tenha em atenção que a ferramenta AMSE é uma ferramenta do Windows. Esta ferramenta suporta a maioria das tarefas que pode realizar através de programação com [o SDK do AMS para .NET](https://github.com/Azure/azure-sdk-for-media-services), [Azure SDK para Java](https://github.com/Azure/azure-sdk-for-java) ou [SDK do PHP do Azure](https://github.com/Azure/azure-sdk-for-php).

## <a name="a-idvodscenariosadelivering-media-on-demand-with-azure-media-services-common-scenarios-and-tasks"></a><a id="vod_scenarios"></a>Fornecer Multimédia a Pedido com os Serviços de Multimédia do Azure: cenários e tarefas comuns
Esta secção descreve cenários comuns e fornece ligações para tópicos relevantes. O diagrama seguinte mostra as principais partes da plataforma dos Media Services envolvidas na distribuição de conteúdos a pedido.

![Fluxo de trabalho do VoD](./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png)

>[!NOTE]
>Quando a sua conta AMS é criada, é adicionado um ponto final de transmissão em fluxo **predefinido** à sua conta no estado **Parado**. Para começar a transmitir o seu conteúdo em fluxo e a tirar partido do empacotamento e encriptação dinâmicos, o ponto final de transmissão em fluxo a partir do qual quer transmitir conteúdo tem de estar no estado **Em execução**.

### <a name="protect-content-in-storage-and-deliver-streaming-media-in-the-clear-non-encrypted"></a>Proteger conteúdos em armazenamento e distribuir multimédia de transmissão em fluxo em segurança (não encriptadas)
1. Carregue um ficheiro mezanino de alta qualidade para um elemento.

    É recomendado aplicar uma opção de encriptação de armazenamento ao seu elemento para proteger os seus conteúdos durante o carregamento e enquanto estes permanecem inativos em armazenamento.
2. Codifique para um conjunto de ficheiros MP4 de velocidade de transmissão adaptável.

    É recomendado aplicar uma opção de encriptação de armazenamento ao elemento de saída para proteger os seus conteúdos enquanto estes permanecem.
3. Configure a política de entrega de elementos (utilizada pelo empacotamento dinâmico).

    Se o seu elemento estiver armazenamento encriptado, **deve** configurar a política de distribuição do elemento.
4. Publique o elemento através da criação de um localizador OnDemand.
5. Transmita os conteúdos publicados.

### <a name="protect-content-in-storage-deliver-dynamically-encrypted-streaming-media"></a>Proteger os conteúdos em armazenamento, distribuir multimédia de transmissão em fluxo encriptada de forma dinâmica

1. Carregue um ficheiro mezanino de alta qualidade para um elemento. Aplique a opção de encriptação de armazenamento ao elemento.
2. Codifique para um conjunto de ficheiros MP4 de velocidade de transmissão adaptável. Aplique a opção de encriptação de armazenamento ao elemento de saída.
3. Crie uma chave de conteúdo de encriptação para o elemento que pretende que seja encriptado dinamicamente durante a reprodução.
4. Configure a política de autorização da chave de conteúdo.
5. Configure a política de entrega de elementos (utilizada pelo empacotamento dinâmico e a encriptação dinâmica).
6. Publique o elemento através da criação de um localizador OnDemand.
7. Transmita os conteúdos publicados.

### <a name="use-media-analytics-to-derive-actionable-insights-from-your-videos"></a>Utilize a Análise de Multimédia para derivar conhecimentos acionáveis dos seus vídeos
A Análise de Multimédia é um conjunto de componentes de voz e visão que facilitam a derivação de conhecimentos acionáveis das organizações e empresas dos seus respetivos ficheiros de vídeo. Para obter mais informações, consulte [Descrição Geral da Análise dos Media Services do Azure](media-services-analytics-overview.md).

1. Carregue um ficheiro mezanino de alta qualidade para um elemento.
2. Utilize um dos seguintes serviços de Análise de Multimédia para processar os seus vídeos:

   * **Indexador** – [Processa vídeos com o Media Indexer do Azure 2](media-services-process-content-with-indexer2.md)
   * **Hyperlapse** – [Hyperlapse de Arquivos de Multimédia com Hyperlapse de Multimédia do Azure](media-services-hyperlapse-content.md)
   * **Deteção de movimento** – [Deteção de movimento para Análise de Multimédia do Azure](media-services-motion-detection.md).
   * **Deteção de rostos e emoções do rosto** – [Deteção de emoções e rostos para a Análise de Multimédia do Azure](media-services-face-and-emotion-detection.md).
   * **Resumo do vídeo** – [Utilize as Miniaturas de Vídeo de Multimédia do Azure para Criar um Resumo de Vídeo](media-services-video-summarization.md)
3. Os processadores de multimédia de Análise de Multimédia produzem ficheiros MP4 ou ficheiros JSON. Se um processador de multimédia produzir um ficheiro MP4, pode transferir progressivamente o ficheiro. Se um processador de multimédia produzir um ficheiro JSON, pode transferir o ficheiro a partir do Blob Storage do Azure.

### <a name="deliver-progressive-download"></a>Fornecer transferência progressiva
1. Carregue um ficheiro mezanino de alta qualidade para um elemento.
2. Codifique para um único ficheiro MP4.
3. Publique o elemento através da criação de um localizador OnDemand ou SAS.

    Se utilizar o localizador SAS, o conteúdo é transferido a partir do Blob Storage do Azure. Neste caso, não precisa de ter pontos finais de transmissão em fluxo no estado iniciado.
4. Transferir progressivamente os conteúdos.

## <a name="a-idlivescenariosadelivering-live-streaming-events-with-azure-media-services"></a><a id="live_scenarios"></a>Fornecer Eventos de Transmissão em Direto com os Serviços de Multimédia do Azure
Ao trabalhar com Transmissão em Fluxo em Direto, os seguintes componentes estão normalmente relacionados:

* Uma câmara, que é utilizada para difundir um evento.
* Um codificador vídeo em direto que converte sinais da câmara para transmissões em fluxos que são enviadas para um serviço de transmissão em fluxo em direto.

Opcionalmente, vários codificadores com sincronização de hora em direto. Para determinados eventos críticos em direto que exigem uma enorme disponibilidade e qualidade da experiência, é recomendado utilizar codificadores ativo-ativo com sincronização de hora para obter uma ativação pós-falha totalmente integrada sem perda de dados.

* Um serviço de transmissão em fluxo em direto que permite realizar o seguinte:
* inserir conteúdos em direto utilizando vários protocolos de transmissão em fluxo em direto (por exemplo, RTMP ou Transmissão em Fluxo Uniforme)
* (opcionalmente) codificação da sua transmissão para uma transmissão em fluxo de velocidade de transmissão adaptável
* pré-visualizar a sua transmissão em fluxo em direto
* gravar e armazenar os conteúdos inseridos de forma a transmiti-los em fluxo posteriormente (Vídeo a Pedido)
* distribuir os conteúdos através de protocolos de transmissão em fluxo comuns (por exemplo, MPEG DASH, Uniforme, HLS) diretamente para os seus clientes ou para uma Rede de Entrega de Conteúdos (CDN) para uma maior distribuição.

Os **Media Services do Microsoft Azure** (AMS) fornecem a capacidade de inserir, codificar, pré-visualizar, armazenar e distribuir os seus conteúdos por transmissão em fluxo em direto.

Quando distribui os seus conteúdos aos seus clientes, o seu objetivo é fornecer um vídeo de alta qualidade para vários dispositivos em condições de rede diferentes. Para obter as condições de qualidade e de rede, utilize codificadores em direto para codificar a sua transmissão para uma transmissão em fluxo de velocidade de transmissão múltipla (velocidade de transmissão adaptável).  Para realizar a transmissão em fluxo para dispositivos diferentes, utilize o [empacotamento dinâmico](media-services-dynamic-packaging-overview.md) dos Media Services para empacotar novamente e de forma dinâmica a sua transmissão em fluxo para protocolos diferentes. Os Serviços de Multimédia suportam a distribuição das seguintes tecnologias de transmissão em fluxo de velocidade de transmissão adaptável: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH.

Nos Media Services do Azure, os **Canais**, **Programas** e **Pontos Finais de Transmissão em Fluxo** lidam com todas as funcionalidades de transmissão em fluxo em direto, incluindo inserção, formatação, DVR, segurança, escalabilidade e redundância.

Um **Canal** representa um pipeline de processamento de conteúdos de transmissão em fluxo em direto. Um Canal pode receber transmissões em fluxo de entrada em direto das seguintes formas:

* Um codificador em direto no local envia um **RTMP** ou uma **Transmissão em Fluxo Uniforme** com velocidade de transmissão múltipla (MP4 fragmentado) para o Canal configurado para distribuição **pass-through**. A entrega **pass-through** ocorre quando as transmissões em fluxo ingeridas passam pelos **Canais** sem qualquer transcodificação ou codificação adicionais. Pode utilizar os seguintes codificadores em direto que transmitem Smooth Streaming com velocidade de transmissão múltipla: MediaExcel, Imagine Communications, Ateme, Envivio, Cisco e Elemental. Os seguintes codificadores em direto transmitem RTMP: codificadores Adobe Flash Live Encoder, Haivision, Telestream Wirecast, Teradek e Tricaster.  Um codificador em direto pode também enviar uma transmissão em fluxo de velocidade de transmissão única para um canal, que não está ativado para live encoding, mas tal não é recomendado. Quando solicitado, os Media Services disponibilizam a transmissão em fluxo para os clientes.

> [!NOTE]
> A utilização de um método pass-through é a forma mais económica de realizar uma transmissão em fluxo em direto quando estiver a realizar vários eventos durante um longo período de tempo e já investiu em codificadores no local. Consulte os detalhes dos [preços](https://azure.microsoft.com/pricing/details/media-services/).
>
>

* Um codificador em direto no local envia uma transmissão fluxo de velocidade única para o Canal ativado para realizar live encoding com os Media Services num dos seguintes formatos: RTP (MPEG-TS), RTMP ou Transmissão em Fluxo Uniforme (MP4 fragmentado). O Canal, em seguida, realiza live encoding da transmissão em fluxo de velocidade de transmissão única de entrada para uma transmissão em fluxo de vídeo com várias velocidades (adaptável). Quando solicitado, os Media Services disponibilizam a transmissão em fluxo para os clientes.

### <a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders-pass-through"></a>Trabalhar com Canais que recebem transmissões em fluxo em direto com velocidade de transmissão múltipla a partir de codificadores no local (pass-through)
O diagrama seguinte mostra as principais partes da plataforma de AMS envolvidas no fluxo de trabalho de **pass-through**.

![Fluxo de trabalho em direto][live-overview2]

Para obter mais informações, consulte [Trabalhar com Canais que recebem transmissões em Fluxo em Direto de Múltipla Velocidade de Transmissão a partir de Codificadores no Local](media-services-live-streaming-with-onprem-encoders.md).

### <a name="working-with-channels-that-are-enabled-to-perform-live-encoding-with-azure-media-services"></a>Trabalhar com Canais ativados para realizar live encoding com Media Services do Azure
O diagrama seguinte mostra as partes principais da plataforma do AMS envolvidas no fluxo de trabalho de Transmissão em Fluxo em Direto onde um Canal é ativado para realizar live encoding comMedia Services.

![Fluxo de trabalho em direto][live-overview1]

Para obter mais informações, consulte [Trabalhar com Canais Ativados para Realizar Live Encoding com Media Services do Azure](media-services-manage-live-encoder-enabled-channels.md).

## <a name="consuming-content"></a>Consumo de conteúdos
Os Media Services do Azure fornecem as ferramentas necessárias para criar aplicações avançadas e dinâmicas de leitor de cliente para a maioria das plataformas, incluindo: Dispositivos iOS dispositivos, Dispositivos Android, Windows, Windows Phone, Xbox e Caixas descodificadoras. O tópico seguinte fornece ligações para SDKs e Estruturas do Leitor que pode utilizar para desenvolver as suas próprias aplicações de cliente que podem consumir multimédia de transmissão de fluxo a partir dos Media Services.

[Developing Video Player Applications (Desenvolver Aplicações do Leitor de Vídeo)](media-services-develop-video-players.md)

## <a name="enabling-azure-cdn"></a>Ativar a CDN do Azure
Os Media Services suportam a integração com a CDN do Azure. Para obter mais informações sobre como ativar a CDN do Azure, consulte [Como Gerir Transmissão em Fluxo de Pontos Finais numa Conta de Media Services](media-services-portal-manage-streaming-endpoints.md).

## <a name="scaling-a-media-services-account"></a>Escalar uma conta dos Media Services

Pode escalar **Media Services** especificando o número das **Unidades Reservadas para Transmissão em Fluxo** e das **Unidades de Codificação Reservadas** que pretende aprovisionar na sua conta.

Também pode escalar a sua conta dos Media Services adicionando-lhe contas deo Storage. Cada conta do Storage está limitada a 500 TB. Para expandir o limite predefinido do seu armazenamento, pode optar por ligar várias contas do Storage numa única conta de Media Services.
Os clientes dos Serviços de Multimédia escolhem um ponto final de transmissão em fluxo **Standard** ou um ou mais pontos finais de transmissão em fluxo **Premium**, de acordo com as respetivas necessidades. O Ponto Final de Transmissão em Fluxo Standard é ideal para a maioria das cargas de trabalho de transmissão em fluxo. Inclui as mesmas funcionalidades das Unidades de Transmissão em Fluxo Premium.
O Ponto Final de Transmissão em Fluxo Standard é ideal para a maioria das cargas de trabalho de transmissão em fluxo. Se tiver uma carga de trabalho avançada ou os seus requisitos de capacidade de transmissão em fluxo não forem adequadas aos destinos do débito do ponto final de transmissão em fluxo standard, ou se quiser controlar a capacidade do serviço do Ponto Final de Transmissão em Fluxo para processar as necessidades crescentes de largura de banda ao ajustar as unidades de escala (também conhecidas como unidades de transmissão em fluxo premium), recomenda-se a alocação de unidades de escala.

[Este](media-services-portal-scale-streaming-endpoints.md) tópico está ligado a tópicos relevantes.

## <a name="support"></a>Suporte
O [Suporte do Azure](https://azure.microsoft.com/support/options/) fornece opções de suporte para o Azure, incluindo os Media Services.

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="service-level-agreement-sla"></a>Contrato de Nível de Serviço (SLA)
* Para Media Services Encoding, podemos garantir 99,9% de disponibilidade das transações da API REST.
* Para a Transmissão em Fluxo, iremos responder com êxito aos pedidos com 99,9% de disponibilidade garantida para os conteúdos de multimédia existentes quando for comprado um ponto final de transmissão em fluxo standard ou premium.
* Para Canais em Direto, garantimos que a execução dos Canais terá uma conectividade externa em, pelo menos, 99,9% das vezes.
* Para Content Protection, garantimos que iremos satisfazer com êxito os pedidos-chave em, pelo menos, 99,9% das vezes.
* Para o Indexador, iremos satisfazer com êxito os pedidos de Tarefa do Indexador com uma Unidade de Codificação Reservada em, pelo menos, 99,9% das vezes.

Para obter mais informações, consulte [SLA do Microsoft Azure](https://azure.microsoft.com/support/legal/sla/).

<!-- Images -->
[overview]: ./media/media-services-overview/media-services-overview.png
[vod-overview]: ./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png
[live-overview1]: ./media/media-services-live-streaming-workflow/media-services-live-streaming-new.png
[live-overview2]: ./media/media-services-live-streaming-workflow/media-services-live-streaming-current.png



<!--HONumber=Jan17_HO2-->


