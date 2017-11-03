---
title: Bases de Dados SQL (PaaS) vs SQL Server na nuvem em VMs (IaaS) | Microsoft Docs
description: "Saiba qual a opção do SQL Server na nuvem que se adequa à sua aplicação: Base de Dados SQL (PaaS) do Azure ou SQL Server na nuvem em Azure Virtual Machines."
services: sql-database, virtual-machines
keywords: Nuvem SQL Server, SQL Server na nuvem, base de dados PaaS, SQL Server na nuvem, DBaaS
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: cjgronlund
ms.assetid: 7467f422-b77d-4b60-9cb5-0f1ec17ec565
ms.service: sql-database
ms.custom: DBs & servers
ms.workload: Active
ms.tgt_pltfrm: vm-windows-sql-server
ms.devlang: na
ms.topic: article
ms.date: 02/01/2017
ms.author: carlrab
ms.openlocfilehash: 436166fcb0fa9103c6b702b63d93a0b222d536d0
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="choose-a-cloud-sql-server-option-azure-sql-paas-database-or-sql-server-on-azure-vms-iaas"></a>Escolha uma opção de SQL Server na nuvem: Base de Dados SQL (PaaS) do Azure ou SQL Server em VMs (IaaS) do Azure
O Azure tem duas opções para alojar cargas de trabalho do SQL Server no Microsoft Azure:

* [Base de Dados SQL do Azure](https://azure.microsoft.com/services/sql-database/): uma base de dados SQL nativa na nuvem, também conhecida como base de dados PaaS (plataforma como um serviço) ou DBaaS (base de dados como serviço) que está otimizada para o desenvolvimento de aplicações SaaS (software como serviço). Oferece compatibilidade com a maior parte das funcionalidades do SQL Server. Para obter mais informações sobre o PaaS, consulte [O que é o PaaS](https://azure.microsoft.com/overview/what-is-paas/).
* [SQL Server em Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/sql-server/): SQL Server instalado e alojado na nuvem em Máquinas Virtuais (VMs) do Windows Server em execução no Azure, também conhecido como uma infraestrutura como um serviço (IaaS).
  O SQL Server nas máquinas virtuais do Azure está otimizado para migrar aplicações do SQL Server existentes. Todas as versões e edições do SQL Server estão disponíveis. Oferece 100% de compatibilidade com o SQL Server, permitindo-lhe alojar tantas bases de dados quanto necessário e executar transações entre bases de dados. Oferece controlo total no SQL Server e no Windows.

Saiba como cada opção se ajusta à plataforma de dados da Microsoft e obtenha ajuda para encontrar a opção adequada aos seus requisitos comerciais. Quer dê prioridade à redução de custos ou a uma administração mínima acima de tudo o resto, este artigo pode ajudá-lo a decidir qual é a abordagem que melhor responde aos requisitos comerciais a que dá mais importância.

## <a name="microsofts-data-platform"></a>Plataforma de dados da Microsoft
Um dos primeiros aspetos a compreender em qualquer debate do Azure versus bases de dados do SQL Server no local é que pode utilizar todas. A plataforma de dados da Microsoft tira partido da tecnologia do SQL Server e disponibiliza-a em máquinas físicas no local, ambientes de nuvem privada, ambientes de nuvem privada alojada de terceiros e nuvem pública. O SQL Server nas máquinas virtuais do Azure permite-lhe responder a necessidades comerciais únicas e diversas através de uma combinação de implementações alojadas na cloud e no local, utilizando o mesmo conjunto de produtos de servidor, ferramentas de desenvolvimento e conhecimentos em todos estes ambientes.

   ![Opções do SQL Server na nuvem: SQL server em IaaS ou base de dados SQL SaaS na nuvem.](./media/sql-database-paas-vs-sql-server-iaas/SQLIAAS_SQL_Server_Cloud_Continuum.png)

Tal como ilustrado no diagrama, cada oferta pode ser caracterizada pelo nível de administração que têm sobre a infraestrutura (no eixo X) e pelo grau de eficiência de custos alcançada através da automatização e consolidação de nível de base de dados (no eixo Y).

Ao conceber uma aplicação, estão disponíveis quatro opções básicas para alojar a parte do SQL Server da aplicação:

* SQL Server em máquinas físicas não virtualizadas
* SQL Server em máquinas virtualizadas no local (nuvem privada)
* SQL Server numa Máquina Virtual do Azure (nuvem pública da Microsoft)
* Base de Dados SQL do Azure (nuvem pública da Microsoft)

Nas secções seguintes, ficará a conhecer o SQL Server na nuvem pública da Microsoft: Base de Dados SQL do Azure e SQL Server em VMs do Azure. Além disso, explorará motivadores de negócio comuns para determinar qual é a opção que funciona melhor para a sua aplicação.

## <a name="a-closer-look-at-azure-sql-database-and-sql-server-on-azure-vms"></a>Um olhar mais atento sobre a Base de Dados SQL do Azure e o SQL Server em VMs do Azure
A **Base de Dados SQL do Azure** é uma base de dados relacional como serviço (DBaaS) alojada na nuvem do Azure, que se insere nas categorias do setor *Software como serviço (SaaS)* e *Plataforma como serviço (PaaS)*. A [Base de Dados SQL](sql-database-technical-overview.md) baseia-se em hardware e software normalizado que é detido, alojado e mantido pela Microsoft. Com a Base de Dados SQL, pode desenvolver diretamente no serviço utilizando a funcionalidade e as funções incorporadas. Ao utilizar a Base de Dados SQL, paga apenas o que utilizar, dispondo de opções para aumentar verticalmente ou horizontalmente para obter um maior desempenho sem interrupções.

O **SQL Server em Máquinas Virtuais (VMs) do Azure** insere-se na categoria do setor *Infraestrutura como serviço (IaaS)* e permite executar o SQL Server dentro de uma máquina virtual na nuvem. Tal como acontece com a Base de Dados SQL, baseia-se em hardware normalizado que é detido, alojado e mantido pela Microsoft. Ao utilizar o SQL Server numa VM, pode pagar apenas o que utilizar lugar por uma licença do SQL Server já incluída numa imagem do SQL Server ou facilmente utilizar uma licença existente. Pode também facilmente dimensionar e colocar em pausa/retomar a VM conforme necessário.

Em geral, estas duas opções de SQL estão otimizadas para diferentes fins:

* A **Base de Dados SQL do Azure** está otimizada para reduzir ao mínimo os custos globais para o aprovisionamento e gestão de muitas bases de dados. Reduz os custos correntes de administração, uma vez que não é necessário gerir as máquinas virtuais, o sistema operativo ou o software de base de dados. Não é necessário gerir as atualizações, a elevada disponibilidade ou as [cópias de segurança](sql-database-automated-backups.md). Em geral, a Base de Dados SQL do Azure permite aumentar substancialmente o número de bases de dados geridas por um único recurso de TI ou de desenvolvimento.
* **O SQL Server em execução em VMs do Azure** está otimizado para migrar as aplicações existentes para o Azure ou para expandir aplicações no local existentes para a nuvem em implementações híbridas. Além disso, pode utilizar o SQL Server numa máquina virtual para desenvolver e testar aplicações do SQL Server tradicionais . Com o SQL Server em VMs do Azure, tem direitos administrativos completos sobre uma instância dedicada do SQL Server e uma VM baseada na nuvem. É uma opção perfeita quando uma organização já tem recursos de TI disponíveis para manter as máquinas virtuais. Estas capacidades permite-lhe criar um sistema altamente personalizado para fazer face aos requisitos de disponibilidade e desempenho específicos da sua aplicação.

A tabela seguinte resume as principais caraterísticas da Base de Dados SQL e do SQL Server em VMs do Azure:

| **Melhor para:** | **Base de Dados SQL do Azure** | **SQL Server numa Máquina Virtual do Azure** |
| --- | --- | --- |
|  |Novas aplicações concebidas na nuvem que têm restrições de tempo em termos de desenvolvimento e marketing. |Aplicações existentes que necessitam de migração rápida para a nuvem com alterações mínimas. Cenários de desenvolvimento e teste rápidos quando não pretende comprar hardware de SQL Server de não produção no local. |
|  | Equipas que necessitam de atualizações, recuperação após desastre e elevada disponibilidade para a base de dados. |Equipas que podem configurar e gerir a elevada disponibilidade, a recuperação após desastre e a aplicação de patches do SQL Server. Algumas das funcionalidades automatizadas fornecidas simplificam-no significativamente. | |
|  | Equipas que não pretendem gerir o sistema operativo subjacente e definições de configuração. |Precisa de um ambiente personalizado com direitos administrativos completos. | |
|  | Bases de dados de até 4 TB ou bases de dados maiores que podem ser [particionadas horizontal ou verticalmente](sql-database-elastic-scale-introduction.md#horizontal-and-vertical-scaling) utilizando um padrão de escalamento horizontal. |Instâncias do SQL Server com até 64 TB de armazenamento. A instância pode suportar tantas bases de dados quanto necessário. | |
|  | | |
| **Recursos:** |Não pretende utilizar recursos de TI para configuração e gestão da infraestrutura subjacente, mas pretende focar-se na camada da aplicação. |Tem alguns recursos de TI para a configuração e gestão. Algumas das funcionalidades automatizadas fornecidas simplificam-no significativamente. |
| **Custo total de propriedade:** |Elimina os custos de hardware e reduz os custos administrativos. |Elimina os custos com hardware. |
| **Continuidade do negócio:** |Para além das capacidades de infraestrutura de tolerância a falhas incorporadas, SQL Database do Azure fornece funcionalidades, tais como [cópias de segurança automatizadas](sql-database-automated-backups.md), [no momento restauro](sql-database-recovery-using-backups.md#point-in-time-restore), [georrestauro](sql-database-recovery-using-backups.md#geo-restore), e [georreplicação ativa](sql-database-geo-replication-overview.md) para aumentar a continuidade do negócio. Para obter mais informações, consulte [Descrição geral da continuidade de negócio da Base de Dados SQL](sql-database-business-continuity.md). |O SQL Server em VMs do Azure permite configurar uma solução de elevada disponibilidade e recuperação após desastre para as necessidades específicas da sua base de dados. Assim, poderá ter um sistema que está altamente otimizado para a sua aplicação. Pode testar e executar ativações pós-falha sempre que necessário. Para obter mais informações, consulte [Elevada Disponibilidade e Recuperação Após Desastre do SQL Server em Azure Virtual Machines](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md). |
| **Nuvem híbrida:** |A sua aplicação no local pode aceder a dados na Base de Dados SQL do Azure. |Com o SQL Server em VMs do Azure, pode ter aplicações que são executadas parcialmente na nuvem e parcialmente no local. Por exemplo, pode expandir a sua rede no local e o Domínio do Active Directory para a nuvem através da [Rede Virtual do Azure](../virtual-network/virtual-networks-overview.md). Além disso, pode armazenar ficheiros de dados no local no Armazenamento do Azure, utilizando [Ficheiros de Dados do SQL Server no Azure](http://msdn.microsoft.com/library/dn385720.aspx). Para obter mais informações, consulte [Introdução à Nuvem Híbrida do SQL Server 2014](http://msdn.microsoft.com/library/dn606154.aspx). |
|  | Suporta a [replicação transacional do SQL Server](https://msdn.microsoft.com/library/mt589530.aspx) como um subscritor para replicar os dados. |Suporta totalmente a [replicação transacional do SQL Server](https://msdn.microsoft.com/library/mt589530.aspx), [Grupos de Disponibilidade AlwaysOn](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md), Serviços de Integração e Envio de Registo para replicar dados. Além disso, as cópias de segurança do SQL Server tradicionais são totalmente suportadas | |
|  | | |

## <a name="business-motivations-for-choosing-azure-sql-database-or-sql-server-on-azure-vms"></a>Motivações comerciais para escolher a Base de Dados SQL do Azure ou o SQL Server em VMs do Azure
### <a name="cost"></a>Custo
Quer seja uma start-up com problemas de liquidez ou uma equipa numa empresa estabelecida que opera com grandes restrições orçamentais, o financiamento limitado é, muitas vezes, o principal fator quando tem de decidir como alojar as suas bases de dados. Nesta secção, aprenderá primeiro as noções básicas do licenciamento e da faturação no Azure com respeito a estas duas opções de base de dados relacional: Base de Dados SQL e SQL Server em VMs do Azure. Também podem aprender a calcular o custo total da aplicação.

#### <a name="billing-and-licensing-basics"></a>Noções básicas de faturação e licenciamento
A **Base de dados SQL** é vendida aos clientes como um serviço, não com uma licença.  O [SQL Server em VMs do Azure](../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md) é vendido com uma licença incluída pagada por minuto. Se tiver uma licença existente, também pode utilizá-la.  

Atualmente, a **Base de Dados SQL** está disponível em vários escalões de serviço, sendo todas eles faturados à hora a um preço fixo com base no escalão de serviço e no nível de desempenho que escolher. Além disso, é-lhe faturado o tráfego de Internet de saída a [taxas de transferência de dados](https://azure.microsoft.com/pricing/details/data-transfers/) regulares. Os escalões de serviço Básico, Standard, Premium e Premium RS foram concebidos para fornecer um desempenho previsível, com vários níveis de desempenho para fazer face aos requisitos de pico da sua aplicação. Pode alternar entre escalões de serviços e níveis de desempenho para fazer face às necessidades variadas de débito da sua aplicação. Se a base de dados tiver um elevado volume transacional e tiver de suportar muitos utilizadores em simultâneo, recomendamos o escalão de serviço Premium. Para obter as informações mais recentes sobre os atuais escalões de serviço suportados, consulte o artigo [Escalões de Serviço da Base de Dados SQL do Azure](sql-database-service-tiers.md). Também pode criar [conjuntos elásticos](sql-database-elastic-pool.md) para partilhar recursos de desempenho entre instâncias da base de dados.

Com a **Base de Dados SQL**, o software de base de dados é automaticamente configurado, corrigido e atualizado pela Microsoft, o que reduz os custos de administração. Além disso, as capacidades de [cópia de segurança incorporada](sql-database-automated-backups.md) permitem-lhe obter uma redução significativa de custos, sobretudo quando tem um número elevado de bases de dados.

Com o **SQL Server em VMs do Azure**, pode utilizar qualquer uma das imagens do SQL Server fornecidas pela plataforma (que inclui uma licença) ou utilizar a sua licença do SQL Server. Todas as versões (2008R2, 2012, 2014, 2016) e edições (programador, rápida, Web, Standard, Enterprise) do SQL Server suportadas estão disponíveis. Além disso, as versões Traga a sua própria licença (BYOL) das imagens estão disponíveis. Quando utiliza as imagens fornecidas pelo Azure, o custo operacional depende do tamanho da VM e a edição do SQL Server que escolher. Independentemente do tamanho da VM ou da edição do SQL Server, paga um custo de licenciamento por minuto do SQL Server e do Windows Server, juntamente com o custo do Armazenamento do Azure para os discos da VM. A opção de faturação por minuto permite-lhe utilizar o SQL Server durante o tempo que precisar, sem comprar licenças adicionais do SQL Server. Se trouxer a sua licença do SQL Server para o Azure, são-lhe cobrados apenas os custos de armazenamento e do Windows Server. Para obter mais informações sobre o modelo «traga a sua própria licença», consulte o artigo [Mobilidade de Licenças através do Software Assurance no Azure](https://azure.microsoft.com/pricing/license-mobility/).

#### <a name="calculating-the-total-application-cost"></a>Calcular o custo total da aplicação
Quando começar a utilizar uma plataforma em nuvem, o custo de execução da sua aplicação inclui os custos de desenvolvimento e de administração, mais os custos de serviço da plataforma em nuvem pública.

Eis o cálculo do custo detalhado para a sua aplicação em execução na Base de Dados SQL Server e no SQL Server em VMs do Azure:

**Ao utilizar a Base de Dados SQL do Azure:**

*Custo total da aplicação = custos de administração altamente minimizados + custos de desenvolvimento de software + custos do serviço Base de Dados SQL*

**Ao utilizar o SQL Server em VMs do Azure:**

*Custo total da aplicação = custo altamente minimizado de desenvolvimento/modificação de software + custos de administração + custos de licenciamento do SQL Server e do Windows Server + custos do Armazenamento do Azure*

Para obter mais informações sobre preços, consulte os seguintes recursos:

* [Preços de Base de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/)
* [Preços de Máquina Virtual](https://azure.microsoft.com/pricing/details/virtual-machines/) para [SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#sql) e para [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#windows)
* [Calculadora de Preços do Azure](https://azure.microsoft.com/pricing/calculator/)

> [!NOTE]
> Existe um pequeno subconjunto de funcionalidades no SQL Server que não são aplicáveis ou não estão disponíveis com a Base de Dados SQL. Consulte os artigos [Funções da Base de Dados SQL](sql-database-features.md) e [Informações sobre Transact-SQL da Base de Dados SQL](sql-database-transact-sql-information.md) para obter mais informações. Se estiver a mover uma solução SQL Server existente para a nuvem, consulte o artigo [Migrar uma base de dados do SQL Server para a Base de Dados SQL do Azure](sql-database-cloud-migrate.md). Quando migra uma aplicação do SQL Server no local existente para a Base de Dados SQL, considere atualizar a aplicação para tirar partido das capacidades que os serviços em nuvem oferecem. Por exemplo, pode considerar utilizar o [Serviço de Aplicações Web do Azure](https://azure.microsoft.com/services/app-service/web/) ou os [Cloud Services do Azure](https://azure.microsoft.com/services/cloud-services/) para alojar a camada da aplicação para aumentar os benefícios de custos.
> 
> 

### <a name="administration"></a>Administração
Para muitas empresas, a decisão de fazer a transição para um serviço em nuvem passa tanto por descartar a complexidade de administração como pelo custo. Com a **Base de dados SQL**, a Microsoft administra o hardware subjacente. A Microsoft replica automaticamente todos os dados para proporcionar elevada disponibilidade, configura e atualiza o software de base de dados, gere o balanceamento de carga e efetua uma ativação pós-falha transparente se existir uma falha do servidor. Pode continuar a administrar a sua base de dados, mas já não necessita de gerir o motor de base de dados, o sistema operativo do servidor ou o hardware.  Exemplos de itens que pode continuar a administrar incluem bases de dados e inícios de sessão, otimização de índices e consultas, e auditoria e segurança.

Com o **SQL Server em VMs do Azure**, tem controlo total sobre o sistema operativo e a configuração da instância do SQL Server. Com uma VM, cabe-lhe a si decidir quando atualizar o sistema operativo e o software de base de dados e quando instalar software adicional, tal como o antivírus. Algumas funcionalidades automatizadas são fornecidas para simplificar significativamente a aplicação de patches, a cópia de segurança e a elevada disponibilidade. Além disso, pode controlar o tamanho da VM, o número de discos e as respetivas configurações de armazenamento. O Azure permite-lhe alterar o tamanho de uma VM conforme necessário. Para obter informações, consulte o artigo [Máquina Virtual e Tamanhos do Serviço em Nuvem do Azure](../virtual-machines/windows/sizes.md). 

### <a name="service-level-agreement-sla"></a>Contrato de Nível de Serviço (SLA)
Para muitos departamentos de TI, cumprir as obrigações de tempo de atividade de um Contrato de Nível de Serviço (SLA) é uma prioridade máxima. Nesta secção, vamos ver que SLA se aplica a cada opção de alojamento de base de dados.

Para os escalões de serviço Básico, Standard, Premium e Premium RS da **Base de Dados SQL**, a Microsoft fornece um SLA de 99,99% de disponibilidade. Para obter as informações mais recentes, consulte [Contrato de Nível de Serviço](https://azure.microsoft.com/support/legal/sla/sql-database/). Para obter as informações mais recentes sobre os escalões de serviço da Base de Dados SQL e os planos de continuidade do negócio suportados, consulte [Escalões de serviço](sql-database-service-tiers.md).

Para o **SQL Server em execução em VMs do Azure**, a Microsoft fornece um SLA de 99,95% de disponibilidade, que abrange apenas a Máquina Virtual. Este SLA não abrange os processos (como o SQL Server) em execução na VM e requer que aloje, pelo menos, duas instâncias de VM num conjunto de disponibilidade. Para obter as informações mais recentes, consulte o [SLA de VM](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Para elevada disponibilidade da base de dados dentro das VMs, deve configurar uma das opções de elevada disponibilidade suportadas no SQL Server, como os [Grupos de Disponibilidade AlwaysOn](http://blogs.technet.com/b/dataplatforminsider/archive/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery.aspx). Utilizar uma opção de elevada disponibilidade suportadas não fornece um SLA adicional, mas permite-lhe alcançar uma disponibilidade de base de dados > 99,99%.

### <a name="market"></a>Tempo de colocação no mercado
A **Base de Dados SQL** é a solução certa para aplicações desenvolvidas para a nuvem quando a produtividade do programador e o tempo rápido de colocação no mercado são fatores essenciais. Com funcionalidade semelhante a DBA programática, é perfeita para programadores e arquitetos de nuvem, uma vez que reduz a necessidade de gerir a base de dados e o sistema operativo subjacentes. Por exemplo, pode utilizar a [API REST](http://msdn.microsoft.com/library/azure/dn505719.aspx) e [Cmdlets do PowerShell](http://msdn.microsoft.com/library/mt740629.aspx) para automatizar e gerir as operações administrativas em milhares de bases de dados. Funcionalidades como os [conjuntos elásticos](sql-database-elastic-pool.md) permitem-lhe focar-se na camada da aplicação e apresentar a sua solução ao mercado mais rapidamente.

O **SQL Server em execução em VMs do Azure** é perfeito se as suas aplicações existentes ou novas necessitam grandes bases de dados, bases de dados inter-relacionaras, ou acesso a todas as funcionalidades do SQL Server ou do Windows. Também é uma boa opção se pretender migrar bases de dados e aplicações no local existentes para o Azure tal como estão. Uma vez que não precisa de alterar as camadas de apresentação, de aplicação e de dados, poupa tempo e orçamento na reformulação da sua solução existente. Em vez disso, pode concentrar-se na migração de todas as suas soluções para o Azure e na execução de algumas otimizações de desempenho que possam ser exigidas pela plataforma do Azure. Para obter mais informações, consulte o artigo [Melhores Práticas de Desempenho para o SQL Server em Azure Virtual Machines](../virtual-machines/windows/sql/virtual-machines-windows-sql-performance.md).

## <a name="summary"></a>Resumo
Este artigo explorou a Base de Dados SQL e o SQL Server em Máquinas Virtuais (VMs) do Azure e abordou os motivadores de negócio comuns que poderão influenciar a sua decisão. Segue-se um resumo das sugestões que deve considerar:

Opte pela **Base de Dados SQL do Azure** se:

* Está a criar novas aplicações baseadas na nuvem para tirar partido das reduções de custos e da otimização de desempenho que os serviços em nuvem proporcionam. Esta abordagem proporciona as vantagens de um serviço em nuvem completamente gerido, ajuda a diminuir o tempo de colocação no mercado inicial e pode fornecer otimização de custos a longo prazo.
* Pretende que seja a Microsoft a efetuar as operações de gestão comuns nas suas bases de dados e precisa de SLAs de maior disponibilidade para as bases de dados.

Opte pelo **SQL Server em VMs do Azure** se:

* Tiver aplicações no local que pretende migrar ou expandir para a nuvem, ou se pretender criar aplicações da empresa com mais de 4 TB. Esta abordagem oferece o benefício de compatibilidade SQL de 100%, capacidade de base de dados de grandes dimensões, controlo total sobre o SQL Server e sobre o Windows e túnel seguro no local. Esta abordagem minimiza os custos de programação e de modificação de aplicações existentes.
* tiver os recursos de TI existentes e pode, em última análise, se proprietário da aplicação de patches, de cópias de segurança e de base de dados de elevada disponibilidade. Repare que algumas funcionalidades automatizadas simplificam significativamente estas operações. 

## <a name="next-steps"></a>Passos seguintes
* Consulte [A sua primeira Base de Dados SQL do Azure](sql-database-get-started-portal.md) para começar a utilizar a Base de Dados SQL.
* Consulte [Preços de Base de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/).
* Consulte [Aprovisionar uma máquina virtual do SQL Server no Azure](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision.md) para começar a utilizar o SQL Server em VMs do Azure.

