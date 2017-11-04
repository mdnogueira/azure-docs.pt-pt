---
title: "Especificação de inserção de Media Services do Azure live MP4 fragmentados | Microsoft Docs"
description: "Esta especificação descreve o protocolo e o formato fragmentado com base em MP4 em direto transmissão em fluxo para ingestão de Media Services do Azure. Pode utilizar os Media Services do Azure para transmitir eventos em direto e difusão conteúdo em tempo real, utilizando o Azure como a plataforma de nuvem. Este documento descreve também as melhores práticas para a criação de elevada disponibilidade redundante robusta e responsiva direta mecanismos de inserção."
services: media-services
documentationcenter: 
author: cenkdin
manager: cfowler
editor: 
ms.assetid: 43fac263-a5ea-44af-8dd5-cc88e423b4de
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: cenkd;juliako
ms.openlocfilehash: 6250b73504bec765b8299060a29e84e771791cc9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-media-services-fragmented-mp4-live-ingest-specification"></a>Especificação de inserção de Media Services do Azure live MP4 fragmentados
Esta especificação descreve o protocolo e o formato fragmentado com base em MP4 em direto transmissão em fluxo para ingestão de Media Services do Azure. Os Media Services fornecem um serviço de transmissão em fluxo em direto que os clientes podem utilizar para sequência eventos em direto e difusão conteúdo em tempo real, utilizando o Azure como a plataforma de nuvem. Este documento descreve também as melhores práticas para a criação de elevada disponibilidade redundante robusta e responsiva direta mecanismos de inserção.

## <a name="1-conformance-notation"></a>1. Notação de conformidade
A chave ",""Não pode," "Necessária", "SHALL", "deverá não deve," "SHOULD", "Não deve," palavras "Recomendados", "Maio," e "Opcional" neste documento é devem ser interpretadas como estas são descritas na RFC 2119.

## <a name="2-service-diagram"></a>2. Diagrama de serviço
O diagrama seguinte mostra a arquitetura de alto nível de serviço de transmissão em fluxo em direto nos Media Services:

1. Um codificador em direto pushes feeds em direto para canais que são criados e aprovisionados através do SDK de Media Services do Azure.
2. Canais, programas e pontos finais de transmissão em fluxo nos Media Services com todas as em direto transmissão em fluxo funcionalidades, incluindo inserção, formatação, DVR, segurança, escalabilidade e redundância de nuvem.
3. Opcionalmente, podem escolher os clientes para implementar uma camada de rede de entrega de conteúdos do Azure entre o ponto final de transmissão em fluxo e os pontos finais de cliente.
4. Fluxo de pontos finais de cliente do ponto final da transmissão através de protocolos HTTP transmissão em fluxo adaptável. Os exemplos incluem Microsoft transmissão em fluxo uniforme, dinâmica transmissão em fluxo adaptável através de HTTP (DASH ou MPEG-DASH) e Apple HTTP Live Streaming (HLS).

![fluxo de inserção][image1]

## <a name="3-bitstream-format--iso-14496-12-fragmented-mp4"></a>3. Formato de Bitstream – ISO 14496-12 MP4 fragmentado
O formato wire para ingestão de transmissão em fluxo em direto abordado neste documento baseia-se no [ISO-14496-12]. Para obter uma explicação detalhada de fragmentados MP4 formato e extensões de ficheiros de vídeo a pedido e a ingestão de transmissão em fluxo em direto, consulte [[MS-SSTR]](http://msdn.microsoft.com/library/ff469518.aspx).

### <a name="live-ingest-format-definitions"></a>Definições do formato de inserção em direto
A lista seguinte descreve formato especial definições que se aplicam em direto ingerir no Media Services do Azure:

1. O **ftyp**, **em direto do manifesto caixa**, e **moov** caixas tem de ser enviadas com cada pedido (POST de HTTP). Estas caixas tem sejam enviadas no início da transmissão em fluxo e sempre que o codificador deve voltar a ligar ao retomar o fluxo de inserção. Para obter mais informações, consulte a secção 6 [1].
2. Secção 3.3.2 [1] Define uma caixa opcional denominada **StreamManifestBox** para direta de inserção. Devido à lógica encaminhamento do Balanceador de carga do Azure, utilizando esta caixa é preterido. A caixa não deve estar presente ao ingestão relacionadas para os serviços de suporte de dados. Se esta caixa está presente, os Media Services silenciosamente ignora-lo.
3. O **TrackFragmentExtendedHeaderBox** caixa definida no 3.2.3.2 no [1] tem de estar presente para que cada fragmento.
4. Versão 2 do **TrackFragmentExtendedHeaderBox** caixa deve ser utilizada para gerar os segmentos de suporte de dados que tenham URLs idênticos em vários centros de dados. O campo de índice de fragmento é REQUIRED para ativação pós-falha do Centro de dados dos formatos de transmissão em fluxo com base no índice, tais como a Apple HLS e MPEG-DASH baseada no índice. Para ativar a ativação pós-falha do Centro de dados em vários locais, o índice de fragmento deve ser sincronizado entre vários codificadores e ser aumentado em 1 para cada fragmento de suporte de dados sucessivas, mesmo em codificador reinícios ou falhas.
5. Secção 3.3.6 [1] Define uma caixa chamada **MovieFragmentRandomAccessBox** (**mfra**) que podem ser enviados no fim da ingestão em direto para indicar o fim do fluxo (EOS) para o canal. Devido à lógica da inserção dos Media Services, utilizar EOS foi preterido e o **mfra** caixa para ingestão em direto não deve ser enviada. Se enviada, os Media Services silenciosamente ignora-lo. Para repor o estado do ponto de inserção, recomendamos que utilize [canal repor](https://docs.microsoft.com/rest/api/media/operations/channel#reset_channels). Também recomendamos que utilize [programa parar](https://msdn.microsoft.com/library/azure/dn783463.aspx#stop_programs) para terminar uma apresentação e o fluxo.
6. A duração de fragmento MP4 deve ser constante, para reduzir o tamanho dos manifestos dos clientes. Uma constante MP4 heurística de transferência de cliente através da utilização de melhora também a duração de fragmento Repita etiquetas. A duração pode flutuam para compensar taxas de moldura de não-inteiros.
7. A duração de fragmento MP4 deve ter entre 2 e 6 aproximadamente segundos.
8. Carimbos e índices de fragmento MP4 (**TrackFragmentExtendedHeaderBox** `fragment_ absolute_ time` e `fragment_index`) deve chegam por ordem ascendente. Embora os Media Services é resiliente para fragmentos duplicados, limitou a capacidade para reordenar os fragmentos de acordo com a linha cronológica de suporte de dados.

## <a name="4-protocol-format--http"></a>4. Formato de protocolo – HTTP
ISO fragmentada baseado em MP4 em direto para serviços de suporte de dados utiliza um pedido de HTTP POST de execução longa padrão para transmitir dados codificado de suporte de dados que são empacotados no fragmentados MP4 formato para o serviço de inserção. Cada HTTP POST envia um concluída fragmentada MP4 bitstream ("transmissão"), a partir do início com caixas de cabeçalho (**ftyp**, **em direto do manifesto caixa**, e **moov** caixas ) e continuar com uma sequência de fragmentos (**moof** e **mdat** caixas). Para a sintaxe de URL para o pedido de HTTP POST, consulte a secção em 9.2 [1]. Um exemplo do POST URL é: 

    http://customer.channel.mediaservices.windows.net/ingest.isml/streams(720p)

### <a name="requirements"></a>Requisitos
Eis os requisitos de detalhado:

1. O codificador deve começar a difusão enviando um pedido de HTTP POST com um vazio "corpo" (comprimento zero conteúdo) utilizando o mesmo URL de ingestão. Isto pode ajudar o codificador rapidamente detetar se o ponto final de ingestão em direto é válido e se existem quaisquer autenticação ou por outras condições necessárias. Por protocolo HTTP, o servidor não é possível enviar novamente uma resposta HTTP até que o pedido completo, incluindo o corpo da mensagem é recebido. Dada a natureza de longa execução de um evento em direto, neste passo, o codificador poderá não conseguir detetar qualquer erro até concluir o envio de todos os dados.
2. O codificador deve processar erros ou desafios de autenticação devido a (1). Se (1) é concluída com êxito com uma resposta 200, continuar.
3. O codificador tem de iniciar um novo pedido de HTTP POST com o fluxo MP4 fragmentado. O payload tem de começar com as caixas de cabeçalho, seguidas de fragmentos. Tenha em atenção que o **ftyp**, **em direto do manifesto caixa**, e **moov** caixas (por esta ordem) é necessário enviar com cada pedido, mesmo que o codificador tem restabelecer a ligação porque anterior pedido foi terminado antes do fim da sequência. 
4. O codificador tem de utilizar fragmentada codificação de transferência para carregar, porque não é possível prever o comprimento do conteúdo completo do evento em direto.
5. Quando o evento é efetuada através de, depois de enviar o último fragmento, o codificador de lidar graciosamente tem de terminar a sequência de mensagem (a maioria das pilhas de cliente HTTP processam-la automaticamente) de codificação de transferência de partes. O codificador tem de aguardar que o serviço de devolverem o código de resposta final e, em seguida, termine a ligação. 
6. O codificador não pode utilizar o `Events()` substantivo conforme descrito em in 9.2 [1] para ingestão em direto para os serviços de suporte de dados.
7. Se o pedido de HTTP POST termina ou exceder o tempo limite com um erro TCP antes do final da transmissão em fluxo, o codificador tem emitir um novo pedido POST utilizando uma ligação nova e siga os requisitos de anteriores. Além disso, o codificador tem reenviar os anteriores dois fragmentos de MP4 para cada controlar no fluxo e retomar sem introduzir uma descontinuidade na linha cronológica de suporte de dados. Reenviar os dois últimos fragmentos MP4 para cada controlar garante que não há nenhuma perda de dados. Por outras palavras, se um fluxo contém um áudio e um registo de vídeo e falha o pedido POST atual, o codificador tem restabeleça a ligação e reenvie os dois últimos fragmentos para controlar áudio, que anteriormente foram enviados com êxito, e os dois últimos fragmentos para o vídeo monitorizar, a qual foram anteriormente enviada com êxito, para se certificar de que não há nenhuma perda de dados. O codificador tem de manter uma memória intermédia de "reencaminhamento" dos fragmentos de suporte de dados, que reenviar quando voltar a ser ligado.

## <a name="5-timescale"></a>5. escala temporal
[[MS-SSTR] ](https://msdn.microsoft.com/library/ff469518.aspx) descreve a utilização de escala temporal para **SmoothStreamingMedia** (secção 2.2.2.1), **StreamElement** (secção 2.2.2.3), **StreamFragmentElement**(Secção 2.2.2.6), e **LiveSMIL** (secção 2.2.7.3.1). Se o valor de escala temporal não estiver presente, utilizado o valor predefinido é 10,000,000 (10 MHz). Embora a especificação de formato de transmissão em fluxo uniforme não bloqueia a utilização de outros valores de escala temporal, a maioria das implementações de codificador utilizar esta predefinição valor (10 MHz) para gerar a transmissão em fluxo uniforme ingestão de dados. Devido ao [empacotamento dinâmico do suporte de dados do Azure](media-services-dynamic-packaging-overview.md) funcionalidade, recomendamos que utilize uma escala temporal 90 KHz para fluxos de vídeos e 44.1 KHz ou 48.1 KHz para fluxos de áudio. Se estiver a valores de escala temporal diferentes para diferentes fluxos, é necessário enviar a escala temporal ao nível do fluxo. Para obter mais informações, consulte [[MS-SSTR]](https://msdn.microsoft.com/library/ff469518.aspx).     

## <a name="6-definition-of-stream"></a>6. Definição de "transmissão"
Fluxo é a unidade básica de operação de ingestão em direto para composição apresentações em direto, processamento de transmissão em fluxo de ativação pós-falha e cenários de redundância. Fluxo está definido como um bitstream MP4 de exclusivo e fragmentado que pode conter um registo único ou vários controla. Uma apresentação completa em direto pode conter um ou mais fluxos, dependendo da configuração dos codificadores em direto. Os exemplos seguintes mostram as várias opções de utilização fluxos para compor uma apresentação completa em direto.

**Exemplo:** 

Um cliente pretende criar uma apresentação de transmissão em fluxo em direto que inclui a forma de áudio/vídeo seguinte:

Vídeo – 3000 kbps, 1500 kbps, 750 kbps

Áudio – 128 kbps

### <a name="option-1-all-tracks-in-one-stream"></a>Opção 1: Controla todos os no um fluxo
Esta opção, um codificador único gera todos os controla de áudio/vídeo e, em seguida, bundles-los para um bitstream MP4 fragmentado. O bitstream MP4 fragmentado, em seguida, é enviado através de uma ligação de HTTP POST único. Neste exemplo, não há apenas um fluxo para esta apresentação em direto.

![Controlar fluxos para um][image2]

### <a name="option-2-each-track-in-a-separate-stream"></a>Opção 2: Cada controlar numa transmissão em fluxo separada
Nesta opção, o codificador coloca um controlar para cada bitstream fragmento MP4 e, em seguida, envia todos os fluxos do através de ligações de HTTP separadas. Isto pode ser feito um codificador ou com vários codificadores. A ingestão em direto vê esta apresentação em direto como composto de quatro fluxos.

![Fluxos separado controla][image3]

### <a name="option-3-bundle-audio-track-with-the-lowest-bitrate-video-track-into-one-stream"></a>Opção 3: Agrupar áudio controlar com o menor controlar de vídeo de velocidade de transmissão em uma fluxo
Esta opção, o cliente escolhe agrupar a controlar áudio com a controlar de vídeo mais baixo de velocidade de transmissão num fragmento MP4 bitstream e deixe as outras controla vídeo dois fluxos separados como. 

![Controla fluxos de áudio e vídeo][image4]

### <a name="summary"></a>Resumo
Não se trata de uma lista exaustiva de todas as opções de ingestão possíveis para este exemplo. Como um matter de verdade, qualquer agrupamento de faixas em fluxos é suportado pelo ingestão em direto. Os clientes e os fornecedores de codificador podem escolher as seus próprios implementações com base na complexidade de engenharia, a capacidade de codificador e redundância e considerações de ativação pós-falha. No entanto, na maioria dos casos, há controlar áudio apenas um para a apresentação em direto completa. Por isso, é importante garantir que o healthiness do fluxo de inserção que contém a controlar de áudio. Muitas vezes, esta consideração resulta numa colocando a controlar áudio no seu próprio fluxo (tal como no opção 2) ou agrupamento-lo com a controlar de vídeo mais baixo de velocidade de transmissão (tal como no opção 3). Além disso, para melhor redundância e tolerância a falhas, enviar o mesmo controlar de áudio em dois fluxos diferentes (opção 2 com redundante controla áudio) ou agrupamento de áudio controlar com, pelo menos, dois controla de vídeo a mais baixa velocidade de transmissão (opção 3 com áudio incluído em, pelo menos, duas fluxos de vídeos) é vivamente recomendável para direta de inserção para os serviços de suporte de dados.

## <a name="7-service-failover"></a>7. Ativação pós-falha do serviço
Fornecido a natureza transmissão em fluxo em direto, suporte de ativação pós-falha boa é essencial para garantir a disponibilidade do serviço. Serviços de suporte de dados foi concebido para processar vários tipos de falhas, incluindo erros de rede, o servidor erros e problemas de armazenamento. Quando utilizado em conjunto com a lógica de ativação pós-falha adequadas no lado do codificador em direto, os clientes podem alcançar um serviço de transmissão em fluxo em direto altamente fiável da nuvem.

Nesta secção, vamos abordar cenários de ativação pós-falha do serviço. Neste caso, a falha ocorre algures no âmbito do serviço e manifestos si próprio como um erro de rede. Seguem-se algumas recomendações para a implementação de codificador para processamento de ativação pós-falha do serviço:

1. Utilize um tempo limite de 10 segundo para estabelecer a ligação de TCP. Se uma tentativa para estabelecer a ligação demora mais de 10 segundos, abortar a operação e tente novamente. 
2. Utilize o limite de tempo curto para enviar o pedido HTTP segmentos de mensagem. Se a duração de fragmento do destino MP4 N segundos, utilize um tempo limite de envio entre N e 2 N segundos; Por exemplo, se a duração de fragmento MP4 segundos 6, utilize um tempo limite de 6 a 12 segundos. Se ocorrer um tempo limite, repor a ligação, abra uma nova ligação e retomar o fluxo de inserção na ligação de novo. 
3. Manter uma memória intermédia graduais que tenha os dois últimos fragmentos para cada controlar que foram com êxito e completamente enviados para o serviço.  Se o pedido de HTTP POST para um fluxo é encerrado ou exceder o tempo limite antes o fim do fluxo de abrir uma nova ligação e começar a outro pedido de HTTP POST, reenviar os cabeçalhos de fluxo, reenviar os dois últimos fragmentos para cada controlar e retomar o fluxo sem introduzir um d iscontinuity na linha cronológica de suporte de dados. Isto reduz a possibilidade de perda de dados.
4. Recomendamos que o codificador não limita o número de tentativas para estabelecer uma ligação ou retomar a transmissão em fluxo depois de ocorre um erro de TCP.
5. Após um erro TCP:
  
    a. A ligação atual tem de ser fechada e tem de ser criada uma nova ligação para um novo pedido de HTTP POST.

    b. O novo HTTP POST URL tem de ser o mesmo que o URL de POST inicial.
  
    c. O novo HTTP POST tem de incluir cabeçalhos de fluxo (**ftyp**, **em direto do manifesto caixa**, e **moov** caixas) que são idênticos para os cabeçalhos de fluxo de POST inicial.
  
    d. Tem de ser reenviar os dois últimos fragmentos enviados para cada controlar e de transmissão em fluxo tem retomar sem introduzir uma descontinuidade na linha cronológica de suporte de dados. Os carimbos de fragmento MP4 devem aumentar continuamente, mesmo em pedidos de HTTP POST.
6. O codificador deve terminar o pedido POST de HTTP, se os dados não estão a ser enviados uma taxa proporcionais a duração de fragmento MP4.  Um pedido POST de HTTP que envia dados pode impedir que os Media Services rapidamente desligar do codificador em caso de uma atualização de serviço. Por este motivo, o HTTP POST para dispersos (sinal ad) controla deve ser curta duração, logo que é enviado o fragmento disperso a terminar.

## <a name="8-encoder-failover"></a>8. ativação pós-falha do codificador
Ativação pós-falha do codificador é o segundo tipo de cenário de ativação pós-falha que tem de ser abordadas para entrega de transmissão em fluxo em direto ponto-a-ponto. Neste cenário, a condição de erro ocorre no lado do codificador. 

![ativação pós-falha do codificador][image5]

As expectativas seguintes aplicam-se do ponto final ingestão em direto quando ocorre a ativação pós-falha de codificador:

1. DEVE ser criada uma nova instância de codificador para continuar a transmissão em fluxo, conforme ilustrado no diagrama (sequência para 3000k vídeo, com linha tracejada).
2. O codificador novo tem de utilizar o mesmo URL para pedidos de HTTP POST que a instância de falha.
3. Pedido POST o codificador novo tem de incluir as mesmas caixas de cabeçalho de MP4 fragmentadas que a instância de falha.
4. O codificador novo tem sincronizado corretamente com todos os outros codificadores em execução para a mesma apresentação em direto para gerar amostras de áudio/vídeo sincronizadas com os limites de fragmento alinhado.
5. O novo fluxo tem de ser semanticamente equivalente com o fluxo anterior e permutáveis níveis do cabeçalho e fragmento.
6. O codificador novo deve tentar minimizar a perda de dados. O `fragment_absolute_time` e `fragment_index` de suportes de dados fragmentos devem aumentar a partir do ponto onde o codificador última paragem. O `fragment_absolute_time` e `fragment_index` deve aumentar de forma contínua, mas é permitido para introduzir uma descontinuidade, se necessário. Os Media Services ignora fragmentos que já tenha recebido e processado, pelo que é melhor err ao lado dos reenviar fragmentos que ao introduzir discontinuities na linha cronológica de suporte de dados. 

## <a name="9-encoder-redundancy"></a>9. redundância do codificador
Para determinados eventos críticos em direto que a pedido mesmo maior disponibilidade e qualidade da experiência, recomendamos que utilize codificadores ativo-ativo para alcançar a ativação pós-falha totalmente integrada sem perda de dados.

![redundância do codificador][image6]

Conforme ilustrado neste diagrama, dois grupos de codificadores push duas cópias de cada fluxo em simultâneo a no serviço em direto. Esta configuração é suportada porque os serviços de suporte de dados pode filtrar fragmentos duplicados com base no fluxo ID e fragmento timestamp. O fluxo em direto resultante e de arquivo é uma única cópia de todos os fluxos que é a melhor agregação possíveis de duas origens. Por exemplo, no caso de Alpine hipotético, desde que não há um codificador (não tem de ser o mesmo) em execução em qualquer momento de determinado no tempo para cada fluxo, o fluxo em direto resultante do serviço é contínuo sem perda de dados. 

Os requisitos para este cenário são quase os mesmos que os requisitos no caso de "Codificador ativação pós-falha", com a exceção que estejam a executar o segundo conjunto de codificadores em simultâneo, como os codificadores primários.

## <a name="10-service-redundancy"></a>10. redundância de serviço
Para elevada disponibilidade redundante distribuição global, por vezes, tem de ter por várias regiões a cópia de segurança para processar perante desastres regionais. Os clientes de expansão na topologia "Redundância de codificador", podem optar por ter uma implementação de serviço redundante numa região diferente da que está ligada ao segundo conjunto de codificadores. Os clientes também podem trabalhar com um fornecedor de rede de entrega de conteúdos para implementar um Gestor de tráfego Global à frente as implementações de dois serviços perfeitamente encaminhar o tráfego de cliente. Os requisitos para os codificadores são os mesmos que as maiúsculas e minúsculas de "Redundância de codificador". A única exceção é que o segundo conjunto de codificadores tem de ser indicada em direto para outro ponto final de inserção. O diagrama seguinte mostra esta configuração:

![redundância de serviço][image7]

## <a name="11-special-types-of-ingestion-formats"></a>11. Tipos especiais de formatos de ingestão
Esta secção descreve os tipos especiais de formatos de ingestão em direto que foram concebidos para processar cenários específicos.

### <a name="sparse-track"></a>Controlar disperso
Quando distribui uma apresentação de transmissão em fluxo em direto com uma experiência de cliente avançado, muitas vezes, é necessário para transmitir eventos sincronizados com o tempo ou sinalizar em banda com os dados de suporte de dados principal. Um exemplo desta situação é inserção dinâmica ad em direto. Este tipo de evento sinalização é diferente do regular de áudio/vídeo de transmissão em fluxo devido ao respetiva natureza dispersa. Por outras palavras, os dados signaling normalmente não acontecer continuamente e o intervalo pode ser difícil de prever. O conceito de controlar disperso foi concebido para ingestão de difusão de dados de sinalização na banda.

Os seguintes passos são uma implementação recomendada para ingestão relacionadas dispersa controlar:

1. Crie um MP4 bitstream separado fragmentado que contém apenas dispersa controla sem controla de áudio/vídeo.
2. No **em direto do manifesto caixa** tal como definido na secção 6 no [1], utilize o *parentTrackName* parâmetro para especificar o nome de controlar o principal. Para obter mais informações, consulte a secção 4.2.1.2.1.2 [1].
3. No **em direto do manifesto caixa**, **manifestOutput** deve ser definido como **verdadeiro**.
4. Tendo em conta a natureza dispersa do evento signaling, recomendamos o seguinte:
   
    a. No início do evento em direto, o codificador envia as caixas de cabeçalho inicial para o serviço, que permite que o serviço registar o controlar dispersa no manifesto de cliente.
   
    b. O codificador deve terminar o pedido POST de HTTP quando não estão a ser enviados dados. Um HTTP POST de longa execução que envia dados pode impedir que os Media Services rapidamente desligar do codificador em caso de um reinício de servidor ou de atualização de serviço. Nestes casos, o servidor de suporte de dados está temporariamente bloqueado numa operação de recepção no socket.
   
    c. Durante o período de tempo quando não estão disponíveis dados de sinalização, feche o codificador SHOULD POST de HTTP do pedido. Enquanto o pedido POST está ativo, o codificador deve enviar dados.

    d. Quando enviar fragmentos dispersos, o codificador pode definir um cabeçalho content-length explícito, se estiver disponível.

    e. Quando enviar dispersos fragmentos com uma ligação nova, o codificador deve começar a enviar das caixas de cabeçalho, seguidas de fragmentos de novo. Trata-se nos casos em que ativação pós-falha ocorre entre e está a ser estabelecida à nova ligação dispersa para um novo servidor que não tem visto a controlar dispersa antes.

    f. O fragmento de controlar dispersa fica disponível para o cliente quando o fragmento de controlar principal correspondente que tem um valor igual ou superior timestamp é disponibilizado para o cliente. Por exemplo, se o fragmento disperso tem um carimbo de t = 1000, é esperado que depois do cliente vê timestamp 1000 de fragmento "vídeo" (assumindo que o nome de controlar principal é "vídeo") ou beyond, pode transferir o fragmento dispersa t = 1000. Tenha em atenção que o sinal real poderiam ser utilizado para uma posição diferente na linha cronológica de apresentação para o objetivo designado. Neste exemplo, é possível que o fragmento disperso de t = 1000 tem um payload XML, que se destine a inserir um anúncio numa posição de alguns segundos mais tarde.

    g. O payload dos fragmentos de controlar dispersa pode estar em diferentes formatos (por exemplo, XML, texto ou binário), dependendo do cenário.

### <a name="redundant-audio-track"></a>Controlar áudio redundante
Um HTTP adaptável transmissão em fluxo cenário típico (por exemplo, transmissão em fluxo uniforme ou DASH), muitas vezes, não existe é apenas um controlar áudio na apresentação de toda. Ao contrário do vídeo controla, que tem vários níveis de qualidade de cliente à sua escolha nas condições de erro, a controlar áudio pode ser um ponto único de falha se a ingestão da transmissão em fluxo que contém a controlar áudio está danificada. 

Para resolver este problema, os Media Services suportam ingestão em direto de faixas áudio redundantes. A ideia é que o mesmo controlar áudio pode ser enviada várias vezes no vários fluxos. Embora o serviço apenas regista a controlar áudio uma vez no manifesto de cliente, pode utilizar redundante controla áudio como as cópias de segurança para obter os fragmentos de áudio, se o principal controlar áudio tem problemas. Para a ingestão de faixas áudio redundantes, o codificador precisa de:

1. Crie a controlar de áudio mesmo fragmento vários MP4 bitstreams. A atividade de áudio redundante tem de ter semanticamente equivalentes, com os mesmo carimbos de fragmento e ser permutáveis níveis do cabeçalho e fragmento.
2. Certifique-se de que a entrada "áudio" o em direto do manifesto (secção 6 no [1]) é igual para todos os controla áudio redundante.

Para redundante controla áudio, recomenda-se a implementação seguinte:

1. Envie cada controlar áudio exclusivo numa transmissão em fluxo por si só. Além disso, enviar uma transmissão em fluxo redundante para cada um destes fluxos de áudio controlar, onde o fluxo de segundo difere de acordo com o primeiro apenas pelo identificador no HTTP POST URL: {protocolo} :// {endereço do servidor} / {publicação ponto path}/Streams({identifier}).
2. Utilize fluxos separados para enviar as dois de forma de vídeo mais baixa. Cada um destes fluxos deve conter também uma cópia de cada track áudio exclusivo. Por exemplo, quando são suportadas várias linguagens, estes fluxos devem conter controla áudio para cada idioma.
3. Utilize as instâncias de servidor separado (codificador) para codificar e enviar os fluxos redundantes mencionados (1) e (2). 

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

[image1]: ./media/media-services-fmp4-live-ingest-overview/media-services-image1.png
[image2]: ./media/media-services-fmp4-live-ingest-overview/media-services-image2.png
[image3]: ./media/media-services-fmp4-live-ingest-overview/media-services-image3.png
[image4]: ./media/media-services-fmp4-live-ingest-overview/media-services-image4.png
[image5]: ./media/media-services-fmp4-live-ingest-overview/media-services-image5.png
[image6]: ./media/media-services-fmp4-live-ingest-overview/media-services-image6.png
[image7]: ./media/media-services-fmp4-live-ingest-overview/media-services-image7.png
