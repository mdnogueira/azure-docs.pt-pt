---
title: "Gerir atualizações para várias máquinas virtuais do Azure | Microsoft Docs"
description: "Este tópico descreve como gerir atualizações para as máquinas virtuais do Azure."
services: automation
documentationcenter: 
author: eslesar
manager: carmonm
editor: 
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/31/2017
ms.author: magoedte;eslesar
ms.openlocfilehash: bb9c19bb489873d1a2175f4a85f7654a3bf099b8
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2017
---
# <a name="manage-updates-for-multiple-machines"></a>Gerir atualizações de várias máquinas

Pode utilizar a gestão de atualizações para gerir atualizações e correções para as suas máquinas virtuais do Windows e do Linux. Na sua conta da [Automatização do Azure](automation-offering-get-started.md), pode:

- Adicionar máquinas virtuais.
- Avaliar o estado das atualizações disponíveis.
- Agendar a instalação das atualizações necessárias.
- Reveja os resultados da implementação para verificar se as atualizações foram aplicadas com êxito em todas as máquinas virtuais que têm a gestão de atualizações ativada.

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar a gestão de atualizações, precisa de:

* Uma conta Run As de Automatização. Para obter instruções sobre como criá-la, veja [Introdução à Automatização do Azure](automation-offering-get-started.md).

* Uma máquina virtual ou um computador que tenha instalado um dos sistemas operativos suportados.

## <a name="supported-operating-systems"></a>Sistemas operativos suportados

A gestão de atualizações é suportada nos sistemas operativos seguintes.

### <a name="windows"></a>Windows

* Windows Server 2008 e versões posteriores e implementações de atualizações no Windows Server 2008 R2 SP1 e versões posteriores. Não são suportadas opções de instalação Server Core e Nano Server.

  O suporte para implementar atualizações no Windows Server 2008 R2 SP1 requer o .NET Framework 4.5 e o Windows Management Framework 5.0 ou posterior.

* Não são suportados os sistemas operativos cliente.

Os agentes do Windows têm de ser configurados para comunicar com um Windows Server Update Services (WSUS) ou de ter acesso ao Microsoft Update.

> [!NOTE]
> O System Center Configuration Manager não consegue gerir o agente do Windows ao mesmo tempo.
>

### <a name="linux"></a>Linux

* CentOS 6 (x86/x64) e 7 (x64)  
* Red Hat Enterprise 6 (x86/x64) e 7 (x64)  
* SUSE Linux Enterprise Server 11 (x86/x64) e 12 (x64)  
* Ubuntu 12.04 LTS e mais recente (x86/x64)   

> [!NOTE]  
> Para evitar que as atualizações sejam aplicadas fora de uma janela de manutenção no Ubuntu, reconfigure o pacote Unattended-Upgrade para desativar as atualizações automáticas. Para obter informações, veja [Automatic Updates topic in the Ubuntu Server Guide](https://help.ubuntu.com/lts/serverguide/automatic-updates.html) (Tópico de Atualizações Automáticas no Guia do Ubuntu Server).

Os agentes do Linux têm de ter acesso a um repositório de atualização.

Esta solução não suporta agentes do OMS para Linux configurados para reportar a várias áreas de trabalho do Operations Management Suite.

## <a name="enable-update-management-for-azure-virtual-machines"></a>Ativar a gestão de atualização para máquinas virtuais do Azure

1. No portal do Azure, abra a conta de Automatização.
2. No painel d lado esquerdo, selecione **Gerir rede**.
3. Na parte superior da janela, selecione **Adicionar VM do Azure**.
   ![Separador Adicionar VM do Azure](./media/manage-update-multi/update-onboard-vm.png)
4. Selecione uma máquina virtual para carregar. É aberta a caixa de diálogo **Ativar Gestão de Atualizações**.
5. Selecione **Ativar**.

   ![Caixa de diálogo Ativar Gestão de Atualizações](./media/manage-update-multi/update-enable.png)

A gestão de atualizações é ativada para a máquina virtual.

## <a name="enable-update-management-for-non-azure-virtual-machines-and-computers"></a>Ativar a gestão de atualização para máquinas virtuais e computadores não Azure

Para obter instruções sobre como ativar a gestão de atualizações para máquinas virtuais e computadores não Azure, veja [Connect Windows computers to the Log Analytics service in Azure (Ligar computadores Windows ao serviço do Log Analytics no Azure)](../log-analytics/log-analytics-windows-agents.md).

Para obter instruções sobre como ativar a gestão de atualizações para máquinas virtuais e computadores Linux não Azure, veja [Connect your Linux computers to Log Analytics](../log-analytics/log-analytics-agent-linux.md) (Ligar os seus computadores Linux ao Log Analytics).

## <a name="view-an-update-assessment"></a>Ver avaliações de atualizações

Depois de ativada a gestão de atualizações, é apresentada a caixa de diálogo **Atualizar gestão**. Pode ver uma lista de atualizações em falta no separador **Atualizações em falta**.

## <a name="collect-data"></a>Recolher dados

Os agentes instalados nas máquinas virtuais e computadores recolhem dados sobre as atualizações e enviam-nos para a gestão de atualizações do Azure.

### <a name="supported-agents"></a>Agentes suportados

A tabela seguinte descreve as origens ligadas que são suportadas por esta solução:

| Origem ligada | Suportado | Descrição |
| --- | --- | --- |
| Agentes do Windows |Sim |A gestão de atualizações recolhe informações sobre as atualizações do sistema de agentes do Windows e inicia a instalação das atualizações necessárias. |
| Agentes do Linux |Sim |A gestão de atualizações recolhe informações sobre as atualizações do sistema a partir dos agentes do Linux e inicia a instalação das atualizações obrigatórias em distribuições suportadas. |
| Grupo de gestão do Operations Manager |Sim |A gestão de atualizações recolhe informações sobre atualizações do sistema de agentes num grupo de gestão ligado. |
| Conta de armazenamento do Azure |Não |O armazenamento do Azure não inclui informações sobre atualizações do sistema. |

### <a name="collection-frequency"></a>Frequência da recolha

Em cada computador Windows gerido, é executada uma análise duas vezes por dia. A cada 15 minutos, a API do Windows é chamada para consultar a hora da última atualização, para determinar se o estado foi alterado. Se assim for, é iniciada uma análise de compatibilidade. Em cada computador Linux gerido, é executada uma análise de três em três horas.

O dashboard pode demorar entre 30 minutos a seis horas a apresentar os dados atualizados a partir dos computadores geridos.

## <a name="schedule-an-update-deployment"></a>Agendar uma implementação de atualizações

Para instalar atualizações, agende uma implementação que siga o seu agendamento e o período de administração da versão.
Pode escolher quais os tipos de atualização a incluir na implementação. Por exemplo, pode incluir atualizações de segurança ou críticas e excluir update rollups.

Para agendar uma nova implementação de atualização numa ou mais máquinas virtuais, selecione **Agendar a implementação da atualização**, na parte superior da caixa de diálogo **Gestão de atualizações**. No painel **Nova implementação de atualização**, especifique o seguinte:

* **Nome**: indique um nome exclusivo para identificar a implementação de atualizações.
* **Tipo de SO**: selecione Windows ou Linux.
* **Computadores a atualizar**: selecione as máquinas virtuais que pretende atualizar.

  ![Painel “Nova implementação de atualizações”](./media/manage-update-multi/update-select-computers.png)

* **Classificação da atualização**: selecione os tipos de software que a implementação de atualizações vai incluir. Os tipos de classificação são:
  * Atualizações críticas
  * Atualizações de segurança
  * Update rollups
  * Pacotes de funcionalidades
  * Service packs
  * Atualizações de definições
  * Ferramentas
  * Atualizações
* **Definições da agenda**: pode aceitar a data e hora predefinidas, que é 30 minutos após a hora atual. Em alternativa, pode especificar uma hora diferente.
   Também pode especificar se a implementação ocorre uma vez ou de acordo com um agendamento periódico. Para configurar um agendamento periódico, selecione a opção **Periódico**, em **Periodicidade**.

   ![Caixa de diálogo Definições de Agendamento](./media/manage-update-multi/update-set-schedule.png)

* **Janela de manutenção (minutos)**. especifique o período de tempo no qual pretende que a implementação da atualização ocorra. Esta definição ajuda a garantir que as alterações são realizadas nos seus períodos de administração definidos.

Depois de concluir a configuração do agendamento, selecione o botão **Criar** para regressar ao dashboard de estado. A tabela **Agendada** mostra o agendamento da implementação que acabou de criar.

> [!WARNING]
> Para as atualizações que requerem reinício, a máquina virtual será reiniciada automaticamente.

## <a name="view-results-of-an-update-deployment"></a>Ver resultados de uma implementação de atualização

Após o início da implementação agendada, pode ver o estado dessa implementação no separador **Implementações de atualizações**, na caixa de diálogo **Gestão de atualizações**.
Se a implementação estiver em execução, o estado será **Em curso**. Após a conclusão bem-sucedida da implementação, muda para **Bem-sucedida**.
Se uma ou mais atualizações falharem na implementação, o estado é **Falha parcial**.

![Estado da implementação de atualizações](./media/manage-update-multi/update-view-results.png)

Para ver o dashboard relativo a uma implementação de atualizações, selecione a implementação concluída.

O painel **Resultados da atualização** mostra o número total de atualizações e os resultados da implementação da máquina virtual.
A tabela à direita mostra uma divisão detalhada de cada atualização e os resultados da instalação. Os resultados da instalação podem ser um dos seguintes valores:

* Não tentada: a atualização não foi instalada porque não havia tempo suficiente disponível com base na duração da janela de manutenção definida.
* Bem-sucedida: a atualização foi executada com êxito.
* Falhou: a atualização falhou.

Para ver todas as entradas de registo que a implementação criou, selecione **Todos os registos**.

Para ver o fluxo de trabalhos do runbook responsável pela gestão da implementação de atualizações na máquina virtual de destino, selecione o mosaico **Saída**.

Para ver informações detalhadas sobre os erros da implementação, selecione **Erros**.

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre a gestão de atualizações, incluindo registos, saídas e erros, veja [Solução de Gestão de Atualizações no OMS](../operations-management-suite/oms-solution-update-management.md).

