---
title: Conceitos, termos e entidades do Scheduler | Microsoft Docs
description: "Conceitos, terminologia e hierarquia de entidades do Agendador do Azure, incluindo tarefas e coleções de tarefas.  Mostra um exemplo abrangente de uma tarefa agendada."
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: 
ms.assetid: 3ef16fab-d18a-48ba-8e56-3f3e0a1bcb92
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: get-started-article
ms.date: 08/18/2016
ms.author: deli
ms.openlocfilehash: 0f035b58ccd140a5481703df7e184206da2ed651
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="scheduler-concepts-terminology--entity-hierarchy"></a>Conceitos, terminologia e hierarquia de entidades do Agendador
## <a name="scheduler-entity-hierarchy"></a>Hierarquia de entidades do Agendador
A tabela seguinte descreve os principais recursos expostos ou utilizados pela API do Agendador:

| Recurso | Descrição |
| --- | --- |
| **Coleção de tarefas** |Uma coleção de tarefas contém um grupo de tarefas e mantém as definições, quotas e limitações que são partilhadas pelas tarefas na coleção. Uma coleção de tarefas é criada por um proprietário da subscrição e agrupa as tarefas com base nos limites de utilização ou da aplicação. Está restringida a uma região. Também permite a imposição de quotas para restringir a utilização de todas as tarefas nessa coleção. As quotas incluem MaxJobs e MaxRecurrence. |
| **Tarefa** |Uma tarefa define uma única ação periódica, com estratégias simples ou complexas para execução. As ações podem incluir pedidos HTTP, da fila de armazenamento, da fila de barramento de serviço ou do tópico de barramento de serviço. |
| **Histórico de tarefas** |Um histórico de tarefas representa os detalhes da execução de uma tarefa. Contém êxito vs. falha, bem como todos os detalhes de resposta. |

## <a name="scheduler-entity-management"></a>Gestão de entidades do Agendador
Um nível elevado, o agendador e a API de gestão de serviço expõem as seguintes operações nos recursos:

| Capacidade | Descrição e endereço de URI |
| --- | --- |
| **Gestão de coleções de tarefas** |Suporte de GET, PUT e DELETE para criar e modificar as coleções de tarefas e as tarefas contidas nas mesmas. Uma coleção de tarefas é um contentor para tarefas e mapas para quotas e definições partilhadas. Os exemplos de quotas, descritos mais tarde, são o número máximo de tarefas e o intervalo de periodicidade mais pequeno. <p>PUT e DELETE: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}`</p><p>GET: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}`</p> |
| **Gestão de tarefas** |Suporte de GET, PUT, POST, PATCH e DELETE para criar e modificar tarefas. Todas as tarefas têm de pertencer a uma coleção de tarefas já existente, pelo que não existe nenhuma criação implícita. <p>`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}/jobs/{jobName}`</p> |
| **Gestão do histórico de tarefas** |Suporte de GET para obtenção de 60 dias do histórico de execução da tarefa, como o tempo decorrido da tarefa e os resultados de execução da tarefa. Adiciona suporte de parâmetros de cadeias de consulta para filtrar com base no estado. <P>`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}/jobs/{jobName}/history`</p> |

## <a name="job-types"></a>Tipos de tarefa
Existem vários tipos de tarefa: tarefas HTTP (incluindo as tarefas HTTPS que suportam SSL), tarefas de fila de armazenamento, tarefas de fila de barramento de serviço e tarefas de tópico de barramento de serviço. As tarefas HTTP são ideais se tiver um ponto final de uma carga de trabalho ou serviço existente. Pode utilizar as tarefas de fila de armazenamento para publicar mensagens em filas de armazenamento, pelo que essas tarefas são ideais para cargas de trabalho que utilizam filas de armazenamento. Do mesmo modo, as tarefas de barramento de serviço são ideais para cargas de trabalho que utilizam tópicos e filas de barramento de serviço.

## <a name="the-job-entity-in-detail"></a>A entidade "tarefa" em detalhe
Um nível básico, uma tarefa agendada tem várias partes:

* A ação a efetuar quando é acionado o temporizador da tarefa  
* (Opcional) O tempo para executar a tarefa  
* (Opcional) Quando repetir a tarefa e com que frequência  
* (Opcional) Uma ação a acionar se a ação principal falhar  

Internamente, uma tarefa agendada também contém dados fornecidos pelo sistema, como o tempo de execução agendado seguinte.

O código seguinte mostra um exemplo abrangente de uma tarefa agendada. Os detalhes são fornecidos nas secções subsequentes.

    {
        "startTime": "2012-08-04T00:00Z",               // optional
        "action":
        {
            "type": "http",
            "retryPolicy": { "retryType":"none" },
            "request":
            {
                "uri": "http://contoso.com/foo",        // required
                "method": "PUT",                        // required
                "body": "Posting from a timer",         // optional
                "headers":                              // optional

                {
                    "Content-Type": "application/json"
                },
            },
           "errorAction":
           {
               "type": "http",
               "request":
               {
                   "uri": "http://contoso.com/notifyError",
                   "method": "POST",
               },
           },
        },
        "recurrence":                                   // optional
        {
            "frequency": "week",                        // can be "year" "month" "day" "week" "minute"
            "interval": 1,                              // optional, how often to fire (default to 1)
            "schedule":                                 // optional (advanced scheduling specifics)
            {
                "weekDays": ["monday", "wednesday", "friday"],
                "hours": [10, 22]
            },
            "count": 10,                                 // optional (default to recur infinitely)
            "endTime": "2012-11-04",                     // optional (default to recur infinitely)
        },
        "state": "disabled",                           // enabled or disabled
        "status":                                       // controlled by Scheduler service
        {
            "lastExecutionTime": "2007-03-01T13:00:00Z",
            "nextExecutionTime": "2007-03-01T14:00:00Z ",
            "executionCount": 3,
                                                "failureCount": 0,
                                                "faultedCount": 0
        },
    }

Conforme visto na tarefa agendada de exemplo acima, uma definição de tarefa tem várias partes:

* Hora de início (“startTime”)  
* Ação (“action”), que inclui a ação de erro (“errorAction”)
* Periodicidade (“recurrence”)  
* Estado (“state”)  
* Estado (“status”)  
* Política de repetição (“retryPolicy”)  

Vamos examinar cada uma destas partes detalhadamente:

## <a name="starttime"></a>startTime
"startTime" é a hora de início e permite ao chamador especificar um desvio de fuso horário durante a transmissão no [formato ISO-8601](http://en.wikipedia.org/wiki/ISO_8601).

## <a name="action-and-erroraction"></a>action e errorAction
"action" é a ação invocada em cada ocorrência e descreve um tipo de invocação de serviço. A ação é o que será executado na agenda fornecida. O agendador suporta ações de HTTP, fila de armazenamento, tópico de barramento de serviço e fila de barramento de serviço.

A ação no exemplo acima é uma ação de HTTP. Segue-se um exemplo de uma ação de fila de armazenamento:

    {
            "type": "storageQueue",
            "queueMessage":
            {
                "storageAccount": "myStorageAccount",  // required
                "queueName": "myqueue",                // required
                "sasToken": "TOKEN",                   // required
                "message":                             // required
                    "My message body",
            },
    }

Segue-se um exemplo de uma ação de tópico de barramento de serviço:

  "action": { "type": "serviceBusTopic", "serviceBusTopicMessage": { "topicPath": "t1",  
      "namespace": "mySBNamespace", "transportType": "netMessaging", // Can be either netMessaging or AMQP "authentication": { "sasKeyName": "QPolicy", "type": "sharedAccessKey" }, "message": "Some message", "brokeredMessageProperties": {}, "customMessageProperties": { "appname": "FromScheduler" } }, }

Segue-se um exemplo de uma ação de fila de barramento de serviço:

  "action": { "serviceBusQueueMessage": { "queueName": "q1",  
      "namespace": "mySBNamespace", "transportType": "netMessaging", // Can be either netMessaging or AMQP "authentication": {  
        "sasKeyName": "QPolicy", "type": "sharedAccessKey" }, "message": "Some message",  
      "brokeredMessageProperties": {}, "customMessageProperties": { "appname": "FromScheduler" } }, "type": "serviceBusQueue" }

"errorAction" é o processador de erros, a ação invocada quando ocorre a ação principal falha. Pode utilizar esta variável para chamar um ponto final de processamento de erros ou enviar uma notificação de utilizador. Pode ser utilizada para alcançar um ponto final secundário caso o principal não esteja disponível (por exemplo, em caso de desastre no site do ponto final) ou notificar um ponto final de processamento de erros. Tal como a ação principal, a ação de erro pode ter uma lógica simples ou composta com base nas outras ações. Para saber como criar um token SAS, veja [Criar e Utilizar uma Assinatura de Acesso Partilhado](https://msdn.microsoft.com/library/azure/jj721951.aspx).

## <a name="recurrence"></a>recurrence
A periodicidade tem várias partes:

* Frequência: um minuto, hora, dia, semana, mês, ano  
* Intervalo: intervalo com a frequência especificada para a periodicidade  
* Agenda prescrita: especifique minutos, horas, dias da semana, meses e dias do mês da periodicidade  
* Contagem: contagem de ocorrências  
* Hora de fim: não serão executadas tarefas após a hora de fim especificada  

Uma tarefa é periódica se tiver um objeto periódico especificado na respetiva definição JSON. Se count e endTime forem especificados, é honrada a regra de conclusão que ocorre primeiro.

## <a name="state"></a>state
O estado da tarefa é um de quatro valores: ativado, desativado, concluído ou falhado. Pode colocar tarefas PUT ou PATCH para atualizá-las para o estado ativado ou desativado. Se uma tarefa tiver sido concluída ou tiver falhado, trata-se de um estado final que não pode ser atualizado (embora a tarefa ainda possa ser DELETED). Um exemplo da propriedade de estado é o seguinte:

        "state": "disabled", // enabled, disabled, completed, or faulted
As tarefas concluídas e falhadas são eliminadas após 60 dias.

## <a name="status"></a>status
Depois de uma tarefa do Agendador ser iniciada, serão devolvidas informações sobre o estado atual da tarefa. Este objeto não é definível pelo utilizador, mas sim pelo sistema. No entanto, está incluído no objeto de tarefa (em vez de um recurso ligado separado) para que possa obter facilmente o estado de uma tarefa.

O estado da tarefa inclui a hora da execução anterior (se aplicável), a hora da execução agendada seguinte (para tarefas em curso) e a contagem de execução da tarefa.

## <a name="retrypolicy"></a>retryPolicy
Se uma tarefa do Agendador falhar, é possível especificar uma política de repetição para determinar se e como a ação será repetida. Tal é determinado através do objeto **retryType** — está definido como **none** se não existir nenhuma política de repetição, conforme mostrado acima. Defina-o como **fixed** se existir uma política de repetição.

Para definir uma política de repetição, é possível especificar duas definições adicionais: um intervalo entre tentativas (**retryInterval**) e o número de tentativas (**retryCount**).

O intervalo entre tentativas, especificado com o objeto **retryInterval**, é o intervalo entre as repetições. O valor predefinido é 30 segundos, o valor mínimo configurável é 15 segundos e o valor máximo é 18 meses. As tarefas nas coleções de tarefas gratuitas têm um valor configurável mínimo de 1 hora.  Está definido no formato ISO 8601. Do mesmo modo, o valor de número de tentativas é especificado com o objeto **retryCount**; é o número de vezes que é tentada uma repetição. O valor predefinido é 4 e o valor máximo é 20\. O **retryInterval** e o **retryCount** são opcionais. São fornecidos os respetivos valores predefinidos se **retryType** estiver definido como **fixed** e não existirem valores especificados explicitamente.

## <a name="see-also"></a>Consultar também
 [O que é o Scheduler?](scheduler-intro.md)

 [Começar a utilizar o Scheduler no portal do Azure](scheduler-get-started-portal.md)

 [Planos e faturação no Azure Scheduler](scheduler-plans-billing.md)

 [Como criar agendas complexas e periodicidade avançada com o Azure Scheduler](scheduler-advanced-complexity.md)

 [Referência da API REST do Azure Scheduler](https://msdn.microsoft.com/library/mt629143)

 [Referência de cmdlets do PowerShell do Azure Scheduler](scheduler-powershell-reference.md)

 [Elevada disponibilidade e fiabilidade do Azure Scheduler](scheduler-high-availability-reliability.md)

 [Limites, predefinições e códigos de erro do Azure Scheduler](scheduler-limits-defaults-errors.md)

 [Autenticação de saída do Azure Scheduler](scheduler-outbound-authentication.md)

