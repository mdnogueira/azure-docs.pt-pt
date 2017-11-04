---
title: "Informações acerca do desempenho de consulta para a SQL Database do Azure | Microsoft Docs"
description: "Monitorização do desempenho de consulta identifica a maior parte das consultas de consumo de CPU para uma base de dados do SQL do Azure."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: monicar
ms.assetid: c2f580b2-3835-453f-89f5-140e02dd2ea7
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: On Demand
ms.date: 07/05/2017
ms.author: sstein
ms.openlocfilehash: 015f3255d0ff9691168899df1e983e70f35215b1
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="azure-sql-database-query-performance-insight"></a>SQL do Azure da base de dados desempenho das consultas
Gerir e otimizar o desempenho das bases de dados relacionais são uma tarefa um desafio que requer conhecimentos significativos e o investimento de tempo. Desempenho das consultas permite-lhe gaste menos tempo a resolução de problemas de desempenho de base de dados ao fornecer o seguinte:

* Mais aprofundadas sobre o seu consumo de recursos (DTU) de bases de dados. 
* As consultas principais por contagem de CPU/duração/execução que potencialmente podem ser otimizados para um melhor desempenho.
* A capacidade para desagregar os detalhes de uma consulta, ver o texto e o histórico de utilização de recursos. 
* As anotações que mostram as ações executadas de otimização do desempenho [Advisor de base de dados SQL do Azure](sql-database-advisor.md)  



## <a name="prerequisites"></a>Pré-requisitos
* Consulta Performance Insight requer que [arquivo de consultas](https://msdn.microsoft.com/library/dn817826.aspx) está ativo na base de dados. Se o arquivo de consultas não está em execução, o portal do pede-lhe para ativá-la.

## <a name="permissions"></a>Permissões
O seguinte [controlo de acesso baseado em funções](../active-directory/role-based-access-control-what-is.md) são necessárias permissões para utilizar o desempenho das consultas: 

* **Leitor**, **proprietário**, **contribuinte**, **contribuinte da BD SQL**, ou **contribuinte de servidor de SQL** são permissões necessário para ver o recurso superior consumir consultas e gráficos. 
* **Proprietário**, **contribuinte**, **contribuinte da BD SQL**, ou **contribuinte de servidor de SQL** são necessárias permissões para ver o texto da consulta.

## <a name="using-query-performance-insight"></a>Utilizar o desempenho das consultas
Desempenho das consultas é fácil de utilizar:

* Abra [portal do Azure](https://portal.azure.com/) e localizar a base de dados que pretende examinar. 
  * No menu do lado esquerdo, em suporte e resolução de problemas, selecione "Query Performance Insight".
* No primeiro separador, reveja a lista de consultas de consumo de recursos principais.
* Selecione uma consulta individual para ver os detalhes.
* Abra [Advisor de base de dados do SQL Azure](sql-database-advisor.md) e verifique se as recomendações estarão disponíveis.
* Utilizar controlos de deslize ou zoom ícones para alterar o intervalo observado.
  
    ![dashboard de desempenho](./media/sql-database-query-performance/performance.png)

> [!NOTE]
> Algumas horas de dados tem de ser capturadas pelo arquivo de consultas da base de dados do SQL Server fornecer o query performance Insight. Se a base de dados não tem nenhuma atividade ou o arquivo de consultas não esteve ativo durante um determinado período de tempo, os gráficos estará vazios quando se apresenta esse período de tempo. Se não está em execução, pode ativar o arquivo de consultas em qualquer altura.   
> 
> 

## <a name="review-top-cpu-consuming-queries"></a>Reveja principal da CPU de consultas de consumo
No [portal](http://portal.azure.com) efetue o seguinte procedimento:

1. Navegue para uma base de dados do SQL Server e clique em **todas as definições** > **suporte + resolução de problemas** > **nas informações de desempenho de consulta**. 
   
    ![Query Performance Insight][1]
   
    Abre a vista de consultas superiores e as consultas de consumo de CPU superiores são listadas.
2. Clique em torno do gráfico para obter mais detalhes.<br>Na linha superior mostra % geral de DTU para a base de dados, enquanto as barras mostram % de CPU consumido pelas consultas selecionadas durante o intervalo selecionado (por exemplo, se **passado semana** está selecionado cada barra representa um dia).
   
    ![consultas superiores][2]
   
    Grelha inferior representa a informação agregada para as consultas visível.
   
   * ID de consulta - o identificador exclusivo da consulta no interior da base de dados.
   * CPU por consulta durante o intervalo de observable (depende da função de agregação).
   * Duração por consulta (depende da função de agregação).
   * Número total de execuções de uma consulta específica.
     
     Selecione ou desmarque consultas individuais para incluir ou exclui-los do gráfico de caixas de verificação a utilizar.
3. Se os dados torna-se obsoleta, clique em de **atualizar** botão.
4. Pode utilizar controlos de deslize e zoom botões para alterar o intervalo de observação e investigar picos: ![definições](./media/sql-database-query-performance/zoom.png)
5. Opcionalmente, se pretender uma vista diferente, pode selecionar **personalizada** separador e definir:
   
   * Métrica (CPU, duração, a contagem de execução)
   * Intervalo de tempo (últimas 24 horas, após uma semana, passado mês). 
   * Número de consultas.
   * Função de agregação.
     
     ![settings](./media/sql-database-query-performance/custom-tab.png)

## <a name="viewing-individual-query-details"></a>Detalhes de consulta individuais de visualização
Para ver detalhes de consulta:

1. Clique em qualquer consulta na lista de consultas superiores.
   
    ![Detalhes](./media/sql-database-query-performance/details.png)
2. Abre a vista de detalhes e a contagem de consumo/duração/execução de CPU de consultas é reduzida ao longo do tempo.
3. Clique em torno do gráfico para obter mais detalhes.
   
   * Principal gráfico mostra a linha de base de dados DTU % geral e as barras são de % de CPU consumido pela consulta selecionada.
   * Segundo gráfico mostra a duração total pela consulta selecionada.
   * Gráfico de inferior mostra o número total de execuções pela consulta selecionada.
     
     ![detalhes de consulta][3]
4. Opcionalmente, utilize os controlos de deslize, zoom botões ou clique em **definições** para personalizar a forma como são apresentados os dados de consulta, ou escolha um período de hora diferente.

## <a name="review-top-queries-per-duration"></a>Reveja as consultas superiores por duração
A atualização recente de desempenho das consultas, introduzimos duas métricas novo que podem ajudar a identificar potenciais congestionamentos: contagem de duração e a execução.<br>

As consultas de execução longa ter a maior possibilidade de bloqueio mais recursos, bloquear a outros utilizadores e limitar a escalabilidade. Também são os melhor candidatos para a otimização.<br>

Para identificar o período de tempo de execução de consultas:

1. Abra **personalizada** separador no desempenho das consultas de base de dados selecionada
2. Alterar as métricas para ser **duração**
3. Selecione o número de consultas e o intervalo de observação
4. Selecione a função de agregação
   
   * **Soma** adiciona todos os tempo de execução da consulta durante o intervalo de observação todo.
   * **Máx.** localiza consultas que tempo de execução estava máximo no intervalo de observação todo.
   * **Média** encontra tempo médio de execução de todas as execuções de consulta e Mostrar parte superior fora destes médias. 
     
     ![duração de consulta][4]

## <a name="review-top-queries-per-execution-count"></a>Reveja as consultas superiores por contagem de execução
Número elevado de execuções poderá não ser que afetam a própria base de dados e a utilização de recursos pode ser reduzida, mas global da aplicação poderá obter lenta.

Em alguns casos, a contagem de execução muito elevado pode levar a aumentar de ida e volta de rede. Ida e volta afetar significativamente o desempenho. Estão sujeitos a latência de rede e a latência de servidor a jusante. 

Por exemplo, muitos sites Web condicionada por dados descontos elevados aceder à base de dados para todos os pedidos de utilizador. Enquanto a ligação de ajuda, o tráfego de rede de maior de agrupamento e a carga no servidor de base de dados de processamento pode afetar negativamente o desempenho.  Conselhos geral é manter ida e volta a um mínimo absoluto.

Para identificar frequentemente executar consultas de ("chatty") de consultas:

1. Abra **personalizada** separador no desempenho das consultas de base de dados selecionada
2. Alterar as métricas para ser **contagem de execução**
3. Selecione o número de consultas e o intervalo de observação
   
    ![Contagem de execução da consulta][5]

## <a name="understanding-performance-tuning-annotations"></a>Noções sobre anotações de otimização de desempenho
Enquanto a explorar a carga de trabalho no desempenho das consultas, é possível que repare ícones com linha vertical por cima do gráfico.<br>

Estes ícones são anotações; representam o desempenho que afetam a ações realizadas por [Advisor de base de dados do SQL Azure](sql-database-advisor.md). Por hovering anotação, obter informações básicas sobre a ação:

![anotação de consulta][6]

Se quiser obter mais informações ou aplicar a recomendação do advisor, clique no ícone. Detalhes de ação é aberto. Se for uma recomendação de Active Directory, pode aplicá-la diretamente ausente utilizando o comando.

![detalhes de anotação de consulta][7]

### <a name="multiple-annotations"></a>Vários anotações.
É possível que, devido ao nível de zoom, irão obter fechadas anotações que estão próximos entre si numa só. Isto será representado por ícone especial, clicar nela irá abrir novo painel onde a lista de agrupadas anotações será apresentado.
Correlacionar consultas e ações de otimização do desempenho pode ajudar a compreender melhor a sua carga de trabalho. 

## <a name="optimizing-the-query-store-configuration-for-query-performance-insight"></a>Otimizar a configuração do arquivo de consultas para o Query Performance Insight
Durante a utilização do desempenho das consultas, que poderão surgir as seguintes mensagens de arquivo de consultas:

* "Arquivo de consultas não está devidamente configurado nesta base de dados. Clique aqui para saber mais."
* "Arquivo de consultas não está devidamente configurado nesta base de dados. Clique aqui para alterar as definições." 

Estas mensagens são apresentados normalmente quando o arquivo de consultas não é possível recolher novos dados. 

Primeiro caso acontece quando o arquivo de consultas está no estado só de leitura e os parâmetros estão definidos de forma ideal. Para corrigir este problema, aumente o tamanho do arquivo de consultas ou limpar o arquivo de consultas.

![botão de qds][8]

Segundo caso acontece quando o arquivo de consultas está desligado ou com parâmetros não se encontram definidos de forma ideal. <br>Pode alterar a política de retenção e a captura e ativar o arquivo de consultas ao executar comandos abaixo ou diretamente a partir do portal:

![botão de qds][9]

### <a name="recommended-retention-and-capture-policy"></a>Política de retenção e a captura recomendada
Existem dois tipos de políticas de retenção:

* Tamanho baseado - se definido como AUTO-irá limpar dados automaticamente quando for atingido perto o tamanho máximo.
* Hora baseada - por predefinição, irá configurá-lo para 30 dias, que significa que, se o arquivo de consultas irá ficar sem espaço, eliminará as informações da consulta com mais de 30 dias

Captura de política pode ser definida como:

* **Todos os** -captura todas as consultas.
* **Auto** -influxo consultas e consultas com a duração de compilação e execução insignificant são ignoradas. Limiares de duração de execução contagem, compilação e tempo de execução são determinados internamente. Esta é a opção predefinida.
* **Nenhum** -arquivo de consultas deixa de captura nova consultas, no entanto, as estatísticas de tempo de execução de consultas já capturadas são ainda recolhidas.

Recomendamos que a definição de todas as políticas para limpar política para 30 dias e AUTOMATICAMENTE:

    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (SIZE_BASED_CLEANUP_MODE = AUTO);

    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (CLEANUP_POLICY = (STALE_QUERY_THRESHOLD_DAYS = 30));

    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (QUERY_CAPTURE_MODE = AUTO);

Aumente o tamanho do arquivo de consultas. Isto pode ser efetuado por ligar a uma base de dados e emitir seguinte consulta:

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (MAX_STORAGE_SIZE_MB = 1024);

Aplicar estas definições, eventualmente, faz com que o arquivo de consultas recolher novas consultas, no entanto, se não pretender aguardar pode limpar o arquivo de consultas. 

> [!NOTE]
> Em execução seguinte de consulta eliminará todas as informações atuais no arquivo de consultas. 
> 
> 

    ALTER DATABASE [YourDB] SET QUERY_STORE CLEAR;


## <a name="summary"></a>Resumo
Desempenho das consultas ajuda-o a compreender o impacto da sua carga de trabalho de consulta e como se relaciona com consumo de recursos de base de dados. Com esta funcionalidade, irá saber mais sobre as melhores consultas de consumo e identificar facilmente aqueles para corrigir antes de ficarem um problema.

## <a name="next-steps"></a>Passos seguintes
Para obter recomendações adicionais sobre como melhorar o desempenho da base de dados SQL, clique em [recomendações](sql-database-advisor.md) no **Query Performance Insight** painel.

![Advisor de desempenho](./media/sql-database-query-performance/ia.png)

<!--Image references-->
[1]: ./media/sql-database-query-performance/tile.png
[2]: ./media/sql-database-query-performance/top-queries.png
[3]: ./media/sql-database-query-performance/query-details.png
[4]: ./media/sql-database-query-performance/top-duration.png
[5]: ./media/sql-database-query-performance/top-execution.png
[6]: ./media/sql-database-query-performance/annotation.png
[7]: ./media/sql-database-query-performance/annotation-details.png
[8]: ./media/sql-database-query-performance/qds-off.png
[9]: ./media/sql-database-query-performance/qds-button.png

