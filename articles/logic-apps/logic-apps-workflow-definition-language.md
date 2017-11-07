---
title: "Esquema de linguagem de definição de fluxo de trabalho - Azure Logic Apps | Microsoft Docs"
description: "Definir fluxos de trabalho com base no esquema de definição de fluxo de trabalho para o Azure Logic Apps"
services: logic-apps
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
ms.assetid: 26c94308-aa0d-4730-97b6-de848bffff91
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 03/21/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: 36eee42b7b10dfb62e569d665f62a94fc94365be
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2017
---
# <a name="workflow-definition-language-schema-for-azure-logic-apps"></a>Esquema de linguagem de definição de fluxo de trabalho para o Azure Logic Apps

Uma definição de fluxo de trabalho contém a lógica real, que é executada como parte da sua aplicação lógica. Esta definição inclui um ou mais acionadores que iniciar a aplicação lógica e uma ou mais ações para a aplicação lógica para tomar.  
  
## <a name="basic-workflow-definition-structure"></a>Estrutura de definição de fluxo de trabalho básico

Segue-se a estrutura básica de uma definição de fluxo de trabalho:  
  
```json
{
    "$schema": "<schema-of the-definition>",
    "contentVersion": "<version-number-of-definition>",
    "parameters": { <parameter-definitions-of-definition> },
    "triggers": [ { <definition-of-flow-triggers> } ],
    "actions": [ { <definition-of-flow-actions> } ],
    "outputs": { <output-of-definition> }
}
```
  
> [!NOTE]
> O [API de REST de gestão do fluxo de trabalho](https://docs.microsoft.com/rest/api/logic/workflows) documentação tem informações sobre como criar e gerir fluxos de trabalho de aplicação de lógica.
  
|Nome do elemento|Necessário|Descrição|  
|------------------|--------------|-----------------|  
|$schema|Não|Especifica a localização para o ficheiro de esquema JSON que descreve a versão de idioma de definição. Esta localização é necessária quando referenciar uma definição externamente. Para este documento, a localização é: <p>`https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#`|  
|contentVersion|Não|Especifica a versão de definição. Quando implementar um fluxo de trabalho utilizando a definição, pode utilizar este valor para se certificar de que é utilizada a definição adequada.|  
|parâmetros|Não|Especifica os parâmetros utilizados para dados de entrada para a definição. Pode ser definido um máximo de 50 parâmetros.|  
|Acionadores|Não|Especifica as informações de acionadores que iniciar o fluxo de trabalho. Pode ser definido um máximo de 10 acionadores.|  
|ações|Não|Especifica as ações que são executadas conforme executa o fluxo. Pode ser definido um máximo de 250 ações.|  
|saídas|Não|Especifica informações sobre os recursos implementados. Pode ser definido um máximo de 10 saídas.|  
  
## <a name="parameters"></a>Parâmetros

Esta secção especifica os parâmetros que são utilizados na definição de fluxo de trabalho no momento da implementação. Tem de ser declarados todos os parâmetros nesta secção antes de poderem ser utilizados noutras secções da definição.  
  
O exemplo seguinte mostra a estrutura de uma definição do parâmetro:  

```json
"parameters": {
    "<parameter-name>" : {
        "type" : "<type-of-parameter-value>",
        "defaultValue": <default-value-of-parameter>,
        "allowedValues": [ <array-of-allowed-values> ],
        "metadata" : { "key": { "name": "value"} }
    }
}
```

|Nome do elemento|Necessário|Descrição|  
|------------------|--------------|-----------------|  
|tipo|Sim|**Tipo**: cadeia <p> **Declaração**:`"parameters": {"parameter1": {"type": "string"}` <p> **Especificação**:`"parameters": {"parameter1": {"value": "myparamvalue1"}}` <p> **Tipo**: securestring <p> **Declaração**:`"parameters": {"parameter1": {"type": "securestring"}}` <p> **Especificação**:`"parameters": {"parameter1": {"value": "myparamvalue1"}}` <p> **Tipo**: int <p> **Declaração**:`"parameters": {"parameter1": {"type": "int"}}` <p> **Especificação**:`"parameters": {"parameter1": {"value" : 5}}` <p> **Tipo**: bool <p> **Declaração**:`"parameters": {"parameter1": {"type": "bool"}}` <p> **Especificação**:`"parameters": {"parameter1": { "value": true }}` <p> **Tipo**: matriz <p> **Declaração**:`"parameters": {"parameter1": {"type": "array"}}` <p> **Especificação**:`"parameters": {"parameter1": { "value": [ array-of-values ]}}` <p> **Tipo**: objeto <p> **Declaração**:`"parameters": {"parameter1": {"type": "object"}}` <p> **Especificação**:`"parameters": {"parameter1": { "value": { JSON-object } }}` <p> **Tipo**: secureobject <p> **Declaração**:`"parameters": {"parameter1": {"type": "object"}}` <p> **Especificação**:`"parameters": {"parameter1": { "value": { JSON-object } }}` <p> **Nota:** o `securestring` e `secureobject` tipos não são devolvidos em `GET` operações. Todas as palavras-passe, chaves e segredos devem utilizar este tipo.|  
|DefaultValue|Não|Especifica o valor predefinido para o parâmetro quando for especificado nenhum valor momento que o recurso é criado.|  
|allowedValues|Não|Especifica uma matriz de valores permitidos para o parâmetro.|  
|Metadados|Não|Especifica informações adicionais sobre o parâmetro, como uma descrição legível ou tempo de criação de dados utilizadas pelo Visual Studio ou outras ferramentas.|  
  
Este exemplo mostra como pode utilizar um parâmetro na secção de corpo de uma ação:  
  
```json
"body" :
{
  "property1": "@parameters('parameter1')"
}
```

 Parâmetros também podem ser utilizados nas saídas.  
  
## <a name="triggers-and-actions"></a>Acionadores e ações  

Acionadores e ações especificam as chamadas que podem participar na execução do fluxo de trabalho. Para obter detalhes sobre nesta secção, consulte [ações de fluxo de trabalho e é acionado](logic-apps-workflow-actions-triggers.md).
  
## <a name="outputs"></a>saídas  

Saídas especificam informações que podem ser devolvidas a partir de um fluxo de trabalho a executar. Por exemplo, se tiver um estado específico ou um valor que pretende controlar para cada execução, pode incluir os dados nas saídas de execução. São apresentados os dados da API de REST de gestão para essa execução e a IU de gestão para que executar no portal do Azure. Também pode fluir estes saídas para outros sistemas externos, como o Power BI para criar dashboards. Saídas são *não* utilizados para responder a pedidos recebidos da API de REST do serviço. Para responder a um pedido recebido através de `response` tipo de ação, eis um exemplo:
  
```json
"outputs": {  
  "key1": {  
    "value": "value1",  
    "type" : "<type-of-value>"  
  }  
} 
```

|Nome do elemento|Necessário|Descrição|  
|------------------|--------------|-----------------|  
|chave1|Sim|Especifica o identificador da chave para a saída. Substitua **chave1** com um nome que pretende utilizar para identificar a saída.|  
|valor|Sim|Especifica o valor de saída.|  
|tipo|Sim|Especifica o tipo para o valor que foi especificado. Os tipos dos valores possíveis são: <ul><li>`string`</li><li>`securestring`</li><li>`int`</li><li>`bool`</li><li>`array`</li><li>`object`</li></ul>|
  
## <a name="expressions"></a>Expressões  

Valores JSON na definição podem ser literais ou podem ser expressões que são avaliadas quando a definição é utilizada. Por exemplo:  
  
```json
"name": "value"
```

 ou  
  
```json
"name": "@parameters('password') "
```

> [!NOTE]
> Alguns expressões obter os respetivos valores de ações de tempo de execução que poderão não existir no início da execução. Pode utilizar **funções** para ajudar a obter alguns destes valores.  
  
As expressões podem aparecer em qualquer local, um valor de cadeia JSON e sempre resultar no outro valor JSON. Quando um valor JSON tem sido determinado para ser uma expressão, o corpo da expressão é extraído ao remover no início de sessão (@). Se necessitar de uma cadeia literal que começa com @, que a cadeia tem ser escape utilizando@. Os exemplos seguintes mostram como expressões são avaliadas.  
  
|Valor JSON|resultado|  
|----------------|------------|  
|"parâmetros de"|Os carateres 'parameters' são devolvidos.|  
|"parâmetros de [1]"|Os carateres 'parameters [1]' são devolvidos.|  
|"@@"|Uma cadeia de 1 caráter que contém ' @' é devolvido.|  
|" @"|Uma cadeia de carateres de 2 que contém ' @' é devolvido.|  
  
Com *cadeia interpolação*, expressões também podem aparecer dentro de cadeias, onde as expressões são moldadas numa `@{ ... }`. Por exemplo: <p>`"name" : "First Name: @{parameters('firstName')} Last Name: @{parameters('lastName')}"`

O resultado é sempre uma cadeia, o que faz esta funcionalidade semelhante a `concat` função. Suponha que definiu `myNumber` como `42` e `myString` como `sampleString`:  
  
|Valor JSON|resultado|  
|----------------|------------|  
|"@parameters('myString')"|Devolve `sampleString` como uma cadeia.|  
|"@{parameters('myString')}"|Devolve `sampleString` como uma cadeia.|  
|"@parameters('myNumber')"|Devolve `42` como um *número*.|  
|"@{parameters('myNumber')}"|Devolve `42` como um *cadeia*.|  
|"Resposta é: @{parameters('myNumber')}"|Devolve a cadeia `Answer is: 42`.|  
|"@concat(' Resposta é: ', string(parameters('myNumber')))"|Devolve a cadeia`Answer is: 42`|  
|"Resposta é: @ @ {parameters('myNumber')}"|Devolve a cadeia `Answer is: @{parameters('myNumber')}`.|  
  
## <a name="operators"></a>Operadores  

Os operadores são os carateres que pode utilizar no interior de expressões ou funções. 
  
|operador|Descrição|  
|--------------|-----------------|  
|.|O operador ponto permite-lhe as propriedades de um objeto de referência|  
|?|O operador de ponto de interrogação permite-lhe referência nulas propriedades de um objeto sem um erro de tempo de execução. Por exemplo, pode utilizar esta expressão para processar saídas de Acionador nulo: <p>`@coalesce(trigger().outputs?.body?.property1, 'my default value')`|  
|'|A plica é a única forma moldar literais de cadeia. Não é possível utilizar aspas no interior de expressões porque este pontuação está em conflito com as aspas JSON que encapsula num wrapper a expressão de toda.|  
|[]|Os parênteses Retos pode ser utilizados para obter um valor a partir de uma matriz com um índice específico. Por exemplo, se tiver uma ação que transmite `range(0,10)`para o `forEach` função, pode utilizar esta função Obter itens de matrizes:  <p>`myArray[item()]`|  
  
## <a name="functions"></a>Funções  

Também pode chamar funções dentro de expressões. A tabela seguinte mostra as funções que podem ser utilizadas numa expressão.  
  
|expressão|Avaliação|  
|----------------|----------------|  
|"@function("Olá")"|Chama o membro da função da definição com a cadeia literal Olá como o primeiro parâmetro.|  
|"@function('-' Útil s!')"|Chama o membro da função da definição com a cadeia de literal 'É útil!' como o primeiro parâmetro|  
|"@function.prop1 ()"|Devolve o valor da propriedade prop1 o `myfunction` membro da definição.|  
|"@function.Prop1 ("Olá")"|Chama o membro da função da definição com a cadeia literal "Olá" como o primeiro parâmetro e devolve a propriedade prop1 do objeto.|  
|"@function(parameters('Hello'))"|Avalia o parâmetro de Olá e transmite o valor para a função|  
  
### <a name="referencing-functions"></a>Funções de referência  

Pode utilizar estas funções para referenciar saídas de outras ações na aplicação lógica ou valores transmitidos quando a aplicação lógica foi criada. Por exemplo, pode referenciar os dados de um passo para utilizá-lo noutro.  
  
|Nome de função|Descrição|  
|-------------------|-----------------|  
|parâmetros|Devolve um valor de parâmetro que é definido na definição do. <p>`parameters('password')` <p> **Número de parâmetro**: 1 <p> **Nome**: parâmetro <p> **Descrição**: necessário. O nome do parâmetro cujos valores que pretende.|  
|Ação|Permite que uma expressão de valor de derivar de outro nome JSON e pares de valor ou o resultado da ação de tempo de execução atual. A propriedade representada pelo propertyPath no exemplo seguinte é opcional. Se propertyPath não for especificado, a referência é o objecto de acção de todo. Esta função só pode ser utilizada dentro de efetue-até condições de uma ação. <p>`action().outputs.body.propertyPath`|  
|ações|Permite que uma expressão de valor de derivar de outro nome JSON e pares de valor ou o resultado da ação de tempo de execução. Estas expressões explicitamente declarar que uma ação depende de outra ação. A propriedade representada pelo propertyPath no exemplo seguinte é opcional. Se propertyPath não for especificado, a referência é o objecto de acção de todo. Pode utilizar este elemento ou o elemento de condições para especificar dependências, mas não terá de utilizar ambas para o mesmo recurso dependente. <p>`actions('myAction').outputs.body.propertyPath` <p> **Número de parâmetro**: 1 <p> **Nome**: nome da ação <p> **Descrição**: necessário. O nome da ação cujos valores que pretende. <p> Estão disponíveis propriedades no objecto de acção: <ul><li>`name`</li><li>`startTime`</li><li>`endTime`</li><li>`inputs`</li><li>`outputs`</li><li>`status`</li><li>`code`</li><li>`trackingId`</li><li>`clientTrackingId`</li></ul> <p>Consulte o [Rest API](http://go.microsoft.com/fwlink/p/?LinkID=850646) para obter detalhes sobre essas propriedades.|
|Acionador|Permite que uma expressão de valor de derivar de outro nome JSON e pares de valor ou o resultado do acionador de tempo de execução. A propriedade representada pelo propertyPath no exemplo seguinte é opcional. Se propertyPath não for especificado, é a referência ao objeto de Acionador de todo. <p>`trigger().outputs.body.propertyPath` <p>Quando utilizada dentro de entradas de um acionador, a função devolve as saídas da execução anterior. No entanto, quando utilizada dentro de condição de um acionador, o `trigger` função devolve as saídas da execução atual. <p> Estão disponíveis propriedades no objecto do acionador: <ul><li>`name`</li><li>`scheduledTime`</li><li>`startTime`</li><li>`endTime`</li><li>`inputs`</li><li>`outputs`</li><li>`status`</li><li>`code`</li><li>`trackingId`</li><li>`clientTrackingId`</li></ul> <p>Consulte o [Rest API](http://go.microsoft.com/fwlink/p/?LinkID=850644) para obter detalhes sobre essas propriedades.|
|actionOutputs|Esta função é a abreviatura para`actions('actionName').outputs` <p> **Número de parâmetro**: 1 <p> **Nome**: nome da ação <p> **Descrição**: necessário. O nome da ação cujos valores que pretende.|  
|actionBody e corpo|Estas funções são a abreviatura para`actions('actionName').outputs.body` <p> **Número de parâmetro**: 1 <p> **Nome**: nome da ação <p> **Descrição**: necessário. O nome da ação cujos valores que pretende.|  
|triggerOutputs|Esta função é a abreviatura para`trigger().outputs`|  
|triggerBody|Esta função é a abreviatura para`trigger().outputs.body`|  
|item|Quando utilizada dentro de uma ação repetida, esta função devolve o item que está na matriz para este iteração da ação. Por exemplo, se tiver uma ação que é executada para cada item uma matriz de mensagens, pode utilizar esta sintaxe: <p>`"input1" : "@item().subject"`| 
  
### <a name="collection-functions"></a>Funções de coleção  

Estas funções funcionam sobre as coleções e geralmente aplicam-se às matrizes, cadeias e, por vezes, dicionários.  
  
|Nome de função|Descrição|  
|-------------------|-----------------|  
|contém|Devolve VERDADEIRO se o dicionário contém uma lista de chaves, contém um valor ou a cadeia contém subcadeia. Por exemplo, esta função devolve `true`: <p>`contains('abacaba','aca')` <p> **Número de parâmetro**: 1 <p> **Nome**: dentro da coleção <p> **Descrição**: necessário. A coleção para pesquisar no. <p> **Número de parâmetro**: 2 <p> **Nome**: encontrar o objeto <p> **Descrição**: necessário. O objeto localizar dentro de **dentro da coleção**.|  
|comprimento|Devolve o número de elementos de uma cadeia ou matriz. Por exemplo, esta função devolve `3`:  <p>`length('abc')` <p> **Número de parâmetro**: 1 <p> **Nome**: coleção <p> **Descrição**: necessário. A coleção para o qual pretende obter o comprimento.|  
|vazio|Devolve true se o objeto, cadeia ou matriz está vazia. Por exemplo, esta função devolve `true`: <p>`empty('')` <p> **Número de parâmetro**: 1 <p> **Nome**: coleção <p> **Descrição**: necessário. A coleção para verificar se está vazia.|  
|intersecção|Devolve uma única matriz nem um objeto que tenha elementos comuns entre as matrizes ou objetos transmitidos. Por exemplo, esta função devolve `[1, 2]`: <p>`intersection([1, 2, 3], [101, 2, 1, 10],[6, 8, 1, 2])` <p>Os parâmetros para a função podem ser um conjunto de objetos ou um conjunto de matrizes (não uma combinação de ambos). Se existirem dois objetos com o mesmo nome, o objeto de última com esse nome é apresentado no objeto final. <p> **Número de parâmetro**: 1...*n* <p> **Nome**: coleção*n* <p> **Descrição**: necessário. As coleções a avaliar. Um objeto tem de ser transmitidas nas aparecer nos resultados de todas as coleções.|  
|União|Devolve uma matriz única ou o objeto com todos os elementos na matriz ou objeto transmitido para esta função. Por exemplo, esta função devolve `[1, 2, 3, 10, 101]`: <p>`union([1, 2, 3], [101, 2, 1, 10])` <p>Os parâmetros para a função podem ser um conjunto de objetos ou um conjunto de matrizes (não uma mistura de ambos). Se existirem dois objetos com o mesmo nome no resultado final, o objeto de última com esse nome é apresentado no objeto final. <p> **Número de parâmetro**: 1...*n* <p> **Nome**: coleção*n* <p> **Descrição**: necessário. As coleções a avaliar. Um objeto que é apresentado em qualquer uma das coleções também é apresentada nos resultados.|  
|primeiro|Devolve o primeiro elemento da matriz ou a cadeia transmitida. Por exemplo, esta função devolve `0`: <p>`first([0,2,3])` <p> **Número de parâmetro**: 1 <p> **Nome**: coleção <p> **Descrição**: necessário. A coleção para tirar o objeto do primeiro.|  
|última|Devolve o último elemento na matriz ou cadeia transmitida. Por exemplo, esta função devolve `3`: <p>`last('0123')` <p> **Número de parâmetro**: 1 <p> **Nome**: coleção <p> **Descrição**: necessário. A coleção para tirar o objeto de última.|  
|tirar|Devolve o primeiro **contagem** transmitido elementos da matriz ou cadeia. Por exemplo, esta função devolve `[1, 2]`:  <p>`take([1, 2, 3, 4], 2)` <p> **Número de parâmetro**: 1 <p> **Nome**: coleção <p> **Descrição**: necessário. A coleção a partir de onde tirar o primeiro **contagem** objetos. <p> **Número de parâmetro**: 2 <p> **Nome**: contagem <p> **Descrição**: necessário. O número de objetos a demorar entre o **coleção**. Tem de ser um número inteiro positivo.|  
|Ignorar|Devolve os elementos na matriz que começa no índice **contagem**. Por exemplo, esta função devolve `[3, 4]`: <p>`skip([1, 2 ,3 ,4], 2)` <p> **Número de parâmetro**: 1 <p> **Nome**: coleção <p> **Descrição**: necessário. A coleção para ignorar a primeira **contagem** objetos de. <p> **Número de parâmetro**: 2 <p> **Nome**: contagem <p> **Descrição**: necessário. O número de objetos para remover o início de **coleção**. Tem de ser um número inteiro positivo.|  
|aderir|Devolve uma cadeia com cada item de uma matriz associada por um delimitador, por exemplo devolve `"1,2,3,4"`:<br /><br /> `join([1, 2, 3, 4], ',')`<br /><br /> **Número de parâmetro**: 1<br /><br /> **Nome**: coleção<br /><br /> **Descrição**: necessário. A coleção para associar os itens do.<br /><br /> **Número de parâmetro**: 2<br /><br /> **Nome**: delimitador<br /><br /> **Descrição**: necessário. A cadeia para delimitar itens.|  
  
### <a name="string-functions"></a>Funções de cadeia

As seguintes funções só se aplicam a cadeias. Também pode utilizar algumas funções de coleção em cadeias.  
  
|Nome de função|Descrição|  
|-------------------|-----------------|  
|concat|Combina qualquer número de cadeias em conjunto. Por exemplo, se o parâmetro 1 é `p1`, esta função devolve `somevalue-p1-somevalue`: <p>`concat('somevalue-',parameters('parameter1'),'-somevalue')` <p> **Número de parâmetro**: 1...*n* <p> **Nome**: cadeia*n* <p> **Descrição**: necessário. As cadeias de combinar numa cadeia única.|  
|subcadeia|Devolve um subconjunto de carateres a partir de uma cadeia. Por exemplo, esta função devolve `abc`: <p>`substring('somevalue-abc-somevalue',10,3)` <p> **Número de parâmetro**: 1 <p> **Nome**: cadeia <p> **Descrição**: necessário. A cadeia a partir da qual foi efetuada a subcadeia. <p> **Número de parâmetro**: 2 <p> **Nome**: índice inicial <p> **Descrição**: necessário. O índice de onde a subcadeia começa no parâmetro 1. <p> **Número de parâmetro**: 3 <p> **Nome**: comprimento <p> **Descrição**: necessário. O comprimento da subcadeia.|  
|Substituir|Substitui uma cadeia com uma determinada cadeia. Por exemplo, esta função devolve `the new string`: <p>`replace('the old string', 'old', 'new')` <p> **Número de parâmetro**: 1 <p> **Nome**: cadeia <p> **Descrição**: necessário. A cadeia é procurada para o parâmetro 2 e atualizada com o parâmetro 3, quando o parâmetro 2 encontra-se no parâmetro 1. <p> **Número de parâmetro**: 2 <p> **Nome**: cadeia antiga <p> **Descrição**: necessário. A cadeia de substituir o parâmetro 3, quando é encontrada uma correspondência no parâmetro 1 <p> **Número de parâmetro**: 3 <p> **Nome**: nova cadeia <p> **Descrição**: necessário. A cadeia é utilizada para substituir a cadeia no parâmetro 2 quando é encontrada uma correspondência no parâmetro 1.|  
|GUID|Esta função gera uma cadeia exclusiva global (GUID). Por exemplo, esta função pode gerar este GUID:`c2ecc88d-88c8-4096-912c-d6f2e2b138ce` <p>`guid()` <p> **Número de parâmetro**: 1 <p> **Nome**: formato <p> **Descrição**: opcional. Um especificador de formato único indica [como formatar o valor deste Guid](https://msdn.microsoft.com/library/97af8hh4%28v=vs.110%29.aspx). O parâmetro de formato pode ser "N", "D", "B", "P" ou "X". Se o formato não for fornecido, "D" é utilizada.|  
|toLower|Converte uma cadeia em minúsculas. Por exemplo, esta função devolve `two by two is four`: <p>`toLower('Two by Two is Four')` <p> **Número de parâmetro**: 1 <p> **Nome**: cadeia <p> **Descrição**: necessário. A cadeia a converter para maiúsculas e minúsculas inferior. Se um carácter na cadeia de não tiver um equivalente em minúsculas, o caráter está incluído o inalterados na cadeia devolvida.|  
|toUpper|Converte uma cadeia em maiúsculas. Por exemplo, esta função devolve `TWO BY TWO IS FOUR`: <p>`toUpper('Two by Two is Four')` <p> **Número de parâmetro**: 1 <p> **Nome**: cadeia <p> **Descrição**: necessário. A cadeia a converter para maiúsculas e minúsculas superior. Se um carácter na cadeia de não tiver um equivalente em maiúsculas, caráter está incluído inalterados na cadeia devolvida.|  
|indexOf|Localize o índice de um valor dentro de um caso de cadeia insensitively. Por exemplo, esta função devolve `7`: <p>`indexof('hello, world.', 'world')` <p> **Número de parâmetro**: 1 <p> **Nome**: cadeia <p> **Descrição**: necessário. A cadeia que pode conter o valor. <p> **Número de parâmetro**: 2 <p> **Nome**: cadeia <p> **Descrição**: necessário. O valor a pesquisar o índice da.|  
|lastindexof|Localize o índice de um valor dentro de um caso de cadeia último insensitively. Por exemplo, esta função devolve `3`: <p>`lastindexof('foofoo', 'foo')` <p> **Número de parâmetro**: 1 <p> **Nome**: cadeia <p> **Descrição**: necessário. A cadeia que pode conter o valor. <p> **Número de parâmetro**: 2 <p> **Nome**: cadeia <p> **Descrição**: necessário. O valor a pesquisar o índice da.|  
|startswith|Verifica se a cadeia começa com um caso de valor insensitively. Por exemplo, esta função devolve `true`: <p>`startswith('hello, world', 'hello')` <p> **Número de parâmetro**: 1 <p> **Nome**: cadeia <p> **Descrição**: necessário. A cadeia que pode conter o valor. <p> **Número de parâmetro**: 2 <p> **Nome**: cadeia <p> **Descrição**: necessário. O valor de cadeia pode começar a utilizar.|  
|endswith|Verifica se a cadeia termina com um caso de valor insensitively. Por exemplo, esta função devolve `true`: <p>`endswith('hello, world', 'world')` <p> **Número de parâmetro**: 1 <p> **Nome**: cadeia <p> **Descrição**: necessário. A cadeia que pode conter o valor. <p> **Número de parâmetro**: 2 <p> **Nome**: cadeia <p> **Descrição**: necessário. O valor de cadeia pode terminar com.|  
|dividir|Divide a cadeia com um separador. Por exemplo, esta função devolve `["a", "b", "c"]`: <p>`split('a;b;c',';')` <p> **Número de parâmetro**: 1 <p> **Nome**: cadeia <p> **Descrição**: necessário. A cadeia que é dividida. <p> **Número de parâmetro**: 2 <p> **Nome**: cadeia <p> **Descrição**: necessário. O separador.|  
  
### <a name="logical-functions"></a>Funções lógicas  

Estas funções são úteis no interior de condições e podem ser utilizadas para avaliar a qualquer tipo de lógica.  
  
|Nome de função|Descrição|  
|-------------------|-----------------|  
|igual a|Devolve true se dois valores são iguais. Por exemplo, se parameter1 someValue, esta função devolve `true`: <p>`equals(parameters('parameter1'), 'someValue')` <p> **Número de parâmetro**: 1 <p> **Nome**: 1 de objeto <p> **Descrição**: necessário. O objeto comparar para **objeto 2**. <p> **Número de parâmetro**: 2 <p> **Nome**: objeto 2 <p> **Descrição**: necessário. O objeto comparar para **objeto 1**.|  
|menor|Devolve VERDADEIRO se o primeiro argumento for menor do que o segundo. Tenha em atenção de que os valores só podem ser do tipo número inteiro, flutuante ou uma cadeia. Por exemplo, esta função devolve `true`: <p>`less(10,100)` <p> **Número de parâmetro**: 1 <p> **Nome**: 1 de objeto <p> **Descrição**: necessário. O objeto para verificar se é inferior a **objeto 2**. <p> **Número de parâmetro**: 2 <p> **Nome**: objeto 2 <p> **Descrição**: necessário. O objeto para verificar se é superior ao **objeto 1**.|  
|lessOrEquals|Devolve true se o primeiro argumento é menor ou igual ao segundo. Tenha em atenção de que os valores só podem ser do tipo número inteiro, flutuante ou uma cadeia. Por exemplo, esta função devolve `true`: <p>`lessOrEquals(10,10)` <p> **Número de parâmetro**: 1 <p> **Nome**: 1 de objeto <p> **Descrição**: necessário. O objeto para verificar se é menor ou igual a **objeto 2**. <p> **Número de parâmetro**: 2 <p> **Nome**: objeto 2 <p> **Descrição**: necessário. O objeto para verificar se é maior que ou igual a **objeto 1**.|  
|maior|Devolve true se o primeiro argumento é maior do que o segundo. Tenha em atenção de que os valores só podem ser do tipo número inteiro, flutuante ou uma cadeia. Por exemplo, esta função devolve `false`:  <p>`greater(10,10)` <p> **Número de parâmetro**: 1 <p> **Nome**: 1 de objeto <p> **Descrição**: necessário. O objeto para verificar se é superior ao **objeto 2**. <p> **Número de parâmetro**: 2 <p> **Nome**: objeto 2 <p> **Descrição**: necessário. O objeto para verificar se é inferior a **objeto 1**.|  
|greaterOrEquals|Devolve true se o primeiro argumento é igual ou superior para o segundo. Tenha em atenção de que os valores só podem ser do tipo número inteiro, flutuante ou uma cadeia. Por exemplo, esta função devolve `false`: <p>`greaterOrEquals(10,100)` <p> **Número de parâmetro**: 1 <p> **Nome**: 1 de objeto <p> **Descrição**: necessário. O objeto para verificar se é maior que ou igual a **objeto 2**. <p> **Número de parâmetro**: 2 <p> **Nome**: objeto 2 <p> **Descrição**: necessário. O objeto para verificar se é menor ou igual a **objeto 1**.|  
|e|Devolve true se ambos os parâmetros são verdadeiras. Ambos os argumentos tem de ser em booleanos. Por exemplo, esta função devolve `false`: <p>`and(greater(1,10),equals(0,0))` <p> **Número de parâmetro**: 1 <p> **Nome**: 1 booleano <p> **Descrição**: necessário. O primeiro argumento tem de ser `true`. <p> **Número de parâmetro**: 2 <p> **Nome**: 2 booleano <p> **Descrição**: necessário. O segundo argumento tem de ser `true`.|  
|ou|Devolve VERDADEIRO se o parâmetro for VERDADEIRO. Ambos os argumentos tem de ser em booleanos. Por exemplo, esta função devolve `true`: <p>`or(greater(1,10),equals(0,0))` <p> **Número de parâmetro**: 1 <p> **Nome**: 1 booleano <p> **Descrição**: necessário. O primeiro argumento que pode ser `true`. <p> **Número de parâmetro**: 2 <p> **Nome**: 2 booleano <p> **Descrição**: necessário. O segundo argumento pode ser `true`.|  
|não|Devolve true se os parâmetros são `false`. Ambos os argumentos tem de ser em booleanos. Por exemplo, esta função devolve `true`: <p>`not(contains('200 Success','Fail'))` <p> **Número de parâmetro**: 1 <p> **Nome**: booleano <p> **Descrição**: devolve true se os parâmetros são `false`. Ambos os argumentos tem de ser em booleanos. Esta função devolve `true`:`not(contains('200 Success','Fail'))`|  
|Se|Devolve um valor especificado com base em se a expressão resultou numa `true` ou `false`.  Por exemplo, esta função devolve `"yes"`: <p>`if(equals(1, 1), 'yes', 'no')` <p> **Número de parâmetro**: 1 <p> **Nome**: expressão <p> **Descrição**: necessário. Um valor booleano que determina qual o valor de expressão deve ser devolvido. <p> **Número de parâmetro**: 2 <p> **Nome**: VERDADEIRO <p> **Descrição**: necessário. O valor a devolver se a expressão é `true`. <p> **Número de parâmetro**: 3 <p> **Nome**: False <p> **Descrição**: necessário. O valor a devolver se a expressão é `false`.|  
  
### <a name="conversion-functions"></a>Funções de conversão  

Estas funções são utilizadas para a conversão entre cada um dos tipos nativos no idioma:  
  
- Cadeia  
  
- número inteiro  
  
- Número de vírgula flutuante  
  
- Valor booleano  
  
- Matrizes  
  
- dicionários  

-   Formulários  
  
|Nome de função|Descrição|  
|-------------------|-----------------|  
|Int|Converta o parâmetro para um número inteiro. Por exemplo, esta função devolve 100 como um número, em vez de uma cadeia: <p>`int('100')` <p> **Número de parâmetro**: 1 <p> **Nome**: valor <p> **Descrição**: necessário. O valor que é convertido para um número inteiro.|  
|Cadeia|Converta o parâmetro numa cadeia. Por exemplo, esta função devolve `'10'`: <p>`string(10)` <p>Também pode converter um objeto numa cadeia. Por exemplo, se o `myPar` parâmetro é um objeto com uma propriedade `abc : xyz`, em seguida, esta função devolve `{"abc" : "xyz"}`: <p>`string(parameters('myPar'))` <p> **Número de parâmetro**: 1 <p> **Nome**: valor <p> **Descrição**: necessário. O valor que é convertido numa cadeia.|  
|JSON|Converter o parâmetro para um valor de tipo JSON e é o oposto da `string()`. Por exemplo, esta função devolve `[1,2,3]` como uma matriz, em vez de uma cadeia: <p>`json('[1,2,3]')` <p>Da mesma forma, pode converter uma cadeia para um objeto. Por exemplo, esta função devolve `{ "abc" : "xyz" }`: <p>`json('{"abc" : "xyz"}')` <p> **Número de parâmetro**: 1 <p> **Nome**: cadeia <p> **Descrição**: necessário. A cadeia que é convertida para um valor de tipo nativo. <p>O `json()` função suporta XML de entrada demasiado. Por exemplo, o valor do parâmetro: <p>`<?xml version="1.0"?> <root>   <person id='1'>     <name>Alan</name>     <occupation>Engineer</occupation>   </person> </root>` <p>é convertido para este JSON: <p>`{ "?xml": { "@version": "1.0" },   "root": {     "person": [     {       "@id": "1",       "name": "Alan",       "occupation": "Engineer"     }   ]   } }`|  
|Número de vírgula flutuante|Converta o argumento de parâmetro para um número de vírgula flutuante. Por exemplo, esta função devolve `10.333`: <p>`float('10.333')` <p> **Número de parâmetro**: 1 <p> **Nome**: valor <p> **Descrição**: necessário. O valor que é convertido para um número de vírgula flutuante.|  
|bool|Converta o parâmetro booleano. Por exemplo, esta função devolve `false`: <p>`bool(0)` <p> **Número de parâmetro**: 1 <p> **Nome**: valor <p> **Descrição**: necessário. O valor que é convertido para um valor booleano.|  
|Base64|Devolve a representação de base64 de cadeia de entrada. Por exemplo, esta função devolve `c29tZSBzdHJpbmc=`: <p>`base64('some string')` <p> **Número de parâmetro**: 1 <p> **Nome**: 1 de cadeia <p> **Descrição**: necessário. A cadeia de codificar para base64 representação.|  
|base64ToBinary|Devolve uma representação binária de uma cadeia codificada em base64. Por exemplo, esta função devolve a representação binária do `some string`: <p>`base64ToBinary('c29tZSBzdHJpbmc=')` <p> **Número de parâmetro**: 1 <p> **Nome**: cadeia <p> **Descrição**: necessário. A cadeia codificada em base64.|  
|base64ToString|Devolve uma representação de cadeia de uma cadeia de based64 codificado. Por exemplo, esta função devolve `some string`: <p>`base64ToString('c29tZSBzdHJpbmc=')` <p> **Número de parâmetro**: 1 <p> **Nome**: cadeia <p> **Descrição**: necessário. A cadeia codificada em base64.|  
|Binário|Devolve uma representação de um valor binária.  Por exemplo, esta função devolve uma representação binária do `some string`: <p>`binary('some string')` <p> **Número de parâmetro**: 1 <p> **Nome**: valor <p> **Descrição**: necessário. O valor que é convertido em binário.|  
|dataUriToBinary|Devolve uma representação binária de um URI de dados. Por exemplo, esta função devolve a representação binária do `some string`: <p>`dataUriToBinary('data:;base64,c29tZSBzdHJpbmc=')` <p> **Número de parâmetro**: 1 <p> **Nome**: cadeia <p> **Descrição**: necessário. O URI para converter para representação binária de dados.|  
|dataUriToString|Devolve uma representação de cadeia de um URI de dados. Por exemplo, esta função devolve `some string`: <p>`dataUriToString('data:;base64,c29tZSBzdHJpbmc=')` <p> **Número de parâmetro**: 1 <p> **Nome**: cadeia<p> **Descrição**: necessário. O URI para converter para representação de cadeia de dados.|  
|dataUri|Devolve um URI de um valor de dados. Por exemplo, esta função devolve dados URI `text/plain;charset=utf8;base64,c29tZSBzdHJpbmc=`: <p>`dataUri('some string')` <p> **Número de parâmetro**: 1<p> **Nome**: valor<p> **Descrição**: necessário. O valor para converter para o URI de dados.|  
|decodeBase64|Devolve uma representação de cadeia de uma cadeia de entrada based64. Por exemplo, esta função devolve `some string`: <p>`decodeBase64('c29tZSBzdHJpbmc=')` <p> **Número de parâmetro**: 1 <p> **Nome**: cadeia <p> **Descrição**: devolve uma representação de cadeia de uma cadeia de entrada based64.|  
|encodeUriComponent|URL escapes a cadeia que é transmitida no. Por exemplo, esta função devolve `You+Are%3ACool%2FAwesome`: <p>`encodeUriComponent('You Are:Cool/Awesome')` <p> **Número de parâmetro**: 1 <p> **Nome**: cadeia <p> **Descrição**: necessário. A cadeia para o escape carateres não seguro URL do.|  
|decodeUriComponent|Anular-URL-escapes a cadeia que é transmitida no. Por exemplo, esta função devolve `You Are:Cool/Awesome`: <p>`encodeUriComponent('You+Are%3ACool%2FAwesome')` <p> **Número de parâmetro**: 1 <p> **Nome**: cadeia <p> **Descrição**: necessário. A cadeia de descodificar os carateres não seguro URL do.|  
|decodeDataUri|Devolve uma representação binária de dados de entrada, cadeia URI. Por exemplo, esta função devolve a representação binária do `some string`: <p>`decodeDataUri('data:;base64,c29tZSBzdHJpbmc=')` <p> **Número de parâmetro**: 1 <p> **Nome**: cadeia <p> **Descrição**: necessário. DataURI descodificar para uma representação binária.|  
|uriComponent|Devolve um URI codificado representação de um valor. Por exemplo, esta função devolve `You+Are%3ACool%2FAwesome`: <p>`uriComponent('You Are:Cool/Awesome')` <p> **Número de parâmetro**: 1<p> **Nome**: cadeia <p> **Descrição**: necessário. A cadeia a ser URI codificado.|  
|uriComponentToBinary|Devolve que uma representação binária de um URI cadeia codificada. Por exemplo, esta função devolve uma representação binária do `You Are:Cool/Awesome`: <p>`uriComponentToBinary('You+Are%3ACool%2FAwesome')` <p> **Número de parâmetro**: 1 <p> **Nome**: cadeia<p> **Descrição**: necessário. A cadeia URI codificado.|  
|uriComponentToString|Devolve que uma representação de cadeia de um URI cadeia codificada. Por exemplo, esta função devolve `You Are:Cool/Awesome`: <p>`uriComponentToBinary('You+Are%3ACool%2FAwesome')` <p> **Número de parâmetro**: 1<p> **Nome**: cadeia<p> **Descrição**: necessário. A cadeia URI codificado.|  
|xml|Devolve uma representação XML do valor. Por exemplo, esta função devolve XML conteúdo representado pelo `'\<name>Alan\</name>'`: <p>`xml('\<name>Alan\</name>')` <p>O `xml()` função suporta um objeto JSON de entrada demasiado. Por exemplo, o parâmetro `{ "abc": "xyz" }` é convertida em conteúdo XML:`\<abc>xyz\</abc>` <p> **Número de parâmetro**: 1<p> **Nome**: valor<p> **Descrição**: necessário. O valor a converter XML.|  
|Matriz|Converta o parâmetro de uma matriz. Por exemplo, esta função devolve `["abc"]`: <p>`array('abc')` <p> **Número de parâmetro**: 1 <p> **Nome**: valor <p> **Descrição**: necessário. O valor que é convertido numa matriz.|
|createArray|Cria uma matriz de parâmetros. Por exemplo, esta função devolve `["a", "c"]`: <p>`createArray('a', 'c')` <p> **Número de parâmetro**: 1...*n* <p> **Nome**: nenhuma*n* <p> **Descrição**: necessário. Os valores para combinar numa matriz.|
|triggerFormDataValue|Devolve um valor único correspondente ao nome da chave de dados do formulário ou saída de Acionador de formulário com codificação.  Se existirem vários corresponde-será erro.  Por exemplo, o seguinte irá devolver `bar`:`triggerFormDataValue('foo')`<br /><br />**Número de parâmetro**: 1<br /><br />**Nome**: nome da chave<br /><br />**Descrição**: necessário. O nome da chave do valor de dados do formulário para devolver.|
|triggerFormDataMultiValues|Devolve uma matriz de valores correspondentes o nome da chave de dados do formulário ou saída de Acionador de formulário com codificação.  Por exemplo, o seguinte irá devolver `["bar"]`:`triggerFormDataValue('foo')`<br /><br />**Número de parâmetro**: 1<br /><br />**Nome**: nome da chave<br /><br />**Descrição**: necessário. O nome da chave dos formato de valores de dados para devolver.|
|triggerMultipartBody|Devolve o corpo de uma parte de uma saída de várias partes do acionador.<br /><br />**Número de parâmetro**: 1<br /><br />**Nome**: índice<br /><br />**Descrição**: necessário. O índice da peça a obter.|
|formDataValue|Devolve um valor único correspondente ao nome da chave de dados do formulário ou saída de ação de formulário com codificação.  Se existirem vários corresponde-será erro.  Por exemplo, o seguinte irá devolver `bar`:`formDataValue('someAction', 'foo')`<br /><br />**Número de parâmetro**: 1<br /><br />**Nome**: nome da ação<br /><br />**Descrição**: necessário. O nome da ação com um dados do formulário ou a resposta de formulário com codificação.<br /><br />**Número de parâmetro**: 2<br /><br />**Nome**: nome da chave<br /><br />**Descrição**: necessário. O nome da chave do valor de dados do formulário para devolver.|
|formDataMultiValues|Devolve uma matriz de valores correspondentes o nome da chave de dados do formulário ou saída de ação de formulário com codificação.  Por exemplo, o seguinte irá devolver `["bar"]`:`formDataMultiValues('someAction', 'foo')`<br /><br />**Número de parâmetro**: 1<br /><br />**Nome**: nome da ação<br /><br />**Descrição**: necessário. O nome da ação com um dados do formulário ou a resposta de formulário com codificação.<br /><br />**Número de parâmetro**: 2<br /><br />**Nome**: nome da chave<br /><br />**Descrição**: necessário. O nome da chave dos formato de valores de dados para devolver.|
|multipartBody|Devolve o corpo de uma parte de uma saída de várias partes de uma ação.<br /><br />**Número de parâmetro**: 1<br /><br />**Nome**: nome da ação<br /><br />**Descrição**: necessário. O nome da ação com uma resposta com várias partes.<br /><br />**Número de parâmetro**: 2<br /><br />**Nome**: índice<br /><br />**Descrição**: necessário. O índice da peça a obter.|

### <a name="manipulation-functions"></a>Funções de manipulação
 
Estas funções se aplicam ao XML e objetos.
 
|Nome de função|Descrição|  
|-------------------|-----------------| 
|Unir|Devolve o objeto não nulo primeiro os argumentos transmitidos. **Tenha em atenção**: uma cadeia vazia não é nula. Por exemplo, se não for definidos um parâmetros 1 e 2, esta função devolve `fallback`:  <p>`coalesce(parameters('parameter1'), parameters('parameter2') ,'fallback')` <p> **Número de parâmetro**: 1...*n* <p> **Nome**: objeto*n* <p> **Descrição**: necessário. Os objetos para procurar um valor nulo.|
|addProperty|Devolve um objeto com uma propriedade adicional. Se a propriedade já existe no tempo de execução será emitido um erro. Por exemplo, esta função devolve o objeto `{ "abc" : "xyz", "def": "uvw" }`: <p>`addProperty(json('{"abc" : "xyz"}'), 'def', 'uvw')` <p> **Número de parâmetro**: 1 <p> **Nome**: objeto <p> **Descrição**: necessário. O objeto a adicionar uma nova propriedade para. <p> **Número de parâmetro**: 2 <p> **Nome**: nome da propriedade <p> **Descrição**: necessário. O nome da nova propriedade. <p> **Número de parâmetro**: 3 <p> **Nome**: valor <p> **Descrição**: necessário. O valor para atribuir a nova propriedade.|
|setProperty|Devolve um objeto com uma propriedade adicional ou uma propriedade existente, defina o valor especificado. Por exemplo, esta função devolve o objeto `{ "abc" : "uvw" }`: <p>`setProperty(json('{"abc" : "xyz"}'), 'abc', 'uvw')` <p> **Número de parâmetro**: 1 <p> **Nome**: objeto <p> **Descrição**: necessário. O objeto no qual pretende definir a propriedade.<p> **Número de parâmetro**: 2 <p> **Nome**: nome da propriedade<p> **Descrição**: necessário. O nome da propriedade novo ou existente. <p> **Número de parâmetro**: 3 <p> **Nome**: valor <p> **Descrição**: necessário. O valor a atribuir à propriedade.|
|removeProperty|Devolve um objeto com uma propriedade foi removida. Se não existe propriedade para remover o objeto original é devolvido. Por exemplo, esta função devolve o objeto `{ "abc" : "xyz" }`: <p>`removeProperty(json('{"abc" : "xyz", "def": "uvw"}'), 'def')` <p> **Número de parâmetro**: 1 <p> **Nome**: objeto <p> **Descrição**: necessário. O objeto para remover a propriedade do.<p> **Número de parâmetro**: 2 <p> **Nome**: nome da propriedade <p> **Descrição**: necessário. O nome da propriedade para remover. <p>|
|XPath|Devolva uma matriz de correspondência de expressão de xpath de um valor que avalia a expressão de xpath para nós XML. <p> **Exemplo 1** <p>Assumir o valor do parâmetro `p1` é uma representação de cadeia deste XML: <p>`<?xml version="1.0"?> <lab>   <robot>     <parts>5</parts>     <name>R1</name>   </robot>   <robot>     <parts>8</parts>     <name>R2</name>   </robot> </lab>` <p>Este código:`xpath(xml(parameters('p1')), '/lab/robot/name')` <p>Devolve <p>`[ <name>R1</name>, <name>R2</name> ]` <p>durante este código: <p>`xpath(xml(parameters('p1')), ' sum(/lab/robot/parts)')` <p>Devolve <p>`13` <p> <p> **Exemplo 2** <p>Dado o seguinte conteúdo XML: <p>`<?xml version="1.0"?> <File xmlns="http://foo.com">   <Location>bar</Location> </File>` <p>Este código:`@xpath(xml(body('Http')), '/*[name()=\"File\"]/*[name()=\"Location\"]')` <p>ou este código: <p>`@xpath(xml(body('Http')), '/*[local-name()=\"File\" and namespace-uri()=\"http://foo.com\"]/*[local-name()=\"Location\" and namespace-uri()=\"\"]')` <p>Devolve <p>`<Location xmlns="http://abc.com">xyz</Location>` <p>E este código:`@xpath(xml(body('Http')), 'string(/*[name()=\"File\"]/*[name()=\"Location\"])')` <p>Devolve <p>``xyz`` <p> **Número de parâmetro**: 1 <p> **Nome**: Xml <p> **Descrição**: necessário. O XML no qual se avalia a expressão XPath. <p> **Número de parâmetro**: 2 <p> **Nome**: XPath <p> **Descrição**: necessário. A expressão de XPath para avaliar.|

### <a name="math-functions"></a>Funções de bibliotecas  

Estas funções podem ser utilizadas para qualquer um dos tipos de números: **números inteiros** e **floats**.  
  
|Nome de função|Descrição|  
|-------------------|-----------------|  
|Adicionar|Devolve o resultado da adição de dois números. Por exemplo, esta função devolve `20.333`: <p>`add(10,10.333)` <p> **Número de parâmetro**: 1 <p> **Nome**: Summand 1 <p> **Descrição**: necessário. O número para adicionar a **Summand 2**. <p> **Número de parâmetro**: 2 <p> **Nome**: Summand 2 <p> **Descrição**: necessário. O número para adicionar a **Summand 1**.|  
|sub|Devolve o resultado de subtraindo dois números. Por exemplo, esta função devolve `-0.333`: <p>`sub(10,10.333)` <p> **Número de parâmetro**: 1 <p> **Nome**: Minuend <p> **Descrição**: necessário. O número que **Subtrahend** é removido. <p> **Número de parâmetro**: 2 <p> **Nome**: Subtrahend <p> **Descrição**: necessário. O número para remover o **Minuend**.|  
|MUL|Devolve o resultado de multiplicando dois números. Por exemplo, esta função devolve `103.33`: <p>`mul(10,10.333)` <p> **Número de parâmetro**: 1 <p> **Nome**: Multiplicand 1 <p> **Descrição**: necessário. O número a multiplicar **Multiplicand 2** com. <p> **Número de parâmetro**: 2 <p> **Nome**: Multiplicand 2 <p> **Descrição**: necessário. O número a multiplicar **Multiplicand 1** com.|  
|Div|Devolve o resultado da divisão de dois números. Por exemplo, esta função devolve `1.0333`: <p>`div(10.333,10)` <p> **Número de parâmetro**: 1 <p> **Nome**: dividendo <p> **Descrição**: necessário. O número dividir pelo **Divisor**. <p> **Número de parâmetro**: 2 <p> **Nome**: Divisor <p> **Descrição**: necessário. O número dividir o **dividendo** pelo.|  
|MOD|Devolve o resto após a divisão de dois números (módulo). Por exemplo, esta função devolve `2`: <p>`mod(10,4)` <p> **Número de parâmetro**: 1 <p> **Nome**: dividendo <p> **Descrição**: necessário. O número dividir pelo **Divisor**. <p> **Número de parâmetro**: 2 <p> **Nome**: Divisor <p> **Descrição**: necessário. O número dividir o **dividendo** pelo. Após a divisão, os restantes foi atribuído.|  
|min.|Existem dois padrões diferentes para chamar esta função. <p>Aqui `min` demora uma matriz e a função devolve `0`: <p>`min([0,1,2])` <p>Em alternativa, esta função pode demorar uma lista separada por vírgulas de valores e também devolve `0`: <p>`min(0,1,2)` <p> **Tenha em atenção**: todos os valores têm de ser números, pelo que o se o parâmetro é uma matriz, a matriz tem de conter apenas números. <p> **Número de parâmetro**: 1 <p> **Nome**: ou valor de coleção <p> **Descrição**: necessário. É uma matriz de valores para localizar o valor mínimo ou o primeiro valor de um conjunto. <p> **Número de parâmetro**: 2...*n* <p> **Nome**: valor*n* <p> **Descrição**: opcional. Se o primeiro parâmetro é um valor, em seguida, pode passar valores adicionais e é devolvido o valor mínimo de todos os valores transmitidos.|  
|Máx.|Existem dois padrões diferentes para chamar esta função. <p>Aqui `max` demora uma matriz e a função devolve `2`: <p>`max([0,1,2])` <p>Em alternativa, esta função pode demorar uma lista separada por vírgulas de valores e também devolve `2`: <p>`max(0,1,2)` <p> **Tenha em atenção**: todos os valores têm de ser números, pelo que o se o parâmetro é uma matriz, a matriz tem de conter apenas números. <p> **Número de parâmetro**: 1 <p> **Nome**: ou valor de coleção <p> **Descrição**: necessário. É uma matriz de valores para encontrar o valor máximo ou o primeiro valor de um conjunto. <p> **Número de parâmetro**: 2...*n* <p> **Nome**: valor*n* <p> **Descrição**: opcional. Se o primeiro parâmetro é um valor, em seguida, pode passar valores adicionais e o número máximo de todos os valores transmitidos é devolvido.|  
|intervalo|Gera uma matriz de números inteiros a partir de um determinado número. Definir o comprimento da matriz devolvido. <p>Por exemplo, esta função devolve `[3,4,5,6]`: <p>`range(3,4)` <p> **Número de parâmetro**: 1 <p> **Nome**: índice inicial <p> **Descrição**: necessário. O primeiro número inteiro na matriz. <p> **Número de parâmetro**: 2 <p> **Nome**: contagem <p> **Descrição**: necessário. Este valor é o número de números inteiros que se encontra na matriz.|  
|rand|Gera um número inteiro aleatório dentro do intervalo especificado (inclusive apenas no final primeiro). Por exemplo, esta função pode devolver um `0` ou '1': <p>`rand(0,2)` <p> **Número de parâmetro**: 1 <p> **Nome**: mínimo <p> **Descrição**: necessário. O número inteiro menor que pode ser devolvido. <p> **Número de parâmetro**: 2 <p> **Nome**: máximo <p> **Descrição**: necessário. Este valor é o número inteiro seguinte após o maior número inteiro que pode ser devolvido.|  
 
### <a name="date-functions"></a>Funções de data  

|Nome de função|Descrição|  
|-------------------|-----------------|  
|utcnow|Devolve o timestamp atual como uma cadeia, por exemplo: `2017-03-15T13:27:36Z`: <p>`utcnow()` <p> **Número de parâmetro**: 1 <p> **Nome**: formato <p> **Descrição**: opcional. É um [único caráter especificador de formato](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) ou um [padrão de formato personalizado](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) que indica como formatar o valor deste carimbo. Se o formato não for fornecido, é utilizado o formato ISO 8601 ("o").|  
|addseconds|Adiciona um número inteiro de segundos para um carimbo de cadeia transmitido. O número de segundos pode ser positivo ou negativo. Por predefinição, o resultado é uma cadeia no formato ISO 8601 ("o"), a menos que é fornecido um especificador de formato. Por exemplo: `2015-03-15T13:27:00Z`: <p>`addseconds('2015-03-15T13:27:36Z', -36)` <p> **Número de parâmetro**: 1 <p> **Nome**: Timestamp <p> **Descrição**: necessário. Uma cadeia que contém a hora. <p> **Número de parâmetro**: 2 <p> **Nome**: segundos <p> **Descrição**: necessário. O número de segundos a adicionar. Pode ser negativo a subtrair segundos. <p> **Número de parâmetro**: 3 <p> **Nome**: formato <p> **Descrição**: opcional. É um [único caráter especificador de formato](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) ou um [padrão de formato personalizado](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) que indica como formatar o valor deste carimbo. Se o formato não for fornecido, é utilizado o formato ISO 8601 ("o").|  
|addminutes|Adiciona um número inteiro de minutos para um carimbo de cadeia transmitido. O número de minutos pode ser positivo ou negativo. Por predefinição, o resultado é uma cadeia no formato ISO 8601 ("o"), a menos que é fornecido um especificador de formato. Por exemplo: `2015-03-15T14:00:36Z`: <p>`addminutes('2015-03-15T13:27:36Z', 33)` <p> **Número de parâmetro**: 1 <p> **Nome**: Timestamp <p> **Descrição**: necessário. Uma cadeia que contém a hora. <p> **Número de parâmetro**: 2 <p> **Nome**: minutos <p> **Descrição**: necessário. O número de minutos para adicionar. Pode ser negativo a subtrair minutos. <p> **Número de parâmetro**: 3 <p> **Nome**: formato <p> **Descrição**: opcional. É um [único caráter especificador de formato](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) ou um [padrão de formato personalizado](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) que indica como formatar o valor deste carimbo. Se o formato não for fornecido, é utilizado o formato ISO 8601 ("o").|  
|addhours|Adiciona um número inteiro de horas para um carimbo de cadeia transmitido. O número de horas pode ser positivo ou negativo. Por predefinição, o resultado é uma cadeia no formato ISO 8601 ("o"), a menos que é fornecido um especificador de formato. Por exemplo: `2015-03-16T01:27:36Z`: <p>`addhours('2015-03-15T13:27:36Z', 12)` <p> **Número de parâmetro**: 1 <p> **Nome**: Timestamp <p> **Descrição**: necessário. Uma cadeia que contém a hora. <p> **Número de parâmetro**: 2 <p> **Nome**: horas <p> **Descrição**: necessário. O número de horas para adicionar. Pode ser negativo a subtrair horas. <p> **Número de parâmetro**: 3 <p> **Nome**: formato <p> **Descrição**: opcional. É um [único caráter especificador de formato](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) ou um [padrão de formato personalizado](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) que indica como formatar o valor deste carimbo. Se o formato não for fornecido, é utilizado o formato ISO 8601 ("o").|  
|adddays|Adiciona um número inteiro de dias para um carimbo de cadeia transmitido. O número de dias pode ser positivo ou negativo. Por predefinição, o resultado é uma cadeia no formato ISO 8601 ("o"), a menos que é fornecido um especificador de formato. Por exemplo: `2015-02-23T13:27:36Z`: <p>`adddays('2015-03-15T13:27:36Z', -2)` <p> **Número de parâmetro**: 1 <p> **Nome**: Timestamp <p> **Descrição**: necessário. Uma cadeia que contém a hora. <p> **Número de parâmetro**: 2 <p> **Nome**: dias <p> **Descrição**: necessário. O número de dias a adicionar. Pode ser negativo a subtrair dias. <p> **Número de parâmetro**: 3 <p> **Nome**: formato <p> **Descrição**: opcional. É um [único caráter especificador de formato](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) ou um [padrão de formato personalizado](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) que indica como formatar o valor deste carimbo. Se o formato não for fornecido, é utilizado o formato ISO 8601 ("o").|  
|FormatDateTime|Devolve uma cadeia no formato de data. Por predefinição, o resultado é uma cadeia no formato ISO 8601 ("o"), a menos que é fornecido um especificador de formato. Por exemplo: `2015-02-23T13:27:36Z`: <p>`formatDateTime('2015-03-15T13:27:36Z', 'o')` <p> **Número de parâmetro**: 1 <p> **Nome**: data <p> **Descrição**: necessário. Uma cadeia que contém a data. <p> **Número de parâmetro**: 2 <p> **Nome**: formato <p> **Descrição**: opcional. É um [único caráter especificador de formato](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) ou um [padrão de formato personalizado](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) que indica como formatar o valor deste carimbo. Se o formato não for fornecido, é utilizado o formato ISO 8601 ("o").|  
|startOfHour|Devolve o início da hora para um carimbo de cadeia transmitido. Por exemplo `2017-03-15T13:00:00Z`:<br /><br /> `startOfHour('2017-03-15T13:27:36Z')`<br /><br /> **Número de parâmetro**: 1<br /><br /> **Nome**: Timestamp<br /><br /> **Descrição**: necessário. Esta é uma cadeia que contém a hora.<br /><br />**Número de parâmetro**: 2<br /><br /> **Nome**: formato<br /><br /> **Descrição**: opcional. É um [único caráter especificador de formato](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) ou um [padrão de formato personalizado](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) que indica como formatar o valor deste carimbo. Se o formato não for fornecido, é utilizado o formato ISO 8601 ("o").|  
|startOfDay|Devolve o início do dia para um carimbo de cadeia transmitido. Por exemplo `2017-03-15T00:00:00Z`:<br /><br /> `startOfDay('2017-03-15T13:27:36Z')`<br /><br /> **Número de parâmetro**: 1<br /><br /> **Nome**: Timestamp<br /><br /> **Descrição**: necessário. Esta é uma cadeia que contém a hora.<br /><br />**Número de parâmetro**: 2<br /><br /> **Nome**: formato<br /><br /> **Descrição**: opcional. É um [único caráter especificador de formato](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) ou um [padrão de formato personalizado](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) que indica como formatar o valor deste carimbo. Se o formato não for fornecido, é utilizado o formato ISO 8601 ("o").| 
|startOfMonth|Devolve o início do mês para um carimbo de cadeia transmitido. Por exemplo `2017-03-01T00:00:00Z`:<br /><br /> `startOfMonth('2017-03-15T13:27:36Z')`<br /><br /> **Número de parâmetro**: 1<br /><br /> **Nome**: Timestamp<br /><br /> **Descrição**: necessário. Esta é uma cadeia que contém a hora.<br /><br />**Número de parâmetro**: 2<br /><br /> **Nome**: formato<br /><br /> **Descrição**: opcional. É um [único caráter especificador de formato](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) ou um [padrão de formato personalizado](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) que indica como formatar o valor deste carimbo. Se o formato não for fornecido, é utilizado o formato ISO 8601 ("o").| 
|DayOfWeek|Devolve o dia do componente de semana de um carimbo de cadeia.  Domingo for 0, segunda é 1 e assim sucessivamente. Por exemplo `3`:<br /><br /> `dayOfWeek('2017-03-15T13:27:36Z')`<br /><br /> **Número de parâmetro**: 1<br /><br /> **Nome**: Timestamp<br /><br /> **Descrição**: necessário. Esta é uma cadeia que contém a hora.| 
|DayOfMonth|Devolve o dia do componente de mês de um carimbo de cadeia. Por exemplo `15`:<br /><br /> `dayOfMonth('2017-03-15T13:27:36Z')`<br /><br /> **Número de parâmetro**: 1<br /><br /> **Nome**: Timestamp<br /><br /> **Descrição**: necessário. Esta é uma cadeia que contém a hora.| 
|DayOfYear|Devolve o dia do componente de ano de um carimbo de cadeia. Por exemplo `74`:<br /><br /> `dayOfYear('2017-03-15T13:27:36Z')`<br /><br /> **Número de parâmetro**: 1<br /><br /> **Nome**: Timestamp<br /><br /> **Descrição**: necessário. Esta é uma cadeia que contém a hora.| 
|batimentos|Devolve os batimentos serão propriedade timestamp uma cadeia. Por exemplo `1489603019`:<br /><br /> `ticks('2017-03-15T18:36:59Z')`<br /><br /> **Número de parâmetro**: 1<br /><br /> **Nome**: Timestamp<br /><br /> **Descrição**: necessário. Esta é uma cadeia que contém a hora.| 
  
### <a name="workflow-functions"></a>Funções de fluxo de trabalho  

Estas funções de ajudam a obter informações sobre o fluxo de trabalho em tempo de execução.  
  
|Nome de função|Descrição|  
|-------------------|-----------------|  
|listCallbackUrl|Devolve uma cadeia para a chamada para invocar o acionador ou ação. <p> **Tenha em atenção**: esta função só pode ser utilizada num **httpWebhook** e **apiConnectionWebhook**, não num **manual**, **periodicidade**, **http**, ou **apiConnection**. <p>Por exemplo, o `listCallbackUrl()` funcionar devolve: <p>`https://prod-01.westus.logic.azure.com:443/workflows/1235...ABCD/triggers/manual/run?api-version=2015-08-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=xxx...xxx` |  
|fluxo de trabalho|Esta função fornece todos os detalhes do fluxo de trabalho próprio em tempo de execução. <p> Estão disponíveis propriedades no objecto do fluxo de trabalho: <ul><li>`name`</li><li>`type`</li><li>`id`</li><li>`location`</li><li>`run`</li></ul> <p> O valor da `run` propriedade é um objeto com as seguintes propriedades: <ul><li>`name`</li><li>`type`</li><li>`id`</li></ul> <p>Consulte o [Rest API](http://go.microsoft.com/fwlink/p/?LinkID=525617) para obter detalhes sobre essas propriedades.<p> Por exemplo, para obter o nome da atual executar, utilize o `"@workflow().run.name"` expressão. |

## <a name="next-steps"></a>Passos seguintes

[Ações de fluxo de trabalho e acionadores](logic-apps-workflow-actions-triggers.md)
