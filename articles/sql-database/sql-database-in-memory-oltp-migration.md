---
title: "OLTP na memória melhora o desempenho do SQL Server txn | Microsoft Docs"
description: "Adopt OLTP de dentro da memória para melhorar o desempenho transacional na base de dados existente do SQL Server."
services: sql-database
documentationcenter: 
author: jodebrui
manager: jhubbard
editor: MightyPen
ms.assetid: c2bf14a0-905b-47b4-afb6-efe9a61147d5
ms.service: sql-database
ms.custom: develop databases
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/22/2016
ms.author: jodebrui
ms.openlocfilehash: 71dd7d36eee210b80ed6a791b52f977a416b6bb7
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="use-in-memory-oltp-to-improve-your-application-performance-in-sql-database"></a>Utilização de memória OLTP para melhorar o desempenho da aplicação na base de dados do SQL Server
[OLTP na memória](sql-database-in-memory.md) podem ser utilizadas para melhorar o desempenho do processamento de transações, a ingestão de dados e cenários de dados transitório, no [Premium](sql-database-service-tiers.md) bases de dados do Azure SQL sem aumentar o escalão de preço. 

> [!NOTE] 
> Saiba como [quórum duplica carga de trabalho de chave da base de dados ao reduzirem DTU por 70% com base de dados SQL](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)


Siga estes passos para adotar OLTP dentro da memória na base de dados existente.

## <a name="step-1-ensure-you-are-using-a-premium-database"></a>Passo 1: Certifique-se de que está a utilizar uma base de dados Premium
OLTP dentro da memória só é suportado em bases de dados Premium. Dentro da memória é suportada se o resultado devolvido é 1 (não 0):

```
SELECT DatabasePropertyEx(Db_Name(), 'IsXTPSupported');
```

*XTP* representa *Alpine processamento de transação*



## <a name="step-2-identify-objects-to-migrate-to-in-memory-oltp"></a>Passo 2: Identificam os objetos a migrar para OLTP na memória
SSMS inclui um **descrição geral de análise do desempenho de transação** relatório que pode executar em relação a uma base de dados com uma carga de trabalho ativa. O relatório identifica as tabelas e procedimentos armazenados que sejam candidatas para a migração para OLTP na memória.

No SSMS, para gerar o relatório:

* No **Object Explorer**, clique com o botão direito do nó de base de dados.
* Clique em **relatórios** > **relatórios padrão** > **descrição geral de análise do desempenho de transação**.

Para obter mais informações, consulte [Determining se uma tabela ou armazenados procedimento deve ser convertidos para serem OLTP na memória](http://msdn.microsoft.com/library/dn205133.aspx).

## <a name="step-3-create-a-comparable-test-database"></a>Passo 3: Criar uma base de dados de teste comparável
Suponha que o relatório indica a que sua base de dados tem uma tabela que seria beneficiar a ser convertido para uma tabela com otimização de memória. Recomendamos que teste primeiro para confirmar a indicação de compatibilidade ao testar.

Precisa de uma cópia de teste da sua base de dados de produção. A base de dados de teste deve estar no mesmo nível de camada de serviço como a base de dados de produção.

Para facilitar a testar, otimizar a base de dados de teste da seguinte forma:

1. Liga à base de dados de teste com o SSMS.
2. Para evitar a necessidade da opção WITH (SNAPSHOT) em consultas, defina a opção de base de dados, conforme mostrado no seguinte instrução de T-SQL:
   
   ```
   ALTER DATABASE CURRENT
    SET
        MEMORY_OPTIMIZED_ELEVATE_TO_SNAPSHOT = ON;
   ```

## <a name="step-4-migrate-tables"></a>Passo 4: Migrar tabelas
Tem de criar e preencher uma cópia com otimização de memória da tabela que pretende testar. Pode criá-la utilizando:

* O Assistente de otimização de memória à mão no SSMS.
* Manual de T-SQL.

#### <a name="memory-optimization-wizard-in-ssms"></a>Assistente de otimização de memória no SSMS
Para utilizar esta opção de migração:

1. Ligar à base de dados de teste com o SSMS.
2. No **Object Explorer**, faça duplo clique na tabela e, em seguida, clique em **Advisor de otimização de memória**.
   
   * O **tabela memória otimizador Advisor** é apresentado o assistente.
3. No assistente, clique em **validação de migração** (ou o **seguinte** botão) para ver se a tabela contém quaisquer funcionalidades não suportadas que não são suportadas em tabelas com otimização de memória. Para obter mais informações, consulte:
   
   * O *lista de verificação de otimização de memória* no [Advisor de otimização de memória](http://msdn.microsoft.com/library/dn284308.aspx).
   * [As construções de Transact-SQL não suportadas pelo OLTP na memória](http://msdn.microsoft.com/library/dn246937.aspx).
   * [Migrar para OLTP na memória](http://msdn.microsoft.com/library/dn247639.aspx).
4. Se a tabela não tem nenhum funcionalidades não suportadas, o parceiro pode efetuar o esquema real e a migração de dados para si.

#### <a name="manual-t-sql"></a>Manual de T-SQL
Para utilizar esta opção de migração:

1. Liga a sua base de dados de teste com SSMS (ou um utilitário semelhante).
2. Obter o script T-SQL concluído para a tabela e seus índices.
   
   * No SSMS, clique no nó da tabela.
   * Clique em **Script tabela como** > **criar para** > **nova janela de consulta**.
3. Na janela do script, adicione WITH (MEMORY_OPTIMIZED = ON) para a instrução CREATE TABLE.
4. Se existir um índice em cluster, alterá-la para NONCLUSTERED.
5. Mudar o nome da tabela existente, utilizando SP_RENAME.
6. Crie a cópia nova da tabela com otimização de memória, executando o script de CREATE TABLE editado.
7. Copie os dados para a tabela com otimização de memória utilizando INSERT... SELECIONE * PARA:

```
INSERT INTO <new_memory_optimized_table>
        SELECT * FROM <old_disk_based_table>;
```


## <a name="step-5-optional-migrate-stored-procedures"></a>Passo 5 (opcional): Migrar procedimentos armazenados
A funcionalidade na memória também pode modificar um procedimento armazenado para um melhor desempenho.

### <a name="considerations-with-natively-compiled-stored-procedures"></a>Considerações de procedimentos armazenados compilados nativamente
Um procedimento armazenado compilado nativamente tem de ter as seguintes opções na sua cláusula T-SQL com:

* NATIVE_COMPILATION
* SCHEMABINDING: significado as tabelas que o procedimento armazenado não pode ter as respetivas definições de coluna alteradas de qualquer forma que iria afetar o procedimento armazenado, a menos largar o procedimento armazenado.

Um módulo nativo tem de utilizar uma grande [em blocos ATOMIC](http://msdn.microsoft.com/library/dn452281.aspx) para a gestão de transação. Não há nenhuma função para uma transação começar explícita ou para ROLLBACK TRANSACTION. Se o seu código detetar uma violação de uma regra de negócio, pode a terminar o bloco atomic com um [THROW](http://msdn.microsoft.com/library/ee677615.aspx) instrução.

### <a name="typical-create-procedure-for-natively-compiled"></a>CREATE PROCEDURE típico para compilados nativamente
Normalmente, o T-SQL para criar um procedimento armazenado compilado nativamente é semelhante para o modelo seguinte:

```
CREATE PROCEDURE schemaname.procedurename
    @param1 type1, …
    WITH NATIVE_COMPILATION, SCHEMABINDING
    AS
        BEGIN ATOMIC WITH
            (TRANSACTION ISOLATION LEVEL = SNAPSHOT,
            LANGUAGE = N'your_language__see_sys.languages'
            )
        …
        END;
```

* Para TRANSACTION_ISOLATION_LEVEL, o INSTANTÂNEO é o valor mais comuns para o procedimento armazenado compilado nativamente. No entanto, um subconjunto dos outros valores também são suportados:
  
  * LEITURA REPETIDA
  * SERIALIZÁVEL
* O valor de idioma deve estar presente na vista de sys.languages.

### <a name="how-to-migrate-a-stored-procedure"></a>Como migrar um procedimento armazenado
Os passos de migração são:

1. Obter o script de CREATE PROCEDURE para o procedimento armazenado interpretado regular.
2. O cabeçalho para fazer corresponder o modelo anterior de regravação.
3. Ascertain se o procedimento armazenado código T-SQL utiliza quaisquer funcionalidades que não são suportadas para procedimentos armazenados compilados nativamente. Implementar soluções se necessário.
   
   * Para mais informações, consulte [problemas de migração para compilada nativamente procedimentos armazenados](http://msdn.microsoft.com/library/dn296678.aspx).
4. Mudar o nome do procedimento armazenado antigo utilizando SP_RENAME. Ou, basta REMOVÊ-la.
5. Execute o script Criar procedimento T-SQL editado.

## <a name="step-6-run-your-workload-in-test"></a>Passo 6: Executar a carga de trabalho no teste
Execute uma carga de trabalho na sua base de dados de teste que é semelhante a carga de trabalho que é executado na sua base de dados de produção. Isto deve ficar a saber os ganhos de desempenho alcançado através da utilização da funcionalidade na memória para as tabelas e procedimentos armazenados.

Atributos principais da carga de trabalho são:

* Número de ligações em simultâneo.
* Rácio de leitura/escrita.

Para personalizar e executar a carga de trabalho de teste, considere utilizar a ferramenta de ostress.exe à mão, ilustrado na [aqui](sql-database-in-memory.md).

Para minimizar a latência de rede, execute o teste na mesma região geográfica do Azure onde a base de dados existe.

## <a name="step-7-post-implementation-monitoring"></a>Passo 7: Monitorização de pós-implementação
Considere os efeitos de desempenho das suas implementações de dentro da memória na produção de monitorização:

* [Monitorizar o armazenamento em memória](sql-database-in-memory-oltp-monitoring.md).
* [Monitorizar a Base de Dados SQL do Azure com vistas de gestão dinâmica (Monitoring Azure SQL Database using dynamic management views)](sql-database-monitoring-with-dmvs.md)

## <a name="related-links"></a>Ligações relacionadas
* [OLTP (otimização de memória) de memória](http://msdn.microsoft.com/library/dn133186.aspx)
* [Introdução aos procedimentos armazenados compilados nativamente](http://msdn.microsoft.com/library/dn133184.aspx)
* [Advisor de otimização de memória](http://msdn.microsoft.com/library/dn284308.aspx)

