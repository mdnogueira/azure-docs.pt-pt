---
title: "Stream Analytics JavaScript definido pelo utilizador as funções do Azure | Microsoft Docs"
description: "Executar consulta avançada mechanics com as funções definidas pelo utilizador de JavaScript"
keywords: "JavaScript, funções, udf definidas pelo utilizador"
services: stream-analytics
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: samacha
ms.openlocfilehash: e8c1c784a598416b478d1430258201053185fdee
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-stream-analytics-javascript-user-defined-functions"></a>Stream Analytics JavaScript definido pelo utilizador as funções do Azure
O Azure Stream Analytics suporta as funções definidas pelo utilizador escritas em JavaScript. Com a avançada conjunto de **cadeia**, **RegExp**, **bibliotecas**, **matriz**, e **data** métodos que JavaScript Fornece, transformações de dados complexas Stream Analytics tarefas ficam mais fáceis de criar.

## <a name="javascript-user-defined-functions"></a>Funções definidas pelo utilizador de JavaScript
As funções definidas pelo utilizador de JavaScript suportam funções escalares sem monitorização de estado, apenas de computação que não necessitam de conectividade externa. O valor devolvido de uma função só pode ser um valor escalar (único). Depois de adicionar uma função definida pelo utilizador do JavaScript para uma tarefa, pode utilizar a função em qualquer parte da consulta, como uma função escalar incorporada.

Seguem-se alguns cenários em que poderão ser úteis funções definidas pelo utilizador do JavaScript:
* Analisar e manipular a cadeias com funções de expressão regular, por exemplo, **Regexp_Replace()** e **Regexp_Extract()**
* Descodificar e codificação de dados, por exemplo, conversão de binário para hexadecimal
* Efetuar cálculos mathematic com JavaScript **bibliotecas** funções
* Efetuar operações de matriz como ordenação, associação, localizar e preenchimento

Eis algumas coisas que não pode fazer com uma função definida pelo utilizador de JavaScript no Stream Analytics:
* Chamada saída REST pontos finais externos, por exemplo, executar inverter a pesquisa IP ou solicitar dados de referência de uma origem externa
* Executar a serialização de formato de evento personalizado ou entradas/saídas de desserialização
* Criar agregações personalizadas

Embora as funções como **Date.GetDate()** ou **Math.random()** não são bloqueadas na definição de funções, deve evitar utilizá-los. Estas funções **não** devolver o mesmo resultado sempre que chamá-los e o serviço Azure Stream Analytics não manter um diário de alterações de invocações de função e devolveu resultados. Se uma função devolve diferentes resultados os mesmos eventos, não é garantida repetibilidade quando uma tarefa for reiniciada por si ou pelo serviço do Stream Analytics.

## <a name="add-a-javascript-user-defined-function-in-the-azure-portal"></a>Adicionar uma função definida pelo utilizador do JavaScript no portal do Azure
Para criar uma função de utilizador definida de JavaScript simple sob uma tarefa de Stream Analytics existente, execute estes passos:

1.  No portal do Azure, localize a tarefa de Stream Analytics.
2.  Em **tarefa TOPOLOGIA**, selecione a sua função. É apresentada uma lista vazia de funções.
3.  Para criar uma nova função definida pelo utilizador, selecione **adicionar**.
4.  No **nova função** painel, para **tipo de função**, selecione **JavaScript**. Um modelo de função predefinido é apresentado no editor.
5.  Para o **UDF alias**, introduza **hex2Int**e altere a implementação de função da seguinte forma:

    ```
    // Convert Hex value to integer.
    function main(hexValue) {
        return parseInt(hexValue, 16);
    }
    ```

6.  Selecione **Guardar**. A função é apresentada na lista de funções.
7.  Selecione o novo **hex2Int** funcionar e verifique a definição de função. Todas as funções têm um **UDF** prefixo adicionado para o alias da função. Terá de *incluem o prefixo* quando chamar a função na sua consulta do Stream Analytics. Neste caso, tem de chamar **UDF.hex2Int**.

## <a name="call-a-javascript-user-defined-function-in-a-query"></a>Chamar uma função definida pelo utilizador do JavaScript numa consulta

1. No editor de consulta, em **tarefa TOPOLOGIA**, selecione **consulta**.
2.  Editar a consulta e, em seguida, chame a função definida pelo utilizador, como esta:

    ```
    SELECT
        time,
        UDF.hex2Int(offset) AS IntOffset
    INTO
        output
    FROM
        InputStream
    ```

3.  Para carregar o ficheiro de dados de exemplo, clique com botão direito a entrada da tarefa.
4.  Para testar a sua consulta, selecione **testar**.


## <a name="supported-javascript-objects"></a>Objetos de JavaScript suportados
Stream Analytics JavaScript definido pelo utilizador as funções do Azure suportam objetos de JavaScript padrão, incorporados. Para obter uma lista destes objetos, consulte [objetos Global](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects).

### <a name="stream-analytics-and-javascript-type-conversion"></a>A conversão do tipo do Stream Analytics e JavaScript

Existem diferenças na tipos Stream Analytics consultar o idioma e o suporte de JavaScript. Esta tabela lista os mapeamentos de conversão entre os dois:

Stream Analytics | JavaScript
--- | ---
bigint | Número (JavaScript só pode representar números inteiros até precisamente 2 ^ 53)
DateTime | Data (JavaScript só suporta milissegundos)
duplo | Número
nvarchar (Max) | Cadeia
registo | Objeto
Matriz | Matriz
VALOR NULO | Valor nulo


Seguem-se conversões de JavaScript para Stream Analytics:


JavaScript | Stream Analytics
--- | ---
Número | Bigint (se o número for arredondar e entre longa. MinValue e longa. MaxValue; caso contrário, é aspas)
Data | DateTime
Cadeia | nvarchar (Max)
Objeto | registo
Matriz | Matriz
Nulo, indefinido | VALOR NULO
Qualquer outro tipo (por exemplo, uma função ou erro) | Não é suportados (resultados de erro de tempo de execução)

## <a name="troubleshooting"></a>Resolução de problemas
Erros de tempo de execução de JavaScript são considerados fatais e estão anexados através do registo de atividade. Para obter o registo, no portal do Azure, aceda ao seu trabalho e selecione **registo de atividade**.


## <a name="other-javascript-user-defined-function-patterns"></a>Outros padrões de função definida pelo utilizador de JavaScript

### <a name="write-nested-json-to-output"></a>Escrever aninhada JSON para a saída
Se tiver um passo de processamento de seguimento que utiliza uma tarefa de Stream Analytics resultado como entrada e requer um formato JSON, pode escrever uma cadeia JSON para saída. O exemplo seguinte, chama o **JSON.stringify()** funcionar para todos os pares nome/valor da entrada do pacote e, em seguida, escrevê-los como um valor de cadeia única na saída.

**Definição de função definida pelo utilizador JavaScript:**

```
function main(x) {
return JSON.stringify(x);
}
```

**Consulta de exemplo:**
```
SELECT
    DataString,
    DataValue,
    HexValue,
    UDF.json_stringify(input) As InputEvent
INTO
    output
FROM
    input PARTITION BY PARTITIONID
```

## <a name="get-help"></a>Obter ajuda
Para obter ajuda adicional, experimente a nossa [fórum do Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passos seguintes
* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência de linguagem de consulta do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Gestão de Stream Analytics referência da REST API do Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
