---
title: "Opções de cluster Windows HPC Pack no Azure | Microsoft Docs"
description: "Saiba mais sobre as opções de com o Microsoft HPC Pack para criar e gerir um desempenho elevado do Windows computação em cluster (HPC) na nuvem do Azure"
services: virtual-machines-windows,cloud-services,batch
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management,hpc-pack
ms.assetid: 02c5566d-2129-483c-9ecf-0d61030442d7
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 10/26/2017
ms.author: danlep
ms.openlocfilehash: 3dd00d4fb0d334e836256e66207358c034fc005a
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2017
---
# <a name="options-with-hpc-pack-to-create-and-manage-a-cluster-for-windows-hpc-workloads-in-azure"></a>Opções com o HPC Pack para criar e gerir um cluster para cargas de trabalho do Windows HPC no Azure
[!INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

Este artigo foca-se nas opções para criar clusters HPC Pack executar cargas de trabalho do Windows. Também existem opções para a criação de clusters HPC Pack para executar [cargas de trabalho Linux HPC](../linux/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="hpc-pack-cluster-in-azure-vms-and-vm-scale-sets"></a>Cluster HPC Pack em VMs do Azure e os conjuntos de dimensionamento VM
### <a name="azure-templates"></a>Modelos do Azure
* (GitHub) [Modelos de cluster HPC Pack 2016](https://github.com/MsHpcPack/HPCPack2016)
* (GitHub) [Modelos de cluster HPC Pack 2012 R2](https://github.com/MsHpcPack/HPCPack2012R2)
* (Marketplace) [Cluster HPC Pack para cargas de trabalho do Windows](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterwindowscn/)
* (Marketplace) [Cluster HPC Pack para cargas de trabalho do Excel](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterexcelcn/)
* (Início rápido) [Criar um cluster HPC](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster)
* (Início rápido) [Criar um cluster HPC com a imagem do nó de computação personalizada](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster-custom-image)

### <a name="azure-vm-images"></a>Imagens VM do Azure
* [Nó principal do HPC Pack 2016 no Windows Server 2016](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.HPCPack2016HeadNodeonWindowsServer2016?tab=Overview)
* [Nó de computação de HPC Pack 2016 no Windows Server 2016](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.HPCPack2016ComputeNodeonWindowsServer2016?tab=Overview)
* [Nó principal do HPC Pack 2016 no Windows Server 2012 R2](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.HPCPack2016HeadNodeonWindowsServer2012R2?tab=Overview)
* [Nó de computação de HPC Pack 2016 no Windows Server 2012 R2](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.HPCPack2016ComputeNodeonWindowsServer2012R2?tab=Overview)
* [Nó principal do HPC Pack 2012 R2 no Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/)
* [Nó de computação de HPC Pack 2012 R2 no Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodeonwindowsserver2012r2/)
* [Pacote HPC computação nó com o Excel no Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodewithexcelonwindowsserver2012r2/)

### <a name="powershell-deployment-script-for-hpc-pack-2012-r2"></a>Script de implementação do PowerShell para o HPC Pack 2012 R2
* [Criar um cluster HPC com o script de implementação do IaaS do HPC Pack](classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

### <a name="tutorials"></a>Tutoriais
* [Tutorial: Implementar um cluster HPC Pack 2016 no Azure](hpcpack-2016-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Tutorial: Introdução com um cluster HPC Pack no Azure para executar cargas de trabalho do Excel e SOA](excel-cluster-hpcpack.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="manual-deployment-with-the-azure-portal"></a>Implementação manual com o portal do Azure
* [Configurar o nó principal de um cluster HPC Pack numa VM do Azure](hpcpack-cluster-headnode.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="cluster-management"></a>Gestão de clusters
* [Gerir os nós de computação de um cluster HPC Pack no Azure](classic/hpcpack-cluster-node-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Aumentar e diminuir a recursos de computação do Azure num cluster HPC Pack](classic/hpcpack-cluster-node-autogrowshrink.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Submeter as tarefas para um cluster HPC Pack no Azure](hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Gestão de tarefas no HPC Pack](https://technet.microsoft.com/library/jj899585.aspx)
* [Gerir um cluster HPC Pack 2016 no Azure com o Azure Active Directory](hpcpack-cluster-active-directory.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a name="burst-with-worker-role-nodes"></a>Burst connosco de função de trabalho 
* [Impulsar para instâncias de trabalho do Azure com o HPC Pack](https://technet.microsoft.com/library/gg481749.aspx)
* [Tutorial: Configurar um cluster de híbrido com o HPC Pack no Azure](../../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)
* [Adicionar nós do Azure "rajada" para um nó principal do pacote HPC no Azure](classic/hpcpack-cluster-node-burst.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a name="burst-with-azure-batch"></a>Burst com o Azure Batch
* [Aceder ao Azure Batch com o HPC Pack](https://technet.microsoft.com/library/mt612877.aspx)

## <a name="rdma-clusters-for-mpi-workloads"></a>Clusters RDMA para cargas de trabalho MPI
* [Configurar um cluster RDMA do Windows com HPC Pack para executar aplicações MPI](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

