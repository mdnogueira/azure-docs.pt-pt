---
title: "Introdução ao Azure Scheduler no portal do Azure | Microsoft Docs"
description: "Introdução ao Agendador do Azure no portal do Azure"
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: 
ms.assetid: e69542ec-d10f-4f17-9b7a-2ee441ee7d68
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/10/2016
ms.author: deli
ms.openlocfilehash: 3861ee121ed1c4d086ea81640e84d924d7d17ea1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-scheduler-in-azure-portal"></a>Introdução ao Agendador do Azure no portal do Azure
É fácil criar tarefas agendadas no Agendador do Azure. Neste tutorial, ficará a saber como criar uma tarefa. Também irá aprender capacidades de monitorização e gestão do Agendador.

## <a name="create-a-job"></a>Criar uma tarefa
1. Inicie sessão no [portal do Azure](https://portal.azure.com/).  
2. Clique em **+novo** > escreva *Agendador* na caixa de pesquisa > selecione **Agendador** nos resultados > clique **Criar**.
   
    ![][marketplace-create]
3. Vamos criar uma tarefa que simplesmente chegue a http://www.microsoft.com/ com um pedido GET. No ecrã da **tarefa do Agendador**, introduza as seguintes informações:
   
   1. **Nome:** `getmicrosoft`  
   2. **Subscrição:** a sua subscrição do Azure   
   3. **Coleção de Tarefas:** selecione uma coleção de tarefas existente ou clique em **Criar novo** > introduza um nome.
4. Em seguida, nas **Definições de Ação**, defina os seguintes valores:
   
   1. **Tipo de Ação:** ` HTTP`  
   2. **Método:** `GET`  
   3. **URL:** ` http://www.microsoft.com`  
      
      ![][action-settings]
5. Por fim, vamos definir uma agenda. A tarefa pode ser definida como uma única tarefa, mas vamos escolher uma agenda de periodicidade:
   
   1. **Periodicidade**: `Recurring`
   2. **Iniciar**: data de hoje
   3. **Recorrer a cada**: `12 Hours`
   4. **Terminar a**: dois dias a partir da data de hoje  
      
      ![][recurrence-schedule]
6. Clique em **Criar**.

## <a name="manage-and-monitor-jobs"></a>Gerir e monitorizar tarefas
Quando é criada uma tarefa, a mesma aparece no dashboard principal do Azure. Clique na tarefa e abre-se uma nova janela com as seguintes abas:

1. Propriedades  
2. Definições de Ação  
3. Agenda  
4. Histórico
5. Utilizadores
   
   ![][job-overview]

### <a name="properties"></a>Propriedades
Estas propriedades só de leitura descrevem os metadados de gestão para a tarefa do Agendador.

   ![][job-properties]

### <a name="action-settings"></a>Definições de ação
Clicar numa tarefa no ecrã **Tarefas** permite-lhe configurar essa tarefa. Isto permite-lhe fazer configurações avançadas, caso não as tenha feito no assistente de criação rápida.

Para todos os tipos de ação, pode alterar a política de repetição e a ação de erro.

Para tipos de ação de tarefa HTTP e HTTPS, pode alterar o método para qualquer verbo HTTP permitido. Também pode adicionar, eliminar ou alterar os cabeçalhos e as informações de autenticação básica.

Para os tipos de ação de fila de armazenamento, pode alterar a conta de armazenamento, nome da fila, token SAS e corpo.

Para os tipos de ação de barramento de serviço, pode alterar o espaço de nomes, caminho do tópico/fila, definições de autenticação, tipo de transporte, propriedades de mensagem e o corpo da mensagem.

   ![][job-action-settings]

### <a name="schedule"></a>Agenda
Isto permite-lhe reconfigurar a agenda, caso gostaria de alterar a agenda que criou no assistente de criação rápida.

Trata-se de uma oportunidade para criar [agendas complexas e periodicidade avançada na tarefa](scheduler-advanced-complexity.md)

Pode alterar a data e hora de início, a agenda de periodicidade e a data e hora de fim (caso a tarefa seja recorrente).

   ![][job-schedule]

### <a name="history"></a>Histórico
O separador **Histórico** exibe as métricas selecionadas para todas as execuções de tarefa no sistema para a tarefa selecionada. Estas métricas fornecem valores em tempo real sobre o estado de funcionamento do seu Agendador:

1. Estado  
2. Detalhes  
3. Tentativas de repetição
4. Ocorrência: 1ª, 2ª, 3ª, etc.
5. Hora de início de execução  
6. Hora de fim de execução
   
   ![][job-history]

Pode clicar numa execução para visualizar os seus **Detalhes de Histórico**, incluindo a resposta completa para cada execução. Esta caixa de diálogo também lhe permite copiar a resposta para a área de transferência.

   ![][job-history-details]

### <a name="users"></a>Utilizadores
O Controlo de Acesso Baseado em Funções (RBAC) do Azure permite uma gestão pormenorizada de acesso ao Agendador do Azure. Para saber como utilizar o separador de Utilizadores, consulte o [Controlo de Acesso Baseado em Funções do Azure](../active-directory/role-based-access-control-configure.md)

## <a name="see-also"></a>Consultar também
 [O que é o Scheduler?](scheduler-intro.md)

 [Conceitos, terminologia e hierarquia de entidades do Scheduler](scheduler-concepts-terms.md)

 [Planos e faturação no Azure Scheduler](scheduler-plans-billing.md)

 [Como criar agendas complexas e periodicidade avançada com o Azure Scheduler](scheduler-advanced-complexity.md)

 [Referência da API REST do Scheduler](https://msdn.microsoft.com/library/mt629143)

 [Referência de cmdlets do PowerShell do Scheduler](scheduler-powershell-reference.md)

 [Elevada disponibilidade e fiabilidade do Scheduler](scheduler-high-availability-reliability.md)

 [Limites, predefinições e códigos de erro do Scheduler](scheduler-limits-defaults-errors.md)

 [Autenticação de saída do Scheduler](scheduler-outbound-authentication.md)

[marketplace-create]: ./media/scheduler-get-started-portal/scheduler-v2-portal-marketplace-create.png
[action-settings]: ./media/scheduler-get-started-portal/scheduler-v2-portal-action-settings.png
[recurrence-schedule]: ./media/scheduler-get-started-portal/scheduler-v2-portal-recurrence-schedule.png
[job-properties]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-properties.png
[job-overview]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-overview-1.png
[job-action-settings]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-action-settings.png
[job-schedule]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-schedule.png
[job-history]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-history.png
[job-history-details]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-history-details.png


[1]: ./media/scheduler-get-started-portal/scheduler-get-started-portal001.png
[2]: ./media/scheduler-get-started-portal/scheduler-get-started-portal002.png
[3]: ./media/scheduler-get-started-portal/scheduler-get-started-portal003.png
[4]: ./media/scheduler-get-started-portal/scheduler-get-started-portal004.png
[5]: ./media/scheduler-get-started-portal/scheduler-get-started-portal005.png
[6]: ./media/scheduler-get-started-portal/scheduler-get-started-portal006.png
[7]: ./media/scheduler-get-started-portal/scheduler-get-started-portal007.png
[8]: ./media/scheduler-get-started-portal/scheduler-get-started-portal008.png
[9]: ./media/scheduler-get-started-portal/scheduler-get-started-portal009.png
[10]: ./media/scheduler-get-started-portal/scheduler-get-started-portal010.png
[11]: ./media/scheduler-get-started-portal/scheduler-get-started-portal011.png
[12]: ./media/scheduler-get-started-portal/scheduler-get-started-portal012.png
[13]: ./media/scheduler-get-started-portal/scheduler-get-started-portal013.png
[14]: ./media/scheduler-get-started-portal/scheduler-get-started-portal014.png
[15]: ./media/scheduler-get-started-portal/scheduler-get-started-portal015.png
