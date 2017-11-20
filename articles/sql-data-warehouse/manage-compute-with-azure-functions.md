---
title: "Azure SQL Data Warehouse - Utilizar as Funções do Azure para automatizar os níveis de computação do SLQ Data Warehouse  | Microsoft Docs"
description: "Como utilizar as funções do Azure para gerir a computação do seu armazém de dados."
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: johnmac
editor: barbkess
ms.assetid: 52DFC191-E094-4B04-893F-B64D5828A901
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: quickstart
ms.date: 11/06/2017
ms.author: elbutter
ms.openlocfilehash: 56543946c2a2cfe63feb249fcd4ac63517700115
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2017
---
# <a name="use-azure-functions-to-automate-sql-dw-compute-levels"></a>Utilize as Funções do Azure para automatizar os níveis de computação do SQL DW

Este tutorial mostra como pode utilizar as Funções do Azure para gerir os níveis de computação do Azure SQL Data Warehouse. Estas arquiteturas são recomendadas para utilização com o SQL Data Warehouse [Otimizado para Elasticidade][Performance Tiers].

Para utilizar o Azure Function App com o SQL Data Warehouse, tem de criar uma [Conta de Principal de Serviço](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-create-service-principal-portal) com acesso de contribuidor na mesma subscrição da sua instância do armazém de dados. 

## <a name="deploy-timer-based-scaler-with-an-azure-resource-manager-template"></a>Implementar dimensionamento baseado no tempo com um Modelo do Azure Resource Manager

Para implementar o modelo, precisa das informações seguintes:

- Nome do grupo de recursos em que a sua instância do armazém do SQL DW se encontra
- Nome do servidor lógico em que a sua instância do armazém do SQL DW se encontra
- Nome da sua instância do SQL DW
- ID do inquilino (ID do Diretório) do seu Azure Active Directory
- ID da subscrição 
- ID da Aplicação Principal de Serviço
- Chave do Segredo do Principal de Serviço

Quando tiver estas informações, implemente este modelo:

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwTimerScaler%2Fazuredeploy.json" target="_blank">
<img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>

Depois de implementar o modelo, deverá ver três recursos novos: um Plano do Serviço de Aplicações do Azure gratuito, um plano do Function App baseado no consumo e uma conta de armazenamento, a qual vai processar os registos e a fila de operações. Continue a ler as outras secções para ver como modificar as funções implementadas de acordo com as suas necessidades.

### <a name="change-the-scale-up-or-scale-down-compute-level"></a>Alterar o nível de computação de aumento ou redução vertical

1. Navegue para o serviço Function App. Se tiver implementado o modelo com os valores predefinidos, este serviço deverá chamar-se *DWOperations*. Assim que Function App estiver aberto, deverá ver cinco funções implementadas no seu serviço do Function App. 

   ![Funções que são implementadas com o modelo](media/manage-compute-with-azure-functions/five-functions.png)

2. Selecione *DWScaleDownTrigger* ou *DWScaleUpTrigger*, dependendo se quer alterar a hora de aumento ou redução vertical. Na lista pendente, selecione Integrate.

   ![Selecionar Integrate para a função](media/manage-compute-with-azure-functions/select-integrate.png)

3. Atualmente, o valor apresentado deverá indicar *%ScaleDownTime%* ou *%ScaleUpTime%*. Estes valores indicam que a agenda se baseia nos valores definidos nas [Definições da Aplicação][Application Settings]. Por agora, pode ignorá-lo e alterar a agenda para a hora que preferir nos próximos passos.

4. Na área “schedule”, adicione a hora na expressão CRON que pretende que reflita a frequência com que quer aumentar verticalmente o SQL Data Warehouse. 

  ![Alterar agenda da função](media/manage-compute-with-azure-functions/change-schedule.png)

  O valor de `schedule` é uma [expressão CRON](http://en.wikipedia.org/wiki/Cron#CRON_expression) que inclui estes seis campos: 
  ```json
  {second} {minute} {hour} {day} {month} {day-of-week}
  ```

  Por exemplo, *"0 30 9 * * 1-5"* reflete um acionador todos os dias da semana às 9:30. Para obter mais informações, veja os [exemplos de agendas][schedule examples] do Azure Functions.


### <a name="change-the-scale-up-or-scale-down-time"></a>Alterar a hora de aumento ou redução vertical

1. Navegue para o serviço Function App. Se tiver implementado o modelo com os valores predefinidos, este serviço deverá chamar-se *DWOperations*. Assim que Function App estiver aberto, deverá ver cinco funções implementadas no seu serviço do Function App. 

2. Selecione *DWScaleDownTrigger* ou *DWScaleUpTrigger*, dependendo se quer alterar o valor de computação de aumento ou redução vertical. Após selecionar as funções, o painel deve mostrar o ficheiro *index.js*.

   ![Alterar o nível de computação do acionador de função](media/manage-compute-with-azure-functions/index-js.png)

3. Altere o valor de *ServiceLevelObjective* para o nível que pretende e prima “Save”. Este é o nível de computação para o qual a sua instância do armazém de dados vai ser aumentado com base na agenda definida na secção Integrate.

### <a name="use-pause-or-resume-instead-of-scale"></a>Utilizar a pausa ou a retoma em vez do dimensionamento 

Atualmente, as funções ligadas por predefinição são *DWScaleDownTrigger* e *DWScaleUpTrigger*. Em alternativa, se pretender utilizar a funcionalidade de pausa e retoma, pode ativar *DWPauseTrigger* ou *DWResumeTrigger*.

1. Navegue para o painel Functions.

   ![Painel Functions](media/manage-compute-with-azure-functions/functions-pane.png)



2. Clique no botão deslizante dos acionadores que quer ativar.

3. Navegue para os separadores *Integrate* de cada acionador, para alterar as agendas dos mesmos.

   [!NOTE]: The functional difference between the scaling triggers and the pause/resume triggers is the message that is sent to the queue. See [Add a new trigger function][Add a new trigger function] for more information.



### <a name="add-a-new-trigger-function"></a>Adicionar uma nova função de acionador

Atualmente, o modelo inclui apenas duas funções de dimensionamento. Isto significa que, ao longo de um dia, pode só pode reduzir verticalmente uma vez e aumentar uma vez. Para um controlo mais granular, como reduzir verticalmente várias vezes por dia ou apresentar um comportamento de dimensionamento diferente aos fins de semana, terá de adicionar outro acionador.

1. Crie uma função em branco nova. Selecione o botão *+*, junto à localização de Functions, para ver o painel do modelo de função.

   ![Criar função nova](media/manage-compute-with-azure-functions/create-new-function.png)

2. Em Linguagem, selecione *Javascript* e, em seguida, selecione *TimerTrigger*.

   ![Criar função nova](media/manage-compute-with-azure-functions/timertrigger-js.png)

3. Dê um nome à função e defina a agenda. A imagem mostra como um utilizador pode acionar a função todos os sábados à meia-noite (sexta ao fim do dia ).

   ![Reduzir verticalmente ao sábado](media/manage-compute-with-azure-functions/scale-down-saturday.png)

4. Copie o conteúdo de *index.js* de uma das outras funções de acionador.

   ![Copiar index js](media/manage-compute-with-azure-functions/index-js.png)

5. Defina a variável da operação como o comportamento desejado, da seguinte forma:

   ```javascript
   // Resume the data warehouse instance
   var operation = {
       "operationType": "ResumeDw"
   }

   // Pause the data warehouse instance
   var operation = {
       "operationType": "PauseDw"
   }

   // Scale the data warehouse instance to DW600
   var operation = {
       "operationType": "ScaleDw",
       "ServiceLevelObjective": "DW600"
   }
   ```


### <a name="complex-scheduling"></a>Agendamento complexo

Esta secção demonstra, de forma breve, o que é necessário para obter um agendamento mais complexo das capacidades de pausa, retoma e dimensionamento.

#### <a name="example-1"></a>Exemplo 1:

Todos os dias, aumente verticalmente às 8:00 para DW600 e reduza verticalmente às 20:00 para DW200.

| Função  | Agenda     | Operação                                |
| :-------- | :----------- | :--------------------------------------- |
| Function1 | 0 0 8 * * *  | `var operation = {"operationType": "ScaleDw",  "ServiceLevelObjective": "DW600"}` |
| Function2 | 0 0 20 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW200"}` |

#### <a name="example-2"></a>Exemplo 2: 

Todos os dias, aumentar verticalmente às 8:00 para DW1000, reduzir verticalmente uma vez para DW600 às 16:00 e reduzir verticalmente às 22:00 para DW200.

| Função  | Agenda     | Operação                                |
| :-------- | :----------- | :--------------------------------------- |
| Function1 | 0 0 8 * * *  | `var operation = {"operationType": "ScaleDw",  "ServiceLevelObjective": "DW1000"}` |
| Function2 | 0 0 16 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW600"}` |
| Function3 | 0 0 22 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW200"}` |

#### <a name="example-3"></a>Exemplo 3: 

Aumentar verticalmente às 8:00 para DW1000 e reduzir verticalmente uma vez para DW600 às 16:00 aos dias de semana. É colocado em pausa à sexta-feira às 23:00 e retomado às 7:00 de segunda-feira.

| Função  | Agenda       | Operação                                |
| :-------- | :------------- | :--------------------------------------- |
| Function1 | 0 0 8 * * 1-5  | `var operation = {"operationType": "ScaleDw",    "ServiceLevelObjective": "DW1000"}` |
| Function2 | 0 0 16 * * 1-5 | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW600"}` |
| Function3 | 0 0 23 * * 5   | `var operation = {"operationType": "PauseDw"}` |
| Function4 | 0 0 7 * * 0    | `var operation = {"operationType": "ResumeDw"}` |



## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as funções do Azure de [acionador de temporização](../azure-functions/functions-create-scheduled-function.md).

Veja o [repositório de exemplos](https://github.com/Microsoft/sql-data-warehouse-samples) do SQL Data Warehouse.



[schedule examples]: ../azure-functions/functions-bindings-timer.md#schedule-examples

[Application Settings]: ../azure-functions/functions-how-to-use-azure-function-app-settings.md
[Add a new trigger function]: manage-compute-with-azure-functions.md#add-a-new-trigger-function

[Performance Tiers]: performance-tiers.md
