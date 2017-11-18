---
title: "Gerir políticas de laboratório no Azure DevTest Labs | Microsoft Docs"
description: "Saiba como definir políticas de laboratório, tais como tamanhos de VM, VMs máximos por utilizador e a automatização de encerramento."
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 7756aa64-49ca-45a0-9f90-0fd101c7be85
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/03/2017
ms.author: tarcher
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7f42baa1ac37f425f915b6949e018f6e7397d3ae
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2017
---
# <a name="manage-all-policies-for-a-lab-in-azure-devtest-labs"></a>Gerir todas as políticas para um laboratório no Azure DevTest Labs

Azure DevTest Labs permite-lhe controlar os custos e minimizar as perdas no seu laboratórios através da gestão de políticas (definições) para cada laboratório. Este artigo explica em detalhe passo a passo como configurar cada política.  

## <a name="set-allowed-virtual-machine-sizes"></a>Conjunto permitido tamanhos de máquina virtual
A política para definir os tamanhos VM permitidos ajuda a minimizar as perdas de laboratório, permitindo-lhe especificar os tamanhos de VM são permitidos no laboratório. Se esta política é ativada, apenas os tamanhos de VM desta lista podem ser utilizados para criar as VMs.

1. No [portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040), selecione um laboratório e, em seguida, selecione **políticas de configuração e**.

    ![O laboratório configuração e as políticas de acesso](./media/devtest-lab-set-lab-policy/policies-menu.png)

1. No laboratório de **políticas de configuração e** painel, selecione **permitido tamanhos de máquinas virtuais**.
   
    ![Tamanhos de máquinas virtuais permitidos](./media/devtest-lab-set-lab-policy/allowed-vm-sizes.png)

1. Selecione **no** para ativar esta política, e **desativar** desativá-los.

1. Se ativar esta política, selecione um ou mais dos tamanhos da VM que podem ser criados no laboratório.

1. Selecione **Guardar**.

## <a name="set-virtual-machines-per-user"></a>Conjunto de máquinas virtuais por utilizador
A política para **máquinas virtuais por utilizador** permite-lhe especificar o número máximo de VMs que podem ser criadas por um utilizador individual. Se um utilizador tenta criar ou afirmações de uma VM quando o limite de utilizador foram satisfeito, uma mensagem de erro indica que a VM não pode ser criado/reclamado. 

1. No laboratório de **políticas de configuração e** painel, selecione **máquinas virtuais por utilizador**.
   
    ![Máquinas virtuais por utilizador](./media/devtest-lab-set-lab-policy/max-vms-per-user.png)

1. Selecione **Sim** para limitar o número de VMs por utilizador. Se não pretender limitar o número de VMs por utilizador, selecione **não**. Se selecionar **Sim**, introduza um valor numérico que indica o número máximo de VMs que podem ser criados ou reclamado por um utilizador. 

1. Selecione **Sim** para limitar o número de VMs que pode utilizar o SSD (disco de estado sólido). Se não pretender limitar o número de VMs que pode utilizar SSD, selecione **não**. Se selecionar **Sim**, introduza um valor que indica o número máximo de VMs que podem ser criadas utilizando SSD. 

1. Selecione **Guardar**.

## <a name="set-virtual-machines-per-lab"></a>Conjunto de máquinas virtuais por laboratório
A política para **máquinas virtuais por laboratório** permite-lhe especificar o número máximo de VMs que podem ser criadas para o laboratório atual. Se um utilizador tentar criar uma VM quando o limite de laboratório tem sido cumprido, uma mensagem de erro indica que não é possível criar a VM. 

1. No laboratório de **políticas de configuração e** painel, selecione **máquinas virtuais por laboratório**.
   
    ![Máquinas virtuais por laboratório](./media/devtest-lab-set-lab-policy/max-vms-per-lab.png)

1. Selecione **Sim** para limitar o número de VMs por laboratório. Se não pretender limitar o número de VMs por laboratório, selecione **não**. Se selecionar **Sim**, introduza um valor numérico que indica o número máximo de VMs que podem ser criados ou reclamado por um utilizador. 

1. Selecione **Sim** para limitar o número de VMs que pode utilizar o SSD (disco de estado sólido). Se não pretender limitar o número de VMs que pode utilizar SSD, selecione **não**. Se selecionar **Sim**, introduza um valor que indica o número máximo de VMs que podem ser criadas utilizando SSD. 

1. Selecione **Guardar**.

## <a name="set-auto-shutdown"></a>Encerramento do conjunto automático
A política de encerramento automático ajuda a minimizar as perdas de laboratório, permitindo-lhe especificar o momento em que VMs este laboratório encerradas.

1. No laboratório de **políticas de configuração e** painel, selecione **encerramento automático**.
   
    ![Encerramento automático](./media/devtest-lab-set-lab-policy/auto-shutdown.png)

1. Selecione **no** para ativar esta política, e **desativar** desativá-los.

1. Se ativar esta política, especifique a hora (e o fuso horário) para encerrar todas as VMs no laboratório atual.

1. Especifique **Sim** ou **não** para a opção para enviar uma notificação de 15 minutos antes do tempo de encerramento automático especificado. Se optar por **Sim**, introduza um ponto de final do URL do webhook ou um endereço de correio eletrónico especificar onde pretende que a notificação para ser publicado ou enviado. O utilizador recebe uma notificação e é dada a opção para atrasar o encerramento.

   Para obter mais informações sobre webhooks, consulte [criar um webhook ou uma função Azure API](../azure-functions/functions-create-a-web-hook-or-api-function.md). 

1. Selecione **Guardar**.

Por predefinição, uma vez ativada, esta política aplica-se a todas as VMs no laboratório atual. Para remover esta definição de uma VM específica, abra o painel de gestão da VM e altere o **encerramento automático** definição.

## <a name="set-auto-start"></a>Início do conjunto automático
A política de início automático permite-lhe especificar quando as VMs no laboratório atual devem ser iniciadas.  

1. No laboratório de **políticas de configuração e** painel, selecione **início automático**.
   
    ![Início automático](./media/devtest-lab-set-lab-policy/auto-start.png)

2. Selecione **no** para ativar esta política, e **desativar** desativá-los.

3. Se ativar esta política, especifique a hora de início agendada, fuso horário e os dias da semana para os quais se aplica a hora. 

4. Selecione **Guardar**.

Uma vez ativada, esta política não é aplicada automaticamente a quaisquer VMs no laboratório atual. Para aplicar esta definição para uma VM específica, abra o painel de gestão da VM e altere o **início automático** definição.

## <a name="set-expiration-date"></a>Definir a data de expiração
Pode definir uma expiração data quando [criar a VM](devtest-lab-add-vm.md). No **definições avançadas**, selecione o ícone de calendário para especificar uma data em que a VM é eliminada automaticamente. Por predefinição, a VM nunca expira ao fim.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Passos seguintes
Assim que tiver definido e aplicadas várias definições de política VM para o laboratório, seguem-se algumas coisas a experimentar seguinte:

* [Compreender os endereços IP partilhados](devtest-lab-shared-ip.md) -explica como partilhado IP endereços são utilizados no DevTest Labs para minimizar o número de endereços IP públicos necessárias para ligar ao seu laboratório VMs.
* [Configurar a gestão de custo](devtest-lab-configure-cost-management.md) -ilustra como utilizar o **tendência do custo estimado mensalmente** gráfico  
  Para ver o mês atual do estimado data de custos e o custo de fim do mês previsto.
* [Criar a imagem personalizada](devtest-lab-create-template.md) - quando criar uma VM, especifique uma base que pode ser uma imagem personalizada ou uma imagem do Marketplace. Este artigo ilustra como criar uma imagem personalizada de um ficheiro VHD.
* [Configurar imagens do Marketplace](devtest-lab-configure-marketplace-images.md) - Azure DevTest Labs suporta a criação de VMs baseadas nas imagens do Azure Marketplace. Este artigo ilustra como especificar que, se existirem, as imagens do Azure Marketplace podem ser utilizado ao criar as VMs num laboratório.
* [Criar uma VM num laboratório](devtest-lab-add-vm-with-artifacts.md) -ilustra como criar uma VM a partir de uma imagem de base (ou personalizado ou Marketplace) e como trabalhar com artefactos em VM.

