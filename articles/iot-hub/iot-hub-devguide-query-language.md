---
title: Compreender o idioma de consulta do IoT Hub do Azure | Microsoft Docs
description: "Guia para programadores - descrição do idioma de consulta de SQL Server como o IoT Hub utilizado para obter informações sobre dispositivos duplos e tarefas do seu IoT hub."
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: 
ms.assetid: 851a9ed3-b69e-422e-8a5d-1d79f91ddf15
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/24/2017
ms.author: elioda
ms.openlocfilehash: fd047b8618f6e6814e0656ac2ab19e30016016fa
ms.sourcegitcommit: 9c3150e91cc3075141dc2955a01f47040d76048a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2017
---
# <a name="iot-hub-query-language-for-device-twins-jobs-and-message-routing"></a>Idioma de consulta do IoT Hub para dispositivos duplos, tarefas e o encaminhamento de mensagens

IoT Hub fornece uma poderosa linguagem de como o SQL para obter informações sobre [dispositivos duplos] [ lnk-twins] e [tarefas][lnk-jobs]e [mensagem encaminhamento][lnk-devguide-messaging-routes]. Este artigo apresenta:

* Uma introdução às funcionalidades principais do idioma de consulta do IoT Hub, e
* Descrição detalhada do idioma.

## <a name="device-twin-queries"></a>Consultas do dispositivo duplo
[Dispositivos duplos] [ lnk-twins] pode conter objetos JSON arbitrários, como as etiquetas e propriedades. IoT Hub permite-lhe dispositivos duplos da consulta como um único documento JSON que contém todas as informações do dispositivo duplo.
Por exemplo, suponha que os dispositivos duplos do IoT hub tem a seguinte estrutura:

```json
{
    "deviceId": "myDeviceId",
    "etag": "AAAAAAAAAAc=",
    "tags": {
        "location": {
            "region": "US",
            "plant": "Redmond43"
        }
    },
    "properties": {
        "desired": {
            "telemetryConfig": {
                "configId": "db00ebf5-eeeb-42be-86a1-458cccb69e57",
                "sendFrequencyInSecs": 300
            },
            "$metadata": {
            ...
            },
            "$version": 4
        },
        "reported": {
            "connectivity": {
                "type": "cellular"
            },
            "telemetryConfig": {
                "configId": "db00ebf5-eeeb-42be-86a1-458cccb69e57",
                "sendFrequencyInSecs": 300,
                "status": "Success"
            },
            "$metadata": {
            ...
            },
            "$version": 7
        }
    }
}
```

IoT Hub expõe dispositivos duplos como uma coleção de documentos chamada **dispositivos**.
Por isso, a seguinte consulta obtém o conjunto completo de dispositivos duplos:

```sql
SELECT * FROM devices
```

> [!NOTE]
> [Os SDKs IoT do Azure] [ lnk-hub-sdks] suporta paginação dos resultados grande.

IoT Hub permite-lhe obter dispositivos duplos filtragem com condições arbitrários. Por exemplo receber dispositivos duplos onde o **location.region** etiquetas está definida como **E.U.A.** utilize a seguinte consulta:

```sql
SELECT * FROM devices
WHERE tags.location.region = 'US'
```

São suportadas operadores booleanos e comparações aritméticas bem. Por exemplo, para obter dispositivos duplos localizados nos E.U.A. e configurado para enviar telemetria inferior a cada minuto utilizam a seguinte consulta:

```sql
SELECT * FROM devices
WHERE tags.location.region = 'US'
    AND properties.reported.telemetryConfig.sendFrequencyInSecs >= 60
```

Para efeitos práticos, também é possível utilizar constantes de matriz com o **IN** e **NIN** operadores (não nos). Por exemplo, para obter os dispositivos duplos que reportam Wi-Fi ou de conectividade com fios utilizam a seguinte consulta:

```sql
SELECT * FROM devices
WHERE properties.reported.connectivity IN ['wired', 'wifi']
```

Muitas vezes, é necessário identificar todos os dispositivos duplos, que contém uma propriedade específica. IoT Hub suporta a função `is_defined()` para esta finalidade. Por exemplo, para obter dispositivos duplos que definem a `connectivity` propriedade utilize a seguinte consulta:

```SQL
SELECT * FROM devices
WHERE is_defined(properties.reported.connectivity)
```

Consulte o [cláusula WHERE] [ lnk-query-where] secção para obter a referência completa das capacidades de filtragem.

Agrupamento e agregações também são suportadas. Por exemplo, para localizar a contagem de dispositivos em cada telemetria o estado da configuração utilize a seguinte consulta:

```sql
SELECT properties.reported.telemetryConfig.status AS status,
    COUNT() AS numberOfDevices
FROM devices
GROUP BY properties.reported.telemetryConfig.status
```

Esta consulta de agrupamento iria devolver um resultado semelhante ao seguinte exemplo. Aqui, três dispositivos comunicam configuração com êxito, dois continua a aplicar a configuração e um comunicou um erro. 

```json
[
    {
        "numberOfDevices": 3,
        "status": "Success"
    },
    {
        "numberOfDevices": 2,
        "status": "Pending"
    },
    {
        "numberOfDevices": 1,
        "status": "Error"
    }
]
```

### <a name="c-example"></a>Exemplo do c#
A funcionalidade de consulta é exposta pelo [c# SDK do serviço] [ lnk-hub-sdks] no **RegistryManager** classe.
Eis um exemplo de uma consulta simples:

```csharp
var query = registryManager.CreateQuery("SELECT * FROM devices", 100);
while (query.HasMoreResults)
{
    var page = await query.GetNextAsTwinAsync();
    foreach (var twin in page)
    {
        // do work on twin object
    }
}
```

Tenha em atenção o **consulta** é criar instâncias do objecto com um tamanho de página (até 100) e, em seguida, várias páginas podem ser obtidas chamando a **GetNextAsTwinAsync** métodos várias vezes.
Tenha em atenção que o objeto da consulta expõe vários **seguinte***, consoante a opção de serialização, é necessária para a consulta, tais como objetos de dispositivo duplo ou a tarefa ou JSON simples para ser utilizados quando usar projeções.

### <a name="nodejs-example"></a>Exemplo de node.js
A funcionalidade de consulta é exposta pelo [o serviço de IoT do Azure SDK para Node.js] [ lnk-hub-sdks] no **registo** objeto.
Eis um exemplo de uma consulta simples:

```nodejs
var query = registry.createQuery('SELECT * FROM devices', 100);
var onResults = function(err, results) {
    if (err) {
        console.error('Failed to fetch the results: ' + err.message);
    } else {
        // Do something with the results
        results.forEach(function(twin) {
            console.log(twin.deviceId);
        });

        if (query.hasMoreResults) {
            query.nextAsTwin(onResults);
        }
    }
};
query.nextAsTwin(onResults);
```

Tenha em atenção o **consulta** é criar instâncias do objecto com um tamanho de página (até 100) e, em seguida, várias páginas podem ser obtidas chamando a **nextAsTwin** métodos várias vezes.
Tenha em atenção que o objeto da consulta expõe vários **seguinte***, consoante a opção de serialização, é necessária para a consulta, tais como objetos de dispositivo duplo ou a tarefa ou JSON simples para ser utilizados quando usar projeções.

### <a name="limitations"></a>Limitações
> [!IMPORTANT]
> Os resultados da consulta podem ter alguns minutos de atraso no que respeita aos valores mais recentes nos dispositivos duplos. Se a consultar os dispositivos individuais duplos por id, é sempre preferível utilizar obter dispositivo duplo API, que sempre contém os valores mais recentes e tem os limites de limitação superior.

Atualmente, em que comparações são suportadas para a instância apenas entre tipos primitivos (não existem objetos), `... WHERE properties.desired.config = properties.reported.config` só é suportada se essas propriedades têm valores primitivos.

## <a name="get-started-with-jobs-queries"></a>Começar a utilizar consultas de tarefas
[As tarefas] [ lnk-jobs] proporcionam uma forma para executar operações em conjuntos de dispositivos. Cada dispositivo duplo contém as informações das tarefas do qual faz parte de uma coleção chamada **tarefas**.
Logicamente,

```json
{
    "deviceId": "myDeviceId",
    "etag": "AAAAAAAAAAc=",
    "tags": {
        ...
    },
    "properties": {
        ...
    },
    "jobs": [
        {
            "deviceId": "myDeviceId",
            "jobId": "myJobId",
            "jobType": "scheduleTwinUpdate",
            "status": "completed",
            "startTimeUtc": "2016-09-29T18:18:52.7418462",
            "endTimeUtc": "2016-09-29T18:20:52.7418462",
            "createdDateTimeUtc": "2016-09-29T18:18:56.7787107Z",
            "lastUpdatedDateTimeUtc": "2016-09-29T18:18:56.8894408Z",
            "outcome": {
                "deviceMethodResponse": null
            }
        },
        ...
    ]
}
```

Atualmente, esta coleção está consultável como **devices.jobs** no IoT Hub do idioma de consulta.

> [!IMPORTANT]
> Atualmente, a propriedade de tarefas nunca é devolvida ao consultar dispositivos duplos (ou seja, as consultas que contém 'a partir de dispositivos'). Só pode ser acedido diretamente com consultas através de `FROM devices.jobs`.
>
>

Por exemplo, para obter todas as tarefas (últimos e agendadas) que afetam um único dispositivo, pode utilizar a seguinte consulta:

```sql
SELECT * FROM devices.jobs
WHERE devices.jobs.deviceId = 'myDeviceId'
```

Tenha em atenção a como esta consulta fornece o estado de específicos do dispositivo (e possivelmente a resposta de método direto) de cada tarefa devolvida.
Também é possível filtrar com condições booleanos arbitrários em todas as propriedades do objeto no **devices.jobs** coleção.
Por exemplo, para obter todos os dispositivos concluída duplo atualização as tarefas que foram criadas depois de Setembro de 2016 para um dispositivo específico, utilize a seguinte consulta:

```sql
SELECT * FROM devices.jobs
WHERE devices.jobs.deviceId = 'myDeviceId'
    AND devices.jobs.jobType = 'scheduleTwinUpdate'
    AND devices.jobs.status = 'completed'
    AND devices.jobs.createdTimeUtc > '2016-09-01'
```

Também pode obter os resultados por dispositivo de uma única tarefa.

```sql
SELECT * FROM devices.jobs
WHERE devices.jobs.jobId = 'myJobId'
```

### <a name="limitations"></a>Limitações
Atualmente, a consulta do **devices.jobs** não suportam:

* Projeções, por conseguinte, apenas `SELECT *` é possível.
* Condições que se referem para o dispositivo duplo para além das propriedades da tarefa (consulte a secção anterior).
* Efetuar agregações, tais como contagem, avg, agrupar por.

## <a name="device-to-cloud-message-routes-query-expressions"></a>As expressões de consulta de rotas de mensagens do dispositivo-nuvem

Utilizar [rotas de dispositivo para nuvem][lnk-devguide-messaging-routes], pode configurar o IoT Hub para emitir mensagens do dispositivo para a nuvem para os pontos finais diferentes com base em expressões avaliadas em comparação com as mensagens individuais.

A rota [condição] [ lnk-query-expressions] utiliza a mesma linguagem de consulta do IoT Hub como condições em consultas duplo e a tarefa. Rota condições são avaliadas nos cabeçalhos de mensagens e um corpo. A expressão de consulta encaminhamento poderá envolver o método apenas cabeçalhos de mensagens, apenas o corpo da mensagem, ou ambos os cabeçalhos de mensagens e corpo da mensagem. IoT Hub assume um esquema para os cabeçalhos e o corpo da mensagem específico para encaminhar mensagens. As secções seguintes descrevem o que é necessário para o IoT Hub encaminhar corretamente.

### <a name="routing-on-message-headers"></a>Encaminhamento nos cabeçalhos de mensagens

IoT Hub assume a representação JSON seguinte de cabeçalhos de mensagens para o encaminhamento de mensagem:

```json
{
    "$messageId": "",
    "$enqueuedTime": "",
    "$to": "",
    "$expiryTimeUtc": "",
    "$correlationId": "",
    "$userId": "",
    "$ack": "",
    "$connectionDeviceId": "",
    "$connectionDeviceGenerationId": "",
    "$connectionAuthMethod": "",
    "$content-type": "",
    "$content-encoding": "",

    "userProperty1": "",
    "userProperty2": ""
}
```

Propriedades do sistema de mensagens têm o prefixo de `'$'` símbolo.
Propriedades do utilizador são sempre acedidas com os respetivos nomes. Se um nome de propriedade de utilizador acontece a operação com uma propriedade de sistema (tais como `$to`), será possível obter a propriedade de utilizador com o `$to` expressão.
Pode aceder sempre a propriedade de sistema utilizando parênteses Retos `{}`: por exemplo, pode utilizar a expressão `{$to}` para aceder a propriedade de sistema `to`. Os nomes de propriedade entre parênteses obtêm sempre a propriedade de sistema correspondente.

Lembre-se de que os nomes de propriedades são sensível a maiúsculas e minúsculas.

> [!NOTE]
> Todas as propriedades de mensagem são cadeias. Propriedades do sistema, conforme descrito no [guia para programadores][lnk-devguide-messaging-format], não estão atualmente disponíveis para utilizar nas consultas.
>

Por exemplo, se utilizar um `messageType` propriedade, pode pretender encaminhar toda a telemetria para um ponto final e todos os alertas para outro ponto final. Pode escrever a seguinte expressão para encaminhar a telemetria:

```sql
messageType = 'telemetry'
```

E a seguinte expressão para encaminhar as mensagens de alerta:

```sql
messageType = 'alert'
```

Expressões e funções também são suportadas. Esta funcionalidade permite-lhe distinguir entre o nível de gravidade, por exemplo:

```sql
messageType = 'alerts' AND as_number(severity) <= 2
```

Consulte o [expressão e condições] [ lnk-query-expressions] secção para obter uma lista completa de funções e operadores suportados.

### <a name="routing-on-message-bodies"></a>Encaminhamento corpos de mensagens

IoT Hub apenas pode encaminhar com base no corpo da mensagem conteúdos se o corpo da mensagem está corretamente formado JSON com codificação UTF-8, UTF-16 ou UTF-32. Definir o tipo de conteúdo da mensagem para `application/json` e a codificação de conteúdo para um das codificações suportadas de UTF nos cabeçalhos da mensagem. Se qualquer um dos cabeçalhos não for especificado, o IoT Hub não irá tentar avaliar uma expressão de consulta que envolvem o corpo da mensagem. Se a mensagem não é uma mensagem JSON ou se a mensagem não especificar o tipo de conteúdo e a codificação de conteúdo, pode continuar a utilizar o encaminhamento de mensagens para encaminhar a mensagem com base nos cabeçalhos de mensagens.

Pode utilizar `$body` na expressão de consulta para encaminhar a mensagem. Pode utilizar uma referência de corpo simples, de referência de matriz de corpo ou várias referências de corpo na expressão de consulta. A expressão de consulta também pode combinar uma referência de corpo com uma referência de cabeçalho da mensagem. Por exemplo, seguem-se todas as expressões de consulta válida:

```sql
$body.message.Weather.Location.State = 'WA'
$body.Weather.HistoricalData[0].Month = 'Feb'
$body.Weather.Temperature = 50 AND $body.message.Weather.IsEnabled
length($body.Weather.Location.State) = 2
$body.Weather.Temperature = 50 AND Status = 'Active'
```

## <a name="basics-of-an-iot-hub-query"></a>Noções básicas de uma consulta do IoT Hub
Cada consulta de IoT Hub é composta por de SELECIONE e cláusulas, com WHERE opcional e cláusulas GROUP BY. Cada consulta é executada numa coleção de documentos JSON, por exemplo, dispositivos duplos. A cláusula FROM indica a coleção de documentos para iterated no (**dispositivos** ou **devices.jobs**). Em seguida, o filtro na cláusula WHERE é aplicado. Com agregações, os resultados deste passo são agrupados, como especificado na cláusula GROUP BY e, para cada grupo, é gerada uma linha conforme especificado na cláusula SELECT.

```sql
SELECT <select_list>
FROM <from_specification>
[WHERE <filter_condition>]
[GROUP BY <group_specification>]
```

## <a name="from-clause"></a>A cláusula FROM
O **do < from_specification >** cláusula pode assumir apenas dois valores: **dos dispositivos** para dispositivos duplos da consulta, ou **de devices.jobs** para detalhes de por dispositivo da tarefa de consulta.

## <a name="where-clause"></a>Cláusula WHERE
O **onde < filter_condition >** cláusula é opcional. Especifica uma ou mais condições que os documentos de JSON na coleção FROM devem satisfazer a incluir como parte do resultado. De qualquer documento JSON tem de avaliar as condições especificadas como "true" para ser incluída no resultado.

As condições permitidas são descritas na secção [expressões e condições][lnk-query-expressions].

## <a name="select-clause"></a>Cláusula SELECT
O **SELECIONE < select_list >** é obrigatória e especifica quais os valores são obtidos a partir da consulta. Especifica os valores JSON para ser utilizado para gerar novos objetos JSON.
Para cada elemento do subconjunto filtrado (e opcionalmente agrupado) da coleção FROM, a fase de projeção gera um novo objeto JSON, construído com os valores especificados na cláusula SELECT.

Segue-se a gramática de cláusula SELECT:

```
SELECT [TOP <max number>] <projection list>

<projection_list> ::=
    '*'
    | <projection_element> AS alias [, <projection_element> AS alias]+

<projection_element> :==
    attribute_name
    | <projection_element> '.' attribute_name
    | <aggregate>

<aggregate> :==
    count()
    | avg(<projection_element>)
    | sum(<projection_element>)
    | min(<projection_element>)
    | max(<projection_element>)
```

**Attribute_name** refere-se a qualquer propriedade do documento JSON da coleção de FROM. Alguns exemplos de cláusulas SELECT podem ser encontrados no [introdução às consultas do dispositivo duplo] [ lnk-query-getstarted] secção.

Atualmente, seleção cláusulas diferentes **SELECIONE*** só são suportados em consultas de agregação em dispositivos duplos.

## <a name="group-by-clause"></a>Cláusula GROUP BY
O **GROUP BY < group_specification >** cláusula passo é opcional que pode ser executado após o filtro especificado na cláusula WHERE e antes da projecção especificada em SELECIONAR. Grupos de documentos com base no valor de um atributo. Estes grupos são utilizados para gerar valores agregados conforme especificado na cláusula SELECT.

Um exemplo de uma consulta com GROUP BY é:

```sql
SELECT properties.reported.telemetryConfig.status AS status,
    COUNT() AS numberOfDevices
FROM devices
GROUP BY properties.reported.telemetryConfig.status
```

A sintaxe formal GROUP BY está:

```
GROUP BY <group_by_element>
<group_by_element> :==
    attribute_name
    | < group_by_element > '.' attribute_name
```

**Attribute_name** refere-se a qualquer propriedade do documento JSON da coleção de FROM.

Atualmente, a cláusula GROUP BY só é suportada ao consultar dispositivos duplos.

## <a name="expressions-and-conditions"></a>As expressões e condições
Um nível elevado, um *expressão*:

* Avalia a uma instância de um tipo JSON (por exemplo, booleano, número, cadeia, matriz ou objeto).
* É definido pela manipulação de dados provenientes do documento JSON de dispositivo e constantes utilizando operadores incorporados e funções.

*Condições* são expressões avaliadas como um valor booleano. Qualquer constante diferente booleano **verdadeiro** são considerados como estando **falso** (incluindo **nulo**, **indefinido**, qualquer instância de objeto ou a matriz qualquer cadeia e claramente o booleano **falso**).

A sintaxe para expressões é:

```
<expression> ::=
    <constant> |
    attribute_name |
    <function_call> |
    <expression> binary_operator <expression> |
    <create_array_expression> |
    '(' <expression> ')'

<function_call> ::=
    <function_name> '(' expression ')'

<constant> ::=
    <undefined_constant>
    | <null_constant>
    | <number_constant>
    | <string_constant>
    | <array_constant>

<undefined_constant> ::= undefined
<null_constant> ::= null
<number_constant> ::= decimal_literal | hexadecimal_literal
<string_constant> ::= string_literal
<array_constant> ::= '[' <constant> [, <constant>]+ ']'
```

Para compreender o que cada símbolo a sintaxe de expressões representa, consulte a tabela seguinte:

| Símbolo | Definição |
| --- | --- |
| attribute_name | Qualquer propriedade do documento JSON no **FROM** coleção. |
| binary_operator | Nenhum operador binário listado no [operadores](#operators) secção. |
| function_name| Qualquer função listada no [funções](#functions) secção. |
| decimal_literal |Um número de vírgula flutuante expressado na notação decimal. |
| hexadecimal_literal |Um número expresso pela cadeia de caracteres '0x' seguido de uma cadeia de dígitos hexadecimais. |
| string_literal |As literais de cadeia são cadeias Unicode representadas por uma sequência de zero ou mais carateres Unicode ou sequências de escape. As literais de cadeia estão incluídas no plicas ou aspas. Permitido escapes: `\'`, `\"`, `\\`, `\uXXXX` caracteres Unicode definido pelo 4 dígitos hexadecimais. |

### <a name="operators"></a>Operadores
São suportados os seguintes operadores:

| Família | Operadores |
| --- | --- |
| Aritmética |+, -, *, /, % |
| Lógica |E, EM ALTERNATIVA, NÃO |
| Comparação |=, !=, <, >, <=, >=, <> |

### <a name="functions"></a>Funções
Ao consultar duplos e tarefas suportada a única função é:

| Função | Descrição |
| -------- | ----------- |
| IS_DEFINED(Property) | Devolve um valor boleano que indica se a propriedade foi atribuída um valor (incluindo `null`). |

Em condições de rotas, são suportadas as seguintes funções de bibliotecas:

| Função | Descrição |
| -------- | ----------- |
| Abs(x) | Devolve o valor absoluto (positivo) da expressão numérica especificada. |
| Exp(x) | Devolve o valor da expressão especificada numérico exponencial (i ^ x). |
| Power(x,y) | Devolve o valor da expressão especificada para a potência especificada (x ^ y).|
| SQUARE(x) | Devolve o quadrado do valor numérico especificado. |
| CEILING(x) | Devolve o menor valor de número inteiro maior que ou igual a, a expressão numérica especificada. |
| FLOOR(x) | Devolve o maior número inteiro menor ou igual a expressão numérica especificada. |
| Sign(x) | Devolve o positivo (+ 1), zero (0) ou negativo sessão (-1) da expressão numérica especificada.|
| SQRT(x) | Devolve a raiz quadrada do valor numérico especificado. |

Em condições de rotas, são suportadas as funções de conversão e verificação de tipo seguintes:

| Função | Descrição |
| -------- | ----------- |
| AS_NUMBER | Converte a cadeia de entrada para um número. `noop`Se a entrada é um número; `Undefined` se a cadeia não representa um número.|
| IS_ARRAY | Devolve um valor booleano que indica se o tipo da expressão especificada é uma matriz. |
| IS_BOOL | Devolve um valor booleano que indica se o tipo de expressão especificado é um valor booleano. |
| IS_DEFINED | Devolve um valor boleano que indica se a propriedade foi atribuída um valor. |
| IS_NULL | Devolve um valor booleano que indica se o tipo da expressão especificada é nulo. |
| IS_NUMBER | Devolve um valor booleano que indica se o tipo de expressão especificado é um número. |
| IS_OBJECT | Devolve um valor booleano que indica se o tipo de expressão especificado é um objeto JSON. |
| IS_PRIMITIVE | Devolve um valor booleano que indica se o tipo da expressão especificada é uma primitiva (string, Boolean, numéricos ou `null`). |
| IS_STRING | Devolve um valor booleano que indica se o tipo da expressão especificada é uma cadeia. |

Em condições de rotas, são suportadas as seguintes funções de cadeia:

| Função | Descrição |
| -------- | ----------- |
| CONCAT (x, y,...) | Devolve uma cadeia que é o resultado da concatenar duas ou mais valores de cadeia. |
| LENGTH(x) | Devolve o número de carateres da expressão de cadeia especificada.|
| LOWER(x) | Devolve uma expressão de cadeia após a conversão de dados do caráter em maiúsculas em minúsculas. |
| UPPER(x) | Devolve uma expressão de cadeia após a conversão de dados de carateres em minúsculas em maiúsculas. |
| SUBCADEIA (cadeia, início [, comprimento]) | Devolve a parte de uma expressão de cadeia a partir da posição carácter especificado baseado em zero e continua ao comprimento especificado ou para o fim da cadeia. |
| INDEX_OF (cadeia, fragmento) | Devolve a posição inicial da primeira ocorrência da segunda cadeia de expressão na primeira expressão de cadeia especificada ou -1 se a cadeia não foi encontrada.|
| STARTS_WITH (x, y) | Devolve um booleano que indica se a primeira expressão de cadeia começa com o segundo. |
| ENDS_WITH (x, y) | Devolve um booleano que indica se a primeira expressão de cadeia termina com o segundo. |
| CONTAINS(x,y) | Devolve um booleano que indica se a primeira cadeia de expressão contém o segundo. |

## <a name="next-steps"></a>Passos seguintes
Saiba como executar consultas nas suas aplicações utilizando [SDKs IoT do Azure][lnk-hub-sdks].

[lnk-query-where]: iot-hub-devguide-query-language.md#where-clause
[lnk-query-expressions]: iot-hub-devguide-query-language.md#expressions-and-conditions
[lnk-query-getstarted]: iot-hub-devguide-query-language.md#get-started-with-device-twin-queries

[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-jobs]: iot-hub-devguide-jobs.md
[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-devguide-messaging-routes]: iot-hub-devguide-messages-read-custom.md
[lnk-devguide-messaging-format]: iot-hub-devguide-messages-construct.md
[lnk-devguide-messaging-routes]: ./iot-hub-devguide-messages-read-custom.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
