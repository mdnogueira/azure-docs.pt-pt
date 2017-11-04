---
title: "O consumo do serviço web Machine Learning modelo gestão do Azure | Microsoft Docs"
description: "Este documento descreve os conceitos e os passos envolvidos na consumir serviços web implementados utilizando a gestão de modelo no Azure Machine Learning."
services: machine-learning
author: raymondlaghaeian
ms.author: raymondl
manager: neerajkh
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/06/2017
ms.openlocfilehash: 8b2f806b81bbc6d9328ab179644b79b97d68e7b9
ms.sourcegitcommit: 963e0a2171c32903617d883bb1130c7c9189d730
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2017
---
# <a name="consuming-web-services"></a>Consumir os serviços web
Depois de implementar um modelo como um serviço de web em tempo real, pode enviar os dados e obter predições a partir de uma variedade de plataformas e aplicações. O serviço de web em tempo real que expõe uma API REST para obter as predições. Pode enviar dados para o serviço web no formato único ou vários linha obter um ou mais predições no momento.

Com o serviço Web do Azure Machine Learning, uma aplicação externa comunica com um modelo preditivo forma síncrona, fazendo POST de HTTP chamar para o URL do serviço. Para efetuar um chamada do serviço web, a aplicação de cliente tem de especificar a chave de API que foi criada quando implementar uma predição e colocar os dados de pedido no corpo do pedido POST.

Tenha em atenção que as chaves de API só estão disponíveis no modo de implementação de cluster. Serviços local web não tem chaves.

## <a name="service-deployment-options"></a>Opções de implementação de serviço
Serviços Web do Machine Learning do Azure podem ser implementados para os clusters baseados na cloud para cenários de produção e teste e estações de trabalho locais utilizando o motor de docker. A funcionalidade de modelo preditivo em ambos os casos permanece igual. Implementação de cluster com base fornece dimensionável e performant solução com base nos serviços de contentor do Azure, durante a implementação local pode ser utilizada para depuração. 

A CLI do Azure Machine Learning e API fornece comandos convenientes para criar e gerir ambientes para implementações de serviço através de computação a ```az ml env``` opção. 

## <a name="list-deployed-services-and-images"></a>Serviços de lista implementado e imagens
Pode listar os serviços implementados e imagens utilizando o comando da CLI do Docker ```az ml service list realtime -o table```. Tenha em atenção que este comando sempre funciona no contexto do ambiente de computação atual e não pretende mostrar serviços que são implementados num ambiente que não está configurado para ser atual. Para definir a utilização de ambiente ```az ml env set```. 

## <a name="get-service-information"></a>Obter informações de serviço
Depois do serviço web tiver sido implementado com êxito, utilize o seguinte comando para obter o URL do serviço e outros detalhes para chamar o ponto final do serviço. 

```
az ml service usage realtime -i <service name>
```

Este comando irá imprimir o URL do serviço, cabeçalhos de pedido necessários, swagger URL e dados de exemplo para chamar o serviço se o esquema de API do serviço foi fornecido o momento da implementação.

Pode testar o serviço diretamente a partir da CLI sem composição requst um HTTP, introduzindo o comando da CLI de exemplo com os dados de entrada:

```
az ml service run realtime -i <service name> -d "Your input data"
```

## <a name="get-the-service-api-key"></a>Obter a chave de API do serviço
Para obter a chave do serviço web, utilize o seguinte comando:

```
az ml service keys realtime -i <web service id>
```
Ao criar o pedido HTTP, utilize a chave no cabeçalho de autorização: "Autorização": "portador <key>"

## <a name="get-the-service-swagger-description"></a>Obter a descrição de Swagger do serviço
Se o esquema de API do serviço foi fornecido o ponto final de serviço seria expor um documento Swagger em ```http://<ip>/api/v1/service/<service name>/swagger.json```. O documento Swagger pode ser utilizado para gerar o cliente do serviço e explorar os dados de entrada esperados e outros detalhes sobre o serviço automaticamente.

## <a name="get-service-logs"></a>Obter registos de serviço
Para compreender o comportamento de serviço e diagnosticar problemas, existem várias formas de obter os registos do serviço:
- Comando da CLI ```az ml service logs realtime -i <service id>```. Este comando funciona em cluster e modos locais.
- Se o registo de serviço foi ativado na implementação, os registos do serviço também serão enviados para AppInsight. O comando da CLI ```az ml service usage realtime -i <service id>``` mostra o URL de AppInsight. Tenha em atenção que os registos de AppInsight podem sofrer um atraso por 2 a 5 minutos.
- Registos de cluster podem ser visualizados através da consola Kubernetes que está ligada ao definir o ambiente de cluster atual com o```az ml env set```
- Registos de local docker estão disponíveis através de registos do motor de docker quando o serviço está em execução localmente.

## <a name="call-the-service-using-c"></a>Chamar o serviço com c#
Utilize o URL do serviço para enviar um pedido a partir de uma aplicação de consola c#. 

1. No Visual Studio, crie uma nova aplicação de consola: 
    * No menu, clique em, ficheiro -> novo -> projeto
    * Em Visual Studio c#, clique o ambiente de trabalho do Windows classe, em seguida, selecione a aplicação de consola.
2. Introduza _MyFirstService_ como o nome do projeto, em seguida, clique em OK.
3. Referências do projeto, definido referências como _System.Net_, e _System.Net.Http_.
4. Clique em Ferramentas -> Gestor de pacotes NuGet -> consola do Gestor de pacotes, em seguida, instale o pacote de Microsoft.AspNet.WebApi.Client.
5. Abra o ficheiro Program.cs e substitua o código com o seguinte código:
6. Atualização do _SERVICE_URL_ e _API_KEY_ parâmetros com as informações do seu serviço web.
7. Execute o projeto.

```csharp
using System;
using System.Collections.Generic;
using System.Net.Http;
using System.Net.Http.Headers;
using Newtonsoft.Json;

namespace MyFirstService
{
    class Program
    {
        // Web Service URL (update it with your service url)
        private const string SERVICE_URL = "http://<service ip address>:80/api/v1/service/<service name>/score";
        private const string API_KEY = "your service key";

        static void Main(string[] args)
        {
            Program.PostRequest();
            Console.ReadLine();
        }

        private static void PostRequest()
        {
            HttpClient client = new HttpClient();
            client.BaseAddress = new Uri(SERVICE_URL);
            //For local web service, comment out this line.
            client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", API_KEY);

            var inputJson = new List<RequestPayload>();
            RequestPayload payload = new RequestPayload();
            List<InputDf> inputDf = new List<InputDf>();
            inputDf.Add(new InputDf()
            {
                feature1 = value1,
                feature2 = value2,
            });
            payload.Input_df_list = inputDf;
            inputJson.Add(payload);

            try
            {
                var request = new HttpRequestMessage(HttpMethod.Post, string.Empty);
                request.Content = new StringContent(JsonConvert.SerializeObject(payload));
                request.Content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
                var response = client.SendAsync(request).Result;

                Console.Write(response.Content.ReadAsStringAsync().Result);
            }
            catch (Exception e)
            {
                Console.Out.WriteLine(e.Message);
            }
        }
        public class InputDf
        {
            public double feature1 { get; set; }
            public double feature2 { get; set; }
        }
        public class RequestPayload
        {
            [JsonProperty("input_df")]
            public List<InputDf> Input_df_list { get; set; }
        }
    }
}
```

## <a name="call-the-web-service-using-python"></a>Chamar o serviço web com o Python
Utilize o Python para enviar um pedido para o serviço web em tempo real. 

1. Copie o seguinte exemplo de código para um novo ficheiro de Python.
2. Atualize os parâmetros de dados, o url e o api_key. Para os serviços local web, remova o cabeçalho "Authorization".
3. Execute o código. 

```python
import requests
import json

data = "{\"input_df\": [{\"feature1\": value1, \"feature2\": value2}]}"
body = str.encode(json.dumps(data))

url = 'http://<service ip address>:80/api/v1/service/<service name>/score'
api_key = 'your service key' 
headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}

resp = requests.post(url, data, headers=headers)
resp.text
```
