---
title: Como utilizar o Azure Table Storage do Ruby | Microsoft Docs
description: "Armazene dados estruturados na nuvem através do Table Storage do Azure, um arquivo de dados NoSQL."
services: cosmos-db
documentationcenter: ruby
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 047cd9ff-17d3-4c15-9284-1b5cc61a3224
ms.service: cosmos-db
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 12/08/2016
ms.author: mimig
ms.openlocfilehash: 372bc89f75ad4730f0defbf9d6f9f041ae5ce1bf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-azure-table-storage-from-ruby"></a>Como utilizar o Azure Table Storage do Ruby
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-cosmos-db-langsoon-tip-include](../../includes/storage-table-cosmos-db-langsoon-tip-include.md)]

## <a name="overview"></a>Descrição geral
Este guia mostra como efetuar cenários comuns utilizando o serviço de Azure Table. Os exemplos são escritos utilizando a API de Ruby. Os cenários abrangidos incluem **criar e eliminar uma tabela, a inserir e consultar entidades numa tabela**.

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>Criar uma aplicação Ruby
Para obter instruções sobre como criar uma aplicação Ruby, consulte [Ruby na aplicação Rails Web numa VM do Azure](../virtual-machines/linux/classic/virtual-machines-linux-classic-ruby-rails-web-app.md).

## <a name="configure-your-application-to-access-storage"></a>Configurar a sua aplicação para aceder ao armazenamento
Para utilizar o Storage do Azure, terá de transferir e utilizar o pacote do azure Ruby, que inclui um conjunto de bibliotecas de conveniência que comunicam com os serviços de REST de armazenamento.

### <a name="use-rubygems-to-obtain-the-package"></a>Utilizar RubyGems para obter o pacote
1. Utilize uma interface de linha de comandos, como **PowerShell** (Windows), **Terminal** (Mac), ou **Bash** (Unix).
2. Tipo **azure de instalação gem** na janela de comando para instalar o gem e dependências.

### <a name="import-the-package"></a>Importar o pacote
Utilize o editor de texto favorito, adicione o seguinte na parte superior do ficheiro Ruby em que pretende utilizar o armazenamento:

```ruby
require "azure"
```

## <a name="set-up-an-azure-storage-connection"></a>Configurar uma ligação de armazenamento do Azure
O módulo do azure irá ler as variáveis de ambiente **AZURE\_armazenamento\_conta** e **AZURE\_armazenamento\_acesso\_chave** de informações necessárias para ligar à sua conta do Storage do Azure. Se estas variáveis de ambiente não estiver definidas, tem de especificar as informações de conta antes de utilizar **Azure::TableService** com o seguinte código:

```ruby
Azure.config.storage_account_name = "<your azure storage account>"
Azure.config.storage_access_key = "<your azure storage access key>"
```

Para obter estes valores a partir de um clássico ou conta de armazenamento do Resource Manager no portal do Azure:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Navegue para a conta de armazenamento que pretende utilizar.
3. No painel definições à direita, clique em **chaves de acesso**.
4. No painel de chaves de acesso que aparece, verá a chave de acesso 1 e a chave de acesso 2. Pode utilizar qualquer um destes.
5. Clique no ícone de cópia para copiar a chave para a área de transferência.

## <a name="create-a-table"></a>Criar uma tabela
O **Azure::TableService** objeto permite-lhe trabalhar com tabelas e entidades. Para criar uma tabela, utilize o **criar\_table()** método. O exemplo seguinte cria uma tabela ou imprima o erro se existir alguma.

```ruby
azure_table_service = Azure::TableService.new
begin
    azure_table_service.create_table("testtable")
rescue
    puts $!
end
```

## <a name="add-an-entity-to-a-table"></a>Adicionar uma entidade a uma tabela
Para adicionar uma entidade, primeiro crie um objeto de hash, que define as propriedades de entidade. Tenha em atenção que para cada entidade tem de especificar um **PartitionKey** e **RowKey**. Estes são os identificadores exclusivos da sua entidades e valores que podem ser consultados muito mais rapidamente do que as outras propriedades. Storage do Azure utiliza **PartitionKey** para distribuir automaticamente o entidades da tabela através de vários nós de armazenamento. Entidades com o mesmo **PartitionKey** são armazenados no mesmo nó. O **RowKey** é o ID exclusivo da entidade na partição pertence.

```ruby
entity = { "content" => "test entity",
    :PartitionKey => "test-partition-key", :RowKey => "1" }
azure_table_service.insert_entity("testtable", entity)
```

## <a name="update-an-entity"></a>Atualizar uma entidade
Existem vários métodos disponíveis para atualizar uma entidade existente:

* **Atualizar\_entity():** atualizar uma entidade existente, substituindo-lo.
* **intercalação\_entity():** atualiza uma entidade existente, a intercalação novos valores de propriedade para a entidade existente.
* **Inserir\_ou\_intercalação\_entity():** atualiza uma entidade existente, substituindo-lo. Se nenhuma entidade de existir, será inserido um novo:
* **Inserir\_ou\_substituir\_entity():** atualiza uma entidade existente, a intercalação novos valores de propriedade para a entidade existente. Se nenhuma entidade de existir, será inserido um novo.

O exemplo seguinte demonstra a atualizar uma entidade utilizando **atualizar\_entity()**:

```ruby
entity = { "content" => "test entity with updated content",
    :PartitionKey => "test-partition-key", :RowKey => "1" }
azure_table_service.update_entity("testtable", entity)
```

Com **atualizar\_entity()** e **intercalação\_entity()**, se a entidade que estão a atualizar não existir, a operação de atualização irá falhar. Por conseguinte, se pretender armazenar uma entidade, independentemente de já existir, deve em vez disso, utilizar **inserir\_ou\_substituir\_entity()** ou **inserir\_ou\_intercalação\_entity()**.

## <a name="work-with-groups-of-entities"></a>Trabalhar com grupos de entidades
Por vezes, faz sentido para submeter várias operações em conjunto num batch para garantir atómico processamento pelo servidor. Para realizar que, se crie primeiro um **Batch** de objeto e, em seguida, utilizar o **executar\_batch()** método no **TableService**. O exemplo seguinte demonstra submeter duas entidades com RowKey 2 e 3 num batch. Tenha em atenção que só funciona para entidades com o mesmo PartitionKey.

```ruby
azure_table_service = Azure::TableService.new
batch = Azure::Storage::Table::Batch.new("testtable",
    "test-partition-key") do
    insert "2", { "content" => "new content 2" }
    insert "3", { "content" => "new content 3" }
end
results = azure_table_service.execute_batch(batch)
```

## <a name="query-for-an-entity"></a>Consulta de uma entidade
Para consultar uma entidade numa tabela, utilize o **obter\_entity()** método, transferindo o nome da tabela, **PartitionKey** e **RowKey**.

```ruby
result = azure_table_service.get_entity("testtable", "test-partition-key",
    "1")
```

## <a name="query-a-set-of-entities"></a>Consulta de um conjunto de entidades
Para consultar um conjunto de entidades numa tabela, crie um objeto de hash de consulta e utilize o **consulta\_entities()** método. O exemplo seguinte demonstra a obter todas as entidades com o mesmo **PartitionKey**:

```ruby
query = { :filter => "PartitionKey eq 'test-partition-key'" }
result, token = azure_table_service.query_entities("testtable", query)
```

> [!NOTE]
> Se definir o resultado é demasiado grande para uma única consulta devolver, vai ser devolvido um token de continuação que pode utilizar para obter as páginas subsequentes.
>
>

## <a name="query-a-subset-of-entity-properties"></a>Consultar um subconjunto de propriedades de entidade
Uma consulta a uma tabela pode obter apenas algumas propriedades de uma entidade. Esta técnica, denominada "projeção", reduz a largura de banda e pode melhorar o desempenho de consulta, especialmente para entidades grandes. Utilize a cláusula select e passar os nomes das propriedades que pretende colocar do cliente.

```ruby
query = { :filter => "PartitionKey eq 'test-partition-key'",
    :select => ["content"] }
result, token = azure_table_service.query_entities("testtable", query)
```

## <a name="delete-an-entity"></a>Eliminar uma entidade
Para eliminar uma entidade, utilize o **eliminar\_entity()** método. Tem de passar em nome de tabela que contém a entidade, o PartitionKey e RowKey da entidade.

```ruby
azure_table_service.delete_entity("testtable", "test-partition-key", "1")
```

## <a name="delete-a-table"></a>Eliminar uma tabela
Para eliminar uma tabela, utilize o **eliminar\_table()** método e passar no nome da tabela que pretende eliminar.

```ruby
azure_table_service.delete_table("testtable")
```

## <a name="next-steps"></a>Passos seguintes

* O [Explorador de Armazenamento do Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) é uma aplicação autónoma e gratuita da Microsoft, que lhe permite trabalhar visualmente com dados do Armazenamento do Azure no Windows, macOS e Linux.
* [Azure SDK para Ruby](http://github.com/WindowsAzure/azure-sdk-for-ruby) repositório no GitHub

