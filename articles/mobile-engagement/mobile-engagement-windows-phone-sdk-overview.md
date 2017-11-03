---
title: "Descrição geral do SDK do Silverlight do Azure Mobile Engagement Windows Phone | Microsoft Docs"
description: "Descrição geral do SDK Windows Phone Silverlight do Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 0e3d2420-0509-4952-8891-392e3dad9aaf
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: article
ms.date: 11/03/2016
ms.author: piyushjo
ms.openlocfilehash: c4e8ceee4104c3d3a6c3e6b79322ba1cf8463b22
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="windows-phone-silverlight-sdk-overview-for-azure-mobile-engagement"></a>Descrição geral do SDK do Silverlight do Azure Mobile Engagement do Windows Phone
Comece por aqui obter os detalhes sobre como integrar o Azure Mobile Engagement numa aplicação Windows Phone Silverlight. Se gostaria de experimentá-la primeiro, certifique-se que conclua a nossa [tutorial de 15 minutos](mobile-engagement-windows-phone-get-started.md).

Clique para ver o [SDK conteúdo](mobile-engagement-windows-phone-sdk-content.md)

## <a name="integration-procedures"></a>Procedimentos de integração
1. Comece por aqui: [como integrar o Mobile Engagement na sua aplicação do Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
2. Para obter notificações: [como integrar o alcance (notificações) na sua aplicação do Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement-reach.md)
3. Implementação do plano de etiqueta: [como utilizar o Mobile Engagement avançadas etiquetagem API na aplicação Windows Phone Silverlight](mobile-engagement-windows-phone-use-engagement-api.md)

## <a name="release-notes"></a>Notas de versão
###<a name="331-11032016"></a>3.3.1 (11/03/2016)
Parte da *microsoftazure. Mobileengagement* pacote Nuget **v3.4.1**

* Melhoramentos de estabilidade.

Para a versão anterior, consulte o [concluir notas de versão](mobile-engagement-windows-phone-release-notes.md)

## <a name="upgrade-procedures"></a>Procedimentos de atualização
Se já tiver integrado uma versão mais antiga do nosso SDK na sua aplicação, terá de considerar os seguintes pontos ao atualizar o SDK.

Terá de seguir vários procedimentos, se omitido várias versões do SDK. Consulte o concluída [atualizar procedimentos](mobile-engagement-windows-phone-upgrade-procedure.md). Por exemplo, se a migração do 0.10.1 para 0.11.0 que tem de primeiro siga o procedimento "de 0.9.0 para 0.10.1", em seguida, o procedimento "de 0.10.1 para 0.11.0".

### <a name="from-200-to-330"></a>De 2.0.0 para 3.3.0
#### <a name="test-logs"></a>Registos do teste
Os registos de consola produzidos pelo SDK podem agora ser ativado/desativado/filtrados. Para personalizar esta, atualize a propriedade `EngagementAgent.Instance.TestLogEnabled` para um valor disponível no `EngagementTestLogLevel` enumeração, para a instância:

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

### <a name="upgrade-from-older-versions"></a>Atualizar a partir de versões anteriores
Consulte [procedimentos de atualização](mobile-engagement-windows-phone-upgrade-procedure.md)

