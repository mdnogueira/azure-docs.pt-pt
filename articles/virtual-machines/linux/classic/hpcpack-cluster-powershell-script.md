---
title: Script do PowerShell para implementar o cluster HPC do Linux | Microsoft Docs
description: "Executar um script do PowerShell para implementar um cluster de Linux HPC Pack 2012 R2 em máquinas virtuais do Azure"
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-service-management,hpc-pack
ms.assetid: 73041960-58d3-4ecf-9540-d7e1a612c467
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: big-compute
ms.date: 12/29/2016
ms.author: danlep
ms.openlocfilehash: c15dc66718a855e22f8109448cb8c8a23787b9bf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-linux-high-performance-computing-hpc-cluster-with-the-hpc-pack-iaas-deployment-script"></a>Criar um cluster de computação de alto desempenho (HPC) de Linux com o script de implementação do IaaS do HPC Pack
Execute a script do PowerShell para implementar um cluster HPC Pack 2012 R2 completado para cargas de trabalho do Linux em máquinas virtuais do Azure de implementação do HPC Pack IaaS. O cluster é constituído por um nó principal associados ao Active Directory com o Windows Server e o Microsoft HPC Pack e nós de computação que execute um das distribuições de Linux suportadas pelo pacote HPC. Se pretender implementar um cluster HPC Pack em cargas de trabalho do Azure para Windows, consulte [criar um cluster Windows HPC com o script de implementação do HPC Pack IaaS](../../windows/classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Também pode utilizar um modelo Azure Resource Manager para implementar um cluster HPC Pack. Por exemplo, consulte [criar um cluster HPC connosco de computação do Linux](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/).

> [!IMPORTANT] 
> O script do PowerShell descrito neste artigo cria um cluster do Microsoft HPC Pack 2012 R2 no Azure utilizando o modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager.
> Além disso, o script descrito neste artigo não suporta HPC Pack 2016.

[!INCLUDE [virtual-machines-common-classic-hpcpack-cluster-powershell-script](../../../../includes/virtual-machines-common-classic-hpcpack-cluster-powershell-script.md)]

## <a name="example-configuration-file"></a>Ficheiro de configuração de exemplo
O ficheiro de configuração seguinte cria um controlador de domínio e floresta de domínio e implementa um cluster HPC Pack que tem um nó principal com bases de dados locais e 10 nós de computação do Linux. Todos os serviços de nuvem são criados diretamente na localização Ásia Oriental. Os nós de computação do Linux são criados em dois serviços de nuvem e duas contas de armazenamento (ou seja, *MyLnxCN-0001 e* para *MyLnxCN 0005* no *MyLnxCNService01* e *mylnxstorage01*, e *MyLnxCN 0006* para *MyLnxCN 0010* no *MyLnxCNService02* e *mylnxstorage02* ). Os nós de computação são criados a partir de uma imagem de Linux do OpenLogic CentOS versão 7.0. 

Substitua os seus próprios valores para o nome da sua subscrição e os nomes de conta e o serviço.

```Xml
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>NewDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
    <DomainController>
      <VMName>MyDCServer</VMName>
      <ServiceName>MyHPCService</ServiceName>
      <VMSize>Large</VMSize>
    </DomainController>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>MyLnxCN-%0001%</VMNamePattern>
    <ServiceNamePattern>MyLnxCNService%01%</ServiceNamePattern>
    <MaxNodeCountPerService>5</MaxNodeCountPerService>
    <StorageAccountNamePattern>mylnxstorage%01%</StorageAccountNamePattern>
    <VMSize>Medium</VMSize>
    <NodeCount>10</NodeCount>
    <ImageName>5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20150325 </ImageName>
  </LinuxComputeNodes>
</IaaSClusterConfig>
```
## <a name="troubleshooting"></a>Resolução de problemas
* **Erro "Não existe VNet"**. Se executar o script de implementação de HPC Pack IaaS para implementar vários clusters no Azure em simultâneo com uma subscrição, uma ou mais implementações podem falhar com o erro "VNet *VNet\_nome* não existe".
  Se este erro ocorrer, execute novamente o script para a implementação com falhas.
* **Problema de acesso à Internet a partir da rede virtual do Azure**. Se criar um cluster HPC Pack com um novo controlador de domínio utilizando o script de implementação ou promover manualmente um nó principal VM ao controlador de domínio, pode deparar-se problemas ligar as VMs na rede virtual do Azure para a Internet. Isto pode ocorrer se um servidor DNS do reencaminhador é automaticamente configurado no controlador de domínio, e este servidor DNS do reencaminhador não resolve corretamente.
  
    Para contornar este problema, inicie sessão no controlador de domínio e remover a definição de configuração de reencaminhador ou configurar um servidor DNS do reencaminhador válido. Para tal, no Gestor de servidores, clique em **ferramentas** > **DNS** para abrir o Gestor de DNS e, em seguida, faça duplo clique em **reencaminhadores**.

## <a name="next-steps"></a>Passos seguintes
* Consulte [começar connosco de computação do Linux num cluster HPC Pack no Azure](hpcpack-cluster.md) para obter informações sobre as distribuições Linux suportadas, mover dados e submeter as tarefas para um cluster HPC Pack com o Linux nós de computação.
* Para tutoriais que utilizam o script para criar um cluster e executar uma carga de trabalho do Linux HPC, consulte:
  * [Executar NAMD com o Microsoft HPC Pack em nós de computação do Linux no Azure](hpcpack-cluster-namd.md)
  * [Executar OpenFOAM com o Microsoft HPC Pack em nós de computação do Linux no Azure](hpcpack-cluster-openfoam.md)
  * [Executar ESTRELA-CCM + com o Microsoft HPC Pack no Linux nós de computação no Azure](hpcpack-cluster-starccm.md)

