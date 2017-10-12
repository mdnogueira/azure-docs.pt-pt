---
title: "Introdução às Ligações Híbridas de Reencaminhamento do Azure em Node | Microsoft Docs"
description: "Escreva uma aplicação de consola Node.js para Ligações Híbridas de Reencaminhamento do Azure."
services: service-bus-relay
documentationcenter: node
author: sethmanheim
manager: timlt
editor: 
ms.assetid: e44e4867-3cf3-46be-8f8a-7671e2013bc4
ms.service: service-bus-relay
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: node
ms.workload: na
ms.date: 07/07/2017
ms.author: sethm
ms.openlocfilehash: c3bfc45969f250059988129f532edd12dfe3dcfe
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-relay-hybrid-connections"></a>Introdução às Ligações Híbridas de Reencaminhamento

[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

Este tutorial disponibiliza uma introdução às [Ligações Híbridas de Reencaminhamento do Azure](relay-what-is-it.md#hybrid-connections) e mostra como utilizar o Node.js para criar uma aplicação de cliente que envia mensagens para uma aplicação de serviço de escuta correspondente. 

## <a name="what-will-be-accomplished"></a>O que será efetuado

Uma vez que as Ligações Híbridas necessitam de um cliente e um componente de servidor, iremos criar duas aplicações de consola neste tutorial. Eis os passos:

1. Criar um espaço de nomes de Reencaminhamento com o Portal do Azure.
2. Crie uma ligação híbrida com o Portal do Azure.
3. Escrever uma aplicação de consola de servidor para receber mensagens.
4. Escrever uma aplicação de consola de cliente para enviar mensagens.

## <a name="prerequisites"></a>Pré-requisitos

1. [Node.js](https://nodejs.org/en/).
2. Uma subscrição do Azure.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-using-the-azure-portal"></a>1. Criar um espaço de nomes com o Portal do Azure

Se já tiver criado um espaço de nomes de Reencaminhamento, avance para a secção [Criar uma ligação híbrida com o Portal do Azure](#2-create-a-hybrid-connection-using-the-azure-portal).

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="2-create-a-hybrid-connection-using-the-azure-portal"></a>2. Criar uma ligação híbrida com o Portal do Azure

Se já tiver uma ligação híbrida criada, avance para a secção [Criar uma aplicação de servidor](#3-create-a-server-application-listener).

[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="3-create-a-server-application-listener"></a>3. Criar uma aplicação de servidor (serviço de escuta)

Para escutar e receber mensagens do Reencaminhamento, vamos escrever uma aplicação de consola Node.js.

[!INCLUDE [relay-hybrid-connections-node-get-started-server](../../includes/relay-hybrid-connections-node-get-started-server.md)]

## <a name="4-create-a-client-application-sender"></a>4. Criar uma aplicação cliente (remetente)

Para enviar mensagens para o Reencaminhamento, vamos escrever uma aplicação de consola Node.js.

[!INCLUDE [relay-hybrid-connections-node-get-started-client](../../includes/relay-hybrid-connections-node-get-started-client.md)]

## <a name="5-run-the-applications"></a>5. Executar as aplicações

1. Execute a aplicação de servidor: a partir de um tipo de linha de comandos `node listener.js` do Node.js.
2. Execute a aplicação de cliente: a partir de um tipo de linha de comandos `node sender.js` do Node.js e introduza algum texto.
3. Certifique-se de que a consola da aplicação de servidor produz o texto que foi introduzido na aplicação cliente.

![running-applications](./media/relay-hybrid-connections-node-get-started/running-applications.png)

Parabéns, criou uma aplicação de Ligações Híbridas ponto a ponto com o Node.js!

## <a name="next-steps"></a>Passos seguintes:

* [FAQ de Reencaminhamento](relay-faq.md)
* [Criar um espaço de nomes](relay-create-namespace-portal.md)
* [Introdução ao .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Introdução ao Node](relay-hybrid-connections-node-get-started.md)

