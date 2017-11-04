---
title: "Autorização e autenticação de Service Bus do Azure | Microsoft Docs"
description: "Autentica aplicações para o Service Bus com autenticação de assinatura de acesso partilhado (SAS)."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 18bad0ed-1cee-4a5c-a377-facc4785c8c9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/09/2017
ms.author: sethm
ms.openlocfilehash: b4b9d5d272bdb172f1d40db379a519a4f617550a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="service-bus-authentication-and-authorization"></a>Autenticação e autorização do Service Bus

Aplicações obterem acesso para funções do Service Bus do Azure utilizando a autenticação de token de assinatura de acesso partilhado (SAS). Com SAS, aplicações apresentam um token de barramento de serviço que foi assinada com uma chave simétrica conhecido ambos para o emissor de token e o Service Bus ("partilhado") e essa chave é diretamente associadas uma regra de conceder direitos de acesso específicos, como a permissão para receber / escutar ou enviar mensagens. As regras de SAS são que está configurado no espaço de nomes ou diretamente nos entidades, como uma fila ou um tópico, permitindo para controlo de acesso detalhada.

Os tokens SAS podem ser gerados por um cliente de Service Bus diretamente, ou pode ser geradas por alguns token intermédio emitir o ponto final que o cliente interage com. Por exemplo, pode necessitar de um sistema de cliente chamar um Active Directory autorização protegido web ponto final de serviço para provar a sua direitos de acesso de identidade e de sistema e o serviço web, em seguida, irá devolver o token de Service Bus adequado. Este token SAS pode ser facilmente gerado utilizando o fornecedor de tokens do Service Bus incluído no SDK. 

> [!IMPORTANT]
> Se estiver a utilizar o Azure Active Directory controlo de acesso (também conhecido como o serviço de controlo de acesso ou ACS) em conjunto com o Service Bus, tenha em atenção que o suporte para que este método é agora limitado e se deve migrar a sua aplicação para através da SAS. Para obter mais informações, consulte [esta mensagem de blogue](https://blogs.msdn.microsoft.com/servicebus/2017/06/01/upcoming-changes-to-acs-enabled-namespaces/).

## <a name="shared-access-signature-authentication"></a>Autenticação da assinatura de acesso partilhada

[Autenticação de SAS](service-bus-sas.md) permite-lhe conceder um acesso de utilizador a recursos de barramento de serviço com direitos específicos. Autenticação de SAS no Service Bus envolve a configuração de uma chave criptográfica com direitos associados num recurso de Service Bus. Os clientes, em seguida, podem ter acesso a esse recurso através da apresentação de um token SAS, que consiste o acedidos de URI do recurso e uma expiração assinada com a chave configurada.

Pode configurar as chaves de SAS num espaço de nomes do Service Bus. A chave aplica-se a todas as entidades de mensagens esse espaço de nomes. Também pode configurar as chaves nos tópicos e filas do Service Bus. Também é suportado o SAS [Azure reencaminhamento](../service-bus-relay/relay-authentication-and-authorization.md).

Para utilizar SAS, pode configurar um [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) objeto num espaço de nomes, fila ou tópico. Esta regra é composta pelos seguintes elementos:

* *KeyName* que identifica a regra.
* *PrimaryKey* é uma chave criptográfica utilizada para início de sessão/validar os SAS tokens.
* *SecondaryKey* é uma chave criptográfica utilizada para início de sessão/validar os SAS tokens.
* *Direitos* que representa a coleção de escuta, enviar ou faça a gestão de direitos concedidos.

Regras de autorização de configurada a nível do espaço de nomes podem conceder acesso a todas as entidades num espaço de nomes para os clientes com tokens assinados utilizando a chave correspondente. Até 12 essa autorização regras podem ser configuradas num espaço de nomes de barramento de serviço, fila ou tópico. Por predefinição, um [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) com todos os direitos está configurado para cada espaço de nomes ao que está a ser aprovisionado pela primeira vez.

Para aceder a uma entidade, o cliente requer um token SAS gerado utilizando específico [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). O token SAS é gerado utilizando o HMAC SHA256 de uma cadeia de recurso que constituem o URI do recurso para o qual o acesso foi reclamado e uma expiração com uma chave criptográfica associada com a regra de autorização.

Suporte de autenticação SAS para o Service Bus é incluído nas versões do SDK do .NET 2.0 e posterior. SAS inclui suporte para um [SharedAccessAuthorizationRule](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Todas as APIs que aceitam uma cadeia de ligação como um parâmetro incluem suporte para cadeias de ligação de SAS.

## <a name="next-steps"></a>Passos seguintes

- Continuar a ler [autenticação de Service Bus com assinaturas de acesso partilhado](service-bus-sas.md) para obter mais detalhes sobre SAS.
- [As alterações para ACS ativada espaços de nomes.](https://blogs.msdn.microsoft.com/servicebus/2017/06/01/upcoming-changes-to-acs-enabled-namespaces/)
- Para obter informações correspondentes sobre o reencaminhamento do Azure autenticação e autorização, consulte [Azure reencaminhamento autenticação e autorização](../service-bus-relay/relay-authentication-and-authorization.md). 

