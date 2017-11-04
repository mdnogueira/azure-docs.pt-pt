---
title: "Configuração de armazenamento para VMs de SQL Server | Microsoft Docs"
description: "Este tópico descreve a forma como o Azure configura armazenamento para VMs de SQL Server durante o aprovisionamento (modelo de implementação do Resource Manager). Também explica como configurar o armazenamento para as VMs de SQL Server existente."
services: virtual-machines-windows
documentationcenter: na
author: ninarn
manager: jhubbard
tags: azure-resource-manager
ms.assetid: 169fc765-3269-48fa-83f1-9fe3e4e40947
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/31/2017
ms.author: ninarn
ms.openlocfilehash: 20fbc21224410456919e82f3a63c506eb6e573e4
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="storage-configuration-for-sql-server-vms"></a>Configuração de armazenamento para VMs de SQL Server
Quando configura uma imagem de máquina virtual do SQL Server no Azure, o Portal de ajuda a automatizar a configuração de armazenamento. Isto inclui anexar armazenamento para a VM, tornando esse armazenamento acessível ao SQL Server e configurá-lo a otimizar para os seus requisitos de desempenho específicos.

Este tópico explica como Azure configura o armazenamento para as suas VMs do SQL Server durante o aprovisionamento e para as VMs existentes. Esta configuração é base a [melhores práticas do desempenho](virtual-machines-windows-sql-performance.md) para VMs do Azure com o SQL Server.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Pré-requisitos
Para utilizar as definições de configuração de armazenamento automática, a máquina virtual requer as seguintes características:

* Aprovisionada com um [imagem de galeria do SQL Server](virtual-machines-windows-sql-server-iaas-overview.md#option-1-create-a-sql-vm-with-per-minute-licensing).
* Utiliza o [modelo de implementação do Resource Manager](../../../azure-resource-manager/resource-manager-deployment-model.md).
* Utiliza [armazenamento Premium](../premium-storage.md).

## <a name="new-vms"></a>Novas VMs
As secções seguintes descrevem como configurar o armazenamento para novas máquinas virtuais do SQL Server.

### <a name="azure-portal"></a>Portal do Azure
Quando o aprovisionamento de VM do Azure com uma imagem de galeria do SQL Server, pode optar por configurar automaticamente o armazenamento para a nova VM. Especifique o tamanho de armazenamento, a limites de desempenho e o tipo de carga de trabalho. A seguinte captura de ecrã mostra o painel de configuração de armazenamento utilizado durante a VM do SQL de aprovisionamento.

![Configuração do armazenamento VM do SQL Server durante o aprovisionamento](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-provisioning.png)

Com base nas opções do, o Azure executa as seguintes tarefas de configuração de armazenamento depois de criar a VM:

* Cria e anexa discos de dados de armazenamento premium para a máquina virtual.
* Configura os discos de dados esteja acessível ao SQL Server.
* Configura os discos de dados num agrupamento de armazenamento com base nos tamanho e de desempenho (IOPS e débito) requisitos especificados.
* Associa o agrupamento de armazenamento novo disco na máquina virtual.
* Otimiza esta nova unidade com base no seu tipo de carga de trabalho especificado (armazenamento de dados, processamento transacional ou geral).

Para obter mais detalhes sobre como o Azure configura as definições de armazenamento, consulte o [secção de configuração de armazenamento](#storage-configuration). Para obter instruções completas de como criar uma VM do SQL Server no Portal do Azure, consulte [o tutorial de aprovisionamento](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="resource-manage-templates"></a>Modelos de gerir recursos
Se utilizar os seguintes modelos do Resource Manager, dois discos de dados premium ligados por predefinição, sem qualquer configuração de agrupamento de armazenamento. No entanto, pode personalizar estes modelos para alterar o número de discos de dados premium que estão anexados à máquina virtual.

* [Criar a VM com cópia de segurança automatizada](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autobackup)
* [Criar a VM com uma aplicação de patches automatizada](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autopatching)
* [Criar a VM com a integração AKV](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-keyvault)

## <a name="existing-vms"></a>VMs existentes
Para VMs existentes do SQL Server, pode modificar algumas definições de armazenamento no portal do Azure. Selecione a VM, avance para a área de definições e, em seguida, selecione configuração do SQL Server. O painel de configuração do SQL Server mostra a utilização de armazenamento atual da VM. Todas as unidades que existem na VM são apresentadas neste gráfico. Para cada unidade, o espaço de armazenamento é apresentado em quatro secções:

* Dados do SQL Server
* Registo SQL
* Outros (armazenamento de não-SQL)
* Disponível

![Configurar o armazenamento de VM de SQL Server existente](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-existing.png)

Para configurar o armazenamento para adicionar um novo disco ou expandir um disco existente, clique na hiperligação de edição acima do gráfico.

As opções de configuração que vê varia consoante se utilizou esta funcionalidade antes. Quando utilizar pela primeira vez, pode especificar os requisitos de armazenamento para uma unidade de novo. Se tiver utilizado anteriormente esta funcionalidade para criar uma unidade, pode optar por alargar o armazenamento de nessa unidade.

### <a name="use-for-the-first-time"></a>Utilizar pela primeira vez
Se for a primeira vez ao utilizar esta funcionalidade, pode especificar os limites de tamanho e o desempenho de armazenamento para uma unidade de novo. Esta experiência é semelhante ao que poderá ver em tempo de aprovisionamento. A principal diferença é que não são permitidas para especificar o tipo de carga de trabalho. Esta restrição impede perturbar quaisquer configurações existentes do SQL Server na máquina virtual.

![Configurar os controlos de deslize de armazenamento de servidor SQL](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-usage-sliders.png)

O Azure cria uma nova unidade com base na sua especificações. Neste cenário, o Azure executa as seguintes tarefas de configuração de armazenamento:

* Cria e anexa discos de dados de armazenamento premium para a máquina virtual.
* Configura os discos de dados esteja acessível ao SQL Server.
* Configura os discos de dados num agrupamento de armazenamento com base nos tamanho e de desempenho (IOPS e débito) requisitos especificados.
* Associa o agrupamento de armazenamento novo disco na máquina virtual.

Para obter mais detalhes sobre como o Azure configura as definições de armazenamento, consulte o [secção de configuração de armazenamento](#storage-configuration).

### <a name="add-a-new-drive"></a>Adicionar uma nova unidade
Se já tiver configurado o armazenamento na sua VM do SQL Server, expandir o armazenamento aparece duas novas opções. A primeira opção consiste em Adicionar uma nova unidade, pode aumentar o nível de desempenho da VM.

![Adicionar uma nova unidade a uma VM do SQL Server](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-add-new-drive.png)

No entanto, depois de adicionar a unidade, tem de efetuar algum tipo de configuração extra manual para alcançar o aumento do desempenho.

### <a name="extend-the-drive"></a>Expanda a unidade
A outra opção para expandir o armazenamento está a expandir a unidade existente. Esta opção aumenta o armazenamento disponível para a unidade, mas que não a aumentar o desempenho. Com agrupamentos de armazenamento, não é possível alterar o número de colunas depois de criar o agrupamento de armazenamento. O número de colunas determina o número de gravações paralelas, que pode ser repartidas em discos de dados. Por conseguinte, qualquer discos de dados adicionados não é possível aumentar o desempenho. Apenas pode fornecem mais armazenamento para os dados que está a ser escritos. Esta limitação também significa que, quando expandir a unidade, o número de colunas determina o número mínimo de discos de dados que pode adicionar. Por isso, se criar um agrupamento de armazenamento com discos de dados de quatro, o número de colunas também é quatro. Qualquer altura que expandir o armazenamento, tem de adicionar discos de dados, pelo menos, quatro.

![Expandir uma unidade para uma VM do SQL Server](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-extend-a-drive.png)

## <a name="storage-configuration"></a>Configuração do armazenamento
Esta secção fornece uma referência para as alterações de configuração de armazenamento Azure efetua automaticamente durante o aprovisionamento de VM do SQL Server ou a configuração no Portal do Azure.

* Se tiver selecionado menos de dois TBs de armazenamento para a VM, o Azure não criar um agrupamento de armazenamento.
* Se tiver selecionado, pelo menos, dois TBs de armazenamento para a VM, o Azure configura um agrupamento de armazenamento. A secção seguinte deste tópico fornece os detalhes sobre a configuração do agrupamento de armazenamento.
* Configuração de armazenamento automática sempre utiliza [armazenamento premium](../premium-storage.md) P30 discos de dados. Por conseguinte, não há um mapeamento de 1:1 entre o número selecionado de Terabytes e o número de discos de dados ligados à VM.

Para obter informações sobre preços, consulte o [preços do Storage](https://azure.microsoft.com/pricing/details/storage) página o **armazenamento em disco** separador.

### <a name="creation-of-the-storage-pool"></a>Criação do agrupamento de armazenamento
Azure utiliza as seguintes definições para criar o agrupamento de armazenamento em VMs do SQL Server.

| Definição | Valor |
| --- | --- |
| Tamanho do stripe |256 KB (armazenamento de dados); 64 KB (transacional) |
| Tamanhos de disco |1 TB |
| Cache |Leitura |
| Tamanho da alocação |Tamanho de unidade de alocação de NTFS 64 KB |
| Inicialização instantânea de ficheiros |Ativado |
| Páginas de bloqueio na memória |Ativado |
| Recuperação |Recuperação simples (sem resiliência) |
| Número de colunas |Número de discos de dados<sup>1</sup> |
| Localização de TempDB |Armazenados em discos de dados<sup>2</sup> |

<sup>1</sup> depois de criar o agrupamento de armazenamento, não é possível alterar o número de colunas no agrupamento de armazenamento.

<sup>2</sup> esta definição aplica-se apenas a primeira unidade de criar a utilizar a funcionalidade de configuração de armazenamento.

## <a name="workload-optimization-settings"></a>Definições de Otimização da carga de trabalho
A tabela seguinte descreve as opções de tipo de carga de três trabalho disponíveis e as respetivas otimizações correspondentes:

| Tipo de carga de trabalho | Descrição | Otimizações de |
| --- | --- | --- |
| **Geral** |Predefinição que suporta a maioria das cargas de trabalho |Nenhuma |
| **Processamento de transações** |Otimiza o armazenamento para cargas de trabalho OLTP de bases de dados tradicionais |Sinalizador de rastreio 1117<br/>Sinalizador de rastreio 1118 |
| **Armazém de dados** |Otimiza o armazenamento para cargas de trabalho analíticas e de relatórios |Sinalizador de rastreio 610<br/>Sinalizador de rastreio 1117 |

> [!NOTE]
> Apenas pode especificar o tipo de carga de trabalho quando aprovisionar uma máquina virtual do SQL Server, selecionando-o no passo de configuração de armazenamento.
>
>

## <a name="next-steps"></a>Passos seguintes
Para outros tópicos relacionados com a executar o SQL Server em VMs do Azure, consulte [do SQL Server em Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md).
