---
title: NAMD Microsoft HPC Pack em VMs do Linux | Microsoft Docs
description: "Implementar um cluster do Microsoft HPC Pack no Azure e execute uma simulação NAMD com charmrun em diversos nós de computação do Linux"
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager,hpc-pack
ms.assetid: 76072c6b-ac35-4729-ba67-0d16f9443bd7
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: big-compute
ms.date: 10/13/2016
ms.author: danlep
ms.openlocfilehash: 0c0b9875b4153edcc0ec0096577d041d394a842f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="run-namd-with-microsoft-hpc-pack-on-linux-compute-nodes-in-azure"></a>Executar o NAMD com o Microsoft HPC Pack em nós de computação do Linux no Azure
Este artigo mostra-lhe uma forma de executar uma carga de computação de alto desempenho (HPC) trabalho Linux em máquinas virtuais do Azure. Aqui, configurou um [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) cluster no Azure connosco de computação do Linux e executar um [NAMD](http://www.ks.uiuc.edu/Research/namd/) simulação calcule e visualizar a estrutura de um sistema biomolecular grandes.  

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

* **NAMD** (para o programa de Nanoscale Molecular Dynamics) é um pacote de dynamics molecular paralela foi concebido para simulação de elevado desempenho dos sistemas de grande biomolecular contendo até milhões de átomos. Exemplos destes sistemas incluem o vírus, estruturas de célula e proteins grandes. NAMD dimensiona para centenas de núcleos para simulações típicos e mais do que 500 000 núcleos para as maiores simulações.
* **Microsoft HPC Pack** fornece funcionalidades para serem executados em grande escala HPC e aplicações paralelas em clusters de computadores no local ou de máquinas virtuais do Azure. Originalmente desenvolvido como uma solução para cargas de trabalho do Windows HPC, HPC Pack agora suporta Linux HPC as aplicações em execução no Linux computação VMs de nó implementadas num cluster HPC Pack. Consulte [começar connosco de computação do Linux num cluster HPC Pack no Azure](hpcpack-cluster.md) para uma introdução.

## <a name="prerequisites"></a>Pré-requisitos
* **Nós de computação de cluster HPC Pack com o Linux** -implementar um cluster de pacote HPC connosco de computação do Linux no Azure utilizando um [modelo Azure Resource Manager](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/) ou um [script do PowerShell do Azure](hpcpack-cluster-powershell-script.md). Consulte [começar connosco de computação do Linux num cluster HPC Pack no Azure](hpcpack-cluster.md) para a pré-requisitos e passos para qualquer uma das opções. Se escolher a opção de implementação de script do PowerShell, consulte o ficheiro de configuração de exemplo nos ficheiros de exemplo no final deste artigo. Este ficheiro configura um cluster HPC Pack baseado no Azure constituída por um nó principal do Windows Server 2012 R2 e quatro nós de computação de grande CentOS 6.6 de tamanho. Personalize este ficheiro conforme necessário para o seu ambiente.
* **Ficheiros de software e tutorial NAMD** -software NAMD transferir para Linux a partir de [NAMD](http://www.ks.uiuc.edu/Research/namd/) site (registo necessário). Este artigo baseia-se no NAMD versão 2.10 e utiliza o [x86_64 de Linux (64-bit Intel/AMD com Ethernet)](http://www.ks.uiuc.edu/Development/Download/download.cgi?UserID=&AccessCode=&ArchiveID=1310) arquivo. Transfira também o [ficheiros tutorial NAMD](http://www.ks.uiuc.edu/Training/Tutorials/#namd). As transferências são ficheiros tar e precisa de uma ferramenta do Windows para extrair os ficheiros no nó principal do cluster. Para extrair os ficheiros, siga as instruções neste artigo. 
* **VMD** (opcional) - para ver os resultados da tarefa NAMD, transferir e instalar o programa de visualização molecular [VMD](http://www.ks.uiuc.edu/Research/vmd/) num computador à sua escolha. A versão atual é 1.9.2. Consulte VMD transferir o site para começar a utilizar.  

## <a name="set-up-mutual-trust-between-compute-nodes"></a>Configurar a confiança mútua entre os nós de computação
Executar uma tarefa de entre nós em vários nós do Linux requer que os nós para confiam uns nos outros (por **rsh** ou **ssh**). Quando cria o cluster HPC Pack com o script de implementação do Microsoft HPC Pack IaaS, o script configura automaticamente o confiança mútua permanente para a conta de administrador que especificar. Para os utilizadores de não administrador que criar no domínio do cluster, tem de definir temporária confiança mútua entre os nós quando uma tarefa é atribuída aos mesmos. Em seguida, destrua a relação depois de concluída a tarefa. Para efetuar este procedimento para cada utilizador, forneça um par de chaves RSA para o cluster que utiliza o HPC Pack para estabelecer uma relação de confiança. Siga as instruções.

### <a name="generate-an-rsa-key-pair"></a>Gerar um par de chaves RSA
É fácil gerar um par de chaves RSA, que contém uma chave pública e uma chave privada, executando o Linux **ssh-keygen** comando.

1. Inicie sessão no computador Linux.
2. Execute o seguinte comando:
   
   ```bash
   ssh-keygen -t rsa
   ```
   
   > [!NOTE]
   > Prima **Enter** para utilizar as predefinições, até que o comando é concluído. Não introduza uma frase de acesso aqui; Quando lhe for pedido para uma palavra-passe, basta premir **Enter**.
   > 
   > 
   
   ![Gerar um par de chaves RSA][keygen]
3. Altere o diretório para o diretório de ~/.ssh. A chave privada é armazenada no id_rsa e a chave pública no id_rsa.pub.
   
   ![Chaves públicas e privadas][keys]

### <a name="add-the-key-pair-to-the-hpc-pack-cluster"></a>Adicione o par de chaves para o cluster HPC Pack
1. [Estabelecer ligação ao ambiente de trabalho remoto](../../windows/connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) ao nó principal do VM através do domínio de credenciais fornecido quando implementou o cluster (por exemplo, hpc\clusteradmin). Gerir o cluster do nó principal.
2. Utilize os procedimentos do Windows Server standard para criar uma conta de utilizador de domínio no domínio do Active Directory do cluster. Por exemplo, utilize a ferramenta de utilizador do Active Directory e os computadores no nó principal. Os exemplos deste artigo partem do princípio de que criar um utilizador de domínio com o nome hpcuser no domínio hpclab (hpclab\hpcuser).
3. Adicione o utilizador de domínio para o cluster HPC Pack como um utilizador de cluster. Para obter instruções, consulte [adicionar ou remover utilizadores de cluster](https://technet.microsoft.com/library/ff919330.aspx).
4. Crie um ficheiro denominado C:\cred.xml e copie os dados de chave RSA para a mesma. Pode encontrar um exemplo nos ficheiros de exemplo no final deste artigo.
   
   ```
   <ExtendedData>
     <PrivateKey>Copy the contents of private key here</PrivateKey>
     <PublicKey>Copy the contents of public key here</PublicKey>
   </ExtendedData>
   ```
5. Abra uma linha de comandos e introduza o seguinte comando para definir os dados de credenciais para a conta de hpclab\hpcuser. Utilizar o **extendeddata** parâmetro para transmitir o nome do C:\cred.xml ficheiro que criou para os dados de chave.
   
   ```command
   hpccred setcreds /extendeddata:c:\cred.xml /user:hpclab\hpcuser /password:<UserPassword>
   ```
   
   Este comando seja concluído com êxito sem saída. Depois de definir as credenciais para as contas de utilizador que tem de executar tarefas, guarde o ficheiro de cred.xml numa localização segura ou eliminá-lo.
6. Se gerou o par de chaves RSA num de nós do Linux, lembre-se eliminar as chaves depois de concluir a utilizá-los. Pacote HPC não configurar a confiança mútua se encontrar um ficheiro id_rsa existente ou o ficheiro de id_rsa.pub.

> [!IMPORTANT]
> Não recomendamos a execução uma tarefa de Linux como um administrador de cluster num cluster partilhado, porque uma tarefa submetido por um administrador é executado sob a conta de raiz em nós do Linux. Uma tarefa submetido por um utilizador de administrador não é executado sob uma conta de utilizador de Linux local com o mesmo nome que o utilizador da tarefa. Neste caso, HPC Pack configura uma confiança mútua para este utilizador do Linux em todos os nós atribuído à tarefa. Pode configurar o utilizador de Linux manualmente em nós do Linux antes de executar a tarefa ou HPC Pack cria o utilizador automaticamente quando a tarefa for submetida. Se o pacote HPC cria o utilizador, o HPC Pack elimina-a após a conclusão da tarefa. Para reduzir a ameaça de segurança, as chaves são removidas depois de concluída a tarefa em nós.
> 
> 

## <a name="set-up-a-file-share-for-linux-nodes"></a>Configurar uma partilha de ficheiros para nós do Linux
Agora configurar uma partilha de ficheiros SMB e Monte a pasta partilhada em todos os nós do Linux para permitir que os nós do Linux para aceder aos ficheiros NAMD com um caminho comuns. Seguem-se os passos para montar uma pasta partilhada no nó principal. Para as distribuições, tais como CentOS 6.6 que atualmente não suportam o serviço de ficheiros do Azure, recomenda-se uma partilha. Se os nós do Linux suportarem uma partilha de ficheiros do Azure, consulte o artigo [como utilizar o File storage do Azure com o Linux](../../../storage/files/storage-how-to-use-files-linux.md). Para opções de partilha com o HPC Pack de ficheiros adicionais, consulte [começar connosco de computação do Linux num pacote de Cluster HPC no Azure](hpcpack-cluster.md).

1. Crie uma pasta no nó principal e partilhá-la para todos os utilizadores através da definição de privilégios de leitura/escrita. Neste exemplo, \\ \\CentOS66HN\Namd é o nome da pasta, onde CentOS66HN é o nome de anfitrião do nó principal.
2. Crie uma subpasta denominada namd2 na pasta partilhada. Namd2, crie outra subpasta denominada namdsample.
3. Extraia os ficheiros NAMD na pasta utilizando uma versão do Windows do **tar** ou outro utilitário do Windows que opera num. tar arquivos. 
   
   * Extrair o arquivo de tar NAMD para \\ \\CentOS66HN\Namd\namd2.
   * Extraia os ficheiros tutorial em \\ \\CentOS66HN\Namd\namd2\namdsample.
4. Abra uma janela do Windows PowerShell e execute os seguintes comandos para montar a pasta partilhada em nós do Linux.
   
    ```command
    clusrun /nodegroup:LinuxNodes mkdir -p /namd2
   
    clusrun /nodegroup:LinuxNodes mount -t cifs //CentOS66HN/Namd/namd2 /namd2 -o vers=2.1`,username=<username>`,password='<password>'`,dir_mode=0777`,file_mode=0777
    ```

O primeiro comando cria uma pasta denominada /namd2 em todos os nós no grupo de LinuxNodes. O segundo comando monta //CentOS66HN/Namd/namd2 a pasta partilhada para a pasta com dir_mode e file_mode bits definido como 777. O *username* e *palavra-passe* no comando devem ser as credenciais de um utilizador no nó principal.

> [!NOTE]
> O "\`" símbolo o segundo comando é um símbolo de escape para o PowerShell. "\`," significa "," (caráter vírgula) é uma parte do comando.
> 
> 

## <a name="create-a-bash-script-to-run-a-namd-job"></a>Criar um script de deteção para executar uma tarefa NAMD
A tarefa NAMD tem um *nodelist* de ficheiros para **charmrun** para determinar o número de nós a utilizar quando iniciar NAMD processos. Utilizar um script de deteção que gera o ficheiro nodelist e executa **charmrun** com este ficheiro nodelist. Em seguida, pode submeter uma tarefa NAMD no Gestor de clusters HPC que chama este script.

Com um editor de texto à sua escolha, crie um script de Bash na pasta que contém os ficheiros de programa NAMD /namd2 e dê-lhe o nome hpccharmrun.sh. Para uma rápida prova de conceito, copie o script de hpccharmrun.sh de exemplo fornecido no final deste artigo e aceda a [submeter uma tarefa NAMD](#submit-a-namd-job).

> [!TIP]
> Guarde o script como um ficheiro de texto com o Linux linha endings (LF apenas, não CR LF). Isto garante que é executada corretamente em nós do Linux.
> 
> 

Seguem-se detalhes sobre o que faz esta scripts de bash. 

1. Defina algumas variáveis.
   
   ```bash
   #!/bin/bash
   
   # The path of this script
   SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"
   # Charmrun command
   CHARMRUN=${SCRIPT_PATH}/charmrun
   # Argument of ++nodelist
   NODELIST_OPT="++nodelist"
   # Argument of ++p
   NUMPROCESS="+p"
   ```
2. Obter informações do nó de variáveis de ambiente. $NODESCORES armazena uma lista de palavras de divisão de $CCP_NODES_CORES. $COUNT é o tamanho dos $NODESCORES.
   
   ```
   # Get node information from the environment variables
   NODESCORES=(${CCP_NODES_CORES})
   COUNT=${#NODESCORES[@]}
   ```    
   
   O formato da variável de CCP_NODES_CORES $ é o seguinte:
   
   ```
   <Number of nodes> <Name of node1> <Cores of node1> <Name of node2> <Cores of node2>…
   ```
   
   Esta variável lista o número total de nós, nomes de nó e número de núcleos em cada nó que estão atribuídas à tarefa. Por exemplo, se a tarefa tem 10 núcleos para ser executada, o valor de $CCP_NODES_CORES é semelhante a:
   
   ```
   3 CENTOS66LN-00 4 CENTOS66LN-01 4 CENTOS66LN-03 2
   ```
3. Se não for definida a variável CCP_NODES_CORES $, inicie **charmrun** diretamente. (Isto só deve ocorrer quando executa este script diretamente nos nós do Linux.)
   
   ```
   if [ ${COUNT} -eq 0 ]
   then
     # CCP_NODES is_CORES is not found or is empty, so just run charmrun without nodelist arg.
     #echo ${CHARMRUN} $*
     ${CHARMRUN} $*
   ```
4. Ou criar um ficheiro de nodelist para **charmrun**.
   
   ```
   else
     # Create the nodelist file
     NODELIST_PATH=${SCRIPT_PATH}/nodelist_$$
   
     # Write the head line
     echo "group main" > ${NODELIST_PATH}
   
     # Get every node name and number of cores and write into the nodelist file
     I=1
     while [ ${I} -lt ${COUNT} ]
     do
         echo "host ${NODESCORES[${I}]} ++cpus ${NODESCORES[$(($I+1))]}" >> ${NODELIST_PATH}
         let "I=${I}+2"
     done
   ```
5. Executar **charmrun** com o ficheiro nodelist, obter o estado de retorno e remova o ficheiro de nodelist no final.
   
   ${CCP_NUMCPUS} é outra variável de ambiente definida pelo nó principal do HPC Pack. Armazena o número de núcleos totais atribuído a esta tarefa. Iremos utilizá-la para especificar o número de processos para charmrun.
   
   ```
   # Run charmrun with nodelist arg
   #echo ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*
   ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*
   
   RTNSTS=$?
   rm -f ${NODELIST_PATH}
   fi
   
   ```
6. Sair com o **charmrun** estado devolvido.
   
   ```
   exit ${RTNSTS}
   ```

Segue-se as informações no ficheiro nodelist, o que gera o script:

```
group main
host <Name of node1> ++cpus <Cores of node1>
host <Name of node2> ++cpus <Cores of node2>
…
```

Por exemplo:

```
group main
host CENTOS66LN-00 ++cpus 4
host CENTOS66LN-01 ++cpus 4
host CENTOS66LN-03 ++cpus 2
```

## <a name="submit-a-namd-job"></a>Submeter uma tarefa NAMD
Agora, está pronto para submeter uma tarefa NAMD no Gestor de clusters HPC.

1. Ligar ao seu nó principal do cluster e iniciar o Gestor de clusters HPC.
2. No **gestão de recursos**, certifique-se de que os nós de computação do Linux no **Online** estado. Se não estiverem, selecione-os e clique em **colocar Online**.
3. No **de tarefas gestão**, clique em **nova tarefa**.
4. Introduza um nome para a tarefa como *hpccharmrun*.
   
   ![Nova tarefa HPC][namd_job]
5. No **detalhes da tarefa** página **tarefa recursos**, selecione o tipo de recurso como **nó** e defina o **mínimo** para 3. , executamos a tarefa em três nós do Linux e cada nó tem quatro núcleos.
   
   ![Recursos de tarefa][job_resources]
6. Clique em **editar tarefas** na navegação à esquerda e, em seguida, clique em **adicionar** para adicionar uma tarefa para a tarefa.    
7. No **detalhes da tarefa e redirecionamento de e/s** página, defina os seguintes valores:
   
   * **Linha de comandos**-
     `/namd2/hpccharmrun.sh ++remote-shell ssh /namd2/namd2 /namd2/namdsample/1-2-sphere/ubq_ws_eq.conf > /namd2/namd2_hpccharmrun.log`
     
     > [!TIP]
     > A linha de comando anterior é um único comando sem quebras de linha. Encapsula num wrapper apareça em várias linhas em **linha de comandos**.
     > 
     > 
   * **Diretório de trabalho** -/namd2
   * **Mínimo** - 3
     
     ![Detalhes da tarefa][task_details]
     
     > [!NOTE]
     > Definir o diretório de trabalho aqui porque **charmrun** tenta navegar para o mesmo diretório de trabalho em cada nó. Se o diretório de trabalho não estiver configurado, o HPC Pack inicia o comando numa pasta aleatoriamente nomeada criada num de nós do Linux. Isto faz com que o seguinte erro nos outros nós: `/bin/bash: line 37: cd: /tmp/nodemanager_task_94_0.mFlQSN: No such file or directory.` para evitar este problema, especifique um caminho de pasta que possa ser acedido por todos os nós, como o diretório de trabalho.
     > 
     > 
8. Clique em **OK** e, em seguida, clique em **submeter** para executar esta tarefa.
   
   Por predefinição, o HPC Pack submete a tarefa como a sua conta de utilizador com sessão iniciada atual. Uma caixa de diálogo poderão solicitar-lhe para introduzir o nome de utilizador e palavra-passe depois de clicar em **submeter**.
   
   ![Credenciais de tarefa][creds]
   
   Em algumas condições, o HPC Pack memorizou as informações do utilizador antes de entrada e não mostrar esta caixa de diálogo. Para tornar o HPC Pack voltar a mostrar, introduza o seguinte comando numa linha de comandos e, em seguida, submeter a tarefa.
   
   ```command
   hpccred delcreds
   ```
9. A tarefa demora alguns minutos a concluir.
10. Localizar o registo da tarefa em \\ <headnodeName>\Namd\namd2\namd2_hpccharmrun.log e os ficheiros de saída no \\ <headnodeName>\Namd\namd2\namdsample\1-2-sphere\.
11. Opcionalmente, inicie VMD para ver os resultados da tarefa. Os passos para visualizar o NAMD saída ficheiros (neste caso, um ubiquitin protein molecule num sphere máximo) estão fora do âmbito deste artigo. Consulte [NAMD Tutorial](http://www.life.illinois.edu/emad/biop590c/namd-tutorial-unix-590C.pdf) para obter mais detalhes.
    
    ![Resultados da tarefa][vmd_view]

## <a name="sample-files"></a>Ficheiros de exemplo
### <a name="sample-xml-configuration-file-for-cluster-deployment-by-powershell-script"></a>Ficheiro de configuração do XML de exemplo para implementação de cluster por script do PowerShell
```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
      <Location>West US</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>HeadNodeAsDC</DCOption>
    <DomainFQDN>hpclab.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>CentOS66HN</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>Large</VMSize>
    <EnableRESTAPI />
    <EnableWebPortal />
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>CentOS66LN-%00%</VMNamePattern>
    <ServiceName>MyLnxCNService</ServiceName>
     <VMSize>Large</VMSize>
     <NodeCount>4</NodeCount>
    <ImageName>5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-66-20150325</ImageName>
  </LinuxComputeNodes>
</IaaSClusterConfig>    
```

### <a name="sample-credxml-file"></a>Ficheiro de cred.xml de exemplo
```
<ExtendedData>
  <PrivateKey>-----BEGIN RSA PRIVATE KEY-----
MIIEpQIBAAKCAQEAxJKBABhnOsE9eneGHvsjdoXKooHUxpTHI1JVunAJkVmFy8JC
qFt1pV98QCtKEHTC6kQ7tj1UT2N6nx1EY9BBHpZacnXmknpKdX4Nu0cNlSphLpru
lscKPR3XVzkTwEF00OMiNJVknq8qXJF1T3lYx3rW5EnItn6C3nQm3gQPXP0ckYCF
Jdtu/6SSgzV9kaapctLGPNp1Vjf9KeDQMrJXsQNHxnQcfiICp21NiUCiXosDqJrR
AfzePdl0XwsNngouy8t0fPlNSngZvsx+kPGh/AKakKIYS0cO9W3FmdYNW8Xehzkc
VzrtJhU8x21hXGfSC7V0ZeD7dMeTL3tQCVxCmwIDAQABAoIBAQCve8Jh3Wc6koxZ
qh43xicwhdwSGyliZisoozYZDC/ebDb/Ydq0BYIPMiDwADVMX5AqJuPPmwyLGtm6
9hu5p46aycrQ5+QA299g6DlF+PZtNbowKuvX+rRvPxagrTmupkCswjglDUEYUHPW
05wQaNoSqtzwS9Y85M/b24FfLeyxK0n8zjKFErJaHdhVxI6cxw7RdVlSmM9UHmah
wTkW8HkblbOArilAHi6SlRTNZG4gTGeDzPb7fYZo3hzJyLbcaNfJscUuqnAJ+6pT
iY6NNp1E8PQgjvHe21yv3DRoVRM4egqQvNZgUbYAMUgr30T1UoxnUXwk2vqJMfg2
Nzw0ESGRAoGBAPkfXjjGfc4HryqPkdx0kjXs0bXC3js2g4IXItK9YUFeZzf+476y
OTMQg/8DUbqd5rLv7PITIAqpGs39pkfnyohPjOe2zZzeoyaXurYIPV98hhH880uH
ZUhOxJYnlqHGxGT7p2PmmnAlmY4TSJrp12VnuiQVVVsXWOGPqHx4S4f9AoGBAMn/
vuea7hsCgwIE25MJJ55FYCJodLkioQy6aGP4NgB89Azzg527WsQ6H5xhgVMKHWyu
Q1snp+q8LyzD0i1veEvWb8EYifsMyTIPXOUTwZgzaTTCeJNHdc4gw1U22vd7OBYy
nZCU7Tn8Pe6eIMNztnVduiv+2QHuiNPgN7M73/x3AoGBAOL0IcmFgy0EsR8MBq0Z
ge4gnniBXCYDptEINNBaeVStJUnNKzwab6PGwwm6w2VI3thbXbi3lbRAlMve7fKK
B2ghWNPsJOtppKbPCek2Hnt0HUwb7qX7Zlj2cX/99uvRAjChVsDbYA0VJAxcIwQG
TxXx5pFi4g0HexCa6LrkeKMdAoGAcvRIACX7OwPC6nM5QgQDt95jRzGKu5EpdcTf
g4TNtplliblLPYhRrzokoyoaHteyxxak3ktDFCLj9eW6xoCZRQ9Tqd/9JhGwrfxw
MS19DtCzHoNNewM/135tqyD8m7pTwM4tPQqDtmwGErWKj7BaNZARUlhFxwOoemsv
R6DbZyECgYEAhjL2N3Pc+WW+8x2bbIBN3rJcMjBBIivB62AwgYZnA2D5wk5o0DKD
eesGSKS5l22ZMXJNShgzPKmv3HpH22CSVpO0sNZ6R+iG8a3oq4QkU61MT1CfGoMI
a8lxTKnZCsRXU1HexqZs+DSc+30tz50bNqLdido/l5B4EJnQP03ciO0=
-----END RSA PRIVATE KEY-----</PrivateKey>
  <PublicKey>ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDEkoEAGGc6wT16d4Ye+yN2hcqigdTGlMcjUlW6cAmRWYXLwkKoW3WlX3xAK0oQdMLqRDu2PVRPY3qfHURj0EEellpydeaSekp1fg27Rw2VKmEumu6Wxwo9HddXORPAQXTQ4yI0lWSerypckXVPeVjHetbkSci2foLedCbeBA9c/RyRgIUl227/pJKDNX2Rpqly0sY82nVWN/0p4NAyslexA0fGdBx+IgKnbU2JQKJeiwOomtEB/N492XRfCw2eCi7Ly3R8+U1KeBm+zH6Q8aH8ApqQohhLRw71bcWZ1g1bxd6HORxXOu0mFTzHbWFcZ9ILtXRl4Pt0x5Mve1AJXEKb username@servername;</PublicKey>
</ExtendedData>
```

### <a name="sample-hpccharmrunsh-script"></a>Exemplo de script hpccharmrun.sh
```
#!/bin/bash

# The path of this script
SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"
# Charmrun command
CHARMRUN=${SCRIPT_PATH}/charmrun
# Argument of ++nodelist
NODELIST_OPT="++nodelist"
# Argument of ++p
NUMPROCESS="+p"

# Get node information from ENVs
# CCP_NODES_CORES=3 CENTOS66LN-00 4 CENTOS66LN-01 4 CENTOS66LN-03 4
NODESCORES=(${CCP_NODES_CORES})
COUNT=${#NODESCORES[@]}

if [ ${COUNT} -eq 0 ]
then
    # If CCP_NODES_CORES is not found or is empty, just run the charmrun without nodelist arg.
    #echo ${CHARMRUN} $*
    ${CHARMRUN} $*
else
    # Create the nodelist file
    NODELIST_PATH=${SCRIPT_PATH}/nodelist_$$

    # Write the head line
    echo "group main" > ${NODELIST_PATH}

    # Get every node name & cores and write into the nodelist file
    I=1
    while [ ${I} -lt ${COUNT} ]
    do
        echo "host ${NODESCORES[${I}]} ++cpus ${NODESCORES[$(($I+1))]}" >> ${NODELIST_PATH}
        let "I=${I}+2"
    done

    # Run the charmrun with nodelist arg
    #echo ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*
    ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*

    RTNSTS=$?
    rm -f ${NODELIST_PATH}
fi

exit ${RTNSTS}
```

 

<!--Image references-->
[keygen]:media/hpcpack-cluster-namd/keygen.png
[keys]:media/hpcpack-cluster-namd/keys.png
[namd_job]:media/hpcpack-cluster-namd/namd_job.png
[job_resources]:media/hpcpack-cluster-namd/job_resources.png
[creds]:media/hpcpack-cluster-namd/creds.png
[task_details]:media/hpcpack-cluster-namd/task_details.png
[vmd_view]:media/hpcpack-cluster-namd/vmd_view.png
