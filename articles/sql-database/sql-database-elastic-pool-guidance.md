---
title: "Quando deve ser utilizado um conjunto elástico?"
description: "Um conjunto elástico é uma coleção de recursos disponíveis que são partilhados por um grupo de bases de dados elásticas. Este documento disponibiliza orientações para ajudar a avaliar a adequação de utilizar um conjunto elástico para um grupo de bases de dados."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 3d3941d5-276c-4fd2-9cc1-9fe8b1e4c96c
ms.service: sql-database
ms.custom: multiple databases
ms.devlang: NA
ms.date: 12/19/2016
ms.author: sstein;carlrab
ms.workload: data-management
ms.topic: get-started-article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 6c8420a154d998aa95c0220049ee54b3039a872b
ms.openlocfilehash: a79b78a4e8e683afe5b41a41911e7d5f020eff88


---
# <a name="when-should-an-elastic-pool-be-used"></a>Quando deve ser utilizado um conjunto elástico?
Avalie se a relação de custo-eficiência da utilização de um conjunto elástico compensa com base nos padrões de utilização das bases de dados e nas diferenças de preços entre conjuntos elásticos e bases de dados únicas. Também são disponibilizadas orientações adicionais para ajudar a determinar o tamanho de conjunto atual necessário para um grupo atual de bases de dados SQL.  

* Para obter uma descrição geral dos conjuntos, veja [SQL Database elastic pools (Conjuntos elásticos da Base de Dados SQL)](sql-database-elastic-pool.md).

> [!NOTE]
> Os conjuntos elásticos estão em disponibilidade geral (GA) em todas as regiões do Azure, exceto na Índia Ocidental, onde se encontra, de momento, em pré-visualização. A GA (Disponibilidade Geral) dos conjuntos elásticos nesta região será lançada o mais depressa possível.
>
>

## <a name="elastic-pools"></a>Conjuntos elásticos
Os programadores de SaaS criam as aplicações sobre camadas de dados de grande escala, que consistem em várias bases de dados. Um padrão de aplicação comum é aprovisionar uma base de dados individual para cada cliente. Contudo, muitas vezes, os padrões de cada cliente são distintos e imprevisíveis, sendo difícil prever os requisitos de recursos de cada utilizador de bases de dados individual. Por isso, os programadores podem sobreaprovisionar recursos, com custos consideráveis, para garantir débito e tempos de resposta favoráveis para todas as bases de dados. Ou podem diminuir os custos e arriscar numa experiência de mau desempenho para os clientes. Para saber mais sobre os padrões de estrutura de aplicações SaaS que utilizam conjuntos elásticos, consulte o artigo [Padrões de Estrutura de Aplicações SaaS Multi-inquilino com a Base de Dados SQL do Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md).

Com os conjuntos elásticos na Base de Dados SQL do Azure, os programadores de SaaS podem otimizar o desempenho do preço num grupo de bases de dados dentro de um orçamento prescrito e, ao mesmo tempo, oferecer elasticidade de desempenho para cada base de dados. Os conjuntos permitem aos programadores comprar Unidades de Transação de Bases de Dados elásticas (elastic Database Transaction Units, eDTUs) para conjuntos partilhados por múltiplas bases de dados, de modo a acomodar períodos de utilização imprevisíveis de bases de dados individuais. O requisito de eDTUS dos conjuntos é determinado pela utilização agregada das bases de dados dos mesmos. O número de eDTUs disponíveis para os conjuntos é controlada pelo orçamento do programador. Os conjuntos permitem aos programadores ter em conta o impacto do orçamento no desempenho do conjunto e vice-versa. Os programadores só têm de adicionar bases de dados ao conjunto, definir as eDTUs mínimas e máximas das bases de dados e, depois, definir a eDTU do conjunto com base no orçamento. Os programadores podem utilizar os conjuntos para fazer crescer de forma contínua o serviço, de uma “lean startup” para uma empresa madura e em constante crescimento.  

## <a name="when-to-consider-a-pool"></a>Quando considerar a utilização de um conjunto
Os conjuntos são bastante adequados para um grande número de bases de dados com padrões de utilização específicos. Para uma determinada base de dados, este padrão caracteriza-se por uma utilização média baixa com picos de utilização relativamente raros.

Quantas mais bases de dados adicionar a um conjunto, maior será a poupança. Dependendo do padrão de utilização de aplicações, é possível ver poupanças com um mínimo de duas bases de dados S3.  

As secções seguintes ajudam-no a avaliar se a sua coleção de bases de dados específica pode beneficiar de estar num conjunto. Os exemplos utilizam conjuntos Standard, mas os mesmos princípios também se aplicam aos conjuntos Básicos e Premium.

### <a name="assessing-database-utilization-patterns"></a>Avaliar os padrões de utilização da base de dados
A figura seguinte mostra um exemplo de uma base de dados que está inativa muito tempo, mas que também, periodicamente, apresenta picos de atividade. Este é um padrão de utilização adequado para um conjunto:

   ![uma base de dados individual adequada para um conjunto](./media/sql-database-elastic-pool-guidance/one-database.png)

Para o período de cinco minutos ilustrado, DB1 tem um pico até 90 DTUs, mas a utilização média global é inferior a cinco DTUs. Para executar esta carga de trabalho numa base de dados individual, é necessário o nível de desempenho S3, mas tal deixa a maior dos recursos inutilizados durante os períodos de pouca atividade.

Um conjunto permite que estas DTUs não utilizadas sejam partilhadas entre várias bases de dados, pelo que reduz as DTUs necessárias e os custos gerais.

Com base no exemplo anterior, imagine que existem bases de dados adicionais com padrões de utilização semelhantes a DB1. Nas duas figuras abaixo, é incluída no mesmo gráfico a utilização de quatro bases de dados e de 20 bases de dados, para ilustrar a natureza não sobreposta da respetiva utilização ao longo do tempo:

   ![quatro bases de dados com um padrão de utilização adequado para um conjunto](./media/sql-database-elastic-pool-guidance/four-databases.png)

  ![vinte bases de dados com um padrão de utilização adequado para um conjunto](./media/sql-database-elastic-pool-guidance/twenty-databases.png)

A utilização agregada de DTUs nas 20 bases de dados é ilustrada pela linha preta na figura anterior. Mostra que a utilização agregada de DTUs nunca excede as cem e indica que as 20 bases de dados podem partilhar 100 eDTUs ao longo deste período de tempo. Isto resulta numa redução em 20 vezes das DTUs e numa redução de 13 vezes no preço em comparação com colocar cada base de dados nos níveis de desempenho S3 para bases de dados individuais.

Este exemplo é ideal pelos motivos seguintes:

* Existem grandes diferenças entre a utilização de pico e utilização média por base de dados.  
* A utilização de pico de cada base de dados ocorre em diferentes alturas no tempo.
* Os eDTUs são partilhados entre muitas bases de dados.

O preço de um conjunto é uma função das eDTUs do conjunto. Embora o preço unitário de eDTU de um conjunto seja 1,5 vezes superior ao preço unitário de DTU para bases de dados individuais, **as eDTUs de conjuntos podem ser partilhadas por muitas bases de dados e são necessárias menos eDTUs totais**. Estas distinções nos preços e na partilha das eDTUs são as bases do potencial de poupança nos custos que os conjuntos podem proporcionar.  

As regras básicas seguintes relacionadas com a contagem e a utilização de bases de dados ajudam a garantir que os conjuntos oferecem custos reduzidos quando comparados com a utilização de níveis de desempenho para bases de dados individuais.

### <a name="minimum-number-of-databases"></a>Número mínimo de bases de dados
Se a soma das DTUs dos níveis de desempenho para bases de dados individuais for superior a 1,5 vezes as eDTUs necessárias para o conjunto, a utilização de um conjunto elástico apresenta a melhor relação de custo-eficiência. Para obter os tamanhos disponíveis, veja [eDTU and storage limits for elastic pools and elastic databases (eDTUs e limites de armazenamento dos conjuntos elásticos e das bases de dados elásticas)](sql-database-elastic-pool.md#edtu-and-storage-limits-for-elastic-pools).

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

## <a name="sizing-an-elastic-pool"></a>Dimensionar conjuntos elásticos
O melhor tamanho para um conjunto depende das eDTUs agregadas e dos recursos de armazenamento necessários para todas as bases de dados nesse conjunto. A escolha do tamanho envolve determinar o maior de:

* DTUs máximas utilizadas por todas as bases de dados do conjunto.
* Bytes de armazenamento máximos utilizados por todas as bases de dados do conjunto.

Para obter os tamanhos disponíveis, veja [eDTU and storage limits for elastic pools and elastic databases (eDTUs e limites de armazenamento dos conjuntos elásticos e das bases de dados elásticas)](sql-database-elastic-pool.md#edtu-and-storage-limits-for-elastic-pools).

A Base de Dados SQL avalia automaticamente o histórico de utilização de recursos de bases de dados num servidor de Base de Dados SQL existente e recomenda a configuração de conjunto adequada no portal do Azure. Para além das recomendações, uma experiência incorporada prevê a utilização de eDTUs para um grupo personalizado de bases de dados no servidor. Desta forma, pode fazer uma análise de hipóteses ao adicionar, interativamente, bases de dados ao conjunto e removê-las para ver uma análise da utilização de recursos e obter conselhos de dimensionamento antes de consolidar as alterações. Para obter as instruções, veja [Monitor, manage, and size an elastic pool (Monitorizar, gerir e dimensionar conjuntos elásticos)](sql-database-elastic-pool-manage-portal.md).

Para obter avaliações de utilização de recursos mais flexíveis que permitem estimativas de tamanho ad-hoc para servidores anteriores ao V12 e estimativas de tamanho para bases de dados em diferentes servidores, veja [Powershell script for identifying databases suitable for an elastic pool (Script do PowerShell para identificar bases de dados adequadas para conjuntos elásticos)](sql-database-elastic-pool-database-assessment-powershell.md).

| Capacidade | Experiência do portal | Script do PowerShell |
|:--- |:--- |:--- |
| Granularidade |15 segundos |15 segundos |
| Considera as diferenças de preços entre um conjunto e os níveis de desempenho para bases de dados individuais |Sim |Não |
| Permite personalizar a lista das bases de dados analisadas |Sim |Sim |
| Permite personalizar o período de tempo utilizado na análise |Não |Sim |
| Permite personalizar a lista de bases de dados analisadas em diferentes servidores |Não |Sim |
| Permite personalizar a lista de bases de dados analisadas em servidores v11 |Não |Sim |

Nos casos em que não pode utilizar as ferramentas, as instruções passo a passo seguintes podem ajudá-lo a prever se um conjunto tem uma relação de custo-eficácia melhor do que as bases de dados individuais.

1. Estime as eDTUs necessárias para o conjunto da seguinte forma:

   Máx. (<*Número Total de DBs* X *utilização média de DTUs por DB*>,<br>
   <*Número de DBs com pico em simultâneo* X *utilização de pico de DTUs por DB*)
2. Calcule o espaço de armazenamento necessário para o conjunto ao adicionar o número de bytes de que todas as bases de dados do conjunto precisam. Em seguida, determine o tamanho do conjunto de eDTUs que disponibiliza esta quantidade de armazenamento. Para obter os limites de armazenamento dos conjuntos com base no tamanho do conjunto de eDTUs, veja [eDTU and storage limits for elastic pools and elastic databases (eDTUs e limites de armazenamento dos conjuntos elásticos e das bases de dados elásticas)](sql-database-elastic-pool.md#edtu-and-storage-limits-for-elastic-pools).
3. Escolha a estimativa de eDTUs maior de entre os passos 1 e 2.
4. Veja a [página de preços da Base de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/) e encontre o tamanho de conjunto de eDTU mais pequeno e que seja superior à estimativa do Passo 3.
5. Compare o preço do conjunto do Passo 5 com o preço de utilizar os níveis de desempenho adequado para bases de dados individuais.

## <a name="summary"></a>Resumo
Nem todas as bases de dados individuais são candidatos ideais para conjuntos. As bases de dados que tenham padrões de utilização caracterizados por uma utilização média baixa e picos de utilização relativamente incomuns são excelentes candidatos. Os padrões de utilização das aplicações são dinâmicos, por isso, utilize as informações e as ferramentas descritas neste artigo para fazer a avaliação inicial e ver se os conjuntos são uma boa opção para algumas ou todas as suas bases de dados. Este artigo é apenas um ponto de partida para ajudar a decidir se os conjuntos elásticos são uma boa escolha. Lembre-se de que deve monitorizar continuamente a utilização de recursos histórica e reavaliar, de forma constante, os níveis de desempenho de todas as bases de dados. Tenha em conta que pode mover facilmente bases de dados para dentro e fora dos conjuntos elásticos e, se tiver um grande número de bases de dados, pode ter múltiplos conjuntos de tamanhos diferentes pelos quais as pode dividir.

## <a name="next-steps"></a>Passos seguintes
* [Criar um conjunto elástico](sql-database-elastic-pool-create-portal.md)
* [Monitor, manage, and size an elastic pool (Monitorizar, gerir e dimensionar conjuntos elásticos)](sql-database-elastic-pool-manage-portal.md)
* [Opções e desempenho da Base de Dados SQL: saiba o que está disponível em cada escalão de serviço](sql-database-service-tiers.md)
* [PowerShell script for identifying databases suitable for an elastic pool (Script do PowerShell para identificar bases de dados adequadas para conjuntos elásticos)](sql-database-elastic-pool-database-assessment-powershell.md)



<!--HONumber=Jan17_HO1-->


