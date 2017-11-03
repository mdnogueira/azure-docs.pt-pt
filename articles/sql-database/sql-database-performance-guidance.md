---
title: "Orientações de otimização do desempenho de base de dados SQL do Azure | Microsoft Docs"
description: "Saiba como utilizar as recomendações para melhorar o desempenho de consulta de SQL Database do Azure."
services: sql-database
documentationcenter: na
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: dd8d95fa-24b2-4233-b3f1-8e8952a7a22b
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: On Demand
ms.date: 02/09/2017
ms.author: carlrab
ms.openlocfilehash: 5dc245a29a9106156c207ed7394f8bb289db729e
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="tuning-performance-in-azure-sql-database"></a>Otimização de desempenho na SQL Database do Azure

Base de dados SQL do Azure fornece [recomendações](sql-database-advisor.md) que pode utilizar para melhorar o desempenho da base de dados, ou pode deixar a base de dados do Azure SQL [automaticamente se adaptam a sua aplicação](sql-database-automatic-tuning.md) e aplicar as alterações que irão melhorar o desempenho da sua carga de trabalho.

Em não tem recomendações aplicáveis e continuará a ter problemas de desempenho, pode utilizar os seguintes métodos para melhorar os desempenhos:
1. Aumentar [escalões de serviço](sql-database-service-tiers.md) e fornecer mais recursos à base de dados.
2. Otimizar a sua aplicação e aplicam-se algumas melhores práticas que podem melhorar o desempenho. 
3. Otimize a base de dados ao alterar índices e consultas de forma mais eficiente trabalhar com dados.

Estes são os métodos manuais porque terá de decidir o que [escalões de serviço](sql-database-service-tiers.md) deverá escolher ou terá de reescrever o código de aplicação ou a base de dados e implementar as alterações.

## <a name="increasing-performance-tier-of-your-database"></a>Aumentar a camada de desempenho da base de dados

Base de dados SQL do Azure oferece quatro [escalões de serviço](sql-database-service-tiers.md) que pode escolher entre: básicas, Standard, Premium e Premium RS (desempenho é medido em unidades de débito de base de dados, ou [DTUs](sql-database-what-is-a-dtu.md). Cada escalão de serviço estritamente isola os recursos que pode utilizar a base de dados do SQL Server e garante o desempenho previsível que nível de serviço. Neste artigo, vamos oferecem orientações que podem ajudar a escolher o escalão de serviço para a sua aplicação. Vamos discutir também formas que pode otimizar a sua aplicação para tirar o máximo da SQL Database do Azure.

> [!NOTE]
> Este artigo incida no guia de desempenho das bases de dados na base de dados do Azure SQL. Para orientações de desempenho relacionados com conjuntos elásticos, consulte [considerações sobre preço e desempenho para conjuntos elásticos](sql-database-elastic-pool-guidance.md). Tenha em atenção, no entanto, pode aplicar muitas das recomendações otimização neste artigo para bases de dados num conjunto elástico e obter os benefícios de desempenho semelhante.
> 

* **Básico**: O básico serviço camada oferece um bom desempenho à previsibilidade para cada base de dados, de hora mais de hora. Numa base de dados básica, recursos suficientes suportam um bom desempenho na base de dados pequena que não tem vários pedidos em simultâneo. Casos de utilização típica quando deverá utilizar o escalão de serviço básico são:
  * **Apenas começar a utilizar com a SQL Database do Azure**. As aplicações que estão em desenvolvimento, muitas vezes, não precisam de níveis de elevado desempenho. Bases de dados básicas são um ambiente ideal para o desenvolvimento de base de dados ou de teste, num ponto de preços baixa.
  * **Tiver uma base de dados com um único utilizador**. As aplicações que associa um utilizador único uma base de dados, normalmente, não tem requisitos de concorrência e desempenho elevados. Estas aplicações são candidatos para a camada de serviço básico.
* **Standard**: O padrão camada de serviço oferece desempenho melhorado previsão e fornece um bom desempenho para bases de dados que tenham vários pedidos simultâneos, como o grupo de trabalho e aplicações web. Ao escolher uma base de dados de camada de serviço Standard, tamanho da aplicação de base de dados com base no desempenho previsível, minuto minutos.
  * **A base de dados tem vários pedidos simultâneos**. Aplicações de serviço mais do que um utilizador num momento normalmente têm níveis de desempenho superiores. Por exemplo, aplicações web ou de grupo de trabalho que têm baixa, média requisitos de tráfego de e/s suportando várias consultas em simultâneo são bons candidatos para a camada de serviço Standard.
* **Premium**: camada de serviço o Premium fornece um desempenho previsível, segundo por segundo, para cada base de dados Premium. Quando escolher o escalão de serviço Premium, pode dimensione a sua aplicação de base de dados com base no pico de carga para a base de dados. O plano remove casos em que desempenho variância pode fazer com que pequeno consultas a demorar mais do que o esperado nas operações de sensíveis à latência. Este modelo pode simplificar bastante aos ciclos de validação de produto e desenvolvimento de aplicações que precisam para fazer fortes instruções sobre necessidades de recursos de pico, a variância de desempenho ou a latência de consulta. A maioria dos casos de utilização do escalão de serviço de Premium têm um ou mais destas características:
  * **Elevada pico de carga**. Uma aplicação que requer substanciais CPU, memória ou entrada/saída (e/s) para concluir as suas operações requer um nível dedicado e de elevado desempenho. Por exemplo, uma operação de base de dados conhecida para consumir vários núcleos de CPU para um período de tempo alargado é uma candidata para a camada de serviço Premium.
  * **Demasiados pedidos simultâneos**. Algumas aplicações de base de dados serviço demasiados pedidos simultâneos, por exemplo, quando a servir um Web site que tem um volume de tráfego elevado. Básico e padrão escalões de serviço limitam o número de pedidos em simultâneo por base de dados. Aplicações que necessitam de mais ligações seriam necessário escolher um tamanho de reserva adequado para processar o número máximo de pedidos necessários.
  * **Baixa latência**. Algumas aplicações precisam de garantir uma resposta da base de dados em tempo mínimo. Se um procedimento armazenado específico denomina-se como parte de uma operação de cliente mais vasto, poderá ter um requisito para que a partir desse chamada de retorno em mais do que 20 milissegundos, 99 por cento do tempo. Este tipo de beneficia de aplicação de camada de serviço Premium, para se certificar de que a capacidade de computação necessária está disponível.
* **Premium RS**: O Premium RS camada foi concebida para cargas de trabalho de e/s intensivas que não necessitam de garante a disponibilidade mais elevada. Os exemplos incluem o teste de cargas de trabalho de elevado desempenho ou uma carga de trabalho analítica em que a base de dados não é o sistema de registo.

O nível de serviço que necessita para a base de dados do SQL Server depende dos requisitos de carregamento de pico para a dimensão de cada recurso. Algumas aplicações utilizam uma quantidade trivial de um único recurso, mas têm requisitos significativos para outros recursos.

### <a name="service-tier-capabilities-and-limits"></a>Capacidades de camada de serviço e limites

Em cada camada de serviço, defina o nível de desempenho, pelo que tem a flexibilidade de paga apenas a capacidade que tem. Pode [ajustar capacidade](sql-database-service-tiers.md), ou reduzir vertical, como as alterações de carga de trabalho. Por exemplo, se a carga de trabalho de base de dados elevada durante a época de compras de back-profissional, pode aumentar o nível de desempenho da base de dados durante um período de tempo definido, Julho através de Setembro. Pode reduzir quando termina a época de pico. Pode minimizar paga por otimizar o seu ambiente na nuvem da sazonalidade da sua empresa. Este modelo também funciona bem para ciclos de versão de produto de software. Um agrupamento de teste poderá atribuir capacidade enquanto teste é executado e, em seguida, liberte essa capacidade quando terminarem de teste. Um modelo de pedido de capacidade, paga pelos capacidade à medida que precisa dele e evitar despesas em recursos dedicados que raramente poderá utilizar.

### <a name="why-service-tiers"></a>Por que motivo escalões de serviço?
Apesar de cada carga de trabalho de base de dados pode ser diferente, o objetivo de escalões de serviço é fornecer previsão de desempenho em vários níveis de desempenho. Clientes com os requisitos de recursos de base de dados em grande escala, podem trabalhar num ambiente informático mais dedicado.

## <a name="tune-your-application"></a>Otimizar a sua aplicação
No tradicional no SQL Server local, o processo de planeamento de capacidade inicial, muitas vezes, é separado do processo de execução de uma aplicação na produção. Produto de hardware e licenças adquiridas pela primeira vez, e a otimização de desempenho é efetuada posteriormente. Quando utilizar a SQL Database do Azure, é boa ideia interweave o processo de execução de uma aplicação e otimização-lo. Com o modelo de pagar capacidade a pedido, pode otimizar a sua aplicação para utilizar os recursos mínimo necessários agora, em vez de provocam um aprovisionamento no hardware baseiam guesses dos planos de crescimento futuro para uma aplicação, que frequentemente estão incorretos. Alguns clientes podem optar por não otimizar uma aplicação e, em vez disso, optar por overprovision recursos de hardware. Esta abordagem poderá ser aconselhável se não quiser alterar uma aplicação chave durante um período ocupado. No entanto, a otimização de uma aplicação pode minimizar os requisitos de recursos e inferiores faturas mensais quando utiliza os escalões de serviço na SQL Database do Azure.

### <a name="application-characteristics"></a>Características de aplicação
Apesar dos escalões de serviço de base de dados do Azure SQL foram concebidos para melhorar a estabilidade do desempenho e à previsibilidade para uma aplicação, algumas melhores práticas podem ajudá-lo a otimizar a aplicação para melhor tirar partido dos recursos a um nível de desempenho. Embora muitas aplicações tenham ganhos de desempenho significativas, simplesmente ao mudar para um nível de desempenho superior ou camada de serviço, algumas aplicações precisam de otimização adicional para tirar partido de um nível mais elevado de serviço. Para um melhor desempenho, considere a otimização de aplicação adicional para aplicações que têm estas características:

* **As aplicações que têm um desempenho lento devido a "chatty" comportamento**. Aplicações chatty efetuar operações de acesso de dados excessivo são sensíveis à latência de rede. Poderá ter de modificar estes tipos de aplicações para reduzir o número de operações de acesso de dados na base de dados do SQL Server. Por exemplo, pode melhorar o desempenho da aplicação através da utilização de técnicas, como a criação de batches de consultas ad-hoc ou mover as consultas para procedimentos armazenados. Para obter mais informações, consulte [Batch consultas](#batch-queries).
* **Bases de dados com uma carga de trabalho que consomem muita que não pode ser suportado por uma única máquina toda**. As bases de dados excedem os recursos com o nível de desempenho Premium mais alto podem beneficiar aumentar horizontalmente a carga de trabalho. Para obter mais informações, consulte [fragmentação entre bases de dados](#cross-database-sharding) e [criação de partições funcionais](#functional-partitioning).
* **As aplicações que têm consultas inferior ao ideal –**. Aplicações, especialmente na camada de acesso de dados, que tem mal otimizados consultas não poderão beneficiar de um nível de desempenho superior. Isto inclui consultas que não dispõem de uma cláusula WHERE, tem em falta índices ou tem desatualizada estatísticas. Estas aplicações beneficiam técnicas de otimização de desempenho de consulta padrão. Para obter mais informações, consulte [em falta índices](#identifying-and-adding-missing-indexes) e [consultar Otimização e hinting](#query-tuning-and-hinting).
* **Design de acesso a aplicações que tenham dados inferior ao ideal –**. As aplicações com problemas de simultaneidade de acesso de dados inerente, deadlocking por exemplo, não poderão beneficiar de um nível de desempenho superior. Considere a redução de ida e volta na base de dados do SQL do Azure por dados de colocação em cache do lado do cliente com o serviço de colocação em cache do Azure ou outra tecnologia de colocação em cache. Consulte [colocação em cache de camada de aplicação](#application-tier-caching).

## <a name="tune-your-database"></a>Otimizar a base de dados
Nesta secção, vamos ver alguns técnicas que pode utilizar para otimizar a base de dados do Azure SQL para obter o melhor desempenho para a sua aplicação e executá-la no nível mais baixo de desempenho possível. Algumas destas técnicas correspondem tradicionais do SQL Server de otimização de melhores práticas, mas outros são específicos para a SQL Database do Azure. Em alguns casos, pode examinar os recursos foram consumidos para uma base de dados localizar as áreas mais otimizar e expandir tradicionais do SQL Server técnicas para funcionar na SQL Database do Azure.

### <a name="identify-performance-issues-using-azure-portal"></a>Identificar problemas de desempenho através do portal do Azure
As seguintes ferramentas no portal do Azure podem ajudar a analisar e corrigir problemas de desempenho com a base de dados do SQL Server:

* [Query Performance Insight](sql-database-query-performance.md)
* [Assistente da Base de Dados SQL](sql-database-advisor.md)

O portal do Azure tem mais informações sobre ambas estas ferramentas e como utilizá-los. Para diagnosticar e corrigir problemas de eficientemente, recomendamos que primeiro tente as ferramentas no portal do Azure. Recomendamos que utilize o manual de otimização de abordagens que discutimos a em seguida, em falta índices e otimizar a consulta, em casos especiais.

Encontrar mais informações sobre como identificar problemas na SQL Database do Azure no [monitorização do desempenho](sql-database-single-database-monitor.md) artigo.

### <a name="identifying-and-adding-missing-indexes"></a>Identificar e adicionar os índices em falta
Um problema comum no desempenho da base de dados OLTP relacionado com a estrutura de base de dados físico. Muitas vezes, esquemas de base de dados são concebidas e fornecidas sem as testar à escala (a carga ou no volume de dados). Infelizmente, o desempenho de um plano de consulta poderá ser aceitável em pequena escala, mas diminuir substancialmente em volumes de dados de nível de produção. A origem mais comum deste problema é a falta de índices adequadas para satisfazer os filtros ou outras restrições numa consulta. Muitas vezes, falta de índices de manifestos como uma tabela de análise quando uma procura do índice foi suffice.

Neste exemplo, o plano de consulta selecionada utiliza uma análise quando uma procura seria suffice:

    DROP TABLE dbo.missingindex;
    CREATE TABLE dbo.missingindex (col1 INT IDENTITY PRIMARY KEY, col2 INT);
    DECLARE @a int = 0;
    SET NOCOUNT ON;
    BEGIN TRANSACTION
    WHILE @a < 20000
    BEGIN
        INSERT INTO dbo.missingindex(col2) VALUES (@a);
        SET @a += 1;
    END
    COMMIT TRANSACTION;
    GO
    SELECT m1.col1
    FROM dbo.missingindex m1 INNER JOIN dbo.missingindex m2 ON(m1.col1=m2.col1)
    WHERE m1.col2 = 4;

![Um plano de consulta com índices em falta](./media/sql-database-performance-guidance/query_plan_missing_indexes.png)

Base de dados SQL do Azure pode ajudar a localizar e corrigir comuns em falta condições de índice. DMVs incorporadas em SQL Database do Azure observe compilações de consulta em que um índice seria reduzir significativamente o custo estimado para executar uma consulta. Durante a execução de consulta, base de dados SQL controla a frequência é executado o plano de consulta e controla o intervalo estimado entre o plano de consulta em execução e aquele imagined onde existia nesse índice. Pode utilizar estes DMVs adivinhar rapidamente quais as alterações à sua estrutura física da base de dados poderão melhorar o custo global de carga de trabalho para uma base de dados e a carga de trabalho real.

Pode utilizar esta consulta avaliar potenciais índices em falta:

    SELECT CONVERT (varchar, getdate(), 126) AS runtime,
        mig.index_group_handle, mid.index_handle,
        CONVERT (decimal (28,1), migs.avg_total_user_cost * migs.avg_user_impact *
                (migs.user_seeks + migs.user_scans)) AS improvement_measure,
        'CREATE INDEX missing_index_' + CONVERT (varchar, mig.index_group_handle) + '_' +
                  CONVERT (varchar, mid.index_handle) + ' ON ' + mid.statement + '
                  (' + ISNULL (mid.equality_columns,'')
                  + CASE WHEN mid.equality_columns IS NOT NULL
                              AND mid.inequality_columns IS NOT NULL
                         THEN ',' ELSE '' END + ISNULL (mid.inequality_columns, '')
                  + ')'
                  + ISNULL (' INCLUDE (' + mid.included_columns + ')', '') AS create_index_statement,
        migs.*,
        mid.database_id,
        mid.[object_id]
    FROM sys.dm_db_missing_index_groups AS mig
    INNER JOIN sys.dm_db_missing_index_group_stats AS migs
        ON migs.group_handle = mig.index_group_handle
    INNER JOIN sys.dm_db_missing_index_details AS mid
        ON mig.index_handle = mid.index_handle
    ORDER BY migs.avg_total_user_cost * migs.avg_user_impact * (migs.user_seeks + migs.user_scans) DESC

Neste exemplo, a consulta resultou neste Sugestão:

    CREATE INDEX missing_index_5006_5005 ON [dbo].[missingindex] ([col2])  

Depois de criado, essa mesma instrução SELECT escolhe um plano diferente, que utiliza uma procura em vez de uma análise e, em seguida, executa o plano de forma mais eficiente:

![Um plano de consulta com índices corrigidos](./media/sql-database-performance-guidance/query_plan_corrected_indexes.png)

As informações de chaves é que a capacidade de e/s de um sistema de mercadoria partilhado, é mais limitada do que uma máquina de servidor dedicado. Há um premium na minimizando desnecessários e/s para tirar partido máximo do sistema no DTU de cada nível de desempenho das camadas de serviços de SQL Database do Azure. Estrutura de base de dados físico adequado escolhas podem significativamente melhorar a latência para consultas individuais, melhorar o débito de pedidos simultâneos processadas por unidade de escala e minimizar os custos necessários para satisfazer a consulta. Para mais informações sobre o índice em falta DMVs, consulte [sys.dm_db_missing_index_details](https://msdn.microsoft.com/library/ms345434.aspx).

### <a name="query-tuning-and-hinting"></a>Otimização da consulta e hinting
O otimizador de consultas na base de dados do Azure SQL é semelhante ao otimizador de consultas de SQL Server tradicional. A maioria das melhores práticas para consultas de Otimização e compreender o raciocínio limitações de modelo para o otimizador de consultas também se aplicam a SQL Database do Azure. Se a otimizar as consultas na base de dados do Azure SQL, pode obter o benefício adicional de reduzir as exigências do recurso agregado. A aplicação poderá ser capaz de executar um custo mais baixo que equivalente untuned porque pode ser executado num nível de desempenho inferior.

É um exemplo comum no SQL Server e que também se aplica a SQL Database do Azure como o otimizador de consultas "interceta" parâmetros. Durante a compilação, o otimizador de consultas avalia o valor atual de um parâmetro para determinar se pode gerar um plano de consulta mais ideal. Embora muitas vezes, esta estratégia pode levar a um plano de consulta que é significativamente mais rápido do que um plano compilado sem os valores de parâmetros conhecido, atualmente funciona imperfectly ambos no SQL Server e no SQL Database do Azure. Por vezes, o parâmetro é intercetados não na e, por vezes, o parâmetro é intercetados na, mas o plano gerado é inferior ao ideal para o conjunto completo de valores de parâmetros numa carga de trabalho. Microsoft inclui sugestões de consulta (diretivas) para que possa especificar intenção mais deliberadamente e substituir o comportamento predefinido do parâmetro de interceção. Muitas vezes, se utilizar sugestões, pode corrigir casos em que o comportamento predefinido do SQL Server ou SQL Database do Azure é imperfect para uma carga de trabalho do cliente específico.

O exemplo seguinte demonstra como o processador de consultas pode gerar um plano que é inferior ao ideal para desempenho e requisitos de recursos. Este exemplo mostra também a que se utilizar uma sugestão de consulta, pode reduzir os requisitos de recursos e tempo de execução da consulta da base de dados do SQL Server:

    DROP TABLE psptest1;
    CREATE TABLE psptest1(col1 int primary key identity, col2 int, col3 binary(200));

    DECLARE @a int = 0;
    SET NOCOUNT ON;
    BEGIN TRANSACTION
    WHILE @a < 20000
    BEGIN
        INSERT INTO psptest1(col2) values (1);
        INSERT INTO psptest1(col2) values (@a);
        SET @a += 1;
    END
    COMMIT TRANSACTION
    CREATE INDEX i1 on psptest1(col2);
    GO

    CREATE PROCEDURE psp1 (@param1 int)
    AS
    BEGIN
        INSERT INTO t1 SELECT * FROM psptest1
        WHERE col2 = @param1
        ORDER BY col2;
    END
    GO

    CREATE PROCEDURE psp2 (@param2 int)
    AS
    BEGIN
        INSERT INTO t1 SELECT * FROM psptest1 WHERE col2 = @param2
        ORDER BY col2
        OPTION (OPTIMIZE FOR (@param2 UNKNOWN))
    END
    GO

    CREATE TABLE t1 (col1 int primary key, col2 int, col3 binary(200));
    GO

O código de configuração cria uma tabela que tem skewed a distribuição de dados. O plano de consultas difere consoante o parâmetro está selecionado. Infelizmente, o plano de comportamento a colocação em cache não sempre recompilar a consulta com base no valor de parâmetro mais comuns. Por isso, é possível um plano inferior ao ideal para ser colocado em cache e utilizado para vários valores, mesmo quando um plano diferente pode ser uma melhor opção de plano em média. Em seguida, o plano de consulta cria dois procedimentos armazenados que sejam idênticos, à exceção de um tem uma sugestão de consulta especiais.

**Exemplo, parte 1**

    -- Prime Procedure Cache with scan plan
    EXEC psp1 @param1=1;
    TRUNCATE TABLE t1;

    -- Iterate multiple times to show the performance difference
    DECLARE @i int = 0;
    WHILE @i < 1000
    BEGIN
        EXEC psp1 @param1=2;
        TRUNCATE TABLE t1;
        SET @i += 1;
    END

**Exemplo, parte 2**

(Recomendamos que aguarde pelo menos 10 minutos antes de começar a parte 2 de exemplo, para que os resultados são diferentes dos dados de telemetria resultante.)

    EXEC psp2 @param2=1;
    TRUNCATE TABLE t1;

    DECLARE @i int = 0;
    WHILE @i < 1000
    BEGIN
        EXEC psp2 @param2=2;
        TRUNCATE TABLE t1;
        SET @i += 1;
    END

Cada parte deste exemplo tenta executar uma instrução insert parametrizada 1.000 vezes (para gerar uma carga suficiente para utilizar como um conjunto de dados de teste). Quando executa a procedimentos armazenados, o processador de consultas examina o valor do parâmetro que é transmitido para o procedimento durante a respetiva compilação primeiro (parâmetro "de interceção"). O processador coloca em cache o plano resultante e utiliza-o para invocações posteriores, mesmo que o valor do parâmetro é diferente. O plano ideal não pode ser utilizado em todos os casos. Por vezes, precisa de guia otimizador para escolher um plano que é melhor para as maiúsculas e minúsculas média, em vez das maiúsculas e minúsculas específica do quando a consulta foi compilada primeiro. Neste exemplo, o plano inicial gera um plano de "verificação", que lê a todas as linhas para encontrar cada valor que corresponde do parâmetro:

![Consulta de otimização através da utilização de um plano de análise](./media/sql-database-performance-guidance/query_tuning_1.png)

Porque o procedimento é executada ao utilizar o valor de 1, o plano resultante foi ideal para o valor 1, mas foi inferior ao ideal para todos os outros valores na tabela. O resultado provavelmente não é o que seria aconselhável se escolher o plano de cada aleatoriamente, porque o plano efetua mais lentamente e utiliza mais recursos.

Se executar o teste com `SET STATISTICS IO` definido como `ON`, o trabalho de análise lógica neste exemplo é efetuado em segundo plano. Pode ver que existem 1,148 leituras efetuadas pelo plano (que é ineficaz, se for o caso de média devolver apenas uma linha):

![Consulta de otimização, utilizando uma análise lógica](./media/sql-database-performance-guidance/query_tuning_2.png)

A segunda parte do exemplo utiliza uma sugestão de consulta para informar o otimizador de utilizar um valor específico durante o processo de compilação. Neste caso, força-o processador de consultas para ignorar o valor que é transmitido como parâmetro, e, em vez disso, para assumir `UNKNOWN`. Isto refere-se um valor que tem a frequência de média na tabela (ignorando o desfasamento). O plano resultante é um plano com base na procura que é mais rápido e utiliza menos recursos, em média, o plano na parte 1 deste exemplo:

![Consulta de otimização, utilizando uma sugestão de consulta](./media/sql-database-performance-guidance/query_tuning_3.png)

Pode ver o efeito no **resource_stats** tabela (há um atraso desde o momento em que executar o teste e quando os dados preenche a tabela). Para este exemplo, parte 1 executada durante a janela de tempo de 25:22:00 e a parte 2 executada às 22:35:00. A janela de tempo anterior utilizados mais recursos nessa janela de tempo que o posterior (devido a melhorias de eficiência de plano).

    SELECT TOP 1000 *
    FROM sys.resource_stats
    WHERE database_name = 'resource1'
    ORDER BY start_time DESC

![Consulta de otimização de resultados de exemplo](./media/sql-database-performance-guidance/query_tuning_4.png)

> [!NOTE]
> Embora o volume neste exemplo é intencionalmente pequeno, o efeito de parâmetros inferior ao ideal – pode ser significativo, especialmente nas bases de dados maior. A diferença, em casos extremos, pode estar entre segundos para casos rápidos e horas para os casos lentas.
> 
> 

Pode examinar **resource_stats** para determinar se o recurso para um teste utiliza mais ou menos recursos do que outro teste. Quando se comparam os dados, separar a temporização de testes para que não estejam na mesma janela de 5 minutos no **resource_stats** vista. O objetivo do exercício é para minimizar a quantidade total de recursos utilizados e não para minimizar os recursos das horas de ponta. Geralmente, otimizar um fragmento de código para latência também reduz o consumo de recursos. Certifique-se de que as alterações que efetuar para uma aplicação são necessárias e de que as alterações não afetam negativamente a experiência do cliente para alguém que possam estar a utilizar sugestões de consulta na aplicação.

Se uma carga de trabalho tem um conjunto de repetir consultas, muitas vezes, faz sentido para capturar e validar optimality das opções do plano porque os discos a unidade de tamanho de recurso mínimo necessária para alojar a base de dados. Depois de validá-lo, ocasionalmente reexamine os planos para o ajudar a certificar-se de que não tenham degradado. Pode saber mais sobre [consultar sugestões (Transact-SQL)](https://msdn.microsoft.com/library/ms181714.aspx).

### <a name="cross-database-sharding"></a>Fragmentação entre bases de dados
Porque a SQL Database do Azure é executado no hardware do produto, os limites de capacidade para uma base de dados são inferiores para uma instalação do SQL Server tradicional no local. Alguns clientes utilizam técnicas de fragmentação para propagar-se operações de base de dados através de várias bases de dados quando as operações não cabem dentro dos limites de uma base de dados na base de dados do Azure SQL. A maioria dos clientes que utilizam técnicas de fragmentação na SQL Database do Azure dividir os seus dados numa única dimensão por várias bases de dados. Esta abordagem, tem de compreender que aplicações OLTP frequentemente efetuar transações que se aplicam apenas uma linha ou um pequeno grupo de linhas do esquema.

> [!NOTE]
> Base de dados do SQL Server fornece agora uma biblioteca para melhorar a fragmentação. Para obter mais informações, consulte [descrição geral da biblioteca de cliente da linha de base de dados elástica](sql-database-elastic-database-client-library.md).
> 
> 

Por exemplo, se uma base de dados tiver o nome de cliente, a ordem e detalhes da encomenda (como exemplo tradicional Northwind base de dados que é fornecido com o SQL Server), foi dividir dados em várias bases de dados através do agrupamento de um cliente com a ordem relacionado e as informações de detalhe de ordem. Pode garantir que os dados do cliente permanecem na base de dados individual. A aplicação iria dividir diferentes clientes por bases de dados, de forma eficaz propagando-se a carga em várias bases de dados. Fragmentação, os clientes não só podem evitar o limite de tamanho máximo da base de dados, mas SQL Database do Azure também pode processar as cargas de trabalho significativamente maiores do que os limites dos níveis de desempenho diferentes, desde que cada base de dados individuais se ajusta à sua DTU.

Apesar de fragmentação de base de dados não reduzir a capacidade de recursos de agregação para uma solução, é altamente eficaz em soluções muito grande que são distribuídas por várias bases de dados de suporte. Cada base de dados pode executar um nível diferente de desempenho para suportar muito grande, bases de dados "Efetivo" com os requisitos de recursos de elevado.

### <a name="functional-partitioning"></a>Criação de partições funcionais
Utilizadores do SQL Server, muitas vezes, combinam muitas funções de uma base de dados. Por exemplo, se uma aplicação tiver lógica para gerir o inventário para um arquivo, essa base de dados pode ter lógica associada inventário, controlar as ordens de compra, procedimentos armazenados e vistas indexadas ou materializadas que gerir relatórios do fim do mês. Esta técnica torna mais fácil administrar a base de dados de operações como a cópia de segurança, mas também requer que para o tamanho de hardware ao processar o pico de carga em todas as funções de uma aplicação.

Se utilizar uma arquitetura de escalamento horizontal na SQL Database do Azure, é boa ideia dividir diferentes funções de uma aplicação em bases de dados diferentes. Ao utilizar esta técnica, cada aplicação dimensiona de forma independente. Como uma aplicação fica busier (e aumenta a carga na base de dados), o administrador pode escolher níveis de desempenho independente para cada função na aplicação. Atingiu o limite, com esta arquitetura, uma aplicação pode ser maior do que uma máquina de mercadoria único pode processar, porque a carga é distribuída por várias máquinas.

### <a name="batch-queries"></a>Consultas de batch
Para aplicações que acedam aos dados através da utilização de volume elevado, frequentes, consultar o ad hoc, uma quantidade substancial de tempo de resposta é gasto em comunicações de rede entre a camada de aplicação e a camada de base de dados do Azure SQL. Mesmo quando a aplicação e a SQL Database do Azure estão no mesmo centro de dados, a latência de rede entre os dois poderá ser magnified por um grande número de dados de operações de acesso. Para reduzir a rede de ida e volta para as operações de acesso de dados, considere utilizar a opção para optar por lote de consultas ad hoc ou para compilar o procedimentos armazenados como. Se o lote de consultas ad hoc, pode enviar várias consultas como um lote grande em viagem uma único para a SQL Database do Azure. Se compilar consultas ad hoc num procedimento armazenado, que poderá alcançar o mesmo resultado como se o lote de-los. Utilizar um procedimento armazenado também oferece o benefício de aumentar as possibilidades de colocação em cache os planos de consulta na base de dados do Azure SQL, pelo que pode utilizar o procedimento armazenado novamente.

Algumas aplicações são escrita intensivas. Por vezes, pode reduzir a carga de e/s total numa base de dados por considerar como a juntar em operações de escrita. Muitas vezes, isto é tão simple quanto utilizar transações explícitas em vez de transações de consolidação automática em lotes ad hoc e procedimentos armazenados. Para uma edição de avaliação de diferentes técnicas que pode utilizar, consulte [técnicas para aplicações de base de dados SQL do Azure de criação de batches](https://msdn.microsoft.com/library/windowsazure/dn132615.aspx). Experimentar com a suas próprias carga de trabalho para localizar o modelo de direita para a criação de batches. Lembre-se de que compreende que um modelo poderá ter garantias de consistência transacional ligeiramente diferentes. Localizar a carga de trabalho à direita que minimiza a utilização de recursos requer localizar a combinação certa de compromissos consistência e o desempenho.

### <a name="application-tier-caching"></a>Colocação em cache de camada de aplicação
Algumas aplicações de base de dados têm cargas de trabalho pesado de leitura. Colocação em cache camadas pode reduzir a carga na base de dados e pode, possivelmente, reduzir o nível de desempenho necessário para suportar uma base de dados ao utilizar a SQL Database do Azure. Com [a Cache de Redis do Azure](https://azure.microsoft.com/services/cache/), se tiver uma carga de trabalho pesado de leitura, pode ler os dados uma vez (ou, talvez, uma vez por máquina de camada de aplicação, dependendo de como estiver configurado) e, em seguida, armazenar esses dados fora da sua base de dados do SQL Server. Esta é uma forma para reduzir a carga de base de dados (CPU e e/s de leitura), mas não existe um efeito em consistência transacional porque os dados a ser lidos a partir da cache poderão ser sincronizados com os dados na base de dados. Embora muitas aplicações algum nível de inconsistência é aceitável, que não é true para todas as cargas de trabalho. Deve compreender os requisitos da aplicação antes de implementar uma estratégia de colocação em cache de camada de aplicação.

## <a name="next-steps"></a>Passos seguintes
* Para obter mais informações sobre os escalões de serviço, consulte [opções de base de dados SQL e o desempenho](sql-database-service-tiers.md)
* Para obter mais informações sobre conjuntos elásticos, consulte [o que é um conjunto elástico do Azure?](sql-database-elastic-pool.md)
* Para obter informações sobre agrupamentos de desempenho e elásticos, consulte [quando considerar um conjunto elástico](sql-database-elastic-pool-guidance.md)

