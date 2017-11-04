---
title: "Aplicar as recomendações de desempenho - SQL Database do Azure | Microsoft Docs"
description: "Utilize o portal do Azure para obter recomendações de desempenho que podem otimizar o desempenho da base de dados SQL do Azure."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: monicar
ms.assetid: cda8a646-0584-4368-b28a-85cdd9b54fcd
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: On Demand
ms.date: 07/05/2017
ms.author: sstein
ms.openlocfilehash: 3c621fc557ed466ddf2b514136a32d98be454325
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="find-and-apply-performance-recommendations"></a>Localizar e aplicar as recomendações de desempenho

Pode utilizar o portal do Azure para obter recomendações de desempenho que podem otimizar o desempenho da base de dados SQL do Azure ou para corrigir algumas problema identificado na sua carga de trabalho. **Recomendação de desempenho** página no portal do Azure permite-lhe determinar as principais recomendações com base no respetivo impacto potencial. 

## <a name="viewing-recommendations"></a>Recomendações de visualização

Para ver e aplicar as recomendações de desempenho, terá do correto [controlo de acesso baseado em funções](../active-directory/role-based-access-control-what-is.md) permissões no Azure. **Leitor**, **contribuinte da BD SQL** são necessárias permissões para ver as recomendações, e **proprietário**, **contribuinte da BD SQL** são necessárias permissões para executar qualquer ação; Criar ou remova índices e cancelar a criação de índices.

Utilize os seguintes passos para obter recomendações de desempenho no portal do Azure:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Aceda a **mais serviços** > **bases de dados SQL**e selecione a base de dados.
3. Navegue para **recomendação de desempenho** para ver as recomendações disponíveis para a base de dados selecionada.

Recomendações de desempenho são apresentadas na tabela semelhante ao apresentado na figura seguinte:

![Recomendações](./media/sql-database-advisor-portal/recommendations.png)

Recomendações são ordenadas pelo respetivo impacto potencial no desempenho nas seguintes categorias:

| Impacto | Descrição |
|:--- |:--- |
| Elevado |Recomendações de elevado impacto devem fornecer o impacto mais significativo no desempenho. |
| Médio |Impacto médio recomendações devem melhorar o desempenho, mas não substancialmente. |
| Baixo |Recomendações de impacto baixa devem fornecer um melhor desempenho sem, mas poderão não ser significativos melhoramentos. |


> [!NOTE]
> Tem de base de dados SQL do Azure monitorizar atividades, pelo menos, um dia para identificar algumas recomendações. A base de dados do SQL do Azure pode otimizar mais facilmente para padrões de consulta consistente que pode para aleatórios bursts spotty da atividade. Se as recomendações não estão atualmente disponíveis, o **recomendação de desempenho** página fornece uma mensagem explicar o motivo.
> 

Também pode ver o estado do histórico de operações. Selecione uma recomendação ou estado para ver mais detalhes.

Eis um exemplo de recomendação "Criar índice" no portal do Azure.

![Criar o índice](./media/sql-database-advisor-portal/sql-database-performance-recommendation.png)

## <a name="applying-recommendations"></a>Aplicar as recomendações
Base de dados SQL do Azure dá-lhe controlo total sobre a forma como as recomendações são ativadas utilizando qualquer uma das três opções seguintes: 

* Aplica recomendações individuais um cada vez.
* Ative automático de otimização para aplicar automaticamente recomendações.
* Para implementar uma recomendação manualmente, execute o script de T-SQL recomendado na sua base de dados.

Selecione qualquer recomendação para ver os respectivos detalhes e, em seguida, clique em **Ver script** para rever os detalhes exatos de como a recomendação é criada.

A base de dados permanece online enquanto a recomendação é aplicada – através de recomendação de desempenho ou nunca otimização automática a demora uma base de dados offline.

### <a name="apply-an-individual-recommendation"></a>Aplicar uma recomendação individuais
Pode rever e aceitar recomendações um cada vez.

1. No **recomendações** página, selecione uma recomendação.
2. No **detalhes** página, clique em **aplicar** botão.
   
    ![aplicar a recomendação](./media/sql-database-advisor-portal/apply.png)

Recomendação selecionada são aplicadas na base de dados.

### <a name="removing-recommendations-from-the-list"></a>Remover recomendações da lista
Se a lista de recomendações contiver itens que pretende remover da lista, pode eliminar a recomendação:

1. Selecione uma recomendação na lista de **recomendações** para abrir os detalhes.
2. Clique em **rejeitar** no **detalhes** página.

Se assim o desejar, pode adicionar itens rejeitados de volta para o **recomendações** lista:

1. No **recomendações** página, clique em **vista rejeitada**.
2. Selecione um item rejeitado da lista para ver os respectivos detalhes.
3. Opcionalmente, clique em **anular rejeitar** para adicionar o índice de volta para a lista de principal de **recomendações**.


### <a name="enable-automatic-tuning"></a>Ativar o ajuste automático
Pode definir a base de dados do SQL do Azure para implementar automaticamente recomendações. Como recomendações fiquem disponíveis, são automaticamente aplicadas. Tal como acontece com todas as recomendações gerida pelo serviço, se o impacto de desempenho for negativo, a recomendação é revertida.

1. No **recomendações** página, clique em **Automate**:
   
    ![Definições do Advisor](./media/sql-database-advisor-portal/settings.png)
2. Selecione as ações para automatizar:
   
    ![Recomendado índices](./media/sql-database-advisor-portal/automation.png)

### <a name="manually-run-the-recommended-t-sql-script"></a>Executar manualmente o script T-SQL recomendado
Selecione qualquer recomendação e, em seguida, clique em **Ver script**. Execute este script em relação a base de dados para aplicar a recomendação manualmente.

*Índices que são executados manualmente não são monitorizados e validados para impacto no desempenho pelo serviço* , de modo que é sugerido que monitorizar estes índices após a criação para verificar que fornecem ganhos de desempenho e ajustar ou eliminá-los, se necessário. Para obter detalhes sobre a criação de índices, consulte [criar índice (Transact-SQL)](https://msdn.microsoft.com/library/ms188783.aspx).

### <a name="canceling-recommendations"></a>Cancelar recomendações
Recomendações que estão num **pendente**, **verificar**, ou **êxito** Estado pode ser cancelado. Recomendações com um Estado de **Executing** não pode ser cancelado.

1. Selecione uma recomendação no **otimização histórico** área para abrir o **os detalhes das recomendações** página.
2. Clique em **Cancelar** para abortar o processo de aplicar a recomendação.

## <a name="monitoring-operations"></a>Operações de monitorização
Aplicar a recomendação não poderá acontecer instantaneamente. O portal fornece detalhes sobre o estado de recomendação. Seguem-se Estados possíveis que pode ser um índice em:

| Estado | Descrição |
|:--- |:--- |
| Pendente |Aplica a recomendação comando foi recebido e está agendado para execução. |
| Executar |A recomendação está a ser aplicada. |
| A verificar |Recomendação foi aplicada com êxito e o serviço está a medir as vantagens. |
| Êxito |Recomendação foi aplicada com êxito e tem sido medidos benefícios. |
| Erro |Ocorreu um erro durante o processo de aplicar a recomendação. Isto pode ser um problema passageiro ou possivelmente a um esquema altere para a tabela e o script já não é válido. |
| A reversão |A recomendação foi aplicada, mas foi considerado vvalidação não performant e está a ser revertida automaticamente. |
| Reverter |A recomendação foi revertida. |

Clique uma recomendação de dentro do processo da lista para ver mais detalhes:

![Recomendado índices](./media/sql-database-advisor-portal/operations.png)

### <a name="reverting-a-recommendation"></a>Reverter uma recomendação
Se utilizou as recomendações de desempenho para aplicar a recomendação (ou seja, que não foi possível executar manualmente o script T-SQL), é automaticamente revertida a alteração se encontrar o impacto de desempenho pode ser negativo. Se por alguma razão simplesmente pretende reverter uma recomendação, pode efetuar o seguinte:

1. Selecione uma recomendação aplicada com êxito no **otimização histórico** área.
2. Clique em **reverter** no **detalhes de recomendação** página.

![Recomendado índices](./media/sql-database-advisor-portal/details.png)

## <a name="monitoring-performance-impact-of-index-recommendations"></a>Impacto no desempenho das recomendações do índice de monitorização
Depois das recomendações são implementadas com êxito (atualmente, operações de índice e parametrizar recomendações de consultas só), pode clicar em **informações acerca das consultas** recomendação detalhes página para abrir [consulta Informações acerca do desempenho](sql-database-query-performance.md) e ver o impacto do desempenho das consultas superiores.

![Impacto no desempenho do monitor](./media/sql-database-advisor-portal/query-insights.png)

## <a name="summary"></a>Resumo
Base de dados SQL do Azure fornece recomendações para melhorar o desempenho de base de dados do SQL Server. Ao fornecer scripts T-SQL, obter assistência na otimizar a base de dados e, em última análise melhorando o desempenho das consultas.

## <a name="next-steps"></a>Passos seguintes
Monitorizar as recomendações e continuar a aplicar-lhes para otimizar o desempenho. Cargas de trabalho de base de dados são dinâmicas e alterar continuamente. Base de dados SQL do Azure continua a monitorizar e fornecer recomendações podem potencialmente melhorar o desempenho da base de dados. 

* Consulte [otimização automática](sql-database-automatic-tuning.md) para saber mais sobre a otimização automática na SQL Database do Azure.
* Consulte [recomendações de desempenho](sql-database-advisor.md) para uma descrição geral das recomendações de desempenho de SQL Database do Azure.
* Consulte [Query Performance Insight](sql-database-query-performance.md) para saber mais sobre como ver o impacto do desempenho das consultas superiores.

## <a name="additional-resources"></a>Recursos adicionais
* [O arquivo de consultas](https://msdn.microsoft.com/library/dn817826.aspx)
* [CRIAR O ÍNDICE](https://msdn.microsoft.com/library/ms188783.aspx)
* [Controlo de acesso baseado em funções](../active-directory/role-based-access-control-what-is.md)

