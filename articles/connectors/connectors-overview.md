---
title: "Descrição geral dos conectores de aplicações lógicas | Microsoft Docs"
description: "Descrição geral dos conectores que podem ser utilizados numa aplicação lógica"
services: 
documentationcenter: 
author: jeffhollan
manager: erikre
editor: 
tags: connectors
ms.assetid: ca8dab2e-9b69-4b1e-865d-1facd9f0cdac
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/15/2016
ms.author: jehollan
ms.openlocfilehash: 9cbb258ae9e32549669623e6824dd9b18fa1f68f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="using-connectors-in-a-logic-app"></a>A utilização de conectores uma aplicação lógica
Conectores fornecem acesso rápido aos eventos, dados e ações em plataformas, serviços e protocolos.  A lista completa dos conectores que suporta as Logic Apps pode [ser encontrada aqui](apis-list.md).  Conectores podem ser utilizados como um acionador ou uma ação numa aplicação lógica e pode necessitar de um configurado *ligação* utilizar (por exemplo: autorizar uma conta do Twitter para aceder ou colocar em seu nome).

## <a name="basics"></a>Noções básicas
Os conectores são serviços alojados pode aceder como parte de uma aplicação lógica para integrar com outros serviços Dynamics, do Azure, Salesforce, [mais](apis-list.md).  Estes são implementados e geridas pela Microsoft, pelo que pode criar os fluxos de trabalho de integração com o dimensionamento, débito e segurança Tratado.  Pode adicionar um conector para uma aplicação lógica, procurar e selecionar uma ação de conector ou acionar em **Mostrar Microsoft APIs geridas**.

![Menu ação para selecionar o acionador][1]

Cada ação de conector ou acionador terão o conjunto de propriedades a configurar.  Pode clique nos botões de informações para saber mais sobre a ação ou fazer referência a respetiva documentação [para saber mais](apis-list.md).

Se pretender integrar com um serviço ou a API que não esteja ainda um conector, também pode expandir as logic apps através de um [conetor personalizado](../logic-apps/logic-apps-create-api-app.md) ou chame somente diretamente para o serviço através de um protocolo, como HTTP.

## <a name="triggers"></a>Acionadores
Alguns conectores tem um acionador, o que significa um evento a partir do conector que serão acionados uma aplicação lógica e passar todos os dados como parte do acionador.  Um acionador é sempre o primeiro passo para uma aplicação lógica.  Acionadores populares incluem operações como:

* Periodicidade - executada a cada hora
* Quando é recebido um pedido de HTTP
* Quando é adicionado um item a uma fila
* Quando é recebida uma mensagem de e-mail

Alguns acionadores serão acionados instantâneas que um evento ocorre através de uma notificação para a aplicação lógica, e outros utilizadores terão de um intervalo de periodicidade configurado na frequência a aplicação de lógica irá verificar o serviço para um evento (até a cada 15 segundos).  

Quando é recebido um evento, a aplicação de lógica executar serão acionados e as ações no fluxo de trabalho serão iniciado.  Também será capaz de aceder aos dados de Acionador em todo o fluxo de trabalho (por exemplo o acionador 'On um tweet novo' passará a tweet para a execução).

## <a name="actions"></a>Ações
A maioria dos conectores tem uma ou várias ações que podem ser executadas como parte do fluxo de trabalho.  As ações são quaisquer passos que ocorrem após a execução foi desencadeado acionadores.  Para adicionar uma ação clique o **novo passo** botão e procure o conector que pretende utilizar.  Uma vez selecionado (e, depois de configurar qualquer [ligações](#connections) que poderá ser necessário), verá o cartão de ação pode configurar.  Pode selecionar dados dos passos anteriores ao clicar em qualquer um dos tokens para saídas, ou introduza em qualquer outra configuração conforme necessário.

![Configurar uma ação de conector][2]

## <a name="connections"></a>Ligações
A maioria dos conectores necessitam que configure um *ligação* antes de poder utilizar o conector.  A *ligação* qualquer configuração de início de sessão ou ligação necessária para o conector de acesso.  Para os conectores que utilizam a OAuth, criar uma ligação significa assinatura para o serviço (como o Office 365, Salesforce ou GitHub) onde o seu token de acesso pode ser encriptado e armazenado em segurança num arquivo de segredos do Azure.  Outros conectores (como o FTP e o SQL Server) requerem uma ligação que contém a configuração, como o endereço do servidor, nome de utilizador e palavra-passe.  Estes detalhes de configuração de ligação são também encriptados e armazenados em segurança.  Ligações vão conseguir aceder ao serviço para, desde que permite que o serviço.  Para ligações do Azure Active Directory OAuth (por exemplo, o Office 365 e Dynamics) pode continuar a atualizar o token de acesso indefinidamente.  Outros serviços podem colocar os limites em quanto podemos utilizar um token sem que a ser atualizada.  Em geral, determinadas ações, como a alteração de uma palavra-passe irão invalidar todos os tokens de acesso.  

Ligações podem ser visualizadas e geridas no Azure, clicando em **procurar** e selecionando **API ligações**.  O recurso de ligações de API pode ver, editar, atualizar ou voltar a autorizar quaisquer ligações que criou.

## <a name="next-steps"></a>Passos Seguintes
* [Criar a sua primeira aplicação lógica](../logic-apps/logic-apps-create-a-logic-app.md)
* [Saiba mais comuns utiliza e exemplos das logic apps](../logic-apps/logic-apps-examples-and-scenarios.md)
* [Introdução ao enterprise integration acionadores e ações](../logic-apps/logic-apps-enterprise-integration-overview.md)

<!--Image References -->
[1]: ./media/connectors-overview/addAction.png
[2]: ./media/connectors-overview/configureAction.png
