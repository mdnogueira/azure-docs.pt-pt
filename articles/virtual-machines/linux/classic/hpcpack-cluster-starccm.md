---
title: Executar ESTRELA-CCM + com o HPC Pack em VMs do Linux | Microsoft Docs
description: "Implementar um cluster do Microsoft HPC Pack no Azure e executar uma ESTRELA-CCM + tarefa no Linux vários nós de computação através de uma rede RDMA."
services: virtual-machines-linux
documentationcenter: 
author: xpillons
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager,hpc-pack
ms.assetid: 75523406-d268-4623-ac3e-811c7b74de4b
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: big-compute
ms.date: 09/13/2016
ms.author: xpillons
ms.openlocfilehash: b45fcfb981287035da02fda62eaf5f9436ec2379
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="run-star-ccm-with-microsoft-hpc-pack-on-a-linux-rdma-cluster-in-azure"></a>Executar ESTRELA-CCM + com o Microsoft HPC Pack num RDMA Linux cluster no Azure
Este artigo mostra como implementar um cluster do Microsoft HPC Pack no Azure e execute um [CD adapco ESTRELA-CCM +](http://www.cd-adapco.com/products/star-ccm%C2%AE) tarefa em diversos nós de computação do Linux que estão interligados com InfiniBand.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

Microsoft HPC Pack fornece funcionalidades para executar uma variedade de HPC em grande escala e aplicações paralelas, incluindo aplicações MPI, em clusters de máquinas virtuais do Microsoft Azure. Pacote HPC também suporta aplicações Linux HPC em execução em VMs de nó de computação do Linux que são implementadas num cluster HPC Pack. Para uma introdução à utilização de nós de computação do Linux com o HPC Pack, consulte [começar connosco de computação do Linux num cluster HPC Pack no Azure](hpcpack-cluster.md).

## <a name="set-up-an-hpc-pack-cluster"></a>Configurar um cluster HPC Pack
Transfira os scripts de implementação do HPC Pack IaaS o [Centro de transferências](https://www.microsoft.com/en-us/download/details.aspx?id=44949) e extraia-los localmente.

O Azure PowerShell é um pré-requisito. Se o PowerShell não está configurado no seu computador local, leia o artigo [como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

No momento desta redação, as imagens de Linux no Azure Marketplace (que contém os controladores de InfiniBand para o Azure) são para o SLES 12, CentOS 6.5 e CentOS 7.1. Este artigo baseia-se a utilização do SLES 12. Para obter o nome de todas as imagens de Linux que suportam HPC no Marketplace, pode executar o seguinte comando do PowerShell:

```
    get-azurevmimage | ?{$_.ImageName.Contains("hpc") -and $_.OS -eq "Linux" }
```

O resultado da lista a localização em que estas imagens estão disponíveis e o nome de imagem (**ImageName**) a ser utilizado no modelo de implementação mais tarde.

Antes de implementar o cluster, terá de criar um ficheiro de modelo de implementação de pacote HPC. Porque é filtragem de um cluster pequeno, o nó principal será o controlador de domínio e alojar uma base de dados local do SQL Server.

O modelo seguinte, irá implementar um nó principal, crie um ficheiro XML com o nome **MyCluster.xml**e substitua os valores de **SubscriptionId**, **StorageAccount**,  **Localização**, **VMName**, e **ServiceName** com seu.

    <?xml version="1.0" encoding="utf-8" ?>
    <IaaSClusterConfig>
      <Subscription>
        <SubscriptionId>99999999-9999-9999-9999-999999999999</SubscriptionId>
        <StorageAccount>mystorageaccount</StorageAccount>
      </Subscription>
      <Location>North Europe</Location>
      <VNet>
        <VNetName>hpcvnetne</VNetName>
        <SubnetName>subnet-hpc</SubnetName>
      </VNet>
      <Domain>
        <DCOption>HeadNodeAsDC</DCOption>
        <DomainFQDN>hpc.local</DomainFQDN>
      </Domain>
      <Database>
        <DBOption>LocalDB</DBOption>
      </Database>
      <HeadNode>
        <VMName>myhpchn</VMName>
        <ServiceName>myhpchn</ServiceName>
        <VMSize>Standard_D4</VMSize>
      </HeadNode>
      <LinuxComputeNodes>
        <VMNamePattern>lnxcn-%0001%</VMNamePattern>
        <ServiceNamePattern>mylnxcn%01%</ServiceNamePattern>
        <MaxNodeCountPerService>20</MaxNodeCountPerService>
        <StorageAccountNamePattern>mylnxstorage%01%</StorageAccountNamePattern>
        <VMSize>A9</VMSize>
        <NodeCount>0</NodeCount>
        <ImageName>b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708</ImageName>
      </LinuxComputeNodes>
    </IaaSClusterConfig>

Inicie a criação de nó principal ao executar o comando do PowerShell na linha de comandos elevada:

```
    .\New-HPCIaaSCluster.ps1 -ConfigFile MyCluster.xml
```

Após 20 a 30 minutos, o nó principal deve estar pronto. Pode ligar ao mesmo do portal do Azure, clicando a **Connect** ícone da máquina virtual.

Poderão, eventualmente, tem de corrigir o reencaminhador DNS. Para tal, inicie o Gestor de DNS.

1. Com o nome do servidor no Gestor de DNS, selecione o botão direito **propriedades**e, em seguida, clique em de **reencaminhadores** separador.
2. Clique em de **editar** botão Remover quaisquer reencaminhadores e, em seguida, clique em **OK**.
3. Certifique-se de que o **utilizar sugestões de raiz, se nenhuma reencaminhadores estão disponíveis** caixa de verificação está selecionada e, em seguida, clique em **OK**.

## <a name="set-up-linux-compute-nodes"></a>Configurar nós de computação do Linux
Implementar os nós de computação do Linux, utilizando o mesmo modelo de implementação que utilizou para criar o nó principal.

Copie o ficheiro **MyCluster.xml** do seu computador local para o nó principal e atualize o **NodeCount** etiqueta com o número de nós que pretende implementar (< = 20). Tenha o cuidado de ter suficiente núcleos disponíveis da sua quota do Azure, porque cada instância de A9 irá consumir 16 núcleos na sua subscrição. Pode utilizar instâncias A8 (8 núcleos) em vez de A9 se pretender utilizar mais VMs na atribuição do mesma.

No nó principal, copie os scripts de implementação do HPC Pack IaaS.

Execute os seguintes comandos do Azure PowerShell na linha de comandos elevada:

1. Executar **Add-AzureAccount** para ligar à sua subscrição do Azure.
2. Se tiver várias subscrições, execute **Get-AzureSubscription** para listá-los.
3. Defina uma subscrição predefinida ao executar o **Select-AzureSubscription - SubscriptionName xxxx-predefinição** comando.
4. Executar **.\New-HPCIaaSCluster.ps1 - ConfigFile MyCluster.xml** para iniciar a implementação de nós de computação do Linux.
   
   ![Implementação do nó principal numa ação][hndeploy]

Abra a ferramenta Gestor de clusters HPC Pack. Após alguns minutos, nós de computação do Linux regularmente irão aparecer na lista de nós de computação de cluster. Com o modo de implementação clássica, VMs de IaaS são criadas sequencialmente. Por isso, se o número de nós é importante, obtê-las todas implementadas pode demorar uma quantidade significativa de tempo.

![Nós do Linux no Gestor de clusters do pacote HPC][clustermanager]

Agora que todos os nós estão em execução no cluster, existem definições de infraestrutura adicionais para se.

## <a name="set-up-an-azure-file-share-for-windows-and-linux-nodes"></a>Configurar uma partilha de ficheiros do Azure para nós do Windows e Linux
Pode utilizar o serviço de ficheiros do Azure para armazenar ficheiros de dados, pacotes de aplicações e scripts. Ficheiros do Azure fornecem capacidades CIFS por cima do Blob storage do Azure como um arquivo persistente. Tenha em atenção que esta não é a solução mais dimensionável, mas este é o mais simples e não necessita de VMs dedicadas.

Criar uma partilha de ficheiros do Azure ao seguir as instruções no artigo [introdução ao File storage do Azure no Windows](../../../storage/files/storage-dotnet-how-to-use-files.md).

Mantenha o nome da sua conta de armazenamento como **saname**, o nome da partilha de ficheiros como **sharename**e a chave de conta de armazenamento como **sakey**.

### <a name="mount-the-azure-file-share-on-the-head-node"></a>Montar a partilha de ficheiros do Azure no nó principal
Abra uma linha de comandos elevada e execute o seguinte comando para armazenar as credenciais no cofre do computador local:

```
    cmdkey /add:<saname>.file.core.windows.net /user:<saname> /pass:<sakey>
```

Em seguida, para montar a partilha de ficheiros do Azure, execute:

```
    net use Z: \\<saname>.file.core.windows.net\<sharename> /persistent:yes
```

### <a name="mount-the-azure-file-share-on-linux-compute-nodes"></a>Montar a partilha de ficheiros do Azure em nós de computação do Linux
Uma ferramenta útil que vem com o HPC Pack é a ferramenta de clusrun. Pode utilizar esta ferramenta da linha de comandos para executar o mesmo comando em simultâneo num conjunto de nós de computação. No nosso caso, é utilizado para montar a partilha de ficheiros do Azure e manter a sobreviver a reinícios.
Na linha de comandos elevada no nó principal, execute os seguintes comandos.

Para criar o diretório de montagem:

```
    clusrun /nodegroup:LinuxNodes mkdir -p /hpcdata
```

Para montar a partilha de ficheiros do Azure:

```
    clusrun /nodegroup:LinuxNodes mount -t cifs //<saname>.file.core.windows.net/<sharename> /hpcdata -o vers=2.1,username=<saname>,password='<sakey>',dir_mode=0777,file_mode=0777
```

Para manter a partilha de montagem:

```
    clusrun /nodegroup:LinuxNodes "echo //<saname>.file.core.windows.net/<sharename> /hpcdata cifs vers=2.1,username=<saname>,password='<sakey>',dir_mode=0777,file_mode=0777 >> /etc/fstab"
```

## <a name="install-star-ccm"></a>Instalar ESTRELA-CCM +
Instâncias VM do Azure A8 e A9 fornecem suporte de InfiniBand e as capacidades RDMA. Os controladores de kernel que ativar essas funcionalidades estão disponíveis para o Windows Server 2012 R2, SUSE 12, CentOS 6.5 e CentOS 7.1 imagens no Azure Marketplace. Microsoft MPI e Intel MPI (versão 5. x) são as duas bibliotecas MPI que suportam os controladores no Azure.

ESTRELA de CD-adapco-CCM + 11.x de versão e mais tarde incluídas com a versão de Intel MPI 5. x, para que o suporte de InfiniBand para o Azure está incluído.

Obter o Linux64 ESTRELA-CCM + pacote a partir de [CD adapco portal](https://steve.cd-adapco.com). No nosso caso, utilizámos o versão 11.02.010 precisão misto.

No nó principal, no **/hpcdata** ficheiros do Azure partilhar, crie um script de shell denominado **setupstarccm.sh** com o seguinte conteúdo. Este script será executado em cada nó de computação para configurar ESTRELA-CCM + localmente.

#### <a name="sample-setupstarcmsh-script"></a>Exemplo de script setupstarcm.sh
```
    #!/bin/bash
    # setupstarcm.sh to set up STAR-CCM+ locally

    # Create the CD-adapco main directory
    mkdir -p /opt/CD-adapco

    # Copy the STAR-CCM package from the file share to the local directory
    cp /hpcdata/StarCCM/STAR-CCM+11.02.010_01_linux-x86_64.tar.gz /opt/CD-adapco/

    # Extract the package
    tar -xzf /opt/CD-adapco/STAR-CCM+11.02.010_01_linux-x86_64.tar.gz -C /opt/CD-adapco/

    # Start a silent installation of STAR-CCM without the FLEXlm component
    /opt/CD-adapco/starccm+_11.02.010/STAR-CCM+11.02.010_01_linux-x86_64-2.5_gnu4.8.bin -i silent -DCOMPUTE_NODE=true -DNODOC=true -DINSTALLFLEX=false

    # Update memory limits
    echo "*               hard    memlock         unlimited" >> /etc/security/limits.conf
    echo "*               soft    memlock         unlimited" >> /etc/security/limits.conf
```
Agora, para configurar ESTRELA-CCM + em todos os seus Linux nós de computação, abra uma linha de comandos elevada e execute o seguinte comando:

```
    clusrun /nodegroup:LinuxNodes bash /hpcdata/setupstarccm.sh
```

Enquanto estiver a executar o comando, pode monitorizar a utilização da CPU, utilizando o mapa térmico do Gestor de clusters. Após alguns minutos, todos os nós devem ser corretamente configurados.

## <a name="run-star-ccm-jobs"></a>Executar ESTRELA-CCM + tarefas
Pacote HPC é utilizado para as respetivas capacidades de Programador de tarefas para executar ESTRELA-CCM + tarefas. Para tal, é necessário o suporte de alguns scripts que são utilizadas para iniciar a tarefa e execute ESTRELA-CCM +. Os dados de entrada são mantidos numa partilha de ficheiros de Azure primeiro de simplicidade.

O seguinte script do PowerShell é utilizado para a fila uma ESTRELA-CCM + tarefa. Demora três argumentos:

* O nome do modelo
* O número de nós a ser utilizado
* O número de núcleos em cada nó para ser utilizado

Porque ESTRELA-CCM + pode preencher a largura de banda de memória, é, normalmente, é melhor utilizar menos núcleos por nós de computação e adicionar novos nós. O número exato de núcleos por nó dependerá a família de processadores e a velocidade de interligação.

Os nós são atribuídos exclusivamente para a tarefa e não podem ser partilhados com outros trabalhos. A tarefa não foi iniciada como um trabalho MPI diretamente. O **runstarccm.sh** script de shell iniciará o iniciador MPI.

O modelo de entrada e de **runstarccm.sh** script são armazenados no **/hpcdata** partilha que foi anteriormente instalada.

Ficheiros de registo com o nome com o ID da tarefa e são armazenados no **/hpcdata partilha**, juntamente com a ESTRELA-CCM + ficheiros de saída.

#### <a name="sample-submitstarccmjobps1-script"></a>SubmitStarccmJob.ps1 script de exemplo
```
    Add-PSSnapin Microsoft.HPC -ErrorAction silentlycontinue
    $scheduler="headnodename"
    $modelName=$args[0]
    $nbCoresPerNode=$args[2]
    $nbNodes=$args[1]

    #---------------------------------------------------------------------------------------------------------
    # Create a new job; this will give us the job ID that's used to identify the name of the uploaded package in Azure
    #
    $job = New-HpcJob -Name "$modelName $nbNodes $nbCoresPerNode" -Scheduler $scheduler -NumNodes $nbNodes -NodeGroups "LinuxNodes" -FailOnTaskFailure $true -Exclusive $true
    $jobId = [String]$job.Id

    #---------------------------------------------------------------------------------------------------------
    # Submit the job     
    $workdir =  "/hpcdata"
    $execName = "$nbCoresPerNode runner.java $modelName.sim"

    $job | Add-HpcTask -Scheduler $scheduler -Name "Compute" -stdout "$jobId.log" -stderr "$jobId.err" -Rerunnable $false -NumNodes $nbNodes -Command "runstarccm.sh $execName" -WorkDir "$workdir"


    Submit-HpcJob -Job $job -Scheduler $scheduler
```
Substitua **runner.java** com a ESTRELA preferencial-Iniciador do modelo CCM + Java e código de registo.

#### <a name="sample-runstarccmsh-script"></a>Exemplo de script runstarccm.sh
```
    #!/bin/bash
    echo "start"
    # The path of this script
    SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"
    echo ${SCRIPT_PATH}
    # Set the mpirun runtime environment
    export CDLMD_LICENSE_FILE=1999@flex.cd-adapco.com

    # mpirun command
    STARCCM=/opt/CD-adapco/STAR-CCM+11.02.010/star/bin/starccm+

    # Get node information from ENVs
    NODESCORES=(${CCP_NODES_CORES})
    COUNT=${#NODESCORES[@]}
    NBCORESPERNODE=$1

    # Create the hostfile file
    NODELIST_PATH=${SCRIPT_PATH}/hostfile_$$
    echo ${NODELIST_PATH}

    # Get every node name and write into the hostfile file
    I=1
    NBNODES=0
    while [ ${I} -lt ${COUNT} ]
    do
        echo "${NODESCORES[${I}]}" >> ${NODELIST_PATH}
        let "I=${I}+2"
        let "NBNODES=${NBNODES}+1"
    done
    let "NBCORES=${NBNODES}*${NBCORESPERNODE}"

    # Run STAR-CCM with the hostfile argument
    #  
    ${STARCCM} -np ${NBCORES} -machinefile ${NODELIST_PATH} \
        -power -podkey "<yourkey>" -rsh ssh \
        -mpi intel -fabric UDAPL -cpubind bandwidth,v \
        -mppflags "-ppn $NBCORESPERNODE -genv I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -genv I_MPI_DAPL_UD=0 -genv I_MPI_DYNAMIC_CONNECTION=0" \
        -batch $2 $3
    RTNSTS=$?
    rm -f ${NODELIST_PATH}

    exit ${RTNSTS}
```

No nosso teste, é utilizado um token de licença de energia-a pedido. Para esse token, tem de definir o **$CDLMD_LICENSE_FILE** variável de ambiente para  **1999@flex.cd-adapco.com**  e a chave no **- podkey** opção da linha de comandos.

Após alguma inicialização, extrai o script – do **$CCP_NODES_CORES** variáveis de ambiente que HPC Pack definidas – lista de nós para criar um hostfile que utiliza o iniciador MPI. Este hostfile irá conter a lista de nomes de nó de computação que são utilizados para a tarefa, o nome de um por linha.

O formato do **$CCP_NODES_CORES** segue este padrão:

```
<Number of nodes> <Name of node1> <Cores of node1> <Name of node2> <Cores of node2>...`
```

Em que:

* `<Number of nodes>`é o número de nós atribuídos a esta tarefa.
* `<Name of node_n_...>`é o nome de cada nó atribuído a esta tarefa.
* `<Cores of node_n_...>`é o número de núcleos no nó atribuído a esta tarefa.

O número de núcleos (**$NBCORES**) também é calculado com base no número de nós (**$NBNODES**) e o número de núcleos por nó (fornecido como parâmetro **$NBCORESPERNODE**).

Para as opções de MPI são aqueles que são utilizadas com Intel MPI no Azure:

* `-mpi intel`Para especificar Intel MPI.
* `-fabric UDAPL`Para utilizar o Azure InfiniBand verbos.
* `-cpubind bandwidth,v`Para otimizar a largura de banda para MPI com ESTRELA-CCM +.
* `-mppflags "-ppn $NBCORESPERNODE -genv I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -genv I_MPI_DAPL_UD=0 -genv I_MPI_DYNAMIC_CONNECTION=0"`Para tornar MPI Intel trabalhar com o Azure InfiniBand e para definir o número necessário de núcleos por nó.
* `-batch`para iniciar ESTRELA-CCM + em modo batch com nenhuma IU.

Por fim, para iniciar uma tarefa, certifique-se de que os nós estão em execução e que estão online no Gestor de clusters. Em seguida, a partir de uma linha de comandos do PowerShell, execute este:

```
    .\ SubmitStarccmJob.ps1 <model> <nbNodes> <nbCoresPerNode>
```

## <a name="stop-nodes"></a>Parar nós
Mais tarde no, depois de terminado com os seus testes, pode utilizar os seguintes comandos do PowerShell de pacote HPC para parar e iniciar nós:

```
    Stop-HPCIaaSNode.ps1 -Name <prefix>-00*
    Start-HPCIaaSNode.ps1 -Name <prefix>-00*
```

## <a name="next-steps"></a>Passos seguintes
Tente executar outras cargas de trabalho do Linux. Por exemplo, consulte:

* [Executar NAMD com o Microsoft HPC Pack em nós de computação do Linux no Azure](hpcpack-cluster-namd.md)
* [Executar OpenFOAM com o Microsoft HPC Pack num cluster Linux RDMA no Azure](hpcpack-cluster-openfoam.md)

<!--Image references-->
[hndeploy]:media/hpcpack-cluster-starccm/hndeploy.png
[clustermanager]:media/hpcpack-cluster-starccm/ClusterManager.png
