---
title: "Definir a ordem de implementação de recursos do Azure | Microsoft Docs"
description: "Descreve como definir um recurso como depende de outro recurso durante a implementação para se certificar de recursos são implementados na ordem correta."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: 
ms.assetid: 34ebaf1e-480c-4b4d-9bf6-251bd3f8f2cf
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/03/2017
ms.author: tomfitz
ms.openlocfilehash: 3d6a46116ae9d7d940bc10dfa832540f42c0af7e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="define-the-order-for-deploying-resources-in-azure-resource-manager-templates"></a>Definir a ordem de implementação de recursos em modelos do Azure Resource Manager
Para um determinado recurso, podem existir outros recursos que tem de existir antes do recurso é implementado. Por exemplo, um servidor SQL tem de existir antes de tentar implementar uma base de dados do SQL Server. Define esta relação assinalando um recurso como depende de outros recursos. Definir uma dependência com o **dependsOn** elemento, ou utilizando o **referência** função. 

O Resource Manager avalia as dependências entre os recursos e implementa-los, por ordem dependente. Quando os recursos não são dependentes entre si, o Gestor de recursos implementa-los em paralelo. Só tem de definir as dependências de recursos que são implementadas no mesmo modelo. 

## <a name="dependson"></a>dependsOn
No seu modelo, o elemento de dependsOn permite-lhe definir um recurso como um dependente um ou mais recursos. O valor pode ser uma lista separada por vírgulas de nomes de recursos. 

O exemplo seguinte mostra um conjunto de dimensionamento de máquina virtual depende de um balanceador de carga, rede virtual e um ciclo que cria várias contas de armazenamento. Destes outros recursos não são mostrados no exemplo seguinte, mas teria de existe noutro local no modelo.

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "[variables('namingInfix')]",
  "location": "[variables('location')]",
  "apiVersion": "2016-03-30",
  "tags": {
    "displayName": "VMScaleSet"
  },
  "dependsOn": [
    "[variables('loadBalancerName')]",
    "[variables('virtualNetworkName')]",
    "storageLoop",
  ],
  ...
}
```

No exemplo anterior, uma dependência está incluída nos recursos que são criados através de um ciclo de cópia com o nome **storageLoop**. Por exemplo, consulte [criar várias instâncias de recursos no Azure Resource Manager](resource-group-create-multiple.md).

Quando definir as dependências, pode incluir o espaço de nomes de fornecedor de recursos e o tipo de recurso para evitar ambiguidade. Por exemplo, para esclarecer um balanceador de carga e a rede virtual que pode ter os mesmos nomes que outros recursos, utilize o seguinte formato:

```json
"dependsOn": [
  "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]",
  "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
]
``` 

Enquanto que pode ser inclined utilizar dependsOn para mapear as relações entre os recursos, é importante compreender por que motivo está a fazer. Por exemplo, documentar a forma como os recursos estão interligados, dependsOn não é a abordagem à direita. Não é possível consultar os recursos que foram definidos no elemento dependsOn após a implementação. Utilizando dependsOn, poderá afeta o tempo de implementação porque o Gestor de recursos não implementa em paralelos dois recursos que tenham uma dependência. Para documentar as relações entre os recursos, em vez disso, utilize [associação de recursos](/rest/api/resources/resourcelinks).

## <a name="child-resources"></a>Recursos subordinados
A propriedade de recursos permite-lhe especificar os recursos subordinados que estão relacionados com o recurso que está a ser definido. Recursos subordinados só podem ser definido cinco níveis. É importante ter em atenção que uma dependência implícita não foi criada entre um recurso subordinado e o recurso principal. Se precisar do recurso de subordinados para ser implementada após o recurso principal, tem de estado explicitamente que dependência com a propriedade dependsOn. 

Cada recurso principal aceita apenas determinados tipos de recursos como recursos subordinados. Os tipos de recursos aceites são especificados no [esquema de modelo](https://github.com/Azure/azure-resource-manager-schemas) do recurso principal. O nome do tipo de recurso subordinado inclui o nome do tipo de recurso principal, tal como **Microsoft.Web/sites/config** e **Microsoft.Web/sites/extensions** são os dois recursos subordinados do **Microsoft.Web/sites**.

O exemplo seguinte mostra um SQL server e base de dados SQL. Tenha em atenção que uma dependência explícita está definida entre a base de dados SQL e o SQL server, apesar da base de dados é um elemento subordinado do servidor.

```json
"resources": [
  {
    "name": "[variables('sqlserverName')]",
    "type": "Microsoft.Sql/servers",
    "location": "[resourceGroup().location]",
    "tags": {
      "displayName": "SqlServer"
    },
    "apiVersion": "2014-04-01-preview",
    "properties": {
      "administratorLogin": "[parameters('administratorLogin')]",
      "administratorLoginPassword": "[parameters('administratorLoginPassword')]"
    },
    "resources": [
      {
        "name": "[parameters('databaseName')]",
        "type": "databases",
        "location": "[resourceGroup().location]",
        "tags": {
          "displayName": "Database"
        },
        "apiVersion": "2014-04-01-preview",
        "dependsOn": [
          "[variables('sqlserverName')]"
        ],
        "properties": {
          "edition": "[parameters('edition')]",
          "collation": "[parameters('collation')]",
          "maxSizeBytes": "[parameters('maxSizeBytes')]",
          "requestedServiceObjectiveName": "[parameters('requestedServiceObjectiveName')]"
        }
      }
    ]
  }
]
```

## <a name="reference-function"></a>função de referência
O [referenciar a função](resource-group-template-functions-resource.md#reference) permite uma expressão de valor de derivar de JSON pares nome / valor ou outros recursos de tempo de execução. As expressões de referência implicitamente declara que um recurso depende de outro. O formato geral é:

```json
reference('resourceName').propertyPath
```

No exemplo seguinte, um ponto final de CDN explicitamente depende o perfil da CDN e implicitamente depende de uma aplicação web.

```json
{
    "name": "[variables('endpointName')]",
    "type": "endpoints",
    "location": "[resourceGroup().location]",
    "apiVersion": "2016-04-02",
    "dependsOn": [
            "[variables('profileName')]"
    ],
    "properties": {
        "originHostHeader": "[reference(variables('webAppName')).hostNames[0]]",
        ...
    }
```

Pode utilizar este elemento ou o elemento de dependsOn para especificar dependências, mas não terá de utilizar ambas para o mesmo recurso dependente. Sempre que possível, utilize uma referência implícita para evitar a adição de uma dependência desnecessária.

Para obter mais informações, consulte [referenciar a função](resource-group-template-functions-resource.md#reference).

## <a name="recommendations-for-setting-dependencies"></a>Recomendações para a definição de dependências

Ao decidir as dependências para definir, utilize as seguintes diretrizes:

* Defina como alguns dependências quanto possível.
* Defina um recurso subordinado como depende o recurso principal.
* Utilize o **referência** função para definir as dependências implícitas entre os recursos que precisam de partilhar uma propriedade. Não adicione uma dependência explícita (**dependsOn**) quando que já definiu uma dependência implícita. Esta abordagem reduz o risco de ter dependências desnecessárias. 
* Definir uma dependência quando um recurso não pode ser **criado** sem as funcionalidades da outro recurso. Não defina uma dependência se os recursos de apenas interagem após a implementação.
* Permitir que as dependências cascade sem definição-los explicitamente. Por exemplo, a máquina virtual depende de uma interface de rede virtual e a interface de rede virtual depende de uma rede virtual e endereços IP públicos. Por conseguinte, a máquina virtual está implementados recursos após todos os três, mas não definir explicitamente a máquina virtual como dependentes em todos os recursos de três. Esta abordagem esclarece a ordem da dependência e torna mais fácil alterar o modelo mais tarde.
* Se um valor pode ser determinado antes da implementação, experimente implementar os recursos sem uma dependência. Por exemplo, se um valor de configuração tem o nome do recurso outro, poderá não ter uma dependência. Esta orientação não funciona sempre porque alguns dos recursos verificar a existência da outros recursos. Se receber um erro, adicione uma dependência. 

O Resource Manager identifica dependências circulares durante a validação do modelo. Se receber um erro a indicar que existe uma dependência circular, avalie o modelo para ver se as dependências não são necessários e podem ser removidas. Se remover dependências não funcionar, pode evitar dependências circulares por mover algumas operações de implementação para os recursos subordinados que são implementados depois dos recursos que tenham uma dependência circular. Por exemplo, suponha que está a implementar duas máquinas virtuais, mas tem de definir propriedades em cada um que fazem referência a si. Pode implementá-las pela seguinte ordem:

1. vm1
2. vm2
3. Extensão vm1 depende vm1 e vm2. A extensão define os valores de vm1 que obtém a partir do vm2.
4. Extensão vm2 depende vm1 e vm2. A extensão define os valores de vm2 que obtém a partir do vm1.

Para obter informações sobre a ordem de implementação de avaliar e resolver erros de dependência, consulte [resolver erros comuns de implementação do Azure com o Azure Resource Manager](resource-manager-common-deployment-errors.md).

## <a name="next-steps"></a>Passos seguintes
* Para saber mais sobre as dependências de resolução de problemas durante a implementação, consulte [resolver erros comuns de implementação do Azure com o Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Para saber mais sobre a criação de modelos Azure Resource Manager, consulte [criação de modelos](resource-group-authoring-templates.md). 
* Para obter uma lista das funções disponíveis num modelo, consulte [funções de modelo](resource-group-template-functions.md).

