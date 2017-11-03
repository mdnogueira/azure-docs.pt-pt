---
title: APIs REST do Resource Manager | Microsoft Docs
description: "Uma descrição geral dos exemplos de autenticação e a utilização de REST APIs do Resource Manager"
services: azure-resource-manager
documentationcenter: na
author: navalev
manager: timlt
editor: 
ms.assetid: e8d7a1d2-1e82-4212-8288-8697341408c5
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/13/2017
ms.author: navale;tomfitz;
ms.openlocfilehash: 2f7ba23775545637de865f9ef63680ae22c62164
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="resource-manager-rest-apis"></a>APIs REST do Gestor de Recursos
> [!div class="op_single_selector"]
> * [Azure PowerShell](powershell-azure-resource-manager.md)
> * [CLI do Azure](xplat-cli-azure-resource-manager.md)
> * [Portal](resource-group-portal.md) 
> * [API REST](resource-manager-rest-api.md)
> 
> 

Por detrás de cada chamada para o Azure Resource Manager, atrás de cada modelo implementado, atrás de cada conta de armazenamento configurados existem um ou mais chamadas à API RESTful do Azure Resource Manager. Este tópico é dedicado para essas APIs e como pode ser chamada sem utilizar quaisquer SDK de todo. Esta abordagem é útil se pretender que o controlo total de pedidos para o Azure ou, se o SDK para o seu idioma preferencial não está disponível ou não suporta as operações precisa.

Este artigo não passa através de cada API que é exposto no Azure, mas em vez disso, utiliza algumas operações como exemplos de como ligar aos mesmos. Depois de compreender as noções básicas, pode ler o [referência da API REST do Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/) para encontrar informações detalhadas sobre como utilizar o resto das APIs.

## <a name="authentication"></a>Autenticação
Autenticação para o Resource Manager é processada pelo Azure Active Directory (AD). Para ligar a qualquer API, terá primeiro de autenticar com o Azure AD para receber um token de autenticação que podem passar para cada pedido. Como é que são descrevem uma chamada pura diretamente para as APIs REST, partimos do princípio que não pretende autenticar pedidos para um nome de utilizador e palavra-passe. Podemos também partem do princípio de que não está a utilizar dois mecanismos de autenticação de fator. Por conseguinte, criamos que chama uma aplicação do Azure AD e um serviço principal que são utilizadas para iniciar sessão. Mas, lembre-se de que o Azure AD suporta vários procedimentos de autenticação e todos eles podem ser utilizados para obter esse token de autenticação que é necessário para os pedidos subsequentes de API.
Siga [criar do Azure AD aplicação e um Principal de serviço](resource-group-create-service-principal-portal.md) para instruções passo a passo.

### <a name="generating-an-access-token"></a>Gerar um Token de acesso
Autenticação no Azure AD é feita chamando para o Azure AD, localizado em login.microsoftonline.com. Para efetuar a autenticação, tem de ter as seguintes informações:

* ID de inquilino do Azure AD (o nome do que o Azure AD estão a utilizar para iniciar sessão, muitas vezes, igual a sua empresa, mas não necessário)
* ID da aplicação (executada durante o passo de criação de aplicações do Azure AD)
* Palavra-passe (que selecionou ao criar a aplicação Azure AD)

Os seguintes pedidos de HTTP, certificar-se de que substitui os valores corretos "ID de inquilino do Azure AD", "ID da aplicação" e "Palavra-passe".

**Pedido de HTTP genérico:**

```HTTP
POST /<Azure AD Tenant ID>/oauth2/token?api-version=1.0 HTTP/1.1 HTTP/1.1
Host: login.microsoftonline.com
Cache-Control: no-cache
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&resource=https%3A%2F%2Fmanagement.core.windows.net%2F&client_id=<Application ID>&client_secret=<Password>
```

... será (se a autenticação é concluída com êxito) resultar numa resposta semelhante ao seguinte resposta:

```json
{
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1448199959",
  "not_before": "1448196059",
  "resource": "https://management.core.windows.net/",
  "access_token": "eyJ0eXAiOiJKV1QiLCJhb...86U3JI_0InPUk_lZqWvKiEWsayA"
}
```
(Access_token na resposta anterior foi shortened para aumentar o facilitar a leitura)

**A gerar o token de acesso utilizando Bash:**

```console
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "grant_type=client_credentials&resource=https://management.core.windows.net/&client_id=<application id>&client_secret=<password you selected for authentication>" https://login.microsoftonline.com/<Azure AD Tenant ID>/oauth2/token?api-version=1.0
```

**A gerar o token de acesso através do PowerShell:**

```powershell
Invoke-RestMethod -Uri https://login.microsoftonline.com/<Azure AD Tenant ID>/oauth2/token?api-version=1.0 -Method Post
 -Body @{"grant_type" = "client_credentials"; "resource" = "https://management.core.windows.net/"; "client_id" = "<application id>"; "client_secret" = "<password you selected for authentication>" }
```

A resposta contém um token de acesso, informações sobre quanto esse token é válido e obter informações sobre os recursos pode utilizar esse token para.
O token de acesso que recebeu pela chamada anterior de HTTP deve ser transmitido para todos os pedidos para a API do Gestor de recursos. Transmiti-lo como um valor de cabeçalho com o nome "Autorização" com o valor "Portador YOUR_ACCESS_TOKEN". Tenha em atenção o espaço entre "Portador" e o seu token de acesso.

Como pode ver do resultado HTTP acima, o token é válido durante um período de tempo durante os quais deve colocar em cache e reutilizar esse mesmo token. Mesmo se for possível autenticar no Azure AD para cada chamada à API, seria possível altamente ineficaz.

## <a name="calling-resource-manager-rest-apis"></a>APIs de REST de chamar Resource Manager
Este tópico utiliza apenas algumas APIs para explicar a utilização básica das operações REST. Para obter informações sobre todas as operações, consulte [REST APIs do Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/).

### <a name="list-all-subscriptions"></a>Lista todas as subscrições
Uma das operações mais simples, pode fazê-lo é listar as subscrições disponíveis que pode aceder. O pedido seguinte, é apresentado como o token de acesso é transmitido como um cabeçalho:

(Substitua YOUR_ACCESS_TOKEN o Token de acesso real.)

```HTTP
GET /subscriptions?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json
```

... como resultado, obtém uma lista de subscrições que este principal de serviço tem permissão para aceder a

(Os IDs de subscrição foi shortened para legibilidade)

```json
{
  "value": [
    {
      "id": "/subscriptions/3a8555...555995",
      "subscriptionId": "3a8555...555995",
      "displayName": "Azure Subscription",
      "state": "Enabled",
      "subscriptionPolicies": {
        "locationPlacementId": "Internal_2014-09-01",
        "quotaId": "Internal_2014-09-01"
      }
    }
  ]
}
```

### <a name="list-all-resource-groups-in-a-specific-subscription"></a>Listar todos os grupos de recursos de uma subscrição específica
Todos os recursos disponíveis com as APIs do Gestor de recursos estão aninhados dentro de um grupo de recursos. Pode consultar o Gestor de recursos existente para grupos de recursos na sua subscrição utilizando o seguinte pedido de HTTP GET. Repare como o ID de subscrição é transmitido como parte do URL neste momento.

(Substituir YOUR_ACCESS_TOKEN e SUBSCRIPTION_ID com o seu ID de token e de subscrição de acesso real)

```HTTP
GET /subscriptions/SUBSCRIPTION_ID/resourcegroups?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json
```

A resposta obtiver depende se tem quaisquer grupos de recursos definidos e, em caso afirmativo, quantos.

(Os IDs de subscrição foi shortened para legibilidade)

```json
{
    "value": [
        {
            "id": "/subscriptions/3a8555...555995/resourceGroups/myfirstresourcegroup",
            "name": "myfirstresourcegroup",
            "location": "eastus",
            "properties": {
                "provisioningState": "Succeeded"
            }
        },
        {
            "id": "/subscriptions/3a8555...555995/resourceGroups/mysecondresourcegroup",
            "name": "mysecondresourcegroup",
            "location": "northeurope",
            "tags": {
                "tagname1": "My first tag"
            },
            "properties": {
                "provisioningState": "Succeeded"
            }
        }
    ]
}
```

### <a name="create-a-resource-group"></a>Criar um grupo de recursos
Até ao momento, estamos tiver sido consultando apenas as APIs do Resource Manager para obter informações. Está na altura, vamos criar alguns recursos e vamos começar por mais simples de todos eles, um grupo de recursos. Os seguintes pedidos de HTTP cria um grupo de recursos na região/localização da sua preferência e adiciona uma etiqueta ao mesmo.

(Substitua YOUR_ACCESS_TOKEN, SUBSCRIPTION_ID, RESOURCE_GROUP_NAME sua real Token de acesso, ID de subscrição e o nome do grupo de recursos que pretende criar)

```HTTP
PUT /subscriptions/SUBSCRIPTION_ID/resourcegroups/RESOURCE_GROUP_NAME?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json

{
  "location": "northeurope",
  "tags": {
    "tagname1": "test-tag"
  }
}
```

Se tiver êxito, receberá uma resposta semelhante ao seguinte resposta:

```json
{
  "id": "/subscriptions/3a8555...555995/resourceGroups/RESOURCE_GROUP_NAME",
  "name": "RESOURCE_GROUP_NAME",
  "location": "northeurope",
  "tags": {
    "tagname1": "test-tag"
  },
  "properties": {
    "provisioningState": "Succeeded"
  }
}
```

Criou com êxito um grupo de recursos no Azure. Parabéns!

### <a name="deploy-resources-to-a-resource-group-using-a-resource-manager-template"></a>Implementar recursos num grupo de recursos através de um modelo do Resource Manager
Com o Resource Manager, pode implementar os seus recursos através de modelos. Define um modelo de vários recursos e as respetivas dependências. Nesta secção, partimos do pressuposto está familiarizado com modelos do Resource Manager e iremos apenas mostrar como efetuar a chamada de API para iniciar a implementação. Para obter mais informações sobre como construir modelos, consulte [modelos Authoring Azure Resource Manager](resource-group-authoring-templates.md).

Implementação de um modelo não diferir muito como chamar o outras APIs. Um aspeto importante é que a implementação de um modelo pode demorar bastante muito tempo. A chamada de API apenas devolve e está a funcionar para o utilizador como programador a consulta para o estado da implementação para determinar quando a conclusão da implementação. Para obter mais informações, consulte [controlar as operações do Azure assíncronas](resource-manager-async-operations.md).

Neste exemplo, podemos utilizar um modelo exposto publicamente disponível na [GitHub](https://github.com/Azure/azure-quickstart-templates). O modelo que utilizamos implementa uma VM com Linux a região EUA oeste. Apesar deste exemplo utiliza um modelo disponível num repositório público, como o GitHub, em vez disso, pode passar o modelo completo como parte do pedido. Tenha em atenção que podemos fornecer valores de parâmetro no pedido que são utilizados dentro do modelo implementado.

(Substituir SUBSCRIPTION_ID, RESOURCE_GROUP_NAME, DEPLOYMENT_NAME, YOUR_ACCESS_TOKEN, GLOBALY_UNIQUE_STORAGE_ACCOUNT_NAME, ADMIN_USER_NAME, ADMIN_PASSWORD e DNS_NAME_FOR_PUBLIC_IP valores adequados para o seu pedido)

```HTTP
PUT /subscriptions/SUBSCRIPTION_ID/resourcegroups/RESOURCE_GROUP_NAME/providers/microsoft.resources/deployments/DEPLOYMENT_NAME?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json

{
  "properties": {
    "templateLink": {
      "uri": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-linux-vm/azuredeploy.json",
      "contentVersion": "1.0.0.0",
    },
    "mode": "Incremental",
    "parameters": {
        "newStorageAccountName": {
          "value": "GLOBALY_UNIQUE_STORAGE_ACCOUNT_NAME"
        },
        "adminUsername": {
          "value": "ADMIN_USER_NAME"
        },
        "adminPassword": {
          "value": "ADMIN_PASSWORD"
        },
        "dnsNameForPublicIP": {
          "value": "DNS_NAME_FOR_PUBLIC_IP"
        },
        "ubuntuOSVersion": {
          "value": "15.04"
        }
    }
  }
}
```

A resposta JSON longa para este pedido foi omitida para melhorar a legibilidade desta documentação. A resposta contém informações sobre a implementação transformada em modelo que criou.

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre como lidar com as operações assíncronas REST, consulte [controlar as operações do Azure assíncronas](resource-manager-async-operations.md).
