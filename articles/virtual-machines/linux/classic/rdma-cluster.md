---
title: "Configurar um cluster de Linux RDMA para executar aplicações MPI | Microsoft Docs"
description: "Criar um cluster do Linux do tamanho H16r, H16mr, A8, A9 VMs para utilizar a rede de Azure RDMA para executar aplicações MPI"
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 01834bad-c8e6-48a3-b066-7f1719047dd2
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/14/2017
ms.author: danlep
ms.openlocfilehash: 4b2ceb64b1737918458f6d5c692fc2bfbc0f12ed
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-a-linux-rdma-cluster-to-run-mpi-applications"></a>Configurar um cluster RDMA do Linux para executar aplicações MPI
Saiba como configurar um cluster de Linux RDMA no Azure com [tamanhos de VM de computação de elevado desempenho](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para executar aplicações de Interface de passagem de mensagens (MPI) paralelas. Este artigo fornece os passos para preparar uma imagem de Linux HPC para executar Intel MPI num cluster. Depois de preparação, implementar um cluster de VMs com esta imagem e dos tamanhos de VM do Azure com capacidade RDMA (atualmente H16r, H16mr, A8, A9 ou utilizador). Utilize o cluster para executar aplicações de MPI comunicam de forma eficiente através de uma rede de latência baixa e débito elevado com base na tecnologia de (RDMA) de acesso remoto direto à memória.

> [!IMPORTANT]
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [do Azure Resource Manager](../../../resource-manager-deployment-model.md) e clássico. Este artigo cobre a utilização do modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager.

## <a name="cluster-deployment-options"></a>Opções de implementação de cluster
Seguem-se métodos que pode utilizar para criar um cluster de Linux RDMA com ou sem um programador de tarefas.

* **Scripts CLI do Azure**: conforme mostrado posteriormente neste artigo, utilize o [interface de linha de comandos do Azure](../../../cli-install-nodejs.md) (CLI), um script de implementação de um cluster de VMs com capacidade RDMA. A CLI no modo de gestão do serviço cria serialmente os nós de cluster no modelo de implementação clássica para que implementação de vários nós de computação pode demorar alguns minutos. Para permitir a ligação de rede RDMA, quando utiliza o modelo de implementação clássica, implemente as VMs no mesmo serviço em nuvem.
* **Modelos Azure Resource Manager**: também pode utilizar o modelo de implementação Resource Manager para implementar um cluster de VMs com capacidade RDMA, que liga à rede RDMA. Pode [criar seu próprio modelo](../../../resource-group-authoring-templates.md), ou verifique o [modelos de início rápido do Azure](https://azure.microsoft.com/documentation/templates/) contribuído pela Microsoft ou o community para implementar a solução que pretende que os modelos. Modelos do Resource Manager podem fornecer uma forma rápida e fiável para implementar um cluster do Linux. Para ativar a ligação de rede RDMA, quando utiliza o modelo de implementação Resource Manager, implemente as VMs no mesmo conjunto de disponibilidade.
* **Pacote HPC**: criar um cluster do Microsoft HPC Pack no Azure e adicione o rdma e nós de computação que executam uma distribuição Linux suportada para aceder à rede RDMA. Para obter mais informações, consulte [começar connosco de computação do Linux num cluster HPC Pack no Azure](hpcpack-cluster.md).

## <a name="sample-deployment-steps-in-the-classic-model"></a>Passos de implementação de exemplo no modelo de clássico
Os passos seguintes mostram como utilizar a CLI do Azure para implementar uma VM do SUSE Linux Enterprise Server (SLES) 12 SP1 HPC no Azure Marketplace, personalizá-lo e criar uma imagem VM personalizada. Em seguida, pode utilizar a imagem de um script de implementação de um cluster de VMs com capacidade RDMA.

> [!TIP]
> Utilize os passos semelhantes para implementar um cluster de VMs com capacidade RDMA baseadas nas imagens baseado em CentOS HPC no Azure Marketplace. Alguns passos diferirem ligeiramente, conforme indicado. 
>
>

### <a name="prerequisites"></a>Pré-requisitos
* **Computador cliente**: necessita de um computador de cliente Mac, Linux ou do Windows para comunicar com o Azure. Estes passos partem do princípio de que está a utilizar um cliente Linux.
* **Subscrição do Azure**: Se não tiver uma subscrição, pode criar um [conta gratuita](https://azure.microsoft.com/free/) em apenas alguns minutos. Para clusters maiores, considere uma subscrição pay as you go ou outras opções de compra.
* **Disponibilidade de tamanho VM**: os tamanhos de instância seguintes são compatíveis com RDMA: H16r, H16mr, A8 e A9. Verifique [produtos disponíveis por região](https://azure.microsoft.com/regions/services/) de disponibilidade em regiões do Azure.
* **Quota de núcleos**: poderá ter de aumentar a quota de núcleos para implementar um cluster de computação intensiva VMs. Por exemplo, precisa de, pelo menos, 128 núcleos se pretender implementar 8 A9 VMs, conforme mostrado neste artigo. A subscrição também poderá limitar o número de núcleos que pode implementar em determinados famílias de tamanho VM, incluindo a série de H. Para pedir um aumento de quota [abrir um pedido de suporte ao cliente online](../../../azure-supportability/how-to-create-azure-support-request.md) , sem encargos.
* **CLI do Azure**: [instalar](../../../cli-install-nodejs.md) a CLI do Azure e [ligar à sua subscrição do Azure](../../../xplat-cli-connect.md) do computador cliente.

### <a name="provision-an-sles-12-sp1-hpc-vm"></a>Aprovisionar uma VM do SLES 12 SP1 HPC
Depois de iniciarem sessão no Azure com a CLI do Azure, execute `azure config list` para confirmar que o resultado mostra o modo de gestão de serviço. Se não existir, defina o modo ao executar o comando:

    azure config mode asm


Escreva o seguinte para listar todas as subscrições que estão autorizados a utilizar:

    azure account list

A subscrição do Active Directory atual é identificada com `Current` definido como `true`. Se esta subscrição não for aquele que pretende utilizar para criar o cluster, defina o ID de subscrição adequada como a subscrição do Active Directory:

    azure account set <subscription-Id>

Para ver as imagens de SLES 12 SP1 HPC publicamente disponíveis no Azure, execute um comando como o seguinte, assumindo que a sua suporta de ambiente de shell **grep**:

    azure vm image list | grep "suse.*hpc"

Aprovisionar uma VM com capacidade RDMA com uma imagem de SLES 12 SP1 HPC ao executar um comando como o seguinte:

    azure vm create -g <username> -p <password> -c <cloud-service-name> -l <location> -z A9 -n <vm-name> -e 22 b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-sp1-hpc-v20160824

Em que:

* O tamanho (A9 neste exemplo) é uma dos tamanhos VM com capacidade RDMA.
* O número da porta SSH externo (22 neste exemplo, o que é a predefinição SSH) é qualquer número de porta válido. O número da porta SSH interno está definido como 22.
* É criado um novo serviço em nuvem na região do Azure especificada de localização. Especifique uma localização na qual o tamanho da VM que escolher está disponível.
* Para obter suporte de prioridade SUSE (o que implica encargos adicionais), o nome da imagem SLES 12 SP1 atualmente pode ser uma destas duas opções: 

 `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-sp1-hpc-v20160824`

  `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-sp1-hpc-priority-v20160824`


### <a name="customize-the-vm"></a>Personalizar a VM
Depois da VM concluída SSH para a VM utilizando o endereço IP externo de uma VM (ou nome DNS), o aprovisionamento e a porta externa número configurado e, em seguida, personalizá-lo. Para detalhes de ligação, consulte [como iniciar sessão para uma máquina virtual com Linux](../mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Execute comandos como o utilizador configurado da VM, a menos que o acesso de raiz é necessário para concluir um passo.

> [!IMPORTANT]
> Microsoft Azure não fornece acesso de raiz para VMs com Linux. Para obter acesso administrativo quando estiver ligado como utilizador para a VM, execute comandos utilizando `sudo`.
>
>

* **Atualizações**: Instale atualizações utilizando zypper. Pode também querer instalar utilitários NFS.

  > [!IMPORTANT]
  > Numa VM do SLES 12 SP1 HPC, recomendamos que não aplicar as atualizações de kernel, que podem causar problemas com o RDMA Linux controladores.
  >
  >
* **Intel MPI**: concluir a instalação do Intel MPI na SLES 12 SP1 HPC VM executando o seguinte comando:

        sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
* **Bloquear memória**: códigos de MPI para bloquear a memória disponível para RDMA, adicionar ou alterar as seguintes definições no ficheiro /etc/security/limits.conf. Precisa de acesso de raiz para editar este ficheiro.

    ```
    <User or group name> hard    memlock <memory required for your application in KB>

    <User or group name> soft    memlock <memory required for your application in KB>
    ```

  > [!NOTE]
  > Para fins de teste, pode também definir memlock para ilimitado. Por exemplo: `<User or group name>    hard    memlock unlimited`. Para obter mais informações, consulte [métodos melhor conhecidos para a definição bloqueado tamanho da memória](https://software.intel.com/en-us/blogs/2014/12/16/best-known-methods-for-setting-locked-memory-size).
  >
  >
* **As chaves SSH para as VMs do SLES**: gerar SSH chaves estabelecer confiança para a sua conta de utilizador entre os nós de computação no SLES cluster ao executar tarefas MPI. Se tiver implementado uma VM de HPC com base em CentOS, não siga este passo. Ver as instruções neste artigo para configurar a confiança SSH passwordless entre os nós de cluster depois de capturar a imagem e implementar o cluster.

    Para criar chaves SSH, execute o seguinte comando. Quando lhe for pedido para a entrada, selecione **Enter** para gerar as chaves na localização predefinida, sem configuração de uma palavra-passe.

        ssh-keygen

    Acrescente a chave pública para o ficheiro authorized_keys conhecidos chaves públicas.

        cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys

    No diretório ~/.ssh, editar ou criar o ficheiro de configuração. Forneça o intervalo de endereços IP da rede privada que pretende utilizar no Azure (10.32.0.0/16 neste exemplo):

        host 10.32.0.*
        StrictHostKeyChecking no

    Em alternativa, liste o endereço IP de rede privada da cada VM no seu cluster da seguinte forma:

    ```
    host 10.32.0.1
     StrictHostKeyChecking no
    host 10.32.0.2
     StrictHostKeyChecking no
    host 10.32.0.3
     StrictHostKeyChecking no
    ```

  > [!NOTE]
  > Configurar `StrictHostKeyChecking no` pode criar um potencial risco de segurança quando não for especificado um endereço IP específico ou um intervalo.
  >
  >
* **Aplicações**: instalar quaisquer aplicações precisa ou efetuar outras personalizações antes de capturar a imagem.

### <a name="capture-the-image"></a>Capturar a imagem
Para capturar a imagem, execute o seguinte comando na VM do Linux. Este comando deprovisions a VM, mas mantém as contas de utilizador e as chaves SSH que configurou.

```
sudo waagent -deprovision
```

A partir de computador cliente, execute os seguintes comandos da CLI do Azure, para capturar a imagem. Para obter mais informações, consulte [como capturar uma máquina virtual de Linux clássica como uma imagem](capture-image.md).  

```
azure vm shutdown <vm-name>

azure vm capture -t <vm-name> <image-name>

```

Depois de executar estes comandos, a imagem VM é capturada para a sua utilização e a VM é eliminada. Agora, tem a sua imagem personalizada pronta para implementar um cluster.

### <a name="deploy-a-cluster-with-the-image"></a>Implementar um cluster com a imagem
Modifique o seguinte script de Bash com os valores apropriados para o seu ambiente e executá-la a partir do seu computador cliente. Porque o Azure implementa as VMs serialmente no modelo de implementação clássica, demora alguns minutos para implementar as VMs A9 oito sugerido neste script.

```
#!/bin/bash -x
# Script to create a compute cluster without a scheduler in a VNet in Azure
# Create a custom private network in Azure
# Replace 10.32.0.0 with your virtual network address space
# Replace <network-name> with your network identifier
# Replace "West US" with an Azure region where the VM size is available
# See Azure Pricing pages for prices and availability of compute-intensive VMs

azure network vnet create -l "West US" -e 10.32.0.0 -i 16 <network-name>

# Create a cloud service. All the compute-intensive instances need to be in the same cloud service for Linux RDMA to work across InfiniBand.
# Note: The current maximum number of VMs in a cloud service is 50. If you need to provision more than 50 VMs in the same cloud service in your cluster, contact Azure Support.

azure service create <cloud-service-name> --location "West US" –s <subscription-ID>

# Define a prefix naming scheme for compute nodes, e.g., cluster11, cluster12, etc.

vmname=cluster

# Define a prefix for external port numbers. If you want to turn off external ports and use only internal ports to communicate between compute nodes via port 22, don’t use this option. Since port numbers up to 10000 are reserved, use numbers after 10000. Leave external port on for rank 0 and head node.

portnumber=101

# In this cluster there will be 8 size A9 nodes, named cluster11 to cluster18. Specify your captured image in <image-name>. Specify the username and password you used when creating the SSH keys.

for (( i=11; i<19; i++ )); do
        azure vm create -g <username> -p <password> -c <cloud-service-name> -z A9 -n $vmname$i -e $portnumber$i -w <network-name> -b Subnet-1 <image-name>
done

# Save this script with a name like makecluster.sh and run it in your shell environment to provision your cluster
```

## <a name="considerations-for-a-centos-hpc-cluster"></a>Considerações para um cluster CentOS HPC
Se pretender configurar um cluster com base numa das imagens baseado em CentOS HPC no Azure Marketplace, em vez de SLES 12 HPC, siga os passos gerais na secção anterior. Quando aprovisiona e configura a VM, tenha em atenção as seguintes diferenças:

- Intel MPI já está instalada numa VM aprovisionada a partir de uma imagem com base em CentOS HPC.
- Definições de memória do bloqueio já foram adicionadas no ficheiro de /etc/security/limits.conf a VM.
- Não gere chaves SSH na VM aprovisionar para captura. Em vez disso, recomendamos a configuração da autenticação baseada no utilizador depois de implementar o cluster. Para obter mais informações, consulte a secção seguinte.  

### <a name="set-up-passwordless-ssh-trust-on-the-cluster"></a>Configurar a confiança SSH passwordless no cluster
Num cluster HPC com base em CentOS, existem dois métodos para estabelecer confiança entre os nós de computação: autenticação baseada no utilizador e a autenticação baseada no anfitrião. Autenticação baseada em anfitrião está fora do âmbito deste artigo e, geralmente, tem de ser efetuada através de um script de extensão durante a implementação. Autenticação baseada no utilizador for conveniente para estabelecer confiança após a implementação e requer a geração e a partilha de chaves SSH entre os nós de computação no cluster. Este método é geralmente conhecido como início de sessão SSH passwordless e é necessário quando a execução de tarefas MPI.

Um script de exemplo contribuído da Comunidade está disponível no [GitHub](https://github.com/tanewill/utils/blob/master/user_authentication.sh) para ativar a autenticação de utilizador fácil num cluster HPC com base em CentOS. Transfira e utilize este script utilizando os seguintes passos. Também pode modificar este script ou utilizar qualquer outro método para estabelecer a autenticação SSH passwordless entre os nós de computação de cluster.

    wget https://raw.githubusercontent.com/tanewill/utils/master/ user_authentication.sh

Para executar o script, terá de saber o prefixo para os endereços IP da sub-rede. Obtenha o prefixo executando o seguinte comando num de nós do cluster. A saída deve ter um aspeto semelhante 10.1.3.5 e o prefixo é o 10.1.3 parte.

    ifconfig eth0 | grep -w inet | awk '{print $2}'

Agora, execute o script utilizando três parâmetros: o nome de utilizador comum em nós de computação, a palavra-passe comuns para esse utilizador em nós de computação e o prefixo de sub-rede que foi devolvido do comando anterior.

    ./user_authentication.sh <myusername> <mypassword> 10.1.3

Este script faz o seguinte:

* Cria um diretório no nó de anfitrião com o nome. SSH, o que é necessário para início de sessão passwordless.
* Cria um ficheiro de configuração no diretório. SSH que dá instruções ao início de sessão passwordless para permitir o início de sessão de qualquer nó no cluster.
* Cria ficheiros que contêm os nomes de nó e endereços IP do nó para todos os nós do cluster. Estes ficheiros são mantidos após a execução do script para futura referência.
* Cria um par de chaves público e privado para cada nó de cluster (incluindo o nó de anfitrião) e cria as entradas no ficheiro authorized_keys.

> [!WARNING]
> Executar este script, pode criar um potencial risco de segurança. Certifique-se de que as informações da chave públicas no ~/.ssh não são distribuídas.
>
>

## <a name="configure-intel-mpi"></a>Configurar Intel MPI
Para executar aplicações de MPI no Azure Linux RDMA, terá de configurar algumas variáveis de ambiente específicas de Intel MPI. Eis um exemplo de script de Bash para configurar as variáveis necessárias para executar uma aplicação. Altere o caminho para mpivars.sh conforme necessário para a instalação de Intel MPI.

```
#!/bin/bash -x

# For a SLES 12 SP1 HPC cluster

source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh

# For a CentOS-based HPC cluster

# source /opt/intel/impi/5.1.3.181/bin64/mpivars.sh

export I_MPI_FABRICS=shm:dapl

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB
# Setting the variable to shm:dapl gives best performance for some applications
# If your application doesn’t take advantage of shared memory and MPI together, then set only dapl

export I_MPI_DAPL_PROVIDER=ofa-v2-ib0

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB

export I_MPI_DYNAMIC_CONNECTION=0

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB

# Command line to run the job

mpirun -n <number-of-cores> -ppn <core-per-node> -hostfile <hostfilename>  /path <path to the application exe> <arguments specific to the application>

#end
```

Segue-se o formato de ficheiro do anfitrião. Adicione uma linha para cada nó do cluster. Especifique os endereços IP privados da rede virtual definido anteriormente, não os nomes DNS. Por exemplo, para os dois anfitriões com endereços IP 10.32.0.1 e 10.32.0.2, o ficheiro contém o seguinte:

```
10.32.0.1:16
10.32.0.2:16
```

## <a name="run-mpi-on-a-basic-two-node-cluster"></a>Executar MPI num cluster de dois nós básico
Se ainda não o tiver feito, configure primeiro o ambiente para Intel MPI.

```
# For a SLES 12 SP1 HPC cluster

source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh

# For a CentOS-based HPC cluster

# source /opt/intel/impi/5.1.3.181/bin64/mpivars.sh
```

### <a name="run-an-mpi-command"></a>Execute um comando MPI
Execute um comando MPI num de nós de computação para mostrar que MPI está corretamente instalado e que pode comunicar entre, pelo menos, que dois nós de computação. O seguinte **mpirun** comando executa o **hostname** comando em dois nós.

```
mpirun -ppn 1 -n 2 -hosts <host1>,<host2> -env I_MPI_FABRICS=shm:dapl -env I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -env I_MPI_DYNAMIC_CONNECTION=0 hostname
```
O resultado deve listar os nomes de todos os nós transmitido como entrada para `-hosts`. Por exemplo, um **mpirun** comando com dois nós devolve o resultado semelhante ao seguinte:

```
cluster11
cluster12
```

### <a name="run-an-mpi-benchmark"></a>Executar um benchmark MPI
O seguinte comando do Intel MPI executa um benchmark pingpong para verificar a configuração de cluster e a ligação à rede RDMA.

```
mpirun -hosts <host1>,<host2> -ppn 1 -n 2 -env I_MPI_FABRICS=dapl -env I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -env I_MPI_DYNAMIC_CONNECTION=0 IMB-MPI1 pingpong
```

Num cluster de trabalhar com dois nós, deverá ver um resultado como o seguinte. Na rede Azure RDMA, espere latência ou abaixo 3 microssegundos para mensagem tamanhos até 512 bytes.

```
#------------------------------------------------------------
#    Intel (R) MPI Benchmarks 4.0 Update 1, MPI-1 part
#------------------------------------------------------------
# Date                  : Fri Jul 17 23:16:46 2015
# Machine               : x86_64
# System                : Linux
# Release               : 3.12.39-44-default
# Version               : #5 SMP Thu Jun 25 22:45:24 UTC 2015
# MPI Version           : 3.0
# MPI Thread Environment:
# New default behavior from Version 3.2 on:
# the number of iterations per message size is cut down
# dynamically when a certain run time (per message size sample)
# is expected to be exceeded. Time limit is defined by variable
# "SECS_PER_SAMPLE" (=> IMB_settings.h)
# or through the flag => -time

# Calling sequence was:
# /opt/intel/impi_latest/bin64/IMB-MPI1 pingpong
# Minimum message length in bytes:   0
# Maximum message length in bytes:   4194304
#
# MPI_Datatype                   :   MPI_BYTE
# MPI_Datatype for reductions    :   MPI_FLOAT
# MPI_Op                         :   MPI_SUM
#
#
# List of Benchmarks to run:
# PingPong
#---------------------------------------------------
# Benchmarking PingPong
# #processes = 2
#---------------------------------------------------
       #bytes #repetitions      t[usec]   Mbytes/sec
            0         1000         2.23         0.00
            1         1000         2.26         0.42
            2         1000         2.26         0.85
            4         1000         2.26         1.69
            8         1000         2.26         3.38
           16         1000         2.36         6.45
           32         1000         2.57        11.89
           64         1000         2.36        25.81
          128         1000         2.64        46.19
          256         1000         2.73        89.30
          512         1000         3.09       157.99
         1024         1000         3.60       271.53
         2048         1000         4.46       437.57
         4096         1000         6.11       639.23
         8192         1000         7.49      1043.47
        16384         1000         9.76      1600.76
        32768         1000        14.98      2085.77
        65536          640        25.99      2405.08
       131072          320        50.68      2466.64
       262144          160        80.62      3101.01
       524288           80       145.86      3427.91
      1048576           40       279.06      3583.42
      2097152           20       543.37      3680.71
      4194304           10      1082.94      3693.63

# All processes entering MPI_Finalize

```



## <a name="next-steps"></a>Passos seguintes
* Implementar e executar o Linux MPI aplicações no cluster do Linux.
* Consulte o [documentação da biblioteca de MPI Intel](https://software.intel.com/en-us/articles/intel-mpi-library-documentation/) para obter orientações sobre Intel MPI.
* Tente uma [modelo de início rápido](https://github.com/Azure/azure-quickstart-templates/tree/master/intel-lustre-clients-on-centos) para criar um cluster de Intel Lustre utilizando uma imagem com base em CentOS HPC. Para obter mais informações, consulte [implementar edição de nuvem Intel para Lustre no Microsoft Azure](https://blogs.msdn.microsoft.com/arsen/2015/10/29/deploying-intel-cloud-edition-for-lustre-on-microsoft-azure/).
