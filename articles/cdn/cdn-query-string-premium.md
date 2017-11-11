---
title: "Controlar o comportamento com cadeias de consulta - Premium a colocação em cache a CDN do Azure | Microsoft Docs"
description: "Cadeia de consulta do Azure CDN controla como os ficheiros estão a ser colocados em cache quando estas contêm cadeias de consulta a colocação em cache."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 99db4a85-4f5f-431f-ac3a-69e05518c997
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: mazha
ms.openlocfilehash: ba9c28f0e6df25b101b45edf836d0b95056cbc6f
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2017
---
# <a name="control-azure-content-delivery-network-caching-behavior-with-query-strings---premium"></a>Controlo do Azure conteúdo rede de entrega de colocação em cache comportamento com cadeias de consulta - Premium
> [!div class="op_single_selector"]
> * [Standard](cdn-query-string.md)
> * [CDN do Azure Premium da Verizon](cdn-query-string-premium.md)
> 
> 

## <a name="overview"></a>Descrição geral
Com o Azure entrega rede conteúdos (CDN), pode controlar a forma como os ficheiros são colocadas em cache para um pedido web que contém uma cadeia de consulta. Um pedido web com uma cadeia de consulta, a cadeia de consulta é que parte do pedido que ocorre após o `?` caráter. Uma cadeia de consulta pode conter um ou mais parâmetros que são separados por um `&` caráter. Por exemplo, `http://www.domain.com/content.mov?data1=true&data2=false`. Se existir mais do que um parâmetro de cadeia de consulta num pedido, não importa a ordem dos parâmetros. 

> [!IMPORTANT]
> Os produtos da CDN standard e premium fornecem a mesma cadeia de consulta funcionalidade a colocação em cache, mas a interface de utilizador é diferente.  Este artigo descreve a interface para **CDN do Azure Premium da Verizon**. Para a cache de cadeia de consulta com **CDN do Azure Standard da Akamai** e **CDN do Azure Standard da Verizon**, consulte [controlar o comportamento de colocação em cache da CDN pedidos com cadeias de consulta](cdn-query-string.md).
>

Estão disponíveis três modos de cadeia de consulta:

- **cache de padrão**: modo predefinido. Neste modo, o nó de extremidade CDN transmite as cadeias de consulta do requerente para a origem no primeiro pedido e coloca em cache o elemento. Todos os pedidos subsequentes para o elemento que são servidos do nó de extremidade ignorar as cadeias de consulta até que o elemento em cache expira.
- **cache não**: neste modo, os pedidos com cadeias de consulta não estão em cache no nó de extremidade CDN. O nó de extremidade obtém o elemento diretamente a partir da origem e passa-a para o requerente com cada pedido.
- **cache exclusivo**: neste modo, a cada pedido com um URL único, incluindo a cadeia de consulta é tratado como um recurso com a sua própria cache exclusivo. Por exemplo, a resposta da origem para um pedido para `example.ashx?q=test1` é colocado em cache no nó de extremidade e devolvido para caches subsequentes com a mesma cadeia de consulta. Um pedido para `example.ashx?q=test2` é colocado em cache como um recurso separado com a sua própria definição time-to-live.

## <a name="changing-query-string-caching-settings-for-premium-cdn-profiles"></a>Alterar as definições de perfis da CDN premium a colocação em cache de cadeia de consulta
1. Abrir um perfil CDN, em seguida, clique em **gerir**.
   
    ![Botão de gerir do perfil de CDN](./media/cdn-query-string-premium/cdn-manage-btn.png)
   
    É aberto o portal de gestão do CDN.
2. Coloque o cursor sobre o **HTTP grande** separador, em seguida, coloque o cursor sobre o **definições da Cache** flyout menu. Clique em **colocação em cache de cadeia de consulta**.
   
    Opções de colocação em cache de cadeias de consulta são apresentadas.
   
    ![Cadeia de consulta CDN opções a colocação em cache](./media/cdn-query-string-premium/cdn-query-string.png)
3. Selecione um modo de cadeia de consulta, em seguida, clique em **atualização**.

> [!IMPORTANT]
> Uma vez demora algum tempo para que o registo propagar pela CDN, alterações de definições de cadeia de cache poderão não ser imediatamente visíveis. Para **CDN do Azure Premium da Verizon** perfis, propagação normalmente concluir dentro de 90 minutos, mas em alguns casos pode demorar mais tempo.
 

