---
title: "Compreender a criação de IU definição para o Azure gerida aplicações | Microsoft Docs"
description: "Descreve como criar definições de IU para aplicações geridas do Azure"
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/26/2017
ms.author: tomfitz
ms.openlocfilehash: d8f04d8ed2e56cecb1b7a850bed55a02a9492bb5
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2017
---
# <a name="create-azure-portal-user-interface-for-your-managed-application"></a>Criar a interface de utilizador do portal do Azure para a sua aplicação gerida
Este documento apresenta os conceitos principais do ficheiro createUiDefinition.json. O portal do Azure utiliza este ficheiro para gerar a interface de utilizador para criar uma aplicação gerida.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json",
   "handler": "Microsoft.Compute.MultiVm",
   "version": "0.1.2-preview",
   "parameters": {
      "basics": [ ],
      "steps": [ ],
      "outputs": { }
   }
}
```

Um CreateUiDefinition sempre contém três propriedades: 

* processador
* Versão
* parâmetros

Para aplicações geridas, o processador deve ser sempre `Microsoft.Compute.MultiVm`, e a versão mais recente suportada é `0.1.2-preview`.

O esquema da propriedade parâmetros depende a combinação do processador especificado e a versão. Para aplicações geridas, as propriedades suportadas são `basics`, `steps`, e `outputs`. As propriedades as noções básicas e passos contêm o _elementos_ - como caixas de texto e as dropdowns - a apresentar no portal do Azure. A propriedade de saídas utilizada para mapear os valores de saída dos elementos especificados para os parâmetros do modelo de implementação Azure Resource Manager.

Incluindo `$schema` é recomendada, mas opcionais. Se for especificado, o valor para `version` tem de corresponder à versão dentro de `$schema` URI.

## <a name="basics"></a>Noções básicas
O passo de noções básicas é sempre o primeiro passo do assistente gerado quando o portal do Azure analisa o ficheiro. Para além das que apresenta os elementos especificados na `basics`, o portal injects elementos aos utilizadores escolher a subscrição, o grupo de recursos e a localização para a implementação. Geralmente, os elementos de consultar os parâmetros de toda a implementação, como o nome de credenciais de administrador ou de cluster, devem passar neste passo.

Se o comportamento de um elemento depende da subscrição, o grupo de recursos ou localização do utilizador, que o elemento não pode ser utilizado no Noções básicas. Por exemplo, **Microsoft.Compute.SizeSelector** depende da subscrição e localização para determinar a lista de tamanhos disponíveis do utilizador. Por conseguinte, **Microsoft.Compute.SizeSelector** só pode ser utilizada nos passos. Geralmente, apenas os elementos do **Microsoft.Common** espaço de nomes pode ser utilizado no Noções básicas. Embora alguns elementos em outros espaços de nomes (como **Microsoft.Compute.Credentials**) que não dependem do contexto do utilizador, ainda são permitidas.

## <a name="steps"></a>Passos
A propriedade de passos pode conter zero ou mais passos adicionais para apresentar após Noções básicas, cada um dos quais contém um ou mais elementos. Considere adicionar passos por função ou a camada da aplicação que está a ser implementada. Por exemplo, adicione um passo para entradas para os nós principais e de um passo para nós de trabalho num cluster.

## <a name="outputs"></a>saídas
O portal do Azure utiliza o `outputs` propriedade para mapear os elementos da `basics` e `steps` para os parâmetros do modelo de implementação Azure Resource Manager. As chaves deste dicionário são os nomes dos parâmetros de modelo e os valores são propriedades dos objetos de resultado de elementos referenciados.

## <a name="functions"></a>Funções
Semelhante para funções de modelo no Azure Resource Manager (tanto na sintaxe e a funcionalidade), CreateUiDefinition fornece funções para trabalhar com dos elementos entradas e saídas, bem como as funcionalidades, tais como conditionals.

## <a name="next-steps"></a>Passos seguintes
O próprio ficheiro createUiDefinition.json tem um esquema simple. A profundidade real do mesmo provém de todos os elementos suportados e funções. Os itens são descritos em maior detalhe em:

- [Elementos](create-uidefinition-elements.md)
- [Funções](create-uidefinition-functions.md)

Está aqui disponível atual do esquema JSON para createUiDefinition: https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json. 

Versões posteriores estarão disponíveis na mesma localização. Substitua o `0.1.2-preview` parte do URL e o `version` valor com o identificador da versão que pretende utilizar. Os identificadores de versão atualmente suportados são `0.0.1-preview`, `0.1.0-preview`, `0.1.1-preview`, e `0.1.2-preview`.