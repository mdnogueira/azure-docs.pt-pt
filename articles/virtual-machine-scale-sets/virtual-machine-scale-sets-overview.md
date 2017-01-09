---
title: "Descrição Geral dos Conjuntos de Dimensionamento de Máquinas Virtuais | Microsoft Docs"
description: "Saiba mais sobre os Conjuntos de Dimensionamento de Máquinas Virtuais"
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
ms.date: 11/15/2016
ms.author: guybo
translationtype: Human Translation
ms.sourcegitcommit: 6fb71859d0ba2e0f2b39d71edd6d518b7a03bfe9
ms.openlocfilehash: 4a2cd02f6f9b6ac51c32314ce892e572e569eb7c


---
# <a name="virtual-machine-scale-sets-overview"></a>Descrição Geral dos Conjuntos de Dimensionamento de Máquinas Virtuais
Os conjuntos de dimensionamento de máquinas virtuais são um recurso de computação do Azure que pode ser utilizado para implementar e gerir um conjunto de VMs idênticas. Com todas as VMs configuradas da mesma forma, os conjuntos de dimensionamento de VMs foram concebidos para suportar um verdadeiro dimensionamento automático – não é necessário fazer o aprovisionamento prévio das VMs –, pelo que facilitam a criação de serviços de grande escala destinados a Big Compute, macrodados e cargas de trabalho em contentores.

Para aplicações que têm de aumentar e reduzir verticalmente os recursos de computação, as operações em escala são balanceadas, implicitamente, entre domínios de falha e de atualização. Para obter uma introdução aos conjuntos de dimensionamento de VMs, veja o [anúncio no blogue do Azure](https://azure.microsoft.com/blog/azure-virtual-machine-scale-sets-ga/). 

Veja estes vídeos para saber mais sobre os conjuntos de dimensionamento de VMs:

* [Mark Russinovich talks Azure Scale Sets (Mark Russinovich fala sobre os Conjuntos de Dimensionamento do Azure)](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)  
* [Virtual Machine Scale Sets with Guy Bowerman (Conjuntos de Dimensionamento de Máquinas Virtuais, com Guy Bowerman)](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

## <a name="creating-and-managing-vm-scale-sets"></a>Criar e gerir conjuntos de dimensionamento de VMs
Pode criar um conjunto de dimensionamento de VMs no [portal do Azure](https://portal.azure.com) ao selecionar *novo* e escrever "dimensionamento" na barra de pesquisa. Verá “Conjunto de dimensionamento de máquinas virtuais” nos resultados. A partir daí, pode preencher os campos necessários para personalizar e implementar o seu conjunto de dimensionamento. 

Os conjuntos de dimensionamento de VMs também podem ser definidos e implementados com modelos JSON e [APIs REST](https://msdn.microsoft.com/library/mt589023.aspx), tal como as VMs do Azure Resource Manager individuais. Por conseguinte, pode ser utilizado qualquer método de implementação Azure Resource Manager padrão. Para obter mais informações sobre os modelos, veja [Authoring Azure Resource Manager templates (Criar modelos do Azure Resource Manager)](../azure-resource-manager/resource-group-authoring-templates.md).

Está disponível um conjunto de modelos de exemplo de conjuntos de dimensionamento de VMs no repositório GitHub dos modelos de Início Rápido do Azure, [aqui](https://github.com/Azure/azure-quickstart-templates). (procure os modelos que tenham *vmss* no título)

Nas páginas de detalhes dos modelos, verá um botão que liga à funcionalidade de implementação do portal. Para implementar o conjunto de dimensionamento de VMs, clique no botão e preencha os parâmetros obrigatórios no portal. Se não souber ao certo se um recurso suporta letras maiúsculas ou uma combinação de maiúsculas e minúsculas, é mais seguro utilizar valores de parâmetros sempre em minúsculas. Também está disponível um vídeo útil a dissecar um modelo de conjunto de dimensionamento de VMs:

[VM Scale Set Template Dissection (Dissecação de Modelo de Conjunto de Dimensionamento de VMs)](https://channel9.msdn.com/Blogs/Windows-Azure/VM-Scale-Set-Template-Dissection/player)

## <a name="scaling-a-vm-scale-set-out-and-in"></a>Aumentar e reduzir verticalmente um conjunto de dimensionamento de VMs
Para aumentar ou reduzir o número de máquinas virtuais num conjunto de dimensionamento de VMs, basta alterar a propriedade *capacidade* e reimplementar o modelo. Esta simplicidade permite escrever mais facilmente a sua própria camada de dimensionamento personalizada, se quiser definir eventos de dimensionamento personalizados que não são suportados pelo dimensionamento automático do Azure.

Se estiver a reimplementar um modelo para alterar a capacidade, pode definir um modelo muito mais pequeno que inclua apenas o SKU e a capacidade atualizada. Pode ver um exemplo [aqui](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing).

Para percorrer os passos que criam um conjunto de dimensionamento que se dimensiona automaticamente, veja [Automatically Scale Machines in a Virtual Machine Scale Set (Dimensionar Automaticamente Máquinas num Conjunto de Dimensionamento de Máquinas Virtuais)](virtual-machine-scale-sets-windows-autoscale.md).

## <a name="monitoring-your-vm-scale-set"></a>Monitorizar o conjunto de dimensionamento de VMs
O [portal do Azure](https://portal.azure.com) lista os conjuntos de dimensionamento e mostra as propriedades e as operações básicas, incluindo a apresentação das VMs no conjunto e um gráfico de utilização dos recursos. Para obter mais detalhes, pode utilizar o [Explorador de Recursos do Azure](https://resources.azure.com) para ver os conjuntos de dimensionamento de VMs. Os conjuntos de dimensionamento de VMs são um recurso de Microsoft.Compute, pelo que os pode ver a partir deste site, ao expandir as ligações seguintes:

**Subscrições -> a sua subscrição -> resourceGroups -> fornecedores -> Microsoft.Compute -> virtualMachineScaleSets -> o seu conjunto de dimensionamento de VMs -> etc.**

## <a name="vm-scale-set-scenarios"></a>Cenários de conjuntos de dimensionamento de VMs
Esta secção lista alguns cenários comuns de conjuntos de dimensionamento de VMs. Alguns serviços de nível superior do Azure (como o Batch, o Service Fabric e o Azure Container Service) vão utilizar estes cenários.

* **Aceder através de RDP / SSH a instâncias de conjuntos de dimensionamento de VMs** - é criado um conjunto de dimensionamento de VMs dentro de uma VNET e não são alocados endereços IP públicos às VMs individuais no conjunto de dimensionamento. Este comportamento é útil porque, geralmente, é melhor evitar os custos e a sobrecarga de gestão inerentes à alocação de endereços IP públicos separados para todos os recursos sem monitorização de estado na sua grelha de computação e porque pode ligar facilmente a estas VMs a partir de outros recursos da VNET, incluindo recursos que tenham endereços IP públicos, como balanceadores de carga ou máquinas virtuais autónomas.
* **Ligar a VMs com regras NAT** - pode criar um endereço IP público, atribuí-lo a um balanceador de carga e definir regras NAT de entrada que mapeiem uma porta no endereço IP para uma porta numa VM do conjunto de dimensionamento de VMs. Por exemplo:
  
  | Origem | Porta de origem | Destino | Porta de destino |
  | --- | --- | --- | --- |
  |  IP público |Porta 50.000 |vmss\_0 |Porta 22 |
  |  IP público |Porta 50.001 |vmss\_1 |Porta 22 |
  |  IP público |Porta 50.002 |vmss\_2 |Porta 22 |
  
   Eis um exemplo de criação de um conjunto de dimensionamento de VMs que utiliza regras NAT para ativar a ligação SSH a todas as VMs no conjunto de dimensionamento com um endereço IP público individual: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat)
  
   Eis um exemplo do mesmo, agora com RDP e Windows: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat)
* **Ligar a VMs com um "jumpbox"** - se criar um conjunto de dimensionamento de VMs e uma VM autónoma na mesma VNET, a VM autónoma e as VMs do conjunto de dimensionamento podem ligar-se uma à outra através dos endereços IP internos, conforme definido pela VNET/Sub-rede. Se criar um endereço IP público e o atribuir à VM autónoma, pode aceder por RDP ou SSH à VM autónoma e, depois, ligar desta às instâncias do conjunto de dimensionamento de VMs. Nesta fase, poderá reparar que um conjunto de dimensionamento de VMs simples é, inerentemente, mais seguro do que uma VM autónoma simples com um endereço IP público na respetiva configuração predefinida.
  
   Por exemplo, este modelo implementa um conjunto de dimensionamento simples com uma VM autónoma: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-jumpbox](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-jumpbox)
* **Balanceamento de carga para instâncias de conjunto de dimensionamento de VMs** - se quiser enviar trabalhos para um cluster de computação de VMs através da abordagem “round robin”, pode configurar um balanceador de carga com regras de balanceamento de carga consonantes. Pode definir sondas para verificar se a sua aplicação está a ser executada ao enviar pings para as portas com um protocolo, um intervalo e um caminho de pedido especificados. O [Gateway da Aplicação](https://azure.microsoft.com/services/application-gateway/) do Azure também suporta conjuntos de dimensionamento, juntamente com cenários de balanceamento de carga mais sofisticados.
  
   Eis um exemplo que cria um Conjunto de Dimensionamento de VMs em execução em servidores Web Apache e que utiliza um balanceador de carga para balancear a carga que cada VM recebe: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-ubuntu-web-ssl](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-ubuntu-web-ssl) (observe o tipo de recurso Microsoft.Network/loadBalancers e o networkProfile e o extensionProfile no virtualMachineScaleSet)
* **Implementar um conjunto de dimensionamento de VMs como um cluster de computação num gestor de clusters de PaaS** - por vezes, os conjuntos de dimensionamento são descritos como uma função de trabalho de próxima geração. É uma descrição válida, mas corre o risco de confundir as funcionalidades dos conjuntos de dimensionamento com as funcionalidades de Funções de trabalho de PaaS v1. De certa forma, os conjuntos de dimensionamento de VMs proporcionam uma verdadeira “função de trabalho” ou recurso de trabalho, no sentido em que disponibilizam um recurso de computação que não depende de plataformas/runtime, que é personalizável e que se integra no Azure Resource Manager IaaS.
  
   As funções de trabalho de PaaS v2, embora sejam limitadas em termos de suporte de plataformas/runtime (só suportam imagens da plataforma Windows), também incluem serviços como troca de VIP, definições de atualização configuráveis, definições específicas de implementação de runtime/aplicações, que *ainda* não estão disponíveis nos conjuntos de dimensionamento de VMs ou que serão disponibilizadas por outros serviços PaaS de alto nível, como o Service Fabric. Tendo isto presente, pode considerar os conjuntos de dimensionamento de VMs como sendo uma infraestrutura que suporta PaaS. Ou seja, as soluções de PaaS, como o Service Fabric ou os gestores de clusters como o Mesos, podem ser criadas com base nos conjuntos de dimensionamento de VMs como camadas de computação dimensionáveis.
  
   Para obter um exemplo desta abordagem, o Azure Container Service implementa um cluster com base em conjuntos de dimensionamento com um orquestrador de contentores: [https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos).

## <a name="vm-scale-set-performance-and-scale-guidance"></a>Guia de desempenho e dimensionamento dos conjuntos de dimensionamento de VMs
* Não crie mais de 500 VMs em vários conjuntos de dimensionamento de VMs ao mesmo tempo.
* Não planeie mais de 20 VMs por conta de armazenamento (a não ser que defina a propriedade *sobreaprovisionamento* como “falso”, caso em que pode planear até um máximo de 40).
* Utilize nomes com as letras iniciais o mais afastadas possível para as contas de armazenamento.  Os modelos de VMSS de exemplo nos [modelos de Início Rápido do Azure](https://github.com/Azure/azure-quickstart-templates/) mostram exemplos de como o fazer.
* Se utilizar VMs personalizadas, não planeie mais de 40 VMs por conjunto de dimensionamento numa conta de armazenamento individual.  Antes de poder iniciar a implementação do conjunto de dimensionamento de VMs, precisa de ter a cópia prévia da imagem na conta de armazenamento. Veja as FAQ para obter mais informações.
* Não planeie mais de 4096 VMs por VNET.
* O número de VMs que pode criar é limitado pela quota de núcleos da região em que está a implementar. Poderá ter de contactar o Suporte ao Cliente para aumentar o limite da quota de Computação, mesmo que tenha um limite elevado de núcleos para utilizar com os serviços em nuvem ou com IaaS v1 atualmente. Para consultar a quota, pode executar o comando da CLI do Azure `azure vm list-usage` e o comando do PowerShell `Get-AzureRmVMUsage` (se estiver a utilizar uma versão do PowerShell inferior a 1.0, execute `Get-AzureVMUsage`).

## <a name="vm-scale-set-frequently-asked-questions"></a>Perguntas mais frequentes sobre os conjuntos de dimensionamento de VMs
**P.** Quantas VMs pode ter num conjunto de dimensionamento de VMs?

**R.** Cem, se utilizar imagens de plataformas que possam ser distribuídas em várias contas de armazenamento. Se utilizar imagens personalizadas, até 40 (se a propriedade *sobreaprovisionamento* estiver definida como “falso”; a predefinição são 20), uma vez que as imagens personalizadas estão, atualmente, limitadas a uma única conta de armazenamento.

**P.** Que outros limites de recursos existem para os conjuntos de dimensionamento de VMs?

**R.** Está limitado à criação de um máximo de 500 VMs em vários conjuntos de dimensionamento por região durante um período de dez minutos. Aplicam-se os [Limites do Serviço de Subscrição do Azure](../azure-subscription-service-limits.md) atuais.

**P.** Os conjuntos de dimensionamento de VMs suportam Discos de Dados?

**R.** Não na versão inicial (apesar de os discos de dados estarem atualmente disponíveis em pré-visualização). As opções para armazenar dados são:

* Ficheiros do Azure (unidades SMB partilhadas)
* Unidade do sistema Operativo
* Unidade temporária (local, sem o suporte do armazenamento do Azure)
* Serviço de dados do Azure (por exemplo tabelas do Azure e blobs do Azure)
* Serviço de dados externos (por exemplo, BD remota)

**P.** Que regiões do Azure suportam os conjuntos de dimensionamento de VMs?

**R.** Qualquer região que suporte o Azure Resource Manager suporta os conjuntos de dimensionamento de VMs.

**P.** Como criar uma escala VM definir utilizando uma imagem personalizada?

**R.** Deixe a propriedade vhdContainers em branco; por exemplo:

    "storageProfile": {
        "osDisk": {
            "name": "vmssosdisk",
            "caching": "ReadOnly",
            "createOption": "FromImage",
            "image": {
                "uri": "https://mycustomimage.blob.core.windows.net/system/Microsoft.Compute/Images/mytemplates/template-osDisk.vhd"
            },
            "osType": "Windows"
        }
    },


**P.** Se reduzir a capacidade do conjunto de dimensionamento de VMs de 20 para 15, que VMs vão ser removidas?

**R.** As máquinas virtuais são removidas do conjunto de dimensionamento de forma uniforme entre domínios de atualização e domínios de falha, para maximizar a disponibilidade. São removidas primeiro as VMs com os IDs mais altos.

**P.** E se depois aumentar a capacidade de 15 para 18?

**R.** Se aumentar a capacidade para 18, então, são criadas três VMs novas. Sempre que isso acontecer, o ID da instância da VM é aumentado a partir do valor mais alto anterior (por exemplo, 20, 21, 22). As VMs são balanceadas entre domínios de falha e domínios de atualização.

**P.** Se utilizar várias extensões num conjunto de dimensionamento de VMs, posso forçar uma sequência de execução?

**R.** Não diretamente, mas, relativamente à extensão customScript, o script pode aguardar pela conclusão de outra extensão ([por exemplo, ao monitorizar o registo da extensão](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vmss-lapstack-autoscale/install_lap.sh)). Estão disponíveis orientações adicionais sobre a sequenciação de extensões nesta mensagem do blogue: [Extension Sequencing in Azure VM Scale Sets (Sequenciação de Extensões em Conjuntos de Dimensionamento de VMs do Azure)](https://msftstack.wordpress.com/2016/05/12/extension-sequencing-in-azure-vm-scale-sets/).

**P.** Os conjuntos de dimensionamento de VMs funcionam com os conjuntos de disponibilidade do Azure?

**R.** Sim. Um conjunto de dimensionamento de VMs é um conjunto de disponibilidade implícito com cinco domínios de falha e cinco domínios de atualização. Não tem de configurar nada em virtualMachineProfile. Em versões futuras, é provável que os conjuntos de dimensionamento de VMs abranjam vários inquilinos, mas, por agora, são um único conjunto de disponibilidade.




<!--HONumber=Dec16_HO4-->


