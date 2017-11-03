---
title: "SQL Server FCI - máquinas virtuais do Azure | Microsoft Docs"
description: "Este artigo explica como criar a instância de Cluster de ativação pós-falha do SQL Server em Azure Virtual Machines."
services: virtual-machines
documentationCenter: na
authors: MikeRayMSFT
manager: jhubbard
editor: monicar
tags: azure-service-management
ms.assetid: 9fc761b1-21ad-4d79-bebc-a2f094ec214d
ms.service: virtual-machines-sql
ms.devlang: na
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 09/26/2017
ms.author: mikeray
ms.openlocfilehash: ec35b4a02c04d5b6d0bbf9049927529258c3825b
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="configure-sql-server-failover-cluster-instance-on-azure-virtual-machines"></a>Configurar a instância de Cluster de ativação pós-falha do SQL Server em Virtual Machines do Azure

Este artigo explica como criar uma ativação pós-falha Cluster instância (FCI) do SQL Server em virtual machines do Azure no modelo do Resource Manager. Esta solução usa [edição Datacenter do Windows Server 2016 espaços de armazenamento direto \(S2D\) ](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview) como uma SAN de virtual baseado em software que sincroniza o armazenamento (discos de dados) entre os nós (as VMs do Azure) num Cluster do Windows. S2D é nova no Windows Server 2016.

O diagrama seguinte mostra a solução completa em máquinas virtuais do Azure:

![Grupo de disponibilidade](./media/virtual-machines-windows-portal-sql-create-failover-cluster/00-sql-fci-s2d-complete-solution.png)

O diagrama anterior mostra:

- Dois virtual machines do Azure num cluster de ativação pós-falha do Windows. Quando uma máquina virtual está num cluster de ativação pós-falha também é denominado um *o nó de cluster*, ou *nós*.
- Cada máquina virtual tem dois ou mais discos de dados.
- S2D sincroniza os dados no disco de dados e apresenta o armazenamento sincronizadas como um agrupamento de armazenamento.
- O agrupamento de armazenamento apresenta um volume partilhado de cluster (CSV) para o cluster de ativação pós-falha.
- A função de cluster do SQL Server FCI utiliza o CSV para as unidades de dados.
- Um balanceador de carga do Azure para manter o endereço IP para a FCI do SQL Server.
- Um conjunto de disponibilidade do Azure mantém todos os recursos.

   >[!NOTE]
   >Todos os recursos do Azure estão no diagrama estão no mesmo grupo de recursos.

Para obter detalhes sobre S2D, consulte [edição Datacenter do Windows Server 2016 espaços de armazenamento direto \(S2D\)](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview).

S2D suportam dois tipos de arquiteturas - convergidas e hiperconvergida. A arquitetura neste documento é hiperconvergida. Uma infraestrutura hiperconvergida coloca o armazenamento em servidores do mesmos que alojam a aplicação em cluster. Nesta arquitetura, o armazenamento está em cada nó do SQL Server FCI.

### <a name="example-azure-template"></a>Exemplo modelo do Azure

Pode criar a solução completa no Azure a partir de um modelo. Um exemplo de um modelo está disponível no GitHub, [modelos de início rápido do Azure](https://github.com/MSBrett/azure-quickstart-templates/tree/master/sql-server-2016-fci-existing-vnet-and-ad). Este exemplo não concebido ou não está testado para qualquer carga de trabalho específica. Pode executar o modelo para criar um FCI do SQL Server com o armazenamento de S2D ligado ao seu domínio. Pode avaliar o modelo e modificá-lo para os fins pretendidos.

## <a name="before-you-begin"></a>Antes de começar

Existem alguns aspetos, que terá de conhecer e algumas coisas que precisa no local antes de continuar.

### <a name="what-to-know"></a>O que deve saber
Deve ter uma compreensão operacional das seguintes tecnologias de:

- [Tecnologias de cluster do Windows](http://technet.microsoft.com/library/hh831579.aspx)
-  [Instâncias de Cluster de ativação pós-falha do SQL Server](http://msdn.microsoft.com/library/ms189134.aspx).

Além disso, deve ter uma compreensão das seguintes tecnologias de geral:

- [Solução convergida Hyper utilizando direta de espaços de armazenamento no Windows Server 2016](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct)
- [Grupos de recursos do Azure](../../../azure-resource-manager/resource-group-portal.md)

### <a name="what-to-have"></a>O que tenham

Antes de seguir as instruções neste artigo, já deverá ter:

- Uma subscrição do Microsoft Azure.
- Um domínio do Windows em máquinas virtuais do Azure.
- Uma conta com permissão para criar objetos na máquina virtual do Azure.
- Uma rede virtual do Azure e a sub-rede com o IP suficiente endereços espaço para os seguintes componentes:
   - Ambas as máquinas virtuais.
   - O endereço IP do cluster de ativação pós-falha.
   - Um endereço IP para cada FCI.
- DNS configurado na rede do Azure, que aponta para os controladores de domínio.

Com estas pré-requisitos no local, pode continuar com a criação do cluster de ativação pós-falha. O primeiro passo consiste em criar as máquinas virtuais.

## <a name="step-1-create-virtual-machines"></a>Passo 1: Criar máquinas virtuais

1. Inicie sessão no [portal do Azure](http://portal.azure.com) com a sua subscrição.

1. [Criar um conjunto de disponibilidade do Azure](../tutorial-availability-sets.md).

   A disponibilidade definir grupos máquinas de virtuais em vários domínios de falhas e domínios de atualização. O conjunto de disponibilidade certifica-se de que a aplicação não é afetada por pontos únicos de falha, como o comutador de rede ou a unidade de energia de um bastidor de servidores.

   Se não criou o grupo de recursos para as máquinas virtuais, fazê-lo ao criar um conjunto de disponibilidade do Azure. Se estiver a utilizar o portal do Azure para criar o conjunto de disponibilidade, efetue os seguintes passos:

   - No portal do Azure, clique em  **+**  para abrir o Azure Marketplace. Procurar **do conjunto de disponibilidade**.
   - Clique em **do conjunto de disponibilidade**.
   - Clique em **Criar**.
   - No **criar conjunto de disponibilidade** painel, defina os seguintes valores:
      - **Nome**: um nome para o conjunto de disponibilidade.
      - **Subscrição**: subscrição do Azure.
      - **Grupo de recursos**: Se pretender utilizar um grupo existente, clique em **utilizar existente** e selecione o grupo da lista pendente. Caso contrário, escolha **criar novo** e escreva um nome para o grupo.
      - **Localização**: defina a localização onde pretende criar as máquinas virtuais.
      - **Domínios de falha**: utilizar a predefinição (3).
      - **Domínios de atualização**: utilizar a predefinição (5).
   - Clique em **criar** criar a disponibilidade definido.

1. Crie as máquinas virtuais no conjunto de disponibilidade.

   Aprovisione duas máquinas virtuais do SQL Server no conjunto de disponibilidade do Azure. Para obter instruções, consulte [aprovisionar uma máquina virtual do SQL Server no portal do Azure](virtual-machines-windows-portal-sql-server-provision.md).

   Coloca as duas máquinas virtuais:

   - No Azure mesmo grupo de recursos que definir a sua disponibilidade consta.
   - Na mesma rede que o controlador de domínio.
   - Numa sub-rede com suficiente espaço de endereços IP para máquinas virtuais e todas as FCIs que poderão, eventualmente, utilizar neste cluster.
   - No conjunto de disponibilidade do Azure.   

      >[!IMPORTANT]
      >Não é possível definir ou alterar conjunto depois de criar uma máquina virtual de disponibilidade.

   Escolha uma imagem do Azure Marketplace. Pode utilizar um mercado imagem com o que inclui o Windows Server e SQL Server ou apenas o Windows Server. Para obter mais informações, consulte [descrição geral do SQL Server em Azure Virtual Machines](../../virtual-machines-windows-sql-server-iaas-overview.md)

   As imagens do SQL Server oficiais na galeria do Azure incluem uma instância do SQL Server instalada, e o software de instalação do SQL Server e a chave necessária.

   Escolha a imagem correta de acordo com a forma como pretende pagar para a licença do SQL Server:

   - **Pagar por utilização licenciamento**: O custo por minuto destas imagens inclui o licenciamento do SQL Server:
      - **SQL Server 2016 Enterprise no Datacenter do Windows Server 2016**
      - **SQL Server 2016 padrão no Datacenter do Windows Server 2016**
      - **SQL Server 2016 Programador no Datacenter do Windows Server 2016**

   - **Bring-your-proprietário-licença (BYOL)**

      - **{BYOL} SQL Server 2016 Enterprise no Datacenter do Windows Server 2016**
      - **{BYOL} SQL Server 2016 padrão no Datacenter do Windows Server 2016**

   >[!IMPORTANT]
   >Depois de criar a máquina virtual, remova a instância do SQL Server autónomo pré-instaladas. Irá utilizar o suporte de dados do SQL Server previamente instalado para criar a FCI do SQL Server depois de configurar o cluster de ativação pós-falha e S2D.

   Em alternativa, pode utilizar imagens do Azure Marketplace com apenas o sistema operativo. Escolha um **Datacenter do Windows Server 2016** imagem e instalar a FCI do SQL Server depois de configurar o cluster de ativação pós-falha e S2D. Esta imagem não contém suporte de instalação do SQL Server. Coloque o suporte de dados de instalação numa localização onde é possível executar a instalação do SQL Server para cada servidor.

1. Depois do Azure cria as máquinas virtuais, ligar a cada máquina virtual com o RDP.

   Quando liga pela primeira vez a uma máquina virtual com o RDP, o computador pergunta se pretende permitir este PC ser detetável na rede. Clique em **Sim**.

1. Se estiver a utilizar uma das imagens da máquina virtual baseada no SQL Server, remova a instância do SQL Server.

   - No **programas e funcionalidades**, faça duplo clique **Microsoft SQL Server 2016 (64-bit)** e clique em **desinstalar/alterar**.
   - Clique em **remover**.
   - Selecione a instância predefinida.
   - Remova todas as funcionalidades em **serviços de motor de base de dados**. Não remova **partilhado funcionalidades**. Consulte a imagem seguinte:

      ![Remover funcionalidades](./media/virtual-machines-windows-portal-sql-create-failover-cluster/03-remove-features.png)

   - Clique em **seguinte**e, em seguida, clique em **remover**.

1. <a name="ports"></a>Abra as portas de firewall.

   Em cada máquina virtual, abra as seguintes portas na Firewall do Windows.

   | Objetivo | A porta TCP | Notas
   | ------ | ------ | ------
   | SQL Server | 1433 | Porta normal para instâncias predefinida do SQL Server. Se tiver utilizado uma imagem a partir da galeria, esta porta é automaticamente aberta.
   | Sonda de estado de funcionamento | 59999 | Abra qualquer porta TCP. Num passo posterior, configure o Balanceador de carga [sonda do Estado de funcionamento](#probe) e o cluster para utilizar esta porta.  

1. Adicione armazenamento à máquina virtual. Para obter informações detalhadas, consulte [adicionar armazenamento](../premium-storage.md).

   Ambas as máquinas virtuais precisa de discos de dados, pelo menos, dois.

   Anexe discos em bruto - não NTFS formatado discos.
      >[!NOTE]
      >Se anexar formatada com NTFS discos, só pode ativar S2D com verificação de elegibilidade sem disco.  

   Anexe um mínimo de dois Premium armazenamento (discos SSD) para cada VM. Recomendamos que, pelo menos, P30 discos (1 TB).

   Anfitrião de conjunto de colocação em cache para **só de leitura**.

   A capacidade de armazenamento a que utilizar em ambientes de produção depende da sua carga de trabalho. Os valores descritos neste artigo são de demonstração e teste.

1. [Adicione as máquinas virtuais ao seu domínio pré-existente](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain).

Depois das máquinas virtuais são criadas e configuradas, pode configurar o cluster de ativação pós-falha.

## <a name="step-2-configure-the-windows-failover-cluster-with-s2d"></a>Passo 2: Configurar o Cluster de ativação pós-falha do Windows com S2D

O passo seguinte consiste em configurar o cluster de ativação pós-falha com S2D. Neste passo, será efetuado os substeps seguintes:

1. Adicionar a funcionalidade de Clustering de ativação pós-falha do Windows
1. Validar o cluster
1. Criar o cluster de ativação pós-falha
1. Criar o testemunho de nuvem
1. Adicionar armazenamento

### <a name="add-windows-failover-clustering-feature"></a>Adicionar a funcionalidade de Clustering de ativação pós-falha do Windows

1. Para começar, ligue a máquina virtual primeiro com RDP utilizando uma conta de domínio que seja membro dos administradores locais e tem permissões para criar objetos no Active Directory. Utilize esta conta para o resto da configuração.

1. [Adicione a funcionalidade Clustering de ativação pós-falha para cada máquina virtual](virtual-machines-windows-portal-sql-availability-group-prereq.md#add-failover-clustering-features-to-both-sql-server-vms).

   Para instalar a funcionalidade Clustering de ativação pós-falha a IU do, efetue os seguintes passos em ambas as máquinas virtuais.
   - No **Gestor de servidor**, clique em **gerir**e, em seguida, clique em **para adicionar funções e funcionalidades**.
   - No **Assistente Adicionar funções e funcionalidades**, clique em **seguinte** até chegar ao **selecionar funcionalidades**.
   - No **selecionar funcionalidades**, clique em **Clustering de ativação pós-falha**. Inclua todos requeridas funcionalidades e as ferramentas de gestão. Clique em **adicionar funcionalidades**.
   - Clique em **seguinte** e, em seguida, clique em **concluir** para instalar as funcionalidades.

   Para instalar a funcionalidade Clustering de ativação pós-falha com o PowerShell, execute o seguinte script a partir de uma sessão do PowerShell de administrador das máquinas virtuais.

   ```PowerShell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

Para referência, os passos seguintes siga as instruções no passo 3 de [convergida Hyper solução utilizando direta de espaços de armazenamento no Windows Server 2016](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-3-configure-storage-spaces-direct).

### <a name="validate-the-cluster"></a>Validar o cluster

Este guia refere-se as instruções em [validar cluster](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-31-run-cluster-validation).

Valide o cluster na IU ou com o PowerShell.

Para validar o cluster com a IU, efetue os seguintes passos de uma das máquinas virtuais.

1. No **Gestor de servidor**, clique em **ferramentas**, em seguida, clique em **Gestor de clusters de ativação pós-falha**.
1. No **Gestor de clusters de ativação pós-falha**, clique em **ação**, em seguida, clique em **validar configuração...** .
1. Clique em **Seguinte**.
1. No **selecionar servidores ou um Cluster**, escreva o nome de ambas as máquinas virtuais.
1. No **opções de teste**, escolha **executar apenas os testes selecionados**. Clique em **Seguinte**.
1. No **testar seleção**, incluir todos os testes, exceto **armazenamento**. Consulte a imagem seguinte:

   ![Validar testes](./media/virtual-machines-windows-portal-sql-create-failover-cluster/10-validate-cluster-test.png)

1. Clique em **Seguinte**.
1. No **confirmação**, clique em **seguinte**.

O **validar um Assistente de configuração** executa os testes de validação.

Para validar o cluster com o PowerShell, execute o seguinte script a partir de uma sessão do PowerShell de administrador das máquinas virtuais.

   ```PowerShell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Storage Spaces Direct", "Inventory", "Network", "System Configuration"
   ```

Depois de validar o cluster, crie o cluster de ativação pós-falha.

### <a name="create-the-failover-cluster"></a>Criar o cluster de ativação pós-falha

Este guia refere-se ao [criar o cluster de ativação pós-falha](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-32-create-a-cluster).

Para criar o cluster de ativação pós-falha, é necessário:
- Os nomes das máquinas virtuais que tornam-se os nós do cluster.
- Um nome para o cluster de ativação pós-falha
- Um endereço IP para o cluster de ativação pós-falha. Pode utilizar um endereço IP que não seja utilizado a mesma rede virtual do Azure e a sub-rede como nós do cluster.

O PowerShell seguinte cria um cluster de ativação pós-falha. Atualize o script com os nomes de nós (os nomes de máquina virtual) e um endereço IP disponível a partir da VNET do Azure:

```PowerShell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

### <a name="create-a-cloud-witness"></a>Criar um testemunho de nuvem

Testemunho de nuvem é um novo tipo de testemunho de quórum do cluster armazenado num Blob de armazenamento do Azure. Esta ação remove a necessidade de uma VM separada que aloja uma partilha de testemunho.

1. [Criar um testemunho de nuvem para o cluster de ativação pós-falha](http://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness).

1. Crie um contentor de blob.

1. Guarde as chaves de acesso e o URL do contentor.

1. Configure o testemunho de quórum de cluster de cluster de ativação pós-falha. Configurar o testemunho de quórum na interface de utilizador] consulte. (http://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness) na IU.

### <a name="add-storage"></a>Adicionar armazenamento

Os discos para S2D tem de estar em branco e sem partições ou outros dados. Para limpar discos siga [os passos neste guia](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-34-clean-disks).

1. [Ativar loja direta de espaços \(S2D\)](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-35-enable-storage-spaces-direct).

   O PowerShell seguinte ativa direta de espaços de armazenamento.  

   ```PowerShell
   Enable-ClusterS2D
   ```

   No **Gestor de clusters de ativação pós-falha**, agora, pode ver o agrupamento de armazenamento.

1. [Criar um volume](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-36-create-volumes).

   Uma das funcionalidades do S2D é que este cria automaticamente um agrupamento de armazenamento se a ativar. Agora está pronto para criar um volume. O commandlet PowerShell `New-Volume` automatiza o processo de criação do volume, incluindo formatação, adicionar ao cluster e criar um volume partilhado de cluster (CSV). O exemplo seguinte cria um 800 gigabyte (GB) CSV.

   ```PowerShell
   New-Volume -StoragePoolFriendlyName S2D* -FriendlyName VDisk01 -FileSystem CSVFS_REFS -Size 800GB
   ```   

   Depois de concluída este comando, um volume de 800 GB está montado como um recurso de cluster. O volume está em `C:\ClusterStorage\Volume1\`.

   O diagrama seguinte mostra um volume partilhado de cluster com S2D:

   ![ClusterSharedVolume](./media/virtual-machines-windows-portal-sql-create-failover-cluster/15-cluster-shared-volume.png)

## <a name="step-3-test-failover-cluster-failover"></a>Passo 3: Testar a ativação pós-falha de cluster de ativação pós-falha

No Gestor de clusters de ativação pós-falha, certifique-se de que pode mover o recurso de armazenamento para outro nó de cluster. Se pode ligar ao cluster de ativação pós-falha com **Gestor de clusters de ativação pós-falha** e mover o armazenamento de um nó para outro, está pronto para configurar a FCI.

## <a name="step-4-create-sql-server-fci"></a>Passo 4: Criar do SQL Server FCI

Depois de ter configurado o cluster de ativação pós-falha e todos os componentes de cluster, incluindo o armazenamento, pode criar a FCI do SQL Server.

1. Ligar à máquina virtual primeiro com RDP.

1. No **Gestor de clusters de ativação pós-falha**, certifique-se de que todos os recursos de núcleo de cluster estão na primeira máquina virtual. Se necessário, mova todos os recursos para esta máquina virtual.

1. Localize o suporte de dados de instalação. Se a máquina virtual utiliza uma das imagens do Azure Marketplace, o suporte de dados está localizada em `C:\SQLServer_<version number>_Full`. Clique em **configuração**.

1. No **Centro de instalação do SQL Server**, clique em **instalação**.

1. Clique em **instalação de cluster de ativação pós-falha do novo SQL Server**. Siga as instruções no Assistente para instalar a FCI do SQL Server.

   Os diretórios de dados da FCI têm de ser no armazenamento em cluster. Com S2D, não é um disco partilhado, mas um ponto de montagem para um volume em cada servidor. S2D sincroniza o volume entre ambos os nós. O volume é apresentado para o cluster como volume partilhado de cluster. Utilize o ponto de montagem CSV para os diretórios de dados.

   ![DataDirectories](./media/virtual-machines-windows-portal-sql-create-failover-cluster/20-data-dicrectories.png)

1. Depois de concluir o assistente, a configuração irá instalar um FCI do SQL Server no primeiro nó.

1. Depois do programa de configuração instala com êxito a FCI no primeiro nó, ligue para o segundo nó com o RDP.

1. Abra o **Centro de instalação do SQL Server**. Clique em **instalação**.

1. Clique em **adicionar nó a um cluster de ativação pós-falha do SQL Server**. Siga as instruções no Assistente para instalar o SQL server e adicionar este servidor para a FCI.

   >[!NOTE]
   >Se utilizou uma imagem de galeria do Azure Marketplace com o SQL Server, ferramentas do SQL Server foram incluídas com a imagem. Se não utilizou esta imagem, instale as ferramentas do SQL Server separadamente. Consulte [transferir SQL Server Management Studio (SSMS)](http://msdn.microsoft.com/library/mt238290.aspx).

## <a name="step-5-create-azure-load-balancer"></a>Passo 5: Criar Balanceador de carga do Azure

Nas máquinas virtuais do Azure, clusters, utilize um balanceador de carga para conter um endereço IP que tem de ser um nó do cluster de cada vez. Nesta solução, o Balanceador de carga se o endereço IP para a FCI do SQL Server.

[Criar e configurar um balanceador de carga do Azure](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer).

### <a name="create-the-load-balancer-in-the-azure-portal"></a>Criar o Balanceador de carga no portal do Azure

Para criar o Balanceador de carga:

1. No portal do Azure, vá para o grupo de recursos com as máquinas virtuais.

1. Clique em **+ adicionar**. Procurar no Marketplace para **Balanceador de carga**. Clique em **Balanceador de carga**.

1. Clique em **Criar**.

1. Configure o Balanceador de carga com:

   - **Nome**: um nome que identifique o Balanceador de carga.
   - **Tipo**: O balanceador de carga pode ser públicas ou privadas. Um balanceador de carga privada pode ser acedido a partir a mesma VNET. Aplicações mais Azure podem utilizar um balanceador de carga privada. Se a sua aplicação precisar de aceder ao SQL Server diretamente através da Internet, utilize um balanceador de carga público.
   - **Rede virtual**: A mesma rede que as máquinas virtuais.
   - **Sub-rede**: A mesma sub-rede que as máquinas virtuais.
   - **Endereço IP privado**: O mesmo endereço IP que atribuiu para o recurso de rede de cluster do SQL Server FCI.
   - **subscrição**: subscrição do Azure.
   - **Grupo de recursos**: utilizar o mesmo grupo de recursos como as máquinas virtuais.
   - **Localização**: utilizar a mesma localização do Azure como máquinas virtuais.
   Consulte a imagem seguinte:

   ![CreateLoadBalancer](./media/virtual-machines-windows-portal-sql-create-failover-cluster/30-load-balancer-create.png)

### <a name="configure-the-load-balancer-backend-pool"></a>Configurar o conjunto de back-end de Balanceador de carga

1. Voltar para o grupo de recursos do Azure com as máquinas virtuais e localize o novo Balanceador de carga. Poderá ter de atualizar a vista de grupo de recursos. Clique em do Balanceador de carga.

1. No painel de Balanceador de carga, clique em **conjuntos back-end**.

1. Clique em **+ adicionar** para adicionar um conjunto de back-end.

1. Escreva um nome para o conjunto de back-end.

1. Clique em **adicionar uma máquina virtual**.

1. No **escolher as máquinas virtuais** painel, clique em **escolher um conjunto de disponibilidade**.

1. Escolha que o conjunto de disponibilidade que colocar as máquinas virtuais do SQL Server.

1. No **escolher as máquinas virtuais** painel, clique em **escolher as máquinas virtuais**.

   O portal do Azure deverá ser semelhante a imagem seguinte:

   ![CreateLoadBalancerBackEnd](./media/virtual-machines-windows-portal-sql-create-failover-cluster/33-load-balancer-back-end.png)

1. Clique em **selecione** no **escolher as máquinas virtuais** painel.

1. Clique em **OK** duas vezes.

### <a name="configure-a-load-balancer-health-probe"></a>Configurar uma sonda de estado de funcionamento do Balanceador de carga

1. No painel de Balanceador de carga, clique em **sondas de estado de funcionamento**.

1. Clique em **+ adicionar**.

1. No **sonda do Estado de funcionamento de adicionar** painel, <a name="probe"> </a>definir o estado de funcionamento parâmetros de pesquisa:

   - **Nome**: um nome para a sonda de estado de funcionamento.
   - **Protocolo**: TCP.
   - **Porta**: definido como uma porta TCP disponível. Esta porta necessita de uma porta de firewall aberta. Utilize o [mesma porta](#ports) definido para a sonda de estado de funcionamento na firewall.
   - **Intervalo**: 5 segundos.
   - **Limiar de mau estado de funcionamento**: 2 falhas consecutivas.

1. Clique em OK.

### <a name="set-load-balancing-rules"></a>Definir regras de balanceamento de carga

1. No painel de Balanceador de carga, clique em **as regras de balanceamento de carga**.

1. Clique em **+ adicionar**.

1. Defina a parâmetros de regras de balanceamento de carga:

   - **Nome**: um nome para a regras de balanceamento de carga.
   - **Endereço IP de front-end**: utilizar o endereço IP para o recurso de rede de cluster do SQL Server FCI.
   - **Porta**: definida para a porta TCP do SQL Server FCI. A porta de instância predefinida é 1433.
   - **Porta de back-end**: este valor utiliza a mesma porta como a **porta** valor quando ativar **IP flutuante (devolução direta do servidor)**.
   - **Conjunto back-end**: utilizar o nome do conjunto de back-end configurados anteriormente.
   - **Sonda de estado de funcionamento**: utilizar a pesquisa de estado de funcionamento que configurou anteriormente.
   - **Persistência da sessão**: nenhuma.
   - **Tempo limite (minutos) de inatividade**: 4.
   - **Vírgula flutuante (devolução direta do servidor) de IP**: ativado

1. Clique em **OK**.

## <a name="step-6-configure-cluster-for-probe"></a>Passo 6: Configurar o cluster para a sonda

Defina o parâmetro de porta de sonda de cluster no PowerShell.

Para definir o parâmetro de porta de sonda de cluster, Atualize as variáveis no seguinte script com valores do seu ambiente. Remova os angulares `<>` do script. 

   ```PowerShell
   $ClusterNetworkName = "<Cluster Network Name>"
   $IPResourceName = "<SQL Server FCI IP Address Resource Name>" 
   $ILBIP = "<n.n.n.n>" 
   [int]$ProbePort = <nnnnn>

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

O script anterior, defina os valores para o seu ambiente. A lista seguinte descreve os valores:

   - `<Cluster Network Name>`: Nome do Cluster de ativação pós-falha do Windows Server para a rede. No **Gestor de clusters de ativação pós-falha** > **redes**, faça duplo clique na rede e clique em **propriedades**. O valor correto está sob **nome** no **geral** separador. 

   - `<SQL Server FCI IP Address Resource Name>`: Nome de recursos de endereço IP de FCI SQL Server. No **Gestor de clusters de ativação pós-falha** > **funções**, sob a função de SQL Server FCI em **nome do servidor**, clique com o botão direito do rato no recurso de endereço IP e, em **Propriedades**. O valor correto está sob **nome** no **geral** separador. 

   - `<ILBIP>`: O endereço IP do ILB. Este endereço está configurado no portal do Azure como o endereço front-end do ILB. Isto também se o endereço IP do SQL Server FCI. Pode encontrá-lo na **Gestor de clusters de ativação pós-falha** na mesma página de propriedades onde localizar o `<SQL Server FCI IP Address Resource Name>`.  

   - `<nnnnn>`: É a porta da sonda que configurou na sonda de estado de funcionamento do Balanceador de carga. Qualquer porta TCP utilizada é válida. 

>[!IMPORTANT]
>A máscara de sub-rede para o parâmetro de cluster tem de ser o endereço de difusão de IP de TCP: `255.255.255.255`.

Depois de definir a sonda de cluster, pode ver todos os parâmetros de cluster no PowerShell. Execute o seguintes script:

   ```PowerShell
   Get-ClusterResource $IPResourceName | Get-ClusterParameter 
  ```

## <a name="step-7-test-fci-failover"></a>Passo 7: Testar a ativação pós-falha da FCI

Ativação pós-falha de teste da FCI para validar a funcionalidade de cluster. Efetue os seguintes passos:

1. Ligar a um de nós de cluster do SQL Server FCI com RDP.

1. Abra **Gestor de clusters de ativação pós-falha**. Clique em **funções**. Aviso de nó que possui a função do SQL Server FCI.

1. Clique com botão direito a função do SQL Server FCI.

1. Clique em **mover** e clique em **melhor nó possível**.

**Gestor de clusters de ativação pós-falha** mostra a função e os respetivos recursos fique offline. Os recursos, em seguida, mova e ficar online no outro nó.

### <a name="test-connectivity"></a>Testar conectividade

Para testar a conectividade, inicie sessão outra máquina virtual na mesma rede virtual. Abra **SQL Server Management Studio** e estabelecer ligação com o nome do SQL Server FCI.

>[!NOTE]
>Se necessário, pode [transferir SQL Server Management Studio](http://msdn.microsoft.com/library/mt238290.aspx).

## <a name="limitations"></a>Limitações
Máquinas virtuais do Azure, coordenador de transações distribuídas ' (DTC) da Microsoft não é suportada em FCIs porque a porta RPC não é suportada pelo balanceador de carga.

## <a name="see-also"></a>Veja Também

[A configuração S2D com o ambiente de trabalho remoto (Azure)](http://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-storage-spaces-direct-deployment)

[Hyper-convergido solução com espaços de armazenamento diretos](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct).

[Descrição geral direta do espaço de armazenamento](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview)

[Suporte do SQL Server para S2D](https://blogs.technet.microsoft.com/dataplatforminsider/2016/09/27/sql-server-2016-now-supports-windows-server-2016-storage-spaces-direct/)
