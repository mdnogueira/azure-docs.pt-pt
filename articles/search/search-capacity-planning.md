---
title: Planeamento da capacidade para a Azure Search | Microsoft Docs
description: "Ajuste a partição e réplica recursos de computador na Azure Search, onde cada recurso tem um preço em unidades de pesquisa sujeito a faturação."
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: 1dc16afe-56f9-439d-8874-1733ae1a2b74
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 11/09/2017
ms.author: heidist
ms.openlocfilehash: 47dcd5366ef8ba3d4598e6d418b11997c61bddea
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2017
---
# <a name="scale-resource-levels-for-query-and-indexing-workloads-in-azure-search"></a>Níveis de recursos de escala de consulta e indexação cargas de trabalho na Azure Search
Depois de [escolha um escalão de preço](search-sku-tier.md) e [aprovisionar um serviço de pesquisa](search-create-service-portal.md), o passo seguinte consiste em opcionalmente aumentar o número de réplicas ou partições utilizadas pelo seu serviço. Cada camada oferece um número fixo de unidades de faturação. Este artigo explica como atribuir as unidades para alcançar uma configuração ideal equilibrar os requisitos para a execução da consulta, indexação e armazenamento.

Configuração do recurso está disponível quando configurar um serviço no [escalão básico](http://aka.ms/azuresearchbasic) ou um do [camadas Standard](search-limits-quotas-capacity.md). Para estas camadas de serviços, a capacidade é adquirida em incrementos de *unidades de pesquisa* (SUs) onde cada partição e réplica contagem como um SU. 

Utilizar menos SUs resultados numa fatura proporcionalmente inferior. Faturação destina-se em vigor, desde que o serviço está configurado. Se temporariamente não estiver a utilizar um serviço, é a única forma de evitar faturação por eliminar o serviço e, em seguida, voltar a criá-lo quando precisar dele.

> [!Note]
> Eliminar um serviço elimina tudo no mesmo. Não existe nenhuma das instalações dentro da Azure Search para cópias de segurança e restaurar dados de pesquisa persistentes. Para voltar a implementar um índice existente num novo serviço, deve executar o programa utilizado para criar e carregue-originalmente. 

## <a name="terminology-partitions-and-replicas"></a>Terminologia: as partições e réplicas
Partições e réplicas são os principais recursos que fazer uma cópia de um serviço de pesquisa.

| Recurso | Definição |
|----------|------------|
|*Partições* | Fornece armazenamento de índice e e/s para operações de leitura/escrita (por exemplo, quando reconstruir ou atualizar um índice).|
|*Réplicas* | Instâncias do serviço de pesquisa, principalmente utilizado para operações de consulta de balanceamento de carga. Cada réplica aloja sempre uma cópia de um índice. Se tiver 12 réplicas, terá de 12 cópias de cada índice carregado no serviço.|

> [!NOTE]
> Não há nenhuma forma de manipular ou gerir os índices executam numa réplica diretamente. Uma cópia de cada índice cada réplica faz parte da arquitetura do serviço.
>

## <a name="how-to-allocate-partitions-and-replicas"></a>Como alocar partições e réplicas
Na Azure Search, um serviço inicialmente é atribuído um nível mínimo de recursos constituída por uma partição e uma réplica. Para as camadas de que o suportem, pode ajustar incrementalmente recursos informáticos aumento partições se precisar de mais armazenamento e e/s ou adicionar mais réplicas de volumes maiores de consulta ou um melhor desempenho. Um único serviço tem de ter recursos suficientes para processar todas as cargas de trabalho (indexação e consultas). Não é possível subdividir cargas de trabalho entre vários serviços.

Para aumentar ou alterar a alocação de réplicas e partições, recomendamos que utilize o portal do Azure. O portal impõe limites permitidos combinações que permanecem abaixo os limites máximos:

1. Iniciar sessão para o [portal do Azure](https://portal.azure.com/) e selecione o serviço de pesquisa.
2. No **definições**, abra o **escala** painel e utilizar os controlos de deslize para aumentar ou diminuir o número de partições e réplicas.

Se necessitar de uma abordagem de aprovisionamento baseado em script ou código, o [API de REST de gestão](https://docs.microsoft.com/rest/api/searchmanagement/services) é uma alternativa ao portal.

Geralmente, pesquisa necessário as aplicações mais réplicas de partições, especialmente quando as operações de serviço são totalmente direcionadas para cargas de trabalho de consulta. A secção em [elevada disponibilidade](#HA) explica o motivo.

> [!NOTE]
> Depois de um serviço é aprovisionado, não pode ser atualizado para um SKU superior. Tem de criar um serviço de pesquisa na nova camada e recarregar os índices. Consulte [criar um serviço da Azure Search no portal do](search-create-service-portal.md) para obter ajuda com o aprovisionamento de serviço.
>
>

<a id="HA"></a>

## <a name="high-availability"></a>Elevada disponibilidade
Como é fácil e relativamente rápida para aumentar verticalmente, recomendamos, geralmente, que começa com uma partição e um ou criar duas réplicas e, em seguida, dimensionamento, segurança, como volumes de consulta. Executam cargas de trabalho de consulta principalmente nas réplicas. Se precisar de mais débito ou elevada disponibilidade, provavelmente venha a necessitar réplicas adicionais.

Recomendações gerais para elevada disponibilidade são:

* Duas réplicas para elevada disponibilidade de só de leitura cargas de trabalho (consultas)
* Três ou mais réplicas para elevada disponibilidade de cargas de trabalho de leitura/escrita (consultas mais indexação como documentos individuais são adicionados, atualizados ou eliminados)

Contratos de nível de serviço (SLA) de pesquisa do Azure são direcionados para operações de consulta e em atualizações de índice que consiste em Adicionar, atualizar ou eliminar documentos.

O escalão básico tária uma partição e de três réplicas. Se pretender que a flexibilidade para responder imediatamente a flutuações em termos de débito de indexação e consultas, considere um dos escalões Standard.

### <a name="index-availability-during-a-rebuild"></a>Índice de disponibilidade durante uma reconstrução

Elevada disponibilidade para a Azure Search diz respeito ao consultas e atualizações de índice que não envolvem a reconstruir um índice. Se eliminar um campo, alterar um tipo de dados ou mudar o nome de um campo, terá de recriar o índice. Para reconstruir o índice, tem de eliminar o índice, recrie o índice e recarregar os dados.

> [!NOTE]
> Pode adicionar novos campos a um índice da Azure Search sem reconstrua o índice. O valor do campo novo irá ser nulo para todos os documentos já no índice.

Para manter a disponibilidade de índice durante uma reconstrução, tem de ter uma cópia do índice com um nome diferente no mesmo serviço, ou uma cópia do índice com o mesmo nome num serviço diferente e, em seguida, forneça o redirecionamento ou lógica de ativação pós-falha no seu código.

## <a name="disaster-recovery"></a>Recuperação após desastre
Atualmente, não há nenhum mecanismo incorporado para recuperação após desastre. Adicionar partições ou réplicas seria a estratégia de errado para cumprir os objetivos de recuperação após desastre. A abordagem mais comuns é adicionar redundância ao nível de serviço ao configurar um serviço de pesquisa segundo noutra região. Tal como acontece com disponibilidade durante uma reconstrução do índice, o redirecionamento de lógica de ativação pós-falha tem uma combinação ou a partir do código.

## <a name="increase-query-performance-with-replicas"></a>Aumentar o desempenho de consulta com réplicas
A latência de consulta é um indicador de que as réplicas adicionais são necessárias. Geralmente, o primeiro passo para melhorar o desempenho da consulta consiste em adicionar mais este recurso. À medida que adiciona réplicas, cópias adicionais do índice são colocadas online para suportar cargas de trabalho de consulta maiores e carregar equilibrar os pedidos de ativação pós-falha de várias réplicas.

Não é possível fornecemos estimativas de disco rígidas em consultas por segundo (QPS): consulta desempenho depende da complexidade da consulta e cargas de trabalho concorrentes. Embora a adição de réplicas claramente resulta numa melhor desempenho, o resultado não é linear estritamente: adicionar três réplicas garante triplo débito.

Para obter orientações sobre como fazer uma estimativa QPS para as cargas de trabalho, consulte [considerações de desempenho e a Otimização da Azure Search](search-performance-optimization.md).

## <a name="increase-indexing-performance-with-partitions"></a>Aumentar o desempenho de indexação com partições
Procurar aplicações que necessitam de perto a atualização de dados em tempo real serão necessário proporcionalmente mais partições que as réplicas. Adicionar partições propaga operações de leitura/escrita num grande número de recursos de computação. Também proporciona mais espaço em disco para armazenar adicionais índices e documentos.

Índices maiores demorar mais tempo a consulta. Como tal, poderá achar que cada aumento incremental em partições requer um aumento de menor mas proporcional réplicas. A complexidade das suas consultas e o volume de consulta irá ter em conta no rapidez a execução de consultas está ativada em torno.

## <a name="basic-tier-partition-and-replica-combinations"></a>O escalão básico: combinações de partição e réplica
Um serviço básico pode ter exatamente uma partição e até três réplicas, para um máximo limite três SUs. O recurso apenas ajustável está réplicas. É necessário um mínimo de duas réplicas para elevada disponibilidade em consultas.

<a id="chart"></a>

## <a name="standard-tiers-partition-and-replica-combinations"></a>Os escalões Standard: combinações de partição e réplica
Esta tabela mostra os SUs necessários para suportar combinações de réplicas e partições, sujeita a limite de 36 SU, para todos os escalões Standard.

|   | **1 partição** | **2 partições** | **3 partições** | **4 partições** | **6 partições** | **12 partições** |
| --- | --- | --- | --- | --- | --- | --- |
| **1 réplica** |1 SU |2 SU |3 SU |4 SU |6 SU |12 SU |
| **2 réplicas** |2 SU |4 SU |6 SU |8 SU |12 SU |24 SU |
| **3 réplicas** |3 SU |6 SU |9 SU |12 SU |18 SU |36 SU |
| **4 réplicas** |4 SU |8 SU |12 SU |16 SU |24 SU |N/D |
| **5 réplicas** |5 SU |10 SU |15 DE SU |20 SU |30 SU |N/D |
| **6 réplicas** |6 SU |12 SU |18 SU |24 SU |36 SU |N/D |
| **12 réplicas** |12 SU |24 SU |36 SU |N/D |N/D |N/D |

SUs, o preço e a capacidade são explicados em detalhe no site do Azure. Para obter mais informações, consulte [detalhes dos preços](https://azure.microsoft.com/pricing/details/search/).

> [!NOTE]
> O número de réplicas e de partições uniformemente divide em 12 (especificamente, 1, 2, 3, 4, 6, 12). Isto acontece porque a Azure Search divide previamente cada índice em 12 shards para que podem ser distribuído em partes iguais em todas as partições. Por exemplo, se o serviço tem três partições e criar um índice, cada partição irá conter quatro shards do índice. Como shards da Azure Search um índice é um detalhe de implementação, sujeita a alterações em versões futuras. Apesar do número de 12 hoje em dia, não deve esperar esse número sempre ser 12 no futuro.
>
>

## <a name="billing-formula-for-replica-and-partition-resources"></a>Fórmula de faturação para recursos de partição e réplica
A fórmula para calcular o SUs quantos são utilizados para combinações específicas é o produto de réplicas e partições, ou (P X de R = SU). Por exemplo, três réplicas multiplicadas pelo três partições é faturada como nove SUs.

Custo por SU é determinado pelo escalão, com uma velocidade de faturação por unidade inferior para básico que para Standard. Classifica para cada camada pode ser encontrada no [detalhes dos preços](https://azure.microsoft.com/pricing/details/search/).
