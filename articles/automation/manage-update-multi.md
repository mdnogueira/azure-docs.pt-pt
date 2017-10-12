---
title: "Gerir atualizações para várias máquinas virtuais do Azure | Microsoft Docs"
description: "Carregue máquinas virtuais do Azure para gerir atualizações."
services: operations-management-suite
documentationcenter: 
author: eslesar
manager: carmonm
editor: 
ms.assetid: 
ms.service: operations-management-suite
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/25/2017
ms.author: eslesar
ms.openlocfilehash: 89bf87f27fdf276068cba261fc6ae1660307e0b7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-updates-for-multiple-azure-virtual-machines"></a>Gerir atualizações para várias máquinas virtuais do Azure

A gestão de atualizações permite gerir atualizações e correções para as máquinas virtuais do Azure.
A partir da sua conta de [Automatização do Azure](automation-offering-get-started.md), poderá carregar rapidamente máquinas virtuais, avaliar o estado das atualizações disponíveis, agendar a instalação de atualizações necessárias e rever os resultados da implementação para verificar se as atualizações foram aplicadas com êxito a todas as máquinas virtuais para as quais a Gestão de atualizações está ativada.

## <a name="prerequisites"></a>Pré-requisitos

Para executar os passos descritos neste guia, precisará de:

* Uma conta de Automatização do Azure. Para obter instruções sobre como criar uma conta Run As de Automatização do Azure, veja [Conta Run As do Azure](automation-sec-configure-azure-runas-account.md).
* Uma máquina virtual do Azure Resource Manager (não Clássica). Para obter instruções sobre como criar uma VM, veja [Criar a sua primeira máquina virtual do Windows no portal do Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md)

## <a name="enable-update-management-for-azure-virtual-machines"></a>Ativar a gestão de atualização para máquinas virtuais do Azure

1. No portal do Azure, abra a conta de Automatização.
2. No lado esquerdo do ecrã, selecione **Gestão de atualizações**.
3. Na parte superior do ecrã, clique em **Adicionar VM do Azure**.
    ![Carregar VMs](./media/manage-update-multi/update-onboard-vm.png)
4. Selecione uma máquina virtual para carregar. O ecrã **Ativar Gestão de Atualizações** aparece.
5. Clique em **Ativar**.

   ![Ativar a gestão de atualizações](./media/manage-update-multi/update-enable.png)

A gestão de atualizações é ativada para a máquina virtual.

## <a name="view-update-assessment"></a>Ver avaliação de atualizações

Depois de **Gestão de atualizações** ser ativada, o ecrã **Gestão de atualizações** aparece. Pode ver uma lista de atualizações em falta no separador **Atualizações em falta**.

## <a name="schedule-an-update-deployment"></a>Agendar uma implementação de atualizações

Para instalar atualizações, agende uma implementação que siga o seu agendamento e o período de administração da versão.
Pode escolher quais os tipos de atualização a incluir na implementação. Por exemplo, pode incluir atualizações de segurança ou críticas e excluir update rollups.

Para agendar uma nova Implementação de Atualização para uma ou mais máquinas virtuais, clique em **Agendar a implementação da atualização** na parte superior do ecrã **Gestão de atualizações**. No ecrã **Nova implementação de atualização**, especifique o seguinte:

* **Nome** - Indique um nome exclusivo para identificar a implementação de atualizações.
* **Tipo de SO** - Selecione Windows ou Linux.
* **Computadores a atualizar** - Selecione as máquinas virtuais que pretende atualizar.

  ![Selecionar as máquinas virtuais a atualizar](./media/manage-update-multi/update-select-computers.png)

* **Classificação da atualização** - Selecione os tipos de software que a implementação da atualização irá incluir na implementação. Os tipos de classificação são:
  * Atualizações críticas
  * Atualizações de segurança
  * Update rollups
  * Pacotes de funcionalidades
  * Service packs
  * Atualizações de definições
  * Ferramentas
  * Atualizações
* **Definições da agenda** - Pode aceitar a data e hora predefinidas, que é 30 minutos após a hora atual, ou especificar uma hora diferente.
   Também pode especificar se a implementação ocorre uma vez ou configurar um agendamento periódico. Clique na opção Periódico, em Periodicidade, para configurar um agendamento periódico.

   ![Ecrã de Definições de Agendamento de Atualizações](./media/manage-update-multi/update-set-schedule.png)

* **Janela de manutenção (minutos)** - Especifique o período de tempo no qual pretende que a implementação da atualização ocorra.  Isto ajuda a garantir que as alterações são realizadas nos seus períodos de administração definidos.

Depois de concluir a configuração do agendamento, clique no botão **Criar** e regressará ao dashboard de estado.
Tenha em atenção que a tabela **Agendada** mostra o agendamento da implementação que acabou de criar.

> [!WARNING]
> Para as atualizações que requerem um reinício, a máquina virtual será reiniciada automaticamente.

## <a name="view-results-of-an-update-deployment"></a>Ver resultados de uma implementação de atualização

Após o início da implementação agendada, pode ver o estado dessa implementação no separador **Implementações de atualizações** no ecrã **Gestão de atualizações**.
Se estiver em execução, o respetivo estado é apresentado como **Em curso**. Depois de concluir, se for bem sucedida, muda para **Com êxito**.
Se existir uma falha numa ou mais atualizações na implementação, o estado é **Falha parcial**.

![Estado de Implementação da Atualização ](./media/manage-update-multi/update-view-results.png)

Clique na implementação da atualização concluída para ver o dashboard relativo a essa implementação de atualização.

No mosaico **Resultados da atualização** encontra-se um resumo do número total de atualizações e os resultados de implementação da máquina virtual.
Na tabela à direita encontra-se uma divisão detalhada de cada atualização e os resultados da instalação, que podem ter um dos seguintes valores:

* Não tentado - a atualização não foi instalada porque não havia tempo suficiente disponível com base na duração da janela de manutenção definida.
* Com êxito - a atualização foi executada com êxito
* Falhou - a atualização falhou

Clique em **Todos os registos** para ver todas as entradas de registo que a implementação criou.

Clique no mosaico **Saída** para ver o fluxo de tarefas do runbook responsável pela gestão da implementação da atualização na máquina virtual de destino.

Clique em **Erros** para ver informações detalhadas sobre os erros da implementação.

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre a Gestão de atualizações, veja [Gestão de Atualizações](../operations-management-suite/oms-solution-update-management.md).