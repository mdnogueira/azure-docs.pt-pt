---
title: "Como utilizar as palavras-passe de aplicação no Azure MFA? | Microsoft Docs"
description: "Esta página irá ajudar os utilizadores a compreenderem quais são as palavras-passe de aplicação e o que são utilizadas com regard a MFA do Azure."
services: multi-factor-authentication
documentationcenter: 
author: barlanmsft
manager: femila
editor: yossib
ms.assetid: 345b757b-5a2b-48eb-953f-d363313be9e5
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: barlan
ms.custom: end-user
ms.openlocfilehash: 4f343b4d4b5cb3c8be9605e9ab43200072b193e1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="what-are-app-passwords-in-azure-multi-factor-authentication"></a>Quais são palavras-passe de aplicação no Azure multi-factor Authentication?
Determinadas aplicações não baseadas no browser, por exemplo, o cliente de e-mail nativa Apple que utiliza o Exchange Active Sync, atualmente não suportam autenticação multifator. Autenticação multifator é ativada por utilizador. Isto significa que se um utilizador tiver sido ativado para autenticação multifator e estão a tentar utilizar aplicações não baseadas no browser, será possível fazê-lo. Uma palavra-passe de aplicação permite tal utilização.

Assim que tiver uma palavra-passe de aplicação, utilize esta em vez da palavra-passe original com estas aplicações não baseadas no browser. Isto acontece porque ao registar para a verificação de dois passos, está a informar Microsoft para não permitir que qualquer pessoa que inicie sessão com a palavra-passe se estes também não é possível efetuar a verificação de segundo. O cliente de e-mail nativa Apple no seu telemóvel não pode iniciar sessão como porque não é possível pedir a verificação. A solução para este consiste em criar uma mais segura palavra-passe de aplicação não utilizar diárias, mas apenas para essas aplicações que não suportem a verificação de dois passos. Utilize a palavra-passe de aplicação para que as aplicações podem ignorar multi-factor authentication e continuar a funcionar.

> [!NOTE]
> Clientes do Office 2013 (incluindo o Outlook) suportam o novo protocolos de autenticação e pode ser utilizado com verificação de dois passos.  Isto significa que uma vez ativada, as palavras-passe de aplicação não são necessárias para utilização com clientes do Office 2013.  Para obter mais informações, consulte [Office 2013 autenticação moderna pré-visualização pública anunciada](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/).


## <a name="how-to-use-app-passwords"></a>Como utilizar as palavras-passe de aplicação
Seguem-se algumas coisas lembrar-se sobre como utilizar as palavras-passe de aplicação.

* Não crie as suas próprias palavras-passe de aplicação. Em vez disso, são geradas automaticamente. Uma vez que apenas têm de introduzir a palavra-passe de aplicação uma vez por aplicação, é mais segura utilizar uma palavra-passe mais complexa, gerada automaticamente, em vez de efetuar um que pode não se esqueça.
* Atualmente não há um limite de 40 palavras-passe por utilizador. Se tentar criar um após ter atingido o limite, será solicitado elimine uma das suas palavras-passe de aplicação existentes antes de criar um novo.
* Deve utilizar uma palavra-passe por dispositivo, não por aplicação. Por exemplo, pode criar uma palavra-passe de aplicação para o seu computador portátil e utilizar essa palavra-passe de aplicação para todas as aplicações desse portátil. Em seguida, crie uma segunda palavra-passe de aplicação a utilizar para todas as suas aplicações no seu ambiente de trabalho.
* É-lhe dada uma palavra-passe de aplicação pela primeira vez, registar para a verificação de dois passos.  Se precisar de adicionais, pode criá-los.



## <a name="creating-and-deleting-app-passwords"></a>Criar e eliminar as palavras-passe de aplicação
Durante a sua inicial início de sessão, é-lhe dada uma palavra-passe de aplicação que pode utilizar.  Além disso, também pode criar e eliminar as palavras-passe de aplicação mais tarde.  Como fazê-lo depende de como utilizar a autenticação multifator. Responda às questões seguintes para determinar onde deve passar para gerir palavras-passe de aplicação:

1. Utiliza a verificação da sua conta Microsoft pessoal? Se Sim, devem consultar ao [palavras-passe de aplicação e a verificação de dois passos](https://support.microsoft.com/help/12409/microsoft-account-app-passwords-two-step-verification) artigo para obter ajuda. Se não, continue a pergunta de dois.

2. OK, pelo que utilizar a verificação de dois passos para a sua conta escolar ou profissional. Utilizá-la para iniciar sessão em aplicações do Office 365? Se Sim, devem consultar a [criar uma palavra-passe de aplicação para o Office 365](https://support.office.com/article/Create-an-app-password-for-Office-365-3e7c860f-bda4-4441-a618-b53953ee1183) para obter ajuda. Se não, continue a pergunta três.

3. Utiliza a verificação com o Microsoft Azure? Se Sim, continuar para o [gerir palavras-passe de aplicação no portal do Azure](#manage-app-passwords-in-the-Azure-portal) secção deste artigo. Se não, continue a pergunta quatro.

4. Não tem a certeza, quando utiliza a verificação de dois passos? Avance para o [gerir palavras-passe de aplicação com o portal de MyApps](#manage-app-passwords-with-the-myapps-portal) secção deste artigo.


## <a name="manage-app-passwords-in-the-azure-portal"></a>Gerir palavras-passe de aplicação no portal do Azure
Se utilizar a verificação com o Azure, que pretende criar palavras-passe de aplicação através do portal do Azure.

### <a name="to-create-app-passwords-in-the-azure-portal"></a>Para criar palavras-passe de aplicação no portal do Azure
1. Inicie sessão no Portal Clássico do Azure.
2. Na parte superior, o nome de utilizador com o botão direito e selecione a verificação de segurança adicional.
3. Na página proofup, na parte superior, selecione as palavras-passe de aplicação
4. Clique em **Criar**.
5. Introduza um nome para a palavra-passe de aplicação e clique em **seguinte**
6. Copie a palavra-passe de aplicação para a área de transferência e cole-o sua aplicação.

   ![Nuvem](./media/multi-factor-authentication-end-user-app-passwords/app2.png)


### <a name="to-delete-app-passwords-in-the-azure-portal"></a>Para eliminar as palavras-passe de aplicação no portal do Azure
1. Inicie sessão no Portal Clássico do Azure.
2. Na parte superior, o nome de utilizador com o botão direito e selecione a verificação de segurança adicional.
3. Na parte superior, junto a verificação de segurança adicional, selecione **palavras-passe de aplicação.**
4. Junto a palavra-passe de aplicação que pretende eliminar, selecione **eliminar**.
5. Confirmar a eliminação clicando **Sim**.
6. Depois da palavra-passe de aplicação é eliminada, pode clicar em **fechar**.


## <a name="manage-app-passwords-with-the-myapps-portal"></a>Gerir palavras-passe de aplicação com o portal de MyApps.
Se não tem a certeza de como utilizar a autenticação multifator, em seguida, pode sempre criar e eliminar palavras-passe de aplicação através do portal de myapps.

### <a name="to-create-an-app-password-using-the-myapps-portal"></a>Para criar uma palavra-passe de aplicação utilizando o portal de Myapps
1. Inicie sessão no [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Clique no seu nome no canto superior direito e selecione **perfil**.
3. Selecione **verificação adicional de segurança**.
   ![Selecione verificação adicional de segurança - captura de ecrã](./media/multi-factor-authentication-end-user-manage/myapps1.png)

4. Selecione **palavras-passe de aplicação**.
   ![Selecione as palavras-passe de aplicação - captura de ecrã](./media/multi-factor-authentication-end-user-app-passwords/apppass2.png)

5. Clique em **Criar**.
6. Introduza um nome para a palavra-passe de aplicação e clique em **seguinte**.
7. Copie a palavra-passe de aplicação para a área de transferência e cole-o sua aplicação.
   ![Criar uma palavra-passe de aplicação](./media/multi-factor-authentication-end-user-app-passwords/create2.png)

### <a name="to-delete-an-app-password-using-the-myapps-portal"></a>Para eliminar uma palavra-passe de aplicação utilizando o portal de Myapps
1. Inicie sessão no [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Na parte superior, selecione o perfil.
3. Selecione **verificação adicional de segurança**.

   ![Selecione verificação adicional de segurança - captura de ecrã](./media/multi-factor-authentication-end-user-manage/myapps1.png)

4. Selecione **palavras-passe de aplicação**.

   ![Selecione as palavras-passe de aplicação - captura de ecrã](./media/multi-factor-authentication-end-user-app-passwords/apppass2.png)

5. Junto a palavra-passe de aplicação que pretende eliminar, clique em **eliminar**.

   ![Eliminar uma palavra-passe de aplicação](./media/multi-factor-authentication-end-user-app-passwords/delete1.png)

6. Confirme que pretende eliminar essa palavra-passe clicando **Sim**.
7. Depois da palavra-passe de aplicação é eliminada, pode clicar em **fechar**.

## <a name="next-steps"></a>Passos seguintes

- [Gerir as definições da verificação de dois passos](multi-factor-authentication-end-user-manage-settings.md)

- Experimentar o [aplicação Microsoft Authenticator](microsoft-authenticator-app-how-to.md) para verificar os inícios de sessão com as notificações de aplicação, em vez de textos ou chamadas a receber.
