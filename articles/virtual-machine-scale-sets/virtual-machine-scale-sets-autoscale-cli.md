---
title: "Conjuntos de dimensionamento de máquina virtual de dimensionamento automático com a CLI do Azure | Microsoft Docs"
description: "Define como criar regras de dimensionamento automático para dimensionamento da máquina virtual com o 2.0 CLI do Azure"
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 83e93d9c-cac0-41d3-8316-6016f5ed0ce4
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: iainfou
ms.openlocfilehash: 6e8fadd54a78d432ed802f4c4880c2f77bb28c37
ms.sourcegitcommit: 2d1153d625a7318d7b12a6493f5a2122a16052e0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2017
---
# <a name="automatically-scale-a-virtual-machine-scale-set-with-the-azure-cli-20"></a>Dimensionar automaticamente um conjunto com o 2.0 CLI do Azure de dimensionamento de máquina virtual
Quando cria um conjunto de dimensionamento, é possível definir o número de instâncias VM que pretende executar. Como muda o seu pedido de aplicação, pode automaticamente aumentar ou reduzir o número de instâncias de VM. A capacidade de dimensionamento automático permite-lhe manter-se a pedido do cliente ou responder a alterações de desempenho da aplicação durante todo o ciclo de vida da sua aplicação.

Este artigo mostra como criar regras de dimensionamento automático com o 2.0 CLI do Azure que monitorizar o desempenho das instâncias da VM no seu conjunto de dimensionamento. Estas regras de dimensionamento automático aumentam ou reduzir o número de instâncias de VM em resposta a estas métricas de desempenho. Também pode executar estes passos com [Azure PowerShell](virtual-machine-scale-sets-autoscale-powershell.md) ou no [portal do Azure](virtual-machine-scale-sets-autoscale-portal.md).


## <a name="prerequisites"></a>Pré-requisitos
Para criar regras de dimensionamento automático, terá de uma máquina virtual existente conjunto de dimensionamento. Pode criar uma escala definida com o [portal do Azure](virtual-machine-scale-sets-portal-create.md), [Azure CLI 2.0](virtual-machine-scale-sets-create.md#create-from-azure-cli), ou [Azure PowerShell](virtual-machine-scale-sets-create.md#create-from-powershell).

Para tornar mais fácil de criar as regras de dimensionamento automático, definir algumas variáveis para o conjunto de dimensionamento. O exemplo seguinte define as variáveis para o conjunto nomeado de dimensionamento *myScaleSet* no grupo de recursos denominado *myResourceGroup* e no *eastus* região. A subscrição ID é obtido com [mostrar de conta az](/cli/azure/account#az_account_show). Se tiver várias subscrições associadas à sua conta, é devolvida apenas a primeira subscrição. Ajuste os nomes e o ID de subscrição da seguinte forma:

```azurecli
sub=$(az account show --query id -o tsv)
resourcegroup_name="myResourceGroup"
scaleset_name="myScaleSet"
location_name="eastus"
```

## <a name="define-an-autoscale-profile"></a>Definir um perfil de dimensionamento automático
Regras de dimensionamento automático são implementadas como JSON (JavaScript Object Notation) com o 2.0 CLI do Azure. Completado JSON que define e implementa as regras de dimensionamento automático é possível encontrar o artigo. 

O início do perfil de dimensionamento automático define a predefinição, o conjunto de dimensionamento, máximo e mínimo de capacidade. O exemplo seguinte define a capacidade predefinida e mínimo, de *2* VM instâncias e um máximo de *10*:

```json
{
  "name": "autoscale rules",
  "capacity": {
    "minimum": "2",
    "maximum": "10",
    "default": "2"
  }
}
```


## <a name="create-a-rule-to-automatically-scale-out"></a>Criar uma regra para ampliar automaticamente
Se aumentar o seu pedido de aplicação, a carga sobre as instâncias de VM no seu dimensionamento definir aumenta. Se este aumento de carga é consistente, em vez de apenas um breve pedido, pode configurar regras de dimensionamento automático para aumentar o número de instâncias VM no conjunto de dimensionamento. Quando estas instâncias VM são criadas e as suas aplicações são implementadas, inicia o conjunto de dimensionamento distribuir o tráfego aos mesmos através do Balanceador de carga. Controlar as métricas para monitorizar, tais como CPU ou disco, quanto a carga da aplicação têm de cumprir um determinado limiar, e quantas instâncias VM para adicionar à escala definido.

Vamos criar uma regra que aumenta o número de instâncias VM na escala definida quando a carga média da CPU é superior a 70% durante um período de 10 minutos. Quando a regra é acionado, o número de instâncias VM é aumentado por 20%. Em conjuntos de dimensionamento com um pequeno número de instâncias de VM, pode configurar o `type` para *ChangeCount* e aumentar a `value` por *1* ou *2* instâncias. Conjuntos de dimensionamento com um grande número de instâncias de VM, um aumento de 20% ou de 10% instâncias de VM podem ser mais adequadas.

Os parâmetros seguintes são utilizados para esta regra:

| Parâmetro         | Explicação                                                                                                         | Valor           |
|-------------------|---------------------------------------------------------------------------------------------------------------------|-----------------|
| *metricName*      | A métrica de desempenho para monitorizar e aplicar escala definir ações.                                                   | Percentagem de CPU  |
| *intervalo de agregação*       | Frequência as métricas são recolhidas para análise.                                                                   | um minuto        |
| *timeAggregation* | Define a forma como as métricas recolhidas devem ser agregadas para análise.                                                | Média         |
| *timeWindow*      | A quantidade de tempo monitorizado antes em comparação com os valores da métrica e de limiar.                                   | 10 minutos      |
| *operador*        | Operador utilizado para comparar os dados métricos contra o limiar.                                                     | Maior que    |
| *limiar*       | O valor que faz com que a regra de dimensionamento automático acionar uma ação.                                                      | 70%             |
| *direção*       | Define se o conjunto de dimensionamento deve aumentar ou reduzir verticalmente quando a regra se aplica.                                             | Aumentar        |
| *tipo*            | Indica que o número de instâncias VM deve ser alterado por um período de percentagem.                                 | Percentagem de alteração  |
| *valor*           | Quantas instâncias VM devem ser escaladas para cima ou para baixo quando se aplica a regra.                                            | 20              |
| *cooldown*        | A quantidade de tempo de espera antes da regra é aplicada novamente para que as ações de dimensionamento automático de ter a hora entre em vigor. | 5 minutos       |

O exemplo seguinte define a regra para aumentar horizontalmente o número de instâncias VM. O *metricResourceUri* utiliza as variáveis definidas anteriormente para o nome do conjunto do ID de subscrição, o nome do grupo de recursos e o dimensionamento:

```json
{
  "metricTrigger": {
    "metricName": "Percentage CPU",
    "metricNamespace": "",
    "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'",
    "metricResourceLocation": "'$location_name'",
    "timeGrain": "PT1M",
    "statistic": "Average",
    "timeWindow": "PT10M",
    "timeAggregation": "Average",
    "operator": "GreaterThan",
    "threshold": 70
  },
  "scaleAction": {
    "direction": "Increase",
    "type": "PercentChangeCount",
    "value": "20",
    "cooldown": "PT5M"
  }
}
```


## <a name="create-a-rule-to-automatically-scale-in"></a>Criar uma regra para dimensionar automaticamente de
Num evening ou fim de semana, pode diminuir o seu pedido de aplicação. Se este carregamento menor consistente durante um período de tempo, pode configurar regras de dimensionamento automático para reduzir o número de instâncias VM no conjunto de dimensionamento. Esta ação de dimensionamento reduz o custo para executar a escala definida como executar apenas o número de instâncias necessário para satisfazer a procura atual.

Crie outra regra diminui o número de instâncias VM na escala definido quando a carga de CPU média, em seguida, descerem abaixo de 30% durante um período de 10 minutos. O exemplo seguinte define a regra para aumentar horizontalmente o número de instâncias VM. O *metricResourceUri* utiliza as variáveis definidas anteriormente para o nome do conjunto do ID de subscrição, o nome do grupo de recursos e o dimensionamento:

```json
{
  "metricTrigger": {
    "metricName": "Percentage CPU",
    "metricNamespace": "",
    "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'",
    "metricResourceLocation": "'$location_name'",
    "timeGrain": "PT1M",
    "statistic": "Average",
    "timeWindow": "PT10M",
    "timeAggregation": "Average",
    "operator": "LessThan",
    "threshold": 30
  },
  "scaleAction": {
    "direction": "Decrease",
    "type": "PercentChangeCount",
    "value": "20",
    "cooldown": "PT5M"
  }
}
```


## <a name="apply-autoscale-rules-to-a-scale-set"></a>Aplicar regras de dimensionamento automático para um conjunto de dimensionamento
É o último passo para aplicar o perfil de dimensionamento automático e regras para o conjunto de dimensionamento. A escala tem a capacidade para dimensionar automaticamente de entrada ou saída com base no pedido de aplicação. Aplicar o perfil de dimensionamento automático com [criar definições de dimensionamento automático do monitor az](/cli/azure/monitor/autoscale-settings#az_monitor_autoscale_settings_create) da seguinte forma. O JSON concluído utiliza o perfil e as regras seja indicadas nas secções anteriores.

```azurecli
az monitor autoscale-settings create \
    --resource-group myResourceGroup \
    --name autoscale \
    --parameters '{"autoscale_setting_resource_name": "autoscale",
      "enabled": true,
      "location": "'$location_name'",
      "notifications": [],
      "profiles": [
        {
          "name": "autoscale by percentage based on CPU usage",
          "capacity": {
            "minimum": "2",
            "maximum": "10",
            "default": "2"
          },
          "rules": [
            {
              "metricTrigger": {
                "metricName": "Percentage CPU",
                "metricNamespace": "",
                "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'",
                "metricResourceLocation": "'$location_name'",
                "timeGrain": "PT1M",
                "statistic": "Average",
                "timeWindow": "PT10M",
                "timeAggregation": "Average",
                "operator": "GreaterThan",
                "threshold": 70
              },
              "scaleAction": {
                "direction": "Increase",
                "type": "PercentChangeCount",
                "value": "20",
                "cooldown": "PT5M"
              }
            },
            {
              "metricTrigger": {
                "metricName": "Percentage CPU",
                "metricNamespace": "",
                "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'",
                "metricResourceLocation": "'$location_name'",
                "timeGrain": "PT1M",
                "statistic": "Average",
                "timeWindow": "PT10M",
                "timeAggregation": "Average",
                "operator": "LessThan",
                "threshold": 30
              },
              "scaleAction": {
                "direction": "Decrease",
                "type": "PercentChangeCount",
                "value": "20",
                "cooldown": "PT5M"
              }
            }
          ]
        }
      ],
      "tags": {},
      "target_resource_uri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'"
    }'
```


## <a name="monitor-number-of-instances-in-a-scale-set"></a>Monitorizar o número de instâncias de um conjunto de dimensionamento
Para ver o número e o estado de instâncias VM, ver uma lista de instâncias no seu dimensionamento definido com [az vmss-instâncias da lista](/cli/azure/vmss#az_vmss_list_instances). O estado indica se a instância VM aprovisionamento como aumenta horizontalmente de forma a escala definida automaticamente ou é desaprovisionamento como escala reduz horizontalmente automaticamente. O exemplo seguinte visualiza o estado de instância VM para o conjunto nomeado de dimensionamento *myScaleSet* no grupo de recursos denominado *myResourceGroup*:

```azurecli
az vmss list-instances \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --output table
```


## <a name="autoscale-based-on-a-schedule"></a>Dimensionamento automático com base numa agenda
Os exemplos anteriores ampliada automaticamente uma escala definida no ou terminar com a métrica de anfitrião básica, tais como a utilização da CPU. Também pode criar regras de dimensionamento automático com base no agendas. Estas regras baseadas na agenda permitem-lhe automaticamente aumentar horizontalmente o número de instâncias VM à frente de um aumento previsto no pedido de aplicação, tais como as horas de núcleo de trabalho e, em seguida, dimensionar automaticamente o número de instâncias de cada vez que antecipa inferior pedido, tal como o fim de semana.

Para utilizar as regras de dimensionamento automático baseadas na agenda, crie um perfil JSON que define o número de instâncias VM para executar um fixo de início e a janela de tempo de fim. O exemplo seguinte define uma regra para aumentar horizontalmente *10* instâncias em *9* A.M cada dia de trabalho (segunda-feira através de sexta-feira).

```json
{
  "name": "Scale out during each work day",
  "capacity": {
      "minimum": "10",
      "maximum": "10",
      "default": "10"
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
              9
          ],
          "minutes": [
              0
          ]
      }
  }
}
```

Dimensionar no durante o evening, crie outra regra que especifica um número inferior de instâncias de VM e uma hora de início adequado.

O exemplo completo seguinte define as regras de escalamento horizontal e, em seguida, a escala em, em seguida, aplica-se o perfil de dimensionamento automático com [criar definições de dimensionamento automático do monitor az](/cli/azure/monitor/autoscale-settings#az_monitor_autoscale_settings_create). Este exemplo substitui as regras de dimensionamento automático baseado em métrica criadas nos exemplos anteriores. O *metricResourceUri* utiliza as variáveis definidas anteriormente para o nome do conjunto do ID de subscrição, o nome do grupo de recursos e o dimensionamento:

```azurecli
az monitor autoscale-settings create \
    --resource-group myResourceGroup \
    --name autoscale \
    --parameters '{"autoscale_setting_resource_name": "autoscale",
      "enabled": true,
      "location": "'$location_name'",
      "notifications": [],
      "profiles": [
        {
          "name": "Scale out during each work day",
          "capacity": {
            "minimum": "10",
            "maximum": "10",
            "default": "10"
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
                9
              ],
              "minutes": [
                0
              ]
            }
          }
        },
        {
          "name": "Scale in during the evening",
          "capacity": {
            "minimum": "3",
            "maximum": "3",
            "default": "3"
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
                18
              ],
              "minutes": [
                0
              ]
            }
          }
        }
      ],
      "tags": {},
      "target_resource_uri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'"
    }'
```


## <a name="next-steps"></a>Passos seguintes
Neste artigo, aprendeu a utilizar regras de dimensionamento automático para aumentar horizontalmente e aumentar ou diminuir o *número* de instâncias VM no seu dimensionamento definido. Também pode reduzir verticalmente para aumentar ou diminuir a instância VM *tamanho*. Para obter mais informações, consulte [Vertical dimensionamento automático com conjuntos de dimensionamento de máquina virtual](virtual-machine-scale-sets-vertical-scale-reprovision.md).

Para obter informações sobre como gerir as instâncias de VM, consulte [conjuntos de dimensionamento de máquina virtual de gerir com o Azure PowerShell](virtual-machine-scale-sets-windows-manage.md).

Para saber como gerar alertas quando o dimensionamento automático regras acionador, consulte [utilize ações de dimensionamento automático para enviar correio eletrónico e webhook notificações de alertas no Monitor de Azure](../monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md). Também pode [registos de auditoria de Utilize para enviar correio eletrónico e webhook notificações de alertas no Monitor de Azure](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md).
