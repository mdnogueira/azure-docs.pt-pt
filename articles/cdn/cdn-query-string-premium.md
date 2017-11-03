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
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 145067c2ce50b41c4783f4de4052c0e7cb529fc7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="control-azure-cdn-caching-behavior-with-query-strings---premium"></a>Controlo do Azure CDN comportamento com cadeias de consulta - Premium a colocação em cache
> [!div class="op_single_selector"]
> * [Standard](cdn-query-string.md)
> * [CDN do Azure Premium da Verizon](cdn-query-string-premium.md)
> 
> 

## <a name="overview"></a>Descrição geral
Cadeia de consulta controla a forma como os ficheiros estão a ser colocados em cache quando estas contêm cadeias de consulta a colocação em cache.

> [!IMPORTANT]
> Os produtos Standard e Premium CDN fornecem a mesma cadeia de consulta funcionalidade a colocação em cache, mas a interface de utilizador diferente.  Este documento descreve a interface para **CDN do Azure Premium da Verizon**.  Para a cache de cadeia de consulta com **CDN do Azure Standard da Akamai** e **CDN do Azure Standard da Verizon**, consulte [controlar o comportamento de colocação em cache da CDN pedidos com cadeias de consulta](cdn-query-string.md).
> 
> 

Estão disponíveis três modos de:

* **cache de padrão**: Este é o modo predefinido.  O nó de extremidade CDN passarão a cadeia de consulta do requerente para a origem no primeiro pedido e cache o elemento.  Todos os pedidos subsequentes para esse recurso são servidos a partir do nó de extremidade irão ignorar a cadeia de consulta, até que o elemento em cache expira.
* **cache não**: neste modo, os pedidos com cadeias de consulta não estão em cache no nó de extremidade CDN.  O nó de extremidade obtém o elemento diretamente a partir da origem e passa-a para o requerente com cada pedido.
* **cache exclusivo**: neste modo processa cada pedido com uma cadeia de consulta como um recurso com a sua própria cache exclusivo.  Por exemplo, a resposta da origem para um pedido para *foo.ashx?q=bar* teria de ser colocadas em cache no nó de extremidade e devolvido para caches subsequentes com essa mesma cadeia de consulta.  Um pedido para *foo.ashx?q=somethingelse* deverá ser colocado em cache como um recurso separado com o seu próprio tempo TTL.

## <a name="changing-query-string-caching-settings-for-premium-cdn-profiles"></a>Alterar as definições de perfis da CDN premium a colocação em cache de cadeia de consulta
1. No painel de perfil da CDN, clique o **gerir** botão.
   
    ![Botão de gerir do painel do perfil da CDN](./media/cdn-query-string-premium/cdn-manage-btn.png)
   
    É aberto o portal de gestão do CDN.
2. Coloque o cursor sobre o **HTTP grande** separador, em seguida, coloque o cursor sobre o **definições da Cache** flyout.  Clique em **colocação em cache de cadeia de consulta**.
   
    Opções de colocação em cache de cadeias de consulta são apresentadas.
   
    ![Cadeia de consulta CDN opções a colocação em cache](./media/cdn-query-string-premium/cdn-query-string.png)
3. Depois de efetuar a seleção, clique em de **atualização** botão.

> [!IMPORTANT]
> As alterações de definições podem não ser imediatamente visíveis como demora algum tempo para que o registo propagar pela CDN.  Para os perfis <b>CDN do Azure da Verizon</b>, a propagação normalmente fica concluída no prazo de 90 minutos, mas em certos casos pode demorar mais tempo.
> 
> 

