---
title: "Padrões para aplicações de SaaS multi-inquilino e a SQL Database do Azure de conceção | Microsoft Docs"
description: "Saiba mais sobre os requisitos e dados comuns padrões da arquitetura de software do multi-inquilino como um aplicações de base de dados do serviço (SaaS) que são executadas num ambiente de nuvem."
keywords: 
services: sql-database
documentationcenter: 
author: srinia
manager: jhubbard
editor: 
ms.assetid: 1dd20c6b-ddbb-40ef-ad34-609d398d008a
ms.service: sql-database
ms.custom: scale out apps
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: Active
ms.date: 02/01/2017
ms.author: srinia
ms.openlocfilehash: eef48cfcbc7d6c241b5ece863df0be6ecad78ca7
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="design-patterns-for-multi-tenant-saas-applications-and-azure-sql-database"></a>Padrões para aplicações de SaaS multi-inquilino e a SQL Database do Azure de conceção
Neste artigo, pode saber sobre os requisitos e dados comuns padrões da arquitetura de software do multi-inquilino como um aplicações de base de dados do serviço (SaaS) que são executadas num ambiente de nuvem. Também explica os fatores que precisa de considerar e os compromissos de padrões de conceção diferentes. Conjuntos elásticos e ferramentas elásticas na SQL Database do Azure podem ajudar a satisfazer as suas necessidades específicas sem comprometer a outros objetivos.

Os programadores efetuam, por vezes, as escolhas que funcionam com os seus interesses melhor longo prazo, quando estes modelos de inquilinos para as camadas de dados de aplicações de multi-inquilinos de design. Inicialmente, pelo menos, um programador pode interpretar facilidade de desenvolvimento e inferiores custos de fornecedor de serviço em nuvem como o mais importante do que o isolamento de inquilinos ou a escalabilidade de uma aplicação. Esta opção pode levar a preocupações de satisfação do cliente e uma correção de decorrer dispendiosa mais tarde.

Uma aplicação de multi-inquilino é uma aplicação alojada num ambiente de nuvem e que fornece o mesmo conjunto de serviços para centenas ou milhares de inquilinos que não partilhe e ver os dados entre si. Um exemplo é uma aplicação SaaS que forneça serviços aos inquilinos num ambiente alojado na nuvem.

## <a name="multi-tenant-applications"></a>Aplicações de multi-inquilinos
Nas aplicações do multi-inquilinos, dados e a carga de trabalho podem ser facilmente particionados. Pode particionar dados e a carga de trabalho, por exemplo, ao longo dos limites do inquilino, porque o maior número de pedidos de ocorrer dentro confines de um inquilino. Essa propriedade é inerente a carga de trabalho e os dados e favorece o os padrões de aplicação abordados neste artigo.

Os programadores de utilizam este tipo de aplicação em espetro completo de aplicações baseado na nuvem, incluindo:

* Aplicações de base de dados de parceiro que estão a ser transferidas para a nuvem, como aplicações SaaS
* Aplicações de SaaS construídas a para a nuvem a partir do zero
* Aplicações direcionadas para o cliente diretas
* Aplicações da empresa destinados aos parceiros

Aplicações de SaaS que foram concebidas para a nuvem e as raízes como parceiro, normalmente, as aplicações de base de dados são aplicações de multi-inquilinos. Estas aplicações SaaS entregar uma aplicação de software especializadas, como um serviço aos respetivos inquilinos. Os inquilinos podem aceder ao serviço de aplicações e tem propriedade completa de dados associados armazenados como parte da aplicação. Mas, para tirar partido das vantagens de SaaS, inquilinos tem surrender algum controlo sobre os seus próprios dados. Eles confiam o fornecedor do serviço de SaaS para manter os seus dados seguros e isolados dos dados dos outros inquilinos. Os exemplos deste tipo de aplicação de SaaS multi-inquilino são MYOB, SnelStart e Salesforce.com. Cada uma dessas aplicações pode ser particionada ao longo dos limites de inquilino e suporte, vamos discutir neste artigo de padrões de conceção da aplicação.

As aplicações que fornecem um serviço direto aos clientes ou para os empregados dentro de uma organização (normalmente designado como utilizadores, em vez de inquilinos) são noutra categoria no espetro aplicação multi-inquilino. Os clientes subscrevem o serviço e não é o proprietário os dados que o fornecedor de serviços recolhe e armazena. Fornecedores de serviços têm requisitos menos rigorosos para manter os dados dos seus clientes isolados entre si, para além de regulamentos de privacidade exigida por government. Os exemplos deste tipo de aplicação de multi-inquilino orientado para o cliente são fornecedores de conteúdo do suporte de dados como Netflix, que o Spotify e Xbox LIVE. Outros exemplos de aplicações facilmente partições são direcionadas para o cliente, aplicações de dimensionamento de Internet, ou aplicações de Internet das coisas (IoT) em que cada podem de cliente ou dispositivo servirem como uma partição. Limites de partição podem separar os utilizadores e dispositivos.

Nem todas as partições de aplicações facilmente ao longo de uma única propriedade como o inquilino, o cliente, o utilizador ou dispositivo. Um recurso de empresa complexas planeamento da aplicação (ERP), por exemplo, tem produtos, as ordens e clientes. Normalmente, tem um esquema complexo com milhares de elevada disponibilidade interligadas tabelas.

Nenhuma estratégia de partições únicas pode aplicar a todas as tabelas e funciona entre a carga de trabalho de uma aplicação. Este artigo incida no multi-inquilinos as aplicações com dados facilmente partições e cargas de trabalho.

## <a name="multi-tenant-application-design-trade-offs"></a>Estrutura de aplicação multi-inquilino e compromissos
O padrão de conceção que um programador de aplicações de multi-inquilino escolhe normalmente baseiam-se uma consideração os seguintes fatores:

* **Isolamento de inquilinos**. O programador tem de garantir que nenhum inquilino tem acesso indesejado aos dados de outros inquilinos. Expande o requisito de isolamento para outras propriedades, tais como a fornecer proteção de vizinhos inúteis, a capacidade para restaurar dados de um inquilino e implementar as personalizações de inquilino específico.
* **Custo de recursos de nuvem**. Uma aplicação SaaS tem de ser competitiva. Um programador de aplicações de multi-inquilino pode optar por otimizar o custo inferior a utilização dos recursos de nuvem, como o armazenamento e os custos de computação.
* **Facilidade de DevOps**. Um programador de aplicações de multi-inquilino tem de incorporar a proteção de isolamento, a manter e a monitorizar o estado de funcionamento da respetiva aplicação e o esquema de base de dados e a resolver problemas de inquilino. Complexidade no desenvolvimento de aplicações e operação traduz diretamente para uma maior custo e desafia com satisfação do inquilino.
* **Escalabilidade**. A capacidade de adicionar incrementalmente mais inquilinos e a capacidade de inquilinos que precisem da mesma é imperativo para uma operação de SaaS com êxito.

Cada um destes fatores tem compromissos em comparação com a outra. A nuvem de menor custo da oferta podem não oferecer a experiência de programação mais conveniente. É importante para um programador efetuar escolhas-se informado sobre estas opções e os seus compromissos durante o processo de design da aplicação.

É um padrão de desenvolvimento popular para vários inquilinos do pacote para uma ou algumas bases de dados. As vantagens desta abordagem são um custo mais baixo porque pagar para algumas bases de dados e a simplicidade relativa de trabalhar com um número limitado de bases de dados. Mas ao longo do tempo, um programador de aplicações de multi-inquilino de SaaS será tenha em atenção que esta opção tem downsides substanciais no isolamento de inquilino e a escalabilidade. Se o isolamento de inquilinos torna-se importante, esforço adicional é necessário para proteger os dados de inquilino no armazenamento partilhado contra acesso não autorizado ou vizinhos inúteis. Este esforço adicional pode melhorar significativamente os esforços de desenvolvimento e os custos de manutenção de isolamento. Da mesma forma, se for necessário adicionar inquilinos, neste padrão de conceção normalmente requer conhecimentos redistribuir inquilino dados entre bases de dados para dimensionar corretamente a camada de dados de uma aplicação.  

Isolamento de inquilinos, muitas vezes, é um requisito fundamental em aplicações de multi-inquilinos de SaaS que o se adaptar a empresas e organizações. Um programador pode tempted por vantagens percetível no simplicidade e o custo através de isolamento de inquilino e a escalabilidade. Este compromisso pode provar complexas e dispendiosas como o crescimentos do serviço e requisitos de isolamento de inquilino passam a ser gerido na camada da aplicação e mais importante. No entanto, nas aplicações do multi-inquilinos que fornecem um serviço direto, direcionadas para o consumidor aos clientes, isolamento de inquilino poderá ser uma prioridade mais baixa que otimizar o custo de recursos de nuvem.

## <a name="multi-tenant-data-models"></a>Modelos de dados de multi-inquilino
Práticas de estrutura comum para colocação de dados do inquilino seguem três modelos distintos, mostrados na figura 1.

![modelos de dados de aplicação multi-inquilino](./media/sql-database-design-patterns-multi-tenancy-saas-applications/sql-database-multi-tenant-data-models.png)

Figura 1: Práticas de estrutura comum para modelos de dados de multi-inquilino

* **Base de dados por inquilino**. Cada inquilino tem a sua própria base de dados. Todos os dados específicos do inquilino é limitado a base de dados do inquilino e isolada de outros inquilinos e os respetivos dados.
* **Partilhado em partição horizontal da base de dados**. Vários inquilinos partilham uma das várias bases de dados. Um conjunto diferente de inquilinos é atribuído para cada base de dados através da utilização de uma estratégia de criação de partições como hash, intervalo ou criação de partições de lista. Esta estratégia de distribuição de dados, muitas vezes, é referida como fragmentação.
* **Partilhado da base de dados único**. Uma base de dados único, por vezes, grande, contém dados para todos os inquilinos, resolver esta ambiguidade numa coluna de ID de inquilino.

> [!NOTE]
> Um programador de aplicações pode optar por colocar diferentes inquilinos nos esquemas de base de dados diferente e, em seguida, utilize o nome do esquema para eliminar a ambiguidade de inquilinos diferentes. Não recomendamos esta abordagem porque normalmente requer a utilização do SQL Server dinâmico e não pode estar em vigor no plano a colocação em cache. No resto deste artigo, iremos focar-se na abordagem de tabela partilhado para esta categoria de aplicação multi-inquilino.
> 
> 

## <a name="popular-multi-tenant-data-models"></a>Modelos de dados de multi-inquilino populares
É importante avaliar os diferentes tipos de modelos de dados de multi-inquilino em termos de aplicação design compromissos que tiver já identificámos. Estes fatores ajudam caracterizam os três modelos de dados de multi-inquilino mais comuns descritos anteriormente e a respetiva utilização de base de dados, conforme mostrado na figura 2.

* **Isolamento**. O grau de isolamento de inquilinos pode ser uma medida de quantidade isolamento de inquilinos distribui de um modelo de dados.
* **Custo de recursos de nuvem**. A quantidade de partilha de recursos entre inquilinos pode otimizar o custo de recursos de nuvem. Um recurso pode ser definido como o custo de armazenamento e computação.
* **Custo de DevOps**. A facilidade de desenvolvimento de aplicações, implementação e capacidade de gestão reduz o custo global de operação de SaaS.  

Na figura 2, o eixo Y mostra o nível de isolamento de inquilino. O eixo X mostra o nível de partilha de recursos. O cinzento seta diagonal no meio indica a direção dos custos de DevOps, tending aumentar ou diminuir.

![Padrões de conceção de aplicações populares do multi-inquilino](./media/sql-database-design-patterns-multi-tenancy-saas-applications/sql-database-popular-application-patterns.png)

Figura 2: Modelos de dados de multi-inquilino Popular

O quadrant inferior direita na figura 2 mostra um padrão de aplicação que utiliza um potencialmente grande, a base de dados único partilhada e a tabela partilhada (ou esquema separada) abordagem. É boa para partilha uma vez que todos os inquilinos utilizam os mesmos recursos de base de dados (CPU, memória, entrada/saída) numa única base de dados de recursos. Mas o isolamento de inquilino é limitado. Poderá ter de efetuar passos adicionais para proteger os inquilinos entre si na camada da aplicação. Estes passos adicionais podem aumentar significativamente o custo de DevOps de desenvolver e gerir a aplicação. Escalabilidade é limitada pela escala o hardware que aloja a base de dados.

O quadrant inferior esquerda na figura 2 ilustra vários inquilinos em partição horizontal em várias bases de dados (normalmente, um hardware diferente unidades de escala). Cada base de dados aloja um subconjunto de inquilinos, que aborda a preocupação de escalabilidade de outros padrões. Se é necessária para os inquilinos mais mais capacidade, é possível colocar facilmente os inquilinos no novas bases de dados alocados para unidades de escala de hardware novo. No entanto, a quantidade de partilha de recursos é reduzida. Apenas os inquilinos colocados em unidades de escala mesmo recursos de partilha. Esta abordagem fornece pouca melhoria de isolamento de inquilinos porque muitos inquilinos ainda estão colocalizados sem automaticamente a ser protegidos de ações entre si. Complexidade de aplicação permanece elevada.

O quadrant canto superior esquerdo na figura 2 é a abordagem terceira. Coloca os dados de cada inquilino na sua própria base de dados. Esta abordagem tem propriedades de isolamento de inquilinos boa mas limita a partilha de recursos quando cada base de dados tem os seus próprios recursos dedicados. Esta abordagem é boa se todos os inquilinos têm cargas de trabalho previsíveis. Se as cargas de trabalho inquilinas ficam menos previsíveis, o fornecedor não é possível otimizar a partilha de recursos. Unpredictability é comum para aplicações SaaS. O fornecedor tem de ser o aprovisionar superior para satisfazer as exigências ou recursos inferiores. A ação resulta em custos superiores ou inferior satisfação do inquilino. Um nível superior de recurso a partilhar em inquilinos fica desejável para tornar a solução mais económica. Também o aumento do número de bases de dados aumenta o custo de DevOps para implementar e manter a aplicação. Apesar destas questões, este método fornece o isolamento melhores e mais fácil para os inquilinos.

Estes fatores também influenciam o padrão de conceção de que um cliente escolhe:

* **Propriedade de dados do inquilino**. Uma aplicação em que os inquilinos manter a propriedade dos respetivos dados favorece o padrão de uma base de dados individual por inquilino.
* **Dimensionamento**. Uma aplicação que tenha como destino centenas de milhões de inquilinos ou de milhares favorece abordagens, tais como a fragmentação de partilha de base de dados. Requisitos de isolamento ainda podem implicar desafios.
* **Modelo de valor e o negócio**. Se uma aplicação por-inquilino através da receita se pequenos (menos do que um dólar), requisitos de isolamento de se tornar menos críticos e uma base de dados partilhada faz sentido. Se a receita por inquilino é igual ou superior a alguns utilizados no compromisso, um modelo de base de dados por inquilino é mais exequível. Podem ajudar a reduzir os custos de desenvolvimento.

Tendo em conta os design compromissos mostrados na figura 2, necessita de um modelo de multi-inquilino ideal incorporar as propriedades de isolamento de inquilino boa com recurso ideal partilha entre inquilinos. Este modelo encaixa na categoria descrita a quadrant superior direito da figura 2.

## <a name="multi-tenancy-support-in-azure-sql-database"></a>Suporte de vários inquilinos na SQL Database do Azure
Base de dados SQL do Azure suporta todos os padrões de aplicação multi-inquilino descritos na figura 2. Com conjuntos elásticos, também suporta um padrão de aplicação que combina a partilha de recursos boa e as vantagens de isolamento da base de dados por inquilino abordam (consulte o quadrant superior direito na figura 3). Ferramentas de base de dados elástica e capacidades na base de dados do SQL Server ajudam a reduzir o custo de desenvolver e utilizar uma aplicação que tem muitas bases de dados (apresentadas na área sombreada na figura 3). Estas ferramentas podem ajudar a criar e gerir aplicações que utilizam as os padrões de múltiplas base de dados.

![Padrões de base de dados SQL do Azure](./media/sql-database-design-patterns-multi-tenancy-saas-applications/sql-database-patterns-sqldb.png)

Figura 3: Padrões de aplicação multi-inquilino na SQL Database do Azure

## <a name="database-per-tenant-model-with-elastic-pools-and-tools"></a>Modelo de base de dados por inquilino com as ferramentas e conjuntos elásticos
Conjuntos elásticos na base de dados do SQL Server combinam o isolamento de inquilinos com partilha entre bases de dados do inquilino para suportar melhor a abordagem de base de dados por inquilino de recursos. Base de dados do SQL Server é uma solução de camada de dados para fornecedores de SaaS que criam aplicações da multi-inquilinos. O fardo de recurso de partilha entre inquilinos altera da camada de aplicação para a camada de serviço de base de dados. A complexidade da gestão e consultar à escala em bases de dados é simplificada com as tarefas elásticas, consulta elástica, transações elásticas e a biblioteca de clientes de base de dados elásticas.

| Requisitos da aplicação | Capacidades de base de dados do SQL Server |
| --- | --- |
| Isolamento de inquilino e a partilha de recursos |[Conjuntos elásticos](sql-database-elastic-pool.md): atribua um conjunto de recursos de base de dados SQL e partilhar os recursos em várias bases de dados. Além disso, bases de dados individuais podem desenhar a maior quantidade de recursos do agrupamento, conforme necessário para acomodar os picos de procura de capacidade devido a alterações em cargas de trabalho inquilinas. O conjunto elástico em si pode ser escalado para cima ou para baixo conforme necessário. Conjuntos elásticos fornecem também a facilidade de gestão e monitorização e resolução de problemas ao nível do conjunto. |
| Facilidade de DevOps entre bases de dados |[Conjuntos elásticos](sql-database-elastic-pool.md): conforme indicado anteriormente. |
| | [Consulta elástica](sql-database-elastic-query-horizontal-partitioning.md): consulta em bases de dados para relatórios ou entre-inquilinos Analysis Services. |
| | [As tarefas elásticas](sql-database-elastic-jobs-overview.md): pacote e fiável implementar ou alterações de esquema de base de dados de operações de manutenção de base de dados em várias bases de dados. |
| | [Transações elásticas](sql-database-elastic-transactions-overview.md): processo altera para várias bases de dados de uma forma Atómica e isolada. Transações elásticas são necessários quando as aplicações precisam de garantias de "todas ou nada" através de várias operações de base de dados. |
| | [Biblioteca de clientes de base de dados elástica](sql-database-elastic-database-client-library.md): gerir as distribuições de dados e o mapeamento de inquilinos para bases de dados. |

## <a name="shared-models"></a>Modelos partilhados
Conforme descrito anteriormente, para a maioria dos fornecedores de SaaS, uma abordagem de modelo partilhado possa implicar problemas com problemas de isolamento de inquilino e complexidades com o desenvolvimento de aplicações e de manutenção. No entanto, para aplicações de multi-inquilino que fornecem um serviço diretamente aos consumidores, requisitos de isolamento de inquilino não podem ser uma prioridade tão elevada como minimizar os custos. Estes poderão pacote inquilinos num ou mais bases de dados numa alta densidade para reduzir os custos. Modelos de base de dados partilhada a utilizar uma base de dados única ou várias bases de dados em partição horizontal poderão resultar em resulta numa eficiência adicionais no custo de partilha e geral de recursos. Base de dados SQL do Azure fornece isolamento para a gestão à escala na camada de dados e de segurança melhorada de compilação de algumas funcionalidades que ajudam os clientes.

| Requisitos da aplicação | Capacidades de base de dados do SQL Server |
| --- | --- |
| Funcionalidades de isolamento de segurança |[Segurança ao nível da linha](https://msdn.microsoft.com/library/dn765131.aspx) |
| [Esquema de base de dados](https://msdn.microsoft.com/library/dd207005.aspx) | |
| Facilidade de DevOps entre bases de dados |[Consulta elástica](sql-database-elastic-query-horizontal-partitioning.md) |
| | [Tarefas elásticas](sql-database-elastic-jobs-overview.md) |
| | [Transações elásticas](sql-database-elastic-transactions-overview.md) |
| | [Biblioteca de clientes da base de dados elástica](sql-database-elastic-database-client-library.md) |
| | [Divisão de base de dados elástica e intercalação](sql-database-elastic-scale-overview-split-and-merge.md) |

## <a name="summary"></a>Resumo
Requisitos de isolamento de inquilino são importantes para a maioria das aplicações de multi-inquilinos de SaaS. A melhor opção para proporcionar o isolamento leans descontos elevados para a abordagem de base de dados por inquilino. As outras duas abordagens requerem os investimentos em camadas de aplicações complexas que necessitam da equipa de desenvolvimento intruso qualificado para proporcionar o isolamento, o que aumenta significativamente o custo e risco. Se os requisitos de isolamento não são contabilizados numa fase inicial no desenvolvimento de serviço, pode ser ainda mais dispendioso no primeiro dois modelos retrofitting-los. As principais desvantagens associadas ao modelo de base de dados por inquilino estão relacionados com os custos de recursos de nuvem de uma maior devido a partilha reduzida e a manutenção e a gestão de muitas bases de dados. Os programadores de aplicações SaaS dificuldade frequentemente quando efetuam estes compromissos.

Embora compromissos poderão ser principais barreiras as eficazes com a maioria dos fornecedores de serviços de base de dados de nuvem, SQL Database do Azure elimina as barreiras as eficazes com o respetivo conjunto elástico e capacidades de bases de dados elásticas. Os programadores de SaaS podem combinar as características de isolamento de um modelo de base de dados por inquilino e otimizar a partilha de recursos e os melhoramentos de capacidade de gestão de muitas bases de dados utilizando conjuntos elásticos e ferramentas associadas.

Fornecedores de aplicação multi-inquilino que tenham requisitos de isolamento de inquilino e quem pode pacote inquilinos numa base de dados numa alta densidade poderão achar que os dados partilhados modelos resultado na eficiência adicional na partilha de recursos e reduzir o custo global. Ferramentas de base de dados elástica de base de dados SQL do Azure, bibliotecas de fragmentação e funcionalidades de segurança ajudam a fornecedores de SaaS, criar e gerir aplicações de multi-inquilinos.

## <a name="next-steps"></a>Passos seguintes
[Começar a utilizar as ferramentas de base de dados elástica](sql-database-elastic-scale-get-started.md) com uma aplicação de exemplo que demonstra a biblioteca de clientes.

Criar um [dashboard personalizado do conjunto elástico saas](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools-custom-dashboard) com uma aplicação de exemplo que utiliza conjuntos elásticos para uma solução económica e dimensionável da base de dados.

Utilizar as ferramentas do SQL Database do Azure para [Migrar bases de dados existentes para aumentar horizontalmente](sql-database-elastic-convert-to-use-elastic-tools.md).

Para criar um conjunto elástico com o portal do Azure, consulte [criar um conjunto elástico](sql-database-elastic-pool-manage-portal.md).  

Saiba como [monitorizar e gerir um conjunto elástico](sql-database-elastic-pool-manage-portal.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Implementar e explorar uma aplicação de multi-inquilino que utiliza a base de dados SQL do Azure - Wingtip SaaS](sql-database-saas-tutorial.md)
* [O que é um conjunto elástico do Azure?](sql-database-elastic-pool.md)
* [Aumentar horizontalmente com a Base de Dados SQL do Azure](sql-database-elastic-scale-introduction.md)
* [aplicações de multi-inquilinos com ferramentas de base de dados elástica e a segurança ao nível da linha](sql-database-elastic-tools-multi-tenant-row-level-security.md)
* [Autenticação multi-inquilino de aplicações utilizando o Azure Active Directory e o OpenID Connect](../guidance/guidance-multitenant-identity-authenticate.md)
* [Aplicação Tailspin Surveys](../guidance/guidance-multitenant-identity-tailspin.md)


## <a name="questions-and-feature-requests"></a>Perguntas e pedidos de funcionalidades

Para perguntas sobre, encontre-no [fórum de base de dados SQL](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted). Adicionar um pedido de funcionalidade no [fórum de comentários da base de dados SQL](https://feedback.azure.com/forums/217321-sql-database/).

