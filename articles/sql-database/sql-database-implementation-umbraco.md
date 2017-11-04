---
title: "Base de dados SQL do Azure do Azure caso prático - Umbraco | Microsoft Docs"
description: "Saiba mais sobre como Umbraco utiliza a base de dados SQL para aprovisionar rapidamente serviços e de escala de milhares de inquilinos na nuvem"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 5243d31e-3241-4cb0-9470-ad488ff28572
ms.service: sql-database
ms.custom: reference
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: Inactive
ms.date: 01/10/2017
ms.author: carlrab
ms.openlocfilehash: c76ba2189929113c2c6fbdf13f0c9b2b714ae73b
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="umbraco-uses-azure-sql-database-to-quickly-provision-and-scale-services-for-thousands-of-tenants-in-the-cloud"></a>Umbraco utiliza SQL Database do Azure para os serviços rapidamente aprovisionar e escala para milhares de inquilinos na nuvem
![Logótipo de Umbraco](./media/sql-database-implementation-umbraco/umbracologo.png)

Umbraco é um populares open source gestão de conteúdo (CMS) que pode executar nada de sites de campanha ou brochure pequenos para aplicações complexas para as empresas de Fortune 500 e Web sites do suporte de dados globais. 

> "Temos bastante uma grande Comunidade de programadores que utilizam o sistema, com mais de 100 000 programadores nos nossos fóruns e 350,000 mais de sites que estão em direto, executar Umbraco."
> 
> — Morten Christensen, oportunidades potenciais técnica, Umbraco
> 
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-Case-Study-Umbraco/player]
> 
> 

Para simplificar as implementações de cliente, Umbraco adicionado Umbraco-como-um-serviço (UaaS): uma oferta de software-como-um-serviço (SaaS) que elimina a necessidade de implementações no local, fornece a ampliação incorporadas e remove a gestão de sobrecarga, permitindo aos programadores ao concentrar-se na inovação em vez de solução de gestão do produto. Umbraco é capaz de fornecer todas as vantagens pela entidade confiadora no flexível plataforma-como-um-serviço (PaaS) modelo de oferecidas pelo Microsoft Azure.

UaaS permite aos clientes SaaS utilizar Umbraco CMS capacidades que foram anteriormente fora do seu alcance. Estes clientes são aprovisionados com um ambiente de CMS de trabalho que inclui uma base de dados de produção. Os clientes, podem adicionar até duas bases de dados adicionais para desenvolvimento e ambientes de testes, consoante os requisitos. Quando é pedido um novo ambiente, um processo automatizado atribui desse cliente uma base de dados automaticamente. Nova base de dados está pronto em segundos, porque a base de dados já foi previamente aprovisionada por Umbraco do Azure agrupamento elástico de bases de dados disponíveis (consulte a figura 1).

![Ciclo de vida de aprovisionamento Umbraco](./media/sql-database-implementation-umbraco/figure1.png)

Figura 1. Ciclo de vida de aprovisionamento para Umbraco como um serviço (UaaS)

## <a name="azure-elastic-pools-and-automation-simplify-deployments"></a>Azure conjuntos elásticos e automatização simplificam as implementações
Com a SQL Database do Azure e outros serviços do Azure, Umbraco clientes podem aprovisionar respetivos ambientes e Umbraco facilmente pode monitorizar e gerir bases de dados como parte de um fluxo de trabalho intuitivo:

1. Aprovisionar
   
   Umbraco mantém uma capacidade de 200 pré-aprovisionada as bases de dados de conjuntos elásticos. Quando um cliente novo inscreve UaaS, Umbraco fornece o cliente com um novo ambiente CMS em tempo real ao atribuir-lhes uma base de dados do conjunto de disponibilidade.
   
   Quando um conjunto de disponibilidade atinge o limiar, é criado um novo conjunto elástico e novas bases de dados são pré-aprovisionados a atribuir aos clientes conforme necessário.
   
   Implementação é totalmente automatizada utilizando bibliotecas de gestão do c# e filas do Service Bus do Azure.
2. Utilizar
   
   Os clientes utilizar uma a três ambientes (para produção, transição, e/ou desenvolvimento), cada com a sua própria base de dados. Bases de dados do cliente estão em conjuntos elásticos, que permite Umbraco proporcionar dimensionamento eficiente sem ter de aprovisionar excessiva.
   
   ![Descrição geral do projeto Umbraco](./media/sql-database-implementation-umbraco/figure2.png)
   
   ![Detalhe de projeto Umbraco](./media/sql-database-implementation-umbraco/figure3.png)
   
   Figura 2. Web site do cliente Umbraco-como-um-serviço (UaaS), que mostra detalhes e descrição geral do projeto
   
   Base de dados SQL do Azure utiliza unidades de transação de base de dados (DTUs) para representar o poder relativo necessário para transações da base de dados do mundo real. Para clientes de UaaS, bases de dados funcionam normalmente em cerca de 10 DTUs, mas cada um tem a elasticidade para dimensionar a pedido. Isto significa que UaaS pode Certifique-se de que os clientes têm sempre recursos necessários, mesmo em situações das horas de ponta. Por exemplo, durante um evento de desporto Domingos recente, uma UaaS cliente experiente base de dados picos até 100 DTUs durante o jogos. Azure conjuntos elásticos disponibilizada Umbraco suportar essa elevada procura sem degradação do desempenho.
3. Monitorizar
   
   Monitores de Umbraco da base de dados atividade utilizar os dashboards no portal do Azure, juntamente com alertas de e-mail personalizadas.
4. Recuperação após desastre
   
   Azure fornece duas opções de recuperação após desastre (DR): georreplicação ativa e georrestauro. A opção de DR que deve selecionar uma empresa depende em seu [objetivos de continuidade do negócio](sql-database-business-continuity.md).
   
   replicação geográfica activa fornece o nível mais rápido de resposta em caso de um período de indisponibilidade. Utilizar a georreplicação ativa, pode criar até quatro bases de dados secundárias legíveis em servidores em regiões diferentes e, em seguida, pode iniciar a ativação pós-falha de bases de dados secundárias em caso de falha.
   
   Umbraco não requer a georreplicação, mas tirar partido do Azure georrestauro para ajudar a garantir o período de indisponibilidade mínimo na eventualidade de ocorrer uma falha. georrestauro baseia-se em cópias de segurança da base de dados no armazenamento do Azure com redundância geográfica. Que permite aos utilizadores restaurar a partir de uma cópia de segurança quando houver uma falha na região primária.
5. Aprovisionar desativação
   
   Quando é eliminado um ambiente de projeto, são removidas quaisquer bases de dados associadas (desenvolvimento, teste ou em direto) durante a limpeza de filas do Service Bus do Azure. Este processo automatizado restaura as bases de dados não utilizados para o conjunto de disponibilidade de base de dados elásticas do Umbraco, disponibilizando-as para o aprovisionamento de futuro, mantendo a máxima utilização.

## <a name="elastic-pools-allow-uaas-to-scale-with-ease"></a>Conjuntos elásticos permitem UaaS dimensionem com facilidade
Ao tirar partido dos conjuntos elásticos do Azure, Umbraco pode otimizar o desempenho para que os seus clientes sem ter de ativação pós-falha - ou o under-aprovisionar. Umbraco tem atualmente quase 3,000 bases de dados em 19 conjuntos elásticos, com a capacidade de dimensionar facilmente conforme necessário para contemplar qualquer um dos respetivos 325,000 clientes existentes ou novos clientes que estão prontos para implementar um CMS na nuvem.

Na verdade, de acordo com Morten Christensen, levar técnica em Umbraco, "UaaS agora estão a ocorrer crescimento de cerca de 30 novos clientes por dia. Os nossos clientes são delighted com a conveniência da capacidade de aprovisionar novos projetos em segundos, de forma instantânea publicar atualizações para os respetivos sites em direto de um ambiente de desenvolvimento utilizando a 'implementação de um clique' e efetuar alterações, tal como a rapidamente se encontrarem erros."

Se um cliente não necessita de um segundo e/ou terceiro ambiente, pode simplesmente remova nesses ambientes. Que liberta recursos que podem ser utilizados para outros clientes como parte do conjunto de disponibilidade de base de dados elásticas Umbraco.

![Arquitetura de implementação Umbraco](./media/sql-database-implementation-umbraco/figure4.png)

Figura 3. Arquitetura de implementação de UaaS no Microsoft Azure

## <a name="the-path-from-datacenter-to-cloud"></a>O caminho do Centro de dados na nuvem
Quando os programadores de Umbraco inicialmente efetuada a decisão de passar para um modelo de SaaS, estes deverem que têm uma forma económica e dimensionável para terminar o serviço de compilação.

> "conjuntos elásticos são uma opção perfeita para a nossa oferta de SaaS porque, pode marcar capacidade e reduzir verticalmente conforme necessário. O aprovisionamento é fácil e com a nossa configuração, vamos manter as utilização no máximo."
> 
> — Morten Christensen, oportunidades potenciais técnica, Umbraco
> 
> 

"Queremos gaste nosso tempo em resolver problemas dos nossos clientes, não infraestrutura a gerir. Queremos torna mais fácil para os nossos clientes obter o máximo valor,"indica Niels Hartvig, founder de Umbraco. "É considerado inicialmente que alojam os servidores ourselves, mas o planeamento de capacidade seria ter um nightmare." Por coincidência, Umbraco não utilizar qualquer administradores de base de dados, que uma proposta de valor de chave para utilizar UaaS de carateres de sublinhado.

Foi um objetivo importante para os programadores de Umbraco para fornecer uma forma para que os clientes UaaS aprovisionar ambientes rapidamente e sem limitações de capacidade. Mas, fornecer um serviço alojado dedicado nos centros de dados Umbraco necessitaria muitos excesso de capacidade para processar bursts no processamento. Que seria ter se destinam a adição de infraestrutura de computação considerável que poderia ter sido regularmente subutilizada.

Além disso, a equipa de desenvolvimento Umbraco pretendia uma solução que lhe permita-reutilizar como grande parte do respetivo código existente quanto possível. Como Umbraco programadores Estados Mikkel Madsen, "foi satisfeitos com as ferramentas de desenvolvimento Microsoft que já foi familiarizados, como o Microsoft SQL Server, a base de dados do Microsoft Azure SQL, ASP.net e serviços de informação Internet (IIS). Antes de investir num IaaS ou uma PaaS na nuvem solução, queremos certificar-se de que o que seria suporta os nossos ferramentas da Microsoft e plataformas, para que não tenhamos que efetuar alterações em grande escala ao nosso código base. "

Para cumprir todos os respetivos critérios, Umbraco comparados para um parceiro da nuvem com as qualificações seguintes:

* Fiabilidade e capacidade suficiente
* Suporte para as ferramentas de desenvolvimento Microsoft, para que os engenheiros Umbraco não for forçados a reinvent completamente o respetivo ambiente de desenvolvimento
* Presença em todas os mercados geográficos na qual UaaS compete (empresas é necessário para garantir que estes podem aceder aos respetivos dados rapidamente e que os dados são armazenados numa localização que cumpra os requisitos de regulamentação regionais)

## <a name="why-umbraco-chose-azure-for-uaas"></a>Por que motivo o Umbraco selecionou do Azure para UaaS
De acordo com Morten Christensen "após a consideração dos nossas as opções, selecionamos Azure porque-cumprido todos os nossos dos critérios, da capacidade de gestão e a escalabilidade familiaridade e eficácia de custos. Configuramos ambientes em VMs do Azure e cada ambiente tem a suas próprias instância de SQL Database do Azure, com todas as instâncias conjuntos elásticos. Ao separar as bases de dados entre o desenvolvimento, teste e ambientes em direto, pode oferecer os nossos clientes isolamento do desempenho robusta corresponder à escala — uma enorme win. "

Morten continua, "antes, tivemos de aprovisionar servidores de bases de dados web manualmente. Agora, não temos de pensar em-lo. Tudo é um processo automatizado — desde o aprovisionamento para limpeza. "

Morten também é satisfeito com as capacidades de dimensionamento fornecidas pelo Azure. "conjuntos elásticos são uma opção perfeita para a nossa oferta de SaaS porque, pode marcar capacidade e reduzir verticalmente conforme necessário. O aprovisionamento é fácil e com a nossa configuração, vamos manter as utilização no máximo." Estados de Morten, "simplicidade dos conjuntos elásticos, juntamente com a garantia de DTUs baseada em camada de serviço, fornece-na potência para aprovisionar novos agrupamentos de recursos no pedido. Recentemente, um dos nossos clientes maior peaked para 100 DTUs no respetivo ambiente em direto. Utilizar o Azure, o nosso conjuntos elásticos fornecido bases de dados do cliente com os recursos que são necessários em tempo real sem ter de prever os requisitos de DTU. Resumindo, os nossos clientes obter a hora de desative-around esperam e pode satisfazer os nossos contratos de nível de serviço de desempenho."

Mikkel Madsen soma-la: "estamos tiver embraced o algoritmo de Azure poderoso que liga um cenário comum de SaaS (integração novos clientes em tempo real na escala) a nossa padrão de aplicação (pré-aprovisionar bases de dados, o desenvolvimento e live) em cima subjacentes tecnologia (utilizando as filas do Service Bus do Azure em conjunto com a SQL Database do Azure)."

## <a name="with-azure-uaas-is-exceeding-customer-expectations"></a>Com o Azure, UaaS está a exceder as expectativas de cliente
Dado que escolher Azure como o parceiro de nuvem, Umbraco foi capaz de fornecer UaaS clientes com gestão de conteúdo um desempenho otimizado, sem o investimento de IT recursos necessário a partir de uma solução personalizada alojada. Como Morten indica, "estamos adoram a comodidade do programador e a escalabilidade que dá-na Azure e os nossos clientes são thrilled com as funcionalidades e fiabilidade. Em geral, foi um excelente win para-nos!"

## <a name="more-information"></a>Mais informações
* Para obter mais informações sobre conjuntos elásticos do Azure, consulte o artigo [conjuntos elásticos](sql-database-elastic-pool.md).
* Para saber mais sobre o Service Bus do Azure, consulte o artigo [Service Bus do Azure](https://azure.microsoft.com/services/service-bus/).
* Para obter mais informações sobre funções da Web e funções de trabalho, consulte o artigo [as funções de trabalho](../fundamentals-introduction-to-azure.md#compute).    
* Para obter mais informações sobre redes virtuais, consulte o artigo [redes virtuais](https://azure.microsoft.com/documentation/services/virtual-network/).    
* Para saber mais sobre a cópia de segurança e recuperação, consulte o artigo [continuidade do negócio](sql-database-business-continuity.md).    
* Para saber mais sobre a monitorização ppols, consulte [monitorização agrupamentos](sql-database-elastic-pool-manage-portal.md).    
* Para saber mais sobre Umbraco como um serviço, consulte o artigo [Umbraco](https://umbraco.com/cloud).

