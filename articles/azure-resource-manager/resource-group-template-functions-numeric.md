---
title: "Funções do Azure Resource Manager modelo - numérico | Microsoft Docs"
description: "Descreve as funções para utilizar num modelo Azure Resource Manager para funcionar com números."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/08/2017
ms.author: tomfitz
ms.openlocfilehash: 2b7ec44b820e510d1e8bd99ef195546a519c365c
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2017
---
# <a name="numeric-functions-for-azure-resource-manager-templates"></a>Um valor numérico funções para modelos Azure Resource Manager

O Resource Manager fornece as seguintes funções para trabalhar com números inteiros:

* [Adicionar](#add)
* [copyIndex](#copyindex)
* [Div](#div)
* [número de vírgula flutuante](#float)
* [Int](#int)
* [Máx.](#max)
* [Mín.](#min)
* [MOD](#mod)
* [MUL](#mul)
* [sub](#sub)

<a id="add" />

## <a name="add"></a>Adicionar
`add(operand1, operand2)`

Devolve a soma de dois números de inteiros fornecidos.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- | 
|operand1 |Sim |Int |Primeiro número para adicionar. |
|operand2 |Sim |Int |Segundo número a adicionar. |

### <a name="return-value"></a>Valor devolvido

Um número inteiro que contém a soma dos parâmetros.

### <a name="example"></a>Exemplo

O seguinte [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/add.json) adiciona dois parâmetros.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "defaultValue": 5,
            "metadata": {
                "description": "First integer to add"
            }
        },
        "second": {
            "type": "int",
            "defaultValue": 3,
            "metadata": {
                "description": "Second integer to add"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "addResult": {
            "type": "int",
            "value": "[add(parameters('first'), parameters('second'))]"
        }
    }
}
```

O resultado do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| addResult | Int | 8 |

Para implementar este modelo de exemplo com a CLI do Azure, utilize:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/add.json
```

Para implementar este modelo de exemplo com o PowerShell, utilize:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/add.json 
```

<a id="copyindex" />

## <a name="copyindex"></a>copyIndex
`copyIndex(loopName, offset)`

Devolve o índice de um ciclo de iteração. 

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| loopName | Não | Cadeia | O nome do ciclo para obter a iteração. |
| deslocamento |Não |Int |O número a adicionar ao valor iteração baseado em zero. |

### <a name="remarks"></a>Observações

Esta função é sempre utilizada com um **cópia** objeto. Se não for fornecido nenhum valor para **desvio**, é devolvido o valor atual de iteração. O valor de iteração inicia-se em zero. Pode utilizar os ciclos de iteração quando definir os recursos ou variáveis.

O **loopName** propriedade permite-lhe especificar se copyIndex faz referência a um iteração do recurso ou iteração de propriedade. Se não for fornecido nenhum valor para **loopName**, a atual iteração de tipo de recurso é utilizada. Forneça um valor para **loopName** quando iterating numa propriedade. 
 
Para obter uma descrição completa de como utilizar **copyIndex**, consulte [criar várias instâncias de recursos no Azure Resource Manager](resource-group-create-multiple.md).

Para obter um exemplo de utilização **copyIndex** ao definir uma variável, consulte [variáveis](resource-group-authoring-templates.md#variables).

### <a name="example"></a>Exemplo

O exemplo seguinte mostra um ciclo de cópia e o valor de índice incluído no nome. 

```json
"resources": [ 
  { 
    "name": "[concat('examplecopy-', copyIndex())]", 
    "type": "Microsoft.Web/sites", 
    "copy": { 
      "name": "websitescopy", 
      "count": "[parameters('count')]" 
    }, 
    ...
  }
]
```

### <a name="return-value"></a>Valor devolvido

Um número inteiro que representa o índice atual da iteração.

<a id="div" />

## <a name="div"></a>Div
`div(operand1, operand2)`

Devolve a divisão de número inteiro de dois números inteiros fornecidos.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| operand1 |Sim |Int |O número ser dividido. |
| operand2 |Sim |Int |O número que é utilizado para dividir. Não pode ser 0. |

### <a name="return-value"></a>Valor devolvido

Um número inteiro que representa a divisão.

### <a name="example"></a>Exemplo

O seguinte [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/div.json) divide um parâmetro ao outro parâmetro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "defaultValue": 8,
            "metadata": {
                "description": "Integer being divided"
            }
        },
        "second": {
            "type": "int",
            "defaultValue": 3,
            "metadata": {
                "description": "Integer used to divide"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "divResult": {
            "type": "int",
            "value": "[div(parameters('first'), parameters('second'))]"
        }
    }
}
```

O resultado do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| divResult | Int | 2 |

Para implementar este modelo de exemplo com a CLI do Azure, utilize:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/div.json
```

Para implementar este modelo de exemplo com o PowerShell, utilize:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/div.json 
```

<a id="float" />

## <a name="float"></a>Número de vírgula flutuante
`float(arg1)`

Converte o valor de vírgula flutuante um número. Utilizar apenas esta função ao transmitir parâmetros personalizados para uma aplicação, tal como uma aplicação lógica.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |cadeia ou int |O valor para converter uma vírgula flutuante número. |

### <a name="return-value"></a>Valor devolvido
Uma vírgula flutuante ponto número.

### <a name="example"></a>Exemplo

O exemplo seguinte mostra como utilizar o número de vírgula flutuante para passar os parâmetros para uma aplicação lógica:

```json
{
    "type": "Microsoft.Logic/workflows",
    "properties": {
        ...
        "parameters": {
            "custom1": {
                "value": "[float('3.0')]"
            },
            "custom2": {
                "value": "[float(3)]"
            },
```

<a id="int" />

## <a name="int"></a>Int
`int(valueToConvert)`

Converte o valor especificado para um número inteiro.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| valueToConvert |Sim |cadeia ou int |O valor para converter para um número inteiro. |

### <a name="return-value"></a>Valor devolvido

Um número inteiro do valor convertido.

### <a name="example"></a>Exemplo

O seguinte [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/int.json) converte o valor do parâmetro fornecidos pelo utilizador para um número inteiro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringToConvert": { 
            "type": "string",
            "defaultValue": "4"
        }
    },
    "resources": [
    ],
    "outputs": {
        "intResult": {
            "type": "int",
            "value": "[int(parameters('stringToConvert'))]"
        }
    }
}
```

O resultado do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| intResult | Int | 4 |

Para implementar este modelo de exemplo com a CLI do Azure, utilize:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/int.json
```

Para implementar este modelo de exemplo com o PowerShell, utilize:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/int.json
```

<a id="max" />

## <a name="max"></a>Máx.
`max (arg1)`

Devolve o valor máximo de uma matriz de números inteiros ou uma lista separada por vírgulas de números inteiros.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |matriz de números inteiros ou lista de números inteiros separados por vírgulas |A coleção para obter o valor máximo. |

### <a name="return-value"></a>Valor devolvido

Um número inteiro que representa o valor máximo da coleção.

### <a name="example"></a>Exemplo

O seguinte [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/max.json) mostra como utilizar máx com uma matriz e uma lista de números inteiros:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": [0,3,2,5,4]
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "int",
            "value": "[max(parameters('arrayToTest'))]"
        },
        "intOutput": {
            "type": "int",
            "value": "[max(0,3,2,5,4)]"
        }
    }
}
```

O resultado do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| arrayOutput | Int | 5 |
| intOutput | Int | 5 |

Para implementar este modelo de exemplo com a CLI do Azure, utilize:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/max.json
```

Para implementar este modelo de exemplo com o PowerShell, utilize:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/max.json
```

<a id="min" />

## <a name="min"></a>min.
`min (arg1)`

Devolve o valor mínimo de uma matriz de números inteiros ou uma lista separada por vírgulas de números inteiros.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |matriz de números inteiros ou lista de números inteiros separados por vírgulas |A coleção para obter o valor mínimo. |

### <a name="return-value"></a>Valor devolvido

Um número inteiro que representa o valor mínimo da coleção.

### <a name="example"></a>Exemplo

O seguinte [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/min.json) mostra como utilizar min com uma matriz e uma lista de números inteiros:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": [0,3,2,5,4]
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "int",
            "value": "[min(parameters('arrayToTest'))]"
        },
        "intOutput": {
            "type": "int",
            "value": "[min(0,3,2,5,4)]"
        }
    }
}
```

O resultado do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| arrayOutput | Int | 0 |
| intOutput | Int | 0 |

Para implementar este modelo de exemplo com a CLI do Azure, utilize:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/min.json
```

Para implementar este modelo de exemplo com o PowerShell, utilize:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/min.json
```

<a id="mod" />

## <a name="mod"></a>MOD
`mod(operand1, operand2)`

Devolve o resto da divisão de número inteiro através de dois números inteiros fornecidos.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| operand1 |Sim |Int |O número ser dividido. |
| operand2 |Sim |Int |O número que é utilizado para dividir, não pode ser 0. |

### <a name="return-value"></a>Valor devolvido
Um número inteiro que representa o resto.

### <a name="example"></a>Exemplo

O seguinte [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/mod.json) devolve o resto da divisão de um parâmetro ao outro parâmetro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "defaultValue": 7,
            "metadata": {
                "description": "Integer being divided"
            }
        },
        "second": {
            "type": "int",
            "defaultValue": 3,
            "metadata": {
                "description": "Integer used to divide"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "modResult": {
            "type": "int",
            "value": "[mod(parameters('first'), parameters('second'))]"
        }
    }
}
```

O resultado do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| modResult | Int | 1 |

Para implementar este modelo de exemplo com a CLI do Azure, utilize:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/mod.json
```

Para implementar este modelo de exemplo com o PowerShell, utilize:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/mod.json
```

<a id="mul" />

## <a name="mul"></a>MUL
`mul(operand1, operand2)`

Devolve a multiplicação de dois números inteiros fornecidos.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| operand1 |Sim |Int |Primeiro número multiplicar. |
| operand2 |Sim |Int |Segundo número a multiplicar. |

### <a name="return-value"></a>Valor devolvido

Um número inteiro que representa a multiplicação.

### <a name="example"></a>Exemplo

O seguinte [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/mul.json) Multiplica um parâmetro ao outro parâmetro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "defaultValue": 5,
            "metadata": {
                "description": "First integer to multiply"
            }
        },
        "second": {
            "type": "int",
            "defaultValue": 3,
            "metadata": {
                "description": "Second integer to multiply"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "mulResult": {
            "type": "int",
            "value": "[mul(parameters('first'), parameters('second'))]"
        }
    }
}
```

O resultado do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| mulResult | Int | 15 |

Para implementar este modelo de exemplo com a CLI do Azure, utilize:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/mul.json
```

Para implementar este modelo de exemplo com o PowerShell, utilize:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/mul.json
```

<a id="sub" />

## <a name="sub"></a>sub
`sub(operand1, operand2)`

Devolve a subtração de dois números inteiros fornecidos.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| operand1 |Sim |Int |O número que é subtraído da. |
| operand2 |Sim |Int |O número que é subtraído. |

### <a name="return-value"></a>Valor devolvido
Um número inteiro que representa a subtração.

### <a name="example"></a>Exemplo

O seguinte [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/sub.json) subtrai um parâmetro de outro parâmetro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "defaultValue": 7,
            "metadata": {
                "description": "Integer subtracted from"
            }
        },
        "second": {
            "type": "int",
            "defaultValue": 3,
            "metadata": {
                "description": "Integer to subtract"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "subResult": {
            "type": "int",
            "value": "[sub(parameters('first'), parameters('second'))]"
        }
    }
}
```

O resultado do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| subResult | Int | 4 |

Para implementar este modelo de exemplo com a CLI do Azure, utilize:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/sub.json
```

Para implementar este modelo de exemplo com o PowerShell, utilize:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/sub.json
```

## <a name="next-steps"></a>Passos seguintes
* Para obter uma descrição das secções num modelo Azure Resource Manager, consulte [modelos Authoring Azure Resource Manager](resource-group-authoring-templates.md).
* Intercalar vários modelos, consulte [utilizar modelos ligados com o Azure Resource Manager](resource-group-linked-templates.md).
* Para iterar um número de vezes especificado ao criar um tipo de recurso, consulte [criar várias instâncias de recursos no Azure Resource Manager](resource-group-create-multiple.md).
* Para ver como implementar o modelo que criou, consulte [implementar uma aplicação com o modelo Azure Resource Manager](resource-group-template-deploy.md).

