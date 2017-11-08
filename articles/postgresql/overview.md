---
title: "Descrição geral da base de dados do Azure para o serviço de base de dados relacional PostgreSQL | Microsoft Docs"
description: "Fornece uma descrição geral da base de dados do Azure para o serviço de base de dados relacional PostgreSQL."
services: postgresql
author: kamathsun
ms.author: sukamat
manager: jhubbard
editor: jasonwhowell
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 10/20/2017
ms.openlocfilehash: 9aa24dd10ef29c716c05cafeb84e0beb23d50628
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2017
---
# <a name="what-is-azure-database-for-postgresql"></a>O que é a base de dados do Azure para PostgreSQL?

Base de dados do Azure para PostgreSQL é um serviço de base de dados relacional em nuvem da Microsoft incorporado para programadores com base na versão de Comunidade de código aberto [PostgreSQL](https://www.postgresql.org/) motor de base de dados. Este serviço está em pré-visualização pública. Fornece a base de dados do Azure para PostgreSQL:

- Elevada disponibilidade incorporada sem custos adicionais.
- Desempenho previsível, com preços de pay as you go, inclusive.
- Dimensionar no momento em segundos.
- Protegida para proteger os dados confidenciais em rest e em movimento.
- Cópias de segurança automáticas e ponto-na-tempo-restauro até 35 dias.
- Segurança de nível empresarial e conformidade.

Todas estas funcionalidades requerem quase sem administração, e todos os são fornecidos sem custos adicionais. Estas funcionalidades permitem-lhe focar-se no desenvolvimento rápido de aplicações e acelerar o seu tempo de comercialização, em vez de alocar tempo precioso e recursos para gerir máquinas virtuais e infraestrutura. Além disso, pode continuar a desenvolver a sua aplicação com a plataforma da sua preferência e ferramentas open source e fornecer velocidade e a eficiência do seu negócio imperativas sem ter de saber as competências de novo. 

Este artigo é uma introdução à base de dados do Azure para conceitos principais de PostgreSQL e funcionalidades relacionadas com o desempenho, escalabilidade e capacidade de gestão. Consulte estes inícios rápidos para começar:

- [Criar uma base de dados do Azure para PostgreSQL através do portal do Azure](quickstart-create-server-database-portal.md)
- [Criar uma base de dados do Azure para PostgreSQL utilizando a CLI do Azure](quickstart-create-server-database-azure-cli.md)

Para um conjunto de exemplos de CLI do Azure, consulte:

- [Exemplos da CLI do Azure para a base de dados do Azure para PostgreSQL](./sample-scripts-azure-cli.md)

## <a name="adjust-performance-and-scale-within-seconds"></a>Ajustar o desempenho e dimensionamento em segundos
Pré-visualização, a base de dados do Azure para o serviço de PostgreSQL oferece dois escalões de serviço: básico e padrão. Cada camada oferece capacidades para suportar até pesadas da base de dados as cargas de trabalho e de desempenho diferentes. Pode criar a sua primeira aplicação numa base de dados pequena para alguns utilizados no mês e, em seguida, ajustar a escala para satisfazer as necessidades da sua solução. Escalabilidade dinâmica permite que a base de dados transparente responder a mudar rapidamente os requisitos de recursos. Apenas paga pelos recursos necessário e apenas quando que precisar. Consulte [escalões de preço](concepts-service-tiers.md) para obter mais detalhes.

## <a name="monitoring-and-alerting"></a>Monitorização e alertas
Como a decidir quando aumentar e reduzir verticalmente? Utilize o desempenho incorporado, monitorização e alertas de funcionalidades, juntamente com as classificações de desempenho com base em unidades de computação. Utilizar estas ferramentas, pode avaliar rapidamente o impacto da computação unidades de dimensionamento cópias de segurança ou para baixo com base nas suas necessidades de desempenho atual ou prevista. Consulte [alertas](howto-alert-on-metric.md) para obter mais detalhes.

## <a name="keep-your-app-and-business-running"></a>Mantenha a sua aplicação e o seu negócio operacionais
Líderes 99,99% disponibilidade (não disponível na pré-visualização) nível contrato de serviço (SLA), utiliza a tecnologia de uma rede global de datacenters gerida pela Microsoft, da indústria do Azure ajuda a manter a sua aplicação em execução 24/7. Com cada base de dados do Azure para o servidor de PostgreSQL, pode tirar partido de segurança incorporadas, a tolerância a falhas e a proteção de dados que caso contrário, terá de comprar ou conceber, criar e gerir. Com base de dados do Azure para PostgreSQL, cada escalão de serviço oferece um conjunto completo de funcionalidades de continuidade do negócio e as opções que pode utilizar para ficar e em execução e mantenha-se dessa forma. Pode utilizar o [restauro para um ponto anterior no tempo](howto-restore-server-portal.md) para repor um estado anterior de uma base de dados, até 35 dias. Além disso, se o datacenter que aloja as bases de dados sofrer uma falha, pode restaurar bases de dados de cópias georredundante de cópias de segurança recentes.

## <a name="secure-your-data"></a>Proteger os dados
Serviços de base de dados do Azure tem um tradição de segurança de dados que a base de dados do Azure para PostgreSQL mantém com funcionalidades que limitam o acesso, proteger dados em rest e em movimento e ajudam a monitorizar a atividade. Visite o [Centro de fidedignidade do Azure](https://www.microsoft.com/TrustCenter/Security/default.aspx) para obter informações sobre a segurança da plataforma do Azure.

A base de dados do Azure para o serviço de PostgreSQL utiliza a encriptação de armazenamento para dados em rest. Incluindo cópias de segurança de dados são encriptados em disco (à exceção dos ficheiros temporários criados pelo motor durante a execução de consultas). O serviço utiliza cifras AES 256 bits que está incluída na encriptação de armazenamento do Azure e as chaves são gerido de sistema. Encriptação de armazenamento está sempre ativada e não pode ser desativada.

Por predefinição, a base de dados do Azure para o serviço de PostgreSQL está configurado para exigir [segurança de ligação SSL](./concepts-ssl-connection-security.md) para dados em movimento através da rede. Imposição de ligações de SSL entre o servidor de base de dados e as aplicações de cliente ajuda a proteger contra ataques "man no meio" ao encriptar o fluxo de dados entre o servidor e a sua aplicação.  Opcionalmente, pode desativar exigir o SSL para ligar ao seu serviço de base de dados, se a aplicação de cliente não suporta a conectividade SSL.

## <a name="next-steps"></a>Passos seguintes
- Consulte o [página de preços](https://azure.microsoft.com/pricing/details/postgresql/) para comparações de preços e calculadoras.
- Introdução por [criar a sua primeira base de dados do Azure para PostgreSQL](./quickstart-create-server-database-portal.md).
- Criar a sua primeira aplicação no Python, PHP, Ruby, C\#, Java, Node.js: [bibliotecas de ligação](./concepts-connection-libraries.md)
