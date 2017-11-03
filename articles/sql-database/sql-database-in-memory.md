---
title: "Tecnologias de SQL da base de dados dentro da memória do Azure | Microsoft Docs"
description: "Tecnologias de SQL da base de dados dentro da memória do Azure melhoram significativamente o desempenho de transacional e cargas de trabalho de análise."
services: sql-database
documentationCenter: 
author: jodebrui
manager: jhubbard
editor: 
ms.assetid: 250ef341-90e5-492f-b075-b4750d237c05
ms.service: sql-database
ms.custom: develop databases
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2017
ms.author: jodebrui
ms.openlocfilehash: 8930595821cc7662c4ff792b73eb357f1ba29307
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="optimize-performance-by-using-in-memory-technologies-in-sql-database"></a>Otimizar o desempenho ao utilizar tecnologias de dentro da memória na base de dados do SQL Server

Ao utilizar tecnologias de memória da SQL Database do Azure, pode conseguir melhoramentos de desempenho com várias cargas de trabalho: (online transacional processamento de transações (OLTP)), análise (processamento analítico online (OLAP)) e misto (processamento analítico/transação do híbrida (HTAP)). Devido à consulta mais eficiente e o processamento de transações, tecnologias de dentro da memória também ajudam a reduzir o custo. Normalmente, não tem de atualizar o escalão de preço da base de dados para atingir ganhos de desempenho. Em alguns casos, ainda poderá reduzir o escalão de preço, ao persistirem melhorias de desempenho com tecnologias de memória.

Seguem-se dois exemplos de como ajudou a OLTP na memória para melhorar significativamente o desempenho:

- Ao utilizar OLTP na memória, [soluções de negócio de quórum foi capaz de duplicar as carga de trabalho ao melhorar DTUs 70%](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database).
    - DTU significa *unidade de débito de base de dados*, e inclui uma mesurement de consumo de recursos.
- O vídeo seguinte demonstra uma melhoria significativa no consumo de recursos com uma carga de trabalho de exemplo: [OLTP dentro da memória na base de dados SQL do Azure vídeo](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB).
    - Para obter mais detalhes, consulte a mensagem de blogue: [OLTP dentro da memória na mensagem do blogue do Azure SQL da base de dados](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)

Tecnologias de memória estão disponíveis em todas as bases de dados no escalão Premium, incluindo bases de dados em conjuntos elásticos Premium.

O vídeo seguinte explica potenciais ganhos de desempenho com tecnologias de dentro da memória na SQL Database do Azure. Lembre-se de que os ganhos de desempenho que verá sempre dependem de vários fatores, incluindo a natureza da carga de trabalho e os dados, padrão de acesso da base de dados e assim sucessivamente.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-In-Memory-Technologies/player]
>
>

Base de dados SQL do Azure tem as seguintes tecnologias de dentro da memória:

- *OLTP na memória* aumenta o débito e reduz a latência para processamento de transação. Cenários que beneficiam da OLTP na memória são: processamento como comercial e jogos, a ingestão de dados de eventos ou dispositivos de IoT, colocação em cache, carregamento de dados e tabela temporária e cenários de variável de tabela de transações de débito elevado.
- *Colocar em cluster os índices columnstore* reduzir os requisitos de espaço de armazenamento (até 10 vezes) e melhorar o desempenho de consultas de análises e relatórios. Pode utilizá-lo com as tabelas de factos no seu marts de dados para se ajustar mais dados na base de dados e melhorar o desempenho. Além disso, pode utilizar este com dados históricos na sua base de dados operacional para arquivar e ser capaz de consultar até 10 vezes mais dados.
- *Os índices columnstore não em cluster* para HTAP ajudá-lo a obter informações acerca em tempo real da sua empresa através de consultar a base de dados operacional diretamente, sem a necessidade de executar um extrair dispendiosas, transformar, carregar o processo de (ETL) e aguarde que o armazém de dados ser preenchido. Os índices columnstore não em cluster permitem muito rápida execução de consultas de análise na base de dados OLTP, ao reduzir o impacto na carga de trabalho operacional.
- Também pode ter a combinação de uma tabela com otimização de memória com um índice columnstore. Esta combinação permite-lhe efetuar o processamento de transações muito rápido e a *em simultâneo* executar consultas de análises muito rapidamente os mesmos dados.

Os índices columnstore e OLTP na memória tem sido parte de produto do SQL Server desde 2012 e 2014, respetivamente. Base de dados SQL do Azure e o SQL Server partilham a mesma implementação de tecnologias de memória. Doravante, as novas capacidades para estas tecnologias são lançadas na SQL Database do Azure em primeiro lugar, antes de medida que são lançadas no SQL Server.

Este tópico descreve os aspetos do OLTP dentro da memória e columnstore índices que são específicos para a SQL Database do Azure e também inclui exemplos:
- Verá o impacto destas tecnologias nos limites de tamanho de dados e armazenamento.
- Verá como gerir o movimento de bases de dados que utilizam estas tecnologias entre os escalões de preços diferentes.
- Verá dois exemplos que ilustram a utilização do OLTP dentro da memória, bem como os índices columnstore na SQL Database do Azure.

Consulte os seguintes recursos para obter mais informações.

Obter informações aprofundadas sobre as tecnologias de:

- [Descrição geral do OLTP dentro da memória e cenários de utilização](https://msdn.microsoft.com/library/mt774593.aspx) (inclui referências de casos práticos de clientes e informações para começar a utilizar)
- [Documentação do OLTP dentro da memória](http://msdn.microsoft.com/library/dn133186.aspx)
- [Guia de índices Columnstore](https://msdn.microsoft.com/library/gg492088.aspx)
- Híbrida transacional/analíticos processar (HTAP), também conhecido como [análise operacional em tempo real](https://msdn.microsoft.com/library/dn817827.aspx)

Um manual rápida no OLTP na memória: [1 de início rápido: tecnologias do OLTP dentro da memória para o mais rapidamente do desempenho de T-SQL](http://msdn.microsoft.com/library/mt694156.aspx) (outro artigo para o ajudar a começar a utilizar)

Vídeos aprofundados sobre as tecnologias de:

- [OLTP dentro da memória na SQL Database do Azure](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB) (que contém uma demonstração dos benefícios de desempenho e passos para reproduzir estes resultados por si)
- [Vídeos do OLTP dentro da memória: O que é e quando/como utilizá-lo](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2016/10/03/in-memory-oltp-video-what-it-is-and-whenhow-to-use-it/)
- [O índice Columnstore: Análise de memória vídeos do Ignite 2016](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2016/10/04/columnstore-index-in-memory-analytics-i-e-columnstore-index-videos-from-ignite-2016/)

## <a name="storage-and-data-size"></a>Tamanho de dados e armazenamento

### <a name="data-size-and-storage-cap-for-in-memory-oltp"></a>Limite de tamanho e de armazenamento de dados para OLTP na memória

OLTP na memória inclui tabelas com otimização de memória, que são utilizadas para armazenar dados de utilizador. Estas tabelas são necessários para caber na memória. Porque a gerir a memória diretamente no serviço de base de dados SQL, temos o conceito de uma quota de dados do utilizador. Esta ideia é referida como *armazenamento OLTP na memória*.

Cada base de dados autónomo suportados cada escalão de preço de conjunto elástico e o escalão de preço inclui uma determinada quantidade de armazenamento do OLTP dentro da memória. No momento da escrita, pode obter um gigabyte de armazenamento para cada 125 unidades de transação de base de dados (DTUs) ou unidades de transação de bases de dados elásticas (eDTUs).

O [escalões de serviço de base de dados SQL](sql-database-service-tiers.md) artigo tem a lista oficial do armazenamento OLTP na memória que está disponível para a base de dados autónomo e o agrupamento elástico de escalão de preço de cada suportados.

Os seguintes itens contam para o limite de armazenamento OLTP na memória:

- Linhas de dados de utilizador do Active Directory em tabelas com otimização de memória e variáveis de tabela. Tenha em atenção que o antigo versões de linha não contam para a cobertura.
- Índices em tabelas com otimização de memória.
- Sobrecarga operacional das operações de ALTER TABLE.

Se atingiu o limite, receberá um erro de limite de quota e já não é possível inserir ou atualizar os dados. Para atenuar este erro, eliminar dados ou aumentar o escalão de preço da base de dados ou agrupamento.

Para obter mais informações sobre como monitorizar a utilização do armazenamento OLTP na memória e configurar alertas quando quase de acessos a extremidade, consulte [armazenamento de Monitor de memória](sql-database-in-memory-oltp-monitoring.md).

#### <a name="about-elastic-pools"></a>Sobre conjuntos elásticos

Com conjuntos elásticos, o armazenamento de OLTP na memória é partilhado entre todas as bases de dados no conjunto. Por conseguinte, a utilização de uma base de dados pode potencialmente afetar outras bases de dados. Dois mitigações para isto são:

- Configure um máx.-eDTU para bases de dados que é inferior ao número de eDTU do conjunto como um todo. Este máximo caps a utilização do armazenamento OLTP na memória, em qualquer base de dados no conjunto, para o tamanho que corresponde à contagem de eDTU.
- Configure um eDTU mínima que é maior que 0. Este mínimo garante que cada base de dados no conjunto possui a quantidade de armazenamento OLTP dentro da memória disponível que corresponde a eDTU do Min configurado.

### <a name="data-size-and-storage-for-columnstore-indexes"></a>Tamanho dos dados e armazenamento para índices columnstore

Os índices Columnstore não são necessários para caber na memória. Por conseguinte, a extremidade apenas no tamanho de índices é o geral da base de dados tamanho máximo, que é descrita do [escalões de serviço de base de dados SQL](sql-database-service-tiers.md) artigo.

Quando utiliza índices columnstore em cluster, compressão columnar é utilizado para o armazenamento de tabela base. Esta compressão pode reduzir significativamente os requisitos de espaço de armazenamento dos seus dados de utilizador, o que significa que podem ajustar mais dados na base de dados. E a compressão pode ser aumentada mais com [columnar compressão arquivo](https://msdn.microsoft.com/library/cc280449.aspx#Using Columnstore and Columnstore Archive Compression). A quantidade de compressão que pode alcançar depende da natureza dos dados, mas não é invulgar 10 vezes a compressão.

Por exemplo, se tiver uma base de dados com um tamanho máximo de 1 com vários terabytes (TB) e alcançar a compressão de 10 vezes, utilizando os índices columnstore, pode ajustar um total de 10 TB de dados de utilizador na base de dados.

Quando utiliza índices columnstore não em cluster, a tabela de base ainda é armazenada no formato rowstore tradicional. Por conseguinte, reduções de armazenamento não estão tão grandes como com índices columnstore em cluster. No entanto, se estiver a substituir um número de índices não em cluster tradicionais com um índice columnstore único, pode ver uma poupança geral nos requisitos de espaço de armazenamento para a tabela.

## <a name="moving-databases-that-use-in-memory-technologies-between-pricing-tiers"></a>Mover bases de dados que utilizam tecnologias de memória entre escalões de preços

Existem nunca quaisquer incompatibilidades ou outros problemas quando atualizar para um escalão de preço superior, como do padrão para Premium. Apenas aumentam os recursos e funcionalidades disponíveis.

Mas desatualização o escalão de preço pode afetar negativamente a base de dados. O impacto é especialmente aparente quando mudar de Premium para básico ou padrão quando a base de dados contém objetos de OLTP na memória. Tabelas com otimização de memória e os índices columnstore, não estão disponíveis após a mudança para versão anterior (mesmo se permanecem visíveis). Aplicam as mesmas considerações quando está a reduzir o escalão de preço do conjunto elástico ou mover uma base de dados com tecnologias de memória, para um padrão ou básico conjunto elástico.

### <a name="in-memory-oltp"></a>OLTP dentro da memória

*Desatualização de Basic/Standard*: OLTP na memória não é suportada em bases de dados na camada Standard ou básico. Além disso, não se encontra possível mover uma base de dados que tenha quaisquer objetos OLTP na memória para o escalão Standard ou básico.

Antes de mudar a base de dados padrão/básica, remova todas as tabelas com otimização de memória e tipos de tabela, bem como todos os módulos compilados nativamente de T-SQL.

Há uma forma programática compreender se uma determinada base de dados suporta OLTP na memória. Pode executar a seguinte consulta de Transact-SQL:

```
SELECT DatabasePropertyEx(DB_NAME(), 'IsXTPSupported');
```

Se a consulta devolve **1**, OLTP dentro da memória é suportada nesta base de dados.


*Desatualização de para um escalão Premium inferior*: dados em tabelas com otimização de memória devem encaixar-se no armazenamento OLTP na memória que está associado com o escalão de preço da base de dados ou não está disponível num conjunto elástico. Se tentar reduzir o escalão de preço ou mover a base de dados para um conjunto que não tem memória suficiente disponível OLTP na memória, a operação falhar.

### <a name="columnstore-indexes"></a>Índices Columnstore

*Desatualização de para básico ou padrão*: índices Columnstore são suportados apenas o escalão de preços Premium e não os escalões Standard ou básico. Quando mudar a base de dados para básico ou padrão, o índice columnstore fica indisponível. O sistema mantém o índice columnstore, mas nunca utiliza o índice. Se atualizar posteriormente para o Premium, o índice columnstore é imediatamente pronto para ser aproveitado novamente.

Se tiver um **em cluster** índice columnstore, a tabela toda fica indisponível após a mudança para versão anterior de camada. Por conseguinte, recomendamos que remova todos os *em cluster* columnstore indexa antes de mudar a base de dados abaixo o escalão Premium.

*Desatualização de para um escalão Premium inferior*: Esta mudança para versão anterior for bem sucedida se a base de dados completa preencher o tamanho máximo da base de dados para o escalão de preço de destino, ou no armazenamento disponível no agrupamento elástico. Não há nenhum impacto específico de índices columnstore.


<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

&nbsp;

## <a name="1-install-the-in-memory-oltp-sample"></a>1. Instalar o exemplo de OLTP na memória

Pode criar a base de dados de amostra AdventureWorksLT com apenas alguns cliques no [portal do Azure](https://portal.azure.com/). Em seguida, os passos nesta secção explicam como pode enriquecer a sua base de dados AdventureWorksLT com objetos do OLTP dentro da memória e demonstrar benefícios de desempenho.

Para obter uma mais simplistic, mas visualmente mais apelativos para resolver desempenho demonstração para OLTP na memória, consulte:

- Versão: [no-memória-oltp-demonstração-v 1.0](https://github.com/Microsoft/sql-server-samples/releases/tag/in-memory-oltp-demo-v1.0)
- Código de origem: [in-memory-oltp-demo-source-code](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/in-memory/ticket-reservations)

#### <a name="installation-steps"></a>Passos de instalação

1. No [portal do Azure](https://portal.azure.com/), criar uma base de dados Premium num servidor. Definir o **origem** na base de dados de amostra AdventureWorksLT. Para obter instruções detalhadas, consulte [criar a sua primeira base de dados SQL do Azure](sql-database-get-started-portal.md).

2. Ligar à base de dados com o SQL Server Management Studio [(SSMS.exe)](http://msdn.microsoft.com/library/mt238290.aspx).

3. Copiar o [script na memória OLTP Transact-SQL](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/sql_in-memory_oltp_sample.sql) a sua área de transferência. O script T-SQL cria os objetos necessários dentro da memória na base de dados de amostra AdventureWorksLT que criou no passo 1.

4. Cole o script T-SQL no SSMS e, em seguida, execute o script. O `MEMORY_OPTIMIZED = ON` instruções de CREATE TABLE da cláusula são fundamentais. Por exemplo:


```
CREATE TABLE [SalesLT].[SalesOrderHeader_inmem](
    [SalesOrderID] int IDENTITY NOT NULL PRIMARY KEY NONCLUSTERED ...,
    ...
) WITH (MEMORY_OPTIMIZED = ON);
```


#### <a name="error-40536"></a>Erro 40536


Se receber o erro 40536 quando executar o script T-SQL, execute o seguinte script de T-SQL para verificar se a base de dados suporta na memória:


```
SELECT DatabasePropertyEx(DB_Name(), 'IsXTPSupported');
```


Um resultado de **0** significa que em memória não é suportada, e **1** significa que é suportado. Para diagnosticar o problema, certifique-se de que a base de dados na camada de serviço Premium.


#### <a name="about-the-created-memory-optimized-items"></a>Sobre os itens criados com otimização de memória

**As tabelas**: O exemplo contém as seguintes tabelas com otimização de memória:

- SalesLT.Product_inmem
- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem
- Demo.DemoSalesOrderHeaderSeed
- Demo.DemoSalesOrderDetailSeed


Pode inspecionar as tabelas com otimização de memória através de **Object Explorer** no SSMS. Clique com botão direito **tabelas** > **filtro** > **filtrar definições** > **tem otimizada de memória**. O valor é igual a 1.


Ou pode consultar as vistas de catálogo, tais como:


```
SELECT is_memory_optimized, name, type_desc, durability_desc
    FROM sys.tables
    WHERE is_memory_optimized = 1;
```


**Compilados nativamente o procedimento armazenado**: pode inspecionar SalesLT.usp_InsertSalesOrder_inmem através de uma consulta de vista de catálogo:


```
SELECT uses_native_compilation, OBJECT_NAME(object_id), definition
    FROM sys.sql_modules
    WHERE uses_native_compilation = 1;
```


&nbsp;

### <a name="run-the-sample-oltp-workload"></a>Executar a carga de trabalho do OLTP de exemplo

A única diferença entre os dois seguintes *procedimentos armazenados* é que o primeiro procedimento utiliza a otimização de memória versões das tabelas, durante o segundo procedimento utiliza as tabelas no disco regulares:

- SalesLT**.** usp_InsertSalesOrder**_inmem**
- SalesLT**.** usp_InsertSalesOrder**_ondisk**


Nesta secção, verá como utilizar o útil **ostress.exe** utilitário para executar os procedimentos armazenados dois níveis stressful. Pode comparar o tempo que demora para as execuções de dois esforço concluir.


Quando executar ostress.exe, recomendamos que passe os valores de parâmetros concebidos para ambos os procedimentos seguintes:

- Executar um grande número de ligações simultâneas, através da utilização - n100.
- Ter por cada ciclo de ligação centenas de vezes, utilizando - r500.


No entanto, pode querer começar a utilizar valores mais pequenos, como - n10 e - r50 para se certificar de que tudo está a funcionar.


### <a name="script-for-ostressexe"></a>Script para ostress.exe


Esta secção apresenta o script T-SQL que está incorporado no nosso linha de comandos ostress.exe. O script utiliza itens que foram criados pelo script T-SQL que instalou anteriormente.


O script seguinte insere uma ordem de vendas de exemplo com cinco de linha de itens na seguinte com otimização de memória *tabelas*:

- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem


```
DECLARE
    @i int = 0,
    @od SalesLT.SalesOrderDetailType_inmem,
    @SalesOrderID int,
    @DueDate datetime2 = sysdatetime(),
    @CustomerID int = rand() * 8000,
    @BillToAddressID int = rand() * 10000,
    @ShipToAddressID int = rand() * 10000;

INSERT INTO @od
    SELECT OrderQty, ProductID
    FROM Demo.DemoSalesOrderDetailSeed
    WHERE OrderID= cast((rand()*60) as int);

WHILE (@i < 20)
begin;
    EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT,
        @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od;
    SET @i = @i + 1;
end
```


Para tornar o *_ondisk* versão do script T-SQL anterior para ostress.exe, teria de substituir ambas as ocorrências do *_inmem* subcadeia com *_ondisk*. Estas substituições afetam os nomes das tabelas e procedimentos armazenados.


### <a name="install-rml-utilities-and-ostress"></a>Instale os utilitários RML e ostress


Idealmente, deverá planear executar ostress.exe numa máquina virtual do Azure (VM). Poderá criar um [VM do Azure](https://azure.microsoft.com/documentation/services/virtual-machines/) na mesma região geográfica do Azure onde a base de dados AdventureWorksLT reside. Mas pode executar ostress.exe no seu computador portátil em vez disso.


Na VM ou em independentemente do anfitrião escolher, instale os utilitários de repetição Markup Language (RML). Os utilitários incluem ostress.exe.

Para obter mais informações, consulte:
- O debate ostress.exe no [base de dados de exemplo para OLTP na memória](http://msdn.microsoft.com/library/mt465764.aspx).
- [Base de dados de exemplo para OLTP na memória](http://msdn.microsoft.com/library/mt465764.aspx).
- O [blogue para instalar ostress.exe](http://blogs.msdn.com/b/psssql/archive/2013/10/29/cumulative-update-2-to-the-rml-utilities-for-microsoft-sql-server-released.aspx).



<!--
dn511655.aspx is for SQL 2014,
[Extensions to AdventureWorks to Demonstrate In-Memory OLTP]
(http://msdn.microsoft.com/library/dn511655&#x28;v=sql.120&#x29;.aspx)

whereas for SQL 2016+
[Sample Database for In-Memory OLTP]
(http://msdn.microsoft.com/library/mt465764.aspx)
-->



### <a name="run-the-inmem-stress-workload-first"></a>Execute o *_inmem* realçar primeiro a carga de trabalho


Pode utilizar um *RML Cmd Prompt* janela Executar linha de comandos nosso ostress.exe. Os parâmetros da linha de comandos direcionam ostress para:

- Ligações de 100 de ser executados em simultâneo (-n100).
- Tem de cada ligação, execute o script T-SQL vezes 50 (-r50).


```
ostress.exe -n100 -r50 -S<servername>.database.windows.net -U<login> -P<password> -d<database> -q -Q"DECLARE @i int = 0, @od SalesLT.SalesOrderDetailType_inmem, @SalesOrderID int, @DueDate datetime2 = sysdatetime(), @CustomerID int = rand() * 8000, @BillToAddressID int = rand() * 10000, @ShipToAddressID int = rand()* 10000; INSERT INTO @od SELECT OrderQty, ProductID FROM Demo.DemoSalesOrderDetailSeed WHERE OrderID= cast((rand()*60) as int); WHILE (@i < 20) begin; EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT, @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od; set @i += 1; end"
```


Para executar a linha de comandos ostress.exe anterior:


1. Repor o conteúdo de dados de base de dados, executando o seguinte comando no SSMS, para eliminar todos os dados que foi inseridos por qualquer executa anterior:

    ``` tsql
    EXECUTE Demo.usp_DemoReset;
    ```

2. Copie o texto da linha de comandos de ostress.exe anterior para a sua área de transferência.

3. Substitua o `<placeholders>` para os parâmetros -S - U -P -d com os valores reais corretos.

4. Execute a linha de comandos editada numa janela RML Cmd.


#### <a name="result-is-a-duration"></a>Resultado é uma duração


Quando terminar de ostress.exe, escreve a duração de execução como o respetiva final de linha de saída na janela RML Cmd. Por exemplo, uma execução de teste mais curta teve cerca de 1,5 minutos:

`11/12/15 00:35:00.873 [0x000030A8] OSTRESS exiting normally, elapsed time: 00:01:31.867`


#### <a name="reset-edit-for-ondisk-then-rerun"></a>Repor, editar para *_ondisk*, em seguida, volte a executar


Depois de ter o resultado do *_inmem* executar, execute os seguintes passos para o *_ondisk* executar:


1. Repor a base de dados, executando o seguinte comando no SSMS eliminar todos os dados que é inserido através da execução anterior:
```
EXECUTE Demo.usp_DemoReset;
```

2. Edite a linha de comandos ostress.exe para substituir todas *_inmem* com *_ondisk*.

3. Executar novamente ostress.exe na segunda vez e capture o resultado de duração.

4. Repor novamente, a base de dados (para a eliminação de forma responsável que pode ser uma grande quantidade de dados de teste).


#### <a name="expected-comparison-results"></a>Resultados de comparação esperado

Os nossos testes dentro da memória têm mostrado que desempenho melhorado pelo **nove vezes** para esta carga de trabalho simplistic com ostress em execução numa VM do Azure na mesma região do Azure como a base de dados.

<a id="install_analytics_manuallink" name="install_analytics_manuallink"></a>

&nbsp;

## <a name="2-install-the-in-memory-analytics-sample"></a>2. Instalar o exemplo de análise na memória


Nesta secção, comparar os resultados e as estatísticas de e/s quando estiver a utilizar um índice columnstore em comparação com um índice de árvore b tradicional.


Para obter análises em tempo real sobre uma carga de trabalho OLTP, muitas vezes, é melhor utilizar um índice columnstore não em cluster. Para obter mais informações, consulte [descrito de índices Columnstore](http://msdn.microsoft.com/library/gg492088.aspx).



### <a name="prepare-the-columnstore-analytics-test"></a>Preparar o teste de análise columnstore


1. Utilize o portal do Azure para criar uma nova base de dados de AdventureWorksLT da amostra.
 - Utilize esse nome exato.
 - Escolha qualquer camada de serviço Premium.

2. Copiar o [sql_in memory_analytics_sample](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/sql_in-memory_analytics_sample.sql) a sua área de transferência.
 - O script T-SQL cria os objetos necessários dentro da memória na base de dados de amostra AdventureWorksLT que criou no passo 1.
 - O script cria duas tabelas de factos e a tabela de dimensão. As tabelas de factos são preenchidas com 3.5 milhões de linhas cada.
 - O script pode demorar 15 minutos a concluir.

3. Cole o script T-SQL no SSMS e, em seguida, execute o script. O **COLUMNSTORE** palavra-chave no **CREATE INDEX** instrução é crucial, como no:<br/>`CREATE NONCLUSTERED COLUMNSTORE INDEX ...;`

4. Definir AdventureWorksLT para o nível de compatibilidade 130:<br/>`ALTER DATABASE AdventureworksLT SET compatibility_level = 130;`

    Nível 130 não está diretamente relacionada com funcionalidades de memória. Mas nível 130 geralmente fornece um desempenho de consulta mais rápido a 120.


#### <a name="key-tables-and-columnstore-indexes"></a>Tabelas de chaves e os índices columnstore


- dbo. FactResellerSalesXL_CCI é uma tabela que tem um índice columnstore em cluster, que tem avançadas compressão no *dados* nível.

- dbo. FactResellerSalesXL_PageCompressed é uma tabela que tem um equivalente índice em cluster normal, que é comprimido apenas o *página* nível.


#### <a name="key-queries-to-compare-the-columnstore-index"></a>Consultas de chaves para comparar o índice columnstore


Existem [vários tipos de consulta de T-SQL que pode executar](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/clustered_columnstore_sample_queries.sql) para ver as melhorias de desempenho. No passo 2 do script T-SQL, preste atenção a este par de consultas. Diferem apenas numa linha:


- `FROM FactResellerSalesXL_PageCompressed a`
- `FROM FactResellerSalesXL_CCI a`


Um índice columnstore em cluster está a ser o FactResellerSalesXL\_tabela CCI.

A seguinte excerpt de script T-SQL imprime estatísticas para e/s e tempo para a consulta de cada tabela.


```
/*********************************************************************
Step 2 -- Overview
-- Page Compressed BTree table v/s Columnstore table performance differences
-- Enable actual Query Plan in order to see Plan differences when Executing
*/
-- Ensure Database is in 130 compatibility mode
ALTER DATABASE AdventureworksLT SET compatibility_level = 130
GO

-- Execute a typical query that joins the Fact Table with dimension tables
-- Note this query will run on the Page Compressed table, Note down the time
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO

SELECT c.Year
    ,e.ProductCategoryKey
    ,FirstName + ' ' + LastName AS FullName
    ,count(SalesOrderNumber) AS NumSales
    ,sum(SalesAmount) AS TotalSalesAmt
    ,Avg(SalesAmount) AS AvgSalesAmt
    ,count(DISTINCT SalesOrderNumber) AS NumOrders
    ,count(DISTINCT a.CustomerKey) AS CountCustomers
FROM FactResellerSalesXL_PageCompressed a
INNER JOIN DimProduct b ON b.ProductKey = a.ProductKey
INNER JOIN DimCustomer d ON d.CustomerKey = a.CustomerKey
Inner JOIN DimProductSubCategory e on e.ProductSubcategoryKey = b.ProductSubcategoryKey
INNER JOIN DimDate c ON c.DateKey = a.OrderDateKey
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO
SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO


-- This is the same Prior query on a table with a clustered columnstore index CCI
-- The comparison numbers are even more dramatic the larger the table is (this is an 11 million row table only)
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO
SELECT c.Year
    ,e.ProductCategoryKey
    ,FirstName + ' ' + LastName AS FullName
    ,count(SalesOrderNumber) AS NumSales
    ,sum(SalesAmount) AS TotalSalesAmt
    ,Avg(SalesAmount) AS AvgSalesAmt
    ,count(DISTINCT SalesOrderNumber) AS NumOrders
    ,count(DISTINCT a.CustomerKey) AS CountCustomers
FROM FactResellerSalesXL_CCI a
INNER JOIN DimProduct b ON b.ProductKey = a.ProductKey
INNER JOIN DimCustomer d ON d.CustomerKey = a.CustomerKey
Inner JOIN DimProductSubCategory e on e.ProductSubcategoryKey = b.ProductSubcategoryKey
INNER JOIN DimDate c ON c.DateKey = a.OrderDateKey
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO

SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO
```

Numa base de dados com o escalão de preço P2, que pode esperar sobre nove vezes o ganhos de desempenho para esta consulta utilizando o índice columnstore em cluster comparado com o índice tradicional. Com P15, que pode esperar sobre 57 vezes o ganho de desempenho utilizando o índice columnstore.



## <a name="next-steps"></a>Passos seguintes

- [Início rápido 1: Tecnologias OLTP na memória para um desempenho mais rápido T-SQL](http://msdn.microsoft.com/library/mt694156.aspx)

- [Utilização de memória OLTP de uma aplicação existente do SQL do Azure](sql-database-in-memory-oltp-migration.md)

- [Armazenamento de monitor de memória OLTP](sql-database-in-memory-oltp-monitoring.md) para OLTP na memória


## <a name="additional-resources"></a>Recursos adicionais

#### <a name="deeper-information"></a>Informações mais aprofundadas

- [Saiba como quórum duplica a carga de trabalho de chave da base de dados ao reduzirem DTU por 70% com OLTP dentro da memória na base de dados do SQL Server](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)

- [OLTP dentro da memória na mensagem de blogue de base de dados SQL do Azure](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)

- [Saiba mais sobre OLTP na memória](http://msdn.microsoft.com/library/dn133186.aspx)

- [Saiba mais sobre os índices columnstore](https://msdn.microsoft.com/library/gg492088.aspx)

- [Saiba mais sobre a análise operacional em tempo real](http://msdn.microsoft.com/library/dn817827.aspx)

- Consulte [padrões comuns da carga de trabalho e considerações sobre a migração](http://msdn.microsoft.com/library/dn673538.aspx) (que descreve os padrões de carga de trabalho OLTP na memória onde geralmente ganhos de desempenho significativas fornece)

#### <a name="application-design"></a>Design da aplicação

- [OLTP (otimização de memória) de memória](http://msdn.microsoft.com/library/dn133186.aspx)

- [Utilização de memória OLTP de uma aplicação existente do SQL do Azure](sql-database-in-memory-oltp-migration.md)

#### <a name="tools"></a>Ferramentas

- [Portal do Azure](https://portal.azure.com/)

- [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)

- [SQL Server Data Tools (SSDT)](http://msdn.microsoft.com/library/mt204009.aspx)
