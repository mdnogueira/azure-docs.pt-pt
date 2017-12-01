---
title: "Publique um announcment para um laboratório no Azure DevTest Labs | Microsoft Docs"
description: "Saiba como adicionar um anúncio para um laboratório no Azure DevTest Labs"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 67a09946-4584-425e-a94c-abe57c9cbb82
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/30/2017
ms.author: tarcher
ms.openlocfilehash: db92caa497b3db6b3e05e59e169111cedc3c71c6
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2017
---
# <a name="post-an-announcement-to-a-lab-in-azure-devtest-labs"></a>Publicar um anúncio para um laboratório no Azure DevTest Labs

Como um administrador de laboratório, pode publicar um anúncio personalizado um laboratório existente para notificar os utilizadores sobre alterações recentes ou adições para o laboratório. Por exemplo, pode querer informar os utilizadores sobre:

- Novos tamanhos VM que estão disponíveis
- Imagens que não são utilizáveis atualmente
- Atualizações às políticas de laboratório

Depois de publicado, o anúncio é apresentado na página de descrição geral do laboratório e o utilizador pode selecioná-lo para obter mais detalhes.

A funcionalidade de anúncio destina-se a ser utilizado para notificações temporárias.  Pode desativar um anúncio facilmente depois de já não é necessário.

## <a name="steps-to-post-an-announcement-in-an-existing-lab"></a>Passos para publicar um anúncio de um laboratório existente

1. Inicie sessão no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Se necessário, selecione **todos os serviços**e, em seguida, selecione **DevTest Labs** da lista. (O laboratório já pode ser apresentado no Dashboard em **todos os recursos**).
1. Na lista de laboratórios, selecione o laboratório, em que pretende publicar um anúncio.  
1. No laboratório de **descrição geral** área selecione **políticas de configuração e**.  

    ![Botão de configuração e políticas](./media/devtest-lab-announcements/devtestlab-config-and-policies.png)

1. No lado esquerdo em **definições**, selecione **anúncio de laboratório**.

    ![Botão de anúncio de laboratório](./media/devtest-lab-announcements/devtestlab-announcements.png)

1. Para criar uma mensagem para os utilizadores neste laboratório, defina **ativado** para **Sim**.

1. Introduza um **título do anúncio** e **texto de anúncio**.

   O título pode ser até 100 carateres e é apresentado ao utilizador na página de descrição geral do laboratório. Se o utilizador seleciona o título, é apresentado o texto de anúncio.

   O texto de anúncio aceita markdown. Como introduzir o texto de anúncio, pode ver a mensagem na área de pré-visualização na parte inferior do ecrã.

    ![Ecrã de anúncio de laboratório para criar a mensagem.](./media/devtest-lab-announcements/devtestlab-post-announcement.png)


1. Selecione **guardar** assim que o anúncio está pronto para publicar.

Se já não pretende mostrar este anúncio aos utilizadores de laboratório, volte ao **anúncio de laboratório** página e defina **ativado** para **não**.

## <a name="steps-for-users-to-view-an-announcement"></a>Passos para que os utilizadores vejam um anúncio

1. Do [portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040), selecione um laboratório.

1. Se o laboratório tiver um anúncio publicado para o mesmo, é apresentado um aviso de informações na parte superior da página de descrição geral do laboratório. Este aviso de informações é o título de anúncio que foi especificado quando foi criado o anúncio.

    ![Anúncio de laboratório na página Descrição geral](./media/devtest-lab-announcements/devtestlab-user-announcement.png)

1. O utilizador pode selecionar a mensagem para ver o anúncio de todo.

    ![Obter mais informações para o anúncio de laboratório](./media/devtest-lab-announcements/devtestlab-user-announcement-text.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Passos seguintes
* Se alterar ou definir uma política de laboratório, pode querer publicar um anúncio para informar os utilizadores. [Definir políticas e as agendas](devtest-lab-set-lab-policy.md) fornece informações sobre como aplicar restrições e convenções na sua subscrição através da utilização de políticas personalizadas.
* Explorar o [Galeria de modelo de início rápido do DevTest Labs do Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/Samples).
