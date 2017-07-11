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
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/27/2017
ms.author: jroth
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: f0100423550046d18642180ce98e93ce3609749b
ms.contentlocale: pt-pt
ms.lasthandoff: 06/28/2017


---
<a id="overview-of-sql-server-on-azure-virtual-machines" class="xliff"></a>

# Descrição Geral do SQL Server em Máquinas Virtuais do Azure
Este tópico descreve as opções para executar o SQL Server em máquinas virtuais (VMs) do Azure e inclui [ligações para imagens do portal](#option-1-create-a-sql-vm-with-per-minute-licensing) e uma descrição geral das [tarefas comuns](#manage-your-sql-vm).

> [!NOTE]
> Se já estiver familiarizado com o SQL Server e quiser ver apenas como implementar uma VM do SQL Server, veja [Aprovisionar uma máquina virtual do SQL Server no portal do Azure](virtual-machines-windows-portal-sql-server-provision.md).
> 
> 

<a id="overview" class="xliff"></a>

## Descrição geral
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
> O vídeo concentra-se no SQL Server 2016, mas o Azure oferece imagens da VM para várias versões do SQL Server, incluindo 2012, 2014 e 2016. 
> 
> 

<a id="scenarios" class="xliff"></a>

## Cenários
Existem muitos motivos pelos quais pode optar para alojar os dados no Azure. Se a aplicação está a ser movida para o Azure, também melhora o desempenho para mover os dados. No entanto, existem outras vantagens. Tem automaticamente acesso a vários centros de dados para uma recuperação após desastre e uma presença global. Os dados são também altamente protegidos e com disponibilidade.

O SQL Server em execução em VMs do Azure é uma opção para armazenar os seus dados relacionais no Azure. É uma boa escolha para vários cenários. Por exemplo, pode querer configurar a VM do Azure da forma mais semelhante possível a um computador do SQL Server no local. Ou pode querer executar serviços e aplicações adicionais no mesmo servidor de base de dados. Existem dois recursos principais que podem ajudar a pensar através de ainda mais cenários e considerações:

* O [SQL Server on Azure virtual machines (SQL Server em máquinas virtuais do Azure)](https://azure.microsoft.com/services/virtual-machines/sql-server/) oferece uma descrição geral dos melhores cenários para utilizar o SQL Server em VMs do Azure. 
* [Choose a cloud SQL Server option: Azure SQL (PaaS) Database or SQL Server on Azure VMs (IaaS) (Escolher uma opção do SQL Server de nuvem: Base de Dados SQL do Azure (PaaS) ou SQL Server em VMs do Azure (IaaS))](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md) oferece uma comparação detalhada entre a Base de Dados SQL e o SQL Server em execução numa VM.

<a id="create-a-new-sql-vm" class="xliff"></a>

## Criar uma VM do SQL nova
As secções seguintes fornecem ligações diretas para o portal do Azure para as imagens da galeria das máquinas virtuais do SQL Server. Consoante a imagem que selecionou, pode pagar os custos de licenciamento do SQL Server por minuto ou pode trazer a sua própria licença (BYOL).

Pode obter orientações passo a passo para criar uma nova VM SQL no tutorial [Provision a SQL Server virtual machine in the Azure portal (Aprovisionar uma máquina virtual do SQL Server no Portal do Azure)](virtual-machines-windows-portal-sql-server-provision.md). Além disso, reveja as [Melhores práticas do desempenho para as VMs do SQL Server](virtual-machines-windows-sql-performance.md), que explica como selecionar o tamanho da máquina apropriada e outras funcionalidades disponíveis durante o aprovisionamento.

<a id="option-1-create-a-sql-vm-with-per-minute-licensing" class="xliff"></a>

## Opção 1: Criar uma VM do SQL Server com licenciamento por minuto
A tabela seguinte fornece uma matriz das últimas imagens do SQL Server na galeria da máquina virtual. Clique em qualquer ligação para começar a criar uma nova VM do SQL Server com a versão especificada, edição e sistema operativo. 

> [!TIP]
> Para compreender os preços da VM e do SQL destas imagens, consulte [Pricing guidance for SQL Server Azure VMs (Documentação de orientação sobre preços das VMs do Azure do SQL Server)](virtual-machines-windows-sql-server-pricing-guidance.md).

| Versão | Sistema Operativo | Edição |
| --- | --- | --- |
| **SQL Server 2016 SP1** |Windows Server 2016 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2016SP1EnterpriseWindowsServer2016), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2016SP1StandardWindowsServer2016), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2016SP1WebWindowsServer2016), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2016SP1ExpressWindowsServer2016), [Programador](https://portal.azure.com/#create/Microsoft.SQLServer2016SP1DeveloperWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2ExpressWindowsServer2012R2) |
| **SQL Server 2012 SP3** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3ExpressWindowsServer2012R2) |

Para além de nesta lista, estão disponíveis outras combinações de sistemas operativos e versões do SQL Server. Encontre outras imagens através de uma pesquisa no marketplace no portal do Azure. 

## <a id="BYOL"></a> Opção 2: Criar uma VM do SQL Server com uma licença já existente
Também pode trazer a sua licença (BYOL). Neste cenário, apenas paga a VM sem quaisquer taxas adicionais para o licenciamento do SQL Server. Para utilizar a sua própria licença, utilize a matriz das versões do SQL Server, edições e sistemas operativos abaixo. No portal, é adicionado o prefixo **{BYOL}** aos nomes destas imagens.

> [!TIP]
> Colocar a sua própria licença poderá poupar dinheiro ao longo do tempo para cargas de trabalho de produção contínua. Para obter mais informações, consulte [Pricing guidance for SQL Server Azure VMs (Documentação de orientação sobre preços de VMs do Azure do SQL Server)](virtual-machines-windows-sql-server-pricing-guidance.md).

| Versão | Sistema operativo | Edição |
| --- | --- | --- |
| **SQL Server 2016 SP1** |Windows Server 2016 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP1StandardWindowsServer2016), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP1StandardWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2EnterpriseWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2StandardWindowsServer2012R2) |
| **SQL Server 2012 SP2** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3EnterpriseWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3StandardWindowsServer2012R2) |

Para além de nesta lista, estão disponíveis outras combinações de sistemas operativos e versões do SQL Server. Encontre outras imagens através de uma pesquisa no marketplace no portal do Azure (procure "{BYOL} SQL Server").

> [!IMPORTANT]
> Para utilizar imagens de VM BYOL, tem de ter um Contrato Enterprise com [Mobilidade de Licenças através do Software Assurance no Azure](https://azure.microsoft.com/pricing/license-mobility/). Também precisa de uma licença válida para a versão/edição do SQL Server que pretende utilizar. Tem de [fornecer as informações de BYOL necessárias à Microsoft](http://d36cz9buwru1tt.cloudfront.net/License_Mobility_Customer_Verification_Guide.pdf) até **10** dias após o aprovisionamento da VM. 

> [!NOTE]
> Não pode alterar o modelo de licenciamento de uma VM do SQL Server de pagamento por minuto, para utilizar a sua licença. Neste caso, tem de criar uma nova VM BYOL e migrar as bases de dados para a nova VM. 

<a id="manage-your-sql-vm" class="xliff"></a>

## Gerir a VM do SQL
Depois de aprovisionar a VM do SQL Server, existem várias tarefas de gestão opcionais. Em muitos aspetos, pode configurar e gerir o SQL Server exatamente da mesma forma que geriria uma instância do SQL Server no local. Contudo, algumas tarefas são específicas para o Azure. As secções seguintes realçam algumas das seguintes áreas com ligações para mais informações.

<a id="connect-to-the-vm" class="xliff"></a>

### Ligar à VM
Um dos passos de gestão mais básicos é ligar à sua VM do SQL Server através de ferramentas, como o SQL Server Management Studio (SSMS). Para obter instruções sobre como ligar à sua nova VM do SQL Server, consulte [Connect to a SQL Server Virtual Machine on Azure (Ligar a uma Máquina Virtual do SQL Server no Azure)](virtual-machines-windows-sql-connect.md).

<a id="migrate-your-data" class="xliff"></a>

### Migrar os dados
Se tiver uma base de dados existente, deverá movê-la para a VM do SQL recentemente aprovisionada. Para obter uma lista de opções de migração e orientações, consulte o artigo [Migrar uma Base de Dados para o SQL Server numa VM do Azure](virtual-machines-windows-migrate-sql.md).

<a id="configure-high-availability" class="xliff"></a>

### Configurar uma elevada disponibilidade
Se necessitar de elevada disponibilidade, considere configurar Grupos de Disponibilidade do SQL Server. Isto envolve várias VMs do Azure numa rede virtual. O Portal do Azure tem um modelo que configura esta configuração para si. Para obter mais informações, consulte o artigo [Configurar um grupo de Disponibilidade AlwaysOn nas Virtual Machines do Azure Resource Manager](virtual-machines-windows-portal-sql-alwayson-availability-groups.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Se pretender configurar manualmente o Grupo de Disponibilidade e o serviço de escuta associado, consulte o artigo [Configurar Grupos de Disponibilidade AlwaysOn na VM do Azure](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md).

Para outras considerações de elevada disponibilidade, consulte o artigo [Elevada Disponibilidade e Recuperação Após Desastre para o SQL Server em Virtual Machines do Azure](virtual-machines-windows-sql-high-availability-dr.md).

<a id="back-up-your-data" class="xliff"></a>

### Criar cópias de segurança dos dados
As VMs do Azure podem tirar partido da [Cópia de Segurança Automatizada](virtual-machines-windows-sql-automated-backup.md), que cria regularmente cópias de segurança da sua base de dados para o armazenamento de blobs. Pode também utilizar esta técnica manualmente. Para obter mais informações, veja [Use Azure Storage for SQL Server Backup and Restore (Utilizar o Armazenamento do Azure para o Restauro e a Cópia de Segurança do SQL Server)](virtual-machines-windows-use-storage-sql-server-backup-restore.md). Para obter uma descrição geral de todas as opções de cópia de segurança e restauro,veja [Backup and Restore for SQL Server in Azure Virtual Machines (Cópia de segurança e Restauro para o SQL Server em Máquinas Virtuais do Azure)](virtual-machines-windows-sql-backup-recovery.md).

<a id="automate-updates" class="xliff"></a>

### Automatizar as atualizações
As VMs do Azure podem utilizar a [ Aplicação Automatizada de Patches](virtual-machines-windows-sql-automated-patching.md) para agendar uma janela de manutenção para a instalação automática de atualizações importantes do Windows e do SQL Server.

<a id="customer-experience-improvement-program-ceip" class="xliff"></a>

### Programa de melhoramento da experiência do cliente (PMEC)
O Programa de Melhoramento da Experiência do Cliente (PMEC) está ativado por predefinição. Esta ação envia relatórios periodicamente para a Microsoft, para ajudar a melhorar o SQL Server. Não é precisa nenhuma tarefa de gestão com PMEC, a menos que queira desativá-la após o aprovisionamento. Pode personalizar ou desativar o PMEC ligando-se à VM com o ambiente de trabalho remoto. Em seguida, execute o utilitário **Relatórios de Utilização e Erros do SQL Server**. Siga as instruções para desativar os relatórios. 

Para mais informações, consulte a secção do PMEC do tópico [Accept License Terms (Aceitar os Termos de Licenciamento)](https://msdn.microsoft.com/library/ms143343.aspx) . 

<a id="next-steps" class="xliff"></a>

## Passos seguintes

Para perguntas sobre preços, consulte [Pricing guidance for SQL Server Azure VMs (Documentação de orientação sobre preços de VMs do Azure do SQL Server)](virtual-machines-windows-sql-server-pricing-guidance.md) e [Página de preços do Azure](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). Selecione a sua edição de destino do SQL Server na lista **OS/Software**. Em seguida, veja os preços para máquinas virtuais de tamanhos diferentes.

Tem mais alguma pergunta? Em primeiro lugar, consulte as [FAQ acerca do SQL Server em Virtual Machines do Azure](virtual-machines-windows-sql-server-iaas-faq.md). Mas também adicione as suas questões ou comentários na parte inferior de qualquer tópico de VM de SQL para interagir com a Microsoft e a comunidade.

