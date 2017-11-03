---
title: "Funções de modelo do Resource Manager | Microsoft Docs"
description: "Descreve as funções de utilizar um modelo Azure Resource Manager para obter os valores, trabalhar com cadeias e números e obter informações de implementação."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 0644abe1-abaa-443d-820d-1966d7d26bfd
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/18/2017
ms.author: tomfitz
ms.openlocfilehash: 725f12a6b5dcf4b66109512336e8a617013c5974
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-resource-manager-template-functions"></a>Funções de modelo do Azure Resource Manager
Este tópico descreve todos os as funções que pode utilizar num modelo Azure Resource Manager.

Adicione funções nos seus modelos por envolvente-los entre parênteses Retos: `[` e `]`, respetivamente. A expressão é avaliada durante a implementação. Enquanto escritos como uma cadeia literal, o resultado da avaliação da expressão pode ser um tipo JSON diferente, tal como uma matriz, um objeto nem um número inteiro. Apenas como em JavaScript, chamadas de função estejam formatadas como `functionName(arg1,arg2,arg3)`. Referenciar propriedades utilizando os operadores de ponto e [Índice].

Uma expressão de modelo não pode exceder 24,576 carateres.

Funções de modelo e os respetivos parâmetros são sensível. Por exemplo, o Gestor de recursos é resolvido **variables('var1')** e **VARIABLES('VAR1')** como o mesmo. Quando avaliada, a menos que a função expressamente modifica caso (como toUpper ou toLower), a função preserva as maiúsculas e minúsculas. Determinados tipos de recurso podem ter requisitos de cenários independentemente da forma como são avaliadas as funções.

<a id="array" />
<a id="coalesce" />
<a id="concatarray" />
<a id="contains" />
<a id="createarray" />
<a id="empty" />
<a id="first" />
<a id="intersection" />
<a id="last" />
<a id="length" />
<a id="min" />
<a id="max" />
<a id="range" />
<a id="skip" />
<a id="take" />
<a id="union" />

## <a name="array-and-object-functions"></a>Funções de matriz e o objeto
O Resource Manager fornece várias funções para trabalhar com objetos e matrizes.

* [matriz](resource-group-template-functions-array.md#array)
* [Unir](resource-group-template-functions-array.md#coalesce)
* [concat](resource-group-template-functions-array.md#concat)
* [contém](resource-group-template-functions-array.md#contains)
* [createArray](resource-group-template-functions-array.md#createarray)
* [vazio](resource-group-template-functions-array.md#empty)
* [primeiro](resource-group-template-functions-array.md#first)
* [intersecção](resource-group-template-functions-array.md#intersection)
* [JSON](resource-group-template-functions-array.md#json)
* [última](resource-group-template-functions-array.md#last)
* [comprimento](resource-group-template-functions-array.md#length)
* [Mín.](resource-group-template-functions-array.md#min)
* [Máx.](resource-group-template-functions-array.md#max)
* [intervalo](resource-group-template-functions-array.md#range)
* [Ignorar](resource-group-template-functions-array.md#skip)
* [tirar](resource-group-template-functions-array.md#take)
* [União](resource-group-template-functions-array.md#union)

<a id="equals" />
<a id="less" />
<a id="lessorequals" />
<a id="greater" />
<a id="greaterorequals" />

## <a name="comparison-functions"></a>Funções de comparação
O Resource Manager fornece várias funções, para efetuar comparações nos seus modelos.

* [igual a](resource-group-template-functions-comparison.md#equals)
* [menor](resource-group-template-functions-comparison.md#less)
* [lessOrEquals](resource-group-template-functions-comparison.md#lessorequals)
* [maior](resource-group-template-functions-comparison.md#greater)
* [greaterOrEquals](resource-group-template-functions-comparison.md#greaterorequals)

<a id="deployment" />
<a id="parameters" />
<a id="variables" />

## <a name="deployment-value-functions"></a>Funções de valor de implementação
O Resource Manager fornece as seguintes funções, para obter os valores de secções do modelo e os valores relacionados com a implementação:

* [implementação](resource-group-template-functions-deployment.md#deployment)
* [parâmetros](resource-group-template-functions-deployment.md#parameters)
* [variáveis](resource-group-template-functions-deployment.md#variables)

<a id="add" />
<a id="copyindex" />
<a id="div" />
<a id="float" />
<a id="int" />
<a id="minint" />
<a id="maxint" />
<a id="mod" />
<a id="mul" />
<a id="sub" />

## <a name="logical-functions"></a>Funções lógicas
O Resource Manager fornece as seguintes funções para trabalhar com condições lógicas:

* [e](resource-group-template-functions-logical.md#and)
* [bool](resource-group-template-functions-logical.md#bool)
* [Se](resource-group-template-functions-logical.md#if)
* [não](resource-group-template-functions-logical.md#not)
* [ou](resource-group-template-functions-logical.md#or)

## <a name="numeric-functions"></a>Funções numérico
O Resource Manager fornece as seguintes funções para trabalhar com números inteiros:

* [Adicionar](resource-group-template-functions-numeric.md#add)
* [copyIndex](resource-group-template-functions-numeric.md#copyindex)
* [Div](resource-group-template-functions-numeric.md#div)
* [número de vírgula flutuante](resource-group-template-functions-numeric.md#float)
* [Int](resource-group-template-functions-numeric.md#int)
* [Mín.](resource-group-template-functions-numeric.md#min)
* [Máx.](resource-group-template-functions-numeric.md#max)
* [MOD](resource-group-template-functions-numeric.md#mod)
* [MUL](resource-group-template-functions-numeric.md#mul)
* [sub](resource-group-template-functions-numeric.md#sub)

<a id="listkeys" />
<a id="list" />
<a id="providers" />
<a id="reference" />
<a id="resourcegroup" />
<a id="resourceid" />
<a id="subscription" />

## <a name="resource-functions"></a>Funções de recursos
O Resource Manager fornece as seguintes funções, para obter valores de recursos:

* [listKeys e lista {Value}](resource-group-template-functions-resource.md#listkeys)
* [fornecedores](resource-group-template-functions-resource.md#providers)
* [referência](resource-group-template-functions-resource.md#reference)
* [resourceGroup](resource-group-template-functions-resource.md#resourcegroup)
* [resourceId](resource-group-template-functions-resource.md#resourceid)
* [subscrição](resource-group-template-functions-resource.md#subscription)

<a id="base64" />
<a id="base64tojson" />
<a id="base64tostring" />
<a id="concat" />
<a id="containsstring" />
<a id="datauri" />
<a id="datauritostring" />
<a id="emptystring" />
<a id="endswith" />
<a id="firststring" />
<a id="indexof" />
<a id="laststring" />
<a id="lastindexof" />
<a id="lengthstring" />
<a id="padleft" />
<a id="replace" />
<a id="skipstring" />
<a id="split" />
<a id="startswith" />
<a id="string" />
<a id="substring" />
<a id="takestring" />
<a id="tolower" />
<a id="toupper" />
<a id="trim" />
<a id="uniquestring" />
<a id="uri" />
<a id="uricomponent" />
<a id="uricomponenttostring" />

## <a name="string-functions"></a>Funções de cadeia
O Resource Manager fornece as seguintes funções para trabalhar com cadeias de:

* [Base64](resource-group-template-functions-string.md#base64)
* [base64ToJson](resource-group-template-functions-string.md#base64tojson)
* [base64ToString](resource-group-template-functions-string.md#base64tostring)
* [concat](resource-group-template-functions-string.md#concat)
* [contém](resource-group-template-functions-string.md#contains)
* [dataUri](resource-group-template-functions-string.md#datauri)
* [dataUriToString](resource-group-template-functions-string.md#datauritostring)
* [vazio](resource-group-template-functions-string.md#empty)
* [endsWith](resource-group-template-functions-string.md#endswith)
* [primeiro](resource-group-template-functions-string.md#first)
* [GUID](resource-group-template-functions-string.md#guid)
* [indexOf](resource-group-template-functions-string.md#indexof)
* [última](resource-group-template-functions-string.md#last)
* [lastIndexOf](resource-group-template-functions-string.md#lastindexof)
* [comprimento](resource-group-template-functions-string.md#length)
* [padleft do modelo](resource-group-template-functions-string.md#padleft)
* [substituir](resource-group-template-functions-string.md#replace)
* [Ignorar](resource-group-template-functions-string.md#skip)
* [dividir](resource-group-template-functions-string.md#split)
* [startsWith](resource-group-template-functions-string.md#startswith)
* [cadeia](resource-group-template-functions-string.md#string)
* [subcadeia](resource-group-template-functions-string.md#substring)
* [tirar](resource-group-template-functions-string.md#take)
* [toLower](resource-group-template-functions-string.md#tolower)
* [toUpper](resource-group-template-functions-string.md#toupper)
* [limitação](resource-group-template-functions-string.md#trim)
* [uniqueString](resource-group-template-functions-string.md#uniquestring)
* [URI](resource-group-template-functions-string.md#uri)
* [uriComponent](resource-group-template-functions-string.md#uricomponent)
* [uriComponentToString](resource-group-template-functions-string.md#uricomponenttostring)

## <a name="next-steps"></a>Passos seguintes
* Para obter uma descrição das secções num modelo Azure Resource Manager, consulte [modelos Authoring Azure Resource Manager](resource-group-authoring-templates.md)
* Intercalar vários modelos, consulte [utilizar modelos ligados com o Azure Resource Manager](resource-group-linked-templates.md)
* Para iterar um número de vezes especificado ao criar um tipo de recurso, consulte [criar várias instâncias de recursos no Gestor de recursos do Azure](resource-group-create-multiple.md)
* Para ver como implementar o modelo que criou, consulte [implementar uma aplicação com o modelo Azure Resource Manager](resource-group-template-deploy.md)
