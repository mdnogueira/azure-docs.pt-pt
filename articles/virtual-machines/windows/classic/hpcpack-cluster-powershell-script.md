---
title: Script do PowerShell para implementar o cluster Windows HPC | Microsoft Docs
description: "Executar um script do PowerShell para implementar um cluster Windows HPC Pack 2012 R2 em máquinas virtuais do Azure"
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-service-management,hpc-pack
ms.assetid: 286b2be8-2533-40df-b02a-26156b1f1133
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 12/29/2016
ms.author: danlep
ms.openlocfilehash: 85b125ab19671b61d2541af6378c95feb88bf952
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-windows-high-performance-computing-hpc-cluster-with-the-hpc-pack-iaas-deployment-script"></a>Criar um Windows cluster de computação de alto desempenho (HPC) com o script de implementação do IaaS do HPC Pack
Execute a script do PowerShell para implementar um cluster HPC Pack 2012 R2 completado para cargas de trabalho do Windows em máquinas virtuais do Azure de implementação do HPC Pack IaaS. O cluster é composta por um nó principal associados ao Active Directory com o Windows Server e o Microsoft HPC Pack e especificar de recursos de computação adicionais do Windows. Se pretender implementar um cluster HPC Pack no Azure para cargas de trabalho do Linux, consulte [criar um cluster Linux HPC com o script de implementação do HPC Pack IaaS](../../linux/classic/hpcpack-cluster-powershell-script.md). Também pode utilizar um modelo Azure Resource Manager para implementar um cluster HPC Pack. Para obter exemplos, consulte [criar um cluster HPC](https://azure.microsoft.com/documentation/templates/create-hpc-cluster/) e [criar um cluster HPC com uma imagem do nó de computação personalizada](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-custom-image/).

> [!IMPORTANT] 
> O script do PowerShell descrito neste artigo cria um cluster do Microsoft HPC Pack 2012 R2 no Azure utilizando o modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager.
> Além disso, o script descrito neste artigo não suporta HPC Pack 2016.

[!INCLUDE [virtual-machines-common-classic-hpcpack-cluster-powershell-script](../../../../includes/virtual-machines-common-classic-hpcpack-cluster-powershell-script.md)]

## <a name="example-configuration-files"></a>Ficheiros de configuração de exemplo
Nos exemplos a seguir, substitua os seus próprios valores para o Id de subscrição ou o nome e os nomes de conta e o serviço.

### <a name="example-1"></a>Exemplo 1
O ficheiro de configuração seguintes implementa um cluster HPC Pack que tem um nó principal com bases de dados locais e computação cinco nós com o sistema operativo do Windows Server 2012 R2. Todos os serviços de nuvem são criados diretamente na localização EUA oeste. O nó principal age como controlador de domínio da floresta de domínio.

```Xml
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionId>08701940-C02E-452F-B0B1-39D50119F267</SubscriptionId>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <Location>West US</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>HeadNodeAsDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%1000%</VMNamePattern>
    <ServiceName>MyHPCCNService</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>5</NodeCount>
    <OSVersion>WindowsServer2012R2</OSVersion>
  </ComputeNodes>
</IaaSClusterConfig>
```

### <a name="example-2"></a>Exemplo 2
O ficheiro de configuração seguintes implementa um cluster HPC Pack numa floresta existente do domínio. O cluster tem 1 nó principal com bases de dados locais e 12 nós de computação com a extensão BGInfo VM aplicada.
Instalação automática de atualizações do Windows está desativada para todas as VMs na floresta do domínio. Todos os serviços de nuvem são criados diretamente na localização Ásia Oriental. Os nós de computação são criados em três serviços de nuvem e de três contas de armazenamento: *MyHPCCN-0001 e* para *MyHPCCN 0005* no *MyHPCCNService01* e *mycnstorage01*; *MyHPCCN 0006* para *MyHPCCN0010* no *MyHPCCNService02* e *mycnstorage02*; e *MyHPCCN 0011* para *MyHPCCN 0012* no *MyHPCCNService03* e *mycnstorage03*). Os nós de computação são criados a partir de uma imagem privada existente capturada a partir de um nó de computação. A automática aumentar e diminuir o serviço está ativado com predefinição aumentar e diminuir a intervalos.

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
     <NoWindowsAutoUpdate />
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <Certificates>
    <Certificate>
      <Id>1</Id>
      <PfxFile>d:\mytestcert1.pfx</PfxFile>
      <Password>MyPsw!!2</Password>
    </Certificate>
  </Certificates>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%0001%</VMNamePattern>
<ServiceNamePattern>MyHPCCNService%01%</ServiceNamePattern>
<MaxNodeCountPerService>5</MaxNodeCountPerService>
<StorageAccountNamePattern>mycnstorage%01%</StorageAccountNamePattern>
    <VMSize>Medium</VMSize>
    <NodeCount>12</NodeCount>
    <ImageName HPCPackInstalled=”true”>MyHPCComputeNodeImage</ImageName>
    <VMExtensions>
       <VMExtension>
          <ExtensionName>BGInfo</ExtensionName>
          <Publisher>Microsoft.Compute</Publisher>
          <Version>1.*</Version>
       </VMExtension>
    </VMExtensions>
  </ComputeNodes>
  <AutoGrowShrink>
    <CertificateId>1</CertificateId>
  </AutoGrowShrink>
</IaaSClusterConfig>

```

### <a name="example-3"></a>Exemplo 3
O ficheiro de configuração seguintes implementa um cluster HPC Pack numa floresta existente do domínio. O cluster contém um nó principal, servidor de uma base de dados com um disco de dados de 500 GB, dois nós do Mediador de executar o sistema operativo do Windows Server 2012 R2 e cinco nós de computação com o sistema operativo do Windows Server 2012 R2. O serviço em nuvem MyHPCCNService é criado no grupo de afinidade *MyIBAffinityGroup*, e os outros serviços em nuvem são criados no grupo de afinidade *MyAffinityGroup*. A API de REST do agendador de tarefas HPC e o HPC web portal estão ativadas no nó principal.

```Xml
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <AffinityGroup>MyAffinityGroup</AffinityGroup>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>    
  <Domain>
    <DCOption>ExistingDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>NewRemoteDB</DBOption>
    <DBVersion>SQLServer2014_Enterprise</DBVersion>
    <DBServer>
      <VMName>MyDBServer</VMName>
      <ServiceName>MyHPCService</ServiceName>
      <VMSize>ExtraLarge</VMSize>
      <DataDiskSizeInGB>500</DataDiskSizeInGB>
    </DBServer>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
    <EnableRESTAPI />
    <EnableWebPortal />
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%0000%</VMNamePattern>
    <ServiceName>MyHPCCNService</ServiceName>
    <VMSize>A8</VMSize>
<NodeCount>5</NodeCount>
<AffinityGroup>MyIBAffinityGroup</AffinityGroup>
  </ComputeNodes>
  <BrokerNodes>
    <VMNamePattern>MyHPCBN-%0000%</VMNamePattern>
    <ServiceName>MyHPCBNService</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>2</NodeCount>
  </BrokerNodes>
</IaaSClusterConfig>
```



### <a name="example-4"></a>Exemplo 4
O ficheiro de configuração seguintes implementa um cluster HPC Pack numa floresta existente do domínio. O cluster tem dois nós head com bases de dados locais, são criados dois modelos de nó do Azure e são criados três nós do Azure médio de tamanho para o modelo do Azure nó *AzureTemplate1*. Um ficheiro de script é executado no nó principal após a configuração do nó principal.

```Xml
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <AffinityGroup>MyAffinityGroup</AffinityGroup>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>ExistingDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
<VMSize>ExtraLarge</VMSize>
    <PostConfigScript>c:\MyHNPostActions.ps1</PostConfigScript>
  </HeadNode>
  <Certificates>
    <Certificate>
      <Id>1</Id>
      <PfxFile>d:\mytestcert1.pfx</PfxFile>
      <Password>MyPsw!!2</Password>
    </Certificate>
    <Certificate>
      <Id>2</Id>
      <PfxFile>d:\mytestcert2.pfx</PfxFile>
    </Certificate>    
  </Certificates>
  <AzureBurst>
    <AzureNodeTemplate>
      <TemplateName>AzureTemplate1</TemplateName>
      <SubscriptionId>bb9252ba-831f-4c9d-ae14-9a38e6da8ee4</SubscriptionId>
      <CertificateId>1</CertificateId>
      <ServiceName>mytestsvc1</ServiceName>
      <StorageAccount>myteststorage1</StorageAccount>
      <NodeCount>3</NodeCount>
      <RoleSize>Medium</RoleSize>
    </AzureNodeTemplate>
    <AzureNodeTemplate>
      <TemplateName>AzureTemplate2</TemplateName>
      <SubscriptionId>ad4b9f9f-05f2-4c74-a83f-f2eb73000e0b</SubscriptionId>
      <CertificateId>1</CertificateId>
      <ServiceName>mytestsvc2</ServiceName>
      <StorageAccount>myteststorage2</StorageAccount>
      <Proxy>
        <UsesStaticProxyCount>false</UsesStaticProxyCount>     
        <ProxyRatio>100</ProxyRatio>
        <ProxyRatioBase>400</ProxyRatioBase>
      </Proxy>
      <OSVersion>WindowsServer2012</OSVersion>
    </AzureNodeTemplate>
  </AzureBurst>
</IaaSClusterConfig>
```

## <a name="troubleshooting"></a>Resolução de problemas
* **Erro "Não existe VNet"** -se que execute o script para implementar vários clusters no Azure em simultâneo com uma subscrição, uma ou mais implementações poderão falhar com o erro "VNet *VNet\_nome* não existe".
  Se este erro ocorrer, execute o script novamente para a implementação com falhas.
* **Problema de acesso à Internet a partir da rede virtual do Azure** - se que crie um cluster com um novo controlador de domínio utilizando o script de implementação, ou manualmente promover um nó principal VM ao controlador de domínio, podem ocorrer problemas ligar as VMs para a Internet. Este problema pode ocorrer se um servidor DNS do reencaminhador é automaticamente configurado no controlador de domínio, e este servidor DNS do reencaminhador não resolve corretamente.
  
    Para contornar este problema, inicie sessão no controlador de domínio e remover a definição de configuração de reencaminhador ou configurar um servidor DNS do reencaminhador válido. Para configurar esta definição, no Gestor de servidores, clique em **ferramentas** >
    **DNS** para abrir o Gestor de DNS e, em seguida, faça duplo clique em **reencaminhadores**.
* **Problema de acesso à rede RDMA de VMs de computação intensiva** - se adicionar computação do Windows Server ou nó do Mediador de tamanho de VMs com uma capacidade RDMA, tais como A8 ou A9, podem ocorrer problemas de ligação dessas VMs para a rede de aplicação de RDMA. Uma razão, que este problema ocorre é se a extensão de HpcVmDrivers não está corretamente instalada quando são adicionadas as VMs para o cluster. Por exemplo, a extensão pode estar bloqueada no estado de instalação.
  
    Para contornar este problema, verifique primeiro o estado da extensão nas VMs. Se a extensão não está instalada corretamente, tente remover nós do HPC cluster e, em seguida, adicione novamente os nós. Por exemplo, pode adicionar o nó de computação VMs ao executar o script de adicionar HpcIaaSNode.ps1 no nó principal.

## <a name="next-steps"></a>Passos seguintes
* Tente executar uma carga de trabalho de teste no cluster. Por exemplo, consulte o HPC Pack [guia de introdução](https://technet.microsoft.com/library/jj884144).
* Para um tutorial para a implementação do cluster de scripts e executar uma carga de trabalho HPC, consulte [começar com um cluster HPC Pack no Azure para executar cargas de trabalho do Excel e SOA](../../virtual-machines-windows-excel-cluster-hpcpack.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Tente ferramentas HPC Pack para iniciar, parar, adicionar e remover nós de computação de um cluster que cria. Consulte [cluster de nós de computação de gerir num pacote HPC no Azure](hpcpack-cluster-node-manage.md).
* Para obter o configurar para submeter tarefas ao cluster a partir de um computador local, consulte [cluster HPC submeter a tarefas num computador local para um pacote HPC no Azure](../../virtual-machines-windows-hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

