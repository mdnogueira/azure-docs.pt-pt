---
title: "Segurança para os Notification Hubs"
description: "Este tópico explica a segurança para os hubs de notificação do Azure."
services: notification-hubs
documentationcenter: .net
author: ysxu
manager: erikre
editor: 
ms.assetid: 6506177c-e25c-4af7-8508-a3ddca9dc07c
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 06/29/2016
ms.author: yuaxu
ms.openlocfilehash: 7c3283799806135060bb8ca57ea398c93d1106bb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="security"></a>Segurança
## <a name="overview"></a>Descrição geral
Este tópico descreve o modelo de segurança dos Notification Hubs do Azure. Uma vez que os Notification Hubs são uma entidade de barramento de serviço, implementam o mesmo modelo de segurança, como o Service Bus. Para obter mais informações, consulte o [autenticação de barramento de serviço](https://msdn.microsoft.com/library/azure/dn155925.aspx) tópicos.

## <a name="shared-access-signature-security-sas"></a>Segurança de assinatura de acesso partilhado (SAS)
Notification Hubs implementa um esquema de segurança ao nível da entidade chamado SAS (assinatura de acesso partilhado). Este esquema permite que as entidades de mensagens declarar até 12 regras de autorização na respetiva descrição que conceda direitos nessa entidade.

Cada regra contém um nome, um valor de chave (segredo partilhado) e um conjunto de direitos, conforme explicado na secção "Afirmações de segurança". Quando criar um Hub de notificação, duas regras são automaticamente criadas: uma com direitos de escutar (que utiliza a aplicação de cliente) e uma com todos os direitos (que utiliza o back-end de aplicação).

Quando efetuar a gestão do registo de aplicações de cliente, se as informações enviadas através de notificações não é confidenciais (por exemplo, atualizações de Meteorologia), uma forma comum para aceder a um Notification Hub é para dar o valor da chave da regra de acesso só de escuta para a aplicação de cliente e atribua o valor da chave de regra de acesso completo para o back-end da aplicação.

Não é recomendado que incorporar o valor da chave no cliente aplicações da loja Windows. Uma forma de evitar ao incorporar o valor da chave é ter a aplicação de cliente obtê-lo a partir do back-end da aplicação durante o arranque.

É importante compreender que a chave de acesso escuta permite uma aplicação de cliente para se registar para qualquer etiqueta. Se a aplicação tem de restringir registos para etiquetas específicas para os clientes específicos (por exemplo, quando as etiquetas representam os IDs de utilizador), o back-end da aplicação tem de efetuar os registos. Para obter mais informações, consulte Gestão de registo. Tenha em atenção que desta forma, a aplicação de cliente não terão acesso direto aos Hubs de notificação.

## <a name="security-claims"></a>Afirmações de segurança
Semelhante ao outras entidades, as operações de Hub de notificação são permitidas para três afirmações de segurança: escutar, enviar e gerir.

| Afirmação | Descrição | Operações permitidas |
| --- | --- | --- |
| Escutar |Criar/atualizar, ler e eliminar registos único |Criar/atualizar registo<br><br>Registo de leitura<br><br>Todos os registos para um identificador de leitura<br><br>Eliminar registo |
| Enviar |Enviar mensagens para o hub de notificação |Enviar mensagem |
| Gerir |CRUDs nos Notification Hubs (incluindo a atualizar as credenciais PNS e chaves de segurança) e leitura registos com base nas etiquetas |Hubs de notificação de criar/atualizar/leitura/eliminar<br><br>Registos de leitura por etiqueta |

Os Notification Hubs aceitam afirmações concedidas, os tokens de controlo de acesso do Microsoft Azure e os tokens de assinatura gerados com chaves partilhadas configuradas diretamente no Hub de notificação.

