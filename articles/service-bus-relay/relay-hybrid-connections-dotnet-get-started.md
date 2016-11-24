---
title: "Introdução às Ligações Híbridas de Reencaminhamento | Microsoft Docs"
description: "Como escrever uma aplicação de consola C# para Ligações Híbridas"
services: service-bus-relay
documentationcenter: .net
author: jtaubensee
manager: timlt
editor: 
ms.assetid: d1386900-b942-4abf-acfc-38d2ef826253
ms.service: service-bus-relay
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 10/28/2016
ms.author: jotaub,sethm
translationtype: Human Translation
ms.sourcegitcommit: 849d7995e9c74bc929c4f791ae9155ca18ddb77b
ms.openlocfilehash: 3ab92ba4e5c19d4b713ceb2da30835542f343d68


---
# <a name="get-started-with-relay-hybrid-connections"></a>Introdução às Ligações Híbridas de Reencaminhamento
[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

## <a name="what-will-be-accomplished"></a>O que será efetuado
Uma vez que as Ligações Híbridas necessitam de um cliente e um componente de servidor, iremos criar duas aplicações de consola neste tutorial. Eis os passos:

1. Criar um espaço de nomes de Reencaminhamento com o Portal do Azure.
2. Criar uma Ligação Híbrida com o Portal do Azure.
3. Escrever uma aplicação de consola de servidor para receber mensagens.
4. Escrever uma aplicação de consola de cliente para enviar mensagens.

## <a name="prerequisites"></a>Pré-requisitos
1. [Visual Studio 2013 ou Visual Studio 2015](http://www.visualstudio.com). Os exemplos neste tutorial utilizam o Visual Studio 2015.
2. Uma subscrição do Azure.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-using-the-azure-portal"></a>1. Criar um espaço de nomes com o Portal do Azure
Se já tiver criado um espaço de nomes de Reencaminhamento, avance para a secção [Criar uma Ligação Híbrida com o Portal do Azure](#2-create-a-hybrid-connection-using-the-azure-portal).

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="2-create-a-hybrid-connection-using-the-azure-portal"></a>2. Criar uma Ligação Híbrida com o Portal do Azure
Se já tiver uma Ligação Híbrida criada, avance para a secção [Criar uma aplicação de servidor](#3-create-a-server-application-listener).

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




<!--HONumber=Nov16_HO3-->


