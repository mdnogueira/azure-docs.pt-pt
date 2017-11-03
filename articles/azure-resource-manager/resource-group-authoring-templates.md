---
title: Estrutura de modelo do Azure Resource Manager e sintaxe | Microsoft Docs
description: Descreve a estrutura e propriedades de modelos Azure Resource Manager utilizando a sintaxe declarativa de JSON.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 19694cb4-d9ed-499a-a2cc-bcfc4922d7f5
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/14/2017
ms.author: tomfitz
ms.openlocfilehash: dc9b64062d7f68c83aa090eec96744819a5ca423
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="understand-the-structure-and-syntax-of-azure-resource-manager-templates"></a>Compreender a estrutura e a sintaxe de modelos Azure Resource Manager
Este tópico descreve a estrutura de um modelo Azure Resource Manager. Apresente as diferentes secções de um modelo e as propriedades que estão disponíveis dessas secções. O modelo é constituído por JSON e expressões que pode utilizar para construir valores para a sua implementação. Para um tutorial passo a passo sobre como criar um modelo, consulte [criar o primeiro modelo Azure Resource Manager](resource-manager-create-first-template.md).

## <a name="template-format"></a>Formato de modelo
Na sua estrutura mais simples, um modelo contém os seguintes elementos:

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "",
    "parameters": {  },
    "variables": {  },
    "resources": [  ],
    "outputs": {  }
}
```

| Nome do elemento | Necessário | Descrição |
|:--- |:--- |:--- |
| $schema |Sim |Localização do ficheiro de esquema JSON que descreve a versão do idioma do modelo. Utilize o URL apresentado no exemplo anterior. |
| contentVersion |Sim |Versão do modelo (por exemplo, 1.0.0.0). Pode fornecer qualquer valor para este elemento. Quando implementar recursos com o modelo, este valor pode ser utilizado para se certificar de que está a ser utilizado o modelo à direita. |
| parâmetros |Não |Valores que são fornecidos durante a implementação é executada para personalizar a implementação de recursos. |
| variáveis |Não |Valores que são utilizados como fragmentos JSON no modelo para simplificar as expressões de idioma do modelo. |
| Recursos |Sim |Tipos de recursos que são implementados ou atualizados num grupo de recursos. |
| saídas |Não |Valores que são devolvidos após a implementação. |

Cada elemento contém propriedades, que pode definir. O exemplo seguinte contém a sintaxe completa para um modelo:

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "",
    "parameters": {  
        "<parameter-name>" : {
            "type" : "<type-of-parameter-value>",
            "defaultValue": "<default-value-of-parameter>",
            "allowedValues": [ "<array-of-allowed-values>" ],
            "minValue": <minimum-value-for-int>,
            "maxValue": <maximum-value-for-int>,
            "minLength": <minimum-length-for-string-or-array>,
            "maxLength": <maximum-length-for-string-or-array-parameters>,
            "metadata": {
                "description": "<description-of-the parameter>" 
            }
        }
    },
    "variables": {  
        "<variable-name>": "<variable-value>",
        "<variable-name>": { 
            <variable-complex-type-value> 
        }
    },
    "resources": [
      {
          "condition": "<boolean-value-whether-to-deploy>",
          "apiVersion": "<api-version-of-resource>",
          "type": "<resource-provider-namespace/resource-type-name>",
          "name": "<name-of-the-resource>",
          "location": "<location-of-resource>",
          "tags": {
              "<tag-name1>": "<tag-value1>",
              "<tag-name2>": "<tag-value2>"
          },
          "comments": "<your-reference-notes>",
          "copy": {
              "name": "<name-of-copy-loop>",
              "count": "<number-of-iterations>",
              "mode": "<serial-or-parallel>",
              "batchSize": "<number-to-deploy-serially>"
          },
          "dependsOn": [
              "<array-of-related-resource-names>"
          ],
          "properties": {
              "<settings-for-the-resource>",
              "copy": [
                  {
                      "name": ,
                      "count": ,
                      "input": {}
                  }
              ]
          },
          "resources": [
              "<array-of-child-resources>"
          ]
      }
    ],
    "outputs": {
        "<outputName>" : {
            "type" : "<type-of-output-value>",
            "value": "<output-value-expression>"
        }
    }
}
```

Vamos examinar as secções do modelo em maior detalhe posteriormente neste tópico.

## <a name="expressions-and-functions"></a>As expressões e funções
A sintaxe básica do modelo é JSON. No entanto, expressões e as funções expandem os valores JSON disponíveis no modelo.  As expressões são escritas no literais de cadeia JSON cujo primeiro e último carateres são Retos: `[` e `]`, respetivamente. O valor da expressão é avaliado quando o modelo é implementado. Enquanto escritos como uma cadeia literal, o resultado da avaliação da expressão pode ser um tipo JSON diferente, tal como uma matriz nem um número inteiro, consoante a expressão real.  Para ter um literal de cadeia começa com um parêntese `[`, mas não o tiver interpretado como uma expressão, adicione um parêntesis adicional para iniciar a cadeia com `[[`.

Normalmente, utilizar expressões com as funções para executar operações para configurar a implementação. Apenas como em JavaScript, chamadas de função estejam formatadas como `functionName(arg1,arg2,arg3)`. Referenciar propriedades utilizando os operadores de ponto e [Índice].

O exemplo seguinte mostra como utilizar várias funções quando construir valores:

```json
"variables": {
    "location": "[resourceGroup().location]",
    "usernameAndPassword": "[concat(parameters('username'), ':', parameters('password'))]",
    "authorizationHeader": "[concat('Basic ', base64(variables('usernameAndPassword')))]"
}
```

Para obter a lista completa de funções de modelo, consulte [funções de modelo do Azure Resource Manager](resource-group-template-functions.md). 

## <a name="parameters"></a>Parâmetros
Na secção de parâmetros do modelo, especifique os valores que pode introduzir quando implementar os recursos. Estes valores de parâmetros permitem-lhe personalizar a implementação, fornecendo valores que são adaptados para um ambiente específico (por exemplo, o desenvolvimento, teste e produção). Não é necessário fornecer os parâmetros no modelo, mas sem parâmetros do modelo implementaria sempre os mesmos recursos com os mesmos nomes, localizações e as propriedades.

Definir os parâmetros com a estrutura seguinte:

```json
"parameters": {
    "<parameter-name>" : {
        "type" : "<type-of-parameter-value>",
        "defaultValue": "<default-value-of-parameter>",
        "allowedValues": [ "<array-of-allowed-values>" ],
        "minValue": <minimum-value-for-int>,
        "maxValue": <maximum-value-for-int>,
        "minLength": <minimum-length-for-string-or-array>,
        "maxLength": <maximum-length-for-string-or-array-parameters>,
        "metadata": {
            "description": "<description-of-the parameter>" 
        }
    }
}
```

| Nome do elemento | Necessário | Descrição |
|:--- |:--- |:--- |
| parameterName |Sim |Nome do parâmetro. Tem de ser um identificador de JavaScript válido. |
| tipo |Sim |Tipo do valor de parâmetro. Ver a lista de tipos permitidos após esta tabela. |
| DefaultValue |Não |Valor predefinido para o parâmetro se não for fornecido nenhum valor para o parâmetro. |
| allowedValues |Não |Matriz de valores permitidos para o parâmetro para se certificar de que é fornecido o valor correto. |
| MinValue |Não |O valor mínimo de parâmetros de tipo int, este valor é inclusive. |
| MaxValue |Não |O valor máximo de parâmetros de tipo int, este valor é inclusive. |
| minLength |Não |O comprimento mínimo de cadeia, secureString e parâmetros de tipo de matriz, este valor é inclusive. |
| maxLength |Não |O comprimento máximo da cadeia, secureString e parâmetros de tipo de matriz, este valor é inclusive. |
| descrição |Não |Descrição do parâmetro que é apresentada aos utilizadores através do portal. |

Os valores e os tipos permitidos são:

* **cadeia**
* **secureString**
* **Int**
* **bool**
* **objeto** 
* **secureObject**
* **matriz**

Para especificar um parâmetro opcional como, forneça um defaultValue (pode ser uma cadeia vazia). 

Se especificar um nome de parâmetro no modelo que corresponda a um parâmetro de comando para implementar o modelo, não há ambiguidade potencial sobre os valores que fornecer. Gestor de recursos resolve este confusão ao adicionar o sufixo **FromTemplate** para o parâmetro de modelo. Por exemplo, se incluir um parâmetro com o nome **ResourceGroupName** no seu modelo, está em conflito com o **ResourceGroupName** parâmetro o [New-AzureRmResourceGroupDeployment ](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment) cmdlet. Durante a implementação, lhe for pedido para fornecer um valor para **ResourceGroupNameFromTemplate**. Em geral, deve evitar este confusão ao nomear não parâmetros com o mesmo nome de parâmetros utilizada para operações de implementação.

> [!NOTE]
> Todas as palavras-passe, chaves e outros segredos devem utilizar o **secureString** tipo. Se passar dados confidenciais num objeto JSON, utilize o **secureObject** tipo. Não não possível ler os parâmetros do modelo tipos secureString ou secureObject após a implementação de recursos. 
> 
> Por exemplo, a seguinte entrada do histórico de implementação mostra o valor para uma cadeia e objeto, mas não para secureString e secureObject.
>
> ![Mostrar valores de implementação](./media/resource-group-authoring-templates/show-parameters.png)  
>

O exemplo seguinte mostra como definir parâmetros:

```json
"parameters": {
    "siteName": {
        "type": "string",
        "defaultValue": "[concat('site', uniqueString(resourceGroup().id))]"
    },
    "hostingPlanName": {
        "type": "string",
        "defaultValue": "[concat(parameters('siteName'),'-plan')]"
    },
    "skuName": {
        "type": "string",
        "defaultValue": "F1",
        "allowedValues": [
          "F1",
          "D1",
          "B1",
          "B2",
          "B3",
          "S1",
          "S2",
          "S3",
          "P1",
          "P2",
          "P3",
          "P4"
        ]
    },
    "skuCapacity": {
        "type": "int",
        "defaultValue": 1,
        "minValue": 1
    }
}
```

Para saber como os valores de parâmetros de entrada durante a implementação, consulte [implementar uma aplicação com o modelo Azure Resource Manager](resource-group-template-deploy.md). 

## <a name="variables"></a>Variáveis
Na secção de variáveis, construir valores que podem ser utilizados em todo o seu modelo. Não é necessário definir as variáveis, mas são, muitas vezes, simplificam o modelo ao reduzir as expressões complexas.

Definir variáveis com a estrutura seguinte:

```json
"variables": {
    "<variable-name>": "<variable-value>",
    "<variable-name>": { 
        <variable-complex-type-value> 
    }
}
```

O exemplo seguinte mostra como definir uma variável que foi construída a partir de dois valores de parâmetro:

```json
"variables": {
    "connectionString": "[concat('Name=', parameters('username'), ';Password=', parameters('password'))]"
}
```

O exemplo seguinte mostra uma variável que é um tipo JSON complexo e as variáveis construídas a partir de outras variáveis:

```json
"parameters": {
    "environmentName": {
        "type": "string",
        "allowedValues": [
          "test",
          "prod"
        ]
    }
},
"variables": {
    "environmentSettings": {
        "test": {
            "instancesSize": "Small",
            "instancesCount": 1
        },
        "prod": {
            "instancesSize": "Large",
            "instancesCount": 4
        }
    },
    "currentEnvironmentSettings": "[variables('environmentSettings')[parameters('environmentName')]]",
    "instancesSize": "[variables('currentEnvironmentSettings').instancesSize]",
    "instancesCount": "[variables('currentEnvironmentSettings').instancesCount]"
}
```

## <a name="resources"></a>Recursos
Na secção de recursos, é possível definir os recursos que são implementados ou atualizados. Nesta secção pode obter complicada porque tem de compreender os tipos que está a implementar para fornecer os valores corretos. Para os recurso valores específicos (apiVersion, tipo e propriedades) que tem de definir, consulte [definir recursos em modelos do Azure Resource Manager](/azure/templates/). 

Definir recursos com a estrutura seguinte:

```json
"resources": [
  {
      "condition": "<boolean-value-whether-to-deploy>",
      "apiVersion": "<api-version-of-resource>",
      "type": "<resource-provider-namespace/resource-type-name>",
      "name": "<name-of-the-resource>",
      "location": "<location-of-resource>",
      "tags": {
          "<tag-name1>": "<tag-value1>",
          "<tag-name2>": "<tag-value2>"
      },
      "comments": "<your-reference-notes>",
      "copy": {
          "name": "<name-of-copy-loop>",
          "count": "<number-of-iterations>",
          "mode": "<serial-or-parallel>",
          "batchSize": "<number-to-deploy-serially>"
      },
      "dependsOn": [
          "<array-of-related-resource-names>"
      ],
      "properties": {
          "<settings-for-the-resource>",
          "copy": [
              {
                  "name": ,
                  "count": ,
                  "input": {}
              }
          ]
      },
      "resources": [
          "<array-of-child-resources>"
      ]
  }
]
```

| Nome do elemento | Necessário | Descrição |
|:--- |:--- |:--- |
| Condição | Não | Valor booleano que indica se o recurso é implementado. |
| apiVersion |Sim |Versão da API REST para utilizar para criar o recurso. |
| tipo |Sim |Tipo de recurso. Este valor é uma combinação do espaço de nomes do fornecedor de recursos e o tipo de recurso (tais como **Storage/storageaccounts**). |
| nome |Sim |Nome do recurso. O nome tem de seguir as restrições de componente URI definidas no RFC3986. Além disso, os serviços do Azure que expõem o nome do recurso para fora entidades validar o nome para se certificar de não é uma tentativa de spoof de identidade de outra. |
| localização |varia |Georreplicação-localizações suportadas do recurso fornecido. Pode selecionar qualquer uma das localizações disponíveis, mas normalmente faz sentido para escolher aquele que está próximo os seus utilizadores. Normalmente, também faz sentido colocar recursos que interagem entre si na mesma região. A maioria dos tipos de recursos necessitam de uma localização, mas alguns tipos (por exemplo, uma atribuição de função) não necessitam de uma localização. Consulte [Definir localização do recurso na modelos Azure Resource Manager](resource-manager-template-location.md). |
| etiquetas |Não |Etiquetas que estão associadas o recursos. Consulte [Etiquetar recursos em modelos do Azure Resource Manager](resource-manager-template-tags.md). |
| comentários |Não |As notas para documentar recursos no seu modelo |
| Copiar |Não |Se for necessário mais do que uma instância, o número de recursos para criar. O modo predefinido é paralelo. Especifique o modo de série quando não pretender que todos os ou os recursos a implementar em simultâneo. Para obter mais informações, consulte [criar várias instâncias de recursos no Azure Resource Manager](resource-group-create-multiple.md). |
| dependsOn |Não |Recursos a que tem de ser implementados antes de implementar este recurso. O Resource Manager avalia as dependências entre os recursos e implementa-los na ordem correta. Quando os recursos não são dependentes entre si, que são implementados em paralelo. O valor pode ser uma lista separada por vírgulas de um recurso nomes ou identificadores exclusivos de recursos. Apenas lista os recursos que são implementados neste modelo. Recursos que não estão definidos neste modelo tem de existir. Evite adicionar dependências desnecessárias à medida que estes possam lenta a implementação e criar dependências circulares. Para obter orientações sobre as dependências de definição, consulte [definir dependências nos modelos Azure Resource Manager](resource-group-define-dependencies.md). |
| propriedades |Não |Definições de configuração específicas do recurso. Os valores para as propriedades são os mesmos que os valores que fornecem no corpo do pedido para a operação de REST API (método PUT) criar o recurso. Também pode especificar uma matriz de cópia para criar várias instâncias de uma propriedade. Para obter mais informações, consulte [criar várias instâncias de recursos no Azure Resource Manager](resource-group-create-multiple.md). |
| Recursos |Não |Recursos subordinados que dependem do recurso que está a ser definido. Fornece apenas tipos de recursos que são permitidos pelo esquema do recurso principal. O tipo completamente qualificado do recurso subordinado inclui o tipo de recurso principal, tal como **Microsoft.Web/sites/extensions**. Dependência no recurso principal não está implícita. Tem de definir explicitamente que dependência. |

A secção de recursos contém uma matriz de recursos a implementar. Dentro de cada recurso, também pode definir uma matriz de recursos subordinados. Por conseguinte, a secção de recursos pode ter uma estrutura como:

```json
"resources": [
  {
      "name": "resourceA",
  },
  {
      "name": "resourceB",
      "resources": [
        {
            "name": "firstChildResourceB",
        },
        {   
            "name": "secondChildResourceB",
        }
      ]
  },
  {
      "name": "resourceC",
  }
]
```      

Para obter mais informações sobre como definir recursos subordinados, consulte [definir nome e tipo de recurso subordinado no modelo do Resource Manager](resource-manager-template-child-resource.md).

O **condição** elemento Especifica se o recurso é implementado. O valor para este elemento é resolvido para true ou false. Por exemplo, para especificar se for implementada uma nova conta de armazenamento, utilize:

```json
{
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2017-06-01",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "[variables('storageAccountType')]"
    },
    "kind": "Storage",
    "properties": {}
}
```

Para obter um exemplo de utilização de um recurso novo ou existente, consulte [novo ou existente de condição de modelo](https://github.com/rjmax/Build2017/blob/master/Act1.TemplateEnhancements/Chapter05.ConditionalResources.NewOrExisting.json).

Para especificar se uma máquina virtual é implementada com uma palavra-passe ou chave SSH, defina duas versões da máquina virtual no seu modelo e utilize **condição** para diferenciar utilização. Transmita um parâmetro que especifica qual o cenário a implementar.

```json
{
    "condition": "[equals(parameters('passwordOrSshKey'),'password')]",
    "apiVersion": "2016-03-30",
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[concat(variables('vmName'),'password')]",
    "properties": {
        "osProfile": {
            "computerName": "[variables('vmName')]",
            "adminUsername": "[parameters('adminUsername')]",
            "adminPassword": "[parameters('adminPassword')]"
        },
        ...
    },
    ...
},
{
    "condition": "[equals(parameters('passwordOrSshKey'),'sshKey')]",
    "apiVersion": "2016-03-30",
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[concat(variables('vmName'),'ssh')]",
    "properties": {
        "osProfile": {
            "linuxConfiguration": {
                "disablePasswordAuthentication": "true",
                "ssh": {
                    "publicKeys": [
                        {
                            "path": "[variables('sshKeyPath')]",
                            "keyData": "[parameters('adminSshKey')]"
                        }
                    ]
                }
            }
        },
        ...
    },
    ...
}
``` 

Para obter um exemplo de utilização de uma palavra-passe ou chave SSH para implementar a máquina virtual, consulte [modelo de condição de nome de utilizador ou SSH](https://github.com/rjmax/Build2017/blob/master/Act1.TemplateEnhancements/Chapter05.ConditionalResourcesUsernameOrSsh.json).

## <a name="outputs"></a>saídas
Na secção saídas, especifique os valores que são devolvidos por implementação. Por exemplo, pode devolver o URI para aceder a um recurso implementado.

O exemplo seguinte mostra a estrutura de uma definição de saída:

```json
"outputs": {
    "<outputName>" : {
        "type" : "<type-of-output-value>",
        "value": "<output-value-expression>"
    }
}
```

| Nome do elemento | Necessário | Descrição |
|:--- |:--- |:--- |
| outputName |Sim |Nome do valor de saída. Tem de ser um identificador de JavaScript válido. |
| tipo |Sim |Tipo de valor de saída. Valores de saída suportam os mesmos tipos de parâmetros de entrada de modelo. |
| valor |Sim |Expressão de linguagem do modelo que é avaliada e devolvido como valor de saída. |

O exemplo seguinte mostra um valor que é devolvido na secção saídas.

```json
"outputs": {
    "siteUri" : {
        "type" : "string",
        "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
    }
}
```

Para obter mais informações sobre como trabalhar com a saída, consulte [partilha Estado em modelos do Azure Resource Manager](best-practices-resource-manager-state.md).

## <a name="template-limits"></a>Limites de modelo

Limite o tamanho do seu modelo para 1 MB e cada ficheiro de parâmetros para 64 KB. O limite de 1 MB aplica-se para o estado final do modelo de depois foi expandido com as definições do recurso interativo e os valores de parâmetros e variáveis. 

Também estão limitados a:

* 256 parâmetros
* 256 variáveis
* 800 recursos (incluindo o número de cópias)
* 64 valores de saída
* 24,576 carateres de uma expressão de modelo

Pode exceder alguns limites de modelo ao utilizar um modelo aninhado. Para obter mais informações, consulte [utilizar modelos ligados ao implementar os recursos do Azure](resource-group-linked-templates.md). Para reduzir o número de parâmetros, variáveis ou saídas, pode combinar vários valores para um objeto. Para obter mais informações, consulte [objetos como parâmetros](resource-manager-objects-as-parameters.md).

## <a name="next-steps"></a>Passos seguintes
* Para ver modelos completos para vários tipos de soluções, veja os [Modelos de Início Rápido do Azure](https://azure.microsoft.com/documentation/templates/).
* Para obter detalhes sobre as funções que pode utilizar a partir de um modelo, consulte [funções de modelo do Azure Resource Manager](resource-group-template-functions.md).
* Combinar vários modelos durante a implementação, consulte [utilizar modelos ligados com o Azure Resource Manager](resource-group-linked-templates.md).
* Terá de utilizar os recursos que existe dentro de um grupo de recursos diferente. Este cenário é comum quando trabalhar com as contas de armazenamento e as redes virtuais que são partilhadas em vários grupos de recursos. Para obter mais informações, consulte o [resourceId função](resource-group-template-functions-resource.md#resourceid).
