---
title: Validar XML - as do Azure Logic Apps | Microsoft Docs
description: "Validar XML com esquemas de cenários de Azure Logic Apps e B2B utilizando o pacote de integração do Enterprise"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: cgronlun
ms.assetid: d700588f-2d8a-4c92-93eb-e1e6e250e760
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: 8558efffa354cc4bb93820c837077ee997924c95
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="validate-xml-for-enterprise-integration"></a>Validar o XML para a integração do enterprise

Muitas vezes, cenários B2B, os parceiros de um contrato tem de se certificar de que as mensagens que do exchange que são válidas antes de iniciar o processamento de dados. Pode validar documentos com um esquema predefinido utilizando a utilizar o conector de validação XML no pacote de integração empresarial.

## <a name="validate-a-document-with-the-xml-validation-connector"></a>Validar um documento com o conector de validação de XML

1. Criar uma aplicação lógica, e [associar a aplicação para a conta de integração](../logic-apps/logic-apps-enterprise-integration-accounts.md "saiba associar uma conta de integração para uma aplicação lógica") que tenha o esquema que pretende utilizar para validar dados XML.

2. Adicionar um **pedido - pedido de HTTP um quando é recebido** acionador à sua aplicação lógica.

    ![](./media/logic-apps-enterprise-integration-xml/xml-1.png)

3. Para adicionar o **validação XML** ação, escolha **adicionar uma ação**.

4. Para filtrar todas as ações para aquela que pretende, introduza *xml* na caixa de pesquisa. Escolha **validação XML**.

    ![](./media/logic-apps-enterprise-integration-xml/xml-2.png)

5. Para especificar o conteúdo XML que pretende validar, selecione **conteúdo**.

    ![](./media/logic-apps-enterprise-integration-xml/xml-1-5.png)

6. Selecione a etiqueta body como o conteúdo que pretende validar.

    ![](./media/logic-apps-enterprise-integration-xml/xml-3.png)

7. Para especificar o esquema que pretende utilizar para validar anterior *conteúdo* de entrada, escolha **nome do esquema**.

    ![](./media/logic-apps-enterprise-integration-xml/xml-4.png)

8. Guarde o trabalho  

    ![](./media/logic-apps-enterprise-integration-xml/xml-5.png)

Terminou agora a configuração do seu conector de validação. Uma aplicação do mundo real, pode querer armazenar os dados validados numa linha de negócio (LOB) aplicação, como o SalesForce. Para enviar o resultado validado Salesforce, adicione uma ação.

Para testar a ação de validação, efetue um pedido para o ponto final de HTTP.

## <a name="next-steps"></a>Passos seguintes
[Saiba mais sobre o Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Saiba mais sobre o pacote de integração do Enterprise")   

