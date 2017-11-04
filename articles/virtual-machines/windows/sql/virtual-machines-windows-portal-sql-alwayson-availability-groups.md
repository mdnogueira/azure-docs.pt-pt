---
title: Configurar a elevada disponibilidade para as VMs do Azure Resource Manager | Microsoft Docs
description: "Este tutorial mostra como criar um grupo de disponibilidade Always On com máquinas virtuais do Azure no modo Azure Resource Manager."
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: jhubbard
editor: 
tags: azure-resource-manager
ms.assetid: 64e85527-d5c8-40d9-bbe2-13045d25fc68
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/17/2017
ms.author: mikeray
ms.openlocfilehash: d430febee23081b26eee0a68d4beb43228549f52
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-always-on-availability-groups-in-azure-virtual-machines-automatically-resource-manager"></a>Configurar grupos de disponibilidade Always On em Azure Virtual Machines automaticamente: o Gestor de recursos

Este tutorial mostra como criar um grupo de disponibilidade do SQL Server que utiliza máquinas de virtuais do Azure Resource Manager. O tutorial utiliza os painéis do Azure para configurar um modelo. Pode rever as definições predefinidas, escreva as definições necessárias e atualizar os painéis no portal como percorrer neste tutorial.

O tutorial concluído cria um grupo de disponibilidade do SQL Server em Virtual Machines do Azure que incluem os seguintes elementos:

* Uma rede virtual que tem várias sub-redes, incluindo um front-end e uma sub-rede de back-end
* Dois controladores de domínio que tenha um domínio do Active Directory
* Duas máquinas virtuais que executam o SQL Server e que estão implementadas para a sub-rede de back-end e associadas ao domínio do Active Directory
* Um cluster de ativação pós-falha de três nós com o modelo de quórum maioria de nós
* Um grupo de disponibilidade que tem duas réplicas com consolidação síncrona de uma base de dados de disponibilidade

A ilustração seguinte representa a solução completa.

![Arquitetura de laboratório de teste para grupos de disponibilidade no Azure](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/0-EndstateSample.png)

Todos os recursos nesta solução pertencerem a um grupo de recursos única.

Antes de começar este tutorial, confirme o seguinte:

* Já tem uma conta do Azure. Se não tiver uma, [inscrever-se para uma conta de avaliação](http://azure.microsoft.com/pricing/free-trial/).
* Já sabe como utilizar a GUI para Aprovisionar uma máquina virtual do SQL Server da galeria da máquina virtual. Para obter mais informações, consulte [aprovisionar uma máquina virtual do SQL Server no Azure](virtual-machines-windows-portal-sql-server-provision.md).
* Já existe uma compreensão sólida dos grupos de disponibilidade. Para obter mais informações, consulte [Always On nos grupos de disponibilidade (SQL Server)](http://msdn.microsoft.com/library/hh510230.aspx).

> [!NOTE]
> Se estiver interessado em utilizar grupos de disponibilidade com o SharePoint, consulte também [configurar SQL Server 2012 Always On grupos de disponibilidade para SharePoint 2013](http://technet.microsoft.com/library/jj715261.aspx).
>
>

Neste tutorial, utilize o portal do Azure para:

* Escolha o modelo Always On no portal.
* Reveja as definições do modelo e atualizar algumas definições de configuração para o seu ambiente.
* Monitor do Azure à medida que cria o todo o ambiente.
* Ligar a um controlador de domínio e, em seguida, para um servidor que executa o SQL Server.

[!INCLUDE [availability-group-template](../../../../includes/virtual-machines-windows-portal-sql-alwayson-ag-template.md)]

## <a name="provision-the-cluster-from-the-gallery"></a>Aprovisionar o cluster a partir da Galeria
O Azure oferece uma imagem de galeria para a solução completa. Para localizar o modelo:

1. Inicie sessão no portal do Azure utilizando a sua conta.
2. No portal do Azure, clique em **+ novo** para abrir o **novo** painel.
3. No **novo** painel, procure **AlwaysOn**.
   ![Localizar o modelo de AlwaysOn](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/16-findalwayson.png)
4. Nos resultados da pesquisa, localize **Cluster do SQL Server AlwaysOn**.
   ![Modelo de AlwaysOn](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/17-alwaysontemplate.png)
5. No **selecionar um modelo de implementação**, escolha **Resource Manager**.

### <a name="basics"></a>Noções básicas
Clique em **Noções básicas** e configure as seguintes definições:

* **Nome de utilizador administrador** é uma conta de utilizador que tenha permissões de administrador de domínio e é um membro da função de servidor fixa sysadmin do SQL Server em ambas as instâncias do SQL Server. Para este tutorial, utilize **DomainAdmin**.
* **Palavra-passe** é a palavra-passe para a conta de administrador de domínio. Utilize uma palavra-passe complexa. Confirme a palavra-passe.
* **Subscrição** é a subscrição que o Azure cobra executar implementados todos os recursos para o grupo de disponibilidade. Se a sua conta tiver várias subscrições, pode especificar uma subscrição diferente.
* **Grupo de recursos** é o nome para o grupo ao qual pertencem todos os recursos do Azure que são criados por este modelo. Para este tutorial, utilize **SQL-HA-RG**. Para obter mais informações, veja [Descrição geral do Azure Resource Manager](../../../azure-resource-manager/resource-group-overview.md#resource-groups).
* **Localização** é a região do Azure onde o tutorial cria os recursos. Escolha uma região do Azure.

Captura de ecrã seguinte é um concluída **Noções básicas** painel:

![Noções básicas](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/1-basics.png)

Clique em **OK**.

### <a name="domain-and-network-settings"></a>Definições de domínio e de rede
Este modelo de galeria do Azure cria um domínio e os controladores de domínio. Também cria uma rede e duas sub-redes. O modelo não é possível criar servidores numa rede virtual ou domínio existente. O próximo passo configura as definições de domínio e de rede.

No **definições de domínio e de rede** painel, reveja os valores para o domínio predefinidos e as definições de rede:

* **Nome de domínio de raiz de floresta** é o nome de domínio para o domínio do Active Directory que aloja o cluster. Para o tutorial, utilize **contoso.com**.
* **Nome de rede virtual** é o nome de rede para a rede virtual do Azure. Para o tutorial, utilize **autohaVNET**.
* **Nome de sub-rede de controlador de domínio** é o nome de uma parte da rede virtual que aloja o controlador de domínio. Utilize **sub-rede 1**. Esta sub-rede utiliza o prefixo de endereço **10.0.0.0/24**.
* **Nome da sub-rede do SQL Server** é o nome de uma parte da rede virtual que aloja os servidores que execução do SQL Server e o ficheiro de testemunho de partilha. Utilize **sub-rede 2**. Esta sub-rede utiliza o prefixo de endereço **10.0.1.0/26**.

Para saber mais sobre as redes virtuais no Azure, consulte [descrição geral de rede Virtual](../../../virtual-network/virtual-networks-overview.md).  

O **definições de domínio e de rede** deverá ser semelhante à captura de ecrã seguinte:

![Definições de domínio e de rede](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/2-domain.png)

Se necessário, pode alterar estes valores. Para este tutorial, utilize os valores predefinidos.

Reveja as definições e, em seguida, clique em **OK**.

### <a name="availability-group-settings"></a>Definições do grupo de disponibilidade
No **das definições do grupo de disponibilidade**, reveja os valores predefinidos para o grupo de disponibilidade e o serviço de escuta.

* **Nome do grupo de disponibilidade** é o nome de recurso em cluster para o grupo de disponibilidade. Para este tutorial, utilize **Contoso ag**.
* **Nome de serviço de escuta do grupo de disponibilidade** é utilizado pelo cluster e o Balanceador de carga interno. Clientes que estabelecem ligação ao SQL Server podem utilizar este nome para ligar à réplica da base de dados adequada. Para este tutorial, utilize **serviço de escuta de Contoso**.
* **Porta de serviço de escuta do grupo de disponibilidade** Especifica a porta TCP do serviço de escuta do SQL Server. Para este tutorial utiliza a porta predefinida, **1433**.

Se necessário, pode alterar estes valores. Para este tutorial, utilize os valores predefinidos.  

![Definições do grupo de disponibilidade](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/3-availabilitygroup.png)

Clique em **OK**.

### <a name="virtual-machine-size-storage-settings"></a>Tamanho da máquina virtual, as definições de armazenamento
No **tamanho da VM, as definições de armazenamento**, escolha um tamanho de máquina virtual do SQL Server e reveja as outras definições.

* **Tamanho da máquina virtual do SQL Server** é o tamanho para máquinas virtuais que executam o SQL Server. Escolha um tamanho de máquina virtual adequado para a carga de trabalho. Se está a criar este ambiente para o tutorial, utilize **DS2**. Para cargas de trabalho de produção, escolha um tamanho de máquina virtual que pode suportar a carga de trabalho. Muitas cargas de trabalho de produção requerem **DS4** ou superior. O modelo cria duas máquinas virtuais deste tamanho e instala o SQL Server em cada um. Para obter mais informações, consulte [tamanhos das virtual machines](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!NOTE]
> Azure instala a edição Enterprise do SQL Server. O custo depende da edição e o tamanho de máquina virtual. Para obter informações detalhadas sobre os custos atuais, consulte [máquinas de virtuais preços](http://azure.microsoft.com/pricing/details/virtual-machines/#Sql).
>
>

* **Tamanho de máquina virtual do controlador de domínio** é o tamanho de máquina virtual para os controladores de domínio. Para este tutorial utilização **D2**.
* **Tamanho da máquina virtual o testemunho da partilha de ficheiros** é o tamanho de máquina virtual para o testemunho de partilha de ficheiros. Para este tutorial, utilize **A1**.
* **Conta de armazenamento de SQL** é o nome da conta de armazenamento que contém os dados do SQL Server e os discos de sistema operativo. Para este tutorial, utilize **alwaysonsql01**.
* **Conta de armazenamento de DC** é o nome da conta de armazenamento para os controladores de domínio. Para este tutorial, utilize **alwaysondc01**.
* **Tamanho do disco de dados do SQL Server** TB é o tamanho do disco de dados do SQL Server no TB. Especifique um número entre 1 e 4. Para este tutorial, utilize **1**.
* **Otimização de armazenamento** define as definições de configuração de armazenamento específica para as máquinas de virtuais do SQL Server com base no tipo de carga de trabalho. Todas as máquinas virtuais do SQL Server neste cenário utilizar o armazenamento premium com a cache de anfitrião de disco do Azure definido como só de leitura. Além disso, pode otimizar as definições de SQL Server para a carga de trabalho escolhendo uma das três estas definições:

  * **Carga de trabalho geral** define sem definições de configuração específico.
  * **Processamento transacional** sinalizador 1117 e 1118 de rastreio de conjuntos.
  * **Armazenamento de dados** 1117 do sinalizador de rastreio de conjuntos e 610.

Para este tutorial, utilize **carga de trabalho geral**.

![Definições de armazenamento de tamanho VM](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/4-vm.png)

Reveja as definições e, em seguida, clique em **OK**.

#### <a name="a-note-about-storage"></a>Nota sobre o armazenamento
Otimizações adicionais dependem do tamanho dos discos de dados do SQL Server. Para cada terabyte do disco de dados, o Azure adiciona um armazenamento adicional de premium 1 TB. Quando um servidor necessita de 2 TB ou mais, o modelo cria um agrupamento de armazenamento em cada máquina virtual do SQL Server. Um agrupamento de armazenamento é um formulário de Virtualização de armazenamento onde vários discos estão configurados para fornecer capacidade, resiliência e desempenho superior.  O modelo, em seguida, cria um espaço de armazenamento no agrupamento de armazenamento e apresenta um disco de dados único para o sistema operativo. O modelo designa este disco como o disco de dados do SQL Server. O modelo tunes o agrupamento de armazenamento para o SQL Server utilizando as seguintes definições:

* Tamanho do stripe é a definição de intercalação para o disco virtual. Cargas de trabalho transacionais utilizam 64 KB. Cargas de trabalho de armazém de dados utilizam 256 KB.
* Resiliência é simple (sem resiliência).

> [!NOTE]
> Armazenamento premium do Azure é localmente redundante e mantém três cópias dos dados numa única região, pelo que não for necessária resiliência adicional no agrupamento de armazenamento.
>
>

* Contagem de colunas é igual ao número de discos no agrupamento de armazenamento.

Para obter informações adicionais sobre o espaço de armazenamento e agrupamentos de armazenamento, consulte:

* [Descrição geral dos espaços de armazenamento](http://technet.microsoft.com/library/hh831739.aspx)
* [Cópia de segurança do Windows Server e agrupamentos de armazenamento](http://technet.microsoft.com/library/dn390929.aspx)

Para obter mais informações sobre os procedimentos de configuração do SQL Server, consulte [melhores práticas de desempenho para o SQL Server em virtual machines do Azure](virtual-machines-windows-sql-performance.md).

### <a name="sql-server-settings"></a>Definições do SQL Server
No **definições do SQL Server**, reveja e modifique o prefixo de nome de máquina virtual do SQL Server, versão do SQL Server, conta de serviço do SQL Server e palavra-passe e a agenda de manutenção de aplicação de patches de automática SQL.

* **Prefixo de nome de servidor do SQL Server** é utilizado para criar um nome para cada máquina virtual do SQL Server. Para este tutorial, utilize **sqlserver**. O modelo de nomes de máquinas virtuais do SQL Server *sqlserver-0* e *sqlserver-1*.
* **Versão do SQL Server** é a versão do SQL Server. Para este tutorial utilização **SQL Server 2014**. Também pode optar por **SQL Server 2012** ou **SQL Server 2016**.
* **Nome de utilizador de conta de serviço do SQL Server** é o nome de conta de domínio para o serviço SQL Server. Para este tutorial, utilize **sqlservice**.
* **Palavra-passe** é a palavra-passe para a conta de serviço do SQL Server.  Utilize uma palavra-passe complexa. Confirme a palavra-passe.
* **Correção do SQL Server automática agenda de manutenção** identifica o dia da semana em que o Azure patches automaticamente os SQL Servers. Para este tutorial, escreva **Domingo**.
* **Hora de início de manutenção de correção do SQL Server automática** é a hora do dia para a região do Azure quando começa a aplicação de patches automática.

> [!NOTE]
> A janela de aplicação de patches para cada máquina virtual é escalonada por uma hora. Apenas uma máquina virtual é corrigida um momento para evitar a interrupção dos serviços.
>
>

![Definições do SQL Server](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/5-sql.png)

Reveja as definições e, em seguida, clique em **OK**.

### <a name="summary"></a>Resumo
Na página Resumo, Azure valida as definições. Também pode transferir o modelo. Reveja o resumo. Clique em **OK**.

### <a name="buy"></a>Comprar
Este painel final contém **termos de utilização**, e **política de privacidade**. Reveja estas informações. Quando estiver pronto para o Azure para começar a criar as máquinas virtuais e todas as outras necessários recursos para o grupo de disponibilidade, clique em **criar**.

O portal do Azure cria o grupo de recursos e todos os recursos.

## <a name="monitor-deployment"></a>Implementação de monitor
Monitorize o progresso da implementação do portal do Azure. Um ícone que representa a implementação é automaticamente afixado ao dashboard do portal do Azure.

![Dashboard do Azure](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/11-deploydashboard.png)

## <a name="connect-to-sql-server"></a>Ligar ao SQL Server
As novas instâncias do SQL Server estão em execução em máquinas virtuais que tem ligação à internet endereços IP. Pode de ambiente de trabalho remoto (RDP) diretamente a cada máquina virtual do SQL Server.

Para o RDP para um servidor de SQL, siga estes passos:

1. De que o dashboard do portal do Azure, certifique-se de que a implementação é concluída com êxito.
2. Clique em **recursos**.
3. No **recursos** painel, clique em **sqlserver-0**, que é o nome do computador de uma das máquinas virtuais que está a executar o SQL Server.
4. No painel **sqlserver-0**, clique em **Connect**. O browser pede-lhe se pretende abrir ou guardar o objeto de ligação remota. Clique em **abra**.
5. **Ligação de ambiente de trabalho remota** poderá avisá-lo que não seja possível identificar o publicador desta ligação remota. Clique em **Ligar**.
6. Segurança do Windows pede-lhe para introduzir as suas credenciais para ligar para o endereço IP do controlador de domínio primário. Clique em **utilizar outra conta**. Para **nome de utilizador**, tipo **contoso\DomainAdmin**. Se configurou esta conta quando o administrador do nome de utilizador no modelo. Utilize a palavra-passe complexa que escolheu quando configurou o modelo.
7. **Ambiente de trabalho remoto** poderá avisá-lo se o computador remoto não foi possível autenticar devido a problemas com o seu certificado de segurança. Mostra o nome do certificado de segurança. Se seguiu o tutorial, o nome é **sqlserver 0.contoso.com**. Clique em **Sim**.

Está agora ligado com RDP para a máquina virtual do SQL Server. Pode abrir o SQL Server Management Studio, ligar a instância predefinida do SQL Server e certifique-se de que o grupo de disponibilidade está configurado.
