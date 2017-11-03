---
title: "Configurar a verificação de dois passos para a minha conta escolar ou profissional | Microsoft Docs"
description: "Quando a sua empresa configura Azure multi-factor Authentication, será solicitado para se inscrever para verificação de dois passos. Saiba como configurá-la. "
services: multi-factor-authentication
keywords: como utilizar o azure, active directory na nuvem, tutorial do Active Directory
documentationcenter: 
author: barlanmsft
manager: angrobe
editor: pblachar
ms.assetid: 46f83a6a-dbdd-4375-8dc4-e7ea77c16357
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2017
ms.author: barlan
ms.custom: end-user
ms.openlocfilehash: a345cacfe53f37f851eebb76b5b8997cb9c0b54b
ms.sourcegitcommit: bd0d3ae20773fc87b19dd7f9542f3960211495f9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2017
---
# <a name="set-up-my-account-for-two-step-verification"></a>Configurar a minha conta para a verificação de dois passos
Verificação de dois passos é um passo de segurança adicional que ajuda a proteger a sua conta, tornando mais difícil para outras pessoas para entrar na. Se estiver a ler este artigo, provavelmente, recebeu uma mensagem de e-mail do administrador de conta escolar ou profissional sobre multi-factor Authentication. Ou, talvez tentou iniciar sessão e recebeu uma mensagem a pedir-lhe configurar a verificação de segurança adicional. Se for esse o caso, **não pode iniciar sessão depois de concluir o processo de inscrição automática**.

Este artigo ajuda-o a configurar a sua **conta escolar ou profissional**. Se pretender ativar a verificação de dois passos para a suas próprias, conta Microsoft pessoal, consulte [sobre a verificação de dois passos](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification).

## <a name="set-up-your-account"></a>Configurar a sua conta

Quando o suporte da sua empresa requer que começar a utilizar a verificação de dois passos, a indicar um ecrã **o seu administrador necessita que configure esta conta para verificação de segurança adicional** é apresentado:

![Configurar](./media/multi-factor-authentication-end-user-first-time/first.png)

Para começar a utilizar, selecione **configurá-lo agora.**

Se não vir um ecrã de como esta quando iniciar sessão, siga as indicações [gerir as definições de verificação em dois passos](multi-factor-authentication-end-user-manage-settings.md#where-to-find-the-settings-page) para localizar a página de definições onde pode gerir as opções de verificação. 

## <a name="decide-how-you-want-to-verify-your-sign-ins"></a>Decida como pretende verificar os inícios de sessão

À primeira pergunta no processo de inscrição é a forma como pretende que nos permitam contactá-lo. Observe as opções na tabela e utilize as ligações para ir para os passos de configuração para cada método.

| Método de contacto | Descrição |
| --- | --- |
| [Aplicação móvel](#use-a-mobile-app-as-the-contact-method) |- **Receba notificações de verificação.** Esta opção envia uma notificação para a aplicação de autenticação no seu smartphone ou tablet. Ver a notificação e, se esta for legítima, selecionar **autenticar** na aplicação. A empresa ou escola pode exigir que introduza um PIN antes de se autenticar.<br>- **Utilize código de verificação.** Neste modo, a aplicação de autenticação gera um código de verificação que atualiza a cada 30 segundos. Introduza o código de verificação mais recente na interface de início de sessão.<br>A aplicação Authenticator da Microsoft está disponível para [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072), e [iOS](http://go.microsoft.com/fwlink/?Linkid=825073). |
| [Chamada de telemóvel ou de texto](#use-your-mobile-phone-as-the-contact-method) |- **Chamada telefónica** coloca uma chamada de voz automatizada para o número de telefone que fornecer. Atender a chamada e prima # no teclado do telefone para se autenticar.<br>- **Mensagem de texto** termina uma mensagem de texto que contém um código de verificação. Os seguintes linha no texto, responda à mensagem de texto ou introduza o código de verificação fornecido para a interface de início de sessão. |
| [Chamada de telefone do escritório](#use-your-office-phone-as-the-contact-method) |Coloca uma chamada de voz automatizada para o número de telefone que fornecer. Atender a chamada e prime # no teclado do telefone para se autenticar. |

## <a name="use-a-mobile-app-as-the-contact-method"></a>Utilizar uma aplicação móvel como método de contacto
Ao utilizar este método requer que instale uma aplicação de autenticação no seu telemóvel ou tablet. Os passos neste artigo baseiam-se na aplicação Microsoft Authenticator, que está disponível para [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072), e [iOS](http://go.microsoft.com/fwlink/?Linkid=825073).

1. Selecione **aplicação móvel** na lista pendente.
2. Selecione **receber notificações de verificação** ou **utilizar código de verificação**, em seguida, selecione **configurar**.

   ![Ecrã de verificação de segurança adicionais](./media/multi-factor-authentication-end-user-first-time/mobileapp.png)

3. No seu telemóvel ou tablet, abra a aplicação e selecione  **+**  para adicionar uma conta. (Nos dispositivos Android, selecione as reticências, em seguida, **adicionar conta**.)
4. Especifique que pretende adicionar uma conta escolar ou profissional. O scanner do código QR no seu telemóvel abre. Se a câmara não está a funcionar corretamente, pode optar por introduzir manualmente as informações da empresa. Para obter mais informações, consulte [adicionar manualmente uma conta](#add-an-account-manually).  
5. A imagem do código QR que antes eram com o ecrã para configurar a aplicação móvel de análise.  Selecione **feito** para fechar o ecrã de código QR.  

   ![Ecrã de código QR](./media/multi-factor-authentication-end-user-first-time/scan2.png)

6. Quando a ativação é concluída no telemóvel, selecione **contactar-me**.  Este passo envia uma notificação ou um código de verificação para o seu telefone. Selecione **verificar**.  
7. Se a sua empresa precisar de um PIN para aprovação de verificação do início de sessão, introduza-o.

   ![Caixa de introduzir um PIN](./media/multi-factor-authentication-end-user-first-time/scan3.png)

8. Depois de concluída a introdução do PIN, selecione **fechar**. Neste momento, a verificação deve ser efetuada com êxito.
9. Recomendamos que introduza o seu número de telemóvel no caso de perder o acesso à sua aplicação móvel. Especifique o seu país na lista pendente e introduza o seu número de telemóvel na caixa junto ao nome do país. Selecione **seguinte**.
10. Neste momento, precisará de configurar palavras-passe de aplicação para aplicações não baseadas no browser, tais como o Outlook 2010 ou anterior ou para a aplicação de e-mail nativas em dispositivos da Apple. Esta ação acontece porque algumas aplicações não suportam a verificação de dois passos. Se não utilizar estas aplicações, clique em **feito** e ignore o resto destes passos.
11. Se estiver a utilizar estas aplicações, a palavra-passe de aplicação de cópia fornecido e cole-o sua aplicação em vez da palavra-passe normal. Pode utilizar a mesma palavra-passe de aplicação para várias aplicações. Para obter mais informações, [ajudar com palavras-passe de aplicação].
12. Clique em **Concluído**.

### <a name="add-an-account-manually"></a>Adicionar manualmente uma conta
Se pretender adicionar uma conta para a aplicação móvel manualmente, em vez de utilizar o leitor de QR, siga estes passos.

1. Selecione o **introduzir manualmente a conta** botão.  
2. Introduza o código e o URL que são fornecidos na mesma página que apresenta o código de barras. Estas informações entra **código** e **URL** caixas da aplicação móvel.

    ![Configurar](./media/multi-factor-authentication-end-user-first-time/barcode2.png)
3. Quando tiver concluído a ativação, selecione **contactar-me**. Este passo envia uma notificação ou um código de verificação para o seu telefone. Selecione **verificar**.

## <a name="use-your-mobile-phone-as-the-contact-method"></a>Utilizar o telemóvel como método de contacto
1. Selecione **telefone de autenticação** na lista pendente.  

    ![Configurar](./media/multi-factor-authentication-end-user-first-time/phone.png)  
2. Escolha o seu país na lista pendente e introduza o seu número de telemóvel.
3. Selecione o método que prefere utilizar com o seu telemóvel - texto ou chamada.
4. Selecione **contactar-me** para verificar o número de telefone. Consoante o modo que selecionou, iremos enviar-lhe um texto ou chamadas. Siga as instruções fornecidas no ecrã, em seguida, selecione **verifique**.
5. Neste momento, precisará de configurar palavras-passe de aplicação para aplicações não baseadas no browser, tais como o Outlook 2010 ou anterior ou para a aplicação de e-mail nativas em dispositivos da Apple. Esta ação acontece porque algumas aplicações não suportam a verificação de dois passos. Se não utilizar estas aplicações, clique em **feito** e ignore o resto dos passos.
6. Se estiver a utilizar estas aplicações, a palavra-passe de aplicação de cópia fornecido e cole-o sua aplicação em vez da palavra-passe normal. Pode utilizar a mesma palavra-passe de aplicação para várias aplicações. Para obter mais informações, [ajudar com palavras-passe de aplicação].
7. Clique em **Concluído**.

## <a name="use-your-office-phone-as-the-contact-method"></a>Utilizar o telefone do escritório como método de contacto
1. Selecione **telefone do escritório** da lista pendente  

    ![Configurar](./media/multi-factor-authentication-end-user-first-time/office.png)  
2. A caixa de número de telefone é preenchida automaticamente com as informações de contacto da empresa. Se o número está incorreto ou em falta, peça ao seu administrador para efetuar alterações.
3. Selecione **contactar-me** para verificar o seu telefone número e iremos ligará para o seu número. Siga as instruções fornecidas no ecrã, em seguida, selecione **verifique**.
4. Neste momento, precisará de configurar palavras-passe de aplicação para aplicações não baseadas no browser, tais como o Outlook 2010 ou anterior ou para a aplicação de e-mail nativas em dispositivos da Apple. Esta ação acontece porque algumas aplicações não suportam a verificação de dois passos. Se não utilizar estas aplicações, clique em **feito** e ignore o resto dos passos.
5. Se estiver a utilizar estas aplicações, a palavra-passe de aplicação de cópia fornecido e cole-o sua aplicação em vez da palavra-passe normal. Pode utilizar a mesma palavra-passe de aplicação para várias aplicações. Para obter mais informações, consulte [quais são as palavras-passe de aplicação](multi-factor-authentication-end-user-app-passwords.md).
6. Clique em **Concluído**.

## <a name="next-steps"></a>Passos seguintes
* Alterar as opções de preferenciais e [gerir as definições de verificação em dois passos](multi-factor-authentication-end-user-manage-settings.md)
* Configurar [palavras-passe de aplicação](multi-factor-authentication-end-user-app-passwords.md) nativo para aplicações de dispositivo que não suportam a verificação de dois passos.
* Veja o [aplicação Microsoft Authenticator](microsoft-authenticator-app-how-to.md) para fast, autenticação segura, mesmo quando não tiver o serviço de célula.

