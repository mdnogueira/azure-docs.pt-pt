---
title: Rastreio de chamadas com a API Inspector - API Management do Azure | Microsoft Docs
description: Saiba como utilizar o API Inspector na API Management do Azure de chamadas de rastreio.
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 4b222327-c8a4-4f33-9a06-adff2a9834d9
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: apimpm
ms.openlocfilehash: 8090b22b63dda6f67e321867977d5dece3d72132
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2017
---
# <a name="how-to-use-the-api-inspector-to-trace-calls-in-azure-api-management"></a>Como utilizar a API de Inspector para rastreio chama-se na API Management do Azure
API Management fornece uma ferramenta de API Inspector para ajudá-lo com as suas APIs de resolução de problemas e depuração. A API Inspector podem ser utilizada através de programação e também pode ser utilizado diretamente a partir do portal do programador. 

Para além das operações de rastreio, API Inspector também rastreios [expressão de política](https://msdn.microsoft.com/library/azure/dn910913.aspx) avaliações. Para uma demonstração, consulte [nuvem abrangem episódio 177: mais funcionalidades de gestão de API](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) e fast-forward para 21:00.

Este guia fornece instruções de utilização da API Inspector.

> [!NOTE]
> API Inspector rastreios apenas são gerados e disponibilizados para pedidos que contêm as chaves de subscrição que pertencem ao [administrador](api-management-howto-create-groups.md) conta.
> 
> 

## <a name="trace-call"></a> Inspector de API de utilização para uma chamada de rastreio
Para utilizar a API Inspector, adicione um **ocp-apim-rastreio: true** cabeçalho para a chamada de operação de pedido e, em seguida, transferir e inspecionar o rastreio, utilizando o URL indicado pelo **ocp-apim-rastreio-location** cabeçalho de resposta. Isto pode ser feito x509securitytokenparameters e também pode ser feito diretamente a partir do portal do programador.

Este tutorial mostra como utilizar a API de Inspector para operações de rastreio utilizando a API de calculadora básica, que está configurado no [gerir a sua primeira API](api-management-get-started.md) tutorial de introdução. Se ainda não concluiu este tutorial demora apenas alguns momentos para importar a API de calculadora básica, ou pode utilizar outra API à sua escolha, tais como a API eco. Cada instância de serviço de API Management está pré-configurada com uma API Eco que pode ser utilizada para experimentar e saber mais sobre a API Management. A API eco devolve novamente qualquer entrada enviada para o mesmo. Para utilizá-lo, pode invocar qualquer verbo HTTP e o valor de retorno serão simplesmente tenha enviado. 

Para começar, clique em **portal do programador** no Portal do Azure para o seu serviço de API Management. Operações podem ser chamadas diretamente a partir do portal do programador, que fornece uma maneira conveniente para ver e testar as operações de uma API.

> Se ainda não criou uma instância de serviço de API Management, consulte o artigo [criar uma instância de serviço de API Management] [ Create an API Management service instance] no [introdução à API Management do Azure] [ Get started with Azure API Management] tutorial.
> 
> 

![Portal do Programador de API Management][api-management-developer-portal-menu]

Clique em **APIs** no menu superior e, em seguida, clique em **calculadora básica**.

![API Eco][api-management-api]

Clique em **experimente** para experimentar o **adicionar dois números inteiros** operação.

![Experimente][api-management-open-console]

Manter as predefinições de valores de parâmetros e selecione a chave de subscrição para o produto que pretende utilizar o **chave de subscrição** pendente.

Por predefinição no portal do programador do **Ocp-Apim-rastreio** cabeçalho já está definido como **verdadeiro**. Configura este cabeçalho se ou não um rastreio é gerado.

![Enviar][api-management-http-get]

Clique em **enviar** para invocar a operação.

![Enviar][api-management-send-results]

A resposta cabeçalhos serão um **ocp-apim-rastreio-location** com um valor semelhante ao seguinte exemplo.

```
ocp-apim-trace-location : https://contosoltdxw7zagdfsprykd.blob.core.windows.net/apiinspectorcontainer/ZW3e23NsW4wQyS-SHjS0Og2-2?sv=2013-08-15&sr=b&sig=Mgx7cMHsLmVDv%2B%2BSzvg3JR8qGTHoOyIAV7xDsZbF7%2Bk%3D&se=2014-05-04T21%3A00%3A13Z&sp=r&verify_guid=a56a17d83de04fcb8b9766df38514742
```

O rastreio pode ser transferido a partir da localização especificada e revisto como é demonstrado no próximo passo. Tenha em atenção que apenas as entradas de 100 registo último são armazenadas e localizações de registo são reutilizadas em rotação. Por isso, se efetuar mais do que 100 chamadas com o rastreio ativado, eventualmente, será iniciada a substituir os rastreios primeiro no local.

## <a name="inspect-trace"></a>Inspecionar o rastreio
Para rever os valores no rastreio, transfira o ficheiro de rastreio do **ocp-apim-rastreio-location** URL. Este é um ficheiro de texto no formato JSON e contém entradas semelhantes ao seguinte exemplo.

```json
{
    "traceId": "abcd8ea63d134c1fabe6371566c7cbea",
    "traceEntries": {
        "inbound": [
            {
                "source": "handler",
                "timestamp": "2015-06-23T19:51:35.2998610Z",
                "elapsed": "00:00:00.0725926",
                "data": {
                    "request": {
                        "method": "GET",
                        "url": "https://contoso5.azure-api.net/calc/add?a=51&b=49",
                        "headers": [
                            {
                                "name": "Ocp-Apim-Subscription-Key",
                                "value": "5d7c41af64a44a68a2ea46580d271a59"
                            },
                            {
                                "name": "Connection",
                                "value": "Keep-Alive"
                            },
                            {
                                "name": "Host",
                                "value": "contoso5.azure-api.net"
                            }
                        ]
                    }
                }
            },
            {
                "source": "mapper",
                "timestamp": "2015-06-23T19:51:35.2998610Z",
                "elapsed": "00:00:00.0726213",
                "data": {
                    "configuration": {
                        "api": {
                            "from": "/calc",
                            "to": {
                                "scheme": "http",
                                "host": "calcapi.cloudapp.net",
                                "port": 80,
                                "path": "/api",
                                "queryString": "",
                                "query": {},
                                "isDefaultPort": true
                            }
                        },
                        "operation": {
                            "method": "GET",
                            "uriTemplate": "/add?a={a}&b={b}"
                        },
                        "user": {
                            "id": 1,
                            "groups": [
                                "Administrators",
                                "Developers"
                            ]
                        },
                        "product": {
                            "id": 1
                        }
                    }
                }
            },
            {
                "source": "handler",
                "timestamp": "2015-06-23T19:51:35.2998610Z",
                "elapsed": "00:00:00.0727522",
                "data": {
                    "message": "Request is being forwarded to the backend service.",
                    "request": {
                        "method": "GET",
                        "url": "http://calcapi.cloudapp.net/api/add?a=51&b=49",
                        "headers": [
                            {
                                "name": "Ocp-Apim-Subscription-Key",
                                "value": "5d7c41af64a44a68a2ea46580d271a59"
                            },
                            {
                                "name": "X-Forwarded-For",
                                "value": "33.52.215.35"
                            }
                        ]
                    }
                }
            }
        ],
        "outbound": [
            {
                "source": "handler",
                "timestamp": "2015-06-23T19:51:35.4256650Z",
                "elapsed": "00:00:00.1960601",
                "data": {
                    "response": {
                        "status": {
                            "code": 200,
                            "reason": "OK"
                        },
                        "headers": [
                            {
                                "name": "Pragma",
                                "value": "no-cache"
                            },
                            {
                                "name": "Content-Length",
                                "value": "124"
                            },
                            {
                                "name": "Cache-Control",
                                "value": "no-cache"
                            },
                            {
                                "name": "Content-Type",
                                "value": "application/xml; charset=utf-8"
                            },
                            {
                                "name": "Date",
                                "value": "Tue, 23 Jun 2015 19:51:35 GMT"
                            },
                            {
                                "name": "Expires",
                                "value": "-1"
                            },
                            {
                                "name": "Server",
                                "value": "Microsoft-IIS/8.5"
                            },
                            {
                                "name": "X-AspNet-Version",
                                "value": "4.0.30319"
                            },
                            {
                                "name": "X-Powered-By",
                                "value": "ASP.NET"
                            }
                        ]
                    }
                }
            },
            {
                "source": "handler",
                "timestamp": "2015-06-23T19:51:35.4256650Z",
                "elapsed": "00:00:00.1961112",
                "data": {
                    "message": "Response headers have been sent to the caller. Starting to stream the response body."
                }
            },
            {
                "source": "handler",
                "timestamp": "2015-06-23T19:51:35.4256650Z",
                "elapsed": "00:00:00.1963155",
                "data": {
                    "message": "Response body streaming to the caller is complete."
                }
            }
        ]
    }
}
```

## <a name="next-steps"> </a>Passos seguintes
* Veja uma demonstração do rastreio expressões de política no [nuvem abrangem episódio 177: mais funcionalidades de gestão de API](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/). Fast-forward para 21:00 para ver a demonstração.

> [!VIDEO https://channel9.msdn.com/Shows/Cloud+Cover/Episode-177-More-API-Management-Features-with-Vlad-Vinogradsky/player]
> 
> 

[Use API Inspector to trace a call]: #trace-call
[Inspect the trace]: #inspect-trace
[Next steps]: #next-steps

[Configure API settings]: api-management-howto-create-apis.md#configure-api-settings
[Responses]: api-management-howto-add-operations.md#responses
[How create and publish a product]: api-management-howto-add-products.md

[Get started with Azure API Management]: api-management-get-started.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance
[Azure Classic Portal]: https://manage.windowsazure.com/


[api-management-developer-portal-menu]: ./media/api-management-howto-api-inspector/api-management-developer-portal-menu.png
[api-management-api]: ./media/api-management-howto-api-inspector/api-management-api.png
[api-management-echo-api-get]: ./media/api-management-howto-api-inspector/api-management-echo-api-get.png
[api-management-developer-key]: ./media/api-management-howto-api-inspector/api-management-developer-key.png
[api-management-open-console]: ./media/api-management-howto-api-inspector/api-management-open-console.png
[api-management-http-get]: ./media/api-management-howto-api-inspector/api-management-http-get.png
[api-management-send-results]: ./media/api-management-howto-api-inspector/api-management-send-results.png




