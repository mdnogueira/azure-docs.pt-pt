---
title: "Otimização de transferência de ficheiros grandes através da rede de entrega de conteúdo do Azure"
description: "Otimização de transferências de ficheiros grandes explicado em profundidade"
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
ms.openlocfilehash: 7a5d5d1d0de24ebb0a5115ede1e572f38454bd78
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="large-file-download-optimization-via-the-azure-content-delivery-network"></a>Otimização de transferência de ficheiros grandes através da rede de entrega de conteúdo do Azure

Tamanhos de ficheiro de conteúdo a entregar através da Internet continuam a crescer devido a funcionalidade avançada, gráficos melhorados e conteúdo multimédia formatado. Este crescimento é suscitada pelo departamento por vários fatores: penetração banda larga, maior dispositivos de armazenamento económico, ampla aumento de definição de alta vídeo e ligado à Internet dispositivos (IoT). Um mecanismo de entrega rápidos e eficientes para ficheiros grandes é essencial para garantir uma experiência de consumidor enjoyable e uniforme.

Entrega de ficheiros grandes tem vários desafios. Em primeiro lugar, o tempo médio para transferir um ficheiro grande pode ser significativo porque as aplicações poderão não transferir todos os dados sequencialmente. Em alguns casos, as aplicações podem transferir a última parte de um ficheiro antes da primeira parte. Quando é pedida apenas uma pequena quantidade de um ficheiro ou um utilizador interrompe uma transferência, a transferência pode falhar. A transferência também pode ser adiada até depois da rede de entrega de conteúdos (CDN) obtém todo o ficheiro a partir do servidor de origem. 

Segundo, a latência entre a máquina de um utilizador e o ficheiro determina a velocidade a que podem visualizar os conteúdos. Além disso, problemas de congestionamento e a capacidade de rede também afetar o débito. As distâncias maiores entre servidores e os utilizadores criar adicionais oportunidades de perda de pacotes ocorrer, o que reduz a qualidade. A redução de qualidade causado por débito limitado e perda de pacotes de aumento poderá aumentar o tempo de espera para uma transferência de ficheiros concluir. 

Terceira, muitos ficheiros grandes não são fornecidos na sua totalidade. Os utilizadores poderão cancelar uma halfway através de transferência ou veja apenas os primeiro alguns minutos de um vídeo MP4 longo. Por conseguinte, as empresas de entrega de software e suportes de dados pretendem fornecer apenas a parte de um ficheiro que for solicitada. Distribuição eficiente as partes pedidas reduz o tráfego de saída do servidor de origem. Distribuição eficiente também reduz a memória e a pressão de e/s no servidor de origem. 

Agora, a rede de entrega de conteúdos do Azure da Akamai oferece uma funcionalidade que fornece eficientemente ficheiros grandes para os utilizadores em todo o mundo à escala. A funcionalidade reduz as latências, dado que reduz a carga nos servidores de origem. Esta funcionalidade está disponível com o escalão de preço Standard da Akamai.

## <a name="configure-a-cdn-endpoint-to-optimize-delivery-of-large-files"></a>Configurar um ponto final da CDN para otimizar a entrega de ficheiros grandes

Pode configurar o ponto final da CDN para otimizar a entrega de ficheiros grandes através do portal do Azure. Também pode utilizar os APIs REST ou de qualquer um dos SDKs de cliente para efetuar este procedimento. Os passos seguintes mostram o processo através do portal do Azure.

1. Para adicionar um novo ponto final no **perfil da CDN** página, selecione **Endpoint**.

    ![Novo ponto final](./media/cdn-large-file-optimization/01_Adding.png)  
 
2. No **otimizado para** na lista pendente, selecione **transferências de ficheiros grandes**.

    ![Otimização de ficheiros grandes selecionada](./media/cdn-large-file-optimization/02_Creating.png)


Depois de criar o ponto final de CDN, aplica-se as otimizações de ficheiros grandes para todos os ficheiros que correspondem a determinados critérios. A secção seguinte descreve este processo.

## <a name="optimize-for-delivery-of-large-files-with-the-azure-content-delivery-network-from-akamai"></a>Otimizar a entrega de ficheiros grandes, com a rede de entrega de conteúdo do Azure da Akamai

Ativa a funcionalidade de tipo de otimização de ficheiros grandes em otimizações de rede e configurações para fornecer a ficheiros grandes mais rápida e mais responsively. Entrega de web geral com Akamai coloca em cache de ficheiros apenas abaixo 1.8 GB e pode túnel (não cache) ficheiros até 150 GB. Caches de otimização de ficheiros grandes ficheiros até 150 GB.

A otimização de ficheiros grandes está em vigor quando forem satisfeitas determinadas condições. Condições incluem como funciona o servidor de origem e os tamanhos e tipos de ficheiros que são pedidos. Antes de obtemos para obter detalhes sobre estes assuntos, deve compreender como funciona a otimização. 

### <a name="object-chunking"></a>Objeto de agrupamento 

A rede de entrega de conteúdos do Azure da Akamai utiliza uma técnica chamada objeto de agrupamento. Quando é pedido um ficheiro grande, a CDN obtém partes mais pequenas do ficheiro de origem. Depois do servidor edge/POP do CDN recebe um pedido de ficheiro completo ou intervalo de bytes, verifica se o tipo de ficheiro é suportado para esta otimização. Também verifica se o tipo de ficheiro cumpre os requisitos de tamanho de ficheiro. Se o tamanho do ficheiro for superior a 10 MB, o servidor edge CDN solicita o ficheiro da origem em segmentos de 2 MB. 

Depois do segmento chega ao limite CDN, tem em cache e servidos imediatamente ao utilizador. A CDN prefetches, em seguida, o segmento seguinte em paralelo. Este obtenção prévia assegura que o conteúdo permanece um segmento antes do utilizador, o que reduz a latência. Este processo continua até que todo o ficheiro é transferido (se requerido), todos os intervalos de byte estão disponíveis (se requerido), ou o cliente termina a ligação. 

Para obter mais informações sobre o pedido de intervalo de bytes, consulte [RFC 7233](https://tools.ietf.org/html/rfc7233).

O CDN coloca em cache quaisquer segmentos como que está a ser recebidos. Todo o ficheiro não tem de ser colocadas em cache na CDN cache. Os pedidos subsequentes para os intervalos de bytes ou ficheiro são servidos da cache do CDN. Se não estiver todos os segmentos são colocadas em cache no CDN, obtenção prévia é utilizada para pedir segmentos da origem. Esta otimização depende a capacidade do servidor de origem para suportar pedidos de intervalo de bytes. _Se o servidor de origem não suporta pedidos de intervalo de bytes, esta otimização não está em vigor._ 

### <a name="caching"></a>Colocação em cache
Otimização de ficheiros grandes utiliza tempos de expiração de colocação em cache predefinido diferente da entrega de web geral. -Distingue entre a colocação em cache positivos e negativos colocação em cache baseado em códigos de resposta HTTP. Se o servidor de origem Especifica um tempo de expiração através de uma cache-control ou expira cabeçalho na resposta, a CDN honra esse valor. Quando não especifica a origem e o ficheiro corresponde as condições de tipo e o tamanho para este tipo de otimização, a CDN utiliza os valores predefinidos para a otimização de ficheiros grandes. Caso contrário, a CDN utiliza as predefinições para a entrega de web geral.


|    | Web geral | Otimização de ficheiros grandes 
--- | --- | --- 
A colocação em cache: positivo <br> HTTP 200, 203, 300, <br> 301, 302 e 410 | 7 dias |1 dia  
A colocação em cache: negativo <br> HTTP 204, 305, 404, <br> e 405 | Nenhuma | 1 segundo 

### <a name="deal-with-origin-failure"></a>Lidar com falha de origem

O período de tempo limite de leitura de origem aumenta de dois segundos para entrega de web geral para dois minutos para o tipo de otimização de ficheiros grandes. Este aumento contas para os tamanhos de ficheiro maior evitar uma ligação de tempo limite prematuro.

Quando uma ligação exceder o tempo limite, a CDN repete um número de vezes antes de enviar um erro de "504 - tempo limite do Gateway" para o cliente. 

### <a name="conditions-for-large-file-optimization"></a>Condições para a otimização de ficheiros grandes

A tabela seguinte lista o conjunto de critérios para ser satisfeito para a otimização de ficheiros grandes:

Condição | Valores 
--- | --- 
Tipos de ficheiro suportados | 3G 2 3gp, asf, avi, bz2, dmg,. exe, f4v, flv, <br> GZ hdp, iso, jxr, m4v, mkv, mov, mp4, <br> MPEG mpg, mts, pkg, qt, rm, swf, tar, <br> tgz, wdp, webm, webp, wma, wmv, zip  
Tamanho mínimo do ficheiro | 10 MB 
Tamanho máximo do ficheiro | 150 GB 
Características de servidor de origem | Tem de suportar pedidos de intervalo de bytes 

## <a name="optimize-for-delivery-of-large-files-with-the-azure-content-delivery-network-from-verizon"></a>Otimizar a entrega de ficheiros grandes, com a rede de entrega de conteúdo do Azure da Verizon

A rede de entrega de conteúdos do Azure da Verizon fornece ficheiros grandes sem um limite no tamanho do ficheiro. Funcionalidades adicionais são ativadas por predefinição para tornar mais rapidamente a entrega de ficheiros grandes.

### <a name="complete-cache-fill"></a>Preenchimento da cache concluída

A funcionalidade de preenchimento de cache completa de predefinição permite a CDN solicitar um ficheiro para a cache, quando um pedido inicial é abandonado ou perdido. 

Preenchimento da cache completa é mais útil para grandes ativos. Normalmente, os utilizadores não transferirem-los do início ao fim. Utilizam transferência progressiva. O comportamento predefinido força o servidor edge para iniciar a obtenção um fundo do elemento do servidor de origem. Posteriormente, o elemento é na cache local do servidor edge. Depois do objeto completo na cache, o servidor edge satisfaz os pedidos de intervalo de bytes para a CDN para o objeto em cache.

O comportamento predefinido pode ser desativado através do motor de regras no escalão Premium da Verizon.

### <a name="peer-cache-fill-hot-filing"></a>Arquivo de acesso frequente de preencher a cache ponto a ponto

A funcionalidade de arquivo de acesso frequente de preenchimento do predefinidos ponto a ponto cache utiliza um algoritmo proprietário sofisticado. Utiliza edge adicional com base na largura de banda de servidores de colocação em cache e agregado pede métricas para satisfazer os pedidos de cliente para objetos de grandes, altamente populares. Esta funcionalidade impede uma situação em que grande número de pedidos adicionais é enviado para o servidor de origem de um utilizador. 

### <a name="conditions-for-large-file-optimization"></a>Condições para a otimização de ficheiros grandes

As funcionalidades de Otimização da Verizon estão ativadas por predefinição. Existem não existem limites no tamanho máximo do ficheiro. 

## <a name="additional-considerations"></a>Considerações adicionais

Considere os seguintes aspetos adicionais para este tipo de otimização.
 
### <a name="azure-content-delivery-network-from-akamai"></a>Rede de entrega de conteúdos do Azure da Akamai

- O processo das secções gera pedidos adicionais para o servidor de origem. No entanto, o volume geral de entregar da origem de dados é muito menor. Os resultados das secções no melhor as características de colocação em cache, a CDN.

- Memória e pressão de e/s são reduzidos na origem porque são entregues partes mais pequenas do ficheiro.

- Para segmentos na cache, a CDN, existem não existem pedidos adicionais para a origem até expira o conteúdo ou se for expulso da cache.

- Os utilizadores podem efetuar pedidos de intervalo para a CDN e está a ser tratadas como qualquer ficheiro normal. Otimização aplica-se apenas se for um tipo de ficheiro válido e o intervalo de byte que se encontra entre 10 MB e 150 GB. Se o tamanho de ficheiro médio pedido for inferior a 10 MB, pode querer utilizar em vez disso, a entrega web geral.

### <a name="azure-content-delivery-network-from-verizon"></a>Rede de entrega de conteúdos do Azure da Verizon

O tipo de otimização de entrega geral web pode proporcionar ficheiros grandes.
