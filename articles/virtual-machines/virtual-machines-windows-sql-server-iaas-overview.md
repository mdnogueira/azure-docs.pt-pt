<properties
    pageTitle="Introdução ao SQL Server em Virtual Machines do Azure | Microsoft Azure"
    description="Mova as cargas de trabalho da base de dados do SQL Server no local para a Nuvem com Virtual Machines do Azure. Comece a trabalhar rapidamente com imagens pré-configuradas da VM do SQL."
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
    ms.date="06/03/2016"
    ms.author="jroth"/>

# Introdução ao SQL Server em Virtual Machines do Azure

Este tópico descreve as opções para executar o SQL Server em Virtual Machines do Azure e fornece orientações e recursos para ajudar a começar.

Poderá ser um administrador da base de dados que quer para mover as cargas de trabalho do SQL Server no local para a Nuvem. Ou poderá ser um programador a considerar as capacidades da base de dados relacional do SQL Server para a sua aplicação Azure. Qual é a vantagem de executar cargas de trabalho do SQL Server em Virtual Machines do Azure? A seguinte descrição geral do vídeo aborda as vantagens e fornece uma descrição técnica.

> [AZURE.VIDEO data-driven-sql-server-2016-azure-vm-is-the-best-platform-for-sql-server-2016]

## Avaliar as vantagens

Antes de começar, avalie primeiro o que ganha com a utilização do SQL Server em VMs do Azure.

Se estiver a mover outras cargas de trabalho para o Azure, tal como uma aplicação empresarial, faz sentido também mover as bases de dados do SQL Server dependentes para o Azure para um melhor desempenho. Mas alojar o SQL Server em VMs do Azure fornece outras vantagens. Por exemplo, tem automaticamente acesso a vários centros de dados para uma recuperação após desastre e uma presença global. Para obter uma lista completa de cenários e vantagens, consulte a [Página de produto do SQL Server em VMs do Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/).

> [AZURE.NOTE] Quando estiver a avaliar o SQL Server em VMs do Azure, reveja também as outras opções de armazenamento e SQL no Azure, tal como [SQL Database](../sql-database/sql-database-technical-overview.md), [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) e [SQL Server Stretch Database](../sql     -server-stretch-database/sql-server-stretch-database-overview.md). Para uma comparação mais detalhada, consulte [Escolher uma opção do SQL Server de nuvem: SQL Database do Azure (PaaS) ou do SQL Server em VMs do Azure (IaaS)](../sql-database/data-management-azure-sql-database-and-sql-server-iaas.md).

Depois de optar por executar o SQL Server em VMs do Azure, uma das suas primeiras decisões é se pretende utilizar uma imagem da VM que inclui os custos de licenciamento do SQL Server. A outra opção consiste em trazer a sua própria licença (BYOL) e apenas pagar a própria VM. As duas secções seguintes descrevem estas opções.

## Opção 1: implementar uma VM do SQL (licenciamento por minuto)
A tabela seguinte fornece uma matriz de imagens do SQL Server disponíveis na galeria da máquina virtual. Clique em qualquer ligação para começar a criar uma nova VM do SQL Server com a versão especificada, edição e sistema operativo. Todas as imagens incluem [os custos de licenciamento do SQL Server](https://azure.microsoft.com/pricing/details/virtual-machines/#Sql).

A orientação passo-a-passo está disponível no tutorial, [Aprovisionar uma máquina virtual do SQL Server no Portal do Azure](virtual-machines-windows-portal-sql-server-provision.md). Além disso, reveja as [Melhores práticas do desempenho para as VMs do SQL Server](virtual-machines-windows-sql-performance.md), que explica como selecionar o tamanho da máquina apropriada e outras funcionalidades disponíveis durante o aprovisionamento.

|Versão do SQL Server|Sistema operativo|Edição do SQL Server|
|---|---|---|
|**SQL Server 2016**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMEnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMStandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMWebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMExpressWindowsServer2012R2)|
|**SQL Server 2014 SP1**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1ExpressWindowsServer2012R2)|
|**SQL Server 2014**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014WebWindowsServer2012R2)|
|**SQL Server 2012 SP3**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3ExpressWindowsServer2012R2)|
|**SQL Server 2012 SP2**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2WebWindowsServer2012R2)|
|**SQL Server 2012 SP2**|Windows Server 2012|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2EnterpriseWindowsServer2012), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2StandardWindowsServer2012), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2WebWindowsServer2012), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2ExpressWindowsServer2012)|
|**SQL Server 2008 R2 SP3**|Windows Server 2008 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3EnterpriseWindowsServer2008R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3StandardWindowsServer2008R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3WebWindowsServer2008R2)|
|**SQL Server 2008 R2 SP3**|Windows Server 2012|[Express](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3ExpressWindowsServer2012)|

## Opção 2: implementar uma VM do SQL Server (BYOL)
A outra opção consiste em trazer a sua própria licença (BYOL). Neste cenário, apenas paga a VM sem quaisquer taxas adicionais para o licenciamento do SQL Server. Para utilizar a sua própria licença, utilize a matriz das versões do SQL Server, edições e sistemas operativos abaixo. No portal, é adicionado o prefixo **{BYOL}** aos nomes de imagens no Portal.

> [AZURE.IMPORTANT] Para utilizar imagens da VM BYOL, tem de ter um Contrato Enterprise com [Licença Mobilidade através do Software Assurance no Azure](https://azure.microsoft.com/pricing/license-mobility/). Também precisa de uma licença válida para a versão/edição do SQL Server que pretende utilizar. Tem de [fornecer as informações de BYOL necessárias à Microsoft](http://d36cz9buwru1tt.cloudfront.net/License_Mobility_Customer_Verification_Guide.pdf) até **10** dias após o aprovisionamento da VM.

Aplicam-se as orientações no [tutorial de aprovisionamento](virtual-machines-windows-portal-sql-server-provision.md), mas tem de utilizar uma das seguintes opções de imagem **BYOL**. Além disso, reveja as [Melhores práticas do desempenho para as VMs do SQL Server](virtual-machines-windows-sql-performance.md), que explica como selecionar o tamanho da máquina apropriada e outras funcionalidades disponíveis durante o aprovisionamento.

|Versão do SQL Server|Sistema operativo|Edição do SQL Server|
|---|---|---|
|**SQL Server 2016**|Windows Server 2012 R2|[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016RTMStandardWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016RTMStandardWindowsServer2012R2)|
|**SQL Server 2014 SP1**|Windows Server 2012 R2|[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP1EnterpriseWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP1StandardWindowsServer2012R2)|
|**SQL Server 2012 SP2**|Windows Server 2012 R2|[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3EnterpriseWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3StandardWindowsServer2012R2)|

## Gerir a VM do SQL
Depois de aprovisionar a VM do SQL Server, existem várias tarefas de gestão opcionais. Em alguns aspetos, pode configurar e gerir o SQL Server exatamente da mesma forma que o faria no local. Mas algumas tarefas são específicas para o Azure. As secções seguintes realçam algumas das seguintes áreas com ligações para mais informações.

### Migrar os dados

Se tiver uma base de dados existente, deverá movê-la para a VM do SQL recentemente aprovisionada. Para obter uma lista de opções de migração e orientações, consulte o artigo [Migrar uma Base de Dados para o SQL Server numa VM do Azure](virtual-machines-windows-migrate-sql.md).

### Configurar uma elevada disponibilidade

Se necessitar de elevada disponibilidade, considere configurar Grupos de Disponibilidade do SQL Server. Isto envolve várias VMs do Azure numa rede virtual. O Portal do Azure tem um modelo que configura esta configuração para si. Para obter mais informações, consulte o artigo [Configurar um grupo de Disponibilidade AlwaysOn nas Virtual Machines do Azure Resource Manager](virtual-machines-windows-portal-sql-alwayson-availability-groups.md). Se pretender configurar manualmente o Grupo de Disponibilidade e o serviço de escuta associado, consulte o artigo [Configurar Grupos de Disponibilidade AlwaysOn na VM do Azure](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md).

Para outras considerações de elevada disponibilidade, consulte o artigo [Elevada Disponibilidade e Recuperação Após Desastre para o SQL Server em Virtual Machines do Azure](virtual-machines-windows-sql-high-availability-dr.md).

### Efetuar uma cópia de segurança dos dados
Armazene cópias de segurança da base de dados diretamente no Azure utilizando o Blob Storage. Para obter mais informações, consulte o artigo [Utilizar o Storage do Azure para o Restauro e a Cópia de Segurança do SQL Server](../sql-database/storage-use-storage-sql-server-backup-restore.md). Apesar de isto funcionar bem com as VMs do SQL, também funciona para bases de dados do SQL Server no local. Para obter uma descrição geral das opções de cópia de segurança e restauro, consulte o artigo [Cópia de segurança e Restauro para o SQL Server em Virtual Machines do Azure](virtual-machines-windows-sql-backup-recovery.md).

As VMs do Azure também podem tirar partido da [Cópia de Segurança Automatizada](virtual-machines-windows-sql-automated-backup.md) e da [Aplicação de Patches automatizada](virtual-machines-windows-sql-automated-patching.md) para o SQL Server.

### Programa de melhoramento da experiência do cliente (PMEC)
O Programa de Melhoramento da Experiência do Cliente (PMEC) está ativado por predefinição. Não é uma tarefa de gestão, a menos que pretenda desativar o PMEC após o aprovisionamento. Pode personalizar ou desativar o PMEC ligando-se à VM com o ambiente de trabalho remoto. Em seguida, execute o utilitário **Relatórios de Utilização e Erros do SQL Server**. Siga as instruções para desativar os relatórios.

## Passos seguintes
[Explorar o Percurso de Aprendizagem](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) para o SQL Server em máquinas virtuais do Azure.

Tem mais alguma pergunta? Em primeiro lugar, consulte as [FAQ acerca do SQL Server em Virtual Machines do Azure](virtual-machines-windows-sql-server-iaas-faq.md). Mas também adicione as suas questões ou comentários na parte inferior de qualquer tópico de VM de SQL para interagir com a Microsoft e a comunidade.



<!--HONumber=Jun16_HO2-->


