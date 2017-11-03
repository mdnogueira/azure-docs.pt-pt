---
title: Monitorizar e atualizar os computadores virtuais Linux no Azure | Microsoft Docs
description: "Saiba como monitorizar o diagnóstico de arranque e métricas de desempenho e gerir atualizações do pacote numa máquina virtual com Linux no Azure"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/08/2017
ms.author: davidmu
ms.custom: mvc
ms.openlocfilehash: 70c17d9a8f7bf6d9106efcb56eee7cd996460c18
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-monitor-and-update-a-linux-virtual-machine-in-azure"></a>Como monitorizar e atualizar uma máquina virtual do Linux no Azure

Para garantir que as máquinas virtuais (VMs) no Azure estão a ser executado corretamente, pode rever o diagnóstico de arranque, métricas de desempenho e gerir atualizações do pacote. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Ativar o diagnóstico de arranque na VM
> * Ver diagnósticos de arranque
> * Veja as métricas de anfitrião
> * Ativar a extensão de diagnóstico na VM
> * Veja as métricas VM
> * Criar alertas com base nas métricas de diagnóstico
> * Gerir atualizações do pacote
> * Configurar monitorização avançada


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este tutorial, necessita que está a executar a CLI do Azure versão 2.0.4 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar o Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="create-vm"></a>Criar VM

Para ver os diagnósticos e as métricas em ação, terá de uma VM. Em primeiro lugar, crie um grupo de recursos com [criar grupo az](/cli/azure/group#create). O exemplo seguinte cria um grupo de recursos denominado *myResourceGroupMonitor* no *eastus* localização.

```azurecli-interactive 
az group create --name myResourceGroupMonitor --location eastus
```

Agora criar uma VM com [az vm criar](https://docs.microsoft.com/cli/azure/vm#az_vm_create). O exemplo seguinte cria uma VM chamada *myVM*:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="enable-boot-diagnostics"></a>Ativar o diagnóstico de arranque

Como efetuar o arranque de VMs com Linux, a extensão de diagnóstico de arranque captura a saída de arranque e armazena-os no armazenamento do Azure. Estes dados podem ser utilizados para resolver problemas de arranque VM. Diagnóstico de arranque não é ativado automaticamente quando criar uma VM com Linux utilizando a CLI do Azure.

Antes de ativar o diagnóstico de arranque, uma conta de armazenamento tem de ser criada para armazenar os registos de arranque. As contas de armazenamento tem de ter um nome globalmente exclusivo, ter entre 3 e 24 carateres e tem de conter apenas números e letras minúsculas. Criar uma conta de armazenamento com o [criar conta de armazenamento az](/cli/azure/storage/account#create) comando. Neste exemplo, uma cadeia aleatória é utilizada para criar um nome de conta de armazenamento exclusivas. 

```azurecli-interactive 
storageacct=mydiagdata$RANDOM

az storage account create \
  --resource-group myResourceGroupMonitor \
  --name $storageacct \
  --sku Standard_LRS \
  --location eastus
```

Ao ativar o diagnóstico de arranque, é necessário o URI para o contentor de blob storage. O comando seguinte consulta a conta de armazenamento para devolver este URI. O valor URI é armazenado em nomes de variáveis *bloburi*, que é utilizada no próximo passo.

```azurecli-interactive 
bloburi=$(az storage account show --resource-group myResourceGroupMonitor --name $storageacct --query 'primaryEndpoints.blob' -o tsv)
```

Ativar agora o diagnóstico de arranque com [ativar o diagnóstico de arranque do vm az](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics#az_vm_boot_diagnostics_enable). O `--storage` valor é o blob URI recolhido no passo anterior.

```azurecli-interactive 
az vm boot-diagnostics enable \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --storage $bloburi
```


## <a name="view-boot-diagnostics"></a>Ver diagnósticos de arranque

Quando é ativado diagnóstico de arranque, sempre que parar e iniciar a VM, informações sobre o processo de arranque são escritas num ficheiro de registo. Neste exemplo, Desalocação da VM com o [az vm desalocar](/cli/azure/vm#deallocate) comando da seguinte forma:

```azurecli-interactive 
az vm deallocate --resource-group myResourceGroupMonitor --name myVM
```

Agora iniciar a VM com o [início de vm az]( /cli/azure/vm#stop) comando da seguinte forma:

```azurecli-interactive 
az vm start --resource-group myResourceGroupMonitor --name myVM
```

Pode obter os dados de diagnóstico de arranque para *myVM* com o [az vm diagnóstico de arranque get-arranque-registo](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics#az_vm_boot_diagnostics_get_boot_log) comando da seguinte forma:

```azurecli-interactive 
az vm boot-diagnostics get-boot-log --resource-group myResourceGroupMonitor --name myVM
```


## <a name="view-host-metrics"></a>Veja as métricas de anfitrião

Uma VM com Linux tem um anfitrião dedicado no Azure que interage com. As métricas são automaticamente recolhidas para o anfitrião e podem ser visualizadas no portal do Azure da seguinte forma:

1. No portal do Azure, clique em **grupos de recursos**, selecione **myResourceGroupMonitor**e, em seguida, selecione **myVM** na lista de recursos.
1. Para ver como o anfitrião de VM está a efetuar, clique em **métricas** no painel de VM, em seguida, selecione qualquer uma do *[anfitrião]* métricas em **as métricas disponíveis**.

    ![Veja as métricas de anfitrião](./media/tutorial-monitoring/monitor-host-metrics.png)


## <a name="install-diagnostics-extension"></a>Instalar a extensão de diagnóstico

> [!IMPORTANT]
> Este documento descreve a versão 2.3 a extensão de diagnóstico do Linux, que foi preterido. Versão 2.3 será suportada até 30 de Junho de 2018.
>
> Pode ser ativada em vez disso, a versão 3.0 a extensão de diagnóstico do Linux. Para obter mais informações, consulte [a documentação](./diagnostic-extension.md).

As métricas de anfitrião básica estão disponíveis, mas mais granular e métricas específicas de VM, que tem de instalar a extensão de diagnóstico do Azure na VM. A extensão de diagnóstico do Azure permite a monitorização adicional e os dados de diagnóstico a obtenção da VM. Pode ver estas métricas de desempenho e criar alertas com base nos como efetua a VM. A extensão de diagnóstico é instalada através do portal do Azure da seguinte forma:

1. No portal do Azure, clique em **grupos de recursos**, selecione **myResourceGroup**e, em seguida, selecione **myVM** na lista de recursos.
1. Clique em **definições de diagnóstico**. A lista mostra que *diagnóstico de arranque* já estão ativadas da secção anterior. Clique na caixa de verificação para *métricas básicas*.
1. No *conta de armazenamento* secção, procure e selecione o *mydiagdata [1234]* conta criada na secção anterior.
1. Clique no botão **Guardar**.

    ![Veja as métricas diagnóstico](./media/tutorial-monitoring/enable-diagnostics-extension.png)


## <a name="view-vm-metrics"></a>Veja as métricas VM

Pode ver as métricas VM da mesma forma que visualizadas anfitrião métricas VM:

1. No portal do Azure, clique em **grupos de recursos**, selecione **myResourceGroup**e, em seguida, selecione **myVM** na lista de recursos.
1. Para ver como a VM está a efetuar, clique em **métricas** no painel de VM e, em seguida, selecione qualquer uma das métricas de diagnóstico em **as métricas disponíveis**.

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

## <a name="manage-package-updates"></a>Gerir atualizações do pacote

Ao utilizar a gestão de atualizações, pode gerir o pacote de atualizações e correções de erros para as suas VMs do Linux do Azure. Diretamente da sua VM, pode rapidamente avaliar o estado das atualizações disponíveis, agendar a instalação de atualizações necessárias e reveja os resultados de implementação para verificar as atualizações foram aplicadas com êxito para a VM.

Para obter informações sobre preços, consulte [automatização preços para gestão de atualizações](https://azure.microsoft.com/pricing/details/automation/)

### <a name="enable-update-management-preview"></a>Ativar a gestão de atualização (pré-visualização)

Ativar a gestão de atualização para a VM

1. No lado esquerdo do ecrã, selecione **máquinas virtuais**.
1. Na lista, selecione uma VM.
1. No ecrã da VM, no **operações** secção, clique em **gestão de atualizações**. O **gestão de atualizações ativar** ecrã é aberto.

A validação é efetuada para determinar se a gestão de atualizações está ativada para esta VM. A validação inclui verifica a existência de uma área de trabalho de análise de registos e a conta de automatização ligada, e se a solução é na área de trabalho.

Uma área de trabalho de análise de registos é utilizada para recolher dados gerados pelas funcionalidades e serviços, tais como a gestão de atualizações. A área de trabalho fornece uma localização única para rever e analisar dados de várias origens. Para executar a ação adicional em VMs que necessitam de atualizações, o automatização do Azure permite-lhe executar scripts em VMs, tal como transferir e aplicar atualizações.

O processo de validação também verifica se a VM está aprovisionada com o Microsoft Monitoring Agent (MMA) e de trabalho híbrida. Este agente é utilizado para comunicar com a VM e obter informações sobre o estado de atualização. 

Se estes pré-requisitos não são cumpridos, é apresentada uma faixa que dá-lhe a opção para ativar a solução.

![Faixa de carregar configuração de gestão de atualização](./media/tutorial-monitoring/manage-updates-onboard-solution-banner.png)

Clique na faixa para ativar a solução. Se qualquer um dos seguintes pré-requisitos foram identificadas como estando em falta após a validação, estes serão automaticamente adicionados:

* [Análise de registo](../../log-analytics/log-analytics-overview.md) área de trabalho
* [Automatização](../../automation/automation-offering-get-started.md)
* A [runbook worker híbrido](../../automation/automation-hybrid-runbook-worker.md) está ativado na VM

O **ativar a gestão de atualização** ecrã é aberto. Configure as definições e clique em **ativar**.

![Ativar a solução de gestão de atualizações](./media/tutorial-monitoring/manage-updates-update-enable.png)

Ativar a solução pode demorar até 15 minutos e, durante este tempo, não deve fechar a janela do browser. Depois da solução estiver ativada, informações sobre atualizações em falta do Gestor de pacote na VM fluem à análise de registos.
Pode demorar entre 30 minutos e 6 horas para os dados disponíveis para análise.

### <a name="view-update-assessment"></a>Ver avaliação de atualizações

Depois do **gestão de atualizações** solução estiver ativada, o **gestão de atualizações** ecrã aparece. Pode ver uma lista de atualizações em falta no separador **Atualizações em falta**.

![Ver o estado de atualização](./media/tutorial-monitoring/manage-updates-view-status-linux.png)

### <a name="schedule-an-update-deployment"></a>Agendar uma implementação de atualizações

Para instalar atualizações, agende uma implementação que se segue a janela de agendamento e manutenção de versão.

Agendar uma nova implementação de atualização para a VM clicando **implementação de atualização de agendamento** na parte superior do **gestão de atualizações** ecrã. No **novo atualizar implementação** ecrã, especifique as seguintes informações:

* **Nome** - Indique um nome exclusivo para identificar a implementação de atualizações.
* **Atualizações para excluir** -Selecione esta opção para introduzir nomes de pacotes para excluir a atualização.
* **Definições da agenda** - Pode aceitar a data e hora predefinidas, que é 30 minutos após a hora atual, ou especificar uma hora diferente. Também pode especificar se a implementação ocorre uma vez ou configurar um agendamento periódico. Clique na opção Periódico, em Periodicidade, para configurar um agendamento periódico.

  ![Ecrã de Definições de Agendamento de Atualizações](./media/tutorial-monitoring/manage-updates-schedule-linux.png)

* **Janela de manutenção (minutos)** - Especifique o período de tempo no qual pretende que a implementação da atualização ocorra.  Isto ajuda a garantir que as alterações são efetuadas no seu janelas de manutenção definidas. 

Depois de concluir a configuração do agendamento, clique no botão **Criar** e regressará ao dashboard de estado.
Tenha em atenção que o **agendada** tabela mostra a agenda de implementação que criou.

> [!WARNING]
> A VM será reiniciada automaticamente após as atualizações são instaladas se houver tempo suficiente na janela de manutenção.

Gestão de atualizações utiliza o Gestor de pacote existente na sua VM para instalar pacotes.

### <a name="view-results-of-an-update-deployment"></a>Ver resultados de uma implementação de atualização

Após o início da implementação agendada, pode ver o estado dessa implementação no separador **Implementações de atualizações** no ecrã **Gestão de atualizações**.
Se estiver em execução, o respetivo estado é apresentado como **Em curso**. Depois de concluir, se for bem sucedida, muda para **Com êxito**.
Se existir uma falha com um ou mais atualizações na implementação, o estado é **falha**.
Clique na implementação da atualização concluída para ver o dashboard relativo a essa implementação de atualização.

![Dashboard de estado de implementação de atualização para implementação específica](./media/tutorial-monitoring/manage-updates-view-results.png)

No **atualizar resultados** mosaico é um resumo do número total de atualizações e resultados de implementação da VM.
Na tabela à direita encontra-se uma divisão detalhada de cada atualização e os resultados da instalação, que podem ter um dos seguintes valores:

* **Não foi efetuada uma tentativa** -a atualização não foi instalada porque não havia tempo insuficiente disponível com base na duração de janela de manutenção definida.
* **Foi concluída com êxito** -a atualização foi transferida e instalada na VM com êxito
* **Não foi possível** -a atualização não foi possível transferir ou instalar na VM.

Clique em **Todos os registos** para ver todas as entradas de registo que a implementação criou.

Clique em de **saída** mosaico para obter o fluxo de trabalho de runbook responsável por gerir a implementação da atualização na VM de destino.

Clique em **Erros** para ver informações detalhadas sobre os erros da implementação.

## <a name="advanced-monitoring"></a>Monitorização avançada 

Pode fazê-lo mais avançadas de monitorização da sua VM utilizando [Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview). Se ainda não o tiver feito, pode inscrever-se para obter um [avaliação gratuita](https://www.microsoft.com/en-us/cloud-platform/operations-management-suite-trial) do Operations Management Suite.

Quando tiver acesso ao portal do OMS, pode encontrar a chave de área de trabalho e o identificador de área de trabalho no painel de definições. Substitua < chave de área de trabalho > e < id da área de trabalho > com os valores para a partir do seu OMS área de trabalho e, em seguida, pode utilizar **conjunto de extensão de vm az** para adicionar a extensão do OMS para a VM:

```azurecli-interactive 
az vm extension set \
  --resource-group myResourceGroupMonitor \
  --vm-name myVM \
  --name OmsAgentForLinux \
  --publisher Microsoft.EnterpriseCloud.Monitoring \
  --version 1.3 \
  --protected-settings '{"workspaceKey": "<workspace-key>"}' \
  --settings '{"workspaceId": "<workspace-id>"}'
```

No painel de pesquisa de registo do portal do OMS, deverá ver *myVM* , tais como o que é mostrado na imagem seguinte:

![Painel do OMS](./media/tutorial-monitoring/tutorial-monitor-oms.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, configurada, revistas e geridos atualizações para uma VM. Aprendeu a:

> [!div class="checklist"]
> * Ativar o diagnóstico de arranque na VM
> * Ver diagnósticos de arranque
> * Veja as métricas de anfitrião
> * Ativar a extensão de diagnóstico na VM
> * Veja as métricas VM
> * Criar alertas com base nas métricas de diagnóstico
> * Gerir atualizações do pacote
> * Configurar monitorização avançada

Avançar para o próximo tutorial para saber mais sobre o Centro de segurança do Azure.

> [!div class="nextstepaction"]
> [Gerir a segurança de VMs](./tutorial-azure-security.md)
