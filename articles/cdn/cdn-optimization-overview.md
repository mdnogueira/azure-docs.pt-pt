---
title: "Otimizar a entrega de conteúdos do Azure para o seu cenário"
description: "Como otimizar o fornecimento de conteúdo para cenários específicos"
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
ms.openlocfilehash: 3544112b025f5df10e6f67c8e2e02f4bb587b4e0
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2017
---
# <a name="optimize-azure-content-delivery-for-your-scenario"></a>Otimizar a entrega de conteúdos do Azure para o seu cenário

Quando distribuir conteúdos a uma grande audiência global, é fundamental para garantir que a entrega otimizada do seu conteúdo. Rede de entrega de conteúdo do Azure pode otimizar a experiência de entrega com base no tipo de conteúdo que tem. O conteúdo pode ser um Web site, uma transmissão em fluxo em direto, um vídeo ou um ficheiro grande para transferência. Quando cria um ponto final de rede (CDN) de entrega de conteúdos, especifique um cenário no **otimizado para** opção. À sua escolha determina qual otimização é aplicada para o conteúdo a entregar a partir do ponto final de CDN.

Opções de otimização foram concebidas para utilizar os comportamentos de melhor prática para melhorar o desempenho de entrega de conteúdos e descarga de origem melhor. As opções de cenário afetam o desempenho ao modificar as configurações para a colocação em cache parcial, objeto de agrupamento e a política de repetição de falha de origem. 

Este artigo fornece uma descrição geral de diversas funcionalidades de Otimização e quando deve utilizá-los. Para obter mais informações sobre funcionalidades e limitações, consulte os respetivos artigos em cada tipo de otimização individuais.

> [!NOTE]
> O **otimizado para** opções podem variar consoante o fornecedor que selecionou. Fornecedores CDN aplicam melhoramento de diversas formas, dependendo do cenário. 

## <a name="provider-options"></a>Opções do fornecedor

A rede de entrega de conteúdos do Azure da Akamai suporta:

* Entrega geral Web 

* Transmissão geral de multimédia

* Suporte de dados de vídeo a pedido de transmissão em fluxo

* Transferência de ficheiro grande

* Aceleração de sites dinâmicos 

A rede de entrega de conteúdos do Azure da Verizon suporta apenas a entrega web geral. Pode ser utilizado para as vídeo a pedido e transferências de ficheiros grandes. Não tem de selecionar um tipo de otimização.

Recomendamos vivamente que teste variações de desempenho entre diferentes fornecedores para selecionar o fornecedor ideal para a entrega.

## <a name="select-and-configure-optimization-types"></a>Selecionar e configurar os tipos de otimização

Para criar um novo ponto final, selecione um tipo de otimização que descreva o cenário e tipo de conteúdo que pretende que o ponto final para fornecer. **Entrega de web geral** é a seleção predefinida. Pode atualizar a opção de otimização para qualquer ponto final da Akamai existente em qualquer altura. Esta alteração não interromper a entrega da CDN. 

1. Selecione um ponto final dentro de um perfil de Standard da Akamai.

    ![Seleção de ponto final ](./media/cdn-optimization-overview/01_Akamai.png)

2. Em **definições**, selecione **otimização**. Em seguida, selecione um tipo a partir de **otimizado para** na lista pendente.

    ![Seleção de Otimização e o tipo](./media/cdn-optimization-overview/02_Select.png)

## <a name="optimization-for-specific-scenarios"></a>Otimização para cenários específicos

Pode otimizar o ponto final da CDN para um dos seguintes cenários. 

### <a name="general-web-delivery"></a>Entrega geral Web

Entrega de web geral é a opção de otimização mais comuns. Foi concebido para a otimização de conteúdo web gerais, tais como páginas Web e aplicações web. Esta otimização também pode ser utilizada para o ficheiro e transferências de vídeo.

Um Web site típico contém conteúdo estático e dinâmico. Conteúdo estático inclui imagens, folhas de estilo que podem ser colocadas em cache e entregue aos diferentes utilizadores e bibliotecas de JavaScript. Conteúdo dinâmico é personalizado para um utilizador individual, tais como itens de notícias adaptados para um perfil de utilizador. Conteúdo dinâmico não está em cache porque é exclusivo para cada utilizador, como o conteúdo de carrinho compras. Entrega de geral web pode otimizar o seu site completo. 

> [!NOTE]
> Se utilizar a rede de entrega de conteúdos do Azure da Akamai, pode querer utilizar esta otimização se o tamanho de ficheiro médio for inferior a 10 MB. Se o tamanho de ficheiro médio for superior a 10 MB, selecione **transferências de ficheiros grandes** do **otimizado para** na lista pendente.

### <a name="general-media-streaming"></a>Transmissão geral de multimédia

Se precisar de utilizar o ponto final de transmissão em fluxo em direto e vídeo a pedido de transmissão em fluxo, recomendamos que suporte de dados gerais de otimização de transmissão em fluxo.

Suporte de dados de transmissão em fluxo está na altura confidencial, porque os pacotes que chegam tarde no cliente podem causar uma experiência de visualização degradada, tais como frequentes da memória intermédia de conteúdos de vídeo. Suporte de dados de transmissão em fluxo otimização reduz a latência de entrega de conteúdo do suporte de dados e proporciona uma experiência de transmissão em fluxo uniforme de utilizadores. 

Este cenário é comum para clientes do serviço de Media Services do Azure. Quando utilizar os Media Services do Azure, obter um ponto de final transmissão em fluxo que pode ser utilizado para a transmissão em direto e a pedido. Com este cenário, os clientes não precisam de mudar para outro ponto final alterar em direto para transmissão em fluxo a pedido. Otimização de transmissão em fluxo geral de suporte de dados suporta este tipo de cenário.

Rede de entrega de conteúdo do Azure da Verizon utiliza o tipo de otimização de entrega web geral para fornecer conteúdo de multimédia de transmissão em fluxo.

Para saber mais sobre a otimização de transmissão em fluxo de suporte de dados, consulte o artigo [suporte de dados de transmissão em fluxo otimização](cdn-media-streaming-optimization.md).

### <a name="video-on-demand-media-streaming"></a>Suporte de dados de vídeo a pedido de transmissão em fluxo

Otimização de transmissão em fluxo de suporte de dados de vídeo a pedido melhora o conteúdo de transmissão em fluxo de vídeo a pedido. Se utilizar um ponto final de transmissão em fluxo de vídeo a pedido, pode querer utilizar esta opção.

Rede de entrega de conteúdo do Azure da Verizon utiliza o tipo de otimização de entrega web geral para fornecer conteúdo de multimédia de transmissão em fluxo.

Para saber mais sobre a otimização de transmissão em fluxo de suporte de dados, consulte o artigo [suporte de dados de transmissão em fluxo otimização](cdn-media-streaming-optimization.md).

> [!NOTE]
> Se o ponto final serve principalmente conteúdo de vídeo a pedido, utilize este tipo de otimização. A principal diferença entre esta Otimização e o suporte de dados gerais otimização de transmissão em fluxo é o limite de tentativas de ligação. O limite de tempo é muito mais curto para trabalhar com cenários de transmissão em fluxo em direto.

### <a name="large-file-download"></a>Transferência de ficheiro grande

Se utilizar a rede de entrega de conteúdos do Azure da Akamai, tem de utilizar a transferência de ficheiros grandes para fornecer ficheiros maiores do que 1.8 GB. A rede de entrega de conteúdos do Azure da Verizon não tem uma limitação no ficheiro de tamanho na otimização de entrega web geral de transferir.

Se utilizar a rede de entrega de conteúdo do Azure da Akamai, transferências de ficheiros grandes estão otimizadas para o conteúdo com mais de 10 MB. Se o tamanho de ficheiro médio for inferior a 10 MB, pode querer utilizar entrega web geral. Se os tamanhos de média ficheiros forem consistentemente superiores a 10 MB, poderá ser mais eficiente para criar um ponto de final separado para ficheiros grandes. Por exemplo, atualizações de software ou firmware normalmente são ficheiros grandes.

Rede de entrega de conteúdo do Azure da Verizon utiliza o tipo de otimização de entrega de web geral para fornecer conteúdo de transferência de ficheiros grandes.

Para saber mais sobre a otimização de ficheiros grandes, consulte [otimização de ficheiros grandes](cdn-large-file-optimization.md).

### <a name="dynamic-site-acceleration"></a>Aceleração de sites dinâmicos

 Aceleração dinâmicas do site está disponível a partir da Akamai e rede de entrega de conteúdos da Verizon perfis. Esta otimização envolve uma taxa adicional a utilizar. Para obter mais informações, consulte a página de preços.

Aceleração dinâmicas do site inclui várias técnicas que beneficiam a latência e o desempenho de conteúdo dinâmico. Incluem técnicas de otimização de rota e da rede, a otimização de TCP e muito mais. 

Pode utilizar esta otimização para acelerar uma aplicação web que inclui várias respostas que não são colocáveis. Os exemplos são os resultados da pesquisa, transações de saída ou dados em tempo real. Pode continuar a utilizar funções de colocação em cache de CDN essenciais para os dados estáticos. 



