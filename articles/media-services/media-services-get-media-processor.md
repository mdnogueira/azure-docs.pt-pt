---
title: Como criar um processador de suporte de dados utilizando o SDK de Media Services do Azure para .NET | Microsoft Docs
description: "Saiba como criar um componente de processador de suporte de dados para codificar, converter o formato, encriptar ou desencriptar o conteúdo de multimédia de Media Services do Azure. Exemplos de código são escritos em c# e utilizam o SDK de Media Services para .NET."
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: dbf9496f-c6f0-42a7-aa36-70f89dcb8ea2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2017
ms.author: juliako
ms.openlocfilehash: c2cbe41b71afa8acc184f9d7f4cfe94686de783e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-get-a-media-processor-instance"></a>Como: obter uma instância de processador de suporte de dados
> [!div class="op_single_selector"]
> * [.NET](media-services-get-media-processor.md)
> * [REST](media-services-rest-get-media-processor.md)
> 
> 

## <a name="overview"></a>Descrição geral
Nos Media Services, que um processador de multimédia é um componente que processa uma tarefa de processamento específico, tal como a codificação, formato conversão, encriptar ou desencriptar o conteúdo de multimédia. Normalmente, crie um processador de multimédia quando estiver a criar uma tarefa para codificar, encriptar ou converter o formato de conteúdo do suporte de dados.

## <a name="azure-media-processors"></a>Processadores de Media Services do Azure 

O tópico seguinte fornece uma lista de processadores de suporte de dados:

* [Codificação processadores de multimédia](scenarios-and-availability.md#encoding-media-processors)
* [Processadores de multimédia de análise](scenarios-and-availability.md#analytics-media-processors)

## <a name="get-media-processor"></a>Obter o processador de multimédia

O método seguinte mostra como obter uma instância de processador de suporte de dados. O exemplo de código pressupõe que a utilização de uma variável de nível de módulo denominada **_context** para referenciar o contexto de servidor, conforme descrito na secção [como: ligar ao suporte de dados de serviços através de programação](media-services-use-aad-auth-to-access-ams-api.md).

    private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
        ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

        if (processor == null)
        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

        return processor;
    }


## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Passos Seguintes
Agora que sabe como obter uma instância de processador de suporte de dados, vá para o [como codificar um elemento](media-services-dotnet-encode-with-media-encoder-standard.md) tópico que irá mostrar como utilizar o codificador de multimédia Standard para codificar um elemento.

