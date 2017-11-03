---
title: "Introdução ao table storage do Azure e o Visual Studio ligado serviços (ASP.NET) | Microsoft Docs"
description: "Como começar a utilizar o table storage do Azure num projeto ASP.NET no Visual Studio depois de se ligar a uma conta de armazenamento utilizando o Visual Studio ligado Services"
services: storage
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: af81a326-18f4-4449-bc0d-e96fba27c1f8
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/21/2016
ms.author: kraigb
ms.openlocfilehash: 32a57e77bf6fe3cff88b9d6772ede9e6669ec75f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-table-storage-and-visual-studio-connected-services-aspnet"></a>Introdução ao table storage do Azure e o Visual Studio ligado serviços (ASP.NET)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Descrição geral

Table storage do Azure permite-lhe armazenar grandes quantidades de dados estruturados. O serviço é um arquivo de dados NoSQL que aceita chamadas autenticadas de dentro e fora da nuvem do Azure. As tabelas do Azure são ideais para armazenar dados estruturados não relacionais.

Este tutorial mostra como escrever código ASP.NET para alguns cenários comuns utilizando entidades de armazenamento de tabela do Azure. Estes cenários incluem a criação de uma tabela, adicionar, consultar e eliminar as entidades da tabela. 

##<a name="prerequisites"></a>Pré-requisitos

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Conta de armazenamento do Azure](../storage/common/storage-create-storage-account.md#create-a-storage-account)

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/vs-storage-aspnet-getting-started-create-azure-account.md)]

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

### <a name="create-an-mvc-controller"></a>Criar um controlador MVC 

1. No **Explorador de soluções**, faça duplo clique **controladores**e, no menu de contexto, selecione **adicionar -> controlador**.

    ![Adicionar um controlador a uma aplicação ASP.NET MVC](./media/vs-storage-aspnet-getting-started-tables/add-controller-menu.png)

1. No **adicionar andaime** caixa de diálogo, selecione **controlador MVC 5 – vazio**e selecione **adicionar**.

    ![Especifique o tipo de controlador MVC](./media/vs-storage-aspnet-getting-started-tables/add-controller.png)

1. No **Adicionar controlador** caixa de diálogo, o nome do controlador *TablesController*e selecione **adicionar**.

    ![Nome do controlador MVC](./media/vs-storage-aspnet-getting-started-tables/add-controller-name.png)

1. Adicione o seguinte *utilizando* diretivas para a `TablesController.cs` ficheiro:

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Table;
    ```

### <a name="create-a-model-class"></a>Criar uma classe de modelo

Muitas dos exemplos neste artigo que utilize um **TableEntity**-derivados de classe designado **CustomerEntity**. Os seguintes passos guiá-lo através de declarar esta classe como uma classe de modelo:

1. No **Explorador de soluções**, faça duplo clique **modelos**e, no menu de contexto, selecione **adicionar -> classe**.

1. No **Adicionar Novo Item** caixa de diálogo, nome de classe, **CustomerEntity**.

1. Abra o `CustomerEntity.cs` de ficheiros e adicione o seguinte **utilizando** diretiva:

    ```csharp
    using Microsoft.WindowsAzure.Storage.Table;
    ```

1. Altere a classe para que, quando terminar, a classe está declarada como no seguinte código. A classe declara uma classe de entidade chamada **CustomerEntity** que utiliza o nome próprio do cliente como a chave da fila e o apelido como a chave de partição.

    ```csharp
    public class CustomerEntity : TableEntity
    {
        public CustomerEntity(string lastName, string firstName)
        {
            this.PartitionKey = lastName;
            this.RowKey = firstName;
        }

        public CustomerEntity() { }

        public string Email { get; set; }
    }
    ```

## <a name="create-a-table"></a>Criar uma tabela

Os passos seguintes mostram como criar uma tabela:

> [!NOTE]
> 
> Esta secção assume que concluiu os passos em [configurar o ambiente de desenvolvimento](#set-up-the-development-environment). 

1. Abra o ficheiro `TablesController.cs`.

1. Adicione um método denominado **CreateTable** que devolve um **ActionResult**.

    ```csharp
    public ActionResult CreateTable()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Dentro do **CreateTable** método, obter um **CloudStorageAccount** objeto que representa as informações da conta de armazenamento. Utilize o seguinte código para obter a cadeia de ligação de armazenamento e informações de conta de armazenamento da configuração do serviço do Azure: (alteração  *&lt;nome da conta de armazenamento >* para o nome da conta do storage do Azure que está a aceder.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Obter um **CloudTableClient** objeto representa um cliente do serviço tabela.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Obter um **CloudTable** objeto que representa uma referência para o nome da tabela pretendido. O **CloudTableClient.GetTableReference** método não faz um pedido de armazenamento de tabelas. A referência é devolvida se ou não a tabela existe. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Chamar o **CloudTable.CreateIfNotExists** método para criar a tabela se ainda não existir. O **CloudTable.CreateIfNotExists** método devolve **verdadeiro** se a tabela não existe e é criada com êxito. Caso contrário, **falso** é devolvido.    

    ```csharp
    ViewBag.Success = table.CreateIfNotExists();
    ```

1. Atualização do **ViewBag** com o nome da tabela.

    ```csharp
    ViewBag.TableName = table.Name;
    ```

1. No **Explorador de soluções**, expanda o **vistas** pasta, faça duplo clique **tabelas**e no menu de contexto, selecione **adicionar -> vista**.

1. No **Adicionar vista** caixa de diálogo, introduza **CreateTable** para o nome da vista e selecione **adicionar**.

1. Abra `CreateTable.cshtml`e modificá-lo de modo a que se parece com o seguinte fragmento de código:

    ```csharp
    @{
        ViewBag.Title = "Create Table";
    }
    
    <h2>Create Table results</h2>

    Creation of @ViewBag.TableName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. No **Explorador de soluções**, expanda o **vistas -> partilhados** pasta e abra `_Layout.cshtml`.

1. Após a última **Html.ActionLink**, adicione o seguinte **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Create table", "CreateTable", "Tables")</li>
    ```

1. Executar a aplicação e selecione **criar tabela** para ver os resultados semelhantes à captura de ecrã seguinte:
  
    ![Criar tabela](./media/vs-storage-aspnet-getting-started-tables/create-table-results.png)

    Tal como mencionado anteriormente, o **CloudTable.CreateIfNotExists** método devolve **verdadeiro** apenas quando a tabela não existe e é criada. Por conseguinte, se executar a aplicação quando a tabela existe, o método devolve **falso**. Para executar a aplicação várias vezes, tem de eliminar a tabela antes de executar novamente a aplicação. Eliminar a tabela pode ser efetuado através de **CloudTable.Delete** método. Também pode eliminar a tabela utilizando o [portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040) ou [Explorador de armazenamento do Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md).  

## <a name="add-an-entity-to-a-table"></a>Adicionar uma entidade a uma tabela

*Entidades* mapa c\# objetos através de uma classe personalizada derivam de **TableEntity**. Para adicionar uma entidade a uma tabela, crie uma classe que define as propriedades de entidade. Nesta secção, irá ver como definir uma classe de entidade que utiliza o nome próprio do cliente como a chave da fila e o apelido como a chave de partição. Em conjunto, a chave da fila e a partição da entidade identificam de forma exclusiva a entidade na tabela. As entidades com a mesma chave de partição podem ser consultadas mais rapidamente do que as entidades com chaves de partição diferentes, mas a utilização de várias chaves de partição permite uma maior escalabilidade de operações simultâneas. Para qualquer propriedade que deva ser armazenada no serviço tabela, a propriedade tem de ser uma propriedade pública de um tipo suportado que expõe os definir e obter os valores.
A classe de entidade *tem* declarar um construtor sem parâmetros público.

> [!NOTE]
> 
> Esta secção assume que concluiu os passos em [configurar o ambiente de desenvolvimento](#set-up-the-development-environment).

1. Abra o ficheiro `TablesController.cs`.

1. Adicionar a diretiva seguinte para que o código de `TablesController.cs` podem aceder a ficheiros o **CustomerEntity** classe:

    ```csharp
    using StorageAspnet.Models;
    ```

1. Adicione um método denominado **AddEntity** que devolve um **ActionResult**.

    ```csharp
    public ActionResult AddEntity()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Dentro do **AddEntity** método, obter um **CloudStorageAccount** objeto que representa as informações da conta de armazenamento. Utilize o seguinte código para obter a cadeia de ligação de armazenamento e informações de conta de armazenamento da configuração do serviço do Azure: (alteração  *&lt;nome da conta de armazenamento >* para o nome da conta do storage do Azure que está a aceder.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Obter um **CloudTableClient** objeto representa um cliente do serviço tabela.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Obter um **CloudTable** objeto que representa uma referência para a tabela à qual vai adicionar nova entidade. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Instanciar e inicializar o **CustomerEntity** classe.

    ```csharp
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    ```

1. Criar um **TableOperation** objeto insere a entidade de cliente.

    ```csharp
    TableOperation insertOperation = TableOperation.Insert(customer1);
    ```

1. Executar a operação de inserção ao chamar o **Cloudtable** método. Pode verificar o resultado da operação inspecionando a **TableResult.HttpStatusCode** propriedade. Um código de estado de 2xx indica que a ação solicitada pelo cliente foi processada com êxito. Por exemplo, com êxito inserções novas entidades resultados num código de estado HTTP de 204, que significa que a operação foi processou com êxito e o servidor não devolveu qualquer conteúdo.

    ```csharp
    TableResult result = table.Execute(insertOperation);
    ```

1. Atualização do **ViewBag** com o nome da tabela e os resultados da operação Inserir.

    ```csharp
    ViewBag.TableName = table.Name;
    ViewBag.Result = result.HttpStatusCode;
    ```

1. No **Explorador de soluções**, expanda o **vistas** pasta, faça duplo clique **tabelas**e no menu de contexto, selecione **adicionar -> vista**.

1. No **Adicionar vista** caixa de diálogo, introduza **AddEntity** para o nome da vista e selecione **adicionar**.

1. Abra `AddEntity.cshtml`e modificá-lo de modo a que se parece com o seguinte fragmento de código:

    ```csharp
    @{
        ViewBag.Title = "Add entity";
    }
    
    <h2>Add entity results</h2>

    Insert of entity into @ViewBag.TableName @(ViewBag.Result == 204 ? "succeeded" : "failed")
    ```
1. No **Explorador de soluções**, expanda o **vistas -> partilhados** pasta e abra `_Layout.cshtml`.

1. Após a última **Html.ActionLink**, adicione o seguinte **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Add entity", "AddEntity", "Tables")</li>
    ```

1. Executar a aplicação e selecione **adicionar entidade** para ver os resultados semelhantes à captura de ecrã seguinte:
  
    ![Adicionar entidade](./media/vs-storage-aspnet-getting-started-tables/add-entity-results.png)

    Pode verificar se a entidade foi adicionada ao seguir os passos na secção, [obter uma única entidade](#get-a-single-entity). Também pode utilizar o [Explorador de armazenamento do Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) para ver todas as entidades para as suas tabelas.

## <a name="add-a-batch-of-entities-to-a-table"></a>Adicionar um lote de entidades a uma tabela

Para além de ser capaz de [adicionar uma entidade a uma tabela de uma altura](#add-an-entity-to-a-table), também pode adicionar entidades no batch. A adição de entidades no batch reduz o número de ida e volta entre o seu código e o serviço de tabela do Azure. Os passos seguintes mostram como adicionar operação de inserção de várias entidades a uma tabela com um único:

> [!NOTE]
> 
> Esta secção assume que concluiu os passos em [configurar o ambiente de desenvolvimento](#set-up-the-development-environment).

1. Abra o ficheiro `TablesController.cs`.

1. Adicione um método denominado **AddEntities** que devolve um **ActionResult**.

    ```csharp
    public ActionResult AddEntities()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Dentro do **AddEntities** método, obter um **CloudStorageAccount** objeto que representa as informações da conta de armazenamento. Utilize o seguinte código para obter a cadeia de ligação de armazenamento e informações de conta de armazenamento da configuração do serviço do Azure: (alteração  *&lt;nome da conta de armazenamento >* para o nome da conta do storage do Azure que está a aceder.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Obter um **CloudTableClient** objeto representa um cliente do serviço tabela.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Obter um **CloudTable** objeto que representa uma referência para a tabela à qual vai adicionar novas entidades. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Instanciar alguns objetos de cliente com base no **CustomerEntity** classe apresentado na secção, de modelo [adicionar uma entidade a uma tabela](#add-an-entity-to-a-table).

    ```csharp
    CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
    customer1.Email = "Jeff@contoso.com";

    CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
    customer2.Email = "Ben@contoso.com";
    ```

1. Obter um **TableBatchOperation** objeto.

    ```csharp
    TableBatchOperation batchOperation = new TableBatchOperation();
    ```

1. Adicione as entidades para o objeto de operação de inserção de lote.

    ```csharp
    batchOperation.Insert(customer1);
    batchOperation.Insert(customer2);
    ```

1. Executar a operação de inserção de lote ao chamar o **CloudTable.ExecuteBatch** método.   

    ```csharp
    IList<TableResult> results = table.ExecuteBatch(batchOperation);
    ```

1. O **CloudTable.ExecuteBatch** método devolve uma lista de **TableResult** objetos onde cada **TableResult** objeto pode ser examinado para determinar o êxito ou falha de cada operação individual. Neste exemplo, a lista de passar para uma vista e permita que a vista apresenta os resultados de cada operação. 
 
    ```csharp
    return View(results);
    ```

1. No **Explorador de soluções**, expanda o **vistas** pasta, faça duplo clique **tabelas**e no menu de contexto, selecione **adicionar -> vista**.

1. No **Adicionar vista** caixa de diálogo, introduza **AddEntities** para o nome da vista e selecione **adicionar**.

1. Abra `AddEntities.cshtml`e modificá-lo, de modo a que tenha um aspeto semelhante ao seguinte.

    ```csharp
    @model IEnumerable<Microsoft.WindowsAzure.Storage.Table.TableResult>
    @{
        ViewBag.Title = "AddEntities";
    }
    
    <h2>Add-entities results</h2>
    
    <table border="1">
        <tr>
            <th>First name</th>
            <th>Last name</th>
            <th>HTTP result</th>
        </tr>
        @foreach (var result in Model)
        {
        <tr>
            <td>@((result.Result as StorageAspnet.Models.CustomerEntity).RowKey)</td>
            <td>@((result.Result as StorageAspnet.Models.CustomerEntity).PartitionKey)</td>
            <td>@result.HttpStatusCode</td>
        </tr>
        }
    </table>
    ```

1. No **Explorador de soluções**, expanda o **vistas -> partilhados** pasta e abra `_Layout.cshtml`.

1. Após a última **Html.ActionLink**, adicione o seguinte **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Add entities", "AddEntities", "Tables")</li>
    ```

1. Executar a aplicação e selecione **adicionar entidades** para ver os resultados semelhantes à captura de ecrã seguinte:
  
    ![Adicionar entidades](./media/vs-storage-aspnet-getting-started-tables/add-entities-results.png)

    Pode verificar se a entidade foi adicionada ao seguir os passos na secção, [obter uma única entidade](#get-a-single-entity). Também pode utilizar o [Explorador de armazenamento do Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) para ver todas as entidades para as suas tabelas.

## <a name="get-a-single-entity"></a>Obter uma única entidade

Esta secção ilustra como obter uma única entidade a partir de uma tabela utilizando a chave de linha da entidade e a chave de partição. 

> [!NOTE]
> 
> Esta secção assume que concluiu os passos em [configurar o ambiente de desenvolvimento](#set-up-the-development-environment)e utiliza dados dos [adicionar um lote de entidades a uma tabela](#add-a-batch-of-entities-to-a-table). 

1. Abra o ficheiro `TablesController.cs`.

1. Adicione um método denominado **GetSingle** que devolve um **ActionResult**.

    ```csharp
    public ActionResult GetSingle()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Dentro do **GetSingle** método, obter um **CloudStorageAccount** objeto que representa as informações da conta de armazenamento. Utilize o seguinte código para obter a cadeia de ligação de armazenamento e informações de conta de armazenamento da configuração do serviço do Azure: (alteração  *&lt;nome da conta de armazenamento >* para o nome da conta do storage do Azure que está a aceder.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Obter um **CloudTableClient** objeto representa um cliente do serviço tabela.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Obter um **CloudTable** objeto que representa uma referência para a tabela a partir da qual estão a obter a entidade. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Criar um objeto de operação de obtenção pega num objeto de entidade derivado **TableEntity**. O primeiro parâmetro é o *partitionKey*, e o segundo parâmetro é o *rowKey*. Utilizar o **CustomerEntity** classe e os dados apresentados na secção [adicionar um lote de entidades a uma tabela](#add-a-batch-of-entities-to-a-table), consulta a tabela para o seguinte fragmento de código um **CustomerEntity**entidade com uma *partitionKey* valor de "Santos" e um *rowKey* valor de "Bernardo":

    ```csharp
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");
    ```

1. Execute a operação de obter.   

    ```csharp
    TableResult result = table.Execute(retrieveOperation);
    ```

1. O resultado de passar para a vista para apresentar.

    ```csharp
    return View(result);
    ```

1. No **Explorador de soluções**, expanda o **vistas** pasta, faça duplo clique **tabelas**e no menu de contexto, selecione **adicionar -> vista**.

1. No **Adicionar vista** caixa de diálogo, introduza **GetSingle** para o nome da vista e selecione **adicionar**.

1. Abra `GetSingle.cshtml`e modificá-lo de modo a que se parece com o seguinte fragmento de código:

    ```csharp
    @model Microsoft.WindowsAzure.Storage.Table.TableResult
    @{
        ViewBag.Title = "GetSingle";
    }
    
    <h2>Get Single results</h2>
    
    <table border="1">
        <tr>
            <th>HTTP result</th>
            <th>First name</th>
            <th>Last name</th>
            <th>Email</th>
        </tr>
        <tr>
            <td>@Model.HttpStatusCode</td>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).RowKey)</td>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).PartitionKey)</td>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).Email)</td>
        </tr>
    </table>
    ```

1. No **Explorador de soluções**, expanda o **vistas -> partilhados** pasta e abra `_Layout.cshtml`.

1. Após a última **Html.ActionLink**, adicione o seguinte **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Get single", "GetSingle", "Tables")</li>
    ```

1. Executar a aplicação e selecione **obter único** para ver os resultados semelhantes à captura de ecrã seguinte:
  
    ![Obter único](./media/vs-storage-aspnet-getting-started-tables/get-single-results.png)

## <a name="get-all-entities-in-a-partition"></a>Obter todas as entidades numa partição

Tal como mencionado na secção, [adicionar uma entidade a uma tabela](#add-an-entity-to-a-table), a combinação de uma partição e uma chave de linha identificar exclusivamente uma entidade numa tabela. Entidades com a mesma chave de partição podem ser consultadas mais rapidamente do que as entidades com chaves de partição diferentes. Esta secção ilustra como consultar uma tabela para todas as entidades de uma partição especificada.  

> [!NOTE]
> 
> Esta secção assume que concluiu os passos em [configurar o ambiente de desenvolvimento](#set-up-the-development-environment)e utiliza dados dos [adicionar um lote de entidades a uma tabela](#add-a-batch-of-entities-to-a-table). 

1. Abra o ficheiro `TablesController.cs`.

1. Adicione um método denominado **GetPartition** que devolve um **ActionResult**.

    ```csharp
    public ActionResult GetPartition()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Dentro do **GetPartition** método, obter um **CloudStorageAccount** objeto que representa as informações da conta de armazenamento. Utilize o seguinte código para obter a cadeia de ligação de armazenamento e informações de conta de armazenamento da configuração do serviço do Azure: (alteração  *&lt;nome da conta de armazenamento >* para o nome da conta do storage do Azure que está a aceder.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Obter um **CloudTableClient** objeto representa um cliente do serviço tabela.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Obter um **CloudTable** objeto que representa uma referência para a tabela a partir da qual estão a obter as entidades. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Instanciar um **TableQuery** objeto especificando a consulta no **onde** cláusula. Utilizar o **CustomerEntity** classe e os dados apresentados na secção [adicionar um lote de entidades a uma tabela](#add-a-batch-of-entities-to-a-table), o seguinte fragmento de código consulta a tabela de uma todas as entidades onde o  **PartitionKey** (apelido do cliente) tem um valor de "Santos":

    ```csharp
    TableQuery<CustomerEntity> query = 
        new TableQuery<CustomerEntity>()
        .Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));
    ```

1. Dentro de um ciclo, chame o **CloudTable.ExecuteQuerySegmented** método transmitir o objeto da consulta instanciado no passo anterior.  O **CloudTable.ExecuteQuerySegmented** método devolve um **TableContinuationToken** objeto que - quando **nulo** -indica que existem não existem mais entidades obter. Dentro do ciclo, utilize outro ciclo para iterar as entidades devolvidas. No exemplo de código seguinte, a cada entidade devolvida é adicionada a uma lista. Depois de terminar o ciclo, a lista é passado para uma vista para apresentar: 

    ```csharp
    List<CustomerEntity> customers = new List<CustomerEntity>();
    TableContinuationToken token = null;
    do
    {
        TableQuerySegment<CustomerEntity> resultSegment = table.ExecuteQuerySegmented(query, token);
        token = resultSegment.ContinuationToken;

        foreach (CustomerEntity customer in resultSegment.Results)
        {
            customers.Add(customer);
        }
    } while (token != null);

    return View(customers);
    ```

1. No **Explorador de soluções**, expanda o **vistas** pasta, faça duplo clique **tabelas**e no menu de contexto, selecione **adicionar -> vista**.

1. No **Adicionar vista** caixa de diálogo, introduza **GetPartition** para o nome da vista e selecione **adicionar**.

1. Abra `GetPartition.cshtml`e modificá-lo de modo a que se parece com o seguinte fragmento de código:

    ```csharp
    @model IEnumerable<StorageAspnet.Models.CustomerEntity>
    @{
        ViewBag.Title = "GetPartition";
    }
    
    <h2>Get Partition results</h2>
    
    <table border="1">
        <tr>
            <th>First name</th>
            <th>Last name</th>
            <th>Email</th>
        </tr>
        @foreach (var customer in Model)
        {
        <tr>
            <td>@(customer.RowKey)</td>
            <td>@(customer.PartitionKey)</td>
            <td>@(customer.Email)</td>
        </tr>
        }
    </table>
    ```

1. No **Explorador de soluções**, expanda o **vistas -> partilhados** pasta e abra `_Layout.cshtml`.

1. Após a última **Html.ActionLink**, adicione o seguinte **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Get partition", "GetPartition", "Tables")</li>
    ```

1. Executar a aplicação e selecione **obter partição** para ver os resultados semelhantes à captura de ecrã seguinte:
  
    ![Obter a partição](./media/vs-storage-aspnet-getting-started-tables/get-partition-results.png)

## <a name="delete-an-entity"></a>Eliminar uma entidade

Esta secção ilustra como eliminar uma entidade a partir de uma tabela.

> [!NOTE]
> 
> Esta secção assume que concluiu os passos em [configurar o ambiente de desenvolvimento](#set-up-the-development-environment)e utiliza dados dos [adicionar um lote de entidades a uma tabela](#add-a-batch-of-entities-to-a-table). 

1. Abra o ficheiro `TablesController.cs`.

1. Adicione um método denominado **DeleteEntity** que devolve um **ActionResult**.

    ```csharp
    public ActionResult DeleteEntity()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Dentro do **DeleteEntity** método, obter um **CloudStorageAccount** objeto que representa as informações da conta de armazenamento. Utilize o seguinte código para obter a cadeia de ligação de armazenamento e informações de conta de armazenamento da configuração do serviço do Azure: (alteração  *&lt;nome da conta de armazenamento >* para o nome da conta do storage do Azure que está a aceder.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Obter um **CloudTableClient** objeto representa um cliente do serviço tabela.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Obter um **CloudTable** objeto que representa uma referência para a tabela a partir do qual está a eliminar a entidade. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Criar um objeto de operação de eliminação demora um objeto de entidade derivado **TableEntity**. Neste caso, utilizamos o **CustomerEntity** classe e os dados apresentados na secção [adicionar um lote de entidades a uma tabela](#add-a-batch-of-entities-to-a-table). A entidade **ETag** tem de ser definido para um valor válido.  

    ```csharp
    TableOperation deleteOperation = 
        TableOperation.Delete(new CustomerEntity("Smith", "Ben") { ETag = "*" } );
    ```

1. Execute a operação de eliminação.   

    ```csharp
    TableResult result = table.Execute(deleteOperation);
    ```

1. O resultado de passar para a vista para apresentar.

    ```csharp
    return View(result);
    ```

1. No **Explorador de soluções**, expanda o **vistas** pasta, faça duplo clique **tabelas**e no menu de contexto, selecione **adicionar -> vista**.

1. No **Adicionar vista** caixa de diálogo, introduza **DeleteEntity** para o nome da vista e selecione **adicionar**.

1. Abra `DeleteEntity.cshtml`e modificá-lo de modo a que se parece com o seguinte fragmento de código:

    ```csharp
    @model Microsoft.WindowsAzure.Storage.Table.TableResult
    @{
        ViewBag.Title = "DeleteEntity";
    }
    
    <h2>Delete Entity results</h2>
    
    <table border="1">
        <tr>
            <th>First name</th>
            <th>Last name</th>
            <th>HTTP result</th>
        </tr>
        <tr>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).RowKey)</td>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).PartitionKey)</td>
            <td>@Model.HttpStatusCode</td>
        </tr>
    </table>

    ```

1. No **Explorador de soluções**, expanda o **vistas -> partilhados** pasta e abra `_Layout.cshtml`.

1. Após a última **Html.ActionLink**, adicione o seguinte **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Delete entity", "DeleteEntity", "Tables")</li>
    ```

1. Executar a aplicação e selecione **eliminar entidade** para ver os resultados semelhantes à captura de ecrã seguinte:
  
    ![Obter único](./media/vs-storage-aspnet-getting-started-tables/delete-entity-results.png)

## <a name="next-steps"></a>Passos seguintes
Ver mais guias de funcionalidades para saber mais sobre as opções adicionais para armazenar dados no Azure.

  * [Introdução ao blob storage do Azure e o Visual Studio ligado serviços (ASP.NET)](../storage/vs-storage-aspnet-getting-started-blobs.md)
  * [Introdução ao armazenamento de filas do Azure e o Visual Studio ligado serviços (ASP.NET)](../storage/vs-storage-aspnet-getting-started-queues.md)
