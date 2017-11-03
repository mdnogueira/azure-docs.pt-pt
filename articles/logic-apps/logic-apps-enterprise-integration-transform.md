---
title: "Converter os dados XML com transformações - Azure Logic Apps | Microsoft Docs"
description: "Criar as transformações ou mapps converter dados XML entre formatos nas logic apps, utilizando o SDK de integração do Enterprise"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: cgronlun
ms.assetid: add01429-21bc-4bab-8b23-bc76ba7d0bde
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: fb6027769377b3527b11f7831dab3bb8d7061c84
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="enterprise-integration-with-xml-transforms"></a>Integração empresarial com transformações XML
## <a name="overview"></a>Descrição geral
O conector de transformação de integração do Enterprise converte dados de um formato para outro formato. Por exemplo, pode ter uma mensagem a receber que contém a data atual no formato YearMonthDay. Pode utilizar uma transformação para reformatar a data para estar no formato MonthDayYear.

## <a name="what-does-a-transform-do"></a>O que faz uma transformação?
Uma transformação, que também é conhecido como um mapa, é composta por um esquema de XML de origem (entrada) e um esquema de XML de destino (saída). Pode utilizar diferentes funções incorporadas para ajudar a manipular ou controlar os dados, incluindo manipulações de cadeia, atribuições condicionais, expressões aritméticas, ao mesmo tempo data hora e o mesmo ciclo construções.

## <a name="how-to-create-a-transform"></a>Como criar uma transformação?
Pode criar um mapa/transformação utilizando o Visual Studio [Enterprise integração SDK](https://aka.ms/vsmapsandschemas). Quando tiver terminado de criar e testar a transformação, carregue a transformação na sua conta de integração. 

## <a name="how-to-use-a-transform"></a>Como utilizar uma transformação
Depois de carregar o mapa/transformação na sua conta de integração, pode utilizá-lo para criar uma aplicação lógica. A aplicação lógica executa as transformações sempre que a aplicação lógica é acionada (e conteúdo de entrada que tem de ser transformados).

**Eis os passos a utilizar uma transformação**:

### <a name="prerequisites"></a>Pré-requisitos

* Criar uma conta de integração e adicione um mapeamento para o mesmo  

Agora que já Tratado dos pré-requisitos, está na altura de criar a sua aplicação lógica:  

1. Criar uma aplicação lógica e [ligá-lo à sua conta de integração](../logic-apps/logic-apps-enterprise-integration-accounts.md "saiba associar uma conta de integração para uma aplicação lógica") que contém o mapa.
2. Adicionar um **pedido** acionador à sua aplicação lógica  
   ![](./media/logic-apps-enterprise-integration-transforms/transform-1.png)    
3. Adicionar o **transformação XML** ação selecionando primeiro **adicionar uma ação**   
   ![](./media/logic-apps-enterprise-integration-transforms/transform-2.png)   
4. Introduza a palavra *transformar* na caixa de pesquisa para filtrar todas as ações de que pretende utilizar  
   ![](./media/logic-apps-enterprise-integration-transforms/transform-3.png)  
5. Selecione o **transformação XML** ação   
6. Adicionar o XML **conteúdo** que lhe transformar. Pode utilizar os dados XML recebe o pedido de HTTP como o **conteúdo**. Neste exemplo, selecione o corpo do pedido HTTP que acionou a aplicação lógica.
7. Selecione o nome do **mapa** que pretende utilizar para efetuar a transformação. O mapa já deve estar na sua conta de integração. No passo anterior, já deu ao acesso de aplicação lógica à sua conta de integração que contém o mapa.      
   ![](./media/logic-apps-enterprise-integration-transforms/transform-4.png) 
8. Guarde o trabalho  
    ![](./media/logic-apps-enterprise-integration-transforms/transform-5.png) 

Neste momento, tiver terminado de configurar o mapa. Uma aplicação do mundo real, poderá armazenar os dados transformados numa aplicação LOB, tais como o SalesForce. Pode facilmente como uma ação para enviar o resultado da transformação Salesforce. 

Pode agora testar a transformação ao efetuar um pedido para o ponto final de HTTP.  

## <a name="features-and-use-cases"></a>Funcionalidades e casos de utilização
* A transformação criada num mapa pode ser simple, tais como copiar um nome e endereço de um documento para outra. Em alternativa, pode criar transformações mais complexas utilizando as operações de out of box mapa.  
* Várias operações de mapa ou as funções estejam prontamente disponíveis, incluindo cadeias, as funções de tempo de data e assim sucessivamente.  
* Para fazer uma cópia de dados diretas entre os esquemas. O mapeador de incluído no SDK, este é tão simple como desenhar uma linha que liga os elementos do esquema de origem com os seus homólogos do esquema de destino.  
* Ao criar um mapa, ver uma representação gráfica do mapa que mostra todas as relações e ligações que cria.
* Utilize a funcionalidade de mapa de teste para adicionar uma mensagem XML de exemplo. Com um clique simple, pode testar o mapa que criou e ver o resultado gerado.  
* Carregar o maps existentes  
* Inclui suporte para o formato XML.

## <a name="learn-more"></a>Saiba mais
* [Saiba mais sobre o Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Saiba mais sobre o pacote de integração do Enterprise")  
* [Saiba mais sobre o maps](../logic-apps/logic-apps-enterprise-integration-maps.md "Saiba mais sobre a maps de integração do enterprise")  

