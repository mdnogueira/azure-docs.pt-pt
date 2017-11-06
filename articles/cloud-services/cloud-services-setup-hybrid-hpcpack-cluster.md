---
title: "Configurar um cluster HPC Pack de híbridos no Azure | Microsoft Docs"
description: "Saiba como utilizar o Microsoft HPC Pack e o Azure para configurar uma pequena, híbrida computação de alto desempenho cluster (HPC)"
services: cloud-services
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: hpc-pack
ms.assetid: 
ms.service: cloud-services
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2017
ms.author: danlep
ms.openlocfilehash: ad5c13723eef352148a40e3e7f4f2ff616867296
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2017
---
# <a name="set-up-a-hybrid-high-performance-computing-hpc-cluster-with-microsoft-hpc-pack-and-on-demand-azure-compute-nodes"></a>Configurar um cluster (HPC) com o Microsoft HPC Pack e os nós de computação do Azure a pedido de computação de alto no desempenho por híbrida
Utilize o Microsoft HPC Pack 2012 R2 e o Azure para configurar um pequeno, híbrida computação de alto desempenho cluster (HPC). O cluster apresentado neste artigo é composta por um nó principal de pacote HPC no local e alguns computação nós implementar a pedido num Azure serviço em nuvem. Em seguida, pode executar tarefas de computação no cluster híbrida.

![Cluster HPC de híbrida][Overview] 

Este tutorial mostra uma abordagem, por vezes denominada cluster "rajada para a nuvem" para utilizar recursos do Azure, dimensionáveis e a pedido para executar aplicações de computação intensivas.

Este tutorial não assume nenhum experiência anterior com clusters de computação ou o HPC Pack. Destina-se apenas para ajudar a implementar um cluster de cálculo híbrido rapidamente para fins de demonstração. Para considerações e os passos para implementar um cluster HPC Pack de híbrida na escala superior num ambiente de produção ou utilizar o HPC Pack 2016, consulte o [detalhadas orientações](http://go.microsoft.com/fwlink/p/?LinkID=200493). Para obter outros cenários com o HPC Pack, incluindo a atribuição de implementação de cluster em máquinas virtuais do Azure, consulte [opções de cluster HPC com o Microsoft HPC Pack no Azure](../virtual-machines/windows/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="prerequisites"></a>Pré-requisitos
* **Subscrição do Azure** -se não tiver uma subscrição do Azure, pode criar um [conta gratuita](https://azure.microsoft.com/free/) em apenas alguns minutos.
* **Um computador no local com o Windows Server 2012 R2 ou Windows Server 2012** -utilizar este computador como o nó principal do HPC cluster. Se já não estão a executar o Windows Server, pode transferir e instalar um [versão de avaliação](https://www.microsoft.com/evalcenter/evaluate-windows-server-2012-r2).
  
  * O computador tem de estar associado a um domínio do Active Directory. Para fins de teste, pode configurar o computador de nó principal como um controlador de domínio. Para adicionar a função de servidor Serviços de domínio do Active Directory e promover o computador de nó principal como um controlador de domínio, consulte a documentação para o Windows Server.
  * Para suportar o HPC Pack, o sistema operativo tem de ser instalado nestes idiomas: inglês, japonês ou chinês (simplificado).
  * Certifique-se de que são instaladas atualizações importantes e críticas.
* **Pacote HPC 2012 R2** - [transferir](http://go.microsoft.com/fwlink/p/?linkid=328024) a instalação do pacote para a versão mais recente gratuita e copie os ficheiros para o computador de nó principal. Escolha os ficheiros de instalação no mesmo idioma como a instalação do Windows Server.

    >[!NOTE]
    > Se pretender utilizar o HPC Pack 2016 em vez de HPC Pack 2012 R2, é necessária configuração adicional. Consulte o [detalhadas orientações](http://go.microsoft.com/fwlink/p/?LinkID=200493).
    > 
* **Conta de domínio** -esta conta tem de ser configurada com permissões de administrador local no nó principal para instalar o pacote HPC.
* **Ligação TCP na porta 443** do nó principal do Azure.

## <a name="install-hpc-pack-on-the-head-node"></a>Instalar o pacote HPC no nó principal
Instale primeiro o Microsoft HPC Pack no seu computador no local com o Windows Server. Este computador torna-se o nó principal do cluster.

1. Inicie sessão no nó principal utilizando uma conta de domínio que tenha permissões de administrador local.

2. Inicie o Assistente de instalação do HPC Pack ao executar o Setup.exe a partir dos ficheiros de instalação do HPC Pack.

3. No **configuração HPC Pack 2012 R2** ecrã, clique em **nova instalação ou adicionar novas funcionalidades para uma instalação existente**.

    ![O programa de configuração do HPC Pack 2012][install_hpc1]

4. No **página de contrato de Software Microsoft do utilizador**, clique em **seguinte**.

5. No **selecionar tipo de instalação** página, clique em **criar um novo cluster HPC através da criação de um nó principal**e, em seguida, clique em **seguinte**.

6. O assistente executa vários testes de pré-instalação. Clique em **seguinte** no **regras de instalação** página se passaram em todos os testes. Caso contrário, reveja as informações fornecidas e efetue as alterações necessárias no seu ambiente. Em seguida, execute novamente os testes ou se for necessário iniciar o Assistente de instalação novamente.
7. No **HPC DB configuração** página, certifique-se **nó principal** está selecionada para todas as bases de dados HPC e, em seguida, clique em **seguinte**. 

    ![Configuração de base de dados][install_hpc4]

8. Aceite as seleções de predefinidas nas restantes páginas do assistente. No **instalar componentes necessários** página, clique em **instalar**.
   
    ![Instalar][install_hpc6]

9. Depois de concluída a instalação, desmarque **iniciar o Gestor de clusters HPC** e, em seguida, clique em **concluir**. (Iniciar Gestor de clusters HPC num passo posterior.)
   
    ![Concluir][install_hpc7]

## <a name="prepare-the-azure-subscription"></a>Preparar a subscrição do Azure
Execute os seguintes passos no [portal do Azure](https://portal.azure.com) com a sua subscrição do Azure. Depois de concluir estes passos, pode implementar nós do Azure a partir do nó principal no local. 
  
  > [!NOTE]
  > Também anote o ID de subscrição do Azure, o que precisa de mais tarde. Localizar o ID **subscrições** no portal.
  > 

### <a name="upload-the-default-management-certificate"></a>Carregue o certificado de gestão predefinido
Pacote HPC instala um certificado autoassinado no nó principal, denominado o certificado predefinido Microsoft HPC Azure Management, pode carregar um certificado de gestão do Azure. Este certificado é fornecido para implementações de teste e uma prova de conceito para proteger a ligação entre o nó principal e o Azure.

1. No computador de nó principal, iniciar sessão para o [portal do Azure](https://portal.azure.com).

2. Clique em **subscrições** > *your_subscription_name*.

3. Clique em **certificados de gestão** > **carregar**.

4. Procure no nó principal para o ficheiro C:\Program Files\Microsoft HPC Pack 2012\Bin\hpccert.cer. Em seguida, clique em **carregar**.

   
O **predefinido HPC Azure Management** certificado aparece na lista de certificados de gestão.

### <a name="create-an-azure-cloud-service"></a>Criar um serviço em nuvem do Azure
> [!NOTE]
> Para melhor desempenho, crie o serviço em nuvem e a conta de armazenamento (num passo posterior) na mesma região geográfica.
> 
> 

1. No portal, clique em **(clássica) de serviços em nuvem** > **+ adicionar**.

2.  Escreva um nome DNS para o serviço, escolha um grupo de recursos e uma localização e, em seguida, clique em **criar**.


### <a name="create-an-azure-storage-account"></a>Criar uma conta do Storage do Azure
1. No portal, clique em **contas do Storage (clássica)** > **+ adicionar**.

2. Escreva um nome para a conta e selecione o **clássico** modelo de implementação.

3. Escolha um grupo de recursos e uma localização e deixe outras definições com valores predefinidos. Em seguida, clique em **Criar**.

## <a name="configure-the-head-node"></a>Configurar o nó principal
Para utilizar o Gestor de clusters HPC para implementar nós do Azure e submeter tarefas, primeiro efetue alguns passos de configuração de cluster necessários.

1. No nó principal, inicie o Gestor de clusters HPC. Se o **selecionar nó Head** for apresentada a caixa de diálogo, clique em **computador Local**. O **lista de tarefas de implementação** aparece.

2. Em **tarefas de implementação necessárias**, clique em **configurar a rede**.
   
    ![Configurar a rede][config_hpc2]

3. No Assistente de configuração de rede, selecione **todos os nós apenas numa rede empresarial** (topologia 5). Esta configuração de rede é mais simples para fins de demonstração.
   
    ![Topologia 5][config_hpc3]
   
4. Clique em **seguinte** para aceitar os valores predefinidos nas restantes páginas do assistente. Em seguida, no **revisão** separador, clique em **configurar** para concluir a configuração de rede.

5. No **lista de tarefas de implementação**, clique em **fornecer credenciais de instalação**.

6. No **credenciais de instalação** diálogo caixa, escreva as credenciais da conta de domínio que utilizou para instalar o pacote HPC. Em seguida, clique em **OK**. 
   
    ![Credenciais de instalação][config_hpc6]
   
7. No **lista de tarefas de implementação**, clique em **configurar a nomenclatura dos novos nós**.

8. No **especificar série de atribuição de nomes de nó** diálogo caixa, aceite a predefinição de nomenclatura série e clique em **OK**. Conclua este passo, apesar dos nós do Azure, que adicionar neste tutorial são denominados automaticamente.
   
    ![Atribuição de nomes de nó][config_hpc8]
   
9. No **lista de tarefas de implementação**, clique em **criar um modelo de nó**. Mais tarde no tutorial, utilize o modelo de nó para adicionar nós do Azure para o cluster.

10. No Assistente de modelo de nó de criação, efetue o seguinte:
    
    a. No **Escolher tipo de modelo de nó** página, clique em **modelo de nó do Windows Azure**e, em seguida, clique em **seguinte**.
    
    ![Modelo de nó][config_hpc10]
    
    b. Clique em **seguinte** para aceitar o nome de modelo predefinido.
    
    c. No **fornecem informações sobre a subscrição** página, introduza o seu ID de subscrição do Azure (disponível no informações da sua conta do Azure). Em seguida, no **certificado de gestão**, procure **predefinido Microsoft HPC Azure Management.** Clique depois em **Seguinte**.
    
    ![Modelo de nó][config_hpc12]
    
    d. No **fornecer as informações do serviço** página, selecione o serviço em nuvem e a conta de armazenamento que criou no passo anterior. Clique depois em **Seguinte**.
    
    ![Modelo de nó][config_hpc13]
    
    e. Clique em **seguinte** para aceitar os valores predefinidos nas restantes páginas do assistente. Em seguida, no **revisão** separador, clique em **criar** para criar o modelo de nó.
    
    > [!NOTE]
    > Por predefinição, o modelo de nó de Azure inclui definições para que possa iniciar (aprovisionar) e parar os nós manualmente, utilizando o Gestor de clusters HPC. Opcionalmente, pode configurar uma agenda para iniciar e parar automaticamente os nós do Azure.
    > 
    > 

## <a name="add-azure-nodes-to-the-cluster"></a>Adicionar nós do Azure para o cluster
Agora, utilize o modelo de nó para adicionar nós do Azure para o cluster. Adicionar nós ao cluster armazena as suas informações de configuração para que possa começar (aprovisionar)-las em qualquer altura no serviço em nuvem. A subscrição apenas obtém cobrada para nós do Azure depois das instâncias em execução no serviço em nuvem.

Siga estes passos para adicionar dois nós pequenos.

1. No Gestor de clusters do HPC, clique em **gestão nó** (chamado **gestão de recursos** nas versões atuais do HPC Pack) > **adicionar nó**.
   
    ![Adicionar o nó][add_node1]

2. No Assistente para adicionar nó, no **selecionar método de implementação** página, clique em **nós do Windows Azure adicionar**e, em seguida, clique em **seguinte**.
   
    ![Adicionar o nó do Azure][add_node1_1]

3. No **especificar novos nós** página, selecione o modelo de nó do Azure que criou anteriormente (chamado por predefinição **predefinido AzureNode modelo**). Em seguida, especifique **2** nós do tamanho **pequeno**e, em seguida, clique em **seguinte**.
   
    ![Especificar nós][add_node2]
   
4. No **concluir o Assistente para adicionar nó** página, clique em **concluir**.
    
     Dois nós do Azure, com o nome **AzureCN-0001 e** e **AzureCN 0002**, são agora apresentadas no Gestor de clusters HPC. Ambos estão no **implementadas não** estado.
   
    ![Nós adicionados][add_node3]

## <a name="start-the-azure-nodes"></a>Inicie os nós do Azure
Quando pretender utilizar os recursos de cluster no Azure, utilize o Gestor de clusters HPC para iniciar nós (aprovisionar) do Azure e colocá-los online.

1. No Gestor de clusters do HPC, clique em **gestão nó** (chamado **gestão de recursos** nas versões atuais do HPC Pack) e selecione os nós do Azure.

2. Clique em **iniciar**e, em seguida, clique em **OK**.
   
   ![Nós de início][add_node4]
   
    Os nós de transição para o **aprovisionamento** estado. Ver o registo de aprovisionamento para acompanhar o progresso de aprovisionamento.
   
    ![Aprovisionar nós][add_node6]

3. Após alguns minutos, os nós do Azure concluir o aprovisionamento e estão no **Offline** estado. Neste estado, as instâncias de função estão em execução, mas ainda não é possível aceitar a tarefas de cluster.

4. Para confirmar que as instâncias de função estão em execução, no portal do Azure, clique em **serviços em nuvem (clássica)** > *your_cloud_service_name*.
   
   Deverá ver dois **HpcWorkerRole** instâncias (nós) em execução no serviço. Pacote HPC implementa também automaticamente dois **HpcProxy** instâncias (tamanho média) para processar a comunicação entre o nó principal e o Azure.

   ![Instâncias em execução][view_instances1]

5. Para colocar online os nós do Azure para executar tarefas de cluster, selecione os nós, rato e, em seguida, clique em **colocar Online**.
   
    ![Nós offline][add_node7]
   
    Gestor de clusters HPC indica que os nós estão no **Online** estado.

## <a name="run-a-command-across-the-cluster"></a>Executar um comando no cluster
Para verificar a instalação, utilize o HPC Pack **clusrun** comando a executar um comando ou uma aplicação num ou mais nós de cluster. Como um exemplo simples, utilize **clusrun** para obter a configuração de IP de nós do Azure.

1. No nó principal, abra uma linha de comandos como administrador.

2. Escreva o seguinte comando:
   
    `clusrun /nodes:azurecn* ipconfig`

3. Se lhe for solicitado, introduza a palavra-passe de administrador do cluster. Deverá ver a saída do comando semelhante ao seguinte.
   
    ![Clusrun][clusrun1]

## <a name="run-a-test-job"></a>Execute uma tarefa de teste
Agora submeta uma tarefa de teste que executa no cluster híbrida. Neste exemplo é uma tarefa simples varrimento paramétrico (um tipo de computação intrinsecamente paralela). Este exemplo executa subtarefas que adicionar um número inteiro para si próprio utilizando o **definir /a** comando. Todos os nós do cluster contribuem para concluir as subtarefas de números inteiros de 1 a 100.

1. No Gestor de clusters do HPC, clique em **de tarefas gestão** > **nova tarefa de varrimento Sweep**.
   
    ![Nova tarefa][test_job1]

2. No **nova tarefa de varrimento Sweep** caixa de diálogo **linha de comandos**, tipo `set /a *+*` (substituir a linha de comandos predefinida que é apresentado). Deixe os valores predefinidos para as restantes definições e, em seguida, clique em **submeter** para submeter a tarefa.
   
    ![Varrimento paramétrico][param_sweep1]

3. Quando a tarefa estiver concluída, faça duplo clique o **My tarefas Sweep** tarefa.

4. Clique em **tarefas vista**e, em seguida, clique em subtarefa para ver o resultado calculado que subtarefa.
   
    ![Resultados de tarefas][view_job361]

5. Para ver o nó que efetuar o cálculo para esse subtarefa, clique em **nós atribuídos**. (O seu cluster poderá mostrar um nome de nó diferente.)
   
    ![Resultados de tarefas][view_job362]

## <a name="stop-the-azure-nodes"></a>Parar os nós do Azure
Depois de experimentar o cluster, pare os nós do Azure para evitar custos desnecessários à sua conta. Este passo para o serviço de nuvem e remove as instâncias da função do Azure.

1. No Gestor de clusters do HPC, no **gestão nó** (chamado **gestão de recursos** em versões anteriores do pacote HPC), selecione ambos os nós do Azure. Em seguida, clique em **parar**.
   
    ![Parar nós][stop_node1]

2. No **nós do Windows Azure parar** caixa de diálogo, clique em **parar**. 
   
3. Os nós de transição para o **parar** estado. Após alguns minutos, o Gestor de clusters HPC mostra que os nós estão **implementadas não**.
   
    ![Nós não implementados][stop_node4]

4. Para confirmar que as instâncias de função já não estão em execução no Azure, no portal do Azure, clique em **(clássica) de serviços em nuvem** > *your_cloud_service_name*. Não existem instâncias são implementadas no ambiente de produção. 
   
    Este passo conclui o tutorial.

## <a name="next-steps"></a>Passos seguintes
* Explorar a documentação para [HPC Pack](https://technet.microsoft.com/library/cc514029).
* Para configurar um implementação de cluster HPC Pack na escala superior de híbrido, consulte [Impulsar para instâncias de função de trabalho do Azure com o Microsoft HPC Pack](http://go.microsoft.com/fwlink/p/?LinkID=200493).
* Para outras formas de criar um cluster HPC Pack no Azure, incluindo através de modelos Azure Resource Manager, consulte o artigo [opções de cluster HPC com o Microsoft HPC Pack no Azure](../virtual-machines/windows/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


[Overview]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/hybrid_cluster_overview.png
[install_hpc1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc1.png
[install_hpc4]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc4.png
[install_hpc6]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc6.png
[install_hpc7]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc7.png
[install_hpc10]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc10.png
[config_hpc2]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc2.png
[config_hpc3]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc3.png
[config_hpc6]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc6.png
[config_hpc8]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc8.png
[config_hpc10]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc10.png
[config_hpc12]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc12.png
[config_hpc13]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc13.png
[add_node1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node1.png
[add_node1_1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node1_1.png
[add_node2]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node2.png
[add_node3]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node3.png
[add_node4]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node4.png
[add_node6]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node6.png
[add_node7]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node7.png
[view_instances1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/view_instances1.png
[clusrun1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/clusrun1.png
[test_job1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/test_job1.png
[param_sweep1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/param_sweep1.png
[view_job361]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/view_job361.png
[view_job362]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/view_job362.png
[stop_node1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/stop_node1.png
[stop_node4]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/stop_node4.png
[view_instances2]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/view_instances2.png
