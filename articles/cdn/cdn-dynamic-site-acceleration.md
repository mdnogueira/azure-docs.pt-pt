---
title: "Aceleração dinâmicas do Site através da CDN do Azure"
description: "Descrição detalhada da aceleração dinâmicas do site"
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
ms.date: 08/02/2017
ms.author: v-semcev
ms.openlocfilehash: be2719e0e02c8bc69800ef4a3e7da3c3164cb9dd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="dynamic-site-acceleration-via-azure-cdn"></a>Aceleração dinâmicas do Site através da CDN do Azure

Com explosão de redes sociais, comércio eletrónico e web hyper personalizadas, é gerada uma percentagem do conteúdo servido aos utilizadores finais rapidamente aumentar em tempo real. Os utilizadores esperam que experiências web personalizado, rápida e fiável, independentemente do respetivo browser, localização, dispositivo ou rede. No entanto, as inovações muito que fará com que estes experiências para exercer também lenta transferências de página e colocar a qualidade da experiência do consumidor em risco. 

Capacidade CDN Standard inclui a capacidade de ficheiros de cache próximo aos utilizadores finais para acelerar a entrega de ficheiros estáticos. No entanto, com aplicações web dinâmicas, esse conteúdo em localizações de limite a colocação em cache não é possível porque o servidor gera o conteúdo em resposta ao comportamento de utilizador. Acelerar o fornecimento desse conteúdo for mais complexo do que a colocação em cache de limite tradicional e necessita de uma solução ponto-a-ponto que lhe tunes cada elemento ao longo do caminho de dados completo de inception para entrega. Com o Azure CDN dinâmica Site aceleração (DSA), o desempenho das páginas web com o conteúdo dinâmico measurably foi melhorado.

CDN do Azure da Akamai e da Verizon oferece otimização DSA através de **otimizado para** menu durante a criação do ponto final.

## <a name="configuring-cdn-endpoint-to-accelerate-delivery-of-dynamic-files"></a>Configurar o ponto final da CDN para acelerar a entrega de ficheiros dinâmicos

Pode configurar o ponto final da CDN para otimizar a entrega de ficheiros dinâmicos através do portal do Azure ao selecionar o **aceleração dinâmicas do site** opção sob o **otimizado para** seleção de propriedade durante a criação do ponto final. Também pode utilizar os APIs REST ou de qualquer um dos SDKs de cliente para efetuar a mesma coisa através de programação. 

### <a name="probe-path"></a>Caminho de pesquisa
Caminho de pesquisa é uma funcionalidade específica aceleração dinâmico do Site e válido é necessário para a criação. DSA utiliza uma pequena *caminho de pesquisa* ficheiro colocada a origem para otimizar o encaminhamento as configurações de rede para a CDN. Pode transferir e carregar o nosso ficheiro de exemplo para o seu site ou utilizar um recurso existente na sua origem que é de aproximadamente 10 KB para o caminho de pesquisa em vez disso, se o recurso existe.

> [!Note]
> DSA incorreu encargos adicionais. Para obter mais informações, consulte o [página de preços](https://azure.microsoft.com/pricing/details/cdn/) para obter mais informações.

As capturas de ecrã seguintes mostram o processo através do portal do Azure.
 
![Adicionar um novo ponto final CDN](./media/cdn-dynamic-site-acceleration/01_Endpoint_Profile.png) 

*Figura 1: Adicionar um novo ponto final da CDN do perfil de CDN*
 
![Criar um novo ponto final CDN com DSA](./media/cdn-dynamic-site-acceleration/02_Optimized_DSA.png)  

*Figura 2: Criar um ponto final de CDN com aceleração dinâmicas do site otimização selecionada*

Assim que for criado o ponto final de CDN, aplica-se as otimizações de DSA para todos os ficheiros que correspondem a determinados critérios. A secção seguinte descreve otimização DSA em detalhe.

## <a name="dsa-optimization-using-azure-cdn"></a>Otimização do DSA utilizando CDN do Azure

Aceleração de Site dinâmico na CDN do Azure acelera a entrega de elementos dinâmicos utilizando as seguintes técnicas:

-   Otimização de rota
-   Otimizações de TCP
-   Obtenção prévia do objeto (Akamai apenas)
-   Compressão de imagem móveis (apenas Akamai)

### <a name="route-optimization"></a>Otimização de rota

Otimização de rota é importante porque a Internet é um local dinâmico, onde o tráfego e temporariamente falhas estão constantemente a topologia da rede. O Border Gateway Protocol (BGP) é o protocolo de encaminhamento de Internet, mas poderá rotas mais rápidas através de servidores de ponto de presença (PoP) intermediário. 

Otimização de rota escolhe o caminho ideal para a origem para que um site continuamente está acessível e dinâmico conteúdo é entregue aos utilizadores finais através da rota mais rápido e mais fiável possíveis. 

A rede da Akamai utiliza técnicas para recolher dados em tempo real e comparar vários caminhos através de diferentes nós no servidor de Akamai, bem como a rota BGP predefinida através da Internet aberta para determinar a rota mais rápida entre a origem e o limite CDN. Estes técnicas evitar Internet pontos de congestionamento e rotas de comprimento. 

Da mesma forma, as utilizações de rede da Verizon uma combinação de Anycast DNS, elevada capacidade suportam PoPs e verificações de estado de funcionamento para determinar os melhor gateways para dados da melhor rota do cliente para a origem.
 
Como resultado, completamente dinâmico e transacional conteúdo é entregue mais rapidamente e mais fiável para os utilizadores finais, mesmo quando é uncacheable. 

### <a name="tcp-optimizations"></a>Otimizações de TCP

Protocolo de controlo de transmissão (TCP) é o padrão do conjunto de protocolos de Internet utilizado para fornecer informações entre aplicações numa rede IP.  Por predefinição, existem várias anterior e descritos pedidos necessários para configurar uma ligação de TCP, bem como os limites para evitar congestions de rede, o que resultam numa inefficiencies à escala. CDN do Azure da Akamai lida com este problema por otimizar em três áreas: 

 - Eliminando início lento
 - Tirar partido das ligações persistentes
 - Otimização de parâmetros de pacotes TCP (Akamai apenas)

#### <a name="eliminating-slow-start"></a>Eliminando início lento

*Lenta início* faz parte do protocolo TCP que impede o congestionamento de rede ao limitar a quantidade de dados enviados através da rede. Começa com o tamanho de janela de congestionamento pequeno entre remetente e o recetor até que o máximo é alcançado ou perda de pacotes é detetada.

CDN do Azure da Akamai e da Verizon elimina lento início em três passos:

1.  Rede Akamai e da Verizon utilizar o estado de funcionamento e a monitorização de largura de banda para medir a largura de banda das ligações entre servidores de PoP edge.
2. As métricas são partilhadas entre servidores do edge PoP, para que cada servidor tem conhecimento das condições de rede e estado de funcionamento do servidor dos outros PoPs em torno deles.  
3. Os servidores de limite CDN agora são capazes de efetuar pressupostos sobre alguns parâmetros de transmissão, por exemplo, o que o tamanho ideal da janela deve ser ao comunicar com outros servidores edge CDN respetiva proximidade. Este passo significa que o tamanho da janela de congestionamento inicial pode ser aumentado, se o estado de funcionamento da ligação entre os servidores de limite CDN é capaz de transferências de dados de pacotes superiores.  

#### <a name="leveraging-persistent-connections"></a>Tirar partido das ligações persistentes

Utilizar uma CDN, menos máquinas exclusivas ligam ao seu servidor de origem em comparação comparada diretamente com os utilizadores ligados diretamente para a origem. CDN do Azure da Akamai e da Verizon agrupamentos também pedidos de utilizador em conjunto para estabelecer ligações menos com a origem.

Conforme mencionado anteriormente, ligações TCP demorar vários pedidos anterior e descritos num handshake de estabelecer uma ligação de novo. Ligações persistentes, também conhecido como "HTTP Keep-Alive," reutilizar as ligações TCP existentes para vários pedidos HTTP guardar vezes reportadas round-trip e acelerar a entrega. 

A rede da Verizon também envia pacotes ligação keep-alive periódicas através da ligação de TCP para impedir que uma ligação aberta de que está a ser fechado.

#### <a name="tuning-tcp-packet-parameters"></a>Parâmetros de pacotes TCP de otimização

Azure CDN da Akamai também tunes os parâmetros que regulam as ligações de servidor para servidor e reduz a quantidade de longa haul arredondar viagens necessárias para obter o conteúdo incorporados no site utilizando as seguintes técnicas:

1.  Aumentar a janela de congestionamento inicial, para que mais pacotes podem ser enviados sem aguardar por uma confirmação.
2.  Diminuir o tempo limite de retransmitir inicial, para que seja detetada uma perda e retransmissão ocorre mais rapidamente.
3.  Diminuir o tempo limite mínimo e máximo retransmitir para reduzir o tempo de espera antes de partindo do princípio de pacotes foram perdidos na transmissão.

### <a name="object-prefetch-akamai-only"></a>Obtenção prévia do objeto (Akamai apenas)

A maioria dos Web sites é constituída por uma página HTML, que referencia vários outros recursos, tais como imagens e scripts. Normalmente, quando um cliente solicita uma página Web, o browser transfere primeiro e analisa o objeto HTML e, em seguida, efetua pedidos adicionais aos elementos ligados que são necessárias para totalmente carregar a página. 

*Obtenção prévia* é uma técnica para obter imagens e scripts incorporada numa página HTML enquanto o HTML é fornecido para o browser e antes do browser mesmo torna estes pedidos de objeto. 

Com o **prefetch** opção ativada no momento quando funciona a CDN HTML base página browser do cliente, a CDN analisa o ficheiro HTML e disponibilizar pedidos adicionais para qualquer ligado recursos e armazená-las na respetiva cache. Quando o cliente efetua os pedidos para os recursos ligados, o servidor edge CDN é já tem os objectos solicitados e pode servi-los imediatamente sem uma ida e volta para a origem. Esta otimização beneficia conteúdos colocáveis e não colocáveis.

### <a name="adaptive-image-compression-akamai-only"></a>Compressão de imagem adaptável (Akamai apenas)

Alguns dispositivos, especialmente móveis aqueles, experiência de tempos a tempos mais lentas velocidades de rede. Nestes cenários, é mais vantajoso para ao utilizador receber mais rapidamente imagens inferior na sua página Web em vez de esperar muito tempo para imagens de resolução completa.

Esta funcionalidade automaticamente monitoriza qualidade da rede e utiliza os métodos padrão de compressão de JPEG quando velocidades de rede são mais lentas melhorar a hora de entrega.

Compressão de imagem adaptável | Extensões de ficheiro  
--- | ---  
Compressão de JPEG | . jpg, JPEG, .jpe, .jig, .jgig, .jgi

## <a name="caching"></a>Colocação em cache

Com DSA, colocação em cache está desativada por predefinição na CDN, mesmo quando a origem inclui cabeçalhos cache-controlo/expira na resposta. Esta predefinição está desativada porque DSA é normalmente utilizado para ativos dinâmicos que devem não ser colocados em cache, uma vez que são exclusivos para cada cliente, e ativar a colocação em cache por predefinição, pode interromper este comportamento.

Se tiver um Web site com uma combinação de recursos dinâmicos e estáticos, é melhor adotar uma abordagem de híbrida para obter o melhor desempenho. 

Se estiver a utilizar ADN com Premium da Verizon, pode ativar a colocação em cache reverter para casos específicos utilizando o motor de regras.  

Uma alternativa consiste em utilizar dois pontos finais da CDN. Um com DSA para fornecer recursos dinâmicos e de outro ponto final com um tipo de otimização estático, por exemplo, entrega web geral, a recursos colocáveis de entrega. Para realizar esta alternativa, vai modificar o URL de página Web para ligar diretamente para o elemento no ponto final da CDN que planeia utilizar. 

Por exemplo: `mydynamic.azureedge.net/index.html` é uma página dinâmica e é carregada a partir do ponto final de DSA.  Página html faz referência a vários recursos estáticos, tais como bibliotecas de JavaScript ou imagens que são carregadas a partir estático ponto final de CDN, tais como `mystatic.azureedge.net/banner.jpg` e `mystatic.azureedge.net/scripts.js`. 

Pode encontrar um exemplo [aqui](https://docs.microsoft.com/azure/cdn/cdn-cloud-service-with-cdn#controller) sobre como utilizar os controladores numa aplicação web ASP.NET para servir conteúdo através de um URL específico da CDN.




