---
title: "Descrição geral e a comparação do Azure em codificadores de suporte de dados a pedido | Microsoft Docs"
description: "Este tópico fornece uma descrição geral e a comparação do Azure em codificadores de suporte de dados a pedido."
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: e6bfc068-fa46-4d68-b1ce-9092c8f3a3c9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2017
ms.author: juliako
ms.openlocfilehash: 538a6ab60168735c2626a93cdeedd8d4999a6efc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="overview-and-comparison-of-azure-on-demand-media-encoders"></a>Descrição geral e a comparação do Azure em codificadores de suporte de dados a pedido
## <a name="encoding-overview"></a>Descrição geral de codificação
Media Services do Azure fornece várias opções para a codificação de suportes de dados na nuvem.

Ao começar com os Media Services, é importante compreender a diferença entre os formatos de codecs e de ficheiros.
Codecs são o software que implementa os algoritmos de compressão/descompressão enquanto formatos de ficheiro são contentores que contêm o vídeo comprimido.

Os Media Services fornecem um empacotamento dinâmico, permitindo-lhe fornecer o conteúdo de velocidade de transmissão adaptável MP4 ou transmissão em fluxo uniforme codificado na transmissão em fluxo suportados pelos Media Services (MPEG DASH, HLS, transmissão em fluxo uniforme) de formatos, sem ter de novamente o pacote para estes formatos de transmissão em fluxo.

>[!NOTE]
>Quando a sua conta AMS é criada, é adicionado um ponto final de transmissão em fluxo **predefinido** à sua conta no estado **Parado**. Para começar a transmitir o seu conteúdo em fluxo e a tirar partido do empacotamento e encriptação dinâmicos, o ponto final de transmissão em fluxo a partir do qual quer transmitir conteúdo tem de estar no estado **Em execução**. Para tirar partido das [empacotamento dinâmico](media-services-dynamic-packaging-overview.md), precisa de fazer o seguinte:
>
>Além disso, codificar o ficheiro de origem para um conjunto de ficheiros MP4 de velocidade de transmissão adaptável ou ficheiros de transmissão em fluxo uniforme de velocidade de transmissão adaptável (os passos da codificação são demonstrados mais à frente neste tutorial).

Os Media Services suportam o seguinte no codificadores a pedido que são descritos neste artigo:

* [Media Encoder Standard](media-services-encode-asset.md#media-encoder-standard)
* [Fluxo de Trabalho Premium do Codificador de Multimédia](media-services-encode-asset.md#media-encoder-premium-workflow)

Este artigo fornece uma breve descrição geral da pedido codificadores de suporte de dados e fornece ligações para artigos que fornecer informações mais detalhadas. O tópico também fornece a comparação entre os codificadores.

>[!NOTE]
>Por predefinição, cada conta de Media Services pode ter uma tarefa de codificação Active Directory cada vez. Pode reservar unidades de codificação que permitem-lhe ter várias tarefas de codificação em execução em simultâneo, um para cada unidade reservada codificação que compra. Para informações, consulte [dimensionamento unidades de codificação](media-services-scale-media-processing-overview.md).

## <a name="media-encoder-standard"></a>Media Encoder Standard
### <a name="how-to-use"></a>Como utilizar
[Como codificar com o codificador de multimédia Standard](media-services-dotnet-encode-with-media-encoder-standard.md)

### <a name="formats"></a>Formatos
[Formatos e codecs](media-services-media-encoder-standard-formats.md)

### <a name="presets"></a>Predefinições
Codificador de multimédia Standard for configurado através de uma das predefinições codificador descritas [aqui](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

### <a name="input-and-output-metadata"></a>Metadados de entrada e de saída
Os metadados da entrada de codificadores é descrito [aqui](media-services-input-metadata-schema.md).

Os metadados de saída codificadores é descrito [aqui](media-services-output-metadata-schema.md).

### <a name="generate-thumbnails"></a>Gerar miniaturas
Para informações, consulte [como gerar miniaturas utilizando um codificador de multimédia Standard](media-services-advanced-encoding-with-mes.md#thumbnails).

### <a name="trim-videos-clipping"></a>Compactar vídeos (recorte)
Para informações, consulte [como cortar vídeos com o codificador de multimédia Standard](media-services-advanced-encoding-with-mes.md#trim_video).

### <a name="create-overlays"></a>Criar as Sobreposições
Para informações, consulte [como criar as Sobreposições utilizando um codificador de multimédia Standard](media-services-advanced-encoding-with-mes.md#overlay).

### <a name="see-also"></a>Consultar também
[O blogue de Media Services](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/)

## <a name="media-encoder-premium-workflow"></a>Fluxo de Trabalho Premium de Codificador de Multimédia
### <a name="overview"></a>Descrição geral
[Introdução às Premium codificação, no suporte de dados do Azure, os serviços](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/)

### <a name="how-to-use"></a>Como utilizar
O fluxo de trabalho do suporte de dados codificador Premium for configurado através de fluxos de trabalho complexos. Ficheiros de fluxo de trabalho foi criados e atualizado utilizando o [estruturador de fluxo de trabalho](media-services-workflow-designer.md) ferramenta.

[Como utilizar o Premium codificação, no suporte de dados do Azure, os serviços](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services/)

### <a name="known-issues"></a>Problemas conhecidos
Se o seu vídeo de entrada não contém fechado captioning, a saída Asset ainda conterá um ficheiro TTML vazio.


## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Artigos relacionados
* [Executar tarefas de codificação avançadas ao personalizar predefinições codificador de multimédia Standard](media-services-custom-mes-presets-with-dotnet.md)
* [Quotas e limitações](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: http://azure.microsoft.com/pricing/details/media-services/
