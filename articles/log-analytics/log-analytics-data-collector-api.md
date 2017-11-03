---
title: "Iniciar o Recoletor de dados de HTTP de análise API | Microsoft Docs"
description: "Pode utilizar a API de Recoletor de dados do registo de análise de HTTP para adicionar dados JSON de POST para o repositório de análise de registos a partir de qualquer cliente que pode chamar a API REST. Este artigo descreve como utilizar a API e tem exemplos de como publicar dados através da utilização de linguagens de programação diferentes."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: 
ms.assetid: a831fd90-3f55-423b-8b20-ccbaaac2ca75
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: bwren
ms.openlocfilehash: 5b4b31b58c7a4bcb93277333502bc082da2062ed
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="send-data-to-log-analytics-with-the-http-data-collector-api-public-preview"></a>Enviar dados para análise de registos com a API de Recoletor de dados de HTTP (pré-visualização pública)
Este artigo mostra como utilizar a API de Recoletor de dados de HTTP para enviar dados para análise de registos de um cliente de REST API.  Descreve como formatou os dados recolhidos pelo seu script ou aplicação, inclua-o num pedido e tem esse pedido autorizado através da análise de registos.  São fornecidos exemplos do PowerShell, c# e Python.

> [!NOTE]
> A API de Recoletor de dados do registo de análise de HTTP está em pré-visualização pública.

## <a name="concepts"></a>Conceitos
Pode utilizar a API de Recoletor de dados de HTTP para enviar dados para análise de registos a partir de qualquer cliente que possa chamar uma API REST.  Esta situação pode ter um runbook na automatização do Azure que recolhe a gestão de dados do Azure ou outra nuvem ou poderão ser um sistema de gestão alternativo que utiliza a análise de registos para consolidar e analisar os dados.

Todos os dados no repositório de análise de registos é armazenado como um registo com um tipo de registo específica.  Formatar os dados a enviar para a API de Recoletor de dados de HTTP como vários registos em JSON.  Ao submeter os dados, é criado um registo individual no repositório para cada registo no payload de pedidos.


![Descrição geral de Recoletores de dados HTTP](media/log-analytics-data-collector-api/overview.png)



## <a name="create-a-request"></a>Criar um pedido
Para utilizar a API de Recoletor de dados de HTTP, crie um pedido POST que inclui os dados para enviar em JavaScript Object Notation (JSON).  As três tabelas listam os atributos que são necessários para cada pedido. Iremos descrevem cada atributo em mais detalhe posteriormente no artigo.

### <a name="request-uri"></a>URI do pedido
| Atributo | Propriedade |
|:--- |:--- |
| Método |POST |
| URI |https://\<CustomerId\>.ods.opinsights.azure.com/api/logs?api-version=2016-04-01 |
| Tipo de conteúdo |application/json |

### <a name="request-uri-parameters"></a>Parâmetros URI do pedido
| Parâmetro | Descrição |
|:--- |:--- |
| CustomerID |O identificador exclusivo para a área de trabalho do Microsoft Operations Management Suite. |
| Recurso |O nome de recurso de API: / api/logs. |
| Versão de API |A versão da API para utilizar com este pedido. Atualmente, é 2016-04-01. |

### <a name="request-headers"></a>Cabeçalhos de pedido
| Cabeçalho | Descrição |
|:--- |:--- |
| Autorização |A assinatura de autorização. O artigo, pode ler sobre como criar um cabeçalho de HMAC SHA256. |
| Tipo de registo |Especifique o tipo de registo dos dados que estão a ser submetidos. Atualmente, o tipo de registo suporta apenas carateres alfanuméricos. Não suporta números ou carateres especiais. |
| x-ms-data |A data em que o pedido foi processado, no formato RFC 1123. |
| campo Hora gerado |O nome de um campo de dados que contém o timestamp do item de dados. Se especificar um campo, em seguida, o respetivo conteúdo é utilizado para **TimeGenerated**. Se este campo não está especificado, a predefinição para **TimeGenerated** é o tempo que a mensagem é ingerida. O conteúdo do campo mensagem deve seguir o formato ISO 8601 aaaa-MM-Aaaathh. |

## <a name="authorization"></a>Autorização
Qualquer pedido de API de Recoletor de dados de HTTP de análise de registo tem de incluir um cabeçalho de autorização. Para autenticar um pedido, tem de iniciar o pedido com o site primário ou a chave secundária para a área de trabalho que está a efetuar o pedido. Em seguida, passe esse assinatura como parte do pedido.   

Eis o formato para o cabeçalho de autorização:

```
Authorization: SharedKey <WorkspaceID>:<Signature>
```

*WorkspaceID* é o identificador exclusivo para a área de trabalho do Operations Management Suite. *Assinatura* é um [Message Authentication Code (HMAC) com base em Hash](https://msdn.microsoft.com/library/system.security.cryptography.hmacsha256.aspx) que é criada a partir do pedido e, em seguida, calculada utilizando a [algoritmo SHA256](https://msdn.microsoft.com/library/system.security.cryptography.sha256.aspx). Em seguida, codificá-lo utilizando a codificação Base64.

Utilize este formato para codificar o **SharedKey** cadeia da assinatura:

```
StringToSign = VERB + "\n" +
                  Content-Length + "\n" +
               Content-Type + "\n" +
                  x-ms-date + "\n" +
                  "/api/logs";
```

Eis um exemplo de uma cadeia de assinatura:

```
POST\n1024\napplication/json\nx-ms-date:Mon, 04 Apr 2016 08:00:00 GMT\n/api/logs
```

Quando tiver a cadeia de assinatura, codificá-lo utilizando o algoritmo HMAC SHA256 em cadeia com codificação UTF-8 e, em seguida, codificar o resultado como Base64. Utilize este formato:

```
Signature=Base64(HMAC-SHA256(UTF8(StringToSign)))
```

Os exemplos nas secções seguintes tem o código de exemplo para ajudar a criar um cabeçalho de autorização.

## <a name="request-body"></a>Corpo do pedido
O corpo da mensagem tem de estar no JSON. Tem de incluir um ou mais registos com os pares de nome e valor de propriedade neste formato:

```
{
"property1": "value1",
" property 2": "value2"
" property 3": "value3",
" property 4": "value4"
}
```

Pode batch vários registos num único pedido utilizando o formato seguinte. Todos os registos tem de ser o mesmo tipo de registo.

```
{
"property1": "value1",
" property 2": "value2"
" property 3": "value3",
" property 4": "value4"
},
{
"property1": "value1",
" property 2": "value2"
" property 3": "value3",
" property 4": "value4"
}
```

## <a name="record-type-and-properties"></a>Tipo de registo e as propriedades
Definir um tipo de registo personalizado ao submeter dados através da API de Recoletor de dados do registo de análise de HTTP. Atualmente, não é possível escrever dados existentes tipos de registo que foram criados por outros tipos de dados e soluções. Análise de registos lê os dados de entrada e, em seguida, cria propriedades que correspondem aos tipos de dados dos valores que introduziu.

Cada pedido para a API de análise do registo tem de incluir um **tipo de registo** cabeçalho com o nome para o tipo de registo. O sufixo **_CL** automaticamente é acrescentado ao nome que introduzir distinguir de outros tipos de registo como um registo personalizado. Por exemplo, se introduzir o nome **MyNewRecordType**, análise de registos cria um registo com o tipo **MyNewRecordType_CL**. Isto ajuda a garantir que não existem não existem conflitos entre os nomes de tipo criados pelo utilizador e os que são fornecidos na atuais ou futuras soluções da Microsoft.

Para identificar o tipo de dados de uma propriedade, análise de registos adiciona um sufixo de nome de propriedade. Se uma propriedade contiver um valor nulo, a propriedade não está incluída desse registo. Esta tabela lista o tipo de dados de propriedade e o sufixo correspondente:

| Tipo de dados de propriedade | Sufixo |
|:--- |:--- |
| Cadeia |_s |
| Valor booleano |_b |
| duplo |_d |
| Data/hora |_t |
| GUID |_g |

O tipo de dados que utiliza a análise de registos para cada propriedade depende se o tipo de registo para o novo registo já existe.

* Se o tipo de registo não existe, análise de registos cria um novo. Análise de registos utiliza a inferência do tipo JSON para determinar o tipo de dados para cada propriedade para o novo registo.
* Se o tipo de registo existe, análise de registos tenta criar um novo registo com base nas propriedades existentes. Se o tipo de dados para uma propriedade no registo novo não corresponde ao e não é possível converter o tipo existente, ou se o registo inclui uma propriedade que não existe, análise de registos cria uma nova propriedade com o sufixo relevante.

Por exemplo, esta entrada de submissão criaria um registo com três propriedades, **number_d**, **boolean_b**, e **string_s**:

![Registo de exemplo 1](media/log-analytics-data-collector-api/record-01.png)

Se tiver submetido, em seguida, esta entrada seguinte, com todos os valores a formatados como cadeias, as propriedades de não ser alterado. Estes valores podem ser convertidos para tipos de dados existente:

![Registo de exemplo 2](media/log-analytics-data-collector-api/record-02.png)

No entanto, se efetuou esta submissão seguinte, em seguida, análise de registos criar novas propriedades **boolean_d** e **string_d**. Não não possível converter estes valores:

![Registo de exemplo 3](media/log-analytics-data-collector-api/record-03.png)

Se, em seguida, submetido a seguinte entrada, antes do tipo de registo foi criado, a análise de registos criaria um registo com três propriedades, **number_s**, **boolean_s**, e **string_s**. Esta entrada, cada um dos valores iniciais é formatada como uma cadeia:

![Registo de exemplo 4](media/log-analytics-data-collector-api/record-04.png)

## <a name="data-limits"></a>Limites de dados
Existem algumas restrições à volta dos dados publicados para a coleção de dados do Log Analytics API.

* Máximo de 30 MB por post para a API de Recoletor de dados de análise do registo. Este é um limite de tamanho para um único pedido de post. Se os dados de um único publique que exceder 30 MB, deve dividir os dados até segmentos de tamanho mais pequeno e enviá-los em simultâneo.
* Máximo de limite de 32 KB para os valores de campo. Se o valor do campo for superior a 32 KB, os dados serão truncados.
* Recomendada número máximo de campos para um determinado tipo é 50. Este é um limite prático de uma perspetiva de experiência de pesquisa e facilidade de utilização.  

## <a name="return-codes"></a>Códigos de retorno
O código de estado HTTP 200, significa que o pedido foi recebido para processamento. Isto indica que a operação foi concluída com êxito.

Esta tabela lista o conjunto completo de códigos de estado que poderá devolver o serviço:

| Código | Estado | Código de erro | Descrição |
|:--- |:--- |:--- |:--- |
| 200 |OK | |O pedido foi aceite com êxito. |
| 400 |Pedido incorreto |InactiveCustomer |A área de trabalho foi fechada. |
| 400 |Pedido incorreto |InvalidApiVersion |A versão de API que especificou não foi reconhecida pelo serviço. |
| 400 |Pedido incorreto |InvalidCustomerId |O ID da área de trabalho especificado é inválido. |
| 400 |Pedido incorreto |InvalidDataFormat |Foi submetida JSON inválido. O corpo da resposta pode conter mais informações sobre como resolver o erro. |
| 400 |Pedido incorreto |InvalidLogType |O tipo de registo especificado continha carateres especiais ou números. |
| 400 |Pedido incorreto |MissingApiVersion |Não foi especificada a versão da API. |
| 400 |Pedido incorreto |MissingContentType |Não foi especificado o tipo de conteúdo. |
| 400 |Pedido incorreto |MissingLogType |Não foi especificado o tipo de registo do valor necessário. |
| 400 |Pedido incorreto |UnsupportedContentType |O tipo de conteúdo não foi definido para **application/json**. |
| 403 |Proibido |InvalidAuthorization |O serviço não conseguiu autenticar o pedido. Certifique-se de que a chave de ID e a ligação de área de trabalho são válidas. |
| 404 |Não foi encontrado | | O URL fornecido está incorreto, ou o pedido é demasiado grande. |
| 429 |Demasiados pedidos | | O serviço está com um elevado volume de dados da sua conta. Repita o pedido mais tarde. |
| 500 |Erro interno do servidor |UnspecifiedError |O serviço encontrou um erro interno. Repita o pedido. |
| 503 |Serviço indisponível |ServiceUnavailable |O serviço está atualmente disponível para receber pedidos. Repita o pedido. |

## <a name="query-data"></a>Consultar dados
Para consultar dados submetidos pela API em Recoletor de dados no HTTP de análise do registo, pesquisa de registos com **tipo** que é igual do **LogType** valor que especificou, acrescentar **_CL**. Por exemplo, se tiver utilizado **MyCustomLog**, em seguida, iria devolver todos os registos com **tipo = MyCustomLog_CL**.

>[!NOTE]
> Se a sua área de trabalho tiver sido atualizada para o [idioma de consulta de análise de registos nova](log-analytics-log-search-upgrade.md), em seguida, a consulta acima alteraria o seguinte.

> `MyCustomLog_CL`

## <a name="sample-requests"></a>Pedidos de exemplo
As secções seguintes, irá encontrar exemplos de como a submissão de dados para a API de Recoletor de dados do registo de análise de HTTP utilizando diferentes linguagens de programação.

Para cada amostra, execute estes passos para definir as variáveis para o cabeçalho de autorização:

1. No portal do Operations Management Suite, selecione o **definições** mosaico e, em seguida, selecione o **origens ligadas** separador.
2. À direita do **ID da área de trabalho**, selecione o ícone de cópia e colagem, em seguida, o ID como o valor de **ID de cliente** variável.
3. À direita do **chave primária**, selecione o ícone de cópia e colagem, em seguida, o ID como o valor de **chave partilhada** variável.

Em alternativa, pode alterar as variáveis para o tipo de registo e os dados JSON.

### <a name="powershell-sample"></a>Exemplo do PowerShell
```
# Replace with your Workspace ID
$CustomerId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"  

# Replace with your Primary Key
$SharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# Specify the name of the record type that you'll be creating
$LogType = "MyRecordType"

# Specify a field with the created time for the records
$TimeStampField = "DateValue"


# Create two records with the same set of properties to create
$json = @"
[{  "StringValue": "MyString1",
    "NumberValue": 42,
    "BooleanValue": true,
    "DateValue": "2016-05-12T20:00:00.625Z",
    "GUIDValue": "9909ED01-A74C-4874-8ABF-D2678E3AE23D"
},
{   "StringValue": "MyString2",
    "NumberValue": 43,
    "BooleanValue": false,
    "DateValue": "2016-05-12T20:00:00.625Z",
    "GUIDValue": "8809ED01-A74C-4874-8ABF-D2678E3AE23D"
}]
"@

# Create the function to create the authorization signature
Function Build-Signature ($customerId, $sharedKey, $date, $contentLength, $method, $contentType, $resource)
{
    $xHeaders = "x-ms-date:" + $date
    $stringToHash = $method + "`n" + $contentLength + "`n" + $contentType + "`n" + $xHeaders + "`n" + $resource

    $bytesToHash = [Text.Encoding]::UTF8.GetBytes($stringToHash)
    $keyBytes = [Convert]::FromBase64String($sharedKey)

    $sha256 = New-Object System.Security.Cryptography.HMACSHA256
    $sha256.Key = $keyBytes
    $calculatedHash = $sha256.ComputeHash($bytesToHash)
    $encodedHash = [Convert]::ToBase64String($calculatedHash)
    $authorization = 'SharedKey {0}:{1}' -f $customerId,$encodedHash
    return $authorization
}


# Create the function to create and post the request
Function Post-OMSData($customerId, $sharedKey, $body, $logType)
{
    $method = "POST"
    $contentType = "application/json"
    $resource = "/api/logs"
    $rfc1123date = [DateTime]::UtcNow.ToString("r")
    $contentLength = $body.Length
    $signature = Build-Signature `
        -customerId $customerId `
        -sharedKey $sharedKey `
        -date $rfc1123date `
        -contentLength $contentLength `
        -fileName $fileName `
        -method $method `
        -contentType $contentType `
        -resource $resource
    $uri = "https://" + $customerId + ".ods.opinsights.azure.com" + $resource + "?api-version=2016-04-01"

    $headers = @{
        "Authorization" = $signature;
        "Log-Type" = $logType;
        "x-ms-date" = $rfc1123date;
        "time-generated-field" = $TimeStampField;
    }

    $response = Invoke-WebRequest -Uri $uri -Method $method -ContentType $contentType -Headers $headers -Body $body -UseBasicParsing
    return $response.StatusCode

}

# Submit the data to the API endpoint
Post-OMSData -customerId $customerId -sharedKey $sharedKey -body ([System.Text.Encoding]::UTF8.GetBytes($json)) -logType $logType  
```

### <a name="c-sample"></a>Exemplo C#
```
using System;
using System.Net;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Security.Cryptography;
using System.Text;
using System.Threading.Tasks;

namespace OIAPIExample
{
    class ApiExample
    {
        // An example JSON object, with key/value pairs
        static string json = @"[{""DemoField1"":""DemoValue1"",""DemoField2"":""DemoValue2""},{""DemoField3"":""DemoValue3"",""DemoField4"":""DemoValue4""}]";

        // Update customerId to your Operations Management Suite workspace ID
        static string customerId = "xxxxxxxx-xxx-xxx-xxx-xxxxxxxxxxxx";

        // For sharedKey, use either the primary or the secondary Connected Sources client authentication key   
        static string sharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx";

        // LogName is name of the event type that is being submitted to Log Analytics
        static string LogName = "DemoExample";

        // You can use an optional field to specify the timestamp from the data. If the time field is not specified, Log Analytics assumes the time is the message ingestion time
        static string TimeStampField = "";

        static void Main()
        {
            // Create a hash for the API signature
            var datestring = DateTime.UtcNow.ToString("r");
            var jsonBytes = Encoding.UTF8.GetBytes(message);
            string stringToHash = "POST\n" + jsonBytes.Length + "\napplication/json\n" + "x-ms-date:" + datestring + "\n/api/logs";
            string hashedString = BuildSignature(stringToHash, sharedKey);
            string signature = "SharedKey " + customerId + ":" + hashedString;

            PostData(signature, datestring, json);
        }

        // Build the API signature
        public static string BuildSignature(string message, string secret)
        {
            var encoding = new System.Text.ASCIIEncoding();
            byte[] keyByte = Convert.FromBase64String(secret);
            byte[] messageBytes = encoding.GetBytes(message);
            using (var hmacsha256 = new HMACSHA256(keyByte))
            {
                byte[] hash = hmacsha256.ComputeHash(messageBytes);
                return Convert.ToBase64String(hash);
            }
        }

        // Send a request to the POST API endpoint
        public static void PostData(string signature, string date, string json)
        {
            try
            {
                string url = "https://" + customerId + ".ods.opinsights.azure.com/api/logs?api-version=2016-04-01";

                System.Net.Http.HttpClient client = new System.Net.Http.HttpClient();
                client.DefaultRequestHeaders.Add("Accept", "application/json");
                client.DefaultRequestHeaders.Add("Log-Type", LogName);
                client.DefaultRequestHeaders.Add("Authorization", signature);
                client.DefaultRequestHeaders.Add("x-ms-date", date);
                client.DefaultRequestHeaders.Add("time-generated-field", TimeStampField);

                System.Net.Http.HttpContent httpContent = new StringContent(json, Encoding.UTF8);
                httpContent.Headers.ContentType = new MediaTypeHeaderValue("application/json");
                Task<System.Net.Http.HttpResponseMessage> response = client.PostAsync(new Uri(url), httpContent);

                System.Net.Http.HttpContent responseContent = response.Result.Content;
                string result = responseContent.ReadAsStringAsync().Result;
                Console.WriteLine("Return Result: " + result);
            }
            catch (Exception excep)
            {
                Console.WriteLine("API Post Exception: " + excep.Message);
            }
        }
    }
}

```

### <a name="python-sample"></a>Exemplo de Python
```
import json
import requests
import datetime
import hashlib
import hmac
import base64

# Update the customer ID to your Operations Management Suite workspace ID
customer_id = 'xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

# For the shared key, use either the primary or the secondary Connected Sources client authentication key   
shared_key = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# The log type is the name of the event that is being submitted
log_type = 'WebMonitorTest'

# An example JSON web monitor object
json_data = [{
   "slot_ID": 12345,
    "ID": "5cdad72f-c848-4df0-8aaa-ffe033e75d57",
    "availability_Value": 100,
    "performance_Value": 6.954,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "true"
},
{   
    "slot_ID": 67890,
    "ID": "b6bee458-fb65-492e-996d-61c4d7fbb942",
    "availability_Value": 100,
    "performance_Value": 3.379,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "false"
}]
body = json.dumps(json_data)

#####################
######Functions######  
#####################

# Build the API signature
def build_signature(customer_id, shared_key, date, content_length, method, content_type, resource):
    x_headers = 'x-ms-date:' + date
    string_to_hash = method + "\n" + str(content_length) + "\n" + content_type + "\n" + x_headers + "\n" + resource
    bytes_to_hash = bytes(string_to_hash).encode('utf-8')  
    decoded_key = base64.b64decode(shared_key)
    encoded_hash = base64.b64encode(hmac.new(decoded_key, bytes_to_hash, digestmod=hashlib.sha256).digest())
    authorization = "SharedKey {}:{}".format(customer_id,encoded_hash)
    return authorization

# Build and send a request to the POST API
def post_data(customer_id, shared_key, body, log_type):
    method = 'POST'
    content_type = 'application/json'
    resource = '/api/logs'
    rfc1123date = datetime.datetime.utcnow().strftime('%a, %d %b %Y %H:%M:%S GMT')
    content_length = len(body)
    signature = build_signature(customer_id, shared_key, rfc1123date, content_length, method, content_type, resource)
    uri = 'https://' + customer_id + '.ods.opinsights.azure.com' + resource + '?api-version=2016-04-01'

    headers = {
        'content-type': content_type,
        'Authorization': signature,
        'Log-Type': log_type,
        'x-ms-date': rfc1123date
    }

    response = requests.post(uri,data=body, headers=headers)
    if (response.status_code >= 200 and response.status_code <= 299):
        print 'Accepted'
    else:
        print "Response code: {}".format(response.status_code)

post_data(customer_id, shared_key, body, log_type)
```

## <a name="next-steps"></a>Passos seguintes
- Utilize o [API de pesquisa de registo](log-analytics-log-search-api.md) obter dados a partir do repositório de análise de registos.
