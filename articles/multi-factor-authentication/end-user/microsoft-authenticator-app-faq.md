---
title: "Microsoft Authenticator aplicação ajuda e suporte | Microsoft Docs"
description: "Fornece uma lista de perguntas mais frequentes e respostas relacionadas com a aplicação Microsoft Authentication e o Azure multi-factor Authentication."
services: multi-factor-authentication
documentationcenter: 
author: barlanmsft
manager: femila
ms.assetid: f04d5bce-e99e-4f75-82d1-ef6369be3402
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/17/2017
ms.author: barlan
ms.reviewer: librown
ms.custom: end-user
ms.openlocfilehash: 4d1302416cfa767415d7bb1e8b426079099caf28
ms.sourcegitcommit: bd0d3ae20773fc87b19dd7f9542f3960211495f9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2017
---
# <a name="microsoft-authenticator-app-faq"></a>Aplicação de autenticação do Microsoft FAQ

Este artigo responde a questões recorrentes que recebemos sobre a aplicação Microsoft Authenticator. Se não vir uma resposta à sua pergunta, vá para o [fórum de aplicação do Microsoft Authenticator](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp). Além disso, temos outro FAQ sobre uma funcionalidade específica da aplicação, [iniciar sessão com o seu telefone FAQ](microsoft-authenticator-app-phone-signin-faq.md).

A aplicação Microsoft Authenticator substituído a aplicação Azure Authenticator, não sendo a aplicação recomendada quando utiliza o Azure multi-factor Authentication. A aplicação Authenticator da Microsoft está disponível para [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072), e [iOS](http://go.microsoft.com/fwlink/?Linkid=825073).

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="what-are-the-codes-in-the-app-for-why-does-the-number-keep-counting-down"></a>Quais são os códigos de na aplicação para? Por que motivo é o número keep contando?

Quando abre a aplicação Authenticator da Microsoft, pode ver as contas que adicionou e um número de dígitos de seis ou oito por cada um deles. Poderá ver um temporizador de 30 segundo contando para baixo.

Estes códigos são utilizados quando iniciam sessão na sua conta. Depois de introduzir o nome de utilizador e palavra-passe, poderá ser-lhe pedido para introduzir um código de verificação. Abra a aplicação Microsoft Authenticator e copie o código que está atualmente a ser mostrada. Introduza esse código na página de início de sessão para concluir.

É o motivo que os códigos de alterar a cada 30 segundos para que nunca utilize o mesmo código duas vezes. Não é como uma palavra-passe que está a ser deveria lembrar-se. A ideia é apenas um utilizador com acesso para o seu telefone a conhecer o seu código de verificação.

Os códigos não necessitam de internet ou de dados, pelo que não precisa de preocupar com o serviço de telefone para iniciar sessão. Quando fechar a aplicação, não continuar em execução em segundo plano e não drenar da bateria. Pode fechar a aplicação e ignorá-lo até a próxima vez que iniciar sessão.  

### <a name="i-only-get-notifications-when-i-have-the-app-open-if-the-app-isnt-open-i-dont-get-any-notifications"></a>Apenas receber notificações tenho a aplicação aberta. Se a aplicação não estiver aberta, posso não obter quaisquer notificações.

Se receber notificações, mas não fazer ruído ou Vibre, apesar da alteração do toque na, primeiro verifique as definições de aplicação. Ative a aplicação utilizar som ou Vibre com o respetivas notificações.

Se não obtiver as notificações de todo, verifique os seguintes casos:

- É o seu telefone no modo silenciosos ou não efetue Disturb? Desse modo pode manter as aplicações de envio de notificações.
- Pode receber notificações de outras aplicações? Caso contrário, poderão existir um problema com ligações de rede no seu telemóvel ou o canal de notificações do Android ou Apple. Pode resolver a primeira opção nas suas definições de telefone, mas poderá ser necessário para que comunique com o fornecedor de serviços para obter ajuda com a segunda opção.
- Pode receber notificações para algumas contas na aplicação, mas não a outros utilizadores? Se Sim, remover a conta problemática da sua aplicação e adicione-a novamente para ativar as notificações push.

Se tiver tentou estas sugestões de resolução de problemas, mas ainda está a ter problemas, envie-nos registos de diagnóstico. Aceda às definições de aplicação, em seguida, selecione **ajuda e comentários** e **enviar registos de**. Em seguida, vá para o [fórum de aplicação do Microsoft Authenticator](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp) e informe-no que problema está a ver e que medidas já tentou até ao momento.

### <a name="im-already-using-the-microsoft-authenticator-application-for-verification-codes-how-do-i-switch-to-one-click-push-notifications"></a>A aplicação Microsoft Authenticator já estiver a utilizar para códigos de verificação. Como mudar para enviar notificações push de um clique?
Aprovar um início de sessão através de notificação push só está disponível para contas Microsoft pessoais ou de trabalho contas escolares ou profissionais Microsoft, não para contas de terceiros como o Google ou o Facebook. Se tiver uma empresa ou escola conta Microsoft, a sua organização pode optar por desativar esta opção.

Se utilizar uma conta Microsoft para a sua conta pessoal e pretende mudar a ativação pós-falha para enviar notificações push, terá de adicionar a sua conta novamente. Volte a registar o dispositivo com a sua conta e configurar as notificações push.  

Se utilizar o Microsoft Authenticator para o seu trabalho ou escola conta, em seguida, organização decide se pretende permitir que as notificações de um clique.

### <a name="do-one-click-push-notifications-work-for-non-microsoft-accounts"></a>Notificações push de um clique funcionam para contas de terceiros?
Não, as notificações push funcionam apenas com contas Microsoft e contas do Azure Active Directory. Se a sua empresa ou escola utiliza contas do Azure AD, estes podem desativar esta funcionalidade.  

### <a name="i-restored-my-device-from-a-backup-and-my-account-codes-are-missing-or-not-working-what-happened"></a>Posso restaurado o meu dispositivo a partir de uma cópia de segurança e códigos a minha conta estão em falta ou não está a funcionar. O que aconteceu?
Por motivos de segurança, iremos não restaurar as contas de cópias de segurança da aplicação.  Depois de restaurar a aplicação, eliminar as contas e adicioná-las novamente.

### <a name="i-got-a-new-device-how-do-i-remove-the-microsoft-authenticator-app-from-my-old-device-and-move-to-the-new-one"></a>Posso obteve um novo dispositivo. Como remover a aplicação Microsoft Authenticator do meu dispositivo antigo e mover para a nova?
Adicionar a aplicação Authenticator da Microsoft para um novo dispositivo não automaticamente removê-lo de todos os outros dispositivos. Para gerir os dispositivos que estão configurados para a sua conta, visite o mesmo Web site que utiliza para gerir a verificação de dois passos e optar por remover aplicações antigas.

Para contas Microsoft pessoais, este Web site está a [segurança da conta](https://account.microsoft.com/security) página. Para contas Microsoft escolar ou profissional, este Web site poderá ser um [MyApps](https://myapps.microsoft.com) ou um portal personalizado que configurou sua organização.

### <a name="how-do-i-remove-an-account-from-the-app"></a>Como remover uma conta a partir da aplicação?
* iOS: a partir do ecrã principal, percorra a esquerda num mosaico de conta. Selecione **Eliminar**.
* Windows Phone: A partir do ecrã principal, selecione o botão do menu, em seguida, **edita contas**. Toque no **X** junto ao nome da conta.
* Android: A partir do ecrã principal, selecione o botão do menu, em seguida, **edita contas**. Toque no **X** junto ao nome da conta.

Se tiver um dispositivo que está registado com a sua organização, poderá ter de concluir um passo adicional para remover a sua conta. Nestes dispositivos, a aplicação Microsoft Authenticator é automaticamente registada como um administrador do dispositivo. Se pretender desinstalar completamente a aplicação, terá de pela primeira vez anular o registo da aplicação nas definições da aplicação.

### <a name="why-does-the-app-request-so-many-permissions"></a>Por que motivo é que a aplicação solicitar permissões de muitas?
Eis uma lista completa das permissões, pode pedir e como são utilizados na aplicação. As permissões específicas que vir dependem do tipo de telefone que tem.

* **Câmara**: utilizamos a câmara para analisar códigos QR quando adicionar uma conta de terceiros, escolar ou profissional.
* **Contactos e phone**: quando iniciar sessão com a sua conta Microsoft pessoal, vamos tentar simplificar o processo ao localizar as contas existentes que utiliza no seu telemóvel.
* **SMS**: quando inicia sessão com a sua conta Microsoft pessoal pela primeira vez, temos de certificar-se de que o número de telefone corresponde dos temos no registo. Enviar-lhe uma mensagem de texto para o telefone onde transferiu a aplicação. A mensagem contém um código de verificação 8 de 6 dígitos. Em vez de pedir-lhe para localizar este código e introduza-o na aplicação, que encontrar na mensagem de texto para si.
* **Desenhar através de outras aplicações**: quando recebem uma notificação para verificar a sua identidade, iremos apresentar a essa notificação através de qualquer outra aplicação pode estar em execução.
* **Receber dados através da internet**: Esta permissão é necessária para enviar notificações.
* **Impedir que o telefone suspensão**: se registar o seu dispositivo com a sua organização, podem alterar esta política no seu telemóvel.
* **Controlar vibration**: pode escolher se pretende que um vibration sempre que recebe uma notificação para verificar a sua identidade.
* **Utilize hardware de identificação digital**: algumas contas profissional e escolar exigem um PIN adicional, sempre que confirme a sua identidade. PARA facilitar o processo, iremos permitem-lhe utilizar a sua impressão digital em vez de introduzir o PIN.
* **Ver as ligações de rede**: ao adicionar uma conta Microsoft, a aplicação necessita de ligação de rede/à internet.
* **Ler o conteúdo do seu armazenamento**: Esta permissão só é utilizada quando comunicar um problema técnico através de definições da aplicação. Algumas informações do seu armazenamento são recolhidas para diagnosticar o problema.
* **Acesso de rede total**: Esta permissão é necessária para enviar notificações para verificar a sua identidade.
* **São executados no arranque**: se reiniciar o seu telemóvel, esta permissão garante que continuam a receber notificações para verificar a sua identidade.

### <a name="why-does-the-microsoft-authenticator-app-allow-you-to-approve-a-request-without-unlocking-the-device"></a>Por que razão a aplicação de autenticação da Microsoft permite-lhe aprovar um pedido sem desbloquear o dispositivo?

Não tem de desbloquear o dispositivo para aprovar pedidos de verificação porque só terá de provar que tem o seu telemóvel consigo. Verificação de dois passos requer comprovar duas coisas – uma coisa que conhece e uma coisa que precisa. A coisa que sabe que é a palavra-passe. Coisa que precisa é o seu telemóvel (configurar com a aplicação Microsoft Authenticator e registado como uma prova de MFA). Por conseguinte, tendo o telefone e aprovar o pedido satisfaz os critérios de segundo fator de autenticação.

### <a name="what-does-the-lock-icon-in-the-account-list-mean"></a>O que significa o ícone de cadeado na lista de conta?

O ícone de cadeado indica que o dispositivo é registado no Azure AD e registado para a conta. Registo de dispositivos para iOS ocorre durante a inscrição no Microsoft Intune.

## <a name="next-steps"></a>Passos seguintes

### <a name="contact-us"></a>Contacte-nos
Se a sua pergunta não foi respondida aqui, Queremos ouvi do utilizador. Vá para o [fórum de aplicação do Microsoft Authenticator](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp) após a sua pergunta e obter ajuda da Comunidade ou deixe um comentário nesta página.


### <a name="related-topics"></a>Tópicos relacionados
* [Sobre a verificação de dois passos](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification) para contas Microsoft
* [Problemas com verificação de dois passos](multi-factor-authentication-end-user-troubleshoot.md) para a sua conta escolar ou profissional?
* [Utilizar o Microsoft Authenticator para iniciar sessão a partir do seu telemóvel](microsoft-authenticator-app-phone-signin-faq.md)
