---
title: "Recomendações de desempenho - SQL Database do Azure | Microsoft Docs"
description: "A base de dados do SQL do Azure fornece recomendações para as bases de dados do SQL Server que pode melhorar o desempenho de consulta atual."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: monicar
ms.assetid: 1db441ff-58f5-45da-8d38-b54dc2aa6145
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: On Demand
ms.date: 09/20/2017
ms.author: sstein
ms.openlocfilehash: 9b6c60a14578842f4b3b1a9e4724eab6de3f8815
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="performance-recommendations"></a>Recomendações de desempenho

Base de dados SQL do Azure aprende e feita com a sua aplicação e fornece recomendações personalizadas, permitindo-lhe para maximizar o desempenho das bases de dados SQL. O desempenho é avaliado em matéria continuamente ao analisar o histórico de utilização de base de dados SQL. As recomendações fornecidas baseiam-se num padrão de carga de trabalho exclusivo da base de dados e ajudam a melhorar o desempenho dele.

> [!TIP]
> [A otimização automática](sql-database-automatic-tuning.md) é a forma recomendada de otimização de desempenho. [Insights inteligentes](sql-database-intelligent-insights.md) é a forma recomendada de monitorização do desempenho. 
>

## <a name="create-index-recommendations"></a>Criar recomendações de índice
Base de dados SQL do Azure continuamente monitoriza as consultas que está a ser executadas e identifica os índices que podem melhorar o desempenho. Assim que o suficiente confiança baseia-se que um índice de determinados está em falta, um novo **criar índice** recomendação será criada. Base de dados SQL do Azure baseia-se a confiança para estimar os ganhos de desempenho que seria colocar o índice através de tempo. Consoante o ganho de desempenho estimado recomendações são categorizadas como alta, média ou baixa. 

Índices criados com as recomendações são sempre sinalizados como auto_created índices. Pode ver os índices são auto_created observando sys.indexes vista. Índices criada automaticamente não bloquear comandos de mudança de nome/ALTER. Se tentar remover a coluna que tenha uma automática criada índice sobre os mesmos, transmite o comando e automática criada índice é removida com o comando bem. Índices regulares bloqueariam o comando ALTER/mudança de nome em colunas que são indexados.

Assim que for aplicada a recomendação de índice de criar, SQL Database do Azure irá comparam o desempenho das consultas com o desempenho da linha de base. Se o novo índice colocado melhorias no desempenho, recomendação irá ser sinalizada como concluída com êxito e relatório impacto estará disponível. No caso do índice não colocar os benefícios, mesmo serão automaticamente revertido. Desta forma, SQL Database do Azure garante que utilizar recomendações serão apenas melhorar o desempenho de base de dados.

Qualquer **criar índice** recomendação tem um back desativar política não permite a aplicar a recomendação se a utilização de base de dados ou um conjunto de DTU foi superior a 80% nos últimos 20 minutos ou se o armazenamento é superior a 90% de utilização. Neste caso, será possível adiar a recomendação.

## <a name="drop-index-recommendations"></a>Remova as recomendações do índice
Para além de detetar um índice em falta, SQL Database do Azure analisa continuamente o desempenho dos índices existentes. Se não for utilizado o índice, SQL Database do Azure recomendará a remover. Remover um índice recomenda-se em dois cenários:
* O índice é um duplicado de outro índice (mesmo indexada e incluído coluna, esquema de partição e os filtros)
* Índice não é utilizado durante um período prolongado (93 dias)

Recomendações de índice de largar também aceder através de verificação após a implementação. Se o desempenho denota uma melhoria o relatório de impacto estará disponível. Caso seja detetada uma degradação do desempenho, recomendação será revertida.


## <a name="parameterize-queries-recommendations"></a>Parametrizar recomendações de consultas
**Parametrizar consultas** recomendações são apresentados quando tem uma ou mais consultas constantemente estão a ser recompiladas mas final cópias de segurança com o mesmo plano de execução de consulta. Esta condição abre-se uma oportunidade para aplicar parametrização forced forçada, que lhe permitirá planos de consulta sejam colocados em cache e reutilizada na futura melhorar o desempenho e reduzir a utilização de recursos. 

Cada consulta emitida inicialmente contra do SQL Server tem de ser compilado para gerar um plano de execução. Cada plano gerado é adicionado à cache de planos e execuções subsequentes da mesma consulta podem reutilizar este plano da cache, eliminando a necessidade de compilação adicional. 

As aplicações que enviar consultas, que incluem os valores não parametrizada, podem levar a uma sobrecarga de desempenho, onde cada essas consulta com valores de parâmetro diferentes o plano de execução é compilado novamente. Em muitos casos as consultas mesmos com o parâmetro diferentes valores geram os mesmo planos de execução, mas estes esquemas ainda separadamente são adicionadas à cache de planos. Planos de execução recompilar utilizam recursos de base de dados, aumentam a consulta tempo de duração e overflow cache de planos causar planos sejam expulsos da cache. Este comportamento do SQL Server pode ser alterado definindo a opção de parameterization forçada na base de dados. 

Para ajudar a calcular o impacto desta recomendação, são fornecidos com uma comparação entre a utilização de CPU real e a utilização de CPU prevista (como se foi aplicada a recomendação). Para além das poupanças da CPU, a duração de consulta diminui o tempo despendido na compilação. Também será muito menos sobrecarga na cache do plano, permitindo que a maioria dos planos para se manter na cache e ser reutilizadas. Pode aplicar esta recomendação rápida e facilmente, clicando no **aplicar** comando. 

Depois de aplicar esta recomendação, irá ativar parameterization forçada dentro de minutos na base de dados e iniciar o processo de monitorização que dura aproximadamente 24 horas. Após este período, será capaz de ver o relatório de validação que mostra a utilização da CPU da base de dados 24 horas antes e após a recomendação. Assistente de base de dados do SQL tem um mecanismo de segurança que é automaticamente revertida a recomendação aplicada no caso de foi detetado um regressão de desempenho.

## <a name="fix-schema-issues-recommendations"></a>Corrija as recomendações de problemas de esquema
**Corrigir problemas de esquema** recomendações são apresentados quando o serviço de base de dados SQL avisos uma anomalias no número de erros SQL relacionados com o esquema a acontecer na base de dados SQL do Azure. Esta recomendação, normalmente, é apresentada quando a sua base de dados encontra vários erros relacionados com o esquema (nome de coluna inválido, nome de objeto inválido, etc.) dentro de uma hora.

"Problemas de esquema" são uma classe de erros de sintaxe no SQL Server que acontecer quando a definição da consulta SQL e a definição do esquema da base de dados não estão alinhados. Por exemplo, uma das colunas esperadas pela consulta pode estar em falta na tabela de destino, ou vice-versa. 

Recomendação "Corrigir o problema de esquema" é apresentada quando o serviço SQL Database do Azure avisos uma anomalias no número de erros SQL relacionados com o esquema a acontecer na base de dados SQL do Azure. A tabela seguinte mostra os erros que estão relacionados com problemas de esquema:

| Código de erro do SQL Server | Mensagem |
| --- | --- |
| 201 |Função ou procedimento "*'espera o parâmetro'*', que não foi fornecido. |
| 207 |Nome de coluna inválido ' *'. |
| 208 |Nome de objeto inválido ' *'. |
| 213 |Nome da coluna ou o número de valores fornecidos não corresponde a definição da tabela. |
| 2812 |Não foi possível encontrar o procedimento armazenado ' *'. |
| 8144 |Função ou procedimento * tem demasiados argumentos especificados. |

## <a name="next-steps"></a>Passos seguintes
Monitorizar as recomendações e continuar a aplicar-lhes para otimizar o desempenho. Cargas de trabalho de base de dados são dinâmicas e alterar continuamente. SQL Database advisor continua a monitorizar e fornecer recomendações podem potencialmente melhorar o desempenho da base de dados. 

* Consulte [otimização automática da base de dados do Azure SQL](sql-database-automatic-tuning.md) para otimização automática de índices de base de dados e os planos de execução de consulta.
* Consulte [Azure SQL inteligente Insights](sql-database-intelligent-insights.md) para automaticamente monitorização do desempenho de base de dados com automatizada de diagnóstico e de raiz a causa de problemas de desempenho.
* Consulte [recomendações de desempenho no portal do Azure](sql-database-advisor-portal.md) para obter os passos sobre como utilizar as recomendações de desempenho no portal do Azure.
* Consulte [Query Performance Insight](sql-database-query-performance.md) para saber mais sobre e ver o impacto do desempenho das consultas superiores.


