---
title: "Resolver problemas de verificação em dois passos | Microsoft Docs"
description: "Este documento irá fornecer as aos utilizadores informações sobre o que fazer se executam para um problema com o Azure multi-factor Authentication."
services: multi-factor-authentication
keywords: "cliente de autenticação multifator, problema de autenticação, ID de correlação"
documentationcenter: 
author: barlanmsft
manager: femila
ms.assetid: 8f3aef42-7f66-4656-a7cd-d25a971cb9eb
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2017
ms.author: barlan
ms.reviewer: richagi
ms.custom: end-user
ms.openlocfilehash: 0ffa3070cef8631c4b38b2aa00ac3dca0d88590b
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="get-help-with-two-step-verification"></a>Obter ajuda com verificação de dois passos
Este artigo responde as perguntas mais comuns que pessoas colocar sobre verificação de dois passos.

## <a name="why-do-i-have-to-perform-two-step-verification-can-i-turn-it-off"></a>Por que motivo é necessário efetuar verificação de dois passos? Pode posso desativá-la?

Verificação de dois passos é uma funcionalidade de segurança a sua organização optar por utilizar para proteger as suas contas. É mais segura do que apenas uma palavra-passe, porque depende de duas formas de autenticação: algo conhece e algo tem consigo. É algo que saiba a palavra-passe. Algo com é um telefone ou dispositivo que normalmente tem consigo. Quando a sua conta está protegida com verificação de dois passos, que significa que um hacker malicioso não é possível iniciar sessão como se a palavra-passe preso de algum modo porque não têm acesso para o seu telemóvel, demasiado.

A Microsoft disponibiliza a verificação de dois passos, mas a sua organização opta por utilizar a funcionalidade. Não é possível ativamente se o suporte da sua empresa requê-lo de que, tal como o não é possível ativamente por não utilizar uma palavra-passe para proteger a sua conta.

Se tiver ativada para a sua conta Microsoft pessoal de verificação de dois passos e pretender alterar as definições, ler [sobre a verificação de dois passos](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification) em vez disso.

## <a name="i-dont-have-my-phone-with-me-today"></a>Não tenho meu telefone com-me hoje

Alguns dias a manter o telemóvel em casa, mas ainda tem de iniciar sessão no trabalho. A primeira coisa que deve tentar é iniciar sessão com um método de verificação diferente. Quando se registou para verificação de dois passos, configurar mais do que um número de telefone? Ao tentar iniciar sessão com um método diferente, siga estes passos:

1. Inicie sessão como faria normalmente.
2. Quando abre a página de verificação de dois passos, escolha **utilizar outra opção de verificação**.

   ![Verificação diferente](./media/multi-factor-authentication-end-user-troubleshoot/diff_option.png)

3. Selecione a opção de verificação que pretende utilizar.
4. Continue com a verificação de dois passos.

Se não vir o **utilizar outra opção de verificação** ligar, em seguida, o que significa que não foi configurado a métodos alternativos quando se registou primeiro para a verificação de dois passos. Contacte o suporte da empresa para obter ajuda o início de sessão na sua conta. Assim que tem sessão iniciada, certifique-se de que [gerir as suas definições](multi-factor-authentication-end-user-manage-settings.md) para adicionar métodos de verificação adicional para a próxima vez.

Se vir o **utilizar outra opção de verificação** ligação, mas não tem acesso às suas métodos alternativos ambos, contacte o suporte da sua empresa para obter ajuda o início de sessão na sua conta.

## <a name="i-lost-my-phone-or-got-a-new-number"></a>Posso perder o meu telefone ou recebeu um novo número de erro
Existem duas formas de aceder novamente à sua conta. É o primeiro para iniciar sessão com o número de telefone de autenticação alternativo, se configurou um. O segundo é colocar o suporte da empresa para limpar as definições.

Se o seu telefone foi perdido ou roubado, recomendamos também que indique da sua empresa que suporte, de modo a poderem repor as palavras-passe de aplicação e limpe quaisquer memorizadas dispositivos.

### <a name="use-an-alternate-phone-number"></a>Utilizar um número de telefone alternativo
Se configurou várias opções de verificação, incluindo um número de telefone secundário ou uma aplicação de autenticação num dispositivo diferente, pode utilizar um para iniciar sessão.

Para iniciar sessão utilizando o número de telefone alternativo, siga estes passos:

1. Inicie sessão como faria normalmente.
2. Quando lhe for pedido para verificar a sua conta, escolha **utilizar outra opção de verificação**.

   ![Verificação diferente](./media/multi-factor-authentication-end-user-troubleshoot/diff_option.png)

3. Selecione o número de telefone ou dispositivo que tenha acesso.
4. Depois de estiver novamente na sua conta, [gerir as suas definições](multi-factor-authentication-end-user-manage-settings.md) para alterar o número de telefone de autenticação.

### <a name="clear-your-settings"></a>Limpar as definições
Se não tiver configurado um número de telefone de autenticação secundária, terá de contactar o suporte da empresa para obter ajuda. Limpar este que as definições da próxima vez que iniciar sessão, será solicitado para [registar para a verificação de dois passos](multi-factor-authentication-end-user-first-time.md) novamente.

## <a name="i-am-not-receiving-a-text-or-call-on-my-phone"></a>Posso não estou a receber um texto ou chamada no meu telefone
Existem vários motivos por que razão pode tentar iniciar sessão, mas não receber o texto ou chamada telefónica. Se tiver recebido com êxito textos ou chamadas telefónicas para o seu telefone no passado, em seguida, isto é provavelmente um problema com o fornecedor de telefone, não a sua conta. Certifique-se de que tem o sinal de célula bom e, se estiver a tentar receber uma mensagem de texto certifique-se de que é capaz de receber mensagens de texto. Peça um friend para chamar o texto ou utilizador, como um teste.

Se tiver aguardaram alguns minutos até um texto ou chamada, é a forma mais rápida para a sua conta experimentar uma opção diferente.

1. Selecione **utilizar outra opção de verificação** na página que está a aguardar a verificação.

    ![Verificação diferente](./media/multi-factor-authentication-end-user-troubleshoot/diff_option.png)
2. Selecione o método de número ou entrega de telefone que pretende utilizar.

    Se recebeu vários códigos de verificação, utilize o mais recente.

Se não tiver outro método configurado, contacte o suporte da empresa e peça-lhes para limpar as definições. Da próxima vez que iniciar sessão, será solicitado para [configurar a autenticação multifator](multi-factor-authentication-end-user-first-time.md) novamente.

Se tiver, muitas vezes, os atrasos devido a sinal de célula incorreto, recomendamos que utilize o [aplicação Microsoft Authenticator](microsoft-authenticator-app-how-to.md) no smartphone. A aplicação pode gerar códigos de segurança aleatório que utiliza para iniciar sessão e estes códigos não requerem qualquer ligação de sinal ou de internet de célula.

## <a name="app-passwords-are-not-working"></a>As palavras-passe de aplicação não estão a funcionar
Em primeiro lugar, certifique-se de que introduziu a palavra-passe de aplicação corretamente. A palavra-passe de aplicação gerados substitui o normal palavra-passe, mas apenas aplicações de ambiente de trabalho mais antigas que não suportam a verificação em dois passos. Se esta ainda não está a funcionar, experimente iniciar sessão e [criar uma nova palavra-passe de aplicação](multi-factor-authentication-end-user-app-passwords.md).  Se ainda não funciona, contacte o suporte da empresa e os [eliminar as palavras-passe de aplicação existentes](../multi-factor-authentication-manage-users-and-devices.md) e, em seguida, pode criar um novo.

## <a name="i-didnt-find-an-answer-to-my-problem"></a>Não encontrar o meu problema uma resposta.
Se tiver já tentou estes passos de resolução de problemas, mas é continuam a executar para problemas, contacte o suporte da empresa. Poderão ajudá-lo.

## <a name="related-topics"></a>Tópicos relacionados
* [Gerir as definições de verificação em dois passos](multi-factor-authentication-end-user-manage-settings.md)  
* [FAQ acerca da aplicação Microsoft Authenticator](microsoft-authenticator-app-faq.md)
