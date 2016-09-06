<properties
    pageTitle="Como criar uma conta do DocumentDB | Microsoft Azure"
    description="Crie uma base de dados NoSQL com o Azure DocumentDB. Siga estas instruções para criar uma conta do DocumentDB e começar a construir a sua base de dados NoSQL rápida de escala global." 
    keywords="criar uma base de dados"
    services="documentdb"
    documentationCenter=""
    authors="mimig1"
    manager="jhubbard"
    editor="monicar"/>

<tags
    ms.service="documentdb"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/25/2016"
    ms.author="mimig"/>

# Como criar uma conta do DocumentDB através do Portal do Azure

> [AZURE.SELECTOR]
- [Portal do Azure](documentdb-create-account.md)
- [ARM e CLI do Azure](documentdb-automation-resource-manager-cli.md)

Para criar uma base de dados com o Microsoft Azure DocumentDB, tem de:

- Possuir uma conta do Azure. Pode obter uma [conta do Azure gratuita](https://azure.microsoft.com/free) caso ainda não tenha uma. 
- Criar uma conta do DocumentDB.  

Pode criar uma conta do DocumentDB ao utilizar o portal do Azure, modelos do Azure Resource Manager ou a interface de linha de comandos (CLI) do Azure. Este artigo mostra como criar uma conta do DocumentDB com o portal do Azure. Para criar uma conta ao utilizar o Azure Resource Manager ou a CLI do Azure, consulte [Automatizar criação de contas de base de dados do DocumentDB](documentdb-automation-resource-manager-cli.md).

Está familiarizado com o DocumentDB? Veja [este](https://azure.microsoft.com/documentation/videos/create-documentdb-on-azure/) vídeo de quatro minutos da autoria de Scott Hanselman para saber como pode realizar as tarefas mais comuns no portal online.

[AZURE.INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

## Passos seguintes

Agora que tem uma conta do DocumentDB, o passo seguinte consiste em criar a base de dados do DocumentDB. Pode criar uma base de dados ao utilizar um dos seguintes:

- O portal do Azure, conforme descrito em [Criar uma base de dados do DocumentDB através do portal do Azure](documentdb-create-database.md).
- As amostras de C# .NET no projeto [DatabaseManagement](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/DatabaseManagement) do repositório [azure-documentdb-net](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples) no GitHub.
- Os tutoriais totalmente inclusivos: [.NET](documentdb-get-started.md), [.NET MVC](documentdb-dotnet-application.md), [Java](documentdb-java-application.md), [Node.js](documentdb-nodejs-application.md) ou [Python](documentdb-python-application.md).
- Os [SDKs do DocumentDB](documentdb-sdk-dotnet.md). O DocumentDB tem SDKs .NET, Java, Python, Node.js e API JavaScript.


Depois de criar a sua base de dados, terá de [adicionar uma ou mais coleções](documentdb-create-collection.md) à base de dados e, em seguida, [adicionar documentos](documentdb-view-json-document-explorer.md) às coleções.

Depois de ter documentos numa coleção, pode utilizar o [DocumentDB SQL](documentdb-sql-query.md) para [executar consultas](documentdb-sql-query.md#executing-queries) relativamente aos seus documentos através do [Explorador de Consultas](documentdb-query-collections-query-explorer.md) no portal, da [API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) ou de um dos [SDKs](documentdb-sdk-dotnet.md).

Para saber mais sobre o DocumentDB, explore estes recursos:

-   [Percurso de aprendizagem para o DocumentDB](https://azure.microsoft.com/documentation/learning-paths/documentdb/)
-   [Conceitos e modelo de recursos do DocumentDB](documentdb-resources.md)



<!--HONumber=ago16_HO5-->


