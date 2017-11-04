---
title: "Iniciar o pedido de suporte para a série 8000 do StorSimple | Microsoft Docs"
description: "Saiba como criar um pedido de suporte e iniciar uma sessão de suporte no dispositivo StorSimple."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 2ebc20fe-f490-4749-8e43-c9fac86f1676
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: alkohli;anbacker
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8d25fb4902d37faca5358e5a9010e9e146e344e1
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2017
---
# <a name="contact-microsoft-support-for-your-storsimple"></a>Contacte o suporte da Microsoft para do StorSimple
> [!NOTE]
> O portal clássico para StorSimple foi preterido. Os gestores de dispositivo do StorSimple será automaticamente mudada para o novo portal do Azure de acordo com a agenda de preterição. Irá receber uma mensagem de e-mail e uma notificação de movimentação do portal. Este documento também será descontinuado em breve. Para ver a versão deste artigo para o novo portal do Azure, aceda a [contactar o suporte da Microsoft para sua StorSimple](storsimple-8000-contact-microsoft-support.md). Para quaisquer perguntas sobre a mudança, consulte [FAQ: mover para o portal do Azure](storsimple-8000-move-azure-portal-faq.md).

Se tiver quaisquer problemas com a sua solução do Microsoft Azure StorSimple, pode criar um pedido de serviço para o suporte técnico. Numa sessão com o seu engenheiro de suporte online, também poderá ter de iniciar uma sessão de suporte no dispositivo StorSimple. Este artigo explica como:

* Como criar um pedido de suporte.
* Como iniciar uma sessão de suporte na interface do Windows PowerShell do dispositivo StorSimple.

Reveja o [SLAs de suporte de série do StorSimple 8000 e informações](https://msdn.microsoft.com/library/mt433077.aspx) antes de criar um pedido de suporte.

## <a name="create-a-support-request"></a>Criar um pedido de suporte
Execute os seguintes passos para criar um pedido de suporte:

#### <a name="to-create-a-support-request"></a>Para criar um pedido de suporte
1. No [portal clássico do Azure](https://manage.windowsazure.com/), no canto superior direito, clique em seu nome de conta e, em seguida, clique em **contactar o suporte da Microsoft**.
   
    ![MS contacte o suporte através do ManagementPortal](./media/storsimple-contact-microsoft-support/Ibiza1.png)
2. Será redirecionado para o novo portal do Azure (portal.azure.com). Clique em de **novo pedido de suporte** mosaico.
   
    ![MS contacte o suporte através do novo portal](./media/storsimple-contact-microsoft-support/Ibiza2.png)
   
    No lado direito do ecrã, o **novo pedido de suporte** painel aparece. 
   
    ![Novo painel de pedido de suporte](./media/storsimple-contact-microsoft-support/Ibiza3a.png)
3. No **Noções básicas** diálogo caixa, concluir os seguintes procedimentos:                                
   
   1. Do **emitir tipo** na lista pendente, selecione **técnica**.
   2. Selecione um **subscrição** na lista pendente.
   3. Do **serviço** na lista pendente, selecione **StorSimple**. 
   4. Selecione um **plano de suporte** na lista pendente. É necessário um plano de suporte pago para ativar o suporte técnico.
4. Clique em **Seguinte**. O **problema** é apresentada a caixa de diálogo.
   
    ![Novo painel de pedido de suporte](./media/storsimple-contact-microsoft-support/Ibiza5a.png) 
5. No **problema** diálogo caixa, concluir os seguintes procedimentos:
   
   1. Selecione um **gravidade** ao nível da lista pendente.
   2. Selecione um **tipo de problema** na lista pendente.
   3. Selecione um **categoria** na lista pendente. 
   4. No **detalhes** caixa, descreva resumidamente o seu problema.
   5. No **período de tempo** caixa, indique a data, hora e fuso horário que corresponde à ocorrência mais recente do seu problema.
   6. Em **carregamento de ficheiros**, clique no ícone de pasta para navegar para o pacote de suporte.
   7. Selecione o **partilhar informações de diagnóstico** caixa de verificação.
6. Clique em **Seguinte**. O **informações de contacto** é apresentada a caixa de diálogo.
   
    ![Novo painel de pedido de suporte](./media/storsimple-contact-microsoft-support/Ibiza6a.png) 
7. Introduza as informações de contacto e selecione um método de contacto (telefone ou e-mail). 
8. Selecione o **guardar alterações de contactos para futuros pedidos de suporte** caixa de verificação.
9. Clique em **Criar**.

Depois de ter submetido o pedido, o engenheiro de suporte irá contactá-lo logo que possível para continuar com o seu pedido.

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
> 
> 

