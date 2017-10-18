---
title: "Gerir uma máquina virtual do Azure com a recolha de inventário | Microsoft Docs"
description: "Gerir uma máquina virtual com a recolha de inventário"
services: automation
keywords: "inventário, automatização,alteração, controlo"
author: jennyhunter-msft
ms.author: jehunte
ms.date: 09/13/2017
ms.topic: hero-article
manager: carmonm
ms.openlocfilehash: bfdd67ac1018f242e415da1a66d93f22bea8d054
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-an-azure-virtual-machine-with-inventory-collection"></a>Gerir uma máquina virtual do Azure com a recolha de inventário

Pode ativar o controlo de inventário para uma máquina virtual do Azure a partir da página de recursos da máquina virtual. Este método fornece uma interface de utilizador baseada no browser para definir e configurar a recolha de inventário.

## <a name="before-you-begin"></a>Antes de começar
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/).
Se não tiver uma máquina virtual do Azure, [crie uma máquina virtual](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/quick-create-portal).

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure
Inicie sessão no [Portal do Azure](https://portal.azure.com/).

## <a name="enable-inventory-collection-from-the-virtual-machine-resource-page"></a>Ativar a recolha de inventário a partir da página de recursos da máquina virtual

1. No painel esquerdo do portal do Azure, selecione **Máquinas virtuais**.
2. Na lista de máquinas virtuais, selecione uma máquina virtual.
3. No menu **Recurso**, em **Operações**, selecione **Inventário (Pré-visualização)**.  
    É apresentada uma faixa na parte superior da janela, notificando-o de que a solução não está ativada. 
4. Para ativar a solução, selecione a faixa.
5. Selecione uma área de trabalho do Log Analytics para armazenar os registos de dados.  
    Se não existir uma área de trabalho disponível para essa região, é-lhe pedido para criar uma área de trabalho predefinida e uma conta de automatização. 
6. Para iniciar a inclusão do seu computador, selecione **Ativar**.

   ![Ver opções de inclusão](./media/automation-vm-inventory/inventory-onboarding-options.png)  
    Uma barra de estado notifica-o de que a solução está a ser ativada. Este processo pode demorar até 15 minutos a concluir. Durante este tempo, pode fechar a janela ou mantê-la aberto e será notificado quando a solução estiver ativada. Pode monitorizar o estado da implementação a partir do painel de notificações.

   ![Ver a solução de inventário imediatamente após a inclusão](./media/automation-vm-inventory/inventory-onboarded.png)

Quando a implementação estiver concluída, a barra de estado desaparece. O sistema ainda está a recolher dados de inventário e os dados podem não estar visíveis. Um conjunto completo de dados pode demorar 24 horas.

## <a name="configure-your-inventory-settings"></a>Configurar as definições de inventário

Por predefinição, o software, os serviços do Windows e os daemons Linux estão configurados para a recolha. Para recolher o registo do Windows e o inventário de ficheiros, configure as definições de recolha do inventário.

1. Na vista **Inventário (Pré-visualização)**, selecione o botão **Editar Definições** na parte superior da janela.
2. Para adicionar uma nova definição de recolha, navegue para a categoria de definição que quer adicionar através dos separadores **Registo do Windows**, **Ficheiros do Windows** e **Ficheiros do Linux**. 
3. Selecione **Adicionar** na parte superior da janela.
4. Para ver os detalhes e as descrições de cada propriedade de definição, visite a [Página de documentação do inventário](https://aka.ms/configinventorydocs).

## <a name="disconnect-your-virtual-machine-from-management"></a>Desligar a máquina virtual da gestão

Para remover a máquina virtual da gestão de inventário:

1. No painel esquerdo do portal do Azure, selecione **Log Analytics** e, em seguida, selecione a área de trabalho que utilizou quando efetuou a inclusão da máquina virtual.
2. Na janela **Log Analytics**, no menu **Recurso**, na categoria **Origens de Dados da Área de Trabalho**, selecione **Máquinas virtuais**. 
3. Na lista, selecione a máquina virtual que quer desligar. A máquina virtual tem uma marca de verificação verde junto a **Esta área de trabalho** na coluna **Ligação OMS**. 
4. Na parte superior da página seguinte, selecione **Desligar**.
5. Na janela de confirmação, selecione **Sim**.  
    Esta ação desliga a máquina virtual da gestão.

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre a gestão de alterações nas definições de ficheiros e do registo nas suas máquinas virtuais, veja [Controlar as alterações de software com a solução Controlo de Alterações](../log-analytics/log-analytics-change-tracking.md).
* Para saber mais sobre a gestão do Windows e atualizações de pacote em máquinas virtuais, veja [Solução de Gestão de Atualizações no OMS](../operations-management-suite/oms-solution-update-management.md).
