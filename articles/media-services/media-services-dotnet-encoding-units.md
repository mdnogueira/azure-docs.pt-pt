---
title: "Dimensionar o suporte de dados de processamento através da adição de unidades de codificação - Azure |  Microsoft Docs"
description: "Saiba como como adicionar unidades de codificação com o .NET"
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: 33f7625a-966a-4f06-bc09-bccd6e2a42b5
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: juliako;milangada;
ms.openlocfilehash: 72a8729d22a9e76c8076d7a3347619a2163e4f09
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-scale-encoding-with-net-sdk"></a>Como dimensionar a codificação com o .NET SDK
> [!div class="op_single_selector"]
> * [Portal](media-services-portal-scale-media-processing.md)
> * [.NET](media-services-dotnet-encoding-units.md)
> * [REST](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 
> 

## <a name="overview"></a>Descrição geral
> [!IMPORTANT]
> Certifique-se de que revê o [descrição geral](media-services-scale-media-processing-overview.md) tópico para obter mais informações sobre dimensionamento processamento tópico de suporte de dados.
> 
> 

Para alterar o tipo de unidade reservada e o número de unidades codificação reservada com o .NET SDK, efetue o seguinte:

    IEncodingReservedUnit encodingS1ReservedUnit = _context.EncodingReservedUnits.FirstOrDefault();
    encodingS1ReservedUnit.ReservedUnitType = ReservedUnitType.Basic; // Corresponds to S1
    encodingS1ReservedUnit.Update();
    Console.WriteLine("Reserved Unit Type: {0}", encodingS1ReservedUnit.ReservedUnitType);

    encodingS1ReservedUnit.CurrentReservedUnits = 2;
    encodingS1ReservedUnit.Update();

    Console.WriteLine("Number of reserved units: {0}", encodingS1ReservedUnit.CurrentReservedUnits);

## <a name="opening-a-support-ticket"></a>Abrir um pedido de suporte
Por predefinição cada conta de Media Services pode ser dimensionados para até 25 codificação e 5 a pedido de transmissão em fluxo unidades reservadas. Pode pedir um limite superior ao abrir um pedido de suporte.

### <a name="open-a-support-ticket"></a>Abra um pedido de suporte
Para abrir um suporte de permissão, efetue o seguinte:

1. Clique em [obter suporte](https://manage.windowsazure.com/?getsupport=true). Se não tiver sessão iniciada, será solicitado para introduzir as suas credenciais.
2. Selecione a sua subscrição.
3. Em tipo de suporte, selecione "Técnica".
4. Clique em "Criar pedido de".
5. Selecione "Media Services do Azure" na lista de produto apresentado na página seguinte.
6. Selecione um "tipo de problema" que é adequado para o seu problema.
7. Clique em continuar.
8. Siga as instruções na página seguinte e, em seguida, introduza os detalhes sobre o problema.
9. Clique em Submeter para abrir o pedido de suporte.

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

