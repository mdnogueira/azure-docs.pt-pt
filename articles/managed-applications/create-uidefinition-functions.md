---
title: "Aplicação do Azure gerida criar funções de definição de IU | Microsoft Docs"
description: "Descreve as funções a utilizar quando criar definições de IU para aplicações geridas do Azure"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/12/2017
ms.author: tomfitz
ms.openlocfilehash: dcf570ca4bdc8eacb7e4d7a8ff0011c8e07b7a40
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2017
---
# <a name="createuidefinition-functions"></a>Funções de CreateUiDefinition
Esta secção contém as assinaturas para todas as funções suportadas de um CreateUiDefinition.

Para utilizar uma função, coloque a declaração com Parênteses Retos. Por exemplo:

```json
"[function()]"
```

Outras funções de cadeias e podem ser referenciadas como parâmetros de uma função, mas tem de ser rodeadas cadeias de plicas. Por exemplo:

```json
"[fn1(fn2(), 'foobar')]"
```

Quando aplicável, pode referenciar propriedades do resultado de uma função utilizando o operador de ponto. Por exemplo:

```json
"[func().prop1]"
```

## <a name="referencing-functions"></a>Funções de referência
Estas funções podem ser utilizadas para referenciar saídas do contexto de um CreateUiDefinition ou propriedades.

### <a name="basics"></a>Noções básicas
Devolve os valores de saída de um elemento que está definido no passo Noções básicas.

O exemplo seguinte devolve o resultado do elemento com o nome `foo` no passo Noções básicas:

```json
"[basics('foo')]"
```

### <a name="steps"></a>passos
Devolve os valores de saída de um elemento que está definido no passo especificado. Para obter os valores de saída de elementos no passo Noções básicas, utilize `basics()` em vez disso.

O exemplo seguinte devolve o resultado do elemento com o nome `bar` no passo com o nome `foo`:

```json
"[steps('foo').bar]"
```

### <a name="location"></a>localização
Devolve a localização selecionada no passo as noções básicas ou contexto atual.

Devolve o exemplo seguinte `"westus"`:

```json
"[location()]"
```

## <a name="string-functions"></a>Funções de cadeia
Estas funções só podem ser utilizadas com cadeias JSON.

### <a name="concat"></a>concat
Concatena uma ou mais cadeias.

Por exemplo, se o valor de saída de `element1` se `"bar"`, em seguida, neste exemplo devolve a cadeia `"foobar!"`:

```json
"[concat('foo', steps('step1').element1), '!']"
```

### <a name="substring"></a>subcadeia
Devolve a subcadeia da cadeia especificada. A subcadeia começa no índice especificado e tem o comprimento especificado.

O exemplo seguinte devolve `"ftw"`:

```json
"[substring('azure-ftw!!!1one', 6, 3)]"
```

### <a name="replace"></a>Substituir
Devolve uma cadeia na qual todas as ocorrências da cadeia especificada na cadeia de atual são substituídas por outra cadeia.

O exemplo seguinte devolve `"Everything is awesome!"`:

```json
"[replace('Everything is terrible!', 'terrible', 'awesome')]"
```

### <a name="guid"></a>GUID
Gera uma cadeia exclusiva global (GUID).

Devolve o exemplo seguinte `"c7bc8bdc-7252-4a82-ba53-7c468679a511"`:

```json
"[guid()]"
```

### <a name="tolower"></a>toLower
Devolve uma cadeia convertida em minúscula.

O exemplo seguinte devolve `"foobar"`:

```json
"[toLower('FOOBAR')]"
```

### <a name="toupper"></a>toUpper
Devolve uma cadeia convertida em maiúsculas.

O exemplo seguinte devolve `"FOOBAR"`:

```json
"[toUpper('foobar')]"
```

## <a name="collection-functions"></a>Funções de coleção
Estas funções podem ser utilizadas com coleções, como cadeias JSON, matrizes e objetos.

### <a name="contains"></a>contém
Devolve `true` se uma cadeia contém a subcadeia especificada, uma matriz contém o valor especificado ou um objeto contém a chave especificada.

#### <a name="example-1-string"></a>Exemplo 1: cadeia
O exemplo seguinte devolve `true`:

```json
"[contains('foobar', 'foo')]"
```

#### <a name="example-2-array"></a>Exemplo 2: matriz
Partem do princípio de `element1` devolve `[1, 2, 3]`. O exemplo seguinte devolve `false`:

```json
"[contains(steps('foo').element1, 4)]"
```

#### <a name="example-3-object"></a>Exemplo 3: objeto
Partem do princípio de `element1` devolve:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

O exemplo seguinte devolve `true`:

```json
"[contains(steps('foo').element1, 'key1')]"
```

### <a name="length"></a>comprimento
Devolve o número de carateres numa cadeia, o número de valores numa matriz ou o número de chaves por um objeto.

#### <a name="example-1-string"></a>Exemplo 1: cadeia
O exemplo seguinte devolve `6`:

```json
"[length('foobar')]"
```

#### <a name="example-2-array"></a>Exemplo 2: matriz
Partem do princípio de `element1` devolve `[1, 2, 3]`. O exemplo seguinte devolve `3`:

```json
"[length(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Exemplo 3: objeto
Partem do princípio de `element1` devolve:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

O exemplo seguinte devolve `2`:

```json
"[length(steps('foo').element1)]"
```

### <a name="empty"></a>vazio
Devolve `true` se a cadeia, uma matriz nem um objeto é nulo ou está vazio.

#### <a name="example-1-string"></a>Exemplo 1: cadeia
O exemplo seguinte devolve `true`:

```json
"[empty('')]"
```

#### <a name="example-2-array"></a>Exemplo 2: matriz
Partem do princípio de `element1` devolve `[1, 2, 3]`. O exemplo seguinte devolve `false`:

```json
"[empty(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Exemplo 3: objeto
Partem do princípio de `element1` devolve:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

O exemplo seguinte devolve `false`:

```json
"[empty(steps('foo').element1)]"
```

#### <a name="example-4-null-and-undefined"></a>Exemplo 4: nulo e não definida
Partem do princípio de `element1` é `null` ou indefinido. O exemplo seguinte devolve `true`:

```json
"[empty(steps('foo').element1)]"
```

### <a name="first"></a>primeiro
Devolve o primeiro caráter da cadeia especificado; primeiro valor da matriz especificada; ou o primeiro valor e chave do objeto especificado.

#### <a name="example-1-string"></a>Exemplo 1: cadeia
O exemplo seguinte devolve `"f"`:

```json
"[first('foobar')]"
```

#### <a name="example-2-array"></a>Exemplo 2: matriz
Partem do princípio de `element1` devolve `[1, 2, 3]`. O exemplo seguinte devolve `1`:

```json
"[first(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Exemplo 3: objeto
Partem do princípio de `element1` devolve:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```
O exemplo seguinte devolve `{"key1": "foobar"}`:

```json
"[first(steps('foo').element1)]"
```

### <a name="last"></a>última
Devolve o último caráter da cadeia especificada, o valor de matriz especificada, ou a chave de última último e o valor do objeto especificado.

#### <a name="example-1-string"></a>Exemplo 1: cadeia
O exemplo seguinte devolve `"r"`:

```json
"[last('foobar')]"
```

#### <a name="example-2-array"></a>Exemplo 2: matriz
Partem do princípio de `element1` devolve `[1, 2, 3]`. O exemplo seguinte devolve `2`:

```json
"[last(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Exemplo 3: objeto
Partem do princípio de `element1` devolve:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

O exemplo seguinte devolve `{"key2": "raboof"}`:

```json
"[last(steps('foo').element1)]"
```

### <a name="take"></a>tirar
Devolve um número especificado de carateres contíguos desde o início da cadeia, um número especificado de valores contíguos desde o início da matriz ou um número especificado de contíguas chaves e valores desde o início do objeto.

#### <a name="example-1-string"></a>Exemplo 1: cadeia
O exemplo seguinte devolve `"foo"`:

```json
"[take('foobar', 3)]"
```

#### <a name="example-2-array"></a>Exemplo 2: matriz
Partem do princípio de `element1` devolve `[1, 2, 3]`. O exemplo seguinte devolve `[1, 2]`:

```json
"[take(steps('foo').element1, 2)]"
```

#### <a name="example-3-object"></a>Exemplo 3: objeto
Partem do princípio de `element1` devolve:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

O exemplo seguinte devolve `{"key1": "foobar"}`:

```json
"[take(steps('foo').element1, 1)]"
```

### <a name="skip"></a>Ignorar
Ignora um número especificado de elementos de uma coleção e, em seguida, devolve os elementos restantes.

#### <a name="example-1-string"></a>Exemplo 1: cadeia
O exemplo seguinte devolve `"bar"`:

```json
"[skip('foobar', 3)]"
```

#### <a name="example-2-array"></a>Exemplo 2: matriz
Partem do princípio de `element1` devolve `[1, 2, 3]`. O exemplo seguinte devolve `[3]`:

```json
"[skip(steps('foo').element1, 2)]"
```

#### <a name="example-3-object"></a>Exemplo 3: objeto
Partem do princípio de `element1` devolve:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```
O exemplo seguinte devolve `{"key2": "raboof"}`:

```json
"[skip(steps('foo').element1, 1)]"
```

## <a name="logical-functions"></a>Funções lógicas
Estas funções podem ser utilizadas no conditionals. Algumas funções não podem suportar todos os tipos de dados JSON.

### <a name="equals"></a>igual a
Devolve `true` se ambos os parâmetros têm o mesmo tipo e valor. Esta função suporta todos os tipos de dados JSON.

O exemplo seguinte devolve `true`:

```json
"[equals(0, 0)]"
```

O exemplo seguinte devolve `true`:

```json
"[equals('foo', 'foo')]"
```

O exemplo seguinte devolve `false`:

```json
"[equals('abc', ['a', 'b', 'c'])]"
```

### <a name="less"></a>menor
Devolve `true` se o primeiro parâmetro é estritamente menor que o segundo parâmetro. Esta função suporta parâmetros apenas de tipo número e a cadeia.

O exemplo seguinte devolve `true`:

```json
"[less(1, 2)]"
```

O exemplo seguinte devolve `false`:

```json
"[less('9', '10')]"
```

### <a name="lessorequals"></a>lessOrEquals
Devolve `true` se o primeiro parâmetro é inferior ou igual ao segundo parâmetro. Esta função suporta parâmetros apenas de tipo número e a cadeia.

O exemplo seguinte devolve `true`:

```json
"[lessOrEquals(2, 2)]"
```

### <a name="greater"></a>maior
Devolve `true` se o primeiro parâmetro é estritamente maior do que o segundo parâmetro. Esta função suporta parâmetros apenas de tipo número e a cadeia.

O exemplo seguinte devolve `false`:

```json
"[greater(1, 2)]"
```

O exemplo seguinte devolve `true`:

```json
"[greater('9', '10')]"
```

### <a name="greaterorequals"></a>greaterOrEquals
Devolve `true` se o primeiro parâmetro é igual ou superior para o segundo parâmetro. Esta função suporta parâmetros apenas de tipo número e a cadeia.

O exemplo seguinte devolve `true`:

```json
"[greaterOrEquals(2, 2)]"
```

### <a name="and"></a>e
Devolve `true` se todos os parâmetros avaliar para `true`. Esta função suporta dois ou mais parâmetros apenas de tipo Booleano.

O exemplo seguinte devolve `true`:

```json
"[and(equals(0, 0), equals('foo', 'foo'), less(1, 2))]"
```

O exemplo seguinte devolve `false`:

```json
"[and(equals(0, 0), greater(1, 2))]"
```

### <a name="or"></a>ou
Devolve `true` se, pelo menos, um dos parâmetros for avaliada como `true`. Esta função suporta dois ou mais parâmetros apenas de tipo Booleano.

O exemplo seguinte devolve `true`:

```json
"[or(equals(0, 0), equals('foo', 'foo'), less(1, 2))]"
```

O exemplo seguinte devolve `true`:

```json
"[or(equals(0, 0), greater(1, 2))]"
```

### <a name="not"></a>não
Devolve `true` se o parâmetro for avaliada como `false`. Esta função suporta parâmetros apenas de tipo Booleano.

O exemplo seguinte devolve `true`:

```json
"[not(false)]"
```

O exemplo seguinte devolve `false`:

```json
"[not(equals(0, 0))]"
```

### <a name="coalesce"></a>Unir
Devolve o valor do primeiro parâmetro não nulo. Esta função suporta todos os tipos de dados JSON.

Partem do princípio de `element1` e `element2` estiverem definidas. O exemplo seguinte devolve `"foobar"`:

```json
"[coalesce(steps('foo').element1, steps('foo').element2, 'foobar')]"
```

## <a name="conversion-functions"></a>Funções de conversão
Estas funções podem ser utilizadas para converter valores entre tipos de dados JSON e codificações.

### <a name="int"></a>Int
Converte o parâmetro para um número inteiro. Esta função suporta os parâmetros de tipo número e a cadeia.

O exemplo seguinte devolve `1`:

```json
"[int('1')]"
```

O exemplo seguinte devolve `2`:

```json
"[int(2.9)]"
```

### <a name="float"></a>Número de vírgula flutuante
Converte o parâmetro uma vírgula flutuante. Esta função suporta os parâmetros de tipo número e a cadeia.

O exemplo seguinte devolve `1.0`:

```json
"[float('1.0')]"
```

O exemplo seguinte devolve `2.9`:

```json
"[float(2.9)]"
```

### <a name="string"></a>Cadeia
Converte o parâmetro numa cadeia. Esta função suporta parâmetros de todos os tipos de dados JSON.

O exemplo seguinte devolve `"1"`:

```json
"[string(1)]"
```

O exemplo seguinte devolve `"2.9"`:

```json
"[string(2.9)]"
```

O exemplo seguinte devolve `"[1,2,3]"`:

```json
"[string([1,2,3])]"
```

O exemplo seguinte devolve `"{"foo":"bar"}"`:

```json
"[string({\"foo\":\"bar\"})]"
```

### <a name="bool"></a>bool
Converte o parâmetro booleano. Esta função suporta parâmetros de tipo número, cadeia e booleano. Semelhante em booleanos em JavaScript, qualquer valor, exceto `0` ou `'false'` devolve `true`.

O exemplo seguinte devolve `true`:

```json
"[bool(1)]"
```

O exemplo seguinte devolve `false`:

```json
"[bool(0)]"
```

O exemplo seguinte devolve `true`:

```json
"[bool(true)]"
```

O exemplo seguinte devolve `true`:

```json
"[bool('true')]"
```

### <a name="parse"></a>analisar
Converte o parâmetro para um tipo nativo. Por outras palavras, esta função é o inverso da `string()`. Esta função suporta parâmetros apenas de tipo cadeia.

O exemplo seguinte devolve `1`:

```json
"[parse('1')]"
```

O exemplo seguinte devolve `true`:

```json
"[parse('true')]"
```

O exemplo seguinte devolve `[1,2,3]`:

```json
"[parse('[1,2,3]')]"
```

O exemplo seguinte devolve `{"foo":"bar"}`:

```json
"[parse('{\"foo\":\"bar\"}')]"
```

### <a name="encodebase64"></a>encodeBase64
Codifica o parâmetro para uma cadeia com codificação de base 64. Esta função suporta parâmetros apenas de tipo cadeia.

O exemplo seguinte devolve `"Zm9vYmFy"`:

```json
"[encodeBase64('foobar')]"
```

### <a name="decodebase64"></a>decodeBase64
Descodifica o parâmetro de uma cadeia codificada base-64. Esta função suporta parâmetros apenas de tipo cadeia.

O exemplo seguinte devolve `"foobar"`:

```json
"[decodeBase64('Zm9vYmFy')]"
```

### <a name="encodeuricomponent"></a>encodeUriComponent
Codifica o parâmetro para uma cadeia de URL, codificado. Esta função suporta parâmetros apenas de tipo cadeia.

O exemplo seguinte devolve `"https%3A%2F%2Fportal.azure.com%2F"`:

```json
"[encodeUriComponent('https://portal.azure.com/')]"
```

### <a name="decodeuricomponent"></a>decodeUriComponent
Descodifica o parâmetro de uma cadeia de URL, codificado. Esta função suporta parâmetros apenas de tipo cadeia.

O exemplo seguinte devolve `"https://portal.azure.com/"`:

```json
"[decodeUriComponent('https%3A%2F%2Fportal.azure.com%2F')]"
```

## <a name="math-functions"></a>Funções de bibliotecas
### <a name="add"></a>Adicionar
Adiciona dois números e devolve o resultado.

O exemplo seguinte devolve `3`:

```json
"[add(1, 2)]"
```

### <a name="sub"></a>sub
Subtrai o segundo número do primeiro número e devolve o resultado.

O exemplo seguinte devolve `1`:

```json
"[sub(3, 2)]"
```

### <a name="mul"></a>MUL
Multiplica dois números e devolve o resultado.

O exemplo seguinte devolve `6`:

```json
"[mul(2, 3)]"
```

### <a name="div"></a>Div
Divide o primeiro número, o segundo número e devolve o resultado. O resultado é sempre um número inteiro.

O exemplo seguinte devolve `2`:

```json
"[div(6, 3)]"
```

### <a name="mod"></a>MOD
Divide o primeiro número, o segundo número e devolve o resto.

O exemplo seguinte devolve `0`:

```json
"[mod(6, 3)]"
```

O exemplo seguinte devolve `2`:

```json
"[mod(6, 4)]"
```

### <a name="min"></a>min.
Devolve os pequenos dos dois números.

O exemplo seguinte devolve `1`:

```json
"[min(1, 2)]"
```

### <a name="max"></a>Máx.
Devolve o maior de dois números.

O exemplo seguinte devolve `2`:

```json
"[max(1, 2)]"
```

### <a name="range"></a>intervalo
Gera uma sequência de números inteiro no intervalo especificado.

O exemplo seguinte devolve `[1,2,3]`:

```json
"[range(1, 3)]"
```

### <a name="rand"></a>rand
Devolve um número inteiro aleatório dentro do intervalo especificado. Esta função não gera criptograficamente seguros números aleatórios.

Devolve o exemplo seguinte `42`:

```json
"[rand(-100, 100)]"
```

### <a name="floor"></a>piso
Devolve o maior número inteiro menor ou igual ao número especificado.

O exemplo seguinte devolve `3`:

```json
"[floor(3.14)]"
```

### <a name="ceil"></a>ceil
Devolve o maior número inteiro maior que ou igual ao número especificado.

O exemplo seguinte devolve `4`:

```json
"[ceil(3.14)]"
```

## <a name="date-functions"></a>Funções de data
### <a name="utcnow"></a>utcNow
Devolve uma cadeia de formato ISO 8601 a data e hora atuais no computador local.

Devolve o exemplo seguinte `"1990-12-31T23:59:59.000Z"`:

```json
"[utcNow()]"
```

### <a name="addseconds"></a>addSeconds
Adiciona um número inteiro de segundos para o carimbo especificado.

O exemplo seguinte devolve `"1991-01-01T00:00:00.000Z"`:

```json
"[addSeconds('1990-12-31T23:59:60Z', 1)]"
```

### <a name="addminutes"></a>addMinutes
Adiciona um número inteiro de minutos para o carimbo especificado.

O exemplo seguinte devolve `"1991-01-01T00:00:59.000Z"`:

```json
"[addMinutes('1990-12-31T23:59:59Z', 1)]"
```

### <a name="addhours"></a>addHours
Adiciona um número inteiro de horas para o carimbo especificado.

O exemplo seguinte devolve `"1991-01-01T00:59:59.000Z"`:

```json
"[addHours('1990-12-31T23:59:59Z', 1)]"
```

## <a name="next-steps"></a>Passos seguintes
* Para uma introdução para o Azure Resource Manager, consulte [descrição geral do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

