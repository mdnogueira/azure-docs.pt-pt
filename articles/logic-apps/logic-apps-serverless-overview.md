---
title: "Descrição geral do Azure sem servidor | Microsoft Docs"
description: "Crie poderosas soluções na nuvem sem ter de refletir sobre a infraestrutura."
keywords: 
services: logic-apps
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: 6803e22a78e27c15ff4fec301cd5bdd55aacd3e3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-azure-serverless-with-functions-and-logic-apps"></a>Descrição geral do Azure sem servidor com as funções e as Logic Apps

Aplicações sem servidor oferecem vantagens de um aumento de velocidade de desenvolvimento, a redução de código necessários e na simplicidade com uma escala.  Este artigo entra os atributos diferentes de soluções sem servidor e ofertas sem servidor do Azure.

## <a name="what-is-serverless"></a>O que é sem servidor?

Sem servidor não significa que existem não existem servidores – tal significa que não tem o Programador de preocupar com servidores.  Uma grande parte do desenvolvimento de aplicação tradicionais é as respostas a questões à volta de dimensionamento, alojar e soluções para satisfazer os pedidos da aplicação de monitorização.  Com Serverless, estas perguntas são tratadas como parte da solução.  Além disso, as aplicações sem servidor são faturadas um plano com base no consumo.  Se a aplicação nunca é utilizada, nunca é tarifas de cobrada uma taxa.  Estas funcionalidades permitem aos programadores focar-se exclusivamente com a lógica de negócio da solução.

Os serviços principais no Azure em torno Serverless são [das funções do Azure](https://azure.microsoft.com/services/functions/) e [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/).  Tanto destas soluções siga princípios acima, e permitem aos programadores criarem aplicações cloud robusta com código mínimas.

## <a name="what-are-azure-functions"></a>Quais são as funções do Azure?

Funções do Azure é uma solução para uma fácil execução de pequenos blocos de código, ou "funções", na nuvem. É possível escrever apenas o código que necessita para o problema em questão, sem ter de se preocupar com toda a aplicação ou a infraestrutura para executá-la. As funções podem tornar o desenvolvimento ainda mais produtivo, e pode utilizar a linguagem de programação de escolha, tal como c#, F #, Node.js, Python ou PHP. Só paga pelos tempo que seu código é executado e Azure dimensiona conforme necessário.

Se quiser iniciar de imediato e utilizar as Funções do Azure, comece por [Criar a sua primeira Função do Azure](../azure-functions/functions-create-first-azure-function.md). Se estiver à procura de mais informações técnicas acerca das Funções, consulte a [referência para programadores](../azure-functions/functions-reference.md).

## <a name="what-are-azure-logic-apps"></a>Quais são Azure Logic Apps?

Aplicações lógicas do Azure fornece uma forma para simplificar e implementar integrações escaláveis e fluxos de trabalho na nuvem. Fornece um designer visual para modelar e automatizar o processo como uma série de passos chamado um fluxo de trabalho.  Existem [muitos conetores](../connectors/apis-list.md) nos vários serviços de nuvem e no local para rapidamente ligar uma aplicação sem servidor para outras APIs.  Uma aplicação lógica começa com um acionador (por exemplo, «Quando uma conta é adicionada ao Dynamics CRM») e depois de acionadas podem iniciar muitas ações de combinações, conversões e lógica de condição.  As aplicações lógicas é uma escolha ideal quando da orquestração diferentes funções do Azure num processo -, especialmente quando o processo requer interação com um sistema externo ou a API.

Para começar a utilizar com as Logic Apps, inicie o com [criar a sua primeira aplicação de lógica](logic-apps-create-a-logic-app.md).  Se estiver à procura de mais informações técnicas sobre Logic Apps, consulte o [referência para programadores](logic-apps-workflow-actions-triggers.md).

## <a name="how-can-i-build-and-deploy-serverless-applications-in-azure"></a>Como criar e implementar aplicações sem servidor no Azure?

Azure fornece um conjunto avançado de ferramentas de desenvolvimento, implementação e gestão de aplicações sem servidor.  As aplicações podem ser criadas diretamente no portal do Azure, ou com [as ferramentas do Visual Studio](logic-apps-serverless-get-started-vs.md).  Depois de uma aplicação foi desenvolvida pode ser [implementadas de forma instantânea](logic-apps-create-deploy-template.md).  Azure também fornece a monitorização de aplicações sem servidor.  Esta monitorização pode ser acedida no portal do Azure, através da API ou SDKs, ou com ferramentas integrada para OMS e o Application Insights.

## <a name="next-steps"></a>Passos seguintes

* [Introdução à criação de uma aplicação sem servidor no Visual Studio](logic-apps-serverless-get-started-vs.md)
* [Criar um dashboard de conhecimentos de cliente com Serverless](logic-apps-scenario-social-serverless.md)
* [Criar um modelo de implementação para uma aplicação lógica](logic-apps-create-deploy-template.md)