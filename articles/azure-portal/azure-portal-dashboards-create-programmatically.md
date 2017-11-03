---
title: Programaticamente Criar Dashboards do Azure | Microsoft Docs
description: Este artigo explica como programaticamente Criar Dashboards do Azure.
services: azure-portal
documentationcenter: 
author: adamab
manager: timlt
editor: tysonn
ms.service: multiple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 09/01/2017
ms.author: adamab
ms.openlocfilehash: 6c0d76207233a04bdec604d95f1779c62f6e2d8f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="programmatically-create-azure-dashboards"></a>Programaticamente Criar Dashboards do Azure

Este documento explica o processo de programaticamente criar e publicar dashboards do Azure. O dashboard mostrado abaixo é referenciado em todo o documento.

![dashboard de exemplo](./media/azure-portal-dashboards-create-programmatically/sample-dashboard.png)

## <a name="overview"></a>Descrição geral

Partilhar dashboards numa tem Azure [recursos](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview) tal como máquinas virtuais e contas de armazenamento.  Por conseguinte, podem ser geridos através de programação através de [REST APIs do Azure Resource Manager](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-manager-rest-api), a [CLI do Azure](https://docs.microsoft.com/en-us/cli/azure/overview), [comandos do PowerShell do Azure](https://docs.microsoft.com/en-us/powershell/azure/get-started-azureps?view=azurermps-4.2.0)e muitas [ Portal do Azure](https://portal.azure.com) funcionalidades criar sobre estas APIs para facilitar a gestão de recursos.  

Cada uma destas APIs e ferramentas proporciona formas de criar, listar, obterem, modificar e eliminar recursos.  Uma vez que os dashboards são recursos, pode escolher a API favorita / ferramenta para utilizar.

Independentemente da ferramenta que utilize, terá de construir uma representação JSON do seu objeto de dashboard antes de poder chamar qualquer API de criação de recursos. Este objeto contém informações sobre as partes (a.k.a. os mosaicos) no dashboard. Inclui os tamanhos, posições, recursos que estão vinculados aos e as personalizações do utilizador.

A forma mais prática para criar cópias de segurança deste documento JSON está a utilizar [portal](https://portal.azure.com/) interativamente adicionar e posicionar os mosaicos. Em seguida, exporte o JSON. Por fim, crie um modelo do resultado para utilização posterior em scripts, programas e ferramentas de implementação.

## <a name="create-a-dashboard"></a>Criar um dashboard

Para criar um novo dashboard, utilize o comando de dashboard novo no ecrã principal do portal.

![comando de novo dashboard](./media/azure-portal-dashboards-create-programmatically/new-dashboard-command.png)

Em seguida, pode utilizar a galeria do mosaico para localizar e adicione peças de mosaicos. Os mosaicos são adicionados ao arrastar e largá-los. Alguns mosaicos suportam o redimensionamento através de um identificador de arrastar, enquanto outros suporte corrige tamanhos que podem ser vistos no respetivo menu de contexto.

### <a name="drag-handle"></a>Identificador de arrastar
![Arraste o identificador](./media/azure-portal-dashboards-create-programmatically/drag-handle.png)

### <a name="fixed-sizes-via-context-menu"></a>Tamanhos fixos através do menu de contexto
![menu de contexto de tamanhos](./media/azure-portal-dashboards-create-programmatically/sizes-context-menu.png)

## <a name="share-the-dashboard"></a>Partilhe o dashboard

Depois de ter configurado o dashboard para sua liking que são os passos para publicar o dashboard (utilizando o comando partilha) e, em seguida, utilize o Explorador de recursos para obter o JSON.

![comando de partilha](./media/azure-portal-dashboards-create-programmatically/share-command.png)

Ao clicar no comando partilha mostra uma caixa de diálogo que pede-lhe para escolher o grupo de recursos e subscrição para publicar. Tenha em atenção que [tem de ter acesso de escrita](https://docs.microsoft.com/en-us/azure/active-directory/role-based-access-control-configure) para o grupo de recursos e subscrição que escolher.

![acesso e partilha](./media/azure-portal-dashboards-create-programmatically/sharing-and-access.png)

## <a name="fetch-the-json-representation-of-the-dashboard"></a>Obter a representação JSON do dashboard

Publicação demora apenas alguns segundos.  Quando terminar, o passo seguinte é ir para o [Explorador de recursos](https://portal.azure.com/#blade/HubsExtension/ArmExplorerBlade) ao obter o JSON.

![procurar o Explorador de recursos](./media/azure-portal-dashboards-create-programmatically/browse-resource-explorer.png)

O Explorador de recursos, navegue para o grupo de recursos e subscrição que escolheu. Em seguida, clique no recurso de dashboard publicado recentemente para revelar JSON.

![json do Explorador de recursos](./media/azure-portal-dashboards-create-programmatically/resource-explorer-json.png)

## <a name="create-a-template-from-the-json"></a>Criar um modelo a partir de JSON

O passo seguinte é criar um modelo este JSON para que os pode ser reutilizado programaticamente com a gestão de recursos adequados APIs, ferramentas de linha de comandos, ou no portal.

Não é necessário compreender a estrutura JSON do dashboard para criar um modelo. Na maioria dos casos, que pretende manter a estrutura e a configuração de cada mosaico e, em seguida, parametrizar o conjunto de recursos do Azure que está a apontar para. Observe o dashboard JSON exportado e localizar todas as ocorrências de Ids de recurso do Azure. A nossa dashboard de exemplo tem mosaicos vários que todos os apontarem para uma única máquina virtual do Azure. Isto acontece porque o nosso dashboard apenas analisa esta único recurso. Se procurar o json de exemplo (incluído no final do documento) "/ subscrições", encontrará várias ocorrências deste ID.

`/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1`

Para publicar este dashboard para qualquer máquina virtual no futuro terá parametrizar cada ocorrência desta cadeia dentro de JSON. 

Existem dois tipos de APIs que criar recursos no Azure. [APIs imperativo](https://docs.microsoft.com/en-us/rest/api/resources/resources) que crie um recurso num momento e um [baseado no modelo de implementação](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-template-deploy) sistema que pode orquestrar a criação de vários recursos dependentes com uma única chamada de API. A última opção suporta nativamente parametrização forced e templating forma podemos utilizá-los para o nosso exemplo.

## <a name="programmatically-create-a-dashboard-from-your-template-using-a-template-deployment"></a>Programaticamente criar um dashboard a partir do seu modelo com uma implementação de modelo

O Azure oferece a capacidade para orquestrar a implementação de vários recursos. Criar um modelo de implementação que expresse precisa o conjunto de recursos para implementar, bem como as relações entre eles.  O formato JSON de cada recurso é igual à foram criá-los um por um. A diferença é que o [idioma modelo](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authoring-templates) adiciona alguns conceitos como variáveis, parâmetros, funções básicas e muito mais. Isto expandido sintaxe só é suportado no contexto de uma implementação de modelos e não funciona se for utilizado com as APIs imperativo debatidas anteriormente.

Se estiver a ficar esta rota, em seguida, parametrização forced deve ser feito utilizando a sintaxe do parâmetro do modelo.  Substitua todas as instâncias do id do recurso que encontrámos anterior, conforme mostrado aqui.

### <a name="example-json-property-with-hard-coded-resource-id"></a>Propriedade JSON de exemplo com o Id do recurso hard-coded
`id: “/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1”`

### <a name="example-json-property-converted-to-a-parameterized-version-based-on-template-parameters"></a>Propriedade JSON de exemplo convertida para uma versão parametrizada com base nos parâmetros de modelo

`id: "[resourceId(parameters('virtualMachineResourceGroup'), ‘Microsoft.Compute/virtualMachines’, parameters('virtualMachineName'))]"`

Também tem de declarar alguns metadados do modelo necessário e os parâmetros na parte superior do modelo json como esta:

```json

{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualMachineName": {
            "type": "string"
        },
        "virtualMachineResourceGroup": {
            "type": "string"
        },
        "dashboardName": {
            "type": "string"
        }
    },
    "variables": {},

    ... rest of template omitted ...
```

__Pode ver o modelo de trabalho completo, no final deste documento.__

Depois de ter crafted o seu modelo pode implementá-la utilizando o [REST APIs](https://docs.microsoft.com/en-us/rest/api/resources/deployments), [PowerShell](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-template-deploy), a [CLI do Azure](https://docs.microsoft.com/en-us/cli/azure/group/deployment#az_group_deployment_create), ou o [página de implementação do modelo do portal ](https://portal.azure.com/#create/Microsoft.Template).

Apresentamos a seguir são duas versões do nosso JSON do dashboard de exemplo. A primeira é a versão que foi exportados a partir do portal do que já foi vinculado a um recurso. O segundo é a versão de modelo pode estar vinculada através de programação para qualquer VM e implementadas utilizando o Azure Resource Manager.

## <a name="json-representation-of-our-example-dashboard-before-templating"></a>Representação JSON do nosso dashboard de exemplo (antes de templating)

Este é o que pode esperar ver se siga as instruções anteriores para obter a representação JSON de um dashboard que já está implementada. Tenha em atenção os identificadores de recursos hard-coded que mostram este dashboard está a apontar, uma máquina virtual do Azure específica.

```json

{
    "properties": {
        "lenses": {
            "0": {
                "order": 0,
                "parts": {
                    "0": {
                        "position": {
                            "x": 0,
                            "y": 0,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "content": "## Azure Virtual Machines Overview\r\nNew team members should watch this video to get familiar with Azure Virtual Machines.",
                                        "title": "",
                                        "subtitle": ""
                                    }
                                }
                            }
                        }
                    },
                    "1": {
                        "position": {
                            "x": 3,
                            "y": 0,
                            "rowSpan": 4,
                            "colSpan": 8
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "content": "This is the team dashboard for the test VM we use on our team. Here are some useful links:\r\n\r\n1. [Getting started](https://www.contoso.com/tsgs)\r\n1. [Troubleshooting guide](https://www.contoso.com/tsgs)\r\n1. [Architecture docs](https://www.contoso.com/tsgs)",
                                        "title": "Test VM Dashboard",
                                        "subtitle": "Contoso"
                                    }
                                }
                            }
                        }
                    },
                    "2": {
                        "position": {
                            "x": 0,
                            "y": 2,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/VideoPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "title": "",
                                        "subtitle": "",
                                        "src": "https://www.youtube.com/watch?v=YcylDIiKaSU&list=PLLasX02E8BPCsnETz0XAMfpLR1LIBqpgs&index=4",
                                        "autoplay": false
                                    }
                                }
                            }
                        }
                    },
                    "3": {
                        "position": {
                            "x": 0,
                            "y": 4,
                            "rowSpan": 3,
                            "colSpan": 11
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Percentage CPU",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "4": {
                        "position": {
                            "x": 0,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Disk Read Operations/Sec",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Disk Write Operations/Sec",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "5": {
                        "position": {
                            "x": 3,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Disk Read Bytes",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Disk Write Bytes",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "6": {
                        "position": {
                            "x": 6,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Network In",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Network Out",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "7": {
                        "position": {
                            "x": 9,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 2
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "id",
                                    "value": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Compute/PartType/VirtualMachinePart",
                            "asset": {
                                "idInputName": "id",
                                "type": "VirtualMachine"
                            },
                            "defaultMenuItemId": "overview"
                        }
                    }
                }
            }
        },
        "metadata": { }
    },
    "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/dashboards/providers/Microsoft.Portal/dashboards/aa9786ae-e159-483f-b05f-1f7f767741a9",
    "name": "aa9786ae-e159-483f-b05f-1f7f767741a9",
    "type": "Microsoft.Portal/dashboards",
    "location": "eastasia",
    "tags": {
        "hidden-title": "Created via API"
    }
}

```

### <a name="template-representation-of-our-example-dashboard"></a>Representação de modelo do nosso dashboard de exemplo

A versão do modelo do dashboard definiu três parâmetros denominados __virtualMachineName__, __virtualMachineResourceGroup__, e __dashboardName__.  Os parâmetros permitem-lhe apontar este dashboard para outra máquina virtual do Azure, sempre que implementar. Os ids de parametrizada são realçados para mostrar que este dashboard pode ser programaticamente configurado e implementado para apontar para qualquer máquina virtual do Azure. A forma mais fácil para testar esta funcionalidade é para o modelo seguinte de copiar e colar no [página do portal do Azure modelo de implementação](https://portal.azure.com/#create/Microsoft.Template). 

Este exemplo implementa um dashboard por si só, mas a linguagem de modelo permite-lhe implementar vários recursos e um ou mais dashboards ao longo do lado do pacote-los. 

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualMachineName": {
            "type": "string"
        },
        "virtualMachineResourceGroup": {
            "type": "string"
        },
        "dashboardName": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "properties": {
                "lenses": {
                    "0": {
                        "order": 0,
                        "parts": {
                            "0": {
                                "position": {
                                    "x": 0,
                                    "y": 0,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [],
                                    "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                                    "settings": {
                                        "content": {
                                            "settings": {
                                                "content": "## Azure Virtual Machines Overview\r\nNew team members should watch this video to get familiar with Azure Virtual Machines.",
                                                "title": "",
                                                "subtitle": ""
                                            }
                                        }
                                    }
                                }
                            },
                            "1": {
                                "position": {
                                    "x": 3,
                                    "y": 0,
                                    "rowSpan": 4,
                                    "colSpan": 8
                                },
                                "metadata": {
                                    "inputs": [],
                                    "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                                    "settings": {
                                        "content": {
                                            "settings": {
                                                "content": "This is the team dashboard for the test VM we use on our team. Here are some useful links:\r\n\r\n1. [Getting started](https://www.contoso.com/tsgs)\r\n1. [Troubleshooting guide](https://www.contoso.com/tsgs)\r\n1. [Architecture docs](https://www.contoso.com/tsgs)",
                                                "title": "Test VM Dashboard",
                                                "subtitle": "Contoso"
                                            }
                                        }
                                    }
                                }
                            },
                            "2": {
                                "position": {
                                    "x": 0,
                                    "y": 2,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [],
                                    "type": "Extension[azure]/HubsExtension/PartType/VideoPart",
                                    "settings": {
                                        "content": {
                                            "settings": {
                                                "title": "",
                                                "subtitle": "",
                                                "src": "https://www.youtube.com/watch?v=YcylDIiKaSU&list=PLLasX02E8BPCsnETz0XAMfpLR1LIBqpgs&index=4",
                                                "autoplay": false
                                            }
                                        }
                                    }
                                }
                            },
                            "3": {
                                "position": {
                                    "x": 0,
                                    "y": 4,
                                    "rowSpan": 3,
                                    "colSpan": 11
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "queryInputs",
                                            "value": {
                                                "timespan": {
                                                    "duration": "PT1H",
                                                    "start": null,
                                                    "end": null
                                                },
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Percentage CPU",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    }
                                                ]
                                            }
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                                    "settings": {}
                                }
                            },
                            "4": {
                                "position": {
                                    "x": 0,
                                    "y": 7,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "queryInputs",
                                            "value": {
                                                "timespan": {
                                                    "duration": "PT1H",
                                                    "start": null,
                                                    "end": null
                                                },
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Disk Read Operations/Sec",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    },
                                                    {
                                                        "name": "Disk Write Operations/Sec",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    }
                                                ]
                                            }
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                                    "settings": {}
                                }
                            },
                            "5": {
                                "position": {
                                    "x": 3,
                                    "y": 7,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "queryInputs",
                                            "value": {
                                                "timespan": {
                                                    "duration": "PT1H",
                                                    "start": null,
                                                    "end": null
                                                },
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Disk Read Bytes",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    },
                                                    {
                                                        "name": "Disk Write Bytes",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    }
                                                ]
                                            }
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                                    "settings": {}
                                }
                            },
                            "6": {
                                "position": {
                                    "x": 6,
                                    "y": 7,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "queryInputs",
                                            "value": {
                                                "timespan": {
                                                    "duration": "PT1H",
                                                    "start": null,
                                                    "end": null
                                                },
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Network In",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    },
                                                    {
                                                        "name": "Network Out",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    }
                                                ]
                                            }
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                                    "settings": {}
                                }
                            },
                            "7": {
                                "position": {
                                    "x": 9,
                                    "y": 7,
                                    "rowSpan": 2,
                                    "colSpan": 2
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "id",
                                            "value": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Compute/PartType/VirtualMachinePart",
                                    "asset": {
                                        "idInputName": "id",
                                        "type": "VirtualMachine"
                                    },
                                    "defaultMenuItemId": "overview"
                                }
                            }
                        }
                    }
                }
            },
            "metadata": { },
            "apiVersion": "2015-08-01-preview",
            "type": "Microsoft.Portal/dashboards",
            "name": "[parameters('dashboardName')]",
            "location": "westus",
            "tags": {
                "hidden-title": "[parameters('dashboardName')]"
            }
        }
    ]
}


```