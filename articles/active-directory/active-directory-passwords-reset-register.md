---
title: "Azure AD: Registo de reposição personalizada de palavra-passe | Microsoft Docs"
description: "Dados de autenticação de registo para reposição personalizada de palavra-passe"
services: active-directory
keywords: "Gestão de palavra-passe do Active Directory, gestão de palavra-passe, reposição personalizada de palavra-passe do Azure AD, SSPR"
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/16/2017
ms.author: joflore
ms.custom: end-user
translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: c6d3d9d8f399816928e794e8956bc35825462fb9
ms.lasthandoff: 04/25/2017


---
# <a name="register-for-self-service-password-reset"></a>Registar-se na reposição personalizada de palavra-passe

Como um utilizador final, se o administrador tiver ativado, pode repor a palavra-passe ou desbloquear a conta de utilizador, sem ter de falar com uma pessoa utilizando a reposição personalizada de palavra-passe (SSPR). Antes de poder utilizar esta funcionalidade, tem de registar os métodos de autenticação ou confirmar os métodos de autenticação predefinidos que o administrador tem preenchido.

## <a name="register-or-confirm-authentication-data-with-sspr"></a>Registar ou confirmar dados de autenticação com SSPR

1. Abra o browser no seu dispositivo e aceda à [página de registo de reposição de palavra-passe](http://aka.ms/ssprsetup)
2. Introduza o seu nome de utilizador e palavra-passe fornecidos pelo administrador
3. Dependendo das opções que o administrador aprovou, irá ver um ou mais dos seguintes itens para configurar ou verificar para serem utilizados se precisar de repor a palavra-passe
    * Telefone do escritório - esta opção só pode ser definida pelo seu administrador
    * Telefone de Autenticação - esta opção deve estar definida para outro número de telefone que teria acesso, como um telemóvel que recebe uma mensagem ou uma chamada (o administrador poderá preencher isto por si com o seu número de telemóvel se já tiver permissão para utilizar essa informação)
    * E-mail de Autenticação - esta opção deve ser definida para um endereço de e-mail alternativo que pode aceder sem a palavra-passe que precisar de repor
    * Perguntas de Segurança - esta opção fornece-lhe uma lista de perguntas que o administrador aprovou para o utilizador responder. Não pode utilizar a mesma resposta para mais do que uma pergunta.
4. Forneça e verifique as informações pedidas pelo seu administrador. Se estiver disponível mais do que uma opção, sugerimos que registe vários métodos para fornecer flexibilidade quando outro método não está disponível (exemplo: não é possível aceder ao seu telefone do escritório e em viagem)

    ![Registe métodos de autenticação e clique em Concluir][Register]

5. Quando concluir o passo 4, escolha **concluir** e agora poderá utilizar a reposição personalizada de palavra-passe quando precisar no futuro.

Se introduzir dados no Telefone de Autenticação ou no E-mail de Autenticação, não é visível no diretório global. As únicas pessoas que podem ver estes dados são os administradores. Apenas o utilizador pode ver as respostas às suas Perguntas de Segurança.

Os administradores podem pedir para confirmar os métodos de autenticação após um período de tempo, para assegurar que continua a ter os métodos adequados registados.

## <a name="next-steps"></a>Passos Seguintes

* [Como alterar a palavra-passe através da reposição personalizada de palavra-passe](active-directory-passwords-update-your-own-password.md)
* [Página de registo de reposição de palavra-passe](http://aka.ms/ssprsetup)
* [Portal de reposição de palavra-passe](https://passwordreset.microsoftonline.com/)
* [Não consegue iniciar sessão com a sua conta Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)

[Register]: ./media/active-directory-passwords-reset-register/register-2-methods.png "Página de registo de reposição de palavra-passe que mostra métodos registados e o botão de conclusão"

