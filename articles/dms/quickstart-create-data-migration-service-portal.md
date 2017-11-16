---
title: "Criar uma instância de serviço de migração de base de dados do Azure no portal do Azure | Microsoft Docs"
description: "Utilizar o portal do Azure para criar uma instância do serviço de migração de base de dados do Azure"
services: database-migration
author: edmacauley
ms.author: edmaca
manager: craigg
ms.reviewer: 
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: quickstart
ms.date: 11/08/2017
ms.openlocfilehash: 4213a0ae73c38dcad403140e3d45e28f2d338020
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="create-a-database-migration-service-instance-using-the-azure-portal"></a>Criar uma instância de serviço de migração de base de dados no portal do Azure
Este guia de introdução, utilizar o portal do Azure para criar uma instância do serviço de migração de base de dados do Azure.  Depois de criar o serviço, poderá utilizá-la para migrar dados do SQL Server no local para uma base de dados SQL do Azure.

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="log-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure
Abra o browser e navegue para o [portal do Microsoft Azure](https://portal.azure.com/). Introduza as suas credenciais para iniciar sessão no portal. A vista predefinida é o dashboard de serviço.

## <a name="create-azure-database-migration-service"></a>Criar o serviço de migração de base de dados do Azure
1. Clique em  **+**  para criar um novo serviço.  Serviço de migração de base de dados ainda está em pré-visualização.  

1. Procurar no marketplace para "a migração", selecione "Serviço de migração de base de dados (pré-visualização)", em seguida, clique em **criar**.

    ![Criar o serviço de migração](media/quickstart-create-data-migration-service-portal/dms-create-service.png)

    - Escolha um **nome do serviço** que é memorable e exclusivo para identificar a sua instância de serviço de migração de base de dados do Azure.
    - Selecione o seu Azure **subscrição** no qual pretende criar o serviço de migração de base de dados.
    - Crie um novo **rede** com um nome exclusivo.
    - Escolha o **localização** que se aproxima do seu servidor de origem ou de destino.
    - Selecione Basic: 1 vCore para o **escalão de preço**.

1. Clique em **Criar**.

Após alguns instantes, o serviço de migração de base de dados do Azure será criado e estará pronto a utilizar.  Irá ver o serviço de base de dados de migração, conforme mostrado na imagem.

![Serviço de migração criado](media/quickstart-create-data-migration-service-portal/dms-service-created.png)

## <a name="clean-up-resources"></a>Limpar recursos
É possível limpar os recursos que criou no início rápido, eliminando o [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md).  Para eliminar o grupo de recursos, navegue para o serviço de migração de base de dados que criou, clique em de **grupo de recursos** nome e, em seguida, selecione **eliminar grupo de recursos**.  Esta ação elimina todos os recursos no grupo de recursos, bem como o grupo em si.

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Migrar o SQL Server no local à BD SQL do Azure](tutorial-sql-server-to-azure-sql.md)