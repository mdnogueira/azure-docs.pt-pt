---
title: "Gerir políticas de laboratório básico no Azure DevTest Labs | Microsoft Docs"
description: "Saiba como definir algumas das políticas básicas (definições) para um laboratório no DevTest Labs"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: tarcher
ms.openlocfilehash: ed35d081b191ec41ed9e5970515057a4715c0d59
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-basic-policies-for-a-lab-in-azure-devtest-labs"></a>Gerir políticas básicas para um laboratório no Azure DevTest Labs

Azure DevTest Labs permite-lhe controlar os custos e minimizar as perdas no seu laboratórios através da gestão de políticas (definições) para cada laboratório. Neste artigo, começar com as políticas ao aprender a definir duas das políticas mais críticas - limitar o número de máquinas virtuais (VM) que pode ser criado ou reclamado por um único utilizador e a configuração de encerramento automático. Para ver como definir todas as políticas de laboratório, consulte o artigo [definir políticas de laboratório no Azure DevTest Labs](devtest-lab-set-lab-policy.md).  

## <a name="accessing-a-labs-policies-in-azure-devtest-labs"></a>Aceder a políticas de um laboratório no Azure DevTest Labs
Os seguintes passos guiá-lo através da configuração de políticas para um laboratório no Azure DevTest Labs:

Para ver (e alterar) as políticas para um laboratório, siga estes passos:

1. Inicie sessão no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selecione **Mais serviços**, e, em seguida, selecione **DevTest Labs** na lista.

1. Na lista de laboratórios, selecione o laboratório pretendido.   

1. Selecione **políticas de configuração e**.

    ![Painel de definições de política](./media/devtest-lab-set-lab-policy/policies-menu.png)

1. O **políticas de configuração e** painel contém um menu de definições que pode especificar. Este artigo abrange apenas as definições para **máquinas virtuais por utilizador** e **encerramento automático**. Para saber mais sobre as restantes definições, consulte [gerir todas as políticas para um laboratório no Azure DevTest Labs](./devtest-lab-set-lab-policy.md). 
   
## <a name="set-virtual-machines-per-user"></a>Conjunto de máquinas virtuais por utilizador
A política para **máquinas virtuais por utilizador** permite-lhe especificar o número máximo de VMs que podem ser criadas por um utilizador individual. Se um utilizador tenta criar ou afirmações de uma VM quando o limite de utilizador foram satisfeito, uma mensagem de erro indica que a VM não pode ser criado/reclamado. 

1. No laboratório de **políticas de configuração e** menu, selecione **máquinas virtuais por utilizador**.
   
    ![Máquinas virtuais por utilizador](./media/devtest-lab-set-lab-policy/max-vms-per-user.png)

1. Selecione **Sim** para limitar o número de VMs por utilizador. Se não pretender limitar o número de VMs por utilizador, selecione **não**. Se selecionar **Sim**, introduza um valor numérico que indica o número máximo de VMs que podem ser criados ou reclamado por um utilizador. 

1. Selecione **Sim** para limitar o número de VMs que pode utilizar o SSD (disco de estado sólido). Se não pretender limitar o número de VMs que pode utilizar SSD, selecione **não**. Se selecionar **Sim**, introduza um valor que indica o número máximo de VMs que podem ser criadas utilizando SSD. 

1. Selecione **Guardar**.

## <a name="set-auto-shutdown"></a>Encerramento do conjunto automático
A política de encerramento automático ajuda a minimizar as perdas de laboratório, permitindo-lhe especificar o tempo que VMs este laboratório são encerradas.

1. No laboratório de **políticas de configuração e** painel, selecione **encerramento automático**.
   
    ![Encerramento automático](./media/devtest-lab-set-lab-policy/auto-shutdown.png)

1. Selecione **no** para ativar esta política, e **desativar** desativá-los.

1. Se ativar esta política, especifique a hora (e o fuso horário) para encerrar todas as VMs no laboratório atual.

1. Especifique **Sim** ou **não** para a opção para enviar uma notificação de 15 minutos antes do tempo de encerramento automático especificado. Se especificar **Sim**, introduza um ponto de final do URL do webhook para receber a notificação. Para obter mais informações sobre webhooks, consulte [criar um webhook ou uma função Azure API](../azure-functions/functions-create-a-web-hook-or-api-function.md). 

1. Selecione **Guardar**.

    Por predefinição, uma vez ativada, esta política aplica-se a todas as VMs no laboratório atual. Para remover esta definição de uma VM específica, abra o painel da VM e altere o **encerramento automático** definição 

## <a name="set-auto-start"></a>Início do conjunto automático
A política de início automático permite-lhe especificar quando as VMs no laboratório atual devem ser iniciadas.  

1. No laboratório de **políticas de configuração e** painel, selecione **início automático**.
   
    ![Início automático](./media/devtest-lab-set-lab-policy/auto-start.png)

2. Selecione **no** para ativar esta política, e **desativar** desativá-los.

3. Se ativar esta política, especifique a hora de início agendada, fuso horário e os dias da semana para os quais se aplica a hora. 

4. Selecione **Guardar**.

    Uma vez ativada, esta política não é aplicada automaticamente a quaisquer VMs no laboratório atual. Para aplicar esta definição para uma VM específica, abra o painel da VM e altere o **início automático** definição 

## <a name="next-steps"></a>Passos seguintes

- [Definir políticas de laboratório no Azure DevTest Labs](devtest-lab-set-lab-policy.md) -aprender a modificar outras políticas de laboratório 
