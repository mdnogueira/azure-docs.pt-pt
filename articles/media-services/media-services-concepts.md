---
title: Conceitos de Media Services do Azure | Microsoft Docs
description: "Este tópico fornece uma descrição geral dos conceitos de serviços de suporte de dados do Azure"
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: dcefc8bc-e2ea-4b38-a643-9010f4436fb5
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/07/2017
ms.author: juliako
ms.openlocfilehash: da2dc87543fd8a0aa99e1de3018a310abe93fa3a
ms.sourcegitcommit: cf42a5fc01e19c46d24b3206c09ba3b01348966f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/29/2017
---
# <a name="azure-media-services-concepts"></a>Conceitos de Media Services do Azure
Este tópico fornece uma descrição geral dos conceitos de Media Services mais importantes.

## <a id="assets"></a>Ativos e armazenamento
### <a name="assets"></a>Elementos
Um [Asset](https://docs.microsoft.com/rest/api/media/operations/asset) contém ficheiros digitais (incluindo as vídeo, áudio, imagens, coleções de miniaturas, controla de texto e legendas ficheiros) e os metadados sobre estes ficheiros. Depois dos ficheiros digitais são carregados para um elemento, pode ser utilizados nos serviços de suporte de dados de codificação e fluxos de trabalho de transmissão em fluxo.

Um recurso está mapeado para um contentor de BLOBs na conta do Storage do Azure e os ficheiros no elemento são armazenados como blobs de blocos num contentor. Os blobs de página não são suportados pelos Media Services do Azure.

Quando decidir o conteúdo do suporte de dados para carregar e armazenar num recurso, aplicam as seguintes considerações:

* Um recurso deve conter apenas uma única e exclusiva instância do conteúdo de multimédia. Por exemplo, uma edição único de um episódio de TV, filmes ou anúncio.
* Um recurso não deve conter vários renditions ou as edições de um ficheiro audiovisual. Um exemplo de uma utilização incorreta de um recurso seria tentar armazenar mais do que um episódio de TV, anúncio ou vários ângulos câmara de um único produção dentro de um recurso. Armazenar várias renditions ou as edições de um ficheiro audiovisual num recurso pode resultar em dificuldades submeter tarefas de codificação, transmissão em fluxo e proteger a entrega de elemento mais tarde no fluxo de trabalho.  

### <a name="asset-file"></a>Ficheiro de elemento
Um [AssetFile](https://docs.microsoft.com/rest/api/media/operations/assetfile) representa um ficheiro de vídeo ou áudio propriamente dito que é armazenado num contentor de blob. Um ficheiro de recurso é sempre associado a um recurso e um recurso pode conter um ou vários ficheiros. A tarefa de codificador de serviços de suporte de dados falha se um objeto de ficheiro de recurso não está associado um ficheiro digital num contentor de blob.

O **AssetFile** instância e o ficheiro de multimédia real são dois objetos distintos. A instância de AssetFile contém metadados sobre o ficheiro de suporte de dados, enquanto o ficheiro de suporte de dados contém o conteúdo de multimédia real.

Não deve tentar alterar os conteúdos de contentores do blob que foram gerados pelos serviços de suporte de dados sem utilizar as APIs do serviço de suporte de dados.

### <a name="asset-encryption-options"></a>Opções de encriptação do elemento
Dependendo do tipo de conteúdo que pretende carregar, armazenar e distribuir, os Media Services fornecem várias opções de encriptação que pode escolher.

>[!NOTE]
>É utilizada qualquer encriptação. Este é o valor predefinido. Ao utilizar esta opção não está protegido o conteúdo em trânsito ou inativo no armazenamento.

Se planear distribuir um MP4 utilizando transferência progressiva, utilize esta opção para carregar o conteúdo.

**StorageEncrypted** – Utilize esta opção para encriptar o seu conteúdo transparente localmente utilizando a encriptação AES de 256 bits e, em seguida, carregue-o no Storage do Azure onde é armazenado encriptados em pausa. Os elementos protegidos com encriptação do storage são desencriptados automaticamente colocados no sistema de ficheiros encriptados antes da codificação e opcionalmente encriptados novamente antes de serem carregados novamente como um novo elemento de saída. O principal motivo para a encriptação de armazenamento é quando pretender proteger os ficheiros de suporte de dados de entrada de alta qualidade com uma encriptação forte Inativos no disco. 

Para fornecer um recurso encriptados de armazenamento, tem de configurar política de entrega de elementos para que os Media Services confie como pretende distribuir os seus conteúdos. Antes do elemento possa ser transmitido, o servidor de transmissão em fluxo remove a encriptação de armazenamento e fluxos de conteúdo através da política de entrega especificado (por exemplo, AES, PlayReady ou sem encriptação). 

**CommonEncryptionProtected** -Utilize esta opção se pretende encriptar (ou carregar já encriptou o) conteúdo com encriptação comum ou PlayReady DRM (por exemplo, transmissão em fluxo uniforme protegida com PlayReady DRM).

**EnvelopeEncryptionProtected** – Utilize esta opção se pretender proteger (ou carregar já protegidos) HTTP Live Streaming (HLS) encriptado com AES Advanced Encryption Standard (). Se estiver a carregar HLS já encriptado com AES, deve ter sido encriptado pelo Gestor de transformação.

### <a name="access-policy"></a>Política de acesso
Um [AccessPolicy](https://docs.microsoft.com/rest/api/media/operations/accesspolicy) define as permissões (como de leitura, escrita e lista) e a duração de acesso a um recurso. Normalmente, seria transmitir um objeto de AccessPolicy para um localizador que, em seguida, seria utilizado para aceder aos ficheiros contidos num recurso.

>[!NOTE]
>Existe um limite de 1,000,000 políticas para diferentes políticas do AMS (por exemplo, para a política Locator ou ContentKeyAuthorizationPolicy). Deve utilizar o mesmo ID de política se estiver a utilizar sempre os mesmas permissões de dias/acesso, por exemplo, políticas para localizadores que pretendam permanecem no local durante muito tempo (políticas de não carregamento). Para obter mais informações, veja [este](media-services-dotnet-manage-entities.md#limit-access-policies) tópico.

### <a name="blob-container"></a>Contentor de blob
Um contentor do blob fornece um agrupamento de um conjunto de blobs. Contentores de BLOBs são utilizados nos serviços de suporte de dados como ponto de limites para o controlo de acesso e localizadores de assinatura de acesso partilhado (SAS) nos recursos. Uma conta de armazenamento do Azure pode conter um número ilimitado de contentores de Blobs. Um contentor pode armazenar um número ilimitado de blobs.

>[!NOTE]
> Não deve tentar alterar os conteúdos de contentores do blob que foram gerados pelos serviços de suporte de dados sem utilizar as APIs do serviço de suporte de dados.
> 
> 

### <a id="locators"></a>Localizadores
[O localizador](https://docs.microsoft.com/rest/api/media/operations/locator)s fornecer um ponto de entrada para aceder aos ficheiros contidos num recurso. Uma política de acesso é utilizada para definir as permissões e a duração do que um cliente tem acesso a um recurso especificado. Os localizadores podem ter uma relação muitos para um com uma política de acesso que localizadores diferentes podem fornecer tempos de início diferentes e tipos de ligação diferentes clientes durante a utilização de todas as definições de duração; e a mesma permissão No entanto, devido a uma restrição de política de acesso partilhado definida pelos serviços de armazenamento do Azure, não pode ter mais de cinco localizadores exclusivos associados um determinado elemento de uma só vez. 

Os Media Services suportam dois tipos de Localizadores: localizadores OnDemandOrigin, utilizados para transmitir multimédia (por exemplo, MPEG DASH, HLS ou transmissão em fluxo uniforme) ou transferir progressivamente os suportes de dados e localizadores de SAS URL, utilizado para carregar ou transferir os ficheiros de suporte de dados to\from storage do Azure. 

>[!NOTE]
>A permissão de lista (AccessPermissions.List) não deve ser utilizada ao criar um localizador OnDemandOrigin. 

### <a name="storage-account"></a>Conta de armazenamento
Todos os acessos ao Storage do Azure é feito através de uma conta de armazenamento. Pode associar uma conta de serviço de suporte de dados com uma ou mais contas de armazenamento. Uma conta pode conter um número ilimitado de contentores, desde que o tamanho total é em 500TB por conta de armazenamento.  Os Media Services fornecem ferramentas ao nível do SDK para que possa gerir várias contas de armazenamento e a distribuição dos seus ativos durante o carregamento para estas contas com base nas métricas ou distribuição aleatória de balanceamento de carga. Para obter mais informações, consulte como trabalhar com [Storage do Azure](https://msdn.microsoft.com/library/azure/dn767951.aspx). 

## <a name="jobs-and-tasks"></a>Trabalhos e tarefas
A [tarefa](https://docs.microsoft.com/en-us/rest/api/media/operations/job) é normalmente utilizada para processar (por exemplo, índice ou codificar) uma apresentação de áudio/vídeo. Se estiver a processar vários vídeos, crie uma tarefa para cada vídeo para ser codificado.

Uma tarefa contém metadados sobre o processamento para ser executada. Cada tarefa contém um ou mais [tarefas](https://docs.microsoft.com/rest/api/media/operations/task)s que especificam uma tarefa de processamento de atómico, os recursos de entrada, saída ativos, um processador de multimédia e as respetivas definições associadas. Tarefas dentro de uma tarefa podem ser ligadas em cadeia, onde o elemento de saída de uma tarefa está indicado como o elemento de entrada para a próxima tarefa. Desta forma uma tarefa pode conter de processamento necessário para uma apresentação de suporte de dados.

## <a id="encoding"></a>Codificação
Media Services do Azure fornece várias opções para a codificação de suportes de dados na nuvem.

Ao começar com os Media Services, é importante compreender a diferença entre os formatos de codecs e de ficheiros.
Codecs são o software que implementa os algoritmos de compressão/descompressão enquanto formatos de ficheiro são contentores que contêm o vídeo comprimido.

Os Media Services fornecem um empacotamento dinâmico, permitindo-lhe fornecer o conteúdo de velocidade de transmissão adaptável MP4 ou transmissão em fluxo uniforme codificado na transmissão em fluxo suportados pelos Media Services (MPEG DASH, HLS, transmissão em fluxo uniforme) de formatos, sem ter de novamente o pacote para estes formatos de transmissão em fluxo.

Para tirar partido das [empacotamento dinâmico](media-services-dynamic-packaging-overview.md), terá de codificar o (origem) mezanino para um conjunto de ficheiros MP4 de velocidade de transmissão adaptável ou ficheiros de transmissão em fluxo uniforme de velocidade de transmissão adaptável e ter, pelo menos, um endpoint de transmissão em fluxo standard ou premium iniciar o estado.

Os Media Services suportam os seguintes codificadores a pedido que são descritos neste artigo:

* [Media Encoder Standard](media-services-encode-asset.md#media-encoder-standard)
* [Fluxo de Trabalho Premium do Codificador de Multimédia](media-services-encode-asset.md#media-encoder-premium-workflow)

Para obter informações sobre codificadores suportados, consulte [codificadores](media-services-encode-asset.md).

## <a name="live-streaming"></a>Transmissão em Direto
Nos Media Services do Azure, um canal representa um pipeline de processamento de conteúdos de transmissão em fluxo em direto. Um canal recebe fluxos de entrada em direto de uma das seguintes formas:

* Um codificador em direto no local envia-múltipla RTMP ou transmissão em fluxo uniforme (MP4 fragmentado) para o canal. Pode utilizar os seguintes codificadores em direto com velocidade de múltipla transmissão em fluxo uniforme de saída: MediaExcel, Ateme, Imagine comunicações, Envivio, Cisco e elementar. Os seguintes codificadores em direto de saída RTMP: codificadores Adobe Flash codificador em direto, Telestream Wirecast, Teradek, Haivision e Transcodificadores. Transmita as transmissões em fluxo através dos canais sem codificação e transcodificação mais. Quando solicitado, os Media Services disponibilizam a transmissão em fluxo para os clientes.
* Um fluxo de velocidade de transmissão única (dos seguintes formatos: RTP (MPEG-TS)), RTMP ou transmissão em fluxo uniforme (MP4 fragmentado)) é enviada para o canal ativado para realizar live encoding com Media Services. O Canal, em seguida, realiza live encoding da transmissão em fluxo de velocidade de transmissão única de entrada para uma transmissão em fluxo de vídeo com várias velocidades (adaptável). Quando solicitado, os Media Services disponibilizam a transmissão em fluxo para os clientes.

### <a name="channel"></a>Canal
Nos Media Services, [canal](https://docs.microsoft.com/rest/api/media/operations/channel)s são responsáveis por processar o conteúdo de transmissão em fluxo em direto. Um canal fornece um ponto final de entrada (URL de inserção) que, em seguida, forneça a um transcoder em direto. O canal recebe fluxos de entrada em direto do transcoder em direto e torna a mesma disponível para transmissão em fluxo através de um ou mais lidam. Canais também fornecem um ponto final de pré-visualização (URL de pré-visualização) que utilizar para pré-visualizar e validar a sua transmissão em fluxo antes do processamento adicional e entrega.

Pode obter o URL de inserção e o URL de pré-visualização quando criar o canal. Para obter estes URLs, o canal não tem de estar no estado iniciado. Quando estiver pronto para começar a enviar por push dados de um transcoder em direto para o canal, o canal tem de ser iniciado. Assim que for iniciada a transcoder em direto a ingestão de dados, pode pré-visualizar a transmissão.

Cada conta de Media Services pode conter vários canais, vários programas e lidam vários. Consoante as necessidades de segurança e de largura de banda, podem ser dedicados StreamingEndpoint serviços para um ou mais canais. Qualquer StreamingEndpoint pode solicitar a partir de qualquer canal.

### <a name="program-event"></a>Programa (evento)
A [programa (evento)](https://docs.microsoft.com/rest/api/media/operations/program) permite-lhe controlar a publicação e armazenamento de segmentos numa transmissão em fluxo em direto. Canais gerem programas (eventos). A relação de canal e o programa é semelhante à multimédia tradicional onde um canal tem uma transmissão em fluxo constante de conteúdo e um programa está confinado a alguns eventos temporizados nesse canal.
Pode especificar o número de horas que pretenda manter o conteúdo gravado para o programa, definindo o **ArchiveWindowLength** propriedade. Este valor pode ser definido a partir de um mínimo de 5 minutos até um máximo de 25 horas.

ArchiveWindowLength dita também que a quantidade máxima de clientes de hora pode recuar a partir da posição atual em direto. Os programas podem ser executados durante o período de tempo especificado, mas o conteúdo que se situe atrás da duração da janela é continuamente descartado. O valor desta propriedade também determina durante quanto tempo os manifestos dos clientes podem aumentar.

Cada programa (evento) está associado um recurso. Para publicar o programa tem de criar um localizador para o elemento associado. Ter este localizador irá permitir compilar um URL de transmissão em fluxo que pode fornecer aos seus clientes.

Um canal suporta até três programas em execução em simultâneo para que possa criar vários arquivos da mesma transmissão em fluxo recebida. Isto permite publicar e arquivar diferentes partes de um evento, conforme necessário. Por exemplo, os seus requisitos de negócios devem arquivar 6 horas de um programa, mas difundir apenas os últimos 10 minutos. Para tal, tem de criar dois programas em execução em simultâneo. Um programa está definido para arquivar 6 horas do evento, mas o programa não está publicado. O outro programa está definido para arquivar durante 10 minutos e este está publicado.

Para obter mais informações, consulte:

* [Trabalhar com canais ativados para realizar Live Encoding com Media Services do Azure](media-services-manage-live-encoder-enabled-channels.md)
* [Trabalhar com Canais que Recebem Transmissões em Fluxo em Direto com Velocidade de Transmissão Múltipla a partir de Codificadores no Local (Working with Channels that Receive Multi-bitrate Live Stream from On-premises Encoders)](media-services-live-streaming-with-onprem-encoders.md)
* [Quotas e limitações](media-services-quotas-and-limitations.md).

## <a name="protecting-content"></a>Proteger conteúdo
### <a name="dynamic-encryption"></a>Encriptação dinâmica
Media Services do Azure permite-lhe proteger suporte de dados desde o momento em que deixa o seu computador através de armazenamento, processamento e entrega. Os Media Services permite-lhe entregar o conteúdo encriptado dinamicamente com AES Advanced Encryption Standard () (utilizando as chaves de encriptação de 128 bits) e a encriptação comum (CENC) com PlayReady e/ou Widevine DRM. Os Media Services também fornecem um serviço para entrega de chaves AES e licenças PlayReady para clientes autorizados.

Atualmente, pode encriptar os seguintes formatos de transmissão em fluxo: HLS, MPEG DASH e transmissão em fluxo uniforme. Não é possível encriptar transferências de transferência progressiva.

Se pretender que para os serviços de suporte de dados encriptar um recurso, tem de associar o seu elemento uma chave de encriptação (CommonEncryption ou EnvelopeEncryption) e também configurar políticas de autorização da chave.

Se pretende transmitir um recurso encriptados de armazenamento, tem de configurar política de entrega de elementos para poder especificar a forma como pretende fornecer o seu elemento.

Quando um fluxo é solicitado por um leitor, os Media Services utiliza a chave especificada para encriptar o conteúdo com uma encriptação de envelope (com AES) ou a encriptação comum (PlayReady ou Widevine) de forma dinâmica. Para desencriptar o fluxo, o leitor solicitará a chave do serviço de entrega de chave. Para decidir se pretende ou não o utilizador está autorizado para obter a chave, o serviço avalia as políticas de autorização que especificou para a chave.

### <a name="token-restriction"></a>Restrição de token
A política de autorização da chave de conteúdo pode ter um ou mais restrições de autorização: abra, restrição ou restrição de IP do token. A política de token restrito tem de ser acompanhada por um token emitido por um Serviço de Token Seguro (STS). Os Media Services suportam tokens no formato simples Web Tokens (SWT) e formato JSON Web tokens (JWT). Serviços de suporte de dados não fornece serviços de Token seguro. Pode criar um STS personalizado ou tirar partido das ACS do Microsoft Azure para tokens de problema. O STS tem de ser configurado para criar o token assinado com especificado chave e emitir afirmações que especificou na configuração de restrição de token. O serviço de entrega de chave de Media Services irá devolver a chave de pedido (ou uma licença) para o cliente se o token é válido e as afirmações na correspondência de token que os que são configurados para a chave (ou uma licença).

Quando configurar o token restrito a política, tem de especificar a chave de verificação principal, o emissor e o parâmetros público-alvo. A chave de verificação principal contém a chave que o token foi assinado com, emissor é o serviço de token seguro que emite o token. O público-alvo (por vezes denominado âmbito) descreve a intenção do token ou o recurso autoriza o token de acesso a. O serviço de entrega de chave de Media Services valida que estes valores no token correspondem aos valores no modelo.

Para obter mais informações, veja os artigos seguintes:

[Descrição geral do conteúdo de proteger](media-services-content-protection-overview.md)
[proteger com AES-128](media-services-protect-with-aes128.md)
[proteger com DRM](media-services-protect-with-drm.md)

## <a name="delivering"></a>Entrega
### <a id="dynamic_packaging"></a>Empacotamento dinâmico
Ao trabalhar com os Media Services, recomenda-se para codificar seus ficheiros de mezanino para uma conjunto de MP4 de velocidade de transmissão adaptável e, em seguida, converter o conjunto para o formato pretendido utilizando a [empacotamento dinâmico](media-services-dynamic-packaging-overview.md).

### <a name="streaming-endpoint"></a>Ponto final de transmissão em fluxo
Um StreamingEndpoint representa um serviço de transmissão em fluxo que pode proporcionar o conteúdo diretamente a uma aplicação de leitor de cliente ou a rede de entrega um conteúdos (CDN) para uma maior distribuição (Media Services do Azure fornece agora a integração da CDN do Azure.) O fluxo de saída de um serviço de ponto final de transmissão em fluxo pode ser uma transmissão em fluxo em direto ou um elemento de vídeo a pedido na sua conta de Media Services. Os clientes dos Serviços de Multimédia escolhem um ponto final de transmissão em fluxo **Standard** ou um ou mais pontos finais de transmissão em fluxo **Premium**, de acordo com as respetivas necessidades. Padrão de ponto final de transmissão em fluxo é adequado para a maioria das cargas de trabalho de transmissão em fluxo. 

O Ponto Final de Transmissão em Fluxo Standard é ideal para a maioria das cargas de trabalho de transmissão em fluxo. Pontos finais de transmissão em fluxo padrão oferecem flexibilidade para entregar o conteúdo para praticamente todos os dispositivos através do empacotamento dinâmico em HLS, MPEG-DASH e a transmissão em fluxo uniforme, bem como a encriptação dinâmica para o Microsoft PlayReady, Widevine da Google, do Fairplay de Apple, e AES-128.  Também dimensionamento de muito pequenos para muito grande público com milhares de visualizadores autorizados em simultâneo através da integração da CDN do Azure. Se tiver uma carga de trabalho avançada ou os requisitos de capacidade de transmissão em fluxo não se ajustem padrão transmissão em fluxo destinos de débito de ponto final ou se pretender controlar a capacidade do serviço StreamingEndpoint para processar a crescer tem de largura de banda, é recomendado alocar unidades de escala (também conhecido como unidades transmissão em fluxo premium).

Recomenda-se para utilizar o empacotamento dinâmico e/ou a encriptação dinâmica.

>[!NOTE]
>Quando a sua conta AMS é criada, é adicionado um ponto final de transmissão em fluxo **predefinido** à sua conta no estado **Parado**. Para começar a transmitir o seu conteúdo em fluxo e a tirar partido do empacotamento e encriptação dinâmicos, o ponto final de transmissão em fluxo a partir do qual quer transmitir conteúdo tem de estar no estado **Em execução**. 

Para obter mais informações, veja [este](media-services-portal-manage-streaming-endpoints.md) tópico.

Por predefinição pode ter até 2 transmissão em fluxo de pontos finais numa conta de Media Services. Para pedir um limite superior, consulte [Quotas e limitações](media-services-quotas-and-limitations.md).

É-lhe faturado apenas quando o seu StreamingEndpoint está no estado de execução.

### <a name="asset-delivery-policy"></a>Política de entrega de elemento
Um dos passos no fluxo de trabalho de entrega de conteúdos de Media Services está a configurar [políticas de entrega para ativos ](https://docs.microsoft.com/rest/api/media/operations/assetdeliverypolicy)que pretende que a transmissão em fluxo. A política de entrega de elemento indica aos Media Services como pretende que os para o seu elemento a entregar: para o protocolo de transmissão em fluxo deve seu elemento ser dinamicamente compactado (por exemplo, MPEG DASH, HLS, transmissão em fluxo uniforme ou todos), pretende encriptar de forma dinâmica ou não o elemento e como (envelope ou encriptação comum).

Se tiver um recurso de encriptados de armazenamento, antes do elemento pode transmissão em fluxo, o servidor de transmissão em fluxo remove a encriptação de armazenamento e fluxos de conteúdo através da política de entrega especificado. Por exemplo, para fornecer o seu elemento encriptado com a chave de encriptação Advanced Encryption Standard (AES), defina o tipo de política para DynamicEnvelopeEncryption. Para remover a encriptação de armazenamento e transmitir o elemento na limpar, defina o tipo de política para NoDynamicEncryption.

### <a name="progressive-download"></a>Transferência progressiva
Transferência progressiva permite-lhe começar a reproduzir o suporte de dados antes do ficheiro completo tenha sido transferido. Pode transferir progressivamente apenas um ficheiro MP4.

>[!NOTE]
>Tem de desencriptar ativos encriptados se desejar para os mesmos estar disponível para transferência progressiva.

Para fornecer aos utilizadores com URLs de transferência progressiva, primeiro tem de criar um localizador OnDemandOrigin. Criar o localizador, dá-lhe o caminho de base para o elemento. Em seguida, temos de acrescentar o nome de ficheiro MP4. Por exemplo:

http://amstest1.Streaming.mediaservices.Windows.NET/3c5fe676-199c-4620-9B03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

### <a name="streaming-urls"></a>URLs de transmissão em fluxo
O conteúdo para clientes de transmissão em fluxo. Para fornecer aos utilizadores com os URLs de transmissão em fluxo, primeiro tem de criar um localizador OnDemandOrigin. Criar o localizador, dá-lhe o caminho de base para o elemento que contenha o conteúdo que pretende transmitir em fluxo. No entanto, para conseguir transmitir este conteúdo tem de modificar ainda mais este caminho. Para construir um URL completo para o ficheiro de manifesto de transmissão em fluxo, tem de concatenar o valor do localizador de caminho e o manifesto (filename.ism) o nome de ficheiro. Em seguida, anexe /Manifest e um formato adequado (se necessário) para o caminho de localizador.

Também pode transmitir o seu conteúdo através de uma ligação SSL. Para tal, certifique-se que os URLs de transmissão em fluxo começar a utilizar HTTPS. Atualmente, AMS não suporta SSL com domínios personalizados.  

>[!NOTE]
>Apenas pode transmitir através de SSL se o ponto de final transmissão em fluxo a partir da qual a fornecer o conteúdo foi criado depois de 10 de Setembro de 2014. Se os URLs de transmissão em fluxo são baseados nos pontos finais de transmissão em fluxo criados após 10 de Setembro, o URL contém "streaming.mediaservices.windows.net" (o novo formato). URL de transmissão em fluxo que contenham "origin.mediaservices.windows.net" (o formato antigo) não suportam SSL. Se o seu URL tem um formato antigo e pretender ser capaz de transmitir através de SSL, crie um novo ponto de final de transmissão em fluxo. Utilize URLs criados com base no novo ponto de final de transmissão em fluxo para transmitir o seu conteúdo através de SSL.

A lista seguinte descreve os formatos de transmissão em fluxo diferentes e fornece exemplos:

* Transmissão em Fluxo Uniforme

{nome da conta do nome dos serviços multimédia do ponto final de transmissão em fluxo}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

http://testendpoint-testaccount.Streaming.mediaservices.Windows.NET/fecebb23-46F6-490d-8B70-203e86b0df58/BigBuckBunny.ISM/manifest

* MPEG DASH

{de transmissão em fluxo ponto final dos serviços de suporte de dados de nome de conta name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

http://testendpoint-testaccount.Streaming.mediaservices.Windows.NET/fecebb23-46F6-490d-8B70-203e86b0df58/BigBuckBunny.ISM/MANIFEST(Format=mpd-Time-CSF)

* Apple HTTP Live transmissão em fluxo (HLS) V4

{de transmissão em fluxo ponto final dos serviços de suporte de dados de nome de conta name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

http://testendpoint-testaccount.Streaming.mediaservices.Windows.NET/fecebb23-46F6-490d-8B70-203e86b0df58/BigBuckBunny.ISM/MANIFEST(Format=m3u8-aapl)

* Apple HTTP Live transmissão em fluxo (HLS) V3

{de transmissão em fluxo ponto final dos serviços de suporte de dados de nome de conta name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl-v3)

http://testendpoint-testaccount.Streaming.mediaservices.Windows.NET/fecebb23-46F6-490d-8B70-203e86b0df58/BigBuckBunny.ISM/MANIFEST(Format=m3u8-aapl-v3)

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

