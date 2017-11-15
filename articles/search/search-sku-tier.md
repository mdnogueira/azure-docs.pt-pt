---
title: "Escolha um SKU ou escalão de preço para a Azure Search | Microsoft Docs"
description: "A pesquisa do Azure pode ser aprovisionada nestes SKUs: gratuita, básico e padrão, está disponível em vários níveis de capacidade e configurações de recursos onde padrão."
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: 8d4b7bca-02a5-43ee-b3f8-03551dfb32fd
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 10/24/2016
ms.author: heidist
ms.openlocfilehash: 781683f27c943e25d5629dd846da357f51c9d4f9
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2017
---
# <a name="choose-a-sku-or-pricing-tier-for-azure-search"></a>Escolher um SKU ou escalão de preço para o Azure Search
Na Azure Search, um [serviço é aprovisionado](search-create-service-portal.md) num escalão de preço específico ou SKU. As opções incluem **livres**, **básico**, ou **padrão**, onde **padrão** está disponível em várias configurações e as capacidades.

O objetivo deste artigo é ajudar a escolher uma camada. Se a capacidade de uma camada fica ser demasiado baixo, terá de aprovisionar um novo serviço na camada superior e, em seguida, volte a carregar os índices. Não há nenhuma atualização no local do mesmo serviço de um SKU para outro.

> [!NOTE]
> Depois de escolher um escalão e [aprovisionar um serviço de pesquisa](search-create-service-portal.md), pode aumentar a réplica e partição contagens no âmbito do serviço. Para obter orientações, consulte [Dimensionar níveis de recursos de consulta e cargas de trabalho de indexação](search-capacity-planning.md).
>
>

## <a name="how-to-approach-a-pricing-tier-decision"></a>Como abordagem uma decisão de escalão de preço
Na Azure Search, a camada determina capacidade, não a disponibilidade de funcionalidades. Geralmente, funcionalidades estão disponíveis em cada camada, incluindo funcionalidades de pré-visualização. A única exceção é a sem suporte de indexadores na S3 HD.

> [!TIP]
> Recomendamos que sempre aprovisionar um **livres** serviço (um por subscrição, sem expiração) para que o prontamente disponíveis para projetos de ponderação leve. Utilize o **livres** service para avaliação e teste; criar um segundo serviço sujeito a faturação no **básico** ou **padrão** camada para produção ou maiores cargas de trabalho de teste.
>
>

Capacidade e os custos de executar o serviço aceda à mão na mão. Informações deste artigo podem ajudá-lo a decidir que SKU disponibiliza o equilíbrio certo entre, mas para qualquer um dos que seja útil, precisa de, pelo menos, aproximadas estimativas nos seguintes:

* Número e tamanho de planear a criação de índices
* Número e tamanho dos documentos para carregar
* Alguns ideia do volume de consulta, em termos de consultas por segundo (QPS). Para obter orientações, consulte [desempenho de pesquisa do Azure e a otimização de](search-performance-optimization.md).

Número e tamanho são importantes porque os limites máximos são atingidos através de um limite rígido a contagem de índices por serviço, ou em recursos (armazenamento ou réplicas) utilizados pelo serviço. O limite real para o seu serviço é que obtém a gastar primeiro: recursos ou objetos.

Estimativas no lado, os seguintes passos devem simplificar o processo de:

* **Passo 1** rever as descrições de SKU abaixo para saber mais sobre as opções disponíveis.
* **Passo 2** responda às perguntas abaixo para chegam a uma decisão preliminar.
* **Passo 3** finalizar a sua decisão ao rever os limites fixos no armazenamento e preços.

## <a name="sku-descriptions"></a>Descrições de SKU
A tabela seguinte fornece descrições de cada camada.

| Escalão | Cenários principais |
| --- | --- |
| **Livre** |Um serviço partilhado, sem encargos, utilizado para avaliação, investigação ou pequenas cargas de trabalho. Porque é partilhado com outros subscritores, débito de consulta e indexação varia com base na pessoa que está a utilizar o serviço. A capacidade é pequena (50 MB ou 3 índices com segurança 10 000 documentos cada o). |
| **Básica** |Cargas de trabalho de produção de pequena no hardware dedicado. Elevada disponibilidade. A capacidade é até 3 réplicas e de 1 partição (2 GB). |
| **S1** |1 padrão suporta flexíveis combinações de partições (12) e as réplicas (12), utilizadas para cargas de trabalho de produção médio no hardware dedicado. Pode atribuir partições e réplicas em combinações suportadas por um número máximo 36 facturável de unidades de pesquisa. Este nível, partições são 25 GB. |
| **S2** |2 padrão executa cargas de trabalho de produção maior utilizando as unidades de 36 pesquisa mesmo que S1, mas com partições de tamanho superiores e as réplicas. Este nível, partições são 100 GB. |
| **S3** |3 padrão executa cargas de trabalho de produção proporcionalmente maiores nos sistemas de fim superiores, em configurações de até 12 partições ou 12 réplicas unidades de pesquisa em 36. Este nível, partições são 200 GB. |
| **S3 HD** |Standard 3 alta densidade foi concebida para um grande número de índices mais pequenos. Pode ter até 3 partições, em 200 GB.|

> [!NOTE]
> Valores máximos de partição e réplica são faturados enviados como unidades de pesquisa (36 unidade máxima por serviço), que impõe um limite inferior eficaz a que o máximo implica no valor de letra. Por exemplo, para utilizar o número máximo de 12 réplicas, pode ter no máximo de 3 partições (3 * 12 = 36 unidades). Da mesma forma, para utilizar partições máximas, reduza as réplicas para 3. Consulte [Dimensionar níveis de recursos de consulta e indexação cargas de trabalho na Azure Search](search-capacity-planning.md) de um gráfico de combinações permitidos.
>
>

## <a name="review-limits-per-tier"></a>Reveja os limites por camada
O gráfico seguinte é um subconjunto dos limites de [limites de serviço da Azure Search](search-limits-quotas-capacity.md). Lista os fatores provavelmente a afetar a uma decisão de SKU. Pode fazer referência a este gráfico ao rever as perguntas abaixo.

| Recurso | Gratuito | Básica | S1 | S2 | S3 | S3 HD |
| --- | --- | --- | --- | --- | --- | --- |
| Contrato de Nível de Serviço (SLA) |N.º <sup>1</sup> |Sim |Sim |Sim |Sim |Sim |
| Limites de índice |3 |5 |50 |200 |200 |1000 <sup>2</sup> |
| Limites do documento |10 000 total |milhões de 1 por serviço |milhões de 15 por partição |milhões de 60 por partição |milhões de 120 por partição |milhões de 1 por índice |
| Partições máximas |N/D |1 |12 |12 |12 |3 <sup>2</sup> |
| Tamanho da partição |Total de 50 MB |2 GB por serviço |25 GB por partição |100 GB por partição (até um máximo de 1.2 TB por serviço) |200 GB por partição (até um máximo de 2.4 TB por serviço) |200 GB (até um máximo de 600 GB por serviço) |
| Réplicas máximas |N/D |3 |12 |12 |12 |12 |

<sup>1</sup> funcionalidades de pré-visualização e o escalão gratuito não são fornecidos com os contratos de nível de serviço (SLAs). Para todos os escalões faturáveis, SLAs entram em vigor quando aprovisionar redundância suficiente para o seu serviço. Dois ou mais réplicas são necessárias para o SLA de consulta (leitura). Três ou mais réplicas são necessárias para consulta e indexação SLA (leitura / escrita). O número de partições não é uma consideração de SLA. 

<sup>2</sup> S3 e S3 HD são apoiados por infraestrutura idênticos capacidade elevada, mas cada um da atinge o limite máximo de formas diferentes. S3 destina-se um número mais pequeno índices muito grande. Como tal, o limite máximo está vinculado ao recurso (2.4 TB para cada serviço). S3 HD destina-se um grande número de índices muito pequenos. Em 1.000 índices, S3 HD atingir os limites na forma de restrições de índice. Se for um cliente de S3 HD que requer mais de 1000 índices, contacte Support da Microsoft para obter informações sobre como proceder.

## <a name="eliminate-skus-that-dont-meet-requirements"></a>Eliminar SKUs que não cumprem os requisitos
As perguntas seguintes podem ajudar a chegam a decisão de SKU certa para a carga de trabalho.

1. Tem **contrato de nível de serviço (SLA)** requisitos? Pode utilizar qualquer camada facturável (básico na cópia de segurança), mas tem de configurar o seu serviço para redundância. Dois ou mais réplicas são necessárias para o SLA de consulta (leitura). Três ou mais réplicas são necessárias para consulta e indexação SLA (leitura / escrita). O número de partições não é uma consideração de SLA.
2. **Quantos indexa** exige que? Uma das maiores variáveis factoring para uma decisão de SKU é o número de índices suportados por cada SKU. Suporte do índice é em markedly diferentes níveis de escalões de preço inferiores. Os requisitos de número de índices pode ser um determinant primária de uma decisão de SKU.
3. **O número de documentos** irá ser carregado para cada índice? O número e tamanho dos documentos determinará o tamanho de uma eventual do índice. Pressupondo que pode fazer a estimativa do tamanho previsto do índice, pode comparar esse número contra o tamanho de partição por SKU, expandido pelo número de partições necessário para armazenar um índice desse tamanho.
4. **O que é a carga esperada**? Depois dos requisitos de armazenamento estão compreendidos, considere as cargas de trabalho de consulta. S2 e ambos os SKUs de S3 oferecem débito de quase equivalentes, mas serão de regras de requisitos de SLA horizontalmente os SKUs de pré-visualização.
5. Se estiver a considerar o escalão S2 ou S3, determinar se necessita de [indexadores](search-indexer-overview.md). Indexadores ainda não estão disponíveis para a camada de S3 HD. Abordagem alternativa consiste em utilizar um modelo de envio para atualizações de índice, onde escrever o código da aplicação para enviar um conjunto de dados para um índice.

Maioria dos clientes pode da regra de um SKU específico entrada ou saída com base na respetiva respostas às questões acima. Se ainda não tem a certeza que SKU em com, pode publicar perguntas nos fóruns MSDN ou StackOverflow ou contacte o suporte do Azure para obter orientações adicionais.

## <a name="decision-validation-does-the-sku-offer-sufficient-storage-and-qps"></a>As decisões de validação: o SKU oferece armazenamento suficiente e QPS?
Como último passo, revê o [página de preços](https://azure.microsoft.com/pricing/details/search/) e [por serviço e por índice secções limites de serviço](search-limits-quotas-capacity.md) para verificar as estimativas contra os limites de subscrição e serviço.

Se os requisitos de preços ou armazenamento são fora dos limites, pode querer refatorar as cargas de trabalho entre vários serviços mais pequenos (por exemplo). No nível mais granular, foi redesenhar índices ser menor ou utilizar filtros para efetuar consultas mais eficiente.

> [!NOTE]
> Requisitos de armazenamento podem ser excessiva inflated se documentos contêm dados supérfluas. Idealmente, os documentos contêm apenas dados pesquisáveis ou metadados. Dados binários é não pesquisáveis e devem ser armazenados em separado (talvez no armazenamento do Azure de tabela ou blob) com um campo no índice para conter uma referência de URL para os dados externos. O tamanho máximo de um documento individuais é 16 MB (ou menos se estiver em massa carregar vários documentos num pedido). Consulte [Service limites na Azure Search](search-limits-quotas-capacity.md) para obter mais informações.
>
>

## <a name="next-step"></a>Passo seguinte
Quando souber que SKU é a opção adequada, continue com estes passos:

* [Criar um serviço de pesquisa no portal](search-create-service-portal.md)
* [Alterar a alocação de partições e réplicas para dimensionar o serviço](search-capacity-planning.md)
