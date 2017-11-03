---
title: "Bibliotecas de gestão de Event Hubs do Azure | Microsoft Docs"
description: "Gerir espaços de nomes de Event Hubs e entidades do .NET"
services: event-hubs
cloud: na
documentationcenter: na
author: sethmanheim
manager: timlt
ms.assetid: 
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/15/2017
ms.author: sethm
ms.openlocfilehash: 0d659cb860a6c98342b548212820efe046decfcc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="event-hubs-management-libraries"></a>Bibliotecas de gestão de Hubs de eventos

As bibliotecas de gestão de Event Hubs dinamicamente podem aprovisionar espaços de nomes de Event Hubs e entidades. Isto permite que com implementações complexas e cenários de mensagens, para que programaticamente pode determinar que entidades para aprovisionar. Estas bibliotecas estão atualmente disponíveis para o .NET.

## <a name="supported-functionality"></a>Funcionalidades suportadas

* Criação de espaço de nomes, a atualização, a eliminação
* Criação de Hubs de eventos, a atualização, a eliminação
* A criação do grupo de consumidores, a atualização, a eliminação

## <a name="prerequisites"></a>Pré-requisitos

Para começar a utilizar as bibliotecas de gestão de Event Hubs, tem de autenticar com o Azure Active Directory (AAD). AAD requer que o se autenticar como um principal de serviço, que fornece acesso aos recursos do Azure. Para obter informações sobre como criar um serviço principal, consulte um dos seguintes artigos:  

* [Utilizar o portal do Azure para criar aplicação do Active Directory e um principal de serviço que pode aceder a recursos](../azure-resource-manager/resource-group-create-service-principal-portal.md)
* [Utilizar o Azure PowerShell para criar um principal de serviço para aceder aos recursos](../azure-resource-manager/resource-group-authenticate-service-principal.md)
* [Utilizar a CLI do Azure para criar um principal de serviço para aceder aos recursos](../azure-resource-manager/resource-group-authenticate-service-principal-cli.md)

Estes tutoriais fornecem-lhe um `AppId` (ID de cliente), `TenantId`, e `ClientSecret` (chave de autenticação), todos os que são utilizados para autenticação, as bibliotecas de gestão. Tem de ter **proprietário** permissões para o grupo de recursos no qual pretende executar.

## <a name="programming-pattern"></a>Padrão de programação

O padrão para manipular a qualquer recurso dos Event Hubs segue um protocolo comum:

1. Obter um token do AAD utilizando o `Microsoft.IdentityModel.Clients.ActiveDirectory` biblioteca.
    ```csharp
    var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

    var result = await context.AcquireTokenAsync(
        "https://management.core.windows.net/",
        new ClientCredential(clientId, clientSecret)
    );
    ```

1. Criar o `EventHubManagementClient` objeto.
    ```csharp
    var creds = new TokenCredentials(token);
    var ehClient = new EventHubManagementClient(creds)
    {
        SubscriptionId = SettingsCache["SubscriptionId"]
    };
    ```

1. Definir o `CreateOrUpdate` parâmetros para os valores especificados.
    ```csharp
    var ehParams = new EventHubCreateOrUpdateParameters()
    {
        Location = SettingsCache["DataCenterLocation"]
    };
    ```

1. Execute a chamada.
    ```csharp
    await ehClient.EventHubs.CreateOrUpdateAsync(resourceGroupName, namespaceName, EventHubName, ehParams);
    ```

## <a name="next-steps"></a>Passos seguintes
* [Amostra de gestão de .NET](https://github.com/Azure-Samples/event-hubs-dotnet-management/)
* [Referência de Microsoft.Azure.Management.EventHub](/dotnet/api/Microsoft.Azure.Management.EventHub) 
