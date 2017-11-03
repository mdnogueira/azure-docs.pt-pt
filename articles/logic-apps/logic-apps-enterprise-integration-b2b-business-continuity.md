---
title: "Recuperação de desastres para a conta de integração de B2B - Azure Logic Apps | Microsoft Docs"
description: "Recuperação de desastres do Logic Apps B2B"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: cf44af18-1fe5-41d5-9e06-cc57a968207c
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/10/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: 4896d9da456bcc17b1a4d92259ef3d57f8575d8b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="logic-apps-b2b-cross-region-disaster-recovery"></a>Recuperação de desastre por várias regiões lógica B2B de aplicações

B2B cargas de trabalho envolvem transações dinheiro como encomendas pendentes e faturas. Durante um evento de desastre, é fundamental para uma empresa para rapidamente recuperar para cumprir os SLAs de nível empresarial acordadas com os respetivos parceiros. Este artigo demonstra como criar um plano de continuidade do negócio para cargas de trabalho B2B. 

* Preparação de recuperação de desastre 
* Efetuar a ativação pós-falha região secundária durante um evento de desastre 
* Reverter para a região primária após um evento de desastre

## <a name="disaster-recovery-readiness"></a>Preparação de recuperação de desastre  

1. Identifique uma região secundária e crie um [conta integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) na região secundária.

2. Adicione parceiros, esquemas e contratos para os fluxos de mensagens requerida onde o estado de execução tem de ser replicadas para a conta de integração de região secundária.

   > [!TIP]
   > Certifique-se de que existe consistência na Convenção de nomenclatura o artefacto de conta de integração em regiões. 

3. Para solicitar o estado de execução a partir da região primária, crie uma aplicação lógica na região secundária. 

   Esta aplicação lógica deve ter um *acionador* e um *ação*. 
   O acionador deve ligar à conta de integração de região primária e a ação deve ligar à conta de integração de região secundária. 
   Com base num intervalo de tempo, o acionador consulta região primária tabela de estado de execução e obtém os registos de novo, se aplicável. A ação atualiza-los para a conta de integração de região secundária. 
   Isto ajuda a obter o estado de incremental runtime da região primária para a região secundária.

4. Continuidade do negócio na conta de integração de Logic Apps foi concebida para suportar com base em protocolos de B2B - X12, AS2 e EDIFACT. Para obter passos detalhados, selecione as respetivas ligações.

5. A recomendação é implementar demasiado todos os recursos de região primária numa região secundária. 

   Recursos de região primária incluem a SQL Database do Azure ou do Azure Cosmos DB, Service Bus do Azure e utilizado para as mensagens, a API Management do Azure e a funcionalidade de Azure Logic Apps no App Service do Azure de Event Hubs do Azure.   

6. Estabelece uma ligação a partir de uma região primária para uma região secundária. Para solicitar o estado de execução a partir de uma região primária, crie uma aplicação lógica numa região secundária. 

   A aplicação lógica deve ter um acionador e uma ação. 
   O acionador deve ligar a uma conta de integração de região primária. 
   A ação deve ligar a uma conta de integração de região secundária. 
   Com base num intervalo de tempo, o acionador consulta região primária tabela de estado de execução e obtém os registos de novo, se aplicável. 
   A ação atualiza-los para uma conta de integração de região secundária. 
   Este processo ajuda a obter o estado de incremental runtime da região primária para a região secundária.

Continuidade do negócio numa conta integração Logic Apps fornece suporte com base no B2B protocolos X12, AS2 e EDIFACT. Para obter passos detalhados sobre como utilizar X12 e AS2, consulte [X12](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#x12) e [AS2](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#as2) neste artigo.

## <a name="fail-over-to-a-secondary-region-during-a-disaster-event"></a>Efetuar a ativação pós-falha para uma região secundária durante um evento de desastre

Durante um evento de desastre, quando a região primária não está disponível para a continuidade do negócio, tráfego direta para a região secundária. Uma região secundária ajuda-o a empresas para recuperar as funções rapidamente para satisfazer o RPO/RTO acordadas pelos respetivos parceiros. Também minimiza esforços para efetuar a ativação pós-falha de uma região noutra região. 

Há uma latência esperada ao copiar os números de controlo de uma região primária para uma região secundária. Para evitar o envio de números de controlo gerado duplicado para parceiros durante um evento de desastre, recomenda-se aumentar os números de controlo nos contratos de região secundária utilizando [cmdlets do PowerShell](https://blogs.msdn.microsoft.com/david_burgs_blog/2017/03/09/fresh-of-the-press-new-azure-powershell-cmdlets-for-upcoming-x12-connector-disaster-recovery).

## <a name="fall-back-to-a-primary-region-post-disaster-event"></a>Reverter para um evento de pós-desastre região primária

Para reverter para a região primária quando estiver disponível, siga estes passos:

1. Pare a aceitação das mensagens de parceiros na região secundária.  

2. Aumentar os números de controlo gerado para todos os contratos de região primária utilizando [cmdlets do PowerShell](https://blogs.msdn.microsoft.com/david_burgs_blog/2017/03/09/fresh-of-the-press-new-azure-powershell-cmdlets-for-upcoming-x12-connector-disaster-recovery).  

3. Tráfego direto da região secundária para a região primária.

4. Certifique-se de que a aplicação de lógica criada na região secundária para extrair a executar o estado da região primária está ativada.

## <a name="x12"></a>X12 

Continuidade do negócio para EDI X 12 documentos baseia-se em números de controlo:

> [!TIP]
> Também pode utilizar o [X12 rápido iniciar modelo](https://azure.microsoft.com/documentation/templates/201-logic-app-x12-disaster-recovery-replication/) para criar as logic apps. Criar contas de automatização primária e secundária são pré-requisitos para utilizar o modelo. O modelo de ajuda a criar as duas logic apps, uma para números de controlo recebido e outra para números de controlo gerado. Respetivos acionadores e ações são criadas nas logic apps, ligar o acionador para a conta do principal de integração e a ação para a conta de integração secundário.

**Pré-requisitos**

Para ativar a recuperação após desastre para as mensagens de entrada, selecione as definições de verificação duplicado no X12 receber definições do contrato.

![Selecione as definições de verificação duplicado](./media/logic-apps-enterprise-integration-b2b-business-continuity/dupcheck.png)  

1. Criar um [aplicação lógica](../logic-apps/logic-apps-create-a-logic-app.md) numa região secundária.    

2. Pesquisar nos **X12**e selecione **X12-quando um número de controlo é modificado**.   

   ![Procure o X12](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn1.png)

   O acionador pede-lhe estabelecer uma ligação a uma conta de integração. 
   O acionador deve ser ligado a uma conta de integração de região primária.

3. Introduza um nome de ligação, selecione o *conta de integração de região primária* na lista e escolha **criar**.   

   ![Nome de conta de integração de região primária](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn2.png)

4. O **DateTime para iniciar a sincronização de número de controlo** definição é opcional. O **frequência** pode ser definido como **dia**, **hora**, **minuto**, ou **segundo** com um intervalo.   

   ![DateTime e a frequência](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn3.png)

5. Selecione **novo passo** > **adicionar uma ação**.

   ![Novo passo, adicionar uma ação](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn4.png)

6. Pesquisar nos **X12**e selecione **X12-adicionar ou atualizar os números de controlo**.   

   ![Adicionar ou atualizar os números de controlo](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn5.png)

7. Para ligar uma ação para uma conta de integração de região secundária, selecione **Alterar ligação** > **Adicionar nova ligação** para obter uma lista das contas de integração disponível. Introduza um nome de ligação, selecione o *conta de integração de região secundária* na lista e escolha **criar**. 

   ![Nome de conta de integração de região secundária](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn6.png)

8. Mudar para entradas em bruto clicando no ícone no canto superior direito.

   ![Mudar para entradas não processadas](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12rawinputs.png)

9. Selecione o corpo do selecionador de conteúdo dinâmico e guarde a aplicação lógica.

   ![Campos de conteúdo dinâmicos](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn7.png)

   Com base num intervalo de tempo, o acionador consulta a tabela de número de controlo de região primária recebido e obtém os registos de novo. 
   A ação atualiza os registos na conta de integração de região secundária. 
   Se não existirem não existem atualizações, o estado de Acionador é apresentado como **ignorados**.   

   ![Tabela de número de controlo](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12recevicedcn8.png)

Com base num intervalo de tempo, o estado de incremental runtime replica a partir de uma região primária para uma região secundária. Durante um evento de desastre, quando a região primária não está disponível, direto tráfego para a região secundária para a continuidade do negócio. 

## <a name="edifact"></a>EDIFACT 

Continuidade do negócio para documentos EDI EDIFACT baseia-se em números de controlo.

**Pré-requisitos**

Para ativar a recuperação após desastre para as mensagens de entrada, selecione as definições de verificação duplicado no receber definições seu contrato EDIFACT.

![Selecione as definições de verificação duplicado](./media/logic-apps-enterprise-integration-b2b-business-continuity/edifactdupcheck.png)  

1. Criar um [aplicação lógica](../logic-apps/logic-apps-create-a-logic-app.md) numa região secundária.    

2. Pesquisar nos **EDIFACT**e selecione **EDIFACT - quando um número de controlo é modificado**.

   ![Procure EDIFACT](./media/logic-apps-enterprise-integration-b2b-business-continuity/edifactcn1.png)

   O acionador pede-lhe estabelecer uma ligação a uma conta de integração. 
   O acionador deve ser ligado a uma conta de integração de região primária. 

3. Introduza um nome de ligação, selecione o *conta de integração de região primária* na lista e escolha **criar**.    

   ![Nome de conta de integração de região primária](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12CN2.png)

4. O **DateTime para iniciar a sincronização de número de controlo** definição é opcional. O **frequência** pode ser definido como **dia**, **hora**, **minuto**, ou **segundo** com um intervalo.    

   ![DateTime e a frequência](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn3.png)

6. Selecione **novo passo** > **adicionar uma ação**.    

   ![Novo passo, adicionar uma ação](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn4.png)

7. Pesquisar nos **EDIFACT**e selecione **EDIFACT - adicionar ou atualizar os números de controlo**.   

   ![Adicionar ou atualizar os números de controlo](./media/logic-apps-enterprise-integration-b2b-business-continuity/EdifactChooseAction.png)

8. Para ligar uma ação para uma conta de integração de região secundária, selecione **Alterar ligação** > **Adicionar nova ligação** para obter uma lista das contas de integração disponível. Introduza um nome de ligação, selecione o *conta de integração de região secundária* na lista e escolha **criar**.

   ![Nome de conta de integração de região secundária](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn6.png)

9. Mudar para entradas em bruto clicando no ícone no canto superior direito.

   ![Mudar para entradas não processadas](./media/logic-apps-enterprise-integration-b2b-business-continuity/Edifactrawinputs.png)

10. Selecione o corpo do selecionador de conteúdo dinâmico e guarde a aplicação lógica.   

   ![Campos de conteúdo dinâmicos](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12CN7.png)

   Com base num intervalo de tempo, o acionador consulta a tabela de número de controlo de região primária recebido e obtém os registos de novo.
   A ação atualiza os registos para a conta de integração de região secundária. 
   Se não existirem não existem atualizações, o estado de Acionador é apresentado como **ignorados**.

   ![Tabela de número de controlo](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12recevicedcn8.png)

Com base num intervalo de tempo, o estado de incremental runtime replica a partir de uma região primária para uma região secundária. Durante um evento de desastre, quando a região primária não está disponível, direto tráfego para a região secundária para a continuidade do negócio. 

## <a name="as2"></a>AS2 

Continuidade do negócio para documentos que utilizam o protocolo AS2 baseia o ID de mensagem e o valor de Dinâmicas.

> [!TIP]
> Também pode utilizar o [AS2 início rápido modelo](https://github.com/Azure/azure-quickstart-templates/pull/3302) para criar as logic apps. Criar contas de automatização primária e secundária são pré-requisitos para utilizar o modelo. O modelo de ajuda a criar uma aplicação lógica que tem um acionador e uma ação. A aplicação lógica cria uma ligação a partir de um acionador para uma conta de integração primária e uma ação para uma conta de integração secundário.

1. Criar um [aplicação lógica](../logic-apps/logic-apps-create-a-logic-app.md) na região secundária.  

2. Pesquisar nos **AS2**e selecione **AS2 - valor MIC de quando é criado**.   

   ![Procure AS2](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid1.png)

   Um acionador pede-lhe estabelecer uma ligação a uma conta de integração. 
   O acionador deve ser ligado a uma conta de integração de região primária. 
   
3. Introduza um nome de ligação, selecione o *conta de integração de região primária* na lista e escolha **criar**.

   ![Nome de conta de integração de região primária](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid2.png)

4. O **DateTime para iniciar a sincronização de valor MIC** definição é opcional. O **frequência** pode ser definido como **dia**, **hora**, **minuto**, ou **segundo** com um intervalo.   

   ![DateTime e a frequência](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid3.png)

5. Selecione **novo passo** > **adicionar uma ação**.  

   ![Novo passo, adicionar uma ação](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid4.png)

6. Pesquisar nos **AS2**e selecione **AS2 - adicionar ou atualizar o conteúdo do MIC**.  

   ![Adição de MIC ou atualização](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid5.png)

7. Para ligar uma ação para uma conta de integração secundária, selecione **Alterar ligação** > **Adicionar nova ligação** para obter uma lista das contas de integração disponível. Introduza um nome de ligação, selecione o *conta de integração de região secundária* na lista e escolha **criar**.

   ![Nome de conta de integração de região secundária](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid6.png)

8. Mudar para entradas em bruto clicando no ícone no canto superior direito.

   ![Mudar para entradas não processadas](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2rawinputs.png)

9. Selecione o corpo do selecionador de conteúdo dinâmico e guarde a aplicação lógica.   

   ![Conteúdo dinâmico](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid7.png)

   Com base num intervalo de tempo, o acionador consulta a tabela de região primária e obtém os registos de novo. A ação atualiza-los para a conta de integração de região secundária. 
   Se não existirem não existem atualizações, o estado de Acionador é apresentado como **ignorados**.  

   ![Tabela de região primária](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid8.png)

Com base num intervalo de tempo, o estado de incremental runtime replica a partir de região primária para a região secundária. Durante um evento de desastre, quando a região primária não está disponível, direto tráfego para a região secundária para a continuidade do negócio. 

## <a name="next-steps"></a>Passos seguintes

[Monitorizar mensagens B2B](logic-apps-monitor-b2b-message.md)

