---
title: "Adicionar uma Rede de Entrega de Conteúdos ao Serviço de Aplicações do Azure | Microsoft Docs"
description: "Adicionar uma Rede de Entrega de Conteúdos ao Serviço de Aplicações do Azure para entregar os seus ficheiros estáticos a partir de nós de extremidade."
services: app-service
author: syntaxc4
ms.author: cfowler
ms.date: 04/03/2017
ms.topic: hero-article
ms.service: app-service-web
manager: erikre
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: 7ba3737566401152a3171e8926beca188045230c
ms.lasthandoff: 04/22/2017

---
# <a name="add-a-content-deliver-network-on-an-azure-app-service"></a>Adicionar uma Rede de Entrega de Conteúdos ao Serviço de Aplicações do Azure

Neste tutorial, vai adicionar uma Rede de Entrega de Conteúdos (CDN) ao seu Serviço de Aplicações do Azure para expor o conteúdo estático num servidor Edge. Vai criar um Perfil de CDN, que é uma coleção que inclui até dez Pontos Finais de CDN.

As Redes de Entrega de Conteúdos colocam em cache conteúdo Web estático em localizações estrategicamente colocadas, de modo a fornecer o débito máximo para disponibilização de conteúdo aos utilizadores. As vantagens de utilizar a CDN para colocar em cache recursos de sites incluem:

* Melhor desempenho e experiência do utilizador para os utilizadores finais, especialmente quando se utilizam aplicações nas quais são necessárias vários percursos de ida e volta para carregar conteúdo.
* Grande dimensionamento para processar melhor cargas elevadas instantâneas, como no início de um evento de iniciação de um produto.
* Ao distribuir os pedidos de utilizador e publicar conteúdo a partir de servidores Edge, é enviado menos o tráfego para a origem.

> [!TIP]
> Reveja a lista atualizada de [Azure CDN pop locations (Localizações pop da CDN do Azure)](https://docs.microsoft.com/en-us/azure/cdn/cdn-pop-locations).
>

## <a name="deploy-the-sample"></a>Implementar a Amostra

Para concluir este tutorial, precisa de uma aplicação implementada nas Aplicações Web. Siga o [manual de início rápido de HTML estático](app-service-web-get-started-html.md) como base para este tutorial.

## <a name="step-1---login-to-azure-portal"></a>Passo 1 - Iniciar sessão no Portal do Azure

Em primeiro lugar, abra o seu browser favorito e navegue para o [Portal](https://portal.azure.com) do Azure.

## <a name="step-2---create-a-cdn-profile"></a>Passo 2 - Criar um Perfil da CDN

Clique no botão `+ New`, na navegação do lado esquerdo, e clique em **Web + Móvel**. Na categoria Web + Móvel, selecione **CDN**.

Especifique os campos seguintes:

| Campo | Valor da amostra | Descrição |
|---|---|---|
| Nome | myCDNProfile | Um nome para o perfil de CDN. |
| Localização | Europa Ocidental | Esta é a localização do Azure onde as informações do seu perfil da CDN serão armazenadas. Esta ação não tem qualquer impacto sobre as localizações de ponto final da CDN. |
| Grupo de recursos | myResourceGroup | Para obter mais informações sobre os grupos de Recursos, veja [Descrição geral do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md#resource-groups). |
| Escalão de preço | Standard da Akamai | Veja [Descrição Geral da CDN](../cdn/cdn-overview.md#azure-cdn-features) para obter uma comparação dos escalões de preço. |

Clique em **Criar**.

Abra o concentrador de grupos de recursos na navegação do lado esquerdo e selecione **myResourceGroup**. A partir da lista de recursos, selecione **myCDNProfile**.

![azure-cdn-profile-created](media/app-service-web-tutorial-content-delivery-network/azure-cdn-profile-created.png)

## <a name="step-3---create-a-cdn-endpoint"></a>Passo 3 - Criar um Ponto Final da CDN

Clique em **+ Ponto Final** nos comandos ao lado da caixa de pesquisa, o que abre o painel Criação de ponto final.

Especifique os campos seguintes:

| Campo | Valor da amostra | Descrição |
|---|---|
| Nome |  | Este nome será utilizado para aceder aos seus recursos em cache no domínio `<endpointname>.azureedge.net` |
| Tipo de origem | Aplicação Web | Selecionar um tipo de origem proporciona-lhe menus de contexto para os campos restantes. Selecionar a origem personalizada disponibiliza-lhe um campo de texto para o nome de anfitrião da sua origem. |
| Nome de anfitrião da origem | |  A lista pendente apresentará todas as origens disponíveis do tipo que especificou. Se tiver selecionado Origem personalizada como o seu Tipo de origem, vai escrever no domínio da sua origem personalizada  |

Clique em **Adicionar**.

O Ponto Final da Rede de Entrega de Conteúdos é criado e o estado atualizado para **em execução**.

![azure-cdn-endpoint-created](media/app-service-web-tutorial-content-delivery-network/azure-cdn-endpoint-created.png)

## <a name="step-4---serve-from-azure-cdn"></a>Passo 4 - Servir a partir da CDN do Azure

Agora que o Ponto Final da CDN está em **execução**, deverá conseguir aceder aos conteúdos a partir do mesmo.

Tendo em conta que utilizámos o [manual de início rápido de HTML estático](app-service-web-get-started-html.md) como base para este tutorial, deveremos ter as pastas seguintes disponíveis na nossa CDN: `css`, `img` e `js`.

Os caminhos de conteúdo entre o URL das Aplicações Web, `http://<app_name>.azurewebsites.net/img/`, e o URL do Ponto Final da CDN, `http://<endpointname>.azureedge.net/img/`, são iguais, o que significa que pode simplesmente substituir o domínio do Ponto Final da CDN de qualquer conteúdo estático, para que seja servido a partir da CDN.

Vamos extrair a nossa primeira imagem a partir do Ponto Final da CDN, ao navegar para o URL seguinte no seu browser preferido:

```bash
http://<endpointname>.azureedge.net/img/03-enterprise.png
```

Agora que o conteúdo estático está disponível na CDN, pode atualizar a sua aplicação de modo a utilizar o Ponto Final da CDN para entregar o conteúdo ao utilizador final.

Consoante a linguagem em que o seu site foi criado, poderá haver muitas arquiteturas para ajudar à contingência de CDN. Por exemplo, ASP.NET disponibiliza suporte para [combinação (bundling) e minificação](https://docs.microsoft.com/en-us/aspnet/mvc/overview/performance/bundling-and-minification#using-a-cdn), que também permitem capacidades de contingência de CDN.

Se a sua linguagem não tiver uma biblioteca ou suporte incorporado para contingência de CDN incorporado, pode utilizar uma arquitetura de javascript, como [FallbackJS](http://fallback.io/), que suporta o carregamento de [scripts](https://github.com/dolox/fallback/tree/master/examples/loading-scripts), [folhas de estilo](https://github.com/dolox/fallback/tree/master/examples/loading-stylesheets) e [imagens](https://github.com/dolox/fallback/tree/master/examples/loading-images).

## <a name="step-5---purge-the-cdn"></a>5 - Limpar a CDN

Por vezes, poderá ser necessário forçar uma limpeza da CDN, se quiser terminar o conteúdos antes de o TTL (time-to-live) expirar.

É possível remover manualmente a CDN do Azure, a partir do painel Perfil de CDN ou do painel Ponto Final da CDN. Se selecionar a limpeza a partir da página Perfil, será necessário selecionar o ponto final a limpar.

Para limpar o conteúdo, escreva os caminhos do conteúdo que pretende limpar. Pode transmitir um caminho de ficheiro completo para limpar um ficheiro individual ou um segmento de caminho para limpar e atualizar o conteúdo numa determinada pasta.

Depois de indicar todos os caminhos do conteúdo que pretende limpar, clique em **Limpar**.

![app-service-web-purge-cdn](media/app-service-web-tutorial-content-delivery-network/app-service-web-purge-cdn.png)

## <a name="step-6---map-a-custom-domain"></a>Passo 6 - Mapear um domínio personalizado

Mapear um domínio personalizado para o ponto final da CDN proporciona um domínio uniforme para a sua aplicação Web.

De modo a mapear um domínio personalizado para o Ponto Final da CDN, crie um registo CNAME junto da sua entidade de registo de domínios.

> [!NOTE]
> Os registos CNAME são uma funcionalidade de DNS que mapeia domínios de origem, como `www.contosocdn.com` ou `static.contosocdn.com`, para um domínio de destino.

Neste caso, vamos adicionar o domínio de origem `static.contosocdn.com`, que vai apontar para o domínio de destino, que é o Ponto Final da CDN.

| domínio de origem | domínio de destino |
|---|---|
| static.contosocdn.com | &lt;endpointname&gt;.azureedge.net |

No painel de descrição geral do Ponto Final da CDN, clique no botão `+ Custom domain`.

No painel Adicionar domínio personalizado, introduza o seu domínio personalizado, incluindo o subdomínio, na caixa de diálogo. Por exemplo, introduza o nome de domínio no formato `static.contosocdn.com`.

Clique em **Adicionar**.

## <a name="step-7---version-content"></a>Passo 7 - Conteúdo de Versão

Na navegação do lado esquerdo do Ponto Final da CDN, selecione **Colocação em cache**, no cabeçalho Definições.

O painel **Colocação em cache** permite-lhe configurar de que forma é que a CDN lida com as cadeias de consulta do pedido.

> [!NOTE]
> Para obter uma descrição das opções de comportamento de colocação em cache da cadeia de consulta, leia o tópico [Control Azure CDN caching behavior with query strings](../cdn/cdn-query-string.md) (Controlar o comportamento de colocação em cache da CDN do Azure com cadeias de consulta).

Selecione **Colocar em cache todos os URL exclusivos** na lista pendente relativa ao comportamento de colocação em cache da Cadeia de consulta.

Clique em **Guardar**.

## <a name="next-steps"></a>Passos Seguintes

* [What is Azure CDN](../best-practices-cdn.md?toc=%2fazure%2fcdn%2ftoc.json) (O que é a CDN do Azure)
* [Enable HTTPS on an Azure CDN custom domain](../cdn/cdn-custom-ssl.md) (Ativar HTTPS num domínio personalizado da CDN do Azure)
* [Improve performance by compressing files in Azure CDN](../cdn/cdn-improve-performance.md) (Comprimir ficheiros na CDN do Azure para melhorar o desempenho)
* [Pré-carregar recursos num ponto final da CDN do Azure](../cdn/cdn-preload-endpoint.md)

