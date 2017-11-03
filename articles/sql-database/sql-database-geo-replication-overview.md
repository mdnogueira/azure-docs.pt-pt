---
title: "Ativação pós-falha de grupos e a georreplicação ativa - SQL Database do Azure | Microsoft Docs"
description: "Utilizar grupos de ativação de pós-falha automática com georreplicação ativa e ativar a ativação pós-falha de autoomatic na eventualidade de ocorrer uma falha."
services: sql-database
documentationcenter: na
author: anosov1960
manager: jhubbard
editor: monicar
ms.assetid: 2a29f657-82fb-4283-9a83-e14a144bfd93
ms.service: sql-database
ms.custom: business continuity
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Active
ms.date: 10/11/2017
ms.author: sashan
ms.openlocfilehash: ef9463e464928b8fa8e64019037a41711cb77830
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="overview-failover-groups-and-active-geo-replication"></a>Descrição geral: Grupos de ativação pós-falha e a georreplicação ativa
Replicação geográfica activa permite-lhe configurar até quatro legíveis secundários bases de dados em localizações de centro de dados idêntica ou diferente (regiões). Bases de dados secundárias estão disponíveis para consulta e de ativação pós-falha se existir uma falha do Centro de dados ou a impossibilidade de estabelecer ligação à base de dados primária. A ativação pós-falha tem de ser iniciada manualmente a aplicação do utilizador. Após a ativação pós-falha, a nova principal tem um ponto final de ligação diferente. 

> [!NOTE]
> Replicação geográfica activa está disponível para todas as bases de dados em todos os escalões de serviço em todas as regiões.
>  

Grupos de auto-ativação pós-falha de base de dados SQL do Azure (na pré-visualização) é uma funcionalidade de base de dados do SQL Server foi concebida para gerir automaticamente de relação de replicação geográfica, conectividade e ativação pós-falha à escala. Com o mesmo, os clientes obtêm a capacidade de recuperar automaticamente várias bases de dados relacionados na região secundária depois de falhas regionais catastrófica ou outros eventos de não planeados resultam na perda total ou parcial de disponibilidade do serviço de base de dados do SQL Server na região primária. Além disso, podem utilizar as bases de dados secundárias legíveis para descarregamento de cargas de trabalho só de leitura.  Porque os grupos de ativação de pós-falha automática envolvem várias bases de dados, tem de ser configurados no servidor primário. Os servidores primários e secundários têm de estar na mesma subscrição. Grupos de auto-ativação pós-falha suportam a replicação de todas as bases de dados no grupo de apenas um servidor secundário numa região diferente. Replicação geográfica activa, sem grupos de ativação de pós-falha automática, permite até quatro bases de dados secundárias em qualquer região.

Se estiver a utilizar replicação geográfica activa e para qualquer pelo motivo da falha da base de dados primária ou simplesmente tem de ser colocada offline, pode iniciar a ativação pós-falha para qualquer uma das suas bases de dados secundárias. Quando a ativação pós-falha se encontra ativada para uma das bases de dados secundárias, todas as outras bases de dados secundárias são automaticamente associados a nova principal. Se estiver a utilizar grupos de auto-ativação pós-falha (em pré-visualização) para gerir a recuperação de base de dados e qualquer falha que afeta uma ou várias das bases de dados nos resultados de grupo na ativação pós-falha automática. Pode configurar a política de ativação de pós-falha automática que melhor se adeque às necessidades da sua aplicação, ou pode ativamente por não participar e utilizar a ativação manual. Além disso, os grupos de auto-ativação pós-falha (em pré-visualização) fornecem leitura / escrita e pontos finais de escuta de só de leitura que permanecem inalterados durante as ativações pós-falha. Se utilizar a ativação de ativação pós-falha manual ou automática, ativação pós-falha comutadores todas as bases de dados secundárias no grupo principal. Uma vez concluída a ativação pós-falha da base de dados, o registo DNS é atualizado automaticamente para redirecionar os pontos finais para a região de novo.

Pode gerir a replicação e ativação pós-falha de uma base de dados individual ou um conjunto de bases de dados num servidor ou num agrupamento elástico com georreplicação ativa. Pode fazê-lo que utilizar 

- O [portal do Azure](sql-database-geo-replication-portal.md)
- [PowerShell: Base de dados individual](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
- [PowerShell: Agrupamento elástico](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
- [PowerShell: Grupo de ativação pós-falha](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md)
- [Transact-SQL: Base de dados individual ou agrupamento elástico](/sql/t-sql/statements/alter-database-azure-sql-database)
- [API de REST: Base de dados individual](/rest/api/sql/replicationlinks/failover)
- [API de REST: Grupo de ativação pós-falha](/rest/api/sql/failovergroups/failover). 
 
Após a ativação pós-falha, certifique-se de que os requisitos de autenticação para o servidor e base de dados estão configurados na nova principal. Para obter mais informações, consulte [segurança da base de dados do SQL Server após a recuperação de desastre](sql-database-geo-replication-security-config.md). Isto aplica-se a georreplicação e ativação de pós-falha automaticamente grupos de Active Directory (em pré-visualização).

Replicação geográfica activa tira partido do [Always On](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) tecnologia do SQL Server para replicar as transações consolidadas na base de dados primária de forma assíncrona para uma base de dados secundária utilizando ler o isolamento de instantâneo consolidado (RCSI). Os grupos de auto-ativação pós-falha fornecem a semântica de grupo em cima de replicação geográfica activa, mas é utilizado o mesmo mecanismo de replicação assíncrona. Enquanto em qualquer momento de determinado, a base de dados secundária poderão ser ligeiramente atrás de base de dados primária, os dados secundários são garantidos nunca ter transações parciais. Redundância por várias regiões permite que aplicações rapidamente recuperar a partir de uma perda permanente de todo o datacenter ou partes de um datacenter causado por perante desastres naturais, erros humanos catastrófica ou atos maliciosos. Os dados específicos do RPO podem ser encontrados em [descrição geral da continuidade do negócio](sql-database-business-continuity.md).

A figura seguinte mostra um exemplo de replicação geográfica activa configurado com um site primário na região Norte Central-nos e secundários na região Sul Central-nos.

![relação de replicação geográfica](./media/sql-database-active-geo-replication/geo-replication-relationship.png)

Dado que as bases de dados secundárias legíveis, pode ser utilizados para efetuar a descarga de cargas de trabalho só de leitura como tarefas de relatório. Se estiver a utilizar replicação geográfica activa, é possível criar a base de dados secundária na mesma região com o site primário, mas não aumenta maior resiliência a aplicação às falhas catastrófica. Se estiver a utilizar grupos de auto-ativação pós-falha (em pré-visualização), a base de dados secundária é sempre criado numa região diferente.

Para além de desastre recuperação georreplicação ativa pode ser utilizada nos seguintes cenários:

* **Base de dados de migração**: pode utilizar a georreplicação ativa para migrar uma base de dados de um servidor para outro online com o período de indisponibilidade mínimo.
* **As atualizações de aplicações**: pode criar um secundário adicional como uma cópia de back-falhar durante as atualizações de aplicações.

Para alcançar a continuidade do negócio real, a adição de redundância de base de dados entre centros de dados é apenas uma parte da solução. Recuperar uma aplicação (serviço) ponto-a-ponto após uma falha catastrófica necessita de recuperação de todos os componentes que constituem o serviço e quaisquer serviços dependentes. Exemplos destes componentes incluem o software de cliente (por exemplo, um browser com uma JavaScript personalizado), extremidades web de front-, armazenamento e DNS. É fundamental que todos os componentes são resilientes a falhas de mesmo e ficam disponíveis dentro do objetivo de tempo de recuperação (RTO) da sua aplicação. Por conseguinte, tem de identificar todos os serviços dependentes e compreender as garantias e capacidades que fornecem. Em seguida, tem de colocar os passos adequados para garantir que as suas funções de serviço durante a ativação pós-falha dos serviços do qual depende. Para obter mais informações sobre soluções de conceção para recuperação após desastre, consulte [conceber soluções de nuvem de recuperação de desastre utilizando replicação geográfica activa](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

## <a name="active-geo-replication-capabilities"></a>Capacidades de replicação geográfica activa
A funcionalidade replicação geográfica activa fornece as seguintes capacidades essenciais:
* **Replicação assíncrona automática**: só pode criar uma base de dados secundária ao adicionar a base de dados existente. O elemento secundário pode ser criado em qualquer servidor da SQL Database do Azure. Depois de criado, a base de dados secundária é preenchido com os dados copiados a partir da base de dados primária. Este processo é conhecido como o seeding. Depois de base de dados secundária foi criado e pré-propagadas, as atualizações à base de dados primária são no modo assíncrono replicadas na base de dados secundária automaticamente. Replicação assíncrona significa que transações são consolidadas na base de dados primária replicar a base de dados secundária. 
* **Bases de dados secundárias legíveis**: uma aplicação pode aceder a uma base de dados secundária para operações só de leitura, utilizando os princípios de segurança idêntica ou diferente utilizados para aceder à base de dados primária. As bases de dados secundárias operam em modo de isolamento do instantâneo para garantir que a replicação das atualizações dos principais (reprodução de registo) não está atrasada pelo consultas executadas no secundário.

   > [!NOTE]
   > A reprodução de registo está atrasada na base de dados secundária se existem atualizações do esquema principal. A última opção necessita de um bloqueio de esquema de base de dados secundária. 
   > 

* **Várias bases de dados secundárias legíveis**: dois ou mais bases de dados secundárias aumentam a redundância e nível de proteção para a base de dados primária e a aplicação. Se existirem várias bases de dados secundárias, a aplicação permanece protegida mesmo se uma das bases de dados secundárias falhar. Se existir apenas uma base de dados secundária, e este falhar, a aplicação está exposta ao risco mais elevado, até que seja criada uma nova base de dados secundária.

   > [!NOTE]
   > Se estiver a utilizar para criar uma aplicação distribuída global e tem de fornecer acesso só de leitura aos dados em mais de quatro segions georreplicação ativa, pode criar secundária de uma secundária (um processo conhecido como encadeamento). Desta forma pode alcançar praticamente ilimitada escala de replicação de base de dados. Além disso, o encadeamento reduz a sobrecarga da replicação de base de dados primária. O compromisso é o desfasamento de aumento nas bases de dados mais folha secundários. 
   >

* **Suporte de bases de dados do conjunto elástico**: georreplicação ativa pode ser configurada para uma base de dados em qualquer conjunto elástico. A base de dados secundária pode ser noutro conjunto elástico. Para bases de dados normais, secundário pode ser uma se elástico de agrupamento e vice-versa desde que os escalões de serviço são os mesmos. 
* **Nível de desempenho configurável da base de dados secundária**: bases de dados primários e secundários são necessários para ter a mesma camada de serviço (básica, Standard, Premium). Uma base de dados secundária pode ser criado com o nível de desempenho inferior (DTUs) ao principal. Esta opção não é recomendada para aplicações com a atividade de escrita da base de dados elevada porque o desfasamento de aumento aumenta o risco de perda de dados substanciais após uma ativação pós-falha. Além disso, após a ativação pós-falha desempenho da aplicação é afetado até que a nova principal é atualizado para um nível de desempenho superior. O gráfico de percentagem de e/s de registo no portal do Azure fornece uma boa forma de estimar o nível de desempenho mínima de secundário que é necessário para Suster a carga de replicação. Por exemplo, se a base de dados primária é P6 (1000 DTU) e o percentagem de e/s de registo corresponde a 50% secundário tem de ser, pelo menos, P4 (500 DTU). Também pode obter os dados de e/s de registo utilizando [resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) ou [sys.dm db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) vistas da base de dados.  Para obter mais informações sobre os níveis de desempenho de base de dados SQL, consulte [opções de base de dados SQL e desempenho](sql-database-service-tiers.md). 
* **Ativação pós-falha controlado de utilizador e a reativação pós-falha**: uma base de dados secundária pode explicitamente mudar para a função primária em qualquer altura, a aplicação ou o utilizador. Durante uma falha real a opção "não planeada" deve ser utilizada, que promove imediatamente uma secundária para ser o primário. Quando o principal falha recupera e esteja novamente disponível, o sistema marca primário como um elemento secundário recuperado automaticamente e colocá-lo com a nova principal. Devido à natureza assíncrona de replicação, uma pequena quantidade de dados pode ser perdida durante as ativações pós-falha não planeadas se falhar de um site primário antes de ser replicado as alterações mais recentes para o secundário. Quando um site primário com várias bases de dados secundárias foi falhar, o sistema é automaticamente reconfigura as relações de replicação e liga as restantes secundárias primário recentemente promovido sem exigir qualquer intervenção do utilizador. Após a interrupção que causou a ativação pós-falha é atenuada, pode ser preferível para devolver a aplicação para a região primária. Para tal, o comando de ativação pós-falha deve ser invocado com a opção "planeada". 
* **Manter sincronizadas as credenciais e regras de firewall**: Recomendamos que utilize [regras de firewall de base de dados](sql-database-firewall-configure.md) para georreplicação bases de dados para estas regras podem ser replicadas com a base de dados para garantir que todas as bases de dados secundários tem as mesmas regras de firewall como principal. Esta abordagem elimina a necessidade dos clientes manualmente, configurar e manter as regras de firewall nos servidores que alojam as bases de dados primários e secundários. Da mesma forma, [continha os utilizadores de base de dados](sql-database-manage-logins.md) para dados de acesso garante bases de dados principais e secundários têm sempre as mesmas credenciais de utilizador, pelo que durante uma ativação pós-falha, não existe nenhum interrupções devido a correspondência com inícios de sessão e palavras-passe. Com a adição de [do Azure Active Directory](../active-directory/active-directory-whatis.md), os clientes podem gerir o acesso de utilizador para bases de dados primários e secundários e elimina a necessidade de gerir as credenciais de bases de dados completamente.

## <a name="auto-failover-group-capabilities"></a>Capacidades do grupo de ativação de pós-falha automática

Funcionalidade de grupos de ativação de pós-falha automática fornece uma abstração de elevado desempenho de replicação geográfica activa por suportar a replicação de nível de grupo e a ativação pós-falha automática. Além disso, remove a necessária para alterar a cadeia de ligação do SQL Server após a ativação pós-falha, fornecendo os pontos finais de escuta adicionais. 

* **Grupo de ativação pós-falha**: um ou vários grupos de ativação pós-falha podem ser criados entre dois servidores em regiões diferentes (servidores primários e secundários). Cada grupo pode incluir uma ou várias bases de dados que são recuperados como uma unidade no caso de todos os ou algumas bases de dados principais ficam indisponíveis devido a uma falha na região primária.  
* **Servidor primário**: um servidor que aloja as bases de dados primárias do grupo de ativação pós-falha.
* **Servidor secundário**: um servidor que aloja as bases de dados secundárias no grupo de ativação pós-falha. O servidor secundário não pode estar na mesma região que o servidor principal.
* **Adicionar bases de dados ao grupo de ativação pós-falha**: pode colocar várias bases de dados dentro de um servidor ou dentro de um conjunto elástico no mesmo grupo de ativação pós-falha. Se adicionar uma base de dados autónomo para o grupo, este cria automaticamente uma base de dados secundário utilizando a mesma edição e o nível de desempenho. Se a base de dados primária está num conjunto elástico, secundário é criado automaticamente no agrupamento elástico com o mesmo nome. Se adicionar uma base de dados que já tem uma base de dados secundária no servidor secundário, esse georreplicação é herdada pelo grupo.

   > [!NOTE]
   > Ao adicionar uma base de dados que já tem uma base de dados secundária no servidor que não faz parte do grupo de ativação pós-falha, é criado um novo secundário no servidor secundário. 
   >

* **Serviço de escuta de leitura-escrita de grupo de ativação pós-falha**: registo CNAME no DNS A formados como  **&lt;nome do grupo de ativação pós-falha&gt;. database.windows.net** que aponta para o atual URL do servidor primário. Permite que as aplicações do SQL Server de leitura e escrita para restabelecem a ligação à base de dados principal quando primário alterado após a ativação pós-falha. 
* **Escuta de só de leitura do grupo de ativação pós-falha**: registo CNAME no DNS A formados como  **&lt;nome do grupo de ativação pós-falha&gt;. secondary.database.windows.net** que aponta para o URL do servidor secundário. Permite que as aplicações de SQL só de leitura liguem de forma transparente a base de dados secundária a utilizar as regras de balanceamento de carga especificadas. Opcionalmente, pode especificar se pretende que o tráfego de só de leitura para ser redirecionado automaticamente para o servidor primário quando o servidor secundário não está disponível.
* **Política de ativação pós-falha automática**: por predefinição, o grupo de ativação pós-falha está configurado com uma política de ativação pós-falha automática. O sistema aciona uma ativação pós-falha, assim que a falha é detetada. Se pretender controlar o fluxo de trabalho de ativação pós-falha da aplicação, pode desativar a ativação pós-falha automática. 
* **Ativação pós-falha manual**: podem iniciar manualmente ativação pós-falha em qualquer altura, independentemente da configuração de ativação pós-falha automática. Se a política de ativação pós-falha automática não estiver configurada, a ativação pós-falha manual é necessário para recuperar bases de dados no grupo de ativação pós-falha. Pode iniciar a ativação pós-falha forçada ou amigável (com a sincronização de dados completa). A última opção pode ser utilizado para reposicionar servidor ativo para a região primária. Quando concluir a ativação pós-falha, os registos DNS são automaticamente atualizados para garantir a conectividade ao servidor correto.
* **Período de tolerância a perdas de dados**: porque as bases de dados primários e secundários são sincronizados utilizando a replicação assíncrona, a ativação pós-falha pode resultar na perda de dados. Pode personalizar a política de ativação pós-falha automática para refletir a tolerância à perda de dados da sua aplicação. Ao configurar **GracePeriodWithDataLossHours**, pode controlar quanto o sistema tem de aguardar antes de iniciar a ativação pós-falha que é provável que a perda de dados de resultado. 

   > [!NOTE]
   > Quando o sistema Deteta que o grupo de bases de dados ainda estão online (por exemplo, a interrupção apenas afetado plane de controlo do serviço), ativa imediatamente a ativação pós-falha com a sincronização de dados completa (amigável ativação pós-falha) independentemente do valor definido pelo **GracePeriodWithDataLossHours**. Este comportamento garante que não há nenhuma perda de dados durante a recuperação. O período de tolerância entra em vigor apenas quando uma ativação pós-falha amigável não é possível. Se a interrupção mitigada antes do período de tolerância expirar, a ativação pós-falha não se encontra ativada.
   >

* **Vários grupos de ativação pós-falha**: pode configurar vários grupos de ativação pós-falha para o mesmo par de servidores para controlar a escala de ativações pós-falha. Cada grupo de ativação pós-falha independentemente. Se a aplicação multi-inquilino utiliza conjuntos elásticos, pode utilizar esta capacidade para misturar bases de dados primários e secundários em cada agrupamento. Desta forma pode reduzir o impacto de indisponibilidade para apenas metade dos inquilinos.

## <a name="best-practices-of-building-highly-available-service"></a>Melhores práticas de criação do serviço de elevada disponibilidade

Para criar um serviço de elevada disponibilidade que utiliza a base de dados SQL do Azure, deve seguir estas diretrizes:

- **Grupo de ativação pós-falha de utilização**: um ou vários grupos de ativação pós-falha podem ser criados entre dois servidores em regiões diferentes (servidores primários e secundários). Cada grupo pode incluir uma ou várias bases de dados que são recuperados como uma unidade no caso de todos os ou algumas bases de dados principais ficam indisponíveis devido a uma falha na região primária. O grupo de ativação pós-falha cria uma base de dados de georreplicação secundária com o objetivo de serviço mesmo como principal. Se adicionar uma relação de replicação geográfica existente para o grupo de ativação pós-falha, certifique-se a que georreplicação-secundário está configurado com o objetivo de nível de serviço mesmo como principal.
- **Utilizar o serviço de escuta de leitura e escrita para a carga de trabalho OLTP**: quando executar operações de OLTP, utilize  **&lt;nome do grupo de ativação pós-falha&gt;. database.windows.net** que o servidor do URL e as ligações são automaticamente direcionado para o site primário. Este URL não é alterado após a ativação pós-falha.  
Tenha em atenção de que a ativação pós-falha envolve a atualizar que o registo DNS para que ligações de clientes são redirecionadas para a nova principal apenas depois do cliente de DNS cache é atualizado.
- **Utilize o serviço de escuta de só de leitura para a carga de trabalho só de leitura**: Se tiver uma logicamente isolada só de leitura a carga de trabalho é tolerantes a determinados vinculada de dados, pode utilizar a base de dados secundária na aplicação. Para sessões de só de leitura, utilize  **&lt;nome do grupo de ativação pós-falha&gt;. secondary.database.windows.net** como o servidor o URL e a ligação é automaticamente direcionado para o secundário. Também é recomendável que indicar na cadeia de ligação ler intenção utilizando **ApplicationIntent ReadOnly de =**. 
- **Preparado para degradação do desempenho**: decisão de ativação pós-falha do SQL Server é independente do resto da aplicação ou outros serviços utilizado. A aplicação pode ser "misto" com alguns componentes numa região e alguns noutra. Para evitar a degradação, certifique-se a implementação da aplicação redundante na região DR e siga as diretrizes neste artigo.  
Tenha em atenção a aplicação na região DR não tem de utilizar uma cadeia de ligação diferente.  
- **Preparar para a perda de dados**: se for detetada uma falha, o SQL Server aciona automaticamente ativação pós-falha de leitura e escrita se houver perda de dados para o melhor dos nossos dados de conhecimento. Caso contrário, aguarda para o período especificado pelo **GracePeriodWithDataLossHours**. Se tiver especificado **GracePeriodWithDataLossHours**, estar preparadas para a perda de dados. Em geral, durante a falhas, Azure favorece a disponibilidade. Se não é possível suportar a perda de dados, certifique-se de que define **GracePeriodWithDataLossHours** para um número suficientemente grande, por exemplo, 24 horas. 

> [!IMPORTANT]
> Conjuntos elásticos com DTUs 800 ou menos e mais de 250 bases de dados utilizando a georreplicação podem encontrar problemas, incluindo já as ativações pós-falha planeadas e degradação do desempenho.  Estes problemas estejam mais predispostos a ocorrer para cargas de trabalho que consomem muita de escrita, quando pontos finais de georreplicação amplamente são separados por geografia, ou quando são utilizados vários pontos finais secundários para cada base de dados.  Sintomas estes problemas são indicadas quando aumenta o desfasamento de georreplicação ao longo do tempo.  Este atraso pode ser monitorizado com [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database).  Se ocorrerem estes problemas, em seguida, mitigações incluem aumento do número de DTUs do conjunto ou reduzir o número de bases de dados georreplicação no mesmo conjunto.

## <a name="upgrading-or-downgrading-a-primary-database"></a>Atualizar ou desatualização de uma base de dados primária
Pode atualizar ou mudar de uma base de dados primária para um nível de desempenho diferentes (dentro da mesma camada de serviço) sem desligar quaisquer bases de dados secundárias. Ao atualizar, recomendamos que primeiro de atualizar a base de dados secundária e, em seguida, atualizar o site primário. Quando desatualização, inverter a ordem: mudar o primário primeiro e, em seguida, mudar secundário. Quando atualizar ou mudar a base de dados para uma camada de serviço diferente, esta recomendação é imposta. 

> [!NOTE]
> Se tiver criado a base de dados secundária como parte da configuração do grupo de ativação pós-falha não se recomenda mudar a base de dados secundária. Isto é para garantir que a camada de dados tem capacidade suficiente para processar a carga de trabalho regular após a ativação pós-falha está ativada. 
>

## <a name="preventing-the-loss-of-critical-data"></a>Impedir a perda de dados críticos
Devido à latência elevada de redes de área alargada, a cópia contínua utiliza um mecanismo de replicação assíncrona. Replicação assíncrona faz com que alguma perda de dados obrigatório se ocorrer uma falha. No entanto, algumas aplicações podem exigir sem perda de dados. Para proteger estas atualizações críticas, um programador de aplicações pode chamar o [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) procedimento sistema imediatamente a seguir ao consolidar a transação. Chamar **sp_wait_for_database_copy_sync** bloqueia o thread que efectua até que o último transações consolidadas foi transmitida para a base de dados secundária. No entanto, não espera para as transações transmitidas ser reproduzido e consolidada no secundário. **sp_wait_for_database_copy_sync** tem um âmbito para uma ligação a cópia contínua específico. Qualquer utilizador com os direitos de ligação para a base de dados primária pode chamar este procedimento.

> [!NOTE]
> **sp_wait_for_database_copy_sync** impede a perda de dados após a ativação pós-falha, mas não garante a sincronização completa para acesso de leitura. O atraso causado por um **sp_wait_for_database_copy_sync** chamada de procedimento pode ser significativa e depende do tamanho do registo de transação no momento da chamada. 
> 

## <a name="programmatically-managing-failover-groups-and-active-geo-replication"></a>Através de programação gerir grupos de ativação pós-falha e a georreplicação ativa
Tal como abordado anteriormente, grupos de auto-ativação pós-falha (em pré-visualização) e o Active Directory georreplicação também pode ser gerida através de programação utilizando o Azure PowerShell e a API REST. As tabelas seguintes descrevem o conjunto de comandos disponíveis.

**API de Gestor de recursos do Azure e a segurança baseada em funções**: georreplicação ativa inclui um conjunto de APIs do Azure Resource Manager para a gestão, incluindo o [API de REST de base de dados do Azure SQL](https://docs.microsoft.com/rest/api/sql/) e [cmdlets Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview). Estas APIs requerem a utilização de grupos de recursos e suportam a segurança baseada em funções (RBAC). Para obter mais informações sobre como implementar funções de acesso, consulte [controlo de acesso em funções do Azure](../active-directory/role-based-access-control-what-is.md).

## <a name="manage-sql-database-failover-using-transact-sql"></a>Gerir a ativação pós-falha de base de dados do SQL Server com o Transact-SQL

| Comando | Descrição |
| --- | --- |
| [Falha de ALTER DATABASE (base de dados SQL do Azure)](/sql/t-sql/statements/alter-database-azure-sql-database) |Utilize o argumento de servidor para adicionar SECUNDÁRIO ON para criar uma base de dados secundária para uma base de dados existente e inicia a replicação de dados |
| [Falha de ALTER DATABASE (base de dados SQL do Azure)](/sql/t-sql/statements/alter-database-azure-sql-database) |Utilize a ativação pós-falha ou FORCE_FAILOVER_ALLOW_DATA_LOSS para alternar uma base de dados secundária para ser primário para iniciar a ativação pós-falha |
| [Falha de ALTER DATABASE (base de dados SQL do Azure)](/sql/t-sql/statements/alter-database-azure-sql-database) |Utilize remover no servidor SECUNDÁRIO para terminar uma replicação de dados entre uma base de dados do SQL Server e base de dados secundária especificada. |
| [sys.geo_replication_links (SQL Database do Azure)](/sql/relational-databases/system-dynamic-management-views/sys-geo-replication-links-azure-sql-database) |Devolve informações sobre todas as ligações de replicação existente para cada base de dados do servidor lógico da SQL Database do Azure. |
| [sys.dm_geo_replication_link_status (SQL Database do Azure)](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) |Obtém a última replicação, desfasamento de último e outras informações sobre a ligação de replicação para uma determinada base de dados do SQL. |
| [operation_status (SQL Database do Azure)](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) |Mostra o estado para todas as operações de base de dados, incluindo o estado das ligações de replicação. |
| [sp_wait_for_database_copy_sync (SQL Database do Azure)](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) |faz com que a aplicação a aguardar até que todas as transações consolidadas são replicadas e confirmadas pela base de dados secundária Active Directory. |
|  | |

## <a name="manage-sql-database-failover-using-powershell"></a>Gerir a ativação pós-falha de base de dados do SQL Server com o PowerShell

| Cmdlet | Descrição |
| --- | --- |
| [Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase) |Obtém um ou mais bases de dados. |
| [Novo AzureRmSqlDatabaseSecondary](/powershell/module/azurerm.sql/new-azurermsqldatabasesecondary) |Cria uma base de dados secundária para uma base de dados existente e começa a replicação de dados. |
| [Conjunto AzureRmSqlDatabaseSecondary](/powershell/module/azurerm.sql/set-azurermsqldatabasesecondary) |Muda uma base de dados secundária para ser primário para iniciar a ativação pós-falha. |
| [Remover AzureRmSqlDatabaseSecondary](/powershell/module/azurerm.sql/remove-azurermsqldatabasesecondary) |Termina a replicação de dados entre uma base de dados do SQL Server e base de dados secundária especificada. |
| [Get-AzureRmSqlDatabaseReplicationLink](/powershell/module/azurerm.sql/get-azurermsqldatabasereplicationlink) |Obtém as ligações de georreplicação entre uma base de dados do SQL do Azure e um grupo de recursos ou do SQL Server. |
| [Novo AzureRmSqlDatabaseFailoverGroup](/powershell/module/azurerm.sql/set-azurermsqldatabasefailovergroup) |   Este comando cria um grupo de ativação pós-falha e regista-o nos servidores primários e secundários|
| [Remover AzureRmSqlDatabaseFailoverGroup](/powershell/module/azurerm.sql/remove-azurermsqldatabasefailovergroup) | Elimina o grupo de ativação pós-falha do servidor e todas as bases de dados secundárias incluído o grupo |
| [Get-AzureRmSqlDatabaseFailoverGroup](/powershell/module/azurerm.sql/get-azurermsqldatabasefailovergroup) | Obtém a configuração do grupo de ativação pós-falha |
| [Conjunto AzureRmSqlDatabaseFailoverGroup](/powershell/module/azurerm.sql/set-azurermsqldatabasefailovergroup) |   Modifica a configuração do grupo de ativação pós-falha |
| [Comutador AzureRMSqlDatabaseFailoverGroup](/powershell/module/azurerm.sql/switch-azurermsqldatabasefailovergroup) | Ativação pós-falha de acionadores do grupo de ativação pós-falha para o servidor secundário |
|  | |

> [!IMPORTANT]
> Para scripts de exemplo, consulte [configurar e ativação pós-falha de uma base de dados utilizando a replicação geográfica activa](scripts/sql-database-setup-geodr-and-failover-database-powershell.md), [configurar e utilizar a georreplicação ativa agrupado da base de dados de ativação pós-falha](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md), e [ Configurar e ativação pós-falha de uma ativação pós-falha do grupo para uma base de dados (pré-visualização)](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md).
>

## <a name="manage-sql-database-failover-using-the-rest-api"></a>Gerir a ativação pós-falha de base de dados do SQL Server utilizando a API REST
| API | Descrição |
| --- | --- |
| [Criar ou atualização de base de dados (createMode = restaurar)](/rest/api/sql/Databases/CreateOrUpdate) |Cria, atualiza ou restaura um site primário ou de uma base de dados secundária. |
| [Obter criar ou atualizar o estado da base de dados](/rest/api/sql/Databases/CreateOrUpdate) |Devolve o estado durante uma operação de criação. |
| [Definir a base de dados secundária como principal (a ativação pós-falha planeada)](/rest/api/sql/replicationlinks/failover) |Conjuntos de base de dados réplica é primária por falhar ao longo da base de dados de réplica primária atual. |
| [Definir a base de dados secundária como principal (ativação pós-falha não planeada)](/rest/api/sql/replicationlinks/failoverallowdataloss) |Conjuntos de base de dados réplica é primária por falhar ao longo da base de dados de réplica primária atual. Esta operação poderá resultar na perda de dados. |
| [Obter ligação de replicação](/rest/api/sql/replicationlinks/get) |Obtém uma ligação de replicação específicos para uma determinada base de dados do SQL numa parceria de georreplicação. Obtém as informações visíveis na vista de catálogo sys.geo_replication_links. |
| [Ligações de replicação - lista pela base de dados](/rest/api/sql/replicationlinks/listbydatabase) | Obtém todas as ligações de replicação para uma determinada base de dados do SQL numa parceria de georreplicação. Obtém as informações visíveis na vista de catálogo sys.geo_replication_links. |
| [Eliminar ligação de replicação](/rest/api/sql/databases/delete) | Elimina uma ligação de replicação de base de dados. Não é possível efetuar durante a ativação pós-falha. |
| [Criar ou atualizar o grupo de ativação pós-falha](/rest/api/sql/failovergroups/createorupdate) | Cria ou atualiza um grupo de ativação pós-falha |
| [Eliminar grupo de ativação pós-falha](/rest/api/sql/failovergroups/delete) | Remove o grupo de ativação pós-falha do servidor |
| [Ativação pós-falha (planeada)](/rest/api/sql/failovergroups/failover) | A ativação pós-falha do servidor primário atual para este servidor. |
| [Permitir a ativação pós-falha forçada perda de dados](/rest/api/sql/failovergroups/forcefailoverallowdataloss) |ails através do servidor primário atual para este servidor. Esta operação poderá resultar na perda de dados. |
| [Obter grupo de ativação pós-falha](/rest/api/sql/failovergroups/get) | Obtém um grupo de ativação pós-falha. |
| [Liste os grupos de ativação pós-falha pelo servidor](/rest/api/sql/failovergroups/listbyserver) | Lista os grupos de ativação pós-falha num servidor. |
| [Grupo de atualização de ativação pós-falha](/rest/api/sql/failovergroups/update) | Atualiza um grupo de ativação pós-falha. |
|  | |

## <a name="next-steps"></a>Passos seguintes
* Para scripts de exemplo, consulte:
   - [Configurar e utilizar a georreplicação ativa única da base de dados de ativação pós-falha](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
   - [Configurar e utilizar a georreplicação ativa agrupado da base de dados de ativação pós-falha](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
   - [Configurar e ativação pós-falha a ativação pós-falha de grupo para uma base de dados (pré-visualização)](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md)
* Para cenários e uma descrição geral de continuidade de negócio, consulte [descrição geral da continuidade de negócio](sql-database-business-continuity.md)
* Para saber mais sobre o SQL do Azure, base de dados automática de cópias de segurança, consulte [cópias de segurança automatizadas de base de dados SQL](sql-database-automated-backups.md).
* Para saber mais sobre a utilização de cópias de segurança automatizadas para recuperação, consulte [restaurar uma base de dados de cópias de segurança iniciou o serviço](sql-database-recovery-using-backups.md).
* Para saber mais sobre os requisitos de autenticação para um novo servidor primário e a base de dados, consulte [segurança da base de dados do SQL Server após a recuperação de desastre](sql-database-geo-replication-security-config.md).

