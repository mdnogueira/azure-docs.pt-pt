---
title: "Como migrar Logic Apps para o esquema versão 2015-08-01-pré-visualização | Microsoft Docs"
description: "Pode migrar facilmente as Logic Apps para a versão mais recente do esquema. Basta seguir estes passos."
services: logic-apps
documentationcenter: 
author: MSFTMAN
manager: erikre
editor: 
tags: connectors
ms.assetid: 3e177e49-fd69-43e9-9b9b-218abb250c31
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/23/2016
ms.author: deonhe
ms.openlocfilehash: a5a73a9f124e5339b61dbc49021444a208a471f0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-migrate-logic-apps-to-schema-version-2015-08-01-preview"></a>Como migrar Logic Apps para o esquema versão 2015-08-01-pré-visualização
Para mover as Logic Apps existentes para o novo esquema, efetue o seguinte:  

1. Abra a aplicação lógica no Portal do Azure  
2. Clique em Atualizar Esquema:
   
   ![Ícone de API][step1]   
   A página Atualizar Esquema apresenta e fornece uma ligação para um documento que fornece detalhes sobre as melhorias efetuadas no novo esquema: ![Ícone de API][step2]

> [!NOTE]
> Ao selecionar **Atualizar Esquema**, os passos de migração são automaticamente executados e o código de saída é fornecido. Pode utilizar este código para atualizar a definição. No entanto, certifique-se de que segue as melhores práticas de codificação, tal como as descritas na secção **Melhores práticas** abaixo.
> 
> 

## <a name="best-practices-when-migrating-your-logic-apps-to-the-latest-schema-version"></a>Melhores práticas para migrar as Logic Apps para a versão mais recente do esquema:
* Copie o script migrado para uma nova Aplicação Lógica, não substitua o antigo até ter concluído os testes e ter confirmado que a aplicação migrada funciona conforme esperado.
* Teste a aplicação lógica **antes** de passar para a produção
* Após concluir a migração, comece a atualizar as Logic Apps para utilizar as [APIs geridas](apis-list.md) sempre que possível. Por exemplo, pode começar a utilizar o Dropbox v2, sempre que estiver a utilizar o DropBox v1.

## <a name="whats-next"></a>Passos seguintes
* [Saiba como migrar manualmente as Logic Apps](../logic-apps/logic-apps-schema-2015-08-01.md)

<!--Icon references-->
[step1]: ./media/connectors-schema-migration/migrateschema1.png
[step2]: ./media/connectors-schema-migration/migrateschema2.png






