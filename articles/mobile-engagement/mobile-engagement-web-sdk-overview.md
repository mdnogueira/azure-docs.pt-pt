---
title: "Descrição geral do SDK de Web do Azure Mobile Engagement | Microsoft Docs"
description: "As mais recentes atualizações e procedimentos para o SDK Web do Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 5bbc2fda-0f3f-43d0-a73d-0f2c0f8dc25b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: web
ms.devlang: js
ms.topic: article
ms.date: 10/18/2016
ms.author: piyushjo
ms.openlocfilehash: 770a83131a3e661771db50b22ce7de25b2d541cf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-mobile-engagement-web-sdk"></a>SDK da Web do Azure Mobile Engagement
Comece por aqui para todos os detalhes sobre como integrar o Azure Mobile Engagement numa aplicação web. Se gostaria de experimentá-la antes da introdução com a sua própria aplicação web, consulte a nossa [tutorial de 15 minutos](mobile-engagement-web-app-get-started.md).

## <a name="integration-procedures"></a>Procedimentos de integração
1. Saiba [como integrar o Mobile Engagement na sua aplicação web](mobile-engagement-web-integrate-engagement.md).
2. Para implementação do plano de etiqueta, saiba [como utilizar a marcação de API na sua aplicação web de Mobile Engagement avançadas](mobile-engagement-web-use-engagement-api.md).

## <a name="release-notes"></a>Notas de versão
### <a name="202-10182016"></a>2.0.2 (10/18/2016)
* Falhas fixa no privada navegar (Safari).
* Falhas fixa em browsers com cookies desativados.

Para todas as versões, veja o [concluir notas de versão](mobile-engagement-web-release-notes.md).

## <a name="upgrade-procedures"></a>Procedimentos de atualização
### <a name="upgrade-from-121-to-200"></a>Atualizar do 1.2.1 para 2.0.0
As secções seguintes descrevem como migrar uma integração do SDK do Mobile Engagement Web do serviço Capptain, oferecido pelo Capptain SAS, para uma aplicação do Azure Mobile Engagement. Se estiver a migrar de uma versão anterior ao 1.2.1, consulte o Web site Capptain para migrar para 1.2.1 primeiro e, em seguida, aplique os seguintes procedimentos.

Esta versão do SDK do Mobile Engagement Web não suporta TV inteligente Samsung, Opera TV, webOS ou a funcionalidade de alcance.

> [!IMPORTANT]
> Capptain e o Azure Mobile Engagement não são o mesmo serviço, e os procedimentos seguintes Realce apenas como migrar a aplicação de cliente. Migrar o SDK do Mobile Engagement Web na aplicação não é migrados os dados de um servidor de Capptain para um servidor de Mobile Engagement.
> 
> 

#### <a name="javascript-files"></a>Ficheiros de JavaScript
Substitua o ficheiro capptain-sdk.js o Azure-engagement.js ficheiro e, em seguida, Atualize as importações de script em conformidade.

#### <a name="remove-capptain-reach"></a>Remover Capptain alcance
Esta versão do SDK do Mobile Engagement Web não suporta a funcionalidade de alcance. Se tiver integrado Capptain alcance na sua aplicação, terá de removê-lo.

Remova a importação de alcançar CSS da sua página e elimine o ficheiro. css relacionados (capptain reach.css, por predefinição).

Eliminar os seguintes recursos de alcance: a imagem de fechada (capptain close.png, por predefinição) e o ícone de marca (capptain--ícone de notificação, por predefinição).

Remova a IU alcançar nas notificações na aplicação. O esquema predefinido tem o seguinte aspeto:

    <!-- capptain notification -->
    <div id="capptain_notification_area" class="capptain_category_default">
      <div class="icon">
        <img src="capptain-notification-icon.png" alt="icon" />
      </div>
      <div class="content">
        <div class="title" id="capptain_notification_title"></div>
        <div class="message" id="capptain_notification_message"></div>
      </div>
      <div id="capptain_notification_image"></div>
      <div>
        <button id="capptain_notification_close">Close</button>
      </div>
    </div>

Remova a IU de alcançar de mensagens em fila de texto e anúncios web e inquéritos. O esquema predefinido tem o seguinte aspeto:

    <div id="capptain_overlay" class="capptain_category_default">
      <button id="capptain_overlay_close">x</button>
      <div id="capptain_overlay_title"></div>
      <div id="capptain_overlay_body"></div>
      <div id="capptain_overlay_poll"></div>
      <div id="capptain_overlay_buttons">
        <button id="capptain_overlay_exit"></button>
        <button id="capptain_overlay_action"></button>
      </div>
    </div>

Remova o `reach` de objeto da sua configuração, se existir. Este aspeto:

    window.capptain = {
      [...]
      reach: {
        [...]
      }
    }

Remova qualquer outra personalização de alcance, tais como categorias.

#### <a name="remove-deprecated-apis"></a>Remover APIs preteridas
Algumas das APIs de Capptain foram preteridas no SDK do Mobile Engagement Web.

Remover quaisquer chamadas para as APIs seguintes: `agent.connect`, `agent.disconnect`, `agent.pause`, e `agent.sendMessageToDevice`.

Remova qualquer dos seguintes as chamadas de retorno da sua configuração Capptain: `onConnected`, `onDisconnected`, `onDeviceMessageReceived`, e `onPushMessageReceived`.

#### <a name="configuration"></a>Configuração
O Mobile Engagement utiliza uma cadeia de ligação para configurar identificadores do SDK, por exemplo, o identificador da aplicação.

Substitua o ID de aplicação com a cadeia de ligação. Tenha em atenção que as alterações o objetos globais para a configuração do SDK do `capptain` para `azureEngagement`.

Antes da migração:

    window.capptain = {
      appId: ...,
      [...]
    };

Após a migração:

    window.azureEngagement = {
      connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
      [...]
    };

A cadeia de ligação para a sua aplicação é apresentada no portal do Azure.

#### <a name="javascript-apis"></a>APIs de JavaScript
O objeto de JavaScript global `window.capptain` nome foi mudado `window.azureEngagement`, mas pode utilizar o `window.engagement` alias para chamadas de API. Não é possível utilizar este alias para definir a configuração de SDK.

Por exemplo, `capptain.deviceId` fica `engagement.deviceId`, `capptain.agent.startActivity` fica `engagement.agent.startActivity`, e assim sucessivamente.

Se já tiver integrado uma versão anterior do SDK Web do Azure Mobile Engagement na sua aplicação, leia sobre [atualizar procedimentos](mobile-engagement-web-upgrade-procedure.md).

