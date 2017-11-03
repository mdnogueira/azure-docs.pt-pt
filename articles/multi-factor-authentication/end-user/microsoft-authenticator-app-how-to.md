---
title: "Aplicação Microsoft Authenticator para telemóveis | Microsoft Docs"
description: "Saiba como atualizar para a versão mais recente do Azure Authenticator."
services: multi-factor-authentication
documentationcenter: 
author: barlanmsft
manager: femila
ms.assetid: 3065a1ee-f253-41f0-a68d-2bd84af5ffba
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: barlan
ms.reviewer: librown
ms.custom: H1Hack27Feb2017, end-user
ms.openlocfilehash: cae11f53df768daf5846ac719a45715709326d76
ms.sourcegitcommit: bd0d3ae20773fc87b19dd7f9542f3960211495f9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2017
---
# <a name="get-started-with-the-microsoft-authenticator-app"></a>Começar a utilizar a aplicação Authenticator da Microsoft
A aplicação Authenticator da Microsoft fornece um nível adicional de segurança na sua conta escolar ou profissional (por exemplo, bsimon@contoso.com) ou a sua conta Microsoft (por exemplo, bsimon@outlook.com).

A aplicação funciona de uma das seguintes formas:

* **Notificação**. A aplicação pode ajudar a impedir o acesso não autorizado às contas e parar as transações fraudulentas ao enviar uma notificação para o seu smartphone ou tablet. Basta visualizar a notificação e se esta for legítima, selecionar **verifique**. Caso contrário, pode selecionar **negar**.
* **Código de verificação**. A aplicação pode ser utilizada como token de software para gerar um código de verificação de OAuth. Depois de introduzir o nome de utilizador e palavra-passe, introduza o código fornecido pela aplicação para ecrã de início de sessão. O código de verificação fornece uma segunda forma de autenticação.

A aplicação Microsoft Authenticator substitui a aplicação Azure Authenticator. A aplicação Azure Authenticator ainda funciona, mas se decidir mudar para a nova aplicação do Microsoft Authenticator, este artigo pode ajudá-lo.  

## <a name="opt-in-for-two-step-verification"></a>Optar por verificação de dois passos

A aplicação Microsoft Authenticator não funciona por si só. Configure cada uma das suas contas que lhe pede para um segundo método de verificação depois de iniciar sessão com o nome de utilizador e palavra-passe.

Para uma conta escolar ou profissional, normalmente, não obter escolher esta funcionalidade para si próprio. Em vez disso, um administrador de segurança de optar por participar ativamente em seu nome e, em seguida, notifica-o para registar métodos de verificação para a sua conta. Se este cenário aplica-se a si, saiba mais em [que Azure multi-factor Authentication significa para me permitir](multi-factor-authentication-end-user.md).

Para uma conta pessoal, terá de configurar a verificação de dois passos para si próprio. Se tiver uma conta Microsoft, estão disponíveis nesses passos [sobre a verificação de dois passos](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification).

Também pode utilizar o Microsoft Authenticator com contas de terceiros. Pode chamar a funcionalidade de algo diferente de verificação em dois passos, mas deve ser capaz de encontrá-lo em segurança ou as definições de início de sessão.

## <a name="install-the-app"></a>Instalar a aplicação
A aplicação Authenticator da Microsoft está disponível para [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072), e [iOS](http://go.microsoft.com/fwlink/?Linkid=825073).

## <a name="add-accounts-to-the-app"></a>Adicionar contas para a aplicação
Para cada conta que pretende adicionar a aplicação Microsoft Authenticator, utilize um dos seguintes procedimentos:

### <a name="add-a-personal-microsoft-account-to-the-app"></a>Adicionar uma conta Microsoft pessoal para a aplicação

Para uma conta Microsoft pessoal (um que utiliza para iniciar sessão Outlook.com, Xbox, Skype, etc.), tudo o que precisa de executar é iniciar sessão na sua conta na aplicação Microsoft Authenticator.

### <a name="add-a-work-or-school-account-to-the-app-using-the-qr-code-scanner"></a>Adicionar uma conta escolar ou profissional para a aplicação utilizando o Verificador de código QR
1. Vá para o ecrã de definições de verificação de segurança.  Para obter informações sobre como chegar neste ecrã, consulte [alterar as definições de segurança](multi-factor-authentication-end-user-manage-settings.md#where-to-find-the-settings-page).
2. A caixa de verificação junto a **aplicação de autenticação** , em seguida, selecione **configurar**.

    ![O botão Configurar no ecrã de definições de verificação de segurança](./media/authenticator-app-how-to/azureauthe.png)

    Isto apresenta um ecrã com um código QR no mesmo.

    ![Ecrã que fornece o código QR](./media/authenticator-app-how-to/barcode2.png)
3. Abra a aplicação de autenticação da Microsoft. No **contas** ecrã, selecione  **+** e, em seguida, especifique que pretende adicionar uma conta escolar ou profissional.
4. Utilize a câmara para o código QR e, em seguida, selecione **feito** para fechar o ecrã de código QR.

    Se a câmara não está a funcionar corretamente, pode [introduzir manualmente o código QR e o URL](#add-an-account-to-the-app-manually).

5. Quando a aplicação mostra o nome da sua conta com um código de seis dígitos por baixo, terminar.

    ![Ecrã de contas](./media/authenticator-app-how-to/accounts.png)

### <a name="add-an-account-to-the-app-manually"></a>Adicionar manualmente uma conta para a aplicação
1. Vá para o ecrã de definições de verificação de segurança.  Para obter informações sobre como chegar neste ecrã, consulte [alterar as definições de segurança](multi-factor-authentication-end-user-manage-settings.md).
2. Selecione **configurar**.

    ![O botão Configurar no ecrã de definições de verificação de segurança](./media/authenticator-app-how-to/azureauthe.png)

    Isto apresenta um ecrã com um código QR no mesmo.  Tenha em atenção que o código e URL.

    ![Ecrã que fornece o código QR e o URL](./media/authenticator-app-how-to/barcode2.png)
3. Abra a aplicação de autenticação da Microsoft. No **contas** ecrã, selecione  **+** e, em seguida, especifique que pretende adicionar uma conta escolar ou profissional.

4. No scanner, selecione **introduzir manualmente o código**.

    ![Ecrã de análise de um código QR](./media/multi-factor-authentication-end-user-first-time/scan2.png)
5. Introduza o código e o URL nas caixas adequadas na aplicação, em seguida, selecione **concluir**.

    ![Ecrã para introduzir o código e o URL](./media/authenticator-app-how-to/manual.png)

6. Quando a aplicação mostra o nome da sua conta com um código de seis dígitos por baixo, terminar.

    ![Ecrã de contas](./media/authenticator-app-how-to/accounts.png)

### <a name="add-an-account-to-the-app-using-your-devices-fingerprint-or-facial-recognition-capabilities"></a>Adicionar uma conta para a aplicação com a identificação digital ou capacidades de reconhecimento facial o seu dispositivo
Organização pode exigir um PIN para concluir o desafio de verificação. A aplicação Authenticator da Microsoft pode utilizar o seu dispositivo identificação digital ou capacidades de reconhecimento facial em vez de um PIN. Para configurar esta opção na sua verificação primeiro na aplicação, verá uma opção para utilizar o Touch ID (para iOS) ou impressão digital identificação em vez disso. 

Para configurar o Touch ID para a Microsoft Authenticator, tem de concluir um desafio de verificação normal com um PIN. Microsoft Authenticator irá definir automaticamente-lo para dispositivos que suportam o Touch ID. 

![Verificação da configuração de Touch ID](./media/authenticator-app-how-to/touchid1.png)

Do ponto de reencaminhar, quando for necessário para verificar o início de sessão, selecione a notificação push recebido e analisar a sua impressão digital em vez de introduzir o PIN.

![Notificação Push](./media/authenticator-app-how-to/touchid2.png)

## <a name="use-the-app-when-you-sign-in"></a>Utilizar a aplicação quando iniciar sessão

Assim que a conta é adicionada à aplicação, poderá ser-lhe pedido para efetuar uma verificação de teste para se certificar de que tudo foi corretamente configurado. Depois disso, está concluído! Não precisa de fazer mais nada até à próxima vez que iniciar sessão.

Se optar por utilizar códigos de verificação na aplicação, pode começa a vê-los na home page. Se alterar a cada 30 segundos para que tenham sempre um novo código quando precisa de uma. Mas não precisa de fazer nada, com as mesmas até a iniciar sessão e for pedido para introduzir um código de verificação.  
