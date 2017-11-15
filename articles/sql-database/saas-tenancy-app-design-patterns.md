---
title: "Padrões de SaaS multi-inquilino - SQL Database do Azure | Microsoft Docs"
description: "Saiba mais sobre os requisitos e dados comuns padrões da arquitetura de software do multi-inquilino como um aplicações de base de dados do serviço (SaaS) que são executadas no ambiente de nuvem do Azure."
keywords: tutorial de base de dados sql
services: sql-database
documentationcenter: 
author: billgib
manager: craigg
editor: MightyPen,srinia
ms.assetid: 1dd20c6b-ddbb-40ef-ad34-609d398d008a
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Active
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/12/2017
ms.author: billgib
ms.openlocfilehash: 0377baaa4a0db7e3cb2041f3ca018322e379f0df
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2017
---
# <a name="multi-tenant-saas-database-tenancy-patterns"></a>Padrões de inquilinos de base de dados de SaaS multi-inquilino

Ao conceber uma aplicação de SaaS multi-inquilino, tem de escolher cuidadosamente o modelo de inquilinos que melhor se adeque às necessidades da sua aplicação.  Um modelo de inquilinos determina como os dados de cada inquilino estão mapeados para o armazenamento.  À sua escolha do modelo de inquilinos tem impacto sobre a gestão e de design da aplicação.  Mudar para um modelo diferente, mais tarde, por vezes, é dispendioso.

Segue um debate dos modelos de inquilinos alternativo.

## <a name="a-how-to-choose-the-appropriate-tenancy-model"></a>A. Como escolher o modelo de inquilinos adequado

Em geral, o modelo de inquilinos não afeta a função de uma aplicação, mas, provavelmente tem impacto nos outros aspetos da solução global.  Os critérios seguintes são utilizados para avaliar a cada um dos modelos:

- **Escalabilidade:**
    - Número de inquilinos.
    - Armazenamento por-inquilino.
    - Armazenamento em agregado.
    - Carga de trabalho.

- **Isolamento de inquilinos:** &nbsp; isolamento de dados e de desempenho (se carga de trabalho de um inquilino tem impacto nos outros).

- **Custo por inquilino:** &nbsp; os custos de base de dados.

- **Complexidade de desenvolvimento:**
    - Alterações ao esquema.
    - Alterações às consultas (requeridas pelo padrão).

- **Complexidade operacional:**
    - Monitorizar e gerir o desempenho.
    - Gestão de esquema.
    - Restaurar um inquilino.
    - Recuperação após desastre.

- **Customizability:** &nbsp; facilidade de suportar as personalizações de esquema que são específicas do inquilino ou específico da classe de inquilino.

O debate inquilinos concentra-se no *dados* camada.  Mas tenha em atenção para um momento a *aplicação* camada.  Camada da aplicação é tratada como uma entidade monolithic.  Se dividir a aplicação para muitos componentes pequenos, poderá alterar a sua escolha de modelo de inquilinos.  Foi tratar alguns componentes diferente outros sobre os inquilinos e a tecnologia de armazenamento ou plataforma utilizado.

## <a name="b-standalone-single-tenant-app-with-single-tenant-database"></a>B. Aplicação de inquilino único autónomo com base de dados de inquilino único

#### <a name="application-level-isolation"></a>Isolamento de nível de aplicação

Neste modelo, toda a aplicação é instalada repetidamente, uma vez para cada inquilino.  Cada instância da aplicação é uma instância autónoma, pelo que nunca interage com qualquer outra instância autónoma.  Cada instância da aplicação tem apenas um inquilino e, por conseguinte, tem apenas uma base de dados.  O inquilino tem a base de dados para si próprio.

![Estrutura da aplicação autónoma e com exatamente uma base de dados único inquilino.][image-standalone-app-st-db-111a]

Cada instância da aplicação está instalada num grupo de recursos do Azure separado.  O grupo de recursos pode pertencer a uma subscrição que é propriedade o fornecedor do software ou o inquilino.  Em ambos os casos, o fornecedor pode gerir o software para o inquilino.  Cada instância da aplicação está configurada para ligar a respetiva base de dados correspondente.

Cada base de dados do inquilino é implementado como uma base de dados autónomo.  Este modelo fornece o isolamento de base de dados maior.  Mas o isolamento requer que os recursos suficientes ser atribuídos a cada base de dados para processar as respetivas cargas das horas de ponta.  Aqui, é importante que os conjuntos elásticos não podem ser utilizados para bases de dados implementados em grupos de recursos diferente ou a subscrições diferentes.  Esta limitação faz com que esta aplicação autónoma e único inquilino a solução mais dispendiosa de uma perspetiva de custo de base de dados global do modelo.

#### <a name="vendor-management"></a>Gestão do fornecedor

O fornecedor pode aceder a todas as bases de dados em todas as instâncias de aplicações de autónomo, mesmo se as instâncias de aplicações são instaladas em subscrições diferentes de inquilino.  O acesso é conseguido através de ligações de SQL.  Este acesso entre instância pode ativar o fornecedor para centralizar a gestão de esquema e consulta de base de dados em vários locais para fins de criação de relatórios ou análise.  Se pretender a este tipo de gestão centralizada, um catálogo tem de ser implementado que mapeia identificadores de inquilino para a base de dados de URI.  Base de dados SQL do Azure fornece uma biblioteca de fragmentação que é utilizada em conjunto com uma base de dados do SQL Server para fornecer um catálogo.  A biblioteca de fragmentação formally é denominada o [biblioteca de clientes de base de dados elásticas][docu-elastic-db-client-library-536r].

## <a name="c-multi-tenant-app-with-database-per-tenant"></a>C. Aplicação de multi-inquilino com a base de dados por inquilino

Este padrão seguinte utiliza uma aplicação multi-inquilino com muitas bases de dados, a todas as bases de dados único inquilino.  Uma nova base de dados é aprovisionada para cada novo inquilino.  A camada de aplicação é dimensionada *segurança* verticalmente adicionando mais recursos por nó.  Ou a aplicação é dimensionada *saída* horizontalmente adicionando mais nós.  O dimensionamento é com base na carga de trabalho e é independente da escala de bases de dados individuais ou número.

![Estrutura da aplicação multi-inquilino com a base de dados por inquilino.][image-mt-app-db-per-tenant-132d]

#### <a name="customize-for-a-tenant"></a>Personalizar para um inquilino

Como o padrão de aplicação autónoma, a utilização de bases de dados de inquilino único fornece isolamento de inquilinos forte.  Em qualquer aplicação cujo modelo especifica apenas bases de dados de inquilino único, o esquema para qualquer uma base de dados especificado pode ser personalizado e otimizado para o seu inquilino.  Esta personalização não afetar outros inquilinos na aplicação. Talvez um inquilino poderá ter dados para além dos campos de dados básicos que necessitam de todos os inquilinos.  Além disso, o campo de dados adicionais poderá necessitar de um índice.

Com base de dados por inquilino é simples conseguir personalizar o esquema para um ou mais inquilinos individuais.  O fornecedor da aplicação tem de design procedimentos para gerir cuidadosamente as personalizações de esquema à escala.

#### <a name="elastic-pools"></a>Conjuntos elásticos

Quando as bases de dados são implementadas no mesmo grupo de recursos, estes podem ser agrupados em conjuntos de bases de dados elásticas.  Os conjuntos de fornecem uma forma económica de partilhar recursos em muitas bases de dados.  Esta opção de agrupamento é mais barata que necessidade cada base de dados para ser suficientemente grande para acomodar os picos de utilização ocorre com.  Apesar do acesso aos recursos de partilha de bases de dados agrupados ainda possam podem alcançar um elevado grau de isolamento do desempenho.

![Estrutura da aplicação multi-inquilino com a base de dados-por-inquilino, utilizando o conjunto elástico.][image-mt-app-db-per-tenant-pool-153p]

Base de dados SQL do Azure fornece as ferramentas necessárias para configurar, monitorizar e gerir a partilha.  Ambas as métricas de desempenho de base de dados ao nível do conjunto e estão disponíveis no portal do Azure e através da análise de registos.  As métricas podem dar aprofundadas excelente desempenho agregado e inquilino específico.  Bases de dados individuais podem ser movidos entre conjuntos para fornecer recursos reservados para um inquilino específico.  Estas ferramentas permitem-lhe assegurar um bom desempenho de uma forma económica.

#### <a name="operations-scale-for-database-per-tenant"></a>Dimensionamento de operações para a base de dados por inquilino

A plataforma de SQL Database do Azure tem muitas funcionalidades de gestão foi concebidas para uma grande quantidade de gestão de bases de dados à escala, como também mais de 100 000 bases de dados.  Estas funcionalidades tornam o padrão de base de dados por inquilino plausible.

Por exemplo, suponha que um sistema tem uma base de dados do inquilino de 1000 como respetivo apenas uma base de dados.  A base de dados poderá ter 20 índices.  Se o sistema converte-se ao facto de bases de dados de inquilino único de 1000, quatity dos índices aumenta a 20.000.  Base de dados do SQL como parte da [otimização automática][docu-sql-db-automatic-tuning-771a], as funcionalidades de indexação automáticas estão ativadas por predefinição.  A indexação automática gere de automaticamente todos os 20.000 índices e os respetivos otimizações de criação e drop em curso.  Estas ações automatizadas ocorrerem dentro de uma base de dados individuais e não são coordenados ou restringidas pelas ações semelhantes noutras bases de dados.  A indexação automática trata índices de forma diferente numa base de dados ocupado que numa base de dados menos ocupado.  Este tipo de personalização de gestão de índice seria impractical a escala de base de dados por inquilino se esta tarefa de gestão enorme que tiveram de ser efetuadas manualmente.

Outras funcionalidades de gestão que escala bem incluem o seguinte:

- Cópias de segurança incorporadas.
- Elevada disponibilidade.
- Encriptação no disco.
- Telemetria de desempenho.

#### <a name="automation"></a>Automatização

As operações de gestão podem ser convertidos em script e disponibilizadas através de um [devops] [ http-visual-studio-devops-485m] modelo.  As operações podem ainda ser automatizadas e expostas na aplicação.

Por exemplo, pode automatizar a recuperação de um inquilino único para um ponto anterior no tempo.  A recuperação apenas tem de restaurar a um único inquilino da base de dados que armazena o inquilino.  Este restauro tem sem afetar outros inquilinos, que confirma que as operações de gestão são ao nível do lhe granular de cada inquilino individual.

## <a name="d-multi-tenant-app-with-multi-tenant-databases"></a>D. Aplicação multi-inquilino com bases de dados do multi-inquilinos

Outro padrão disponível é armazenar muitos inquilinos numa base de dados do multi-inquilino.  A instância da aplicação pode ter qualquer número de bases de dados do multi-inquilinos.  O esquema de uma base de dados do multi-inquilino tem de ter uma ou mais colunas de identificador de inquilino para que os dados a partir de qualquer determinado inquilino podem ser obtidos seletivamente.  Além disso, o esquema pode requerer alguns tabelas ou colunas que são utilizadas por apenas um subconjunto de inquilinos.  No entanto, dados de referência e de código estáticos são armazenados apenas uma vez e são partilhados por todos os inquilinos.

#### <a name="tenant-isolation-is-sacrificed"></a>Isolamento de inquilino é sacrificed

*Dados:* &nbsp; uma base de dados do multi-inquilino necessariamente sacrifices isolamento de inquilino.  Os dados de vários inquilinos são armazenados em conjunto na base de dados de um.  Durante o desenvolvimento, certifique-se de que as consultas nunca expõem os dados de mais do que um inquilino.  Base de dados SQL suporta [ao nível da linha segurança][docu-sql-svr-db-row-level-security-947w], que pode impor que os dados devolvidos por uma consulta ser confinada para um único inquilino.

*Processamento:* &nbsp; uma base de dados do multi-inquilino partilha recursos de computação e armazenamento em todos os seus inquilinos.  A base de dados como um todo, pode ser monitorizado para se certificar de que está a efetuar acceptably.  No entanto, o sistema do Azure tem nenhuma forma incorporada para monitorizar ou para gerir a utilização destes recursos por um inquilino individual.  Por conseguinte, a base de dados do multi-inquilino acarreta um maior risco de encontrar vizinhos inúteis, em que a carga de trabalho de um inquilino overactive tem impacto sobre a experiência de desempenho de outros inquilinos na mesma base de dados.  Monitorização adicional de nível de aplicação foi monitorizar o desempenho de nível de inquilino.

#### <a name="lower-cost"></a>Custo mais baixo

Em geral, bases de dados do multi-inquilinos tem mais baixo por-inquilino de custos.  Os custos de recursos para uma base de dados autónomo são inferiores de um conjunto elástico equivalently tamanho.  Além disso, para cenários em que os inquilinos necessitam apenas armazenamento limitado, potencialmente milhões de inquilinos podem ser armazenados numa base de dados.  Nenhum conjunto elástico pode conter milhões de bases de dados.  No entanto, uma solução que contém as bases de dados de 1000 por agrupamento, com 1000 agrupamentos, foi possível alcançar a escala de milhões at the risk of se tornar unwieldy para gerir.

São apresentadas duas variações de um modelo de base de dados do multi-inquilino que se segue, com o modelo de multi-inquilino em partição horizontal que está a ser mais flexível e escalável.

## <a name="e-multi-tenant-app-with-a-single-multi-tenant-database"></a>E. Aplicação de multi-inquilino com uma única base de dados do multi-inquilino

O padrão de base de dados do multi-inquilino mais simples utiliza uma base de dados autónomo único para alojar dados para todos os inquilinos.  À medida que são adicionados mais inquilinos, a base de dados é escalado para cima com mais recursos de armazenamento e computação.  Nesta escala cópias de segurança poderá ser tudo o que é necessário, embora haja sempre um limite de escala ultimate.  No entanto, longa antes desse limite é alcançado a base de dados fica unwieldy para gerir.

Operações de gestão que são concentra-se em inquilinos individuais são mais complexas para implementar uma base de dados do multi-inquilino.  E, em escala, estas operações podem ficar unacceptably lentas.  Um exemplo é um restauro de ponto no tempo dos dados para um inquilino.

## <a name="f-multi-tenant-app-with-sharded-multi-tenant-databases"></a>F. Aplicação multi-inquilino com a bases de dados do multi-inquilinos

A maioria das aplicações de SaaS aceder aos dados do inquilino apenas um cada vez.  Neste padrão de acesso permite o inquilino de dados ser distribuída em várias bases de dados ou shards, onde todos os dados para um inquilino está contida no ID de partição um horizontal.  Um modelo em partição horizontal combinado com um padrão de base de dados do multi-inquilino, permite que o dimensionamento praticamente ilimitado.

![Estrutura da aplicação multi-inquilino com a bases de dados do multi-inquilinos.][image-mt-app-sharded-mt-db-174s]

#### <a name="manage-shards"></a>Gerir shards

Fragmentação adiciona complexidade tanto para a estrutura e a gestão operacional.  Um catálogo é necessária para manter o mapeamento entre inquilinos e bases de dados.  Além disso, os procedimentos de gestão são necessárias para gerir os shards e a população de inquilino.  Por exemplo, os procedimentos têm de ser concebidos para adicionar e remover shards e para mover dados de inquilino entre shards.  Uma forma de escala é adicionando um ID de partição horizontal novo e preenchê-lo com novos inquilinos.  Em outras vezes poderá de dividir um ID de partição horizontal densely preenchido em dois shards menos densely povoadas.  Depois de vários inquilinos foram movidos ou descontinuados, poderá intercalar shards sparsely preenchidos em conjunto.  A opção de intercalação resulta na utilização de recursos económico mais.  Os inquilinos também podem ser movidos entre shards para balancear cargas de trabalho.

Base de dados do SQL Server fornece uma ferramenta de divisão/intercalação funciona em conjunto com a biblioteca de fragmentação e a base de dados do catálogo.  Pode dividir a aplicação fornecida e intercalação shards e podem mover dados de inquilino entre shards.  A aplicação mantém também o catálogo durante estas operações, marcar afetados inquilinos como offline antes de movê-los.  Após a mudança, a aplicação de atualizações no catálogo novamente com o mapeamento de novo e marcar o inquilino como online.

#### <a name="smaller-databases-more-easily-managed"></a>Menores bases de dados mais facilmente geridos

Por distribuição de inquilinos através de várias bases de dados, a solução de multi-inquilino a resulta numa menores bases de dados que são geridos mais facilmente.  Por exemplo, o restauro de um inquilino específico para um ponto anterior no tempo agora envolve restaurar uma base de dados mais pequeno de uma cópia de segurança, em vez de uma base de dados maior que contém todos os inquilinos. O tamanho da base de dados e o número de inquilinos por base de dados, podem ser selecionados para balancear a carga de trabalho e os esforços de gestão.

#### <a name="tenant-identifier-in-the-schema"></a>Identificador de inquilino no esquema

Consoante a abordagem de fragmentação utilizada, restrições adicionais podem ser impostas no esquema de base de dados.  A aplicação de divisão/intercalação de base de dados do SQL Server requer que o esquema inclui a chave de fragmentação, que, normalmente, é o identificador de inquilino.  O identificador de inquilino é o elemento à esquerda na chave primária de todas as tabelas em partição horizontal.  O identificador de inquilino permite que a aplicação de divisão/intercalação rapidamente localizar e mover dados associados a um inquilino específico.

#### <a name="elastic-pool-for-shards"></a>Agrupamento elástico de partições horizontais

A bases de dados do multi-inquilinos podem ser colocados em conjuntos elásticos.  Em geral, muitas bases de dados de inquilino único num conjunto é ter como custo eficiente como tendo muitos inquilinos algumas bases de dados do multi-inquilino.  Bases de dados do multi-inquilinos são vantajosos quando existe um grande número de inquilinos relativamente inativos.

## <a name="g-hybrid-sharded-multi-tenant-database-model"></a>G. Modelo da base de dados do multi-inquilino híbrida

No modelo de híbrida, todas as bases de dados tem o identificador de inquilino no respetivo esquema.  As bases de dados têm todas as capacidade de armazenar mais do que um inquilino, e as bases de dados podem ser em partição horizontal.  Por isso, no sentido de esquema, são todas as bases de dados do multi-inquilinos.  Ainda na prática, algumas destas bases de dados contém apenas um inquilino.  Independentemente disso, a quantidade de inquilinos armazenados numa base de dados fornecido não tem efeito no esquema de base de dados.

#### <a name="move-tenants-around"></a>Mover inquilinos-

Em qualquer altura, pode mover um determinado inquilino para a sua própria base de dados do multi-inquilino.  E, em qualquer altura, pode mudar de ideias e regresse ao inquilino para uma base de dados que contém vários inquilinos.  Também pode atribuir um inquilino para a nova base de dados de inquilino único quando Aprovisiona a nova base de dados.

O modelo de híbrida shines quando existem diferenças grande entre as necessidades de recursos de grupos de identificação de inquilinos.  Por exemplo, suponha que os inquilinos a participar numa versão de avaliação gratuita não são garantidos que o mesmo nível elevado de desempenho que são de subscrição de inquilinos.  A política pode ser para inquilinos na fase de avaliação gratuita para ser armazenada numa base de dados multi-inquilino que é partilhado entre todos os inquilinos de avaliação gratuitos.  Quando subscreve um inquilino de avaliação gratuito para o nível de serviço básico, inquilino pode ser movido para outra base de dados de multi-inquilino que poderá ter menos de inquilinos.  Um subscritor que pays para o nível de serviço premium foi possível mover a respetiva base de dados único inquilino won novo.

#### <a name="pools"></a>Conjuntos

Neste modelo híbrida, as bases de dados de inquilino único para os inquilinos do subscritor podem ser colocados em agrupamentos de recursos para reduzir os custos de base de dados por inquilino.  Também é feita no modelo de base de dados por inquilino.

## <a name="h-tenancy-models-compared"></a>H. Modelos de inquilinos em comparação comparados

A tabela seguinte resume as diferenças entre os modelos de inquilinos principal.

| Medida | Aplicação autónoma | Base de dados por inquilino | A multi-inquilino |
| :---------- | :------------- | :------------------ | :------------------- |
| Escala | Médio<br />1-100s | Muito elevado<br />1-100,000s | Ilimitado<br />1-1 000 000s |
| Isolamento de inquilinos | Muito elevado | Elevado | Baixa; exceto nenhum inquilino singleton (que é apenas a uma base de dados MT). |
| Custo de base de dados por inquilino | Elevado; é um tamanho adequado para picos. | Baixa; agrupamentos utilizados. | Mais baixo, para inquilinos pequenos no MT DBs. |
| Gestão e monitorização de desempenho | Por-inquilinos apenas | Agregado + por inquilino | Agregar; Embora é por inquilino apenas para singletons. |
| Complexidade de desenvolvimento | Baixo | Baixo | Média; devido à fragmentação. |
| Complexidade operacional | Baixa-alta. Individualmente simples e complexos à escala. | Médio baixo. Complexidade de endereço de padrões à escala. | Baixa-alta. Gestão de inquilino individual é complexa. |
| &nbsp; ||||

## <a name="next-steps"></a>Passos seguintes

- [Implementar e explorar uma aplicação de Wingtip de multi-inquilino que utiliza o modelo de SaaS de base de dados por inquilino - SQL Database do Azure][docu-sql-db-saas-tutorial-deploy-wingtip-db-per-tenant-496y]

- [Bem-vindo à aplicação de inquilinos de SQL Database do Azure SaaS de amostra de pedidos de Wingtip][docu-saas-tenancy-welcome-wingtip-tickets-app-384w]


<!--  Article link references.  -->

[http-visual-studio-devops-485m]: https://www.visualstudio.com/devops/

[docu-sql-svr-db-row-level-security-947w]: https://docs.microsoft.com/sql/relational-databases/security/row-level-security

[docu-elastic-db-client-library-536r]: sql-database-elastic-database-client-library.md
[docu-sql-db-saas-tutorial-deploy-wingtip-db-per-tenant-496y]: sql-database-saas-tutorial.md
[docu-sql-db-automatic-tuning-771a]: sql-database-automatic-tuning.md
[docu-saas-tenancy-welcome-wingtip-tickets-app-384w]: saas-tenancy-welcome-wingtip-tickets-app.md


<!--  Image references.  -->

[image-standalone-app-st-db-111a]: media/saas-tenancy-app-design-patterns/saas-standalone-app-single-tenant-database-11.png "Estrutura da aplicação autónoma e com exatamente uma base de dados único inquilino."

[image-mt-app-db-per-tenant-132d]: media/saas-tenancy-app-design-patterns/saas-multi-tenant-app-database-per-tenant-13.png "Estrutura da aplicação multi-inquilino com a base de dados por inquilino."

[image-mt-app-db-per-tenant-pool-153p]: media/saas-tenancy-app-design-patterns/saas-multi-tenant-app-database-per-tenant-pool-15.png "Estrutura da aplicação multi-inquilino com a base de dados-por-inquilino, utilizando o conjunto elástico."

[image-mt-app-sharded-mt-db-174s]: media/saas-tenancy-app-design-patterns/saas-multi-tenant-app-sharded-multi-tenant-databases-17.png "Estrutura da aplicação multi-inquilino com a bases de dados do multi-inquilinos."

