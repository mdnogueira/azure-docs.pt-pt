---
title: Escolher entre cloud ou servidor do Azure MFA | Microsoft Docs
description: "Escolha a solução de segurança de autenticação multifator adequada para si ao perguntar o que está a tentar proteger e onde estão localizados os seus utilizadores.  Em seguida, selecione a nuvem, o servidor MFA ou o AD FS."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: ec2270ea-13d7-4ebc-8a00-fa75ce6c746d
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/25/2017
ms.author: kgremban
ms.openlocfilehash: cc5a0a8ff789b878cd5afb62176ae8bcf3f80902
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="choose-the-azure-multi-factor-authentication-solution-for-you"></a>Escolher a solução do Servidor Multi-Factor Authentication para si
Uma vez que existem vários tipos de Multi-Factor Authentication do Azure (MFA), é necessário responder a algumas questões para descobrir qual a versão mais adequada a utilizar.  Estas perguntas são:

* [O que estou a tentar proteger](#what-am-i-trying-to-secure)
* [Onde estão localizados os utilizadores](#where-are-the-users-located)
* [Que funcionalidades preciso?](#what-featured-do-i-need)

As secções seguintes fornecem documentação de orientação sobre como determinar cada uma destas respostas.

## <a name="what-am-i-trying-to-secure"></a>O que estou a tentar proteger?
Para determinar a solução de verificação de dois passos correta, primeiro é necessário responder à pergunta sobre o que está a tentar proteger com um segundo método de autenticação.  É uma aplicação que está no Azure?  Ou um sistema de acesso remoto?  Ao determinar o que estamos a tentar proteger, podemos responder à pergunta sobre onde a Multi-Factor Authentication tem de ser ativada.  

| O que está a tentar proteger | MFA na nuvem | Servidor MFA |
| --- |:---:|:---:|
| Aplicações Microsoft originais |● |● |
| Aplicações SaaS na galeria de aplicações |● |  |
| Aplicações Web publicadas através do Proxy de Aplicação do Azure AD |● |  |
| Aplicações IIS não publicadas através do Proxy de Aplicação do Azure AD | |● |
| Acesso remoto, tais como VPN, RDG | ● | ● |

## <a name="where-are-the-users-located"></a>Onde estão localizados os utilizadores
Em seguida, verificar onde estão localizados os nossos utilizadores ajuda a determinar a solução correta a utilizar, seja na nuvem ou no local através do Servidor MFA.

| Localização do Utilizador | MFA na nuvem | Servidor MFA |
| --- |:---:|:---:|
| Azure Active Directory |● | |
| Azure AD e AD no local utilizando federação com o AD FS |● |● |
| Azure AD e AD no local utilizando DirSync, Azure AD Sync, Azure AD Connect - sem sincronização de palavras-passe |● |● |
| Azure AD e AD no local utilizando DirSync, Azure AD Sync, Azure AD Connect - com sincronização de palavras-passe |● | |
| Active Directory no local | |● |

## <a name="what-features-do-i-need"></a>Que funcionalidades preciso?
A tabela seguinte compara as funcionalidades disponíveis do Multi-Factor Authentication na nuvem e do Servidor Multi-Factor Authentication.

| Funcionalidade | MFA na nuvem | Servidor MFA |
| --- |:---:|:---:|
| Notificação da aplicação móvel como um segundo fator | ● | ● |
| Código de verificação da aplicação móvel como um segundo fator | ● | ● |
| Chamada telefónica como segundo fator | ● | ● |
| SMS unidirecional como segundo fator | ● | ● |
| SMS bidirecional como segundo fator | | ● |
| Tokens de Hardware como segundo fator | | ● |
| Palavras-passe da aplicação para os clientes do Office 365 que não suportam MFA | ● | |
| Controlo de administração sobre métodos de autenticação | ● | ● |
| Modo PIN | | ● |
| Alerta de fraudes |● | ● |
| Relatórios do MFA |● | ● |
| Omissão de Uso Individual | | ● |
| Saudações personalizadas para chamadas telefónicas | ● | ● |
| ID do autor da chamada personalizável para chamadas telefónicas | ● | ● |
| IPs Fidedignos | ● | ● |
| Memorizar MFA para dispositivos fidedignos | ● | |
| Acesso condicional | ● | ● |
| Cache |  | ● |

## <a name="next-steps"></a>Passos seguintes

Agora que compreende a diferença entre o Azure Multi-factor Authentication na cloud ou o Servidor MFA no local, está na altura de configurar e utilizar o Azure Multi-factor Authentication. **Selecione o ícone que representa o seu cenário**

<center>

[![MFA na cloud](./media/multi-factor-authentication-get-started/cloud2.png)](multi-factor-authentication-get-started-cloud.md) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; [ ![Servidor MFA](./media/multi-factor-authentication-get-started/server2.png)](multi-factor-authentication-get-started-server.md) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;  </center>
