---
title: Monitorizar o desempenho de base de dados na Base de Dados SQL do Azure | Microsoft Docs
description: "Saiba mais sobre as opções para monitorizar a base de dados com ferramentas do Azure e vistas de gestão dinâmica."
keywords: "monitorização de base de dados, desempenho de base de dados em nuvem"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: a2e47475-c955-4a8d-a65c-cbef9a6d9b9f
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: On Demand
ms.date: 09/20/2017
ms.author: carlrab
ms.openlocfilehash: 211036f32df719bf329783b3e4333a8496aa1676
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="monitoring-database-performance-in-azure-sql-database"></a>Monitorizar o desempenho de base de dados na Base de Dados SQL do Azure
A monitorização do desempenho de uma base de dados SQL do Azure é iniciada com a monitorização da utilização de recursos em relação ao nível de desempenho de base de dados que escolher. A monitorização ajuda-o a determinar se a base de dados tem excesso de capacidade ou está a ter problemas porque os recursos estão a ser excessivamente utilizados e a decidir se está na altura de ajustar o nível de desempenho e a [camada de serviços](sql-database-service-tiers.md) da base de dados. Pode monitorizar a base de dados com as ferramentas gráficas no [portal do Azure](https://portal.azure.com) ou através de [vistas de gestão dinâmica](https://msdn.microsoft.com/library/ms188754.aspx) do SQL.

> [!TIP]
> Utilize [Azure SQL inteligente Insights](sql-database-intelligent-insights.md) para automático de monitorização do desempenho da sua base de dados. Quando é detetado um problema de desempenho, é gerado um registo de diagnóstico com os detalhes e análise de causa raiz (RCA) do problema. Recomendação de melhoramento de desempenho é fornecida quando possível.
>

## <a name="monitor-databases-using-the-azure-portal"></a>Monitorizar bases de dados com o portal do Azure
No [portal do Azure](https://portal.azure.com/), pode monitorizar a utilização de uma base de dados individual, selecionando a base de dados e clicando no gráfico **Monitorização**. É apresentada a janela **Métricas** que pode alterar ao clicar no botão **Editar gráfico**. Adicione as métricas seguintes:

* Percentagem de CPU
* Percentagem de DTU
* Percentagem de ES de Dados
* Percentagem de tamanho da Base de Dados

Depois de adicionar estas métricas, pode continuar a visualizá-las no gráfico **Monitorização** com mais detalhes sobre a janela **Métricas**. As quatro métricas mostram a percentagem de utilização média relativa à **DTU** da base de dados. Consulte o artigo [camadas do serviço](sql-database-service-tiers.md) para detalhes sobre as DTUs.

![Monitorização da camada de serviços do desempenho da base de dados.](./media/sql-database-service-tiers/sqldb_service_tier_monitoring.png)

Também pode configurar alertas para as métricas de desempenho. Clique no botão **Adicionar alerta** na janela **Métricas**. Siga o assistente para configurar o alerta. Tem a opção de alertar se as métricas excederem um determinado limiar ou se a métrica descer abaixo de um determinado limiar.

Por exemplo, se espera que a carga de trabalho na sua base de dados aumente, pode optar por configurar um alerta por e-mail sempre que a base de dados atingir 80% em qualquer uma das métricas de desempenho. Pode utilizar isto como um aviso inicial para calcular quando poderá ter de mudar para o próximo nível de desempenho superior.

As métricas de desempenho também podem ajudar a determinar se é possível mudar para um nível de desempenho inferior. Parta do princípio de que está a utilizar uma base de dados Standard S2 e que todas as métricas de desempenho mostram que, em média, a base de dados não utiliza mais de 10% em qualquer momento. É provável que a base de dados funcione corretamente no Standard S1. No entanto, esteja atento às cargas de trabalho que aumentam ou flutuam, antes de efetuar a decisão de passar para um nível de desempenho inferior.

## <a name="monitor-databases-using-dmvs"></a>Monitorizar bases de dados do monitor com DMVs
As mesmas métricas que estão expostas no portal também estão disponíveis nas vistas de sistema: [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx)da base de dados **mestra** lógica do seu servidor, e em [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) da base de dados de utilizador. Utilize **sys.resource_stats**, se precisar de monitorizar dados menos granulares ao longo de um período de tempo. Utilize **sys.dm_db_resource_stats**, se precisar de monitorizar dados mais granulares num período de tempo mais pequeno. Para mais informações, consulte [Guia de Desempenho da Base de Dados SQL do Azure](sql-database-single-database-monitor.md#monitor-resource-use).

> [!NOTE]
> **sys.dm_db_resource_stats** devolve um conjunto de resultados vazio quando utilizado em bases de dados das edições Web e Business, que foram extinguidas.
>
>

### <a name="monitor-resource-use"></a>Monitorizar a utilização de recursos

Pode monitorizar a utilização de utilização de recursos [SQL da base de dados Query Performance Insight](sql-database-query-performance.md) e [arquivo de consultas](https://msdn.microsoft.com/library/dn817826.aspx).

Também pode monitorizar a utilização utilizando estas duas vistas:

* [sys.dm db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx)
* [resource_stats](https://msdn.microsoft.com/library/dn269979.aspx)

#### <a name="sysdmdbresourcestats"></a>sys.dm db_resource_stats
Pode utilizar o [sys.dm db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) vista em cada base de dados do SQL Server. O **sys.dm db_resource_stats** vista apresenta dados de utilização de recursos recentes relativamente à camada de serviço. Percentagens médias para memória, escritas de registo, dados e/s e CPU são gravadas a cada 15 segundos e são mantidas durante 1 hora.

Uma vez que esta vista fornece mais granular Ver utilização de recursos, utilize **sys.dm db_resource_stats** primeiro para qualquer análise do estado atual ou a resolução de problemas. Por exemplo, esta consulta apresenta a utilização de recursos máxima e média da base de dados atual através da hora nos últimos:

    SELECT  
        AVG(avg_cpu_percent) AS 'Average CPU use in percent',
        MAX(avg_cpu_percent) AS 'Maximum CPU use in percent',
        AVG(avg_data_io_percent) AS 'Average data I/O in percent',
        MAX(avg_data_io_percent) AS 'Maximum data I/O in percent',
        AVG(avg_log_write_percent) AS 'Average log write use in percent',
        MAX(avg_log_write_percent) AS 'Maximum log write use in percent',
        AVG(avg_memory_usage_percent) AS 'Average memory use in percent',
        MAX(avg_memory_usage_percent) AS 'Maximum memory use in percent'
    FROM sys.dm_db_resource_stats;  

Para outras consultas, consulte os exemplos [sys.dm db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx).

#### <a name="sysresourcestats"></a>resource_stats
O [resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) ver no **mestre** base de dados tem informações adicionais que podem ajudar a monitorizar o desempenho da base de dados SQL no seu nível de desempenho e o escalão de serviço específicos. Os dados são recolhidos a cada 5 minutos e são mantidos para cerca de 35 dias. Esta vista é útil para uma análise histórica duração mais longa do como a base de dados do SQL Server utiliza recursos.

O gráfico seguinte mostra a utilização de recursos da CPU para uma base de dados Premium com o nível de desempenho P2 para cada hora numa semana. Este gráfico é iniciado numa segunda, mostra 5 dias úteis e, em seguida, mostra um fim de semana, quando muito menos acontece na aplicação.

![Utilização de recursos de base de dados do SQL Server](./media/sql-database-performance-guidance/sql_db_resource_utilization.png)

Os dados, esta base de dados tem atualmente um pico de carga da CPU apenas de mais de 50% utilização de CPU relativo para o nível de desempenho P2 (dia na Terça-feira). Se a CPU é o fator dominante no perfil de recursos da aplicação, em seguida, poderá decidir que P2 é o nível de desempenho à direita para garantir que a carga de trabalho sempre se adequa a. Se espera que uma aplicação para aumentar ao longo do tempo, é uma boa ideia ter uma memória intermédia recursos adicionais para que a aplicação não nunca atingirá o limite de nível de desempenho. Se aumentar o nível de desempenho, pode ajudar a evitar erros visíveis para o cliente que podem ocorrer quando uma base de dados não tem suficiente capacidade para processar pedidos de forma eficaz, especialmente em ambientes de sensíveis à latência. Um exemplo é uma base de dados que suporta uma aplicação que desenha páginas Web com base nos resultados de chamadas de base de dados.

Outros tipos de aplicação podem interpretar mesmo gráfico de forma diferente. Por exemplo, se uma aplicação tenta processar os dados de folha de pagamentos por dia e tem o mesmo gráfico, este tipo de modelo "tarefa de lote" poderá fazê-lo ajustar a um nível de desempenho P1. O nível de desempenho P1 tem 100 DTUs em comparação com 200 DTUs a nível de desempenho de P2. O nível de desempenho P1 fornece meio o desempenho de nível de desempenho P2. Por isso, 50 por cento de utilização de CPU no P2 é igual a utilização de CPU de 100 por cento num P1. Se a aplicação não tiver tempos limite, poderá não interessa se uma tarefa demora 2 horas ou 2,5 horas a concluir, se este obtém feito hoje. Uma aplicação nesta categoria, provavelmente, pode utilizar um nível de desempenho P1. Pode tirar partido do facto de que existem períodos de tempo durante o dia quando a utilização de recursos é mais baixo, para que qualquer "grande das horas de ponta" poderão transbordam através de dos troughs mais tarde no dia. O nível de desempenho P1 poderá ser ideal para esse tipo de aplicação (e poupar dinheiro), desde que podem concluir as tarefas na hora por dia.

Expõe de base de dados SQL do Azure consumido informações de recursos para cada base de dados ativa no **resource_stats** ver do **mestre** base de dados em cada servidor. Os dados na tabela são agregados para intervalos de 5 minutos. Com os escalões de serviço básico, Standard e Premium, os dados podem demorar mais de 5 minutos a aparecer na tabela, pelo que estes dados são mais útil para análise histórico em vez de análise de quase em tempo real. Consulta o **resource_stats** visualizar para ver o histórico recente de uma base de dados e para validar se a reserva escolheu entregar o desempenho que pretende quando necessário.

> [!NOTE]
> Tem de estar ligado para o **mestre** base de dados do seu servidor lógico de base de dados do SQL para consulta **resource_stats** nos exemplos seguintes.
> 
> 

Este exemplo mostra como os dados nesta vista são expostos:

    SELECT TOP 10 *
    FROM sys.resource_stats
    WHERE database_name = 'resource1'
    ORDER BY start_time DESC

![A vista de catálogo resource_stats](./media/sql-database-performance-guidance/sys_resource_stats.png)

O exemplo seguinte mostra-lhe diferentes formas que pode utilizar o **resource_stats** vista para obter informações sobre como a base de dados do SQL Server utiliza recursos do catálogo:

1. Para observar os recursos da semana passada utilizar para a base de dados userdb1, pode executar esta consulta:
   
        SELECT *
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND
              start_time > DATEADD(day, -7, GETDATE())
        ORDER BY start_time DESC;
2. Para avaliar a carga de trabalho quão bem o nível de desempenho, terá de abrir subdiretórios de cada aspeto das métricas de recurso: CPU, de leituras, escritas, número de funcionários e número de sessões. Eis um revisto consultar utilizando **resource_stats** para os valores médios e máximos destas métricas de recurso de relatório:
   
        SELECT
            avg(avg_cpu_percent) AS 'Average CPU use in percent',
            max(avg_cpu_percent) AS 'Maximum CPU use in percent',
            avg(avg_data_io_percent) AS 'Average physical data I/O use in percent',
            max(avg_data_io_percent) AS 'Maximum physical data I/O use in percent',
            avg(avg_log_write_percent) AS 'Average log write use in percent',
            max(avg_log_write_percent) AS 'Maximum log write use in percent',
            avg(max_session_percent) AS 'Average % of sessions',
            max(max_session_percent) AS 'Maximum % of sessions',
            avg(max_worker_percent) AS 'Average % of workers',
            max(max_worker_percent) AS 'Maximum % of workers'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
3. Com estas informações sobre os valores médios e máximos de cada métrica de recursos, pode avaliar a carga de trabalho quão bem para o nível de desempenho que escolheu. Normalmente, tempo médio de valores de **resource_stats** dão-lhe uma linha de base boa utilizar contra o tamanho de destino. Deve ser a pen medida primário. Por exemplo, pode estar a utilizar a camada de serviço Standard com nível de desempenho de S2. A média utilizar percentagens de leituras de CPU e e/s e escreve são abaixo 40 por cento, o número médio de trabalhadores é inferior a 50 e o número médio de sessões é inferior a 200. A carga de trabalho pode ajustar o nível de desempenho S1. É fácil ver se a base de dados se encaixa nos limites de trabalho e a sessão. Para ver se uma base de dados se ajusta a um nível de desempenho inferior relativamente a CPU, leituras e escritas, dividir o número DTU de nível de desempenho inferior pelo número DTU do seu nível de desempenho atual e, em seguida, multiplique o resultado por 100:
   
    **S1 DTU / S2 DTU * 100 = 20 / 50 * 100 = 40**
   
    O resultado é a diferença de desempenho relativo entre os níveis de desempenho de dois em percentagem. Se a utilização de recursos não exceder este valor, a carga de trabalho pode ajustar o nível de desempenho inferior. No entanto, tem de observar todos os intervalos de valores de utilização de recursos e determinar, por percentagem, com que frequência a carga de trabalho de base de dados deverá ajustar o nível de desempenho inferior. A seguinte consulta produz a percentagem adequada por dimensão de recursos, com base no limiar de 40 por cento são calculados neste exemplo:
   
        SELECT
            (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU Fit Percent'
            ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log Write Fit Percent'
            ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical Data IO Fit Percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
   
    Com base no seu objetivo de nível de serviço de base de dados (SLO), pode decidir se a carga de trabalho que ajusta o nível de desempenho inferior. Se a carga de trabalho de base de dados SLO é a percentagem de 99,9 e a consulta anterior devolve valores maior percentagem de 99,9 para todas as dimensões de três recursos, a carga de trabalho provável ajusta o nível de desempenho inferior.
   
    Observar a percentagem adequada também dá-lhe informações sobre se deve mover para o próximo nível de desempenho superior para satisfazer os seus SLO. Por exemplo, userdb1 mostra a utilização de CPU seguinte para da última semana:
   
   | Percentagem de CPU média | Percentagem de CPU máxima |
   | --- | --- |
   | 24.5 |100.00 |
   
    A CPU média trata de um trimestre do limite do nível de desempenho, deverá ajustar corretamente o nível de desempenho da base de dados. No entanto, o valor máximo mostra que a base de dados atinge o limite do nível de desempenho. Necessita de mover para o próximo nível de desempenho superior? Observe como demasiadas vezes atingir a carga de trabalho 100 por cento e, em seguida, compare-as com a carga de trabalho de base de dados SLO.
   
        SELECT
        (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log write fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical data I/O fit percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
   
    Se esta consulta devolve um valor inferior a 99,9 por cento para qualquer uma das dimensões três recursos, considere a mover para o próximo nível de desempenho superior ou utilize técnicas de otimização de aplicação para reduzir a carga na base de dados do SQL Server.
4. Neste exercício considera também o aumento de carga de trabalho prevista no futuro.

Para conjuntos elásticos, pode monitorizar bases de dados individuais no conjunto, com as técnicas descritas nesta secção. Mas também é possível monitorizar o conjunto como um todo. Para informações, consulte [Monitorizar e gerir um conjunto elástico](sql-database-elastic-pool-manage-portal.md).


### <a name="maximum-concurrent-requests"></a>Máximo de pedidos simultâneo
Para ver o número de pedidos simultâneos, execute esta consulta de Transact-SQL na base de dados SQL:

    SELECT COUNT(*) AS [Concurrent_Requests]
    FROM sys.dm_exec_requests R

Para analisar a carga de trabalho de uma base de dados do SQL Server no local, para modificar esta consulta para filtrar na base de dados específico que pretende analisar. Por exemplo, se tiver uma base de dados no local com o nome MyDatabase, esta consulta de Transact-SQL devolve a contagem de pedidos simultâneos essa base de dados:

    SELECT COUNT(*) AS [Concurrent_Requests]
    FROM sys.dm_exec_requests R
    INNER JOIN sys.databases D ON D.database_id = R.database_id
    AND D.name = 'MyDatabase'

Isto é apenas um instantâneo num único ponto no tempo. Para obter uma melhor compreensão da sua carga de trabalho e os requisitos do pedido simultâneo, terá de recolher muitos exemplos ao longo do tempo.

### <a name="maximum-concurrent-logins"></a>Inícios de sessão em simultâneo máximos
Pode analisar os seus padrões de utilizador e a aplicação para obter uma ideia da frequência de inícios de sessão. Também pode executar cargas de mundo real num ambiente de teste para se certificar de que não está a atingir este ou outros limites, que vamos discutir neste artigo. Não é uma consulta simples ou uma vista de gestão dinâmica (DMV) que pode apresentar-lhe simultâneas contagens de início de sessão ou do histórico.

Se vários clientes utilizarem a mesma cadeia de ligação, o serviço autentica cada início de sessão. Se 10 utilizadores em simultâneo ligam a uma base de dados com o mesmo nome de utilizador e palavra-passe, seria possível 10 inícios de sessão em simultâneo. Este limite aplica-se apenas a duração do início de sessão e a autenticação. Se o mesmos 10 utilizadores ligam à base de dados sequencialmente, o número de inícios de sessão em simultâneo nunca deverá ser superior a 1.

> [!NOTE]
> Atualmente, este limite não é aplicável às bases de dados em conjuntos elásticos.
> 
> 

### <a name="maximum-sessions"></a>Máximo de sessões
Para ver o número de sessões ativas atuais, execute esta consulta de Transact-SQL na base de dados SQL:

    SELECT COUNT(*) AS [Sessions]
    FROM sys.dm_exec_connections

Se estiver a analisar uma carga de trabalho do SQL Server no local, modifique a consulta focar-se numa base de dados específica. Esta consulta ajuda a determinar necessidades de sessão possíveis para a base de dados se estiver a considerar movê-lo para a SQL Database do Azure.

    SELECT COUNT(*)  AS [Sessions]
    FROM sys.dm_exec_connections C
    INNER JOIN sys.dm_exec_sessions S ON (S.session_id = C.session_id)
    INNER JOIN sys.databases D ON (D.database_id = S.database_id)
    WHERE D.name = 'MyDatabase'

Novamente, estas consultas devolvem uma contagem de ponto no tempo. Se recolher várias amostras ao longo do tempo, terá de compreender melhor da sessão, utilize.

Para análise de base de dados SQL, pode obter estatísticas históricas sessões consultando o [resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) vista e rever o **active_session_count** coluna. 

## <a name="next-steps"></a>Passos seguintes

- Otimizar os índices de base de dados e consultar planos de execução utilizando automaticamente [otimização automática da base de dados do Azure SQL](sql-database-automatic-tuning.md).
- Monitorizar o desempenho de base de dados automaticamente utilizando [Azure SQL inteligente Insights](sql-database-intelligent-insights.md). Esta funcionalidade fornece informações de diagnóstico e análise de problemas de desempenho da causa raiz.
