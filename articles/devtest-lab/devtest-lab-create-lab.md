---
title: "Criar um laboratório no Azure DevTest Labs | Microsoft Docs"
description: "Criar um laboratório no Azure DevTest Labs para máquinas virtuais"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 8b6d3e70-6528-42a4-a2ef-449575d0f928
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/07/2017
ms.author: tarcher
ms.openlocfilehash: 9657a4681f6bd9cc126de50883ce7a66357156e0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-lab-in-azure-devtest-labs"></a>Criar um laboratório no Azure DevTest Labs
Um laboratório no Azure DevTest Labs é a infraestrutura que abrange um grupo de recursos, como máquinas virtuais (VM), que lhe permite gerir melhor esses recursos através da especificação de limites e quotas. Este artigo serve de orientação durante o processo de criação de laboratórios com o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos
Para criar um laboratório, precisa de:

* Uma subscrição do Azure. Para obter informações sobre opções de compra do Azure, consulte [Como comprar o Azure](https://azure.microsoft.com/pricing/purchase-options/) ou [Avaliação gratuita de um mês](https://azure.microsoft.com/pricing/free-trial/). Tem de ser o proprietário da subscrição para criar o laboratório.

## <a name="steps-to-create-a-lab-in-azure-devtest-labs"></a>Passos para criar um laboratório no Azure DevTest Labs
Os passos seguintes mostram como utilizar o portal do Azure para criar um laboratório no Azure DevTest Labs. 

1. Inicie sessão no [portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. No menu principal no lado esquerdo, selecione **Mais serviços** (na parte inferior da lista).

    ![Opção de menu Mais serviços](./media/devtest-lab-create-lab/more-services-menu-option.png)

1. Na lista de serviços disponíveis, **DevTest Labs**.
1. Na área **DevTest Labs**, selecione **Adicionar**.
   
    ![Adicionar um laboratório](./media/devtest-lab-create-lab/add-lab-button.png)

1. Em **Criar um laboratório do DevTest Labs**:
   
    1. Introduza um **Nome de Laboratório** para o novo laboratório.
    2. Selecione a **Subscrição** para associar ao laboratório.
    3. Selecione uma **Localização** onde armazenar o laboratório.
    4. Selecione **Encerramento automático** para especificar se pretende ativar e definir os parâmetros para o encerramento automático de todas as VMs do laboratório. A funcionalidade de encerramento automático é principalmente uma funcionalidade de poupança de custos, na qual poderá especificar quando deseja que a VM seja automaticamente desligada. Pode alterar as configurações de encerramento automático depois de criar o laboratório ao seguir os passos descritos no artigo [Manage all policies for a lab in Azure DevTest Labs](./devtest-lab-set-lab-policy.md#set-auto-shutdown) (Gerir todas as políticas de um laboratório no Azure DevTest Labs).
    1. Introduza as informações de **NOME** e **VALOR** nas **Etiquetas** se quiser criar etiquetas personalizadas que são adicionadas a todos os recursos que criar no laboratório. As etiquetas são úteis para ajudar a gerir e organizar os recursos do laboratório por categoria. Para obter mais informações sobre etiquetas, incluindo como adicioná-las depois de criar o laboratório, veja [Add tags to a lab](devtest-lab-add-tag.md) (Adicionar etiquetas a um laboratório).
    5. Selecione **Afixar ao Dashboard** se pretender que um atalho do laboratório apareça no dashboard do portal.
    6. Selecione **Opções de automatização** para obter modelos do Azure Resource Manager para a automatização de configuração. 
    7. Selecione **Criar**. Pode monitorizar o estado do processo de criação de laboratório ao observar a área de **Notificações**. Depois de concluído, atualize a página para ver o laboratório recém-criado na lista de laboratórios.  
    
    ![Criar uma secção de laboratório do DevTest Labs](./media/devtest-lab-create-lab/create-devtestlab-blade.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Passos seguintes
Assim que tiver criado o seu laboratório, seguem-se alguns passos a ter em consideração:

* [Acesso seguro a um laboratório](devtest-lab-add-devtest-user.md).
* [Definir políticas de laboratório](devtest-lab-set-lab-policy.md).
* [Criar um modelo de laboratório](devtest-lab-create-template.md).
* [Criar artefactos personalizados para as suas VMs](devtest-lab-artifact-author.md).
* [Adicionar uma VM com artefactos para um laboratório](https://azure.microsoft.com/resources/videos/how-to-create-vms-with-artifacts-in-a-devtest-lab/).

