---
title: "Exceções de FabricClient comuns acionadas | Microsoft Docs"
description: "Descreve as exceções e erros que podem ser emitidos pelas APIs de FabricClient ao executar a aplicação e operações de cluster de gestão comuns."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: bb821313-b221-479f-b08e-36cf07e60a07
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/30/2017
ms.author: ryanwi
ms.openlocfilehash: fec85bcf1ce46374815fddcd21ab2e8f73cbd1df
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="common-exceptions-and-errors-when-working-with-the-fabricclient-apis"></a>Exceções e erros ao trabalhar com as APIs de FabricClient comuns
O [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient#System_Fabric_FabricClient) APIs permitem que os administradores de cluster e da aplicação efetuar tarefas administrativas numa aplicação, serviço ou cluster do Service Fabric. Por exemplo, implementação de aplicação, atualização e remoção, a verificação de estado de funcionamento de um cluster ou um serviço de teste. Os programadores de aplicações e os administradores de cluster podem utilizar as APIs de FabricClient para desenvolver as ferramentas para gerir as aplicações e o cluster do Service Fabric.

Existem vários tipos de operações que podem ser efetuadas utilizando FabricClient.  Cada método pode acionar exceções de erros causados por entrada incorreto, erros de runtime ou problemas de infraestrutura transitório.  Consulte a documentação de referência da API para localizar as exceções forem emitidas por um método específico. Existem algumas exceções, no entanto, que podem ser emitidas por muitas diferentes [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient#System_Fabric_FabricClient) APIs. A tabela seguinte lista as exceções que são comuns em FabricClient APIs.

| Exceção | Emitida quando |
| --- |:--- |
| [System.Fabric.FabricObjectClosedException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricobjectclosedexception#System_Fabric_FabricObjectClosedException) |O [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient#System_Fabric_FabricClient) objeto está num estado fechado. Eliminar o [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient#System_Fabric_FabricClient) objeto estiver a utilizar e instanciar um novo [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient#System_Fabric_FabricClient) objeto. |
| [System.TimeoutException](https://docs.microsoft.com/dotnet/core/api/system.timeoutexception#System_TimeoutException) |A operação excedeu o tempo limite. [OperationTimedOut](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode#System_Fabric_FabricErrorCode) é devolvido quando a operação demora mais do que maxoperationtimeout da conclusão. |
| [System. unauthorizedaccessexception](https://docs.microsoft.com/dotnet/core/api/system.unauthorizedaccessexception#System_UnauthorizedAccessException) |A verificação de acesso para a operação falhou. E_ACCESSDENIED é devolvido. |
| [System.Fabric.FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception#System_Fabric_FabricException) |Ocorreu um erro de tempo de execução ao efetuar a operação. Qualquer um dos métodos FabricClient potencialmente pode acionar [FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception#System_Fabric_FabricException), a [ErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception#System_Fabric_FabricException_ErrorCode) propriedade indica a causa exata da exceção. Códigos de erro são definidos no [FabricErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode#System_Fabric_FabricErrorCode) enumeração. |
| [System.Fabric.FabricTransientException](https://docs.microsoft.com/dotnet/api/system.fabric.fabrictransientexception#System_Fabric_FabricTransientException) |A operação falhou devido a uma condição de erro transitório tipo. Por exemplo, uma operação pode falhar porque um quórum de réplicas temporariamente não está acessível. Exceções transitórias correspondem às operações que podem ser repetidas. |

Alguns comuns [FabricErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode#System_Fabric_FabricErrorCode) erros que podem ser devolvidos num [FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception#System_Fabric_FabricException):

| Erro | Condição |
| --- |:--- |
| CommunicationError |Um erro de comunicação causou a operação falhar, repita a operação. |
| InvalidCredentialType |O tipo de credencial é inválido. |
| InvalidX509FindType |O X509FindType é inválido. |
| InvalidX509StoreLocation |O X509 localização do arquivo é inválida. |
| InvalidX509StoreName |O X509 nome do arquivo é inválido. |
| InvalidX509Thumbprint |O X509 cadeia de thumbprint do certificado é inválida. |
| InvalidProtectionLevel |O nível de proteção é inválido. |
| InvalidX509Store |Não é possível abrir o arquivo de certificados de X509. |
| InvalidSubjectName |O nome do requerente é inválido. |
| InvalidAllowedCommonNameList |O formato de cadeia da lista Nome comum é inválido. Deve ser uma lista separada por vírgulas. |

