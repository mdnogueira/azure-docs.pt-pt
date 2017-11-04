---
title: "Avançada com máquinas virtuais do Azure de dimensionamento automático | Microsoft Docs"
description: "Utiliza o Gestor de recursos e conjuntos de dimensionamento de VM com várias regras e perfis que enviem correio eletrónico e chamar URLs de webhook com ações de dimensionamento."
author: anirudhcavale
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 7e3576e2-4a2b-4736-b5ae-98c4689cdd2b
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2016
ms.author: ancav
ms.openlocfilehash: 80955535c8d863cd3d8d1b77e2ab8bc016b6d9f3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="advanced-autoscale-configuration-using-resource-manager-templates-for-vm-scale-sets"></a>Configuração de dimensionamento automático avançadas utilizando modelos do Resource Manager para conjuntos de dimensionamento de VM
Pode no dimensionamento horizontal e em conjuntos de dimensionamento de Máquina Virtual com base nos limiares de métrica de desempenho, por numa agenda periódica, ou por uma data específica. Também pode configurar as notificações de e-mail e webhook para ações de dimensionamento. Estas instruções mostram um exemplo de configuração os objetos com um modelo do Resource Manager no conjunto de dimensionamento da VM.

> [!NOTE]
> Enquanto esta explicação passo a passo explica os passos para conjuntos de dimensionamento de VM, as mesmas informações aplica-se para efetuar o dimensionamento automático [serviços em nuvem](https://azure.microsoft.com/services/cloud-services/), e [Web Apps do App Service -](https://azure.microsoft.com/services/app-service/web/).
> Para uma escala simple de entrada/saída de definição de um conjunto de dimensionamento de VM com base numa métrica de desempenho simples, tais como CPU, consulte o [Linux](../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md) e [Windows](../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md) documentos
>
>

## <a name="walkthrough"></a>Instruções
Esta explicação passo a passo, utilizamos [Explorador de recursos do Azure](https://resources.azure.com/) para configurar e atualizar a definição de dimensionamento automático para um conjunto de dimensionamento. Explorador de recursos do Azure é uma forma fácil de gerir recursos do Azure através de modelos do Resource Manager. Se estiver familiarizado com a ferramenta Explorador de recursos do Azure, leia [esta introdução](https://azure.microsoft.com/blog/azure-resource-explorer-a-new-tool-to-discover-the-azure-api/).

1. Implemente um novo conjunto com uma definição de dimensionamento automático básico de dimensionamento. Este artigo utilizará a partir da Galeria de início rápido do Azure, que tem um Windows do conjunto de dimensionamento com um modelo de dimensionamento automático básico. Conjuntos de dimensionamento de Linux funcionam da mesma forma.
2. Depois de criar o conjunto de dimensionamento, navegue para o recurso de conjunto de dimensionamento do Explorador de recursos do Azure. Consulte o seguinte no nó de insights.

    ![Explorador do Azure](./media/insights-advanced-autoscale-vmss/azure_explorer_navigate.png)

    A execução do modelo foi criado uma definição de dimensionamento automático predefinida com o nome **'autoscalewad'**. No lado direito, pode ver a definição completa desta definição de dimensionamento automático. Neste caso, a definição de dimensionamento automático predefinida é fornecido com uma regra % baseado em CPU escalável e escala.  

3. Agora pode adicionar mais perfis e as regras com base na agenda ou requisitos específicos. Iremos criar uma definição de dimensionamento automático com três perfis. Para compreender os perfis e as regras de dimensionamento automático, reveja [melhores práticas de dimensionamento automático](insights-autoscale-best-practices.md).  

    | Regras de & perfis | Descrição |
    |--- | --- |
    | **Perfil** |**Com base no desempenho/métrica** |
    | Regra |Contagem de mensagens da fila de barramento de serviço > x |
    | Regra |Contagem de mensagens da fila de barramento de serviço < y |
    | Regra |% De CPU > n |
    | Regra |% De CPU < p |
    | **Perfil** |**Dia da semana horas da manhã (nenhum regras)** |
    | **Perfil** |**Dia de início de produto (nenhum regras)** |

4. Eis um cenário de dimensionamento hipotético que utilizamos para esta passagem.

    * **Carga baseado em** -gostaria de aumentar horizontalmente ou em com base na carga na minha aplicação alojada na minha set.* de escala
    * **O tamanho da fila de mensagens** -posso utilizar uma fila de barramento de serviço para as mensagens a receber para a minha aplicação. Posso utilizar contagem de mensagens da fila e a % de CPU e configurar um perfil predefinido para acionar uma ação de dimensionamento, se uma contagem de mensagens ou CPU pedidos com êxito o threshold.*
    * **Hora da semana e dia** -pretendo que um perfil de "Hora do dia', com base periódica semanal chamado 'Horas da manhã de dia da semana'. Com base nos dados históricos, poderei sabê-lo é melhor ter determinado número de instâncias de VM para processar a carga da minha aplicação durante este time.*
    * **Datas especiais** -posso adicionar um perfil de 'Produto iniciar Day'. Posso planeie com antecedência para datas específicas para que a minha aplicação esteja pronta para processar a carga devido anúncios de marketing e quando colocarmos novo produto no application.*
    * *Os dois últimos perfis também podem ter outras métrica baseada em regras de desempenho dentro delas. Neste caso, posso decidiu não ter um e, em vez disso, dependem da métrica de desempenho de predefinição baseada em regras. As regras são opcionais para os perfis periódicos e baseados em data.*

    Atribuição de prioridades do motor de dimensionamento automático de perfis e as regras também é captada no [melhores práticas de dimensionamento automático](insights-autoscale-best-practices.md) artigo.
    Para obter uma lista das métricas comuns para dimensionamento automático, consulte [comuns métricas para o dimensionamento automático](insights-autoscale-common-metrics.md)

5. Certifique-se de está no **leitura/escrita** modo no Explorador de recursos

    ![Autoscalewad, definição de dimensionamento automático predefinido](./media/insights-advanced-autoscale-vmss/autoscalewad.png)

6. Clique em Editar. **Substitua** o elemento 'perfis' na definição de dimensionamento automático com a seguinte configuração:

    ![Perfis](./media/insights-advanced-autoscale-vmss/profiles.png)

    ```
    {
            "name": "Perf_Based_Scale",
            "capacity": {
              "minimum": "2",
              "maximum": "12",
              "default": "2"
            },
            "rules": [
              {
                "metricTrigger": {
                  "metricName": "MessageCount",
                  "metricNamespace": "",
                  "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue",
                  "timeGrain": "PT5M",
                  "statistic": "Average",
                  "timeWindow": "PT5M",
                  "timeAggregation": "Average",
                  "operator": "GreaterThan",
                  "threshold": 10
                },
                "scaleAction": {
                  "direction": "Increase",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              },
              {
                "metricTrigger": {
                  "metricName": "MessageCount",
                  "metricNamespace": "",
                  "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue",
                  "timeGrain": "PT5M",
                  "statistic": "Average",
                  "timeWindow": "PT5M",
                  "timeAggregation": "Average",
                  "operator": "LessThan",
                  "threshold": 3
                },
                "scaleAction": {
                  "direction": "Decrease",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              },
              {
                "metricTrigger": {
                  "metricName": "Percentage CPU",
                  "metricNamespace": "",
                  "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/<this_vmss_name>",
                  "timeGrain": "PT5M",
                  "statistic": "Average",
                  "timeWindow": "PT30M",
                  "timeAggregation": "Average",
                  "operator": "GreaterThan",
                  "threshold": 85
                },
                "scaleAction": {
                  "direction": "Increase",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              },
              {
                "metricTrigger": {
                  "metricName": "Percentage CPU",
                  "metricNamespace": "",
                  "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/<this_vmss_name>",
                  "timeGrain": "PT5M",
                  "statistic": "Average",
                  "timeWindow": "PT30M",
                  "timeAggregation": "Average",
                  "operator": "LessThan",
                  "threshold": 60
                },
                "scaleAction": {
                  "direction": "Decrease",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              }
            ]
          },
          {
            "name": "Weekday_Morning_Hours_Scale",
            "capacity": {
              "minimum": "4",
              "maximum": "12",
              "default": "4"
            },
            "rules": [],
            "recurrence": {
              "frequency": "Week",
              "schedule": {
                "timeZone": "Pacific Standard Time",
                "days": [
                  "Monday",
                  "Tuesday",
                  "Wednesday",
                  "Thursday",
                  "Friday"
                ],
                "hours": [
                  6
                ],
                "minutes": [
                  0
                ]
              }
            }
          },
          {
            "name": "Product_Launch_Day",
            "capacity": {
              "minimum": "6",
              "maximum": "20",
              "default": "6"
            },
            "rules": [],
            "fixedDate": {
              "timeZone": "Pacific Standard Time",
              "start": "2016-06-20T00:06:00Z",
              "end": "2016-06-21T23:59:00Z"
            }
          }
    ```
    Para e os respetivos valores de campos suportados, consulte [documentação da API de REST de dimensionamento automático](https://msdn.microsoft.com/en-us/library/azure/dn931928.aspx). Agora, a definição de dimensionamento automático contém três perfis explicados anteriormente.

7. Por fim, observe o dimensionamento automático **notificação** secção. Notificações de dimensionamento automático permitem-lhe efetuar três ações quando Escalamento horizontal ou na ação é acionado com êxito.
   - Notificar o administrador e coadministradores da sua subscrição
   - Um conjunto de utilizadores de e-mail
   - Acione uma chamada webhook. Quando acionado, o webhook envia os metadados sobre a condição de dimensionamento automático e a escala de conjunto de recursos. Para saber mais sobre o payload de webhook de dimensionamento automático, consulte o artigo [configurar Webhook & notificações por E-Mail para o dimensionamento automático](insights-autoscale-to-webhook-email.md).

   Adicione o seguinte para a definição de dimensionamento automático substituir o **notificação** elemento cujo valor é nulo

   ```
   "notifications": [
      {
        "operation": "Scale",
        "email": {
          "sendToSubscriptionAdministrator": true,
          "sendToSubscriptionCoAdministrators": false,
          "customEmails": [
              "user1@mycompany.com",
              "user2@mycompany.com"
              ]
        },
        "webhooks": [
          {
            "serviceUri": "https://foo.webhook.example.com?token=abcd1234",
            "properties": {
              "optional_key1": "optional_value1",
              "optional_key2": "optional_value2"
            }
          }
        ]
      }
    ]

   ```

   Atingiu o **colocar** botão no Explorador de recursos para atualizar a definição de dimensionamento automático.

Atualizado com uma definição de dimensionamento automático um conjunto a incluir vários perfis de dimensionamento e dimensionar as notificações de dimensionamento de VM.

## <a name="next-steps"></a>Passos Seguintes
Utilize estas ligações para saber mais sobre o dimensionamento automático.

[Resolver problemas de dimensionamento automático com conjuntos de dimensionamento de Máquina Virtual](../virtual-machine-scale-sets/virtual-machine-scale-sets-troubleshoot.md)

[Métricas de comuns de dimensionamento automático](insights-autoscale-common-metrics.md)

[Melhores práticas para o dimensionamento automático do Azure](insights-autoscale-best-practices.md)

[Gerir o dimensionamento automático através do PowerShell](insights-powershell-samples.md#create-and-manage-autoscale-settings)

[Gerir o dimensionamento automático ao utilizar a CLI](insights-cli-samples.md#autoscale)

[Configurar o Webhook & notificações por E-Mail para o dimensionamento automático](insights-autoscale-to-webhook-email.md)
