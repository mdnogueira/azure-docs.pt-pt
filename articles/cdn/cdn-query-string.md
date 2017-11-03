---
title: "Controlar o comportamento com cadeias de consulta a colocação em cache a CDN do Azure | Microsoft Docs"
description: "Cadeia de consulta do Azure CDN controla como os ficheiros estão a ser colocados em cache quando estas contêm cadeias de consulta a colocação em cache."
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
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 8d79626fa8516f226a82d3dac693c2033904c91d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="control-azure-cdn-caching-behavior-with-query-strings"></a>Controlo do Azure CDN comportamento com cadeias de consulta a colocação em cache
> [!div class="op_single_selector"]
> * [Standard](cdn-query-string.md)
> * [CDN do Azure Premium da Verizon](cdn-query-string-premium.md)
> 
> 

## <a name="overview"></a>Descrição geral
Cadeia de consulta controla a forma como os ficheiros estão a ser colocados em cache quando estas contêm cadeias de consulta a colocação em cache.

> [!IMPORTANT]
> Os produtos Standard e Premium CDN fornecem a mesma cadeia de consulta funcionalidade a colocação em cache, mas a interface de utilizador diferente.  Este documento descreve a interface para **CDN do Azure Standard da Akamai** e **CDN do Azure Standard da Verizon**.  Para a cache de cadeia de consulta com **CDN do Azure Premium da Verizon**, consulte [controlar o comportamento de colocação em cache da CDN pedidos com cadeias de consulta - Premium](cdn-query-string-premium.md).
> 
> 

Estão disponíveis três modos de:

* **Ignorar as cadeias de consulta**: Este é o modo predefinido.  O nó de extremidade CDN passarão a cadeia de consulta do requerente para a origem no primeiro pedido e cache o elemento.  Todos os pedidos subsequentes para esse recurso são servidos a partir do nó de extremidade irão ignorar a cadeia de consulta, até que o elemento em cache expira.
* **Ignorar a colocação em cache para URL com cadeias de consulta**: neste modo, os pedidos com cadeias de consulta não estão em cache no nó de extremidade CDN.  O nó de extremidade obtém o elemento diretamente a partir da origem e passa-a para o requerente com cada pedido.
* **Colocar em cache todos os URLs únicos**: neste modo processa cada pedido com uma cadeia de consulta como um recurso com a sua própria cache exclusivo.  Por exemplo, a resposta da origem para um pedido para *foo.ashx?q=bar* teria de ser colocadas em cache no nó de extremidade e devolvido para caches subsequentes com essa mesma cadeia de consulta.  Um pedido para *foo.ashx?q=somethingelse* deverá ser colocado em cache como um recurso separado com o seu próprio tempo TTL.

## <a name="changing-query-string-caching-settings-for-standard-cdn-profiles"></a>Alterar as definições de perfis da CDN padrão a colocação em cache de cadeia de consulta
1. No painel de perfil da CDN, clique o ponto final da CDN que pretende gerir.
   
    ![Pontos finais de painel de perfil CDN](./media/cdn-query-string/cdn-endpoints.png)
   
    Abre o painel de ponto final CDN.
2. Clique em de **configurar** botão.
   
    ![Botão de gerir do painel do perfil da CDN](./media/cdn-query-string/cdn-config-btn.png)
   
    Abre o painel de configuração da CDN.
3. Selecione uma definição do **comportamento de colocação em cache de cadeia de consulta** pendente.
   
    ![Cadeia de consulta CDN opções a colocação em cache](./media/cdn-query-string/cdn-query-string.png)
4. Depois de efetuar a seleção, clique em de **guardar** botão.

> [!IMPORTANT]
> As alterações de definições podem não ser imediatamente visíveis como demora algum tempo para que o registo propagar pela CDN.  Para os perfis <b>CDN do Azure da Akamai</b>, a propagação normalmente fica concluída num minuto.  Para os perfis <b>CDN do Azure da Verizon</b>, a propagação normalmente fica concluída no prazo de 90 minutos, mas em certos casos pode demorar mais tempo.
> 
> 

