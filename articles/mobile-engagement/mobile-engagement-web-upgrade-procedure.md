---
title: "Procedimentos de atualização de SDK do Mobile Engagement Web do Azure | Microsoft Docs"
description: "As mais recentes atualizações e procedimentos para o SDK Web do Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: a20529b4-ec8d-4503-8ae9-09b5f0846d5b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: web
ms.devlang: js
ms.topic: article
ms.date: 06/07/2016
ms.author: piyushjo
ms.openlocfilehash: afa8037dcb7a53042fa606e2c4014b442d4be326
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-mobile-engagement-web-sdk-upgrade-procedures"></a>Procedimentos de atualização de SDK do Mobile Engagement Web do Azure
Se já tiver integrado uma versão anterior do SDK Web do Azure Mobile Engagement na sua aplicação web, terá de considerar os seguintes pontos ao atualizar o SDK.

Se saltou a várias versões do SDK do Mobile Engagement Web, poderá ter de concluir vários procedimentos durante o processo de atualização. Por exemplo, se migrar de 1.4.0 para 1.6.0, primeiro siga os procedimentos para atualizar do 1.4.0 para 1.5.0. Em seguida, siga os procedimentos para atualizar do 1.5.0 para 1.6.0.

Qualquer versão de atualização, substituir uma versão anterior do Azure-engagement.js ficheiro com a versão mais recente do ficheiro.

## <a name="upgrade-from-121-to-200"></a>Atualizar do 1.2.1 para 2.0.0
Esta secção descreve como migrar uma integração do SDK do Mobile Engagement Web do serviço Capptain, oferecido pelo Capptain SAS, para uma aplicação do Azure Mobile Engagement. Se estiver a migrar de uma versão anterior, consulte o Web site Capptain migrar primeiro para 1.2.1 e, em seguida, aplique os seguintes procedimentos.

Esta versão do SDK do Mobile Engagement Web não suporta TV inteligente Samsung, Opera TV, webOS ou a funcionalidade de alcance.

> [!IMPORTANT]
> Capptain e o Azure Mobile Engagement não estão no mesmo serviço. O procedimento seguinte realça apenas como migrar a aplicação de cliente. Migrar o SDK do Mobile Engagement Web na aplicação não é migrados os dados de um servidor de Capptain para um servidor de Mobile Engagement.
> 
> 

### <a name="javascript-files"></a>Ficheiros de JavaScript
Substitua o ficheiro capptain-sdk.js o Azure-engagement.js ficheiro e, em seguida, Atualize as importações de script em conformidade.

### <a name="remove-capptain-reach"></a>Remover Capptain alcance
Esta versão do SDK do Mobile Engagement Web não suporta a funcionalidade de alcance. Se integrado Capptain alcance na sua aplicação, terá de removê-lo.

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

### <a name="remove-deprecated-apis"></a>Remover APIs preteridas
Algumas das APIs de Capptain foram preteridas no SDK do Mobile Engagement Web.

Remover quaisquer chamadas para as APIs seguintes: `agent.connect`, `agent.disconnect`, `agent.pause`, e `agent.sendMessageToDevice`.

Remova quaisquer instâncias de chamadas de retorno seguintes da sua configuração de Capptain: `onConnected`, `onDisconnected`, `onDeviceMessageReceived`, e `onPushMessageReceived`.

### <a name="configuration"></a>Configuração
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

A cadeia de ligação para a sua aplicação é apresentada no Portal do Azure.

### <a name="javascript-apis"></a>APIs de JavaScript
O objeto de JavaScript global `window.capptain` nome foi mudado `window.azureEngagement` mas pode utilizar o `window.engagement` alias para chamadas de API. Não é possível utilizar o alias para definir a configuração de SDK.

Por exemplo, `capptain.deviceId` fica `engagement.deviceId`, `capptain.agent.startActivity` fica `engagement.agent.startActivity`, e assim sucessivamente.

