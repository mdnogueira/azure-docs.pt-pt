---
title: "Opções de cluster HPC Pack do Linux no Azure | Microsoft Docs"
description: "Saiba mais sobre as opções de com o Microsoft HPC Pack para criar e gerir um Linux elevado desempenho computação em cluster (HPC) na nuvem do Azure"
services: virtual-machines-linux,cloud-services
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management,hpc-pack
ms.assetid: ac60624e-aefa-40c3-8bc1-ef6d5c0ef1a2
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: big-compute
ms.date: 10/26/2017
ms.author: danlep
ms.openlocfilehash: ae36e64c0261b1fe8d02d6dcb80df7cdee333db9
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2017
---
# <a name="options-with-hpc-pack-to-create-and-manage-a-cluster-for-linux-hpc-workloads-in-azure"></a>Opções com o HPC Pack para criar e gerir um cluster para cargas de trabalho do Linux HPC no Azure
[!INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

Este artigo foca-se nas opções para utilizar o pacote HPC para executar cargas de trabalho do Linux. Também existem opções para executar [cargas de trabalho do Windows HPC com o HPC Pack](../windows/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="hpc-pack-cluster-in-azure-vms-and-vm-scale-sets"></a>Cluster HPC Pack em VMs do Azure e os conjuntos de dimensionamento VM
### <a name="azure-templates"></a>Modelos do Azure
* (GitHub) [Modelos de cluster HPC Pack 2016](https://github.com/MsHpcPack/HPCPack2016)
* (GitHub) [Modelos de cluster HPC Pack 2012 R2](https://github.com/MsHpcPack/HPCPack2012R2)
* (Marketplace) [Cluster HPC Pack para cargas de trabalho do Linux](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/)
* (Início rápido) [Criar um cluster HPC connosco de computação do Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster-linux-cn)

### <a name="powershell-deployment-script-for-hpc-pack-2012-r2"></a>Script de implementação do PowerShell para o HPC Pack 2012 R2
* [Criar um cluster Linux HPC com o script de implementação do IaaS do HPC Pack](../windows/classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)

### <a name="tutorials"></a>Tutoriais
* [Tutorial: Introdução a nós de computação do Linux num cluster HPC Pack no Azure](classic/hpcpack-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Tutorial: Executar NAMD com o Microsoft HPC Pack no Linux nós de computação em do Azure](classic/hpcpack-cluster-namd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Tutorial: Executar OpenFOAM com o Microsoft HPC Pack num cluster Linux RDMA no Azure](classic/hpcpack-cluster-openfoam.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Tutorial: Executar ESTRELA-CCM + com o Microsoft HPC Pack num RDMA Linux cluster no Azure](classic/hpcpack-cluster-starccm.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)

### <a name="cluster-management"></a>Gestão de clusters
* [Submeter as tarefas para um cluster HPC Pack no Azure](../windows/hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Gestão de tarefas no HPC Pack](https://technet.microsoft.com/library/jj899585.aspx)

## <a name="rdma-clusters-for-mpi-workloads"></a>Clusters RDMA para cargas de trabalho MPI
* [Tutorial: Executar OpenFOAM com o Microsoft HPC Pack num cluster Linux RDMA no Azure](classic/hpcpack-cluster-openfoam.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Configurar um cluster de Linux RDMA para executar aplicações MPI](classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)

