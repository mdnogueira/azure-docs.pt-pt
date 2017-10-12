---
title: "Descrição geral dos Serviços de Multimédia do Azure | Microsoft Docs"
description: "Este tópico fornece uma descrição geral dos Media Services do Azure"
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 7a5e9723-c379-446b-b4d6-d0e41bd7d31f
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 07/04/2017
ms.author: juliako;anilmur
ms.openlocfilehash: 2a175aed40b9775d9a4de6877eb3467b43819568
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-media-services-overview"></a>Descrição geral dos Serviços de Multimédia do Azure 

Os Media Services do Microsoft Azure são uma plataforma baseado na nuvem extensível que permite aos programadores compilar uma gestão de multimédia dimensionável e aplicações de entrega. Os Serviços de Multimédia são baseados nas APIs REST, que permitem carregar, armazenar, codificar e empacotar de forma segura conteúdos de vídeo ou áudio, para o envio de transmissão em fluxo, tanto a pedido como em direto, para vários clientes (por exemplo, TV, PC e dispositivos móveis).

Pode compilar fluxos de trabalho ponto-a-ponto utilizando inteiramente os Media Services. Pode também optar por utilizar componentes de terceiros para algumas partes do seu fluxo de trabalho. Por exemplo, codificar utilizando um codificador de terceiros. Em seguida, carregue, proteja, empacote e entregue através dos Media Services.

Pode também optar por transmitir os seus conteúdos em direto ou enviar conteúdos a pedido. O tópico também está ligado a outros tópicos relevantes.

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
Pode ver percurso de aprendizagem do AMS aqui:

* [Fluxo de Trabalho de Transmissão em Direto do AMS](https://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
* [Fluxo de Trabalho de Transmissão de Fluxo a Pedido do AMS](https://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)

## <a name="prerequisites"></a>Pré-requisitos

Para começar a utilizar os Media Services do Azure, deve ter o seguinte:

* Uma conta do Azure. Se não tiver uma conta, pode criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com).
* Uma conta de Media Services do Azure Para obter mais informações, consulte [Criar Conta](media-services-portal-create-account.md).
* (Opcional) Configurar o ambiente de desenvolvimento. Escolha .NET ou a API REST para o ambiente de desenvolvimento. Para obter mais informações, consulte [Configurar o ambiente](media-services-dotnet-how-to-use.md).

    Além disso, saiba como [estabelecer ligação através de programação à API AMS](media-services-use-aad-auth-to-access-ams-api.md).
* Um ponto final de transmissão em fluxo standard ou premium no estado iniciado.  Para obter mais informações, consulte [Gerir pontos finais de transmissão em fluxo](media-services-portal-manage-streaming-endpoints.md)

## <a name="sdks-and-tools"></a>SDKs e ferramentas

Para compilar soluções de Media Services, pode utilizar:

* [API REST dos Serviços de Multimédia](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference)
* Um dos SDKs do Cliente disponíveis:
    * [SDK do .NET dos Media Services do Azure](https://github.com/Azure/azure-sdk-for-media-services),
    * [Azure SDK para Java](https://github.com/Azure/azure-sdk-for-java),
    * [SDK do PHP do Azure](https://github.com/Azure/azure-sdk-for-php),
    * [Media Services do Azure para Node.js](https://github.com/michelle-becker/node-ams-sdk/blob/master/lib/request.js) (Esta é uma versão não Microsoft de um SDK Node.js. Esta é mantida por uma comunidade e não tem atualmente uma cobertura de 100% das APIs do AMS).
* Ferramentas existentes:
    * [Portal do Azure](https://portal.azure.com/)
    * [Explorador de Media Services do Azure](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE – Azure Media Services Explorer, uma aplicação Winforms/C# para Windows)

## <a name="concepts-and-overview"></a>Conceitos e descrição geral
Para saber mais sobre os conceitos de Media Services do Azure, consulte [Conceitos](media-services-concepts.md).

Para obter um conjunto de procedimentos que apresente todos os componentes principais dos Media Services do Azure, consulte [Tutorial Passo-a-Passo dos Media Services do Azure](https://docs.com/fukushima-shigeyuki/3439/english-azure-media-services-step-by-step-series). Este conjunto possui uma excelente descrição geral dos conceitos e utiliza a ferramenta AMSE para demonstrar as tarefas AMS. A ferramenta AMSE é uma ferramenta do Windows. Esta ferramenta suporta a maioria das tarefas que pode realizar através de programação com [o SDK do AMS para .NET](https://github.com/Azure/azure-sdk-for-media-services), [Azure SDK para Java](https://github.com/Azure/azure-sdk-for-java) ou [SDK do PHP do Azure](https://github.com/Azure/azure-sdk-for-php).

## <a name="supported-scenarios-and-availability-of-media-services-across-data-centers"></a>Cenários suportados e disponibilidade dos Serviços de Multimédia em datacenters

Para obter informações detalhadas, veja [Cenários do AMS e disponibilidade das funcionalidades e dos serviços em todos os datacenters](scenarios-and-availability.md).

## <a name="service-level-agreement-sla"></a>Contrato de Nível de Serviço (SLA)

* Para Media Services Encoding, podemos garantir 99,9% de disponibilidade das transações da API REST.
* Para a Transmissão em Fluxo, iremos responder com êxito aos pedidos com 99,9% de disponibilidade garantida para os conteúdos de multimédia existentes quando for comprado um ponto final de transmissão em fluxo standard ou premium.
* Para Canais em Direto, garantimos que a execução dos Canais terá uma conectividade externa em, pelo menos, 99,9% das vezes.
* Para Content Protection, garantimos que iremos satisfazer com êxito os pedidos-chave em, pelo menos, 99,9% das vezes.
* Para o Indexador, iremos satisfazer com êxito os pedidos de Tarefa do Indexador com uma Unidade de Codificação Reservada em, pelo menos, 99,9% das vezes.

Para obter mais informações, consulte [SLA do Microsoft Azure](https://azure.microsoft.com/support/legal/sla/).

Para obter informações sobre a disponibilidade em datacenters, veja a secção [Disponibilidade](scenarios-and-availability.md#availability).

## <a name="support"></a>Suporte

O [Suporte do Azure](https://azure.microsoft.com/support/options/) fornece opções de suporte para o Azure, incluindo os Media Services.

## <a name="provide-feedback"></a>Enviar comentários

[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
