---
title: "Repetíveis cópia no Azure Data Factory | Microsoft Docs"
description: "Saiba como evitar duplicados, apesar de um setor que copia dados é executado mais do que uma vez."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 0519a9c60386f8fea0047a661e48f008d3141c5e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="repeatable-copy-in-azure-data-factory"></a>Repetíveis cópia no Azure Data Factory

## <a name="repeatable-read-from-relational-sources"></a>Repetíveis leitura a partir de origens relacionais
Quando armazena a cópia de dados de dados relacionais, manter a repetibilidade em mente para evitar resultados inesperados. No Azure Data Factory, pode voltar a executar um setor manualmente. Também pode configurar a política de repetição para um conjunto de dados para que um setor será novamente executado quando ocorre uma falha. Quando um setor é voltar a executar qualquer forma, tem de certificar-se de que os mesmos dados é a leitura não independentemente um setor é executado o número de vezes.  
 
> [!NOTE]
> Os exemplos seguintes são para o Azure SQL, mas são aplicáveis a qualquer arquivo de dados que suporta conjuntos de dados retangular. Terá de ajustar o **tipo** de origem e o **consulta** propriedade (por exemplo: consulta em vez de sqlReaderQuery) para os dados de arquivo.   

Normalmente, ao ler do relacionais arquivos, pretende apenas os dados correspondente a essa segmentação de leitura. Uma forma de fazê-lo seria utilizando variáveis de sistema WindowStart e WindowEnd disponíveis no Azure Data Factory. Leia sobre as variáveis e funções no Azure Data Factory aqui no [do Azure Data Factory - as funções e variáveis do sistema](data-factory-functions-variables.md) artigo. Exemplo: 

```json
"source": {
    "type": "SqlSource",
    "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm\\'', WindowStart, WindowEnd)"
},
```
Esta consulta lê os dados que se situe no intervalo de duração setor (WindowStart -> WindowEnd) da tabela MyTable. Volte a executar este setor seria também sempre Certifique-se de que os mesmos dados é de leitura. 

Noutros casos, pode pretender ler a tabela completa e pode definir o sqlReaderQuery da seguinte forma:

```json
"source": 
{            
    "type": "SqlSource",
    "sqlReaderQuery": "select * from MyTable"
},
```

## <a name="repeatable-write-to-sqlsink"></a>Operação de escrita repetíveis SqlSink
Quando copiar dados para **Azure SQL/SQL Server** de outros arquivos de dados, terá de manter a repetibilidade em mente para evitar resultados inesperados. 

Quando copiar dados para a base de dados do Azure SQL/SQL Server, a atividade de cópia acrescenta dados para a tabela do sink por predefinição. Imaginemos que está a copiar dados de um ficheiro CSV (valores separados por vírgulas) que contém dois registos para a tabela seguinte uma base de dados do servidor SQL/SQL do Azure. Quando um setor é executado, os dois registos são copiados para a tabela SQL. 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
```

Suponha que encontrou erros no ficheiro de origem e atualizar a quantidade de baixo Tube de 2 a 4. Se voltar a executar o setor de dados para esse período manualmente, encontrará dois novos registos anexados a base de dados do Azure SQL/SQL Server. Este exemplo parte do princípio de que nenhuma das colunas na tabela tem restrição de chave primária.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Para evitar este comportamento, tem de especificar semântica UPSERT utilizando um dos dois mecanismos seguintes:

### <a name="mechanism-1-using-sqlwritercleanupscript"></a>Mecanismo de 1: sqlWriterCleanupScript utilizando o
Pode utilizar o **sqlWriterCleanupScript** propriedade a limpeza dos dados da tabela do sink antes de inserir os dados, quando um setor é executado. 

```json
"sink":  
{ 
  "type": "SqlSink", 
  "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM table WHERE ModifiedDate >= \\'{0:yyyy-MM-dd HH:mm}\\' AND ModifiedDate < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
}
```

Quando um setor é executado, o script de limpeza é executado pela primeira vez para eliminar dados que corresponde ao setor da tabela SQL. A atividade de cópia, em seguida, insere dados para a tabela de SQL. Se o setor será novamente executado, a quantidade é atualizada conforme pretendido.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Suponha que o registo de Washer simples é removido do original csv. Em seguida, volte a executar o setor produziria o seguinte resultado: 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
7     Down Tube    4            2015-05-01 00:00:00
```

A atividade de cópia foi executado o script de limpeza para eliminar os dados para esse setor correspondentes. Em seguida, lê-lo a entrada de csv (que contidas, em seguida, apenas um registo) e serão inseridos na tabela. 

### <a name="mechanism-2-using-sliceidentifiercolumnname"></a>Mecanismo de 2: sliceIdentifierColumnName utilizando o
> [!IMPORTANT]
> Atualmente, sliceIdentifierColumnName não é suportada para o Azure SQL Data Warehouse. 

O segundo mecanismo para alcançar repetibilidade é ter uma coluna dedicada (sliceIdentifierColumnName) na tabela de destino. Esta coluna seria utilizada pelo Azure Data Factory para garantir que a origem e de destino permanecem sincronizadas. Esta abordagem funciona quando existe flexibilidade na alterar ou definir o esquema da tabela de SQL de destino. 

Esta coluna é utilizada pelo Azure Data Factory para fins de repetibilidade e o processo do Azure Data Factory não fazer quaisquer alterações de esquema para a tabela. Para utilizar esta abordagem:

1. Definir uma coluna do tipo **binário (32)** no destino da tabela de SQL. Não deverá haver nenhum restrições nesta coluna. Vamos nome desta coluna como AdfSliceIdentifier para este exemplo.


    Tabela de origem:

    ```sql
    CREATE TABLE [dbo].[Student](
       [Id] [varchar](32) NOT NULL,
       [Name] [nvarchar](256) NOT NULL
    )
    ```

    Tabela de destino: 

    ```sql
    CREATE TABLE [dbo].[Student](
       [Id] [varchar](32) NOT NULL,
       [Name] [nvarchar](256) NOT NULL,
       [AdfSliceIdentifier] [binary](32) NULL
    )
    ```

2. Utilize-a atividade de cópia da seguinte forma:
   
    ```json
    "sink":  
    { 
   
        "type": "SqlSink", 
        "sliceIdentifierColumnName": "AdfSliceIdentifier"
    }
    ```

O Azure Data Factory preenche esta coluna de acordo com a respetiva necessidade de Certifique-se de que a origem e de destino permanecem sincronizadas. Os valores desta coluna não devem ser utilizados fora neste contexto. 

Semelhante ao mecanismo 1, atividade de cópia automaticamente limpa os dados para o setor especificado de tabela de SQL de destino. Em seguida, inserem dados de origem para a tabela de destino. 

## <a name="next-steps"></a>Passos seguintes
Reveja o conector seguinte artigos que, para obter exemplos JSON concluídos: 

- [Base de Dados SQL do Azure](data-factory-azure-sql-connector.md)
- [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md)
- [SQL Server](data-factory-sqlserver-connector.md)