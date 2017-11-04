---
title: "Como utilizar a criação de batches de mensagens em fila para melhorar o desempenho da aplicação SQL Database do Azure"
description: "O tópico fornece uma prova que operações de base de dados lotes imroves significativamente a velocidade e escalabilidade das suas aplicações de base de dados do Azure SQL. Embora estas técnicas de lotes de trabalho para qualquer base de dados do SQL Server, o foco do artigo é no Azure."
services: sql-database
documentationcenter: na
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 563862ca-c65a-46f6-975d-10df7ff6aa9c
ms.service: sql-database
ms.custom: develop apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: On Demand
ms.date: 07/12/2016
ms.author: sstein
ms.openlocfilehash: 8622bddc809c9d95f7acf359ff708d5ab31cf620
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="how-to-use-batching-to-improve-sql-database-application-performance"></a>Como utilizar a criação de batches de mensagens em fila para melhorar o desempenho de aplicações de base de dados SQL
Criação de batches de operações para a SQL Database do Azure significativamente melhora o desempenho e a escalabilidade das suas aplicações. Para compreender as vantagens, a primeira parte deste artigo abrange alguns resultados do teste de exemplo que comparam sequenciais e batches pedidos para uma base de dados do SQL Server. O resto do artigo mostra as técnicas, cenários e as considerações para ajudar a utilizar a criação de batches com êxito nas suas aplicações do Azure.

## <a name="why-is-batching-important-for-sql-database"></a>Por isso é criação de batches importante para a base de dados SQL?
Criação de batches de chamadas para um serviço remoto é uma estratégia para aumentar o desempenho e escalabilidade bem conhecida. Sejam corrigidos custos de processamento para qualquer interações com um serviço remoto, como serialização, a transferência de rede e a anulação da serialização. Empacotamento transações separadas muitos para um único lote minimiza estes custos.

Neste documento, vamos pretende examinar a base de dados do SQL vários cenários e as estratégias de criação de batches. Embora estas estratégias também são importantes para aplicações no local que utilizam o SQL Server, existem várias razões para Realce a utilização de criação de batches para a base de dados SQL:

* Não há potencialmente maior latência de rede no acesso à base de dados do SQL Server, especialmente se estiver a aceder base de dados do SQL de fora do mesmo centro de dados do Microsoft Azure.
* As características de multi-inquilino da base de dados do SQL Server, significa que a eficiência da dados acesso camada está correlacionada com a escalabilidade geral da base de dados. Base de dados do SQL Server tem a impedir que qualquer utilizador/inquilino único monopolizing recursos de base de dados em detrimento dos outros inquilinos. Em resposta a utilização que excedam quotas predefinidas, base de dados SQL pode reduzir o débito ou responder com exceções de limitação. Resulta numa eficiência, tais como a criação de batches, permitem-lhe fazer mais trabalho na base de dados do SQL Server antes de atingir estes limites. 
* Criação de batches também é rentável para arquiteturas que utilizem várias bases de dados (fragmentação). A eficiência da sua interação com cada unidade de base de dados ainda é um fator chave a escalabilidade geral. 

Uma das vantagens da utilização de base de dados SQL é que não tem de gerir os servidores que alojam a base de dados. No entanto, esta infraestrutura gerida também significa que tem de pensar otimizações de base de dados de forma diferente. Já não pode ser para melhorar a infraestrutura de rede ou de hardware de base de dados. Microsoft Azure controla os ambientes. A área principal que pode controlar é como a sua aplicação interage com base de dados SQL. Criação de batches é uma destas otimizações de. 

A primeira parte do documento examina várias técnicas de lotes para aplicações de .NET que utilizam a base de dados SQL. As últimas duas secções abrangem os cenários e as diretrizes de lotes.

## <a name="batching-strategies"></a>Estratégias de criação de batches
### <a name="note-about-timing-results-in-this-topic"></a>Tenha em atenção sobre os resultados de temporização neste tópico
> [!NOTE]
> Resultados não são benchmarks mas não se destinam a mostrar **desempenho relativo**. As temporizações baseiam-se uma média de, pelo menos, 10 execuções do teste. As operações são inserções para uma tabela vazia. Estes testes foram medidos pre-V12 e não necessariamente corresponde ao débito que podem ocorrer numa base de dados V12 utilizando a nova [escalões de serviço](sql-database-service-tiers.md). A vantagem relativa de técnica de lotes deve ser semelhante.
> 
> 

### <a name="transactions"></a>Transações
Parece um para iniciar uma revisão de criação de batches por debater transações. Mas a utilização de transações do lado do cliente tem um efeito de lotes do lado do servidor subtis que melhora o desempenho. Transações podem ser adicionadas e com apenas algumas linhas de código, para que fornecem uma forma rápida para melhorar o desempenho das operações sequenciais.

Considere o seguinte código c# que contenha uma sequência de insert e operações numa tabela simple de atualização.

    List<string> dbOperations = new List<string>();
    dbOperations.Add("update MyTable set mytext = 'updated text' where id = 1");
    dbOperations.Add("update MyTable set mytext = 'updated text' where id = 2");
    dbOperations.Add("update MyTable set mytext = 'updated text' where id = 3");
    dbOperations.Add("insert MyTable values ('new value',1)");
    dbOperations.Add("insert MyTable values ('new value',2)");
    dbOperations.Add("insert MyTable values ('new value',3)");

O seguinte código ADO.NET sequencialmente executa estas operações.

    using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
    {
        conn.Open();

        foreach(string commandString in dbOperations)
        {
            SqlCommand cmd = new SqlCommand(commandString, conn);
            cmd.ExecuteNonQuery();                   
        }
    }

A melhor forma de otimizar este código é implementar algum tipo de lado do cliente de criação de batches destas chamadas. Mas não existe uma forma simples para aumentar o desempenho deste código envolvendo simplesmente a sequência de chamadas de uma transação. Eis o mesmo código que utiliza uma transação.

    using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
    {
        conn.Open();
        SqlTransaction transaction = conn.BeginTransaction();

        foreach (string commandString in dbOperations)
        {
            SqlCommand cmd = new SqlCommand(commandString, conn, transaction);
            cmd.ExecuteNonQuery();
        }

        transaction.Commit();
    }

Transações, na verdade, estão a ser utilizadas em ambos estes exemplos. No primeiro exemplo, cada chamada individuais é uma transação implícita. No segundo exemplo, uma transação explícita encapsula num wrapper todas as chamadas. Pela documentação para o [registo de transações de escrita antecipada](https://msdn.microsoft.com/library/ms186259.aspx), registos são libertar no disco quando consolida a transação. Por isso, incluindo chamadas mais de uma transação, a operação de escrita para o registo de transações pode atrasar o até que a transação foi consolidada. Em vigor, pretende ativar a criação de batches para escritas para o registo de transações do servidor.

A tabela seguinte mostra alguns resultados do teste ad-hoc. Os testes de efetuar as mesmas inserções sequenciais com e sem transações. Para a perspetiva mais, o primeiro conjunto de testes executou remotamente a partir de um computador portátil para a base de dados no Microsoft Azure. O segundo conjunto de testes executou a partir de um serviço em nuvem e a base de dados que ambos resided no mesmo datacenter Microsoft Azure (EUA oeste). A tabela seguinte mostra a duração em milissegundos do inserções sequenciais com e sem transações.

**No local para Azure**:

| Operações | Nenhuma transação (ms) | Transações (ms) |
| --- | --- | --- |
| 1 |130 |402 |
| 10 |1208 |1226 |
| 100 |12662 |10395 |
| 1000 |128852 |102917 |

**Azure para o Azure (mesmo centro de dados)**:

| Operações | Nenhuma transação (ms) | Transações (ms) |
| --- | --- | --- |
| 1 |21 |26 |
| 10 |220 |56 |
| 100 |2145 |341 |
| 1000 |21479 |2756 |

> [!NOTE]
> Resultados não são testes de desempenho. Consulte o [nota sobre os resultados de temporização neste tópico](#note-about-timing-results-in-this-topic).
> 
> 

Com base nos resultados de teste anterior, na verdade, uma única operação de moldagem de uma transação diminui o desempenho. Mas como aumentar o número de operações dentro de uma transação única, como a melhoria desempenho torna-se mais marcada. A diferença de desempenho também é mais evidente quando todas as operações de ocorrerem dentro do Centro de dados do Microsoft Azure. A latência de aumento da utilização de base de dados do SQL de fora do datacenter do Microsoft Azure overshadows os ganhos de desempenho da utilização de transações.

Embora a utilização de transações pode aumentar o desempenho, continuar a [observar as melhores práticas para ligações de transações e](https://msdn.microsoft.com/library/ms187484.aspx). Manter a transação mais curta possível e fechar a ligação de base de dados após a conclusão do trabalho. A utilizar a instrução no exemplo anterior garante que a ligação é fechada quando tiver concluído o bloco de código subsequentes.

O exemplo anterior demonstra que pode adicionar uma transação local para qualquer código ADO.NET com duas linhas. Transações oferecem uma forma rápida de melhorar o desempenho de código que torna sequencial inserir, atualizar e eliminar operações. No entanto, para um desempenho mais rápido, considere alterar o código further to tirar partido do lado do cliente de criação de batches, tais como parâmetros de valor de tabela.

Para obter mais informações sobre transações por ADO.NET, consulte [transações locais em ADO.NET](https://docs.microsoft.com/dotnet/framework/data/adonet/local-transactions).

### <a name="table-valued-parameters"></a>parâmetros de valor de tabela
Parâmetros de valor de tabela suportam tipos de tabela definido pelo utilizador como parâmetros instruções Transact-SQL, funções e procedimentos armazenados. Esta técnica de lotes do lado do cliente permite-lhe enviar várias linhas de dados de parâmetro de valor de tabela. Para utilizar parâmetros de valor de tabela, defina primeiro um tipo de tabela. A seguinte instrução Transact-SQL cria um tipo de tabela com o nome **MyTableType**.

    CREATE TYPE MyTableType AS TABLE 
    ( mytext TEXT,
      num INT );


No código, crie um **DataTable** com os nomes de mesmos exatos e tipos de tipo de tabela. Passar esta **DataTable** num parâmetro de uma consulta de texto ou o procedimento armazenado chamada. O exemplo seguinte mostra esta técnica:

    using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
    {
        connection.Open();

        DataTable table = new DataTable();
        // Add columns and rows. The following is a simple example.
        table.Columns.Add("mytext", typeof(string));
        table.Columns.Add("num", typeof(int));    
        for (var i = 0; i < 10; i++)
        {
            table.Rows.Add(DateTime.Now.ToString(), DateTime.Now.Millisecond);
        }

        SqlCommand cmd = new SqlCommand(
            "INSERT INTO MyTable(mytext, num) SELECT mytext, num FROM @TestTvp",
            connection);

        cmd.Parameters.Add(
            new SqlParameter()
            {
                ParameterName = "@TestTvp",
                SqlDbType = SqlDbType.Structured,
                TypeName = "MyTableType",
                Value = table,
            });

        cmd.ExecuteNonQuery();
    }

No exemplo anterior, o **SqlCommand** objeto insere linhas de um parâmetro de valor de tabela,  **@TestTvp** . Criado anteriormente **DataTable** objeto está atribuído para este parâmetro com o **SqlCommand.Parameters.Add** método. Criação de batches inserções numa chamada significativamente aumenta o desempenho através de inserções sequenciais.

Para melhorar ainda mais o exemplo anterior, utilize um procedimento armazenado em vez de um comando baseado em texto. O comando de Transact-SQL seguinte cria um procedimento armazenado que aceita o **SimpleTestTableType** parâmetro de valor de tabela.

    CREATE PROCEDURE [dbo].[sp_InsertRows] 
    @TestTvp as MyTableType READONLY
    AS
    BEGIN
    INSERT INTO MyTable(mytext, num) 
    SELECT mytext, num FROM @TestTvp
    END
    GO

Em seguida, altere o **SqlCommand** objeto declaração no exemplo de código anterior para o seguinte.

    SqlCommand cmd = new SqlCommand("sp_InsertRows", connection);
    cmd.CommandType = CommandType.StoredProcedure;

Na maioria dos casos, os parâmetros de valor de tabela ter equivalente ou um melhor desempenho do que outras técnicas de lotes. Parâmetros de valor de tabela são, muitas vezes, preferível, mais flexíveis do que outras opções. Por exemplo, outras técnicas, tais como a cópia em massa de SQL Server, apenas permitem a inserção de linhas novas. Mas com parâmetros de valor de tabela, pode utilizar lógica no procedimento armazenado para determinar as linhas são atualizações e que são insere. Também pode ser modificado o tipo de tabela para conter uma coluna de "Operação" que indica se a linha especificada deve ser inserir, atualizar ou eliminada.

A tabela seguinte mostra os resultados do teste de ad-hoc a utilização de parâmetros de valor de tabela em milissegundos.

| Operações | No local para o Azure (ms) | Azure mesmo centro de dados (ms) |
| --- | --- | --- |
| 1 |124 |32 |
| 10 |131 |25 |
| 100 |338 |51 |
| 1000 |2615 |382 |
| 10000 |23830 |3586 |

> [!NOTE]
> Resultados não são testes de desempenho. Consulte o [nota sobre os resultados de temporização neste tópico](#note-about-timing-results-in-this-topic).
> 
> 

Os ganhos de desempenho da criação de batches é imediatamente aparente. No teste anterior sequencial, 1000 demorou segundos 129 fora do datacenter e 21 segundos no Centro de dados. Mas com parâmetros de valor de tabela, as operações de 1000 demorar apenas 2.6 segundos fora do datacenter e 0.4 segundos no Centro de dados.

Para obter mais informações sobre os parâmetros de valor de tabela, consulte [Table-Valued parâmetros](https://msdn.microsoft.com/library/bb510489.aspx).

### <a name="sql-bulk-copy"></a>Cópia em massa SQL
Cópia em massa SQL é outra forma de inserir grandes quantidades de dados de uma base de dados de destino. As aplicações de .NET podem utilizar o **SqlBulkCopy** operações de inserção de classe para executar em massa. **SqlBulkCopy** é semelhante em função para a ferramenta de linha de comandos, **Bcp.exe**, ou a instrução de Transact-SQL, **inserção em massa**. Exemplo de código seguinte mostra como efetuar a cópia em massa as linhas da origem de **DataTable**, tabela, para a tabela de destino do SQL Server, MyTable.

    using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
    {
        connection.Open();

        using (SqlBulkCopy bulkCopy = new SqlBulkCopy(connection))
        {
            bulkCopy.DestinationTableName = "MyTable";
            bulkCopy.ColumnMappings.Add("mytext", "mytext");
            bulkCopy.ColumnMappings.Add("num", "num");
            bulkCopy.WriteToServer(table);
        }
    }

Existem alguns casos onde cópia em massa é preferencial através de parâmetros de valor de tabela. Consulte a tabela de comparação de valor de tabela parâmetros versus operações de inserção em massa no tópico [Table-Valued parâmetros](https://msdn.microsoft.com/library/bb510489.aspx).

Os seguintes resultados de teste do ad-hoc mostram o desempenho da criação de batches com **SqlBulkCopy** em milissegundos.

| Operações | No local para o Azure (ms) | Azure mesmo centro de dados (ms) |
| --- | --- | --- |
| 1 |433 |57 |
| 10 |441 |32 |
| 100 |636 |53 |
| 1000 |2535 |341 |
| 10000 |21605 |2737 |

> [!NOTE]
> Resultados não são testes de desempenho. Consulte o [nota sobre os resultados de temporização neste tópico](#note-about-timing-results-in-this-topic).
> 
> 

Em tamanhos de lote mais pequenos, os parâmetros de valor de tabela de utilização outperformed o **SqlBulkCopy** classe. No entanto, **SqlBulkCopy** efetuada 12-31% mais rapidamente do que os parâmetros de valor de tabela para os testes de linhas de 1000 e 10 000. Como parâmetros de valor de tabela, **SqlBulkCopy** é uma boa opção para inserções em lote, especialmente quando comparado com o desempenho das operações não em lotes.

Para obter mais informações sobre a cópia em massa no ADO.NET, consulte [operações de cópia em massa no SQL Server](https://msdn.microsoft.com/library/7ek5da1a.aspx).

### <a name="multiple-row-parameterized-insert-statements"></a>Instruções parametrizadas inserir várias linhas
É uma alternativa para pequenas lotes para construir uma instrução de inserção parametrizada grande que insere várias linhas. Exemplo de código seguinte demonstra esta técnica.

    using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
    {
        connection.Open();

        string insertCommand = "INSERT INTO [MyTable] ( mytext, num ) " +
            "VALUES (@p1, @p2), (@p3, @p4), (@p5, @p6), (@p7, @p8), (@p9, @p10)";

        SqlCommand cmd = new SqlCommand(insertCommand, connection);

        for (int i = 1; i <= 10; i += 2)
        {
            cmd.Parameters.Add(new SqlParameter("@p" + i.ToString(), "test"));
            cmd.Parameters.Add(new SqlParameter("@p" + (i+1).ToString(), i));
        }

        cmd.ExecuteNonQuery();
    }


Neste exemplo destina-se para mostrar o conceito básico. Um cenário mais realista seria percorrer as entidades necessárias para construir a cadeia de consulta e os parâmetros de comandos em simultâneo. Está limitado a um total de parâmetros de consulta 2100, pelo que isto limita o número total de linhas que podem ser processados desta forma.

Os seguintes resultados de teste do ad-hoc mostram o desempenho deste tipo de instrução insert em milissegundos.

| Operações | Parâmetros de valor de tabela (ms) | INSERÇÃO de instrução única (ms) |
| --- | --- | --- |
| 1 |32 |20 |
| 10 |30 |25 |
| 100 |33 |51 |

> [!NOTE]
> Resultados não são testes de desempenho. Consulte o [nota sobre os resultados de temporização neste tópico](#note-about-timing-results-in-this-topic).
> 
> 

Esta abordagem pode ser ligeiramente mais rápida para lotes são menos de 100 linhas. Embora a melhoria for pequena, esta técnica é outra opção que poderá funcionar corretamente no seu cenário de aplicação específica.

### <a name="dataadapter"></a>DataAdapter
O **DataAdapter** classe permite-lhe modificar um **DataSet** objeto e, em seguida, submeter as alterações como operações INSERT, UPDATE e DELETE. Se estiver a utilizar o **DataAdapter** desta forma, é importante salientar que efetuadas chamadas separadas para cada operação distinct. Para melhorar o desempenho, utilize o **UpdateBatchSize** propriedade para o número de operações que deve ser em lotes num momento. Para obter mais informações, consulte [efetuar Batch operações utilizando DataAdapters](https://msdn.microsoft.com/library/aadf8fk2.aspx).

### <a name="entity-framework"></a>Do Entity framework
Do Entity Framework não suporta atualmente a criação de batches. Os programadores diferentes na Comunidade foi efetuada uma tentativa demonstrar soluções, como ignorar o **SaveChanges** método. Mas as soluções são normalmente complexa e personalizada para as aplicações e o modelo de dados. O projeto de codeplex do Entity Framework tem atualmente uma página de debate sobre este pedido de funcionalidade. Para ver este debate, consulte [Design reunião notas - 2 de Agosto de 2012](http://entityframework.codeplex.com/wikipage?title=Design%20Meeting%20Notes%20-%20August%202%2c%202012).

### <a name="xml"></a>XML
Por questões de exaustividade, iremos sentir que é importante abordadas XML como uma estratégia de lotes. No entanto, a utilização de XML tem nenhum vantagens através de outros métodos e as desvantagens várias. A abordagem é semelhante aos parâmetros de valor de tabela, mas um ficheiro XML ou a cadeia é transferida para um procedimento armazenado em vez de uma tabela definida pelo utilizador. O procedimento armazenado analisa os comandos no procedimento armazenado.

Existem várias desvantagens para esta abordagem:

* Trabalhar com XML pode ser complicada e propensas ao erro.
* Ao analisar o XML na base de dados pode ser intensivas em CPU.
* Na maioria dos casos, este método é mais lento do que os parâmetros de valor de tabela.

Por esta razão, não se recomenda a utilização de XML para consultas de batch.

## <a name="batching-considerations"></a>Considerações sobre a criação de batches
As secções seguintes fornecem mais orientação para a utilização de aplicações de base de dados SQL de criação de batches.

### <a name="tradeoffs"></a>Fala
Consoante a arquitetura, a criação de batches pode envolver uma variação entre o desempenho e resiliência. Por exemplo, considere o cenário em que a função inesperadamente fica inativo. Se perder a uma linha de dados, o impacto é menor do que o impacto da perda de um lote de linhas unsubmitted grande. Não há um maior risco quando a memória intermédia linhas antes de os enviar para a base de dados numa janela de tempo especificado.

Devido a esta compromisso, avalie o tipo de operações que o batch. Batch de forma mais agressiva (lotes maiores e mais intervalos de tempo) com dados seja menos críticos.

### <a name="batch-size"></a>Tamanho do lote
No nossos testes, normalmente, não havia nenhuma vantagem em lotes grande de última hora para segmentos mais pequenos. Na verdade, muitas vezes, esta subdivision resultou no desempenho mais lento ao submeter um único lote grande. Por exemplo, considere um cenário onde pretende inserir linhas de 1000. A tabela seguinte mostra como tempo que demora a utilizar os parâmetros de valor de tabela para inserir linhas 1000 quando dividido em lotes mais pequenos.

| Tamanho do lote | Iterações | Parâmetros de valor de tabela (ms) |
| --- | --- | --- |
| 1000 |1 |347 |
| 500 |2 |355 |
| 100 |10 |465 |
| 50 |20 |630 |

> [!NOTE]
> Resultados não são testes de desempenho. Consulte o [nota sobre os resultados de temporização neste tópico](#note-about-timing-results-in-this-topic).
> 
> 

Pode ver que o melhor desempenho para 1000 linhas é submetê-las ao mesmo tempo. Outros testes (não apresentados aqui) surgiu um ganhos de desempenho pequeno para interromper um lote de 10000 linhas dois lotes de 5000. Mas o esquema de tabela para estes testes é relativamente simple, pelo que deverá efetuar testes no seus dados específicos e tamanhos de batch para verificar estes findings.

Outro fator a ter em consideração é que, se o batch total ficar demasiado grande, base de dados do SQL Server poderá limitar e refuse consolidar o lote. Para obter os melhores resultados, teste o seu cenário específico para determinar se existe um tamanho de lote ideal. Se o tamanho do lote configuráveis no tempo de execução para ativar os ajustes rápidos com base no desempenho ou erros.

Por fim, equilibrar o tamanho do lote com os riscos associados à criação de batches. Se existem erros transitórios ou a função falha, considere as consequências de repetir a operação ou de perda de dados no batch.

### <a name="parallel-processing"></a>Processamento paralelo
E se demorou a abordagem de reduzir o tamanho do lote mas utilizados vários threads para executar o trabalho? Novamente, os nossos testes mostrou que vários lotes multithread mais pequeno normalmente a efetuadas worse um único lote maior. O seguinte teste tenta inserir linhas de 1000 num ou mais lotes paralelas. Este teste mostra como mais lotes em simultâneo, na verdade, diminuir o desempenho.

| Tamanho do lote [iterações] | Dois threads (ms) | Quatro threads (ms) | Seis threads (ms) |
| --- | --- | --- | --- |
| 1000 [1] |277 |315 |266 |
| 500 [2] |548 |278 |256 |
| 250 [4] |405 |329 |265 |
| 100 [10] |488 |439 |391 |

> [!NOTE]
> Resultados não são testes de desempenho. Consulte o [nota sobre os resultados de temporização neste tópico](#note-about-timing-results-in-this-topic).
> 
> 

Existem várias razões possíveis para a degradação do desempenho devido a paralelismo:

* Existem várias chamadas de rede em simultâneo em vez de um.
* Várias operações em relação a uma única tabela podem resultar em contenção e a bloquear.
* Existem sobrecargas associadas multithreading.
* A despesa dos abrir várias ligações prevalece sobre o benefício de processamento paralelo.

Se destinar a tabelas diferentes ou bases de dados, é possível ver alguns obter com esta estratégia de desempenho. Fragmentação de base de dados ou federações seria um cenário para esta abordagem. Fragmentação utiliza várias bases de dados e encaminha dados diferentes para cada base de dados. Se cada lote pequeno é uma base de dados diferentes, em seguida, efetuar as operações em paralelo pode ser mais eficiente. No entanto, os ganhos de desempenho não é suficientemente significativo utilizar como base para uma decisão para utilizar a fragmentação de base de dados na sua solução.

Em algumas estruturas, execução paralela de lotes de menores pode resultar na melhor débito de pedidos num sistema com carga. Neste caso, apesar de ser mais rápida processar um único lote maior, processamento de lotes de vários em paralelo poderá ser mais eficiente.

Se utilizar a execução paralela, considere a controlar o número máximo de threads de trabalho. Um número mais pequeno poderá resultar em menos contenção e um tempo de execução mais rápido. Além disso, considere a carga adicional que isto coloca na base de dados do destino nas ligações e transações.

### <a name="related-performance-factors"></a>Fatores de desempenho relacionados
Documentação de orientação típica no desempenho da base de dados também afeta a criação de batches. Por exemplo, inserir desempenho é reduzido para tabelas com uma chave primária grande ou vários índices não em cluster.

Se os parâmetros de valor de tabela utilizam um procedimento armazenado, pode utilizar o comando **SET NOCOUNT ON** no início do procedimento. Esta declaração suprime o retorno da contagem de linhas no procedimento afetadas. No entanto, no nossos testes, a utilização de **SET NOCOUNT ON** tinha sem qualquer efeito ou diminuir o desempenho. O procedimento armazenado de teste foi simple com um único **inserir** comando do parâmetro de valor de tabela. É possível que mais complexos procedimentos armazenados seriam beneficiar esta instrução. Mas não parta do princípio que adicionar **SET NOCOUNT ON** para o procedimento armazenado automaticamente melhora o desempenho. Para compreender o efeito, testar o procedimento armazenado com e sem a **SET NOCOUNT ON** instrução.

## <a name="batching-scenarios"></a>Cenários de criação de batches
As secções seguintes descrevem como utilizar parâmetros de valor de tabela em três cenários de aplicação. O primeiro cenário mostra como colocação em memória intermédia e criação de batches podem trabalhar em conjunto. O segundo cenário melhora o desempenho efetuando o detalhe do mestre de operações numa chamada de procedimento armazenado único. O cenário final mostra como utilizar os parâmetros de valor de tabela numa operação "UPSERT".

### <a name="buffering"></a>Colocação em memória intermédia
Apesar de existirem alguns cenários que são candidatos óbvios de criação de batches, há muitos cenários que podem tirar partido da criação de batches pelo processamento atrasado. No entanto, o processamento atrasado acarreta também um maior risco de que os dados se perde na eventualidade de ocorrer uma falha inesperada. É importante compreender este risco e considerar as consequências.

Por exemplo, considere uma aplicação web que controla o histórico de navegação de cada utilizador. Em cada pedido de página, a aplicação foi possível efetuar uma chamada para registar a vista de página do utilizador de base de dados. Mas, mais elevado desempenho e escalabilidade podem ser conseguidos através da memória intermédia de atividades de navegação dos utilizadores e, em seguida, enviar estes dados para a base de dados em lotes. Pode acionar a atualização de base de dados por tempo decorrido e/ou tamanho da memória intermédia. Por exemplo, uma regra de especificar que o batch deve ser processado após 20 segundos ou quando a memória intermédia atinge a 1000 itens.

O seguinte código de exemplo utiliza [extensões reativa - Rx](https://msdn.microsoft.com/data/gg577609) para processar eventos colocados em memória intermédia gerados por uma classe de monitorização. Quando a memória intermédia preenche ou de um tempo limite é atingido, o lote de dados de utilizador é enviado para a base de dados com um parâmetro de valor de tabela.

A seguinte classe NavHistoryData modelos os detalhes de navegação do utilizador. Contém informações básicas, tais como o identificador de utilizador, o URL acedido e o tempo de acesso.

    public class NavHistoryData
    {
        public NavHistoryData(int userId, string url, DateTime accessTime)
        { UserId = userId; URL = url; AccessTime = accessTime; }
        public int UserId { get; set; }
        public string URL { get; set; }
        public DateTime AccessTime { get; set; }
    }

A classe de NavHistoryDataMonitor é responsável pela colocação em memória intermédia os dados de navegação do utilizador para a base de dados. Contém um método, RecordUserNavigationEntry, que responde ao gerar uma **OnAdded** eventos. O código seguinte mostra a lógica de construtor que utiliza Rx para criar uma coleção observable com base no evento. Cinge-se, em seguida, a esta coleção observable com o método de memória intermédia. A sobrecarga Especifica que a memória intermédia deve ser enviada a cada 20 segundos ou entradas de 1000.

    public NavHistoryDataMonitor()
    {
        var observableData =
            Observable.FromEventPattern<NavHistoryDataEventArgs>(this, "OnAdded");

        observableData.Buffer(TimeSpan.FromSeconds(20), 1000).Subscribe(Handler);           
    }

O processador de todos os itens colocados em memória intermédia converte um tipo de valor de tabela e, em seguida, transmite deste tipo para um procedimento armazenado que processa o batch. O código seguinte mostra a definição de completa para o NavHistoryDataEventArgs e as classes de NavHistoryDataMonitor.

    public class NavHistoryDataEventArgs : System.EventArgs
    {
        public NavHistoryDataEventArgs(NavHistoryData data) { Data = data; }
        public NavHistoryData Data { get; set; }
    }

    public class NavHistoryDataMonitor
    {
        public event EventHandler<NavHistoryDataEventArgs> OnAdded;

        public NavHistoryDataMonitor()
        {
            var observableData =
                Observable.FromEventPattern<NavHistoryDataEventArgs>(this, "OnAdded");

            observableData.Buffer(TimeSpan.FromSeconds(20), 1000).Subscribe(Handler);           
        }

        public void RecordUserNavigationEntry(NavHistoryData data)
        {    
            if (OnAdded != null)
                OnAdded(this, new NavHistoryDataEventArgs(data));
        }

        protected void Handler(IList<EventPattern<NavHistoryDataEventArgs>> items)
        {
            DataTable navHistoryBatch = new DataTable("NavigationHistoryBatch");
            navHistoryBatch.Columns.Add("UserId", typeof(int));
            navHistoryBatch.Columns.Add("URL", typeof(string));
            navHistoryBatch.Columns.Add("AccessTime", typeof(DateTime));
            foreach (EventPattern<NavHistoryDataEventArgs> item in items)
            {
                NavHistoryData data = item.EventArgs.Data;
                navHistoryBatch.Rows.Add(data.UserId, data.URL, data.AccessTime);
            }

            using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
            {
                connection.Open();

                SqlCommand cmd = new SqlCommand("sp_RecordUserNavigation", connection);
                cmd.CommandType = CommandType.StoredProcedure;

                cmd.Parameters.Add(
                    new SqlParameter()
                    {
                        ParameterName = "@NavHistoryBatch",
                        SqlDbType = SqlDbType.Structured,
                        TypeName = "NavigationHistoryTableType",
                        Value = navHistoryBatch,
                    });

                cmd.ExecuteNonQuery();
            }
        }
    }

Para utilizar esta classe buffering, a aplicação cria um objeto de NavHistoryDataMonitor estático. Sempre que um utilizador acede a uma página, a aplicação chama o método de NavHistoryDataMonitor.RecordUserNavigationEntry. A lógica buffering prossegue para a asseguramos enviar estas entradas para a base de dados em lotes.

### <a name="master-detail"></a>Detalhe de principal
Parâmetros de valor de tabela são úteis para cenários de inserção simples. No entanto, pode ser mais difícil para inserções de batch que envolvem mais do que uma tabela. O cenário "mestre/Detalhes" é um bom exemplo. A tabela principal identifica a entidade principal. Uma ou mais tabelas de detalhe armazenam mais dados sobre a entidade. Neste cenário, as relações de chave externas impor a relação de detalhes para uma entidade principal exclusivo. Considere uma versão simplificada da tabela PurchaseOrder e a respetiva tabela de OrderDetail associada. O Transact-SQL seguinte cria a tabela de PurchaseOrder com quatro colunas: OrderID, OrderDate, CustomerID e estado.

    CREATE TABLE [dbo].[PurchaseOrder](
    [OrderID] [int] IDENTITY(1,1) NOT NULL,
    [OrderDate] [datetime] NOT NULL,
    [CustomerID] [int] NOT NULL,
    [Status] [nvarchar](50) NOT NULL,
     CONSTRAINT [PrimaryKey_PurchaseOrder] 
    PRIMARY KEY CLUSTERED ( [OrderID] ASC ))

Cada ordem contém um ou mais compras de produto. Esta informação é capturada na tabela PurchaseOrderDetail. O Transact-SQL seguinte cria a tabela de PurchaseOrderDetail com colunas de cinco: OrderID, OrderDetailID, ProductID, UnitPrice e OrderQty.

    CREATE TABLE [dbo].[PurchaseOrderDetail](
    [OrderID] [int] NOT NULL,
    [OrderDetailID] [int] IDENTITY(1,1) NOT NULL,
    [ProductID] [int] NOT NULL,
    [UnitPrice] [money] NULL,
    [OrderQty] [smallint] NULL,
     CONSTRAINT [PrimaryKey_PurchaseOrderDetail] PRIMARY KEY CLUSTERED 
    ( [OrderID] ASC, [OrderDetailID] ASC ))

A coluna de OrderID na tabela PurchaseOrderDetail tem de referenciar uma ordem da tabela PurchaseOrder. A seguinte definição de uma chave externa impõe esta restrição.

    ALTER TABLE [dbo].[PurchaseOrderDetail]  WITH CHECK ADD 
    CONSTRAINT [FK_OrderID_PurchaseOrder] FOREIGN KEY([OrderID])
    REFERENCES [dbo].[PurchaseOrder] ([OrderID])

Para utilizar parâmetros de valor de tabela, tem de ter um tipo de tabela definido pelo utilizador para cada tabela de destino.

    CREATE TYPE PurchaseOrderTableType AS TABLE 
    ( OrderID INT,
      OrderDate DATETIME,
      CustomerID INT,
      Status NVARCHAR(50) );
    GO

    CREATE TYPE PurchaseOrderDetailTableType AS TABLE 
    ( OrderID INT,
      ProductID INT,
      UnitPrice MONEY,
      OrderQty SMALLINT );
    GO

Em seguida, defina um procedimento armazenado que aceita tabelas destes tipos. Este procedimento permite que uma aplicação para um conjunto de encomendas pendentes e detalhes da encomenda numa única chamada de lote localmente. O Transact-SQL seguinte fornece a declaração de procedimento armazenado concluído para este exemplo de ordem de compra.

    CREATE PROCEDURE sp_InsertOrdersBatch (
    @orders as PurchaseOrderTableType READONLY,
    @details as PurchaseOrderDetailTableType READONLY )
    AS
    SET NOCOUNT ON;

    -- Table that connects the order identifiers in the @orders
    -- table with the actual order identifiers in the PurchaseOrder table
    DECLARE @IdentityLink AS TABLE ( 
    SubmittedKey int, 
    ActualKey int, 
    RowNumber int identity(1,1)
    );

          -- Add new orders to the PurchaseOrder table, storing the actual
    -- order identifiers in the @IdentityLink table   
    INSERT INTO PurchaseOrder ([OrderDate], [CustomerID], [Status])
    OUTPUT inserted.OrderID INTO @IdentityLink (ActualKey)
    SELECT [OrderDate], [CustomerID], [Status] FROM @orders ORDER BY OrderID;

    -- Match the passed-in order identifiers with the actual identifiers
    -- and complete the @IdentityLink table for use with inserting the details
    WITH OrderedRows As (
    SELECT OrderID, ROW_NUMBER () OVER (ORDER BY OrderID) As RowNumber 
    FROM @orders
    )
    UPDATE @IdentityLink SET SubmittedKey = M.OrderID
    FROM @IdentityLink L JOIN OrderedRows M ON L.RowNumber = M.RowNumber;

    -- Insert the order details into the PurchaseOrderDetail table, 
          -- using the actual order identifiers of the master table, PurchaseOrder
    INSERT INTO PurchaseOrderDetail (
    [OrderID],
    [ProductID],
    [UnitPrice],
    [OrderQty] )
    SELECT L.ActualKey, D.ProductID, D.UnitPrice, D.OrderQty
    FROM @details D
    JOIN @IdentityLink L ON L.SubmittedKey = D.OrderID;
    GO

Neste exemplo, definido localmente @IdentityLink tabela armazena os valores de OrderID reais de linhas recentemente inseridas. Estes identificadores de ordem são diferentes dos valores OrderID temporários no @orders e @details parâmetros de valor de tabela. Por este motivo, o @IdentityLink tabela, em seguida, liga os valores de OrderID do @orders parâmetro aos valores reais OrderID para novas linhas na tabela PurchaseOrder. Após este passo, o @IdentityLink tabela pode facilitar a inserir os detalhes da encomenda com o OrderID real que satisfaz a restrição de chave externa.

Este procedimento armazenado pode ser utilizado a partir do código ou a partir de outras chamadas de Transact-SQL. Consulte a secção de parâmetros de valor de tabela deste documento para obter um exemplo de código. O Transact-SQL seguinte mostra como chamar o sp_InsertOrdersBatch.

    declare @orders as PurchaseOrderTableType
    declare @details as PurchaseOrderDetailTableType

    INSERT @orders 
    ([OrderID], [OrderDate], [CustomerID], [Status])
    VALUES(1, '1/1/2013', 1125, 'Complete'),
    (2, '1/13/2013', 348, 'Processing'),
    (3, '1/12/2013', 2504, 'Shipped')

    INSERT @details
    ([OrderID], [ProductID], [UnitPrice], [OrderQty])
    VALUES(1, 10, $11.50, 1),
    (1, 12, $1.58, 1),
    (2, 23, $2.57, 2),
    (3, 4, $10.00, 1)

    exec sp_InsertOrdersBatch @orders, @details

Esta solução permite que cada lote utilizar um conjunto de valores de OrderID que começam em 1. Estes valores OrderID temporários descrevem as relações no lote, mas os valores de OrderID reais são determinados no momento da operação de inserção. Pode executar repetidamente as mesmas instruções no exemplo anterior e gerar as ordens exclusivas na base de dados. Por este motivo, considere adicionar mais lógica código ou a base de dados, que impede as ordens duplicadas quando utilizar esta técnica de criação de batches.

Este exemplo demonstra que podem ser em ainda mais complexas operações de base de dados, tais como de detalhe do mestre de operações, de lotes utilizando os parâmetros de valor de tabela.

### <a name="upsert"></a>UPSERT
Outro cenário lotes envolve em simultâneo atualizar linhas existentes e inserir novas linhas. Esta operação é por vezes referida como uma operação de "UPSERT" (atualização + insert). Em vez de fazer chamadas separadas para inserir e a ATUALIZAÇÃO, é mais adequada para esta tarefa instrução MERGE. A instrução MERGE pode executar ambos os insert e operações numa única chamada de atualização.

Parâmetros de valor de tabela podem ser utilizados com a instrução MERGE para efetuar atualizações e inserções. Por exemplo, considere uma tabela de empregado simplificada que contém as seguintes colunas: campo IDdeEmpregado, nome próprio, apelido, SocialSecurityNumber:

    CREATE TABLE [dbo].[Employee](
    [EmployeeID] [int] IDENTITY(1,1) NOT NULL,
    [FirstName] [nvarchar](50) NOT NULL,
    [LastName] [nvarchar](50) NOT NULL,
    [SocialSecurityNumber] [nvarchar](50) NOT NULL,
     CONSTRAINT [PrimaryKey_Employee] PRIMARY KEY CLUSTERED 
    ([EmployeeID] ASC ))

Neste exemplo, pode utilizar o facto de que o SocialSecurityNumber é exclusivo para executar uma intercalação das várias empregados. Em primeiro lugar, crie o tipo de tabela definido pelo utilizador:

    CREATE TYPE EmployeeTableType AS TABLE 
    ( Employee_ID INT,
      FirstName NVARCHAR(50),
      LastName NVARCHAR(50),
      SocialSecurityNumber NVARCHAR(50) );
    GO

Em seguida, crie um procedimento armazenado ou escrever código que utiliza a instrução MERGE para efetuar a atualização e inserir. O exemplo seguinte utiliza a instrução MERGE num parâmetro de valor de tabela, @employees, do tipo EmployeeTableType. O conteúdo a @employees tabela não são mostrados aqui.

    MERGE Employee AS target
    USING (SELECT [FirstName], [LastName], [SocialSecurityNumber] FROM @employees) 
    AS source ([FirstName], [LastName], [SocialSecurityNumber])
    ON (target.[SocialSecurityNumber] = source.[SocialSecurityNumber])
    WHEN MATCHED THEN 
    UPDATE SET
    target.FirstName = source.FirstName, 
    target.LastName = source.LastName
    WHEN NOT MATCHED THEN
       INSERT ([FirstName], [LastName], [SocialSecurityNumber])
       VALUES (source.[FirstName], source.[LastName], source.[SocialSecurityNumber]);

Para obter mais informações, consulte a documentação e exemplos de instrução MERGE. Embora o trabalho mesmo foi possível efetuar um passo de várias armazenados chamada de procedimento com separar INSERT e as operações de ATUALIZAÇÃO, a instrução de intercalação é mais eficiente. Código de base de dados também pode construir chamadas de Transact-SQL que utilizem a instrução de intercalação diretamente, sem necessidade de dois chamadas de base de dados para inserir e a ATUALIZAÇÃO.

## <a name="recommendation-summary"></a>Recomendação resumo
A lista seguinte fornece um resumo das recomendações lotes debatidos neste tópico:

* Utilize a colocação em memória intermédia e criação de batches para aumentar o desempenho e a escalabilidade das aplicações de base de dados SQL.
* Compreenda fala entre a criação de batches/colocação em memória intermédia e resiliência. Durante uma falha de função, o risco de perda de um lote não processado de dados críticos da empresa poderá compensar o benefício de desempenho de criação de batches.
* Tentativa de manter todas as chamadas para a base de dados num único centro de dados para reduzir a latência.
* Se optar por uma única técnica de lotes, parâmetros de valor de tabela oferecem o melhor desempenho e a flexibilidade.
* Para obter o desempenho de inserção mais rápido, siga estas Diretrizes gerais mas testar o cenário:
  * Para < 100 linhas, utilize um único comando INSERT parametrizado.
  * Para < 1000 linhas, utilize parâmetros de valor de tabela.
  * Para > = 1000 linhas, utilize SqlBulkCopy.
* Para atualizar e eliminar operações, utilize parâmetros de valor de tabela com a lógica de procedimento armazenado que determina a operação correta de cada linha no parâmetro de tabela.
* Diretrizes de tamanho de lote:
  * Utilize os tamanhos de batch maiores que façam sentido para a sua aplicação e os requisitos comerciais.
  * Equilibrar os ganhos de desempenho de lotes grande com os riscos de falhas temporárias ou catastrófica. O que é o consequence de novas tentativas ou perda de dados no lote? 
  * Teste o maior tamanho do lote para verificar que a base de dados do SQL Server não rejeitá-lo.
  * Crie definições de configuração esse controlo de criação de batches, tais como o tamanho do lote ou a janela de tempo buffering. Estas definições fornecem flexibilidade. Pode alterar o comportamento de lotes de produção sem voltar a implementar o serviço em nuvem.
* Evite a execução paralela de lotes funcionar numa tabela na base de dados de um único. Se optar por dividir um único lote em vários threads de trabalho, execute testes para determinar o número ideal de threads. Depois de um limiar não especificado, mais threads irão diminuir o desempenho em vez de o aumente demasiado.
* Considere a memória intermédia de tamanho e a hora como uma forma de implementar a criação de batches para cenários mais.

## <a name="next-steps"></a>Passos seguintes
Este artigo concentra-se na forma como programação técnicas relacionadas com a criação de batches e de design de base de dados podem melhorar o desempenho da aplicação e a escalabilidade. Mas este é um fator na sua estratégia geral. Para obter mais formas de melhorar o desempenho e escalabilidade, consulte [orientações de desempenho de SQL Database do Azure para bases de dados individuais](sql-database-performance-guidance.md) e [considerações sobre preço e desempenho de um conjunto elástico](sql-database-elastic-pool-guidance.md).

