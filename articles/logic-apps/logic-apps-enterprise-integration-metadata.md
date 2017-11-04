---
title: "Gerir metadados de artefacto da conta integração - Azure Logic Apps | Microsoft Docs"
description: "Adicionar ou obter metadados de artefacto de contas de automatização do Azure Logic Apps"
author: padmavc
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 11/21/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: 473da6436ad39709359b579162a6b60efc38d61f
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="manage-artifact-metadata-in-integration-accounts-for-logic-apps"></a>Gerir metadados de artefactos em contas de integração para aplicações lógicas

Pode definir metadados personalizados para artefactos em contas de automatização e obter esses metadados durante o tempo de execução para a sua aplicação lógica. Por exemplo, pode especificar os metadados para artefactos como parceiros, contratos, esquemas e mapas - armazenam todos os metadados utilizando pares chave-valor. Atualmente, os artefactos não é possível criar metadados através da IU, mas pode utilizar as APIs REST para criar os metadados. Para adicionar metadados quando criar ou selecionar um parceiro, o contrato ou o esquema no portal do Azure, escolha **editar**. Para obter os metadados de artefacto nas logic apps, pode utilizar a funcionalidade de pesquisa de artefacto da conta de integração.

## <a name="add-metadata-to-artifacts-in-integration-accounts"></a>Adicionar metadados a artefactos em contas de automatização

1. Criar um [conta integração](logic-apps-enterprise-integration-create-integration-account.md).

2. Adicionar um artefacto à sua conta de integração, por exemplo, um [parceiro](logic-apps-enterprise-integration-partners.md#how-to-create-a-partner), [contrato](logic-apps-enterprise-integration-agreements.md#how-to-create-agreements), ou [esquema](logic-apps-enterprise-integration-schemas.md).

3.  Selecione o artefacto, escolha **editar**e introduza os detalhes de metadados.

    ![Introduza os metadados](media/logic-apps-enterprise-integration-metadata/image1.png)

## <a name="retrieve-metadata-from-artifacts-for-logic-apps"></a>Obter metadados a partir de artefactos para aplicações lógicas

1. Criar um [aplicação lógica](logic-apps-create-a-logic-app.md).

2. Criar um [ligação da sua aplicação lógica à sua conta de integração](logic-apps-enterprise-integration-create-integration-account.md#link-an-integration-account-to-a-logic-app). 

3. No Designer de aplicação lógica, adicionar um acionador como *pedido* ou *HTTP* à sua aplicação lógica.

4.  Escolha **passo seguinte** > **adicionar uma ação**. Procurar *integração* para que possa encontrar e, em seguida, selecione **Integration Account - pesquisa de artefacto da conta de integração**.

    ![Selecione a pesquisa de artefacto da conta de integração](media/logic-apps-enterprise-integration-metadata/image2.png)

5. Selecione o **artefactos tipo**e forneça o **artefactos nome**.

    ![Selecione o tipo de artefacto e especifique o nome de artefactos](media/logic-apps-enterprise-integration-metadata/image3.png)

## <a name="example-retrieve-partner-metadata"></a>Exemplo: Obter metadados a parceiro

Metadados de parceiro tem estes `routingUrl` detalhes:

![Localizar o parceiro de metadados "routingURL"](media/logic-apps-enterprise-integration-metadata/image6.png)

1. Na sua aplicação lógica, adicione o acionador, um **Integration Account - pesquisa de artefacto da conta de integração** ação para o seu parceiro e um **HTTP**.

    ![Adicionar o acionador, pesquisa de artefacto e "HTTP" para a sua aplicação lógica](media/logic-apps-enterprise-integration-metadata/image4.png)

2. Para obter o URI, aceda à vista de código para a sua aplicação lógica. A definição da aplicação lógica deve aspeto neste exemplo:

    ![Pesquisa de pesquisa](media/logic-apps-enterprise-integration-metadata/image5.png)


## <a name="next-steps"></a>Passos seguintes
* [Saiba mais sobre contratos](logic-apps-enterprise-integration-agreements.md "Saiba mais sobre contratos de integração do enterprise")  
