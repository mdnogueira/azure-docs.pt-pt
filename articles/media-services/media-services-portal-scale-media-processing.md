---
title: Processamento de suporte de dados de escala no portal do Azure | Microsoft Docs
description: Este tutorial explica os passos de dimensionamento suportes de dados no portal do Azure de processamento.
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: e500f733-68aa-450c-b212-cf717c0d15da
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/04/2017
ms.author: juliako
ms.openlocfilehash: 46ca29d3e66701f2abcb185791089e94761984e8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="change-the-reserved-unit-type"></a>Alterar tipo de unidade reservada
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encoding-units.md)
> * [Portal](media-services-portal-scale-media-processing.md)
> * [REST](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 
> 

## <a name="overview"></a>Descrição geral

As contas dos Serviços de Multimédia estão associadas a um Tipo de Unidade Reservada, que determina a velocidade do processamento das suas tarefas de processamento de mulitmédia. Pode escolher de entre os tipos de unidades reservadas **S1**, **S2** ou **S3**. Por exemplo, a mesma tarefa de trabalho de codificação é executada mais depressa se utilizar o tipo de unidade reservada **S2** em comparação com o tipo **S1**.

Para além de especificar o tipo de unidade reservada, pode especificar o aprovisionamento da sua conta com **Unidades Reservadas** (RUs). O número de RUs aprovisionadas determina o número de tarefas de multimédia que podem ser processadas em simultâneo numa determinada conta.

>[!NOTE]
>As RUs destinam-se a paralelizar todos os processamentos de multimédia, incluindo trabalhos de indexação com o Azure Media Indexer. No entanto, ao contrário da codificação, os trabalhos de indexação não são processados mais depressa com unidades reservadas mais rápidas.

> [!IMPORTANT]
> Certifique-se de que revê o [descrição geral](media-services-scale-media-processing-overview.md) tópico para obter mais informações sobre dimensionamento processamento tópico de suporte de dados.
> 
> 

## <a name="scale-media-processing"></a>Dimensionar processamento de multimédia
Para alterar o tipo de unidade reservada e o número de unidades reservadas, efetue o seguinte:

1. No [portal do Azure](https://portal.azure.com/) selecione a sua conta de Serviços de Multimédia do Azure.
2. No **definições** janela, selecione **unidades reservadas de multimédia**.
   
    Para alterar o número de unidades reservadas para o tipo de unidade reservada selecionado, utilize o **suporte de dados fornecido unidades** controlo de deslize.
   
    Para alterar o **um tipo de unidade reservado**, prima S1, S2 ou S3.
   
    ![Página de processadores](./media/media-services-portal-scale-media-processing/media-services-scale-media-processing.png)
3. Prima o botão GUARDAR para guardar as alterações.
   
    As unidades novos reservadas são alocadas quando prime guardar.

## <a name="next-steps"></a>Passos seguintes
Rever os percursos de aprendizagem dos Serviços de Multimédia

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

