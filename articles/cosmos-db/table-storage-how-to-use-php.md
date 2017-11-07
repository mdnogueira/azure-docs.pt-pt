---
title: Como utilizar o table storage do PHP | Microsoft Docs
description: "Saiba como utilizar o serviço de tabela do PHP para criar e eliminar uma tabela e inserir, eliminar e consultar a tabela."
services: cosmos-db
documentationcenter: php
author: mimig1
manager: jhubbard
editor: tysonn
ms.assetid: 1e57f371-6208-4753-b2a0-05db4aede8e3
ms.service: cosmos-db
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: php
ms.topic: article
ms.date: 11/03/2017
ms.author: mimig
ms.openlocfilehash: 5cfdfc814d1dfc15149d7b100ba1f6b68e90bb36
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2017
---
# <a name="how-to-use-azure-table-storage-from-php"></a>Como utilizar o Table storage do Azure do PHP
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-cosmos-db-langsoon-tip-include](../../includes/storage-table-cosmos-db-langsoon-tip-include.md)]

## <a name="overview"></a>Descrição geral
Este guia mostra como efetuar cenários comuns utilizando o serviço de Azure Table. Os exemplos são escritos no PHP e a utilização de [Azure SDK para PHP][download]. Os cenários abrangidos incluem **criar e eliminar uma tabela e inserir, eliminar e consultar entidades numa tabela**. Para obter mais informações sobre o serviço de tabelas do Azure, consulte o [passos](#next-steps) secção.

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-php-application"></a>Criar uma aplicação PHP
O único requisito para criar uma aplicação PHP que acede ao serviço de tabelas do Azure está a referenciar de classes no Azure SDK para PHP de dentro do seu código. Pode utilizar as ferramentas de desenvolvimento para criar a sua aplicação, incluindo o bloco de notas.

Neste guia, pode utiliza as funcionalidades do serviço de tabela que podem ser chamadas de dentro de uma aplicação PHP localmente, ou no código em execução dentro de uma função da web do Azure, a função de trabalho ou o Web site.

## <a name="get-the-azure-client-libraries"></a>Obtenha as bibliotecas de cliente do Azure
[!INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-access-the-table-service"></a>Configurar a sua aplicação para aceder ao serviço tabela
Para utilizar as APIs do serviço de tabelas do Azure, tem de:

1. Referenciar o ficheiro do Carregador automático utilizando o [require_once] [ require_once] declaração, e
2. Referência a quaisquer classes que poderá utilizar.

O exemplo seguinte mostra como inclui o Carregador automático ficheiros e a referência a **ServicesBuilder** classe.

> [!NOTE]
> Os exemplos deste artigo partem do princípio de que instalou as bibliotecas de cliente do PHP do Azure através do compositor. Se tiver instalado as bibliotecas manualmente, tem de referenciar o <code>WindowsAzure.php</code> Carregador automático ficheiros.
>
>

```php
require_once 'vendor/autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

Nos exemplos abaixo, o `require_once` instrução sempre é apresentada, mas apenas as classes de necessárias para o exemplo para executar são referenciadas.

## <a name="set-up-an-azure-storage-connection"></a>Configurar uma ligação de armazenamento do Azure
Ao instanciar um cliente do serviço de tabelas do Azure, primeiro tem de ter uma cadeia de ligação válido. O formato da cadeia de ligação de serviço tabela é:

Para aceder a um serviço em direto:

```php
DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]
```

Para aceder ao armazenamento emulador:

```php
UseDevelopmentStorage=true
```

Para criar qualquer cliente de serviço do Azure, tem de utilizar o **ServicesBuilder** classe. Pode:

* passar a cadeia de ligação direta ou
* Utilize o **CloudConfigurationManager (CCM)** para verificar a várias origens externas para a cadeia de ligação:
  * Por predefinição, este inclui suporte para uma origem externa - variáveis de ambientais
  * Pode adicionar novas origens ao alargar o **ConnectionStringSource** classe

Para os exemplos aqui descritos, a cadeia de ligação irá ser transmitida diretamente.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);
```

## <a name="create-a-table"></a>Criar uma tabela
A **TableRestProxy** objeto permite-lhe criar uma tabela com o **createTable** método. Ao criar uma tabela, pode definir o limite de tempo de serviço tabela. (Para obter mais informações sobre o limite de tempo de serviço tabela, consulte [tempos limite de definição para operações de serviço tabela][table-service-timeouts].)

```php
require_once 'vendor\autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create table REST proxy.
$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

try    {
    // Create table.
    $tableRestProxy->createTable("mytable");
}
catch(ServiceException $e){
    $code = $e->getCode();
    $error_message = $e->getMessage();
    // Handle exception based on error codes and messages.
    // Error codes and messages can be found here:
    // http://msdn.microsoft.com/library/azure/dd179438.aspx
}
```

Para informações sobre restrições de nomes de tabela, consulte [compreender o modelo de dados do serviço tabela][table-data-model].

## <a name="add-an-entity-to-a-table"></a>Adicionar uma entidade a uma tabela
Para adicionar uma entidade a uma tabela, crie um novo **entidade** de objeto e transmiti-lo para **TableRestProxy -> insertEntity**. Tenha em atenção que quando criar uma entidade, terá de especificar um `PartitionKey` e `RowKey`. Estes são os identificadores exclusivos para uma entidade e são valores que podem ser consultados muito mais rapidamente do que outras propriedades de entidade. O sistema utiliza `PartitionKey` para distribuir automaticamente o entidades da tabela através de vários nós de armazenamento. Entidades com o mesmo `PartitionKey` são armazenados no mesmo nó. (Efetuar operações em várias entidades armazenadas no mesmo nó melhor do que em entidades armazenadas em nós diferentes.) O `RowKey` é o ID exclusivo de uma entidade dentro de uma partição.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;

// Create table REST proxy.
$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

$entity = new Entity();
$entity->setPartitionKey("tasksSeattle");
$entity->setRowKey("1");
$entity->addProperty("Description", null, "Take out the trash.");
$entity->addProperty("DueDate",
                        EdmType::DATETIME,
                        new DateTime("2012-11-05T08:15:00-08:00"));
$entity->addProperty("Location", EdmType::STRING, "Home");

try{
    $tableRestProxy->insertEntity("mytable", $entity);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179438.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
}
```

Para obter informações sobre as propriedades de tabela e tipos, consulte [compreender o modelo de dados do serviço tabela][table-data-model].

O **TableRestProxy** classe fornece dois métodos alternativos para inserir entidades: **insertOrMergeEntity** e **insertOrReplaceEntity**. Para utilizar estes métodos, crie um novo **entidade** e transmita-o como um parâmetro para cada método. Cada método de inserir a entidade se não existir. Se a entidade já existir, **insertOrMergeEntity** atualiza os valores de propriedade se as propriedades já existem e adiciona novas propriedades se não existir, enquanto **insertOrReplaceEntity** completamente substitui uma entidade existente. O exemplo seguinte mostra como utilizar **insertOrMergeEntity**. Se a entidade com `PartitionKey` "tasksSeattle" e `RowKey` "1" ainda não existir, será inserido. No entanto, se anteriormente tiver sido introduzida (conforme ilustrado no exemplo acima), o `DueDate` propriedade será atualizada e o `Status` propriedade será adicionada. O `Description` e `Location` propriedades também são atualizadas, mas com valores que efetivamente deixe-os inalterado. Se estas duas propriedades última não foram adicionadas, conforme mostrado no exemplo, mas existiam na entidade de destino, os respetivos valores existentes permanecerá inalterados.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;

// Create table REST proxy.
$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

//Create new entity.
$entity = new Entity();

// PartitionKey and RowKey are required.
$entity->setPartitionKey("tasksSeattle");
$entity->setRowKey("1");

// If entity exists, existing properties are updated with new values and
// new properties are added. Missing properties are unchanged.
$entity->addProperty("Description", null, "Take out the trash.");
$entity->addProperty("DueDate", EdmType::DATETIME, new DateTime()); // Modified the DueDate field.
$entity->addProperty("Location", EdmType::STRING, "Home");
$entity->addProperty("Status", EdmType::STRING, "Complete"); // Added Status field.

try    {
    // Calling insertOrReplaceEntity, instead of insertOrMergeEntity as shown,
    // would simply replace the entity with PartitionKey "tasksSeattle" and RowKey "1".
    $tableRestProxy->insertOrMergeEntity("mytable", $entity);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179438.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="retrieve-a-single-entity"></a>Obter uma única entidade
O **TableRestProxy -> getEntity** método permite-lhe obter uma entidade única através de consultas para o respetivo `PartitionKey` e `RowKey`. No exemplo abaixo, a chave de partição `tasksSeattle` e a chave de linha `1` são transmitidos para o **getEntity** método.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create table REST proxy.
$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

try    {
    $result = $tableRestProxy->getEntity("mytable", "tasksSeattle", 1);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179438.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$entity = $result->getEntity();

echo $entity->getPartitionKey().":".$entity->getRowKey();
```

## <a name="retrieve-all-entities-in-a-partition"></a>Obter todas as entidades numa partição
Consultas de entidade são construídas utilizando filtros (para obter mais informações, consulte [consultar tabelas e entidades][filters]). Para obter todas as entidades numa partição, utilize o filtro "PartitionKey eq *partition_name*". O exemplo seguinte mostra como obter todas as entidades no `tasksSeattle` partição transferindo um filtro para o **queryEntities** método.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create table REST proxy.
$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

$filter = "PartitionKey eq 'tasksSeattle'";

try    {
    $result = $tableRestProxy->queryEntities("mytable", $filter);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179438.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$entities = $result->getEntities();

foreach($entities as $entity){
    echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
}
```

## <a name="retrieve-a-subset-of-entities-in-a-partition"></a>Obter um subconjunto de entidades numa partição
O mesmo padrão utilizado no exemplo anterior, pode ser utilizado para obter qualquer subconjunto de entidades numa partição. O subconjunto de entidades é possível obter são determinados pelo filtro de utilizar (para obter mais informações, consulte [consultar tabelas e entidades][filters]). O exemplo seguinte mostra como utilizar um filtro para obter todas as entidades com um específico `Location` e um `DueDate` menor do que uma data especificada.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create table REST proxy.
$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

$filter = "Location eq 'Office' and DueDate lt '2012-11-5'";

try    {
    $result = $tableRestProxy->queryEntities("mytable", $filter);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179438.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$entities = $result->getEntities();

foreach($entities as $entity){
    echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
}
```

## <a name="retrieve-a-subset-of-entity-properties"></a>Obter um subconjunto de propriedades de entidade
Uma consulta pode obter um subconjunto de propriedades de entidade. Esta técnica, denominada *projecção*, reduz a largura de banda e pode melhorar o desempenho de consulta, especialmente para entidades grandes. Para especificar uma propriedade a recuperar, transmitir o nome da propriedade para o **consulta -> addSelectField** método. Pode chamar este método várias vezes para adicionar propriedades. Depois de executar **TableRestProxy -> queryEntities**, as entidades devolvidas só terão das propriedades selecionadas. (Se pretender regressar um subconjunto de entidades de tabela, utilize um filtro conforme mostrado nas consultas acima.)

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Table\Models\QueryEntitiesOptions;

// Create table REST proxy.
$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

$options = new QueryEntitiesOptions();
$options->addSelectField("Description");

try    {
    $result = $tableRestProxy->queryEntities("mytable", $options);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179438.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

// All entities in the table are returned, regardless of whether
// they have the Description field.
// To limit the results returned, use a filter.
$entities = $result->getEntities();

foreach($entities as $entity){
    $description = $entity->getProperty("Description")->getValue();
    echo $description."<br />";
}
```

## <a name="update-an-entity"></a>Atualizar uma entidade
Uma entidade existente pode ser atualizada utilizando o **entidade -> setProperty** e **entidade -> addProperty** métodos na entidade e, em seguida, chamar **TableRestProxy -> updateEntity**. O exemplo seguinte obtém uma entidade, modifica uma propriedade, remove outra propriedade e adiciona uma nova propriedade. Tenha em atenção que pode remover uma propriedade, definindo o valor como **nulo**.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;

// Create table REST proxy.
$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

$result = $tableRestProxy->getEntity("mytable", "tasksSeattle", 1);

$entity = $result->getEntity();

$entity->setPropertyValue("DueDate", new DateTime()); //Modified DueDate.

$entity->setPropertyValue("Location", null); //Removed Location.

$entity->addProperty("Status", EdmType::STRING, "In progress"); //Added Status.

try    {
    $tableRestProxy->updateEntity("mytable", $entity);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179438.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="delete-an-entity"></a>Eliminar uma entidade
Para eliminar uma entidade, transmitir o nome da tabela e a entidade `PartitionKey` e `RowKey` para o **TableRestProxy -> deleteEntity** método.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create table REST proxy.
$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

try    {
    // Delete entity.
    $tableRestProxy->deleteEntity("mytable", "tasksSeattle", "2");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179438.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

Tenha em atenção para verificações de simultaneidade, pode definir Etag de uma entidade eliminada utilizando o **DeleteEntityOptions -> setEtag** método e transmitir o **DeleteEntityOptions** object **deleteEntity** como um parâmetro quarto.

## <a name="batch-table-operations"></a>Operações de tabela do batch
O **TableRestProxy -> batch** método permite-lhe executar várias operações num único pedido. O padrão aqui envolve adicionar operações a **BatchRequest** objeto e, em seguida, transmitir o **BatchRequest** de objeto para o **TableRestProxy -> batch** método. Para adicionar uma operação para um **BatchRequest** objeto, pode chamar qualquer um dos seguintes métodos várias vezes:

* **addInsertEntity** (adiciona uma operação insertEntity)
* **addUpdateEntity** (adiciona uma operação updateEntity)
* **addMergeEntity** (adiciona uma operação mergeEntity)
* **addInsertOrReplaceEntity** (adiciona uma operação insertOrReplaceEntity)
* **addInsertOrMergeEntity** (adiciona uma operação insertOrMergeEntity)
* **addDeleteEntity** (adiciona uma operação deleteEntity)

O exemplo seguinte mostra como executar **insertEntity** e **deleteEntity** operações num único pedido:

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;
use MicrosoftAzure\Storage\Table\Models\BatchOperations;

    // Create table REST proxy.
$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

// Create list of batch operation.
$operations = new BatchOperations();

$entity1 = new Entity();
$entity1->setPartitionKey("tasksSeattle");
$entity1->setRowKey("2");
$entity1->addProperty("Description", null, "Clean roof gutters.");
$entity1->addProperty("DueDate",
                        EdmType::DATETIME,
                        new DateTime("2012-11-05T08:15:00-08:00"));
$entity1->addProperty("Location", EdmType::STRING, "Home");

// Add operation to list of batch operations.
$operations->addInsertEntity("mytable", $entity1);

// Add operation to list of batch operations.
$operations->addDeleteEntity("mytable", "tasksSeattle", "1");

try    {
    $tableRestProxy->batch($operations);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179438.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

Para obter mais informações sobre a criação de batches de operações da tabela, consulte [efetuar transações de grupo de entidade][entity-group-transactions].

## <a name="delete-a-table"></a>Eliminar uma tabela
Por fim, para eliminar uma tabela, transmitir o nome de tabela para o **TableRestProxy -> deleteTable** método.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create table REST proxy.
$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

try    {
    // Delete table.
    $tableRestProxy->deleteTable("mytable");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179438.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="next-steps"></a>Passos seguintes
Agora que aprendeu as noções básicas do serviço tabela do Azure, siga estas ligações para saber mais sobre as tarefas de armazenamento mais complexas.

* O [Explorador de Armazenamento do Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) é uma aplicação autónoma e gratuita da Microsoft, que lhe permite trabalhar visualmente com dados do Armazenamento do Azure no Windows, macOS e Linux.

* [Centro para programadores do PHP](/develop/php/).

[download]: http://go.microsoft.com/fwlink/?LinkID=252473
[require_once]: http://php.net/require_once
[table-service-timeouts]: http://msdn.microsoft.com/library/azure/dd894042.aspx

[table-data-model]: http://msdn.microsoft.com/library/azure/dd179338.aspx
[filters]: http://msdn.microsoft.com/library/azure/dd894031.aspx
[entity-group-transactions]: http://msdn.microsoft.com/library/azure/dd894038.aspx
