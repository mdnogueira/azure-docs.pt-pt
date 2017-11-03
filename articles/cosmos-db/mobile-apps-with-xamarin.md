---
title: "Criar aplicações móveis com o Xamarin e base de dados do Azure Cosmos | Microsoft Docs"
description: "Um tutorial que cria um Xamarin iOS, Android ou formulários de aplicação utilizando a BD do Cosmos do Azure. BD do Cosmos do Azure é um fast, a escala de planet, a base de dados de nuvem para aplicações móveis."
services: cosmos-db
documentationcenter: .net
author: arramac
manager: monicar
editor: 
ms.assetid: ff97881a-b41a-499d-b7ab-4f394df0e153
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/03/2017
ms.author: arramac
ms.openlocfilehash: 4dfe9c755f3e7d5414ae04dd4027defd6cef2e4a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="build-mobile-applications-with-xamarin-and-azure-cosmos-db"></a>Criar aplicações móveis com o Xamarin e base de dados do Azure Cosmos
As aplicações móveis mais necessitam para armazenar dados na nuvem e base de dados do Azure Cosmos é uma base de dados de nuvem para aplicações móveis. Tem tudo que necessita de um para programadores móveis. É uma base de dados completamente gerido como um serviço que dimensiona a pedido. -Pode colocar os dados para a aplicação de forma transparente, os seus utilizadores estão localizados em torno de todo o mundo. Utilizando o [Azure Cosmos DB .NET Core SDK](documentdb-sdk-dotnet-core.md), pode ativar a aplicações móveis do Xamarin interaja diretamente com a BD do Cosmos do Azure, sem uma camada média.

Este artigo fornece um tutorial para a criação de aplicações móveis com o Xamarin e base de dados do Azure Cosmos. Pode encontrar o código de origem completo para o tutorial em [Xamarin e BD do Cosmos do Azure no GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin), incluindo como gerir utilizadores e permissões.

## <a name="azure-cosmos-db-capabilities-for-mobile-apps"></a>Capacidades do Cosmos BD do Azure para aplicações móveis
BD do Azure do Cosmos fornece as seguintes capacidades de chaves para os programadores de aplicações móveis:

![Capacidades do Cosmos BD do Azure para aplicações móveis](media/mobile-apps-with-xamarin/documentdb-for-mobile.png)

* Consultas de avançada em dados sem esquemas. BD do Cosmos do Azure armazena os dados como sem esquemas documentos JSON heterogéneos coleções. Oferece [consultas avançadas e rápidas](documentdb-sql-query.md) sem a necessidade de se preocupar com esquemas ou índices.
* Débito rápido. Demora apenas alguns milissegundos para ler e escrever documentos com base de dados do Azure Cosmos. Os programadores podem especificar o débito que precisam e base de dados do Azure Cosmos honra-lo com o SLA de 99,99 percentagem.
* Ilimitada escala. As coleções de base de dados do Azure Cosmos [aumentar conforme a sua aplicação cresce](partition-data.md). Pode começar com o tamanho de dados de pequena e débito de centenas de pedidos por segundo. As coleções podem aumentar a petabytes de dados e débito arbitrariamente grande com centenas de milhões de pedidos por segundo.
* Globalmente distribuído. Utilizadores de aplicações móveis são em viagem, muitas vezes por todo o mundo. BD do Cosmos do Azure é um [base de dados globalmente distribuída](distribute-data-globally.md). Clique no mapa para tornar os seus dados acessível aos seus utilizadores.
* Autorização avançada incorporado. Com base de dados do Azure Cosmos, pode facilmente implementar padrões populares, como [dados por utilizador](https://aka.ms/documentdb-xamarin-todouser) ou multiuser partilhados dados, sem o código de autorização personalizada complexas.
* Consultas de Geoespacial. Muitas aplicações móveis oferecem experiências georreplicação contextuais hoje. Com suporte de primeira classe para [geoespacial tipos](geospatial.md), os faz com que a base de dados do Azure Cosmos criar estes experiências fácil realizar.
* Binários anexos. Os dados de aplicação, muitas vezes, incluem blobs binários. Suporte nativo para anexos torna mais fácil de utilizar a base de dados do Azure Cosmos como uma cafetaria de paragem para os seus dados de aplicação.

## <a name="azure-cosmos-db-and-xamarin-tutorial"></a>Tutorial de base de dados do Cosmos e Xamarin do Azure
O tutorial seguinte mostra como criar uma aplicação móvel com o Xamarin e base de dados do Azure Cosmos. Pode encontrar o código de origem completo para o tutorial em [Xamarin e BD do Cosmos do Azure no GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin).

### <a name="get-started"></a>Introdução
É fácil começar a utilizar o Azure Cosmos DB. Aceda ao portal do Azure e criar uma nova conta de base de dados do Azure Cosmos. Clique em de **início rápido** separador. Transferir o exemplo de lista de tarefas de formulários de Xamarin que já está ligado à sua conta de base de dados do Azure Cosmos. 

![Início rápido de BD do Cosmos do Azure para aplicações móveis](media/mobile-apps-with-xamarin/cosmos-db-quickstart.png)

Ou se tiver uma aplicação Xamarin existente, pode adicionar o [pacote NuGet do Azure Cosmos DB](documentdb-sdk-dotnet-core.md). BD do Azure do Cosmos suporta xamarin. IOS, xamarin. Android, e formulários de Xamarin partilhado bibliotecas.

### <a name="work-with-data"></a>Trabalhar com dados
Os registos de dados são armazenados na base de dados do Azure Cosmos como sem esquemas documentos JSON heterogéneos coleções. Pode armazenar documentos com estruturas diferentes na mesma coleção:

```cs
    var result = await client.CreateDocumentAsync(collectionLink, todoItem);
```

Nos seus projetos de Xamarin, pode utilizar consultas de linguagem integrada através de dados sem esquemas:

```cs
    var query = await client.CreateDocumentQuery<ToDoItem>(collectionLink)
                    .Where(todoItem => todoItem.Complete == false)
                    .AsDocumentQuery();

    Items = new List<TodoItem>();
    while (query.HasMoreResults) {
        Items.AddRange(await query.ExecuteNextAsync<TodoItem>());
    }
```
### <a name="add-users"></a>Adicionar utilizadores
Como muitas obter exemplos de introdução, o exemplo de base de dados do Azure Cosmos transferiu autentica para o serviço utilizando um chave mestra codificado no código da aplicação. Esta predefinição não é uma boa prática para uma aplicação que pretende executar em qualquer lugar, exceto o emulador local. Se um utilizador não autorizado obtido a chave mestra, todos os dados em toda a sua conta de base de dados do Azure Cosmos foi comprometidos. Em vez disso, pretende que a aplicação para aceder apenas os registos para o utilizador com sessão iniciada. BD do Cosmos do Azure permite aos programadores conceder a leitura de aplicação ou de leitura/escrita permissão para uma coleção, um conjunto de documentos agrupados por uma chave de partição ou um documento específico. 

Siga estes passos para modificar a aplicação de lista de tarefas para uma aplicação de lista de tarefas multiuser: 

  1. Adicione início de sessão para a sua aplicação utilizando o Facebook, do Active Directory ou qualquer outro fornecedor.

  2. Criar uma coleção de Azure Cosmos DB UserItems com **/userId** como a chave de partição. Especificar a chave de partição para a coleção permite a BD do Azure Cosmos infinitamente dimensione à medida que cresce o número de utilizadores de aplicações, ao continuar oferecer consultas rápidas.

  3. Adicione o Mediador de Token de recursos do Azure Cosmos DB. Esta API Web simples autentica os utilizadores e tokens curta duração problemas a utilizadores com sessão iniciada com acesso apenas aos documentos na sua partição. Neste exemplo, o recurso Mediador de Token está alojada no App Service.

  4. Modificar a aplicação para autenticar para o recurso Mediador de Token com o Facebook e pedir os tokens de recursos para os utilizadores de Facebook com sessão iniciada. Em seguida, pode aceder aos respetivos dados na coleção UserItems.  

Pode encontrar um exemplo de código completo deste padrão em [Mediador de Token de recurso no GitHub](http://aka.ms/documentdb-xamarin-todouser). Este diagrama ilustra a solução:

![Mediador do Azure de utilizadores e permissões de base de dados do Cosmos](media/mobile-apps-with-xamarin/documentdb-resource-token-broker.png)

Se pretender que os dois utilizadores tenham acesso para a mesma lista de tarefas, pode adicionar permissões adicionais para o token de acesso no Mediador de Token de recurso.

### <a name="scale-on-demand"></a>Dimensionar a pedido
BD do Cosmos do Azure é uma base de dados gerido como um serviço. À medida que aumenta a sua base de utilizadores, não precisa de preocupar com as VMs de aprovisionamento ou aumentar núcleos. Tudo o que precisa para saber se a BD do Azure Cosmos é a aplicação tem de quantas operações por segundo (débito). Pode especificar o débito através de **escala** separador através da utilização de uma medida de débito chamado unidades de pedido (RUs) por segundo. Por exemplo, uma operação de leitura num documento de 1 KB requer 1 RU. Também pode adicionar alertas para o **débito** métrica para monitorizar o crescimento de tráfego e através de programação alterar o débito como alertas acionar.

![Débito de escala do Cosmos BD do Azure a pedido](media/mobile-apps-with-xamarin/cosmos-db-xamarin-scale.png)

### <a name="go-planet-scale"></a>Aceda a escala de planet
Como a popularidade de ganhos de aplicação, poderá obter utilizadores em todo o mundo. Ou, talvez pretende estar preparadas para eventos imprevistos. Aceda ao portal do Azure e abra a sua conta de base de dados do Azure Cosmos. Clique no mapa para tornar os seus dados continuamente replicar para qualquer número de regiões por todo o mundo. Esta capacidade disponibiliza os dados sempre que são os seus utilizadores. Também pode adicionar as políticas de ativação pós-falha para estar preparado para contingencies.

![Escala de base de dados do Cosmos do Azure em regiões geográficas](media/mobile-apps-with-xamarin/cosmos-db-xamarin-replicate.png)

Parabéns! Concluiu a solução e ter uma aplicação móvel com o Xamarin e base de dados do Azure Cosmos. Seguir passos semelhantes para compilar aplicações Cordova utilizando o Azure Cosmos DB JavaScript SDK e as aplicações nativas do iOS/Android utilizando APIs REST do Azure Cosmos DB.

## <a name="next-steps"></a>Passos seguintes
* Ver o código de origem para [Xamarin e BD do Cosmos do Azure no GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin).
* Transferir o [Cosmos Azure DB .NET Core SDK](documentdb-sdk-dotnet-core.md).
* Localizar mais exemplos de código para [aplicações .NET](documentdb-dotnet-samples.md).
* Saiba mais sobre [capacidades de consulta avançada de base de dados do Azure Cosmos](documentdb-sql-query.md).
* Saiba mais sobre [geoespacial suporte do BD Azure Cosmos](geospatial.md).



