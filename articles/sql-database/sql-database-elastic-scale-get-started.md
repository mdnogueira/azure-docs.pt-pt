---
title: "Começar a utilizar as ferramentas de base de dados elástica | Microsoft Docs"
description: "EXPLICAÇÃO básica da funcionalidade de ferramentas de base de dados elástica do SQL Database do Azure, incluindo uma aplicação de exemplo simples para execução."
services: sql-database
documentationcenter: 
manager: jhubbard
author: ddove
editor: CarlRabeler
ms.assetid: b6911f8d-2bae-4d04-9fa8-f79a3db7129d
ms.service: sql-database
ms.custom: scale out apps
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: ddove
ms.openlocfilehash: 600334c58ce62a1e53e8a57dd1566bd211249164
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="get-started-with-elastic-database-tools"></a>Começar a utilizar as ferramentas de base de dados elástica
Este documento apresenta-lhe a experiência de programação, ajudando-o para executar a aplicação de exemplo. O exemplo cria uma aplicação em partição horizontal simples e explicar as principais capacidades de ferramentas de base de dados elástica. O exemplo demonstra as funções do [biblioteca de clientes de base de dados elástica](sql-database-elastic-database-client-library.md).

Para instalar a biblioteca, visite [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). A biblioteca é instalada com a aplicação de exemplo descrita na secção seguinte.

## <a name="prerequisites"></a>Pré-requisitos
* Visual Studio 2012 ou posterior com c#. Transferir uma versão gratuita em [Visual Studio Downloads](http://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).
* NuGet 2.7 ou posterior. Para obter a versão mais recente, consulte [instalar NuGet](http://docs.nuget.org/docs/start-here/installing-nuget).

## <a name="download-and-run-the-sample-app"></a>Transferir e executar a aplicação de exemplo
O **elástico ferramentas DB para SQL do Azure - introdução** aplicação de exemplo ilustra os aspetos mais importantes a experiência de desenvolvimento de aplicações em partição horizontal que utilize ferramentas de base de dados elástica. Concentra-se em casos de utilização da chave para [gestão de mapa de partições horizontais](sql-database-elastic-scale-shard-map-management.md), [encaminhamento de dados dependentes](sql-database-elastic-scale-data-dependent-routing.md), e [consultar várias partições horizontais](sql-database-elastic-scale-multishard-querying.md). Para transferir e executar o exemplo, siga estes passos: 

1. Transferir o [elástico ferramentas DB para SQL do Azure - introdução exemplo](https://code.msdn.microsoft.com/windowsapps/Elastic-Scale-with-Azure-a80d8dc6) da MSDN. Deszipe o exemplo para uma localização que escolher.

2. Para criar um projeto, abra o **ElasticScaleStarterKit.sln** soluções do **c#** diretório.

3. A solução para o projeto de exemplo, abra o **App. config** ficheiro. Em seguida, siga as instruções no ficheiro para adicionar o nome do servidor SQL Database do Azure e as informações início de sessão (nome de utilizador e palavra-passe).

4. Crie e execute a aplicação. Quando lhe for pedido, ative o Visual Studio para restaurar os pacotes de NuGet da solução. Esta ação transfere a versão mais recente da biblioteca de clientes de base de dados elástica do NuGet.

5. Experimentar as diferentes opções para saber mais sobre as capacidades de biblioteca de cliente. Tenha em atenção os passos que a aplicação demora na consola de saída e à vontade explorar o código em segundo plano.
   
    ![Progresso][4]

Congratulations – tiver com êxito incorporadas e executar a sua primeira aplicação em partição horizontal utilizando ferramentas de base de dados elástica na base de dados do SQL Server. Utilize o Visual Studio ou o SQL Server Management Studio para ligar à base de dados SQL e observe rápida shards criado o exemplo. Vai notar novas bases de dados de partição horizontal de exemplo e uma base de dados manager partições horizontais do mapa que tiver criado o exemplo.

> [!IMPORTANT]
> Recomendamos que utilize sempre a versão mais recente do Management Studio, para que o se a manter sincronizadas com as atualizações do Azure e a SQL Database. [Atualize o SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
> 
> 

### <a name="key-pieces-of-the-code-sample"></a>Peças chave de exemplo de código
* **Gerir shards e partições horizontais mapeia**: O código ilustra como trabalhar com shards, intervalos e mapeamentos no ficheiro **ShardManagementUtils.cs**. Para obter mais informações, consulte [ampliação das bases de dados com o Gestor de mapa de partições horizontais](http://go.microsoft.com/?linkid=9862595).  

* **Encaminhamento de dados dependentes**: encaminhamento de transações para o ID de partição horizontal direita é apresentado na **DataDependentRoutingSample.cs**. Para obter mais informações, consulte [encaminhamento de dados dependentes](http://go.microsoft.com/?linkid=9862596). 

* **Consultar o ao longo de vários shards**: consultar em shards é ilustrado no ficheiro **MultiShardQuerySample.cs**. Para obter mais informações, consulte [consultar várias partições horizontais](http://go.microsoft.com/?linkid=9862597).

* **Adicionar shards vazios**: A iterativo adição de novos shards vazios é realizada o código no ficheiro **CreateShardSample.cs**. Para obter mais informações, consulte [ampliação das bases de dados com o Gestor de mapa de partições horizontais](http://go.microsoft.com/?linkid=9862595).

### <a name="other-elastic-scale-operations"></a>Outras operações de dimensionamento elástico
* **Divisão de um ID de partição horizontal existente**: A capacidade de dividir shards é fornecida pelo **ferramenta de intercalação de divisão**. Para obter mais informações, consulte [mover dados entre bases de dados de nuvem de escalamento horizontal](sql-database-elastic-scale-overview-split-and-merge.md).

* **A intercalação de partições horizontais existentes**: partições horizontais intercala também é efetuada utilizando o **ferramenta de intercalação de divisão**. Para obter mais informações, consulte [mover dados entre bases de dados de nuvem de escalamento horizontal](sql-database-elastic-scale-overview-split-and-merge.md).   

## <a name="cost"></a>Custo
As ferramentas de base de dados elásticas estão livres. Quando utilizar ferramentas de base de dados elástica, não receberá quaisquer taxas adicionais sobre o custo de utilização da sua do Azure. 

Por exemplo, a aplicação de exemplo cria novas bases de dados. O custo de isto depende a edição da base de dados do SQL Server que escolher e a utilização do Azure da sua aplicação.

Para obter informações sobre preços, consulte [base de dados SQL detalhes de preços](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre as ferramentas de base de dados elásticas, consulte as seguintes páginas:

* Exemplos de código: 
  * [Ferramentas de base de dados elástica para o SQL do Azure - introdução](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-a80d8dc6?SRC=VSIDE)
  * [Ferramentas de base de dados elástica para o SQL do Azure - Entity Framework integração](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-bae904ba?SRC=VSIDE)
  * [Elasticidade de partições horizontais no Centro de scripts](https://gallery.technet.microsoft.com/scriptcenter/Elastic-Scale-Shard-c9530cbe)
* Blogue: [anúncio do dimensionamento elástico](https://azure.microsoft.com/blog/2014/10/02/introducing-elastic-scale-preview-for-azure-sql-database/)
* Microsoft Virtual Academy: [implementar fragmentação através de escalamento horizontal com o vídeo de biblioteca de cliente de base de dados elástica](https://mva.microsoft.com/training-courses/elastic-database-capabilities-with-azure-sql-db-16554?l=lWyQhF1fC_6306218965) 
* Canal 9: [horizontal elástico vídeo de descrição geral](http://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Database-Elastic-Scale)
* Fórum de discussão: [fórum da SQL Database do Azure](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted)
* Para medir o desempenho: [contadores de desempenho para o Gestor de mapa de partições horizontais](sql-database-elastic-database-client-library.md)

<!--Anchors-->
[The Elastic Scale Sample Application]: #The-Elastic-Scale-Sample-Application
[Download and Run the Sample App]: #Download-and-Run-the-Sample-App
[Cost]: #Cost
[Next steps]: #next-steps

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-get-started/newProject.png
[2]: ./media/sql-database-elastic-scale-get-started/click-online.png
[3]: ./media/sql-database-elastic-scale-get-started/click-CSharp.png
[4]: ./media/sql-database-elastic-scale-get-started/output2.png

