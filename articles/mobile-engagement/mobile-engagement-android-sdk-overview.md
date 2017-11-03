---
title: "Integração do Android SDK do Azure Mobile Engagement"
description: "Descreve como integrar o SDK do Azure Mobile Engagement em aplicações Android"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: a91ed04f-f3ce-4692-a6dd-b56a28d7dee8
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 07/17/2017
ms.author: piyushjo;ricksal
ms.openlocfilehash: 35935e911f1f17989beb71978396c6d1b7d601d6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="android-sdk-integration-for-azure-mobile-engagement"></a>Integração do Android SDK do Azure Mobile Engagement
> [!div class="op_single_selector"]
> * [Universal Windows](mobile-engagement-windows-store-sdk-overview.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-sdk-overview.md)
> * [iOS](mobile-engagement-ios-sdk-overview.md)
> * [Android](mobile-engagement-android-sdk-overview.md)
> 
> 

Este documento descreve todas as integração e a configuração opções disponíveis para Android SDK do Azure Mobile Engagement.

## <a name="prerequisites"></a>Pré-requisitos
[!INCLUDE [Prereqs](../../includes/mobile-engagement-android-prereqs.md)]

## <a name="advanced-features"></a>Funcionalidades avançadas
### <a name="reporting-features"></a>Funcionalidades de relatórios
Pode adicionar estas funcionalidades:

1. [Opções de relatórios avançadas](mobile-engagement-android-advanced-reporting.md)
2. [Opções de relatórios de localização](mobile-engagement-android-location-reporting.md)
3. [Opções de configuração avançadas](mobile-engagement-android-advanced-configuration.md)

### <a name="notifications"></a>Notificações:
[Como integrar o alcance (notificações) na sua aplicação Android](mobile-engagement-android-integrate-engagement-reach.md)

1. Google Cloud Messaging (GCM): [como integrar o GCM Mobile engagement](mobile-engagement-android-gcm-integrate.md)
2. Amazon Device Messaging (ADM): [como integrar ADM Mobile engagement](mobile-engagement-android-adm-integrate.md)

### <a name="tag-plan-implementation"></a>Implementação do plano de etiqueta:
[Como utilizar a marcação de API na sua aplicação Android de Mobile Engagement avançadas](mobile-engagement-android-use-engagement-api.md)

## <a name="release-notes"></a>Notas de versão

### <a name="431-07172017"></a>4.3.1 (07/17/2017)
* Corrigir falhas que raramente pode ter ocorrido ao chamar `EngagementAgentUtils.isInDedicatedEngagementProcess`, que também é utilizado pelo `EngagementApplication` classe.

### <a name="430-06272017"></a>4.3.0 (06/27/2017)
* Suporte Android 8 (versões anteriores do SDK não funcionará no Android 8).
* Não existem mais dependência na biblioteca de suporte.
* Remover `EngagementFragmentActivity` classe.
* Devido a [limites de execução em segundo plano](https://developer.android.com/preview/features/background.html) no Android 8, os registos em segundo plano podem sofrer um atraso de até que o utilizador interage com o dispositivo, este irá ter um impacto no campanha Push **entregues** e **notificação do sistema apresentada** estatísticas a ser atrasadas se o dispositivo foi suspenso (a notificação irá ainda ser apresentada, serão anel e Vibre em tempo real sem problemas).
* Devido a [limites de localização em segundo plano](https://developer.android.com/preview/features/background-location-limits.html), o localização em segundo plano não será atualizada frequentemente no Android 8 em tempo real.

Para todas as versões, consulte o [concluir notas de versão](mobile-engagement-android-release-notes.md).

## <a name="upgrade-procedures"></a>Procedimentos de atualização
Se já tiver integrado uma versão mais antiga do nosso SDK na sua aplicação, consulte [atualizar procedimentos](mobile-engagement-android-upgrade-procedure.md).

