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
ms.date: 11/14/2017
ms.author: sethm
ms.openlocfilehash: b8b5887f2003dd793ae7a50f066b893f685002a0
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="service-bus-authentication-and-authorization"></a>Autenticação e autorização do Service Bus

Aplicações obterem acesso aos recursos do Azure Service Bus utilizando a autenticação de token de assinatura de acesso partilhado (SAS). Com SAS, aplicações apresentam um token de barramento de serviço que foi assinada com uma chave simétrica conhecido ambos para o emissor de token e o Service Bus (, por conseguinte, "partilhado") e essa chave é diretamente associadas uma regra de conceder direitos de acesso específicos, como a permissão para escutar/receber ou enviar mensagens. As regras de SAS são que está configurado no espaço de nomes ou diretamente nos entidades, tais como uma fila ou um tópico, permitindo para controlo de acesso detalhada.

Os tokens SAS podem ser gerados por um cliente de Service Bus diretamente, ou pode ser geradas por alguns token intermédio emitir o ponto final com o qual o cliente interage. Por exemplo, um sistema pode exigir o cliente chamar um Active Directory autorização protegido web ponto final de serviço para provar a respetiva identidade e direitos de acesso do sistema e o serviço web, em seguida, devolve o Service Bus adequado token. Este token SAS pode ser facilmente gerado utilizando o fornecedor de tokens do Service Bus incluído no SDK do Azure. 

> [!IMPORTANT]
> Se estiver a utilizar o Azure Active Directory controlo de acesso (também conhecido como o serviço de controlo de acesso ou ACS) com o Service Bus, tenha em atenção que o suporte para que este método é agora limitado e se deve migrar a sua aplicação para utilizar SAS. Para obter mais informações, consulte [esta mensagem de blogue](https://blogs.msdn.microsoft.com/servicebus/2017/06/01/upcoming-changes-to-acs-enabled-namespaces/) e [neste artigo](service-bus-migrate-acs-sas.md).

## <a name="shared-access-signature-authentication"></a>Autenticação da assinatura de acesso partilhada

[Autenticação de SAS](service-bus-sas.md) permite-lhe conceder acesso de utilizadores aos recursos do Service Bus, com direitos específicos. Autenticação de SAS no Service Bus envolve a configuração de uma chave criptográfica com direitos associados num recurso de Service Bus. Os clientes, em seguida, podem ter acesso a esse recurso através da apresentação de um token SAS, que consiste o acedidos de URI do recurso e uma expiração assinada com a chave configurada.

Pode configurar as chaves de SAS num espaço de nomes do Service Bus. A chave aplica-se a todas as entidades de mensagens dentro desse espaço de nomes. Também pode configurar as chaves nos tópicos e filas do Service Bus. Também é suportado o SAS [Azure reencaminhamento](../service-bus-relay/relay-authentication-and-authorization.md).

Para utilizar SAS, pode configurar um [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) objeto num espaço de nomes, fila ou tópico. Esta regra é composta pelos seguintes elementos:

* *KeyName*: identifica a regra.
* *PrimaryKey*: uma chave criptográfica utilizada para início de sessão/validar os SAS tokens.
* *SecondaryKey*: uma chave criptográfica utilizada para início de sessão/validar os SAS tokens.
* *Direitos*: representa a coleção de **escutar**, **enviar**, ou **gerir** direitos concedidos.

Regras de autorização de configurada a nível do espaço de nomes podem conceder acesso a todas as entidades num espaço de nomes para os clientes com tokens assinados utilizando a chave correspondente. Pode configurar até 12 dessas regras de autorização num espaço de nomes de barramento de serviço, fila ou tópico. Por predefinição, um [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) com todos os direitos está configurado para cada espaço de nomes ao que está a ser aprovisionado pela primeira vez.

Para aceder a uma entidade, o cliente requer um token SAS gerado utilizando específico [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). O token SAS é gerado utilizando o HMAC SHA256 de uma cadeia de recurso que constituem o URI do recurso para o qual o acesso foi reclamado e uma expiração com uma chave criptográfica associada com a regra de autorização.

Suporte de autenticação SAS para o Service Bus é incluído nas versões do SDK do .NET 2.0 e posterior. SAS inclui suporte para um [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Todas as APIs que aceitam uma cadeia de ligação como um parâmetro incluem suporte para cadeias de ligação de SAS.

## <a name="next-steps"></a>Passos seguintes

- Continuar a ler [autenticação de Service Bus com assinaturas de acesso partilhado](service-bus-sas.md) para obter mais detalhes sobre SAS.
- Como [migrar a partir do Azure Active Directory acesso controlo (ACS) para autorização de assinatura de acesso partilhado](service-bus-migrate-acs-sas.md).
- [Espaços de nomes de alterações para ACS ativada](https://blogs.msdn.microsoft.com/servicebus/2017/06/01/upcoming-changes-to-acs-enabled-namespaces/).
- Para obter informações correspondentes sobre o reencaminhamento do Azure autenticação e autorização, consulte [Azure reencaminhamento autenticação e autorização](../service-bus-relay/relay-authentication-and-authorization.md). 

