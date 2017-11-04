## <a name="repeatability-during-copy"></a>Repetibilidade durante a cópia
Quando a cópia de dados para o Azure SQL/SQL Server de outros dados armazena um tem de manter a repetibilidade em mente para evitar resultados inesperados. 

Quando copiar dados para a base de dados do Azure SQL/SQL Server, a atividade de cópia irá por predefinição ACRESCENTAR o conjunto de dados para a tabela do sink por predefinição. Por exemplo, ao copiar dados a partir de uma origem de ficheiro CSV (dados de valores separados por vírgulas) que contém dois registos a base de dados do Azure SQL/SQL Server, é este aspeto a tabela:

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
```

Suponha que encontrou erros no ficheiro de origem e atualizar a quantidade de baixo Tube de 2 a 4 no ficheiro de origem. Se executar novamente o setor de dados para esse período, encontrará dois novos registos anexados a base de dados do Azure SQL/SQL Server. O abaixo parte do princípio de nenhuma das colunas na tabela tem restrição de chave primária.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Para evitar isto, terá de especificar a semântica UPSERT por tirar partido de uma do abaixo 2 mecanismos indicados abaixo.

> [!NOTE]
> Um setor pode ser novamente executado automaticamente no Azure Data Factory de acordo com a política de repetição especificada.
> 
> 

### <a name="mechanism-1"></a>Mecanismo de 1
Pode tirar partido **sqlWriterCleanupScript** propriedade para executar primeiro a ação de limpeza quando um setor é executado. 

```json
"sink":  
{ 
  "type": "SqlSink", 
  "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM table WHERE ModifiedDate >= \\'{0:yyyy-MM-dd HH:mm}\\' AND ModifiedDate < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
}
```

O script de limpeza iria ser executado primeiro durante a cópia de um setor específico que pretende eliminar os dados da tabela de SQL correspondente a essa setor. A atividade será subsequentemente inserir os dados na tabela do SQL Server. 

Se o setor está agora a voltar a executar, em seguida, que encontrará que quantidade é atualizada como pretendido.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Suponha que o registo de Washer simples é removido do original csv. Em seguida, voltar a executar o setor produziria o seguinte resultado: 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
7     Down Tube    4            2015-05-01 00:00:00
```
Nada de novo tinha de ser efetuada. A atividade de cópia foi executado o script de limpeza para eliminar os dados para esse setor correspondentes. Em seguida, lê-lo a entrada de csv (que, em seguida, contidos apenas 1 registo) e serão inseridos na tabela. 

### <a name="mechanism-2"></a>Mecanismo de 2
> [!IMPORTANT]
> sliceIdentifierColumnName não é suportada para o Azure SQL Data Warehouse neste momento. 

Outro mecanismo para alcançar repetibilidade é ter uma coluna dedicada (**sliceIdentifierColumnName**) na tabela de destino. Esta coluna seria utilizada pelo Azure Data Factory para garantir que a origem e de destino permanecem sincronizadas. Esta abordagem funciona quando existe flexibilidade na alterar ou definir o esquema da tabela de SQL de destino. 

Esta coluna seria utilizada pelo Azure Data Factory para fins de repetibilidade e no processo do Azure Data Factory não efetuará quaisquer alterações de esquema para a tabela. Para utilizar esta abordagem:

1. Defina uma coluna do tipo binário (32) no destino da tabela de SQL. Não deverá haver nenhum restrições nesta coluna. Vamos nome desta coluna como 'ColumnForADFuseOnly' para este exemplo.
2. Utilize-a atividade de cópia da seguinte forma:
   
    ```json
    "sink":  
    { 
   
        "type": "SqlSink", 
        "sliceIdentifierColumnName": "ColumnForADFuseOnly"
    }
    ```

O Azure Data Factory preencherá esta coluna de acordo com a respetiva necessidade de Certifique-se de que a origem e de destino permanecem sincronizadas. Os valores desta coluna não devem ser utilizados fora neste contexto pelo utilizador. 

Semelhante ao mecanismo 1, atividade de cópia será automaticamente primeiro limpar os dados para o setor especificado de tabela de SQL de destino e, em seguida, executar a atividade de cópia normalmente para inserir os dados de origem para o destino para esse setor. 

