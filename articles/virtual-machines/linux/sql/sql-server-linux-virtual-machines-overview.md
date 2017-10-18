---
title: "Descrição Geral do SQL Server em Máquinas Virtuais do Linux no Azure | Microsoft Docs"
description: "Saiba como executar edições completas do SQL Server em máquinas virtuais do Linux no Azure. Obtenha ligações diretas para todas as imagens de VMs do SQL Server do Linux e conteúdos relacionados."
services: virtual-machines-linux
documentationcenter: 
author: rothja
manager: jhubbard
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: get-started-article
ms.workload: iaas-sql-server
ms.date: 10/02/2017
ms.author: jroth
ms.openlocfilehash: 787e696edd2a446bec280b763fe5ac591782c8ae
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-sql-server-on-azure-virtual-machines-linux"></a>Descrição Geral do SQL Server em Máquinas Virtuais do Azure (Linux)

> [!div class="op_single_selector"]
> * [Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)
> * [Linux](sql-server-linux-virtual-machines-overview.md)

Este tópico descreve as opções para executar o SQL Server em máquinas virtuais (VMs) do Linux no Azure e inclui [ligações para imagens do portal](#option-1-create-a-sql-vm-with-per-minute-licensing).

> [!NOTE]
> Se já estiver familiarizado com o SQL Server e quiser ver apenas como implementar uma VM do Linux do SQL Server, veja [Aprovisionar uma VM do SQL Server do Linux no Azure](provision-sql-server-linux-virtual-machine.md). Em alternativa, se quiser criar uma VM do Windows com o SQL Server, veja [Aprovisionar uma VM do SQL Server do Windows no Azure](../../windows/sql/virtual-machines-windows-portal-sql-server-provision.md).

Se for um administrador de base de dados ou um programador, as VMs do Azure oferecem uma forma de mover as cargas de trabalho no local do SQL Server e as aplicações para a Nuvem.

## <a name="scenarios"></a>Cenários

Existem muitos motivos pelos quais pode optar para alojar os dados no Azure. Se estiver a desenvolver ou a migrar a sua aplicação para o Azure, melhora o desempenho para localizar também os dados de back-end no Azure. Tem automaticamente acesso a vários centros de dados para uma recuperação após desastre e uma presença global. Os dados são também altamente protegidos e com disponibilidade.

O SQL Server em execução em VMs do Azure é uma opção para armazenar os seus dados relacionais no Azure. Tem também a opção de utilizar o serviço Base de Dados SQL do Azure. Para obter mais informações sobre como escolher entre o SQL Server em Máquinas Virtuais e a Base de Dados SQL do Azure, veja [Escolha uma opção de SQL Server na nuvem: Base de Dados SQL (PaaS) do Azure ou SQL Server em VMs (IaaS) do Azure](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md).

## <a name="create-a-new-sql-vm"></a>Criar uma VM do SQL nova

Pode obter orientações passo a passo para criar uma nova VM SQL no tutorial [Aprovisionar uma VM do SQL Server do Linux no Azure](provision-sql-server-linux-virtual-machine.md).

A tabela seguinte fornece uma matriz das últimas imagens do SQL Server na galeria da máquina virtual. Clique em qualquer ligação para começar a criar uma nova VM do SQL Server com a versão especificada, edição e sistema operativo.

> [!TIP]
> Para compreender os preços da VM e do SQL destas imagens, veja a [página de preços das VMs do SQL Server do Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

| Versão | Sistema Operativo | Edição |
| --- | --- | --- |
| **SQL Server 2017** | Red Hat Enterprise Linux (RHEL) 7.4 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonRedHatEnterpriseLinux74), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonRedHatEnterpriseLinux74), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonRedHatEnterpriseLinux74), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonRedHatEnterpriseLinux74), [Programador](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonRedHatEnterpriseLinux74) |
| **SQL Server 2017** | SUSE Linux Enterprise Server (SLES) v12 SP2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonSLES12SP2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonSLES12SP2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonSLES12SP2), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2), [Programador](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP2) |
| **SQL Server 2017** | Ubuntu 16.04 LTS |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonUbuntuServer1604LTS), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonUbuntuServer1604LTS), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonUbuntuServer1604LTS), [Programador](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonUbuntuServer1604LTS) |

> [!NOTE]
> Para ver as imagens de máquina virtual do SQL Server do Windows disponíveis, veja [Descrição Geral do SQL Server em Máquinas Virtuais do Azure (Windows)](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md).

## <a id="packages"></a> Pacotes instalados

Quando configurar o SQL Server no Linux, instale o pacote do motor de base de dados e, em seguida, vários pacotes opcionais, consoante os seus requisitos. As imagens de máquina virtual do Linux para o SQL Server instalam automaticamente a maioria dos pacotes por si. A tabela seguinte mostra os pacotes que são instalados para cada distribuição.

| Distribuição | [Motor de Base de Dados](https://docs.microsoft.com/sql/linux/sql-server-linux-setup) | [Ferramentas](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools) | [Agente do SQL Server](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-sql-agent) | [Pesquisa em Texto Completo](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-full-text-search) | [SSIS](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-ssis) | [Suplemento do HA](https://docs.microsoft.com/sql/linux/sql-server-linux-business-continuity-dr) |
|---|---|---|---|---|---|---|
| RHEL | ![sim](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![sim](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![sim](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![sim](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![sim](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![não](./media/sql-server-linux-virtual-machines-overview/no.png) |
| SLES | ![sim](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![sim](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![sim](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![sim](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![não](./media/sql-server-linux-virtual-machines-overview/no.png) | ![não](./media/sql-server-linux-virtual-machines-overview/no.png) |
| Ubuntu | ![sim](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![sim](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![sim](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![sim](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![sim](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![sim](./media/sql-server-linux-virtual-machines-overview/yes.png) |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre como configurar e utilizar o SQL Server no Linux, veja [Descrição Geral do SQL Server no Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview).
