---
title: "Modelo de dados de telemetria de informações de aplicação do Azure - contexto de telemetria | Microsoft Docs"
description: Modelo de dados do contexto de telemetria do Application Insights
services: application-insights
documentationcenter: .net
author: SergeyKanzhelev
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 05/15/2017
ms.author: sergkanz
ms.openlocfilehash: d6a0cad8bda6ca68aa691867e84f540c5ac9f6f3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="telemetry-context-application-insights-data-model"></a>Contexto de telemetria: modelo de dados do Application Insights

Todos os itens de telemetria podem ter um campos de contexto com tipo seguro. Cada campo permite um cenário de monitorização específico. Utilize a coleção de propriedades personalizadas para armazenar informações contextuais personalizadas ou específicos da aplicação.


##<a name="application-version"></a>Versão da aplicação

As informações nos campos de contexto de aplicação são sempre sobre a aplicação que está a enviar a telemetria. Versão da aplicação é utilizado para analisar as alterações de tendência o comportamento da aplicação e respetivos correlação para as implementações.

Comprimento máximo: 1024


##<a name="client-ip-address"></a>Endereço IP do cliente

O endereço IP do dispositivo cliente. São suportados IPv4 e IPv6. Quando a telemetria é enviada a partir de um serviço, o contexto de localização é sobre o utilizador que iniciou a operação no serviço. Application Insights extrair as informações de geolocalização do IP de cliente e, em seguida, truncá-lo. Por isso, IP de cliente por si só não pode ser utilizado como informações de identificação do utilizador final. 

Comprimento máximo: 46


##<a name="device-type"></a>Tipo de dispositivo

Originalmente este campo foi utilizado para indicar o tipo de dispositivo que do utilizador final da aplicação está a utilizar. Hoje em dia utilizada principalmente para distinguir a telemetria de JavaScript com o tipo de dispositivo Browser de telemetria do lado do servidor com o dispositivo escreva 'PC'.

Comprimento máximo: 64


##<a name="operation-id"></a>Id de operação

Um identificador exclusivo da operação de raiz. Este identificador permite a telemetria de grupo através de vários componentes. Consulte [correlação de telemetria](application-insights-correlation.md) para obter mais detalhes. O id da operação é criado por um pedido ou uma vista de página. Todos os outra telemetria define este campo para o valor para a vista de pedido ou página contentora. 

Comprimento máximo: 128


##<a name="parent-operation-id"></a>ID de operação de principal

O identificador exclusivo do principal do item de telemetria de imediato. Consulte [correlação de telemetria](application-insights-correlation.md) para obter mais detalhes.

Comprimento máximo: 128


##<a name="operation-name"></a>Nome da operação

O nome (agrupar) da operação. O nome da operação é criado por um pedido ou uma vista de página. Todos os outros itens de telemetria defina este campo para o valor para a vista de pedido ou página contentora. Nome da operação é utilizado para localizar todos os itens de telemetria para um grupo de operações (por exemplo "GET Home/Index'). Esta propriedade de contexto é utilizada para responder a perguntas como "quais são as exceções típicas nesta página."

Comprimento máximo: 1024


##<a name="synthetic-source-of-the-operation"></a>Origem sintética da operação

Nome da origem sintético. Alguma telemetria da aplicação pode representar o tráfego sintético. É possível indexar o web site, testes de disponibilidade do site ou rastreios de diagnóstico bibliotecas, como o Application Insights SDK próprio de crawler de web.

Comprimento máximo: 1024


##<a name="session-id"></a>id de sessão

ID de sessão - a instância de interação do utilizador com a aplicação. As informações nos campos de contexto de sessão são sempre sobre o utilizador final. Quando a telemetria é enviada a partir de um serviço, o contexto da sessão é sobre o utilizador que iniciou a operação no serviço.

Comprimento máximo: 64


##<a name="anonymous-user-id"></a>Id de utilizador anónimo

Id de utilizador anónimo. Representa o utilizador final da aplicação. Quando a telemetria é enviada a partir de um serviço, é o contexto de utilizador sobre o utilizador que iniciou a operação no serviço.

[Amostragem](app-insights-sampling.md) é um das técnicas para minimizar a quantidade de telemetria recolhida. O exemplo de entrada ou saída todas as telemetrias correlacionada tenta algoritmo de amostragem. Id de utilizador anónimo é utilizada para geração de pontuação de amostragem. Id de utilizador anónimo, por isso, deve ser um valor aleatório suficiente. 

A utilização de id de utilizador anónimo para armazenar o nome de utilizador é uma utilização indevida do campo. Utilize o id de utilizador autenticado.

Comprimento máximo: 128


##<a name="authenticated-user-id"></a>Id de utilizador autenticado

Id de utilizador autenticado. O oposto do id de utilizador anónimo, este campo representa o utilizador com um nome amigável. Dado que as respetivas informações de PII, não por predefinição é recolhido pelo SDK mais.

Comprimento máximo: 1024


##<a name="account-id"></a>Id da conta

Nas aplicações de multi-inquilinos, este é o ID de conta ou o nome, que o utilizador está a funcionar com. Exemplos podem ser o ID de subscrição para o portal ou blogue nome blogging plataforma Azure.

Comprimento máximo: 1024


##<a name="cloud-role"></a>Função de nuvem

Nome da função a aplicação faz parte. Mapas diretamente para o nome de função no azure. Também pode ser utilizada para distinguir micro serviços, que fazem parte de uma única aplicação.

Comprimento máximo: 256


##<a name="cloud-role-instance"></a>Instância de função na nuvem

Nome da instância onde a aplicação está em execução. Nome do computador no local, nome da instância do Azure.

Comprimento máximo: 256


##<a name="internal-sdk-version"></a>Interno: Versão do SDK

Versão do SDK. Consulte https://github.com/Microsoft/ApplicationInsights-Home/blob/master/SDK-AUTHORING.md#sdk-version-specification para obter informações.

Comprimento máximo: 64


##<a name="internal-node-name"></a>Interno: O nome do nó

Este campo representa o nome de nó utilizado para fins de faturação. Utilizá-lo para substituir a deteção padrão de nós.

Comprimento máximo: 256


## <a name="next-steps"></a>Passos seguintes

- Saiba como [expandir e filtrar telemetria](app-insights-api-filtering-sampling.md).
- Consulte [modelo de dados](application-insights-data-model.md) para o modelo de tipos e os dados do Application Insights.
- Veja a coleção de propriedades de contexto padrão [configuração](app-insights-configuration-with-applicationinsights-config.md#telemetry-initializers-aspnet).
