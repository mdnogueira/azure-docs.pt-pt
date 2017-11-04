---
title: Logic Apps B2B edifact descodificar resolver UNH2.5 - Azure Logic Apps | Microsoft Docs
description: Azure Logic Apps B2B edifact descodificar resolver UNH2.5
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: cf44af18-1fe5-41d5-9e06-cc57a968207c
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: 62ad8183cc6e9f56255b2729a04ee7710d00a21a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-handle-edifact-documents-having-unh25-segment"></a>Como processar documentos EDIFACT ter UNH2.5 segmento
Quando UNH2.5 está presente no documento EDIFACT, está a ser utilizado para a pesquisa de esquema. 

Exemplo: O campo UNH é **EAN008** na mensagem EDIFACT  
UNH + SSDD1 + ORDENS: D: 03B: ANULAR:**EAN008**'  

Passos a seguir para processar a mensagem 
1. Atualizar o esquema
2. Verifique as definições de contrato  

## <a name="update-the-schema"></a>Atualizar o esquema
Para processar a mensagem, terá de implementar um esquema com o nome de nó de raiz UNH2.5.  Para um exemplo tendo em conta, o nome de raiz de esquema seria **EFACT_D03B_ORDERS_EAN008**  

Para cada D03B_ORDERS com um segmento UNH2.5 diferente, terá de implementar um esquema individuais.  

## <a name="add-schema-to-the-edifact-agreement"></a>Adicionar o esquema para o contrato de EDIFACT
### <a name="edifact-decode"></a>EDIFACT descodificar
Descodificar a mensagem a receber, configure o esquema no EDIFACT contrato receber definições
1. Adicionar o esquema para a conta de integração    
2. Configurar o esquema do EDIFACT contrato recebem as definições. 
3. Selecione contratos EDIFACT e clique em **editar como JSON**.  Adicione o valor UNH2.5 no contrato de receber **schemaReferences**
![](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image1.png)

### <a name="edifact-encode"></a>EDIFACT codificar
Para codificar a mensagem a receber, configure o esquema nas definições de envio de contratos EDIFACT
1. Adicionar o esquema para a conta de integração    
2. Configure as definições de envio de contratos EDIFACT o esquema. 
3. Selecione contratos EDIFACT e clique em **editar como JSON**.  Adicione o valor UNH2.5 no contrato de enviar **schemaReferences**
![](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image2.png)

## <a name="next-steps"></a>Passos Seguintes
* [Saiba mais sobre contratos de conta de integração](../logic-apps/logic-apps-enterprise-integration-agreements.md "Saiba mais sobre contratos de integração do enterprise")  