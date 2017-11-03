---
title: "Utilizar o ambiente de trabalho remoto com funções do Azure | Microsoft Docs"
description: "Utilizar o ambiente de trabalho remoto com funções do Azure"
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: f5727ebe-9f57-4d7d-aff1-58761e8de8c1
ms.service: multiple
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/11/2016
ms.author: kraigb
ms.openlocfilehash: eab135d10c0d6df8ca72ac47d6804017a998a3d2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="using-remote-desktop-with-azure-roles"></a>Utilizar o ambiente de trabalho remoto com funções do Azure
Ao utilizar o SDK do Azure e os serviços de ambiente de trabalho remoto, pode aceder a máquinas virtuais que são alojadas pelo Azure e funções do Azure. No Visual Studio, pode configurar os serviços de ambiente de trabalho remoto de um projeto de serviço em nuvem do Azure. Para ativar os serviços de ambiente de trabalho remoto, tem de criar um projeto de trabalho que contém uma ou mais funções e, em seguida, publicá-lo no Azure.

> [!IMPORTANT]
> Deve aceder a uma função do Azure para desenvolvimento apenas ou de resolução de problemas. O objetivo de cada máquina virtual está a executar uma função específica da sua aplicação do Azure, não para executar outras aplicações de cliente. Se pretender utilizar o Azure para alojar uma máquina virtual que pode utilizar para qualquer finalidade, consulte a aceder a máquinas de virtuais do Azure no Explorador de servidores.
> 
> 

## <a name="to-enable-and-use-remote-desktop-for-an-azure-role"></a>Para ativar e utilizar o ambiente de trabalho remoto para uma função do Azure
1. No Explorador de soluções, abra o menu de atalho para o seu projeto de serviço de nuvem e, em seguida, escolha **publicar**.
   
    O **publicar aplicação Azure** é apresentado o assistente.
   
    ![Publicar o comando para um projeto de serviço em nuvem](./media/vs-azure-tools-remote-desktop-roles/IC799161.png)
2. Na parte inferior **definições de publicação do Microsoft Azure** página do assistente, selecione o **ativar o ambiente de trabalho remoto** para todos os caixa de verificação de funções. 
   
    O **configuração do ambiente de trabalho remoto** é apresentada a caixa de diálogo.
3. Na parte inferior do **configuração do ambiente de trabalho remoto** diálogo caixa, escolha o **mais opções** botão. 
   
    Esta ação apresenta uma caixa de lista pendente que lhe permite criar ou escolha um certificado para que possam encriptar as informações de credenciais quando a ligação através do ambiente de trabalho remoto.
4. Na lista pendente, escolha  **&lt;criar >**, ou escolha um já existente na lista. 
   
    Se escolher um certificado existente, ignore os passos seguintes.
   
   > [!NOTE]
   > Os certificados que sejam necessárias para uma ligação de ambiente de trabalho remoto são diferentes dos certificados que utiliza para outras operações do Azure. O certificado de acesso remoto tem de ter uma chave privada.
   > 
   > 
   
    O **Criar certificado** é apresentada a caixa de diálogo.
   
   1. Forneça um nome amigável para o novo certificado e, em seguida, escolha o **OK** botão. O novo certificado é apresentado na caixa de lista pendente.
   2. No **configuração do ambiente de trabalho remoto** diálogo caixa, forneça um nome de utilizador e uma palavra-passe.
      
       Não é possível utilizar uma conta existente. Não especifique administrador como o nome de utilizador para a nova conta.
      
      > [!NOTE]
      > Se a palavra-passe não cumpre os requisitos de complexidade, é apresentado um ícone vermelho junto à caixa de texto de palavra-passe. A palavra-passe tem de incluir as letras em maiúsculas, letras minúsculas e números ou símbolos.
      > 
      > 
   3. Selecione uma data em que a conta irá expirar e depois das ligações de ambiente de trabalho remotas serão bloqueadas.
   4. Depois de que forneceu as informações necessárias, escolha o **OK** botão.
      
       São adicionadas várias definições que permitem dos serviços de acesso remoto para os ficheiros. csdef e. cscfg.
5. No **definições de publicação do Microsoft Azure** assistente, escolha o **OK** botão quando estiver pronto para publicar o seu serviço em nuvem.
   
    Se não estiver pronto para publicar, escolha o **Cancelar** botão. As definições de configuração são guardadas e pode publicar o seu serviço em nuvem mais tarde.

## <a name="connect-to-an-azure-role-by-using-remote-desktop"></a>Ligar a uma função do Azure, utilizando o ambiente de trabalho remoto
Depois de publicar o seu serviço em nuvem no Azure, pode utilizar o Explorador de servidores para iniciar sessão as máquinas virtuais que aloja do Azure. 

1. No Explorador de servidores, expanda o **Azure** nó e, em seguida, expanda o nó para um serviço em nuvem e uma das respetivas funções para apresentar uma lista de instâncias.
2. Abra o menu de atalho para um nó de instância e, em seguida, escolha **ligar utilizando o ambiente de trabalho remoto**.
   
    ![Ligação através do ambiente de trabalho remoto](./media/vs-azure-tools-remote-desktop-roles/IC799162.png)
3. Introduza o nome de utilizador e palavra-passe que criou anteriormente. Tem agora sessão iniciada na sua sessão remota.

