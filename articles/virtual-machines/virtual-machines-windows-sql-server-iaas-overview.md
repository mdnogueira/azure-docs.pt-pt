---
title: "Descrição Geral do SQL Server em Máquinas Virtuais do Azure | Microsoft Docs"
description: "Saiba como executar edições completas do SQL Server em máquinas virtuais do Windows. Obtenha ligações diretas para todas as imagens de VMs do SQL e conteúdos relacionados."
services: virtual-machines-windows
documentationcenter: 
author: rothja
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: c505089e-6bbf-4d14-af0e-dd39a1872767
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 12/01/2016
ms.author: jroth
translationtype: Human Translation
ms.sourcegitcommit: bef7de37e358b49c77a4774e3e90a5e1de273310
ms.openlocfilehash: 5c9cbe96b92546e802190879919602da8687542f


---
# <a name="overview-of-sql-server-on-azure-virtual-machines"></a>Descrição Geral do SQL Server em Máquinas Virtuais do Azure
Este tópico descreve as opções para executar o SQL Server em máquinas virtuais (VMs) do Azure e inclui [ligações para imagens do portal](#option-1-create-a-sql-vm-with-per-minute-licensing) e uma descrição geral das [tarefas comuns](#manage-your-sql-vm).

> [!NOTE]
> Se já estiver familiarizado com o SQL Server e quiser ver apenas como implementar uma VM do SQL Server, veja [Aprovisionar uma máquina virtual do SQL Server no portal do Azure](virtual-machines-windows-portal-sql-server-provision.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
> 
> 

## <a name="overview"></a>Descrição geral
Se for um administrador de base de dados ou um programador, as VMs do Azure oferecem uma forma de mover as cargas de trabalho no local do SQL Server e as aplicações para a Nuvem. O vídeo seguinte apresenta uma descrição geral técnica das VMs do Azure SQL Server.

> [!VIDEO https://channel9.msdn.com/Events/DataDriven/SQLServer2016/Azure-VM-is-the-best-platform-for-SQL-Server-2016/player]
> 
> 

O vídeo abrange as seguintes áreas:

| Hora | Área |
| --- | --- |
| 00:21 |O que são VMs do Azure? |
| 01:45 |Segurança |
| 02:50 |Conectividade |
| 03:30 |Desempenho e fiabilidade de armazenamento |
| 05:20 |Tamanhos de VM |
| 05:54 |Elevada disponibilidade e SLA |
| 07:30 |Suporte de configuração |
| 08:00 |Monitorização |
| 08:32 |Demonstração: Criar uma VM do SQL Server 2016 |

> [!NOTE]
> O vídeo concentra-se no SQL Server 2016, mas o Azure oferece imagens de VM para muitas das versões do SQL Server, incluindo 2008, 2012, 2014 e 2016. 
> 
> 

## <a name="scenarios"></a>Cenários
Existem muitos motivos pelos quais pode optar para alojar os dados no Azure. Se a aplicação está a ser movida para o Azure, também melhora o desempenho para mover os dados. No entanto, existem outras vantagens. Tem automaticamente acesso a vários centros de dados para uma recuperação após desastre e uma presença global. Os dados são também altamente protegidos e com disponibilidade.

O SQL Server em execução em VMs do Azure é uma opção para armazenar os seus dados relacionais no Azure. É uma boa escolha para vários cenários. Por exemplo, pode querer configurar a VM do Azure da forma mais semelhante possível a um computador do SQL Server no local. Ou pode querer executar serviços e aplicações adicionais no mesmo servidor de base de dados. Existem dois recursos principais que podem ajudar a pensar através de ainda mais cenários e considerações:

* O [SQL Server on Azure virtual machines (SQL Server em máquinas virtuais do Azure)](https://azure.microsoft.com/services/virtual-machines/sql-server/) oferece uma descrição geral dos melhores cenários para utilizar o SQL Server em VMs do Azure. 
* [Choose a cloud SQL Server option: Azure SQL (PaaS) Database or SQL Server on Azure VMs (IaaS) (Escolher uma opção do SQL Server de nuvem: Base de Dados SQL do Azure (PaaS) ou SQL Server em VMs do Azure (IaaS))](../sql-database/sql-database-paas-vs-sql-server-iaas.md) oferece uma comparação detalhada entre a Base de Dados SQL e o SQL Server em execução numa VM.

## <a name="create-a-new-sql-vm"></a>Criar uma VM do SQL nova
As secções seguintes fornecem ligações diretas para o portal do Azure para as imagens da galeria das máquinas virtuais do SQL Server. Consoante a imagem que selecionou, pode pagar os custos de licenciamento do SQL Server por minuto ou pode trazer a sua própria licença (BYOL).

Pode obter orientações passo a passo para este processo no tutorial [Aprovisionar uma máquina virtual do SQL Server no Portal do Azure](virtual-machines-windows-portal-sql-server-provision.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Além disso, reveja as [Melhores práticas do desempenho para as VMs do SQL Server](virtual-machines-windows-sql-performance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), que explica como selecionar o tamanho da máquina apropriada e outras funcionalidades disponíveis durante o aprovisionamento.

## <a name="option-1-create-a-sql-vm-with-per-minute-licensing"></a>Opção 1: Criar uma VM do SQL Server com licenciamento por minuto
A tabela seguinte fornece uma matriz de imagens do SQL Server disponíveis na galeria da máquina virtual. Clique em qualquer ligação para começar a criar uma nova VM do SQL Server com a versão especificada, edição e sistema operativo.

| Versão | Sistema Operativo | Edição |
| --- | --- | --- |
| **SQL 2016** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMEnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMStandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMWebWindowsServer2012R2), [Dev](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMDeveloperWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMExpressWindowsServer2012R2) |
| **SQL 2014 SP1** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1ExpressWindowsServer2012R2) |
| **SQL 2014** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014WebWindowsServer2012R2) |
| **SQL 2012 SP3** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3ExpressWindowsServer2012R2) |
| **SQL 2012 SP2** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2WebWindowsServer2012R2) |
| **SQL 2012 SP2** |Windows Server 2012 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2EnterpriseWindowsServer2012), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2StandardWindowsServer2012), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2WebWindowsServer2012), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2ExpressWindowsServer2012) |
| **SQL 2008 R2 SP3** |Windows Server 2008 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3EnterpriseWindowsServer2008R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3StandardWindowsServer2008R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3WebWindowsServer2008R2) |
| **SQL 2008 R2 SP3** |Windows Server 2012 |[Express](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3ExpressWindowsServer2012) |

## <a name="a-idbyola-option-2-create-a-sql-vm-with-an-existing-license"></a><a id="BYOL"></a> Opção 2: Criar uma VM do SQL Server com uma licença já existente
Também pode trazer a sua licença (BYOL). Neste cenário, apenas paga a VM sem quaisquer taxas adicionais para o licenciamento do SQL Server. Para utilizar a sua própria licença, utilize a matriz das versões do SQL Server, edições e sistemas operativos abaixo. No portal, é adicionado o prefixo **{BYOL}** aos nomes destas imagens.

| Versão | Sistema operativo | Edição |
| --- | --- | --- |
| **SQL Server 2016** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016RTMStandardWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016RTMStandardWindowsServer2012R2) |
| **SQL Server 2014 SP1** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP1EnterpriseWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP1StandardWindowsServer2012R2) |
| **SQL Server 2012 SP2** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3EnterpriseWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3StandardWindowsServer2012R2) |

> [!IMPORTANT]
> Para utilizar imagens de VM BYOL, tem de ter um Contrato Enterprise com [Mobilidade de Licenças através do Software Assurance no Azure](https://azure.microsoft.com/pricing/license-mobility/). Também precisa de uma licença válida para a versão/edição do SQL Server que pretende utilizar. Tem de [fornecer as informações de BYOL necessárias à Microsoft](http://d36cz9buwru1tt.cloudfront.net/License_Mobility_Customer_Verification_Guide.pdf) até **10** dias após o aprovisionamento da VM.
> 

> [!NOTE]
> Não pode alterar o modelo de licenciamento de uma VM do SQL Server de pagamento por minuto, para utilizar a sua licença. Neste caso, tem de criar uma nova VM BYOL e migrar as bases de dados para a nova VM. 

## <a name="manage-your-sql-vm"></a>Gerir a VM do SQL
Depois de aprovisionar a VM do SQL Server, existem várias tarefas de gestão opcionais. Em muitos aspetos, pode configurar e gerir o SQL Server exatamente da mesma forma que geriria uma instância do SQL Server no local. Contudo, algumas tarefas são específicas para o Azure. As secções seguintes realçam algumas das seguintes áreas com ligações para mais informações.

### <a name="connect-to-the-vm"></a>Ligar à VM
Um dos passos de gestão mais básicos é ligar à sua VM do SQL Server através de ferramentas, como o SQL Server Management Studio (SSMS). Para obter instruções sobre como ligar à sua nova VM do SQL Server, consulte [Connect to a SQL Server Virtual Machine on Azure (Ligar a uma Máquina Virtual do SQL Server no Azure)](virtual-machines-windows-sql-connect.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="migrate-your-data"></a>Migrar os dados
Se tiver uma base de dados existente, deverá movê-la para a VM do SQL recentemente aprovisionada. Para obter uma lista de opções de migração e orientações, consulte o artigo [Migrar uma Base de Dados para o SQL Server numa VM do Azure](virtual-machines-windows-migrate-sql.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="configure-high-availability"></a>Configurar uma elevada disponibilidade
Se necessitar de elevada disponibilidade, considere configurar Grupos de Disponibilidade do SQL Server. Isto envolve várias VMs do Azure numa rede virtual. O Portal do Azure tem um modelo que configura esta configuração para si. Para obter mais informações, consulte o artigo [Configurar um grupo de Disponibilidade AlwaysOn nas Virtual Machines do Azure Resource Manager](virtual-machines-windows-portal-sql-alwayson-availability-groups.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Se pretender configurar manualmente o Grupo de Disponibilidade e o serviço de escuta associado, consulte o artigo [Configurar Grupos de Disponibilidade AlwaysOn na VM do Azure](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Para outras considerações de elevada disponibilidade, consulte o artigo [Elevada Disponibilidade e Recuperação Após Desastre para o SQL Server em Virtual Machines do Azure](virtual-machines-windows-sql-high-availability-dr.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="back-up-your-data"></a>Criar cópias de segurança dos dados
As VMs do Azure podem tirar partido da [Cópia de Segurança Automatizada](virtual-machines-windows-sql-automated-backup.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), que cria regularmente cópias de segurança da sua base de dados para o armazenamento de blobs. Pode também utilizar esta técnica manualmente. Para obter mais informações, veja [Use Azure Storage for SQL Server Backup and Restore (Utilizar o Armazenamento do Azure para o Restauro e a Cópia de Segurança do SQL Server)](virtual-machines-windows-use-storage-sql-server-backup-restore.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Para obter uma descrição geral de todas as opções de cópia de segurança e restauro,veja [Backup and Restore for SQL Server in Azure Virtual Machines (Cópia de segurança e Restauro para o SQL Server em Máquinas Virtuais do Azure)](virtual-machines-windows-sql-backup-recovery.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="automate-updates"></a>Automatizar as atualizações
As VMs do Azure podem utilizar a [ Aplicação Automatizada de Patches](virtual-machines-windows-sql-automated-patching.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para agendar uma janela de manutenção para a instalação automática de atualizações importantes do Windows e do SQL Server.

### <a name="customer-experience-improvement-program-ceip"></a>Programa de melhoramento da experiência do cliente (PMEC)
O Programa de Melhoramento da Experiência do Cliente (PMEC) está ativado por predefinição. Esta ação envia relatórios periodicamente para a Microsoft, para ajudar a melhorar o SQL Server. Não é precisa nenhuma tarefa de gestão com PMEC, a menos que queira desativá-la após o aprovisionamento. Pode personalizar ou desativar o PMEC ligando-se à VM com o ambiente de trabalho remoto. Em seguida, execute o utilitário **Relatórios de Utilização e Erros do SQL Server**. Siga as instruções para desativar os relatórios. 

Para mais informações, consulte a secção do PMEC do tópico [Accept License Terms (Aceitar os Termos de Licenciamento)](https://msdn.microsoft.com/library/ms143343.aspx) . 

## <a name="next-steps"></a>Passos seguintes
[Explorar o Percurso de Aprendizagem](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) para o SQL Server em máquinas virtuais do Azure.

Tem mais alguma pergunta? Em primeiro lugar, consulte as [FAQ acerca do SQL Server em Virtual Machines do Azure](virtual-machines-windows-sql-server-iaas-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Mas também adicione as suas questões ou comentários na parte inferior de qualquer tópico de VM de SQL para interagir com a Microsoft e a comunidade.




<!--HONumber=Dec16_HO1-->


