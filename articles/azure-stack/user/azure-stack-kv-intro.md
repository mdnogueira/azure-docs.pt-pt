---
title: "Introdução Cofre de chaves de pilha do Azure | Microsoft Docs"
description: Saiba como o Cofre de chaves de pilha do Azure gere chaves e segredos
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 70f1684a-3fbb-4cd1-bf29-9f9882e98fe9
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/04/2017
ms.author: sngun
ms.openlocfilehash: 621a0cb865d0c050d7271d10bd14076f9f0c6f67
ms.sourcegitcommit: 963e0a2171c32903617d883bb1130c7c9189d730
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2017
---
# <a name="introduction-to-key-vault-in-azure-stack"></a>Introdução ao Cofre de chaves na pilha do Azure

## <a name="prerequisites"></a>Pré-requisitos 

* Tem de subscrever uma oferta, que inclui o serviço Cofre de chaves do Azure.  
* [PowerShell está configurado para utilização com o Azure pilha](azure-stack-powershell-configure-user.md).
 
## <a name="key-vault-basics"></a>Noções básicas do Cofre de chaves
O Cofre de chaves na pilha do Azure ajuda a salvaguardar as chaves criptográficas e segredos aplicações e serviços da nuvem utilizam. Ao utilizar o Cofre de chaves, pode encriptar chaves e segredos, tais como:
   * Chaves de autenticação 
   * Chaves de conta de armazenamento
   * Chaves de encriptação de dados
   * ficheiros. pfx
   * Palavras-passe

A chave de cofre simplifica o processo de gestão de chaves e permite-lhe manter o controlo de teclas que acede e encripta os seus dados. Os programadores podem criar as chaves de desenvolvimento e teste em minutos e migrá-las totalmente para as chaves de produção. Os administradores de segurança podem conceder (e revogar) a permissão para as chaves, conforme necessário.

Qualquer pessoa com uma subscrição de pilha do Azure pode criar e utilizar cofres de chaves. Embora a chave de cofre beneficiar os programadores e administradores de segurança, o operador que gere outros serviços de pilha do Azure para uma organização pode implementar e geri-lo. Por exemplo, a pilha de Azure operador pode iniciar sessão com uma subscrição de pilha do Azure, criar um cofre para a organização na qual pretende armazenar chaves e, em seguida, ser responsável por estas tarefas operacionais:

* Criar ou importar uma chave ou segredo.
* Revogar ou eliminar uma chave ou segredo.
* Autorize utilizadores ou aplicações para aceder ao Cofre de chaves, por isso, em seguida, pode gerir ou utilizar as suas chaves e segredos.
* Configurar a utilização de chave (por exemplo, iniciar sessão ou encriptar).

O operador, em seguida, pode fornecer aos programadores com identificadores de recurso uniforme (URI) para chamar a partir das suas aplicações. Os operadores também podem fornecer aos administradores de segurança com informações de registo de utilização de chave.

Os programadores também podem gerir as chaves diretamente, com APIs. Para obter mais informações, consulte o Guia do programador do Cofre de chaves.

## <a name="scenarios"></a>Cenários
Os cenários seguintes descrevem como o Cofre de chaves pode ajudar a satisfazer as necessidades de programadores e administradores de segurança.

### <a name="developer-for-an-azure-stack-application"></a>Programador de uma aplicação de pilha do Azure
**Problema:** pretendo que escrever uma aplicação para a pilha do Azure que utilize chaves para assinatura e encriptação. Pretendo que estas chaves sejam externas à minha aplicação, para que a solução seja adequada para uma aplicação que é distribuída geograficamente.

**Declaração:** chaves são armazenadas num cofre e invocadas por um URI, quando necessário.

### <a name="developer-for-software-as-a-service-saas"></a>Programador de software como serviço (SaaS)
**Problema:** não quero a responsabilidade de responsabilidade ou um potencial para chaves e segredos do meu cliente. Pretendo que os clientes sejam proprietário e gerir as respetivas chaves, para que eu me possa concentrar no que sei fazer de melhor, ou seja, fornecer as principais funcionalidades de software.

**Declaração:** clientes podem importar as suas próprias chaves para a pilha do Azure e, em seguida, geri-los. 

### <a name="chief-security-officer-cso"></a>Diretor responsável pela segurança (CSO)
**Problema:** pretendo certificar-se de que a minha organização está em controlo do ciclo de vida chave e pode monitorizar a utilização de chave.

**Declaração:** Cofre de chaves foi concebido para que o Microsoft não consulte ou extraia as suas chaves. Quando uma aplicação precisa de realizar operações de criptografia utilizando chaves dos clientes, o Cofre de chaves utiliza as chaves em nome da aplicação. A aplicação não consegue ver as chaves dos clientes. Embora utilizemos vários serviços de pilha do Azure e recursos, pode gerir as chaves a partir de uma localização única na pilha do Azure. O Cofre fornece uma única interface, independentemente de cofres de quantos tiver na pilha do Azure, em que regiões suporte e as aplicações que utilizam.

## <a name="next-steps"></a>Passos seguintes

* [Gerir o Cofre de chaves na pilha do Azure através do portal](azure-stack-kv-manage-portal.md)  
* [Gerir o Cofre de chaves na pilha do Azure utilizando o PowerShell](azure-stack-kv-manage-powershell.md)

