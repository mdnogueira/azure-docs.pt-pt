---
title: "Fluxo em direto com codificadores no local que criar fluxos de transmissão múltipla - Azure | Microsoft Docs"
description: "Este tópico descreve como configurar um canal que recebe um transmissão múltipla em fluxo em direto de um codificador no local. O fluxo de, em seguida, pode ser fornecido para aplicações de reprodução de cliente através de um ou mais transmissão em fluxo pontos finais, utilizando um dos seguintes protocolos de transmissão em fluxo adaptável: HLS, transmissão em fluxo uniforme, DASH."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: d9f0912d-39ec-4c9c-817b-e5d9fcf1f7ea
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 04/12/2017
ms.author: cenkd;juliako
ms.openlocfilehash: d7c33dc0a3c1f01cc53a91e05feb33272cb21f47
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="live-streaming-with-on-premises-encoders-that-create-multi-bitrate-streams"></a>Em direto com codificadores no local que criar fluxos de transmissão múltipla transmissão em fluxo
## <a name="overview"></a>Descrição geral
Nos Media Services do Azure, uma *canal* representa um pipeline de processamento de conteúdos de transmissão em direto. Um canal recebe fluxos de entrada em direto de uma das seguintes formas:

* Um codificador em direto de no local envia uma transmissão múltipla RTMP ou transmissão em fluxo uniforme (MP4 fragmentados) transmitido em fluxo para o canal não está ativado para realizar live encoding com Media Services. Transmita as transmissões em fluxo através dos canais sem qualquer processamento adicional. Este método é denominado *pass-through*. Um codificador em direto pode também enviar um fluxo de velocidade de transmissão única para um canal que não está ativado para live encoding, mas não recomendamos que. Os Media Services disponibilizam a transmissão fluxo para os clientes que solicitá-la.

  > [!NOTE]
  > Utilizar um método pass-through é a forma mais económica TTL transmissão em fluxo.


* Um codificador em direto no local envia um fluxo de velocidade de transmissão única para o canal ativado para realizar live encoding com Media Services dos seguintes formatos: RTP (MPEG-TS), RTMP ou transmissão em fluxo uniforme (MP4 fragmentados). O canal, em seguida, realiza a live encoding do fluxo de velocidade de transmissão única de entrada para um fluxo com velocidade de vídeo (adaptável). Os Media Services disponibilizam a transmissão fluxo para os clientes que solicitá-la.

A partir da versão 2.10 de serviços de suporte de dados, quando criar um canal, pode especificar como pretende que o canal para receber o fluxo de entrada. Também pode especificar se pretende que o canal para realizar live encoding da sua transmissão em fluxo. Tem duas opções:

* **Pass-Through**: especificar este valor se planeia utilizar um codificador em direto no local que tenha uma sequência de transmissão múltipla (uma sequência de pass-through) como saída. Neste caso, o fluxo de entrada atravessa a saída sem qualquer tipo de codificação. Este é o comportamento de um canal antes do lançamento 2.10. Este artigo fornece detalhes sobre como trabalhar com canais deste tipo.
* **Live Encoding**: Escolha este valor se planear utilizar serviços de suporte de dados para codificar a sua transmissão em fluxo em direto para uma sequência de transmissão múltipla velocidade de transmissão única. Abandonar o fileparser um canal de codificação em direto num **com** Estado implica encargos de faturação. Recomendamos que pare os canais executar imediatamente depois de concluída a evitar custos extra por hora do evento de transmissão em direto. Os Media Services disponibilizam a transmissão fluxo para os clientes que solicitá-la.

> [!NOTE]
> Este artigo aborda os atributos de canais que não estão ativados para realizar live encoding. Para obter informações sobre como trabalhar com canais ativados para realizar live encoding, consulte [em direto de transmissão em fluxo utilizando os Media Services do Azure para criar fluxos de transmissão múltipla](media-services-manage-live-encoder-enabled-channels.md).
>
>Para obter informações sobre codificadores recomendada no local, consulte [recomendado codificadores no local](media-services-recommended-encoders.md).

O diagrama seguinte representa um fluxo de trabalho em fluxo em direto, que utiliza um codificador em direto no local para ter RTMP de transmissão múltipla ou MP4 fragmentados fluxos (transmissão em fluxo uniforme) como saída.

![Fluxo de trabalho em direto][live-overview]

## <a id="scenario"></a>Cenário comum de transmissão em direto
Os passos seguintes descrevem as tarefas envolvidas na criação de aplicações comuns de transmissão em direto.

1. Ligue uma câmara de vídeo a um computador. Iniciar e configurar um codificador em direto no local que tenha um RTMP de transmissão múltipla ou MP4 fragmentados fluxo (transmissão em fluxo uniforme) como saída. Para obter mais informações, consulte [Suporte RTMP dos Media Services do Azure e Codificadores em Direto](http://go.microsoft.com/fwlink/?LinkId=532824).

    Também pode efetuar este passo depois de criar o canal.
2. Crie e inicie um canal.

3. URL de inserção de obter o canal.

    O codificador em direto utiliza o URL de inserção para enviar a transmissão para o canal.
4. Obter o URL de pré-visualização do canal.

    Utilize este URL para verificar se o canal está a receber corretamente a transmissão em fluxo em direto.
5. Crie um programa.

    Quando utiliza o portal do Azure, criar um programa também cria um recurso.

    Quando utiliza o .NET SDK ou REST, terá de criar um recurso e especificar para utilizar este recurso ao criar um programa.
6. Publique o elemento associado o programa.   

    >[!NOTE]
    >Quando é criada a sua conta de Media Services do Azure, uma **predefinido** ponto final de transmissão em fluxo é adicionado à sua conta na **parado** estado. O ponto final de transmissão em fluxo a partir do qual quer transmitir conteúdo tem de estar no estado **Em execução**.

7. Inicie o programa quando estiver pronto para começar a transmissão em fluxo e o arquivamento.

8. Opcionalmente, o codificador em direto pode ser indicado para iniciar um anúncio. O anúncio é inserido na transmissão de saída.

9. Pare o programa sempre que pretender interromper a transmissão e arquivar o evento.

10. Eliminar o programa (e, opcionalmente, elimine o elemento).     

## <a id="channel"></a>Descrição de um canal e os respetivos componentes relacionados
### <a id="channel_input"></a>Canal de entrada (inserção) configurações
#### <a id="ingest_protocols"></a>Transmissão em fluxo de protocolo de inserção
Os Media Services suportam ingestão relacionadas feeds em direto utilizando MP4 de transmissão múltipla fragmentada e RTMP de transmissão múltipla protocolos de transmissão em fluxo. Quando o RTMP de inserção protocolo de transmissão em fluxo é selecionada, dois pontos finais (entrados) de inserção são criados para o canal:

* **URL primário**: Especifica o URL completamente qualificado da RTMP primário o canal de ponto final de inserção.
* **URL secundário** (opcional): Especifica o URL completamente qualificado da RTMP secundário o canal de ponto final de inserção.

Utilize o URL secundário se pretender melhorar a tolerância de durabilidade e falhas do seu inserção fluxo (bem como tolerância de ativação pós-falha e falhas do codificador), especialmente para os seguintes cenários:

- Codificador único duplo enviar aos URLs primários e secundários:

    O objetivo principal deste cenário é fornecer mais resiliência flutuações de rede e jitters. Não processam alguns codificadores RTMP rede desliga bem. Quando acontece um rede se desligar, um codificador poderá parar de codificação e, em seguida, enviar os dados colocados em memória intermédia quando acontece uma restabelecer a ligação. Isto faz com que discontinuities e perda de dados. Desliga da rede pode acontecer devido a uma rede incorreto ou a manutenção do lado do Azure. URLs principal/secundário reduzir os problemas de rede e fornecem um processo de atualização controlado. Sempre que um agendada rede se desligar acontecer, os Media Services gere primário e secundário desliga e fornece um atrasada desligar entre os dois. Codificadores em seguida, tem tempo para manter envio de dados e voltar a ligar novamente. A ordem do desliga pode ser aleatórias, mas será sempre haver um atraso entre URLs secundário/primário ou secundário/primária. Neste cenário, o codificador ainda é o ponto único de falha.

- Vários codificadores, com cada codificador enviar para um ponto de dedicado:

    Este cenário fornece tanto codificador e ingere redundância. Neste cenário, encoder1 pushes para o URL principal e encoder2 pushes para o URL secundário. Quando um codificador falha, o codificador de outro pode manter a enviar dados. Redundância de dados pode ser mantida porque os serviços de suporte de dados não desliga URLs primários e secundários ao mesmo tempo. Este cenário pressupõe que codificadores são tempo sincronizado e fornecerem exatamente os mesmos dados.  

- Vários codificadores duplo enviar aos URLs primários e secundários:

    Neste cenário, ambos os codificadores enviar dados para os URLs primários e secundários. Isto fornece a melhor fiabilidade e tolerância a falhas, bem como redundância de dados. Este cenário pode tolerar a ambas as falhas do codificador e desliga, mesmo se um codificador deixa de funcionar. Pressupõe que codificadores são tempo sincronizado e fornecerem exatamente os mesmos dados.  

Para obter informações sobre codificadores em direto RTMP, consulte [suporte de RTMP de serviços de suporte de dados do Azure e codificadores em direto](http://go.microsoft.com/fwlink/?LinkId=532824).

#### <a name="ingest-urls-endpoints"></a>Ingestão de URLs (pontos finais)
Um canal fornece um ponto final de entrada (URL de inserção) que especificou no codificador em direto, pelo que pode impulsionar o codificador fluxos os canais.   

Pode obter os URLs de inserção quando criar o canal. Para que possa obter estes URLs, o canal não tem de ser o **executar** estado. Quando estiver pronto para começar a enviar por push dados para o canal, o canal tem de constar da **executar** estado. Depois do canal iniciado ingestão de dados, pode pré-visualizar a transmissão através do URL de pré-visualização.

Tem uma opção de ingestão relacionadas um MP4 fragmentados (transmissão em fluxo uniforme) em fluxo em direto através de uma ligação SSL. Para a ingestão através de SSL, certifique-se atualizar o URL de inserção para HTTPS. Atualmente, não é possível de inserção RTMP através de SSL.

#### <a id="keyframe_interval"></a>Intervalo de Keyframe
Quando estiver a utilizar um codificador em direto no local para gerar o fluxo de transmissão múltipla, o intervalo de keyframe Especifica a duração do grupo de imagens (GOP) utilizado pelo codificador que externo. Depois do canal recebe esta sequência de entrada, pode fornecer sua transmissão em fluxo em direto para aplicações de reprodução de cliente em qualquer um dos seguintes formatos: transmissão em fluxo uniforme, dinâmica transmissão em fluxo adaptável através de HTTP (DASH) e HTTP Live Streaming (HLS). Quando estiver a fazer a transmissão em fluxo em direto, HLS é sempre empacotada dinamicamente. Por predefinição, os Media Services calcula automaticamente o HLS segmento empacotamento rácio (fragmentos por segmento) com base no intervalo keyframe que é recebido do codificador em direto.

A tabela seguinte mostra como é calculada a duração de segmento:

| Intervalo de Keyframe | Rácio de empacotamento de segmento HLS (FragmentsPerSegment) | Exemplo |
| --- | --- | --- |
| Menor ou igual a 3 segundos |3:1 |Se KeyFrameInterval (ou GOP) segundos 2, o rácio de empacotamento de segmento HLS predefinido é 3 para 1. Esta ação cria um segmento HLS 6 segundo. |
| 3 a 5 segundos |2:1 |Se KeyFrameInterval (ou GOP) é de 4 segundos, o rácio de empacotamento de segmento HLS predefinido é 2 para 1. Esta ação cria um segmento HLS segundo 8. |
| Maior 5 segundos |1:1 |Se KeyFrameInterval (ou GOP) segundos 6, o rácio de empacotamento de segmento HLS predefinido é 1 para 1. Esta ação cria um segmento HLS 6 segundo. |

Pode alterar o rácio de fragmentos por segmento ao configurar a saída do canal e definição FragmentsPerSegment ChannelOutputHls.

Também pode alterar o valor do intervalo keyframe ao definir a propriedade KeyFrameInterval ChanneInput. Se definir explicitamente KeyFrameInterval, os HLS segmentar rácio de empacotamento que fragmentspersegment é calculada através de regras descritas anteriormente.  

Se definir explicitamente KeyFrameInterval e FragmentsPerSegment, os Media Services utiliza os valores que definir.

#### <a name="allowed-ip-addresses"></a>Endereços IP permitidos
Pode definir os endereços IP que estão autorizados a publicar vídeo neste canal. Pode ser especificado um endereço IP permitido como um dos seguintes:

* Um endereço IP único (por exemplo, 10.0.0.1)
* Um intervalo IP que utiliza um endereço IP e uma máscara de sub-rede CIDR (por exemplo, 10.0.0.1/22)
* Um intervalo IP que utiliza um endereço IP e uma máscara de sub-rede de ponto decimal (por exemplo, 10.0.0.1(255.255.252.0))

Se não houver endereços IP são especificados e não existe nenhuma definição de regra, em seguida, não é permitido nenhum endereço IP. Para permitir um endereço IP, crie uma regra e defina 0.0.0.0/0.

### <a name="channel-preview"></a>Pré-visualização do canal
#### <a name="preview-urls"></a>URLs de pré-visualização
Canais de fornecem um ponto final de pré-visualização (URL de pré-visualização) que utilizar para pré-visualizar e validar a sua transmissão em fluxo antes do processamento adicional e entrega.

Pode obter o URL de pré-visualização quando criar o canal. Para que possa obter o URL, o canal não tem de ser o **executar** estado. Depois do canal iniciado ingestão de dados, pode pré-visualizar a transmissão.

Atualmente, o fluxo de pré-visualização pode ser fornecido apenas em MP4 fragmentados formato (transmissão em fluxo uniforme), independentemente do tipo de entrada especificado. Pode utilizar o [Monitor de estado de funcionamento de transmissão em fluxo uniforme](http://smf.cloudapp.net/healthmonitor) leitor para testar a transmissão em fluxo uniforme. Também pode utilizar um leitor de que está alojado no portal do Azure para ver a sua transmissão em fluxo.

#### <a name="allowed-ip-addresses"></a>Endereços IP permitidos
Pode definir os endereços IP que estão autorizados a ligar ao ponto final de pré-visualização. Se não houver endereços IP forem especificados, é permitido qualquer endereço IP. Pode ser especificado um endereço IP permitido como um dos seguintes:

* Um endereço IP único (por exemplo, 10.0.0.1)
* Um intervalo IP que utiliza um endereço IP e uma máscara de sub-rede CIDR (por exemplo, 10.0.0.1/22)
* Um intervalo IP que utiliza um endereço IP e uma máscara de sub-rede de ponto decimal (por exemplo, 10.0.0.1(255.255.252.0))

### <a name="channel-output"></a>Canal de saída
Para obter informações sobre a saída de canal, consulte o [Keyframe intervalo](#keyframe_interval) secção.

### <a name="channel-managed-programs"></a>Gerido de canal de programas
Um canal está associado a programas que pode utilizar para controlar a publicação e armazenamento de segmentos numa transmissão em fluxo em direto. Canais gerem programas. A relação de canal e o programa é semelhante à multimédia tradicional, onde um canal tem uma transmissão em fluxo constante de conteúdo e um programa está confinado a alguns eventos temporizados nesse canal.

Pode especificar o número de horas que pretenda manter o conteúdo gravado para o programa através da configuração da duração da **Janela de Arquivo**. Este valor pode ser definido a partir de um mínimo de 5 minutos até um máximo de 25 horas. Duração da janela de arquivo dita também que o número máximo de clientes de hora pode recuar a partir da posição atual em direto. Os programas podem ser executados durante o período de tempo especificado, mas o conteúdo que se situe atrás da duração da janela é continuamente descartado. O valor desta propriedade também determina durante quanto tempo os manifestos dos clientes podem aumentar.

Cada programa está associado um recurso que armazena o conteúdo de transmissão em fluxo. Um recurso está mapeado para um contentor de BLOBs de blocos na conta do storage do Azure e os ficheiros no elemento são armazenados como blobs no contentor. Para publicar o programa para que os seus clientes, podem ver a sequência, tem de criar um localizador OnDemand para o elemento associado. Pode utilizar este localizador para compilar um URL de transmissão em fluxo que pode fornecer aos seus clientes.

Um canal suporta até três programas em execução em simultâneo, para que possa criar vários arquivos da mesma transmissão em fluxo recebida. Pode publicar e arquivar diferentes partes de um evento, conforme necessário. Por exemplo, imagine que é o requisito de negócio para arquivar 6 horas de um programa, mas difundir apenas os últimos 10 minutos. Para tal, tem de criar dois programas em execução em simultâneo. Um programa está definido para arquivar seis horas do evento, mas o programa não está publicado. O outro programa está definido para arquivar durante 10 minutos e este está publicado.

Não deve reutilizar programas existentes para novos eventos. Em vez disso, crie um novo programa para cada evento. Inicie o programa quando estiver pronto para começar a transmissão em fluxo e o arquivamento. Pare o programa sempre que pretender interromper a transmissão e arquivar o evento.

Para eliminar conteúdo arquivado, pare e elimine o programa e, em seguida, elimine o elemento associado. Não é possível eliminar um recurso, se utiliza um programa-lo. O programa deve ser eliminado primeiro.

Mesmo depois de parar e eliminar o programa, os utilizadores podem transmitir o seu conteúdo arquivado como um vídeo a pedido, até que elimine o elemento. Se pretender manter o conteúdo arquivado, mas não o tiver disponível para transmissão em fluxo, elimine o localizador de transmissão em fluxo.

## <a id="states"></a>Estados de canal e faturação
Os valores possíveis para o estado atual de um canal incluem:

* **Parado**: Este é o estado inicial do canal após a respetiva criação. Neste estado, as propriedades do canal podem ser atualizadas, mas não é permitida a transmissão em fluxo.
* **Iniciar**: O canal está a ser iniciado. Não são permitidas transmissões em fluxo nem atualizações durante este estado. Se ocorrer um erro, o canal devolve para o **parado** estado.
* **Executar**: O canal pode processar fluxos em direto.
* **Parar**: O canal está a ser parado. Não são permitidas transmissões em fluxo nem atualizações durante este estado.
* **Eliminar**: O canal está a ser eliminado. Não são permitidas transmissões em fluxo nem atualizações durante este estado.

A tabela seguinte mostra como o canal Estados mapa para o modo de faturação.

| Estado do canal | Portais indicadores de IU | Foi cobrado? |
| --- | --- | --- | --- |
| **A iniciar** |**A iniciar** |Não (estado transitório) |
| **Em execução** |**Pronto** (nenhum programas em execução)<p><p>ou<p>**Transmissão em fluxo** (pelo menos um programa em execução) |Sim |
| **A parar** |**A parar** |Não (estado transitório) |
| **Parado** |**Parado** |Não |

## <a id="cc_and_ads"></a>Fechado inserção captioning e ad
A tabela seguinte demonstra normas suportadas para inserção captioning e ad fechada.

| Standard | Notas |
| --- | --- |
| CEA 708 e EIA 608 (708/608) |CEA 708 EIA 608, sendo captioning fechado normas para os Estados Unidos e Canadá.<p><p>Atualmente, captioning só é suportada se executadas no fluxo de entrada codificado. Tem de utilizar um codificador de multimédia em direto que pode inserir legendas 608 ou 708 no fluxo codificado que é enviado aos Media Services. Media Services disponibilizam o conteúdo com inseridas legendas para os visualizadores autorizados. |
| TTML dentro .ismt (transmissão em fluxo uniforme controla de texto) |Empacotamento dinâmico de serviços de suporte de dados permite que os seus clientes transmitir o conteúdo em qualquer um dos seguintes formatos: DASH, HLS ou transmissão em fluxo uniforme. No entanto, se tiver de inserção MP4 fragmentado (transmissão em fluxo uniforme) com legendas dentro .ismt (transmissão em fluxo uniforme texto controla), pode fornecer o fluxo de transmissão em fluxo uniforme apenas a clientes. |
| SCTE 35 |SCTE 35 é um sistema signaling digital que é utilizado para rectângulos inserção de publicidade. A jusante recetores utilizam o sinal para splice publicidade em fluxo para o tempo atribuído. É necessário enviar SCTE 35 como um registo disperso no fluxo de entrada.<p><p>Atualmente, o fluxo de entrada suportado apenas formatar esse ativada sinais de ad está fragmentada MP4 (transmissão em fluxo uniforme). A única suportada saída formato também é a transmissão em fluxo uniforme. |

## <a id="considerations"></a>Considerações
Quando estiver a utilizar um codificador em direto no local para enviar uma sequência de transmissão múltipla a um canal, aplicam as seguintes restrições:

* Certifique-se de que tem uma ligação de Internet livre suficiente para enviar dados para os pontos de inserção.
* URL de inserção de uma secundária a utilizar requer mais largura de banda.
* Fluxo de entrada de transmissão múltipla pode ter um máximo de 10 níveis de qualidade do vídeo (camadas) e um máximo de 5 controla áudio.
* A maior velocidade de transmissão de média de qualquer um dos níveis de qualidade do vídeo deve ser inferior a 10 Mbps.
* A agregação de média de forma para todos os fluxos de áudio e vídeos deve ser abaixo 25 Mbps.
* Não é possível alterar o protocolo de entrada enquanto o canal ou os seus programas associados estão em execução. Se necessitar de protocolos diferentes, deve criar canais separados para cada protocolo de entrada.
* Pode inserir uma velocidade de transmissão única no seu canal. Mas, porque o canal não processar o fluxo, as aplicações de cliente também irão receber uma transmissão em fluxo. (Não recomendamos esta opção.)

Seguem-se outras considerações relacionadas com a trabalhar com canais e componentes relacionados:

* Sempre que reconfigurar o codificador em direto, chame o **repor** método no canal. Antes de repor o canal, terá de parar o programa. Depois de repor o canal, reinicie o programa.
* Um canal pode ser parado apenas quando está a ser o **executar** estado e todos os programas no canal foram parados.
* Por predefinição, pode adicionar apenas cinco canais à sua conta de Media Services. Para obter mais informações, consulte [Quotas e limitações](media-services-quotas-and-limitations.md).
* É-lhe faturado apenas quando o canal está no **executar** estado. Para obter mais informações, consulte o [canal Estados e faturação](media-services-live-streaming-with-onprem-encoders.md#states) secção.

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="feedback"></a>Comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Tópicos relacionados
[Codificadores recomendada no local](media-services-recommended-encoders.md)

[Especificação de inserção de Azure vida MP4 fragmentadas dos Media Services](media-services-fmp4-live-ingest-overview.md)

[Descrição geral dos Media Services do Azure e cenários comuns](media-services-overview.md)

[Conceitos de Media Services](media-services-concepts.md)

[live-overview]: ./media/media-services-manage-channels-overview/media-services-live-streaming-current.png
