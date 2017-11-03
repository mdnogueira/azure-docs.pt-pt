---
title: "Ativar uma licenciada imagem no laboratório no Azure DevTest Labs | Microsoft Docs"
description: Aprenda a ativar uma imagem licenciada no Azure DevTest Labs no portal do Azure
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 221390d2-8d3b-4e1f-b454-43d33f8072b7
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: tarcher
ms.openlocfilehash: a74eff05285602574e45703dbe5b6caf074adecd
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="enable-a-licensed-image-in-your-lab-in-azure-devtest-labs"></a>Ativar uma imagem no laboratório no Azure DevTest Labs licenciada

No Azure DevTest Labs, uma imagem licenciada é aquele que inclui os termos e condições – normalmente, a partir de uma terceiros – que têm de ser aceites antes da imagem é acessível aos utilizadores no laboratório. As secções seguintes descrevem como trabalhar com imagens licenciadas para que fiquem disponíveis para utilização para a criação de máquinas virtuais.

## <a name="determining-whether-a-licensed-image-is-available-to-users"></a>Ao determinar se uma imagem licenciada está disponível para utilizadores
O primeiro passo para permitir aos utilizadores criar VMs a partir de uma imagem licenciada é certificar-se de que os termos e condições foram aceites para a imagem licenciada. Os passos seguintes mostram como pode ver o estado da oferta de uma imagem de licenciado e, se necessário, aceite os termos e condições.

1. Inicie sessão no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selecione **Mais serviços**, e, em seguida, selecione **DevTest Labs** na lista.

1. Na lista de laboratórios, selecione o laboratório pretendido.  

1. No painel esquerdo abaixo **definições**, selecione **políticas de configuração e**.

1. No painel esquerdo abaixo **BASES de máquinas virtuais**, selecione **imagens do Marketplace**. 

    ![Item de menu de imagens do Marketplace](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-marketplace-images.png)

    É apresentada uma lista de todas as imagens do marketplace disponíveis, incluindo o **oferecem estado** de cada imagem.

    ![Lista de imagens do marketplace que mostra o estado da oferta de cada imagem](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-offer-status.png)

    Uma imagem licenciada mostra um Estado de oferta de 
    
    - **Termos aceites:** a imagem licenciada está disponível para os utilizadores criem VMs. 
    - **Termos de revisão necessário:** a imagem licenciada não está atualmente disponível para os utilizadores. Os termos e condições da licença de têm de ser aceites antes dos utilizadores de laboratório podem utilizá-la para criar as VMs. 

## <a name="making-a-licensed-image-available-to-lab-users"></a>Disponibilizar uma imagem licenciada aos utilizadores de laboratório
Para certificar-se de que uma imagem licenciada está disponível para utilizadores de laboratório, um proprietário de laboratório com permissões de administrador primeiro deve aceitar os termos e condições para essa imagem licenciada. Ativar a implementação programática para a subscrição associada uma imagem licenciada automaticamente aceita os termos legais e as declarações de privacidade para essa imagem. [Trabalhar com imagens do Marketplace no Azure Resource Manager](https://azure.microsoft.com/blog/working-with-marketplace-images-on-azure-resource-manager/) fornece informações adicionais sobre a implementação programática de imagens do marketplace.

Pode ativar a implementação programática para uma imagem licenciada, seguindo estes passos:

1. No [portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040) na lista de **imagens do Marketplace**, identificar uma imagem licenciada para a qual pretende que os utilizadores ter acesso, mas cujos termos não foram aceites. Por exemplo, poderá ver uma Máquina Virtual ciência de dados que mostra o estado **termos aceites** ou **termos revisão necessário**.

    ![Configurar a implementação programática janela](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-licensed-images.png)

   > [!NOTE]
   > VMs de ciência de dados são imagens de Máquina Virtual do Azure, pré-instalado, configurar e testar com várias ferramentas populares que são frequentemente utilizadas para análise de dados, formação de AI e de aprendizagem. [Introdução ao Data ciência de Máquina Virtual do Azure para Linux e Windows](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview) fornece um elevado grau de informações sobre DSVMs.
   >
   >

1. No **oferecem estado** coluna para a imagem, selecione **termos revisão necessário**.

1. Na janela de configurar a implementação programática, selecione **ativar**.

    ![Configurar a implementação programática janela](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-enable-programmatic-deployment.png)

   > [!IMPORTANT]
   > Poderá ver várias subscrições da lista na janela de configurar a implementação programática. Certifique-se de que pretende ativar a implementação programática apenas para a subscrição que se destinam.
   >
   >


1. Selecione **Guardar**. Na lista de imagens do marketplace, imagem que mostra agora **termos aceites** e está disponível para os utilizadores criar máquinas virtuais.

## <a name="related-blog-posts"></a>Mensagens de blogue relacionados

- [Imagens personalizadas ou fórmulas?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Copiar as imagens personalizadas entre Azure DevTest Labs](http://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Passos seguintes

- [Adicionar uma VM para o laboratório](./devtest-lab-add-vm-with-artifacts.md)
