<properties
    pageTitle="Descrição Geral do SQL Server em Máquinas Virtuais do Azure | Microsoft Azure"
    description="Saiba como executar edições completas do SQL Server em máquinas virtuais do Windows. Obtenha ligações diretas para todas as imagens de VMs do SQL e conteúdos relacionados."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="09/21/2016"
    ms.author="jroth"/>


# Descrição Geral do SQL Server em Máquinas Virtuais do Azure

Este tópico descreve as opções para executar o SQL Server em máquinas virtuais do Azure e inclui [ligações para imagens do portal](#option-1-deploy-a-sql-vm-per-minute-licensing) e uma descrição geral das [tarefas comuns](#manage-your-sql-vm).

>[AZURE.NOTE] Se já estiver familiarizado com o SQL Server e quiser ver apenas como implementar uma VM do SQL Server, veja [Aprovisionar uma máquina virtual do SQL Server no Portal do Azure](virtual-machines-windows-portal-sql-server-provision.md).

## Descrição geral
Poderá ser um administrador da base de dados que quer para mover as cargas de trabalho do SQL Server no local para a Nuvem. Ou poderá ser um programador a considerar as capacidades da base de dados relacional do SQL Server para a sua aplicação Azure. Qual é a vantagem de executar cargas de trabalho do SQL Server em Virtual Machines do Azure? A seguinte descrição geral do vídeo aborda as vantagens e fornece uma descrição técnica.

> [AZURE.VIDEO data-driven-sql-server-2016-azure-vm-is-the-best-platform-for-sql-server-2016]

## Avaliar as vantagens

Antes de começar, avalie primeiro o que ganha com a utilização do SQL Server em VMs do Azure.

Se estiver a mover outras cargas de trabalho para o Azure, tal como uma aplicação empresarial, faz sentido também mover as bases de dados do SQL Server dependentes para o Azure, para obter um desempenho melhor. Mas alojar o SQL Server em VMs do Azure fornece outras vantagens. Por exemplo, tem automaticamente acesso a vários centros de dados para uma recuperação após desastre e uma presença global. Para obter uma lista completa de cenários e vantagens, consulte a [Página de produto do SQL Server em VMs do Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/).

> [AZURE.NOTE] Quando estiver a avaliar o SQL Server em VMs do Azure, reveja também as outras opções de armazenamento e do SQL no Azure, como a [Base de dados SQL](../sql-database/sql-database-technical-overview.md), o [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) e o [SQL Server Stretch Database](../sql     -server-stretch-database/sql-server-stretch-database-overview.md). Para uma comparação mais detalhada, veja [Choose a cloud SQL Server option: Azure SQL (PaaS) Database or SQL Server on Azure VMs (IaaS) (Escolher uma opção do SQL Server de nuvem: Base de Dados SQL do Azure (PaaS) ou SQL Server em VMs do Azure (IaaS))](../sql-database/sql-database-paas-vs-sql-server-iaas.md).

Depois de optar por executar o SQL Server em VMs do Azure, uma das suas primeiras decisões é se pretende utilizar uma imagem da VM que inclui os custos de licenciamento do SQL Server. A outra opção consiste em trazer a sua própria licença (BYOL) e apenas pagar a própria VM. As duas secções seguintes descrevem estas opções.

## Criar uma VM do SQL nova
As secções seguintes fornecem ligações diretas para o portal do Azure para as imagens da galeria das máquinas virtuais do SQL Server. Consoante a imagem que selecionou, pode pagar os custos de licenciamento do SQL Server por minuto ou pode trazer a sua própria licença (BYOL).

Pode obter orientações passo a passo para este processo no tutorial [Aprovisionar uma máquina virtual do SQL Server no Portal do Azure](virtual-machines-windows-portal-sql-server-provision.md). Além disso, reveja as [Melhores práticas do desempenho para as VMs do SQL Server](virtual-machines-windows-sql-performance.md), que explica como selecionar o tamanho da máquina apropriada e outras funcionalidades disponíveis durante o aprovisionamento.

## Opção 1: Criar uma VM do SQL Server com licenciamento por minuto
A tabela seguinte fornece uma matriz de imagens do SQL Server disponíveis na galeria da máquina virtual. Clique em qualquer ligação para começar a criar uma nova VM do SQL Server com a versão especificada, edição e sistema operativo.

|Versão|Sistema Operativo|Edição|
|---|---|---|
|**SQL 2016**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMEnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMStandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMWebWindowsServer2012R2), [Dev](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMDeveloperWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMExpressWindowsServer2012R2)|
|**SQL 2014 SP1**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1ExpressWindowsServer2012R2)|
|**SQL 2014**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014WebWindowsServer2012R2)|
|**SQL 2012 SP3**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3ExpressWindowsServer2012R2)|
|**SQL 2012 SP2**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2WebWindowsServer2012R2)|
|**SQL 2012 SP2**|Windows Server 2012|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2EnterpriseWindowsServer2012), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2StandardWindowsServer2012), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2WebWindowsServer2012), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2ExpressWindowsServer2012)|
|**SQL 2008 R2 SP3**|Windows Server 2008 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3EnterpriseWindowsServer2008R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3StandardWindowsServer2008R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3WebWindowsServer2008R2)|
|**SQL 2008 R2 SP3**|Windows Server 2012|[Express](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3ExpressWindowsServer2012)|

## Opção 2: Criar uma VM do SQL Server com uma licença já existente
Também pode trazer a sua licença (BYOL). Neste cenário, apenas paga a VM sem quaisquer taxas adicionais para o licenciamento do SQL Server. Para utilizar a sua própria licença, utilize a matriz das versões do SQL Server, edições e sistemas operativos abaixo. No portal, é adicionado o prefixo **{BYOL}** aos nomes destas imagens.

|Versão|Sistema operativo|Edição|
|---|---|---|
|**SQL Server 2016**|Windows Server 2012 R2|[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016RTMStandardWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016RTMStandardWindowsServer2012R2)|
|**SQL Server 2014 SP1**|Windows Server 2012 R2|[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP1EnterpriseWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP1StandardWindowsServer2012R2)|
|**SQL Server 2012 SP2**|Windows Server 2012 R2|[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3EnterpriseWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3StandardWindowsServer2012R2)|

> [AZURE.IMPORTANT] Para utilizar imagens de VM BYOL, tem de ter um Contrato Enterprise com [Mobilidade de Licenças através do Software Assurance no Azure](https://azure.microsoft.com/pricing/license-mobility/). Também precisa de uma licença válida para a versão/edição do SQL Server que pretende utilizar. Tem de [fornecer as informações de BYOL necessárias à Microsoft](http://d36cz9buwru1tt.cloudfront.net/License_Mobility_Customer_Verification_Guide.pdf) até **10** dias após o aprovisionamento da VM.

## Gerir a VM do SQL
Depois de aprovisionar a VM do SQL Server, existem várias tarefas de gestão opcionais. Em muitos aspetos, pode configurar e gerir o SQL Server exatamente da mesma forma que geriria uma instância do SQL Server no local. Contudo, algumas tarefas são específicas para o Azure. As secções seguintes realçam algumas das seguintes áreas com ligações para mais informações.

### Migrar os dados

Se tiver uma base de dados existente, deverá movê-la para a VM do SQL recentemente aprovisionada. Para obter uma lista de opções de migração e orientações, consulte o artigo [Migrar uma Base de Dados para o SQL Server numa VM do Azure](virtual-machines-windows-migrate-sql.md).

### Configurar uma elevada disponibilidade

Se necessitar de elevada disponibilidade, considere configurar Grupos de Disponibilidade do SQL Server. Isto envolve várias VMs do Azure numa rede virtual. O Portal do Azure tem um modelo que configura esta configuração para si. Para obter mais informações, consulte o artigo [Configurar um grupo de Disponibilidade AlwaysOn nas Virtual Machines do Azure Resource Manager](virtual-machines-windows-portal-sql-alwayson-availability-groups.md). Se pretender configurar manualmente o Grupo de Disponibilidade e o serviço de escuta associado, consulte o artigo [Configurar Grupos de Disponibilidade AlwaysOn na VM do Azure](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md).

Para outras considerações de elevada disponibilidade, consulte o artigo [Elevada Disponibilidade e Recuperação Após Desastre para o SQL Server em Virtual Machines do Azure](virtual-machines-windows-sql-high-availability-dr.md).

### Criar cópias de segurança dos dados
As VMs do Azure podem tirar partido da [Cópia de Segurança Automatizada](virtual-machines-windows-sql-automated-backup.md), que cria regularmente cópias de segurança da sua base de dados para o armazenamento de blobs. Pode também utilizar esta técnica manualmente. Para obter mais informações, veja [Use Azure Storage for SQL Server Backup and Restore (Utilizar o Armazenamento do Azure para o Restauro e a Cópia de Segurança do SQL Server)](virtual-machines-windows-use-storage-sql-server-backup-restore.md). Para obter uma descrição geral de todas as opções de cópia de segurança e restauro,veja [Backup and Restore for SQL Server in Azure Virtual Machines (Cópia de segurança e Restauro para o SQL Server em Máquinas Virtuais do Azure)](virtual-machines-windows-sql-backup-recovery.md).

### Automatizar as atualizações
As VMs do Azure podem utilizar a [ Aplicação Automatizada de Patches](virtual-machines-windows-sql-automated-patching.md) para agendar uma janela de manutenção para a instalação automática de atualizações importantes do Windows e do SQL Server.

### Programa de melhoramento da experiência do cliente (PMEC)
O Programa de Melhoramento da Experiência do Cliente (PMEC) está ativado por predefinição. Não é uma tarefa de gestão, a menos que pretenda desativar o PMEC após o aprovisionamento. Pode personalizar ou desativar o PMEC ligando-se à VM com o ambiente de trabalho remoto. Em seguida, execute o utilitário **Relatórios de Utilização e Erros do SQL Server**. Siga as instruções para desativar os relatórios.

## Passos seguintes
[Explorar o Percurso de Aprendizagem](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) para o SQL Server em máquinas virtuais do Azure.

Tem mais alguma pergunta? Em primeiro lugar, consulte as [FAQ acerca do SQL Server em Virtual Machines do Azure](virtual-machines-windows-sql-server-iaas-faq.md). Mas também adicione as suas questões ou comentários na parte inferior de qualquer tópico de VM de SQL para interagir com a Microsoft e a comunidade.



<!--HONumber=Sep16_HO3-->


