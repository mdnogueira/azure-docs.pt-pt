---
title: "Descrição geral dos conjuntos de dimensionamento de máquinas virtuais do Azure | Microsoft Docs"
description: "Saiba mais sobre os conjuntos de dimensionamento de máquinas virtuais do Azure"
services: virtual-machine-scale-sets
documentationcenter: 
author: gbowerman
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/01/2017
ms.author: guybo
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5fa08049fd0b13945de307e9d28224ea0d5a1307
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="what-are-virtual-machine-scale-sets-in-azure"></a>O que são os conjuntos de dimensionamento de máquinas virtuais do Azure?
Os conjuntos de dimensionamento de máquinas virtuais são um recurso de computação do Azure que pode utilizar para implementar e gerir um conjunto de VMs idênticas. Com todas as VMs configuradas da mesma forma, os conjuntos de dimensionamento foram concebidos para suportar um autêntico dimensionamento automático, sem que seja necessário o pré-aprovisionamento de VMs. Assim, é mais fácil criar serviços em grande escala orientados para macrocomputação, macrodados e cargas de trabalho em contentores.

Para aplicações que têm de aumentar e reduzir verticalmente os recursos de computação, as operações em escala são balanceadas, implicitamente, entre domínios de falha e de atualização. Para obter outra introdução aos conjuntos de dimensionamento de, veja o [anúncio no blogue do Azure](https://azure.microsoft.com/blog/azure-virtual-machine-scale-sets-ga/).

Para obter mais informações sobre os conjuntos de dimensionamento, veja estes vídeos:

* [Mark Russinovich talks Azure Scale Sets (Mark Russinovich fala sobre os conjuntos de dimensionamento do Azure)](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)  
* [Virtual Machine Scale Sets with Guy Bowerman (Conjuntos de Dimensionamento de Máquinas Virtuais, com Guy Bowerman)](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

## <a name="creating-and-managing-scale-sets"></a>Criar e gerir conjuntos de dimensionamento
Pode criar um conjunto de dimensionamento no [portal do Azure](https://portal.azure.com) ao selecionar **novo** e escrever **dimensionamento** na barra de pesquisa. É apresentado **Conjunto de dimensionamento de máquinas virtuais** nos resultados. A partir daí, pode preencher os campos necessários para personalizar e implementar o seu conjunto de dimensionamento. Também tem opções no portal para configurar regras de dimensionamento automático básicas com base na utilização da CPU. 

Os conjuntos de dimensionamento podem ser implementados numa [zona de disponibilidade](../availability-zones/az-overview.md).

> [!NOTE]
> Os conjuntos de dimensionamento de máquinas virtuais só suportam a implementação de uma zona de disponibilidade única. A implementação de várias zonas será suportada no futuro.

Pode utilizar modelos JSON e [APIs REST](https://msdn.microsoft.com/library/mt589023.aspx), tais como VMs do Azure Resource Manager individuais, para definir e implementar conjuntos de dimensionamento. Por conseguinte, pode utilizar qualquer método de implementação Azure Resource Manager padrão. Para obter mais informações sobre os modelos, veja [Authoring Azure Resource Manager templates (Criar modelos do Azure Resource Manager)](../azure-resource-manager/resource-group-authoring-templates.md).

Está disponível uma gama de modelos de exemplos de conjuntos de dimensionamento de máquinas virtuais no [repositório do GitHub dos modelos de Início Rápido do Azure](https://github.com/Azure/azure-quickstart-templates). (Procure os modelos que tenham **vmss** no título.)

Para os exemplos de modelo de Início rápido, um botão "Implementar no Azure" no ficheiro Leia-me para cada modelo é ligado à funcionalidade Implementação do portal. Para implementar o conjunto de dimensionamento, clique no botão e preencha os parâmetros obrigatórios no portal. 

## <a name="scaling-a-scale-set-out-and-in"></a>Aumentar e reduzir verticalmente um conjunto de dimensionamento
Pode clicar na secção **Dimensionamento**, em **Definições**, para alterar a capacidade dos conjuntos de dimensionamento no portal do Azure. 

Para alterar a capacidade dos conjuntos de dimensionamento na linha de comandos, utilize o comando **scale** na [CLI do Azure](https://github.com/Azure/azure-cli). Por exemplo, utilize este comando para definir um conjunto de dimensionamento com uma capacidade de dez VMs:

```bash
az vmss scale -g resourcegroupname -n scalesetname --new-capacity 10 
```

Para definir o número de VMs num conjunto de dimensionamento com o PowerShell, utilize o comando **Update-AzureRmVmss**:

```PowerShell
$vmss = Get-AzureRmVmss -ResourceGroupName resourcegroupname -VMScaleSetName scalesetname  
$vmss.Sku.Capacity = 10
Update-AzureRmVmss -ResourceGroupName resourcegroupname -Name scalesetname -VirtualMachineScaleSet $vmss
```

Para aumentar ou reduzir o número de máquinas virtuais num conjunto de dimensionamento com um modelo do Azure Resource Manager, altere a propriedade **capacidade** e reimplemente o modelo. Esta simplicidade permite integrar mais facilmente os conjuntos de dimensionamento com o Dimensionamento Automático do Azure ou escrever a sua própria camada de dimensionamento personalizada, se tiver de definir eventos de dimensionamento personalizados que o Dimensionamento Automático do Azure não suporta. 

Se estiver a reimplementar um modelo do Azure Resource Manager para alterar a capacidade, pode definir um modelo muito mais pequeno que inclua apenas o pacote da propriedade **SKU** e a capacidade atualizada. [Segue-se um exemplo](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing).

## <a name="autoscale"></a>Dimensionamento Automático

Opcionalmente, é possível configurar um conjunto de dimensionamento com definições de dimensionamento automático, se for criado no portal do Azure. O número de VMs pode, depois, ser aumentado ou diminuído, com base na utilização média da CPU. 

Muitos dos modelos de conjuntos de dimensionamento nos [modelos de Início Rápido do Azure](https://github.com/Azure/azure-quickstart-templates) têm definições de dimensionamento automático. Também pode adicionar definições de dimensionamento automático a conjuntos de dimensionamento já existentes. Por exemplo, este script do Azure PowerShell adiciona dimensionamento automático baseado na CPU a um conjunto de dimensionamento:

```PowerShell

$subid = "yoursubscriptionid"
$rgname = "yourresourcegroup"
$vmssname = "yourscalesetname"
$location = "yourlocation" # e.g. southcentralus

$rule1 = New-AzureRmAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/$subid/resourceGroups/$rgname/providers/Microsoft.Compute/virtualMachineScaleSets/$vmssname -Operator GreaterThan -MetricStatistic Average -Threshold 60 -TimeGrain 00:01:00 -TimeWindow 00:05:00 -ScaleActionCooldown 00:05:00 -ScaleActionDirection Increase -ScaleActionValue 1
$rule2 = New-AzureRmAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/$subid/resourceGroups/$rgname/providers/Microsoft.Compute/virtualMachineScaleSets/$vmssname -Operator LessThan -MetricStatistic Average -Threshold 30 -TimeGrain 00:01:00 -TimeWindow 00:05:00 -ScaleActionCooldown 00:05:00 -ScaleActionDirection Decrease -ScaleActionValue 1
$profile1 = New-AzureRmAutoscaleProfile -DefaultCapacity 2 -MaximumCapacity 10 -MinimumCapacity 2 -Rules $rule1,$rule2 -Name "autoprofile1"
Add-AzureRmAutoscaleSetting -Location $location -Name "autosetting1" -ResourceGroup $rgname -TargetResourceId /subscriptions/$subid/resourceGroups/$rgname/providers/Microsoft.Compute/virtualMachineScaleSets/$vmssname -AutoscaleProfiles $profile1
```

Pode encontrar uma lista de métricas válidas para dimensionar em [Supported metrics with Azure Monitor (Métricas suportadas com o Azure Monitor)](../monitoring-and-diagnostics/monitoring-supported-metrics.md), na secção "Microsoft.Compute/virtualMachineScaleSets". Também estão disponíveis opções de dimensionamento automático mais avançadas, incluindo o dimensionamento automático com base numa agenda e a utilização de webhooks para integrar em sistemas de alerta.

## <a name="monitoring-your-scale-set"></a>Monitorizar o conjunto de dimensionamento
O [portal do Azure](https://portal.azure.com) apresenta uma lista de conjuntos de dimensionamento e mostra as propriedades dos mesmos. Também suporta operações de gestão. Pode realizar operações de gestão em conjuntos de dimensionamento e em VMs individuais dentro de um conjunto de dimensionamento. O portal disponibiliza, igualmente, um gráfico de utilização de recursos personalizável. 

Se precisar de ver ou editar a definição de JSON subjacente de um recurso do Azure, também pode utilizar o [Explorador de Recursos do Azure](https://resources.azure.com). Os conjuntos de dimensionamento são um recurso no fornecedor de recursos do Microsoft.Compute Azure. A partir deste site, pode expandir as ligações seguintes para vê-los:

**Subscrições** > **a sua subscrição** > **resourceGroups** > **fornecedores** > **Microsoft.Compute** > **virtualMachineScaleSets** > **o seu conjunto de dimensionamento** > etc.

## <a name="scale-set-scenarios"></a>Cenários de conjuntos de dimensionamento
Esta secção lista alguns cenários comuns de conjuntos de dimensionamento. Alguns serviços de nível superior do Azure (como o Batch, o Service Fabric e o Container Service) utilizam estes cenários.

* **Aceder através de RDP ou SSH a instâncias de conjuntos de dimensionamento**: é criado um conjunto de dimensionamento dentro de uma rede virtual e não são alocados endereços IP públicos às VMs individuais no conjunto de dimensionamento por predefinição. Esta política evita a sobrecarga de despesas e de gestão inerentes à alocação de endereços IP públicos separados a todos os nós na sua grelha de computação. Se precisa de direcionar ligações externas às máquinas virtuais do conjunto de dimensionamento, pode configurar um conjunto de dimensionamento para atribuir automaticamente endereços IP públicos a novas VMs. Em alternativa, pode ligar a VMs a partir de outros recursos na sua rede virtual que podem ser endereços IP públicos alocados, como, por exemplo, balanceadores de carga e máquinas virtuais autónomas. 
* **Ligar a VMs com regras NAT**: pode criar um endereço IP público, atribuí-lo a um balanceador de carga e definir um conjunto NAT de entrada. Estas ações mapeiam portas no endereço IP para uma porta numa VM num conjunto de dimensionamento. Por exemplo:
  
  | Origem | Porta de origem | Destino | Porta de destino |
  | --- | --- | --- | --- |
  |  IP público |Porta 50.000 |vmss\_0 |Porta 22 |
  |  IP público |Porta 50.001 |vmss\_1 |Porta 22 |
  |  IP público |Porta 50.002 |vmss\_2 |Porta 22 |
  
   [Neste exemplo](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat), são definidas regras NAT para ativar uma ligação SSH a todas as VMs num conjunto de dimensionamento ao utilizar um único endereço IP público.
  
   [Este exemplo](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat) faz o mesmo com RDP e o Windows.
* **Ligar a VMs com um "jumpbox"**: se criar um conjunto de dimensionamento e uma VM autónoma na mesma rede virtual, a VM autónoma e a VM do conjunto de dimensionamento podem ligar-se uma à outra através dos endereços IP internos, conforme definido pela rede virtual ou pela sub-rede. Se criar um endereço IP público e o atribuir à VM autónoma, pode utilizar RDP ou SSH para ligar à mesma. Em seguida, pode ligar dessa máquina às instâncias do conjunto de dimensionamento. Nesta fase, poderá reparar que um conjunto de dimensionamento simples é, inerentemente, mais seguro do que uma VM autónoma simples com um endereço IP público na respetiva configuração predefinida.
  
   Por exemplo, [este modelo](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-jumpbox) implementa um conjunto de dimensionamento simples com uma VM autónoma. 
* **Balanceamento de carga para instâncias de conjuntos de dimensionamento**:se quiser enviar trabalhos para um cluster de cálculo de VMs através da abordagem “round robin”, pode configurar um balanceador de carga do Azure com regras de balanceamento de carga de camada 4 consonantes. Pode definir sondas para verificar se a sua aplicação está a ser executada ao enviar pings para as portas com um protocolo, um intervalo e um caminho de pedido especificados. O [Gateway de Aplicação do Azure](https://azure.microsoft.com/services/application-gateway/) também suporta conjuntos de dimensionamento, juntamente com cenários de balanceamento de carga de camada 7 e outros mais sofisticados.
  
   [Este exemplo](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-ubuntu-web-ssl) cria um conjunto de dimensionamento que executa servidores Web Apache e utiliza um balanceador de carga para balancear a carga que cada VM recebe. (Repare no tipo de recurso Microsoft.Network/loadBalancers e networkProfile e no extensionProfile em virtualMachineScaleSet.)

   [Este exemplo para Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-ubuntu-app-gateway) e [este exemplo para Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-app-gateway) utilizam o Gateway de Aplicação.  

* **Implementar um conjunto de dimensionamento como um cluster de cálculo num gestor de clusters de PaaS**: por vezes, os conjuntos de dimensionamento são descritos como uma função de trabalho de próxima geração. Embora seja uma descrição válida, corre o risco de confundir as funcionalidades dos conjuntos de dimensionamento com as funcionalidades dos Serviços Cloud do Azure. De certa forma, os conjuntos de dimensionamento são uma verdadeira função de trabalho ou recurso de função de trabalho. São um recurso de computação generalizado que é independente de plataforma/runtime, personalizável e que se integra no IaaS do Azure Resource Manager.
  
   As funções de trabalho dos Serviços Cloud são limitadas em termos de suporte para plataformas/runtime (imagens de plataforma do Windows apenas). Contudo, também incluem serviços como alternância de VIP, definições de atualização configuráveis e definições específicas de runtime/implementação de aplicações. Estes serviços *ainda* não estão disponíveis nos conjuntos de dimensionamento ou são fornecidos por outros serviços de PaaS de nível superior, como o Azure Service Fabric. Pode encarar os conjuntos de dimensionamento como uma infraestrutura que suporta PaaS. As soluções de PaaS como o [Service Fabric](https://azure.microsoft.com/services/service-fabric/) são criadas com base nesta infraestrutura.
  
   [Neste exemplo](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos) desta abordagem, o [Azure Container Service](https://azure.microsoft.com/services/container-service/) implementa um cluster com base em conjuntos de dimensionamento com um orquestrador de contentores.

## <a name="scale-set-performance-and-scale-guidance"></a>Orientação de desempenho e dimensionamento dos conjuntos de dimensionamento
* Os conjuntos de dimensionamento suportam até mil VMs. Se criar e carregar as suas próprias imagens de VM personalizadas, o limite é 300. Para obter considerações sobre quando utilizar conjuntos de dimensionamento grandes, veja [Trabalhar com conjuntos de dimensionamento de máquinas virtuais de grande escala](virtual-machine-scale-sets-placement-groups.md).
* Não é necessário criar previamente contas de armazenamento do Azure para utilizar os conjuntos de dimensionamento. Os conjuntos de dimensionamento suportam os discos geridos do Azure, o que elimina as preocupações de desempenho quanto ao número de discos por conta de armazenamento. Para obter mais informações, veja [Conjuntos de dimensionamento de VMs do Azure e discos e geridos](virtual-machine-scale-sets-managed-disks.md).
* Considere utilizar o armazenamento Premium do Azure em vez do Armazenamento Standard, para beneficiar de tempos de aprovisionamento de VMs mais rápidos e previsíveis e um desempenho de E/S melhorado.
* A quota de núcleos na região em que está a implementar limita o número de VMs que pode criar. Poderá ter de contactar o Suporte ao Cliente para aumentar o seu limite de quota de computação, mesmo que tenha atualmente um limite elevado de núcleos para utilizar com os Serviços Cloud do Azure. Para consultar a sua quota, execute este comando da CLI do Azure: `azure vm list-usage`. Ou execute este comando do PowerShell: `Get-AzureRmVMUsage`.

## <a name="frequently-asked-questions-for-scale-sets"></a>Perguntas mais frequentes sobre os conjuntos de dimensionamento
**P.** Quantas VMs posso ter num conjunto de dimensionamento?

**R.** Os conjuntos de dimensionamento podem ter entre zero e mil VMs com base nas imagens da plataforma ou entre zero e 300 VMs com base em imagens personalizadas. 

**P.** Os conjuntos de dimensionamento suportam discos de dados?

**R.** Sim. Os conjuntos de dimensionamento podem definir uma configuração de discos de dados anexados, que se aplica a todas as VMs nos conjuntos. Para obter mais informações, veja [Conjuntos de dimensionamento de VMs do Azure e discos de dados anexados](virtual-machine-scale-sets-attached-disks.md). As outras opções para armazenar dados são:

* Ficheiros do Azure (unidades SMB partilhadas)
* Unidade do sistema Operativo
* Unidade temporária (local, sem o suporte do Armazenamento do Azure)
* Serviço de dados do Azure (por exemplo, tabelas do Azure e blobs do Azure)
* Serviço de dados externo (por exemplo, base de dados remota)

**P.** Que regiões do Azure suportam os conjuntos de dimensionamento?

**R.** Todas as regiões suportam conjuntos de dimensionamento.

**P.** Como posso utilizar uma imagem personalizada para criar um conjunto de dimensionamento?

**R.** Crie um disco gerido com base no VHD da sua imagem personalizada e faça referência ao mesmo no modelo do conjunto de dimensionamento. [Segue-se um exemplo](https://github.com/chagarw/MDPP/tree/master/101-vmss-custom-os).

**P.** Se reduzir a capacidade do conjunto de dimensionamento de 20 para 15, que VMs são removidas?

**R.** As máquinas virtuais são removidas do conjunto de dimensionamento de forma uniforme entre domínios de atualização e domínios de falha, para maximizar a disponibilidade. São removidas primeiro as VMs com os IDs mais altos.

**P.** E se, depois, aumentar a capacidade de 15 para 18?

**R.** Se aumentar a capacidade para 18, então, são criadas três VMs novas. Sempre que isso acontecer, o ID da instância da VM é aumentado a partir do valor mais alto anterior (por exemplo, 20, 21, 22). As VMs são balanceadas entre os domínios de falhas e os domínios de atualização.

**P.** Se utilizar várias extensões num conjunto de dimensionamento, posso forçar uma sequência de execução?

**R.** Não diretamente, mas, relativamente à extensão customScript, o script pode aguardar pela conclusão de outra extensão. Pode obter orientações adicionais sobre a sequenciação de extensões nesta mensagem do blogue: [Extension Sequencing in Azure VM Scale Sets (Sequenciação de Extensões em Conjuntos de Dimensionamento de VMs do Azure)](https://msftstack.wordpress.com/2016/05/12/extension-sequencing-in-azure-vm-scale-sets/).

**P.** Os conjuntos de dimensionamento funcionam com os conjuntos de disponibilidade do Azure?

**R.** Sim. Os conjuntos de dimensionamento são conjuntos de disponibilidade implícitos com cinco domínios de falha e cinco domínios de atualização. Os conjuntos de dimensionamento com mais de cem VMs abrangem vários *grupos de posicionamento*, que são equivalentes a vários conjuntos de disponibilidade. Para obter mais informações sobre os grupos de posicionamento, veja [Trabalhar com conjuntos de dimensionamento de máquinas virtuais de grande escala](virtual-machine-scale-sets-placement-groups.md). Um conjunto de disponibilidade de VMs pode existir na mesma rede virtual como um conjunto de dimensionamento de VMs. Uma configuração comum é colocar as VMs de nó de controlo (que, muitas vezes, requerem uma configuração exclusiva) num conjunto de disponibilidade e os nós de dados no conjunto de dimensionamento.

Pode obter mais respostas a perguntas sobre os conjuntos de dimensionamento nas [Azure Virtual Machine Scale Sets FAQ (FAQ dos conjuntos de dimensionamento de máquinas virtuais do Azure)](virtual-machine-scale-sets-faq.md).
