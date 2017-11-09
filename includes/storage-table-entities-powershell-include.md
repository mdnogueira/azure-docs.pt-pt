<!--created by Robin Shahan to go in the articles for table storage w/powershell.
    There is one for Azure Table Storage and one for Azure Cosmos DB Table API -->

## <a name="managing-table-entities"></a>Gerir as entidades da tabela

Agora que tem uma tabela, vamos ver como gerir entidades ou linhas, na tabela. 

Uma entidade pode ter até 255 propriedades, incluindo 3 Propriedades do sistema: **PartitionKey**, **RowKey**, e **Timestamp**. É responsável por a inserir e a atualizar os valores de **PartitionKey** e **RowKey**. O servidor gere o valor de **Timestamp**, que não pode ser modificado. Em conjunto o **PartitionKey** e **RowKey** identificar de forma exclusiva cada entidade dentro de uma tabela.

* **PartitionKey**: determina a partição que a entidade é armazenada no.
* **RowKey**: identificam de forma exclusiva a entidade na partição.

Pode definir até 252 propriedades personalizadas para uma entidade. 

### <a name="add-table-entities"></a>Adicione as entidades da tabela

Adicionar entidades para uma tabela utilizando **adicionar StorageTableRow**. Estes exemplos utilizam chaves de partição com valores "partition1" e "partition2" e chaves de linha igual a abreviaturas de estado. As propriedades de cada entidade são o nome de utilizador e userid. 

```powershell
$partitionKey1 = "partition1"
$partitionKey2 = "partition2"

# add four rows 
Add-StorageTableRow `
    -table $storageTable `
    -partitionKey $partitionKey1 `
    -rowKey ("CA") -property @{"username"="Chris";"userid"=1}

Add-StorageTableRow `
    -table $storageTable `
    -partitionKey $partitionKey2 `
    -rowKey ("NM") -property @{"username"="Jessie";"userid"=2}

Add-StorageTableRow `
    -table $storageTable `
    -partitionKey $partitionKey1 `
    -rowKey ("WA") -property @{"username"="Christine";"userid"=3}

Add-StorageTableRow `
    -table $storageTable `
    -partitionKey $partitionKey2 `
    -rowKey ("TX") -property @{"username"="Steven";"userid"=4}
```

### <a name="query-the-table-entities"></a>As entidades da tabela de consulta

Existem várias formas diferentes de consultar as entidades numa tabela.

#### <a name="retrieve-all-entities"></a>Obter todas as entidades

Para obter todas as entidades, utilize **Get-AzureStorageTableRowAll**.

```powershell
Get-AzureStorageTableRowAll -table $storageTable | ft
```

Este comando gera resultados semelhantes para a tabela seguinte:

| ID de utilizador | o nome de utilizador | Partição | rowkey |
|----|---------|---------------|----|
| 1 | Chris | Partition1 | AC |
| 3 | Christine | Partition1 | WA |
| 2 | Jessie | Partition2 | NM |
| 4 | Steven | Partition2 | TX |

#### <a name="retrieve-entities-for-a-specific-partition"></a>Obter entidades para uma partição específica

Para obter todas as entidades numa partição específica, utilize **Get-AzureStorageTableRowByPartitionKey**.

```powershell
Get-AzureStorageTableRowByPartitionKey -table $storageTable -partitionKey $partitionKey1 | ft
```
Os resultados semelhante a tabela seguinte:

| ID de utilizador | o nome de utilizador | Partição | rowkey |
|----|---------|---------------|----|
| 1 | Chris | Partition1 | AC |
| 3 | Christine | Partition1 | WA |

#### <a name="retrieve-entities-for-a-specific-value-in-a-specific-column"></a>Obter entidades para um valor específico numa coluna específica

Para obter entidades em que o valor numa coluna específica é igual a um determinado valor, utilize **Get-AzureStorageTableRowByColumnName**.

```powershell
Get-AzureStorageTableRowByColumnName -table $storageTable `
    -columnName "username" `
    -value "Chris" `
    -operator Equal
```

Esta consulta obtém um registo.

|Campo|valor|
|----|----|
| ID de utilizador | 1 |
| o nome de utilizador | Chris |
| PartitionKey | Partition1 |
| RowKey      | AC |

#### <a name="retrieve-entities-using-a-custom-filter"></a>Obter entidades utilizando um filtro personalizado 

Para obter entidades utilizando um filtro personalizado, utilize **Get-AzureStorageTableRowByCustomFilter**.

```powershell
Get-AzureStorageTableRowByCustomFilter `
    -table $storageTable `
    -customFilter "(userid eq '1')"
```

Esta consulta obtém um registo.

|Campo|valor|
|----|----|
| ID de utilizador | 1 |
| o nome de utilizador | Chris |
| PartitionKey | Partition1 |
| RowKey      | AC |

### <a name="updating-entities"></a>Atualizar entidades 

Existem três passos para atualizar entidades. Em primeiro lugar, obter a entidade para ser alterada. Segundo, efetue a alteração. Terceira, consolidar a alteração utilizando **atualização AzureStorageTableRow**.

Atualizar a entidade com o nome de utilizador = 'Jessie' com o nome de utilizador = 'Jessie2'. Este exemplo mostra também outra forma de criar um filtro personalizado utilizando tipos de .NET. 

```powershell
# Create a filter and get the entity to be updated.
[string]$filter = `
    [Microsoft.WindowsAzure.Storage.Table.TableQuery]::GenerateFilterCondition("username",`
    [Microsoft.WindowsAzure.Storage.Table.QueryComparisons]::Equal,"Jessie")
$user = Get-AzureStorageTableRowByCustomFilter `
    -table $storageTable `
    -customFilter $filter

# Change the entity.
$user.username = "Jessie2" 

# To commit the change, pipe the updated record into the update cmdlet.
$user | Update-AzureStorageTableRow -table $storageTable 

# To see the new record, query the table.
Get-AzureStorageTableRowByCustomFilter -table $storageTable `
    -customFilter "(username eq 'Jessie2')"
```

O resultado mostra que o registo de Jessie2.

|Campo|valor|
|----|----|
| ID de utilizador | 2 |
| o nome de utilizador | Jessie2 |
| PartitionKey | Partition2 |
| RowKey      | NM |

### <a name="deleting-table-entities"></a>Eliminar as entidades da tabela

Pode eliminar uma entidade ou todas as entidades na tabela.

#### <a name="deleting-one-entity"></a>Eliminar uma entidade

Para eliminar uma entidade única, obtenha uma referência para essa entidade e encaminhá-la para **remover AzureStorageTableRow**.

```powershell
# Set filter.
[string]$filter = `
  [Microsoft.WindowsAzure.Storage.Table.TableQuery]::GenerateFilterCondition("username",`
  [Microsoft.WindowsAzure.Storage.Table.QueryComparisons]::Equal,"Jessie2")

# Retrieve entity to be deleted, then pipe it into the remove cmdlet.
$userToDelete = Get-AzureStorageTableRowByCustomFilter `
    -table $storageTable `
    -customFilter 
$userToDelete | Remove-AzureStorageTableRow -table $storageTable 

# Retrieve entities from table and see that Jessie2 has been deleted.
Get-AzureStorageTableRowAll -table $storageTable | ft
```

#### <a name="delete-all-entities-in-the-table"></a>Eliminar todas as entidades na tabela 

Para eliminar todas as entidades na tabela, pode obter os resultados do pipe para o cmdlet remove. 

```powershell
# Get all rows and pipe it into the remove cmdlet.
Get-AzureStorageTableRowAll `
    -table $storageTable | Remove-AzureStorageTableRow -table $storageTable 

# List entities in the table (there won't be any).
Get-AzureStorageTableRowAll -table $storageTable | ft
```