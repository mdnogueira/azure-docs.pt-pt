---
title: 'Azure AD: Registo SSPR | Microsoft Docs'
description: "Registar dados de autenticação de palavra-passe self-service do Azure AD repor"
services: active-directory
keywords: 
documentationcenter: 
author: barlanmsft
manager: femila
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2017
ms.author: barlan
ms.custom: end-user
ms.openlocfilehash: b884f8bc3a20052fa0cb40772deef591b69d7b10
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="register-for-self-service-password-reset"></a>Registar-se na reposição personalizada de palavra-passe

> [!IMPORTANT]
> **Está aqui porque está a ter problemas em iniciar sessão?** Se assim for, [Eis como pode alterar e repor a sua própria palavra-passe](active-directory-passwords-update-your-own-password.md).

Como um utilizador final, pode repor a palavra-passe ou desbloquear a conta sem ter de enunciar para uma pessoa que utiliza a reposição de palavra-passe self-service (SSPR). Antes de poder utilizar esta funcionalidade, tem de registar os métodos de autenticação ou confirmar os métodos de autenticação predefinidos que o administrador tem preenchido.

## <a name="register-or-confirm-authentication-data-with-sspr"></a>Registar ou confirmar dados de autenticação com SSPR

1. Abra o browser no seu dispositivo e aceda à [página de registo de reposição de palavra-passe](http://aka.ms/ssprsetup)
2. Introduza o seu nome de utilizador e palavra-passe fornecidos pelo administrador
3. Dependendo de como a sua equipa de TI tiver configurado coisas, um ou mais das seguintes opções estão disponíveis para configurar e verificar. O administrador pode preencher algumas desta situação para si se tiverem permissão para utilizar as informações.
    * Telefone do escritório apenas é capaz de ser definida pelo administrador
    * Telefone de autenticação deve ser definido para outro número de telefone seria tem acesso a como um telemóvel que pode receber um texto ou chamada.
    * E-Mail de autenticação deve ser definido para um endereço de correio eletrónico alternativo que pode aceder sem a palavra-passe que ter de repor.
    * Perguntas de segurança fornece uma lista de perguntas a que o administrador tiver aprovado para que possa responder. Não pode utilizar a mesma pergunta ou responder a mais do que uma vez.
4. Forneça e verifique as informações pedidas pelo seu administrador. Se estiver disponível mais do que uma opção, sugerimos que registar vários métodos para fornecer flexibilidade quando outro método não está disponível (exemplo: estiverem em deslocação e não é possível aceder ao seu telefone do escritório)

    ![Registe métodos de autenticação e clique em Concluir][Register]

5. Quando concluir o passo 4, escolha **concluir** e agora poderá utilizar a reposição personalizada de palavra-passe quando precisar no futuro.

Se introduzir dados no Telefone de Autenticação ou no E-mail de Autenticação, não é visível no diretório global. As únicas pessoas que podem ver estes dados são os administradores. Apenas o utilizador pode ver as respostas às suas Perguntas de Segurança.

Os administradores podem pedir para confirmar os métodos de autenticação após um período de tempo, para assegurar que continua a ter os métodos adequados registados.

## <a name="common-problems-and-their-solutions"></a>Problemas comuns e as respetivas soluções

 Eis alguns casos de erro comuns e as respetivas soluções:

| Caso de erro| O erro vir?| Solução |
| --- | --- | --- |
| Posso obter uma página ". Contacte o administrador" depois de introduzir o meu ID de utilizador | Contacte o administrador <br> <br> Detetámos que a palavra-passe da conta de utilizador não é gerida pela Microsoft. Como resultado, não conseguimos automaticamente repor a palavra-passe. <br> <br> Terá de contactar a sua equipa de TI para obter mais assistência. | Está a ver esta mensagem porque a sua equipa de TI gere a palavra-passe no seu ambiente no local e não permite-lhe repor a palavra-passe do não é possível aceder a ligação de conta. <br> <br> Para repor a palavra-passe, contacte a equipa de TI diretamente para obter ajuda e informar de que pretende repor a palavra-passe, pelo que podem ativar esta funcionalidade para si.|
| Posso obter um erro "a conta não está ativada para a reposição de palavra-passe" depois de introduzir o meu ID de utilizador | A sua conta não permite reposição da palavra-passe <br> <br> Lamentamos, mas a sua equipa de TI não tenha configurado a sua conta para utilização com este serviço. <br> <br> Se quiser, podemos contactá um administrador na sua organização para repor a palavra-passe por si. | Está a ver esta mensagem porque a sua equipa de TI não activou a reposição da sua organização de palavra-passe de não é possível aceder a ligação de conta ou ainda não está licenciado para utilizar a funcionalidade. <br> <br> Para repor a palavra-passe, clique o contacto uma ligação de administrador para enviar uma mensagem de e-mail da sua empresa e informar de que pretende repor a palavra-passe, pelo que podem ativar esta funcionalidade para si da equipa de TI. |
| Posso obter um erro de "pode não verificar a sua conta" depois de introduzir o meu ID de utilizador | Não foi possível verificar a sua conta <br> <br> Se quiser, podemos contactá um administrador na sua organização para repor a palavra-passe por si. | Está a ver esta mensagem porque estão ativados para a reposição de palavra-passe, mas não tiver registado para utilizar o serviço. Para registar para a reposição de palavra-passe, aceda ao http://aka.ms/ssprsetup após ter recuperou o acesso à sua conta. <br> <br> Para repor a palavra-passe, clique o contacto uma ligação de administrador para enviar uma mensagem de e-mail da sua empresa equipa de TI. |

## <a name="next-steps"></a>Passos Seguintes

* [Como alterar a palavra-passe através da reposição personalizada de palavra-passe](active-directory-passwords-update-your-own-password.md)
* [Página de registo de reposição de palavra-passe](http://aka.ms/ssprsetup)
* [Portal de reposição de palavra-passe](https://passwordreset.microsoftonline.com/)
* [Não consegue iniciar sessão com a sua conta Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)

[Register]: ./media/active-directory-passwords-reset-register/register-2-methods.png "Página de registo de reposição de palavra-passe que mostra métodos registados e o botão de conclusão"
