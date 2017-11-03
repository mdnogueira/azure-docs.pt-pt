---
title: Saiba como proteger o acesso aos dados na base de dados do Azure Cosmos | Microsoft Docs
description: "Saiba mais sobre conceitos de controlo de acesso na BD do Cosmos do Azure, incluindo o mestre de chaves, chaves de só de leitura, os utilizadores e permissões."
services: cosmos-db
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 8641225d-e839-4ba6-a6fd-d6314ae3a51c
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/24/2017
ms.author: mimig
ms.openlocfilehash: 383e04f91eec2f465b381ce30f2d6d24c488b731
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="securing-access-to-azure-cosmos-db-data"></a>Proteger o acesso aos dados da base de dados do Azure Cosmos
Este artigo fornece uma descrição geral de proteger o acesso aos dados armazenados no [base de dados do Microsoft Azure Cosmos](https://azure.microsoft.com/services/cosmos-db/).

BD do Cosmos do Azure utiliza dois tipos de chaves para autenticar os utilizadores e fornecer acesso aos seus dados e recursos. 

|tipo de chave|Recursos|
|---|---|
|[Mestre de chaves](#master-keys) |Utilizado para recursos administrativos: contas, bases de dados, os utilizadores e permissões de base de dados|
|[Tokens de recursos](#resource-tokens)|Utilizado para recursos de aplicação: coleções, documentos, os anexos, procedimentos armazenados, acionadores e UDFs|

<a id="master-keys"></a>

## <a name="master-keys"></a>Mestre de chaves 

Chaves mestras fornecer acesso aos todos os administrativos recursos para a conta de base de dados. Mestre de chaves:  
- Fornece acesso a contas, bases de dados, os utilizadores e permissões. 
- Não pode ser utilizado para fornecer acesso granular a coleções e os documentos.
- São criados durante a criação de uma conta.
- Pode ser novamente gerada em qualquer altura.

Cada conta consiste em duas chaves mestras: uma chave primária e chave secundária. O objetivo das chaves duplos é para que possa voltar a gerar ou, reverta chaves, fornecer acesso contínuo à sua conta e a dados. 

Para além das duas chaves mestras para a conta de base de dados do Cosmos, existem duas chaves de só de leitura. Estas chaves como só de leitura permitem apenas operações de leitura na conta. As chaves só de leitura não fornecem acesso a recursos de permissões de leitura.

Servidor primário, secundário, só de leitura e chaves mestras de leitura e escrita podem ser obtidas e será gerada no portal do Azure. Para obter instruções, consulte [ver, copiar e voltar a gerar acesso chaves](manage-account.md#keys).

![Controlo de acesso (IAM) no portal do Azure - demonstrar a segurança de base de dados NoSQL](./media/secure-access-to-data/nosql-database-security-master-key-portal.png)

O processo de rodar a chave mestra é simple. Navegue até ao portal do Azure para obter a chave secundária, em seguida, substitua a chave primária com a chave secundária na sua aplicação, em seguida, roda para a chave primária no portal do Azure.

![Rotação de chave mestra no portal do Azure - demonstrar a segurança de base de dados NoSQL](./media/secure-access-to-data/nosql-database-security-master-key-rotate-workflow.png)

### <a name="code-sample-to-use-a-master-key"></a>Exemplo de código para utilizar uma chave mestra

O exemplo de código seguinte ilustra como utilizar um ponto final de conta de base de dados do Cosmos e a chave mestra para instanciar um DocumentClient e criar uma base de dados. 

```csharp
//Read the Azure Cosmos DB endpointUrl and authorization keys from config.
//These values are available from the Azure portal on the Azure Cosmos DB account blade under "Keys".
//NB > Keep these values in a safe and secure location. Together they provide Administrative access to your DocDB account.

private static readonly string endpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
private static readonly SecureString authorizationKey = ToSecureString(ConfigurationManager.AppSettings["AuthorizationKey"]);

client = new DocumentClient(new Uri(endpointUrl), authorizationKey);

// Create Database
Database database = await client.CreateDatabaseAsync(
    new Database
    {
        Id = databaseName
    });
```

<a id="resource-tokens"></a>

## <a name="resource-tokens"></a>Tokens de recursos

Tokens de recurso fornecem acesso aos recursos da aplicação dentro de uma base de dados. Tokens de recursos:
- Fornece acesso a coleções específicas, chaves de partição, documentos, os anexos, procedimentos armazenados, acionadores e UDFs.
- São criados quando um [utilizador](#users) é concedido [permissões](#permissions) a um recurso específico.
- São recriadas quando um recurso de permissão é atuou após a chamada de POST, GET ou PUT.
- Utilize um token de recurso de hash criado especificamente para o utilizador, o recurso e a permissão.
- São tempo vinculado com um período de validade personalizável. Timespan válido predefinido é uma hora. Duração do token, no entanto, pode ser especificada explicitamente, até um máximo de cinco horas.
- Forneça uma alternativa segura para fornecer a chave mestra. 
- Permitir que os clientes de leitura, escrita e eliminar recursos na conta de base de dados do Cosmos, de acordo com as permissões que lhes foi concedido.

Pode utilizar um token de recursos (através da criação de utilizadores de base de dados do Cosmos e permissões) que se pretende fornecer acesso a recursos na sua conta de base de dados do Cosmos para um cliente que não pode ser considerados fidedigno com a chave mestra.  

Tokens de recursos do cosmos DB fornecem uma alternativa segura que permite que os clientes de leitura, escrita e eliminação de recursos na sua conta de base de dados do Cosmos, de acordo com as permissões que tiver concedidas e sem necessidade de um principal ou a chave apenas de leitura.

Eis um padrão de conceção comuns na qual os tokens de recursos podem ser solicitados, gerados e distribuídos aos clientes:

1. Um serviço de camada média dimensão que é configurado para servir a uma aplicação móvel a partilha de fotografias do utilizador. 
2. O serviço de camada média dimensão que tiver a chave mestra da conta de base de dados do Cosmos.
3. A aplicação de fotografias está instalada em dispositivos móveis do utilizador final. 
4. No início de sessão, a aplicação de fotografias estabelece a identidade do utilizador com o serviço de camada média dimensão que. Este mecanismo de estabelecimento de identidade é puramente até a aplicação.
5. Assim que a identidade for estabelecida, o serviço de camada média dimensão que pedidos com base na identidade de permissões.
6. O serviço de camada média dimensão que envia um token de recursos novamente para a aplicação de telefone.
7. A aplicação de telefone pode continuar a utilizar o token de recurso para aceder diretamente aos recursos da base de dados do Cosmos com as permissões definidas pelo token de recurso e para o intervalo permitido pelo token de recurso. 
8. Quando o token de recurso expira, os pedidos subsequentes de recebem uma exceção não autorizado 401.  Neste momento, a aplicação de telefone restabeleça a identidade e pede um novo token de recurso.

    ![Fluxo de trabalho de tokens de recurso do Cosmos BD do Azure](./media/secure-access-to-data/resourcekeyworkflow.png)

Gestão e de geração de token de recurso é processado pelo bibliotecas de cliente nativas do Cosmos DB; No entanto, se utilizar o resto deve construir os cabeçalhos de pedido/autenticação. Para obter mais informações sobre a criação de cabeçalhos de autenticação para REST, consulte [controlo de acesso nos recursos de BD do Cosmos](https://docs.microsoft.com/rest/api/documentdb/access-control-on-documentdb-resources) ou [código de origem para os nossos SDKs](https://github.com/Azure/azure-documentdb-node/blob/master/source/lib/auth.js).

Para obter um exemplo de um serviço de camada média utilizado para gerar ou Mediador de tokens de recursos, consulte o [ResourceTokenBroker aplicação](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin/UserItems/ResourceTokenBroker/ResourceTokenBroker/Controllers).

<a id="users"></a>

## <a name="users"></a>Utilizadores
Os utilizadores de base de dados do cosmos estão associados uma base de dados de base de dados do Cosmos.  Cada base de dados pode conter zero ou mais utilizadores de base de dados do Cosmos.  O exemplo de código seguinte mostra como criar um recurso de utilizador de base de dados do Cosmos.

```csharp
//Create a user.
User docUser = new User
{
    Id = "mobileuser"
};

docUser = await client.CreateUserAsync(UriFactory.CreateDatabaseUri("db"), docUser);
```

> [!NOTE]
> Cada utilizador de base de dados do Cosmos tem uma propriedade de PermissionsLink que pode ser utilizada para obter a lista de [permissões](#permissions) associados ao utilizador.
> 
> 

<a id="permissions"></a>

## <a name="permissions"></a>Permissões
Um recurso de permissão de base de dados do Cosmos está associado um utilizador de base de dados do Cosmos.  Cada utilizador pode conter zero ou mais permissões de base de dados do Cosmos.  Um recurso de permissão fornece acesso a um token de segurança que precisa do utilizador ao tentar aceder a um recurso de aplicação específica.
Existem dois níveis de acesso disponíveis que podem ser fornecidas por um recurso de permissão:

* Todos: O utilizador possui permissão total no recurso.
* Leitura: O utilizador só pode ler o conteúdo do recurso, mas não é possível efetuar a escrita, update ou delete operações no recurso.

> [!NOTE]
> Para executar a base de dados do Cosmos o utilizador tem de ter a permissão All na coleção na qual o procedimento armazenado será executado de procedimentos armazenados.
> 
> 

### <a name="code-sample-to-create-permission"></a>Exemplo de código para criar a permissão

O exemplo de código seguinte mostra como criar um recurso de permissão, o token de recurso do recurso de permissão de leitura e associar as permissões com o [utilizador](#users) criado acima.

```csharp
// Create a permission.
Permission docPermission = new Permission
{
    PermissionMode = PermissionMode.Read,
    ResourceLink = documentCollection.SelfLink,
    Id = "readperm"
};
  
docPermission = await client.CreatePermissionAsync(UriFactory.CreateUserUri("db", "user"), docPermission);
Console.WriteLine(docPermission.Id + " has token of: " + docPermission.Token);
```

Se especificar uma chave de partição para a coleção, em seguida, a permissão para a coleção, documentos e anexos recursos também tem de incluir ResourcePartitionKey para além de ResourceLink.

### <a name="code-sample-to-read-permissions-for-user"></a>Exemplo de código para permissões de leitura para utilizador

Para obter facilmente a permissão de todos os recursos associados com um utilizador específico, base de dados do Cosmos disponibilizam uma permissão feed para cada objeto de utilizador.  O fragmento de código seguinte mostra como obter a permissão associada ao utilizador criado acima, construir uma lista de permissão e criar um novo DocumentClient em nome do utilizador.

```csharp
//Read a permission feed.
FeedResponse<Permission> permFeed = await client.ReadPermissionFeedAsync(
  UriFactory.CreateUserUri("db", "myUser"));
 List<Permission> permList = new List<Permission>();

foreach (Permission perm in permFeed)
{
    permList.Add(perm);
}

DocumentClient userClient = new DocumentClient(new Uri(endpointUrl), permList);
```

## <a name="next-steps"></a>Passos seguintes
* Para saber mais sobre a segurança da base de dados de base de dados do Cosmos, consulte [Cosmos DB: base de dados de segurança](database-security.md).
* Para saber mais sobre a gestão de chaves principais e só de leitura, consulte [como gerir uma conta de base de dados do Azure Cosmos](manage-account.md#keys).
* Para saber como construir tokens de autorização de BD do Cosmos do Azure, consulte [controlo de acesso nos recursos do Azure Cosmos DB](https://docs.microsoft.com/rest/api/documentdb/access-control-on-documentdb-resources).
