---
title: "Descrição geral da base de dados do Azure para o serviço de base de dados relacional MySQL | Microsoft Docs"
description: "Descrição geral da base de dados do Azure para o serviço de base de dados relacional MySQL."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 10/20/2017
ms.custom: mvc
ms.openlocfilehash: 2a9efdd9285dfa5fca450ede64e5f6ee54cbc72b
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2017
---
# <a name="what-is-azure-database-for-mysql"></a>O que é a base de dados do Azure para MySQL?
Base de dados do Azure para MySQL é um serviço de base de dados relacional em nuvem da Microsoft com base no [MySQL Comunidade edição](https://www.mysql.com/products/community/) motor de base de dados. Este serviço está em pré-visualização pública. Fornece a base de dados do Azure para MySQL:

- Elevada disponibilidade incorporada sem custos adicionais.
- Desempenho previsível, com preços de pay as you go, inclusive.
- Dimensionar no momento em segundos.
- Protegida para proteger os dados confidenciais em rest e em movimento.
- Cópias de segurança automáticas e ponto-na-tempo-restauro até 35 dias.
- Segurança de nível empresarial e conformidade.

Estas capacidades requerem quase sem administração e todos os são fornecidos sem custos adicionais. Estas permitem-lhe focar-se no desenvolvimento rápido de aplicações e acelerar o seu tempo de comercialização em vez de atribuir tempo precioso e recursos para gerir máquinas virtuais e infraestrutura. Além disso, pode continuar a desenvolver a sua aplicação com a plataforma à sua escolha para fornecer velocidade e a eficiência as exigências de negócio, todos os sem necessidade de aprender novo competências e ferramentas open source.

![Base de dados do Azure para o diagrama conceptual MySQL](media/overview/1-azure-db-for-mysql-conceptual-diagram.png)

Este artigo é uma introdução à base de dados do Azure para conceitos principais de MySQL e funcionalidades relacionadas com o desempenho, escalabilidade e capacidade de gestão, com ligações para explorar detalhes. Consulte estes inícios rápidos para começar:
- [Criar uma Base de Dados do Azure para o servidor MySQL com o portal do Azure](quickstart-create-mysql-server-database-using-azure-portal.md)
- [Criar uma Base de Dados do Azure para o servidor MySQL com a CLI do Azure](quickstart-create-mysql-server-database-using-azure-cli.md)

Para um conjunto de exemplos de CLI do Azure, consulte:
- [Exemplos da CLI do Azure para a base de dados do Azure para MySQL](sample-scripts-azure-cli.md)

## <a name="adjust-performance-and-scale-within-seconds"></a>Ajustar o desempenho e dimensionamento em segundos
Pré-visualização, a base de dados do Azure para o serviço de MySQL oferece dois escalões de serviço: básico e padrão. Cada camada oferece capacidades para suportar até pesadas da base de dados as cargas de trabalho e de desempenho diferentes. Pode criar a sua primeira aplicação numa base de dados pequena para alguns utilizados no mês e, em seguida, ajustar a escala para satisfazer as necessidades da sua solução. Escalabilidade dinâmica permite que a base de dados transparente responder a mudar rapidamente os requisitos de recursos. Apenas paga pelos recursos necessário e apenas quando que precisar. Consulte [escalões de preço](concepts-service-tiers.md) para obter mais detalhes.

## <a name="monitoring-and-alerting"></a>Monitorização e alertas
Como a decidir quando aumentar e reduzir verticalmente? Utilize o desempenho incorporado, monitorização e alertas de funcionalidades, juntamente com as classificações de desempenho com base em unidades de computação. Utilizar estas ferramentas, pode avaliar rapidamente o impacto da computação unidades de dimensionamento cópias de segurança ou para baixo com base nas suas necessidades de desempenho atual ou prevista. Consulte [alertas](howto-alert-on-metric.md) para obter mais detalhes.

## <a name="keep-your-app-and-business-running"></a>Mantenha a sua aplicação e o seu negócio operacionais
Líderes 99,99% disponibilidade nível contrato de serviço (SLA), utiliza a tecnologia de uma rede global de datacenters gerida pela Microsoft, da indústria do Azure ajuda a manter a sua aplicação em execução 24/7. Com cada base de dados do Azure para o servidor de MySQL, pode tirar partido de segurança incorporadas, a tolerância a falhas e a proteção de dados que caso contrário, terá de comprar ou conceber, criar e gerir. Com base de dados do Azure para MySQL, pode utilizar o restauro de ponto no tempo para recuperar um servidor para um estado anterior, até 35 dias.

## <a name="secure-your-data"></a>Proteger os dados
Serviços de base de dados do Azure tem um tradição de segurança de dados que mantém a base de dados do Azure para MySQL, com as funcionalidades que limitam o acesso, proteger dados em rest e em movimento e ajudarem a monitorizar a atividade. Visite o [Centro de fidedignidade do Azure](https://www.microsoft.com/en-us/TrustCenter/Security/default.aspx) para obter informações sobre a segurança da plataforma do Azure.

A base de dados do Azure para o serviço de MySQL utiliza a encriptação de armazenamento para dados em rest. Os dados, incluindo cópias de segurança, são encriptados em disco (à exceção dos ficheiros temporários criados pelo motor durante a execução de consultas). O serviço utiliza cifras AES 256 bits que está incluída na encriptação de armazenamento do Azure e as chaves são gerido de sistema. Encriptação de armazenamento está sempre ativada e não pode ser desativada.

Por predefinição, a base de dados do Azure para o serviço de MySQL está configurado para exigir [segurança de ligação SSL](./concepts-ssl-connection-security.md) para dados em movimento através da rede. Imposição de ligações de SSL entre o servidor de base de dados e as aplicações de cliente ajuda a proteger contra ataques "man no meio" ao encriptar o fluxo de dados entre o servidor e a sua aplicação.  Opcionalmente, pode desativar exigir o SSL para ligar ao seu serviço de base de dados, se a aplicação de cliente não suporta a conectividade SSL.

## <a name="next-steps"></a>Passos seguintes
Agora que já leu uma introdução à base de dados do Azure para o MySQL e respondeu à pergunta "O que é do Azure da base de dados para MySQL?", está pronto para:
- Consulte a página de preços para comparações de preços e calculadoras. [Preços](https://azure.microsoft.com/pricing/details/mysql/)
- Comece por criar o primeiro servidor. [Criar uma Base de Dados do Azure para o servidor MySQL com o portal do Azure](quickstart-create-mysql-server-database-using-azure-portal.md)
- Criar a sua primeira aplicação utilizando a linguagem preferencial: [Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md)  |  [PHP](./connect-php.md) | [.NET (c#)](./connect-csharp.md) | [aceda](./connect-go.md)
