---
title: "Utilize o dimensionamento automático do Azure com a métrica de convidado num modelo de conjunto de dimensionamento do Linux | Microsoft Docs"
description: "Saiba como para dimensionar automaticamente através de métricas de convidado num modelo de conjunto de dimensionamento de Máquina Virtual do Linux"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: na
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: negat
ms.openlocfilehash: 98635ea6695fdb1e55456b5b6a293a3b4ad9d839
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="autoscale-using-guest-metrics-in-a-linux-scale-set-template"></a>Dimensionamento automático através de métricas de convidado num modelo de conjunto de dimensionamento do Linux

Existem dois tipos de métricas no Azure que estão reunidas a partir de VMs e conjuntos de dimensionamento: alguns vêm da VM do anfitrião e outros vêm da VM do convidado. Um nível elevado, se estiver a utilizar o padrão da CPU, disco e as métricas da rede, em seguida, nas métricas anfitriões são provavelmente uma boa opção. Se, no entanto, terá uma seleção maior das métricas, métricas de convidado são provavelmente uma opção melhor. Vamos observe as diferenças entre as duas:

Métricas de anfitrião são mais simples e mais fiável. Não necessitam de configuração adicional porque estes não são recolhidos pelo anfitrião de VM, enquanto que necessitam de métricas de convidado-nos instalar o [extensão do Windows Azure Diagnostics](../virtual-machines/windows/extensions-diagnostics-template.md) ou [extensão de diagnóstico do Azure de Linux](../virtual-machines/linux/diagnostic-extension.md)na VM do convidado. Um motivo comum para utilizar as métricas de convidados em vez de métricas de anfitrião é que as métricas de convidado fornecem uma seleção maior das métricas de métricas de anfitrião. Um exemplo desse tipo é métricas do consumo de memória, que só estão disponíveis através de métricas de convidado. As métricas de anfitrião suportados estão listadas [aqui](../monitoring-and-diagnostics/monitoring-supported-metrics.md), e as métricas frequentemente utilizadas convidado são listadas [aqui](../monitoring-and-diagnostics/insights-autoscale-common-metrics.md). Este artigo mostra como modificar o [modelo de conjunto de dimensionamento viável mínimo](./virtual-machine-scale-sets-mvss-start.md) utilizar regras de dimensionamento automático com base nas métricas de convidado para conjuntos de dimensionamento do Linux.

## <a name="change-the-template-definition"></a>Altere a definição do modelo

Pode ser visto o nosso modelo de conjunto mínimo de escala viável [aqui](https://raw.githubusercontent.com/gatneil/mvss/minimum-viable-scale-set/azuredeploy.json), e o nosso modelo para implementar o dimensionamento de Linux definido com dimensionamento automático baseada no convidado pode ser visto [aqui](https://raw.githubusercontent.com/gatneil/mvss/guest-based-autoscale-linux/azuredeploy.json). Vamos examinar diff utilizado para criar este modelo (`git diff minimum-viable-scale-set existing-vnet`) peça a informação:

Em primeiro lugar, iremos adicionar parâmetros para `storageAccountName` e `storageAccountSasToken`. O agente de diagnóstico irá armazenar os dados métricos num [tabela](../cosmos-db/table-storage-how-to-use-dotnet.md) nesta conta de armazenamento. A partir do agente de diagnóstico Linux versão 3.0, utilizar uma chave de acesso de armazenamento já não é suportada. Iremos tem de utilizar um [SAS Token](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

```diff
     },
     "adminPassword": {
       "type": "securestring"
+    },
+    "storageAccountName": {
+      "type": "string"
+    },
+    "storageAccountSasToken": {
+      "type": "securestring"
     }
   },
```

Em seguida, iremos modificar o conjunto de dimensionamento `extensionProfile` para incluir a extensão de diagnóstico. Nesta configuração, vamos especificar o ID de recurso da escala definido para recolher métricas, bem como a conta de armazenamento e o SAS token para utilizar para armazenar as métricas. Também iremos especificar frequência as métricas são agregadas (neste caso, cada minuto) e as métricas para controlar (neste cenário memória utilizada por cento). Para obter informações mais detalhadas sobre esta configuração e métricas que não seja a percentagem de memória utilizada, consulte [esta documentação](../virtual-machines/linux/diagnostic-extension.md).

```diff
                 }
               }
             ]
+          },
+          "extensionProfile": {
+            "extensions": [
+              {
+                "name": "LinuxDiagnosticExtension",
+                "properties": {
+                  "publisher": "Microsoft.Azure.Diagnostics",
+                  "type": "LinuxDiagnostic",
+                  "typeHandlerVersion": "3.0",
+                  "settings": {
+                    "StorageAccount": "[parameters('storageAccountName')]",
+                    "ladCfg": {
+                      "diagnosticMonitorConfiguration": {
+                        "performanceCounters": {
+                          "sinks": "WADMetricJsonBlob",
+                          "performanceCounterConfiguration": [
+                            {
+                              "unit": "percent",
+                              "type": "builtin",
+                              "class": "memory",
+                              "counter": "percentUsedMemory",
+                              "counterSpecifier": "/builtin/memory/percentUsedMemory",
+                              "condition": "IsAggregate=TRUE"
+                            }
+                          ]
+                        },
+                        "metrics": {
+                          "metricAggregation": [
+                            {
+                              "scheduledTransferPeriod": "PT1M"
+                            }
+                          ],
+                          "resourceId": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]"
+                        }
+                      }
+                    }
+                  },
+                  "protectedSettings": {
+                    "storageAccountName": "[parameters('storageAccountName')]",
+                    "storageAccountSasToken": "[parameters('storageAccountSasToken')]",
+                    "sinksConfig": {
+                      "sink": [
+                        {
+                          "name": "WADMetricJsonBlob",
+                          "type": "JsonBlob"
+                        }
+                      ]
+                    }
+                  }
+                }
+              }
+            ]
           }
         }
       }
```

Por último, iremos adicionar um `autoscaleSettings` recursos para configurar o dimensionamento automático com base nestas métricas. Este recurso tem um `dependsOn` cláusula que faça referência a escala definido para garantir que o conjunto de dimensionamento existe antes de tentar para dimensionar automaticamente. Vamos escolher uma métrica diferentes para dimensionar automaticamente no, seria utilizamos o `counterSpecifier` da configuração de extensão de diagnóstico, como o `metricName` na configuração de dimensionamento automático. Para obter mais informações sobre a configuração de dimensionamento automático, consulte o [melhores práticas de dimensionamento automático](..//monitoring-and-diagnostics/insights-autoscale-best-practices.md) e [documentação de referência da API REST da Azure Monitor](https://msdn.microsoft.com/library/azure/dn931928.aspx).

```diff
+    },
+    {
+      "type": "Microsoft.Insights/autoscaleSettings",
+      "apiVersion": "2015-04-01",
+      "name": "guestMetricsAutoscale",
+      "location": "[resourceGroup().location]",
+      "dependsOn": [
+        "Microsoft.Compute/virtualMachineScaleSets/myScaleSet"
+      ],
+      "properties": {
+        "name": "guestMetricsAutoscale",
+        "targetResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]",
+        "enabled": true,
+        "profiles": [
+          {
+            "name": "Profile1",
+            "capacity": {
+              "minimum": "1",
+              "maximum": "10",
+              "default": "3"
+            },
+            "rules": [
+              {
+                "metricTrigger": {
+                  "metricName": "/builtin/memory/percentUsedMemory",
+                  "metricNamespace": "",
+                  "metricResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]",
+                  "timeGrain": "PT1M",
+                  "statistic": "Average",
+                  "timeWindow": "PT5M",
+                  "timeAggregation": "Average",
+                  "operator": "GreaterThan",
+                  "threshold": 60
+                },
+                "scaleAction": {
+                  "direction": "Increase",
+                  "type": "ChangeCount",
+                  "value": "1",
+                  "cooldown": "PT1M"
+                }
+              },
+              {
+                "metricTrigger": {
+                  "metricName": "/builtin/memory/percentUsedMemory",
+                  "metricNamespace": "",
+                  "metricResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]",
+                  "timeGrain": "PT1M",
+                  "statistic": "Average",
+                  "timeWindow": "PT5M",
+                  "timeAggregation": "Average",
+                  "operator": "LessThan",
+                  "threshold": 30
+                },
+                "scaleAction": {
+                  "direction": "Decrease",
+                  "type": "ChangeCount",
+                  "value": "1",
+                  "cooldown": "PT1M"
+                }
+              }
+            ]
+          }
+        ]
+      }
     }
   ]
 }
```





## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
