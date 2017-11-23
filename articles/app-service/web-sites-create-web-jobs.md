---
title: Executar tarefas em segundo plano com WebJobs no App Service do Azure
description: "Saiba como utilizar WebJobs para executar tarefas em segundo plano nas web apps do App Service do Azure, as API apps ou as aplicações móveis."
services: app-service
documentationcenter: 
author: ggailey777
manager: erikre
editor: jimbe
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/09/2017
ms.author: glenga;david.ebbo;suwatch;pbatum;naren.soni
ms.openlocfilehash: 91839d8f547340d55f6badb3350a393a48a13c7d
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2017
---
# <a name="run-background-tasks-with-webjobs-in-azure-app-service"></a>Executar tarefas em segundo plano com WebJobs no App Service do Azure

## <a name="overview"></a>Descrição geral
WebJobs é uma funcionalidade do [App Service do Azure](https://docs.microsoft.com/azure/app-service/) que permite-lhe executar um programa ou script no mesmo contexto como uma aplicação API, aplicação móvel ou aplicação web. Não há sem custos adicionais para utilizar WebJobs.

Este artigo mostra como implementar WebJobs utilizando o [portal do Azure](https://portal.azure.com) para carregar um ficheiro executável ou script. Para obter informações sobre como desenvolver e implementar WebJobs utilizando o Visual Studio, consulte [WebJobs implementar com o Visual Studio](websites-dotnet-deploy-webjobs.md).

O SDK de WebJobs do Azure pode ser utilizado com WebJobs para simplificar a muitas tarefas de programação. Para obter mais informações, consulte [o que é o SDK de WebJobs](https://github.com/Azure/azure-webjobs-sdk/wiki).

As funções do Azure proporciona outra forma de executar scripts e programas. Para ver uma comparação entre as funções e WebJobs, consulte [escolha entre fluxo, as Logic Apps, funções e WebJobs](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md).

## <a name="webjob-types"></a>Tipos de trabalho Web

A tabela seguinte descreve as diferenças entre *contínua* e *acionada* WebJobs.


|Contínuo  |Acionado  |
|---------|---------|
| Começa imediatamente quando o trabalho Web foi criado. Para manter a tarefa terminar, o programa ou script, normalmente, faz o trabalho no interior de um ciclo endless. Se a tarefa terminar, pode reiniciá-lo. | Inicia o apenas quando acionado com base num agendamento ou manualmente. |
| É executado em todas as instâncias que a aplicação web é executado. Opcionalmente, pode restringir o trabalho Web a uma única instância. |É executado numa única instância que Azure seleciona para balanceamento de carga.|
| Suporta a depuração remota. | Não suporta a depuração remota.|

> [!NOTE]
> Uma aplicação web pode tempo limite após 20 minutos de inatividade. O temporizador de reposição de pedidos apenas para o site scm (implementação) ou às páginas da aplicação web no portal. Pedidos para o site real não repor o temporizador. Se a aplicação for executada contínua ou ativar o WebJobs agendadas, **Always On** para se certificar de que os trabalhos Web executam de forma fiável. Esta funcionalidade está disponível apenas nos básicas, Standard e Premium [escalões de preço](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="acceptablefiles"></a>Tipos de ficheiro suportados para scripts ou programas

São suportados os seguintes tipos de ficheiro:

* . cmd,. bat, .exe (utilizando o Windows cmd)
* . ps1 (utilizando o PowerShell)
* .SH (utilizando Bash)
* SH (utilizando o PHP)
* . PY (com o Python)
* . js (com o Node.js)
* . JAR (utilizando Java)

## <a name="CreateContinuous"></a>Criar um WebJob contínuo

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. No [portal do Azure](https://portal.azure.com), vá para o **do serviço de aplicações** página da sua aplicação API, aplicação móvel ou aplicação web do app Service.

2. Selecione **WebJobs**.

   ![Selecione WebJobs](./media/web-sites-create-web-jobs/select-webjobs.png)

2. No **WebJobs** página, selecione **adicionar**.

    ![Página do trabalho Web](./media/web-sites-create-web-jobs/wjblade.png)

3. Utilize o **adicionar WebJob** definições conforme especificado na tabela.

   ![Adicionar página de trabalho Web](./media/web-sites-create-web-jobs/addwjcontinuous.png)

   | Definição      | Valor da amostra   | Descrição  |
   | ------------ | ----------------- | ------------ |
   | **Nome** | myContinuousWebJob | Um nome exclusivo dentro de uma aplicação de serviço de aplicações. Tem de começar com uma letra ou um número e não pode conter carateres especiais diferente de "-" e "_". |
   | **Carregamento de ficheiros** | ConsoleApp.zip | A *. zip* ficheiro que contém o ficheiro executável ou script, bem como quaisquer ficheiros de suporte necessários para executar o programa ou script. Os tipos de ficheiro executável ou script suportados estão listados no [tipos de ficheiro suportados](#acceptablefiles) secção. |
   | **Tipo** | Contínuo | O [WebJob tipos](#webjob-types) descrito anteriormente neste artigo. |
   | **Dimensionamento** | De ocorrências múltiplas | Disponível apenas para WebJobs contínuos. Determina se o programa ou script é executado em todas as instâncias ou apenas uma instância. A opção para executar em várias instâncias não se aplica ao gratuito ou partilhado [escalões de preço](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). | 

4. Clique em **OK**.

   O novo WebJob é apresentado no **WebJobs** página.

   ![Lista de WebJobs](./media/web-sites-create-web-jobs/listallwebjobs.png)

2. Para interromper ou reiniciar um WebJob contínuo, faça duplo clique WebJob na lista e clique em **parar** ou **iniciar**.

    ![Parar um WebJob contínuo](./media/web-sites-create-web-jobs/continuousstop.png)

## <a name="CreateOnDemand"></a>Criar um WebJob accionado manualmente

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. No [portal do Azure](https://portal.azure.com), vá para o **do serviço de aplicações** página da sua aplicação API, aplicação móvel ou aplicação web do app Service.

2. Selecione **WebJobs**.

   ![Selecione WebJobs](./media/web-sites-create-web-jobs/select-webjobs.png)

2. No **WebJobs** página, selecione **adicionar**.

    ![Página do trabalho Web](./media/web-sites-create-web-jobs/wjblade.png)

3. Utilize o **adicionar WebJob** definições conforme especificado na tabela.

   ![Adicionar página de trabalho Web](./media/web-sites-create-web-jobs/addwjtriggered.png)

   | Definição      | Valor da amostra   | Descrição  |
   | ------------ | ----------------- | ------------ |
   | **Nome** | myTriggeredWebJob | Um nome exclusivo dentro de uma aplicação de serviço de aplicações. Tem de começar com uma letra ou um número e não pode conter carateres especiais diferente de "-" e "_".|
   | **Carregamento de ficheiros** | ConsoleApp.zip | A *. zip* ficheiro que contém o ficheiro executável ou script, bem como quaisquer ficheiros de suporte necessários para executar o programa ou script. Os tipos de ficheiro executável ou script suportados estão listados no [tipos de ficheiro suportados](#acceptablefiles) secção. |
   | **Tipo** | Acionado | O [WebJob tipos](#webjob-types) descrito anteriormente neste artigo. |
   | **Acionadores** | Manual | |

4. Clique em **OK**.

   O novo WebJob é apresentado no **WebJobs** página.

   ![Lista de WebJobs](./media/web-sites-create-web-jobs/listallwebjobs.png)

7. Para executar o trabalho Web, clique no respetivo nome na lista e clique em **executar**.
   
    ![Executar WebJob](./media/web-sites-create-web-jobs/runondemand.png)

## <a name="CreateScheduledCRON"></a>Criar um WebJob agendado

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. No [portal do Azure](https://portal.azure.com), vá para o **do serviço de aplicações** página da sua aplicação API, aplicação móvel ou aplicação web do app Service.

2. Selecione **WebJobs**.

   ![Selecione WebJobs](./media/web-sites-create-web-jobs/select-webjobs.png)

2. No **WebJobs** página, selecione **adicionar**.

   ![Página do trabalho Web](./media/web-sites-create-web-jobs/wjblade.png)

3. Utilize o **adicionar WebJob** definições conforme especificado na tabela.

   ![Adicionar página de trabalho Web](./media/web-sites-create-web-jobs/addwjscheduled.png)

   | Definição      | Valor da amostra   | Descrição  |
   | ------------ | ----------------- | ------------ |
   | **Nome** | myScheduledWebJob | Um nome exclusivo dentro de uma aplicação de serviço de aplicações. Tem de começar com uma letra ou um número e não pode conter carateres especiais diferente de "-" e "_". |
   | **Carregamento de ficheiros** | ConsoleApp.zip | A *. zip* ficheiro que contém o ficheiro executável ou script, bem como quaisquer ficheiros de suporte necessários para executar o programa ou script. Os tipos de ficheiro executável ou script suportados estão listados no [tipos de ficheiro suportados](#acceptablefiles) secção. |
   | **Tipo** | Acionado | O [WebJob tipos](#webjob-types) descrito anteriormente neste artigo. |
   | **Acionadores** | Agendadas | Para o agendamento para trabalhar de forma fiável, ative a funcionalidade Always On. Always On está disponível apenas a básicas, Standard e Premium escalões de preço.|
   | **Expressão de CRON** | 0 0/20 * * * * | [As expressões de CRON](#cron-expressions) são descritas na secção seguinte. |

4. Clique em **OK**.

   O novo WebJob é apresentado no **WebJobs** página.

   ![Lista de WebJobs](./media/web-sites-create-web-jobs/listallwebjobs.png)

## <a name="cron-expressions"></a>Expressões de CRON

A [expressão CRON](https://en.wikipedia.org/wiki/Cron) é composto de seis campos: `{second} {minute} {hour} {day} {month} {day of the week}`.  Eis alguns exemplos:

* Cada 15 minutos:`0 */15 * * * *`
* A cada hora (ou seja, sempre que o número de minutos é de 0):`0 0 * * * *` 
* Hora a hora de 09: 00 para as 17: 00:`0 0 9-17 * * *` 
* Às 9:30 AM todos os dias:`0 30 9 * * *`
* Às 9:30 AM cada dia da semana:`0 30 9 * * 1-5`

Pode introduzir a expressão de CRON no portal ou incluem um `settings.job` ficheiro na raiz da sua WebJob *. zip* ficheiro, como no exemplo seguinte:

```json
{
    "schedule": "0 */15 * * * *"
}
``` 

> [!NOTE]
> Quando implementar um WebJob do Visual Studio, marcar a `settings.job` ficheiro propriedades como **copiar se for mais recente**.

## <a name="ViewJobHistory"></a>Ver o histórico de tarefas

1. Selecione o trabalho Web que pretende ver histórico para e, em seguida, selecione o **registos** botão.
   
   ![Botão de registos](./media/web-sites-create-web-jobs/wjbladelogslink.png)

2. No **detalhes do trabalho Web** página, selecione uma hora para ver os detalhes para uma execução.
   
   ![Detalhes do trabalho Web](./media/web-sites-create-web-jobs/webjobdetails.png)

3. No **detalhes da execução de trabalho Web** página, selecione **alternar saída** para ver o texto do conteúdo de registo.
   
    ![Detalhes de execução da tarefa Web](./media/web-sites-create-web-jobs/webjobrundetails.png)

   Para ver o texto de resultado numa janela do browser separados, selecione **transferir**. Para transferir o próprio texto, clique com botão direito **transferir** e utilize as opções do browser para guardar o conteúdo do ficheiro.
   
5. Selecione o **WebJobs** trilho ligação na parte superior da página para ir para uma lista de WebJobs.

    ![Trilho do trabalho Web](./media/web-sites-create-web-jobs/breadcrumb.png)
   
    ![Lista de WebJobs no dashboard do histórico](./media/web-sites-create-web-jobs/webjobslist.png)
   
## <a name="NextSteps"></a> Passos seguintes

O SDK de WebJobs do Azure pode ser utilizado com WebJobs para simplificar a muitas tarefas de programação. Para obter mais informações, consulte [o que é o SDK de WebJobs](https://github.com/Azure/azure-webjobs-sdk/wiki).
