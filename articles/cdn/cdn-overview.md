---
title: "Descrição geral do Azure CDN | Microsoft Docs"
description: "Saiba o que é a Rede de Entrega de Conteúdos (CDN) do Azure e como utilizá-la para fornecer conteúdo de largura de banda alta ao colocar em cache blobs e conteúdo estático."
services: cdn
documentationcenter: 
author: smcevoy
manager: akucer
editor: 
ms.assetid: 866e0c30-1f33-43a5-91f0-d22f033b16c6
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 02/08/2017
ms.author: v-semcev
ms.openlocfilehash: 909c4dc3feaeaedf56ecacc78f4b7e0e15d98875
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-the-azure-content-delivery-network-cdn"></a>Descrição geral da Rede de Entrega de Conteúdos (CDN) do Azure
> [!NOTE]
> Este documento descreve o que é a Rede de Entrega de Conteúdos (CDN) do Azure, como funciona e as funcionalidades de cada produto da CDN do Azure.  Se pretende ignorar estas informações e ir diretamente para um tutorial sobre como criar um ponto final da CDN, consulte [Utilizar a CDN do Azure](cdn-create-new-endpoint.md).  Se pretende ver uma lista das localizações de nós da CDN atual, consulte [Localizações de POP da CDN do Azure](cdn-pop-locations.md).
> 
> 

A Rede de Entrega de Conteúdos (CDN) do Azure coloca em cache conteúdo Web estático em localizações estrategicamente colocadas de modo a fornecer o débito máximo para disponibilização de conteúdo aos utilizadores.  A CDN oferece aos programadores uma solução global para o fornecimento de conteúdo de largura de banda alta ao colocar em cache o conteúdo em nós físicos por todo o mundo. 

As vantagens de utilizar a CDN para colocar em cache recursos de sites incluem:

* Melhor desempenho e experiência do utilizador para os utilizadores finais, especialmente quando se utilizam aplicações nas quais são necessárias vários percursos de ida e volta para carregar conteúdo.
* Grande dimensionamento para processar melhor cargas elevadas instantâneas, como no início de um evento de iniciação de um produto.
* Ao distribuir os pedidos de utilizador e publicar conteúdo a partir de servidores Edge, é enviado menos o tráfego para a origem.

## <a name="how-it-works"></a>Como funciona
![Descrição geral da CDN](./media/cdn-overview/cdn-overview.png)

1. Um utilizador (Alice) solicita um ficheiro (também denominado recurso) ao utilizar um URL com um nome de domínio especial, tal como `<endpointname>.azureedge.net`.  O DNS encaminha o pedido para a localização do Ponto de Presença (POP) com o melhor desempenho.  Normalmente, este é o POP que está mais próximo geograficamente do utilizador.
2. Se os servidores Edge no POP não têm o ficheiro na respetiva cache, o servidor Edge solicita o ficheiro da origem.  A origem pode ser uma aplicação Web do Azure, um Serviço em Nuvem do Azure, uma conta de Armazenamento do Azure ou qualquer servidor Web acessível publicamente.
3. A origem devolve o ficheiro ao servidor Edge, incluindo os cabeçalhos HTTP opcionais que descrevem o valor de TTL (time-to-live) do ficheiro.
4. O servidor Edge coloca em cache o ficheiro e devolve o ficheiro para o requerente original (Alice).  O ficheiro permanece em cache no servidor Edge até que o valor de TTL expire.  Se a origem não tiver especificado um valor de TTL, o TTL predefinido é de sete dias.
5. Os utilizadores adicionais podem solicitar o mesmo ficheiro utilizando esse URL e também podem ser direcionados para esse mesmo POP.
6. Se o valor de TTL para o ficheiro ainda não tiver expirado, o servidor Edge devolve o ficheiro da cache.  Isto traduz-se numa experiência de utilizador mais rápida e mais dinâmica.

## <a name="azure-cdn-features"></a>Funcionalidades da CDN do Azure
Existem três produtos da CDN do Azure: **CDN do Azure Standard da Akamai**, **CDN do Azure Standard da Verizon** e **CDN do Azure Premium da Verizon**.  A tabela seguinte lista as funcionalidades disponíveis com cada produto.

|  | Standard da Akamai | Standard da Verizon | Premium da Verizon |
| --- | --- | --- | --- |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;__Funções e Otimizações de Desempenho__ |
| [Aceleração de Site Dinâmico](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration) | **&#x2713;**  | **&#x2713;** | **&#x2713;** |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Aceleração de Site Dinâmico - Compressão de Imagem Adaptável](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#adaptive-image-compression-akamai-only) | **&#x2713;**  |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Aceleração de Site Dinâmico - Obtenção Prévia de Objeto](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#object-prefetch-akamai-only) | **&#x2713;**  |  |  |
| [Otimização da Transmissão em fluxo de Vídeo](https://docs.microsoft.com/azure/cdn/cdn-media-streaming-optimization) | **&#x2713;**  | \* |  \* |
| [Otimização de Ficheiros Grandes](https://docs.microsoft.com/azure/cdn/cdn-large-file-optimization) | **&#x2713;**  | \* |  \* |
| [Balanceamento de carga de Servidor Global (GSLB)](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-load-balancing-azure) |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Remoção rápida](cdn-purge-endpoint.md) |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Pré-carregamento de recursos](cdn-preload-endpoint.md) | |**&#x2713;** |**&#x2713;** |
| [Colocação em cache de cadeias de consulta](cdn-query-string.md) |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| Pilha dupla de IPv4/IPv6 |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Suporte HTTP/2](cdn-http2.md) |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; __Segurança__ |
| Suporta HTTPS com ponto final CDN |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [HTTPS de domínio personalizado](cdn-custom-ssl.md) | |**&#x2713;** |**&#x2713;** |
| [Suporte de nomes de domínio personalizado](cdn-map-content-to-custom-domain.md) |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Filtro geográfico](cdn-restrict-access-by-country.md) |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Autenticação de tokens](cdn-token-auth.md)|  |  |**&#x2713;**| 
| [Proteção contra DDOS](https://www.us-cert.gov/ncas/tips/ST04-015) |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;__Análises e Relatórios__ |
| [Análise principal](cdn-analyze-usage-patterns.md) | **&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Relatórios HTTP avançados](cdn-advanced-http-reports.md) | | |**&#x2713;** |
| [Estatísticas em tempo real](cdn-real-time-stats.md) | | |**&#x2713;** |
| [Alertas em tempo real](cdn-real-time-alerts.md) | | |**&#x2713;** |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; __Facilidade de Utilização__ |
| Integração fácil com os serviços Azure, tais como o [Armazenamento](cdn-create-a-storage-account-with-cdn.md), [Serviços Cloud](cdn-cloud-service-with-cdn.md), [Aplicações Web](../app-service/app-service-web-tutorial-content-delivery-network.md) e [Serviços de Multimédia](../media-services/media-services-portal-manage-streaming-endpoints.md) |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| Gestão através da [API REST](https://msdn.microsoft.com/library/mt634456.aspx), do [.NET](cdn-app-dev-net.md), do [Node.js](cdn-app-dev-node.md) ou do [PowerShell](cdn-manage-powershell.md). |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Motor de entrega de conteúdo personalizável com base em regras](cdn-rules-engine.md) | | |**&#x2713;** |
| Definições de cache/cabeçalho (utilizando o [motor de regras](cdn-rules-engine.md)) | | |**&#x2713;** |
| Redirecionar/reescrever URL (utilizando o [motor de regras](cdn-rules-engine.md)) | | |**&#x2713;** |
| Regras de dispositivos móveis (utilizando o [motor de regras](cdn-rules-engine.md)) | | |**&#x2713;** |

\*A Verizon suporta o fornecimento de ficheiros grandes e multimédia diretamente através do General Web Delivery.


> [!TIP]
> Existe uma funcionalidade que gostaria de ver na CDN do Azure?  [Envie-nos comentários](https://feedback.azure.com/forums/169397-cdn)! 
> 
> 

## <a name="next-steps"></a>Passos seguintes
Para começar a utilizar a CDN, consulte [Utilizar a CDN do Azure](cdn-create-new-endpoint.md).

Se for um cliente existente da CDN, já pode gerir os pontos finais da CDN através do [Portal do Microsoft Azure](https://portal.azure.com) ou com o [PowerShell](cdn-manage-powershell.md).

Para ver o CDN em ação, consulte o [vídeo da nossa sessão de Compilação 2016](https://azure.microsoft.com/documentation/videos/build-2016-leveraging-the-new-azure-cdn-apis-to-build-wicked-fast-applications/).

Saiba como automatizar o CDN do Azure com [.NET](cdn-app-dev-net.md) ou [node. js](cdn-app-dev-node.md).

Para obter informações sobre preços, consulte [Preços da CDN](https://azure.microsoft.com/pricing/details/cdn/).

