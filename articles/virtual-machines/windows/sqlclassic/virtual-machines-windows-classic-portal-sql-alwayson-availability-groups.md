---
title: "Configurar o grupo de disponibilidade Always On em Virtual Machines do Azure (clássica) | Microsoft Docs"
description: "Crie um grupo de disponibilidade Always On com máquinas virtuais do Azure. Este tutorial utiliza principalmente as ferramentas de interface de utilizador e, em vez de scripting."
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 8d48a3d2-79f8-4ab0-9327-2f30ee0b2ff1
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/17/2017
ms.author: mikeray
ms.openlocfilehash: b360fe9f28eeb9b10c82fce729165b1b572ac3c6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-always-on-availability-group-in-azure-virtual-machines-classic"></a>Configurar o grupo de disponibilidade Always On em Virtual Machines do Azure (clássica)
> [!div class="op_single_selector"]
> * [Clássico: IU](../classic/portal-sql-alwayson-availability-groups.md)
> * [Clássico: PowerShell](../classic/ps-sql-alwayson-availability-groups.md)
<br/>

Antes de começar, considere o que pode concluir esta tarefa no modelo Azure Resource Manager. Recomendamos que o modelo Azure Resource Manager para novas implementações. Consulte [SQL Server Always On nos grupos de disponibilidade em máquinas virtuais do Azure](../sql/virtual-machines-windows-portal-sql-availability-group-overview.md).

> [!IMPORTANT] 
> A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager. O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Resource Manager e clássico](../../../azure-resource-manager/resource-manager-deployment-model.md). Este artigo explica como utilizar o modelo de implementação clássica. 

Para concluir esta tarefa com o modelo Azure Resource Manager, consulte [SQL Server Always On nos grupos de disponibilidade em máquinas virtuais do Azure](../sql/virtual-machines-windows-portal-sql-availability-group-overview.md).

Este tutorial ponto-a-ponto mostra-lhe como implementar grupos de disponibilidade com o SQL Server Always On em execução em máquinas de virtuais do Azure.

No final do tutorial, a solução SQL Server Always On no Azure serão consistem os seguintes elementos:

* Uma rede virtual que contém várias sub-redes e inclui um front-end e uma sub-rede de back-end
* Um controlador de domínio com um domínio do Active Directory (Azure AD)
* Duas máquinas virtuais que executam o SQL Server e que estão implementadas para a sub-rede de back-end e associadas ao domínio do Azure AD
* Um cluster de ativação pós-falha de três nós com o modelo de quórum maioria de nós
* Um grupo de disponibilidade que tem duas réplicas com consolidação síncrona de uma base de dados de disponibilidade

A ilustração seguinte é uma representação gráfica da solução.

![Arquitetura de laboratório de teste para grupos de disponibilidade no Azure](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC791912.png)

Tenha em atenção que se trata de uma configuração de possíveis. Por exemplo, pode minimizar o número de máquinas virtuais para um grupo de disponibilidade de duas réplicas. Esta configuração guarda em horas de computação no Azure utilizando o controlador de domínio como o testemunho de partilha de ficheiros de quórum num cluster de dois nós. Este método reduz a contagem de máquinas virtuais por um da configuração de ilustradas.

Este tutorial assume o seguinte:

* Já tem uma conta do Azure.
* Já sabe como utilizar o GUI na galeria da máquina virtual para Aprovisionar uma máquina virtual clássica que executa o SQL Server.
* Já existe uma compreensão sólida dos grupos de disponibilidade Always On. Para obter mais informações, consulte [sempre em grupos de disponibilidade (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx).

> [!NOTE]
> Se estiver interessado em utilizar grupos de disponibilidade Always On com o SharePoint, consulte também [configurar SQL Server 2012 Always On grupos de disponibilidade para SharePoint 2013](https://technet.microsoft.com/library/jj715261.aspx).
> 
> 

## <a name="create-the-virtual-network-and-domain-controller-server"></a>Criar o servidor de controlador de domínio e de rede virtual
Começar com uma nova conta de avaliação do Azure. Depois de configurar a sua conta, deve estar no ecrã inicial do portal clássico do Azure.

1. Clique em de **novo** botão no canto esquerdo da parte inferior da página, conforme mostrado na captura de ecrã seguinte.
   
    ![Clique em novo no portal](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665511.gif)
2. Clique em **serviços de rede** > **rede Virtual** > **criação personalizada**, conforme mostrado na captura de ecrã seguinte.
   
    ![Criar rede Virtual](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665512.gif)
3. No **criar uma rede VIRTUAL** diálogo caixa, crie uma nova rede virtual avance através das páginas e utilizando as definições na tabela seguinte. 
   
   | Página | Definições |
   | --- | --- |
   | Detalhes de rede virtual |**NOME = ContosoNET**<br/>**REGIÃO = EUA oeste** |
   | Servidores DNS e Conetividade VPN |Nenhuma |
   | Espaços de endereços de rede virtual |As definições são apresentadas na captura de ecrã seguinte: ![Criar rede Virtual](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784620.png) |
4. Crie a máquina virtual que irá utilizar como o controlador de domínio (DC). Clique em **novo** > **computação** > **Máquina Virtual** > **da galeria**, conforme mostrado na captura de ecrã seguinte.
   
    ![Criar uma máquina virtual](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784621.png)
5. No **criar máquina VIRTUAL de um** diálogo caixa, configure uma nova máquina virtual, avance através das páginas e utilizando as definições na tabela seguinte. 
   
   | Página | Definições |
   | --- | --- |
   | Selecione o sistema operativo da máquina virtual |Windows Server 2012 R2 Datacenter |
   | Configuração da máquina virtual |**DATA de lançamento da versão** = (mais recentes)<br/>**NOME da máquina VIRTUAL** = ContosoDC<br/>**CAMADA** = PADRÃO<br/>**TAMANHO** = A2 (2 núcleos)<br/>**NOVO nome de utilizador** = AzureAdmin<br/>**NOVA palavra-passe** = Contoso! 000<br/>**Confirmar** = Contoso! 000 |
   | Configuração da máquina virtual |**SERVIÇO em NUVEM** = criar um novo serviço em nuvem<br/>**NOME de DNS do serviço de NUVEM** = um nome de serviço de nuvem exclusivo<br/>**NOME DNS** = um nome exclusivo (ex: ContosoDC123)<br/>**REGIÃO/grupo de AFINIDADE de rede VIRTUAL** = ContosoNET<br/>**SUB-redes da rede VIRTUAL** = Back(10.10.2.0/24)<br/>**CONTA de armazenamento** = utilizar uma conta de armazenamento gerado automaticamente<br/>**CONJUNTO de disponibilidade** = (nenhum) |
   | Opções de máquina virtual |Utilize as predefinições |

Depois de configurar a nova máquina virtual, aguarde que a máquina virtual para ser provsioned. Este processo demora algum tempo a concluir. Se clicar no **Máquina Virtual** separador no portal clássico do Azure, pode ver ContosoDC Estados de ciclos de **inicial (aprovisionamento)** para **parado**, **inicial**, **em execução (aprovisionamento)**e, finalmente, **executar**.

O servidor DC com êxito está agora aprovisionado. Em seguida, irá configurar o domínio do Active Directory neste servidor DC.

## <a name="configure-the-domain-controller"></a>Configurar o controlador de domínio
Nos passos seguintes, configure a máquina ContosoDC como um controlador de domínio corp.contoso.com.

1. No portal, selecione o **ContosoDC** máquina. No **Dashboard** separador, clique em **Connect** para abrir um ficheiro de ambiente de trabalho remoto (RDP) para acesso de ambiente de trabalho remoto.
   
    ![Ligar à máquina Vritual](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784622.png)
2. Inicie sessão com a sua conta de administrador configurado (**\AzureAdmin**) e palavra-passe (**Contoso! 000**).
3. Por predefinição, o **Gestor de servidor** dashboard deve ser apresentado.
4. Clique em de **para adicionar funções e funcionalidades** ligação no dashboard.
   
    ![Explorador de servidores adicionar funções](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784623.png)
5. Clique em **seguinte** até chegar à **funções de servidor** secção.
6. Selecione o **serviços de domínio do Active Directory** e **servidor DNS** funções. Quando lhe for pedido, adicione mais funcionalidades que requerem a estas funções.
   
   > [!NOTE]
   > Irá receber uma aviso que não há nenhum endereço IP estático de validação. Se estiver a testar a configuração, clique em **continuar**. Para cenários de produção [utilizar o PowerShell para definir o endereço IP estático do computador de controlador domínio](../../../virtual-network/virtual-networks-reserved-private-ip.md).
   > 
   > 
   
    ![Funções caixa de diálogo Adicionar](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784624.png)
7. Clique em **seguinte** até chegar a **confirmação** secção. Selecione o **reinicie o servidor de destino automaticamente se necessário** caixa de verificação.
8. Clique em **Instalar**.
9. Depois das funcionalidades são instaladas, regresse ao **Gestor de servidor** dashboard.
10. Selecione o novo **do AD DS** opção no painel esquerdo.
11. Clique em de **mais** hiperligação na barra de aviso amarelo.
    
     ![Caixa de diálogo do AD DS na máquina de virtual do servidor DNS](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784625.png)
12. No **ação** coluna o **todos os detalhes da tarefa de servidor** caixa de diálogo, clique em **promover este servidor a controlador de domínio**.
13. No **Assistente do configuração de serviços de domínio do Active Directory**, utilize os seguintes valores:
    
    | Página | Definição |
    | --- | --- |
    | Configuração de implementação |**Adicionar uma nova floresta** = selecionados<br/>**Nome de domínio de raiz** = corp.contoso.com |
    | Opções de controlador de domínio |**Palavra-passe** = Contoso! 000<br/>**Confirmar palavra-passe** = Contoso! 000 |
14. Clique em **seguinte** passar por outras páginas no assistente. No **verificação de pré-requisitos** página, certifique-se de que vê a mensagem seguinte: **todas as verificações de pré-requisitos passaram com êxito**. Tenha em atenção que deve consultar todas as mensagens de aviso aplicável, mas é possível continuar a instalação.
15. Clique em **Instalar**. O **ContosoDC** máquina virtual reiniciará automaticamente.

## <a name="configure-domain-accounts"></a>Configurar contas de domínio
Os passos seguintes configuram as contas do Active Directory para utilização posterior.

1. Voltar a iniciar sessão para o **ContosoDC** máquina.
2. No **Gestor de servidor**, clique em **ferramentas** > **Active Directory Administrative Center**.
   
    ![Centro de administração do Active Directory](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784626.png)
3. No **Active Directory Administrative Center**, selecione **corp (local)** no painel esquerdo.
4. No lado direito **tarefas** painel, clique em **novo** > **utilizador**. Utilize as seguintes definições:
   
   | Definição | Valor |
   | --- | --- |
   | **Nome próprio** |Instalar |
   | **SamAccountName de utilizador** |Instalar |
   | **Palavra-passe** |Contoso! 000 |
   | **Confirmar palavra-passe** |Contoso! 000 |
   | **Outras opções de palavra-passe** |Selecionado |
   | **Palavra-passe nunca expira** |Assinalado |
5. Clique em **OK** para criar o **instalar** utilizador. Esta conta será utilizada para configurar o cluster de ativação pós-falha e o grupo de disponibilidade.
6. Criar dois utilizadores adicionais, **CORP\SQLSvc1** e **CORP\SQLSvc2**, com os mesmos passos. Estas contas serão utilizadas para instâncias do SQL Server. Em seguida, terá de conceder **CORP\Install** as permissões necessárias para configurar o clustering de ativação pós-falha do Windows.
7. No **Active Directory Administrative Center**, clique em **corp (local)** no painel esquerdo. No **tarefas** painel, clique em **propriedades**.
   
    ![Propriedades do utilizador CORP](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784627.png)
8. Selecione **extensões**e, em seguida, clique em de **avançadas** botão no **segurança** separador.
9. No **definições de segurança avançadas para corp** caixa de diálogo, clique em **adicionar**.
10. Clique em **selecionar um principal**, procure **CORP\Install**e, em seguida, clique em **OK**.
11. Selecione o **ler todas as propriedades** e **criar objetos de computador** permissões.
    
     ![Permissões de utilizador Corp](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784628.png)
12. Clique em **OK**e, em seguida, clique em **OK** novamente. Feche a janela de propriedades da rede empresarial.

Agora que configurou o Active Directory e os objetos de utilizador, irá criar as três máquinas virtuais do SQL Server e associe-las a este domínio.

## <a name="create-the-sql-server-virtual-machines"></a>Criar as máquinas virtuais do SQL Server
Crie três máquinas virtuais. Um é um nó de cluster e dois estão para o SQL Server. Para criar cada uma das máquinas virtuais, volte ao portal clássico do Azure, clique em **novo** > **computação** > **Máquina Virtual** > **da galeria**. Em seguida, utilize os modelos na seguinte tabela para o ajudar a criar as máquinas virtuais.

| Página | VM1 | VM2 | VM3 |
| --- | --- | --- | --- |
| Selecione o sistema operativo da máquina virtual |**O Centro de dados do Windows Server 2012 R2** |**Enterprise do SQL Server 2014 RTM** |**Enterprise do SQL Server 2014 RTM** |
| Configuração da máquina virtual |**DATA de lançamento da versão** = (mais recentes)<br/>**NOME da máquina VIRTUAL** = ContosoWSFCNode<br/>**CAMADA** = PADRÃO<br/>**TAMANHO** = A2 (2 núcleos)<br/>**NOVO nome de utilizador** = AzureAdmin<br/>**NOVA palavra-passe** = Contoso! 000<br/>**Confirmar** = Contoso! 000 |**DATA de lançamento da versão** = (mais recentes)<br/>**NOME da máquina VIRTUAL** = ContosoSQL1<br/>**CAMADA** = PADRÃO<br/>**TAMANHO** = A3 (4 núcleos)<br/>**NOVO nome de utilizador** = AzureAdmin<br/>**NOVA palavra-passe** = Contoso! 000<br/>**Confirmar** = Contoso! 000 |**DATA de lançamento da versão** = (mais recentes)<br/>**NOME da máquina VIRTUAL** = ContosoSQL2<br/>**CAMADA** = PADRÃO<br/>**TAMANHO** = A3 (4 núcleos)<br/>**NOVO nome de utilizador** = AzureAdmin<br/>**NOVA palavra-passe** = Contoso! 000<br/>**Confirmar** = Contoso! 000 |
| Configuração da máquina virtual |**SERVIÇO em NUVEM** = nome DNS do serviço de nuvem exclusivo criado anteriormente (ex: ContosoDC123)<br/>**REGIÃO/grupo de AFINIDADE de rede VIRTUAL** = ContosoNET<br/>**SUB-redes da rede VIRTUAL** = Back(10.10.2.0/24)<br/>**CONTA de armazenamento** = utilizar uma conta de armazenamento gerado automaticamente<br/>**CONJUNTO de disponibilidade** = criar um disponibilidade definido<br/>**NOME DO CONJUNTO DE DISPONIBILIDADE** = SQLHADR |**SERVIÇO em NUVEM** = nome DNS do serviço de nuvem exclusivo criado anteriormente (ex: ContosoDC123)<br/>**REGIÃO/grupo de AFINIDADE de rede VIRTUAL** = ContosoNET<br/>**SUB-redes da rede VIRTUAL** = Back(10.10.2.0/24)<br/>**CONTA de armazenamento** = utilizar uma conta de armazenamento gerado automaticamente<br/>**CONJUNTO de disponibilidade** = SQLHADR (também pode configurar o conjunto depois de criada a máquina de disponibilidade. As três máquinas devem ser atribuídas ao conjunto de disponibilidade SQLHADR.) |**SERVIÇO em NUVEM** = nome DNS do serviço de nuvem exclusivo criado anteriormente (ex: ContosoDC123)<br/>**REGIÃO/grupo de AFINIDADE de rede VIRTUAL** = ContosoNET<br/>**SUB-redes da rede VIRTUAL** = Back(10.10.2.0/24)<br/>**CONTA de armazenamento** = utilizar uma conta de armazenamento gerado automaticamente<br/>**CONJUNTO de disponibilidade** = SQLHADR (também pode configurar o conjunto depois de criada a máquina de disponibilidade. As três máquinas devem ser atribuídas ao conjunto de disponibilidade SQLHADR.) |
| Opções de máquina virtual |Utilize as predefinições |Utilize as predefinições |Utilize as predefinições |

<br/>

> [!NOTE]
> A configuração anterior sugere máquinas de virtuais do escalão STANDARD, porque as máquinas de escalão básico não suportam pontos finais com balanceamento de carga. Terá de pontos finais com balanceamento de carga mais tarde para criar um serviço de escuta do grupo de disponibilidade. Além disso, os tamanhos de máquina são sugeridos aqui destinam-se para grupos de disponibilidade de teste em Azure Virtual Machines. Para obter o melhor desempenho em cargas de trabalho de produção, consulte as recomendações para tamanhos de máquina do SQL Server e a configuração no [práticas recomendadas do SQL Server em Azure Virtual Machines](../sql/virtual-machines-windows-sql-performance.md).
> 
> 

Depois das três máquinas virtuais são totalmente aprovisionadas, precisa de associá-los para o **corp.contoso.com** domínio e conceder direitos administrativos de CORP\Install para as máquinas. Para tal, utilize os seguintes passos para cada um dos três máquinas virtuais.

1. Em primeiro lugar, altere o endereço do servidor DNS preferencial. Transfira o ficheiro RDP de cada máquina virtual para o diretório no local, selecionando a máquina virtual na lista e clicando a **Connect** botão. Para selecionar uma máquina virtual, clique em qualquer local, mas a primeira célula na linha, conforme mostrado na captura de ecrã seguinte.
   
    ![Transferir o ficheiro RDP](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC664953.jpg)
2. Abra o ficheiro RDP que transferiu e iniciar sessão para a máquina virtual utilizando a sua conta de administrador configurado (**BUILTIN\AzureAdmin**) e palavra-passe (**Contoso! 000**).
3. Depois de iniciar sessão, deverá ver o **Gestor de servidor** dashboard. Clique em **servidor Local** no painel esquerdo.
4. Clique em de **endereço IPv4 atribuído pelo DHCP, IPv6 ativado** ligação.
5. No **ligações de rede** diálogo caixa, clique no ícone de rede.
   
    ![Alterar o servidor DNS de máquina virtual preferido](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784629.png)
6. Na barra de comandos, clique em **alterar as definições desta ligação**. (Dependendo do tamanho da janela do, poderá ter de clicar na seta à direita dupla para ver este comando).
7. Selecione **protocolo IP versão 4 (TCP/IPv4)**e, em seguida, clique em **propriedades**.
8. Selecione **utilize os seguintes endereços de servidor DNS** e, em seguida, especifique **10.10.2.4** no **servidor DNS preferencial**.
9. O **10.10.2.4** endereço é o endereço que está atribuído a uma máquina virtual na sub-rede 10.10.2.0/24 numa rede virtual do Azure. Que a máquina virtual é **ContosoDC**. Para verificar **ContosoDC**do endereço IP, utilize **nslookup contosodc** na janela da linha de comandos, conforme mostrado na captura de ecrã seguinte.
   
    ![Utilize NSLOOKUP para localizar o endereço IP do controlador de domínio](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC664954.jpg)
10. Clique em **OK** > **fechar** para consolidar as alterações. Agora pode associar a máquina virtual para **corp.contoso.com**.
11. Volta a **servidor Local** janela, clique em de **grupo de trabalho** ligação.
12. No **nome do computador** secção, clique em **alteração**.
13. Selecione o **domínio** caixa de verificação, escreva **corp.contoso.com** na caixa de texto e, em seguida, clique em **OK**.
14. No **segurança do Windows** diálogo caixa, especifique as credenciais para a conta de administrador de domínio predefinida (**CORP\AzureAdmin**) e a palavra-passe (**Contoso! 000**).
15. Quando vir a mensagem "Boas-vindas ao domínio corp.contoso.com", clique em **OK**.
16. Clique em **fechar** > **reiniciar agora** na caixa de diálogo.

### <a name="add-the-corpinstall-user-as-an-administrator-on-each-virtual-machine"></a>Adicionar o utilizador Corp\Install como um administrador em cada máquina virtual
1. Aguarde até os reinícios da máquina virtual e, em seguida, abra o RDP do ficheiro novo para iniciar sessão para a máquina virtual utilizando o **BUILTIN\AzureAdmin** conta.
2. No **Gestor de servidor** clique **ferramentas** > **gestão de computadores**.
   
    ![Gestão de computadores](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784630.png)
3. No **gestão de computadores** diálogo caixa, expanda **utilizadores e grupos locais**e, em seguida, clique em **grupos**.
4. Faça duplo clique o **administradores** grupo.
5. No **administradores propriedades** caixa de diálogo, clique em de **adicionar** botão.
6. Introduza o utilizador **CORP\Install**e, em seguida, clique em **OK**. Quando lhe forem pedidas as credenciais, utilize o **AzureAdmin** conta com o **Contoso! 000** palavra-passe.
7. Clique em **OK** para fechar o **administrador propriedades** caixa de diálogo.

### <a name="add-the-failover-clustering-feature-to-each-virtual-machine"></a>Adicione a funcionalidade Clustering de ativação pós-falha para cada máquina virtual
1. No **Gestor de servidor** dashboard, clique em **para adicionar funções e funcionalidades**.
2. No **Assistente Adicionar funções e funcionalidades**, clique em **seguinte** até chegar à **funcionalidades** página.
3. Selecione **Clustering de ativação pós-falha**. Quando lhe for pedido, adicione outras funcionalidades dependentes.
   
    ![Adicione a funcionalidade de Clustering de ativação pós-falha à máquina virtual](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784631.png)
4. Clique em **seguinte**e, em seguida, clique em **instalar** no **confirmação** página.
5. Quando o **Clustering de ativação pós-falha** concluída a instalação de funcionalidades, clique em **fechar**.
6. Terminar a máquina virtual.
7. Repita os passos nesta secção para todos os três servidores: **ContosoWSFCNode**, **ContosoSQL1**, e **ContosoSQL2**.

O SQL Server máquinas virtuais são agora aprovisionadas e em execução, mas cada um tem as opções predefinidas para o SQL Server.

## <a name="create-the-failover-cluster"></a>Criar o cluster de ativação pós-falha
Nesta secção, vai criar o cluster de ativação pós-falha que irá alojar o grupo de disponibilidade que irá criar mais tarde. Por agora, deve ter efetuado o seguinte procedimento para cada um dos três máquinas virtuais que irá utilizar no cluster de ativação pós-falha:

* Totalmente aprovisionado a máquina virtual no Azure
* Associado a máquina virtual ao domínio
* Adicionar **CORP\Install** ao grupo de administradores local
* Adicionar a funcionalidade de clustering de ativação pós-falha

Todas estas são pré-requisitos em cada máquina virtual antes de a poder associar ao cluster de ativação pós-falha.

Além disso, tenha em atenção que a Azure virtual network não comportar-se da mesma forma como sendo uma rede no local. Terá de criar o cluster pela seguinte ordem:

1. Criar um cluster de nó único num nó (**ContosoSQL1**).
2. Modificar o endereço IP de cluster para um endereço IP não utilizado (**10.10.2.101**).
3. Coloque online o nome do cluster.
4. Adicionar outros nós (**ContosoSQL2** e **ContosoWSFCNode**).

Utilize os seguintes passos para concluir as tarefas que configurar totalmente o cluster.

1. Abra o ficheiro RDP para **ContosoSQL1**e inicie sessão utilizando a conta de domínio **CORP\Install**.
2. No **Gestor de servidor** dashboard, clique em **ferramentas** > **Gestor de clusters de ativação pós-falha**.
3. No painel esquerdo, faça duplo clique **Gestor de clusters de ativação pós-falha**e, em seguida, clique em **criar um Cluster**, conforme mostrado na captura de ecrã seguinte.
   
    ![Criar cluster](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784632.png)
4. O Assistente para criar clusters, crie um cluster de um nó avance através das páginas e utilizando as definições na tabela seguinte:
   
   | Página | Definições |
   | --- | --- |
   | Antes de começar |Utilize as predefinições |
   | Selecionar servidores |Tipo **ContosoSQL1** no **introduza o nome de servidor** e clique em **adicionar** |
   | Aviso de validação |Selecione **um número não precisar do suporte da Microsoft para este cluster e, por conseguinte, não pretender executar testes de validação. Quando posso clicar em seguinte, continue a criar o cluster**. |
   | Ponto de acesso para administrar o Cluster |Tipo **Cluster1** no **nome do Cluster** |
   | Confirmação |Utilize as predefinições, exceto se estiver a utilizar os espaços de armazenamento. Consulte o aviso após esta tabela. |
   
   > [!WARNING]
   > Se estiver a utilizar [espaços de armazenamento](https://technet.microsoft.com/library/hh831739), vários discos que agrupa em agrupamentos de armazenamento, tem de limpar o **adicionar todo o armazenamento elegível ao cluster** caixa de verificação a **confirmação** página. Se não desmarque esta opção, os discos virtuais irão ser desligados durante o processo de clustering. Como resultado, também não serão apresentados no Gestor de discos ou Explorer até que os espaços de armazenamento são removidos do cluster e novamente ligados com o PowerShell.
   > 
   > 
5. No painel esquerdo, expanda **Gestor de clusters de ativação pós-falha**e, em seguida, clique em **Cluster1.corp.contoso.com**.
6. No painel central, desloque para baixo até o **recursos principais do Cluster** secção e expanda o **Name: Clutser1** detalhes. Deverá ver ambos o **nome** e o **endereço IP** recursos no **falha** estado. O recurso de endereço IP não pode ser colocado online porque o cluster é atribuído o mesmo endereço IP que o computador, que é um endereço duplicado.
7. Clique com botão direito a **endereço IP** recursos e, em seguida, clique em **propriedades**.
   
    ![Propriedades do cluster](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784633.png)
8. Selecione **endereço IP estático**, especifique **10.10.2.101** no **endereço** caixa de texto e, em seguida, clique em **OK**.
9. No **recursos principais do Cluster** secção, faça duplo clique **Name: Cluster1**e, em seguida, clique em **colocar Online**. Aguarde até que ambos os recursos estão online. Quando o recurso de nome de cluster estiver online, o servidor DC é atualizado com uma nova conta de computador do Active Directory. Esta conta do Active Directory será utilizada para executar o serviço de cluster do grupo de disponibilidade mais tarde.
10. Adicione os nós restantes ao cluster. Na árvore de browser, clique com botão direito **Cluster1.corp.contoso.com**e, em seguida, clique em **adicionar nó**, conforme mostrado na captura de ecrã seguinte.
    
     ![Adicionar nó ao cluster](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784634.png)
11. No **Assistente de adicionar nó**, clique em **seguinte** no **selecionar servidores** página, adicione **ContosoSQL2** e **ContosoWSFCNode** à lista introduzindo o nome do servidor no **introduza o nome de servidor** e, em seguida, clicando em **adicionar**. Quando tiver terminado, clique em **seguinte**.
12. No **aviso de validação** página, clique em **não**, embora um cenário de produção, deve executar os testes de validação. Clique depois em **Seguinte**.
13. No **confirmação** página, clique em **seguinte** para adicionar nós.
    
    > [!WARNING]
    > Se estiver a utilizar [espaços de armazenamento](https://technet.microsoft.com/library/hh831739), vários discos que agrupa em agrupamentos de armazenamento, tem de limpar o **adicionar todo o armazenamento elegível ao cluster** caixa de verificação. Se não desmarque esta opção, os discos virtuais irão ser desligados durante o processo de clustering. Como resultado, estes também não aparecerá no Gestor de discos ou Explorer até que os espaços de armazenamento são removidos do cluster e novamente ligado com o PowerShell.
    > 
    > 
14. Depois dos nós são adicionados ao cluster, clique em **concluir**. Gestor de clusters de ativação pós-falha deve agora mostrar que o cluster tem três nós e na lista de **nós** contentor.
15. Terminar a sessão de ambiente de trabalho remota.

## <a name="prepare-the-sql-server-instances-for-availability-groups"></a>Preparar as instâncias do SQL Server para grupos de disponibilidade
Nesta secção, executará o seguinte procedimento em ambos **ContosoSQL1** e **contosoSQL2**:

* Adicionar um início de sessão para **NT AUTHORITY\System** com as permissões necessárias definidas para a instância do SQL Server predefinida.
* Adicionar **CORP\Install** como uma função sysadmin para a instância do SQL Server predefinida.
* Abra a firewall para acesso remoto do SQL Server.
* Ative a funcionalidade de grupos sempre na disponibilidade.
* Alterar a conta de serviço do SQL Server para **CORP\SQLSvc1** e **CORP\SQLSvc2**, respetivamente.

Estas ações podem ser efetuadas por qualquer ordem. Contudo, os passos seguintes ajudá-los por ordem. Siga os passos para ambos **ContosoSQL1** e **ContosoSQL2**:

1. Se não terminou a sessão de ambiente de trabalho remota para a máquina virtual, faça-o agora.
2. Abrir os ficheiros RDP para **ContosoSQL1** e **ContosoSQL2**e inicie sessão como **BUILTIN\AzureAdmin**.
3. Adicionar **NT AUTHORITY\System** para inícios de sessão do SQL Server com as permissões necessárias. Abra o **SQL Server Management Studio**.
4. Clique em **Connect** para ligar a instância predefinida do SQL Server.
5. No **Object Explorer**, expanda **segurança**e, em seguida, expanda **inícios de sessão**.
6. Clique com botão direito do **NT AUTHORITY\System** início de sessão e, em seguida, clique em **propriedades**.
7. No **Securables** página, para o servidor local, selecione **conceder** para as seguintes permissões e, em seguida, clique em **OK**.
   
   * Alterar qualquer grupo de disponibilidade
   * Estabelecer a ligação SQL
   * Ver estado do servidor
8. Adicionar **CORP\Install** como um **sysadmin** função para a instância do SQL Server predefinida. No **Object Explorer**, faça duplo clique **inícios de sessão**e, em seguida, clique em **novo início de sessão**.
9. Tipo **CORP\Install** no **nome de início de sessão**.
10. No **funções de servidor** página, selecione **sysadmin**e, em seguida, clique em **OK**. Depois de criar o início de sessão, pode vê-lo, expandindo **inícios de sessão** no **Object Explorer**.
11. Para criar uma regra de firewall para SQL Server, o **iniciar** ecrã aberto **Firewall do Windows com segurança avançada**.
12. No painel esquerdo, selecione **regras de entrada**. No painel direito, clique em **nova regra**.
13. No **tipo de regra** página, clique em **programa** > **seguinte**.
14. No **programa** página, selecione **este caminho de programa**, tipo **%ProgramFiles%\Microsoft Server\MSSQL12 do SQL Server. MSSQLSERVER\MSSQL\Binn\sqlservr.exe** na caixa de texto e, em seguida, clique em **seguinte**. Se estiver a seguir estas instruções, mas utilizar o SQL Server 2012, o diretório de SQL Server é **MSSQL11. MSSQLSERVER**.
15. No **ação** página, mantenha **permitir a ligação** selecionada e, em seguida, clique em **seguinte**.
16. No **perfil** página, aceite as predefinições e, em seguida, clique em **seguinte**.
17. No **nome** página, especifique um nome de regra, tal como **do SQL Server (regra de programa)**, no **nome** texto caixa, em seguida, clique em **concluir**.
18. Para ativar o **em grupos de disponibilidade Always** funcionalidade, no **iniciar** ecrã aberto **Gestor de configuração do SQL Server**.
19. Na árvore de browser, clique em **serviços do SQL Server**, clique com botão direito do **SQL Server (MSSQLSERVER)** de serviço e, em seguida, clique em **propriedades**.
20. Clique em de **sempre na disponibilidade elevada** separador, selecione **ativar grupos de disponibilidade Always**, como apresentado na seguinte captura de ecrã e, em seguida, clique em **aplicar**. Clique em **OK** na caixa de diálogo e efetue não feche o **propriedades** ainda a caixa de diálogo. Irá reiniciar o serviço SQL Server depois de alterar a conta de serviço.
    
     ![Ativar sempre em grupos de disponibilidade](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665520.gif)
21. Para alterar a conta de serviço do SQL Server, clique o **iniciar sessão** separador, escreva **CORP\SQLSvc1** (para **ContosoSQL1**) ou **CORP\SQLSvc2** (para **ContosoSQL2**) no **nome da conta**, preencha e confirme a palavra-passe e, em seguida, clique em **OK**.
22. Na caixa de diálogo aberta, clique em **Sim** para reiniciar o serviço do SQL Server. Depois de reinicia o serviço SQL Server, as alterações que é efetuada no **propriedades** caixa de diálogo são eficazes.
23. Terminar as máquinas virtuais.

## <a name="create-the-availability-group"></a>Criar o grupo de disponibilidade
Agora está pronto para configurar um grupo de disponibilidade. Segue-se uma descrição do que irá fazer:

* Criar uma nova base de dados (**MyDB1**) no **ContosoSQL1**.
* Coloque uma cópia de segurança completa e uma cópia de segurança do registo de transações da base de dados.
* Restaurar o completo e as cópias de segurança de registo **ContosoSQL2** com o **NORECOVERY** opção.
* Criar o grupo de disponibilidade (**AG1**) com consolidação síncrona, ativação pós-falha automática e as réplicas secundárias legíveis.

### <a name="create-the-mydb1-database-on-contososql1"></a>Criar a base de dados MyDB1 ContosoSQL1
1. Se não já terminou as sessões de ambiente de trabalho remotas para **ContosoSQL1** e **ContosoSQL2**, fazê-lo agora.
2. Abra o ficheiro RDP para **ContosoSQL1**e inicie sessão como **CORP\Install**.
3. No **Explorador de ficheiros**, em **c:\\**, crie um diretório com o nome **cópia de segurança**. Irá utilizar este diretório para fazer cópias de segurança e restaurar a base de dados.
4. O novo diretório com o botão direito, aponte para **partilhar com**e, em seguida, clique em **pessoas específicas**, conforme mostrado na captura de ecrã seguinte.
   
    ![Crie uma pasta de cópia de segurança](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665521.gif)
5. Adicionar **CORP\SQLSvc1**e, em seguida, atribua-lhe o **leitura/escrita** permissão. Adicionar **CORP\SQLSvc2**e, em seguida, atribua-lhe o **leitura** permissão, como mostrado na seguinte captura de ecrã e, em seguida, clique em **partilha**. Depois do processo de partilha de ficheiros estiver concluído, clique em **feito**.
   
    ![Conceder permissões para a pasta de cópia de segurança](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665522.gif)
6. Para criar a base de dados a partir de **iniciar** menu, abra **SQL Server Management Studio**e, em seguida, clique em **Connect** para ligar a instância predefinida do SQL Server.
7. No **Object Explorer**, faça duplo clique **bases de dados**e, em seguida, clique em **nova base de dados**.
8. No **nome de base de dados**, tipo **MyDB1**e, em seguida, clique em **OK**.

### <a name="make-a-full-backup-of-mydb1-and-restore-it-on-contososql2"></a>Assuma um completo cópia de segurança de MyDB1 e restaurá-lo no ContosoSQL2
1. Para fazer uma cópia de segurança completa da base de dados, no **Object Explorer**, expanda **bases de dados**, faça duplo clique **MyDB1**, aponte para **tarefas**e, em seguida, clique em **cópia de segurança**.
2. No **origem** secção, mantenha **tipo de cópia de segurança** definido como **completa**. No **destino** secção, clique em **remover** para remover o caminho de ficheiro predefinido para o ficheiro de cópia de segurança.
3. No **destino** secção, clique em **adicionar**.
4. No **nome de ficheiro** caixa de texto, escreva  **\\ContosoSQL1\backup\MyDB1.bak**, clique em **OK**e, em seguida, clique em **OK** novamente para efetuar cópias de segurança da base de dados. Quando concluída a operação de cópia de segurança, clique em **OK** novamente para fechar a caixa de diálogo.
5. Para fazer uma cópia de segurança do registo de transações da base de dados, no **Object Explorer**, expanda **bases de dados**, faça duplo clique **MyDB1**, aponte para **tarefas**e, em seguida, clique em **cópia de segurança**.
6. No **tipo de cópia de segurança**, selecione **registo de transações**. Manter o **destino** ficheiro conjunto de caminho para um que especificou anteriormente e, em seguida, clique em **OK**. Depois de concluída a operação de cópia de segurança, clique em **OK** novamente.
7. Para restaurar o completo e transações no registo cópias de segurança no **ContosoSQL2**, abra o ficheiro RDP para **ContosoSQL2**e inicie sessão como **CORP\Install**. Deixe a sessão de ambiente de trabalho remota para **ContosoSQL1** abrir.
8. Do **iniciar** menu, abra **SQL Server Management Studio**e, em seguida, clique em **Connect** para ligar a instância predefinida do SQL Server.
9. No **Object Explorer**, faça duplo clique **bases de dados**e, em seguida, clique em **restaurar base de dados**.
10. No **origem** secção, selecione **dispositivo**e clique no botão de reticências **...** Adicionar...
11. No **selecionar dispositivos de cópia de segurança**, clique em **adicionar**.
12. No **localização do ficheiro de cópia de segurança**, tipo  **\\ContosoSQL1\backup**, clique em **atualizar**, selecione **MyDB1.bak**, clique em **OK**e, em seguida, clique em **OK** novamente. Deverá ver a cópia de segurança completa e a cópia de segurança do registo no **define a cópia de segurança para restaurar** painel.
13. Vá para o **opções** página, selecione **RESTORE WITH NORECOVERY** no **estado de recuperação**e, em seguida, clique em **OK** para restaurar a base de dados. Depois de concluída a operação de restauro, clique em **OK**.

### <a name="create-the-availability-group"></a>Criar o grupo de disponibilidade
1. Volte à sessão de ambiente de trabalho remota para **ContosoSQL1**. No **Object Explorer** no SQL Server Management Studio, clique com botão direito **sempre na disponibilidade elevada**e, em seguida, clique em **Assistente de novo grupo de disponibilidade**, conforme mostrado na captura de ecrã seguinte.
   
    ![Iniciar o Assistente de novo grupo de disponibilidade](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665523.gif)
2. No **introdução** página, clique em **seguinte**. No **Especificar nome do grupo de disponibilidade** , escreva **AG1** no **nome do grupo de disponibilidade**, em seguida, clique em **seguinte** novamente.
   
    ![Assistente de novo grupo de disponibilidade, especificar nome do grupo de disponibilidade](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665524.gif)
3. No **selecionar bases de dados** página, selecione **MyDB1**e, em seguida, clique em **seguinte**. A base de dados cumpre os pré-requisitos para um grupo de disponibilidade, porque ter demorado, pelo menos, uma cópia de segurança completa na réplica primária pretendida.
   
    ![Assistente de novo grupo de Availabilty, selecionadas bases de dados](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665525.gif)
4. no **especificar réplicas** página, clique em **Adicionar réplica**.
   
    ![Assistente de novo grupo de Availabilty, especificar réplicas](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665526.gif)
5. No **ligar ao servidor** caixa de diálogo, escreva **ContosoSQL2** no **nome do servidor**e, em seguida, clique em **Connect**.
   
    ![Assistente de novo grupo de Availabilty, ligar ao servidor](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665527.gif)
6. Reverter o **especificar réplicas** página, deverá ver **ContosoSQL2** listado no **réplicas disponíveis**. Configure as réplicas conforme mostrado na captura de ecrã seguinte. Quando tiver terminado, clique em **seguinte**.
   
    ![Assistente de novo grupo de Availabilty, especifique as réplicas (concluir)](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665528.gif)
7. No **selecionar sincronização de dados inicial** página, selecione **apenas junção**e, em seguida, clique em **seguinte**. Já realizou a sincronização de dados manualmente quando efetuadas as cópias de segurança completa e a transação no **ContosoSQL1** e restauradas-los **ContosoSQL2**. Pode optar por não executar a cópia de segurança e restaurar as operações na base de dados e em vez disso, selecione **completa** para permitir que o Assistente novo do grupo de disponibilidade efetuar a sincronização de dados para si. No entanto, não recomendamos esta opção para bases de dados muito grandes, que se encontram no algumas empresas.
   
    ![Assistente de novo grupo de Availabilty, sincronização de dados inicial selecione](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665529.gif)
8. No **validação** página, clique em **seguinte**. Esta página deve ter um aspeto semelhante à seguinte captura de ecrã. Não há um aviso para a configuração do serviço de escuta porque não tiver configurado um serviço de escuta do grupo de disponibilidade. Pode ignorar este aviso, porque este tutorial não configurar um serviço de escuta. Para configurar o serviço de escuta depois de concluir este tutorial, consulte [configurar um serviço de escuta do ILB para grupos de disponibilidade Always no Azure](../classic/ps-sql-int-listener.md).
   
    ![Availabilty Assistente novo grupo de validação](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665530.gif)
9. No **resumo** página, clique em **concluir**e, em seguida, aguarde enquanto o assistente configura o novo grupo de disponibilidade. No **progresso** página, pode clicar **mais detalhes** para ver o progresso de detalhado. Depois de concluído o assistente, Inspecione o **resultados** página para verificar que o grupo de disponibilidade é criado com êxito, conforme mostrado na seguinte captura de ecrã e, em seguida, clique em **fechar** para sair do assistente.
   
    ![Assistente de novo grupo de Availabilty, resultados](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665531.gif)
10. No **Object Explorer**, expanda **sempre na disponibilidade elevada**e, em seguida, expanda **grupos de disponibilidade**. Deverá ver o novo grupo de disponibilidade neste contentor. Clique com botão direito **AG1 (principal)**e, em seguida, clique em **Mostrar Dashboard**.
    
     ![Grupo de disponibilidade de Mostrar Dashboard](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665532.gif)
11. O **sempre Dashboard** deve ter um aspeto semelhante à captura de ecrã seguinte. Pode ver as réplicas, o modo de ativação pós-falha de cada réplica e o estado da sincronização.
    
     ![Dashboard de grupo de disponibilidade](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665533.gif)
12. Volte ao **Gestor de servidor**, clique em **ferramentas**e, em seguida, abra **Gestor de clusters de ativação pós-falha**.
13. Expanda **Cluster1.corp.contoso.com**e, em seguida, expanda **serviços e aplicações**. Selecione **funções** e tenha em atenção que o **AG1** função do grupo de disponibilidade foi criada. Tenha em atenção que AG1 não tem um endereço IP através da base de dados, os clientes podem ligar ao grupo de disponibilidade, porque não foi possível configurar um serviço de escuta. Pode ligar diretamente ao nó principal para operações de leitura / escrita e o nó secundário para consultas só de leitura.
    
     ![AG no Gestor de clusters de ativação pós-falha](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665534.gif)

> [!WARNING]
> Não tente efetuar a ativação pós-falha do grupo de disponibilidade do Gestor de clusters de ativação pós-falha. Todas as operações de ativação pós-falha devem ser efetuadas a partir do **sempre Dashboard** no SQL Server Management Studio. Para obter mais informações, consulte [restrições no utilizando a ativação pós-falha Gestor de clusters com grupos de disponibilidade](https://msdn.microsoft.com/library/ff929171.aspx).
> 
> 

## <a name="next-steps"></a>Passos seguintes
Agora com êxito implementar SQL Server Always On através da criação de um grupo de disponibilidade no Azure. Para configurar um serviço de escuta para este grupo de disponibilidade, consulte [configurar um serviço de escuta do ILB para grupos de disponibilidade Always On no Azure](../classic/ps-sql-int-listener.md).

Para outras informações sobre como utilizar o SQL Server no Azure, consulte [do SQL Server em Azure Virtual Machines](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

