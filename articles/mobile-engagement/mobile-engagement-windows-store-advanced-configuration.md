---
title: "Avançadas de configuração para o Engagement de aplicações universais do Windows SDK"
description: "Opções de configuração para o Azure Mobile Engagement com aplicações universais do Windows avançadas"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 6d85dd5d-ac07-43ba-bbe4-e91c3a17690b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 10/04/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: cb9454212c94cf65093219c3d24c71277ede7877
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="advanced-configuration-for-windows-universal-apps-engagement-sdk"></a>Avançadas de configuração para o Engagement de aplicações universais do Windows SDK
> [!div class="op_single_selector"]
> * [Universal Windows](mobile-engagement-windows-store-advanced-configuration.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
> * [iOS](mobile-engagement-ios-integrate-engagement.md)
> * [Android](mobile-engagement-android-advanced-configuration.md)
> 
> 

Este procedimento descreve como configurar várias opções de configuração para aplicações do Azure Mobile Engagement Android.

## <a name="prerequisites"></a>Pré-requisitos
[!INCLUDE [Prereqs](../../includes/mobile-engagement-windows-store-prereqs.md)]

## <a name="advanced-configuration"></a>Configuração avançada
### <a name="disable-automatic-crash-reporting"></a>Desativar o relatório de falhas automática
Pode desativar a funcionalidade do Engagement de relatórios de falhas automática. Em seguida, quando ocorre uma exceção não processada, o Engagement faz nada.

> [!WARNING]
> Se desativar esta funcionalidade, em seguida, quando ocorre uma falha não processada na aplicação, o Engagement não envia as falhas **e** não feche a sessão e tarefas.
> 
> 

Para desativar o relatório de falhas automática, personalize a configuração, dependendo da forma que o declarado:

#### <a name="from-engagementconfigurationxml-file"></a>De `EngagementConfiguration.xml` ficheiro
Definir as falhas de relatório `false` entre `<reportCrash>` e `</reportCrash>` etiquetas.

#### <a name="from-engagementconfiguration-object-at-run-time"></a>De `EngagementConfiguration` objeto em tempo de execução
Definir falhas do relatório para false utilizando o seu objeto EngagementConfiguration.

        /* Engagement configuration. */
        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

        /* Disable Engagement crash reporting. */
        engagementConfiguration.Agent.ReportCrash = false;

### <a name="disable-real-time-reporting"></a>Desativar o relatório em tempo real
Por predefinição, os relatórios do serviço o Engagement os registos em tempo real. Se a aplicação comunica frequentemente os registos, é melhor para a memória intermédia os registos e para comunicá-los ao mesmo tempo em intervalos de tempo regulares. Esta opção é denominada "impulsar modo".

Para fazê-lo, chame o método:

        EngagementAgent.Instance.SetBurstThreshold(int everyMs);

O argumento é um valor na **milissegundos**. Sempre que pretender reativar o registo em tempo real, chame o método sem quaisquer parâmetros ou com o valor de 0.

Modo de rajada ligeiramente aumenta a vida bateria, mas tem um impacto no Monitor Engagement: todos os duração de sessões e as tarefas são arredondados o limiar de rajada (assim, as sessões e as tarefas mais curtos do que o limiar de rajada poderá não estar visível). Recomendamos que utilize um limiar de rajada que 30000 (30s). Registos guardados do estão limitados a 300 itens. Se enviar é demasiado longa, que pode perder algumas registos.

> [!WARNING]
> Não é possível configurar o limiar de rajada para um período de menor que um segundo. Se o fizer, o SDK mostra um rastreio com o erro e repõe automaticamente o valor predefinido, zero segundos. Isto aciona o SDK para reportar os registos em tempo real.
> 
> 

[here]:http://www.nuget.org/packages/Capptain.WindowsCS
[NuGet website]:http://docs.nuget.org/docs/start-here/overview
