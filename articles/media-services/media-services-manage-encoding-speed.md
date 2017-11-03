---
title: Gerir velocidade e simultaneidade da sua encoding com Media Services do Azure | Microsoft Docs
description: "Este artigo fornece uma breve descrição geral de como pode gerir velocidade e simultaneidade das suas tarefas/tarefas codificação com Media Services do Azure."
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: 676313f8-a158-4e3a-a99b-2c29a341ecc9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2017
ms.author: juliako
ms.openlocfilehash: 0463904fd9bf1138587d0d214e572ddd38cc2184
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
#  <a name="manage-speed-and-concurrency-of-your-encoding"></a>Gerir velocidade e simultaneidade da codificação

Este artigo fornece uma breve descrição geral de como pode gerir velocidade e simultaneidade de tarefas as codificação/tarefas.

## <a name="overview"></a>Descrição geral

Nos Media Services, um **um tipo de unidade reservado** determina a velocidade com que o suporte de dados de processamento de tarefas é processados. Pode escolher de entre os tipos de unidades reservadas **S1**, **S2** ou **S3**. Por exemplo, a mesma tarefa de trabalho de codificação é executada mais depressa se utilizar o tipo de unidade reservada **S2** em comparação com o tipo **S1**. O [dimensionamento unidades de codificação](media-services-scale-media-processing-overview.md) tópico mostra uma tabela que o ajuda a tomar a decisão ao escolher entre diferentes velocidades de codificação.

Para além de especificar o tipo de unidade reservado, pode especificar para aprovisionar a conta com **unidades reservadas**. O número de unidades reservadas aprovisionadas determina o número de tarefas de multimédia que podem ser processadas em simultâneo numa determinada conta. Por exemplo, se a sua conta tem cinco unidades reservadas, em seguida, as tarefas de suporte de dados de cinco estarão em execução em simultâneo desde como existem tarefas a serem processados. As tarefas restantes irão aguardar na fila e irão obter captadas para processar sequencialmente quando terminar, uma tarefa em execução. Se uma conta não tem quaisquer unidades reservadas aprovisionadas, em seguida, tarefas irão estar captadas sequencialmente. Neste caso, o tempo de espera entre uma tarefa adira agora e iniciar um seguinte irá depender a disponibilidade dos recursos no sistema.

Para obter informações detalhadas e exemplos mostram como unidades de codificação de escala, consulte [isto](media-services-scale-media-processing-overview.md) tópico.

## <a name="next-step"></a>Passo seguinte

[Unidades de codificação de escala](media-services-scale-media-processing-overview.md)

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

