---
title: "O que é o serviço Base de Dados SQL do Azure? | Microsoft Docs"
description: "Obtenha uma introdução à Base de Dados SQL: detalhes técnicos e capacidades do sistema de gestão de bases de dados relacionais (RDBMS) da Microsoft na nuvem."
keywords: "introdução à sql, introdução sql, o que é a base de dados sql"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: cgronlun
ms.assetid: c561f600-a292-4e3b-b1d4-8ab89b81db48
ms.service: sql-database
ms.custom: overview, mvc
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: Active
ms.date: 09/20/2017
ms.author: carlrab
ms.openlocfilehash: f97a1e7977e28765985991ba9e38ed4618e00bda
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="what-is-the-azure-sql-database-service"></a>O que é o serviço Base de Dados SQL do Azure? 

A Base de Dados SQL é um serviço de bases de dados relacionais para fins gerais do Microsoft Azure que suporta estruturas como dados relacionados, JSON, espaciais e XML. Proporciona [desempenho dimensionável de forma dinâmica](sql-database-service-tiers.md) e disponibiliza opções como [índices columnstore](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) para análises e relatórios aprofundados e [OLTP](sql-database-in-memory.md) dentro da memória para processamento transacional avançado. A Microsoft lida com a aplicação de patches e a atualização da base de código do SQL ininterruptamente e abstrai toda a gestão da infraestrutura subjacente. 

A Base de Dados SQL partilha a base de código com o [motor de bases de dados do Microsoft SQL Server](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation). Com a estratégia da cloud prioritária da Microsoft, as mais recentes capacidades do SQL Server são lançadas primeiro na Base de Dados SQL e, depois, no próprio SQL Server. Esta abordagem disponibiliza-lhe as últimas capacidades do SQL Server sem sobrecarga em termos de aplicação de patches ou atualizações - com estas funcionalidades novas testadas em milhões de bases de dados. Para obter informações sobre as novas capacidades quando são anunciadas, veja:

- **[Mapa do Azure para a Base de Dados SQL](https://azure.microsoft.com/roadmap/?category=databases)**: o local onde encontra as novidades atuais e futuras. 
- **[blogue da Base de Dados SQL do Azure](https://azure.microsoft.com/blog/topics/database)**: o local onde os membros da equipa de produto do SQL Server escrevem sobre as notícias e as funcionalidades da Base de Dados SQL. 

A Base de Dados SQL proporciona um desempenho previsível a vários níveis de serviço, que fornece escalabilidade dinâmica sem tempo de inatividade, otimização inteligente incorporada, escalabilidade e disponibilidade globais e opções de segurança avançadas — tudo isto sem praticamente qualquer administração. Estas capacidades permitem-lhe concentrar-se no desenvolvimento rápido de aplicações e acelerar o seu tempo de colocação no mercado, em vez de alocar tempo e recursos valiosos para a gestão de máquinas virtuais e de infraestruturas. O serviço Base de Dados SQL encontra-se atualmente em 38 datacenters em todo o mundo, sendo que são colocados online mais datacenters regularmente, o que lhe permite executar a sua base de dados num datacenter perto de si.

> [!NOTE]
> Veja [Azure Trust Center](https://azure.microsoft.com/support/trust-center/security/) (Centro de Fidedignidade do Azure) para obter informações sobre a segurança da plataforma Azure.
>

## <a name="scalable-performance-and-pools"></a>Dimensionar o desempenho e os conjuntos

Com a Base de Dados SQL, as bases de dados estão isoladas umas das outras e são portáteis, tendo cada uma o seu próprio [escalão de serviço](sql-database-service-tiers.md) com um nível de desempenho garantido. Este serviço disponibiliza diferentes níveis de desempenho para diferentes necessidades e permite agrupar as bases de dados, de modo a maximizar a utilização de recursos e poupar dinheiro.

### <a name="adjust-performance-and-scale-without-downtime"></a>Ajuste o desempenho e dimensione a capacidade sem períodos de indisponibilidade

A Base de Dados SQL tem quatro escalões de serviço para suportar cargas de trabalho de bases de dados leves ou pesadas: Básica, Standard, Premium e Premium RS. Pode criar a sua primeira aplicação numa base de dados pequena e individual a um baixo custo por mês e, em seguida, alterar o escalão de serviço manual ou programaticamente em qualquer altura para satisfazer as necessidades da sua solução. Pode ajustar o desempenho sem provocar tempos de inatividade na aplicação ou nos clientes. A escalabilidade dinâmica permite que a base de dados responda de forma transparente a requisitos de recursos em rápida mutação e permite-lhe pagar apenas pelos recursos de que precisa, quando precisa.

   ![dimensionamento](./media/sql-database-what-is-a-dtu/single_db_dtus.png)

### <a name="elastic-pools-to-maximize-resource-utilization"></a>Conjuntos elásticos para maximizar a utilização de recursos

Para muitas empresas e aplicações, ser capaz de criar bases de dados individuais e aumentar e reduzir o desempenho a pedido é suficiente, sobretudo se os padrões de utilização forem relativamente previsíveis. No entanto, se tiver padrões de utilização imprevisíveis, pode tornar-se difícil gerir os custos e o seu modelo de negócio. Os [conjuntos elásticos](sql-database-elastic-pool.md) foram concebidos para resolver este problema. O conceito é simples. Os recursos de desempenho são alocados a um conjunto em vez de a uma base de dados individual e paga os recursos de desempenho coletivo do conjunto em vez do desempenho da base de dados individual. 

   ![conjuntos elásticos](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

Com os conjuntos elásticos, não tem de se concentrar em ajustar o desempenho da base de dados à medida que a procura pelos recursos flutua. As bases de dados agrupadas consomem os recursos de desempenho do conjunto elástico conforme necessário. As bases de dados agrupadas consomem os limites do conjunto, mas não os excedem, pelo que os seus custos se mantêm previsíveis, mesmo que a utilização das bases de dados não. Além disso, pode [adicionar e remover bases de dados do conjunto](sql-database-elastic-pool-manage-portal.md), dimensionando a sua aplicação de um punhado de bases de dados para milhares, tudo dentro de um orçamento controlado por si. Também pode controlar os recursos mínimos e máximos disponíveis para as bases de dados do conjunto, para garantir que nenhuma utiliza todos os recursos e que todas as bases de dados agrupadas têm uma quantidade mínima garantida de recursos. Para saber mais sobre os padrões de estrutura de aplicações SaaS que utilizam conjuntos elásticos, veja [Design Patterns for Multi-tenant SaaS Applications with SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md) (Padrões de Design para Aplicações SaaS multi-inquilino com a Base de Dados SQL).

### <a name="blend-single-databases-with-pooled-databases"></a>Combinar bases de dados individuais com bases de dados agrupadas

Qualquer que seja a sua opção — bases de dados individual ou conjuntos elásticos —, não está limitado. Pode misturar bases de dados individuais com conjuntos elásticos e alterar os escalões de serviço das bases de dados individuais e dos conjuntos elásticos rápida e facilmente para os adaptar à sua situação. Com o poder e a o alcance do Azure, pode combinar outros serviços do Azure com a Base de Dados SQL para satisfazer as necessidades de design da sua aplicação única, aumentar as eficiências de custos e recursos e conseguir novas oportunidades de negócio.

### <a name="extensive-monitoring-and-alerting-capabilities"></a>Capacidades alargadas de monitorização e alertas

Mas como pode comparar o desempenho relativo das bases de dados individuais e dos conjuntos elásticos? Como pode saber qual é o ponto de paragem certo quando aumenta ou reduz verticalmente o desempenho? Utilize as ferramentas de [monitorização de desempenho ](sql-database-performance.md) e de [alertas incorporadas](sql-database-insights-alerts-portal.md) em conjunto com as classificações de desempenho baseadas em [Unidades de Transação da Base de Dados (DTUs) de bases de dados individuais e DTUs elásticas (eDTUs) de conjuntos elásticos](sql-database-what-is-a-dtu.md). Com estas ferramentas, pode avaliar rapidamente o impacto de aumentar ou reduzir verticalmente consoante as necessidades de desempenho atuais ou do projeto. Consulte o artigo [Opções e desempenho da Base de Dados SQL: compreender o que está disponível em casa escalão de serviço](sql-database-service-tiers.md) para obter mais detalhes.

Além disso, a Base de Dados SQL pode [emitir métricas e registos de diagnósticos](sql-database-metrics-diag-logging.md) para uma monitorização mais fácil. Pode configurar a Base de Dados SQL para armazenar a utilização de recursos, funções de trabalho e sessões e a conectividade a um dos recursos do Azure seguintes:

- **Armazenamento do Azure**: para arquivar grandes quantidades de telemetria a um preço baixo
- **Hub de Eventos do Azure**: para integrar a telemetria da Base de Dados SQL na sua solução personalizada de monitorizção ou em pipelines ativos.
- **Azure Log Analytics**: para uma solução de monitorização incorporada com capacidade para relatórios, alertas e mitigação

    ![arquitetura](./media/sql-database-metrics-diag-logging/architecture.png)

## <a name="availability-capabilities"></a>Capacidades de disponibilidade

O contrato de nível de serviço [(SLA)](http://azure.microsoft.com/support/legal/sla/) do Azure líder da indústria, que garante 99,99% de disponibilidade, com tecnologia de uma rede global de datacenters geridos pela Microsoft, ajuda a manter a sua aplicação operacional 24 horas por dia, sete dias por semana. Além disso, a Base de Dados SQL proporciona funcionalidades de [continuidade de negócio e escalabilidade global](sql-database-business-continuity.md):

- **[Cópias de segurança automáticas](sql-database-automated-backups.md)**: a Base de Dados SQL faz cópias de segurança completas, diferenciais e de registos de transações.
- **[Restauros para um ponto anterior no tempo](sql-database-recovery-using-backups.md)**: a Base de Dados SQL suporta a recuperação para qualquer ponto anterior no tempo dentro do período de retenção de cópias de segurança automáticas.
- **[Georreplicação ativa](sql-database-geo-replication-overview.md)**: a Base de Dados SQL permite-lhe configurar até quatro bases de dados legíveis secundárias no mesmo ou nos datacenters do Azure distribuídos globalmente.  Por exemplo, se tiver uma aplicação SaaS com uma base de dados de catálogo que tem um volume elevado de transações só de leitura simultâneas, utilize a georreplicação ativa para permitir uma escala de leitura global e remover estrangulamentos na base de dados principal que se devam a cargas de trabalho de leitura. 
- **[Grupos de ativação pós-falha](sql-database-geo-replication-overview.md)**: a Base de Dados SQL permite-lhe ativar a disponibilidade elevada e o balanceamento de carga numa escala global, incluindo georreplicação transparente e ativações pós-falha de conjuntos de dados grandes e conjuntos elásticos. Com os grupos de ativação pós-falha e a georreplicação ativa, é possível criar aplicações SaaS distribuídas globalmente com uma sobrecarga mínima em termos de administração, ficando a cabo da Base de Dados SQL a monitorização complexa, o encaminhamento e a orquestração de ativações pós-falha.

## <a name="built-in-intelligence"></a>Inteligência incorporada

Com a Base de Dados SQL, pode tirar partido da inteligência incorporada que o ajuda a reduzir significativamente os custos de execução e gestão de bases de dados e que maximiza, tanto o desempenho, como a segurança, da sua aplicação. Executando milhões de cargas de trabalho dos clientes ininterruptamente, a Base de Dados SQL recolhe e processa uma gigantesca quantidade de dados de telemetria, respeitando também totalmente a privacidade dos clientes em segundo plano. Vários algoritmos avaliam continuamente os dados de telemetria, de modo a que o serviço possa aprender e adaptar-se com a sua aplicação. Com babe nestas análises, o serviço oferece recomendações de melhoria do desempenho, personalizadas à medida da sua carga de trabalho específica. 

### <a name="automatic-performance-monitoring-and-tuning"></a>Monitorização de desempenho automático e Otimização

A Base de Dados SQL disponibiliza informações detalhadas sobre as consultas que tem de monitorizar. Aprende os padrões da sua base de dados e permite-lhe adaptar o esquema da mesma à carga de trabalho. Base de dados do SQL Server fornece [recomendações de otimização do desempenho](sql-database-advisor.md), onde pode consultar as ações de Otimização e aplicá-las. 

No entanto, a monitorização contínua de bases de dados é uma tarefa difícil e entediante, especialmente se forem muitas. [Insights inteligentes](sql-database-intelligent-insights.md) não esta tarefa para si por automaticamente monitorização do desempenho de base de dados SQL em escala e informa dos problemas de degradação de desempenho, identifica a causa do problema e fornece melhorias de desempenho recomendações sempre que possível.

Pode ser impossível gerir eficazmente um grande número de bases de dados, mesmo tendo em conta todas as ferramentas e relatórios que a Base de Dados SQL e o Azure proporcionam. Em vez de monitorização e otimizar a sua base de dados manualmente, poderá considerar delegar algumas da monitorização e otimização de ações a base de dados do SQL Server utilizar [otimização automática](sql-database-automatic-tuning.md). A Base de Dados SQL aplica recomendações, testa e verifica automaticamente todas as ações de otimização, para garantir que o desempenho continua a melhorar. Desta forma, a Base de Dados SQL adapta-se automaticamente à sua carga de trabalho de forma controlada e segura. A otimização automática significa que o desempenho da sua base de dados é cuidadosamente monitorizado e comparado antes e depois de cada ação de otimização e, caso não melhore, as ações são revertidas.

Hoje em dia, muitos dos nossos parceiros que executam [aplicações SaaS multi-inquilino](sql-database-design-patterns-multi-tenancy-saas-applications.md) tendo a Base de Dados SQL como base confiam na otimização de desempenho automática, para garantir que as aplicações têm sempre um desempenho estável e previsível. Para estes parceiros, esta funcionalidade reduz significativamente o risco de ocorrência de incidentes de desempenho a meio da noite. Além disso, uma vez que parte da base de clientes deles também utiliza o SQL Server, os parceiros utilizam as mesmas recomendações de indexação disponibilizadas pela Base de Dados SQL para ajudar os clientes do SQL Server.

Existem dois aspetos de otimização automáticos, que são [disponíveis na base de dados do SQL Server](sql-database-automatic-tuning.md):

- **A gestão automática de índices**: identifica os índices devem ser adicionados na base de dados e índices que devem ser removidos.
- **Correção automática plano**: identifica problemáticos planos e corrige os problemas de desempenho do plano SQL (disponível em breve, já disponível no SQL Server 2017).

### <a name="adaptive-query-processing"></a>Processamento de consultas adaptável

Também adicionámos o conjunto de funcionalidades de [processamento de consultas adaptável](/sql/relational-databases/performance/adaptive-query-processing) à Base de Dados SQL, incluindo a execução intercalada para funções de valor de tabela com múltiplas instruções, feedback de concessão de memória no modo de lote e associações adaptativas no modo de lote. Todas estas funcionalidades de processamento de consultas adaptativo aplica técnicas de “aprendizagem e adaptação” semelhantes, o que ajuda a ir ainda mais longe na resolução de problemas de desempenho relacionados com questões de otimização de consultas historicamente complicadas.

### <a name="intelligent-threat-detection"></a>Deteção de ameaças inteligente

 A [Deteção de Ameaças do SQL](sql-database-threat-detection.md) tira partido da [auditoria da Base de Dados SQL](sql-database-auditing.md) para monitorizar continuamente as bases de dados SQL do Azure relativamente a tentativas prejudiciais de aceder a dados confidenciais. A deteção de ameaças do SQL proporciona uma nova camada de segurança, o que permite aos clientes detetar e responder a potenciais ameaças à medida que ocorrem, ao fornecer alertas de segurança para atividades anómalas. Os utilizadores recebem alertas sobre atividades suspeitas nas bases de dados, potenciais vulnerabilidades, ataques de injeção de SQL e padrões de acesso anómalos às bases de dados. Os alertas da deteção de ameaças do SQL mostram detalhes da atividade suspeita e a ação recomendada para investigar e mitigar essa ameaça. Os utilizadores podem explorar os eventos suspeitos para determinar se estes resultam de uma tentativa de aceder, violar ou explorar dados na base de dados. A deteção de ameaças facilita lidar com potenciais ameaças à base de dados sem que seja necessário ser especialista em segurança ou gerir sistemas de monitorização de segurança avançados.

## <a name="advanced-security-and-compliance"></a>Segurança e conformidade avançadas

A Base de Dados SQL proporciona um conjunto de [funcionalidades de segurança e conformidade incorporadas](sql-database-security-overview.md), para ajudar a sua aplicação a cumprir diversos requisitos de segurança e conformidade. 

### <a name="auditing-for-compliance-and-security"></a>Auditoria para conformidade e segurança

A [Auditoria da Base de Dados SQL](sql-database-auditing.md) regista os eventos da base de dados e escreve-os num registo de auditoria na sua conta de armazenamento do Azure. A auditoria pode ajudá-lo a manter a conformidade regulatória, a compreender as atividades da base de dados e a obter informações relativas a discrepâncias e anomalias que possam traduzir preocupações comerciais ou suspeitas de violações de segurança.

### <a name="data-encryption-at-rest"></a>Encriptação de dados inativos

A [encriptação de dados transparente](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) da Base de Dados SQL ajuda a proteger contra a ameaça de atividades maliciosas, ao fazer a encriptação e desencriptação inativa em tempo real da base de dados, das cópias de segurança associadas e dos ficheiros de registo de transações sem que seja necessário fazer alterações à aplicação. A partir de maio de 2017, todas as bases de dados SQL do Azure novas estão automaticamente protegidas com a encriptação de dados transparente (TDE). A TDE é a tecnologia comprovada de encriptação inativa do SQL e que é exigida por muitas normas de conformidade para proteger de roubos de suportes de dados de armazenamento. Os clientes podem utilizar o Azure Key Vault para gerir as chaves e outros segredos da encriptação TDE de uma forma segura e que está em conformidade.

### <a name="data-encryption-in-motion"></a>Encriptação de dados ativa

A Base de Dados SQL é o único sistema de bases de dados a oferecer proteção de dados confidenciais em movimento, inativos e durante o processamento de consultas com [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine). Always Encrypted é uma funcionalidade pioneira da indústria que oferece segurança de dados sem paralelo contra violações que envolvam o roubo de dados confidenciais. Por exemplo, com o Always Encrypted, os números dos cartões de crédito dos clientes são sempre armazenados encriptados na base de dados, inclusivamente durante o processamento de consultas, permitindo a desencriptação no local de utilização por parte das equipas autorizadas ou das aplicações que têm de processar esses dados.

### <a name="dynamic-data-masking"></a>Máscara de dados dinâmica

A [máscara de dados dinâmica da Base de Dados SQL](sql-database-dynamic-data-masking-get-started.md) limita a exposição de dados confidenciais ao mascará-los para utilizadores sem privilégios. A máscara de dados dinâmica ajuda a evitar acessos não autorizados a dados confidenciais, ao permitir aos clientes designar a quantidade de dados confidenciais a revelar com um impacto mínimo na camada de aplicação. É uma funcionalidade de segurança baseada em políticas que omite os dados confidenciais no conjunto de resultados de uma consulta em campos da base de dados designados, sendo que os dados na base de dados não são alterados.

### <a name="row-level-security"></a>Segurança ao Nível da Linha

A [segurança ao nível da linha](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) permite aos clientes controlar o acesso às linhas numa tabela da base de dados com base nas características do utilizador que executa uma consulta (como uma associação a um grupo ou contexto de execução). A segurança ao nível da linha (RLS) simplifica o design e a programação da segurança na sua aplicação. O RLS permite-lhe implementar restrições ao acesso à linha de dados. É possível, por exemplo, garantir que os colaboradores só têm acesso às linhas de dados que são pertinentes para o departamento deles ou limitar o acesso a dados por parte de um cliente apenas àqueles que são relevantes para a empresa dele.

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Integração do Azure Active Directory e a autenticação multifator

A Base de Dados SQL permite-lhe gerir centralmente identidades de utilizadores de bases de dados e outros serviços Microsoft com a [integração do Azure Active Directory](sql-database-aad-authentication.md). Esta capacidade simplifica a gestão de permissões e melhora a segurança. O Azure Active Directory suporta a [autenticação multifator](sql-database-ssms-mfa-authentication.md) (MFA), para aumentar a segurança de dados e aplicações, suportando, ao mesmo tempo, um processo de início de sessão único.

### <a name="compliance-certification"></a>Certificação de conformidade

A Base de Dados SQL participa em auditorias regulares e foi certificada por várias normas de conformidade. Para obter mais informações, veja o [Centro de Fidedignidade do Microsoft Azure](https://azure.microsoft.com/support/trust-center/), onde pode encontrar a lista mais recente de [certificações de conformidade da Base de Dados SQL](https://azure.microsoft.com/support/trust-center/services/).

## <a name="easy-to-use-tools"></a>Ferramentas fáceis de utilizar

Com a Base de Dados SQL, criar e manter aplicações é mais fácil e produtivo. A Base de Dados SQL permite-lhe dedicar-se ao que sabe fazer melhor: criar aplicações excelentes. Pode gerir e programar na Base de Dados SQL com as ferramentas e as competências que já tem.

- **[O portal do Azure](https://portal.azure.com/)**: uma aplicação baseada na Web para gerir todos os serviços do Azure 
- **[SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)**: uma aplicação de cliente gratuita e transferível para gerir qualquer infraestrutura do SQL, desde o SQL Server à Base de Dados SQL
- **[SQL Server Data Tools in Visual Studio](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)**: uma aplicação de cliente gratuita e transferível para programar bases de dados relacionais do SQL Server, bases de dados SQL do Azure, pacotes do Integration Services, modelos de dados do Analysis Services e relatórios do Reporting Services.
- **[Visual Studio Code](https://code.visualstudio.com/docs)**: um editor de código, gratuito, transferível e de código aberto para Windows, macOS e Linux que suporta extensões, incluindo a [extensão mssql](https://aka.ms/mssql-marketplace), para consultar o Microsoft SQL Server, a Base de Dados SQL do Azure e o SQL Data Warehouse.

A Base de Dados suporta a criação de aplicações com Python, Java, Node.js, PHP, Ruby e .NET em macOS, Linux e Windows. A Base de Dados SQL suporta as mesmas [bibliotecas de ligações](sql-database-libraries.md) que o SQL Server.

## <a name="engage-with-the-sql-server-engineering-team"></a>Envolver a equipa de engenharia do SQL Server

- [DBA Stack Exchange](https://dba.stackexchange.com/questions/tagged/sql-server): colocar questões de administração de base de dados
- [Stack Overflow](http://stackoverflow.com/questions/tagged/sql-server): colocar questões de desenvolvimento
- [Fóruns do MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?category=sqlserver): colocar questões técnicas
- [Microsoft Connect](https://connect.microsoft.com/SQLServer/Feedback): relatar erros e pedir funcionalidades
- [Reddit](https://www.reddit.com/r/SQLServer/): discutir sobre o SQL Server

## <a name="next-steps"></a>Passos seguintes

- Consultar a [página de preços](https://azure.microsoft.com/pricing/details/sql-database/) para ver comparações de preços e calculadoras de bases de dados individuais e conjuntos elásticos.

- Veja estes guias de introdução para começar:

  - [Criar uma base de dados SQL no portal do Azure](sql-database-get-started-portal.md)  
  - [Criar uma base de dados SQL com a CLI do Azure](sql-database-get-started-cli.md)
  - [Criar uma base de dados SQL utilizando o PowerShell](sql-database-get-started-powershell.md)

- Para um conjunto de amostras de CLI do Azure e PowerShell, veja:
  - [Exemplos da CLI do Azure para a Base de Dados SQL (Azure CLI samples for SQL Database)](sql-database-cli-samples.md)
  - [Exemplos do Azure PowerShell para a Base de Dados SQL (Azure PowerShell samples for SQL Database)](sql-database-powershell-samples.md)
