---
title: "Monitorizar alterações de máquina virtual - Azure eventos grelha & Logic Apps | Microsoft Docs"
description: "Verifique a existência de alterações de configuração em máquinas virtuais (VMs) utilizando a grelha de eventos do Azure e Logic Apps"
keywords: "as Logic apps, grelhas de eventos, a máquina virtual, VM"
services: logic-apps
author: ecfan
manager: anneta
ms.assetid: 
ms.workload: logic-apps
ms.service: logic-apps
ms.topic: article
ms.date: 11/30/2017
ms.author: LADocs; estfan
ms.openlocfilehash: df1e19b772b41064aff1f345dee93813f0c21c73
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2017
---
# <a name="monitor-virtual-machine-changes-with-azure-event-grid-and-logic-apps"></a>Monitorizar alterações de máquina virtual com a grelha de eventos do Azure e Logic Apps

Pode iniciar uma automatizada [fluxo de trabalho de aplicação de lógica](../logic-apps/logic-apps-what-are-logic-apps.md) quando os eventos específicos acontecer em recursos do Azure ou recursos de terceiros. Estes recursos podem publicar esses eventos para um [grelha de eventos do Azure](../event-grid/overview.md). Por sua vez, a grelha de evento pushes esses eventos para os subscritores que tenham as filas, webhooks, ou [dos event hubs](../event-hubs/event-hubs-what-is-event-hubs.md) como pontos finais. Como um subscritor, a aplicação lógica pode aguardar esses eventos da grelha de eventos antes de executar fluxos de trabalho automatizados para efetuar tarefas - sem que escrever qualquer código.

Por exemplo, aqui estão alguns eventos que podem enviar editores para subscritores através do serviço de grelha de eventos do Azure:

* Criar, ler, atualizar ou eliminar um recurso. Por exemplo, pode monitorizar as alterações que podem implicar custos na sua subscrição do Azure e afetam a fatura. 
* Adicionar ou remover uma pessoa a partir de uma subscrição do Azure.
* A aplicação executa uma ação específica.
* É apresentada uma nova mensagem numa fila.

Este tutorial cria uma aplicação lógica que monitoriza as alterações a uma máquina virtual e envia mensagens de correio eletrónico sobre essas alterações. Quando criar uma aplicação lógica com uma subscrição de eventos para um recurso do Azure, eventos de fluxo do recurso de através de uma grelha de eventos para a aplicação lógica. O tutorial explica-lhe criar esta aplicação lógica:

![Descrição-geral de máquina virtual de monitor com eventos grelha e a lógica da aplicação](./media/monitor-virtual-machine-changes-event-grid-logic-app/monitor-virtual-machine-event-grid-logic-app-overview.png)

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Crie uma aplicação lógica que monitoriza os eventos a partir de uma grelha de eventos.
> * Adicione uma condição que especificamente verifica a existência de alterações de máquina virtual.
> * Envie correio eletrónico quando a máquina virtual é alterado.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta de e-mail do [qualquer fornecedor de correio eletrónico suportado pelo Azure Logic Apps](../connectors/apis-list.md), tais como o Outlook do Office 365, Outlook.com ou Gmail, para enviar notificações. Este tutorial utiliza o Outlook do Office 365.

* A [máquina virtual](https://azure.microsoft.com/services/virtual-machines). Se ainda não o fez, crie uma máquina virtual através de um [criar um tutorial VM](https://docs.microsoft.com/azure/virtual-machines/). Para tornar a máquina virtual publicar eventos, [não precisa de fazer mais nada](../event-grid/overview.md).

## <a name="create-a-logic-app-that-monitors-events-from-an-event-grid"></a>Criar uma aplicação lógica que monitoriza os eventos a partir de uma grelha de eventos

Em primeiro lugar, crie uma aplicação lógica e adicionar um acionador de grelha de eventos que monitoriza o grupo de recursos para a máquina virtual. 

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). 

2. No canto superior esquerdo do menu principal do Azure, escolha **novo** > **integração empresarial com** > **aplicação lógica**.

   ![Criar uma aplicação lógica](./media/monitor-virtual-machine-changes-event-grid-logic-app/azure-portal-create-logic-app.png)

3. Crie a sua aplicação lógica com as definições especificadas na tabela seguinte:

   ![Indicar os detalhes da aplicação lógica](./media/monitor-virtual-machine-changes-event-grid-logic-app/create-logic-app-for-event-grid.png)

   | Definição | Valor sugerido | Descrição | 
   | ------- | --------------- | ----------- | 
   | **Nome** | *{logic-nome da aplicação}* | Indique um nome exclusivo para a aplicação lógica. | 
   | **Subscrição** | *{your-subscrição do Azure}* | Selecione a mesma subscrição do Azure para todos os serviços neste tutorial. | 
   | **Grupo de recursos** | *{your-Azure-resource-group}* | Selecione o mesmo grupo de recursos do Azure para todos os serviços neste tutorial. | 
   | **Localização** | *{your-região do Azure}* | Selecione a região mesma para todos os serviços neste tutorial. | 
   | | | 

4. Quando estiver pronto, selecione **afixar ao dashboard**e escolha **criar**.

   Acabou de criar um recurso do Azure para a aplicação lógica. 
   Quando o Azure implementar a sua aplicação lógica, o Estruturador de Aplicações Lógicas mostra-lhe modelos de padrões comuns, para que possa começar mais depressa.

   > [!NOTE] 
   > Quando seleciona **afixar ao dashboard**, a aplicação lógica abre automaticamente no Designer de aplicações lógicas. Caso contrário, pode manualmente localize e abra a aplicação lógica.

5. Agora, escolha um modelo de aplicação lógica. Em **modelos**, escolha **aplicação lógica em branco** pelo que pode criar a sua aplicação de lógica a partir do zero.

   ![Escolher o modelo da aplicação lógica](./media/monitor-virtual-machine-changes-event-grid-logic-app/choose-logic-app-template.png)

   O Designer de aplicações lógicas agora mostra-lhe [ *conectores* ](../connectors/apis-list.md) e [ *acionadores* ](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts) que pode utilizar para iniciar a aplicação lógica bem como as ações que pode adicionar após um acionador para efetuar tarefas. Um acionador é um evento que cria uma instância da aplicação lógica e inicia o fluxo de trabalho de aplicação lógica. 
   A aplicação de lógica tem um acionador, como o primeiro item.

6. Na caixa de pesquisa, introduza "grelha de evento" como o filtro. Selecione este acionador: **grelha de eventos do Azure - num evento de recursos**

   ![Selecione este acionador: "Azure eventos grelha - num evento de recursos"](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger.png)

7. Quando lhe for solicitado, inicie sessão na grelha de eventos do Azure com as suas credenciais do Azure.

   ![Inicie sessão com as suas credenciais do Azure](./media/monitor-virtual-machine-changes-event-grid-logic-app/sign-in-event-grid.png)

   > [!NOTE]
   > Se iniciou sessão com uma conta Microsoft pessoal, tal como @outlook.com ou @hotmail.com, o acionador de grelha de evento pode não aparecer corretamente. Como solução, escolha [ligar com o Principal de serviço](../azure-resource-manager/resource-group-create-service-principal-portal.md), ou autenticar como um membro do Azure Active Directory que está associada a sua subscrição do Azure, por exemplo, *nome de utilizador*@emailoutlook.onmicrosoft.com.

8. Subscreve agora a sua aplicação lógica para eventos de publicador. Forneça os detalhes para a sua subscrição de evento conforme especificado na tabela seguinte:

   ![Forneça detalhes para a subscrição de evento](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger-details-generic.png)

   | Definição | Valor sugerido | Descrição | 
   | ------- | --------------- | ----------- | 
   | **Subscrição** | *{virtual-machine-subscrição do Azure}* | Selecione a subscrição do Azure o publicador de eventos. Para este tutorial, selecione a subscrição do Azure para a máquina virtual. | 
   | **Tipo de Recurso** | Microsoft.Resources.resourceGroups | Selecione o tipo de recurso o publicador de eventos. Para este tutorial, selecione o valor especificado para a sua aplicação lógica monitoriza apenas os grupos de recursos. | 
   | **Nome do recurso** | *{virtual-machine-resource--nome do grupo}* | Selecione o nome do Editor recursos. Para este tutorial, selecione o nome do grupo de recursos para a máquina virtual. | 
   | Para as definições opcionais, escolha **Mostrar opções avançadas**. | *{consulte as descrições}* | * **Prefixo filtro**: para este tutorial, deixe esta definição em branco. O comportamento predefinido corresponde a todos os valores. No entanto, pode especificar uma cadeia de prefixo como um filtro, por exemplo, um caminho e um parâmetro para um recurso específico. <p>* **Sufixo filtro**: para este tutorial, deixe esta definição em branco. O comportamento predefinido corresponde a todos os valores. No entanto, pode especificar uma cadeia de sufixo como um filtro, por exemplo, uma extensão de nome de ficheiro, se pretender que os tipos de ficheiro específicos apenas.<p>* **Nome da subscrição**: forneça um nome exclusivo para a sua subscrição de evento. |
   | | | 

   Quando estiver pronto, o acionador de grelha de evento aspeto que poderá ter neste exemplo:
   
   ![Detalhes de Acionador de grelha de eventos de exemplo](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger-details.png)

9. Guarde a sua aplicação lógica. Na barra de ferramentas do estruturador, escolha **Guardar**. Para fechar e ocultar detalhes de uma ação na sua aplicação lógica, escolha a barra de título a ação.

   ![Guardar a aplicação lógica](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-save.png)

   Quando guardar a sua aplicação lógica com um acionador de grelha de evento, o Azure cria automaticamente uma subscrição de evento para a sua aplicação lógica para o recurso selecionado. Por isso, quando o recurso publica um evento à grelha de evento, o que grelha de evento pushes automaticamente o evento à sua aplicação lógica. Este evento aciona a sua aplicação lógica, em seguida, cria e executa uma instância do fluxo de trabalho que definir nestes passos.

A aplicação lógica está agora em direto e escuta eventos da grelha de eventos, mas não faz nada de até que adicione ações para o fluxo de trabalho. 

## <a name="add-a-condition-that-checks-for-virtual-machine-changes"></a>Adicionar uma condição que verifica a existência de alterações de máquina virtual

Para executar o fluxo de trabalho de aplicação lógica apenas quando ocorre um evento específico, adicione uma condição que verifica a máquina virtual "operações de escrita". Quando esta condição for verdadeira, a sua aplicação lógica envia que e-mail com detalhes sobre a máquina virtual atualizado.

1. No Designer de aplicação lógica, sob o acionador de grelha de eventos, escolha **novo passo** > **adicionar uma condição**.

   ![Adicionar uma condição para a sua aplicação lógica](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-add-condition-step.png)

   O Designer de aplicação lógica adiciona uma condição vazia para o fluxo de trabalho, incluindo caminhos de ação a seguir com base se a condição for true ou false.

   ![Condição vazia](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-add-empty-condition.png)

2. No **condição** caixa, escolha **editar no modo avançado**.
Introduza esta expressão:

   `@equals(triggerBody()?['data']['operationName'], 'Microsoft.Compute/virtualMachines/write')`

   A condição agora parece que este exemplo:

   ![Condição vazia](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-condition-expression.png)

   Esta expressão verifica o evento `body` para um `data` objeto onde o `operationName` propriedade é o `Microsoft.Compute/virtualMachines/write` operação. 
   Saiba mais sobre [esquema de eventos de evento grelha](../event-grid/event-schema.md).

3. Para fornecer uma descrição para a condição, escolha o **reticências** (**...** ) botão na forma de condição, em seguida, escolha **mudar o nome**.

   > [!NOTE] 
   > Os exemplos neste tutorial posteriores também fornecem descrições para os passos no fluxo de trabalho de aplicação lógica.

4. Agora escolher **editar no modo básico** para que a expressão resolve automaticamente conforme mostrado:

   ![Condição de aplicação lógica](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-condition-1.png)

5. Guarde a sua aplicação lógica.

## <a name="send-email-when-your-virtual-machine-changes"></a>Enviar correio eletrónico quando altera a sua máquina virtual

Agora adicione um [ *ação* ](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts) para que receber um e-mail quando a condição especificada é verdadeira.

1. A condição **se for verdadeiro** caixa, escolha **adicionar uma ação**.

   ![Adicionar ação para quando a condição for verdadeira](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-condition-2.png)

2. Na caixa de pesquisa, introduza "e-mail" como o filtro. Com base no seu fornecedor de e-mail, localize e selecione o conector correspondente. Em seguida, selecione a ação "enviar e-mail" para o conector. Por exemplo: 

   * Numa conta escolar ou profissional do Azure, selecione o conector Office 365 Outlook. 
   * Em contas Microsoft pessoais, selecione o conector Outlook.com. 
   * Em contas do Gmail, selecione o conector Gmail. 

   Vamos continuar a utilizar o conector Office 365 Outlook. 
   Se utilizar outro fornecedor, os passos são os mesmos, mas a IU poderá ser diferente. 

   ![Selecione a ação "enviar correio eletrónico"](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email.png)

3. Se ainda não tiver uma ligação para o seu fornecedor de e-mail, iniciar sessão para a conta de e-mail quando está a ser pedido para a autenticação.

4. Forneça detalhes para a mensagem de e-mail conforme especificado na tabela seguinte:

   ![Ação de e-mail em branco](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-empty-email-action.png)

   > [!TIP]
   > Para selecionar campos disponíveis no fluxo de trabalho, clique na caixa de edição, por isso, que o **conteúdo dinâmico** lista é aberto, ou escolha **adicionar conteúdo dinâmico**. Para obter mais campos, escolha **ver mais** para cada secção na lista. Para fechar o **conteúdo dinâmico** lista, escolha **adicionar conteúdo dinâmico**.

   | Definição | Valor sugerido | Descrição | 
   | ------- | --------------- | ----------- | 
   | **Para** | *{endereço de correio eletrónico de destinatário}* |Introduza o endereço de e-mail do destinatário. Para fins de teste, pode utilizar o seu próprio endereço de e-mail. | 
   | **Assunto** | O recurso atualizado: **requerente**| Introduza o conteúdo para o assunto do e-mail. Para este tutorial, introduza o texto sugerido e selecione o evento **requerente** campo. Aqui, o assunto do correio eletrónico inclui o nome para o recurso atualizado (máquina virtual). | 
   | **Corpo** | Grupo de recursos: **tópico** <p>Tipo de evento: **tipo de evento**<p>ID de evento: **ID**<p>Hora: **tempo do evento** | Introduza o conteúdo para o corpo do e-mail. Para este tutorial, introduza o texto sugerido e selecione o evento **tópico**, **tipo de evento**, **ID**, e **tempo do evento** campos para que o seu correio eletrónico inclui o nome do grupo de recursos, tipo de evento, timestamp de eventos e ID de evento para a atualização. <p>Para adicionar linhas em branco no seu conteúdo, prima Shift + Enter. | 
   | | | 

   > [!NOTE] 
   > Se selecionar um campo que representa uma matriz, o estruturador adiciona automaticamente um **para cada** ciclo à volta a ação que faça referência a matriz. Desta forma, a sua aplicação lógica realiza essa ação em cada item da matriz.

   Agora, a ação de correio eletrónico aspeto que poderá ter neste exemplo:

   ![Selecione as saídas para incluir no e-mail](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email-details.png)

   E a sua aplicação lógica terminar aspeto que poderá ter neste exemplo:

   ![Aplicação lógica concluído](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-completed.png)

5. Guarde a sua aplicação lógica. Para fechar e ocultar detalhes de cada ação na sua aplicação lógica, escolha a barra de título a ação.

   ![Guardar a aplicação lógica](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-save-completed.png)

   A aplicação lógica está agora em direto, mas aguarda a alterações à máquina virtual antes de fazer nada. 
   Para testar a sua aplicação lógica agora, avance para a secção seguinte.

## <a name="test-your-logic-app-workflow"></a>Testar o seu fluxo de trabalho de aplicação lógica

1. Para verificar a sua aplicação lógica está a obter os eventos especificados, atualize a máquina virtual. 

   Por exemplo, pode redimensionar a máquina virtual no portal do Azure ou [redimensionar a VM com o Azure PowerShell](../virtual-machines/windows/resize-vm.md). 

   Após alguns instantes, deve obter um e-mail. Por exemplo:

   ![E-mail sobre a atualização da máquina virtual](./media/monitor-virtual-machine-changes-event-grid-logic-app/email.png)

2. Para rever o é executado e acionar histórico para a sua aplicação lógica, no menu aplicação lógica, escolha **descrição geral**. Para ver mais detalhes sobre uma execução, selecione a linha relativa à mesma.

   ![Histórico de execução de aplicação lógica](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-run-history.png)

3. Para ver as entradas e saídas de cada passo, expanda o passo que pretende rever. Estas informações podem ajudá-lo a diagnosticar e depurar problemas na sua aplicação lógica.
 
   ![Detalhes de histórico da execução de aplicação de lógica](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-run-history-details.png)

Parabéns, ter criado e executar uma aplicação lógica que monitoriza os eventos de recursos através de uma grelha de eventos e e-mails, quando esses eventos ocorrem. Também aprendeu como facilmente pode criar fluxos de trabalho que automatizar os processos e integram os sistemas e serviços em nuvem.

Pode monitorizar outras alterações de configuração com grelhas de eventos e as logic apps, por exemplo:

* Uma máquina virtual obtém acesso baseado em funções direitos de controlo (RBAC).
* São efetuadas alterações a um grupo de segurança de rede (NSG) numa interface de rede (NIC).
* Discos para uma máquina virtual são adicionados ou removidos.
* Um endereço IP público é atribuído a uma máquina virtual NIC.

## <a name="clean-up-resources"></a>Limpar recursos

Este tutorial utiliza recursos e efetua ações de pagar a sua subscrição do Azure. Por isso, quando concluir o tutorial e de teste, certifique-se de que desative ou elimine quaisquer recursos em que não pretende implicar custos.

* Para parar de executar a sua aplicação lógica sem eliminar o seu trabalho, desative-a. No menu da aplicação lógica, escolha **Descrição geral**. Na barra de ferramentas, escolha **Desativar**.

  ![Desativar a sua aplicação lógica](./media/monitor-virtual-machine-changes-event-grid-logic-app/turn-off-disable-logic-app.png)

  > [!TIP]
  > Se não vir o menu da aplicação lógica, experimente regressar ao dashboard do Azure e reabra a aplicação lógica.

* Para eliminar permanentemente a sua aplicação lógica, no menu de aplicação lógica, escolha **descrição geral**. Na barra de ferramentas, escolha **Eliminar**. Confirme que pretende eliminar a sua aplicação lógica e escolha **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

* [Criar e eventos personalizados com grelha de eventos de rotas](../event-grid/custom-event-quickstart.md)
