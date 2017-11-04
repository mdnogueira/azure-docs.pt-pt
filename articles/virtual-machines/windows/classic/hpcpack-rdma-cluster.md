---
title: "Configurar um cluster do Windows RDMA para executar aplicações MPI | Microsoft Docs"
description: "Saiba como criar um cluster Windows HPC Pack com tamanho H16r, H16mr, A8, A9 VMs para utilizar a rede de Azure RDMA para executar aplicações MPI."
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-service-management,hpc-pack
ms.assetid: 7d9f5bc8-012f-48dd-b290-db81c7592215
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 06/01/2017
ms.author: danlep
ms.openlocfilehash: 19be1d693fe13af0f6c1ab0cb6f7bc829b9fad5a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-a-windows-rdma-cluster-with-hpc-pack-to-run-mpi-applications"></a>Configurar um cluster do Windows RDMA com o HPC Pack para executar aplicações MPI
Configurar um cluster do Windows RDMA no Azure com [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) e [tamanhos de VM de computação de elevado desempenho](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para executar aplicações de Interface de passagem de mensagens (MPI) paralelas. Quando configurar com capacidade RDMA, baseado no Windows Server nós num cluster HPC Pack, aplicações MPI comunicam forma eficiente através de uma baixa latência, a rede de alto débito no Azure baseia-se na tecnologia de (RDMA) de acesso remoto direto à memória.

Se pretender executar cargas de trabalho MPI em VMs do Linux que acedam à rede Azure RDMA, consulte [configurar um cluster de Linux RDMA para executar aplicações MPI](../../linux/classic/rdma-cluster.md).

## <a name="hpc-pack-cluster-deployment-options"></a>Opções de implementação de cluster HPC Pack
Pacote HPC da Microsoft é uma ferramenta fornecida sem custos adicionais para criar HPC clusters no local ou no Azure para executar o Windows ou Linux HPC aplicações. Pacote HPC inclui um ambiente de tempo de execução para a implementação Microsoft da mensagem de transmissão de Interface para Windows (MS-MPI). Quando utilizado com instâncias com capacidade RDMA a executar o sistema operativo Windows Server suportado, HPC Pack fornece uma opção para executar aplicações MPI do Windows que acedam à rede Azure RDMA eficiente. 

Este artigo apresenta dois cenários e ligações para orientações detalhadas para configurar um cluster do Windows RDMA com o Microsoft HPC Pack. 

* Cenário 1. Implementar instâncias de função de trabalho de computação intensiva (PaaS)
* Cenário 2. Implementar nós de computação de computação intensiva VMs (IaaS)

Para a pré-requisitos gerais utilizar instâncias intensivas de computação com o Windows, consulte [tamanhos de VM de computação de elevado desempenho](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="scenario-1-deploy-compute-intensive-worker-role-instances-paas"></a>Cenário 1: Implementar instâncias de função de trabalho de computação intensiva (PaaS)
De um cluster HPC Pack existente, adicione recursos de computação adicional em instâncias de função de trabalho do Azure (nós do Azure) em execução num serviço cloud (PaaS). Esta funcionalidade, também designado por "impulsar para o Azure" do pacote HPC, suporta uma variedade de tamanhos para as instâncias da função de trabalho. Ao adicionar os nós do Azure, tem de especificar um dos tamanhos com capacidade RDMA.

Seguem-se considerações e os passos para impulsar para o rdma e instâncias do Azure existente de um (normalmente, no local) cluster. Utilize procedimentos semelhantes para adicionar instâncias de função de trabalho para um nó principal do HPC Pack implementado na VM do Azure.

> [!NOTE]
> Para um tutorial para impulsar para o Azure com o HPC Pack, consulte [configurar um cluster de híbrido com o HPC Pack](../../../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md). Tenha em atenção os seguintes passos aplicam-se especificamente para o rdma e as considerações de nós do Azure.
> 
> 

![Impulsar para o Azure][burst]

### <a name="steps"></a>Passos
1. **Implementar e configurar um nó principal do HPC Pack 2012 R2**
   
    Transferir o pacote de instalação do HPC Pack mais recente do [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=49922). Para requisitos e instruções para preparar a implementação de uma rajada do Azure, consulte [Impulsar para instâncias de trabalho do Azure com o Microsoft HPC Pack](https://technet.microsoft.com/library/gg481749.aspx).
2. **Configurar um certificado de gestão na subscrição do Azure**
   
    Configure um certificado para proteger a ligação entre o nó principal e o Azure. Para opções e procedimentos, consulte [cenários para configurar o certificado de gestão do Azure para HPC Pack](http://technet.microsoft.com/library/gg481759.aspx). Para implementações de teste, o HPC Pack instala um predefinido Microsoft HPC Azure certificado de gestão pode carregar rapidamente a sua subscrição do Azure.
3. **Criar um novo serviço de nuvem e de uma conta de armazenamento**
   
    Utilize o portal do Azure para criar um serviço em nuvem e de uma conta de armazenamento para a implementação numa região onde estão disponíveis as instâncias com capacidade RDMA.
4. **Criar um modelo de nó do Azure**
   
    Utilize o Assistente de modelo do nó de criação no Gestor de clusters HPC. Para obter os passos, consulte [criar um modelo do Azure nó](http://technet.microsoft.com/library/gg481758.aspx#BKMK_Templ) nos "Passos para implementar o Azure nós com o Microsoft HPC Pack".
   
    Para testes iniciais, sugerimos que configurar uma política de disponibilidade manual no modelo.
5. **Adicionar nós ao cluster**
   
    Utilize o Assistente de nó de adicionar no Gestor de clusters HPC. Para obter mais informações, consulte [adicionar nós do Azure para o Cluster do Windows HPC](http://technet.microsoft.com/library/gg481758.aspx#BKMK_Add).
   
    Quando especificar o tamanho de nós, selecione um dos tamanhos de instância com capacidade RDMA.
   
   > [!NOTE]
   > Em cada rajada para implementação do Azure com as instâncias intensivas de computação, o HPC Pack implementa automaticamente um mínimo de duas instâncias com capacidade RDMA (por exemplo, A8) como nós de proxy, além das instâncias da função de trabalho do Azure que especificar. Os nós de proxy utilizam núcleos que estão atribuídos à subscrição e pagar juntamente com as instâncias da função de trabalho do Azure.
   > 
   > 
6. **Iniciar (aprovisionar) os nós e colocá-los online para executar tarefas**
   
    Selecione os nós e utilize o **iniciar** ação no Gestor de clusters HPC. Quando o aprovisionamento estiver concluído, selecione os nós e utilize o **colocar Online** ação no Gestor de clusters HPC. Os nós, estará pronto executar tarefas.
7. **Submeter tarefas ao cluster**
   
   Utilize as ferramentas de submissão de tarefa HPC Pack para executar tarefas de cluster. Consulte [pacote HPC da Microsoft: gestão de tarefas](http://technet.microsoft.com/library/jj899585.aspx).
8. **A parar (deprovision) nós**
   
   Quando tiver terminado de tarefas em execução, colocar offline os nós e utilizar o **parar** ação no Gestor de clusters HPC.

## <a name="scenario-2-deploy-compute-nodes-in-compute-intensive-vms-iaas"></a>Cenário 2: Implementar nós de computação intensiva VMs (IaaS) de computação
Neste cenário, implemente o nó principal do HPC Pack e nós de computação de cluster em VMs de uma rede virtual do Azure. Pacote HPC fornece vários [opções de implementação em VMs do Azure](../../linux/hpcpack-cluster-options.md), incluindo scripts de implementação automática e modelos de início rápido do Azure. Por exemplo, as considerações e os passos seguintes ajudá-lo para utilizar o [script de implementação de HPC Pack IaaS](hpcpack-cluster-powershell-script.md) para automatizar a implementação de um cluster HPC Pack 2012 R2 no Azure.

![Cluster em VMs do Azure][iaas]

### <a name="steps"></a>Passos
1. **Criar um nó principal do cluster e as VMs do nó de computação ao executar o script de implementação do HPC Pack IaaS num computador cliente**
   
    Transferir o pacote de Script de implementação do HPC Pack IaaS do [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=49922).
   
    Para preparar o computador cliente, criar o ficheiro de configuração do script e execute o script, consulte [criar um Cluster HPC com o script de implementação do HPC Pack IaaS](hpcpack-cluster-powershell-script.md). 
   
    Para implementar nós de computação com capacidade RDMA, tenha em atenção as seguintes considerações adicionais:
   
   * **Rede virtual**: Especifique uma nova rede virtual numa região na qual o rdma e tamanho da instância que pretende utilizar está disponível.
   * **Sistema operativo Windows Server**: para suportar a conetividade RDMA, especifique um sistema operativo Windows Server 2012 R2 ou Windows Server 2012 para o nó de computação VMs.
   * **Serviços em nuvem**: Recomendamos a implementação do nó principal no serviço de uma nuvem e os nós de computação num serviço cloud diferente.
   * **Tamanho de nó de HEAD**: para este cenário, considere um tamanho de, pelo menos, A4 (Extra grande) para o nó principal.
   * **Extensão de HpcVmDrivers**: O script de implementação instala o agente da VM do Azure e a extensão de HpcVmDrivers automaticamente quando implementar nós de computação de A8 ou A9 tamanho com um sistema operativo Windows Server. HpcVmDrivers instala controladores no nó de computação VMs, pelo que se possam ligar à rede RDMA. Em VMs de série de H com capacidade RDMA, tem de instalar manualmente a extensão de HpcVmDrivers. Consulte [tamanhos de VM de computação de elevado desempenho](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
   * **Configuração de rede de cluster**: O script de implementação configura automaticamente o cluster HPC Pack na topologia 5 (todos os nós na rede empresarial). Esta topologia é necessária para todas as implementações de cluster HPC Pack em VMs. Não altere a topologia de rede de cluster mais tarde.
2. **Coloque online os nós de computação para executar tarefas**
   
    Selecione os nós e utilize o **colocar Online** ação no Gestor de clusters HPC. Os nós, estará pronto executar tarefas.
3. **Submeter tarefas ao cluster**
   
    Ligar ao nó principal para submeter tarefas ou configurar um computador no local para efetuar este procedimento. Para informações, consulte [cluster submeter tarefas para um HPC no Azure](../../virtual-machines-windows-hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
4. **Colocar offline os nós e pare (desalocar)-las**
   
    Quando tiver terminado de tarefas em execução, colocar offline os nós no Gestor de clusters HPC. Em seguida, utilize ferramentas de gestão do Azure para encerrá-las.

## <a name="run-mpi-applications-on-the-cluster"></a>Executar aplicações MPI no cluster
### <a name="example-run-mpipingpong-on-an-hpc-pack-cluster"></a>Exemplo: Executar mpipingpong num cluster HPC Pack
Para verificar uma implementação de pacote HPC das instâncias com capacidade RDMA, execute o HPC Pack **mpipingpong** comando no cluster. **mpipingpong** envia pacotes de dados entre nós emparelhado repetidamente para calcular valores de latência e débito e estatísticas para a rede de aplicação com capacidade RDMA. Este exemplo mostra um padrão normal para executar uma tarefa MPI (neste caso, **mpipingpong**) utilizando o cluster **mpiexec** comando.

Este exemplo assume que adicionou nós do Azure numa configuração de "rajada para o Azure" ([cenário 1](#scenario-1.-deploy-compute-intensive-worker-role-instances-\(PaaS\) in this article). Se tiver implementado o HPC Pack num cluster de VMs do Azure, terá de modificar a sintaxe de comando para especificar um grupo de outro nó e definir variáveis de ambiente adicionais para direcionar o tráfego de rede para a rede RDMA.

Para executar mpipingpong no cluster:

1. No nó principal ou num computador cliente está corretamente configurada, abra uma linha de comandos.
2. Para estimar latência entre pares de nós numa implementação do Azure rajada de quatro nós, escreva o seguinte comando para submeter uma tarefa para executar mpipingpong com um tamanho de pacotes pequenos e número de iterações:
   
    ```Command
    job submit /nodegroup:azurenodes /numnodes:4 mpiexec -c 1 -affinity mpipingpong -p 1:100000 -op -s nul
    ```
   
    O comando devolve o ID da tarefa que é submetido.
   
    Se implementou o cluster HPC Pack implementado em VMs do Azure, especifique um grupo de nó que contenha implementadas num serviço em nuvem única de VMs de nó de computação e modificar o **mpiexec** comando da seguinte forma:
   
    ```Command
    job submit /nodegroup:vmcomputenodes /numnodes:4 mpiexec -c 1 -affinity -env MSMPI_DISABLE_SOCK 1 -env MSMPI_PRECONNECT all -env MPICH_NETMASK 172.16.0.0/255.255.0.0 mpipingpong -p 1:100000 -op -s nul
    ```
3. Quando a tarefa estiver concluída, para ver o resultado (neste caso, o resultado da tarefa 1 da tarefa), escreva o seguinte
   
    ```Command
    task view <JobID>.1
    ```
   
    onde &lt; *JobID* &gt; é o ID da tarefa que foi submetido.
   
    O resultado inclui resultados de latência semelhantes ao seguinte.
   
    ![Latência de pong de ping][pingpong1]
4. Para estimar o débito entre pares de nós de rajada do Azure, escreva o seguinte comando para submeter uma tarefa para ser executada **mpipingpong** com um tamanho de pacote e alguns iterações:
   
    ```Command
    job submit /nodegroup:azurenodes /numnodes:4 mpiexec -c 1 -affinity mpipingpong -p 4000000:1000 -op -s nul
    ```
   
    O comando devolve o ID da tarefa que é submetido.
   
    Num cluster HPC Pack implementado em VMs do Azure, modifique o comando conforme indicado no passo 2.
5. Quando a tarefa estiver concluída, para ver o resultado (neste caso, o resultado da tarefa 1 da tarefa), escreva o seguinte:
   
    ```Command
    task view <JobID>.1
    ```
   
   O resultado inclui resultados de débito semelhantes ao seguinte.
   
   ![Débito de pong de ping][pingpong2]

### <a name="mpi-application-considerations"></a>Considerações de aplicação de MPI
Seguem-se considerações para executar aplicações MPI com o HPC Pack no Azure. Algumas aplicam-se apenas para implementações de nós do Azure (instâncias de função de trabalho adicionadas numa configuração de "rajada para o Azure").

* Instâncias de função de trabalho num serviço em nuvem são periodicamente reaprovisionadas sem aviso prévio pelo Azure (por exemplo, para manutenção do sistema ou no caso de que falha de uma instância). Se uma instância é reaprovisionada enquanto estiver em execução uma tarefa MPI, a instância perde os dados e devolve o estado quando esta foi implementada pela primeira vez, que pode fazer com que o trabalho MPI a falhar. Os mais nós que utilizar para uma única tarefa MPI e mais tempo a tarefa é executada, o mais provável que uma das instâncias é reaprovisionada enquanto uma tarefa está em execução. Considere também a esta se designar um único nó na implementação como um servidor de ficheiros.
* Para executar tarefas MPI no Azure, não terá de utilizar as instâncias com capacidade RDMA. Pode utilizar qualquer tamanho da instância que é suportado pelo pacote HPC. No entanto, as instâncias com capacidade RDMA são recomendadas para executar tarefas MPI relativamente em grande escala, que são sensíveis a latência e a largura de banda da rede que liga os nós. Se utilizar outros tamanhos para executar tarefas MPI sensíveis à latência e largura de banda, recomendamos a execução de tarefas pequenas, na qual uma única tarefa é executada em apenas alguns nós.
* As aplicações implementadas para instâncias do Azure estão sujeitas os termos de licenciamento associados à aplicação. Consulte o fornecedor de qualquer aplicação comercial para restrições de licenciamento, ou outras para executar na nuvem. Nem todos os fornecedores oferecem licenciamento pay as you go.
* Instâncias do Azure necessita de configuração adicional para acesso local nós, partilhas e servidores de licenças. Por exemplo, para permitir que os nós do Azure aceder a um servidor de licenças no local, pode configurar uma rede virtual do Azure do site para site.
* Para executar aplicações MPI nas instâncias do Azure, registar cada aplicação de MPI Firewall do Windows nas instâncias, executando o **hpcfwutil** comando. Isto permite que as comunicações de MPI seja realizada numa porta que é atribuída dinamicamente através da firewall.
  
  > [!NOTE]
  > Para rajada para implementações do Azure, também pode configurar um comando de exceção de firewall para executar automaticamente em todos os nós do Azure novos que são adicionados ao cluster. Depois de executar o **hpcfwutil** comando e certifique-se de que a aplicação funciona, adicione o comando para um script de arranque para os nós do Azure. Para obter mais informações, consulte [utilizar um Script de arranque para nós do Azure](https://technet.microsoft.com/library/jj899632.aspx).
  > 
  > 
* Pacote HPC utiliza a variável de ambiente de cluster CCP_MPI_NETMASK para especificar um intervalo de endereços aceitáveis para a comunicação de MPI. A partir de HPC Pack 2012 R2, a variável de ambiente de cluster CCP_MPI_NETMASK afeta apenas a comunicação de MPI entre nós de computação de cluster associados a um domínio (no local ou em VMs do Azure). A variável é ignorada por nós adicionados numa rajada para configuração do Azure.
* Não é possível executar tarefas MPI entre instâncias do Azure que são implementadas nos serviços de nuvem diferente (por exemplo, no rajada para implementações do Azure com os modelos de nó diferente ou nós de computação de VM do Azure implementados em múltiplos serviços em nuvem). Se tiver múltiplas implementações de nó do Azure são iniciadas com modelos de nó diferente, tem de executar a tarefa MPI no apenas um conjunto de nós do Azure.
* Quando adicionar nós do Azure para o cluster e colocá-los online, o serviço de agendador de tarefas HPC tentar imediatamente iniciar tarefas em nós. Se apenas uma parte da sua carga de trabalho pode ser executado no Azure, certifique-se de que atualizar ou criar modelos de tarefas para definir o que tipos de tarefas podem ser executados no Azure. Por exemplo, para garantir que tarefas submetidas com um modelo de tarefa apenas executam em nós do Azure, adicione a propriedade do nó grupos ao modelo de tarefa e selecione AzureNodes como o valor necessário. Para criar grupos personalizados para os nós do Azure, utilize o cmdlet Add-HpcGroup HPC PowerShell.

## <a name="next-steps"></a>Passos seguintes
* Como alternativa à utilização HPC Pack, desenvolva com o serviço Azure Batch para executar aplicações MPI no gerido conjuntos de nós de computação no Azure. Consulte [utilizar tarefas de várias instâncias para executar aplicações de Interface de passagem de mensagens (MPI) no Azure Batch](../../../batch/batch-mpi.md).
* Se pretender executar Linux MPI aplicações que acedam à rede Azure RDMA, consulte [configurar um cluster de Linux RDMA para executar aplicações MPI](../../linux/classic/rdma-cluster.md).

<!--Image references-->
[burst]:media/hpcpack-rdma-cluster/burst.png
[iaas]:media/hpcpack-rdma-cluster/iaas.png
[pingpong1]:media/hpcpack-rdma-cluster/pingpong1.png
[pingpong2]:media/hpcpack-rdma-cluster/pingpong2.png
