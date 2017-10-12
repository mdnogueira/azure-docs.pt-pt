---
title: "O que é o cofre de chave do Azure? | Microsoft Docs"
description: "O cofre de chave do Azure ajuda a salvaguardar as chaves criptográficas e os segredos utilizados pelas aplicações em nuvem e pelos serviços. Ao utilizar o cofre de chave do Azure, os clientes podem encriptar chaves e segredos (tal como chaves de autenticação, chaves de conta de armazenamento, chaves de encriptação de dados, ficheiros .PFX e palavras-passe) utilizando as teclas que estejam protegidas por módulos de segurança de hardware (HSMs)."
services: key-vault
documentationcenter: 
author: cabailey
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: e759df6f-0638-43b1-98ed-30b3913f9b82
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/19/2017
ms.author: cabailey
ms.openlocfilehash: 3b679a2a8ba91a8a9dec0534a3d8930fcaa7afcf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-azure-key-vault"></a>O que é o cofre de chave do Azure?
O Cofre de Chaves do Azure chave está disponível na maior parte das regiões. Para obter mais informações, consulte a [página de preços do Cofre de Chaves](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="introduction"></a>Introdução
O cofre de chave do Azure ajuda a salvaguardar as chaves criptográficas e os segredos utilizados pelas aplicações em nuvem e pelos serviços. Ao utilizar o cofre de chaves do Azure, pode encriptar chaves e segredos (tal como chaves de autenticação, chaves de conta de armazenamento, chaves de encriptação de dados, ficheiros .PFX e palavras-passe) utilizando as teclas que estão protegidas por módulos de segurança de hardware (HSM). Para maior segurança, pode importar ou gerar chaves nos HSMs. Se optar por fazê-lo, a Microsoft processa as suas chaves nos HSMs validados por FIPS 140-2 de Nível 2 (hardware e firmware).  

A chave de cofre simplifica o processo de gestão de chaves e permite-lhe manter o controlo de teclas que acede e encripta os seus dados. Os programadores podem criar as chaves de desenvolvimento e teste em minutos e migrá-las totalmente para as chaves de produção. Os administradores de segurança podem conceder (e revogar) a permissão para as chaves, conforme necessário.

Utilize a tabela seguinte para melhor compreender como a chave de cofre pode ajudar a satisfazer as necessidades dos programadores e dos administradores de segurança.

| Função | Declaração do problema | Resolvido pelo cofre de chave do Azure |
| --- | --- | --- |
| Programador de uma aplicação do Azure |"Quero criar uma aplicação para o Azure que utilize chaves para assinatura e encriptação, mas quero que essas chaves sejam externas à minha aplicação, para que a solução seja adequada a uma aplicação que é distribuída geograficamente. <br/><br/>Também quero que as chaves e os segredos estejam protegidos, sem que tenha de escrever eu próprio o código. E também quero que seja fácil utilizar as chaves e os segredos a partir das minhas aplicações, com um desempenho ótimo”. |As chaves √ são armazenadas num cofre e invocadas pelo URI quando necessário.<br/><br/> √ As chaves são salvaguardadas pelo Azure, utilizando os algoritmos de norma da indústria, os comprimentos de chave e os módulos de segurança de hardware (HSMs).<br/><br/> √ As chaves são processadas nos HSMs que residem no mesmos datacenters do Azure que as aplicações. Desta forma, é proporcionada mais fiabilidade e latência reduzida do que se as chaves residissem numa localização separada, como no local. |
| Programador de software como um serviço (SaaS) |"Não quero a responsabilidade ou um potencial encargo para chaves e segredos dos inquilino dos meus clientes. <br/><br/>Pretendo que os clientes sejam proprietário e efetuem a gestão das suas próprias chaves para que eu me possa concentrar no que sei fazer de melhor, ou seja, fornecer as principais funcionalidades de software." |Os clientes √ podem importar as suas próprias chaves para o Azure e geri-las. Se uma aplicação SaaS tiver de fazer operações de criptografia com as chaves dos respetivos clientes, o Cofre de Chaves faz essas operações em nome da aplicação. A aplicação não consegue ver as chaves dos clientes. |
| Responsável pela segurança (CSO) |"Quero saber se as nossas aplicações estão em conformidade com os HSMs FIPS 140-2 de nível 2 para uma gestão de chaves segura. <br/><br/>Pretendo certificar-me de que a minha organização está a controlar o ciclo de vida das chave e pode monitorizar a utilização das mesmas. <br/><br/>E embora utilizemos vários serviços e recursos do Azure, pretendo gerir as chaves a partir de uma única localização no Azure." |Os HSMs √ têm a certificação FIPS 140-2 de nível 2 validada.<br/><br/>A chave do cofre √ foi concebida para que a Microsoft não consulte ou extraia as suas chaves.<br/><br/>√ Próximo do registo em tempo real de utilização da chave.<br/><br/>√ O cofre fornece uma interface única, independentemente de quantos cofres tiver no Azure, de quantas regiões suporta e quais as aplicações em que os utiliza. |

Qualquer pessoa com uma subscrição do Azure pode criar e utilizar cofres de chave. Apesar de a chave de cofre beneficiar os programadores e os administradores de segurança, poderia ser implementada e gerida pelo administrador de uma organização que gere outros serviços do Azure para uma organização. Por exemplo, este administrador deverá iniciar sessão com uma subscrição do Azure, criar um cofre para a organização na qual pretende armazenar chaves e, em seguida, ser responsável por tarefas operacionais como:

* Criar ou importar uma chave ou segredo
* Revogar ou eliminar uma chave ou segredo
* Autorizar aos utilizadores ou aplicações o acesso à chave de cofre para que possam gerir ou utilizar as suas chaves e segredos
* Configurar a utilização da chave (por exemplo, iniciar sessão ou encriptar)
* Monitorizar a utilização da chave

Este administrador iria fornecer URIs aos programadores para os contactar a partir das suas aplicações e iria fornecer também ao respetivo administrador de segurança as informações de utilização da chave de registo. 

   ![Descrição geral do cofre de chave do Azure][1]

Os programadores também podem gerir as chaves diretamente, com APIs. Para obter mais informações, consulte o [manual do programador da chave](key-vault-developers-guide.md).

## <a name="next-steps"></a>Passos Seguintes
Para uma introdução tutorial para um administrador, consulte [Introdução ao cofre de chave do Azure](key-vault-get-started.md).

Para obter mais informações sobre o registo de utilização do cofre de chave, consulte [Registo do cofre de chave do Azure](key-vault-logging.md).

Para obter mais informações sobre a utilização de chaves e segredos com o Cofre de Chaves do Azure, veja [About Keys, Secrets, and Certificates (Sobre Chaves, Segredos e Certificados)](https://msdn.microsoft.com/library/azure/dn903623\(v=azure.1\).aspx).

<!--Image references-->
[1]: ./media/key-vault-whatis/AzureKeyVault_overview.png
