---
title: "Precisa de ajuda com o portal de aplicações My no Azure Active Directory | Microsoft Docs"
description: "Obter as instruções para efetuar tarefas comuns ao trabalhar com o painel de acesso."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: c67cd675-b567-41e1-8bc2-e06fe0b38d3b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/21/2017
ms.author: markvi
ms.reviewer: japere
ms.openlocfilehash: 9e2f8aa6ad7534ff822907285aa9fe290a4db586
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2017
---
# <a name="do-you-need-help-with-the-my-apps-portal"></a>Tem de ajudar com o portal de aplicações My?

Provavelmente atingiu esta página porque Infelizmente estivesse a executar no problema quando utilizar o portal de aplicações My. Enquanto existirem casos que requerem que contacte o suporte técnico ou o administrador para obter um problema resolvido, aqui estão alguns tópicos de resolução de problemas que poderão ajudar a, primeiro.

## <a name="i-am-having-trouble-signing-into-the-my-apps-portal"></a>Estou com dificuldades a iniciar sessão no portal do meu aplicações

Problemas gerais para verificar:

- Verifique se está a iniciar sessão para o URL correto: [https://myapps.microsoft.com](https://myapps.microsoft.com)

- Tente adicionar o URL para sites fidedignos do browser.

- Certifique-se a palavra-passe não está expirada ou esquecimento. Verifique [aqui](active-directory-passwords-update-your-own-password.md) para obter mais detalhes sobre como atualizar a palavra-passe.

- Verifique se as informações de contacto de autenticação são até Data e não a bloquear o acesso. Verifique [aqui](https://docs.microsoft.com/en-us/azure/multi-factor-authentication/end-user/multi-factor-authentication-end-user) para obter mais detalhes sobre como configurar as suas informações de autenticação.

- Tente limpar os cookies do browser e, em seguida, tente voltar a iniciar sessão.

Se ainda tiver com problemas ao tentar iniciar sessão, contacte o administrador para obter ajuda.


## <a name="how-do-i-update-my-password"></a>Como atualizar a minha palavra-passe?

Se esqueceu a sua palavra-passe, nunca recebeu um da sua equipa de TI, foi bloqueado fora da sua conta ou pretender alterá-lo, consulte [ajuda, esqueci minha palavra-passe do Azure AD](active-directory-passwords-update-your-own-password.md) para obter mais detalhes.

## <a name="how-do-i-register-for-password-reset"></a>Como registar para a reposição de palavra-passe?

Como um utilizador final, pode repor a palavra-passe ou desbloquear a conta sem ter de enunciar para uma pessoa que utiliza a reposição de palavra-passe self-service (SSPR). Antes de poder utilizar esta funcionalidade, tem de registar os métodos de autenticação ou confirmar os métodos de autenticação predefinidos que o administrador tem preenchido. Para obter mais detalhes, consulte [registar para a reposição de palavra-passe self-service](active-directory-passwords-reset-register.md).


## <a name="i-am-having-trouble-installing-the-my-apps-portal-browser-extension"></a>Estou a ter problemas ao instalar a extensão de portal browser aplicações My

Verifique se está a cumprir os requisitos do browser:

- O portal necessita de um browser que suporte JavaScript e ativou CSS. Se estiver a utilizar palavra-passe de aplicações baseadas no único início de sessão, a extensão associada tem de estar instalada, bem como. Esta extensão é transferida automaticamente quando iniciarem uma aplicação que está configurada para a palavra-passe de aplicações baseadas no único início de sessão.

- Os requisitos do browser para a extensão são:
    - Internet Explorer 8, 9, 10, 11 no Windows 7 ou posterior
    - Limite de aniversário da edição do Windows 10 ou posterior
    - No Windows 7 ou posterior e no MacOS X ou posterior do Chrome
    - Firefox 26.0 ou posterior no Windows XP SP2 ou posterior e no Mac OS X 10.6 ou posterior

Também pode transferir a extensão para Chrome e limite de ligações diretas abaixo:

- [Extensões do Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

- [Extensão de limite](https://www.microsoft.com/store/apps/9pc9sckkzk84)

Após a instalação tente os seguintes passos, se tiver problemas com:

- Verifique nas definições da extensão de browser que a extensão está ativada.

- Reinicie o seu browser e inicie sessão no portal do meu aplicações.

- Limpar os cookies do browser e inicie sessão no portal do meu aplicações.

## <a name="how-do-i-add-a-new-app"></a>Como posso adicionar uma nova aplicação?

1.  No **aplicações** página, clique em **Adicionar aplicação**.

2.  Procure a aplicação que pretende adicionar e, em seguida, clique em **adicionar**.

**Comentários:**

- Só tem acesso a esta opção se o administrador tiver ativado esta para a sua conta.

- Se a aplicação necessita de permissão, poderá ter de aguardar a aprovação do administrador.


## <a name="how-do-i-manage-my-group-memberships"></a>Como gerir a minha associações?

1. Clique no mosaico da aplicação de grupos. 
2. Para criar um grupo, em grupos de proprietário, clique em criar grupo e, em seguida, siga as instruções.
3. Para aderir a um grupo, em grupos sou in, clique em aderir a grupo e, em seguida, siga as instruções.

**Comentários:**

- Só tem acesso a esta opção se o administrador tiver ativado esta para a sua conta.

- São membros de grupos permitem-lhe ver os detalhes e deixe o grupo.

- Grupos que são proprietários de permite ver mais detalhes, adicionar ou remover membros e deixam o grupo.


## <a name="next-steps"></a>Passos seguintes

Para informações relacionadas com a resolução de problemas, consulte [problemas ao utilizar a aplicação móvel ou um site do painel de acesso de aplicação](active-directory-application-access-panel-content-map.md)

