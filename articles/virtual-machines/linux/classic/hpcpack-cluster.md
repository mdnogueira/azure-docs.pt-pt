---
title: "VMs de computação do Linux num cluster HPC Pack | Microsoft Docs"
description: "Saiba como criar e utilizar um cluster HPC Pack no Azure para Linux elevado desempenho informática cargas de trabalho (HPC)"
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager,hpc-pack
ms.assetid: 4d080fdd-5ffe-4f54-a78d-4c818f6eb3fb
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: big-compute
ms.date: 10/12/2016
ms.author: danlep
ms.openlocfilehash: 809d3944311badf265117d353b65642e044d900c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-linux-compute-nodes-in-an-hpc-pack-cluster-in-azure"></a>Introdução aos nós de computação do Linux num cluster HPC Pack no Azure
Configurar um [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029.aspx) com uma distribuição Linux suportada de nós de computação de cluster no Azure que contém um nó principal com o Windows Server e várias. Explore as opções para mover dados entre os nós do Linux e o nó principal do cluster do Windows. Saiba como submeter as tarefas de Linux HPC para o cluster.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

Um nível elevado, o diagrama seguinte mostra o cluster HPC Pack criar e trabalhar com.

![Cluster de pacote HPC connosco do Linux][scenario]

Para outras opções executar cargas de trabalho de Linux HPC no Azure, consulte [recursos técnicos para batch e computação de alto desempenho](../../../batch/big-compute-resources.md).

## <a name="deploy-an-hpc-pack-cluster-with-linux-compute-nodes"></a>Implementar um cluster de pacote HPC connosco de computação do Linux
Este artigo mostra que existem duas opções para implementar um cluster HPC Pack no Azure com o Linux nós de computação. Ambos os métodos de utilizam uma imagem do Marketplace do Windows Server com o HPC Pack para criar o nó principal. 

* **Modelo Azure Resource Manager** -utilizar um modelo do Azure Marketplace, ou um modelo de início rápido da Comunidade, para automatizar a criação do cluster no modelo de implementação Resource Manager. Por exemplo, o [cluster HPC Pack para cargas de trabalho do Linux](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/) modelo no Azure Marketplace cria uma infraestrutura de cluster HPC Pack concluída para o Linux HPC cargas de trabalho.
* **Script do PowerShell** -utilize o [script de implementação do Microsoft HPC Pack IaaS](../../windows/classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) (**New-HpcIaaSCluster.ps1**) para automatizar uma implementação completa do cluster no modelo de implementação clássica. Este script do PowerShell do Azure utiliza uma imagem de VM de pacote HPC no Azure Marketplace para a implementação rápida e fornece um conjunto abrangente de parâmetros de configuração para implementar nós de computação do Linux.

Para obter mais informações sobre as opções de implementação de cluster HPC Pack no Azure, consulte [opções para criar e gerir um computação de alto desempenho (HPC) do cluster no Azure com o Microsoft HPC Pack](../hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="prerequisites"></a>Pré-requisitos
* **Subscrição do Azure** -pode utilizar uma subscrição no serviço Global do Azure ou do Azure China. Se não tiver uma conta, pode criar um [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.
* **Quota de núcleos** -poderá ter de aumentar a quota de núcleos, especialmente se optar por implementar vários nós do cluster sem especializados tamanhos de VM. Para aumentar uma quota, abra um pedido de suporte online do cliente, sem encargos.
* **As distribuições do Linux** -atualmente HPC Pack suporta as distribuições de Linux seguintes nós de computação. Pode utilizar Marketplace as versões destes distribuições, quando disponíveis, ou forneça o seu próprio.
  
  * **Com base em centOS**: 6.5 6.6, 6.7, 7.0, 7.1, 7.2, 6.5 HPC, 7.1 HPC
  * **Red Hat Enterprise Linux**: 6.7, 6.8, 7.2
  * **SUSE Linux Enterprise Server**: SLES 12 SLES 12 (Premium), SLES 12 SP1, SLES 12 SP1 (Premium), SLES 12 para HPC, SLES 12 para HPC (Premium)
  * **Ubuntu Server**: 14.04 LTS, 16.04 LTS
    
    > [!TIP]
    > Para utilizar a rede de Azure RDMA com uma dos tamanhos VM com capacidade RDMA, especifique uma imagem do SUSE Linux Enterprise Server 12 HPC ou baseada em CentOS HPC no Azure Marketplace. Para obter mais informações, consulte [tamanhos de VM de computação de elevado desempenho](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
    > 
    > 

Pré-requisitos adicionais para implementar o cluster utilizando o script de implementação do IaaS do HPC Pack:

* **Computador cliente** -necessita de um computador de cliente baseada em Windows para executar o script de implementação de cluster.
* **O Azure PowerShell** - [instalar e configurar o Azure PowerShell](/powershell/azure/overview) (versão 0.8.10 ou posterior) no computador cliente.
* **Script de implementação de HPC Pack IaaS** - transferir e Descompacte a versão mais recente do script do [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=44949). Pode verificar a versão do script, executando `.\New-HPCIaaSCluster.ps1 –Version`. Este artigo baseia-se numa versão 4.4.1 ou posterior do script.

### <a name="deployment-option-1-use-a-resource-manager-template"></a>Opção de implementação 1. Utilizar um modelo do Resource Manager
1. Vá para o [cluster HPC Pack para cargas de trabalho do Linux](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/) modelo no Azure Marketplace e clique **implementar**.
2. No portal do Azure, reveja as informações e, em seguida, clique em **criar**.
   
    ![Criação do portal][portal]
3. No **Noções básicas** painel, introduza um nome para o cluster, o que também os nomes de nó principal do VM. Pode escolher um grupo de recursos existente ou crie um grupo para a implementação numa localização que está disponível para si. A localização afeta a disponibilidade de determinados tamanhos de VM e outros serviços do Azure (consulte [produtos disponíveis por região](https://azure.microsoft.com/regions/services/)).
4. No **aceda a definições de nó** painel, para uma primeira implementação, pode aceitar geralmente as predefinições. 
   
   > [!NOTE]
   > O **URL de scripts pós-configuração** é uma definição opcional para especificar um script do Windows PowerShell publicamente disponível que pretende executar no nó principal VM depois de se encontra em execução. 
   > 
   > 
5. No **definições de nó de computação** painel, selecione um tipo de nomenclatura padrão para os nós, o número e tamanho de nós e a distribuição de Linux para implementar.
6. No **as definições de infraestrutura** painel, introduza os nomes para a rede virtual e do Active Directory domínio, domínio e credenciais de administrador da VM e um padrão de nomenclatura para as contas de armazenamento.
   
   > [!NOTE]
   > Pacote HPC utiliza o domínio do Active Directory para autenticar utilizadores de cluster. 
   > 
   > 
7. Depois de executar os testes de validação e reveja os termos de utilização, clique em **Compra**.

### <a name="deployment-option-2-use-the-iaas-deployment-script"></a>Opção de implementação 2. Utilize o script de implementação do IaaS
Seguem-se os pré-requisitos adicionais para implementar o cluster utilizando o script de implementação do IaaS do HPC Pack:

* **Computador cliente** -necessita de um computador de cliente baseada em Windows para executar o script de implementação de cluster.
* **O Azure PowerShell** - [instalar e configurar o Azure PowerShell](/powershell/azure/overview) (versão 0.8.10 ou posterior) no computador cliente.
* **Script de implementação de HPC Pack IaaS** - transferir e Descompacte a versão mais recente do script do [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=44949). Pode verificar a versão do script, executando `.\New-HPCIaaSCluster.ps1 –Version`. Este artigo baseia-se numa versão 4.4.1 ou posterior do script.

**Ficheiro de configuração XML**

O script de implementação do HPC Pack IaaS utiliza um ficheiro de configuração XML como entrada para descrever o cluster HPC. O ficheiro de configuração de exemplo seguinte especifica um cluster pequeno constituída por um nó principal do HPC Pack e dois nós de computação do Linux do A7 CentOS 7.0 de tamanho. 

Modificar o ficheiro conforme necessário para o seu ambiente e a configuração de cluster pretendido e guarde-o com um nome, como HPCDemoConfig.xml. Por exemplo, terá de fornecer o nome da sua subscrição e um nome de conta de armazenamento exclusivas e nome do serviço de nuvem. Além disso, pode querer escolha outra imagem Linux suportada para nós de computação. Para obter mais informações sobre os elementos no ficheiro de configuração, consulte o ficheiro de Manual.rtf na pasta de scripts e [criar um cluster HPC com o script de implementação do HPC Pack IaaS](../../windows/classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>allvhdsje</StorageAccount>
  </Subscription>
  <Location>Japan East</Location>  
  <VNet>
    <VNetName>centos7rdmavnetje</VNetName>
    <SubnetName>CentOS7RDMACluster</SubnetName>
  </VNet>
  <Domain>
    <DCOption>HeadNodeAsDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>CentOS7RDMA-HN</VMName>
    <ServiceName>centos7rdma-je</ServiceName>
  <VMSize>ExtraLarge</VMSize>
  <EnableRESTAPI />
  <EnableWebPortal />
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>CentOS7RDMA-LN%1%</VMNamePattern>
    <ServiceName>centos7rdma-je</ServiceName>
    <VMSize>A7</VMSize>
    <NodeCount>2</NodeCount>
    <ImageName>5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20150325</ImageName>
  </LinuxComputeNodes>
</IaaSClusterConfig>
```

**Para executar o script de implementação do IaaS do HPC Pack**

1. Abra o Windows PowerShell no computador cliente como um administrador.
2. Altere o diretório para a pasta onde o script está instalado (E:\IaaSClusterScript neste exemplo).
   
    ```powershell
    cd E:\IaaSClusterScript
    ```
3. Execute o seguinte comando para implementar o cluster HPC Pack. Este exemplo assume que o ficheiro de configuração está localizado numa E:\HPCDemoConfig.xml
   
    ```powershell
    .\New-HpcIaaSCluster.ps1 –ConfigFile E:\HPCDemoConfig.xml –AdminUserName MyAdminName
    ```
   
    a. Porque o **AdminPassword** não está especificado no comando anterior, são-lhe pedido para introduzir a palavra-passe do utilizador *MyAdminName*.
   
    b. Em seguida, inicia o script validar o ficheiro de configuração. Pode demorar vários minutos, consoante a ligação de rede.
   
    ![Validação][validate]
   
    c. Depois de passaram validações, o script apresenta uma lista de recursos de cluster para criar. Introduza *Y* para continuar.
   
    ![Recursos][resources]
   
    d. O script começa a implementar o cluster HPC Pack e conclui a configuração sem mais passos manuais. Pode executar o script durante vários minutos.
   
    ![Implementação][deploy]
   
   > [!NOTE]
   > Neste exemplo, o script gera um ficheiro de registo automaticamente uma vez que o **- LogFile** parâmetro não está especificado. Os registos não são escritos em tempo real, mas são recolhidos no fim da implementação e a validação. Se o processo de PowerShell for interrompido enquanto o script é executado, alguns registos serão perdidos.
   > 
   > 

## <a name="connect-to-the-head-node"></a>Ligar ao nó principal
Depois de implementar o cluster HPC Pack no Azure, [estabelecer ligação ao ambiente de trabalho remoto](../../windows/connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) ao nó principal do VM através do domínio de credenciais fornecido quando implementou o cluster (por exemplo, *hpc\\clusteradmin*). Gerir o cluster do nó principal.

No nó principal, inicie o Gestor de clusters HPC para verificar o estado do cluster HPC Pack. Pode gerir e nós de computação de monitor de nós de computação de Linux, da mesma forma que trabalha com o Windows. Por exemplo, verá os nós do Linux listados no **gestão de recursos** (estes nós são implementados com o **LinuxNode** modelo).

![Gestão de nó][management]

Consulte também os nós do Linux no **mapa térmico** vista.

![Mapa térmico][heatmap]

## <a name="how-to-move-data-in-a-cluster-with-linux-nodes"></a>Como mover dados num cluster connosco do Linux
Existem várias opções para mover dados entre os nós do Linux e o nó principal do cluster do Windows. Seguem-se três métodos comuns, descritos mais detalhadamente nas secções seguintes:

* **Ficheiros do Azure** -expõe uma partilha de ficheiros SMB gerida para armazenar ficheiros de dados no armazenamento do Azure. Nós do Windows e nós do Linux podem montar uma partilha de ficheiros do Azure como uma unidade ou pasta ao mesmo tempo, mesmo que está a ser implementados em redes virtuais diferentes.
* **Nó principal SMB partilhar** -monta uma pasta partilhada de Windows padrão do nó principal em nós do Linux.
* **O servidor NFS do head nó** -fornece uma solução de partilha de ficheiros para um ambiente misto do Windows e Linux.

### <a name="azure-file-storage"></a>File storage do Azure
O [ficheiros do Azure](https://azure.microsoft.com/services/storage/files/) serviço expõe partilhas de ficheiros utilizando o protocolo SMB 2.1 padrão. Serviços de nuvem e as VMs do Azure podem partilhar dados de ficheiros entre componentes da aplicação através de partilhas montadas e as aplicações no local podem aceder a dados numa partilha de ficheiros através da API do armazenamento de ficheiros. 

Para obter passos detalhados criar uma partilha de ficheiros do Azure e monte-lo no nó principal, consulte [introdução ao File storage do Azure no Windows](../../../storage/files/storage-how-to-use-files-windows.md). Para montar a partilha de ficheiros do Azure em nós do Linux, consulte [como utilizar o File storage do Azure com o Linux](../../../storage/files/storage-how-to-use-files-linux.md). Para configurar ligações persistentes, consulte [Persisting ligações aos ficheiros do Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx).

No exemplo seguinte, crie uma partilha de ficheiros do Azure numa conta de armazenamento. Para montar a partilha no nó principal, abra uma linha de comandos e introduza os seguintes comandos:

```command
cmdkey /add:allvhdsje.file.core.windows.net /user:allvhdsje /pass:<storageaccountkey>

net use Z: \\allvhdje.file.core.windows.net\rdma /persistent:yes
```

Neste exemplo, allvhdsje é o nome da sua conta de armazenamento, storageaccountkey é a chave de conta de armazenamento e rdma é o nome de partilha de ficheiros do Azure. A partilha de ficheiros do Azure está montada como z no nó principal.

Para montar a partilha de ficheiros do Azure em nós do Linux, execute um **clusrun** comando no nó principal. **[Clusrun](https://technet.microsoft.com/library/cc947685.aspx)**  é uma ferramenta de HPC Pack útil para realizar tarefas administrativas em vários nós. (Consulte também [Clusrun para nós do Linux](#Clusrun-for-Linux-nodes) neste artigo.)

Abra uma janela do Windows PowerShell e introduza os seguintes comandos:

```powershell
clusrun /nodegroup:LinuxNodes mkdir -p /rdma

clusrun /nodegroup:LinuxNodes mount -t cifs //allvhdsje.file.core.windows.net/rdma /rdma -o vers=2.1`,username=allvhdsje`,password=<storageaccountkey>'`,dir_mode=0777`,file_mode=0777
```

O primeiro comando cria uma pasta denominada /rdma em todos os nós no grupo de LinuxNodes. O segundo comando monta o ficheiro do Azure partilha allvhdsjw.file.core.windows.net/rdma para a pasta de /rdma com dir e bits de modo de ficheiro definido como 777. O segundo comando, allvhdsje é o nome da sua conta de armazenamento em storageaccountkey é a chave de conta de armazenamento.

> [!NOTE]
> O "\`" símbolo o segundo comando é um símbolo de escape para o PowerShell. "\`," significa que a "," (caráter vírgula) é uma parte do comando.
> 
> 

### <a name="head-node-share"></a>Partilha de nó principal
Em alternativa, monte uma pasta partilhada do nó principal em nós do Linux. Uma partilha fornece a forma mais simples para partilhar ficheiros, mas o nó principal e todos os nós do Linux tem de ser implementados na mesma rede virtual. Eis os passos.

1. Crie uma pasta no nó principal e partilhe-a para todos os utilizadores com permissões de leitura/escrita. Por exemplo, partilhar D:\OpenFOAM no nó principal como \\CentOS7RDMA HN\OpenFOAM. Aqui CentOS7RDMA HN é o nome de anfitrião do nó principal.
   
    ![Permissões de partilha de ficheiros][fileshareperms]
   
    ![Partilha de ficheiros][filesharing]
2. Abra uma janela do Windows PowerShell e execute os seguintes comandos:
   
    ```powershell
    clusrun /nodegroup:LinuxNodes mkdir -p /openfoam
   
    clusrun /nodegroup:LinuxNodes mount -t cifs //CentOS7RDMA-HN/OpenFOAM /openfoam -o vers=2.1`,username=<username>`,password='<password>'`,dir_mode=0777`,file_mode=0777
    ```

O primeiro comando cria uma pasta denominada /openfoam em todos os nós no grupo de LinuxNodes. O segundo comando monta //CentOS7RDMA-HN/OpenFOAM a pasta partilhada para a pasta com dir e bits de modo de ficheiro definido como 777. O nome de utilizador e palavra-passe no comando devem ser o nome de utilizador e palavra-passe de um utilizador de cluster no nó principal. (Consulte [adicionar ou remover utilizadores de cluster](https://technet.microsoft.com/library/ff919330.aspx).)

> [!NOTE]
> O "\`" símbolo o segundo comando é um símbolo de escape para o PowerShell. "\`," significa que a "," (caráter vírgula) é uma parte do comando.
> 
> 

### <a name="nfs-server"></a>Servidor NFS
O serviço NFS permite-lhe partilhar e migrar ficheiros entre computadores que executam o sistema operativo Windows Server 2012 utilizando o protocolo SMB e os computadores baseados em Linux através do protocolo NFS. O servidor NFS e todos os outros nós têm de ser implementados na mesma rede virtual. Proporciona uma melhoria da compatibilidade com os nós do Linux comparada a uma partilha SMB. Por exemplo, suporta ligações de ficheiro.

1. Para instalar e configurar um servidor NFS, siga os passos no [servidor para a partilha primeiro do sistema de ficheiros de rede ponto a ponto](http://blogs.technet.com/b/filecab/archive/2012/10/08/server-for-network-file-system-first-share-end-to-end.aspx).
   
    Por exemplo, crie uma partilha NFS denominada nfs com as seguintes propriedades:
   
    ![Autorização de NFS][nfsauth]
   
    ![Permissões de partilha NFS][nfsshare]
   
    ![Permissões NTFS de NFS][nfsperm]
   
    ![Propriedades de gestão de NFS][nfsmanage]
2. Abra uma janela do Windows PowerShell e execute os seguintes comandos:
   
    ```powershell
    clusrun /nodegroup:LinuxNodes mkdir -p /nfsshare
   
    clusrun /nodegroup:LinuxNodes mount CentOS7RDMA-HN:/nfs /nfsshared
    ```
   
   O primeiro comando cria uma pasta denominada /nfsshared em todos os nós no grupo de LinuxNodes. O segundo comando monta a partilha NFS CentOS7RDMA HN: / nfs para a pasta. Aqui CentOS7RDMA HN: / nfs é o caminho de partilha NFS incluída na sua remoto.

## <a name="how-to-submit-jobs"></a>Como submeter tarefas
Existem várias formas para submeter tarefas ao cluster HPC Pack:

* Gestor de clusters HPC ou GUI de Gestor de tarefas HPC
* HPC web portal
* API REST

Submissão de tarefa para o cluster no Azure através do HPC web portal e ferramentas de HPC Pack GUI são as mesmas que nos Windows nós de computação. Consulte [Gestor de tarefas HPC Pack](https://technet.microsoft.com/library/ff919691.aspx) e [como submeter as tarefas de um computador de cliente no local](../../windows/hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Para submeter tarefas através da API REST, consulte [criar e submeter tarefas, utilizando a API de REST no Microsoft HPC Pack](http://social.technet.microsoft.com/wiki/contents/articles/7737.creating-and-submitting-jobs-by-using-the-rest-api-in-microsoft-hpc-pack-windows-hpc-server.aspx). Para submeter tarefas de um cliente Linux, consulte também o exemplo de Python no [HPC Pack SDK](https://www.microsoft.com/download/details.aspx?id=47756).

## <a name="clusrun-for-linux-nodes"></a>Clusrun para nós do Linux
O pacote HPC [clusrun](https://technet.microsoft.com/library/cc947685.aspx) ferramenta pode ser utilizada para executar comandos em nós do Linux ou através de uma linha de comandos ou o Gestor de clusters HPC. Seguem-se alguns exemplos básicos.

* Mostra os nomes de utilizador atual em todos os nós do cluster.
  
    ```command
    clusrun whoami
    ```
* Instalar o **gdb** ferramenta de depurador com **yum** em todos os nós no linuxnodes grupo e, em seguida, reinicie os nós após 10 minutos.
  
    ```command
    clusrun /nodegroup:linuxnodes yum install gdb –y; shutdown –r 10
    ```
* Criar um script de shell que apresenta cada número entre 1 e 10 para um segundo em cada nó do Linux no cluster, executá-la e mostrar a saída de nós imediatamente.
  
    ```command
    clusrun /interleaved /nodegroup:linuxnodes echo \"for i in {1..10}; do echo \\\"\$i\\\"; sleep 1; done\" ^> script.sh; chmod +x script.sh; ./script.sh
    ```

> [!NOTE]
> Poderá ter de utilizar determinados caracteres de escape em **clusrun** comandos. Como é mostrado neste exemplo, utilize ^ numa linha de comandos para o escape a ">" símbolo.
> 
> 

## <a name="next-steps"></a>Passos seguintes
* Tente como aumentar verticalmente o um grande número de nós do cluster, ou tentar executar uma carga de trabalho do Linux no cluster. Por exemplo, consulte [NAMD executar com o Microsoft HPC Pack no Linux nós de computação em Azure](hpcpack-cluster-namd.md).
* Tente um cluster com [VMs com capacidade RDMA, de computação intensiva](../../windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) executar cargas de trabalho MPI. Por exemplo, consulte [cluster OpenFOAM executar com o Microsoft HPC Pack num RDMA Linux no Azure](hpcpack-cluster-openfoam.md).
* Se estiver interessado em trabalhar com Linux nós num cluster HPC Pack no local, consulte o [TechNet orientações](https://technet.microsoft.com/library/mt595803.aspx).

<!--Image references-->
[scenario]:media/hpcpack-cluster/scenario.png
[portal]:media/hpcpack-cluster/portal.png
[validate]:media/hpcpack-cluster/validate.png
[resources]:media/hpcpack-cluster/resources.png
[deploy]:media/hpcpack-cluster/deploy.png
[management]:media/hpcpack-cluster/management.png
[heatmap]:media/hpcpack-cluster/heatmap.png
[fileshareperms]:media/hpcpack-cluster/fileshare1.png
[filesharing]:media/hpcpack-cluster/fileshare2.png
[nfsauth]:media/hpcpack-cluster/nfsauth.png
[nfsshare]:media/hpcpack-cluster/nfsshare.png
[nfsperm]:media/hpcpack-cluster/nfsperm.png
[nfsmanage]:media/hpcpack-cluster/nfsmanage.png
