---
title: "Notas de versão de Media Services | Microsoft Docs"
description: "Notas de versão de serviços de multimédia"
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 3ca2d7af-1cf0-45fa-9585-3b73f3ee057d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: media
ms.devlang: dotnet
ms.topic: article
ms.date: 10/18/2017
ms.author: juliako
ms.openlocfilehash: 3000acf91a66af3ec512af52362f7f1e2ba0019b
ms.sourcegitcommit: 3e3a5e01a5629e017de2289a6abebbb798cec736
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2017
---
# <a name="azure-media-services-release-notes"></a>Notas de versão de Media Services do Azure
Estas notas de versão resumem as alterações de versões anteriores e problemas conhecidos.

> [!NOTE]
> Queremos ouvi dos nossos clientes e concentrar-se na corrigir problemas que afetam. Para comunicar um problema ou colocar questões, publique no [fórum MSDN do Azure Media Services].
> 
> 

## <a id="issues"></a>Problemas conhecidos atualmente
### <a id="general_issues"></a>Problemas gerais de serviços de multimédia
| Problema | Descrição |
| --- | --- |
| Vários cabeçalhos HTTP comuns não são fornecidos na REST API. |Se desenvolver aplicações de Media Services utilizando a API REST, encontrará que alguns campos de cabeçalho HTTP comuns (incluindo CLIENT-REQUEST-ID, o ID de pedido e retorno-CLIENT-REQUEST-ID) não são suportadas. Os cabeçalhos serão adicionados numa atualização futura. |
| Não é permitida a codificação de percentagem. |Os Media Services utiliza o valor da propriedade IAssetFile.Name ao criar os URLs para os conteúdos de transmissão em fluxo (por exemplo, http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) Por este motivo, por cento de codificação não é permitida. O valor da **nome** propriedade não pode ter qualquer um dos seguintes [por cento codificação-reservados carateres](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters):! *' ();: @& = + $, /? % # [] ". Além disso, só pode existir um '.' para a extensão de nome de ficheiro. |
| O método de ListBlobs que faz parte da falha de 3 de versão do SDK de armazenamento do Azure. |Os Media Services gera os URLs de SAS com base no [2012-02-12](https://docs.microsoft.com/rest/api/storageservices/Version-2012-02-12) versão. Se pretender utilizar o SDK de armazenamento do Azure para blobs de lista num contentor de BLOBs, utilize o [CloudBlobContainer.ListBlobs](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobs.aspx) método que faz parte da versão do SDK de armazenamento do Azure 2. x. O método de ListBlobs que faz parte da versão do SDK de armazenamento do Azure de 3 irá falhar. |
| Os Media Services limitação mecanismo restringe a utilização de recursos para aplicações que tornam o pedido excessivo no serviço. O serviço poderá devolveu o código de estado HTTP do serviço indisponível (503). |Para obter mais informações, consulte a descrição do código de estado HTTP 503 no [códigos de erro de serviços de suporte de dados do Azure](media-services-encoding-error-codes.md) artigo. |
| Ao consultar entidades, não há um limite de 1000 entidades devolvido de uma só vez porque pública REST v2 limita os resultados da consulta para resultados de 1000. |Tem de utilizar **ignorar** e **demorar** (.NET) / **superior** (REST), conforme descrito em [neste exemplo .NET](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) e [neste exemplo de REST API](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities). |
| Alguns clientes podem ter através de um problema de repetições etiqueta no manifesto de transmissão em fluxo uniforme. |Para obter mais informações, veja [esta](media-services-deliver-content-overview.md#known-issues) secção. |
| Objetos de SDK .NET dos Media Services do Azure não podem ser serializados e assim não funcionar com colocação em cache do Azure. |Se tentar serializar o objeto de SDK AssetCollection adicioná-lo a colocação em cache do Azure, é emitida uma exceção. |


## <a id="rest_version_history"></a>Histórico da versão de API REST
Para obter informações sobre o histórico da versão de API de REST dos serviços de suporte de dados, consulte [referência da API REST do Azure Media Services].

## <a name="october-2017-release"></a>Versão de Outubro de 2017
> [!IMPORTANT] 
> O lembrete: Media Services do Azure vai ser descontinuar o suporte para chaves de autenticação de ACS.  No dia 1 de Junho de 2018, já não poderá autenticar com o back-end do AMS através de código de utilização de chaves de ACS. Tem de atualizar o seu código para utilizar o Azure Active Directory (AAD) por artigo [Azure Active Directory (Azure AD)-autenticação com base no](media-services-use-aad-auth-to-access-ams-api.md). Também receberá avisos no portal do Azure desta alteração.

### <a name="updates-for-october-2017-include"></a>As atualizações de Outubro de 2017 incluem:
#### <a name="sdks"></a>SDKs
* .NET SDK atualizado para suportar a autenticação do AAD.  Suporte para a autenticação de ACS foi removido do SDK .NET mais recente no Nuget.org a encorajar migração mais rápida para o AAD. 
* JAVA SDK atualizado para suportar a autenticação do AAD.  Foi adicionado suporte para a autenticação do AAD ao nosso SDK de Java. Pode ler mais detalhes sobre como utilizar o SDK de Java com AMS no artigo [começar com o cliente de Java SDK de Media Services do Azure](media-services-java-how-to-use.md)

#### <a name="file-based-encoding"></a>Codificação baseada em ficheiros
1.  Agora, pode utilizar o codificador Premium para codificar o conteúdo para H.265(HEVC) codec de vídeo. Não há nenhum impacto sobre preços para escolher H.265 através de outros codecs como 264. Consulte o [termos de serviço Online](https://azure.microsoft.com/support/legal/) para uma nota importante sobre HEVC patente license(s).
2.  Se tiver as vídeo de origem que está codificada com H.265(HEVC) codec vídeo, tais como as vídeo capturado usando iOS11 ou GoPro heroína 6, podem agora utilizar o codificador Premium ou o codificador padrão para codificar os vídeos. Consulte o [termos de serviço Online](https://azure.microsoft.com/support/legal/) para uma nota importante sobre license(s) patente.
3.  Se tiver conteúdo que contém vários idiomas áudio pistas, em seguida, desde que os valores de idioma estão identificados corretamente, de acordo com a especificação de formato de ficheiro correspondente (por exemplo, MP4 ISO), em seguida, pode utilizar o codificador padrão para codificar esse conteúdo para transmissão em fluxo. O localizador de transmissão em fluxo resultante será uma lista os idiomas de áudio disponíveis.
4.  Codificador padrão suporta agora dois novos só de áudio predefinições do sistema, "AAC áudio" e "AAC boa qualidade áudio". Ambos produzem saída AAC stereo, às taxas de bits de 128 kbps e 192 kbps, respetivamente.
5.  Codificador Premium agora suporta os formatos de ficheiro de QuickTime/MOV como entrada, desde que o vídeo codec é uma do [Apple ProRes tipos listados aqui](https://docs.microsoft.com/en-us/azure/media-services/media-services-media-encoder-standard-formats), sendo áudio AAC ou PCM.

> [!NOTE]
> Codificador Premium não suporta, por exemplo, as vídeo DVC/DVCPro encapsulada nos ficheiros de QuickTime/MOV, como entrada.  No entanto, o codificador padrão suporta estas codecs vídeos.
>
>

6.  Correções de erros em codificadores:
    * Pode submeter as tarefas com um recurso de entrada e depois destas concluída, modifique o elemento (por exemplo, ao adicionar/eliminar/mudar o nome de ficheiros dentro do elemento) e submeter tarefas adicionais. 
    * Qualidade melhorada de miniaturas JPEG produzido pelo codificador padrão
    * Melhoramentos ao codificador padrão de vídeos de curta duração. Melhor de processamento de metadados de entrada e de geração em miniatura no vídeos tão curta duração.
    * Melhoramentos para o descodificador 264 utilizado no codificador padrão, elimina determinados raros artefactos. 

#### <a name="media-analytics"></a>Análise de Multimédia
* GA do Redactor de suporte de dados do Azure - este processador de suporte de dados (MP) irá efetuar anonymization por diária esbater pessoal faces de pessoas selecionadas e é ideal para utilização em situações de segurança e de suporte de dados de notícias públicas. Para obter uma descrição geral neste processador de novo, consulte a mensagem de blogue [aqui](https://azure.microsoft.com/blog/azure-media-redactor/). Para obter documentação detalhada e definições, consulte [Redact faces análise de multimédia do Azure](media-services-face-redaction.md).



## <a name="june-2017-release"></a>Versão de Junho de 2017

Os Media Services agora suporta [Azure Active Directory (Azure AD)-autenticação com base no](media-services-use-aad-auth-to-access-ams-api.md).

> [!IMPORTANT]
> Atualmente, os Media Services suportam o modelo de autenticação do serviço de controlo de acesso do Azure. No entanto, a autorização de controlo de acesso vai ser preterida no dia 1 de Junho de 2018. Recomendamos a migração para o modelo de autenticação do Azure AD logo que possível.

## <a name="march-2017-release"></a>Versão de Março de 2017

Agora, pode utilizar Azure de multimédia Standard para [gerar automaticamente um ladder de velocidade de transmissão](media-services-autogen-bitrate-ladder-with-mes.md) especificando o "transmissão em fluxo adaptável" cadeia de configuração predefinida durante a criação de uma tarefa de codificação. "Transmissão em fluxo adaptável" é a predefinição recomendada se pretende codificar um vídeo para transmissão em fluxo com os Media Services. Se precisar de personalizar uma codificação da configuração predefinida para o seu cenário específico, pode começar com [estes](media-services-mes-presets-overview.md) predefinições.

Agora, pode utilizar o padrão de suporte de dados do Azure ou fluxo de trabalho do suporte de dados codificador Premium para [criar uma tarefa de codificação que gera fMP4 segmentos](media-services-generate-fmp4-chunks.md). 


## <a name="february-2017-release"></a>Versão de Fevereiro de 2017

A partir de 1 de abril de 2017, qualquer Registo de tarefa na sua conta com mais de 90 dias será eliminado automaticamente, juntamente com os seus registos de Tarefas associados, mesmo se o número total de registos for inferior à quota máxima. Se precisar de arquivar as informações de tarefas, pode utilizar o código descrito [aqui](media-services-dotnet-manage-entities.md).

## <a name="january-2017-release"></a>Versão de Janeiro de 2017

No Microsoft Azure dos Media Services (AMS), um **ponto final de transmissão em fluxo** representa um serviço de transmissão em fluxo que pode proporcionar o conteúdo diretamente a uma aplicação de leitor de cliente ou a rede de entrega um conteúdos (CDN) para uma maior distribuição. Os Media Services também fornecem uma integração perfeita CDN do Azure. O fluxo de saída de um serviço de StreamingEndpoint pode ser uma transmissão em fluxo em direto, um vídeo a pedido ou transferência progressiva do seu elemento na sua conta de Media Services. Cada conta de Media Services do Azure inclui um predefinido StreamingEndpoint. Lidam adicionais pode ser criadas com a conta. Existem duas versões do lidam, 1.0 e 2.0. A partir de com 10 de Janeiro de 2017, as contas recentemente criadas do AMS irão incluir versão 2.0 **predefinido** StreamingEndpoint. Os pontos finais transmissão em fluxo adicionais que adicionar a esta conta também será versão 2.0. Esta alteração não irá afetar as contas existentes; lidam existente será versão 1.0 e pode ser atualizados para a versão 2.0. Com esta alteração vão ocorrer alterações de comportamento, faturação e funcionalidade (para obter mais informações, consulte [isto](media-services-streaming-endpoints-overview.md) artigo).

Além disso, começando com a versão 2.15, Media Services do Azure adicionadas as seguintes propriedades para a entidade de ponto final de transmissão em fluxo: **CdnProvider**, **CdnProfile**, **FreeTrialEndTime**, **StreamingEndpointVersion**. Para uma descrição geral detalhada destas propriedades, consulte [isto](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint). 

## <a name="december-2016-release"></a>Versão de Dezembro de 2016

Media Services do Azure agora permite-lhe aceder a dados de telemetria/métricas para os respetivos serviços. A versão atual do AMS permite-lhe recolher dados de telemetria de canais em direto, StreamingEndpoint, e em direto entidades de arquivo. Para obter mais informações, consulte [isto](media-services-telemetry-overview.md) artigo.

## <a id="july_changes16"></a>Versão de Julho de 2016
### <a name="updates-to-manifest-file-ism-generated-by-encoding-tasks"></a>Atualizações para o ficheiro de manifesto (*. ISM) gerado por tarefas de codificação
Quando uma tarefa de codificação foi submetida para o codificador de multimédia Standard ou o codificador de multimédia do Azure, a tarefa de codificação gera um [ficheiro de manifesto de transmissão em fluxo](media-services-deliver-content-overview.md) (* ISM) ficheiro no resultado do recurso. Com a versão mais recente do serviço, a sintaxe deste ficheiro de manifesto de transmissão em fluxo foi atualizada.

> [!NOTE]
> A sintaxe do ficheiro de manifesto (ISM) transmissão em fluxo é reservada para uso interno e está sujeita a alterações em futuros lançamentos. Não modifique ou manipular os conteúdos deste ficheiro.
> 
> 

### <a name="a-new-client-manifest-ismc-file-is-generated-in-the-output-asset-when-an-encoding-task-outputs-one-or-more-mp4-files"></a>Um manifesto de cliente novo (*. Ficheiro ISMC) é gerado na saída Asset quando uma tarefa de codificação produz um ou mais ficheiros MP4
A partir da versão mais recente do serviço, após a conclusão de uma tarefa de codificação que gera um ficheiros MP4 mais, a saída Asset também irá conter um ficheiro de manifesto (*.ismc) transmissão em fluxo do cliente. O ficheiro .ismc ajuda a melhorar o desempenho de transmissão em fluxo dinâmico. 

> [!NOTE]
> A sintaxe do ficheiro de manifesto (.ismc) do cliente é reservada para uso interno e está sujeita a alterações em futuros lançamentos. Não modifique ou manipular os conteúdos deste ficheiro.
> 
> 

Para obter mais informações, consulte [isto](https://blogs.msdn.microsoft.com/randomnumber/2016/07/08/encoder-changes-within-azure-media-services-now-create-ismc-file/) blogue.

### <a name="known-issues"></a>Problemas conhecidos
Alguns clientes podem ter através de um problema de repetições etiqueta no manifesto de transmissão em fluxo uniforme. Para obter mais informações, veja [esta](media-services-deliver-content-overview.md#known-issues) secção.

## <a id="apr_changes16"></a>Versão de Abril de 2016
### <a name="azure-media-analytics"></a>Análise de multimédia do Azure
Media Services do Azure introduzida análise de multimédia do Azure para intelligence vídeo poderoso. Para obter informações detalhadas, consulte [descrição geral do Azure Media Services análise](media-services-analytics-overview.md).

### <a name="apple-fairplay-preview"></a>Apple FairPlay (pré-visualização)
Media Services do Azure agora permite-lhe encriptar de forma dinâmica a sua HTTP Live Streaming (HLS) conteúdo do FairPlay da Apple. Também pode utilizar o serviço de entrega de licença do AMS para fornecer licenças do FairPlay aos clientes. Para obter mais informações, consulte [Media Services do Azure utilizado para transmitir o seu HLS conteúdo protegido do FairPlay da Apple ](media-services-protect-hls-with-fairplay.md).

## <a id="feb_changes16"></a>Versão de Fevereiro de 2016
A versão mais recente do Azure SDK de Media Services para .NET (3.5.3) contém uma correção de erros relacionada Widevine. Foi o problema: não foi reutilizada AssetDeliveryPolicy para vários recursos encriptados com Widevine. Como parte desta correção de erros da seguinte propriedade foi adicionada ao SDK: **WidevineBaseLicenseAcquisitionUrl**.

    Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
        new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
    {
        {AssetDeliveryPolicyConfigurationKey.WidevineBaseLicenseAcquisitionUrl,"http://testurl"},

    };

## <a id="jan_changes_16"></a>Versão de Janeiro de 2016
Unidades codificação reservada mudado para reduzir confusões com nomes de codificador.

As unidades de reservado codificação básicas, Standard e Premium forem mudadas para S1, S2, e unidades reservadas de S3, respetivamente.  Clientes que utilizam básico RUs codificação hoje Verão S1 como a etiqueta no portal do Azure (e na factura) ao padrão e Premium verá as etiquetas S2 e S3, respetivamente. 

## <a id="dec_changes_15"></a>Versão de Dezembro de 2015

### <a name="azure-media-encoder-deprecation-announcement"></a>Anúncio de preterição codificador de multimédia do Azure

Codificador de multimédia do Azure vão ser preterida a partir de aproximadamente 12 meses a partir da versão de codificador de multimédia Standard.

### <a name="azure-sdk-for-php"></a>SDK do Azure para PHP
A equipa do Azure SDK publicado uma nova versão do [Azure SDK para PHP](http://github.com/Azure/azure-sdk-for-php) pacote que contém as atualizações e novas funcionalidades para os Media Services do Microsoft Azure. Em particular, o SDK de serviços de suporte de dados do Azure para PHP agora suporta a versão mais recente [conteúdo proteção](media-services-content-protection-overview.md) funcionalidades: encriptação dinâmica com AES e DRM (PlayReady e Widevine) com e sem restrição de Token. Também suporta dimensionamento [unidades de codificação](media-services-dotnet-encoding-units.md).

Para obter mais informações, consulte:

* O [SDK do Microsoft Azure Media Services para PHP](http://southworks.com/blog/2015/12/09/new-microsoft-azure-media-services-sdk-for-php-release-available-with-new-features-and-samples/) blogue.
* O seguinte [exemplos de código](http://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices) para ajudar a começar rapidamente:
  * **vodworkflow_aes.php**: Este é um ficheiro PHP que mostra como utilizar a encriptação dinâmica AES-128 e o serviço de entrega de chave. Se baseia no exemplo .NET explicado [isto](media-services-protect-with-aes128.md) artigo.
  * **vodworkflow_aes.php**: Este é um ficheiro PHP que mostra como utilizar a encriptação dinâmica PlayReady e o serviço de entrega de licença. Se baseia no exemplo .NET explicado [isto](media-services-protect-with-drm.md) artigo.
  * **scale_encoding_units.php**: Este é um ficheiro PHP que mostra como dimensionar codificação unidade reservada.

## <a id="nov_changes_15"></a>Versão de Novembro de 2015
Media Services do Azure oferece agora o serviço de entrega de licença Widevine da Google na nuvem. Para obter mais detalhes, consulte [este blogue de anúncio](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/). Além disso, consulte [neste tutorial](media-services-protect-with-drm.md) e [repositório do GitHub](http://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm). 

Tenha em atenção que os serviços de entrega de licença Widevine fornecidos pelos serviços de suporte de dados do Azure está em pré-visualização. Para obter mais informações consulte [este blogue](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/).

## <a id="oct_changes_15"></a>Versão de Outubro de 2015
Serviços de suporte de dados do Azure (AMS) está agora em direto nos seguintes centros de dados: sul do Brasil, Índia Ocidental, Sul da Índia e Índia Central. Agora, pode utilizar o portal do Azure para [criar contas de serviço de multimédia](media-services-portal-create-account.md) e realizar várias tarefas descritas [aqui](https://azure.microsoft.com/documentation/services/media-services/). No entanto, o Live Encoding não está ativado nestes centros de dados. Além disso, nem todos os tipos de Unidades de Codificação Reservadas estão disponíveis nestes centros de dados.

* Sul do Brasil:                                          Estão disponíveis apenas Unidades de Codificação reservadas Básicas e Standard
* Índia Ocidental, Sul da Índia e Índia Central:              Estão disponíveis apenas Unidades de Codificação Reservadas Básicas

## <a id="september_changes_15"></a>Versão de Setembro de 2015
* Agora, AMS oferece a capacidade de proteger tanto as vídeo a pedido (VOD) em direto fluxos com a tecnologia de Widevine DRM modulares. Pode utilizar os seguintes parceiros de serviços de entrega para o ajudar a fornecer licenças Widevine: [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/), [castLabs](http://castlabs.com/company/partners/azure/). Para obter mais informações, consulte [este blogue](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/).
  
    Pode utilizar [SDK .NET do AMS](https://www.nuget.org/packages/windowsazure.mediaservices/) (começando com a versão 3.5.1) ou a API REST para configurar o seu AssetDeliveryConfiguration para utilizar Widevine.  
* AMS suporte adicionado para Apple ProRes vídeos. Pode agora carregar os ficheiros de vídeos de origem QuickTime utilizam Apple ProRes ou outros codecs. Para obter mais informações, consulte [este blogue](https://azure.microsoft.com/blog/announcing-support-for-apple-prores-videos-in-azure-media-services/).
* Agora, pode utilizar codificador de multimédia Standard para extração de arquivo subplano recorte e em direto. Para obter mais informações, consulte [este blogue](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/).
* Foram efetuadas as seguintes atualizações de filtragem: 
  
  * Agora, pode utilizar o formato de Apple HTTP Live Streaming (HLS) com o filtro só de áudio. Esta atualização permite-lhe remover controlar só de áudio, especificando (só de áudio = false) no URL.
  * Quando definir os filtros para os recursos, tem agora capacidade para combinar múltiplos (até 3) filtros num URL único.
    
    Para obter mais informações consulte [isto](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) blogue.
* AMS suporta agora o posso-Frames Jumbo HLS v4. Suporte de I-Frame otimiza fast-forward e rewind operações. Por predefinição, todas as saídas de v4 HLS incluem listas de reprodução de I-Frame (EXT-X-I-FRAME-STREAM-INF).
  
    Para obter mais informações consulte [isto](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) blogue.

## <a id="august_changes_15"></a>Versão de Agosto de 2015
* Azure SDK de Media Services para Java V0.8.0 versão e amostras novo estão agora disponíveis. Para obter mais informações, consulte:
  
  * [Mensagem de blogue](http://southworks.com/blog/2015/08/25/microsoft-azure-media-services-sdk-for-java-v0-8-0-released-and-new-samples-available/)
  * [Repositório de exemplos de Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
* Azure Media Player a atualização com suporte de sequência de áudio múltiplos. Para obter mais informações, consulte:
  * [Mensagem de blogue](https://azure.microsoft.com/blog/2015/08/13/azure-media-player-update-with-multi-audio-stream-support/)

## <a id="july_changes_15"></a>Versão de Julho de 2015
* Anunciar a disponibilidade geral do codificador de multimédia Standard. Para obter mais informações, consulte [esta mensagem de blogue](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/).
  
    Codificador de multimédia Standard utiliza predefinições descritas [isto](http://go.microsoft.com/fwlink/?LinkId=618336) secção. Tenha em atenção que ao utilizar uma predefinição para codifica 4K, deve ter o **Premium** reservados de um tipo de unidade. Para obter mais informações, consulte [como codificação de escala](media-services-scale-media-processing-overview.md).
* Legendas em tempo real em direto com Media Services do Azure e leitor. Para obter mais informações, consulte [esta mensagem de blogue](https://azure.microsoft.com/blog/2015/07/08/live-real-time-captions-with-azure-media-services-and-player/)

### <a name="media-services-net-sdk-updates"></a>Atualizações SDK do .NET dos serviços de suporte de dados
SDK do .NET de serviços de suporte de dados do Azure está agora versão 3.4.0.0. A seguinte funcionalidade foi adicionada nesta versão:  

* Suporte implementado para arquivo em direto. Tenha em atenção que não é possível transferir um elemento que contenha um arquivo em direto.
* Suporte implementado para filtros dinâmicos.
* Funcionalidade implementada que permite aos utilizadores manter o contentor de armazenamento ao eliminar o recurso.
* Correções de erros relacionados com a repetir as políticas de canais.
* Ativado **o fluxo de trabalho do suporte de dados codificador Premium**.

## <a id="june_changes_15"></a>Versão de Junho de 2015
### <a name="media-services-net-sdk-updates"></a>Atualizações SDK do .NET dos serviços de suporte de dados
SDK do .NET de serviços de suporte de dados do Azure está agora versão 3.3.0.0. A seguinte funcionalidade foi adicionada nesta versão:  

* suporte para o spec OpenId Connect deteção,
* suporte para processamento de rollover de chaves no lado do fornecedor de identidade. 

Se estiver a utilizar um fornecedor de identidade que expõe o documento de identificação OpenID Connect (tal como os seguintes fornecedores: Azure Active Directory, Google, Salesforce), pode instruir os Media Services do Azure para obter as chaves de assinatura de validação de JWT token de OpenID ligar a especificação de deteção. 

Para obter mais informações, consulte [utilizando Json Web as chaves de especificação de deteção OpenID Connect para trabalhar com JWT token autenticação nos serviços de suporte de dados do Azure](http://gtrifonov.com/2015/06/07/using-json-web-keys-from-openid-connect-discovery-spec-to-work-with-jwt-token-authentication-in-azure-media-services/).

## <a id="may_changes_15"></a>Versão de Maio de 2015
Anunciar as seguintes novas funcionalidades:

* [Uma versão de pré-visualização de Live Encoding com Media Services](media-services-manage-live-encoder-enabled-channels.md)
* [Manifesto dinâmico](media-services-dynamic-manifest-overview.md)
* [Uma pré-visualização do processador de multimédia Hyperlapse de multimédia do Azure](https://azure.microsoft.com/blog/?p=286281&preview=1&_ppp=61e1a0b3db)

## <a id="april_changes_15"></a>Versão de Abril de 2015
### <a name="general-media-services-updates"></a>Atualizações dos serviços de suporte de dados gerais
* [Anunciar o leitor de Media Services do Azure](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/).
* Começando com 2.10 de REST de serviços de suporte de dados, canais que estão configurados para a ingestão de um protocolo RTMP, são criadas com o site primário e secundário os URLs de inserção. Para obter mais informações, consulte [configurações de inserção do canal](media-services-live-streaming-with-onprem-encoders.md#channel_input)
* Atualizações do indexador de suporte de dados do Azure
* Suporte para idioma espanhol
* Novo formato do xml de configuração

Para obter mais informações consulte [este blogue](https://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/).

### <a name="media-services-net-sdk-updates"></a>Atualizações SDK do .NET dos serviços de suporte de dados
SDK do .NET de serviços de suporte de dados do Azure está agora a versão 3.2.0.0.

Seguem-se algumas das atualizações de cliente:

* **Alteração de última hora**: alterado **TokenRestrictionTemplate.Issuer** e **TokenRestrictionTemplate.Audience** seja do tipo cadeia.
* Atualizações relacionados com a criação personalizada Repita políticas.
* Correções de erros relacionados com a carregar/transferência de ficheiros.
* O **MediaServicesCredentials** classe agora aceita o ponto final de controlo de acesso primária e secundária para se autenticarem nas.

## <a id="march_changes_15"></a>Versão de Março de 2015
### <a name="general-media-services-updates"></a>Atualizações dos serviços de suporte de dados gerais
* Os Media Services fornecem agora integração da CDN do Azure. Para suportar a integração, o **CdnEnabled** propriedade foi adicionada ao **StreamingEndpoint**.  **CdnEnabled** pode ser utilizado com as APIs REST a partir da versão 2.9 (para obter mais informações, consulte [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint)).  **CdnEnabled** pode ser utilizado com o SDK .NET partir da versão 3.1.0.2 (para obter mais informações, consulte [StreamingEndpoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mediaservices.client.istreamingendpoint\(v=azure.10\).aspx)).
* Anúncio de **o fluxo de trabalho do suporte de dados codificador Premium**. Para obter mais informações, consulte [Introducing Premium codificação na Media Services do Azure](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/).

## <a id="february_changes_15"></a>Versão de Fevereiro de 2015
### <a name="general-media-services-updates"></a>Atualizações dos serviços de suporte de dados gerais
API de REST dos serviços de suporte de dados está agora versão 2.9. Começando com esta versão, pode ativar a integração da CDN do Azure com os pontos finais de transmissão em fluxo. Para obter mais informações, consulte [StreamingEndpoint](https://msdn.microsoft.com/library/dn783468.aspx).

## <a id="january_changes_15"></a>Versão de Janeiro de 2015
### <a name="general-media-services-updates"></a>Atualizações dos serviços de suporte de dados gerais
Anúncio de disponibilidade geral (GA) de proteção de conteúdos com a encriptação dinâmica. Para obter mais informações, consulte [Media Services do Azure melhora a segurança de transmissão em fluxo com a tecnologia de disponibilidade geral da DRM](https://azure.microsoft.com/blog/2015/01/29/azure-media-services-enhances-streaming-security-with-general-availability-of-drm-technology/).

### <a name="media-services-net-sdk-updates"></a>Atualizações SDK do .NET dos serviços de suporte de dados
SDK do .NET de serviços de suporte de dados do Azure está agora versão 3.1.0.1.

Esta versão marcado o construtor de Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization.TokenRestrictionTemplate predefinido como obsoleto. Este novo construtor aceita TokenType como um argumento.

    TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);


## <a id="december_changes_14"></a>Versão de Dezembro de 2014
### <a name="general-media-services-updates"></a>Atualizações dos serviços de suporte de dados gerais
* Algumas atualizações e novas funcionalidades adicionadas para o processador de multimédia de indexador de Azure. Para obter mais informações, consulte [notas de lançamento da versão de indexador de suporte de dados do Azure 1.1.6.7](https://azure.microsoft.com/blog/2014/12/03/azure-media-indexer-version-1-1-6-7-release-notes/).
* Adicionar uma nova API de REST, que permite atualizar codificação unidades reservadas: [EncodingReservedUnitType com REST](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype).
* Suporte de CORS adicionados para o serviço de entrega de chave.
* Foram efetuadas as melhorias de desempenho da consulta de opções de política de autorização.
* No Centro de dados da China, o [URL de entrega de chave](https://docs.microsoft.com/rest/api/media/operations/contentkey#get_delivery_service_url) é agora por cliente (tal como outros centros de dados).
* Duração de destino do automática HLS adicionada. Ao efetuar a transmissão em fluxo em direto, HLS é sempre empacotada dinamicamente. Por predefinição, os Media Services calcula automaticamente HLS segmento empacotamento rácio (FragmentsPerSegment) com base num intervalo de keyframe (KeyFrameInterval), também referido como um grupo de imagens – GOP, que é recebida do codificador em direto. Para obter mais informações, consulte [trabalhar com o Azure Media Services em direto de transmissão em fluxo].

### <a name="media-services-net-sdk-updates"></a>Atualizações SDK do .NET dos serviços de suporte de dados
* [SDK .NET do Azure Media Services](http://www.nuget.org/packages/windowsazure.mediaservices/) está agora versão 3.1.0.0.
* Atualizar a dependência do SDK do .net para o .NET 4.5 Framework.
* Adicionar uma nova API que permite atualizar unidades reservadas de codificação. Para obter mais informações, consulte [atualizar o tipo de unidade reservado e aumentar a codificação RUs através do .NET](media-services-dotnet-encoding-units.md).
* Suporte adicionado JWT (JSON Web Token) para autenticação de token. Para obter mais informações, consulte [autenticação de token JWT nos Media Services do Azure e a encriptação dinâmica](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).
* Foram adicionados relativos desvios para BeginDate e ExpirationDate no modelo de licença PlayReady.

## <a id="november_changes_14"></a>Versão de Novembro de 2014
* Os Media Services agora permite-lhe para a ingestão de conteúdo de transmissão em fluxo uniforme (FMP4) em direto através de uma ligação SSL. Para a ingestão através de SSL, certifique-se atualizar o URL de inserção para HTTPS.  Tenha em atenção que, atualmente, AMS não suporta SSL com domínios personalizados.  Para obter mais informações sobre a transmissão em fluxo em direto, consulte [trabalhar com o Azure Media Services em direto de transmissão em fluxo].
* Atualmente, não é possível inserir um fluxo em direto RTMP através de uma ligação SSL.
* Apenas pode transmitir através de SSL se o ponto de final transmissão em fluxo a partir da qual a fornecer o conteúdo foi criado depois de 10 de Setembro de 2014. Se os URLs de transmissão em fluxo são baseados nos pontos finais de transmissão em fluxo criados após 10 de Setembro, o URL contém "streaming.mediaservices.windows.net" (o novo formato). URL de transmissão em fluxo que contenham "origin.mediaservices.windows.net" (o formato antigo) não suportam SSL. Se o seu URL tem um formato antigo e pretender ser capaz de transmitir através de SSL, [criar um novo ponto de final de transmissão em fluxo](media-services-portal-manage-streaming-endpoints.md). Utilize URLs criados com base no novo ponto de final de transmissão em fluxo para transmitir o seu conteúdo através de SSL.

## <a id="october_changes_14"></a>Versão de Outubro de 2014
### <a id="new_encoder_release"></a>Versão de codificador de serviços de multimédia
Anunciar a nova versão do codificador de multimédia do suporte de dados de serviços do Azure. Com a versão mais recente codificador de multimédia do Azure lhe é cobrados apenas para GBs de saída, mas caso contrário, o codificador nova é a funcionalidade compatível com o codificador anterior. Para obter mais informações [detalhes de preços de serviços de suporte de dados]).

### <a id="oct_sdk"></a>.NET SDK dos Media Services
SDK de Media Services para .NET extensões está agora versão 2.0.0.3.

SDK de Media Services para .NET é agora versão 3.0.0.8.

Foram efetuadas as seguintes alterações:

* Refactoring em classes de política de repetição.
* Cadeia de agente de utilizador a adicionar a cabeçalhos de pedido de http.
* A adicionar o passo de compilação de restauro do nuget.
* Corrigir os testes de cenário para utilizar x509 cert do repositório.
* A validar as definições ao atualizar o canal e final de transmissão em fluxo.

### <a name="new-github-repository-to-host-media-services-samples"></a>Novo repositório do GitHub para exemplos de Media Services do anfitrião
Amostras estão localizadas em [repositório do GitHub de amostras de Media Services do Azure](https://github.com/Azure/Azure-Media-Services-Samples).

## <a id="september_changes_14"></a>Versão de Setembro de 2014
Metadados de REST de serviços de suporte de dados estão agora versão 2.7. Para obter mais informações sobre as atualizações mais recentes do REST, consulte [referência da API REST do Azure Media Services].

SDK de Media Services para .NET é agora versão 3.0.0.7

### <a id="sept_14_breaking_changes"></a>As alterações de última hora
* **Origem** nome foi mudado para [StreamingEndpoint].
* Uma alteração no comportamento predefinido ao utilizar o **portal do Azure** de codificar e, em seguida, publicar ficheiros MP4.

Anteriormente, quando utilizar o portal clássico do Azure para publicar um URL SAS um único ficheiro MP4 vídeo elemento seria possível criar (URLs de SAS permitem-lhe transferir o vídeo a partir de um armazenamento de BLOBs). Atualmente, quando utilizar o portal clássico do Azure para codificar e, em seguida, publicar um elemento de vídeo único ficheiro MP4, o URL do gerado aponta para um ponto final de transmissão em fluxo do Azure Media Services.  Esta alteração não afeta vídeos MP4 que são carregados nos serviços de suporte de dados diretamente e publicados sem ser codificado por Media Services do Azure.

Atualmente, tem duas opções seguintes para resolver o problema.

* Ativar unidades de transmissão em fluxo e utilizar o empacotamento dinâmico para transmitir o elemento de mp4 como uma apresentação de transmissão em fluxo uniforme.
* Crie um url SAS para o mp4 transferir (ou em progressivamente reproduzir). Para obter mais informações sobre como criar um localizador SAS, consulte [entrega de conteúdos].

### <a id="sept_14_GA_changes"></a>Novos funcionalidades/cenários que fazem parte da versão GA
* **Processador de multimédia de indexador**. Para obter mais informações consulte [indexação ficheiros de suporte de dados com o indexador de suporte de dados do Azure].
* O [StreamingEndpoint] entidade agora permite-lhe adicionar nomes de domínio personalizado (anfitrião).
  
    Para um nome de domínio personalizado ser utilizado como o nome do ponto final de transmissão em fluxo Media Services, terá de adicionar nomes de anfitrião personalizado para o ponto final de transmissão em fluxo. Utilize os APIs de REST de serviços de suporte de dados ou o .NET SDK para adicionar nomes de anfitrião personalizado.
  
    Aplicam as seguintes considerações:
  
  * Tem de ter a propriedade de nome de domínio personalizado.
  * A propriedade do nome de domínio tem de ser validada por Media Services do Azure. Para validar o domínio, crie um CName que mapeie <MediaServicesAccountId>.<parent domain> Para verificar o dns. < zona de dns de mediaservices >. 
  * Tem de criar outro CName que mapeia o nome de anfitrião personalizado (por exemplo, sports.contoso.com) para o nome de anfitrião do seu StreamingEndpont de serviços de suporte de dados (por exemplo, amstest.streaming.mediaservices.windows.net).

    Para obter mais informações, consulte o **CustomHostNames** propriedade no [StreamingEndpoint] artigo.

### <a id="sept_14_preview_changes"></a>Novos funcionalidades/cenários que fazem parte da versão de pré-visualização pública
* Em direto pré-visualização de transmissão em fluxo. Para obter mais informações, consulte [trabalhar com o Azure Media Services em direto de transmissão em fluxo].
* Serviço de entrega de chave. Para obter mais informações, consulte [utilizando a encriptação dinâmica de AES-128 e o serviço de entrega de chave].
* Encriptação dinâmica AES. Para obter mais informações, consulte [utilizando a encriptação dinâmica de AES-128 e o serviço de entrega de chave].
* Serviço de entrega de licença PlayReady. Para obter mais informações, consulte [utilizando a encriptação dinâmica de PlayReady e o serviço de entrega de licença].
* Encriptação PlayReady dinâmico. Para obter mais informações, consulte [utilizando a encriptação dinâmica de PlayReady e o serviço de entrega de licença].
* O modelo de licença PlayReady de serviços de multimédia. Para obter mais informações, consulte [descrição de geral do suporte de dados dos serviços PlayReady licença modelo].
* Armazenamento de transmissão em fluxo encriptada ativos. Para obter mais informações, consulte [encriptados conteúdo da armazenamento de transmissão em fluxo].

## <a id="august_changes_14"></a>Versão de Agosto de 2014
Quando codificar um elemento, um elemento de saída é produzido após a conclusão da tarefa de codificação. Até que esta versão, o codificador de serviços de multimédia do Azure produziu metadados sobre recursos de saída. Também a partir desta versão codificador produz metadados sobre recursos de entrada. Para obter mais informações, consulte o [metadados da entrada] e [metadados de saída] artigos.

## <a id="july_changes_14"></a>Versão de Julho de 2014
Foram efetuadas as seguintes correções de erros para a Packager de serviços de suporte de dados do Azure e o encriptador:

* Áudio apenas desempenha voltando transmuxing um recurso de arquivo em direto para HTTP Live Streaming – Esta correção e agora são reproduzidas áudio e vídeo.
* Quando o empacotamento um recurso para transmissão em fluxo em direto HTTP e a encriptação de envelope AES de 128 bits, os fluxos de em pacote não reproduzir em dispositivos Android – corrigir este erro e a sequência em pacote reproduz em dispositivos Android que suportem HTTP Live Streaming.

## <a id="may_changes_14"></a>Versão de Maio de 2014
### <a id="may_14_changes"></a>Atualizações dos serviços de suporte de dados gerais
Agora, pode utilizar [empacotamento dinâmico] fluxo HTTP Live Streaming (HLS) v3. Para transmitir em fluxo HLS v3, adicione o seguinte formato para o caminho de localização de origem: * .ism/manifest(format=m3u8-aapl-v3). Para obter mais informações, consulte [blogue de Nick Drouin].

Empacotamento dinâmico agora também suporta a entrega HLS (v3 e v4) encriptado com PlayReady com base na transmissão em fluxo uniforme estaticamente encriptado com PlayReady. Para obter informações sobre como encriptar a transmissão em fluxo uniforme com PlayReady, consulte [Protecting transmissão em fluxo uniforme com PlayReady].

### <a name="may_14_donnet_changes"></a>Atualizações SDK do .NET dos serviços de suporte de dados
As seguintes melhorias estão incluídas na versão SDK .NET dos Media Services 3.0.0.5:

* Maior velocidade e resiliência de recursos de suporte de dados de carregamento/transferência.
* Melhoramentos no processamento de exceções de lógica e os transitório de repetição: 
  
  * Lógica de deteção e volte a tentar erro transitório foram melhoradas para exceções são causadas por consultar, a guardar as alterações, carregamento ou transferência de ficheiros. 
  * Ao obter Web exceções (por exemplo, durante um pedido de token ACS), irá reparar que registaram erros fatais estão a falhar mais rapidamente agora.

Para obter mais informações, consulte [repetir lógica o SDK de Media Services para .NET].

## <a id="april_changes_14"></a>Versão de codificador de Abril de 2014
### <a name="april_14_enocer_changes"></a>Atualizações de codificador de serviços de multimédia
* Suporte adicionado para ingestão relacionadas ficheiros AVI criados utilizando o editor de nonlinear Grass Valley EDIUS, em que o vídeo é ligeiramente comprimida utilizando codec Grass Valley HQ/HQX. Para obter mais informações, consulte [Grass Valley Announces EDIUS 7 de transmissão em fluxo através de Cloud].
* Suporte adicionado para especificar a Convenção de nomenclatura para os ficheiros produzido pelo codificador de multimédia. Para obter mais informações, consulte [controlar o suporte de dados de serviço codificador saída os nomes de ficheiro].
* Adicionado suporte para a sobreposição de vídeo e/ou áudio. Para obter mais informações, consulte [criar sobrepõe].
* Suporte adicionado para stitching em conjunto vários segmentos de vídeos. Para obter mais informações, consulte [Stitching segmentos de vídeo].
* Corrigido um erro relacionado com transcodificação de MP4s onde áudio tem foi codificado com MPEG-1 áudio camada 3 (também conhecido como MP3).

## <a id="jan_feb_changes_14"></a>Versões do Janeiro/Fevereiro de 2014
### <a name="jan_fab_14_donnet_changes"></a>SDK .NET 3.0.0.1, 3.0.0.2 e 3.0.0.3 de serviços de multimédia do Azure
As alterações 3.0.0.1 e 3.0.0.2 incluem:

* Fixos problemas relacionados com a utilização de consultas LINQ com declarações de OrderBy.
* Dividir soluções de teste no [GitHub] na unidade com base em testes e testes com base no cenário.

Para obter mais detalhes sobre as alterações, consulte: [Azure .NET SDK de Media Services 3.0.0.1 e 3.0.0.2 versões].

As seguintes alterações foram efetuadas 3.0.0.3:

* Atualizar as dependências de armazenamento do Azure para utilizar a versão 3.0.3.0. 
* Corrigir o problema de compatibilidade com versões anteriores para 3.0. *.* versões. 

## <a id="december_changes_13"></a>Versão de Dezembro de 2013
### <a name="dec_13_donnet_changes"></a>.NET SDK 3.0.0.0 de serviços de multimédia do Azure
> [!NOTE]
> 3.0.x.x versões não são compatíveis com versões anteriores com as versões de 2.4.x.x.
> 
> 

A versão mais recente do SDK de serviços de suporte de dados está agora 3.0.0.0. Pode transferir o pacote mais recente a partir do Nuget ou obter os bits de [GitHub].

Começando com o SDK de Media Services versão 3.0.0.0, pode reutilizar o [do Azure Active Directory acesso Control Service (ACS)] tokens. Para obter mais informações, consulte a secção "Reutilizar controlo serviço Tokens de acesso" o [ligar aos Media Services com o SDK de Media Services para .NET] artigo.

### <a name="dec_13_donnet_ext_changes"></a>Extensões do SDK do .NET 2.0.0.0 de serviços de multimédia do Azure
As extensões de SDK .NET dos serviços do Azure suporte de dados é um conjunto de métodos de extensão e funções de programa auxiliar que irão simplificar o seu código e facilitar o desenvolvimento com os Media Services do Azure. Pode obter os bits mais recentes do [extensões do SDK .NET do Azure suporte de dados de serviços].

## <a id="november_changes_13"></a>Versão de Novembro de 2013
### <a name="nov_13_donnet_changes"></a>As alterações SDK do .NET de serviços de multimédia do Azure
Começando com esta versão, o SDK de Media Services para .NET processa falha transitória os erros que podem ocorrer ao efetuar chamadas à camada de API de REST dos serviços de suporte de dados.

## <a id="august_changes_13"></a>Versão de Agosto de 2013
### <a name="aug_13_powershell_changes"></a>Cmdlets do PowerShell de serviços de suporte de dados incluídas nas ferramentas do Azure SDK
Os seguintes cmdlets PowerShell de serviços de suporte de dados estão agora incluídos nas [ferramentas do sdk do azure].

* Get-AzureMediaServices 
  
    Por exemplo, `Get-AzureMediaServicesAccount`.
* New-AzureMediaServicesAccount 
  
    Por exemplo, `New-AzureMediaServicesAccount -Name “MediaAccountName” -Location “Region” -StorageAccountName “StorageAccountName”`.
* New-AzureMediaServicesKey 
  
    Por exemplo, `New-AzureMediaServicesKey -Name “MediaAccountName” -KeyType Secondary -Force`.
* Remove-AzureMediaServicesAccount 
  
    Por exemplo, `Remove-AzureMediaServicesAccount -Name “MediaAccountName” -Force`.

## <a id="june_changes_13"></a>Versão de Junho de 2013
### <a name="june_13_general_changes"></a>Alterações de Media Services do Azure
As alterações mencionadas nesta secção são as atualizações incluídas em versões do serviços de suporte de dados de Junho de 2013.

* Capacidade para ligar a várias contas de armazenamento para uma conta de serviço de suporte de dados. 
  
    StorageAccount
  
    Asset.StorageAccountName e Asset.StorageAccount
* Capacidade para atualizar Job.Priority. 
* Notificação relacionado com a entidades e propriedades: 
  
    JobNotificationSubscription
  
    NotificationEndPoint
  
    Tarefa
* Asset.Uri 
* Locator.Name 

### <a name="june_13_dotnet_changes"></a>Alterações do SDK .NET dos Media Services do Azure
As seguintes alterações estão incluídas em Junho de 2013 liberta o SDK de Media Services. O SDK de Media Services mais recente está disponível no GitHub.

* Começando com a versão 2.3.0.0, suporta o SDK de Media Services ligar o armazenamento várias contas para uma conta de Media Services. As APIs seguintes suportam esta funcionalidade:
  
    O tipo de IStorageAccount.
  
    A propriedade Microsoft.WindowsAzure.MediaServices.Client.CloudMediaContext.StorageAccounts.
  
    A propriedade StorageAccount.
  
    A propriedade StorageAccountName.
  
    Para obter mais informações, consulte [gerir recursos de serviços de suporte de dados em várias contas do Storage].
* Notificação relacionados com APIs. Começando com a versão 2.2.0.0 que tem a capacidade para escutar notificações de armazenamento de filas do Azure. Para obter mais informações, consulte [processamento de notificações de tarefa de serviços de suporte de dados].
  
    A propriedade Microsoft.WindowsAzure.MediaServices.Client.IJob.JobNotificationSubscriptions.
  
    O tipo de Microsoft.WindowsAzure.MediaServices.Client.INotificationEndPoint.
  
    O tipo de Microsoft.WindowsAzure.MediaServices.Client.IJobNotificationSubscription.
  
    O tipo de Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointCollection.
  
    O tipo de Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointType.
  
    O tipo de Microsoft.WindowsAzure.MediaServices.Client.NotificationJobState.
* Dependência no cliente do Storage do Azure SDK 2.0 (Microsoft.WindowsAzure.StorageClient.dll).
* Dependência de OData 5.5 (Microsoft.Data.OData.dll).

## <a id="december_changes_12"></a>Versão de Dezembro de 2012
### <a name="dec_12_dotnet_changes"></a>Alterações do SDK .NET dos Media Services do Azure
* O IntelliSense: Adicionada em falta documentação Intellisense para muitos tipos.
* Microsoft.Practices.TransientFaultHandling.Core: Corrigido um problema onde o SDK ainda tinha uma dependência de uma versão antiga desta assemblagem. O SDK agora referencia a versão 5.1.1209.1 desta assemblagem.

Correções para problemas encontrados na Novembro de 2012 SDK:

* IAsset.Locators.Count: Esta contagem é agora corretamente comunicada no novo IAsset interfaces depois de todos os localizadores tem sido eliminados.
* IAssetFile.ContentFileSize: Este valor é agora corretamente definido após um carregamento por IAssetFile.Upload(filepath).
* IAssetFile.ContentFileSize: Esta propriedade agora pode ser definida quando criar um ficheiro de recurso. Foi anteriormente só de leitura.
* IAssetFile.Upload(filepath): Corrigido um problema em que este método de carregamento síncronos foi argumentoutofrangeexception o seguinte erro ao carregar vários ficheiros para o elemento. O erro foi "Falha ao autenticar o pedido de servidor. Certifique-se de que o valor de cabeçalho de autorização está formado corretamente, incluindo a assinatura."
* IAssetFile.UploadAsync: Corrigido um problema onde não mais de 5 ficheiros foi carregados em simultâneo.
* IAssetFile.UploadProgressChanged: Este evento é agora fornecido pelo SDK.
* IAssetFile.DownloadAsync (cadeia, BlobTransferClient, ILocator, CancellationToken): Esta sobrecarga de método é agora fornecida.
* IAssetFile.DownloadAsync: Corrigido um problema onde não mais de 5 ficheiros foi transferidos em simultâneo.
* IAssetFile.Delete(): Corrigido um problema onde chamar delete pode acionar uma excepção se nenhum ficheiro foi carregado para o IAssetFile.
* Tarefas: Corrigido um problema onde encadeamento um "MP4 a tarefa de fluxos uniforme" com um "PlayReady a tarefa de proteção" através de um modelo de tarefa não criaria todas as tarefas de todo.
* EncryptionUtils.GetCertificateFromStore(): Este método já não emite uma exceção de referência nula devido a falhas de localizar o certificado com base em problemas de configuração do certificado.

## <a id="november_changes_12"></a>Versão de Novembro de 2012
As alterações mencionadas nesta secção foram atualizações incluídas em Novembro de 2012 (versão 2.0.0.0) SDK. Estas alterações podem exigir qualquer código escrito para a pré-visualização de Junho de 2012 versão do SDK a modificação ou foi reescrita.

* Elementos
  
    IAsset.Create(assetName) é a função de criação de recurso apenas. IAsset.Create carrega já não ficheiros como parte da chamada de método. Utilize IAssetFile para carregar.
  
    O método de IAsset.Publish e o valor de enumeração AssetState.Publish foram removidos do SDK dos serviços. Qualquer código que se baseie neste valor tem de ser escrito novamente.
* FileInfo
  
    Esta classe foi removida e substituída por IAssetFile.
  
    IAssetFiles
  
    IAssetFile substitui FileInfo e tem um comportamento diferente. Utilizá-la, instanciar o objeto IAssetFiles, seguido de um carregamento de ficheiros ou utilizando o SDK de Media Services ou o SDK de armazenamento do Azure. Podem ser utilizadas as sobrecargas de IAssetFile.Upload seguintes:
  
  * IAssetFile.Upload(filePath): Um método síncrono que bloqueia o thread e recomenda-se apenas ao carregar um único ficheiro.
  * IAssetFile.UploadAsync (filePath blobTransferClient, localizador, cancellationToken): um método assíncrono. Este é o mecanismo de carregamento preferencial. 
    
    Erro conhecido: utilizar o cancellationToken realmente irá cancelar o carregamento; No entanto, o estado de cancelamento das tarefas pode ser qualquer uma de um número de Estados. Corretamente tem de detetar e processar as exceções.
* Localizadores
  
    As versões de origem específicos foram removidas. O contexto de SAS específicos. Locators.CreateSasLocator (asset, accessPolicy) serão marcadas como preterida ou removida por depois da disponibilidade geral Consulte a secção de localizadores em novas funcionalidades para o comportamento atualizado.

## <a id="june_changes_12"></a>Versão de pré-visualização de Junho de 2012
A seguinte funcionalidade foi novidade da versão de Novembro do SDK.

* Eliminar entidades
  
    IAsset, IAssetFile, ILocator, IAccessPolicy, IContentKey objetos agora são eliminados ao nível do objeto, ou seja, IObject.Delete(), em vez de exigir um eliminar na coleção, que é cloudMediaContext.ObjCollection.Delete(objInstance).
* Localizadores
  
    Os localizadores agora devem ser criados utilizando o método CreateLocator e utilizam os valores de enumeração LocatorType.SAS ou LocatorType.OnDemandOrigin como um argumento para o tipo específico de localizador que pretende criar.
  
    Novas propriedades foram adicionadas às localizadores para tornar mais fácil obter os URIs utilizável para o seu conteúdo. Foi pretendem este recriar de localizadores fornecem mais flexibilidade para futura extensibilidade de terceiros e aumentar a facilidade de utilização para as aplicações de cliente de suporte de dados.
* Suporte de método assíncronas
  
    Foi adicionado suporte assíncrono para todos os métodos.

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!-- Anchors. -->

<!-- Images. -->

<!--- URLs. --->
[fórum MSDN do Azure Media Services]: http://social.msdn.microsoft.com/forums/azure/home?forum=MediaServices
[referência da API REST do Azure Media Services]: https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference
[detalhes de preços de serviços de suporte de dados]: http://azure.microsoft.com/pricing/details/media-services/
[metadados da entrada]: http://msdn.microsoft.com/library/azure/dn783120.aspx
[metadados de saída]: http://msdn.microsoft.com/library/azure/dn783217.aspx
[entrega de conteúdos]: http://msdn.microsoft.com/library/azure/hh973618.aspx
[indexação ficheiros de suporte de dados com o indexador de suporte de dados do Azure]: http://msdn.microsoft.com/library/azure/dn783455.aspx
[StreamingEndpoint]: http://msdn.microsoft.com/library/azure/dn783468.aspx
[trabalhar com o Azure Media Services em direto de transmissão em fluxo]: http://msdn.microsoft.com/library/azure/dn783466.aspx
[utilizando a encriptação dinâmica de AES-128 e o serviço de entrega de chave]: http://msdn.microsoft.com/library/azure/dn783457.aspx
[utilizando a encriptação dinâmica de PlayReady e o serviço de entrega de licença]: http://msdn.microsoft.com/library/azure/dn783467.aspx
[Preview features]: http://azure.microsoft.com/services/preview/
[descrição de geral do suporte de dados dos serviços PlayReady licença modelo]: http://msdn.microsoft.com/library/azure/dn783459.aspx
[encriptados conteúdo da armazenamento de transmissão em fluxo]: http://msdn.microsoft.com/library/azure/dn783451.aspx
[Azure portal]: https://manage.windowsazure.com
[empacotamento dinâmico]: http://msdn.microsoft.com/library/azure/jj889436.aspx
[blogue de Nick Drouin]: http://blog-ndrouin.azurewebsites.net/hls-v3-new-old-thing/
[Protecting transmissão em fluxo uniforme com PlayReady]: http://msdn.microsoft.com/library/azure/dn189154.aspx
[repetir lógica o SDK de Media Services para .NET]: http://msdn.microsoft.com/library/azure/dn745650.aspx
[Grass Valley Announces EDIUS 7 de transmissão em fluxo através de Cloud]: http://www.streamingmedia.com/Producer/Articles/ReadArticle.aspx?ArticleID=96351&utm_source=dlvr.it&utm_medium=twitter
[controlar o suporte de dados de serviço codificador saída os nomes de ficheiro]: http://msdn.microsoft.com/library/azure/dn303341.aspx
[criar sobrepõe]: http://msdn.microsoft.com/library/azure/dn640496.aspx
[Stitching segmentos de vídeo]: http://msdn.microsoft.com/library/azure/dn640504.aspx
[Azure .NET SDK de Media Services 3.0.0.1 e 3.0.0.2 versões]: http://www.gtrifonov.com/2014/02/07/windows-azure-media-services-.net-sdk-3.0.0.2-release/
[do Azure Active Directory acesso Control Service (ACS)]: http://msdn.microsoft.com/library/hh147631.aspx
[ligar aos Media Services com o SDK de Media Services para .NET]: http://msdn.microsoft.com/library/azure/jj129571.aspx
[extensões do SDK .NET do Azure suporte de dados de serviços]: https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev
[ferramentas do sdk do azure]: https://github.com/Azure/azure-sdk-tools
[GitHub]: https://github.com/Azure/azure-sdk-for-media-services
[gerir recursos de serviços de suporte de dados em várias contas do Storage]: http://msdn.microsoft.com/library/azure/dn271889.aspx
[processamento de notificações de tarefa de serviços de suporte de dados]: http://msdn.microsoft.com/library/azure/dn261241.aspx

