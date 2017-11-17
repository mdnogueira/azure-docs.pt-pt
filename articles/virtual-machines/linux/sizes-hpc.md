---
title: Tamanhos de VM Linux do Azure - HPC | Microsoft Docs
description: "Lista os tamanhos diferentes disponíveis para Linux elevado desempenho informática máquinas virtuais no Azure. Apresenta informações sobre o número de vCPUs, discos de dados e NICs, bem como armazenamento débito e a rede de largura de banda para tamanhos de nesta série."
services: virtual-machines-linux
documentationcenter: 
author: jonbeck7
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/08/2017
ms.author: jonbeck
ms.openlocfilehash: a235261dc477ab29dc17fe1e680e4e91ae2f4ede
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2017
---
# <a name="high-performance-compute-virtual-machine-sizes"></a>Tamanhos de máquinas virtuais de computação de elevado desempenho

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## <a name="rdma-capable-instances"></a>Instâncias com capacidade RDMA
Uma interface de rede para a conectividade de (RDMA) de acesso remoto direto à memória de funcionalidade de um subconjunto das instâncias intensivas de computação (H16r, H16mr, A8 e A9). Esta interface está além da interface de rede do Azure padrão disponível para outros tamanhos de VM. 
  
Esta interface que permite que as instâncias comunicar através de uma rede InfiniBand, operativo FDR as taxas de máquinas virtuais H16r e H16mr e QDR as taxas de A8 e A9 as máquinas virtuais com capacidade RDMA. Estas capacidades RDMA podem melhorar a escalabilidade e o desempenho de aplicações de Interface de passagem de mensagens (MPI) em execução em Intel MPI 5. x ou uma versão posterior.

Implemente as VMs com capacidade RDMA no mesmo conjunto de disponibilidade (ao utilizar o modelo de implementação Azure Resource Manager) ou o mesmo serviço de nuvem (quando utiliza o modelo de implementação clássica). Siga a requisitos adicionais para VMs com Linux com capacidade RDMA à rede Azure RDMA.

### <a name="distributions"></a>Distribuições
 
Implemente uma VM de computação intensivas de uma das imagens no Azure Marketplace que suporte a conectividade RDMA:
  
* **Ubuntu** -Ubuntu Server 16.04 LTS. Configurar controladores RDMA na VM e registe o Intel para transferir Intel MPI:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]

* **SUSE Linux Enterprise Server** -SLES 12 SP3 para HPC, SLES 12 SP3 para HPC (Premium), SLES 12 SP1 para HPC, SLES 12 SP1 para HPC (Premium). RDMA controladores estão instalados e pacotes de Intel MPI são distribuídas na VM. Instale MPI executando o seguinte comando:

  ```bash
  sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
  ```
    
* **Com base em centOS HPC** -com base em CentOS 7.3 HPC, com base em CentOS 7.1 HPC, com base em CentOS 6.8 HPC ou baseada em CentOS HPC 6.5 (para a série H, versão 7.1 ou posterior é recomendado). Controladores RDMA e Intel MPI 5.1 são instaladas na VM.  
 
  > [!NOTE]
  > Nas imagens de HPC com base em CentOS, atualizações de kernel estão desativadas no **yum** ficheiro de configuração. Isto acontece porque os controladores de Linux RDMA são distribuídos como um pacote RPM e atualizações de controladores poderão não funcionar se kernel é atualizado.
  > 
 
### <a name="cluster-configuration"></a>Configuração de cluster 
    
É necessária configuração de sistema adicionais para executar tarefas MPI em VMs em cluster. Por exemplo, num cluster de VMs, tem de estabelecer fidedignidade entre os nós de computação. Para as definições típicas, consulte [configurar um cluster de Linux RDMA para executar aplicações MPI](classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

### <a name="network-topology-considerations"></a>Considerações de topologia de rede
* Em com capacidade RDMA VMs com Linux no Azure, Eth1 está reservado para o tráfego de rede RDMA. Não altere as definições de Eth1 ou quaisquer informações no ficheiro de configuração que faça referência a esta rede. Eth0 está reservado para regular tráfego de rede do Azure.

* No Azure, o IP através de InfiniBand (IB) não é suportada. Apenas RDMA sobre IB é suportada.

## <a name="using-hpc-pack"></a>Utilizar o pacote HPC
[Pacote HPC](https://technet.microsoft.com/library/jj899572.aspx), gratuita HPC cluster e a tarefa de solução de gestão da Microsoft, é uma opção para utilizar as instâncias intensivas de computação com Linux. As versões mais recentes do suporte de HPC Pack várias distribuições em Linux para executar em nós de computação do implementado em VMs do Azure, gerido por um nó principal do Windows Server. Connosco de computação do Linux com capacidade RDMA com Intel MPI, HPC Pack pode agendar e executar Linux MPI aplicações que acedam à rede RDMA. Consulte [começar connosco de computação do Linux num cluster HPC Pack no Azure](classic/hpcpack-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

## <a name="other-sizes"></a>Outros tamanhos de
- [Fins gerais](sizes-general.md)
- [Com otimização de computação](sizes-compute.md)
- [Com otimização de memória](sizes-memory.md)
- [Com otimização de armazenamento](sizes-storage.md)
- [GPU](../windows/sizes-gpu.md)


## <a name="next-steps"></a>Passos seguintes

- Para começar a implementar e utilizar tamanhos de computação intensiva com o RDMA no Linux, consulte [configurar um cluster de Linux RDMA para executar aplicações MPI](classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

- Saiba mais sobre como [unidades (ACU) de computação do Azure](acu.md) podem ajudar a comparar o desempenho de computação em SKUs do Azure.




