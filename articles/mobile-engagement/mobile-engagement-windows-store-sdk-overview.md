---
title: "Integração de Universal SDK do Windows do Azure Mobile Engagement | Microsoft Docs"
description: "Windows integração Universal para o SDK do Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 9ded187d-5c07-4377-a41c-ce205dd38b50
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 11/03/2016
ms.author: piyushjo
ms.openlocfilehash: d616ad58156a19e89b3e106639a38df67cbd0abb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="windows-universal-sdk-integration-for-azure-mobile-engagement"></a>Integração de Universal SDK do Windows do Azure Mobile Engagement
Este documento descreve todas as integração e a configuração opções disponíveis para o Azure Mobile Engagement Windows Universal SDK.

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, primeiro tem de concluir a nossa [tutorial de 15 minutos](mobile-engagement-windows-store-dotnet-get-started.md).

## <a name="advanced-features"></a>Funcionalidades avançadas
### <a name="reporting-features"></a>Funcionalidades de relatórios
Pode adicionar estas funcionalidades:

1. [Opções de relatórios avançadas](mobile-engagement-windows-store-advanced-reporting.md)
2. [Opções de configuração avançada](mobile-engagement-windows-store-advanced-configuration.md)

### <a name="notifications"></a>Notificações
[Como integrar o alcance (notificações) na sua aplicação Universal do Windows](mobile-engagement-windows-store-integrate-engagement-reach.md)

### <a name="tag-plan-implementation"></a>Implementação do plano de etiqueta:
[Como utilizar o Mobile Engagement avançadas etiquetagem API na sua aplicação Universal do Windows](mobile-engagement-windows-store-use-engagement-api.md)

## <a name="release-notes"></a>Notas de versão
### <a name="341-11032016"></a>3.4.1 (11/03/2016)

* Melhoramentos de estabilidade.

Para versões anteriores, consulte o [concluir notas de versão](mobile-engagement-windows-store-release-notes.md)

## <a name="upgrade-procedures"></a>Procedimentos de atualização
Se já tiver integrado uma versão antiga do Engagement na sua aplicação, terá de considerar os seguintes pontos ao atualizar o SDK.

Se perdida várias versões do SDK, poderá ter de seguir vários procedimentos. Consulte o concluída [atualizar procedimentos](mobile-engagement-windows-store-upgrade-procedure.md). Por exemplo, se a migração do 0.10.1 para 0.11.0 que tem de primeiro siga o procedimento "de 0.9.0 para 0.10.1", em seguida, o procedimento "de 0.10.1 para 0.11.0".

### <a name="from-330-to-340"></a>De 3.3.0 para 3.4.0
#### <a name="test-logs"></a>Registos do teste
Os registos de consola produzidos pelo SDK podem agora ser ativado/desativado/filtrados. Para personalizar, atualize a propriedade `EngagementAgent.Instance.TestLogEnabled` para um dos valores disponíveis a partir do `EngagementTestLogLevel` enumeração, por exemplo:

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

#### <a name="resources"></a>Recursos
A sobreposição de alcance foi melhorada. Faz parte dos recursos de pacote NuGet do SDK.

Durante a atualização para a nova versão do SDK, pode escolher se pretende manter os ficheiros existentes na pasta sobreposição dos seus recursos ou não:

* Se a sobreposição anterior está a funcionar para si ou são integrar o `WebView` elementos manualmente, em seguida, pode optar por manter a sua sair ficheiros, irá continuem a funcionar.
* Para atualizar para a novo sobreposição, substitua o inteiro `overlay` pasta dos seus recursos pelo novo no pacote do SDK (aplicações UWP: após a atualização, pode aproveitar a nova pasta de sobreposição % USERPROFILE %\\.nuget\packages\ MicrosoftAzure.MobileEngagement\3.4.0\content\win81\Resources).

> [!WARNING]
> Utilizar a sobreposição de nova substitui quaisquer personalizações efetuadas na versão anterior.
> 
> 

### <a name="upgrade-from-older-versions"></a>Atualizar a partir de versões anteriores
Consulte [procedimentos de atualização](mobile-engagement-windows-store-upgrade-procedure.md)

