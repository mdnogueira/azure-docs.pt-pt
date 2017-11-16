---
title: "Bem-vindo à aplicação Wingtips - base de dados SQL do Azure | Microsoft Docs"
description: "Saiba mais sobre modelos de inquilinos de base de dados e sobre a aplicação de Wingtips SaaS de exemplo, para a base de dados do Azure SQL Server no ambiente de nuvem."
keywords: tutorial de base de dados sql
services: sql-database
documentationcenter: 
author: billgib
manager: craigg
editor: MightyPen
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Active
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/12/2017
ms.author: billgib;genemi
ms.openlocfilehash: e10a954ba57782f4f79131ab583b5a73edf4ba02
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2017
---
# <a name="welcome-to-the-wingtip-tickets-sample-saas-azure-sql-database-tenancy-app"></a>Bem-vindo à aplicação de inquilinos de SQL Database do Azure SaaS de amostra de pedidos de Wingtip

Bem-vindo à aplicação Wingtip bilhetes exemplo SQL Database do Azure SaaS inquilinos e os tutoriais. A arquitetura de base de dados refere-se para o modo de isolamento de dados que a aplicação fornece aos clientes que pagar para ser alojada na sua aplicação. A ativação pós-falha simplificar neste momento, a cada cliente tem uma base de dados de todo a próprio ou partilha uma base de dados com outro cliente.

## <a name="wingtip-tickets-app"></a>Aplicação de bilhetes Wingtip

A aplicação de exemplo Wingtip bilhetes ilustra os efeitos de modelos de inquilinos de base de dados diferente na conceção e gestão de aplicações de SaaS multi-inquilino. Os tutoriais associados diretamente descrevem esses mesmos efeitos. Pedidos de suporte de Wingtip está incorporado no SQL Database do Azure.

Pedidos de suporte de Wingtip foi concebido para processar vários cenários de design e de gestão que são utilizados pelos clientes de SaaS reais. Os padrões de utilização emerged são contabilizados no Wingtip pedidos de suporte.

Pode instalar a aplicação de bilhetes Wingtip na sua própria subscrição do Azure em cinco minutos. A instalação inclui a inserção de dados de exemplo para vários inquilinos. Em segurança pode instalar a aplicação e os scripts de gestão para todos os modelos, porque as instalações não interagir ou interferir com entre si.

#### <a name="code-in-github"></a>Código no Github

Código da aplicação e os scripts de gestão, estão disponíveis no GitHub:

- **Aplicação autónoma** modelo: *(disponíveis dentro de dias).*
- **Base de dados por inquilino** modelo: [WingtipSaaS repositório](https://github.com/Microsoft/WingtipSaaS/).
- **A multi-inquilino** modelo, o *híbrida*: *(disponíveis dentro de dias).*

O mesmo código de uma base para a aplicação de bilhetes Wingtip é reutilizado para todos os modelos de anteriores listados. Pode utilizar o código a partir do Github para iniciar os seus projetos de SaaS.



## <a name="major-database-tenancy-models"></a>Modelos de inquilinos de base de dados principais

Pedidos de suporte de Wingtip é um evento de listagem e de emissão de permissões de aplicação SaaS. Wingtip fornece serviços que são necessárias por venues. Todos os itens seguintes são aplicáveis a cada venue:

- Pays a ser alojado na sua aplicação.
- É um *inquilino* no Wingtip.
- Eventos de anfitriões. Os seguintes eventos são relacionados:
    - Preços de permissão.
    - Vendas de permissão.
    - Clientes que comprar pedidos de suporte.

A aplicação, juntamente com os scripts de gestão e tutoriais, showcases um cenário de SaaS completo. O cenário inclui as seguintes atividades:

- Aprovisionamento de inquilinos.
- Monitorizar e gerir o desempenho.
- Gestão de esquema.
- Entre-inquilinos de relatórios e análise.

Todas essas atividades são fornecidas em qualquer escala é necessária.



## <a name="code-samples-for-each-tenancy-model"></a>Exemplos de código para cada modelo de inquilinos

Um conjunto de modelos de aplicação merecem. No entanto, outras implementações podem misturar elementos de dois ou mais modelos.

#### <a name="standalone-app-model"></a>Modelo de aplicação autónoma

![Modelo de aplicação autónoma][standalone-app-model-62s]

Este modelo utiliza uma aplicação de inquilino único. Por conseguinte, este modelo tem apenas uma base de dados e armazena os dados para o um inquilino. O inquilino gostar concluído isolamento de outros inquilinos na base de dados.

Pode utilizar este modelo quando propor instâncias da sua aplicação para muitos clientes diferentes, para cada cliente a executar seu próprio. O cliente, em seguida, é o único inquilino. Enquanto a base de dados armazena os dados para apenas um cliente, a base de dados armazena os dados para muitos clientes do cliente.

- *(Tutoriais para este modelo serão publicados aqui dentro de alguns dias. Uma ligação será aqui.)*

#### <a name="database-per-tenant"></a>Base de dados por inquilino

![Base de dados por modelo de inquilino][database-per-tenant-model-35d]

Este modelo tem vários inquilinos na instância da aplicação. Ainda para cada inquilino novo, outra base de dados está alocado para utilização apenas por ao novo inquilino.

Este modelo fornece isolamento de segurança completa para cada inquilino. O serviço SQL Database do Azure tem sofisticação para tornar este modelo plausible.

- [Introdução a um exemplo de aplicação de SaaS de multi-inquilino de base de dados SQL] [ saas-dbpertenant-wingtip-app-overview-15d] -tem mais informações sobre este modelo.

#### <a name="sharded-multi-tenant-databases-the-hybrid"></a>A multi-inquilinos bases de dados, a híbrida

![Modelo da base de dados do multi-inquilino, o híbrida][sharded-multitenantdb-model-hybrid-79m]

Este modelo tem vários inquilinos na instância da aplicação. Este modelo também tem vários inquilinos em algumas ou todas as suas bases de dados. Este modelo é boa para oferta diferentes escalões de serviço para que os clientes podem paga mais se o valor total de isolamento de base de dados.

O esquema de cada base de dados inclui um identificador de inquilino. O identificador de inquilino é mesmo nessas bases de dados que armazenam apenas um inquilino.

- *(Tutoriais para este modelo serão publicados aqui dentro de alguns dias. Uma ligação será aqui.)*




## <a name="tutorials-for-each-tenancy-model"></a>Tutoriais para cada modelo de inquilinos

Cada modelo de inquilinos é descrito pelo seguinte:

- Um conjunto de artigos tutorial.
- Código de origem armazenado no repositório do Github que se encontra dedicado para o modelo:
    - O código para a aplicação de Wingtip pedidos de suporte.
    - O código de script para cenários de gestão.

#### <a name="tutorials-for-management-scenarios"></a>Tutoriais para cenários de gestão

Os artigos tutorial para cada modelo abrangem os seguintes cenários de gestão:

- Aprovisionamento do inquilino.
- Monitorização de desempenho e gestão.
- Gestão de esquema.
- Entre-inquilinos de relatórios e análise.
- Restauro de um inquilino para um ponto anterior no tempo.
- Recuperação após desastre.



## <a name="next-steps"></a>Passos seguintes

- [Introdução a um exemplo de aplicação de SaaS de multi-inquilino de base de dados SQL] [ saas-dbpertenant-wingtip-app-overview-15d] -tem mais informações sobre este modelo.

- [Padrões de inquilinos de base de dados de SaaS multi-inquilino][multi-tenant-saas-database-tenancy-patterns-60p]



<!-- Image references. -->

[standalone-app-model-62s]: media/saas-tenancy-welcome-wingtip-tickets-app/model-standalone-app.png "Modelo de aplicação autónoma"

[database-per-tenant-model-35d]: media/saas-tenancy-welcome-wingtip-tickets-app/model-database-per-tenant.png "Base de dados por modelo de inquilino"

[sharded-multitenantdb-model-hybrid-79m]: media/saas-tenancy-welcome-wingtip-tickets-app/model-sharded-multitenantdb-hybrid.png "Modelo da base de dados do multi-inquilino, o híbrida"



<!-- Article references. -->

[saas-dbpertenant-wingtip-app-overview-15d]: saas-dbpertenant-wingtip-app-overview.md

[multi-tenant-saas-database-tenancy-patterns-60p]: saas-tenancy-app-design-patterns.md

