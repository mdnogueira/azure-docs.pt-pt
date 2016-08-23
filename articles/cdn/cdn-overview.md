<properties
    pageTitle="Descrição geral do Azure CDN | Microsoft Azure"
    description="Saiba o que é a Rede de Entrega de Conteúdos (CDN) do Azure e como utilizá-la para fornecer conteúdo de largura de banda alta ao colocar em cache blobs e conteúdo estático."
    services="cdn"
    documentationCenter=""
    authors="camsoper"
    manager="erikre"
    editor=""/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="07/28/2016"
    ms.author="casoper"/>

# Descrição geral da Rede de Entrega de Conteúdos (CDN) do Azure

> [AZURE.NOTE] Este documento descreve o que é a Rede de Entrega de Conteúdos (CDN) do Azure, como funciona e as funcionalidades de cada produto da CDN do Azure.  Se pretende ignorar estas informações e ir diretamente para um tutorial sobre como criar um ponto final da CDN, consulte [Utilizar a CDN do Azure](cdn-create-new-endpoint.md).  Se pretende ver uma lista das localizações de nós da CDN atual, consulte [Localizações de POP da CDN do Azure](cdn-pop-locations.md).

A Rede de Entrega de Conteúdos (CDN) do Azure coloca em cache conteúdo Web estático em localizações estrategicamente colocadas de modo a fornecer o débito máximo para disponibilização de conteúdo aos utilizadores.  A CDN oferece aos programadores uma solução global para o fornecimento de conteúdo de largura de banda alta ao colocar em cache o conteúdo em nós físicos por todo o mundo. 

As vantagens de utilizar a CDN para colocar em cache recursos de sites incluem:

- Melhor desempenho e experiência do utilizador para os utilizadores finais, especialmente quando se utilizam aplicações nas quais são necessárias vários percursos de ida e volta para carregar conteúdo.
- Grande dimensionamento para processar melhor cargas elevadas instantâneas, como no início de um evento de iniciação de um produto.
- Ao distribuir os pedidos de utilizador e publicar conteúdo a partir de servidores Edge, é enviado menos o tráfego para a origem.


## Como funciona

![Descrição geral da CDN](./media/cdn-overview/cdn-overview.png)

1. Um utilizador (Alice) solicita um ficheiro (também denominado recurso) ao utilizar um URL com um nome de domínio especial, tal como `<endpointname>.azureedge.net`.  O DNS encaminha o pedido para a localização do Ponto de Presença (POP) com o melhor desempenho.  Normalmente, este é o POP que está mais próximo geograficamente do utilizador.

2. Se os servidores Edge no POP não têm o ficheiro na respetiva cache, o servidor Edge solicita o ficheiro da origem.  A origem pode ser uma aplicação Web do Azure, um Serviço em Nuvem do Azure, uma conta de Armazenamento do Azure ou qualquer servidor Web acessível publicamente.

3. A origem devolve o ficheiro ao servidor Edge, incluindo os cabeçalhos HTTP opcionais que descrevem o valor de TTL (time-to-live) do ficheiro.

4. O servidor Edge coloca em cache o ficheiro e devolve o ficheiro para o requerente original (Alice).  O ficheiro permanece em cache no servidor Edge até que o valor de TTL expire.  Se a origem não tiver especificado um valor de TTL, o TTL predefinido é de 7 dias.

5. Os utilizadores adicionais (como Bernardo) podem, então, solicitar o mesmo ficheiro ao utilizar esse mesmo URL e também podem ser direcionados para esse mesmo POP.

6. Se o valor de TTL para o ficheiro ainda não tiver expirado, o servidor Edge devolve o ficheiro da cache.  Isto traduz-se numa experiência de utilizador mais rápida e mais dinâmica.


## Funcionalidades da CDN do Azure

Existem três produtos da CDN do Azure: **CDN do Azure Standard da Akamai**, **CDN do Azure Standard da Verizon** e **CDN do Azure Premium da Verizon**.  A tabela seguinte lista as funcionalidades disponíveis com cada produto.

|       | Standard da Akamai | Standard da Verizon | Premium da Verizon |
|-------|-----------------|------------------|-----------------|
| Integração fácil com os serviços Azure, tais como o [Armazenamento](cdn-create-a-storage-account-with-cdn.md), [Serviços Cloud](cdn-cloud-service-with-cdn.md), [Aplicações Web](../app-service-web/cdn-websites-with-cdn.md) e [Serviços de Multimédia](../media-services/media-services-manage-origins.md#enable_cdn) | **&#x2713;** | **&#x2713;** | **&#x2713;**|
| Suporte de HTTPS | **&#x2713;** | **&#x2713;** | **&#x2713;** |
| Balanceamento de carga | **&#x2713;** | **&#x2713;** | **&#x2713;** |
| Proteção contra DDOS | **&#x2713;** | **&#x2713;** | **&#x2713;** |
| Pilha dupla de IPv4/IPv6 | **&#x2713;** | **&#x2713;** | **&#x2713;** |
| [Suporte de nomes de domínio personalizado](cdn-map-content-to-custom-domain.md) | **&#x2713;** | **&#x2713;** | **&#x2713;** |
| [Colocação em cache de cadeia de consulta](cdn-query-string.md) | **&#x2713;** | **&#x2713;** | **&#x2713;** |
| [Filtragem de país](cdn-restrict-access-by-country.md) |  | **&#x2713;** | **&#x2713;** |
| [Remoção rápida](cdn-purge-endpoint.md) | **&#x2713;** | **&#x2713;** | **&#x2713;** |
| [Pré-carregamento de recursos](cdn-preload-endpoint.md) |  | **&#x2713;** | **&#x2713;** |
| [Análise de núcleo](cdn-analyze-usage-patterns.md) |  | **&#x2713;** | **&#x2713;** |
| [Gestão através da API de REST](https://msdn.microsoft.com/library/mt634456.aspx) | **&#x2713;** | **&#x2713;** | **&#x2713;** |
| [Motor de entrega de conteúdo personalizável com base em regras](cdn-rules-engine.md) | | | **&#x2713;** |
| [Relatórios avançados de HTTP](cdn-advanced-http-reports.md) | | | **&#x2713;** |
| [Estatísticas em tempo real](cdn-real-time-stats.md) | | | **&#x2713;** |

>[AZURE.TIP] Existe uma funcionalidade que gostaria de ver na CDN do Azure?  [Envie-nos comentários](https://feedback.azure.com/forums/169397-cdn)! 

## Passos seguintes

Para começar a utilizar a CDN, consulte [Utilizar a CDN do Azure](./cdn-create-new-endpoint.md).

Se for um cliente existente da CDN, já pode gerir os pontos finais da CDN através do [Portal do Microsoft Azure](https://portal.azure.com).

Para ver o CDN em ação, consulte o [vídeo da nossa sessão de Compilação 2016](https://azure.microsoft.com/documentation/videos/build-2016-leveraging-the-new-azure-cdn-apis-to-build-wicked-fast-applications/).

Saiba como automatizar o CDN do Azure com [.NET](./cdn-app-dev-net.md) ou [node. js](./cdn-app-dev-node.md).

Para obter informações sobre preços, consulte [Preços da CDN](https://azure.microsoft.com/pricing/details/cdn/).



<!--HONumber=Aug16_HO1-->


