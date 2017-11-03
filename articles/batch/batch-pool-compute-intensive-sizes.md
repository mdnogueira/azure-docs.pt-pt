---
title: "Utilize as VMs do intensivas de computação do Azure com o Batch | Microsoft Docs"
description: Como tirar partido de tamanhos de VM com capacidade RDMA ou GPU-ativado em conjuntos do Azure Batch
services: batch
documentationcenter: 
author: dlepow
manager: timlt
editor: 
ms.assetid: 
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2017
ms.author: danlep
ms.openlocfilehash: 7624a905f81024fa87f15164efc56a300843972d
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="use-rdma-capable-or-gpu-enabled-instances-in-batch-pools"></a>Utilize com capacidade RDMA ou preparados para a GPU instâncias em conjuntos do Batch

Para executar determinadas tarefas de lote, poderá tirar partido de tamanhos de VM do Azure foi concebido para computação em grande escala. Por exemplo, para executar várias instâncias [cargas de trabalho MPI](batch-mpi.md), pode escolher A8, A9, ou os tamanhos de série de H possuir uma rede de interface para acesso remoto direto de memória (RDMA). Estes tamanhos de ligam a uma rede de InfiniBand para comunicação entre nós, que pode acelerar a aplicações MPI. Ou, para aplicações de CUDA, pode escolher os tamanhos de série N que incluem gráficos NVIDIA Tesla processamento cartões de unidade (GPU).

Este artigo fornece orientação e exemplos para utilizar alguns dos tamanhos especializados do Azure em conjuntos do Batch. Para obter especificações e em segundo plano, consulte:

* Tamanhos de VM de computação de elevado desempenho ([Linux](../virtual-machines/linux/sizes-hpc.md), [Windows](../virtual-machines/windows/sizes-hpc.md)) 

* Tamanhos de VM GPU-ativado ([Linux](../virtual-machines/linux/sizes-gpu.md), [Windows](../virtual-machines/windows/sizes-gpu.md)) 


## <a name="subscription-and-account-limits"></a>Subscrição e limites de conta

* **Quotas** - [quota de núcleos dedicado por conta do Batch](batch-quota-limit.md#resource-quotas) poderá limitar o número ou tipo de nós, pode adicionar a um conjunto do Batch. É mais provável alcançar uma quota ao escolher com capacidade RDMA, GPU-ativado ou outros tamanhos de VM especializados. Por predefinição, esta quota é 20 núcleos. Uma quota separada aplica-se a [VMs de prioridade baixa](batch-low-pri-vms.md), caso as utilize. 

Se precisar de pedir um aumento de quota, abra uma [pedido de suporte ao cliente online](../azure-supportability/how-to-create-azure-support-request.md) , sem encargos.

* **Disponibilidade de região** - intensivas de computação VMs podem não estar disponíveis nas regiões onde criar as contas do Batch. Para verificar se está disponível um tamanho, consulte o artigo [produtos disponíveis por região](https://azure.microsoft.com/regions/services/).


## <a name="dependencies"></a>Dependências

As capacidades RDMA e GPU dos tamanhos intensivas de computação só são suportadas em determinados sistemas operativos. Consoante o sistema operativo, poderá ter de instalar ou configurar o controlador adicional ou outro software. As tabelas seguintes resumem estas dependências. Consulte os artigos ligados para obter mais detalhes. Para opções para configurar conjuntos do Batch, consulte o artigo neste artigo.


### <a name="linux-pools---virtual-machine-configuration"></a>Conjuntos de Linux - configuração de Máquina Virtual

| Tamanho | Capacidade | Sistemas operativos | Software necessário | Definições do agrupamento |
| -------- | -------- | ----- |  -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/linux/sizes-hpc.md#rdma-capable-instances) | RDMA | Ubuntu 16.04 LTS,<br/>SUSE Linux Enterprise Server 12 HPC, ou<br/>Com base em centOS HPC<br/>(Do azure Marketplace) | Intel MPI 5 | Ativar a comunicação entre nós, desative a execução de tarefas simultâneas |
| [Série de NC *](../virtual-machines/linux/n-series-driver-setup.md#install-cuda-drivers-for-nc-vms) | NVIDIA Tesla K80 GPU | Ubuntu 16.04 LTS,<br/>Red Hat Enterprise Linux 7.3, ou<br/>CentOS-based 7.3<br/>(Do azure Marketplace) | Controladores NVIDIA CUDA Toolkit 9.0 | N/D | 
| [Série NV](../virtual-machines/linux/n-series-driver-setup.md#install-grid-drivers-for-nv-vms) | NVIDIA Tesla M60 GPU | Ubuntu 16.04 LTS,<br/>Red Hat Enterprise Linux 7.3, ou<br/>CentOS-based 7.3<br/>(Do azure Marketplace) | 4.3 de grelha NVIDIA controladores | N/D |

* Conectividade de RDMA em NC24r VMs é suportada no Ubuntu 16.04 LTS ou baseada em CentOS 7.3 HPC (a partir do Azure Marketplace) com Intel MPI.



### <a name="windows-pools---virtual-machine-configuration"></a>Conjuntos de Windows - configuração de Máquina Virtual

| Tamanho | Capacidade | Sistemas operativos | Software necessário | Definições do agrupamento |
| -------- | ------ | -------- | -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/windows/sizes-hpc.md#rdma-capable-instances) | RDMA | Windows Server 2012 R2 ou<br/>Windows Server 2012 (do Azure Marketplace) | Microsoft MPI 2012 R2 ou posterior, ou<br/> Intel MPI 5<br/><br/>Extensão da VM do Azure de HpcVMDrivers | Ativar a comunicação entre nós, desative a execução de tarefas simultâneas |
| [Série de NC *](../virtual-machines/windows/n-series-driver-setup.md) | NVIDIA Tesla K80 GPU | Windows Server 2016 ou <br/>Windows Server 2012 R2 (do Azure Marketplace) | Controladores NVIDIA Tesla ou controladores CUDA Toolkit 9.0| N/D | 
| [Série NV](../virtual-machines/windows/n-series-driver-setup.md) | NVIDIA Tesla M60 GPU | Windows Server 2016 ou<br/>Windows Server 2012 R2 (do Azure Marketplace) | 4.3 de grelha NVIDIA controladores | N/D |

* Conectividade de RDMA em NC24r VMs é suportada no Windows Server 2012 R2 (a partir do Azure Marketplace) com a extensão de HpcVMDrivers e Microsoft MPI ou Intel MPI.

### <a name="windows-pools---cloud-services-configuration"></a>Conjuntos de Windows - configuração de serviços Cloud

> [!NOTE]
> Série N tamanhos não são suportados em conjuntos do Batch com a configuração de serviços em nuvem.
>

| Tamanho | Capacidade | Sistemas operativos | Software necessário | Definições do agrupamento |
| -------- | ------- | -------- | -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/windows/sizes-hpc.md#rdma-capable-instances) | RDMA | Windows Server 2012 R2<br/>Windows Server 2012, ou<br/>Windows Server 2008 R2 (família de SO convidado) | Microsoft MPI 2012 R2 ou posterior, ou<br/>Intel MPI 5<br/><br/>Extensão da VM do Azure de HpcVMDrivers | Ativar a comunicação entre nós,<br/> Desative a execução de tarefas simultâneas |





## <a name="pool-configuration-options"></a>Opções de configuração do agrupamento

Para configurar um tamanho VM especializado para o conjunto do Batch, as APIs do Batch e ferramentas fornecem várias opções para instalar o software necessário ou de controladores, incluindo:

* [Iniciar a tarefa](batch-api-basics.md#start-task) -carregar um pacote de instalação como um ficheiro de recursos para uma conta de armazenamento do Azure na mesma região que a conta do Batch. Crie uma linha de comandos da tarefa de início para instalar o ficheiro de recursos automaticamente quando inicia o conjunto. Para obter mais informações, consulte o [documentação da REST API](/rest/api/batchservice/add-a-pool-to-an-account#bk_starttask).

  > [!NOTE] 
  > A tarefa de início deve ser executado com permissões de elevada (administrador) e tem de aguardar com êxito.
  >

* [Pacote de aplicação](batch-application-packages.md) - adicionar um pacote de instalação zipped à sua conta do Batch e configurar uma referência de pacote no conjunto. Esta definição carrega e unzips o pacote em todos os nós no conjunto. Se o pacote de um programa de instalação, crie uma linha de comandos da tarefa de início para instalar automaticamente a aplicação em todos os nós do conjunto. Opcionalmente, instale o pacote quando uma tarefa é agendada para ser executada num nó.

* [Imagem de agrupamento personalizados](batch-custom-images.md) - criar uma imagem do Windows ou a VM com Linux personalizada que contém controladores, software, ou outras definições necessárias para o tamanho da VM. 

* [Lote Shipyard](https://github.com/Azure/batch-shipyard) configura automaticamente a GPU e o RDMA para trabalhar de forma transparente com de cargas de trabalho do Azure batch. Batch Shipyard inteiramente é suscitada pelo departamento com ficheiros de configuração. Existem muitos exemplo receitas configurações disponíveis que permitem GPU e no RDMA cargas de trabalho, tais como o [CNTK GPU receitas](https://github.com/Azure/batch-shipyard/tree/master/recipes/CNTK-GPU-OpenMPI) que pré-configura os controladores GPU em série N VMs e carrega o Toolkit cognitivos Microsoft software como uma imagem de Docker.


## <a name="example-microsoft-mpi-on-an-a8-vm-pool"></a>Exemplo: Microsoft MPI num conjunto de A8 VM

Para executar aplicações de Windows MPI num conjunto de nós de A8 do Azure, terá de instalar uma implementação de MPI suportada. Seguem-se passos de exemplo para instalar [Microsoft MPI](https://msdn.microsoft.com/library/bb524831(v=vs.85).aspx) no Windows agrupamento com um pacote de aplicação do Batch.

1. Transferir o [o pacote de configuração](http://go.microsoft.com/FWLink/p/?LinkID=389556) (MSMpiSetup.exe) para a versão mais recente do Microsoft MPI.
2. Crie um ficheiro zip do pacote.
3. Carregar o pacote para a sua conta do Batch. Para obter os passos, consulte o [pacotes de aplicações](batch-application-packages.md) orientações. Especifique um id de aplicação, como *MSMPI*e uma versão como *8.1*. 
4. Utilizar as APIs do Batch ou o portal do Azure, crie um conjunto na configuração de serviços da nuvem com o número pretendido de nós e o dimensionamento. A tabela seguinte mostra as definições de exemplo para configurar MPI no modo automático utilizando uma tarefa de início:

| Definição | Valor |
| ---- | ----- | 
| **Tipo de imagem** | Serviços Cloud |
| **Família de SO** | Windows Server 2012 R2 (família de SO 4) |
| **Tamanho de nó** | A8 padrão |
| **Comunicação internós ativada** | Verdadeiro |
| **Máx. tarefas por nó** | 1 |
| **Referências do pacote de aplicação** | MSMPI |
| **Iniciar a tarefa ativada** | Verdadeiro<br>**Linha de comandos** - `"cmd /c %AZ_BATCH_APP_PACKAGE_MSMPI#8.1%\\MSMpiSetup.exe -unattend -force"`<br/>**A identidade do utilizador** -autouser de agrupamento, admin<br/>**Aguarde êxito** - verdadeiro

## <a name="example-nvidia-tesla-drivers-on-nc-vm-pool"></a>Exemplo: Controladores NVIDIA Tesla no conjunto de VM de NC

Para executar aplicações de CUDA num conjunto de nós de NC do Linux, tem de instalar CUDA Toolkit 9.0 em nós. O Toolkit instala os controladores de NVIDIA Tesla GPU necessários. Seguem-se passos de exemplo para implementar uma imagem personalizada do Ubuntu 16.04 LTS com os controladores da GPU:

1. Implemente a VM do Azure NC6 Ubuntu 16.04 LTS a executar. Por exemplo, crie a VM na região dos EUA Sul Central. Certifique-se de que criar a VM com um disco gerido.
2. Siga os passos para ligar à VM e [instalar controladores CUDA](../virtual-machines/linux/n-series-driver-setup.md#install-cuda-drivers-for-nc-vms).
3. Desaprovisionar o agente do Linux e, em seguida, [capturar a imagem de VM com Linux](../virtual-machines/linux/capture-image.md).
4. Crie uma conta do Batch numa região que suporta VMs de NC.
5. Utilizar as APIs do Batch ou o portal do Azure, criar um conjunto [utilizando a imagem personalizada](batch-custom-images.md) e com o número pretendido de nós e o dimensionamento. A tabela seguinte mostra as definições de agrupamento de exemplo para a imagem:

| Definição | Valor |
| ---- | ---- |
| **Tipo de imagem** | Imagem personalizada |
| **Imagem personalizada** | Nome da imagem |
| **SKU do agente de nó** | batch.node.ubuntu 16.04 |
| **Tamanho de nó** | NC6 padrão |



## <a name="next-steps"></a>Passos seguintes

* Para executar tarefas MPI num conjunto do Azure Batch, consulte o [Windows](batch-mpi.md) ou [Linux](https://blogs.technet.microsoft.com/windowshpc/2016/07/20/introducing-mpi-support-for-linux-on-azure-batch/) exemplos.

* Para obter exemplos de cargas de trabalho para a GPU do batch, consulte o [Batch Shipyard](https://github.com/Azure/batch-shipyard/) receitas.