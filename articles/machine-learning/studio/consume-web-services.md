---
title: "Como consumir um serviço Web do Azure Machine Learning | Microsoft Docs"
description: "Depois de um de machine learning serviço é implementado, o serviço RESTFul Web que é tornado disponível pode ser utilizado como o serviço de resposta-pedido em tempo real ou como um serviço de execução do batch."
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 804f8211-9437-4982-98e9-ca841b7edf56
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 06/02/2017
ms.author: garye
ms.openlocfilehash: ed41ec58dbdfd41931e8bab3a3cbe33caafe9a74
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2017
---
# <a name="how-to-consume-an-azure-machine-learning-web-service"></a>Como consumir um serviço Web do Azure Machine Learning

Depois de implementar um modelo preditivo de Machine Learning do Azure como um serviço Web, pode utilizar uma API REST para enviar os dados e obter as predições. Pode enviar os dados em tempo real ou no modo de batch.

Pode encontrar mais informações sobre como criar e implementar um serviço Web do Machine Learning utilizar aqui o Machine Learning Studio:

* Para um tutorial sobre como criar uma experimentação no Machine Learning Studio, consulte [criar a sua primeira experimentação](create-experiment.md).
* Para obter mais informações sobre como implementar um serviço Web, consulte [implementar um serviço Web do Machine Learning](publish-a-machine-learning-web-service.md).
* Para obter mais informações sobre o Machine Learning em geral, visite o [Centro de documentação do Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/).

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="overview"></a>Descrição geral
Com o serviço Web do Azure Machine Learning, uma aplicação externa comunica com um modelo de pontuação de fluxo de trabalho de Machine Learning em tempo real. Uma chamada de serviço Web do Machine Learning devolve resultados de predição para uma aplicação externa. Para efetuar um chamada do serviço Web do Machine Learning, transmitir uma chave de API que foi criada quando implementou uma predição. O serviço Web do Machine Learning é baseado em REST, uma opção de arquitetura popular para projetos de programação web.

O Azure Machine Learning tem dois tipos de serviços:

* O serviço de resposta-pedido (RRS) – A latência baixa, o serviço altamente dimensionável, que fornece uma interface para os modelos sem monitorização de estado criados e implementados a partir do Machine Learning Studio.
* Execução serviço batch (BES) – um assíncrono que pontua um lote de registos de dados do serviço.

Para obter mais informações sobre os serviços Web do Machine Learning, consulte [implementar um serviço Web do Machine Learning](publish-a-machine-learning-web-service.md).

## <a name="get-an-azure-machine-learning-authorization-key"></a>Obter uma chave de autorização do Azure Machine Learning
Ao implementar a sua experimentação, chaves de API são geradas para o serviço Web. Pode obter as chaves de várias localizações.

### <a name="from-the-microsoft-azure-machine-learning-web-services-portal"></a>No portal de serviços Web do Microsoft Azure Machine Learning
Iniciar sessão para o [serviços Web do Microsoft Azure Machine Learning](https://services.azureml.net) portal.

Para obter a chave de API para um serviço Web de aprendizagem máquina novo:

1. No portal de serviços Web do Azure Machine Learning, clique em **serviços Web** menu superior.
2. Clique no serviço de Web para o qual pretende obter a chave.
3. No menu superior, clique em **Consume**.
4. Copie e guarde o **chave primária**.

Para obter a chave de API para um serviço Web de aprendizagem máquina clássico:

1. No portal de serviços Web do Azure Machine Learning, clique em **serviços Web clássicos** menu superior.
2. Clique no serviço de Web com a qual está a trabalhar.
3. Clique no ponto de final para o qual pretende obter a chave.
4. No menu superior, clique em **Consume**.
5. Copie e guarde o **chave primária**.

### <a name="classic-web-service"></a>Serviço Web clássico
 Também pode obter uma chave para um serviço Web clássico de Machine Learning Studio.

#### <a name="machine-learning-studio"></a>Machine Learning Studio
1. No Machine Learning Studio, clique em **serviços WEB** à esquerda.
2. Clique num serviço Web. O **chave de API** no **DASHBOARD** separador.

## <a id="connect"></a>Ligar a um serviço Web do Machine Learning
Pode ligar a um serviço Web do Machine Learning utilizando qualquer linguagem de programação que suporte o HTTP de pedido e resposta. Pode ver exemplos em c#, Python e R de uma página de ajuda do serviço Web do Machine Learning.

**Ajuda da API de aprendizagem do computador** ajuda de API do Machine Learning é criada quando implementar um serviço Web. Consulte [instruções de aprendizagem do Azure - implementar o serviço Web](walkthrough-5-publish-web-service.md).
A ajuda de API do Machine Learning contém detalhes sobre uma predição do serviço Web.

1. Clique no serviço de Web com a qual está a trabalhar.
2. Clique no ponto de final para o qual pretende ver a página de ajuda de API.
3. No menu superior, clique em **Consume**.
4. Clique em **página de ajuda de API** em pontos finais de pedido-resposta ou execução de lote.

**A vista de API do Machine Learning ajuda para um serviço Web de novo**

No [Portal de serviços do Azure Machine Learning Web](https://services.azureml.net/):

1. Clique em **serviços WEB** no menu superior.
2. Clique no serviço de Web para o qual pretende obter a chave.

Clique em **serviço Web de utilização** para obter o URI para o pedido Reposonse e o código de exemplo e serviços de execução do Batch em c#, R e Python.

Clique em **API do Swagger** obter Swagger baseada em documentação para as APIs chamado a partir de URI fornecido.

### <a name="c-sample"></a>Exemplo do c#
Para ligar a um serviço Web do Machine Learning, utilize um **HttpClient** transmitir ScoreData. ScoreData contém um FeatureVector, um vetor n dimensional das funcionalidades numéricos que representa o ScoreData. Autenticar para o serviço de Machine Learning com uma chave de API.

Para ligar a um serviço Web do Machine Learning, o **Microsoft.AspNet.WebApi.Client** pacote NuGet tem de estar instalado.

**Instalar Microsoft.AspNet.WebApi.Client NuGet no Visual Studio**

1. Publicar o conjunto de dados de transferência do UCI: conjunto de dados de classe para adultos 2 serviço Web.
2. clique em **Ferramentas** > **Gestor de Pacotes NuGet** > **Consola de Gestor de Pacotes**.
3. Escolha **Install-Package Microsoft.AspNet.WebApi.Client**.

**Para executar o exemplo de código**

1. Publicar "exemplo 1: Transferir o conjunto de dados a partir de UCI: o conjunto de dados do adulto 2 classe" experimentação, parte da coleção de exemplo de Machine Learning.
2. Atribua apiKey com a chave de um serviço Web. Consulte **obter uma chave de autorização do Azure Machine Learning** acima.
3. Atribua serviceUri com o URI do pedido.

**Eis o que um pedido de conclusão terá um aspeto semelhante.**
```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Net.Http;
using System.Net.Http.Formatting;
using System.Net.Http.Headers;
using System.Text;
using System.Threading.Tasks;

namespace CallRequestResponseService
{
    class Program
    {
        static void Main(string[] args)
        {
            InvokeRequestResponseService().Wait();
        }

        static async Task InvokeRequestResponseService()
        {
            using (var client = new HttpClient())
            {
                var scoreRequest = new
                {
                    Inputs = new Dictionary<string, List<Dictionary<string, string>>> () {
                        {
                            "input1",
                            // Replace columns labels with those used in your dataset
                            new List<Dictionary<string, string>>(){new Dictionary<string, string>(){
                                    {
                                        "column1", "value1"
                                    },
                                    {
                                        "column2", "value2"
                                    },
                                    {
                                        "column3", "value3"
                                    }
                                }
                            }
                        },
                    },
                    GlobalParameters = new Dictionary<string, string>() {}
                };

                // Replace these values with your API key and URI found on https://services.azureml.net/
                const string apiKey = "<your-api-key>"; 
                const string apiUri = "<your-api-uri>";
                
                client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue( "Bearer", apiKey);
                client.BaseAddress = new Uri(apiUri);

                // WARNING: The 'await' statement below can result in a deadlock
                // if you are calling this code from the UI thread of an ASP.Net application.
                // One way to address this would be to call ConfigureAwait(false)
                // so that the execution does not attempt to resume on the original context.
                // For instance, replace code such as:
                //      result = await DoSomeTask()
                // with the following:
                //      result = await DoSomeTask().ConfigureAwait(false)

                HttpResponseMessage response = await client.PostAsJsonAsync("", scoreRequest);

                if (response.IsSuccessStatusCode)
                {
                    string result = await response.Content.ReadAsStringAsync();
                    Console.WriteLine("Result: {0}", result);
                }
                else
                {
                    Console.WriteLine(string.Format("The request failed with status code: {0}", response.StatusCode));

                    // Print the headers - they include the requert ID and the timestamp,
                    // which are useful for debugging the failure
                    Console.WriteLine(response.Headers.ToString());

                    string responseContent = await response.Content.ReadAsStringAsync();
                    Console.WriteLine(responseContent);
                }
            }
        }
    }
}
```

### <a name="python-sample"></a>Exemplo de Python
Para ligar a um serviço Web do Machine Learning, utilize o **urllib2** biblioteca para Python 2. x e **urllib.request** biblioteca para o Python 3. Passará ScoreData, que contém um FeatureVector, um vetor n dimensional das funcionalidades numéricos que representa o ScoreData. Autenticar para o serviço de Machine Learning com uma chave de API.

**Para executar o exemplo de código**

1. Implementar "exemplo 1: Transferir o conjunto de dados a partir de UCI: o conjunto de dados do adulto 2 classe" experimentação, parte da coleção de exemplo de Machine Learning.
2. Atribua apiKey com a chave de um serviço Web. Consulte o **obter uma chave de autorização do Azure Machine Learning** secção perto do início deste artigo.
3. Atribua serviceUri com o URI do pedido.

**Eis o que um pedido de conclusão terá um aspeto semelhante.**
```python
import urllib2 # urllib.request for Python 3.X
import json

data = {
    "Inputs": {
        "input1":
        [
            {
                'column1': "value1",   
                'column2': "value2",   
                'column3': "value3"
            }
        ],
    },
    "GlobalParameters":  {}
}

body = str.encode(json.dumps(data))

# Replace this with the URI and API Key for your web service
url = '<your-api-uri>'
api_key = '<your-api-key>'
headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}

# "urllib.request.Request(uri, body, headers)" for Python 3.X
req = urllib2.Request(url, body, headers)

try:
    # "urllib.request.urlopen(req)" for Python 3.X
    response = urllib2.urlopen(req)

    result = response.read()
    print(result)
# "urllib.error.HTTPError as error" for Python 3.X
except urllib2.HTTPError, error: 
    print("The request failed with status code: " + str(error.code))

    # Print the headers - they include the requert ID and the timestamp, which are useful for debugging the failure
    print(error.info())
    print(json.loads(error.read())) 
```

### <a name="r-sample"></a>Exemplo de R

Para ligar a um serviço Web do Machine Learning, utilize o **RCurl** e **rjson** bibliotecas para efetuar o pedido e processar resposta JSON devolvida. Passará ScoreData, que contém um FeatureVector, um vetor n dimensional das funcionalidades numéricos que representa o ScoreData. Autenticar para o serviço de Machine Learning com uma chave de API.

**Eis o que um pedido de conclusão terá um aspeto semelhante.**
```r
library("RCurl")
library("rjson")

# Accept SSL certificates issued by public Certificate Authorities
options(RCurlOptions = list(cainfo = system.file("CurlSSL", "cacert.pem", package = "RCurl")))

h = basicTextGatherer()
hdr = basicHeaderGatherer()

req = list(
    Inputs = list(
            "input1" = list(
                list(
                        'column1' = "value1",
                        'column2' = "value2",
                        'column3' = "value3"
                    )
            )
        ),
        GlobalParameters = setNames(fromJSON('{}'), character(0))
)

body = enc2utf8(toJSON(req))
api_key = "<your-api-key>" # Replace this with the API key for the web service
authz_hdr = paste('Bearer', api_key, sep=' ')

h$reset()
curlPerform(url = "<your-api-uri>",
httpheader=c('Content-Type' = "application/json", 'Authorization' = authz_hdr),
postfields=body,
writefunction = h$update,
headerfunction = hdr$update,
verbose = TRUE
)

headers = hdr$value()
httpStatus = headers["status"]
if (httpStatus >= 400)
{
print(paste("The request failed with status code:", httpStatus, sep=" "))

# Print the headers - they include the requert ID and the timestamp, which are useful for debugging the failure
print(headers)
}

print("Result:")
result = h$value()
print(fromJSON(result))
```

### <a name="javascript-sample"></a>Exemplo de JavaScript

Para ligar a um serviço Web do Machine Learning, utilize o **pedido** pacote npm no seu projeto. Também irá utilizar o `JSON` objeto para formatar os dados introduzidos e analisar o resultado. Instalar utilizando `npm install request --save`, ou adicione `"request": "*"` para Package. JSON em `dependencies` e execute `npm install`.

**Eis o que um pedido de conclusão terá um aspeto semelhante.**
```js
let req = require("request");

const uri = "<your-api-uri>";
const apiKey = "<your-api-key>";

let data = {
    "Inputs": {
        "input1":
        [
            {
                'column1': "value1",
                'column2': "value2",
                'column3': "value3"
            }
        ],
    },
    "GlobalParameters": {}
}

const options = {
    uri: uri,
    method: "POST",
    headers: {
        "Content-Type": "application/json",
        "Authorization": "Bearer " + apiKey,
    },
    body: JSON.stringify(data)
}

req(options, (err, res, body) => {
    if (!err && res.statusCode == 200) {
        console.log(body);
    } else {
        console.log("The request failed with status code: " + res.statusCode);
    }
});
```