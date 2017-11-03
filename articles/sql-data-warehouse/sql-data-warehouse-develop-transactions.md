---
title: "Transações no SQL Data Warehouse | Microsoft Docs"
description: "Sugestões para implementar as transações no Azure SQL Data Warehouse para desenvolver soluções."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: ae621788-e575-41f5-8bfe-fa04dc4b0b53
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: t-sql
ms.date: 10/31/2016
ms.author: jrj;barbkess
ms.openlocfilehash: 29d53e18539f2c24dd64090b2ac6f9dd4c783961
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="transactions-in-sql-data-warehouse"></a>Transações no SQL Data Warehouse
Como seria de esperar, o armazém de dados SQL suporta transações como parte da carga de trabalho de armazém de dados. No entanto, para garantir que o desempenho do armazém de dados do SQL Server é mantido na escala algumas funcionalidades estão limitadas quando comparado com o SQL Server. Este artigo realça as diferenças e apresenta uma lista de outros. 

## <a name="transaction-isolation-levels"></a>Níveis de isolamento da transação
O SQL Data Warehouse implementa transações ACID. No entanto, o isolamento do suporte de transacional está limitado a `READ UNCOMMITTED` e não pode ser alterado. Pode implementar um número de codificação métodos para impedir a desatualização leituras de dados se esta for uma preocupação para si. Os métodos mais populares tirar partido CTAS e mudança de partições de tabela (frequentemente designado como o padrão de janela deslizante) para impedir que os utilizadores consultar os dados que ainda está a ser preparados. As vistas que previamente filtrar os dados também é uma abordagem mais popular.  

## <a name="transaction-size"></a>Tamanho de transação
Uma transação de modificação de dados individual está limitada no tamanho. O limite de hoje em dia é aplicado "por distribuição". Por conseguinte, é possível calcular a alocação total multiplicando o limite, a contagem de distribuição. Para aproximado o número máximo de linhas na transação dividir a extremidade de distribuição, o tamanho total de cada linha. Para colunas de comprimento variável considere colocar um comprimento de coluna médio, em vez de utilizar o tamanho máximo.

Na tabela abaixo os seguintes pressupostos foram efetuadas:

* Ocorreu uma distribuição mesmo de dados 
* O comprimento de linha média é 250 bytes

| [DWU][DWU] | Limite máximo por distribuição (GiB) | Número de distribuições | Tamanho máximo de transações (GiB) | # Linhas por distribuição | Número máximo de linhas por transação |
| --- | --- | --- | --- | --- | --- |
| DW100 |1 |60 |60 |4,000,000 |240,000,000 |
| DW200 |1.5 |60 |90 |6,000,000 |360,000,000 |
| DW300 |2.25 |60 |135 |9,000,000 |540,000,000 |
| DW400 |3 |60 |180 |12,000,000 |720,000,000 |
| DW500 |3.75 |60 |225 |15,000,000 |900,000,000 |
| DW600 |4.5 |60 |270 |18,000,000 |1,080,000,000 |
| DW1000 |7.5 |60 |450 |30,000,000 |1,800,000,000 |
| DW1200 |9 |60 |540 |36,000,000 |2,160,000,000 |
| DW1500 |11.25 |60 |675 |45,000,000 |2,700,000,000 |
| DW2000 |15 |60 |900 |60,000,000 |3,600,000,000 |
| DW3000 |22.5 |60 |1,350 |90,000,000 |5,400,000,000 |
| DW6000 |45 |60 |2,700 |180,000,000 |10,800,000,000 |

O limite de tamanho de transação é aplicado por transação ou a operação. Não é aplicado em todas as transações em simultâneo. Por conseguinte cada transação tem permissão para escrever esta quantidade de dados no registo. 

Para otimizar e minimiza a quantidade de dados escritos no registo consulte o [transações melhores práticas] [ Transactions best practices] artigo.

> [!WARNING]
> Só pode ser alcançado o tamanho máximo de transação de HASH ou tabelas ROUND_ROBIN distribuída em que a propagação de dados é mesmo. Se a transação é a escrita de dados de forma distorcida para as distribuições do limite é provável que está acessível antes do tamanho máximo de transações.
> <!--REPLICATED_TABLE-->
> 
> 

## <a name="transaction-state"></a>Estado da transação
O SQL Data Warehouse utiliza a função XACT_STATE() para comunicar uma transação falhou com o valor de -2. Isto significa que a transação falhou e que está marcada para reversão apenas

> [!NOTE]
> A utilização de -2 pela função XACT_STATE para denotar uma falha na transação representa o comportamento diferente para o SQL Server. SQL Server utiliza o valor de -1 para representar uma transação não consolidável. SQL Server podem tolerar alguns erros dentro de uma transação sem ele ter de ser marcados como não consolidável. Por exemplo `SELECT 1/0` iria originar um erro, mas não força uma transação para um Estado não consolidável. SQL Server também permite ao leituras na transação não consolidável. No entanto, o armazém de dados do SQL não permitem-lhe fazê-lo. Se ocorrer um erro no interior de uma transação do SQL Data Warehouse entrará automaticamente o estado de-2 e não poderá efetuar qualquer selecione ainda mais as instruções até que a instrução foi revertida. Consequentemente, é importante verificar que o código da aplicação para ver se utiliza XACT_STATE() como poderá ter de efetuar as modificações de código.
> 
> 

Por exemplo, no SQL Server, poderá ver uma transação que tem este aspeto:

```sql
SET NOCOUNT ON;
DECLARE @xact_state smallint = 0;

BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(INT,'ABC');
    END TRY
    BEGIN CATCH
        SET @xact_state = XACT_STATE();

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;

        IF @@TRANCOUNT > 0
        BEGIN
            PRINT 'ROLLBACK';
            ROLLBACK TRAN;
        END

    END CATCH;

IF @@TRANCOUNT >0
BEGIN
    PRINT 'COMMIT';
    COMMIT TRAN;
END

SELECT @xact_state AS TransactionState;
```

Se deixar o seu código dado que está acima, em seguida, obterá a seguinte mensagem de erro:

Tarifas de mensagens 111233, 16 nível, 1, 1 111233; de linha de estado A transação atual foi abortada e quaisquer alterações pendentes tem foi revertidas. Causa: Uma transação no estado só de reversão de não foi explicitamente revertida antes de um DDL, DML ou instrução SELECT.

Também irá obter o resultado das funções ERROR_ * não.

No SQL Data Warehouse o código de tem de ser ligeiramente alterado:

```sql
SET NOCOUNT ON;
DECLARE @xact_state smallint = 0;

BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(INT,'ABC');
    END TRY
    BEGIN CATCH
        SET @xact_state = XACT_STATE();

        IF @@TRANCOUNT > 0
        BEGIN
            PRINT 'ROLLBACK';
            ROLLBACK TRAN;
        END

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;
    END CATCH;

IF @@TRANCOUNT >0
BEGIN
    PRINT 'COMMIT';
    COMMIT TRAN;
END

SELECT @xact_state AS TransactionState;
```

Agora é observado o comportamento esperado. Erro na transação é gerido e as funções ERROR_ * fornecem valores conforme esperado.

Tudo o que mudou é que o `ROLLBACK` da transação tinha ocorrer antes da leitura das informações de erro no `CATCH` bloco.

## <a name="errorline-function"></a>Função Error_Line()
É também vale a pena realçar que o SQL Data Warehouse não implementar ou suportam a função de ERROR_LINE(). Se tiver isto no seu código terá de removê-lo para ser compatível com o SQL Data Warehouse. Utilize etiquetas de consulta no seu código em vez disso, para implementar a funcionalidade equivalente. Consulte o [etiqueta] [ LABEL] artigo para obter mais detalhes sobre esta funcionalidade.

## <a name="using-throw-and-raiserror"></a>Utilizar THROW e RAISERROR
ACIONE é a implementação mais moderna para desencadear exceções no SQL Data Warehouse mas RAISERROR também é suportada. Existem algumas diferenças que são, prestando especial atenção aos contudo.

* Mensagens de erro números não podem estar no intervalo de 100 000-150 000 para THROW definido pelo utilizador
* Mensagens de erro RAISERROR sejam fixos em 50 000
* Não é suportada a utilização de sys

## <a name="limitiations"></a>Limitiations
O SQL Data Warehouse tem alguns outras restrições relacionadas com transações.

Estas são as seguintes:

* Não existem transações distribuídas
* Não existem transações aninhadas permitidas
* Não guarde pontos permitido
* Não existem transações com o nome
* Não existem transações marcadas
* Sem suporte de DDL como `CREATE TABLE` dentro de um utilizador definido transação

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre a otimização de transações, consulte [transações melhores práticas][Transactions best practices].  Para mais informações sobre outros procedimentos recomendados de armazém de dados do SQL Server, consulte o artigo [melhores práticas do SQL Data Warehouse][SQL Data Warehouse best practices].

<!--Image references-->

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md
[development overview]: ./sql-data-warehouse-overview-develop.md
[Transactions best practices]: ./sql-data-warehouse-develop-best-practices-transactions.md
[SQL Data Warehouse best practices]: ./sql-data-warehouse-best-practices.md
[LABEL]: ./sql-data-warehouse-develop-label.md

<!--MSDN references-->

<!--Other Web references-->
