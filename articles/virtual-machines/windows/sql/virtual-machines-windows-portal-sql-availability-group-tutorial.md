---
title: "Grupos de disponibilidade do SQL Server - máquinas virtuais do Azure - Tutorial | Microsoft Docs"
description: Este tutorial mostra como criar um servidor sempre no grupo de disponibilidade SQL em Azure Virtual Machines.
services: virtual-machines
documentationCenter: na
authors: MikeRayMSFT
manager: jhubbard
editor: monicar
tags: azure-service-management
ms.assetid: 08a00342-fee2-4afe-8824-0db1ed4b8fca
ms.service: virtual-machines-sql
ms.devlang: na
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/09/2017
ms.author: mikeray
ms.openlocfilehash: 228ca9ca5fddc493d27bfd6a40df5ee7306d6aa9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-always-on-availability-group-in-azure-vm-manually"></a>Configurar sempre no grupo de disponibilidade na VM do Azure manualmente

Este tutorial mostra como criar um servidor sempre no grupo de disponibilidade SQL em Azure Virtual Machines. O tutorial concluído cria um grupo de disponibilidade com uma réplica de base de dados em dois servidores do SQL Server.

**Estimativa de tempo**: demora cerca de 30 minutos a concluir depois dos pré-requisitos são cumpridos.

O diagrama ilustra a compilação no tutorial.

![Grupo de disponibilidade](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

## <a name="prerequisites"></a>Pré-requisitos

O tutorial parte do princípio de que tem uma compreensão básica do SQL Server em grupos de disponibilidade Always. Se precisar de mais informações, consulte o artigo [descrição geral de grupos de disponibilidade Always (SQL Server)](http://msdn.microsoft.com/library/ff877884.aspx).

A tabela seguinte lista os pré-requisitos que precisa para concluir antes de iniciar este tutorial:

|  |Requisito |Descrição |
|----- |----- |----- |
|![parênteses](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png) | Dois servidores do SQL Server | -Num conjunto de disponibilidade do Azure <br/> -Num único domínio <br/> -Com a funcionalidade de Clustering de ativação pós-falha instalada |
|![parênteses](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)| Windows Server | Partilha de ficheiros para o testemunho de cluster |  
|![parênteses](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Conta de serviço do SQL Server | Conta de domínio |
|![parênteses](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Conta de serviço do SQL Server Agent | Conta de domínio |  
|![parênteses](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Abrir portas de firewall | -SQL Server: **1433** para a instância predefinida <br/> -Ponto final de espelhamento: **5022** ou qualquer porta disponível <br/> -Sonda do Balanceador de carga as do azure: **59999** ou qualquer porta disponível |
|![parênteses](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Adicionar a funcionalidade de Clustering de ativação pós-falha | Esta funcionalidade necessitam de ambos os servidores do SQL Server |
|![parênteses](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Conta de domínio de instalação | -Administrador local em cada servidor de SQL <br/> -O membro da função de servidor fixa sysadmin do SQL Server para cada instância do SQL Server  |


Antes de começar o tutorial, terá de [concluir os pré-requisitos para a criação de grupos de disponibilidade Always em Azure Virtual Machines](virtual-machines-windows-portal-sql-availability-group-prereq.md). Se os pré-requisitos são já foi concluídos, pode saltar para [criar Cluster](#CreateCluster).


<!--**Procedure**: *This is the first “step”. Make titles H2’s and short and clear – H2’s appear in the right pane on the web page and are important for navigation.*-->

<a name="CreateCluster"></a>
##Criar o cluster

Depois dos pré-requisitos estão concluídos, o primeiro passo é criar um Cluster de ativação pós-falha do Windows Server que inclua dois SQL Servers e um servidor de testemunho.  

1. RDP para o primeiro servidor de SQL Server utilizando uma conta de domínio que seja de administrador nos servidores do SQL Server e o servidor de testemunho.

   >[!TIP]
   >Se seguiu o [documento de pré-requisitos](virtual-machines-windows-portal-sql-availability-group-prereq.md), criou uma conta chamada **CORP\Install**. Utilize esta conta.

2. No **Gestor de servidor** dashboard, selecione **ferramentas**e, em seguida, clique em **Gestor de clusters de ativação pós-falha**.
3. No painel esquerdo, faça duplo clique **Gestor de clusters de ativação pós-falha**e, em seguida, clique em **criar um Cluster**.
   ![Criar Cluster](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/40-createcluster.png)
4. O Assistente para criar clusters, crie um cluster de um nó avance através das páginas com as definições na tabela seguinte:

   | Página | Definições |
   | --- | --- |
   | Antes de começar |Utilize as predefinições |
   | Selecionar servidores |Escreva o nome do SQL Server primeiro no **introduza o nome de servidor** e clique em **adicionar**. |
   | Aviso de validação |Selecione **um número não precisar do suporte da Microsoft para este cluster e, por conseguinte, não pretender executar testes de validação. Quando posso clicar em seguinte, continue a criar o cluster**. |
   | Ponto de acesso para administrar o Cluster |Escreva um nome de cluster, por exemplo **SQLAGCluster1** no **nome do Cluster**.|
   | Confirmação |Utilize as predefinições, exceto se estiver a utilizar os espaços de armazenamento. Consulte a nota após esta tabela. |

### <a name="set-the-cluster-ip-address"></a>Definir o endereço IP de cluster

1. No **Gestor de clusters de ativação pós-falha**, desloque para baixo até **recursos principais do Cluster** e expanda os detalhes do cluster. Deverá ver ambos o **nome** e o **endereço IP** recursos no **falha** estado. O recurso de endereço IP não pode ser colocado online porque o cluster é atribuído o mesmo endereço IP que o computador de si próprio, pelo que é um endereço duplicado.

2. Clique com botão direito a **endereço IP** recursos e, em seguida, clique em **propriedades**.

   ![Propriedades do cluster](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/42_IPProperties.png)

3. Selecione **endereço IP estático** e especifique um endereço de sub-rede onde o SQL Server está na caixa de texto endereço disponível. Em seguida, clique em **OK**.
4. No **recursos principais do Cluster** secção, clique no nome do cluster e clique em **colocar Online**. Em seguida, aguarde até que ambos os recursos estão online. Quando o recurso de nome de cluster estiver online, atualiza o servidor DC com uma nova conta de computador do AD. Utilize esta conta do AD para executar o serviço de grupo de disponibilidade em cluster mais tarde.

### <a name="addNode"></a>Adicionar o servidor de SQL ao cluster

Adicione outro servidor do SQL Server para o cluster.

1. Na árvore de browser, clique com o botão direito do cluster e clique em **adicionar nó**.

    ![Adicionar nó ao Cluster](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/44-addnode.png)

1. No **Assistente de adicionar nó**, clique em **seguinte**. No **selecionar servidores** página, adicione o segundo servidor de SQL. Escreva o nome do servidor no **introduza o nome de servidor** e, em seguida, clique em **adicionar**. Quando tiver terminado, clique em **seguinte**.

1. No **aviso de validação** página, clique em **não** (num cenário de produção, deve executar os testes de validação). Clique depois em **Seguinte**.

8. No **confirmação** página se estiver a utilizar os espaços de armazenamento, desmarque a caixa de verificação com etiqueta **adicionar todo o armazenamento elegível ao cluster.**

   ![Adicionar a confirmação do nó](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/46-addnodeconfirmation.png)

    >[!WARNING]
   >Se estiver a utilizar os espaços de armazenamento e não desmarque **adicionar todo o armazenamento elegível ao cluster**, Windows Desanexa os discos virtuais durante o processo de clustering. Como resultado, não aparecerem no Gestor de discos ou Explorer até que são removidos os espaços de armazenamento do cluster e novamente ligado com o PowerShell. Os espaços de armazenamento agrupa vários discos para agrupamentos de armazenamento. Para obter mais informações, consulte [espaços de armazenamento](https://technet.microsoft.com/library/hh831739).

1. Clique em **Seguinte**.

1. Clique em **Concluir**.

   Gestor de clusters de ativação pós-falha mostra que o cluster tem um novo nó e -la na lista de **nós** contentor.

10. Terminar a sessão de ambiente de trabalho remota.

### <a name="add-a-cluster-quorum-file-share"></a>Adicionar uma partilha de ficheiros de quórum do cluster

Neste exemplo o cluster do Windows utiliza uma partilha de ficheiros para criar um quórum de cluster. Este tutorial utiliza um quórum maioria de partilha de ficheiros e de nó. Para obter mais informações, consulte [Noções sobre configurações de quórum num Cluster de ativação pós-falha](http://technet.microsoft.com/library/cc731739.aspx).

1. Ligar ao servidor de membro de testemunho de partilha de ficheiros com uma sessão de ambiente de trabalho remoto.

1. No **Gestor de servidor**, clique em **ferramentas**. Abra **gestão de computadores**.

1. Clique em **pastas partilhadas**.

1. Clique com botão direito **partilhas**e clique em **nova partilha...** .

   ![Nova partilha](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/48-newshare.png)

   Utilize **partilhado pasta Assistente para criar um** para criar uma partilha.

1. No **caminho da pasta**, clique em **procurar** e localizem ou criar um caminho para a pasta partilhada. Clique em **Seguinte**.

1. No **nome, descrição e as definições** Certifique-se de que o nome da partilha e o caminho. Clique em **Seguinte**.

1. No **as permissões da pasta partilhada** definir **personalizar permissões**. Clique em **personalizados...** .

1. No **personalizar permissões**, clique em **adicionar...** .

1. Certifique-se de que a conta utilizada para criar o cluster tem controlo total.

   ![Nova partilha](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/50-filesharepermissions.png)

1. Clique em **OK**.

1. No **as permissões da pasta partilhada**, clique em **concluir**. Clique em **concluir** novamente.  

1. Termine a sessão do servidor

### <a name="configure-cluster-quorum"></a>Configurar o quórum do cluster

Em seguida, configure o quórum do cluster.

1. Ligar para o primeiro nó de cluster com o ambiente de trabalho remoto.

1. No **Gestor de clusters de ativação pós-falha**, clique com o botão direito do cluster, aponte para **mais ações**e clique em **configurar definições de quórum do Cluster...** .

   ![Nova partilha](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/52-configurequorum.png)

1. No **Cluster Assistente para configurar quórum**, clique em **seguinte**.

1. No **selecionar opção de configuração de quórum**, escolha **selecionar o testemunho de quórum**e clique em **seguinte**.

1. No **selecionar testemunho de quórum**, clique em **configurar um testemunho de partilha de ficheiros**.

   >[!TIP]
   >Windows Server 2016 suporta um testemunho de nuvem. Se escolher este tipo de testemunho, não precisa de um ficheiro de testemunho de partilha. Para obter mais informações, consulte [implementar um testemunho de nuvem para um Cluster de ativação pós-falha](http://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness). Este tutorial utiliza um testemunho de partilha de ficheiros, que é suportado pelos sistemas operativos anteriores.

1. No **configurar testemunho de partilha de ficheiros**, escreva o caminho para a partilha que criou. Clique em **Seguinte**.

1. Verifique as definições no **confirmação**. Clique em **Seguinte**.

1. Clique em **Concluir**.

Os recursos de núcleo de cluster fiquem configurados com um testemunho de partilha de ficheiros.

## <a name="enable-availability-groups"></a>Ativar grupos de disponibilidade

Em seguida, ative o **grupos de Disponibilidade AlwaysOn** funcionalidade. Execute estes passos em ambos os servidores SQL.

1. Do **iniciar** ecrã, iniciar **Gestor de configuração do SQL Server**.
2. Na árvore de browser, clique em **serviços do SQL Server**, em seguida, clique com botão direito do **SQL Server (MSSQLSERVER)** de serviço e clique em **propriedades**.
3. Clique em de **elevada disponibilidade do AlwaysOn** separador, em seguida, selecione **ative os grupos de Disponibilidade AlwaysOn**, da seguinte forma:

    ![Ativar grupos de Disponibilidade AlwaysOn](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/54-enableAlwaysOn.png)

4. Clique em **Aplicar**. Clique em **OK** na caixa de diálogo de pop-up.

5. Reinicie o serviço do SQL Server.

Repita estes passos no servidor SQL.

<!-----------------
## <a name="endpoint-firewall"></a>Open firewall for the database mirroring endpoint

Each instance of SQL Server that participates in an Availability Group requires a database mirroring endpoint. This endpoint is a TCP port for the instance of SQL Server that is used to synchronize the database replicas in the Availability Groups on that instance.

On both SQL Servers, open the firewall for the TCP port for the database mirroring endpoint.

1. On the first SQL Server **Start** screen, launch **Windows Firewall with Advanced Security**.
2. In the left pane, select **Inbound Rules**. On the right pane, click **New Rule**.
3. For **Rule Type**, choose **Port**.
1. For the port, specify TCP and choose an unused TCP port number. For example, type *5022* and click **Next**.

   >[!NOTE]
   >For this example, we're using TCP port 5022. You can use any available port.

5. In the **Action** page, keep **Allow the connection** selected and click **Next**.
6. In the **Profile** page, accept the default settings and click **Next**.
7. In the **Name** page, specify a rule name, such as **Default Instance Mirroring Endpoint** in the **Name** text box, then click **Finish**.

Repeat these steps on the second SQL Server.
-------------------------->

## <a name="create-a-database-on-the-first-sql-server"></a>Criar uma base de dados no primeiro servidor de SQL

1. Inicie o ficheiro RDP para o primeiro servidor de SQL com uma conta de domínio que seja membro da função de servidor fixa sysadmin.
1. Abra o SQL Server Management Studio e ligue para o primeiro servidor de SQL.
7. No **Object Explorer**, faça duplo clique **bases de dados** e clique em **nova base de dados**.
8. No **nome de base de dados**, tipo **MyDB1**, em seguida, clique em **OK**.

### <a name="backupshare"></a>Criar uma partilha de cópia de segurança

1. No primeiro servidor de SQL no **Gestor de servidor**, clique em **ferramentas**. Abra **gestão de computadores**.

1. Clique em **pastas partilhadas**.

1. Clique com botão direito **partilhas**e clique em **nova partilha...** .

   ![Nova partilha](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/48-newshare.png)

   Utilize **partilhado pasta Assistente para criar um** para criar uma partilha.

1. No **caminho da pasta**, clique em **procurar** e localizem ou criar um caminho para a pasta de partilha de cópia de segurança da base de dados. Clique em **Seguinte**.

1. No **nome, descrição e as definições** Certifique-se de que o nome da partilha e o caminho. Clique em **Seguinte**.

1. No **as permissões da pasta partilhada** definir **personalizar permissões**. Clique em **personalizados...** .

1. No **personalizar permissões**, clique em **adicionar...** .

1. Certifique-se de que as contas de serviço do SQL Server e SQL Server Agent para ambos os servidores têm controlo total.

   ![Nova partilha](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/68-backupsharepermission.png)

1. Clique em **OK**.

1. No **as permissões da pasta partilhada**, clique em **concluir**. Clique em **concluir** novamente.  

### <a name="take-a-full-backup-of-the-database"></a>Criar um completo cópia de segurança da base de dados

Terá de criar cópias de segurança nova base de dados ao inicializar a cadeia de registos. Se não efetuar uma cópia de segurança da base de dados nova, não pode ser incluído num grupo de disponibilidade.

1. No **Object Explorer**, faça duplo clique na base de dados, aponte para **tarefas...** , clique em **cópia de segurança**.

1. Clique em **OK** colocar um completo cópia de segurança para a localização de cópia de segurança predefinida.

## <a name="create-the-availability-group"></a>Criar o grupo de disponibilidade
Agora, está pronto para configurar um grupo de disponibilidade utilizando os seguintes passos:

* Crie uma base de dados no primeiro servidor de SQL.
* Coloque uma cópia de segurança completa e uma cópia de segurança do registo de transações da base de dados
* Restaurar o completo e cópias de segurança de registo para o segundo servidor de SQL com o **NORECOVERY** opção
* Criar o grupo de disponibilidade (**AG1**) com consolidação síncrona, ativação pós-falha automática e as réplicas secundárias legíveis

### <a name="create-the-availability-group"></a>Crie o grupo de disponibilidade:

1. Numa sessão de ambiente de trabalho remoto para o primeiro servidor de SQL. No **Object Explorer** no SSMS, faça duplo clique **elevada disponibilidade do AlwaysOn** e clique em **Assistente de novo grupo de disponibilidade**.

    ![Iniciar o Assistente de novo grupo de disponibilidade](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/56-newagwiz.png)

2. No **introdução** página, clique em **seguinte**. No **Especificar nome do grupo de disponibilidade** , escreva um nome para o grupo de disponibilidade, por exemplo **AG1**, na **nome do grupo de disponibilidade**. Clique em **Seguinte**.

    ![Assistente de nova AG, especifique o nome de AG](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/58-newagname.png)

3. No **selecionar bases de dados** página, selecione a base de dados e clique em **seguinte**.

   >[!NOTE]
   >A base de dados cumpre os pré-requisitos para um grupo de disponibilidade, porque ter demorado, pelo menos, uma cópia de segurança completa na réplica primária pretendida.

   ![Assistente de nova AG, selecione as bases de dados](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/60-newagselectdatabase.png)
4. No **especificar réplicas** página, clique em **Adicionar réplica**.

   ![Assistente de nova AG, especifique as réplicas](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/62-newagaddreplica.png)
5. O **ligar ao servidor** aparece a caixa de diálogo. Escreva o nome do segundo servidor no **nome do servidor**. Clique em **Ligar**.

   Volta a **especificar réplicas** página, deverá ver o segundo servidor listado no **réplicas de disponibilidade**. Configure as réplicas da seguinte forma.

   ![Assistente de nova AG, especifique as réplicas (completa)](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/64-newagreplica.png)

6. Clique em **pontos finais** para ver o ponto final de espelhamento para este grupo de disponibilidade. Utilizar a mesma porta que utilizou quando definiu o [regra de firewall para pontos finais de espelhamento da base de dados](virtual-machines-windows-portal-sql-availability-group-prereq.md#endpoint-firewall).

    ![Assistente de nova AG, selecione a sincronização inicial de dados](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/66-endpoint.png)

8. No **selecionar sincronização de dados inicial** página, selecione **completa** e especifique uma localização de rede partilhada. Para a localização, utilize o [partilha de cópia de segurança que criou](#backupshare). No exemplo estava, **\\\\\<primeiro o SQL Server\>\Backup\**. Clique em **Seguinte**.

   >[!NOTE]
   >Sincronização completa demora uma cópia de segurança completa da base de dados na primeira instância do SQL Server e restaura-lo para a segunda instância. Sincronização completa não é recomendada para bases de dados grandes, porque poderá demorar muito tempo. Pode reduzir este tempo por colocar uma cópia de segurança da base de dados e restaurado com manualmente `NO RECOVERY`. Se a base de dados já for restaurado com `NO RECOVERY` na segunda do SQL Server antes de configurar o grupo de disponibilidade, escolha **apenas junção**. Se pretender efetuar a cópia de segurança depois de configurar o grupo de disponibilidade, escolha **ignorar sincronização de dados inicial**.

    ![Assistente de nova AG, selecione a sincronização inicial de dados](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/70-datasynchronization.png)

9. No **validação** página, clique em **seguinte**. Esta página deve ter um aspeto semelhante a imagem seguinte:

    ![Assistente de nova AG, validação](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/72-validation.png)

    >[!NOTE]
    >Não há um aviso para a configuração do serviço de escuta porque não tiver configurado um serviço de escuta do grupo de disponibilidade. Pode ignorar este aviso porque em máquinas virtuais do Azure cria o serviço de escuta depois de criar o Balanceador de carga do Azure.

10. No **resumo** página, clique em **concluir**, em seguida, aguarde enquanto o assistente configura o novo grupo de disponibilidade. No **progresso** página, pode clicar **mais detalhes** para ver o progresso de detalhado. Assim que o assistente estiver concluído, Inspecione o **resultados** página para verificar se o grupo de disponibilidade é criado com êxito.

     ![Assistente de novo AG resulta](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/74-results.png)
11. Clique em **fechar** para sair do assistente.

### <a name="check-the-availability-group"></a>Verifique o grupo de disponibilidade

1. No **Object Explorer**, expanda **elevada disponibilidade do AlwaysOn**, em seguida, expanda **grupos de disponibilidade**. Deverá ver o novo grupo de disponibilidade neste contentor. O grupo de disponibilidade com o botão direito e clique em **Mostrar Dashboard**.

   ![Mostrar AG Dashboard](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/76-showdashboard.png)

   O **AlwaysOn Dashboard** deve ter um aspeto semelhante a isto.

   ![Dashboard de AG](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/78-agdashboard.png)

   Pode ver as réplicas, o modo de ativação pós-falha de cada réplica e o estado da sincronização.

2. No **Gestor de clusters de ativação pós-falha**, clique em cluster. Selecione **funções**. O nome do grupo de disponibilidade que utilizou é uma função em cluster. Se o grupo de disponibilidade não tem um endereço IP para ligações de cliente, porque não foi possível configurar um serviço de escuta. Irá configurar o serviço de escuta depois de criar um balanceador de carga do Azure.

   ![AG no Gestor de clusters de ativação pós-falha](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/80-clustermanager.png)

   > [!WARNING]
   > Não tente efetuar a ativação pós-falha do grupo de disponibilidade do Gestor de clusters de ativação pós-falha. Todas as operações de ativação pós-falha devem ser efetuadas a partir do **AlwaysOn Dashboard** no SSMS. Para obter mais informações, consulte [restrições no utilizando a ativação pós-falha Gestor de clusters com grupos de disponibilidade](https://msdn.microsoft.com/library/ff929171.aspx).
    >

Neste momento, tem um grupo de disponibilidade com réplicas em duas instâncias do SQL Server. Pode mover o grupo de disponibilidade entre instâncias. Não é possível ligar ao grupo de disponibilidade ainda porque não dispõe de um serviço de escuta. Máquinas virtuais do Azure, o serviço de escuta necessita de um balanceador de carga. O passo seguinte consiste em criar Balanceador de carga no Azure.

<a name="configure-internal-load-balancer"></a>

## <a name="create-an-azure-load-balancer"></a>Criar um balanceador de carga do Azure

Máquinas virtuais do Azure, um grupo de disponibilidade do SQL Server necessita de um balanceador de carga. O Balanceador de carga se o endereço IP para o serviço de escuta do grupo de disponibilidade. Esta secção resume como criar o Balanceador de carga no portal do Azure.

1. No portal do Azure, vá para o grupo de recursos em que os servidores do SQL Server estão e clique em **+ adicionar**.
2. Procurar **Balanceador de carga**. Escolha o Balanceador de carga publicado pela Microsoft.

   ![AG no Gestor de clusters de ativação pós-falha](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/82-azureloadbalancer.png)

1.  Clique em **Criar**.
3. Configure os parâmetros seguintes para o Balanceador de carga.

   | Definição | Campo |
   | --- | --- |
   | **Nome** |Utilize um nome de texto para o Balanceador de carga, por exemplo **sqlLB**. |
   | **Tipo** |Interno |
   | **Rede virtual** |Utilize o nome da rede virtual do Azure. |
   | **Sub-rede** |Utilize o nome da sub-rede que a máquina virtual está numa.  |
   | **Atribuição de endereços IP** |Estático |
   | **Endereço IP** |Utilize um endereço disponível da sub-rede. |
   | **Subscrição** |Utilize a mesma subscrição que a máquina virtual. |
   | **Localização** |Utilize a mesma localização que a máquina virtual. |

   O painel do portal do Azure deve ter o seguinte aspeto:

   ![Criar Balanceador de carga](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/84-createloadbalancer.png)

1. Clique em **criar**, para criar o Balanceador de carga.

Para configurar o Balanceador de carga, terá de criar um conjunto de back-end, uma pesquisa e definir a regras de balanceamento de carga. Efetue estes no portal do Azure.

### <a name="add-backend-pool"></a>Adicionar o conjunto de back-end

1. No portal do Azure, aceda ao seu grupo de disponibilidade. Poderá ter de atualizar a vista para ver o Balanceador de carga criado recentemente.

   ![Localizar o Balanceador de carga no grupo de recursos](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/86-findloadbalancer.png)

1. Clique o Balanceador de carga, clique em **conjuntos back-end**e clique em **+ adicionar**. Defina o conjunto de back-end da seguinte forma:

   | Definição | Descrição | Exemplo
   | --- | --- |---
   | **Nome** | Escreva um nome de texto | SQLLBBE
   | **Associado a** | Escolha a partir da lista | Conjunto de disponibilidade
   | **Conjunto de disponibilidade** | Utilize um nome do conjunto de disponibilidade que as suas VMs do SQL Server estão em | sqlAvailabilitySet |
   | **Máquinas virtuais** |Os dois nomes de VM do Azure SQL Server | SQLServer-0, sqlserver 1

1. Escreva o nome para o conjunto de back-end.

1. Clique em **+ adicionar uma máquina virtual**.

1. Para o conjunto de disponibilidade, escolha que o conjunto de disponibilidade que os servidores do SQL Server estão em.

1. Para máquinas virtuais, incluem ambos os servidores SQL. Não inclua o servidor de testemunho de partilha de ficheiros.

1. Clique em **OK** para criar o conjunto de back-end.

### <a name="set-the-probe"></a>Definir a sonda

1. Clique o Balanceador de carga, clique em **sondas de estado de funcionamento**e clique em **+ adicionar**.

1. Defina a sonda de estado de funcionamento da seguinte forma:

   | Definição | Descrição | Exemplo
   | --- | --- |---
   | **Nome** | Texto | SQLAlwaysOnEndPointProbe |
   | **Protocolo** | Escolha TCP | TCP |
   | **Porta** | Qualquer porta não utilizada | 59999 |
   | **Intervalo**  | A quantidade de tempo entre tentativas da sonda em segundos |5 |
   | **Limiar de mau estado de funcionamento** | O número de falhas consecutivas da sonda que têm de ocorrer para uma máquina virtual para ser considerado em mau estado de funcionamento  | 2 |

1. Clique em **OK** para definir a sonda de estado de funcionamento.

### <a name="set-the-load-balancing-rules"></a>Definir a regras de balanceamento de carga

1. Clique o Balanceador de carga, clique em **as regras de balanceamento de carga**e clique em **+ adicionar**.

1. Defina a forma de regras de balanceamento de carga.
   | Definição | Descrição | Exemplo
   | --- | --- |---
   | **Nome** | Texto | SQLAlwaysOnEndPointListener |
   | **Endereço IP de front-end** | Escolha um endereço |Utilize o endereço que criou quando criou o Balanceador de carga. |
   | **Protocolo** | Escolha TCP |TCP |
   | **Porta** | Utilizar a porta para a instância do SQL Server | 1433 |
   | **Porta de back-end** | Este campo não é utilizado quando o IP flutuante está definido para direta do servidor retorno | 1433 |
   | **Sonda** |O nome especificado para a sonda | SQLAlwaysOnEndPointProbe |
   | **Persistência da sessão** | Na lista pendente | **Nenhum** |
   | **Tempo limite de inatividade** | Minutos para manter uma ligação de TCP aberta | 4 |
   | **Vírgula flutuante (devolução direta do servidor) de IP** | |Ativado |

   > [!WARNING]
   > Devolução direta do servidor é definida durante a criação. Não pode ser alterada.

1. Clique em **OK** para definir a regras de balanceamento de carga.

## <a name="configure-listener"></a>Configurar o serviço de escuta

A seguinte coisa a fazer é configurar um serviço de escuta do grupo de disponibilidade no cluster de ativação pós-falha.

> [!NOTE]
> Este tutorial mostra como criar um serviço de escuta único - com um endereço IP do ILB. Para criar um ou mais serviços de escuta com um ou mais endereços IP, consulte [Balanceador de carga e do serviço de escuta do grupo de disponibilidade criar | Azure](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
>
>

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

## <a name="set-listener-port"></a>Porta do serviço de escuta de conjunto

No SQL Server Management Studio, defina a porta do serviço de escuta.

1. Inicie o SQL Server Management Studio e ligue-se para a réplica primária.

1. Navegue para **AlwaysOn alta disponibilidade** | **grupos de disponibilidade** | **serviços de escuta do grupo de disponibilidade**.

1. Deverá ver o nome do serviço de escuta que criou no Gestor de clusters de ativação pós-falha. O nome do serviço de escuta com o botão direito e clique em **propriedades**.

1. No **porta** caixa, especifique o número de porta para o serviço de escuta do grupo de disponibilidade com $EndpointPort que utilizou anteriormente (era a predefinição de 1433), em seguida, clique em **OK**.

Tem agora um grupo de disponibilidade do SQL Server em máquinas virtuais do Azure em execução no modo Resource Manager.

## <a name="test-connection-to-listener"></a>Testar a ligação ao serviço de escuta

Para testar a ligação:

1. RDP para um SQL Server que está a ser a mesma rede virtual, mas não possui a réplica. Pode utilizar o servidor de SQL no cluster.

1. Utilize **sqlcmd** utilitário para testar a ligação. Por exemplo, o script seguinte estabelece uma **sqlcmd** ligação para a réplica primária através do serviço de escuta com autenticação do Windows:

    ```
    sqlcmd -S <listenerName> -E
    ```

    Se o serviço de escuta está a utilizar uma porta diferente da predefinição a porta (1433), especifique a porta na cadeia de ligação. Por exemplo, o seguinte comando sqlcmd se liga a um serviço de escuta na porta 1435:

    ```
    sqlcmd -S <listenerName>,1435 -E
    ```

A ligação de SQLCMD liga-se automaticamente para qualquer instância do SQL Server aloja a réplica primária.

> [!TIP]
> Certifique-se de que a porta especificada está aberta na firewall de ambos os servidores SQL. Ambos os servidores necessitam de uma regra de entrada para a porta TCP que utiliza. Para obter mais informações, consulte [adicionar ou Editar regra de Firewall](http://technet.microsoft.com/library/cc753558.aspx).
>
>



<!--**Notes**: *Notes provide just-in-time info: A Note is “by the way” info, an Important is info users need to complete a task, Tip is for shortcuts. Don’t overdo*.-->


<!--**Procedures**: *This is the second “step." They often include substeps. Again, use a short title that tells users what they’ll do*. *("Configure a new web project.")*-->

<!--**UI**: *Note the format for documenting the UI: bold for UI elements and arrow keys for sequence. (Ex. Click **File > New > Project**.)*-->

<!--**Screenshot**: *Screenshots really help users. But don’t include too many since they’re difficult to maintain. Highlight areas you are referring to in red.*-->

<!--**No. of steps**: *Make sure the number of steps within a procedure is 10 or fewer. Seven steps is ideal. Break up long procedure logically.*-->


<!--**Next steps**: *Reiterate what users have done, and give them interesting and useful next steps so they want to go on.*-->

## <a name="next-steps"></a>Passos seguintes

- [Adicionar um endereço IP para um balanceador de carga para um segundo grupo de disponibilidade](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md#Add-IP).
