---
title: Media Services do Azure perguntas mais frequentes | Microsoft Docs
description: Perguntas mais frequentes (FAQ)
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 5374f7f4-c189-43ef-8b7f-f2f4141e2748
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2017
ms.author: juliako
ms.openlocfilehash: e2a580b835496c677f048599ae83287dbb61621d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="frequently-asked-questions"></a>Perguntas mais frequentes

Este artigo aborda perguntas mais frequentes geradas pela Comunidade de utilizadores dos serviços de suporte de dados do Azure (AMS).

## <a name="general-ams-faqs"></a>Perguntas frequentes do AMS geral

P: como a transmitir para os dispositivos Apple iOS

R: adicionar "(formato = m3u8-aapl)" caminho para a parte "/ Manifest" do URL para indicam ao servidor de origem de transmissão em fluxo deve devolver back HLS conteúdo para consumo no Apple iOS dispositivos nativos (para obter mais informações, consulte (distribuição de conteúdos) [ suporte de dados-services-entregar-conteúdo-overview.md]),

P: como pode dimensionar a indexação

R: de unidades reservadas são os mesmos para tarefas de codificação e Indexing. Siga as instruções [como escala unidades de codificação reservadas](media-services-scale-media-processing-overview.md). **Tenha em atenção** que o desempenho de indexador não é afetado por tipo de unidade reservado.

P: Posso carregou, codificou e publicado um vídeo. O que seria o motivo pelo qual o vídeo não desempenha a quando tento transmiti-lo?

Não tem o partir do qual está a tentar reprodução no ponto final de transmissão em fluxo a: uma das razões mais comuns é a **executar** estado.  

P: posso fazer compositing num fluxo em direto?

R: compositing em fluxos em direto atualmente não é oferecido na Media Services do Azure, pelo que é necessário para compor previamente no seu computador.

P: Posso utilizar a CDN do Azure com transmissão em fluxo em direto?

R: Media Services suporta a integração com a CDN do Azure (para obter mais informações, consulte [como gerir transmissão em fluxo pontos finais numa conta de Media Services](media-services-portal-manage-streaming-endpoints.md)).  Pode utilizar em direto de transmissão em fluxo com CDN. Media Services do Azure fornece saídas de transmissão em fluxo, HLS e MPEG-DASH uniforme. Todos os estes formatos de utilizam HTTP para transferir dados e obter benefícios da colocação em cache de HTTP. Em direto de transmissão em fluxo real de dados de áudio/vídeo são divididos para fragmentos e este fragmentos individuais obterem em cache na CDN. Apenas dados têm de ser atualizadas são os dados de manifesto. CDN, atualiza periodicamente dados manifesto.

P: serviços de multimédia do Azure Does suportam imagens armazenar?

R: Se pretender apenas tirar para armazenar imagens JPEG ou PNG, deve manter existentes no Blob Storage do Azure. Não há nenhuma vantagem colocando-los na sua conta de Media Services, a menos que pretenda mantê-las associado as vídeo ou áudio ativos. Ou, se é possível que tenha uma necessidade de utilizar as imagens de como as Sobreposições no codificador vídeo. Codificador de multimédia Standard suporta imagens sobrepor por cima de vídeos e que é o que lista JPEG e PNG como suportados formatos de entrada. Para obter mais informações, consulte [criar sobrepõe](media-services-advanced-encoding-with-mes.md#overlay).

P: como copiar recursos de uma conta de Media Services para outro.

R: para copiar recursos de uma conta de Media Services para outro utilizando o .NET, utilize [IAsset.Copy](https://github.com/Azure/azure-sdk-for-media-services-extensions/blob/dev/MediaServices.Client.Extensions/IAssetExtensions.cs#L354) método de extensão disponível no [extensões do SDK .NET do Azure suporte de dados de serviços](https://github.com/Azure/azure-sdk-for-media-services-extensions/) repositório. Para obter mais informações, consulte [isto](https://social.msdn.microsoft.com/Forums/azure/28912d5d-6733-41c1-b27d-5d5dff2695ca/migrate-media-services-across-subscription?forum=MediaServices) thread de Fórum.

P: quais são os carateres suportados para atribuir nomes a ficheiros ao trabalhar com AMS?

R: Media Services utiliza o valor da propriedade IAssetFile.Name ao criar os URLs para os conteúdos de transmissão em fluxo (por exemplo, http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) Por este motivo, por cento de codificação não é permitida. O valor da **nome** propriedade não pode ter qualquer um dos seguintes [por cento codificação-reservados carateres](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters):! *' ();: @& = + $, /? % # [] ". Além disso, só pode existir um '.' para a extensão de nome de ficheiro.

P: como ligar através de REST?

R: para obter informações sobre como ligar à API do AMS, consulte [aceder à API de serviços de suporte de dados do Azure com a autenticação do Azure AD](media-services-use-aad-auth-to-access-ams-api.md). Depois de ligar com êxito a https://media.windows.net, receberá um redirecionamento 301 especificando noutro URI de serviços de suporte de dados. Tem de se as chamadas subsequentes para o novo URI. 

P: como rodar um vídeo durante o processo de codificação.

R: o [codificador de multimédia Standard](media-services-dotnet-encode-with-media-encoder-standard.md) suporta rotação pelos ângulos de 180/90/270. O comportamento predefinido é "Auto", onde este tenta detetar os metadados de rotação no ficheiro de entrada MP4/MOV bem como compensá-lo. Incluem o seguinte **origens** elemento a uma das predefinições de json definidas [aqui](media-services-mes-presets-overview.md):

    "Version": 1.0,
    "Sources": [
    {
      "Streams": [],
      "Filters": {
        "Rotation": "90"
      }
    }
    ],
    "Codecs": [

    ...


## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
