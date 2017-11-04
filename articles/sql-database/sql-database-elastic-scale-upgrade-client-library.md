---
title: "Atualizar para a biblioteca de cliente mais recente da base de dados elásticas | Microsoft Docs"
description: "Utilize o Nuget para a biblioteca de cliente de atualização de base de dados elásticas."
services: sql-database
documentationcenter: 
manager: jhubbard
author: ddove
ms.assetid: 0a546510-76e7-465e-9271-f15ff0cfa959
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: ddove
ms.openlocfilehash: 62609f2488a01d9b406c6b730c53ecc9f9b6ea1b
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="upgrade-an-app-to-use-the-latest-elastic-database-client-library"></a>Atualizar uma aplicação para utilizar a biblioteca de cliente mais recente da base de dados elástica
Novas versões do [biblioteca de clientes de base de dados elástica](sql-database-elastic-database-client-library.md) estão disponíveis através de NuGetand da interface do Gestor de NuGetPackage no Visual Studio. Atualização contém correções de erros e suporte para novas capacidades da biblioteca de clientes.

**Para a versão mais recente:** aceda a [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

Reconstruir a aplicação com a biblioteca de novo, bem como alterar os metadados do Gestor de mapa de partições horizontais existentes armazenados no seu bases de dados do SQL do Azure para suportar novas funcionalidades.

Executar estes passos pela ordem garante que versões antigas da biblioteca de clientes já não estiverem presentes no seu ambiente quando os objetos de metadados estão atualizados, que significa que não serão possível criar objetos de metadados de uma versão antiga após a atualização.   

## <a name="upgrade-steps"></a>Passos de atualização
**1. Atualize as suas aplicações.** No Visual Studio, transferir e referencia a versão de biblioteca de cliente mais recente para todos os seus projetos de programação que utilizam a biblioteca; em seguida, reconstruir e implementar. 

* Na sua solução Visual Studio, selecione **ferramentas** --> **Gestor de pacotes NuGet** -->  **gerir pacotes NuGet para solução**. 
* (Visual Studio 2013) No painel esquerdo, selecione **atualizações**e, em seguida, selecione o **atualização** botão no pacote **biblioteca do Azure SQL da base de dados elásticas escala cliente** que é apresentado na janela.
* (Visual Studio 2015) Defina a caixa de filtro para **atualizar disponíveis**. Selecione o pacote de atualização e clique em de **atualizar** botão.
* (Visual Studio 2017) Na parte superior da caixa de diálogo, selecione **atualizações**. Selecione o pacote de atualização e clique em de **atualizar** botão.
* Criar e implementar. 

**2. Atualize os scripts.** Se estiver a utilizar **PowerShell** scripts para gerir shards, [transferir a nova versão de biblioteca](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) e copie-o para o diretório a partir da qual pode executar scripts. 

**3. Atualize o serviço de divisão de intercalação.** Se utilizar a ferramenta de intercalação de divisão de base de dados elástica para reorganize dados fragmentados, [transferir e implementar a versão mais recente da ferramenta](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/). Passos de atualização de detalhado para o serviço pode ser encontrado [aqui](sql-database-elastic-scale-overview-split-and-merge.md). 

**4. Atualizar as bases de dados do Gestor de mapa de partições horizontais**. Atualize os metadados que suportam o Maps de partições horizontais na SQL Database do Azure.  Existem duas formas que pode realizar esta tarefa, utilizando o PowerShell ou c#. Ambas as opções são mostradas abaixo.

***Opção 1: Atualizar metadados com o PowerShell***

1. Transferir o utilitário mais recente da linha de comandos para NuGet da [aqui](http://nuget.org/nuget.exe) e guardar para uma pasta. 
2. Abra uma linha de comandos, navegue para a mesma pasta e emitir o comando:`nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Client`
3. Navegue para a subpasta que contém a nova versão da DLL de cliente que ter acabou de transferir, por exemplo:`cd .\Microsoft.Azure.SqlDatabase.ElasticScale.Client.1.0.0\lib\net45`
4. Transferir o scriptlet de atualização de cliente de base de dados elástica do [Centro de scripts](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-Elastic-6442e6a9)e guarde-o para a mesma pasta que contém a DLL.
5. A partir dessa pasta, execute "PowerShell.\upgrade.ps1" na linha de comandos e siga as instruções.

***Opção 2: Os metadados de atualização utilizar c#***

Em alternativa, criar uma aplicação do Visual Studio que abre o ShardMapManager, Itera todas as partições horizontais e efetua a atualização de metadados ao chamar os métodos [UpgradeLocalStore](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradelocalstore.aspx) e [UpgradeGlobalStore ](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradeglobalstore.aspx) tal como neste exemplo: 

    ShardMapManager smm =
       ShardMapManagerFactory.GetSqlShardMapManager
       (connStr, ShardMapManagerLoadPolicy.Lazy); 
    smm.UpgradeGlobalStore(); 

    foreach (ShardLocation loc in
     smm.GetDistinctShardLocations()) 
    {   
       smm.UpgradeLocalStore(loc); 
    } 

Estes técnicas para atualizações de metadados podem ser aplicadas várias vezes sem perigo. Por exemplo, se uma versão mais antiga do cliente inadvertidamente cria uma partição horizontal depois de já tiver atualizado, pode executar a atualização novamente em todas as partições horizontais para se certificar de que a versão mais recente de metadados está presente em toda a infraestrutura. 

**Nota:** novas versões da biblioteca de clientes publicados para data continuar a trabalhar com versões anteriores dos metadados do Gestor de mapa de partições horizontais na BD SQL do Azure e vice-versa.   No entanto, para tirar partido de algumas das novas funcionalidades no cliente mais recente, os metadados tem de ser atualizado.   Tenha em atenção que as atualizações de metadados não irão afetar quaisquer dados de utilizador ou dados específicos da aplicação, apenas objectos criada e utilizada pelo Gestor de mapa de partições horizontais.  E as aplicações continuam a funcionar através de sequência de atualização descrita acima. 

## <a name="elastic-database-client-version-history"></a>Histórico de versões de cliente de base de dados elástica
Para o histórico de versão, aceda a [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]:./media/sql-database-elastic-scale-upgrade-client-library/nuget-upgrade.png

