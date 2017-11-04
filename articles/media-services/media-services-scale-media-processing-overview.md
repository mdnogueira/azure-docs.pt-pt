---
title: "Descrição geral do suporte de dados de processamento de dimensionamento | Microsoft Docs"
description: "Este tópico é uma descrição geral do suporte de dados de dimensionamento processamento com Media Services do Azure."
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: 780ef5c2-3bd6-4261-8540-6dee77041387
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/04/2017
ms.author: juliako
ms.openlocfilehash: 1009d8b87cb3a131909f3ae9ee231f9ebb408c04
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="scaling-media-processing-overview"></a>Descrição geral do suporte de dados de processamento de dimensionamento
Esta página fornece uma descrição geral de como e o motivo dimensionar o processamento de suporte de dados. 

## <a name="overview"></a>Descrição geral
As contas dos Serviços de Multimédia estão associadas a um Tipo de Unidade Reservada, que determina a velocidade do processamento das suas tarefas de processamento de mulitmédia. Pode escolher de entre os tipos de unidades reservadas **S1**, **S2** ou **S3**. Por exemplo, a mesma tarefa de trabalho de codificação é executada mais depressa se utilizar o tipo de unidade reservada **S2** em comparação com o tipo **S1**. Para obter mais informações, consulte o [reservado tipos de unidade](https://azure.microsoft.com/blog/high-speed-encoding-with-azure-media-services/).

Para além de especificar o tipo de unidade reservado, pode especificar para aprovisionar a sua conta com unidades reservadas. O número de unidades reservadas aprovisionadas determina o número de tarefas de multimédia que podem ser processadas em simultâneo numa determinada conta. Por exemplo, se a sua conta tem cinco unidades reservadas, em seguida, as tarefas de suporte de dados de cinco estarão em execução em simultâneo desde como existem tarefas a serem processados. As tarefas restantes irão aguardar na fila e irão obter captadas para processar sequencialmente quando terminar, uma tarefa em execução. Se uma conta não tem quaisquer unidades reservadas aprovisionadas, em seguida, tarefas irão estar captadas sequencialmente. Neste caso, o tempo de espera entre uma tarefa adira agora e iniciar um seguinte irá depender a disponibilidade dos recursos no sistema.

## <a name="choosing-between-different-reserved-unit-types"></a>Escolher entre tipos diferentes de unidade reservada
A tabela seguinte ajuda-o a tomar a decisão ao escolher entre diferentes velocidades de codificação. Também fornece alguns casos de benchmark e fornece os URLs de SAS que pode utilizar para transferir vídeos em que pode realizar os seus próprios testes:

| Cenários | **S1** | **S2** | **S3** |
| --- | --- | --- | --- |
| Caso de utilização prevista |Velocidade de transmissão única de codificação. <br/>Ficheiros SD ou abaixo resoluções, não um prazo confidenciais, baixo custo. |Velocidade de transmissão única e várias codificação de velocidade de transmissão.<br/>Utilização normal para SD e HD codificação. |Velocidade de transmissão única e várias codificação de velocidade de transmissão.<br/>Vídeos de resolução do HD e de 4K completos. Tempo de resposta mais rápida confidencial, mais rápida de codificação. |
| Benchmark |[Ficheiro de entrada: 5 minutos longos 640x360p em 29.97 fotogramas por segundo](https://wamspartners.blob.core.windows.net/for-long-term-share/Whistler_5min_360p30.mp4?sr=c&si=AzureDotComReadOnly&sig=OY0TZ%2BP2jLK7vmcQsCTAWl33GIVCu67I02pgarkCTNw%3D).<br/><br/>Codificação para uma velocidade de transmissão única ficheiro MP4, a resolução do mesmo, demora cerca de 11 minutos. |[Ficheiro de entrada: 5 minutos longos 1280x720p em 29.97 fotogramas por segundo](https://wamspartners.blob.core.windows.net/for-long-term-share/Whistler_5min_720p30.mp4?sr=c&si=AzureDotComReadOnly&sig=OY0TZ%2BP2jLK7vmcQsCTAWl33GIVCu67I02pgarkCTNw%3D)<br/><br/>Com "H264 velocidade de transmissão única 720p" predefinição de codificação demora cerca de 5 minutos.<br/><br/>Codificação com "H264 múltipla 720p" predefinição demora cerca de 11.5 minutos. |[Ficheiro de entrada: 5 minutos longos 1920x1080p em 29.97 fotogramas por segundo](https://wamspartners.blob.core.windows.net/for-long-term-share/Whistler_5min_1080p30.mp4?sr=c&si=AzureDotComReadOnly&sig=OY0TZ%2BP2jLK7vmcQsCTAWl33GIVCu67I02pgarkCTNw%3D). <br/><br/>Com "H264 velocidade de transmissão única 1080p" predefinição de codificação demora cerca de 2.7 minutos.<br/><br/>Codificação com "H264 múltipla 1080p" predefinição demora cerca de 5.7 minutos. |

## <a name="considerations"></a>Considerações
> [!IMPORTANT]
> Reveja as considerações descritas nesta secção.  
> 
> 

* Unidades reservadas de trabalho para parallelizing todo o processamento de suporte de dados, incluindo a indexação tarefas utilizando o indexador de suporte de dados do Azure.  No entanto, ao contrário da codificação, os trabalhos de indexação não são processados mais depressa com unidades reservadas mais rápidas.
* Se utilizar o agrupamento partilhado, ou seja, sem quaisquer unidades reservadas, em seguida, as tarefas de codificar têm o mesmo desempenho tal como acontece com S1 RUs. No entanto, não há nenhum limite superior ao momento em que as suas tarefas podem gastam num Estado em fila e, em qualquer momento, no máximo apenas uma tarefa estarão em execução.
* Os seguintes centros de dados não oferecem o **S2** reservados de um tipo de unidade: sul do Brasil e oeste da Índia.
* O Centro de dados seguintes não oferecem o **S3** reservados de um tipo de unidade: Índia Ocidental.

## <a name="billing"></a>Faturação

São-lhe cobrados os minutos efetivos de utilização de Unidades Reservadas de Multimédia. Para obter uma explicação detalhada, consulte a secção Perguntas mais frequentes sobre o [preços dos Media Services](https://azure.microsoft.com/pricing/details/media-services/) página.   

## <a name="quotas-and-limitations"></a>Quotas e limitações
Para obter informações sobre as quotas e limitações e como abrir um pedido de suporte, consulte [Quotas e limitações](media-services-quotas-and-limitations.md).

## <a name="next-step"></a>Passo seguinte
Obter a tarefa de processamento de dimensionamento do suporte de dados com uma destas tecnologias: 

> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encoding-units.md)
> * [Portal](media-services-portal-scale-media-processing.md)
> * [REST](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 
> 

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

