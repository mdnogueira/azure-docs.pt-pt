---
title: "Erros de modelo inválido do Azure | Microsoft Docs"
description: "Descreve como resolver erros de modelo inválido."
services: azure-resource-manager,azure-portal
documentationcenter: 
author: tfitzmac
manager: timlt
editor: 
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: support-article
ms.date: 09/13/2017
ms.author: tomfitz
ms.openlocfilehash: e58c10cfb4cdd4ba49945e6c19845cbc957d6326
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="resolve-errors-for-invalid-template"></a>Resolva os erros de modelo inválido

Este artigo descreve como resolver erros de modelo inválido.

## <a name="symptom"></a>Sintoma

Quando implementar um modelo, receberá um erro a indicar:

```
Code=InvalidTemplate
Message=<varies>
```

A mensagem de erro depende do tipo de erro.

## <a name="cause"></a>Causa

Este erro poderá resultar de vários tipos diferentes de erros. Estão normalmente relacionados um erro de sintaxe ou estrutural no modelo.

## <a name="solution"></a>Solução

### <a name="solution-1---syntax-error"></a>Solução 1 - erro de sintaxe

Se receber uma mensagem de erro que indica a validação do modelo falha, pode ter um problema de sintaxe no seu modelo.

```
Code=InvalidTemplate
Message=Deployment template validation failed
```

Este erro é fácil fazer como expressões de modelo podem ser intricate. Por exemplo, a atribuição de nome seguinte para uma conta do storage contém um conjunto de parênteses Retos, três funções, três conjuntos de parênteses, um conjunto de plicas e uma propriedade:

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
```

Se não fornecer a sintaxe correspondente, o modelo produz um valor que é diferente da sua intenção.

Quando receber este tipo de erro, reveja cuidadosamente a sintaxe de expressão. Considere a utilização de um editor de JSON como [Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) ou [Visual Studio Code](resource-manager-vs-code.md), que pode avisá-lo sobre os erros de sintaxe.

### <a name="solution-2---incorrect-segment-lengths"></a>Solução 2 - comprimentos de segmento incorreto

Outro modelo inválido erro ocorre quando o nome de recurso não está no formato correto.

```
Code=InvalidTemplate
Message=Deployment template validation failed: 'The template resource {resource-name}'
for type {resource-type} has incorrect segment lengths.
```

Um recurso de nível de raiz tem de ter um menor segmento no nome do que o tipo de recurso. Cada segmento é diferenciado por uma barra. No exemplo seguinte, o tipo tem dois segmentos e o nome tem um segmento, pelo que é um **nome válido**.

```json
{
  "type": "Microsoft.Web/serverfarms",
  "name": "myHostingPlanName",
  ...
}
```

Mas o exemplo seguinte é **não um nome válido** porque tem o mesmo número de segmentos que o tipo.

```json
{
  "type": "Microsoft.Web/serverfarms",
  "name": "appPlan/myHostingPlanName",
  ...
}
```

Para obter recursos subordinados, o tipo e o nome tem o mesmo número de segmentos. Este número de segmentos faz sentido porque o nome completo e o tipo para o elemento subordinado inclui o nome do principal e o tipo. Por conseguinte, o nome completo ainda tem um segmento menor que o tipo completo.

```json
"resources": [
    {
        "type": "Microsoft.KeyVault/vaults",
        "name": "contosokeyvault",
        ...
        "resources": [
            {
                "type": "secrets",
                "name": "appPassword",
                ...
            }
        ]
    }
]
```

Obter os segmentos direita pode ser tricky com tipos de Gestor de recursos que são aplicados através de fornecedores de recursos. Por exemplo, aplicar um bloqueio de recursos para um web site necessita de um tipo com quatro segmentos. Por conseguinte, o nome é três segmentos:

```json
{
    "type": "Microsoft.Web/sites/providers/locks",
    "name": "[concat(variables('siteName'),'/Microsoft.Authorization/MySiteLock')]",
    ...
}
```

### <a name="solution-3---parameter-is-not-valid"></a>Solução 3 - parâmetro não é válido

Se o modelo especifica os valores permitidos para um parâmetro e fornecer um valor que não é um desses valores, receberá uma mensagem semelhante ao seguinte erro:

```
Code=InvalidTemplate;
Message=Deployment template validation failed: 'The provided value {parameter value}
for the template parameter {parameter name} is not valid. The parameter value is not
part of the allowed values
```

Duplo Verifique os valores permitidos no modelo e forneça um durante a implementação.

### <a name="solution-4---circular-dependency-detected"></a>Solução 4 - dependência circular detetada

Recebe este erro quando os recursos são dependentes entre si de uma forma que impede a implementação de iniciar. Uma combinação de interdependencies torna dois ou mais recursos de espera para outros recursos que também estão a aguardar. Por exemplo, resource1 depende resource3, resource2 depende resource1 e resource3 depende resource2. Normalmente, pode resolver este problema removendo dependências desnecessárias.
