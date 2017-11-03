---
title: "Pré-carregar recursos num ponto final do Azure CDN | Microsoft Docs"
description: "Saiba como carregar previamente conteúdo em cache num ponto final de CDN do Azure."
services: cdn
documentationcenter: 
author: smcevoy
manager: erikre
editor: 
ms.assetid: 5ea3eba5-1335-413e-9af3-3918ce608a83
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 1f2dcd9a91bb6e883cbef06373c1acd98bf8d45f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="pre-load-assets-on-an-azure-cdn-endpoint"></a>Pré-carregar recursos num ponto final da CDN do Azure
[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Por predefinição, ativos são pela primeira vez colocados em cache conforme forem necessários. Isto significa que o primeiro pedido de cada região poderá demorar mais tempo, uma vez que os servidores edge não terão o conteúdo em cache e será necessário reencaminhar o pedido para o servidor de origem. Pré-carregar conteúdo evita esta latência acessos primeiro.

Além de proporcionarem uma melhor experiência de cliente, pré-carregar os recursos em cache também pode reduzir o tráfego de rede no servidor de origem.

> [!NOTE]
> Pré-carregar recursos é útil para grandes eventos ou conteúdo que torna-se em simultâneo disponível para um grande número de utilizadores, tal como uma nova versão de filmes ou uma atualização de software.
> 
> 

Este tutorial orienta-o através da pré-carregamento de conteúdo em cache em todos os nós de limite da CDN do Azure.

## <a name="walkthrough"></a>Instruções
1. No [Portal do Azure](https://portal.azure.com), navegue para o perfil CDN com o ponto final que pretende carregar previamente.  É aberto o painel de perfil.
2. Clique no ponto final na lista.  É aberto o painel de ponto final.
3. No painel de ponto final CDN, clique no botão de carga.
   
    ![Painel de ponto final CDN](./media/cdn-preload-endpoint/cdn-endpoint-blade.png)
   
    É aberto o painel de carga.
   
    ![Painel de carga da CDN](./media/cdn-preload-endpoint/cdn-load-blade.png)
4. Introduza o caminho completo de cada recurso que pretende carregar (por exemplo, `/pictures/kitten.png`) no **caminho** caixa de texto.
   
   > [!TIP]
   > Mais **caminho** caixas de texto serão apresentada depois de introduzir texto para permitir-lhe criar uma lista de vários recursos.  Pode eliminar os recursos na lista ao clicar no botão de reticências (…).
   > 
   > Caminhos tem de ser um URL relativo, que se adeque às seguintes [expressão regular](https://msdn.microsoft.com/library/az24scfc.aspx):  
   > >Carregar um caminho de ficheiro único `@"^(?:\/[a-zA-Z0-9-_.%=\u0020]+)+$"`;  
   > >Carregar um único ficheiro com a cadeia de consulta`@"^(?:\?[-_a-zA-Z0-9\/%:;=!,.\+'&\u0020]*)?$";`  
   > 
   > Cada elemento tem de ter o suas próprias caminho.  Não há nenhuma funcionalidade de caráter universal para recursos de pré-ao carregar.
   > 
   > 
   
    ![Botão de carga](./media/cdn-preload-endpoint/cdn-load-paths.png)
5. Clique em de **carga** botão.
   
    ![Botão de carga](./media/cdn-preload-endpoint/cdn-load-button.png)

> [!NOTE]
> Há uma limitação de pedidos de carga de 10 por minuto por perfil da CDN. 50 caminhos são permitidos por pedido. Cada caminho tem um limite de comprimento do caminho de 1024 carateres.
> 
> 

## <a name="see-also"></a>Consultar também
* [Remover um ponto final de CDN do Azure](cdn-purge-endpoint.md)
* [Referência da API de REST de CDN do Azure - remover ou carregar previamente um ponto final](https://msdn.microsoft.com/library/mt634451.aspx)

