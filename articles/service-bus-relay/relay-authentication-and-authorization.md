---
title: "Autorização e autenticação de reencaminhamento do Azure | Microsoft Docs"
description: "Descrição geral da autenticação da assinatura de acesso partilhado (SAS) no reencaminhamento do Azure"
services: service-bus-relay
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2017
ms.author: sethm
ms.openlocfilehash: 4ef8cbf22f2fcd7017af16083240608e5ca0fb5e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-relay-authentication-and-authorization"></a>Autorização e autenticação de reencaminhamento do Azure

As aplicações podem autenticar para reencaminhamento do Azure utilizando a autenticação de assinatura de acesso partilhado (SAS). Autenticação de SAS permite que as aplicações autenticar para o serviço de reencaminhamento do Azure com uma chave de acesso configurada no espaço de nomes de reencaminhamento. Em seguida, pode utilizar esta chave para gerar um token de assinatura de acesso partilhado que os clientes podem utilizar para autenticar para o serviço de reencaminhamento.

## <a name="shared-access-signature-authentication"></a>Autenticação da assinatura de acesso partilhada
[Autenticação de SAS](../service-bus-messaging/service-bus-sas.md) permite-lhe conceder um acesso de utilizador a recursos de reencaminhamento do Azure com direitos específicos. Autenticação de SAS envolve a configuração de uma chave criptográfica com direitos associados num recurso. Os clientes, em seguida, podem ter acesso a esse recurso através da apresentação de um token SAS, que consiste o acedidos de URI do recurso e uma expiração assinada com a chave configurada.

Pode configurar as chaves de SAS num espaço de nomes de reencaminhamento. Ao contrário das mensagens do Service Bus, [ligações híbridas de reencaminhamento](relay-hybrid-connections-protocol.md) suporta os remetentes não autorizados ou anonymous. Pode ativar acesso anónimo para a entidade ao criar, conforme mostrado na captura a partir do portal de ecrã seguinte:

![][0]

Para utilizar SAS, pode configurar um [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) objeto de um espaço de nomes de reencaminhamento que consiste no seguinte:

* *KeyName* que identifica a regra.
* *PrimaryKey* é uma chave criptográfica utilizada para início de sessão/validar os SAS tokens.
* *SecondaryKey* é uma chave criptográfica utilizada para início de sessão/validar os SAS tokens.
* *Direitos* que representa a coleção de escuta, enviar ou faça a gestão de direitos concedidos.

Regras de autorização de configurada a nível do espaço de nomes podem conceder acesso a todas as ligações de reencaminhamento num espaço de nomes para os clientes com tokens assinados utilizando a chave correspondente. Até 12 essa autorização regras podem ser configuradas num espaço de nomes de reencaminhamento. Por predefinição, um [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) com todos os direitos está configurado para cada espaço de nomes ao que está a ser aprovisionado pela primeira vez.

Para aceder a uma entidade, o cliente requer um token SAS gerado utilizando específico [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). O token SAS é gerado utilizando o HMAC SHA256 de uma cadeia de recurso que constituem o URI do recurso para o qual o acesso foi reclamado e uma expiração com uma chave criptográfica associada com a regra de autorização.

Suporte de autenticação SAS para o reencaminhamento do Azure está incluído nas versões do SDK do .NET 2.0 e posterior. SAS inclui suporte para um [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Todas as APIs que aceitam uma cadeia de ligação como um parâmetro incluem suporte para cadeias de ligação de SAS.

## <a name="next-steps"></a>Passos seguintes
- Continuar a ler [autenticação de Service Bus com assinaturas de acesso partilhado](../service-bus-messaging/service-bus-sas.md) para obter mais detalhes sobre SAS.
- Consulte o [guia de protocolo do Azure reencaminhamento híbrido ligações](relay-hybrid-connections-protocol.md) para obter informações detalhadas sobre a capacidade de ligações híbridas.
- Para obter informações correspondentes sobre mensagens do Service Bus autenticação e autorização, consulte [barramento de serviço de autenticação e autorização](../service-bus-messaging/service-bus-authentication-and-authorization.md). 

[0]: ./media/relay-authentication-and-authorization/hcanon.png