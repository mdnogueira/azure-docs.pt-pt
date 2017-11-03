---
title: "Suporte de dados de transmissão em fluxo otimização através da rede de entrega de conteúdo do Azure"
description: "Otimizar os ficheiros de multimédia de transmissão em fluxo para entrega uniforme"
services: cdn
documentationcenter: 
author: smcevoy
manager: erikre
editor: 
ms.assetid: 
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: v-semcev
ms.openlocfilehash: 02cd0fe30a2a14f42a16ed12f714d496bbb23b36
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="media-streaming-optimization-via-the-azure-content-delivery-network"></a>Suporte de dados de transmissão em fluxo otimização através da rede de entrega de conteúdo do Azure 
 
Aumento da utilização de vídeo de alta definição na Internet, que cria dificuldades para entrega eficiente de ficheiros grandes. Os clientes esperam uniforme reprodução de vídeo a pedido ou em direto a recursos de vídeos numa variedade de clientes e redes em todo o mundo. Um mecanismo de entrega rápidos e eficientes para suporte de dados de ficheiros de transmissão em fluxo é essencial para garantir uma experiência de consumidor enjoyable e uniforme.  

Suporte de dados de transmissão em fluxo em direto é particularmente difíceis de fornecer devido ao tamanhos grandes e o número de visualizadores autorizados em simultâneo. Grandes atrasos fazer com que os utilizadores a sair. Uma vez fluxos em direto não não possível colocar em cache antecedência e latências grande não são aceitáveis para os espetadores, tem de ser entregue fragmentos de vídeos de uma forma atempada. 

Os padrões de pedido de transmissão em fluxo também fornecem alguns novos desafios. Quando uma transmissão em fluxo em direto popular ou uma série nova for lançada de vídeo a pedido, milhares para milhões de visualizadores autorizados podem solicitar o fluxo ao mesmo tempo. Neste caso, a consolidação de pedido inteligente é vital para sobrecarregar não os servidores de origem quando os recursos não são colocadas em cache ainda.
 
Agora, a rede de entrega de conteúdos do Azure da Akamai oferece uma funcionalidade que fornece eficientemente recursos de suporte de dados de transmissão em fluxo para os utilizadores em todo o mundo à escala. A funcionalidade reduz as latências, dado que reduz a carga nos servidores de origem. Esta funcionalidade está disponível com o escalão de preço Standard da Akamai. 

A rede de entrega de conteúdos do Azure da Verizon fornece suporte de dados de transmissão em fluxo diretamente no tipo de otimização de entrega de web geral.
 
## <a name="configure-an-endpoint-to-optimize-media-streaming-in-the-azure-content-delivery-network-from-akamai"></a>Configurar um ponto final para otimizar o suporte de dados de transmissão em fluxo da rede de entrega de conteúdo do Azure da Akamai
 
Pode configurar o ponto final de entrega de conteúdos (CDN) de rede para otimizar a entrega de ficheiros grandes através do portal do Azure. Também pode utilizar os APIs REST ou de qualquer um dos SDKs de cliente para efetuar este procedimento. Os passos seguintes mostram o processo através do portal do Azure:

1. Para adicionar um novo ponto final no **perfil da CDN** página, selecione **Endpoint**.
  
    ![Novo ponto final](./media/cdn-media-streaming-optimization/01_Adding.png)

2. No **otimizado para** na lista pendente, selecione **vídeo no pedido suporte de dados de transmissão em fluxo** para recursos de vídeo a pedido. Se fizer uma combinação de em direto e, em seguida, transmissão em fluxo de vídeo a pedido, selecione **suporte de dados gerais de transmissão em fluxo**.

    ![Transmissão em fluxo selecionado](./media/cdn-media-streaming-optimization/02_Creating.png) 
 
Depois de criar o ponto final, se aplica a otimização para todos os ficheiros que correspondem a determinados critérios. A secção seguinte descreve este processo. 
 
## <a name="media-streaming-optimizations-for-the-azure-content-delivery-network-from-akamai"></a>Suporte de dados de transmissão em fluxo otimizações da rede de entrega de conteúdo do Azure da Akamai
 
Os fragmentos de suporte de dados de transmissão em fluxo a Otimização da Akamai está eficaz para direta ou suporte de transmissão em fluxo de vídeo a pedido que utiliza suportes de dados individuais para entrega. Este processo é diferente de um único recurso de grande transferido através de transferência progressiva ou através da utilização de pedidos de intervalo de bytes. Para informações sobre essa estilo de entrega de suporte de dados, consulte [otimização de ficheiros grandes](cdn-large-file-optimization.md).


Os suporte de dados gerais entrega ou as vídeo a pedido entrega otimização tipos de suporte utilizam um CDN com otimizações de back-end para fornecer recursos de suporte de dados mais rapidamente. Também utilizam configurações para recursos de suporte de dados com base nas melhores práticas aprendidas ao longo do tempo.

### <a name="caching"></a>Colocação em cache

Se a rede de entrega de conteúdos do Azure da Akamai Deteta que o elemento é um manifesto de transmissão em fluxo ou um fragmento, utiliza períodos de expiração de colocação em cache diferentes de entrega de web geral. (Consulte a lista completa na tabela seguinte). Como sempre, cache-control ou cabeçalhos expira enviados a partir de origem são cumpridos. Se o elemento não é um recurso de suporte de dados, coloca em cache utilizando os tempos de expiração para entrega web geral.

O tempo de colocação em cache negativo abreviado é útil para a descarga de origem de quando número de utilizadores que pedem um fragmento que ainda não existe. Um exemplo é uma transmissão em fluxo em direto onde os pacotes não estão disponíveis da origem esse segundo. O intervalo já é a colocação em cache também ajuda a descarregar pedidos da origem porque a conteúdos de vídeo, normalmente, não se encontra modificado.
 

|   | Entrega de web geral | Suporte de dados gerais de transmissão em fluxo | Suporte de dados de vídeo a pedido de transmissão em fluxo  
--- | --- | --- | ---
A colocação em cache: positivo <br> HTTP 200, 203, 300, <br> 301, 302 e 410 | 7 dias |365 dias | 365 dias   
A colocação em cache: negativo <br> HTTP 204, 305, 404, <br> e 405 | Nenhuma | 1 segundo | 1 segundo
 
### <a name="deal-with-origin-failure"></a>Lidar com falha de origem  

Entrega de multimédia de vídeo a pedido e entrega de multimédia geral também tem tempo limite de origem e um registo de repetição com base nas melhores práticas para padrões de pedido comuns. Por exemplo, uma vez que a entrega de multimédia geral destina-se em direto e entrega de multimédia de vídeo a pedido, utiliza um limite de tempo mais curto ligação devido à natureza sensíveis ao tempo de transmissão em direto.

Quando uma ligação exceder o tempo limite, a CDN repete um número de vezes antes de enviar um erro de "504 - tempo limite do Gateway" para o cliente. 

Quando um ficheiro corresponde à lista de condições de tipo e o tamanho de ficheiro, a CDN utiliza o comportamento para suporte de dados de transmissão em fluxo. Caso contrário, utiliza a entrega de web geral.
   
### <a name="conditions-for-media-streaming-optimization"></a>Condições para a otimização de transmissão em fluxo de suporte de dados 

A tabela seguinte lista o conjunto de critérios para ser satisfeito para otimização de transmissão em fluxo de suporte de dados: 
 
Tipos de transmissão em fluxo suportados | Extensões de ficheiro  
--- | ---  
Apple HLS | m3u8 m3u, m3ub, chave, ts, aac
Adobe HDS | f4m f4x, drmmeta, arranque, f4f,<br>Estrutura de seg Frag URL <br> (regex de correspondência: ^(/.*)Seq(\d+)-Frag(\d+)
TRAÇO | MPD, traço, divx, ismv, m4s, m4v, mp4, mp4v, <br> sidx webm, mp4a, m4a, isma
Transmissão em fluxo uniforme | o manifesto /, QualityLevels/fragmentos /
  

 
## <a name="media-streaming-optimizations-for-the-azure-content-delivery-network-from-verizon"></a>Suporte de dados de transmissão em fluxo otimizações para a rede de entrega de conteúdo do Azure da Verizon

A rede de entrega de conteúdos do Azure da Verizon fornece recursos de suporte de dados de transmissão em fluxo diretamente através do tipo de otimização de entrega web geral. Algumas funcionalidades da CDN diretamente ajudar na entrega recursos de suporte de dados por predefinição.

### <a name="partial-cache-sharing"></a>Partilha de cache parciais

Partilha de cache parciais permite que a CDN para servir parcialmente na cache de conteúdo para novos pedidos. Por exemplo, se o primeiro pedido para a CDN resulta numa falha de acerto na cache, o pedido é enviado para a origem. Embora este conteúdo incompleto é carregado para a cache CDN, outros pedidos para a CDN podem começar a obter estes dados. 

### <a name="cache-fill-wait-time"></a>Tempo de espera de preenchimento de cache

 A funcionalidade de tempo de espera de preenchimento de cache força o servidor edge para conter todos os pedidos subsequentes para o mesmo recurso, até que chegam cabeçalhos de resposta HTTP do servidor de origem. Se os cabeçalhos de resposta HTTP da origem chegam antes de expira o temporizador, todos os pedidos que foram colocar em espera são servidos fora da cache crescente. Ao mesmo tempo, a cache é preenchida por dados da origem. Por predefinição, o tempo de espera de preenchimento de cache é definido como 3,000 milissegundos. 

