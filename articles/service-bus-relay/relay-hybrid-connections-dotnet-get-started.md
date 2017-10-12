---
title: "Introdução às Ligações Híbridas de Reencaminhamento do Azure | Microsoft Docs"
description: "Escreva uma aplicação de consola C# para Ligações Híbridas de Reencaminhamento do Azure."
services: service-bus-relay
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: d1386900-b942-4abf-acfc-38d2ef826253
ms.service: service-bus-relay
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 07/07/2017
ms.author: sethm
ms.openlocfilehash: 1af23bfd46dd7d3781505473f7c1d86e65ea9bc7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-relay-hybrid-connections"></a>Introdução às Ligações Híbridas de Reencaminhamento
[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

Este tutorial disponibiliza uma introdução às [Ligações Híbridas de Reencaminhamento do Azure](relay-what-is-it.md#hybrid-connections) e mostra como utilizar o .NET para criar uma aplicação de cliente que envia mensagens para uma aplicação de serviço de escuta correspondente. 

## <a name="what-will-be-accomplished"></a>O que será efetuado
Uma vez que as Ligações Híbridas necessitam de um cliente e um componente de servidor, este tutorial cria duas aplicações de consola. Eis os passos:

1. Criar um espaço de nomes de Reencaminhamento com o Portal do Azure.
2. Crie uma ligação híbrida nesse espaço de nomes, com o portal do Azure.
3. Escrever uma aplicação de consola (serviço de escuta) de servidor para receber mensagens.
4. Escrever uma aplicação de consola (remetente) de cliente para enviar mensagens.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa dos seguintes pré-requisitos:

1. [Visual Studio 2015 ou superior](http://www.visualstudio.com). Os exemplos neste tutorial utilizam o Visual Studio 2017.
2. Uma subscrição do Azure.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-using-the-azure-portal"></a>1. Criar um espaço de nomes com o Portal do Azure
Se já tiver criado um espaço de nomes de Reencaminhamento, avance para a secção [Criar uma ligação híbrida com o Portal do Azure](#2-create-a-hybrid-connection-using-the-azure-portal).

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="2-create-a-hybrid-connection-using-the-azure-portal"></a>2. Criar uma ligação híbrida com o Portal do Azure
Se já tiver criado uma ligação híbrida, avance para a secção [Criar uma aplicação de servidor](#3-create-a-server-application-listener).

[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="3-create-a-server-application-listener"></a>3. Criar uma aplicação de servidor (serviço de escuta)
Para ouvir e receber mensagens do Reencaminhamento, vamos criar uma aplicação de consola C# com o Visual Studio.

[!INCLUDE [relay-hybrid-connections-dotnet-get-started-server](../../includes/relay-hybrid-connections-dotnet-get-started-server.md)]

## <a name="4-create-a-client-application-sender"></a>4. Criar uma aplicação cliente (remetente)
Para enviar mensagens para o Reencaminhamento, vamos criar uma aplicação de consola C# com o Visual Studio.

[!INCLUDE [relay-hybrid-connections-dotnet-get-started-client](../../includes/relay-hybrid-connections-dotnet-get-started-client.md)]

## <a name="5-run-the-applications"></a>5. Executar as aplicações
1. Execute a aplicação de servidor.
2. Execute a aplicação cliente e introduza algum texto.
3. Certifique-se de que a consola da aplicação de servidor produz o texto que foi introduzido na aplicação cliente.

![running-applications](./media/relay-hybrid-connections-dotnet-get-started/running-applications.png)

Parabéns, criou uma aplicação de Ligações Híbridas ponto a ponto.

## <a name="next-steps"></a>Passos seguintes:
* [FAQ de Reencaminhamento](relay-faq.md)
* [Criar um espaço de nomes](relay-create-namespace-portal.md)
* [Introdução ao Nó](relay-hybrid-connections-node-get-started.md)

