---
title: "Microsoft Authenticator phone início de sessão - contas do Azure e da Microsoft | Microsoft Docs"
description: "Utilize o seu telefone para iniciar sessão sua conta Microsoft em vez de escrever a palavra-passe. Este artigo responde a perguntas mais frequentes sobre esta funcionalidade."
services: multi-factor-authentication
documentationcenter: 
author: barlanmsft
manager: femila
ms.assetid: 
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/12/2017
ms.author: barlan
ms.reviewer: librown
ms.custom: end-user
ms.openlocfilehash: b62900b780884c241d96f0bed8e93a1a0927cfe3
ms.sourcegitcommit: bd0d3ae20773fc87b19dd7f9542f3960211495f9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2017
---
# <a name="sign-in-with-your-phone-not-your-password"></a>Inicie sessão com o seu telemóvel, não a palavra-passe

A aplicação Microsoft Authenticator ajuda a manter as suas contas segura pela verificação em dois passos a efetuar depois de introduzir a palavra-passe. Mas sabia que que pode substituir a palavra-passe para a sua conta Microsoft pessoal inteiramente?

Esta funcionalidade está disponível em dispositivos iOS e Android e funciona com contas Microsoft pessoais.

## <a name="how-it-works"></a>Como funciona

Muitos dos utilizam a aplicação Microsoft Authenticator para verificação de dois passos quando iniciam sessão na sua conta Microsoft. Escreva a palavra-passe e vá para a aplicação para aprovar uma notificação ou obtenha um código de verificação. Com o telefone início de sessão, ignorar a palavra-passe e fazer a verificação de identidade no seu telemóvel. Uma vez phone início de sessão é um tipo de verificação em dois passos, ainda tem de fornecer uma coisa que conhece e uma coisa que precisa para verificar a sua identidade. O telefone ainda está coisa que precisa e de PIN ou biométrico chave do seu telemóvel é coisa que sabe.

## <a name="how-to-get-started"></a>Como começar

Para iniciar sessão sua conta Microsoft pessoal com o seu telemóvel, siga estes passos:

1. Ative o telefone início de sessão para a sua conta.

  - Se não tiver a aplicação Microsoft Authenticator ainda, instalar e adicionar a sua conta Microsoft pessoal, de acordo com os passos no [página Microsoft Authenticator](microsoft-authenticator-app-how-to.md). São ativadas automaticamente contas recentemente adicionadas, pelo que está pronto para continuar.

  - Se já utilizar Microsoft Authenticator para verificação de dois passos, selecione a sua conta na aplicação home page e selecione **ativar phone início de sessão** no menu pendente.

  >[!NOTE]
  >Para proteger a sua conta, é necessário um PIN ou biométrico bloqueio no seu dispositivo. Se mantiver o seu telefone desbloqueado, a aplicação aparece um pedido de pedir-lhe configurar um bloqueio antes de ativar o telefone início de sessão.

3. A maioria das páginas em que normalmente iria introduza a palavra-passe de conta do Microsoft tem uma ligação que indica que **utilizar uma aplicação em vez disso,**. Selecione esta ligação para iniciar sessão com o seu telefone.

4. Microsoft envia uma notificação para o seu telefone. Aprove a notificação para iniciar sessão na sua conta.   

## <a name="faq"></a>FAQ

### <a name="how-is-signing-in-with-my-phone-more-secure-than-typing-a-password"></a>Como é iniciar sessão com o meu telefone mais segura do que escrever uma palavra-passe?  

Hoje em dia maioria das pessoas inicie sessão em web sites ou aplicações ao utilizar um nome de utilizador e palavra-passe.  Infelizmente, as palavras-passe são, muitas vezes, perdidas, roubadas ou adivinhadas por hackers. Quando configurar a aplicação Authenticator da Microsoft para iniciar sessão, iremos gerar uma chave no seu telemóvel, que pode desbloquear a sua conta. Estamos a proteger esta chave com o PIN ou biométrico que já utilizam no seu telemóvel.  Quando iniciar sessão com o seu telefone, esta chave é utilizada para provar a sua identidade de forma segura com dois fatores – o telemóvel em si e a capacidade para o desbloquear. 

A chave utilizada é semelhante para as chaves utilizadas no Windows Hello e as especificações de FIDO Alliance UAF. A biografia do dados só são utilizado para proteger a chave localmente e é nunca enviada para ou armazenada na nuvem. 
 
### <a name="where-can-i-use-my-phone-to-replace-my-password-and-where-would-i-still-need-the-password"></a>Onde posso utilizar o meu telefone para substituir a minha palavra-passe e, em que seria ainda necessário a palavra-passe?  

Atualmente, a funcionalidade de início de sessão phone só funciona com aplicações web e serviços que são com tecnologia Microsoft contas pessoais, iOS ou Android aplicações que utilizam uma conta Microsoft pessoal e aplicações no Windows 10 que utilizam uma conta Microsoft pessoal. Quando iniciar sessão como um destes web sites ou aplicações, na página onde, geralmente, introduza a palavra-passe há uma ligação que indica que **utilizar uma aplicação em vez disso,**. 

Telefone início de sessão não pode ser utilizado para desbloquear um PC com Windows, XBOX ou as versões de ambiente de trabalho de aplicações da Microsoft, tais como aplicações do Office neste momento.
 
### <a name="does-this-replace-two-step-verification-should-i-turn-it-off"></a>Isto substitui verificação em dois passos? Deve posso desativá-la?   

Por vezes. Estamos a trabalhar expandir o âmbito do telefone início de sessão, mas por agora ainda existem locais do ecossistema da Microsoft que não o suportam. Nesses locais, estamos a utilizar ainda verificação em dois passos para o início de sessão seguro. Por esse motivo, não, não deve desativar a verificação de dois passos para a sua conta.
 
### <a name="okay-if-i-keep-two-step-verification-turned-on-for-my-account-do-i-have-to-approve-two-notifications"></a>Okay, se manter a verificação de dois passos ativada para a minha conta, é necessário que aprovar duas notificações?

Não, não. O início de sessão na sua conta Microsoft com o seu telefone conta como a verificação de dois passos. Em vez de escrever a palavra-passe, em seguida, aprovar uma notificação de provar a sua identidade a saber como desbloquear o telefone e, em seguida, aprovar uma notificação. Não será a enviar uma notificação segundo aprovar.

### <a name="what-if-i-lose-my-phone-or-dont-have-it-with-me-how-can-i-access-my-account"></a>E se perder o meu telefone ou não o tiver com-me, como posso aceder à minha conta?  

Pode sempre clicar **em alternativa, utilize uma palavra-passe** na página de início de sessão para voltar ao utilizar a palavra-passe. Tenha em atenção que se utilizar a verificação de dois passos, ainda precisa de um segundo método para verificar o início de sessão. É por esse motivo vivamente Aconselhamo-lo para se certificar de que tem informações de segurança adicional, atualizado na sua conta. Pode gerir as suas informações de segurança em https://account.live.com/proofs/manage.
 
### <a name="how-do-i-stop-using-this-feature-and-go-back-to-entering-my-password"></a>Como deixar de utilizar esta funcionalidade e volte a introduzir a minha palavra-passe?

Clique em **em alternativa, utilize uma palavra-passe** quando iniciar sessão. Estamos Lembre-se à sua escolha mais recente e oferecem que, por predefinição, da próxima vez que iniciar sessão. Se alguma vez pretender voltar a iniciar sessão com o seu telemóvel, clique em **utilizar uma aplicação em vez disso,**. 
 
### <a name="can-i-use-the-app-to-sign-in-to-all-my-accounts-with-microsoft"></a>Pode utilizar a aplicação para iniciar sessão em todas as minhas contas com a Microsoft?   
Esta funcionalidade só está disponível para contas Microsoft pessoais neste momento. 
 
### <a name="can-i-sign-into-my-pc-with-my-phone"></a>Pode consigo iniciar sessão no meu computador com o meu telefone?  
Para o seu PC, recomendamos que iniciar sessão com Windows Hello no Windows 10 com a letra, identificação digital ou um PIN.   
 
### <a name="can-i-sign-in-with-my-windows-phone"></a>Pode iniciar sessão com o meu telefone do Windows?  
Neste momento, estamos não estiver a desenvolver esta funcionalidade para a Microsoft Authenticator no Windows Phone. 

## <a name="next-steps"></a>Passos seguintes
Se ainda não transferiu a aplicação Authenticator da Microsoft, consulte o-lo. A aplicação está disponível para [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), e o telefone início de sessão está disponível na aplicação Microsoft Authenticator para [Android](http://go.microsoft.com/fwlink/?Linkid=825072) e [iOS](http://go.microsoft.com/fwlink/?Linkid=825073).

Se tiver dúvidas sobre a aplicação em geral, observe o [perguntas frequentes do autenticador Microsoft](microsoft-authenticator-app-faq.md)
