---
title: Criar e ligar a uma base de dados MySQL no Azure
description: "Saiba como utilizar o portal do Azure para criar uma base de dados MySQL e, em seguida, ligar ao mesmo a partir de uma aplicação web do PHP no Azure."
documentationcenter: php
services: app-service\web
author: cephalin
manager: erikre
editor: 
tags: mysql
ms.assetid: 55465a9a-7e65-4fd9-8a65-dd83ee41f3e5
ms.service: multiple
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 04/25/2017
ms.author: robmcm;cephalin
ms.openlocfilehash: c072cb3a7d376d1e3c2b9f741f5410106e701256
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-connect-to-a-mysql-database-in-azure"></a>Criar e ligar a uma base de dados MySQL no Azure
Este tutorial mostra como criar uma base de dados MySQL no [portal do Azure](https://portal.azure.com) (fornecedor é [ClearDB](http://www.cleardb.com/)) e como ligar ao mesmo a partir de uma aplicação de web do PHP em execução no [App Service do Azure](app-service/app-service-web-overview.md).

> [!NOTE]
> Também pode criar uma base de dados MySQL como parte de um <a href="https://portal.azure.com/#create/WordPress.WordPress" target="_blank">modelo de aplicação Marketplace</a>.
>
>

## <a name="create-a-mysql-database-in-azure-portal"></a>Criar uma base de dados MySQL no portal do Azure
Para criar uma base de dados MySQL no portal do Azure, efetue o seguinte:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. No menu à esquerda, clique em **novo** > **dados + armazenamento** > **base de dados MySQL**.

    ![Criar uma base de dados MySQL no Azure - início](./media/store-php-create-mysql-database/create-db-1-start.png)
3. Na nova base de dados MySQL [painel](azure-portal-overview.md), configurar a sua nova base de dados MySQL da seguinte forma (*painel*: uma página de portal abre-se horizontalmente):

   * **Nome de base de dados**: escreva um nome de forma exclusiva
   * **Subscrição**: selecione a subscrição a utilizar
   * **Tipo de base de dados**: selecione **partilhados** de camadas de baixo custo ou livres, ou **dedicado** para obter recursos dedicados.
   * **Grupo de recursos**: adicionar a base de dados MySQL existente [grupo de recursos](azure-resource-manager/resource-group-overview.md) ou colocá-la num novo. Recursos no mesmo grupo podem ser facilmente geridos em conjunto.
   * **Localização**: selecione uma localização perto de si. Ao adicionar a um grupo de recursos existente, está a bloquear a localização do grupo de recursos.
   * **Escalão de preço**: clique em **escalão de preço**, em seguida, selecione uma opção de preço (**mercúrio** camada é gratuita) e, em seguida, clique em **selecione**.
   * **Termos legais**: clique em **termos legais**, reveja os detalhes de compra e clique em **comprar**.
   * **Afixar ao dashboard**: selecione se pretende aceder diretamente a partir do dashboard. Isto é especialmente útil se não estiver familiarizado com ainda navegação do portal.

     A seguinte captura de ecrã é apenas um exemplo de como pode configurar a base de dados MySQL.  
     ![Criar uma base de dados MySQL no Azure - configurar](./media/store-php-create-mysql-database/create-db-2-configure.png)
4. Quando terminar, configurar, clique em **criar**.

    ![Criar uma base de dados MySQL no Azure - criar](./media/store-php-create-mysql-database/create-db-3-create.png)

    Verá uma notificação de pop-up permitindo que sabe que a implementação foi iniciada.

    ![Criar uma base de dados MySQL no Azure - em curso](./media/store-php-create-mysql-database/create-db-4-started-status.png)

    Irá obter outro pop-up assim que a implementação é concluída com êxito. O portal também irá abrir o painel de base de dados MySQL automaticamente.

<a name="connect"></a>

## <a name="connect-to-your-mysql-database"></a>Ligar à base de dados MySQL
Para ver as informações de ligação para a sua nova base de dados MySQL, basta clicar **propriedades** no painel da sua aplicação web.

![Criar uma base de dados MySQL no Azure - painel de base de dados MySQL](./media/store-php-create-mysql-database/create-db-5-finished-db-blade.png)

Agora, pode utilizar essas informações de ligação em qualquer aplicação web. Um exemplo que mostra como utilizar as informações de ligação a partir de uma aplicação PHP simple está disponível [aqui](https://github.com/WindowsAzure/azure-sdk-for-php-samples/tree/master/tasklist-mysql).

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações, consulte o [Centro para programadores do PHP](/develop/php/).
