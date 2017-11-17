---
title: "Controlar Azure rede de entrega de conteúdos comportamento com cadeias de consulta a colocação em cache | Microsoft Docs"
description: "Cadeia de consulta do Azure CDN controla como os ficheiros são colocadas em cache quando estas contêm cadeias de consulta a colocação em cache."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 17410e4f-130e-489c-834e-7ca6d6f9778d
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: mazha
ms.openlocfilehash: 04c9ad5e58af073204eb6a16df96f0517a0ee668
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2017
---
# <a name="control-azure-content-delivery-network-caching-behavior-with-query-strings"></a>Controlo do Azure conteúdo rede de entrega de colocação em cache comportamento com cadeias de consulta
> [!div class="op_single_selector"]
> * [Standard](cdn-query-string.md)
> * [CDN do Azure Premium da Verizon](cdn-query-string-premium.md)
> 

## <a name="overview"></a>Descrição geral
Com o Azure entrega rede conteúdos (CDN), pode controlar a forma como os ficheiros são colocadas em cache para um pedido web que contém uma cadeia de consulta. Um pedido web com uma cadeia de consulta, a cadeia de consulta é que parte do pedido que ocorre após um ponto de interrogação (?). Uma cadeia de consulta pode conter um ou mais pares de valor de chave, em que o nome do campo e o respetivo valor são separados por um sinal de igual (=). Cada par chave-valor é separado por um e comercial (&). Por exemplo `http://www.contoso.com/content.mov?field1=value1&field2=value2`. Se existir mais do que um par chave-valor de uma cadeia de consulta de um pedido, não importa a sua ordem. 

> [!IMPORTANT]
> Os produtos da CDN standard e premium fornecem a mesma cadeia de consulta funcionalidade a colocação em cache, mas a interface de utilizador é diferente.  Este artigo descreve a interface para **CDN do Azure Standard da Akamai** e **CDN do Azure Standard da Verizon**. Para a cache de cadeia de consulta com **CDN do Azure Premium da Verizon**, consulte [controlar o comportamento de colocação em cache da CDN pedidos com cadeias de consulta - Premium](cdn-query-string-premium.md).

Estão disponíveis três modos de cadeia de consulta:

- **Ignorar as cadeias de consulta**: modo predefinido. Neste modo, o nó de extremidade CDN transmite as cadeias de consulta do requerente para a origem no primeiro pedido e coloca em cache o elemento. Todos os pedidos subsequentes para o elemento que são servidos do nó de extremidade ignorar as cadeias de consulta até que o elemento em cache expira.
- **Ignorar a colocação em cache para cadeias de consulta**: neste modo, os pedidos com cadeias de consulta não estão em cache no nó de extremidade CDN. O nó de extremidade obtém o elemento diretamente a partir da origem e passa-a para o requerente com cada pedido.
- **Colocar em cache todos os URLs únicos**: neste modo, a cada pedido com um URL único, incluindo a cadeia de consulta é tratado como um recurso com a sua própria cache exclusivo. Por exemplo, a resposta da origem para um pedido para `example.ashx?q=test1` é colocado em cache no nó de extremidade e devolvido para caches subsequentes com a mesma cadeia de consulta. Um pedido para `example.ashx?q=test2` é colocado em cache como um recurso separado com a sua própria definição time-to-live.

## <a name="changing-query-string-caching-settings-for-standard-cdn-profiles"></a>Alterar as definições de perfis da CDN padrão a colocação em cache de cadeia de consulta
1. Abrir um perfil CDN, em seguida, selecione o ponto final da CDN que pretende gerir.
   
   ![Pontos finais de perfil CDN](./media/cdn-query-string/cdn-endpoints.png)
   
2. Em definições, clique em **Cache**.
   
    ![Botão de Cache do perfil de CDN](./media/cdn-query-string/cdn-cache-btn.png)
   
3. No **comportamento de colocação em cache de cadeia de consulta** lista, selecione um modo de cadeia de consulta, em seguida, clique em **guardar**.
   
  <!--- Replace screen shot after general caching goes live ![CDN query string caching options](./media/cdn-query-string/cdn-query-string.png) --->

> [!IMPORTANT]
> Uma vez demora algum tempo para que o registo propagar pela CDN, alterações de definições de cadeia de cache poderão não ser imediatamente visíveis. Para os perfis **CDN do Azure da Akamai**, a propagação normalmente fica concluída num minuto. Para os perfis **CDN do Azure da Verizon**, a propagação normalmente fica concluída no prazo de 90 minutos, mas em certos casos pode demorar mais tempo.


