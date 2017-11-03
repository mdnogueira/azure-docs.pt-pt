Pode facilmente [Dimensionar automaticamente](../articles/monitoring-and-diagnostics/insights-autoscale-best-practices.md) sua [máquinas virtuais (VMs)](../articles/virtual-machines/windows/overview.md) quando utiliza [conjuntos de dimensionamento de máquina virtual](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) e [funcionalidade de dimensionamento automático do Azure Monitor](../articles/monitoring-and-diagnostics/monitoring-overview-autoscale.md). As VMs têm de ser membros de uma escala definido para ser ampliada automaticamente. Este artigo fornece informações que lhe permitem compreender melhor como dimensionar as suas VMs verticalmente e horizontalmente utilizando métodos automáticas e manuais.

## <a name="horizontal-or-vertical-scaling"></a>Dimensionamento horizontal ou vertical

A funcionalidade de dimensionamento automático de Monitor de Azure apenas dimensiona horizontalmente, que é um aumento ("out") ou diminuir ("em") do número de VMs. Dimensionamento horizontal é mais flexível numa situação de nuvem como permite-lhe executar potencialmente milhares de VMs para processar carga. Dimensionar horizontalmente alterando automaticamente ou manualmente a capacidade (ou a contagem de instâncias) do conjunto de dimensionamento. 

Dimensionamento vertical mantém o mesmo número de VMs, mas faz com que as VMs mais ("cópia de segurança") ou menos ("desativado") poderosas. Energia é medida em atributos como memória, velocidade da CPU ou espaço em disco. Dimensionamento vertical está dependente da disponibilidade de hardware maior, que chega a um limite superior e pode variar consoante a região rapidamente. Também normalmente vertical dimensionamento necessita de uma VM para parar e reiniciar. Dimensionamento verticalmente, definindo um tamanho de novo na configuração das VMs no conjunto de dimensionamento.

Através de runbooks no [da automatização do Azure](../articles/automation/automation-intro.md), pode facilmente [Dimensionar VMs num conjunto de dimensionamento](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-vertical-scale-reprovision.md) ou reduzir vertical.

## <a name="create-a-virtual-machine-scale-set"></a>Criar um conjunto de dimensionamento de máquina virtual

Conjuntos de dimensionamento tornam mais fácil para implementar e gerir VMs idênticas como um conjunto. Pode criar Linux ou conjuntos de dimensionamento de Windows utilizando o [portal do Azure](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-portal-create.md), [Azure PowerShell](../articles/virtual-machines/windows/tutorial-create-vmss.md), ou o [CLI do Azure](../articles/virtual-machines/linux/tutorial-create-vmss.md). Também pode criar e gerir conjuntos de dimensionamento com SDKs, tais como [Python](/develop/python) ou [Node.js](/nodejs/azure), ou diretamente com o [REST APIs](/rest/api/compute/virtualmachinescalesets). O dimensionamento automático de VMs é conseguido através da aplicação de regras e as métricas para o conjunto de dimensionamento.

## <a name="configure-autoscale-for-a-scale-set"></a>Configurar o dimensionamento automático para um conjunto de dimensionamento

Dimensionamento automático fornece o número correto de VMs para processar a carga na sua aplicação. Permite-lhe adicionar VMs para processar os aumentos de carga e a poupar dinheiro removendo as VMs que são junto inativo. Especifique um número mínimo e máximo de VMs para ser executada com base num conjunto de regras. Ter um mínimo de torna se a aplicação está sempre em execução, mesmo em nenhuma carga. Ter um valor máximo limita o total possível custo de hora a hora.

Pode ativar o dimensionamento automático ao criar o conjunto através de dimensionamento [Azure PowerShell](../articles/monitoring-and-diagnostics/insights-powershell-samples.md#create-and-manage-autoscale-settings) ou [CLI do Azure](https://docs.microsoft.com/cli/azure/monitor/autoscale-settings). Também pode ativá-lo Depois do conjunto de dimensionamento é criado. Pode criar um conjunto de dimensionamento, instale a extensão e configurar o dimensionamento automático utilizando um [modelo Azure Resource Manager](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md). No portal do Azure, ativar o dimensionamento automático de Monitor do Azure ou ativar o dimensionamento automático das definições do conjunto de dimensionamento.

![Ativar o dimensionamento automático](./media/virtual-machines-autoscale/virtual-machines-autoscale-enable.png)
 
### <a name="metrics"></a>Métricas

A funcionalidade de dimensionamento automático de Monitor do Azure permite-lhe aumentar o número de VMs em execução cópias de segurança ou para baixo com base nos [métricas](../articles/monitoring-and-diagnostics/insights-autoscale-common-metrics.md). Por predefinição, as VMs fornecem básicas métricas de nível do anfitrião para o disco, a rede e a utilização de CPU. Quando configurar a recolha de dados de diagnóstico com a extensão de diagnóstico, contadores de desempenho de SO convidado adicionais ficam disponíveis para o disco, memória e CPU.

![Critérios de métricos](./media/virtual-machines-autoscale/virtual-machines-autoscale-criteria.png)

Se a aplicação tem de dimensionar com base nas métricas que não estão disponíveis através do anfitrião, em seguida, as VMs no conjunto de dimensionamento tem de ter o [extensão de diagnóstico do Linux](../articles/virtual-machines/linux/diagnostic-extension.md) ou [extensão de diagnóstico do Windows](../articles/virtual-machines/windows/ps-extensions-diagnostics.md)instalado. Se criar um conjunto utilizando o portal do Azure de dimensionamento, terá de também utilizar o Azure PowerShell ou a CLI do Azure para instalar a extensão com a configuração de diagnóstico que precisa.
 
### <a name="rules"></a>Regras

[Regras](../articles/monitoring-and-diagnostics/monitoring-autoscale-scale-by-custom-metric.md) combinar uma métrica com uma ação para ser executada. Quando forem cumpridas condições da regra, um ou mais ações de dimensionamento automático são acionadas. Por exemplo, poderá ter uma regra definida que aumenta o número de VMs por 1 se a utilização da CPU média ultrapassar 85 por cento.

![Ações de dimensionamento automático](./media/virtual-machines-autoscale/virtual-machines-autoscale-actions.png)
 
### <a name="notifications"></a>Notificações

Pode [configurar acionadores](../articles/monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md) para que específicos na web URLs são denominados ou os e-mails são enviados com base nas regras de dimensionamento automático que criar. Webhooks permitem-lhe encaminhar as notificações de alerta do Azure para outros sistemas de notificações de pós-processamento ou personalizados.

## <a name="manually-scale-vms-in-a-scale-set"></a>Dimensionar Manualmente VMs num conjunto de dimensionamento

### <a name="horizontal"></a>horizontal

Pode adicionar ou remover VMs alterando a capacidade do conjunto de dimensionamento. No portal do Azure, pode diminuir ou aumentar o número de VMs (apresentada como **instância contagem**) na escala definir fazendo deslizar a barra de condição de substituição no ecrã de dimensionamento esquerdo ou direito.

Com o Azure PowerShell, tem de obter o conjunto de dimensionamento objeto utilizando [Get-AzureRmVmss](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmss). Em seguida, defina o **sku.capacity** definir a propriedade para o número de VMs que pretende e a atualização de escala com [atualização AzureRmVmss](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmss). Utilizar a CLI do Azure, alterar a capacidade com o **-nova capacidade** parâmetro para o [escala de vmss az](https://docs.microsoft.com/cli/azure/vmss#scale) comando.

### <a name="vertical"></a>Vertical

Pode alterar manualmente o tamanho das VMs no portal do Azure no ecrã de tamanho para o conjunto de dimensionamento. Pode utilizar o Azure PowerShell com o Get-AzureRmVmss, definir a propriedade de sku de referência de imagem e, em seguida, utilizar [atualização AzureRmVmss](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmss) e [atualização AzureRmVmssInstance](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmssinstance).

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre os conjuntos de dimensionamento [considerações de Design para conjuntos de dimensionamento](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview.md).

