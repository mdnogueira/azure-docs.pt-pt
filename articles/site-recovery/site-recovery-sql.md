---
title: "Replicar aplicações com o SQL Server e o Azure Site Recovery | Microsoft Docs"
description: Este artigo descreve como replicar do SQL Server utilizando o Azure Site Recovery para capacidades de desastres do SQL Server.
services: site-recovery
documentationcenter: 
author: prateek9us
manager: gauravd
editor: 
ms.assetid: 9126f5e8-e9ed-4c31-b6b4-bf969c12c184
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2017
ms.author: pratshar
ms.openlocfilehash: e53f60979e01a0eabe118d3ae6457a61bd4b0ded
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="protect-sql-server-using-sql-server-disaster-recovery-and-azure-site-recovery"></a>Proteger o SQL Server utilizando a recuperação de desastres do SQL Server e o Azure Site Recovery

Este artigo descreve como proteger o SQL Server de back-end de uma aplicação utilizando uma combinação de continuidade do negócio do SQL Server e as tecnologias de (BCDR) de recuperação de desastre, e [do Azure Site Recovery](site-recovery-overview.md).

Antes de começar, certifique-se de que compreende funcionalidades de recuperação de desastres do SQL Server, incluindo o clustering de ativação pós-falha, grupos de disponibilidade Always On, espelhamento de base de dados e o envio de registo.


## <a name="sql-server-deployments"></a>Implementações do SQL Server

Muitas cargas de trabalho utilizam o SQL Server como base, e pode ser integrada com aplicações como o SharePoint, Dynamics e o SAP, para implementar os serviços de dados.  SQL Server pode ser implementado em várias formas:

* **Servidor do SQL Server autónomo**: SQL Server e todas as bases de dados estão alojados num único computador (física ou virtual). Quando virtualizado, anfitrião clustering é utilizada para elevada visibilidade local. Ao nível do convidado elevada disponibilidade não está implementada.
* **Instâncias do SQL Server ativação pós-falha Clustering (sempre no FCI)**: dois ou mais nós com o SQL Server instanced com discos partilhados estão configurados num cluster de ativação pós-falha do Windows. Se um nó estiver desativado, o cluster pode falhar do SQL Server para outra instância. Esta configuração é normalmente utilizada para implementar elevada disponibilidade num site primário. Esta implementação não protege contra a falha ou falha na camada de armazenamento partilhado. Um disco partilhado pode ser implementado através de iSCSI, canal de Fibra ou de vhdx partilhado.
* **SQL Server em grupos de disponibilidade Always**: dois ou mais nós estão configurados em partilhado nada de cluster, com bases de dados do SQL Server configuradas num grupo de disponibilidade, com replicação síncrona e ativação pós-falha automática.

 Este artigo utiliza as seguintes nativas SQL desastre recuperação tecnologias para recuperar bases de dados a um site remoto:

* SQL Server em grupos de disponibilidade Always, para fornecer a recuperação de desastres para SQL Server 2012 ou 2014 edições de Enterprise.
* Base de dados espelhamento do SQL no modo de segurança elevada, para o SQL Server Standard edition (qualquer versão) ou para o SQL Server 2008 R2.

## <a name="site-recovery-support"></a>Suporte de recuperação de site

### <a name="supported-scenarios"></a>Cenários suportados
Recuperação de sites pode proteger o SQL Server conforme resumido na tabela.

**Cenário** | **Para um site secundário** | **Para o Azure**
--- | --- | ---
**Hyper-V** | Sim | Sim
**VMware** | Sim | Sim
**Servidor físico** | Sim | Sim

### <a name="supported-sql-server-versions"></a>Versões suportadas do SQL Server
Estas versões do SQL Server são suportados para os cenários suportados:

* SQL Server 2016 Enterprise e Standard
* SQL Server 2014 Enterprise e Standard
* SQL Server 2012 Enterprise e Standard
* SQL Server 2008 R2 Enterprise e Standard

### <a name="supported-sql-server-integration"></a>Integração do SQL Server suportada

Recuperação de sites pode ser integrada com tecnologias de SQL Server BCDR nativas resumidas na tabela, para fornecer uma solução de recuperação após desastre.

**Funcionalidade** | **Detalhes** | **SQL Server** |
--- | --- | ---
**Grupo de disponibilidade Always On** | Executam várias instâncias de autónoma do SQL Server num cluster de ativação pós-falha com vários nós.<br/><br/>Bases de dados podem ser agrupados em grupos de ativação pós-falha que podem ser copiados (espelhadas) nas instâncias do SQL Server, de modo que não é necessário nenhum armazenamento partilhado.<br/><br/>Permite a recuperação de desastre entre um site primário e um ou mais sites secundários. Dois nós podem ser configurados num partilhado nada cluster com bases de dados do SQL Server configurado num grupo de disponibilidade com replicação síncrona e ativação pós-falha automática. | SQL Server 2014 & 2012 Enterprise edition
**(Sempre no FCI) de clustering de ativação pós-falha** | SQL Server utiliza o Windows clustering de ativação pós-falha elevada disponibilidade de cargas de trabalho no local do SQL Server.<br/><br/>Nós com instâncias do SQL Server com discos partilhados estão configurados num cluster de ativação pós-falha. Se uma instância não está disponível o cluster de ativação pós-falha para outro.<br/><br/>O cluster não protege contra a falha ou falhas no armazenamento partilhado. O disco partilhado pode ser implementado com o iSCSI, canal de fibra, ou partilhados VHDXs. | Edições do SQL Server para empresas<br/><br/>SQL Server Standard edition (limitado a dois nós apenas)
**Base de dados de espelhamento (modo de segurança alta)** | Protege uma base de dados para uma única cópia secundária. Disponível em ambos os segurança elevada (síncrona) e modos de replicação (assíncrona) de elevado desempenho. Não necessita de um cluster de ativação pós-falha. | SQL Server 2008 R2<br/><br/>SQL Server Enterprise todas as edições
**Servidor do SQL autónomo** | O SQL Server e base de dados estão alojados num único servidor (físico ou virtual). Clustering do anfitrião é utilizado para elevada disponibilidade se o servidor virtual. Elevada disponibilidade não ao nível do convidado. | Edição Enterprise ou Standard

## <a name="deployment-recommendations"></a>Recomendações de implementação

Esta tabela resume o nosso recomendações para integrar tecnologias BCDR do SQL Server com a recuperação de Site.

| **Versão** | **Edição** | **Implementação** | **No local para local** | **No local para o Azure** |
| --- | --- | --- | --- | --- |
| SQL Server 2014 ou 2012 |Enterprise |Instância de cluster de ativação pós-falha |Grupos de disponibilidade Always On |Grupos de disponibilidade Always On |
|| Enterprise |Always On nos grupos de disponibilidade para elevada disponibilidade |Grupos de disponibilidade Always On |Grupos de disponibilidade Always On | |
|| Standard |Instância de cluster de ativação pós-falha (FCI) |Replicação de recuperação de site com o espelho local |Replicação de recuperação de site com o espelho local | |
|| Enterprise ou Standard |Autónomo |Replicação de recuperação de site |Replicação de recuperação de site | |
| SQL Server 2008 R2 ou 2008 |Enterprise ou Standard |Instância de cluster de ativação pós-falha (FCI) |Replicação de recuperação de site com o espelho local |Replicação de recuperação de site com o espelho local |
|| Enterprise ou Standard |Autónomo |Replicação de recuperação de site |Replicação de recuperação de site | |
| SQL Server (qualquer versão) |Enterprise ou Standard |Instância de cluster de ativação pós-falha - a aplicação de DTC |Replicação de recuperação de site |Não suportado |

## <a name="deployment-prerequisites"></a>Pré-requisitos da implementação

* Uma implementação no local do SQL Server, executar uma versão suportada do SQL Server. Normalmente, também terá de Active Directory para o SQL server.
* Os requisitos para o cenário em que pretende implementar. Saiba mais sobre os requisitos de suporte para [replicação para o Azure](site-recovery-support-matrix-to-azure.md) e [no local](site-recovery-support-matrix.md), e [os pré-requisitos de implementação](site-recovery-prereq.md).
* Para configurar a recuperação no Azure, execute o [avaliação de preparação de Máquina Virtual do Azure](http://www.microsoft.com/download/details.aspx?id=40898) ferramenta na suas máquinas virtuais do SQL Server, para se certificar de que está a compatível com o Azure e a recuperação de sites.

## <a name="set-up-active-directory"></a>Configurar o Active Directory

Configure o Active Directory, no site secundário de recuperação, para o SQL Server ser executado corretamente.

* **Enterprise pequeno**— com um pequeno número de aplicações e o controlador de domínio único para o site no local, se pretender efetuar a ativação pós-falha de todo o local, recomendamos que utilize replicação de recuperação de sites para replicar o controlador de domínio para o datacenter secundário ou para o Azure.
* **Médias e grandes empresas**— se tiver um grande número de aplicações, uma floresta do Active Directory, e pretende falhar pelo carga de trabalho ou aplicação, recomendamos que configure um controlador de domínio adicional no Centro de dados secundário, ou no Azure. Se estiver a utilizar grupos de disponibilidade Always On para recuperar a um site remoto, recomendamos que configurou o outro controlador de domínio adicionais no site secundário ou no Azure, a utilizar para a instância do SQL Server recuperada.

As instruções neste artigo presumem que um controlador de domínio está disponível na localização secundária. [Leia mais](site-recovery-active-directory.md) sobre como proteger o Active Directory com a recuperação de Site.


## <a name="integrate-with-sql-server-always-on-for-replication-to-azure"></a>Integrar com o SQL Server Always On para replicação no Azure

Eis o que precisa de fazer:

1. Importar scripts para a sua conta de automatização do Azure. Contém os scripts a ativação pós-falha o grupo de disponibilidade SQL num [máquina virtual do Resource Manager](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAG.ps1) e um [máquina de virtual clássica](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAGClassic.ps1).

    [![Implementar no Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)


1. Adicione o ASR-SQL-FailoverAG como uma ação de pré-instalação do primeiro grupo o plano de recuperação.

1. Siga as instruções disponíveis no script para criar uma variável de automatização para fornecer o nome dos grupos de disponibilidade.

### <a name="steps-to-do-a-test-failover"></a>Passos para efetuar uma ativação pós-falha de teste

SQL Always On não suporta nativamente ativação pós-falha de teste. Por conseguinte, recomendamos o seguinte:

1. Configurar [cópia de segurança do Azure](../backup/backup-azure-vms.md) na máquina virtual que aloja a réplica do grupo de disponibilidade no Azure.

1. Antes de acionar a ativação pós-falha de teste do plano de recuperação, recupere a máquina virtual a partir de cópia de segurança criada no passo anterior.

    ![Restaurar a partir de cópia de segurança do Azure ](./media/site-recovery-sql/restore-from-backup.png)

1. [Forçar um quórum](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/force-a-wsfc-cluster-to-start-without-a-quorum#PowerShellProcedure) na máquina virtual restaurada a partir de cópia de segurança.

1. Atualize o serviço de escuta IP para um IP disponível na rede de ativação pós-falha de teste.

    ![Atualizar o serviço de escuta do IP](./media/site-recovery-sql/update-listener-ip.png)

1. Colocar serviço de escuta online.

    ![Colocar serviço de escuta Online](./media/site-recovery-sql/bring-listener-online.png)

1. Crie um balanceador de carga com um que IP criado no conjunto IP de front-end correspondente a cada serviço de escuta do grupo de disponibilidade e a máquina virtual do SQL Server adicionadas no conjunto de back-end.

     ![Criar Balanceador de carga - conjunto de IP de front-end ](./media/site-recovery-sql/create-load-balancer1.png)

    ![Criar Balanceador de carga - conjunto back-end ](./media/site-recovery-sql/create-load-balancer2.png)

1. Efetue uma ativação pós-falha do plano de recuperação.

### <a name="steps-to-do-a-failover"></a>Passos para efetuar uma ativação pós-falha

Depois de ter adicionado o script no plano de recuperação e validar o plano de recuperação, ao efetuar uma ativação pós-falha de teste, pode efetuar a ativação pós-falha do plano de recuperação.


## <a name="integrate-with-sql-server-always-on-for-replication-to-a-secondary-on-premises-site"></a>Integrar com o SQL Server Always On para a replicação para um site secundário no local

Se o SQL Server estiver a utilizar grupos de disponibilidade de elevada disponibilidade (ou um FCI), recomendamos que utilize grupos de disponibilidade no site de recuperação. Tenha em atenção que isto aplica-se às aplicações que não utilizam as transações distribuídas.

1. [Configurar bases de dados](https://msdn.microsoft.com/library/hh213078.aspx) em grupos de disponibilidade.
1. Crie uma rede virtual no site secundário.
1. Configure uma ligação de VPN de site a site entre a rede virtual e o site primário.
1. Criar uma máquina virtual no site de recuperação e instalar o SQL Server no mesmo.
1. Expanda a existente Always On nos grupos de disponibilidade para a nova VM do SQL Server. Configure esta instância do SQL Server como uma cópia da réplica assíncrona.
1. Criar um serviço de escuta do grupo de disponibilidade ou atualizar o serviço de escuta existente para incluir a máquina virtual de réplica assíncrona.
1. Certifique-se de que o farm de aplicação está configurado utilizando o serviço de escuta. Se estiver a configuração de cópia de segurança utilizando o nome do servidor de base de dados, atualize-a para utilizar o serviço de escuta, por isso não terá de reconfigurar após a ativação pós-falha.

Para aplicações que utilizam as transações distribuídas, é recomendável implementar a recuperação de Site com [replicação de site para site do servidor VMware/físico](site-recovery-vmware-to-vmware.md).

### <a name="recovery-plan-considerations"></a>Considerações do plano de recuperação
1. Adicione este script de exemplo para a biblioteca do VMM, nos sites primários e secundários.

        Param(
        [string]$SQLAvailabilityGroupPath
        )
        import-module sqlps
        Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force

1. Quando cria um plano de recuperação para a aplicação, adicione uma ação de pré-instalação para o passo de grupo-1 convertidos em script, que invoca o script para efetuar a ativação pós-falha de grupos de disponibilidade.

## <a name="protect-a-standalone-sql-server"></a>Proteger um servidor de SQL Server autónomo

Neste cenário, recomendamos que utilize replicação de recuperação de sites para proteger a máquina do SQL Server. Os passos exatos dependem se do SQL Server é uma VM ou um servidor físico e se pretender replicar para o Azure ou no local secundário site. Saiba mais sobre [cenários de recuperação de Site](site-recovery-overview.md).

## <a name="protect-a-sql-server-cluster-standard-editionwindows-server-2008-r2"></a>Proteger um cluster do SQL Server (standard edition/Windows Server 2008 R2)

Para um cluster a executar o SQL Server Standard edition ou SQL Server 2008 R2, recomendamos que utilize replicação de recuperação de sites para proteger o SQL Server.

### <a name="on-premises-to-on-premises"></a>Local para local

* Se a aplicação utiliza transações distribuídas Recomendamos que implemente [recuperação de sites com replicação SAN](site-recovery-vmm-san.md) num ambiente Hyper-V, ou [servidor/físico de VMware para VMware](site-recovery-vmware-to-vmware.md) para um ambiente VMware.
* Para aplicações de DTC não, utilize a abordagem acima para recuperar o cluster como um servidor autónomo, tirando partido de uma segurança elevada local espelho da base de dados.

### <a name="on-premises-to-azure"></a>No local para Azure

Recuperação de sites não fornece convidado suporte do cluster quando replicar para o Azure. SQL Server também não fornecem uma solução de recuperação após desastre de baixo custo para a edição Standard. Neste cenário, recomendamos que proteger o cluster do SQL Server no local para um SQL Server autónomo e recuperar no Azure.

1. Configure uma instância do SQL Server autónomo adicionais no site no local.
1. Configure a instância para servir um espelho para as bases de dados que pretende proteger. Configure o espelhamento no modo de segurança elevado.
1. Configurar a recuperação de Site no site no local, para ([Hyper-V](site-recovery-hyper-v-site-to-azure.md) ou [VMware VMs/servidores físicos)](site-recovery-vmware-to-azure-classic.md).
1. Utilize replicação de recuperação de sites para replicar a nova instância do SQL Server para o Azure. Uma vez que é uma cópia de espelho de alta segurança, serão sincronizado com o cluster primário, mas será replicado para o Azure utilizando a replicação do Site Recovery.


![Cluster Standard](./media/site-recovery-sql/standalone-cluster-local.png)

### <a name="failback-considerations"></a>Considerações sobre a reativação pós-falha

Para clusters do SQL Server Standard, a reativação pós-falha após uma ativação pós-falha não planeada requer uma cópia de segurança do SQL server e o restauro, a instância de espelho para o cluster original, com reestablishment do espelhamento.

## <a name="next-steps"></a>Passos seguintes
[Saiba mais](site-recovery-components.md) sobre a arquitetura de recuperação de sites.
