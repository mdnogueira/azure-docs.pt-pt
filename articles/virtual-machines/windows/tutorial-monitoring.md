---
title: "Monitorização do Azure e a atualização e máquinas virtuais do Windows | Microsoft Docs"
description: "Tutorial - monitorizar e atualizar uma Máquina Virtual do Windows com o Azure PowerShell"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/04/2017
ms.author: davidmu
ms.custom: mvc
ms.openlocfilehash: a37aed8b3321d3518ffd73e09f5bb21266a7e577
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-and-update-a-windows-virtual-machine-with-azure-powershell"></a>Monitorizar e atualizar a Máquina Virtual do Windows com o Azure PowerShell

Monitorização do Azure utiliza o agentes para recolher dados de arranque e de desempenho de VMs do Azure, armazenar estes dados no armazenamento do Azure e tornam acessível através do portal, o módulo Azure PowerShell e a CLI do Azure. Gestão de atualizações permite-lhe gerir as atualizações e correções de erros para as suas VMs do Windows Azure.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Ativar o diagnóstico de arranque numa VM
> * Ver diagnósticos de arranque
> * Veja as métricas anfitrião VM
> * Instalar a extensão de diagnóstico
> * Veja as métricas VM
> * Criar um alerta
> * Gerir atualizações do Windows
> * Configurar monitorização avançada

Este tutorial requer a versão do módulo 3.6 ou posterior do Azure PowerShell. Executar ` Get-Module -ListAvailable AzureRM` para localizar a versão. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-azurerm-ps).

Para concluir o exemplo neste tutorial, tem de ter uma máquina virtual existente. Se for necessário, isto [script de exemplo](../scripts/virtual-machines-windows-powershell-sample-create-vm.md) pode criar uma por si. Ao trabalhar através do tutorial, substitua o grupo de recursos, o nome da VM e a localização em que o necessário.

## <a name="view-boot-diagnostics"></a>Ver diagnósticos de arranque

Como máquinas virtuais do Windows efetuar o arranque, o agente de diagnóstico de arranque captura a saída de ecrã que pode ser utilizada para fins de resolução de problemas. Esta capacidade está ativada por predefinição. As capturas de ecrã capturadas estão armazenadas numa conta de armazenamento do Azure, que também é criada por predefinição. 

Pode obter os dados de diagnóstico de arranque com o [Get-AzureRmVMBootDiagnosticsData](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmbootdiagnosticsdata) comando. No exemplo seguinte, diagnóstico de arranque é transferido para a raiz do * c:\* unidade. 

```powershell
Get-AzureRmVMBootDiagnosticsData -ResourceGroupName myResourceGroup -Name myVM -Windows -LocalPath "c:\"
```

## <a name="view-host-metrics"></a>Veja as métricas de anfitrião

Uma VM do Windows tem uma VM de anfitrião dedicada no Azure que interage com. As métricas são automaticamente recolhidas para o anfitrião e podem ser visualizadas no portal do Azure.

1. No portal do Azure, clique em **grupos de recursos**, selecione **myResourceGroup**e, em seguida, selecione **myVM** na lista de recursos.
2. Clique em **métricas** no painel de VM e, em seguida, selecione qualquer uma das métricas de anfitrião em **as métricas disponíveis** para ver como a VM do anfitrião está a efetuar.

    ![Veja as métricas de anfitrião](./media/tutorial-monitoring/tutorial-monitor-host-metrics.png)

## <a name="install-diagnostics-extension"></a>Instalar a extensão de diagnóstico

As métricas de anfitrião básica estão disponíveis, mas mais granular e métricas específicas de VM, que tem de instalar a extensão de diagnóstico do Azure na VM. A extensão de diagnóstico do Azure permite a monitorização adicional e os dados de diagnóstico a obtenção da VM. Pode ver estas métricas de desempenho e criar alertas com base nos como efetua a VM. A extensão de diagnóstico é instalada através do portal do Azure da seguinte forma:

1. No portal do Azure, clique em **grupos de recursos**, selecione **myResourceGroup**e, em seguida, selecione **myVM** na lista de recursos.
2. Clique em **definições de diagnóstico**. A lista mostra que *diagnóstico de arranque* já estão ativadas da secção anterior. Clique na caixa de verificação para *métricas básicas*.
3. Clique em de **ativar a monitorização de ao nível do convidado** botão.

    ![Veja as métricas diagnóstico](./media/tutorial-monitoring/enable-diagnostics-extension.png)

## <a name="view-vm-metrics"></a>Veja as métricas VM

Pode ver as métricas VM da mesma forma que visualizadas anfitrião métricas VM:

1. No portal do Azure, clique em **grupos de recursos**, selecione **myResourceGroup**e, em seguida, selecione **myVM** na lista de recursos.
2. Para ver como a VM está a efetuar, clique em **métricas** no painel de VM e, em seguida, selecione qualquer uma das métricas de diagnóstico em **as métricas disponíveis**.

    ![Veja as métricas VM](./media/tutorial-monitoring/monitor-vm-metrics.png)

## <a name="create-alerts"></a>Criar alertas

Pode criar alertas com base nas métricas de desempenho específicos. Alertas podem ser utilizados para notificar que quando a utilização média da CPU excede um determinado limiar ou disponível espaço livre em disco descerem abaixo de uma determinada quantidade, por exemplo. Alertas são apresentados no portal do Azure ou podem ser enviados por e-mail. Também pode acionar runbooks de automatização do Azure ou do Azure Logic Apps em resposta a alertas a serem gerados.

O exemplo seguinte cria um alerta para utilização média da CPU.

1. No portal do Azure, clique em **grupos de recursos**, selecione **myResourceGroup**e, em seguida, selecione **myVM** na lista de recursos.
2. Clique em **regras de alerta** no painel de VM, em seguida, clique em **Adicionar alerta métrica** na parte superior do painel de alertas.
4. Forneça um **nome** para o alerta, tais como *myAlertRule*
5. Para acionar um alerta quando a percentagem de CPU excede 1.0 para cinco minutos, deixe todas as outras predefinições selecionadas.
6. Opcionalmente, selecione a caixa para *proprietários, contribuintes e leitores de E-Mail* para enviar a notificação por correio eletrónico. A ação predefinida é apresentar uma notificação no portal.
7. Clique no botão **OK**.

## <a name="manage-windows-updates"></a>Gerir atualizações do Windows

Gestão de atualizações permite-lhe gerir as atualizações e correções de erros para as suas VMs do Windows Azure.
Diretamente da sua VM, pode rapidamente avaliar o estado das atualizações disponíveis, agendar a instalação de atualizações necessárias e reveja os resultados de implementação para verificar as atualizações foram aplicadas com êxito para a VM.

Para obter informações sobre preços, consulte [automatização preços para gestão de atualizações](https://azure.microsoft.com/pricing/details/automation/)

### <a name="enable-update-management"></a>Ativar a gestão de atualização

Ative a gestão de atualização para a VM:
 
1. No lado esquerdo do ecrã, selecione **máquinas virtuais**.
2. Na lista, selecione uma VM.
3. No ecrã da VM, no **operações** secção, clique em **gestão de atualizações**. O **ativar a gestão de atualização** ecrã é aberto.

A validação é efetuada para determinar se a gestão de atualizações está ativada para esta VM. A validação inclui verifica a existência de uma área de trabalho de análise de registos e a conta de automatização ligada, e se a solução é na área de trabalho.

Uma área de trabalho de análise de registos é utilizada para recolher dados gerados pelas funcionalidades e serviços, tais como a gestão de atualizações. A área de trabalho fornece uma localização única para rever e analisar dados de várias origens. Para executar a ação adicional em VMs que necessitam de atualizações, o automatização do Azure permite-lhe executar scripts em VMs, tal como transferir e aplicar atualizações.

O processo de validação também verifica se a VM está aprovisionada com o Microsoft Monitoring Agent (MMA) e de trabalho híbrida. Este agente é utilizado para comunicar com a VM e obter informações sobre o estado de atualização. 

Se estes pré-requisitos não são cumpridos, é apresentada uma faixa que dá-lhe a opção para ativar a solução.

![Faixa de carregar configuração de gestão de atualização](./media/tutorial-monitoring/manageupdates-onboard-solution-banner.png)

Clique na faixa para ativar a solução. Se qualquer um dos seguintes pré-requisitos foram identificadas como estando em falta após a validação, estes serão automaticamente adicionados:

* [Análise de registo](../../log-analytics/log-analytics-overview.md) área de trabalho
* [Automatização](../../automation/automation-offering-get-started.md)
* A [runbook worker híbrido](../../automation/automation-hybrid-runbook-worker.md) está ativado na VM

O **ativar a gestão de atualização** ecrã é aberto. Configure as definições e clique em **ativar**.

![Ativar a solução de gestão de atualizações](./media/tutorial-monitoring/manageupdates-update-enable.png)

Ativar a solução pode demorar até 15 minutos e, durante este tempo, não deve fechar a janela do browser. Depois da solução estiver ativada, informações sobre atualizações na VM em falta fluem à análise de registos.
Pode demorar entre 30 minutos e 6 horas para os dados disponíveis para análise.

### <a name="view-update-assessment"></a>Ver avaliação de atualizações

Depois de **Gestão de atualizações** ser ativada, o ecrã **Gestão de atualizações** aparece. Pode ver uma lista de atualizações em falta no separador **Atualizações em falta**.

 ![Ver o estado de atualização](./media/tutorial-monitoring/manageupdates-view-status-win.png)

### <a name="schedule-an-update-deployment"></a>Agendar uma implementação de atualizações

Para instalar atualizações, agende uma implementação que siga o seu agendamento e o período de administração da versão.
Pode escolher quais os tipos de atualização a incluir na implementação. Por exemplo, pode incluir críticos ou atualizações de segurança e de exclusão update rollups.

Agendar uma nova implementação de atualização para a VM clicando **implementação de atualização de agendamento** na parte superior do **gestão de atualizações** ecrã. No **novo atualizar implementação** ecrã, especifique as seguintes informações:

* **Nome** - Indique um nome exclusivo para identificar a implementação de atualizações.
* **Classificação da atualização** -selecione os tipos de software incluída na implementação da implementação da atualização. Os tipos de classificação são:
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

  ![Ecrã de Definições de Agendamento de Atualizações](./media/tutorial-monitoring/manageupdates-schedule-win.png)

* **Janela de manutenção (minutos)** - Especifique o período de tempo no qual pretende que a implementação da atualização ocorra.  Isto ajuda a garantir que as alterações são realizadas nos seus períodos de administração definidos.

Depois de concluir a configuração do agendamento, clique no botão **Criar** e regressará ao dashboard de estado.
Tenha em atenção que o **agendada** tabela mostra a agenda de implementação que criou.

> [!WARNING]
> Para as atualizações que requerem um reinício, a VM é reiniciada automaticamente.

### <a name="view-results-of-an-update-deployment"></a>Ver resultados de uma implementação de atualização

Após o início da implementação agendada, pode ver o estado dessa implementação no separador **Implementações de atualizações** no ecrã **Gestão de atualizações**.
Se estiver em execução, o respetivo estado é apresentado como **Em curso**. Depois de concluir, se for bem sucedida, muda para **Com êxito**.
Se existir uma falha numa ou mais atualizações na implementação, o estado é **Falha parcial**.
Clique na implementação da atualização concluída para ver o dashboard relativo a essa implementação de atualização.

   ![Dashboard de estado de implementação de atualização para implementação específica](./media/tutorial-monitoring/manageupdates-view-results.png)

No **atualizar resultados** mosaico é um resumo do número total de atualizações e resultados de implementação da VM.
Na tabela à direita encontra-se uma divisão detalhada de cada atualização e os resultados da instalação, que podem ter um dos seguintes valores:

* **Não foi efetuada uma tentativa** -a atualização não foi instalada porque não havia tempo insuficiente disponível com base na duração de janela de manutenção definida.
* **Foi concluída com êxito** -a atualização foi bem-sucedida
* **Não foi possível** -a atualização falhou

Clique em **Todos os registos** para ver todas as entradas de registo que a implementação criou.

Clique em de **saída** mosaico para obter o fluxo de trabalho de runbook responsável por gerir a implementação da atualização na VM de destino.

Clique em **Erros** para ver informações detalhadas sobre os erros da implementação.

## <a name="advanced-monitoring"></a>Monitorização avançada 

Pode fazê-lo mais avançadas de monitorização da sua VM utilizando [Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview). Se ainda não o tiver feito, pode inscrever-se para obter um [avaliação gratuita](https://www.microsoft.com/en-us/cloud-platform/operations-management-suite-trial) do Operations Management Suite.

Quando tiver acesso ao portal do OMS, pode encontrar a chave de área de trabalho e o identificador de área de trabalho no painel de definições. Utilize o [conjunto AzureRmVMExtension](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmextension) comando para adicionar a extensão do OMS à VM. Atualizar os valores das variáveis no abaixo exemplo para refletir a chave de área de trabalho do OMS e área de trabalho ID.  

```powershell
$omsId = "<Replace with your OMS Id>"
$omsKey = "<Replace with your OMS key>"

Set-AzureRmVMExtension -ResourceGroupName myResourceGroup `
  -ExtensionName "Microsoft.EnterpriseCloud.Monitoring" `
  -VMName myVM `
  -Publisher "Microsoft.EnterpriseCloud.Monitoring" `
  -ExtensionType "MicrosoftMonitoringAgent" `
  -TypeHandlerVersion 1.0 `
  -Settings @{"workspaceId" = $omsId} `
  -ProtectedSettings @{"workspaceKey" = $omsKey} `
  -Location eastus
```

Após alguns minutos, deverá ver a nova VM, na área de trabalho OMS. 

![Painel do OMS](./media/tutorial-monitoring/tutorial-monitor-oms.png)

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, configurou e rever as VMs com o Centro de segurança do Azure. Aprendeu a:

> [!div class="checklist"]
> * Criar uma rede virtual
> * Criar um grupo de recursos e a VM 
> * Ativar o diagnóstico de arranque na VM
> * Ver diagnósticos de arranque
> * Veja as métricas de anfitrião
> * Instalar a extensão de diagnóstico
> * Veja as métricas VM
> * Criar um alerta
> * Gerir atualizações do Windows
> * Configurar monitorização avançada

Avançar para o próximo tutorial para saber mais acerca do Centro de segurança do Azure.

> [!div class="nextstepaction"]
> [Gerir a segurança de VMs](./tutorial-azure-security.md)