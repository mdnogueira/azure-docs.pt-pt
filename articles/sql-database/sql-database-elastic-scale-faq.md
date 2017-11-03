---
title: "Escala elástica do SQL do Azure FAQ | Microsoft Docs"
description: "Perguntas mais frequentes sobre a escala elástica do SQL do Azure da base de dados."
services: sql-database
documentationcenter: 
manager: jhubbard
author: ddove
editor: 
ms.assetid: e60dde9c-bb7b-4f2f-b52c-bdb506d49fcb
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: ddove
ms.openlocfilehash: 4a2f73df00dd706699b20062d443af3bb88b6ef4
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="elastic-database-tools-faq"></a>Ferramentas de base de dados elástica FAQ
#### <a name="if-i-have-a-single-tenant-per-shard-and-no-sharding-key-how-do-i-populate-the-sharding-key-for-the-schema-info"></a>Se tiver um único inquilino por ID de partição horizontal e nenhuma chave de fragmentação, como posso preencher a chave de fragmentação para as informações de esquema?
O objeto de informações de esquema só é utilizado para dividir os cenários de intercalação. Se uma aplicação está inerentemente de inquilino único, não requer a ferramenta de intercalação de divisão e, consequentemente, não é necessário para preencher o objeto de informações de esquema.

#### <a name="ive-provisioned-a-database-and-i-already-have-a-shard-map-manager-how-do-i-register-this-new-database-as-a-shard"></a>Posso ter aprovisionado uma base de dados e já tiver um Gestor de mapa de partições horizontais, como registar esta nova base de dados como um ID de partição horizontal?
Consulte  **[a adição de um ID de partição horizontal para uma aplicação utilizando a biblioteca de clientes de base de dados elástica](sql-database-elastic-scale-add-a-shard.md)**. 

#### <a name="how-much-do-elastic-database-tools-cost"></a>Quantidade de ferramentas de base de dados elástica custos?
Utilizar a biblioteca de cliente da base de dados elástica não implica quaisquer custos. Acumular custos apenas para as bases de dados SQL do Azure que utiliza para shards e o Gestor de mapa de partições horizontais, bem como as funções da web/trabalho que aprovisionar para a ferramenta de intercalação de divisão.

#### <a name="why-are-my-credentials-not-working-when-i-add-a-shard-from-a-different-server"></a>Por que razão as minhas credenciais não estiverem a funcionar quando adiciono um ID de partição horizontal a partir de um servidor diferente?
Não usar credenciais no formato "ID de utilizador =username@servername", em vez disso, basta utilizar "ID de utilizador = nome de utilizador".  Além disso, lembre-se de que o início de sessão de "nomedeutilizador" tem permissões nas partições horizontais.

#### <a name="do-i-need-to-create-a-shard-map-manager-and-populate-shards-every-time-i-start-my-applications"></a>É necessário criar um Gestor de mapa de partições horizontais e preencher shards sempre que iniciar a minhas aplicações?
Não, a criação do Gestor de mapa de partições horizontais (por exemplo,  **[ShardMapManagerFactory.CreateSqlShardMapManager](http://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.createsqlshardmapmanager.aspx)**) é uma operação única.  A aplicação deve utilizar a chamada  **[ShardMapManagerFactory.TryGetSqlShardMapManager()](http://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager.aspx)**  no momento do arranque de aplicação.  Deve existir apenas um desse chamada por domínio de aplicação.

#### <a name="i-have-questions-about-using-elastic-database-tools-how-do-i-get-them-answered"></a>Posso ter dúvidas sobre como utilizar as ferramentas de base de dados elástica, como obtê-los respondidas?
Contacte-no [fórum da SQL Database do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted).

#### <a name="when-i-get-a-database-connection-using-a-sharding-key-i-can-still-query-data-for-other-sharding-keys-on-the-same-shard--is-this-by-design"></a>Quando posso obter uma ligação de base de dados utilizando uma chave de fragmentação, posso ainda pode consultar os dados para outras chaves de fragmentação de partições horizontais mesmo.  É por predefinição?
APIs de dimensionamento flexível dão-lhe uma ligação à base de dados correto para a sua chave de fragmentação, mas não fornecer a filtragem de chave de fragmentação.  Adicionar **onde** cláusulas à sua consulta restringir o âmbito para a chave de fragmentação fornecidas, se necessário.

#### <a name="can-i-use-a-different-azure-database-edition-for-each-shard-in-my-shard-set"></a>Posso utilizar uma edição de base de dados do Azure diferente para cada partição horizontal no meu conjunto de partições horizontais?
Sim, um ID de partição horizontal é uma base de dados individuais e, por conseguinte, um ID de partição horizontal pode ser uma edição Premium enquanto outra ser uma edição Standard. Além disso, a edição de um ID de partição horizontal pode aumentar ou reduzir verticalmente várias vezes durante a duração das partições horizontais.

#### <a name="does-the-split-merge-tool-provision-or-delete-a-database-during-a-split-or-merge-operation"></a>Não o aprovisionamento da ferramenta de intercalação de divisão (ou elimina) uma base de dados durante uma operação de intercalação ou divisão?
Não. Para **dividir** operações, a base de dados de destino tem de existir com o esquema apropriado e registado com o Gestor de mapa de partições horizontais.  Para **intercalação** operações, tem de eliminar as partições horizontais do Gestor de mapa de partições horizontais e, em seguida, elimine a base de dados.

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

