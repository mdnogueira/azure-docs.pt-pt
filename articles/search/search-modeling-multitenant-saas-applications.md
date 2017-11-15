---
title: "Modelação de arquitetura \"multitenancy\" na Azure Search | Microsoft Docs"
description: "Saiba mais sobre os padrões de conceção comuns para aplicações de SaaS multi-inquilino durante a utilização da Azure Search."
services: search
manager: jhubbard
author: ashmaka
documentationcenter: 
ms.assetid: 72e9696a-553b-47dc-9e05-a82db0ebf094
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 11/09/2017
ms.author: ashmaka
ms.openlocfilehash: 622ae64e118dd2498aff0bf2e9f6c1dbfb0ab045
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2017
---
# <a name="design-patterns-for-multitenant-saas-applications-and-azure-search"></a>Padrões para aplicações de SaaS multi-inquilino e da Azure Search de conceção
Uma aplicação multi-inquilino é aquele que fornece as mesmas serviços e recursos para qualquer número de inquilinos que não é possível ver ou partilhar os dados de qualquer outro inquilino. Este documento descreve as estratégias de isolamento de inquilino para multi-inquilino aplicações criadas com a Azure Search.

## <a name="azure-search-concepts"></a>Conceitos de pesquisa do Azure
Como uma solução de pesquisa-como-um-serviço da Azure Search permite aos programadores adicionar experiências de pesquisa avançada para aplicações sem qualquer infraestrutura de gestão ou se tornar um especialista na obtenção de informações. Dados são carregados para o serviço e, em seguida, armazenados na nuvem. Utilizar pedidos simples para a API da Azure Search, os dados podem, em seguida, ser modificados e pesquisados. Uma descrição geral do serviço pode ser encontrada na [neste artigo](http://aka.ms/whatisazsearch). Antes de debater padrões de conceção, é importante compreender alguns conceitos na Azure Search.

### <a name="search-services-indexes-fields-and-documents"></a>Serviços de pesquisa, índices, campos e documentos
Ao utilizar a pesquisa do Azure, uma subscreve um *serviço de pesquisa*. Como são carregados dados para a Azure Search, esta está armazenada num *índice* no âmbito do serviço de pesquisa. Pode ser um número de índices dentro de um único serviço. Para utilizar os conceitos familiares de bases de dados, o serviço de pesquisa pode ser likened para uma base de dados enquanto os índices dentro de um serviço podem ser likened a tabelas dentro de uma base de dados.

Cada índice dentro de um serviço de pesquisa tem o seu próprio esquema, que é definida por um número de personalizável *campos*. Dados são adicionados a um índice da Azure Search sob a forma de indivíduo *documentos*. Cada documento tem de ser carregado para um índice específico e deve encaixar-se do esquema que indexar. Quando procurar dados de utilização da Azure Search, são emitidas as consultas de pesquisa em texto completo em relação a um índice específico.  Para comparar estes conceitos a uma base de dados, podem ser likened campos para colunas existentes numa tabela e documentos podem ser likened para linhas.

### <a name="scalability"></a>Escalabilidade
Qualquer serviço da Azure Search no padrão [escalão de preço](https://azure.microsoft.com/pricing/details/search/) pode dimensionar de duas dimensões: armazenamento e disponibilidade.

* *As partições* podem ser adicionados para aumentar o armazenamento de um serviço de pesquisa.
* *Réplicas* podem ser adicionados a um serviço para aumentar o débito de pedidos que pode processar um serviço de pesquisa.

Adicionar e remover as partições e réplicas permitirá a capacidade do serviço de pesquisa para aumentar com a quantidade de dados e os pedidos de aplicações de tráfego. Para que um serviço de pesquisa alcançar uma leitura [SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/), necessita de duas réplicas. Para que um serviço alcançar uma leitura e escrita [SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/), necessita de três réplicas.

### <a name="service-and-index-limits-in-azure-search"></a>Limites de serviço e o índice da Azure Search
Existem algumas diferentes [escalões de preço](https://azure.microsoft.com/pricing/details/search/) na Azure Search, cada um dos escalões tem diferentes [limites e quotas](search-limits-quotas-capacity.md). Alguns destes limites são ao nível do serviço, alguns são ao nível do índice e alguns são ao nível da partição.

|  | Básica | Standard1 | Standard2 | Standard3 | Standard3 HD |
| --- | --- | --- | --- | --- | --- |
| Réplicas máximas por serviço |3 |12 |12 |12 |12 |
| Partições máximas por serviço |1 |12 |12 |12 |3 |
| Unidades de pesquisa máximo (réplicas * partições) por serviço |3 |36 |36 |36 |36 (máximas 3 partições) |
| Documentos máximos por serviço |1 milhão |180 milhões |milhões de 720 |1.4 milhões |milhões de 600 |
| Armazenamento máximo por serviço |2GB |300 GB |1.2 TB |2.4 TB |600 GB |
| Documentos máximos por partição |1 milhão |milhões de 15 |milhões de 60 |milhões de 120 |milhões de 200 |
| Armazenamento máximo por partição |2GB |25 GB |100 GB |200 GB |200 GB |
| Índices máximos por serviço |5 |50 |200 |200 |3000 (máximo 1000 índices/partição) |

#### <a name="s3-high-density"></a>S3 Alta densidade '
No escalão de preço S3 da Azure Search, há uma opção para o modo de alta densidade (HD) foi concebido especificamente para cenários de multi-inquilino. Em muitos casos, é necessário suportar um grande número de inquilinos mais pequenos num único serviço para alcançar os benefícios da eficiência simplicidade e o custo.

S3 HD permite os índices de pequenos muitas para estar sob a gestão de um serviço de procura única criados pelo assiste comerciais a capacidade de aumentar horizontalmente os índices com partições para a capacidade para alojar mais índices num único serviço.

Concretely, um serviço de S3 ter entre 1 e 200 índices que em conjunto, podem alojar até 1.4 mil milhões de documentos. Um HD S3 por outro lado permitiria índices individuais apenas ir até 1 milhões de documentos, mas pode processar até 1000 índices por partição (até 3000 por serviço) com uma contagem de total de documento de milhões de 200 por partição (até 600 milhões por serviço).

## <a name="considerations-for-multitenant-applications"></a>Considerações para aplicações multi-inquilino
Aplicações multi-inquilino tem distribuir eficazmente os recursos entre os inquilinos preservando algum nível de privacidade entre os vários inquilinos. Existem algumas considerações ao estruturar a arquitetura de uma aplicação desse tipo:

* *Isolamento de inquilinos:* os programadores de aplicações tem de tomar as medidas adequadas para se certificar de que nenhum inquilinos tem não autorizado ou indesejável acesso aos dados de outros inquilinos. Para além da perspetiva de privacidade dos dados, estratégias de isolamento de inquilino necessitam de uma gestão eficaz da proteção de vizinhos inúteis e de recursos partilhados.
* *Custo de recursos de nuvem:* como com qualquer outra aplicação, as soluções de software têm de permanecer custo competitiva como um componente de uma aplicação multi-inquilino.
* *Facilidade de operações:* quando desenvolver uma arquitetura multi-inquilino, o impacto nas operações e a complexidade da aplicação é uma consideração importante. A pesquisa do Azure tem um [SLA de 99,9%](https://azure.microsoft.com/support/legal/sla/search/v1_0/).
* *Requisitos de espaço global:* aplicações multi-inquilino poderão ter de forma eficaz servir inquilinos que estão distribuídos por todo o mundo.
* *Escalabilidade:* os programadores de aplicações tem de considerar como de reconciliar entre manter um nível baixo suficientemente de complexidade de aplicação e a estruturação da aplicação a dimensionar com o número de inquilinos e o tamanho dos dados dos inquilinos e carga de trabalho.

A Azure Search oferece limites alguns que podem ser utilizados para isolar os dados e a carga de trabalho dos inquilinos.

## <a name="modeling-multitenancy-with-azure-search"></a>Modelação de arquitetura "multitenancy" com a pesquisa do Azure
No caso de um cenário de multi-inquilino, o programador da aplicação consome um ou mais serviços de pesquisa e dividir os seus inquilinos entre serviços, índices ou ambos. A pesquisa do Azure tem alguns padrões comuns quando um cenário de multi-inquilino de modelação:

1. *Índice por inquilino:* cada inquilino tem a suas próprias índice dentro de um serviço de pesquisa que é partilhado com outros inquilinos.
2. *Serviço por inquilino:* cada inquilino tem o seu próprio serviço de pesquisa do Azure dedicada, oferta nível mais elevado de separação de dados e a carga de trabalho.
3. *A combinação de ambos:* inquilinos maiores e mais-Active Directory estão atribuídos serviços dedicados enquanto inquilinos mais pequenos são atribuídos índices individuais dentro de serviços partilhados.

## <a name="1-index-per-tenant"></a>1. Índice por inquilino
![Um portrayal do modelo de índice por inquilino](./media/search-modeling-multitenant-saas-applications/azure-search-index-per-tenant.png)

Um modelo de índice por inquilino, vários inquilinos ocupam um único serviço de pesquisa do Azure onde cada inquilino tem os seus próprios índice.

Os inquilinos alcançarem isolamento de dados, uma vez que todos os pedidos de pesquisa e operações de documento são emitidas um nível de índice da Azure Search. Na camada da aplicação, há a deteção de necessidade para direcionar o tráfego de vários inquilinos para os índices adequados enquanto também gere recursos ao nível do serviço em todos os inquilinos.

Um atributo chave do modelo de índice por inquilino é a capacidade para o programador da aplicação exceder a capacidade de um serviço de pesquisa entre inquilinos da aplicação. Se os inquilinos tiverem uma distribuição desigual da carga de trabalho, a combinação ideal de inquilinos pode ser distribuída em índices de um serviço de pesquisa para acomodar um número de inquilinos altamente Active Directory, consome enquanto processa em simultâneo uma longa cauda do menor inquilinos Active Directory. O compromisso é a impossibilidade de modelo para processar situações em que cada inquilino em simultâneo é altamente Active Directory.

O modelo de índice por inquilino fornece a base para um modelo de custo de variável, onde um todo o serviço da Azure Search é comprou prévio e, em seguida, subsequentemente, preenchida com inquilinos. Isto permite a capacidade não utilizada para designado para as avaliações e as contas gratuitas.

Para aplicações com um requisitos de espaço global, o modelo de índice por inquilino não pode ser mais eficiente. Se os inquilinos de uma aplicação são distribuídos em todo o mundo, um serviço separado poderá ser necessário para cada região que pode duplicar os custos em cada um deles.

A pesquisa do Azure permite a escala de índices individuais e o número total de índices a crescer. Se um preço adequado camada é escolhido, partições e réplicas podem ser adicionadas para o serviço de pesquisa completa, quando um índice individuais no âmbito do serviço ficar demasiado grande em termos de armazenamento ou tráfego.

Se o número total de índices ficar demasiado grande para um único serviço, tem de ser aprovisionada para acomodar os novos inquilinos outro serviço. Se os índices têm de ser movidos entre serviços de pesquisa à medida que são adicionados novos serviços, os dados do índice tem de ser copiados manualmente a partir de um índice para outro como da Azure Search não permite a um índice para ser movida.

## <a name="2-service-per-tenant"></a>2. Serviço por inquilino
![Um portrayal do modelo de serviço por inquilino](./media/search-modeling-multitenant-saas-applications/azure-search-service-per-tenant.png)

Numa arquitetura por-inquilino de serviço, cada inquilino tem o seu próprio serviço de pesquisa.

Neste modelo, a aplicação permite o nível máximo de isolamento para os seus inquilinos. Cada serviço tem dedicada de armazenamento e débito para processar o pedido de pesquisa, bem como as chaves de API separadas.

Aplicações onde cada inquilino tem requisitos de espaço de grandes dimensões ou a carga de trabalho variabilidade pouca do inquilino para o inquilino, o modelo de serviço por inquilino é uma opção efetiva como recursos não são partilhados entre as cargas de trabalho de vários inquilinos.

Um serviço por modelo de inquilino também oferece o benefício de um modelo de custo previsível, fixos. Não há um investimento prévio num serviço de pesquisa de todo, até um inquilino para preencher, no entanto, o custo por inquilino é superior a um modelo de índice por inquilino.

O modelo de serviço por inquilino é uma escolha eficiente para aplicações com um requisitos de espaço global. Com inquilinos distribuída geograficamente, é fácil ter o serviço de cada inquilino na região adequada.

Os desafios Dimensionar este padrão surgem quando individuais inquilinos outgrow o seu serviço. A pesquisa do Azure não suporta atualmente a atualizar o escalão de preço de um serviço de pesquisa, para que todos os dados teria manualmente seja copiado para um novo serviço.

## <a name="3-mixing-both-models"></a>3. A combinação de ambos os modelos
Outro padrão para a modelação de arquitetura "multitenancy" é a combinação de estratégias de índice por inquilino e serviço por inquilino.

Ao combinar dois padrões, inquilinos maiores de uma aplicação podem ocupam serviços dedicados enquanto final muito menos ativos, mais pequenos de inquilinos pode ocupam índices num serviço partilhado. Este modelo assegura que os inquilinos maiores têm consistentemente elevado desempenho do serviço, ajudando a proteger os inquilinos inferior de qualquer vizinhos inúteis.

No entanto, implementar esta estratégia baseia-se prospeção no prever que os inquilinos necessitam de um serviço dedicada versus um índice num serviço partilhado. Aumenta a complexidade de aplicação com a necessidade de gerir ambos estes modelos de arquitetura "multitenancy".

## <a name="achieving-even-finer-granularity"></a>Alcançar granularidade mesmo melhorar
Os padrões de conceção acima para modelar os cenários de multi-inquilino na Azure Search partem do princípio de um âmbito uniform onde cada inquilino seja uma instância completa de uma aplicação. No entanto, as aplicações, por vezes, podem processar vários âmbitos mais pequenos.

Se os modelos de serviço por inquilino e índice por inquilino não são suficientemente pequenos âmbitos, é possível modelar um índice para alcançar um grau mesmo melhorar de granularidade.

Para que um único índice se comportam de forma diferente de pontos finais de cliente diferente, um campo pode ser adicionado a um índice que designa um determinado valor para cada cliente possíveis. Sempre que um cliente chamadas da Azure Search para consultar ou modificar um índice, o código da aplicação cliente especifica o valor adequado para esse campo através da Azure Search [filtro](https://msdn.microsoft.com/library/azure/dn798921.aspx) capacidade no momento da consulta.

Este método pode ser utilizado para obter uma funcionalidade de contas de utilizador em separado, níveis de permissão separada e até mesmo completamente separadas aplicações.

> [!NOTE]
> Utilizar a abordagem descrita acima configurar um único índice para servir vários inquilinos afeta a importância dos resultados da pesquisa. Pesquisa relevância pontuações são calculadas um âmbito de nível de índice, não é um âmbito de nível de inquilino, para que os dados de todos os inquilinos incorporados nas estatísticas de subjacente dos pontuações de relevância, tais como a frequência de prazo.
> 
> 

## <a name="next-steps"></a>Passos seguintes
A pesquisa do Azure é uma escolha apelativa para muitas aplicações, [ler mais sobre as capacidades de robustas o serviço](http://aka.ms/whatisazsearch). Quando avaliar os padrões de conceção vários para aplicações multi-inquilino, considere o [vários escalões de preços](https://azure.microsoft.com/pricing/details/search/) e o respetivo [os limites de serviço](search-limits-quotas-capacity.md) a melhor adaptarem da Azure Search para ajustar à aplicação e arquiteturas de todos os tamanhos de cargas de trabalho.

Quaisquer perguntas sobre a Azure Search e cenários de multi-inquilino podem ser direcionadas para azuresearch_contact@microsoft.com.

