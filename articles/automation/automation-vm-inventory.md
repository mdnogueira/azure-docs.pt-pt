---
title: "Gerir a VM do Azure com a recolha de inventário | Microsoft Docs"
description: "Gerir a VM com a recolha de inventário"
services: automation
keywords: "inventário, automatização,alteração, controlo"
author: jennyhunter-msft
ms.author: jehunte
ms.date: 09/13/2017
ms.topic: hero-article
manager: carmonm
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 5291d112c2cdf157543fe301d5a5c80f3fe561ae
ms.contentlocale: pt-pt
ms.lasthandoff: 09/25/2017

---

# <a name="manage-an-azure-virtual-machine-with-inventory-collection"></a>Gerir uma máquina virtual do Azure com a recolha de inventário

O controlo de inventário pode ser ativado para uma máquina virtual do Azure a partir da página de recursos da máquina. Este método fornece uma interface de utilizador baseada no browser para definir e configurar a recolha de inventário.

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.
Se não tiver uma máquina virtual do Azure, crie uma [máquina virtual](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/quick-create-portal) antes de começar.

## <a name="log-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [Portal do Azure](https://portal.azure.com/).

## <a name="enable-inventory-collection-from-the-virtual-machine-resource-page"></a>Ativar a recolha de inventário a partir da página de recursos da máquina virtual

1. No ecrã à esquerda, selecione **Máquinas virtuais**.
1. Na lista, selecione uma máquina virtual.
1. Selecione **Inventário (Pré-visualização)** no menu de recursos abaixo de **Operações**.
1. É apresentada uma faixa na parte superior da página, notificando-o de que a solução não está ativada. Clique na faixa para ativar a solução.
1. Selecione uma Área de Trabalho do Log Analytics para armazenar os registos de dados. Se não existir uma áreas de trabalho disponível para essa região, é-lhe pedido para criar uma área de trabalho predefinida e uma conta de Automatização. Clique em **Ativar** para iniciar a inclusão do seu computador.

   ![Ver opções de inclusão](./media/automation-vm-inventory/inventory-onboarding-options.png)  

1. Uma barra de estado notifica-o de que a solução está a ser ativada. Este processo pode demorar até 15 minutos a concluir. Durante este tempo, pode fechar o painel ou mantê-lo aberto e será informado quando a solução estiver ativada. Pode monitorizar o estado da implementação a partir do painel de notificações.

   ![Ver a solução de inventário imediatamente após a inclusão](./media/automation-vm-inventory/inventory-onboarded.png)

1. Quando a implementação estiver concluída, a barra de estado irá desaparecer. Neste momento, o sistema ainda está a recolher dados de inventário e os dados podem não estar visíveis. Um conjunto completo de dados pode demorar 24 horas.

## <a name="configure-your-inventory-settings"></a>Configurar as definições de inventário

Por predefinição, o Software, os Serviços do Windows e os Daemons Linux estão configurados para a recolha. Para recolher o inventário dos Ficheiros e Registo do Windows, configure as definições de recolha de inventário.

1. Na vista **Inventário (Pré-visualização)**, selecione o botão **Editar Definições** na parte superior da página.
2. Para adicionar uma nova definição de recolha, navegue para a categoria de definição que pretende adicionar utilizando os separadores **Registo do Windows**, **Ficheiros do Windows** e **Ficheiros do Linux**. Clique em **Adicionar** no início da página.
3. Para ver os detalhes e as descrições de cada propriedade de definição, visite a [Página de documentação do inventário](https://aka.ms/configinventorydocs).

## <a name="disconnecting-your-virtual-machine-from-management"></a>Desligar a máquina virtual da gestão

Para remover a máquina da gestão de inventário:

1. No menu esquerdo no portal do Azure, clique em **Log Analytics** e, em seguida, clique para selecionar a área de trabalho que utilizou quando efetuou a inclusão da máquina virtual.
1. Na página Log Analytics, selecione **Máquinas virtuais** na categoria **Origens de dados da área de trabalho** no menu de recursos. 
1. Selecione a máquina virtual que pretende desligar da lista. Apresenta uma marca de verificação verde junto ao texto que indica "Esta área de trabalho" na coluna **Ligação OMS**. Clique em **Desligar** na parte superior da página seguinte e em **Sim** na caixa de diálogo de confirmação para desligar a máquina da gestão.

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre a gestão de alterações nas definições de ficheiros e do registo nas suas máquinas virtuais, consulte [Controlo de Alterações](../log-analytics/log-analytics-change-tracking.md).
* Para saber mais sobre a gestão do Windows e atualizações de pacote em máquinas virtuais, consulte [Gestão de Atualizações](../operations-management-suite/oms-solution-update-management.md)

