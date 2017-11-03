---
title: Substituir o comportamento HTTP utilizando o motor de regras do Azure CDN | Microsoft Docs
description: "O motor de regras permite-lhe personalizar como os pedidos de HTTP são processados pela CDN do Azure, tais como o fornecimento de determinados tipos de conteúdo a bloquear, definir uma política de colocação em cache e modificar os cabeçalhos de HTTP."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 625a912b-91f2-485d-8991-128cc194ee71
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: abfe283476206b181018d187675b47112dc5ad2f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="override-http-behavior-using-the-azure-cdn-rules-engine"></a>Substituir o comportamento HTTP utilizando o motor de regras da CDN do Azure
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Descrição geral
O motor de regras permite-lhe personalizar a forma como os pedidos de HTTP são processados, como o fornecimento de determinados tipos de conteúdo a bloquear, definir uma política de colocação em cache e modificar os cabeçalhos de HTTP.  Este tutorial demonstrar criar uma regra que irá alterar o comportamento da cache de elementos da CDN.  Há também conteúdo de vídeo disponível na "[Consulte também](#see-also)" secção.

   > [!TIP] 
   > Para obter uma referência para a sintaxe em detalhe, consulte [regras motor referência](cdn-rules-engine-reference.md).
   > 


## <a name="tutorial"></a>Tutorial
1. No painel de perfil da CDN, clique o **gerir** botão.
   
    ![Botão de gerir do painel do perfil da CDN](./media/cdn-rules-engine/cdn-manage-btn.png)
   
    É aberto o portal de gestão do CDN.
2. Clique em de **HTTP grande** separador, seguido de **motor de regras**.
   
    São apresentadas as opções para uma nova regra.
   
    ![Novas opções de regra CDN](./media/cdn-rules-engine/cdn-new-rule.png)
   
   > [!IMPORTANT]
   > A ordem em que várias regras estão listadas afeta a forma como são processadas. Uma regra subsequente pode substituir as ações especificadas por uma regra de anterior.
   > 
   > 
3. Introduza um nome no **nome / descrição** caixa de texto.
4. Identifique o tipo de pedidos que a regra será aplicada a.  Por predefinição, o **sempre** está selecionada condição de correspondência.  Irá utilizar **sempre** para este tutorial, por isso deixe que selecionou.
   
   ![Condição de correspondência CDN](./media/cdn-rules-engine/cdn-request-type.png)
   
   > [!TIP]
   > Existem muitos tipos de correspondência de condições disponíveis na lista pendente.  Clicar no ícone azul informativo para a esquerda da condição correspondência explicar a condição selecionada atualmente em detalhe.
   > 
   >  Para obter a lista completa de expressões condicionais com detalhes, consulte [expressões condicionais do motor de regras](cdn-rules-engine-reference-match-conditions.md).
   >  
   > Para obter a lista completa das condições de correspondência em detalhe, consulte [condições de corresponder ao motor de regras](cdn-rules-engine-reference-match-conditions.md).
   > 
   > 
5. Clique em de  **+**  junto a **funcionalidades** para adicionar uma nova funcionalidade.  Na lista pendente no lado esquerdo, selecione **idade de máxima de interno Force**.  Na caixa de texto que aparece, introduza **300**.  Deixe os valores predefinidos restantes.
   
   ![Funcionalidade CDN](./media/cdn-rules-engine/cdn-new-feature.png)
   
   > [!NOTE]
   > Como com condições de correspondência, clicar no ícone informativo azul para a esquerda da nova funcionalidade apresentará detalhes sobre esta funcionalidade.  Em case de **idade de máxima de interno Force**, estamos a substituir o elemento **Cache-Control** e **expira** cabeçalhos para controlar quando o nó de extremidade CDN irá atualizar o recurso da origem.  Nosso exemplo de 300 segundos significa que o nó de extremidade da CDN serão colocados em cache o elemento para 5 minutos antes de atualizar o recurso de origem.
   > 
   > Para obter a lista completa das funcionalidades em detalhe, consulte [detalhes de funcionalidade do motor de regras](cdn-rules-engine-reference-features.md).
   > 
   > 
6. Clique em de **adicionar** botão para guardar a nova regra.  A nova regra agora está a aguardar aprovação. Quando for aprovado, o estado deixará de **XML pendente** para **XML Active Directory**.
   
   > [!IMPORTANT]
   > Alterações de regras podem demorar até 90 minutos para propagar pela CDN.
   > 
   > 

## <a name="see-also"></a>Consultar também
* [Descrição geral da CDN do Azure](cdn-overview.md)
* [Referência do motor de regras](cdn-rules-engine-reference.md)
* [Condições de correspondência do motor de regras](cdn-rules-engine-reference-match-conditions.md)
* [Expressões condicionais de motor de regras](cdn-rules-engine-reference-conditional-expressions.md)
* [Funcionalidades do motor de regras](cdn-rules-engine-reference-features.md)
* [Substituir o comportamento HTTP predefinido utilizando o motor de regras](cdn-rules-engine.md)
* [Azure sextas: Poderosas novo as funcionalidades Azure CDN Premium](https://azure.microsoft.com/documentation/videos/azure-cdns-powerful-new-premium-features/) (vídeo)