---
title: "Introdução às Ligações Híbridas de Reencaminhamento do Azure em Node | Microsoft Docs"
description: "Como escrever uma aplicação de consola Node para Ligações Híbridas"
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
ms.date: 05/22/2017
ms.author: sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: 532ff423ff53567b6ce40c0ea7ec09a689cee1e7
ms.openlocfilehash: d8f3f6fbe256b34b812369dc1f7492ed4f15d3d3
ms.contentlocale: pt-pt
ms.lasthandoff: 06/05/2017


---
<a id="get-started-with-relay-hybrid-connections" class="xliff"></a>

# Introdução às Ligações Híbridas de Reencaminhamento

[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

<a id="what-will-be-accomplished" class="xliff"></a>

## O que será efetuado

Uma vez que as Ligações Híbridas necessitam de um cliente e um componente de servidor, iremos criar duas aplicações de consola neste tutorial. Eis os passos:

1. Criar um espaço de nomes de Reencaminhamento com o Portal do Azure.
2. Criar uma Ligação Híbrida com o Portal do Azure.
3. Escrever uma aplicação de consola de servidor para receber mensagens.
4. Escrever uma aplicação de consola de cliente para enviar mensagens.

<a id="prerequisites" class="xliff"></a>

## Pré-requisitos

1. [Node.js](https://nodejs.org/en/) (Este exemplo utiliza o Node 7.0).
2. Uma subscrição do Azure.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

<a id="1-create-a-namespace-using-the-azure-portal" class="xliff"></a>

## 1. Criar um espaço de nomes com o Portal do Azure

Se já tiver criado um espaço de nomes de Reencaminhamento, avance para a secção [Criar uma Ligação Híbrida com o Portal do Azure](#2-create-a-hybrid-connection-using-the-azure-portal).

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

<a id="2-create-a-hybrid-connection-using-the-azure-portal" class="xliff"></a>

## 2. Criar uma Ligação Híbrida com o Portal do Azure

Se já tiver uma Ligação Híbrida criada, avance para a secção [Criar uma aplicação de servidor](#3-create-a-server-application-listener).

[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

<a id="3-create-a-server-application-listener" class="xliff"></a>

## 3. Criar uma aplicação de servidor (serviço de escuta)

Para escutar e receber mensagens do Reencaminhamento, vamos escrever uma aplicação de consola Node.js.

[!INCLUDE [relay-hybrid-connections-dotnet-get-started-server](../../includes/relay-hybrid-connections-node-get-started-server.md)]

<a id="4-create-a-client-application-sender" class="xliff"></a>

## 4. Criar uma aplicação cliente (remetente)

Para enviar mensagens para o Reencaminhamento, vamos escrever uma aplicação de consola Node.js.

[!INCLUDE [relay-hybrid-connections-dotnet-get-started-client](../../includes/relay-hybrid-connections-node-get-started-client.md)]

<a id="5-run-the-applications" class="xliff"></a>

## 5. Executar as aplicações

1. Execute a aplicação de servidor.
2. Execute a aplicação cliente e introduza algum texto.
3. Certifique-se de que a consola da aplicação de servidor produz o texto que foi introduzido na aplicação cliente.

![running-applications](./media/relay-hybrid-connections-node-get-started/running-applications.png)

Parabéns, criou uma aplicação de Ligações Híbridas ponto a ponto.

<a id="next-steps" class="xliff"></a>

## Passos seguintes:

* [FAQ de Reencaminhamento](relay-faq.md)
* [Criar um espaço de nomes](relay-create-namespace-portal.md)
* [Introdução ao .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Introdução ao Node](relay-hybrid-connections-node-get-started.md)


