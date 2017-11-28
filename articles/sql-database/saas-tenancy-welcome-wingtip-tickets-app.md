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
ms.date: 11/17/2017
ms.author: billgib
ms.openlocfilehash: 2a36df0e45af5bcce5338d04b7e1ba44221ae964
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="the-wingtip-tickets-saas-application"></a>A aplicação de Wingtip bilhetes SaaS

O mesmo *Wingtip bilhetes* aplicação é implementada em cada uma das três amostras. A aplicação é um evento simple de listagem e relatórios de aplicações SaaS filtragem pequenos venues - theaters, clubs, etc. Cada venue é um inquilino da aplicação e se tem os seus próprios dados: venue detalhes, listas de eventos, os clientes, as ordens de permissão, etc.  A aplicação, juntamente com os scripts de gestão e tutoriais, showcases um cenário de SaaS ponto-a-ponto. Isto inclui o aprovisionamento de inquilinos, monitorizar e gerir o desempenho, gestão de esquema e inquilinos entre análises e relatórios.

## <a name="three-saas-application-patterns"></a>Três padrões de aplicações SaaS

Estão disponíveis; três versões da aplicação cada explicar um padrão de inquilinos de base de dados diferente na SQL Database do Azure.  O primeiro utiliza uma aplicação de inquilino único com uma base de dados de inquilino único isolado. O segundo utiliza uma aplicação de multi-inquilino, com uma base de dados por inquilino. O terceiro exemplo utiliza uma aplicação multi-inquilino com a bases de dados do multi-inquilinos.

![Três padrões de inquilinos][image-three-tenancy-patterns]

 Cada amostra inclui scripts de gestão e tutoriais explorar um intervalo de design e padrões de gestão, que pode utilizar na sua própria aplicação.  Cada amostra implementa no menor do que cinco minutos.  Todas as três podem ser implementado lado lado a lado, pelo que pode comparar as diferenças na conceção e na gestão.

## <a name="standalone-application-pattern"></a>Padrão de aplicação autónoma

O padrão de aplicação autónomo utiliza uma aplicação de inquilino único com uma base de dados de inquilino único para cada inquilino. Aplicação de cada inquilino é implementada num grupo de recursos do Azure separado. Isto pode ser na subscrição do fornecedor de serviços ou de subscrição do inquilino e gerido pelo fornecedor em nome do inquilino. Este padrão fornece o isolamento de inquilino é maior, mas é, normalmente, mais dispendioso vez que não há nenhuma oportunidade de partilhar recursos em vários inquilinos.

Veja o [tutoriais] [ docs-tutorials-for-wingtip-sa] e código no GitHub [.../Microsoft/WingtipTicketsSaaS-StandaloneApp][github-code-for-wingtip-sa].

## <a name="database-per-tenant-pattern"></a>Base de dados por padrão de inquilino

A base de dados por padrão de inquilino é eficaz para fornecedores de serviços que estão preocupados com o isolamento de inquilino e pretendem executar um serviço centralizado que permite a utilização de baixo custo de recursos partilhados. É criada uma base de dados para cada venue ou inquilino e todas as bases de dados são geridas centralmente. Bases de dados podem ser alojados no conjuntos elásticos para fornecer económico e uma gestão mais fácil de desempenho, que tira partido do padrões de carga de trabalho imprevisíveis aos inquilinos. Uma base de dados do catálogo detém o mapeamento entre inquilinos e as bases de dados. Este mapeamento é gerido utilizando as funcionalidades de gestão de mapa de partições horizontais do [biblioteca de clientes de base de dados elásticas](sql-database-elastic-database-client-library.md), que fornece uma gestão eficiente de ligação à aplicação.

Veja o [tutoriais] [ docs-tutorials-for-wingtip-dpt] e código no GitHub [.../Microsoft/WingtipTicketsSaaS-DbPerTenant][github-code-for-wingtip-dpt].

## <a name="sharded-multi-tenant-database-pattern"></a>Padrão da base de dados do multi-inquilino

Bases de dados do multi-inquilinos são eficazes para fornecedores de serviços à procura de custo mais baixo por inquilino e há problema com o isolamento de inquilino reduzida. Este padrão permite packing grande número de inquilinos numa base de dados único, a impulsionar o custo por inquilino para baixo. Escala infinita near é possível ao fragmentação através da base de dados de vários inquilinos.  Uma base de dados do catálogo novamente mapeia inquilinos para bases de dados.  

Este padrão também permite que um modelo de híbrida, na qual pode otimizar o custo com múltiplos inquilinos numa base de dados ou otimizar para isolamento com um inquilino único na sua própria base de dados. Se a opção pode ser efetuada numa base de inquilino por inquilino quando o inquilino seja aprovisionado ou posterior, sem afetar a aplicação.

Veja o [tutoriais] [ docs-tutorials-for-wingtip-mt] e código no GitHub [.../Microsoft/WingtipTicketsSaaS-MultiTenantDb][github-code-for-wingtip-mt].

## <a name="next-steps"></a>Passos seguintes

#### <a name="conceptual-descriptions"></a>Descrições conceptuais

- Uma explicação mais detalhada de padrões de inquilinos a aplicação está disponível em [padrões de inquilinos da base de dados de SaaS multi-inquilino][saas-tenancy-app-design-patterns-md]

#### <a name="tutorials-and-code"></a>Tutoriais e código

- Aplicação autónoma:
    - [Tutoriais para a aplicação autónoma][docs-tutorials-for-wingtip-sa].
    - [Código autónomo, no GitHub][github-code-for-wingtip-sa].

- Base de dados por inquilino:
    - [Tutoriais para a base de dados por inquilino][docs-tutorials-for-wingtip-dpt].
    - [Código de base de dados por inquilino, no GitHub][github-code-for-wingtip-dpt].

- A multi-inquilino:
    - [Tutoriais para a multi-inquilino][docs-tutorials-for-wingtip-mt].
    - [Código para a multi-inquilino, no GitHub][github-code-for-wingtip-mt].



<!-- Image references. -->

[image-three-tenancy-patterns]: media/saas-tenancy-welcome-wingtip-tickets-app/three-tenancy-patterns.png "Três padrões de inquilinos."

<!-- Docs.ms.com references. -->

[saas-tenancy-app-design-patterns-md]: saas-tenancy-app-design-patterns.md

<!-- WWWeb http references. -->

[docs-tutorials-for-wingtip-sa]: https://aka.ms/wingtipticketssaas-sa
[github-code-for-wingtip-sa]: https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp

[docs-tutorials-for-wingtip-dpt]: https://aka.ms/wingtipticketssaas-dpt
[github-code-for-wingtip-dpt]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant

[docs-tutorials-for-wingtip-mt]: https://aka.ms/wingtipticketssaas-mt
[github-code-for-wingtip-mt]: https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb

