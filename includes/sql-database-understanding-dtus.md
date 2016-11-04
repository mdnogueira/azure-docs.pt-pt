A Unidade de Transação da Base de Dados (DTU) é a unidade de medida na SQL Database que representa a potência relativa das bases de dados baseada numa medida do mundo real: a transação da base de dados. Pegámos num conjunto de operações que são características do processamento de transações online (OLTP) e medimos quantas transações foi possível concluir por segundo em condições de carga completa (que é a versão abreviada, pode ler todos os detalhes em [Descrição geral de referência](../articles/sql-database/sql-database-benchmark-overview.md)). 

Por exemplo, uma base de dados Premium P11 com 1750 DTUs fornece 350 x mais potência de computação de DTUs do que uma base de dados básica com cinco DTUs. 

![Introdução à SQL Database: DTUs da base de dados individual por camada e nível.](./media/sql-database-understanding-dtus/single_db_dtus.png)

> [!NOTE]
> Se está a migrar uma base de dados SQL Server existente, pode utilizar uma ferramenta de terceiros, a [Calculadora de DTUs da SQL Database do Azure](http://dtucalculator.azurewebsites.net/), para obter uma estimativa do nível de desempenho e a camada de serviço de que a sua base de dados poderá necessitar na SQL Database do Azure.
> 
> 

### DTU vs. eDTU
A DTU das bases de dados individuais é diretamente convertida para a eDTU das bases de dados elásticas. Por exemplo, uma base de dados num Conjunto de bases de dados elásticas básicas oferece até cinco eDTUs. É o mesmo desempenho de uma única base de dados básica individual. A diferença é que a base de dados elástica não irá consumir quaisquer eDTUs do conjunto até ter de o fazer. 

![Introdução à SQL Database: conjuntos elásticos por camada.](./media/sql-database-understanding-dtus/sqldb_elastic_pools.png)

Um exemplo simples pode ajudar. Vejamos um Conjunto de bases de dados elásticas básicas com 1000 DTUs e adicionemos 800 bases de dados. Desde que apenas 200 das 800 bases de dados estejam a ser utilizadas num dado momento (5 DTUs X 200 = 1000), não atingirá a capacidade do conjunto e o desempenho da base de dados não se degradará. Este exemplo foi simplificado para efeitos de clareza. As contas reais são um pouco mais complexas. O portal faz as contas por si e faz uma recomendação com base no histórico da utilização da base de dados. Veja [Considerações sobre os preços e o desempenho de um conjunto de bases de dados elásticas](../articles/sql-database/sql-database-elastic-pool-guidance.md) para saber como funcionam as recomendações ou para realizar os cálculos. 

<!--HONumber=Jun16_HO2-->


