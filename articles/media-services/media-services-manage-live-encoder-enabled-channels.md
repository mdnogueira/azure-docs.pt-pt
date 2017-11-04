---
title: "Em direto de transmissão em fluxo utilizando os Media Services do Azure para criar fluxos de transmissão múltipla | Microsoft Docs"
description: "Este tópico descreve como configurar um canal que recebe uma transmissão em fluxo em direto de um codificador no local e, em seguida, realiza live encoding fluxo de velocidade de transmissão adaptável com os Media Services. O fluxo pode, em seguida, entregar para aplicações de reprodução de cliente através de um ou mais de transmissão em fluxo pontos finais, utilizando um dos seguintes protocolos de transmissão em fluxo adaptável: HLS, transmissão em fluxo uniforme, MPEG DASH."
services: media-services
documentationcenter: 
author: anilmur
manager: cfowler
editor: 
ms.assetid: 30ce6556-b0ff-46d8-a15d-5f10e4c360e2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: juliako;anilmur
ms.openlocfilehash: d5f76d532b236e67a4e69eb820e2cfc3033a80c6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="live-streaming-using-azure-media-services-to-create-multi-bitrate-streams"></a>Transmissão em fluxo em direto utilizando os Serviços de Multimédia do Azure para criar transmissões com velocidade de transmissão múltipla
## <a name="overview"></a>Descrição geral
Nos serviços de suporte de dados do Azure (AMS), um **canal** representa um pipeline de processamento de conteúdos de transmissão em fluxo em direto. A **canal** recebe fluxos de entrada em direto de uma das seguintes formas:

* Um codificador em direto no local envia uma transmissão fluxo de velocidade única para o Canal ativado para realizar live encoding com os Media Services num dos seguintes formatos: RTP (MPEG-TS), RTMP ou Transmissão em Fluxo Uniforme (MP4 fragmentado). O Canal, em seguida, realiza live encoding da transmissão em fluxo de velocidade de transmissão única de entrada para uma transmissão em fluxo de vídeo com várias velocidades (adaptável). Quando solicitado, os Media Services disponibilizam a transmissão em fluxo para os clientes.
* Um codificador em direto no local envia uma múltipla **RTMP** ou **transmissão em fluxo uniforme** (MP4 fragmentado) para o canal não está ativado para realizar live encoding com AMS. As transmissões em fluxo pass-through **canal**s sem qualquer processamento adicional. Este método é denominado **pass-through**. Pode utilizar os seguintes codificadores em direto com velocidade de múltipla transmissão em fluxo uniforme de saída: MediaExcel, Ateme, Imagine comunicações, Envivio, Cisco e elementar. Os seguintes codificadores em direto de saída RTMP: codificadores Adobe Flash suporte de dados em direto codificador (FMLE), Telestream Wirecast, Haivision, Teradek e Transcodificadores.  Um codificador em direto pode também enviar uma transmissão em fluxo de velocidade de transmissão única para um canal, que não está ativado para live encoding, mas tal não é recomendado. Quando solicitado, os Media Services disponibilizam a transmissão em fluxo para os clientes.
  
  > [!NOTE]
  > Utilizar um método pass-through é a forma mais económica TTL transmissão em fluxo.
  > 
  > 

A partir da versão 2.10 de serviços de suporte de dados, quando criar um canal, pode especificar de que forma pretende que para o canal receber o fluxo de entrada e se pretende ou não quiser para o canal realizar live encoding da sua transmissão em fluxo. Tem duas opções:

* **Nenhum** – especificar este valor, se planeia utilizar um codificador em direto no local, que será de saída do fluxo de transmissão múltipla (uma sequência de pass-through). Neste caso, o fluxo de entrada passem a saída sem qualquer tipo de codificação. Este é o comportamento de um canal antes da versão 2.10.  Para obter mais informações sobre como trabalhar com canais deste tipo, consulte [transmissão em fluxo em direto com codificadores no local que criar fluxos de transmissão múltipla](media-services-live-streaming-with-onprem-encoders.md).
* **Standard** – escolha este valor, se pretender utilizar os Media Services para codificar a sua transmissão em fluxo em direto para o fluxo de transmissão múltipla velocidade de transmissão única. Lembre-se de que existe um impacto de faturação para live encoding e deve Lembre-se de que sai de um canal de codificação em direto no estado "Em execução" resultará em encargos faturação.  Recomenda-se que pare os canais executar imediatamente depois de concluída a evitar custos extra por hora do evento de transmissão em fluxo em direto.

> [!NOTE]
> Este tópico descreve os atributos de canais que estão ativados para realizar live encoding (**padrão** tipo de codificação). Para obter informações sobre como trabalhar com canais que não estão ativados para realizar live encoding, consulte [transmissão em fluxo em direto com codificadores no local que criar fluxos de transmissão múltipla](media-services-live-streaming-with-onprem-encoders.md).
> 
> Certifique-se de que revê o [considerações](media-services-manage-live-encoder-enabled-channels.md#Considerations) secção.
> 
> 

## <a name="billing-implications"></a>Implicações de faturação
Um canal de codificação em direto é iniciada assim que é transições de estado para "Em execução" através da API de faturação.   Também pode ver o estado no portal do Azure, ou na ferramenta do Explorador de serviços de suporte de dados do Azure (http://aka.ms/amse).

A tabela seguinte mostra como os Estados de canal mapeiam Estados de faturação no portal do Azure e API. Tenha em atenção que os Estados são ligeiramente diferentes entre a API e Portal UX. Assim que um canal está no estado "Em execução" através da API ou, no estado "Pronto" ou "Transmissão em fluxo" no portal do Azure, faturação estará ativa.
Para parar o canal de faturação mais, terá de parar o canal através da API ou no portal do Azure.
É responsável por parar os canais quando tiver terminado com o canal de codificação em direto.  Falha ao parar um canal de codificação resultará na faturação contínua.

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

### <a name="automatic-shut-off-for-unused-channels"></a>Encerrar-desativar automático para canais não utilizados
Começando com 25 de Janeiro de 2016, os Media Services implementado uma atualização que automaticamente para um canal (com em direto codificação ativado) depois de que tem estado em execução num Estado não utilizado durante um longo período. Isto aplica-se para canais que tenham não existem programas de Active Directory e que não recebeu uma entrada contribuição feed por um longo período de tempo.

O limiar para um período de não utilizado é nominally 12 horas, mas está sujeita a alterações.

## <a name="live-encoding-workflow"></a>Fluxo de trabalho de codificação em direto
O diagrama seguinte representa um fluxo de trabalho de transmissão em fluxo em direto onde um canal recebe uma transmissão em fluxo dos seguintes protocolos: RTMP, transmissão em fluxo uniforme ou RTP (MPEG-TS); -lo, em seguida, codifica o fluxo de um fluxo de transmissão múltipla. 

![Fluxo de trabalho em direto][live-overview]

## <a id="scenario"></a>Cenário comum de transmissão em fluxo em direto
Os seguintes são passos gerais referentes à criação de aplicações comuns de transmissão em fluxo em direto.

> [!NOTE]
> Atualmente, a duração máxima recomendada de um evento em direto é de 8 horas. Contacte amslived através de Microsoft.com se tiver de executar um canal durante períodos de tempo mais longos. Lembre-se de que existe um impacto de faturação para live encoding e deve Lembre-se de que a sair de um canal de codificação em direto no estado "Em execução" resultará em encargos faturação por hora.  Recomenda-se que pare os canais executar imediatamente depois de concluída a evitar custos extra por hora do evento de transmissão em fluxo em direto. 
> 
> 

1. Ligue uma câmara de vídeo a um computador. Inicie e configure um codificador em direto no local que pode apresentar um **único** transmissão em fluxo dos seguintes protocolos: RTMP, transmissão em fluxo uniforme ou RTP (MPEG-TS). 
   
    Este passo também pode ser realizado depois de criar o Canal.
2. Crie e inicie um Canal. 
3. Obtenha o URL de inserção do Canal. 
   
    O URL de inserção é utilizado pelo codificador em direto para enviar a transmissão para o Canal.
4. Obtenha o URL de pré-visualização do Canal. 
   
    Utilize este URL para verificar se o canal está a receber corretamente a transmissão em fluxo em direto.
5. Crie um programa. 
   
    Quando utilizar o portal do Azure, criar um programa também cria um recurso. 
   
    Ao utilizar o .NET SDK ou REST terá de criar um recurso e especificar para utilizar este recurso ao criar um programa. 
6. Publique o elemento associado ao programa.   
   
    >[!NOTE]
    >Quando a sua conta AMS é criada, é adicionado um ponto final de transmissão em fluxo **predefinido** à sua conta no estado **Parado**. O ponto final de transmissão em fluxo a partir do qual quer transmitir conteúdo tem de estar no estado **Em execução**. 
    
7. Inicie o programa quando estiver pronto para começar a transmissão em fluxo e o arquivamento.
8. Opcionalmente, o codificador em direto pode ser indicado para iniciar um anúncio. O anúncio é inserido na transmissão de saída.
9. Pare o programa sempre que pretender interromper a transmissão e arquivar o evento.
10. Elimine o Programa (e, opcionalmente, elimine o elemento).   

> [!NOTE]
> É muito importante não se esqueça de parar um canal de codificação em direto. Lembre-se de que existe uma hora a hora impacto para live encoding de faturação e deve Lembre-se de que sai de um canal de codificação em direto no estado "Em execução" resultará em encargos faturação.  Recomenda-se que pare os canais executar imediatamente depois de concluída a evitar custos extra por hora do evento de transmissão em fluxo em direto. 
> 
> 

## <a id="channel"></a>Canal de entrada (inserção) configurações
### <a id="Ingest_Protocols"></a>Transmissão em fluxo de protocolo de inserção
Se o **tipo de codificador** está definido como **padrão**, as opções válidas são:

* **RTP** (MPEG-TS): o fluxo de transporte de MPEG-2 sobre RTP.  
* Velocidade de transmissão única **RTMP**
* Velocidade de transmissão única **MP4 fragmentado** (transmissão em fluxo uniforme)

#### <a name="rtp-mpeg-ts---mpeg-2-transport-stream-over-rtp"></a>RTP (MPEG-TS) - sequência de transporte de MPEG-2 sobre RTP.
Caso de utilização típica: 

Professional broadcasters normalmente trabalham com codificadores em direto de ponta no local dos fornecedores, como as tecnologias Elemental, Ericsson, Ateme, Imagine ou Envivio para enviar uma transmissão em fluxo. Muitas vezes, é utilizado em conjunto com um departamento de TI e redes privadas.

Considerações:

* Recomenda-se vivamente a utilização de um fluxo de transporte de programa único (SPTS) de entrada. 
* Pode introduzir até 8 fluxos de áudio utilizando TS MPEG-2 sobre RTP. 
* O fluxo de vídeo deve ter uma velocidade de transmissão média abaixo 15 Mbps
* A velocidade de transmissão média agregada dos fluxos de áudio deve ser inferior a 1 Mbps
* Seguem-se codecs suportados:
  
  * MPEG-2 / H.262 vídeo 
    
    * Perfil principal (4:2:0)
    * Perfil de alta (4:2:0, 4:2:2)
    * Perfil 422 (4:2:0, 4:2:2)
  * AVC MPEG-4 / 264 vídeo  
    
    * Linha de base, principal, perfil de alta (4 de 8 bits: 2:0)
    * Perfil de 10 elevada (10 bits 4:2:0)
    * Perfil 422 elevada (10 bits 4:2:2)
  * Áudio MPEG-2 AAC-LC 
    
    * Mono, Stereo, Surround (5.1, 7.1)
    * Empacotamento ADTS de estilo de MPEG-2
  * Áudio do Dolby Digital (AC-3) 
    
    * Mono, Stereo, Surround (5.1, 7.1)
  * Áudio MPEG (camada II e III) 
    
    * Mono, Stereo
* Difusão recomendada codificadores incluem:
  
  * Imagine comunicações Selenio ENC 1
  * Imagine comunicações Selenio ENC 2
  * Elementar em direto

#### <a id="single_bitrate_RTMP"></a>RTMP de velocidade de transmissão única
Considerações:

* Fluxo de entrada não pode conter as vídeo de transmissão múltipla
* O fluxo de vídeo deve ter uma velocidade de transmissão média abaixo 15 Mbps
* A sequência de áudio deve ter uma velocidade de transmissão média abaixo 1 Mbps
* Seguem-se codecs suportados:
* AVC MPEG-4 / 264 vídeo
* Linha de base, principal, perfil de alta (4 de 8 bits: 2:0)
* Perfil de 10 elevada (10 bits 4:2:0)
* Perfil 422 elevada (10 bits 4:2:2)
* Áudio MPEG-2 AAC-LC
* Mono, Stereo, Surround (5.1, 7.1)
* frequência de amostragem 44.1 kHz
* Empacotamento ADTS de estilo de MPEG-2
* Recomendada codificadores incluem:
* Telestream Wirecast
* Codificador em direto Flash suporte de dados

#### <a name="single-bitrate-fragmented-mp4-smooth-streaming"></a>MP4 fragmentado de velocidade de transmissão única (Transmissão em Fluxo Uniforme)
Caso de utilização típica:

Utilizar no local codificadores em direto, de fornecedores Elemental tecnologias, Ericsson, Ateme, Envivio para enviar o fluxo de entrada através da internet aberto para um Azure que Centro de dados.

Considerações:

Mesmo que para [RTMP de velocidade de transmissão única](media-services-manage-live-encoder-enabled-channels.md#single_bitrate_RTMP).

#### <a name="other-considerations"></a>Outras considerações
* Não é possível alterar o protocolo de entrada enquanto o Canal ou os seus programas associados estiverem em execução. Se necessitar de protocolos diferentes, deve criar canais separados para cada protocolo de entrada.
* Resolução máxima de fluxo de vídeo de entrada é de 1920 x 1080 e no máximo 60 campos por segundo se entrelaçadas ou 30 fotogramas por segundo se transferência progressiva.

### <a name="ingest-urls-endpoints"></a>Ingestão de URLs (pontos finais)
Um canal fornece um ponto final de entrada (URL de inserção) que especificou no codificador em direto, pelo que pode impulsionar o codificador fluxos os canais.

Pode obter os URLs de inserção depois de criar um canal. Para obter estes URLs, o canal não tem de ser o **executar** estado. Quando estiver pronto para começar a enviar por push dados para o canal, tem de ter o **executar** estado. Assim que for iniciada o canal a ingestão de dados, pode pré-visualizar a transmissão através do URL de pré-visualização.

Tem uma opção de ingestão relacionadas MP4 fragmentado (transmissão em fluxo uniforme) em fluxo em direto através de uma ligação SSL. Para a ingestão através de SSL, certifique-se atualizar o URL de inserção para HTTPS. Tenha em atenção que, atualmente, AMS não suporta SSL com domínios personalizados.  

### <a name="allowed-ip-addresses"></a>Endereços IP permitidos
Pode definir os endereços IP que estão autorizados a publicar vídeo neste canal. Os endereços IP permitidos podem ser especificados como um endereço IP único (por exemplo,  "10.0.0.1"), um intervalo IP com um endereço IP e uma máscara sub-rede CIDR (por exemplo "10.0.0.1/22") ou um intervalo IP com um endereço IP e uma máscara sub-rede de ponto decimal (por exemplo, '10.0.0.1(255.255.252.0)').

Se não for especificado qualquer endereço IP e não existir nenhuma definição de regra, então, não será permitido qualquer endereço IP. Para permitir um endereço IP, crie uma regra e defina 0.0.0.0/0.

## <a name="channel-preview"></a>Pré-visualização do canal
### <a name="preview-urls"></a>URLs de pré-visualização
Canais de fornecem um ponto final de pré-visualização (URL de pré-visualização) que utilizar para pré-visualizar e validar a sua transmissão em fluxo antes do processamento adicional e entrega.

Pode obter o URL de pré-visualização quando criar o canal. Para obter o URL, o canal não tem de ser o **executar** estado.

Assim que for iniciada o canal a ingestão de dados, pode pré-visualizar a transmissão.

> [!NOTE]
> Atualmente o fluxo de pré-visualização só pode ser fornecido no MP4 fragmentado formato (transmissão em fluxo uniforme) independentemente do tipo de entrada especificado. Pode utilizar o [http://smf.cloudapp.net/healthmonitor](http://smf.cloudapp.net/healthmonitor) leitor para testar a transmissão em fluxo uniforme. Também pode utilizar um leitor alojado no portal do Azure para ver a sua transmissão em fluxo.
> 
> 

### <a name="allowed-ip-addresses"></a>Endereços IP permitidos
Pode definir os endereços IP que estão autorizados a ligar ao ponto final de pré-visualização. Se não houver endereços IP são especificados qualquer endereço IP será permitido. Os endereços IP permitidos podem ser especificados como um endereço IP único (por exemplo,  "10.0.0.1"), um intervalo IP com um endereço IP e uma máscara sub-rede CIDR (por exemplo "10.0.0.1/22") ou um intervalo IP com um endereço IP e uma máscara sub-rede de ponto decimal (por exemplo, "10.0.0.1(255.255.252.0)").

## <a name="live-encoding-settings"></a>Em direto as definições de codificação
Esta secção descreve a forma como as definições para o codificador em direto no canal podem ser ajustadas, quando o **tipo de codificação** de um canal está definido como **padrão**.

> [!NOTE]
> Quando inputting vários idiomas controla e fazer live encoding com o Azure, RTP só é suportada para a entrada multilingues. Pode definir até 8 fluxos de áudio utilizando TS MPEG-2 sobre RTP. Ingestão relacionadas vários controla áudio com RTMP ou transmissão em fluxo uniforme não é atualmente suportada. Ao fazer live encoding com [em direto no local codifica](media-services-live-streaming-with-onprem-encoders.md), não há nenhum desse limitação porque que é enviado para AMS passa através de um canal sem qualquer processamento adicional.
> 
> 

### <a name="ad-marker-source"></a>Origem de marcador do AD
Pode especificar a origem dos sinais de marcadores ad. Valor predefinido é **Api**, que indica que o codificador em direto no canal deve escutar para um assíncrona **API de marcador do Ad**.

A opção de válido **Scte35** (permitido apenas se o protocolo de transmissão em fluxo de inserção estiver definido como RTP (MPEG-TS). Quando Scte35 for especificado, o codificador em direto irá analisar SCTE 35 sinais de fluxo de entrada RTP (MPEG-TS).

### <a name="cea-708-closed-captions"></a>CEA 708 fechado legendas
Um sinalizador opcional que indica o codificador em direto para ignorar quaisquer dados de legendas CEA 708 incorporado as vídeo de entrada. Quando o sinalizador está definido como false (predefinição), o codificador irá detetar e reintroduza CEA 708 dados para os fluxos de vídeo de saída.

### <a name="video-stream"></a>Fluxo de vídeo
Opcional. Descreve o fluxo de vídeo de entrada. Se este campo não for especificado, é utilizado o valor predefinido. Esta definição só é permitida se o protocolo de transmissão em fluxo de entrada é definida como RTP (MPEG-TS).

#### <a name="index"></a>Índice
Um índice baseado em zero que especifica que o fluxo de vídeo entrada deve ser processado pelo codificador em direto no canal. Esta definição só se aplica se ingestão de protocolo de transmissão em fluxo é RTP (MPEG-TS).

Valor predefinido é zero. Recomenda-se para enviar uma sequência de transporte de programa único (SPTS). Se o fluxo de entrada contém vários programas, o codificador em direto analisa a tabela de mapa do programa (PMT) na entrada, identifica as entradas que tem um nome de tipo de fluxo de vídeo de MPEG-2 ou 264 e dispõe os mesmos na ordem especificada no PMT. O índice baseado em zero, em seguida, é utilizado para recolher a entrada de n-ésimo nesse disposição.

### <a name="audio-stream"></a>Sequência de áudio
Opcional. Descreve os fluxos de áudio entrados. Se este campo não for especificado, aplicam-se os valores predefinidos especificados. Esta definição só é permitida se o protocolo de transmissão em fluxo de entrada é definida como RTP (MPEG-TS).

#### <a name="index"></a>Índice
Recomenda-se para enviar uma sequência de transporte de programa único (SPTS). Se o fluxo de entrada contém vários programas, o codificador em direto no canal analisa a tabela de mapa do programa (PMT) na entrada, identifica as entradas que tem um nome de tipo de fluxo de MPEG-2 AAC ADTS ou um sistema de AC a 3 ou AC-3. o sistema-B ou PES privada MPEG-2 ou MPEG-1 Áudio ou áudio MPEG-2 e dispõe os mesmos na ordem especificada no PMT. O índice baseado em zero, em seguida, é utilizado para recolher a entrada de n-ésimo nesse disposição.

#### <a name="language"></a>Idioma
O identificador de linguagem do fluxo de áudio, cumprindo ISO 639-2, tais como ENG. Se não estiver presente, a predefinição é UND (indefinido).

Podem existir até 8 conjuntos de sequência de áudio especificado se a entrada para o canal é TS MPEG-2 sobre RTP. No entanto, não pode ser nenhum duas entradas com o mesmo valor de índice.

### <a id="preset"></a>Predefinição de sistema
Especifica a predefinição para ser utilizado pelo codificador em direto dentro deste canal. Atualmente, é o único valor permitido **Default720p** (predefinição).

Tenha em atenção que, se precisar de predefinições personalizadas, deve contacte amslived através de Microsoft.com.

**Default720p** será codificar o vídeo para seguintes 7 camadas.

#### <a name="output-video-stream"></a>Fluxo de vídeo de saída
| Velocidade de transmissão | Largura | Altura | MaxFPS | Perfil | Nome de fluxo de saída |
| --- | --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |Elevado |Video_1280x720_3500kbps |
| 2200 |960 |540 |30 |Principal |Video_960x540_2200kbps |
| 1350 |704 |396 |30 |Principal |Video_704x396_1350kbps |
| 850 |512 |288 |30 |Principal |Video_512x288_850kbps |
| 550 |384 |216 |30 |Principal |Video_384x216_550kbps |
| 350 |340 |192 |30 |Linha de base |Video_340x192_350kbps |
| 200 |340 |192 |30 |Linha de base |Video_340x192_200kbps |

#### <a name="output-audio-stream"></a>Sequência de áudio de saída
Áudio está codificado para stereo AAC LC em kbps 64, frequência de amostragem de 44.1 kHz.

## <a name="signaling-advertisements"></a>Sinalização anúncios
Quando o canal tem Live Encoding ativado, que tem um componente no seu pipeline de processamento de vídeo e possam ser manipulados. Pode assinalar para o canal inserir o fluxo de velocidade de transmissão adaptável saída slates e/ou anúncios. Slates ainda são imagens que pode utilizar para configurar o feed entrado em direto em certos casos (por exemplo, durante uma quebra de comercial). Sinais de publicidade, se a hora sincronizada sinais que incorporar para o fluxo de saída para saber se o leitor de vídeo qualquer ação especial – tal como para mudar para um anúncio no momento adequado. Consulte este [blogue](https://codesequoia.wordpress.com/2014/02/24/understanding-scte-35/) para uma descrição geral do mecanismo de signaling SCTE 35 utilizado para esta finalidade. Abaixo é um cenário típico, que pode implementar no seu evento em direto.

1. Tem a sua visualizadores obter uma imagem de pré-evento de antes de começa o evento.
2. Tem a sua visualizadores obter uma imagem de pós-evento após termina o evento.
3. Tem a sua visualizadores obter uma imagem de evento de erro se existir um problema durante o evento (por exemplo, falha de energia no stadium).
4. Envie uma imagem de quebra de AD para ocultar o feed durante uma quebra de comercial de evento em direto.

Seguem-se as propriedades, que pode definir quando sinalização anúncios. 

### <a name="duration"></a>Duração
A duração, em segundos, da quebra de comercial. Isto tem de ser um valor positivo diferente de zero para iniciar a quebra de comercial. Quando uma quebra de comercial está em curso e a duração está definida como zero com o CueId correspondente a quebra de comercial em curso, em seguida, esse break foi cancelada.

### <a name="cueid"></a>CueId
Um ID exclusivo para a quebra de comercial, para ser utilizadas por aplicações a jusante para efetuar ações adequadas. Tem de ser um número inteiro positivo. Pode definir este valor para qualquer aleatório um número inteiro positivo ou utilizar um sistema a montante para controlar os Ids de rectângulos. Certifique-se normalizar os ids de números inteiros positivos antes de submeter através da API.

### <a name="show-slate"></a>Mostrar ardósia
Opcional. Sinalizar o codificador em direto para mudar para o [predefinido ardósia](media-services-manage-live-encoder-enabled-channels.md#default_slate) imagem durante uma quebra de comercial e ocultar o feed de vídeo de entrada. Áudio também é muted durante ardósia. Predefinição é **falso**. 

A imagem utilizada será especificado através de propriedade de Id de recurso de predefinição ardósia no momento da criação de canal. A ardósia será esticada para se ajustar ao tamanho da imagem de apresentação. 

## <a name="insert-slate--images"></a>Inserir imagens ardósia
O codificador em direto no canal pode ser indicado para mudar para uma imagem de ardósia. Também pode ser indicado para terminar uma ardósia em curso. 

O codificador em direto pode ser configurado para mudar para uma imagem de ardósia e ocultar o sinal de vídeo de entrada em determinadas situações – por exemplo, durante uma quebra de ad. Se esses uma ardósia não estiver configurada, as vídeo de entrada não está oculto durante esse quebra de ad.

### <a name="duration"></a>Duração
A duração de ardósia em segundos. Isto tem de ser um valor positivo diferente de zero para iniciar a ardósia. Se existir uma ardósia em curso, e uma duração de zero é especificada, será terminada esse ardósia em curso.

### <a name="insert-slate-on-ad-marker"></a>Inserir ardósia em marcador do ad
Quando definido como true, esta definição configura o codificador em direto para inserir uma imagem de ardósia durante uma quebra de ad. O valor predefinido é verdadeiro. 

### <a id="default_slate"></a>Id de recurso de predefinido ardósia

Opcional. Especifica o Id de recurso do elemento de serviços de suporte de dados que contém a imagem de ardósia. Predefinição é nulo. 


>[!NOTE] 
>Antes de criar o canal, a imagem de ardósia com as seguintes restrições deve ser também carregada como um recurso dedicado (não existem outros ficheiros devem estar neste recurso). Esta imagem é utilizada apenas quando o codificador em direto é a inserir uma ardósia devido a uma quebra de ad ou tenha sido explicitamente assinalado para inserir uma ardósia. O codificador em direto pode também aceder num modo ardósia durante determinadas condições de erro – por exemplo se o sinal de entrada é perdido. Atualmente, não há nenhuma opção para utilizar uma imagem personalizada quando o codificador em direto entra essa um Estado 'perdeu a entrada de sinal'. Pode votar para esta funcionalidade [aqui](https://feedback.azure.com/forums/169396-azure-media-services/suggestions/10190457-define-custom-slate-image-on-a-live-encoder-channel).


* No máximo 1920 x 1080 na resolução.
* No máximo 3 em Mbytes de tamanho.
* O nome do ficheiro tem de ter uma extensão de *.jpg.
* A imagem é necessário carregar para um elemento como o AssetFile apenas na medida em que o recurso e este AssetFile deverá ser marcado como ficheiro principal. O elemento não pode ser armazenamento encriptado.

Se o **predefinido ardósia Id de recurso** não for especificado, e **inserir ardósia em marcador ad** está definido como **verdadeiro**, uma imagem de Media Services do Azure predefinida será utilizada para ocultar o vídeo de entrada fluxo. Áudio também é muted durante ardósia. 

## <a name="channels-programs"></a>Programas do canal
Um canal está associado a programas que permitem controlar a publicação e armazenamento de segmentos numa transmissão em fluxo em direto. Canais gerem Programas. A relação de canal e o programa é muito semelhante à multimédia tradicional onde um canal tem uma transmissão em fluxo constante de conteúdo e um programa está confinado a alguns eventos temporizados nesse canal.

Pode especificar o número de horas que pretenda manter o conteúdo gravado para o programa através da configuração da duração da **Janela de Arquivo**. Este valor pode ser definido a partir de um mínimo de 5 minutos até um máximo de 25 horas. A duração da janela de arquivo dita também o tempo máximo que os clientes podem recuar a partir da posição atual em direto. Os programas podem ser executados durante o período de tempo especificado, mas o conteúdo que se situe atrás da duração da janela é continuamente descartado. O valor desta propriedade também determina durante quanto tempo os manifestos dos clientes podem aumentar.

Cada programa está associado um recurso que armazena o conteúdo de transmissão em fluxo. Um recurso está mapeado para um contentor de BLOBs de blocos na conta do Storage do Azure e os ficheiros no elemento são armazenados como blobs no contentor. Para publicar o programa, pelo que os seus clientes, podem ver a sequência tem de criar um localizador OnDemand para o elemento associado. Ter este localizador irá permitir compilar um URL de transmissão em fluxo que pode fornecer aos seus clientes.

Um canal suporta até três programas em execução em simultâneo para que possa criar vários arquivos da mesma transmissão em fluxo recebida. Isto permite publicar e arquivar diferentes partes de um evento, conforme necessário. Por exemplo, os seus requisitos de negócios devem arquivar 6 horas de um programa, mas difundir apenas os últimos 10 minutos. Para tal, tem de criar dois programas em execução em simultâneo. Um programa está definido para arquivar 6 horas do evento, mas o programa não está publicado. O outro programa está definido para arquivar durante 10 minutos e este está publicado.

Não deve reutilizar programas existentes para novos eventos. Em vez disso, crie e inicie um novo programa para cada evento, conforme descrito na secção de programação em direto de transmissão em fluxo aplicações.

Inicie o programa quando estiver pronto para começar a transmissão em fluxo e o arquivamento. Pare o programa sempre que pretender interromper a transmissão e arquivar o evento. 

Para eliminar conteúdo arquivado, pare e elimine o programa e, em seguida, elimine o elemento associado. Não é possível eliminar um elemento se este é utilizado por um programa; o programa deve ser eliminado primeiro. 

Mesmo depois de parar e eliminar o programa, os utilizadores conseguirão transmitir o seu conteúdo arquivado como um vídeo a pedido até que elimine o elemento.

Se pretende manter o conteúdo arquivado, mas não o quer manter disponível para transmissão em fluxo, elimine o localizador de transmissão em fluxo.

## <a name="getting-a-thumbnail-preview-of-a-live-feed"></a>Obter uma pré-visualização em miniatura de um feed em direto
Quando Live Encoding estiver ativada, agora pode obter uma pré-visualização do feed em direto como atinge o canal. Isto pode ser uma ferramenta valiosa para verificar se o feed em direto, na verdade, está a atingir o canal. 

## <a id="states"></a>Estados de canal e a forma como os Estados de mapeiam para o modo de faturação
O estado atual de um Canal. Os valores possíveis incluem:

* **Parado**. Este é o estado inicial de um Canal após a sua criação. Neste estado, as propriedades do Canal podem ser atualizadas, mas a transmissão em fluxo não é permitida.
* **Iniciar**. O Canal está a ser iniciado. Não são permitidas transmissões em fluxo nem atualizações durante este estado. Caso ocorra um erro, o Canal volta para o estado Parado.
* **Executar**. O Canal é capaz de processar transmissões em fluxo.
* **Parar**. O Canal está a ser parado. Não são permitidas transmissões em fluxo nem atualizações durante este estado.
* **Eliminar**. O Canal está a ser apagado. Não são permitidas transmissões em fluxo nem atualizações durante este estado.

A tabela seguinte mostra como os estados de um Canal mapeiam para o modo de faturação. 

| Estado do canal | Indicadores IU do portal | Foi cobrado? |
| --- | --- | --- |
| A Iniciar |A Iniciar |Não (estado transitório) |
| A Executar |Pronto (nenhum programa em execução)<br/>ou<br/>A Transmitir em fluxo (pelo menos um programa em execução) |Sim |
| A Parar |A Parar |Não (estado transitório) |
| Parada |Parada |Não |

> [!NOTE]
> Atualmente, a média de início do canal é cerca de 2 minutos, mas por vezes, pode demorar até 20 + minutos. Reposições do canal podem demorar até 5 minutos.
> 
> 

## <a id="Considerations"></a>Considerações
* Quando um canal de **padrão** tipo de codificação sofre uma perda de feed de origem de entrada/contribuição, ao substituir o áudio/vídeo de origem com uma ardósia de erro e silêncio-compensates para o mesmo. O canal irá continuar a emitir uma ardósia até que a entrada/contribuição feed retoma. Recomendamos que um canais em direto não ser mantida num Estado durante um período superior a 2 horas. Esse ponto, o comportamento do canal no restabelecimento de ligação de entrada não é garantido, nem é o comportamento em resposta a um comando de reposição. Terá de parar o canal, eliminá-lo e criar um novo.
* Não é possível alterar o protocolo de entrada enquanto o Canal ou os seus programas associados estiverem em execução. Se necessitar de protocolos diferentes, deve criar canais separados para cada protocolo de entrada.
* Sempre que reconfigurar o codificador em direto, chame o **repor** método no canal. Antes de repor o canal, terá de parar o programa. Depois de repor o canal, reinicie o programa.
* Um canal pode ser parado apenas quando está no Estado em execução e todos os programas no canal foram parados.
* Por predefinição só é possível adicionar 5 canais à sua conta de Media Services. Esta é uma quota não restritiva em todas as contas de novo. Para obter mais informações, consulte [Quotas e limitações](media-services-quotas-and-limitations.md).
* Não é possível alterar o protocolo de entrada enquanto o Canal ou os seus programas associados estiverem em execução. Se necessitar de protocolos diferentes, deve criar canais separados para cada protocolo de entrada.
* É faturado apenas quando o canal está a ser o **executar** estado. Para obter mais informações, consulte [isto](media-services-manage-live-encoder-enabled-channels.md#states) secção.
* Atualmente, a duração máxima recomendada de um evento em direto é de 8 horas. Contacte amslived através de Microsoft.com, se tiver de executar um Canal durante períodos de tempo mais longos.
* Certifique-se de que tem o partir do qual pretende transmitir o conteúdo no ponto final de transmissão em fluxo a **executar** estado.
* Quando inputting vários idiomas controla e fazer live encoding com o Azure, RTP só é suportada para a entrada multilingues. Pode definir até 8 fluxos de áudio utilizando TS MPEG-2 sobre RTP. Ingestão relacionadas vários controla áudio com RTMP ou transmissão em fluxo uniforme não é atualmente suportada. Ao fazer live encoding com [em direto no local codifica](media-services-live-streaming-with-onprem-encoders.md), não há nenhum desse limitação porque que é enviado para AMS passa através de um canal sem qualquer processamento adicional.
* A predefinição de codificação utiliza a noção de "taxa de intervalo máximo" da 30 fps. Por isso, caso a entrada é 60fps 59.97i, as frames Jumbo entradas são ignoradas/de-interlaced para 30/29.97 fps. Se a entrada é 50fps/50i, as frames Jumbo entradas são ignorados/de-interlaced para 25 fps. Se a entrada é 25 fps, saída permanece no 25 fps.
* Não se esqueça de parar sua canais quando terminar. Se não o fizer, irá continuar a faturação.

## <a name="known-issues"></a>Problemas conhecidos
* No tempo de arranque de canal foi melhorado para uma média de 2 minutos, mas por vezes de crescente procura foi ainda demorar até 20 + minutos.
* Suporte RTP é catered para broadcasters profissionais. Reveja as notas sobre RTP no [isto](https://azure.microsoft.com/blog/2015/04/13/an-introduction-to-live-encoding-with-azure-media-services/) blogue.
* Imagens de ardósia devem estar em conformidade restrições descritas [aqui](media-services-manage-live-encoder-enabled-channels.md#default_slate). Se tentar criar um canal com uma ardósia predefinido que é maior do que 1920 x 1080, o pedido será erro.
* Uma vez novamente... não se esqueça de parar sua canais quando terminar de transmissão em fluxo. Se não o fizer, irá continuar a faturação.

## <a name="next-step"></a>Passo seguinte
Rever os percursos de aprendizagem dos Serviços de Multimédia

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Tópicos relacionados
[Entrega de eventos de transmissão em fluxo em direto com Media Services do Azure](media-services-overview.md)

[Criar canais que realizam live encoding da transmissão de um único fluxo de velocidade de transmissão adaptável com o Portal](media-services-portal-creating-live-encoder-enabled-channel.md)

[Criar canais que realizam live encoding da transmissão de um único fluxo de velocidade de transmissão adaptável com o .NET SDK](media-services-dotnet-creating-live-encoder-enabled-channel.md)

[Gerir canais com a REST API](https://docs.microsoft.com/rest/api/media/operations/channel)
 
[Conceitos de serviços de multimédia](media-services-concepts.md)

[Especificação de inserção de Media Services do Azure Live MP4 fragmentados](media-services-fmp4-live-ingest-overview.md)

[live-overview]: ./media/media-services-manage-live-encoder-enabled-channels/media-services-live-streaming-new.png

