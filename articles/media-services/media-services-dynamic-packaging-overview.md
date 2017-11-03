---
title: "Descrição geral de empacotamento dinâmico de Media Services do Azure | Microsoft Docs"
description: "O tópico fornecem e uma descrição geral do empacotamento dinâmico."
author: Juliako
manager: cfowler
editor: 
services: media-services
documentationcenter: 
ms.assetid: 0d9e4f54-5daa-45c1-bfaa-cf09ca89b812
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: juliako
ms.openlocfilehash: 2d212599302fced3f60085ab30cdeaefc1ee2e6a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="dynamic-packaging"></a>Empacotamento dinâmico
## <a name="overview"></a>Descrição geral
Media Services do Microsoft Azure podem ser utilizados para fornecer os formatos de muitos ficheiros de origem do suporte de dados, formatos, transmissão em fluxo de suporte de dados e proteção de conteúdos formata a uma variedade de tecnologias de cliente (por exemplo, iOS, XBOX, o Silverlight, o Windows 8). Estes clientes compreender protocolos diferentes, por exemplo iOS requer um formato HTTP Live Streaming (HLS) V4 e Silverlight e Xbox necessitam de transmissão em fluxo uniforme. Se tiver um conjunto de velocidade de transmissão adaptável (múltipla) MP4 ou um conjunto de ficheiros a transmissão em fluxo uniforme de velocidade de transmissão adaptável pretende servir clientes que compreender MPEG DASH, HLS ou transmissão em fluxo uniforme de ficheiros (ISO Base suporte de dados 14496-12), deve tirar partido do empacotamento dinâmico de Media Services.

Com o empacotamento dinâmico, tudo o que precisa é criar um elemento que contenha um conjunto de ficheiros MP4 de velocidade de transmissão adaptável ou ficheiros de transmissão em fluxo uniforme de velocidade de transmissão adaptável. Em seguida, com base no formato especificado no pedido de manifesto ou fragmento, o On-a pedido de transmissão em fluxo servidor irá garantir que recebe o fluxo no protocolo que escolheu. Como resultado, só tem de armazenar e pagar pelos ficheiros num único formato de armazenamento e os Media Services irão compilar e disponibilizar a resposta adequada com base nos pedidos de um cliente.

O diagrama seguinte mostra as tradicionais de codificação e o fluxo de trabalho de empacotamento estático.

![A codificação estático](./media/media-services-dynamic-packaging-overview/media-services-static-packaging.png)

O diagrama seguinte mostra o fluxo de trabalho do empacotamento dinâmico.

![A codificação dinâmica](./media/media-services-dynamic-packaging-overview/media-services-dynamic-packaging.png)


## <a name="common-scenario"></a>Cenário comum
1. Carregue um ficheiro de entrada (denominado um ficheiro mezanino). Por exemplo, 264, MP4 ou WMV (para obter a lista dos formatos suportados, consulte [formatos suportados pelo codificador de multimédia Standard](media-services-media-encoder-standard-formats.md).
2. Codificar o ficheiro de mezanino para conjuntos de velocidade de transmissão adaptável 264 MP4.
3. Publique o elemento que contém a velocidade de transmissão adaptável MP4 definido através da criação do localizador no pedido.
4. Crie os URLs de transmissão em fluxo para aceder e transmitir o seu conteúdo.

## <a name="preparing-assets-for-dynamic-streaming"></a>A preparar ativos para a transmissão em fluxo dinâmica
Para preparar o seu elemento para a transmissão em fluxo dinâmico tem duas opções:

1. [Carregar um ficheiro mestre](media-services-dotnet-upload-files.md).
2. [Utilize o codificador de codificador de multimédia Standard para produzir 264 MP4 de velocidade de transmissão adaptável conjuntos](media-services-dotnet-encode-with-media-encoder-standard.md).
3. [Transmitir o seu conteúdo](media-services-deliver-content-overview.md).

## <a id="unsupported_formats"></a>Formatos que não são suportados pelo empacotamento dinâmico
Os seguintes formatos de ficheiro de origem não são suportados pelo empacotamento dinâmico.

* Ficheiros do Dolby mp4 digitais.
* Dolby digitais uniforme os ficheiros.

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

