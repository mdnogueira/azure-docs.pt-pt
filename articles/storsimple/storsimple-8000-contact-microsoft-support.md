---
title: "Criar pedido de suporte ou maiúsculas e minúsculas para a série 8000 do StorSimple | Microsoft Docs"
description: "Saiba como registar o pedido de suporte e iniciar uma sessão de suporte no seu dispositivo de série 8000 do StorSimple."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2017
ms.author: alkohli;
ms.openlocfilehash: 4b5a14237ce79100f980b2186b2c3c887abaa296
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="contact-microsoft-support"></a>Contacte o suporte da Microsoft

O Gestor de dispositivos do StorSimple fornece a capacidade de **iniciar um novo pedido de suporte** no painel de resumo de serviço. Se tiver quaisquer problemas com a sua solução StorSimple, pode criar um pedido de serviço para o suporte técnico. Numa sessão com o seu engenheiro de suporte online, também poderá ter de iniciar uma sessão de suporte no dispositivo StorSimple. Este artigo explica como:

* Como criar um pedido de suporte.
* Como gerir uma pedido de vida de suporte no portal.
* Como iniciar uma sessão de suporte na interface do Windows PowerShell do dispositivo StorSimple.

Reveja o [SLAs de suporte de série do StorSimple 8000 e informações](https://msdn.microsoft.com/library/mt433077.aspx) antes de criar um pedido de suporte.

## <a name="create-a-support-request"></a>Criar um pedido de suporte

Consoante o [plano de suporte](https://azure.microsoft.com/support/plans/), pode criar pedidos de suporte para um problema no dispositivo StorSimple diretamente a partir do painel de resumo do serviço Gestor de dispositivos do StorSimple. Execute os seguintes passos para criar um pedido de suporte:

#### <a name="to-create-a-support-request"></a>Para criar um pedido de suporte

1. Aceda ao seu serviço Gestor de Dispositivos do StorSimple. As definições de resumo do painel de serviço, aceda a **suporte + resolução de problemas** secção e, em seguida, clique em **novo pedido de suporte**.
     
    ![MS contacte o suporte através do novo portal](./media/storsimple-8000-contact-microsoft-support/contactsupport1.png)
   
2. No **novo pedido de suporte** painel, selecione **Noções básicas**. No **Noções básicas** painel, efetue os seguintes passos:
   1. Do **emitir tipo** na lista pendente, selecione **técnica**.
   2. Atual **subscrição**, **serviço** tipo e o **recursos** (serviço do Gestor de dispositivos do StorSimple) são automaticamente escolhido. 
   3. Selecione um **plano de suporte** na lista pendente, se tiver vários planos de associados à subscrição. É necessário um plano de suporte pago para ativar o suporte técnico.
   4. Clique em **Seguinte**.

       ![MS contacte o suporte através do novo portal](./media/storsimple-8000-contact-microsoft-support/contactsupport2.png)

3. No **novo pedido de suporte** painel, selecione **passo 2 problema**. No **problema** painel, efetue os seguintes passos:
    
    1. Escolha o **gravidade**.
    2. Especifique se o problema está relacionado com a aplicação ou o serviço do Gestor de dispositivos do StorSimple.
    3. Escolha um **categoria** para este problema e fornecer mais **detalhes** sobre o problema.
    4. Forneça a data de início e a hora para o problema.
    5. No **carregamento de ficheiros**, clique no ícone de pasta para navegar para o pacote de suporte.
    6. Verifique **partilhar informações de diagnóstico**.
    7. Clique em **Seguinte**.

       ![MS contacte o suporte através do novo portal](./media/storsimple-8000-contact-microsoft-support/contactsupport3.png) 

4. No **novo pedido de suporte** painel, clique em **informações de contacto do passo 3**. No **informações de contacto** painel, efetue os seguintes passos:

    1. No **contacte opções**, forneça o seu método de contacto preferido (telefone ou e-mail) e o idioma. O tempo de resposta é selecionado automaticamente com base no seu plano de subscrição.
    2. As informações de contacto, forneça o nome de correio eletrónico, contacte opcional, país. Selecione o **guardar alterações de contactos para futuros pedidos de suporte** caixa de verificação.
    3. Clique em **Criar**.
   
        ![MS contacte o suporte através do novo portal](./media/storsimple-8000-contact-microsoft-support/contactsupport5.png)   

    Support da Microsoft irá utilizar estas informações para entrar, para obter mais informações, diagnóstico e de resolução.
Depois de ter submetido o pedido, o engenheiro de suporte irá contactá-lo logo que possível para continuar com o seu pedido.

## <a name="manage-a-support-request"></a>Gerir um pedido de suporte

Depois de criar um pedido de suporte, pode gerir o ciclo de vida do mesmo no portal.

#### <a name="to-manage-your-support-requests"></a>Para gerir os seus pedidos de suporte

1. Para obter a página de ajuda e suporte, navegue para **Procurar > ajuda + suporte**.

    ![Gerir pedidos de suporte](./media/storsimple-8000-contact-microsoft-support/managesupport1.png)

2. Uma tabela listagem de todos os pedidos de suporte é apresentada no **ajuda + suporte** painel.

    ![Gerir pedidos de suporte](./media/storsimple-8000-contact-microsoft-support/managesupport2.png)

3. Selecione e clique num pedido de suporte. Pode ver o estado e os detalhes para este pedido. Clique em **+ nova mensagem** se pretender que a seguir a cópia de segurança neste pedido.

    ![Gerir pedidos de suporte](./media/storsimple-8000-contact-microsoft-support/managesupport3.png)

## <a name="start-a-support-session-in-windows-powershell-for-storsimple"></a>Iniciar uma sessão de suporte no Windows PowerShell para StorSimple

Para resolver quaisquer problemas que podem ocorrer com o dispositivo StorSimple, terá de envolvimento com a equipa de Support da Microsoft. Support da Microsoft podem ter de utilizar uma sessão de suporte para iniciar sessão no seu dispositivo.

Execute os seguintes passos para iniciar uma sessão de suporte:

#### <a name="to-start-a-support-session"></a>Para iniciar uma sessão de suporte

1. Aceder ao dispositivo diretamente através da consola de série ou através de uma sessão telnet a partir de um computador remoto. Para tal, siga os passos no [utilizar o PuTTY para ligar à consola de série do dispositivo](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console).
2. Na sessão que se abre, prima a **Enter** chave para obter uma linha de comandos.
3. No menu da consola de série, selecione a opção 1, **iniciar sessão com acesso total**.
4. Na linha de comandos, escreva a palavra-passe seguinte:
   
    `Password1`
5. Na linha de comandos, escreva o seguinte comando:
   
    `Enable-HcsSupportAccess`
6. Será apresentada ao utilizador uma cadeia encriptada. Copie esta cadeia para um editor de texto como o bloco de notas.
7. Guarde esta cadeia e enviá-lo numa mensagem de e-mail ao Microsoft Support.

> [!IMPORTANT]
> Pode desativar o acesso de suporte executando `Disable-HcsSupportAccess`. O dispositivo StorSimple também irá tentar desativar o acesso de suporte de 8 horas após a sessão foi iniciada. É uma melhor prática para alterar as credenciais de dispositivo do StorSimple depois de iniciar uma sessão de suporte.


## <a name="next-steps"></a>Passos seguintes

Saiba como [diagnosticar e resolver problemas relacionados com o seu dispositivo de série 8000 do StorSimple](storsimple-troubleshoot-deployment.md)
