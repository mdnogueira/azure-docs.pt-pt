---
title: "Azure MFA inicie sessão com a verificação de dois passos | Microsoft Docs"
description: "Esta página irá fornecer orientações sobre onde ir para ver os vários início de sessão métodos disponíveis com a MFA do Azure."
keywords: "autenticação de utilizador, o início de sessão experiência, início de sessão com o telemóvel, início de sessão com o telefone do escritório"
services: multi-factor-authentication
documentationcenter: 
author: barlanmsft
manager: femila
ms.assetid: b310b762-471b-4b26-887a-a321c9e81d46
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2017
ms.author: barlan
ms.reviewer: librown
ms.custom: end-user
ms.openlocfilehash: f4e67815cfe03c775dae83d214aea4f2ac37fcdc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="the-sign-in-experience-with-azure-multi-factor-authentication"></a>A experiência de início de sessão com o multi-factor Authentication do Azure
> [!NOTE]
> O objetivo deste artigo é a guiá-lo através de uma experiência de início de sessão normal. Para obter ajuda com início de sessão ou a resolver problemas, consulte [problemas com o Azure multi-factor Authentication](multi-factor-authentication-end-user-troubleshoot.md).

## <a name="what-will-your-sign-in-experience-be"></a>O que será a experiência de início de sessão?
A experiência de início de sessão é diferente consoante aquilo que escolher utilizar como o segundo fator: uma chamada telefónica, uma aplicação de autenticação ou textos. Escolha a opção que descreve melhor que estão a fazer:

| Como pode iniciar sessão? |
| --- |
| [Com uma chamada telefónica para o meu telefone de escritório ou móvel](#signing-in-with-a-phone-call) |
| [Com um texto para o meu telemóvel](#signing-in-with-a-text-message)
| [Notificações da aplicação Microsoft Authenticator](#signing-in-with-the-microsoft-authenticator-app-using-notification) |
| [Com códigos de verificação da aplicação Microsoft Authenticator](#signing-in-with-the-microsoft-authenticator-app-using-verification-code) |
| [Com um método alternativo, porque o meu método preferido não é possível utilizar neste momento](#signing-in-with-an-alternate-method) |

## <a name="signing-in-with-a-phone-call"></a>Iniciar sessão com uma chamada telefónica
As seguintes informações descrevem a experiência de verificação de dois passos com uma chamada para o seu telefone do escritório ou móvel.

1. Inicie sessão para uma aplicação ou serviço como o Office 365, utilizando o seu nome de utilizador e palavra-passe.  
2. Microsoft chama a.  
3. Atenda o telefone e prima a tecla #.  

## <a name="signing-in-with-a-text-message"></a>Iniciar sessão com uma mensagem de texto
As informações seguintes descrevem a experiência de verificação de dois passos com uma mensagem de texto para o seu telemóvel:

1. Inicie sessão para uma aplicação ou serviço como o Office 365, utilizando o seu nome de utilizador e palavra-passe.
2. Microsoft envia uma mensagem de texto que contém um código de número.
3. Introduza o código na caixa fornecida na página de início de sessão.

## <a name="signing-in-with-the-microsoft-authenticator-app"></a>Iniciar sessão com a aplicação de autenticação da Microsoft
As informações seguintes descrevem a experiência de utilização da aplicação Microsoft Authenticator para verificações de validação em dois passos. Existem duas formas diferentes de utilizar a aplicação. Pode receber notificações push no seu dispositivo ou pode abrir a aplicação para obter um código de verificação.

### <a name="to-sign-in-with-a-notification-from-the-microsoft-authenticator-app"></a>Para iniciar sessão com uma notificação da aplicação Microsoft Authenticator
1. Inicie sessão para uma aplicação ou serviço como o Office 365, utilizando o seu nome de utilizador e palavra-passe.
2. Microsoft envia uma notificação para a aplicação Microsoft Authenticator no seu dispositivo.

  ![Microsoft envia a notificação](./media/multi-factor-authentication-end-user-signin/notify.png)

3. Abra a notificação no seu telemóvel e selecione o **verifique** chave. Se a sua empresa necessita de um PIN, introduza-o aqui.
4. Agora que deve ser iniciou sessão.

### <a name="to-sign-in-using-a-verification-code-with-the-microsoft-authenticator-app"></a>Para iniciar sessão com um código de verificação com a aplicação Microsoft Authenticator

Se utilizar a aplicação Authenticator da Microsoft para obter códigos de verificação, em seguida, quando abrir a aplicação verá um número em nome da sua conta. Este número altera a cada 30 segundos para que não utilizem o mesmo número duas vezes. Quando estiver a pedido para um código de verificação, abra a aplicação e utilizar qualquer número atualmente é apresentado.

1. Inicie sessão para uma aplicação ou serviço como o Office 365, utilizando o seu nome de utilizador e palavra-passe.
2. Microsoft pede-lhe um código de verificação.

  ![Introduza o código de verificação](./media/multi-factor-authentication-end-user-signin/verify3.png)

3. Abra a aplicação Microsoft Authenticator no seu telemóvel e introduza o código na caixa de onde está a iniciar sessão.

## <a name="signing-in-with-an-alternate-method"></a>Iniciar sessão com um método alternativo
Por vezes, não tem o telefone ou dispositivo que configurou como método de verificação preferida. Esta situação é a razão pela qual recomendamos que configure métodos de cópia de segurança para a sua conta. A secção seguinte mostra como iniciar sessão com um método alternativo quando o método principal pode não estar disponível.

1. Inicie sessão para uma aplicação ou serviço como o Office 365, utilizando o seu nome de utilizador e palavra-passe.
2. Selecione **utilizar outra opção de verificação**. Consulte as opções de verificação diferente com base no número que configurou.
3. Escolha um método alternativo e iniciar sessão.

  ![Utilize o método alternativo](./media/multi-factor-authentication-end-user-signin/alt.png)

## <a name="next-steps"></a>Passos seguintes

Se tiver problemas em iniciar sessão com a verificação de dois passos, obter mais informações em [problemas com o Azure multi-factor Authentication](multi-factor-authentication-end-user-troubleshoot.md).

Saiba como [gerir as definições da verificação de dois passos](multi-factor-authentication-end-user-manage-settings.md).

Saiba como [começar a utilizar a aplicação Microsoft Authenticator](microsoft-authenticator-app-how-to.md) para que possa utilizar notificações para iniciar sessão, em vez de textos e chamadas telefónicas.
