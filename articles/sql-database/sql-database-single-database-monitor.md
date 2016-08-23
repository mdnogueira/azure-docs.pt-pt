<properties
    pageTitle="Monitorizar o desempenho de base de dados na Base de Dados SQL do Azure | Microsoft Azure"
    description="Saiba mais sobre as opções para monitorizar a base de dados com ferramentas do Azure e vistas de gestão dinâmica."
    keywords="monitorização de base de dados, desempenho de base de dados em nuvem"
    services="sql-database"
    documentationCenter=""
    authors="CarlRabeler"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="data-management"
    ms.date="07/06/2016"
    ms.author="carlrab"/>

# Monitorizar o desempenho de base de dados na Base de Dados SQL do Azure
A monitorização do desempenho de uma base de dados SQL do Azure é iniciada com a monitorização da utilização de recursos em relação ao nível de desempenho de base de dados que escolher. A monitorização ajuda-o a determinar se a base de dados tem excesso de capacidade ou está a ter problemas porque os recursos estão a ser excessivamente utilizados e a decidir se está na altura de ajustar o nível de desempenho e a [camada de serviços](sql-database-service-tiers.md) da base de dados. Pode monitorizar a base de dados com as ferramentas gráficas no [portal do Azure](https://portal.azure.com) ou através de [vistas de gestão dinâmica](https://msdn.microsoft.com/library/ms188754.aspx) do SQL.

## Monitorizar bases de dados com o portal do Azure

No [portal do Azure](https://portal.azure.com/), pode monitorizar a utilização de uma base de dados individual, selecionando a base de dados e clicando no gráfico **Monitorização**. É apresentada a janela **Métricas** que pode alterar ao clicar no botão **Editar gráfico**. Adicione as métricas seguintes:

- Percentagem de CPU
- Percentagem de DTU
- Percentagem de ES de Dados
- Percentagem de tamanho da Base de Dados

Depois de adicionar estas métricas, pode continuar a visualizá-las no gráfico **Monitorização** com mais detalhes sobre a janela **Métricas**. As quatro métricas mostram a percentagem de utilização média relativa à **DTU** da base de dados. Consulte o artigo [camadas do serviço](sql-database-service-tiers.md) para detalhes sobre as DTUs.

![Monitorização da camada de serviços do desempenho da base de dados.](./media/sql-database-service-tiers/sqldb_service_tier_monitoring.png)

Também pode configurar alertas para as métricas de desempenho. Clique no botão **Adicionar alerta** na janela **Métricas**. Siga o assistente para configurar o alerta. Tem a opção de alertar se as métricas excederem um determinado limiar ou se a métrica descer abaixo de um determinado limiar.

Por exemplo, se espera que a carga de trabalho na sua base de dados aumente, pode optar por configurar um alerta por e-mail sempre que a base de dados atingir 80% em qualquer uma das métricas de desempenho. Pode utilizar isto como um aviso inicial para calcular quando poderá ter de mudar para o próximo nível de desempenho superior.

As métricas de desempenho também podem ajudar a determinar se é possível mudar para um nível de desempenho inferior. Parta do princípio de que está a utilizar uma base de dados Standard S2 e que todas as métricas de desempenho mostram que, em média, a base de dados não utiliza mais de 10% em qualquer momento. É provável que a base de dados funcione corretamente no Standard S1. No entanto, esteja atento às cargas de trabalho que aumentam ou flutuam, antes de efetuar a decisão de passar para um nível de desempenho inferior.

## Monitorizar bases de dados do monitor com DMVs

As mesmas métricas que estão expostas no portal também estão disponíveis nas vistas de sistema: [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx)da base de dados **mestra** lógica do seu servidor, e em [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) da base de dados de utilizador. Utilize **sys.resource_stats**, se precisar de monitorizar dados menos granulares ao longo de um período de tempo. Utilize **sys.dm_db_resource_stats**, se precisar de monitorizar dados mais granulares num período de tempo mais pequeno. Para mais informações, consulte [Guia de Desempenho da Base de Dados SQL do Azure](sql-database-performance-guidance.md#monitoring-resource-use-with-sysresourcestats).

>[AZURE.NOTE] **sys.dm_db_resource_stats** devolve um conjunto de resultados vazio quando utilizado em bases de dados das edições Web e Business, que foram extinguidas.

Para conjuntos de bases de dados elásticas, pode monitorizar bases de dados individuais no conjunto, com as técnicas descritas nesta secção. Mas também é possível monitorizar o conjunto como um todo. Para informações, consulte [Monitorizar e gerir um conjunto de bases de dados elásticas](sql-database-elastic-pool-manage-portal.md).



<!--HONumber=Aug16_HO1-->


