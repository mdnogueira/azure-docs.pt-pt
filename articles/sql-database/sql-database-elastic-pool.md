---
title: "Gerir várias bases de dados do SQL Server com o Azure de conjuntos elástico | Microsoft Docs"
description: "Gerir e dimensionar várias bases de dados SQL - centenas e milhares - utilizam conjuntos elásticos. Um preço para recursos que pode distribuir sempre que necessário."
keywords: "várias bases de dados, os recursos de base de dados e de desempenho de base de dados"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: b46e7fdc-2238-4b3b-a944-8ab36c5bdb8e
ms.service: sql-database
ms.custom: DBs & servers
ms.devlang: NA
ms.date: 10/11/2017
ms.author: carlrab
ms.workload: Active
ms.topic: article
ms.tgt_pltfrm: NA
ms.openlocfilehash: 2f1ff7a7c2ecf04069ffa6afcc66e2f0f9915b35
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="elastic-pools-help-you-manage-and-scale-multiple-azure-sql-databases"></a>Ajudar a gerir e dimensionar várias bases de dados SQL do Azure de conjuntos elásticos

Conjuntos de base de dados SQL elásticas são uma solução simple e rentável para gerir e dimensionamento várias bases de dados que tenham diferentes e imprevisíveis exigências de utilização. As bases de dados num agrupamento elástico estão num único servidor SQL Database do Azure e partilhar um número de conjunto de recursos ([unidades de transação de base de dados elásticas](sql-database-what-is-a-dtu.md) (eDTUs)) um preço do conjunto. Com os conjuntos elásticos na Base de Dados SQL do Azure, os programadores de SaaS podem otimizar o desempenho do preço num grupo de bases de dados dentro de um orçamento prescrito e, ao mesmo tempo, oferecer elasticidade de desempenho para cada base de dados. 

> [!NOTE]
> Os conjuntos elásticos estão em disponibilidade geral (GA) em todas as regiões do Azure, exceto na Índia Ocidental, onde se encontra, de momento, em pré-visualização. A GA dos conjuntos elásticos nesta região vai ocorrer assim que possível.
>

## <a name="what-are-sql-elastic-pools"></a>Quais são os conjuntos elásticos SQL? 

Os programadores de SaaS criam as aplicações sobre camadas de dados de grande escala, que consistem em várias bases de dados. Um padrão de aplicação comum é aprovisionar uma base de dados individual para cada cliente. Contudo, muitas vezes, os padrões de cada cliente são distintos e imprevisíveis, sendo difícil prever os requisitos de recursos de cada utilizador de bases de dados individual. Tradicionalmente, tiver duas opções: 

- Aprovisionar excessiva recursos com base na utilização máxima e através de pagamento, ou
- Under-aprovisionar custo, em detrimento de desempenho e cliente satisfação durante picos de guardar. 

Conjuntos elásticos resolverem este problema, garantindo que as bases de dados obter os recursos de desempenho que precisam e quando precisam-lo. Fornecem um mecanismo de alocação de recursos simples dentro de um orçamento previsível. Para saber mais sobre os padrões de estrutura de aplicações SaaS que utilizam conjuntos elásticos, consulte o artigo [Padrões de Estrutura de Aplicações SaaS Multi-inquilino com a Base de Dados SQL do Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md).

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Elastic-databases-helps-SaaS-developers-tame-explosive-growth/player]
>

Conjuntos elásticos ativar o programador comprar [unidades de transação de base de dados elásticas](sql-database-what-is-a-dtu.md) (eDTUs) para um agrupamento partilhado por várias bases de dados para acomodar imprevisíveis períodos de utilização por bases de dados individuais. O requisito de eDTUS dos conjuntos é determinado pela utilização agregada das bases de dados dos mesmos. O número de eDTUs disponíveis para os conjuntos é controlada pelo orçamento do programador. Os programadores só têm de adicionar bases de dados ao conjunto, definir as eDTUs mínimas e máximas das bases de dados e, depois, definir a eDTU do conjunto com base no orçamento. Os programadores podem utilizar os conjuntos para fazer crescer de forma contínua o serviço, de uma “lean startup” para uma empresa madura e em constante crescimento.

Dentro do conjunto, é dada às bases de dados individuais a flexibilidade para se dimensionarem automaticamente dentro de parâmetros definidos. Uma base de dados sobrecarregada pode consumir mais eDTUs para responder às necessidades. As bases de dados sujeitas a cargas mais leves consomem menos e as bases de dados que não estão sujeitas a qualquer carga não consomem eDTUs. O aprovisionamento de recursos para o conjunto completo e não para bases de dados individuais simplifica as tarefas de gestão. Além disso, tem um orçamento previsível para o conjunto. Podem ser adicionais mais eDTUs a um conjunto existente sem qualquer período de indisponibilidade da base de dados, à exceção de que as bases de dados podem ter de ser movidas para fornecer os recursos de computação adicionais para a nova reserva de eDTU. Do mesmo modo, se as eDTUs adicionais já não forem necessárias, podem ser removidas de um conjunto existente em qualquer momento. Além disso, pode adicionar ou subtrair bases de dados ao conjunto. Se uma base de dados estiver a subutilizar recursos de forma previsível, remova-a.

## <a name="when-should-you-consider-a-sql-database-elastic-pool"></a>Quando considerar um conjunto elástico da base de dados SQL?

Os conjuntos são bastante adequados para um grande número de bases de dados com padrões de utilização específicos. Para uma determinada base de dados, este padrão caracteriza-se por uma utilização média baixa com picos de utilização relativamente raros.

Quantas mais bases de dados adicionar a um conjunto, maior será a poupança. Dependendo do padrão de utilização de aplicações, é possível ver poupanças com um mínimo de duas bases de dados S3. 

As secções seguintes ajudam-no a avaliar se a sua coleção de bases de dados específica pode beneficiar de estar num conjunto. Os exemplos utilizam conjuntos Standard, mas os mesmos princípios também se aplicam aos conjuntos Básicos e Premium.

### <a name="assessing-database-utilization-patterns"></a>Avaliar os padrões de utilização da base de dados

A figura seguinte mostra um exemplo de uma base de dados que está inativa muito tempo, mas que também, periodicamente, apresenta picos de atividade. Este é um padrão de utilização adequado para um conjunto:

   ![uma base de dados individual adequada para um conjunto](./media/sql-database-elastic-pool/one-database.png)

Para o período de cinco minutos ilustrado, DB1 tem um pico até 90 DTUs, mas a utilização média global é inferior a cinco DTUs. Para executar esta carga de trabalho numa base de dados individual, é necessário o nível de desempenho S3, mas tal deixa a maior dos recursos inutilizados durante os períodos de pouca atividade.

Um conjunto permite que estas DTUs não utilizadas sejam partilhadas entre várias bases de dados, pelo que reduz as DTUs necessárias e os custos gerais.

Com base no exemplo anterior, imagine que existem bases de dados adicionais com padrões de utilização semelhantes a DB1. Nas duas figuras abaixo, é incluída no mesmo gráfico a utilização de quatro bases de dados e de 20 bases de dados, para ilustrar a natureza não sobreposta da respetiva utilização ao longo do tempo:

   ![quatro bases de dados com um padrão de utilização adequado para um conjunto](./media/sql-database-elastic-pool/four-databases.png)

   ![vinte bases de dados com um padrão de utilização adequado para um conjunto](./media/sql-database-elastic-pool/twenty-databases.png)

A utilização agregada de DTUs nas 20 bases de dados é ilustrada pela linha preta na figura anterior. Mostra que a utilização agregada de DTUs nunca excede as cem e indica que as 20 bases de dados podem partilhar 100 eDTUs ao longo deste período de tempo. Isto resulta numa redução em 20 vezes das DTUs e numa redução de 13 vezes no preço em comparação com colocar cada base de dados nos níveis de desempenho S3 para bases de dados individuais.

Este exemplo é ideal pelos motivos seguintes:

* Existem grandes diferenças entre a utilização de pico e utilização média por base de dados. 
* A utilização de pico de cada base de dados ocorre em diferentes alturas no tempo.
* Os eDTUs são partilhados entre muitas bases de dados.

O preço de um conjunto é uma função das eDTUs do conjunto. Embora o preço unitário de eDTU de um conjunto seja 1,5 vezes superior ao preço unitário de DTU para bases de dados individuais, **as eDTUs de conjuntos podem ser partilhadas por muitas bases de dados e são necessárias menos eDTUs totais**. Estas distinções nos preços e na partilha das eDTUs são as bases do potencial de poupança nos custos que os conjuntos podem proporcionar. 

As regras básicas seguintes relacionadas com a contagem e a utilização de bases de dados ajudam a garantir que os conjuntos oferecem custos reduzidos quando comparados com a utilização de níveis de desempenho para bases de dados individuais.

### <a name="minimum-number-of-databases"></a>Número mínimo de bases de dados

Se a soma das DTUs dos níveis de desempenho para bases de dados individuais for superior a 1,5 vezes as eDTUs necessárias para o conjunto, a utilização de um conjunto elástico apresenta a melhor relação de custo-eficiência. Para obter os tamanhos disponíveis, veja [eDTU and storage limits for elastic pools and elastic databases (eDTUs e limites de armazenamento dos conjuntos elásticos e das bases de dados elásticas)](sql-database-resource-limits.md#elastic-pool-storage-sizes-and-performance-levels).

***Exemplo***<br>
Para que a relação de custo-eficácia de um conjunto de 100 eDTUs seja superior à utilização de níveis de desempenho para bases de dados individuais, são necessárias, pelo menos, duas bases de dados S3 ou 15 bases de dados S0.

### <a name="maximum-number-of-concurrently-peaking-databases"></a>Número máximo de bases de dados com picos simultâneos

Ao partilhar eDTUs, nem todas as bases de dados de um conjunto podem utilizá-las ao mesmo tempo até ao limite disponível de quando são utilizados níveis de desempenho para bases de dados individuais. Quanta menos bases de dados tiverem picos em simultâneo, menor será a definição de eDTUs do conjunto e maior será a relação de custo-eficiência do mesmo. Em geral, não mais do que 2/3 (ou 67%) das bases de dados do conjunto devem atingir o limite de eDTUs em simultâneo.

***Exemplo***<br>
Para reduzir os custos de três bases de dados S3 num conjunto de 200 eDTU, duas bases de dados, no máximo, podem ter o pico de utilização ao mesmo tempo. Caso contrário, se mais de duas destas quatro bases de dados S3 tiverem o pico em simultâneo, o conjunto terá de ser dimensionado para mais de 200 eDTUs. Se o conjunto for redimensionado para mais de 200 eDTUs, será necessário adicionar mais bases de dados S3 ao conjunto, de modo a manter os custos inferiores aos dos níveis de desempenho para bases de dados individuais.

Tenha em conta que este exemplo não considera a utilização de outras bases de dados do conjunto. Se todas as bases de dados tiverem alguma utilização num determinado momento, menos de 2/3 (67%) das bases de dados podem ter o pico em simultâneo.

### <a name="dtu-utilization-per-database"></a>Utilização de DTU por base de dados
Uma grande diferença entre o pico e a utilização média das bases de dados indica períodos longos de pouca utilização e curtos períodos de elevada utilização. Este padrão de utilização é ideal para partilhar recursos entre bases de dados. A inclusão de bases de dados num conjunto deve ser considerada quando o pico de utilização for cerca de 1,5 vezes superior à utilização média.

***Exemplo***<br>
Uma base de dados S3 que tenha como pico 100 DTUs e utilize, em média, 67 DTUs ou menos, é uma boa candidata para partilhar eDTUs num conjunto. Em alternativa, uma base de dados S1 que tenha como pico 20 DTUs e utilize, em média, 13 DTUs ou menos, é uma boa candidata para um conjunto.

## <a name="how-do-i-choose-the-correct-pool-size"></a>Como devo escolher o tamanho do conjunto correto?

O melhor tamanho para um conjunto depende das eDTUs agregadas e dos recursos de armazenamento necessários para todas as bases de dados nesse conjunto. A escolha do tamanho envolve determinar o maior de:

* DTUs máximas utilizadas por todas as bases de dados do conjunto.
* Bytes de armazenamento máximos utilizados por todas as bases de dados do conjunto.

Para obter os tamanhos disponíveis, veja [eDTU and storage limits for elastic pools and elastic databases (eDTUs e limites de armazenamento dos conjuntos elásticos e das bases de dados elásticas)](sql-database-resource-limits.md#elastic-pool-storage-sizes-and-performance-levels).

A Base de Dados SQL avalia automaticamente o histórico de utilização de recursos de bases de dados num servidor de Base de Dados SQL existente e recomenda a configuração de conjunto adequada no portal do Azure. Para além das recomendações, uma experiência incorporada prevê a utilização de eDTUs para um grupo personalizado de bases de dados no servidor. Desta forma, pode fazer uma análise de hipóteses ao adicionar, interativamente, bases de dados ao conjunto e removê-las para ver uma análise da utilização de recursos e obter conselhos de dimensionamento antes de consolidar as alterações. Para obter as instruções, veja [Monitor, manage, and size an elastic pool (Monitorizar, gerir e dimensionar conjuntos elásticos)](sql-database-elastic-pool-manage-portal.md).

Nos casos em que não pode utilizar as ferramentas, as instruções passo a passo seguintes podem ajudá-lo a prever se um conjunto tem uma relação de custo-eficácia melhor do que as bases de dados individuais.

1. Estime as eDTUs necessárias para o conjunto da seguinte forma:

   Máx. (<*Número Total de DBs* X *utilização média de DTUs por DB*>,<br>
   <*Número de DBs com pico em simultâneo* X *utilização de pico de DTUs por DB*)
2. Calcule o espaço de armazenamento necessário para o conjunto ao adicionar o número de bytes de que todas as bases de dados do conjunto precisam. Em seguida, determine o tamanho do conjunto de eDTUs que disponibiliza esta quantidade de armazenamento. Para obter os limites de armazenamento dos conjuntos com base no tamanho do conjunto de eDTUs, veja [eDTU and storage limits for elastic pools and elastic databases (eDTUs e limites de armazenamento dos conjuntos elásticos e das bases de dados elásticas)](sql-database-resource-limits.md#elastic-pool-storage-sizes-and-performance-levels).
3. Escolha a estimativa de eDTUs maior de entre os passos 1 e 2.
4. Veja a [página de preços da Base de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/) e encontre o tamanho de conjunto de eDTU mais pequeno e que seja superior à estimativa do Passo 3.
5. Compare o preço do conjunto do Passo 5 com o preço de utilizar os níveis de desempenho adequado para bases de dados individuais.

## <a name="using-other-sql-database-features-with-elastic-pools"></a>Utilizar outras funcionalidades de base de dados do SQL Server com conjuntos elásticos

### <a name="elastic-jobs-and-elastic-pools"></a>As tarefas elásticas e conjuntos elásticos

Com um conjunto, as tarefas de gestão são simplificadas através da execução de scripts em **[tarefas elásticas](sql-database-elastic-jobs-overview.md)**. Uma tarefa elástica elimina a maior parte da monotonia associada a grandes números de bases de dados. Para começar, veja [Introdução às Tarefas elásticas](sql-database-elastic-jobs-getting-started.md).

Para obter mais informações sobre outras ferramentas de base de dados para trabalhar com várias bases de dados, veja [Aumentar horizontalmente com a Base de Dados SQL do Azure](sql-database-elastic-scale-introduction.md).

### <a name="business-continuity-options-for-databases-in-an-elastic-pool"></a>Opções de continuidade do negócio para bases de dados num agrupamento elástico
As bases de dados agrupadas suportam geralmente as mesmas [funcionalidades de continuidade de negócio](sql-database-business-continuity.md) que estão disponíveis para as bases de dados individuais.

- **Restauro de ponto no tempo**: restauro de ponto no tempo utiliza cópias de segurança da base de dados automática para recuperar uma base de dados num agrupamento para um ponto específico no tempo. Veja [Restauro para um ponto anterior no tempo](sql-database-recovery-using-backups.md#point-in-time-restore)

- **Georrestauro**: georrestauro fornece a opção de recuperação predefinida quando uma base de dados não está disponível devido a um incidente na região onde está alojada a base de dados. Veja [Restaurar uma Base de Dados SQL do Azure ou fazer a ativação pós-falha para uma secundária](sql-database-disaster-recovery.md)

- **Replicação geográfica activa**: para aplicações que têm requisitos de recuperação mais agressiva do que pode oferecer georrestauro, configurar [georreplicação ativa](sql-database-geo-replication-overview.md).

## <a name="manage-elastic-pools-and-databases-using-the-azure-portal"></a>Gerir conjuntos elásticos e bases de dados no portal do Azure

### <a name="creating-a-new-sql-database-elastic-pool-using-the-azure-portal"></a>Criar um novo conjunto elástico de SQL Database no portal do Azure

Existem duas formas de criar um conjunto elástico no portal do Azure. Pode fazê-lo a partir do zero se souber qual é a configuração de conjunto que pretende ou começar com uma recomendação do serviço. Base de dados SQL tem intelligence incorporado que recomenda uma configuração de conjunto elástico se é mais económico para si com base na telemetria de utilização passado para as bases de dados. 

Criar um agrupamento elástico de uma página de servidor existente no portal é a forma mais fácil de mover bases de dados existentes para um conjunto elástico. Também pode criar um conjunto elástico pesquisando **agrupamento elástico de SQL** no **Marketplace** ou clicar **+ adicionar** na página de conjuntos elásticos SQL. É possível especificar um servidor novo ou existente através deste agrupamento de aprovisionamento de fluxo de trabalho.

> [!NOTE]
> Pode criar vários conjuntos num servidor, mas não é possível adicionar bases de dados de diferentes servidores ao mesmo conjunto.
> 

Escalão de preço do conjunto determina as funcionalidades disponíveis para os elastics no agrupamento e o número máximo de eDTUs (eDTU máxima) e armazenamento (GBs) disponível para cada base de dados. Para obter mais informações, consulte [limites de recursos para conjuntos elásticos](sql-database-resource-limits.md#elastic-pool-storage-sizes-and-performance-levels).

Para alterar o escalão de preço do conjunto, clique em **Escalão de preço**, clique no escalão de preço pretendido e, em seguida, clique em **Selecionar**.

> [!IMPORTANT]
> Depois de escolher o escalão de preço e consolidar as alterações ao clicar em **OK** no último passo, não poderá alterar o escalão de preço do conjunto. Para alterar o escalão de preço de um conjunto elástico existente, crie um conjunto elástico no escalão de preço pretendido e migre as bases de dados para este novo conjunto.
>

Se as bases de dados com que está a trabalhar tiverem telemetria de histórico de utilização suficiente, o gráfico **Utilização de GB e eDTU estimada** e o gráfico de barras **Utilização de eDTU real** são atualizados para o ajudar a tomar decisões de configuração. Além disso, o serviço poderá apresentar uma mensagem de recomendação para o ajudar a dimensionar corretamente o conjunto.

O serviço Base de Dados SQL avalia o histórico de utilização e recomenda um ou mais conjuntos quando é mais rentável do que utilizar bases de dados individuais. Cada recomendação está configurada com um subconjunto exclusivo das bases de dados do servidor que melhor se adequam ao conjunto.

![conjunto recomendado](./media/sql-database-elastic-pool-create-portal/recommended-pool.png) 

A recomendação de conjunto é composta por:

- Um escalão de preço para o conjunto (básico, Standard, Premium ou Premium RS)
- O número adequado de **eDTUs POR CONJUNTO** (também denominado Número máximo de eDTUs por conjunto)
- O **número máximo de eDTUs** e o **número mínimo de eDTUs** por base de dados
- A lista de bases de dados recomendadas para o conjunto

> [!IMPORTANT]
> O serviço tem em conta os últimos 30 dias de telemetria quando recomenda conjuntos. Para uma base de dados ser considerada candidata para um conjunto elástico, deve existir, pelo menos, 7 dias. As bases de dados que já estão num conjunto elástico não são consideradas candidatas para recomendações de conjunto elástico.
>

O serviço avalia as necessidades de recursos e a relação custo-eficácia da movimentação das bases de dados individuais em cada escalão de serviço para conjuntos do mesmo escalão. Por exemplo, todas as bases de dados Standard num servidor são avaliadas para determinar a respetiva adequação a um Conjunto Elástico Standard. Isto significa que o serviço não faz recomendações entre escalões, tal como mover uma base de dados Standard para um conjunto Premium.

Depois de adicionar as bases de dados para o conjunto, as recomendações são geradas dinamicamente com base no histórico de utilização das bases de dados que selecionou. Estas recomendações são apresentadas no gráfico de utilização de GB e eDTU e na faixa de recomendação no topo do **configurar conjunto** página. Estas recomendações destinam-se para ajudá-lo na criação de um conjunto elástico otimizado para as bases de dados específicas.

![recomendações dinâmicas](./media/sql-database-elastic-pool-create-portal/dynamic-recommendation.png)

### <a name="manage-and-monitor-an-elastic-pool"></a>Gerir e monitorizar um conjunto elástico

No portal do Azure, pode monitorizar a utilização de um conjunto elástico e as bases de dados nesse agrupamento. Também pode efetuar um conjunto de alterações para o conjunto elástico e submeter todas as alterações ao mesmo tempo. Estas alterações incluem a adição ou remoção de bases de dados, alterar as definições do conjunto elástico ou alterar as definições de base de dados.

O gráfico seguinte mostra um agrupamento elástico de exemplo. A vista inclui:

* Gráficos para monitorizar a utilização de recursos do conjunto elástico e as bases de dados contidos no conjunto.
* O **configurar** botão de agrupamento para efetuar alterações ao conjunto elástico.
* O **criar base de dados** botão que cria uma base de dados e adiciona-o para o conjunto elástico atual.
* As tarefas elásticas que o ajudam a gerem um grande número de bases de dados através da execução de scripts de Transact SQL em todas as bases de dados numa lista.

![Vista de conjunto](./media/sql-database-elastic-pool-manage-portal/basic.png)

Pode aceder a um conjunto específico para ver a utilização de recursos. Por predefinição, o agrupamento está configurado para ver a utilização de armazenamento e de eDTU de última hora. O gráfico pode ser configurado para mostrar as métricas de diferentes ao longo de vários intervalos de tempo. Clique em de **utilização de recursos** gráfico em **monitorização do conjunto elástico** para mostrar uma vista detalhada das métricas especificadas durante a janela de tempo especificado.

![Monitorização do conjunto elástico](./media/sql-database-elastic-pool-manage-portal/basic-2.png)

![Página métrica](./media/sql-database-elastic-pool-manage-portal/metric.png)

### <a name="to-customize-the-chart-display"></a>Para personalizar a apresentação de gráfico

Pode editar o gráfico e a página de métrica para apresentar outras métricas, tais como a percentagem de CPU, percentagem de es de dados e a percentagem de es de registo utilizados.

![Clique em Editar](./media/sql-database-elastic-pool-manage-portal/edit-metric.png)

No **editar gráfico** formulário, pode selecionar um intervalo de tempo (decorridos desde a hora, hoje em dia, ou últimos semana), ou clique em **personalizado** para selecionar qualquer intervalo de datas nas últimas duas semanas. Pode escolher entre uma barra ou um gráfico de linhas e, em seguida, selecione os recursos para monitorizar.

> [!Note]
> Apenas as métricas com a mesma unidade de medida podem ser apresentadas no gráfico ao mesmo tempo. Por exemplo, se selecionar "percentagem de eDTU", em seguida, pode apenas selecionar outras métricas percentagem como a unidade de medida.
>

![Clique em Editar](./media/sql-database-elastic-pool-manage-portal/edit-chart.png)

### <a name="manage-and-monitor-databases-in-an-elastic-pool"></a>Gerir e monitorizar bases de dados num agrupamento elástico

Bases de dados individuais também podem ser monitorizadas para potenciais problemas. Em **monitorização de base de dados elásticas**, há um gráfico que mostra as métricas das cinco bases de dados. Por predefinição, o gráfico mostra as bases de 5 dados superiores no agrupamento de pela utilização de eDTU médio na última hora. 

![Monitorização do conjunto elástico](./media/sql-database-elastic-pool-manage-portal/basic-3.png)

Clique em de **utilização de eDTU para bases de dados para a hora passada** em **monitorização de base de dados elástica**. Esta ação abre **utilização de recursos de base de dados** e fornece uma vista detalhada da utilização de base de dados no conjunto. Utilizar a grelha na parte inferior da página, pode selecionar quaisquer bases de dados no agrupamento para apresentar a utilização do gráfico (até 5 as bases de dados). Também pode personalizar a janela de métricas e a hora apresentada no gráfico clicando **editar gráfico**.

![Página de utilização de recursos de base de dados](./media/sql-database-elastic-pool-manage-portal/db-utilization.png)

### <a name="to-customize-the-view"></a>Para personalizar a vista

Pode editar o gráfico para selecionar um intervalo de tempo (decorridos desde hora ou últimos 24 horas), ou clique em **personalizado** para selecionar um dia diferentes nas últimas 2 semanas para apresentar.

![Clique em Editar gráfico](./media/sql-database-elastic-pool-manage-portal/db-utilization-blade.png)

![Clique em personalizada](./media/sql-database-elastic-pool-manage-portal/editchart-date-time.png)

Também pode clicar o **comparar bases de dados ao** pendente para selecionar uma métrica diferente a utilizar quando a comparação com as bases de dados.

![Edite o gráfico](./media/sql-database-elastic-pool-manage-portal/edit-comparison-metric.png)

### <a name="to-select-databases-to-monitor"></a>Para selecionar as bases de dados para monitorizar

Na lista de base de dados no **utilização de recursos de base de dados** página, pode encontrar bases de dados específicos ao procurar através das páginas na lista ou escrevendo o nome de uma base de dados. Utilize a caixa de verificação para selecionar a base de dados.

![Pesquisa para bases de dados monitorizar](./media/sql-database-elastic-pool-manage-portal/select-dbs.png)


### <a name="add-an-alert-to-an-elastic-pool-resource"></a>Adicionar um alerta para um recurso do conjunto elástico

Pode adicionar regras a um conjunto elástico que enviar e-mail para pessoas ou alerta cadeias para pontos finais de URL, quando o conjunto elástico chega a um limiar de utilização que configurou.

**Para adicionar um alerta a qualquer recurso:**

1. Clique no **utilização de recursos** gráfico para abrir o **métrica** página, clique em **Adicionar alerta**e, em seguida, preencha as informações de **adicionar uma regra de alerta** página (**recursos** é automaticamente definido até ser o conjunto que está a trabalhar com).
2. Escreva um **nome** e **Descrição** que identifica o alerta e os destinatários.
3. Escolha um **métrica** que pretenda a partir da lista de alertas.

   O gráfico mostra dinamicamente a utilização de recursos para esse métrica ajudar a escolher um limiar.

4. Escolha um **condição** (superior, inferior, etc.) e um **limiar**.
5. Escolha um **período** de tempo que a regra de métrica deve ser satisfeita antes dos acionadores de alerta.
6. Clique em **OK**.

Para obter mais informações, consulte [criam alertas de base de dados SQL no portal do Azure](sql-database-insights-alerts-portal.md).

### <a name="move-a-database-into-an-elastic-pool"></a>Mover uma base de dados para um conjunto elástico

Pode adicionar ou remover bases de dados a partir de um conjunto existente. As bases de dados podem ser noutros conjuntos. No entanto, só pode adicionar bases de dados que estão no mesmo servidor lógico.

 ![Clique em Configurar conjunto](./media/sql-database-elastic-pool-manage-portal/configure-pool.png)

![Clique em Adicionar ao agrupamento](./media/sql-database-elastic-pool-manage-portal/add-to-pool.png)

![Selecione para adicionar bases de dados](./media/sql-database-elastic-pool-manage-portal/add-databases-pool.png)

![Adições de agrupamento pendente](./media/sql-database-elastic-pool-manage-portal/pending-additions.png)

![Clicar em Guardar](./media/sql-database-elastic-pool-manage-portal/click-save.png)

### <a name="move-a-database-out-of-an-elastic-pool"></a>Mover uma base de dados fora de um conjunto elástico

![lista de bases de dados](./media/sql-database-elastic-pool-manage-portal/select-pools-removal.png)

### <a name="change-performance-settings-of-an-elastic-pool"></a>Alterar as definições de desempenho de um conjunto elástico

Como monitorizar a utilização de recursos de um conjunto elástico, poderá descobrir que são necessários alguns ajustes. Talvez o conjunto tem uma alteração nos limites de desempenho ou o armazenamento. Possivelmente que pretende alterar as definições de base de dados no conjunto. Pode alterar a configuração do conjunto em qualquer altura para obter o melhor equilíbrio de desempenho e o custo. Consulte [quando um conjunto elástico a utilizar?](sql-database-elastic-pool.md) para obter mais informações.

Para alterar os limites de armazenamento ou de eDTUs por conjunto e eDTUs por base de dados:

![Utilização de recursos de agrupamento elástico](./media/sql-database-elastic-pool-manage-portal/resize-pool.png)

## <a name="manage-elastic-pools-and-databases-using-powershell"></a>Gerir conjuntos elásticos e bases de dados com o PowerShell

Para criar e gerir conjuntos elásticos da base de dados SQL com o Azure PowerShell, utilize os seguintes cmdlets PowerShell. Se precisar de instalar ou atualizar o PowerShell, consulte [módulo Azure PowerShell instalar](/powershell/azure/install-azurerm-ps). Para criar e gerir bases de dados, servidores e as regras de firewall, consulte [criar e gerir servidores SQL Database do Azure e as bases de dados com o PowerShell](sql-database-servers-databases.md#manage-azure-sql-servers-databases-and-firewalls-using-powershell). 

> [!TIP]
> Para scripts de exemplo do PowerShell, consulte [criar conjuntos elásticos e mover bases de dados entre conjuntos e fora de um conjunto com o PowerShell](scripts/sql-database-move-database-between-pools-powershell.md) e [utilize o PowerShell para monitorizar e dimensionar um agrupamento elástico de SQL na base de dados do Azure SQL](scripts/sql-database-monitor-and-scale-pool-powershell.md).
>

| Cmdlet | Descrição |
| --- | --- |
|[Novo-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/new-azurermsqlelasticpool)|Cria um conjunto de bases de dados elásticas num servidor lógico SQL.|
|[Get-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/get-azurermsqlelasticpool)|Obtém os conjuntos elásticos e os respetivos valores de propriedade num servidor SQL lógico.|
|[Set-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/set-azurermsqlelasticpool)|Modifica as propriedades de um conjunto de bases de dados elásticas num servidor lógico SQL. Por exemplo, utilizar o **StorageMB** propriedade para modificar o armazenamento máximo do conjunto elástico.|
|[Remove-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/remove-azurermsqlelasticpool)|Elimina um conjunto de bases de dados elásticas num servidor lógico SQL.|
|[Get-AzureRmSqlElasticPoolActivity](/powershell/module/azurerm.sql/get-azurermsqlelasticpoolactivity)|Obtém o estado das operações num conjunto elástico num servidor SQL lógico.|
|[New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase)|Cria uma nova base de dados num conjunto existente ou como uma base de dados. |
|[Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase)|Obtém um ou mais bases de dados.|
|[Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase)|Define as propriedades para uma base de dados ou move de uma base de dados para fora do ou entre conjuntos elásticos.|
|[Remove-AzureRmSqlDatabase](/powershell/module/azurerm.sql/remove-azurermsqldatabase)|Remove uma base de dados.|


> [!TIP]
> Criação de muitas bases de dados num agrupamento elástico pode demorar concluída ao utilizar o portal ou cmdlets do PowerShell que criam apenas uma única base de dados de cada vez. Para automatizar a criação de para um conjunto elástico, consulte o artigo [CreateOrUpdateElasticPoolAndPopulate](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae).
>

## <a name="manage-elastic-pools-and-databases-using-the-azure-cli"></a>Gerir conjuntos elásticos e bases de dados utilizando a CLI do Azure

Para criar e gerir conjuntos elásticos da base de dados SQL com o [CLI do Azure](/cli/azure/overview), utilize o seguinte [SQL Database do Azure CLI](/cli/azure/sql/db) comandos. Utilize o [Cloud Shell](/azure/cloud-shell/overview) para executar a CLI no seu browser ou [instale-a](/cli/azure/install-azure-cli) no macOS, Linux ou Windows. 

> [!TIP]
> Para scripts de exemplo da CLI do Azure, consulte [CLI de utilização para mover uma base de dados SQL do Azure num agrupamento elástico de SQL](scripts/sql-database-move-database-between-pools-cli.md) e [CLI do Azure de utilização para dimensionar um agrupamento elástico de SQL na base de dados do Azure SQL](scripts/sql-database-scale-pool-cli.md).
>

| Cmdlet | Descrição |
| --- | --- |
|[Criar AZ elástico de sql agrupamento](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_create)|Cria um conjunto elástico.|
|[lista de agrupamento elástico de sql AZ](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_list)|Devolve uma lista de conjuntos elásticos num servidor.|
|[lista-bds do agrupamento elástico de sql AZ](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_list_dbs)|Devolve uma lista de bases de dados num agrupamento elástico.|
|[agrupamento elástico de sql AZ lista-edições](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_list_editions)|Também inclui definições de DTU do agrupamento disponíveis, os limites de armazenamento e por definições de base de dados. Para reduzir verbosidade, limites de armazenamento adicional e por base de dados, as definições estão ocultadas por predefinição.|
|[atualização de agrupamento elástico de sql AZ](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update)|As atualizações de um conjunto elástico.|
|[eliminação de agrupamento elástico de sql AZ](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_delete)|Elimina o conjunto elástico.|

## <a name="manage-databases-within-elastic-pools-using-transact-sql"></a>Gerir bases de dados dentro de conjuntos elásticos, utilizando Transact-SQL

Para criar e mover bases de dados dentro de conjuntos elásticos existentes ou para devolver informações sobre um conjunto elástico da base de dados SQL com o Transact-SQL, utilize os seguintes comandos de T-SQL. Pode emitir estes comandos no portal do Azure, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs), ou qualquer outro programa que pode ligar a um servidor da SQL Database do Azure e passar comandos Transact-SQL. Para criar e gerir bases de dados, servidores e as regras de firewall, consulte [criar e gerir servidores SQL Database do Azure e as bases de dados com o Transact-SQL](sql-database-servers-databases.md#manage-azure-sql-servers-databases-and-firewalls-using-transact-sql).

> [!IMPORTANT]
> Não é possível criar, atualizar ou eliminar um agrupamento elástico de SQL Database do Azure com o Transact-SQL. Pode adicionar ou remover bases de dados a partir de um conjunto elástico e pode utilizar DMVs para devolver informações sobre conjuntos elásticos existentes.
>

| Comando | Descrição |
| --- | --- |
|[Criar base de dados (SQL Database do Azure)](/sql/t-sql/statements/create-database-azure-sql-database)|Cria uma nova base de dados num conjunto existente ou como uma base de dados. Tem de estar ligado à base de dados principal para criar uma nova base de dados.|
| [Falha de ALTER DATABASE (base de dados SQL do Azure)](/sql/t-sql/statements/alter-database-azure-sql-database) |Mova uma base de dados para, de ou entre conjuntos elásticos.|
|[REMOVER a base de dados (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Elimina uma base de dados.|
|[sys.elastic_pool_resource_stats (SQL Database do Azure)](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)|Devolve estatísticas de utilização de recursos para todos os conjuntos de bases de dados elásticas um servidor lógico. Para cada conjunto de bases de dados elásticas, não há uma linha para cada segundo 15 reporting janela (quatro linhas por minuto). Isto inclui a CPU, e/s, registo, o consumo de armazenamento e utilização de pedido/sessão em simultâneo por todas as bases de dados no conjunto.|
|[sys.database_service_objectives (SQL Database do Azure)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Devolve o edition (camada de serviço), o objetivo de serviço (escalão de preço) e o nome do conjunto elástico, se existir, para uma base de dados SQL do Azure ou um Azure SQL Data Warehouse. Se tem sessão iniciada base de dados mestra num servidor de base de dados do Azure SQL, devolve informações sobre todas as bases de dados. Para o Azure SQL Data Warehouse, tem de estar ligado à base de dados mestra.|

## <a name="manage-elastic-pools-and-databases-using-the-rest-api"></a>Gerir conjuntos elásticos e bases de dados utilizando a API REST

Para criar e gerir elástico da base de dados SQL agrupamentos utilizam estes pedidos de REST API.

| Comando | Descrição |
| --- | --- |
|[Conjuntos elásticos - criar ou atualizar](/rest/api/sql/elasticpools/createorupdate)|Cria um novo conjunto elástico ou atualiza um conjunto elástico existente.|
|[Eliminar conjuntos elásticos-](/rest/api/sql/elasticpools/delete)|Elimina o conjunto elástico.|
|[Obter conjuntos elásticos-](/rest/api/sql/elasticpools/get)|Obtém um conjunto elástico.|
|[Conjuntos elásticos - lista pelo servidor](/rest/api/sql/elasticpools/listbyserver)|Devolve uma lista de conjuntos elásticos num servidor.|
|[Conjuntos elásticos - atualização](/rest/api/sql/elasticpools/update)|As atualizações de um conjunto elástico existente.|
|[Recomenda conjuntos elásticos - Get](/rest/api/sql/recommendedelasticpools/get)|Obtém um conjunto elástico recommented.|
|[Recomenda conjuntos elásticos - lista pelo servidor](/rest/api/sql/recommendedelasticpools/listbyserver)|Devolve os conjuntos elásticos recomendados.|
|[Recomenda conjuntos elásticos - as métricas de lista](/rest/api/sql/recommendedelasticpools/listmetrics)|Devolve recommented métricas do conjunto elástico.|
|[Atividades do conjunto elástico](/rest/api/sql/elasticpoolactivities)|Devolve as atividades do conjunto elástico.|
|[Atividades de base de dados do conjunto elástico](/rest/api/sql/elasticpooldatabaseactivities)|Devolve a atividade em bases de dados dentro de um conjunto elástico.|
|[Bases de dados - criar ou atualizar](/rest/api/sql/databases/createorupdate)|Cria uma nova base de dados ou atualiza uma base de dados existente.|
|[Get - bases de dados](/rest/api/sql/databases/get)|Obtém uma base de dados.|
|[Para obter agrupamento elástico de bases de dados-](/rest/api/sql/databases/getbyelasticpool)|Obtém uma base de dados dentro de um conjunto elástico.|
|[Para obter as bases de dados - recomendado conjunto elástico](/rest/api/sql/databases/getbyrecommendedelasticpool)|Obtém uma base de dados dentro de um conjunto elástico recommented.|
|[Bases de dados - lista por agrupamento elástico](/rest/api/sql/databases/listbyelasticpool)|Devolve uma lista de bases de dados num agrupamento elástico.|
|[Bases de dados - lista por conjunto elástico recomendado](/rest/api/sql/databases/listbyrecommendedelasticpool)|Devolve uma lista de bases de dados dentro de um conjunto elástico recommented.|
|[Bases de dados - lista pelo servidor](/rest/api/sql/databases/listbyserver)|Devolve uma lista de bases de dados num servidor.|
|[Bases de dados - atualização](/rest/api/sql/databases/update)|Atualiza a base de dados existente.|

## <a name="next-steps"></a>Passos seguintes

* Para obter um vídeo, consulte [decorrer vídeo do Microsoft Virtual Academy nas capacidades de elásticas de SQL Database do Azure](https://mva.microsoft.com/training-courses/elastic-database-capabilities-with-azure-sql-db-16554)
* Para saber mais sobre os padrões de estrutura de aplicações SaaS que utilizam conjuntos elásticos, consulte o artigo [Padrões de Estrutura de Aplicações SaaS Multi-inquilino com a Base de Dados SQL do Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md).
* Para um tutorial de SaaS que utilizam conjuntos elásticos, consulte [introdução à aplicação Wingtip SaaS](sql-database-wtp-overview.md).
