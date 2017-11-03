---
title: "Descrição geral em direto de transmissão em fluxo utilizando os Media Services do Azure | Microsoft Docs"
description: "Este tópico fornece uma descrição geral da transmissão em direto com Media Services do Azure."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: fb63502e-914d-4c1f-853c-4a7831bb08e8
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 06/29/2017
ms.author: juliako
ms.openlocfilehash: 6f500f25129470a679c75cae6cd1abc9d71b72a7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-live-streaming-using-azure-media-services"></a>Descrição geral em direto de transmissão em fluxo utilizando os Media Services do Azure
## <a name="overview"></a>Descrição geral
Quando distribui os eventos de transmissão em fluxo em direto com Media Services do Azure os seguintes componentes estão normalmente relacionados:

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

Quando distribui os seus conteúdos aos seus clientes, o seu objetivo é fornecer um vídeo de alta qualidade para vários dispositivos em condições de rede diferentes. Para tal, utilize codificadores em direto para codificar a sua transmissão em fluxo vídeo transmissão múltipla (velocidade de transmissão adaptável).  Para realizar a transmissão em fluxo para dispositivos diferentes, utilize o [empacotamento dinâmico](media-services-dynamic-packaging-overview.md) dos Media Services para empacotar novamente e de forma dinâmica a sua transmissão em fluxo para protocolos diferentes. Os Serviços de Multimédia suportam a distribuição das seguintes tecnologias de transmissão em fluxo de velocidade de transmissão adaptável: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH.

Nos Media Services do Azure, os **Canais**, **Programas** e **Pontos Finais de Transmissão em Fluxo** lidam com todas as funcionalidades de transmissão em fluxo em direto, incluindo inserção, formatação, DVR, segurança, escalabilidade e redundância.

Um **Canal** representa um pipeline de processamento de conteúdos de transmissão em fluxo em direto. Um Canal pode receber transmissões em fluxo de entrada em direto das seguintes formas:

* Um codificador em direto no local envia um **RTMP** ou uma **Transmissão em Fluxo Uniforme** com velocidade de transmissão múltipla (MP4 fragmentado) para o Canal configurado para distribuição **pass-through**. A distribuição **pass-through** ocorre quando as transmissões em fluxo inseridas passam pelos **Canais** sem qualquer processamento adicional. Pode utilizar os seguintes codificadores em direto com velocidade de múltipla transmissão em fluxo uniforme de saída: MediaExcel, Ateme, Imagine comunicações, Envivio, Cisco e elementar. Os seguintes codificadores em direto de saída RTMP: Adobe Flash suporte de dados em direto codificador (FMLE), Telestream Wirecast, Haivision, Teradek e Transcodificadores tricaster.  Um codificador em direto pode também enviar uma transmissão em fluxo de velocidade de transmissão única para um canal, que não está ativado para live encoding, mas tal não é recomendado. Quando solicitado, os Media Services disponibilizam a transmissão em fluxo para os clientes.

  > [!NOTE]
  > A utilização de um método pass-through é a forma mais económica de realizar uma transmissão em fluxo em direto quando estiver a realizar vários eventos durante um longo período de tempo e já investiu em codificadores no local. Consulte os detalhes dos [preços](https://azure.microsoft.com/pricing/details/media-services/).
  > 
  > 
* Um codificador em direto no local envia um fluxo de velocidade de transmissão única para o canal ativado para realizar live encoding com Media Services dos seguintes formatos: RTMP ou transmissão em fluxo uniforme (MP4 fragmentados). RTP (MPEG-TS) também é suportada, fornecida tem uma ligação dedicada ao centro de dados do Azure. Os seguintes codificadores em direto com saída RTMP são conhecidos para trabalhar com canais deste tipo: Telestream Wirecast, FMLE. O Canal, em seguida, realiza live encoding da transmissão em fluxo de velocidade de transmissão única de entrada para uma transmissão em fluxo de vídeo com várias velocidades (adaptável). Quando solicitado, os Media Services disponibilizam a transmissão em fluxo para os clientes.

A partir da versão 2.10 de serviços de suporte de dados, quando criar um canal, pode especificar de que forma pretende que para o canal receber o fluxo de entrada e se pretende ou não quiser para o canal realizar live encoding da sua transmissão em fluxo. Tem duas opções:

* **Nenhum** (pass-through) – especificar este valor, se planeia utilizar um codificador em direto no local, que será de saída do fluxo de transmissão múltipla (uma sequência de pass-through). Neste caso, o fluxo de entrada passem a saída sem qualquer tipo de codificação. Este é o comportamento de um canal antes da versão 2.10.  
* **Standard** – escolha este valor, se pretender utilizar os Media Services para codificar a sua transmissão em fluxo em direto para o fluxo de transmissão múltipla velocidade de transmissão única. Este método é mais económico para como aumentar verticalmente rapidamente para eventos pouco frequentes. Lembre-se de que existe um impacto de faturação para live encoding e deve Lembre-se de que sai de um canal de codificação em direto no estado "Em execução" resultará em encargos faturação.  Recomenda-se que pare os canais executar imediatamente depois de concluída a evitar custos extra por hora do evento de transmissão em fluxo em direto.

## <a name="comparison-of-channel-types"></a>Comparação dos tipos de canal
A tabela seguinte fornece um guia para comparar os dois tipos de canal suportados nos Media Services

| Funcionalidade | Canal pass-through | Canal de padrão |
| --- | --- | --- |
| Entrada de velocidade de transmissão única é codificada em múltiplos de forma na nuvem |Não |Sim |
| Resolução máxima, número de camadas |1080p, 8 camadas, 60 + fps |720p, 6 camadas, 30 fps |
| Protocolos de entrada |RTMP, transmissão em fluxo de uniforme |RTMP, transmissão em fluxo uniforme e RTP |
| Preço |Consulte o [página de preços](https://azure.microsoft.com/pricing/details/media-services/) e clique no separador "Vídeo em direto" |Consulte o [página de preços](https://azure.microsoft.com/pricing/details/media-services/) |
| Tempo de execução máximo |24x7 |8 horas |
| Suporte para inserir slates |Não |Sim |
| Suporte para ad sinalização |Não |Sim |
| Legendas de CEA 608/708 pass-through |Sim |Sim |
| Capacidade de recuperar breves stalls no feed de contribuição |Sim |Não (canal começará slating após 6 + segundos w/o dados de entrada) |
| Suporte para GOPs de entrada não uniforme |Sim |Não – entrada têm de ser corrigida seg 2 GOPs |
| Suporte para a entrada de taxa de moldura variável |Sim |Não – entrada têm de ser corrigida velocidade de fotogramas.<br/>Variações secundárias são tolerated, por exemplo, durante em segundo plano do movimento elevada. Mas o codificador não é possível remover a 10 fotogramas por segundo. |
| Auto-shutoff de canais de entrada quando feed está perdido |Não |Após 12 horas, se não houver nenhum programa em execução |

## <a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders-pass-through"></a>Trabalhar com Canais que recebem transmissões em fluxo em direto com velocidade de transmissão múltipla a partir de codificadores no local (pass-through)
O diagrama seguinte mostra as principais partes da plataforma de AMS envolvidas no fluxo de trabalho de **pass-through**.

![Fluxo de trabalho em direto](./media/media-services-live-streaming-workflow/media-services-live-streaming-current.png)

Para obter mais informações, consulte [Trabalhar com Canais que recebem transmissões em Fluxo em Direto de Múltipla Velocidade de Transmissão a partir de Codificadores no Local](media-services-live-streaming-with-onprem-encoders.md).

## <a name="working-with-channels-that-are-enabled-to-perform-live-encoding-with-azure-media-services"></a>Trabalhar com Canais ativados para realizar live encoding com Media Services do Azure
O diagrama seguinte mostra as partes principais da plataforma do AMS envolvidas no fluxo de trabalho de Transmissão em Fluxo em Direto onde um Canal é ativado para realizar live encoding comMedia Services.

![Fluxo de trabalho em direto](./media/media-services-live-streaming-workflow/media-services-live-streaming-new.png)

Para obter mais informações, consulte [Trabalhar com Canais Ativados para Realizar Live Encoding com Media Services do Azure](media-services-manage-live-encoder-enabled-channels.md).

## <a name="description-of-a-channel-and-its-related-components"></a>Descrição de um canal e os respetivos componentes relacionados
### <a name="channel"></a>Canal
Nos Media Services, [canal](https://docs.microsoft.com/rest/api/media/operations/channel)s são responsáveis por processar o conteúdo de transmissão em fluxo em direto. Um canal fornece um ponto final de entrada (URL de inserção) que, em seguida, forneça a um transcoder em direto. O canal recebe fluxos de entrada em direto do transcoder em direto e torna a mesma disponível para transmissão em fluxo através de um ou mais lidam. Canais também fornecem um ponto final de pré-visualização (URL de pré-visualização) que utilizar para pré-visualizar e validar a sua transmissão em fluxo antes do processamento adicional e entrega.

Pode obter o URL de inserção e o URL de pré-visualização quando criar o canal. Para obter estes URLs, o canal não tem de estar no estado iniciado. Quando estiver pronto para começar a enviar por push dados de um transcoder em direto para o canal, o canal tem de ser iniciado. Assim que for iniciada a transcoder em direto a ingestão de dados, pode pré-visualizar a transmissão.

Cada conta de Media Services pode conter vários canais, vários programas e lidam vários. Consoante as necessidades de segurança e de largura de banda, podem ser dedicados StreamingEndpoint serviços para um ou mais canais. Qualquer StreamingEndpoint pode solicitar a partir de qualquer canal.

### <a name="program"></a>Programa
A [programa](https://docs.microsoft.com/rest/api/media/operations/program) permite-lhe controlar a publicação e armazenamento de segmentos numa transmissão em fluxo em direto. Canais gerem Programas. A relação entre o Canal e o Programa é muito semelhante à multimédia tradicional onde um canal tem uma transmissão em fluxo constante de conteúdo e um programa está confinado a alguns eventos temporizados nesse canal.
Pode especificar o número de horas que pretenda manter o conteúdo gravado para o programa, definindo o **ArchiveWindowLength** propriedade. Este valor pode ser definido a partir de um mínimo de 5 minutos até um máximo de 25 horas.

ArchiveWindowLength dita também que a quantidade máxima de clientes de hora pode recuar a partir da posição atual em direto. Os programas podem ser executados durante o período de tempo especificado, mas o conteúdo que se situe atrás da duração da janela é continuamente descartado. O valor desta propriedade também determina durante quanto tempo os manifestos dos clientes podem aumentar.

Cada programa está associado um Elemento. Para publicar o programa tem de criar um localizador para o elemento associado. Ter este localizador irá permitir compilar um URL de transmissão em fluxo que pode fornecer aos seus clientes.

Um canal suporta até três programas em execução em simultâneo para que possa criar vários arquivos da mesma transmissão em fluxo recebida. Isto permite publicar e arquivar diferentes partes de um evento, conforme necessário. Por exemplo, os seus requisitos de negócios devem arquivar 6 horas de um programa, mas difundir apenas os últimos 10 minutos. Para tal, tem de criar dois programas em execução em simultâneo. Um programa está definido para arquivar 6 horas do evento, mas o programa não está publicado. O outro programa está definido para arquivar durante 10 minutos e este está publicado.

## <a name="billing-implications"></a>Implicações de faturação
Um canal começa assim que é transições de estado para "Em execução" através da API de faturação.  

A tabela seguinte mostra como os Estados de canal mapeiam Estados de faturação no portal do Azure e API. Tenha em atenção que os Estados são ligeiramente diferentes entre a API e Portal UX. Assim que um canal está no estado "Em execução" através da API ou, no estado "Pronto" ou "Transmissão em fluxo" no portal do Azure, faturação estará ativa.

Para parar o canal de faturação mais, terá de parar o canal através da API ou no portal do Azure.
É responsável por parar os canais quando tiver terminado com o canal. Falha ao parar o canal irá resultar no faturação contínua.

> [!NOTE]
> Ao trabalhar com canais padrão, AMS será automaticamente shutoff nenhum canal que ainda está no estado "Em execução" 12 horas depois do feed de entrada não se tenha perdido e existem não existem programas em execução. No entanto, será ainda faturado por período de tempo, que o canal estava num estado "Em execução".
>
>

### <a id="states"></a>Estados de canal e a forma como efectuam o mapeamento para o modo de faturação
O estado atual de um Canal. Os valores possíveis incluem:

* **Parado**. Este é o estado inicial do canal após a respetiva criação (a menos que autostart foi seleccionado no portal.) Não existem faturação ocorre neste estado. Neste estado, as propriedades do Canal podem ser atualizadas, mas a transmissão em fluxo não é permitida.
* **Iniciar**. O Canal está a ser iniciado. Não existem faturação ocorre neste estado. Não são permitidas transmissões em fluxo nem atualizações durante este estado. Caso ocorra um erro, o Canal volta para o estado Parado.
* **Executar**. O Canal é capaz de processar transmissões em fluxo. Agora, está faturação utilização. Tem de parar o canal para impedir mais de faturação.
* **Parar**. O Canal está a ser parado. Não existem faturação ocorre neste estado transitório. Não são permitidas transmissões em fluxo nem atualizações durante este estado.
* **Eliminar**. O Canal está a ser apagado. Não existem faturação ocorre neste estado transitório. Não são permitidas transmissões em fluxo nem atualizações durante este estado.

A tabela seguinte mostra como os estados de um Canal mapeiam para o modo de faturação.

| Estado do canal | Indicadores IU do portal | É faturação? |
| --- | --- | --- |
| A Iniciar |A Iniciar |Não (estado transitório) |
| A Executar |Pronto (nenhum programa em execução)<br/>ou<br/>A Transmitir em fluxo (pelo menos um programa em execução) |SIM |
| A Parar |A Parar |Não (estado transitório) |
| Parada |Parada |Não |

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Tópicos relacionados
[Especificação de inserção de Media Services do Azure Live MP4 fragmentados](media-services-fmp4-live-ingest-overview.md)

[Trabalhar com canais ativados para realizar Live Encoding com Media Services do Azure](media-services-manage-live-encoder-enabled-channels.md)

[Trabalhar com Canais que Recebem Transmissões em Fluxo em Direto com Velocidade de Transmissão Múltipla a partir de Codificadores no Local (Working with Channels that Receive Multi-bitrate Live Stream from On-premises Encoders)](media-services-live-streaming-with-onprem-encoders.md)

[Quotas e limitações](media-services-quotas-and-limitations.md).  

[Conceitos de serviços de multimédia](media-services-concepts.md)
