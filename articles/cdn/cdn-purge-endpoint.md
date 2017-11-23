---
title: Remover um ponto final de CDN do Azure | Microsoft Docs
description: "Saiba como remover todos os conteúdos em cache a partir de um ponto final de CDN do Azure."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 0b50230b-fe82-4740-90aa-95d4dde8bd4f
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: b6157ddaf320f942a704d32f066b821425596308
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2017
---
# <a name="purge-an-azure-cdn-endpoint"></a>Remover um ponto final de CDN do Azure
## <a name="overview"></a>Descrição geral
Nós de limite CDN do Azure irão colocar em cache ativos até que time-to-live (TTL) o elemento de expirar.  Após TTL o elemento expira, quando um cliente solicita o elemento de nó de extremidade, o nó de extremidade irá obter uma nova cópia atualizada do elemento para servir o pedido de cliente e arquivo atualizar a cache.

A melhor prática para se certificar de que os utilizadores obtenham sempre a cópia mais recente dos seus ativos é a versão os recursos para cada atualização e publicá-los como novos URLs.  CDN imediatamente irá obter os recursos de novo para os pedidos de cliente seguintes.  Por vezes, pode pretender remover o conteúdo em cache de todos os nós de limite e forçar todos eles obter novos recursos atualizados.  Isto pode dever-se a atualizações à sua aplicação web, ou a rapidamente os recursos de atualização que contêm informações incorretas.

> [!TIP]
> Tenha em atenção que apenas remover limpa os conteúdos em cache em servidores do limite do CDN.  Qualquer caches a jusante, tais como servidores de proxy e caches local do browser, ainda podem manter uma cópia do ficheiro em cache.  É importante lembrar-se de isto quando definir um ficheiro time-to-live.  Pode forçar um cliente a jusante para pedir a versão mais recente do ficheiro, atribua um nome exclusivo sempre que efetua a atualização ou ao tirar partido da [colocação em cache de cadeia de consulta](cdn-query-string.md).  
> 
> 

Este tutorial orienta-o através da remoção de recursos de todos os nós de limite de um ponto final.

## <a name="walkthrough"></a>Instruções
1. No [Portal do Azure](https://portal.azure.com), navegue para o perfil CDN com o ponto final que pretende remover.
2. No painel de perfil CDN, clique no botão de remoção.
   
    ![Painel do perfil da CDN](./media/cdn-purge-endpoint/cdn-profile-blade.png)
   
    É aberto o painel de remoção.
   
    ![Painel de remoção CDN](./media/cdn-purge-endpoint/cdn-purge-blade.png)
3. No painel de remoção, selecione o endereço de serviço que pretende remover da lista pendente de URL.
   
    ![Remover o formulário](./media/cdn-purge-endpoint/cdn-purge-form.png)
   
   > [!NOTE]
   > Também pode aceder ao painel de remoção clicando a **remover** botão no painel de ponto final de CDN.  Nesse caso, o **URL** campo serão pré-preenchidos com o endereço do serviço desse ponto final específico.
   > 
   > 
4. Selecione os recursos que pretende remover a partir de nós edge.  Se pretende limpar todos os recursos, clique em de **remover** caixa de verificação.  Caso contrário, escreva o caminho de cada recurso que pretende remover no **caminho** caixa de texto. Abaixo formatos são suportadas no caminho.
    1. **Remoção de URL único**: remover recurso individual, especificando o URL completo, com ou sem a extensão de ficheiro, por exemplo,`/pictures/strasbourg.png`;`/pictures/strasbourg`
    2. **Remoção de carateres universais**: asterisco (\*) pode ser utilizado como um caráter universal. Remover todas as pastas, subpastas e ficheiros de um ponto final com `/*` no caminho ou remoção de todas as subpastas e ficheiros na pasta específica, especificando a pasta seguido `/*`, por exemplo,`/pictures/*`.  Tenha em atenção que remoção de carateres universais não é suportada pelo Azure CDN da Akamai atualmente. 
    3. **Remoção de domínio de raiz**: remover a raiz do ponto final com "/" no caminho.
   
   > [!TIP]
   > Os caminhos tem de ser especificados para remoção e tem de ser um URL relativo, que se adequam às seguintes [expressão regular](https://msdn.microsoft.com/library/az24scfc.aspx). **Remover todos os** e **remoção de carateres universais** não suportados pela **CDN do Azure da Akamai** atualmente.
   > > Remoção de URL único`@"^\/(?>(?:[a-zA-Z0-9-_.%=\(\)\u0020]+\/?)*)$";`  
   > > Cadeia de consulta`@"^(?:\?[-\@_a-zA-Z0-9\/%:;=!,.\+'&\(\)\u0020]*)?$";`  
   > > Remoção de carateres universais `@"^\/(?:[a-zA-Z0-9-_.%=\(\)\u0020]+\/)*\*$";`. 
   > 
   > Mais **caminho** caixas de texto serão apresentada depois de introduzir texto para permitir-lhe criar uma lista de vários recursos.  Pode eliminar os recursos na lista ao clicar no botão de reticências (…).
   > 
5. Clique em de **remover** botão.
   
    ![Remover botão](./media/cdn-purge-endpoint/cdn-purge-button.png)

> [!IMPORTANT]
> Pedidos de remoção demorar cerca de 2 a 3 minutos para processar com **CDN do Azure da Verizon** (Standard e Premium) e aproximadamente 7 minutos com **CDN do Azure da Akamai**.  CDN do Azure tem um limite de 50 simultâneas remover pedidos em qualquer momento ao nível do perfil. 
> 
> 

## <a name="see-also"></a>Consultar também
* [Pré-carregar recursos num ponto final da CDN do Azure](cdn-preload-endpoint.md)
* [Referência da API de REST de CDN do Azure - remover ou carregar previamente um ponto final](https://msdn.microsoft.com/library/mt634451.aspx)

