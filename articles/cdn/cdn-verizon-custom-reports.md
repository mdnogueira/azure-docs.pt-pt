---
title: "Relatórios personalizados da Verizon | Microsoft Docs"
description: "Pode ver os padrões de utilização para a CDN utilizando os seguintes relatórios: largura de banda, os dados transferidos, pedidos com êxito, os Estados da Cache, rácio de acertos na Cache, IPV4/IPV6 dados transferidos."
services: cdn
documentationcenter: 
author: dksimpson
manager: 
editor: 
ms.assetid: 
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2017
ms.author: v-deasim
ms.openlocfilehash: 8df9fd46fe3ce8d87e7ad5377a21a0bc7a458d2b
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2017
---
# <a name="custom-reports-from-verizon"></a>Relatórios personalizados da Verizon

[!INCLUDE[cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Através da Verizon personalizada relatórios através do portal de gerir para perfis da Verizon, pode definir o tipo de dados a serem recolhidos para relatórios de CNAMEs edge.


## <a name="accessing-verizon-custom-reports"></a>Aceder a relatórios personalizados da Verizon
1. No painel de perfil da CDN, clique o **gerir** botão.
   
    ![Botão de gerir do painel do perfil da CDN](./media/cdn-reports/cdn-manage-btn.png)
   
    É aberto o portal de gestão do CDN.
2. Coloque o cursor sobre o **análise** separador, em seguida, coloque o cursor sobre o **relatórios personalizados** flyout. Clique em **contorno CNAMEs**.
   
    ![Portal de gestão da CDN - menu de relatórios personalizados](./media/cdn-reports/cdn-custom-reports.png)

## <a name="edge-cnames-custom-report"></a>Relatório personalizado de CNAMES Edge
O relatório personalizado Edge CNAMES fornece pedidos e estatísticas de dados transferidos para CNAMEs edge no qual o registo de relatório personalizado foi ativado. Limite CNAMEs consistem em nomes de anfitrião de ponto final de CDN do Azure e qualquer hostnames associadas domínio personalizado. 

Registo de dados de relatório personalizado começa uma hora depois de ativar a capacidade de relatórios personalizada de um CNAME de limite. Pode ver os dados de relatórios através da geração de um relatório de CNAMEs de limite de uma plataforma específica ou para todas as plataformas. A cobertura para este relatório está limitada a CNAMEs o limite para a qual foram recolhidos dados de relatório personalizado durante o período de tempo especificado. O contorno CNAMEs relatório é composta por uma tabela de gráfico e os dados para o limite de 10 superior CNAMEs, de acordo com a métrica definida na opção de métricas. 

Gere um relatório personalizado, definindo as seguintes opções de relatório:

- Métricas: São suportadas as seguintes opções:

   - Pedidos: Indica o número total de pedidos que são direcionados para um CNAME edge no qual a capacidade de relatórios personalizada está ativada. Esta métrica não inclui o código de estado devolvido ao cliente.

   - Transferred de dados: Indica a quantidade total de dados transferidos a partir dos servidores edge para os clientes HTTP (por exemplo, browsers da web) para pedidos que são direcionados para um CNAME edge no qual a capacidade de relatórios personalizada está ativada. A quantidade de dados transferidos é calculada através da adição de cabeçalhos de resposta HTTP para o corpo da resposta. Como resultado, a quantidade de dados transferidos para cada recurso é superior ao tamanho real do ficheiro.

- Agrupamentos: Determina o tipo de estatísticas de que são mostradas abaixo o gráfico de barras. São suportadas as seguintes opções:

   - Códigos de resposta de HTTP: Organiza as estatísticas por código de resposta HTTP (por exemplo, 200, 403, etc.) devolvida ao cliente. 

   - Estado de cache: Organiza as estatísticas por Estado da cache.


Para definir o intervalo de datas para o relatório, pode selecionar um intervalo de datas predefinidos, tais como **hoje** ou **esta semana**, desde na lista pendente ou pode selecionar **personalizada** e introduza manualmente um intervalo de datas clicando os ícones de calendário. 

Depois de selecionar o intervalo de datas, clique em **aceda** para gerar o relatório.

Pode exportar os dados no formato de Excel clicando o símbolo de Excel à direita do **aceda** botão.

![Relatório de CNAMEs](./media/cdn-reports/cdn-cnames-report.png)

## <a name="edge-cnames-custom-report-fields"></a>Campos de relatório personalizado de CNAMES Edge

| Campo                     | Descrição   |
|---------------------------|---------------|
| 2xx                       | Indica o número total de pedidos ou dados transferidos (MB) para o limite CNAME que resulta num código de estado HTTP 2xx (por exemplo, 200 OK). |
| 3xx                       | Indica o número total de pedidos ou dados transferidos (MB) para o limite CNAME que resulta num código de estado HTTP 3xx (por exemplo, encontrar 302 ou 304 não modificado. |
| 4xx                       | Indica o número total de pedidos ou dados transferidos (MB) para o limite CNAME que resulta num código de estado HTTP 4xx (por exemplo, 400 pedido incorreto, 403 proibido ou 404 não encontrado). |
| 5XX                       | Indica o número total de pedidos ou dados transferidos (MB) para o limite CNAME que resulte num código de estado HTTP 5xx (por exemplo, 500 Erro interno do servidor ou Gateway incorreto 502). |
| % De acertos na cache               | Indica a percentagem de pedidos colocáveis que foram fornecidos diretamente a partir da cache para o autor do pedido. |
| Acertos na cache                | Indica o número total de pedidos ou dados transferidos (MB) para o limite CNAME que resulte num acertos na cache, (por exemplo, TCP_EXPIRED_HIT, TCP_HIT ou TCP_PARTIAL_HIT). Um acertos na cache de ocorre quando é encontrada uma versão em cache do conteúdo pedido. |
| Dados transferidos (MB)     | Indica a quantidade total de dados transferidos (MB) de servidores edge para clientes HTTP (browsers da web) para o CNAME do limite. A quantidade de dados transferidos é calculada através da adição de cabeçalhos de resposta de HTTP para o corpo da resposta. Como resultado, a quantidade de dados transferidos para cada recurso é superior ao tamanho real do ficheiro. |
| Descrição               | Identifica um limite de CNAME pelo respetivo nome de anfitrião |
| Pedidos com êxito                      | Indica o número total de pedidos na periferia CNAME |
| Falhas de acertos na                    | Indica o número total de pedidos ou dados transferidos (MB) para o limite CNAME que resulte numa falha de acerto na cache (por exemplo, TCP_CLIENT_REFRESH_MISS, TCP_EXPIRED_MISS ou TCP_MISS). Ocorre uma falha de acerto na cache quando o conteúdo solicitado não foi colocado em cache no servidor edge que cumpridas o pedido. | 
| Não existem Cache                  | Indica o número total de pedidos ou dados transferidos (MB) para o limite CNAME que resulta num código de estado CONFIG_NOCACHE cache.  |
| Outros                     | Indica o número total de pedidos ou os dados transferido (MB) para o limite CNAME indicado que resulta num código de estado HTTP que esteja fora 2xx - 5xx intervalo. |
| Plataforma                  | Indica a plataforma que processa o tráfego o CNAME edge. |
| Não atribuídos               | Indica o número total de pedidos ou dados transferidos (MB) para o CNAME do limite para que o código de estado de cache ou o código de estado HTTP informações não tenha sido registadas.  |
| Uncacheable               | Indica o número total de pedidos ou dados transferidos (MB) para o limite CNAME que resulte num código de estado de UNCACHEABLE cache.  |


## <a name="considerations"></a>Considerações
Relatórios podem ser gerados apenas dentro dos última 18 meses.

