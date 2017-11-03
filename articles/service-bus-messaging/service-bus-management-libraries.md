---
title: "Bibliotecas de gestão do Service Bus do Azure | Microsoft Docs"
description: "Gerir espaços de nomes do Service Bus e entidades do .NET de mensagens."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 10/18/2017
ms.author: sethm
ms.openlocfilehash: 3b7096a073b509217a6ed29b53f88f912e6613f6
ms.sourcegitcommit: d6ad3203ecc54ab267f40649d3903584ac4db60b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2017
---
# <a name="service-bus-management-libraries"></a>Bibliotecas de gestão do Service Bus

As bibliotecas de gestão do Service Bus do Azure podem provisionam de espaços de nomes do Service Bus e entidades. Isto permite com implementações complexas e cenários de mensagens e torna possível através de programação determinar que entidades para aprovisionar. Estas bibliotecas estão atualmente disponíveis para o .NET.

## <a name="supported-functionality"></a>Funcionalidades suportadas

* Criação de espaço de nomes, a atualização, a eliminação
* Criação da fila, a atualização, a eliminação
* Criação de tópico, a atualização, a eliminação
* Criação da subscrição, a atualização, a eliminação

## <a name="prerequisites"></a>Pré-requisitos

Para começar a utilizar as bibliotecas de gestão do Service Bus, tem de autenticar com o serviço do Azure Active Directory (AAD). AAD requer que o se autenticar como um principal de serviço, que fornece acesso aos recursos do Azure. Para obter informações sobre como criar um serviço principal, consulte um dos seguintes artigos:  

* [Utilizar o portal do Azure para criar aplicação do Active Directory e um principal de serviço que pode aceder a recursos](/azure/azure-resource-manager/resource-group-create-service-principal-portal)
* [Utilizar o Azure PowerShell para criar um principal de serviço para aceder aos recursos](/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [Utilizar a CLI do Azure para criar um principal de serviço para aceder aos recursos](/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)

Estes tutoriais fornecem-lhe um `AppId` (ID de cliente), `TenantId`, e `ClientSecret` (chave de autenticação), todos os que são utilizados para autenticação, as bibliotecas de gestão. Tem de ter **proprietário** permissões para o grupo de recursos no qual pretende executar.

## <a name="programming-pattern"></a>Padrão de programação

O padrão para manipular a qualquer recurso de Service Bus segue um protocolo comum:

1. Obter um token do Azure Active Directory utilizando o **Microsoft.IdentityModel.Clients.ActiveDirectory** biblioteca.
   ```csharp
   var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

   var result = await context.AcquireTokenAsync("https://management.core.windows.net/", new ClientCredential(clientId, clientSecret));
   ```
2. Criar o `ServiceBusManagementClient` objeto.

   ```csharp
   var creds = new TokenCredentials(token);
   var sbClient = new ServiceBusManagementClient(creds)
   {
       SubscriptionId = SettingsCache["SubscriptionId"]
   };
   ```
3. Definir o `CreateOrUpdate` parâmetros para os valores especificados.

   ```csharp
   var queueParams = new QueueCreateOrUpdateParameters()
   {
       Location = SettingsCache["DataCenterLocation"],
       EnablePartitioning = true
   };
   ```
4. Execute a chamada.

   ```csharp
   await sbClient.Queues.CreateOrUpdateAsync(resourceGroupName, namespaceName, QueueName, queueParams);
   ```

## <a name="next-steps"></a>Passos seguintes
* [Amostra de gestão de .NET](https://github.com/Azure-Samples/service-bus-dotnet-management/)
* [Referência de Microsoft.Azure.Management.ServiceBus API](/dotnet/api/Microsoft.Azure.Management.ServiceBus)
