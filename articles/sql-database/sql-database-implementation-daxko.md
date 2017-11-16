---
title: "Base de dados SQL do Azure do Azure caso prático - Daxko/CSI | Microsoft Docs"
description: "Saiba mais sobre como Daxko/CSI utiliza a base de dados SQL para acelerar o respetivo ciclo de desenvolvimento e para melhorar o desempenho e serviços do cliente"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 00c8a713-f20c-4d6b-b8b7-0c1b9ba5f05b
ms.service: sql-database
ms.custom: reference
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: Inactive
ms.date: 01/10/2017
ms.author: carlrab
ms.openlocfilehash: 7a05836be4a0879fa7103d070c683f45c06cd741
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2017
---
# <a name="daxkocsi-used-azure-to-accelerate-its-development-cycle-and-to-enhance-its-customer-services-and-performance"></a>Daxko/CSI utilizado do Azure para acelerar o respetivo ciclo de desenvolvimento e para melhorar o desempenho e serviços do cliente
![Logótipo de Daxko/CSI](./media/sql-database-implementation-daxko/csidaxkologo25.png)

Software Daxko/CSI deparam um desafio: base dos centros de adequação a e a recriação do cliente foi crescer rapidamente, graças ao sucesso da sua solução de software de empresarial abrangente, mas mantendo cópias de segurança com as necessidades de infraestrutura de TI para esse cliente crescente base foi testar a empresa equipa de TI. A empresa foi cada vez mais restrita por crescente overhead de operações, especialmente para gerir as suas bases de dados crescentes. Worse, esse overhead de operações foi cortando para recursos de desenvolvimento para o novo iniciativas, como novas funcionalidades de mobilidade para software da empresa.

De acordo com o David Molina, Director de produto de desenvolvimento em Daxko/CSI, fornecido pelo Azure CSI Software com o modelo de (PaaS) de plataforma-como-um-serviço necessitava de para simplificar a gestão de base de dados, aumentar a escalabilidade e libertar recursos para se focarem em software em vez do OPS Manager. "Base de dados SQL do azure foi uma excelente opção para-nos. Não terem de se preocupar com a manutenção de um servidor de SQL, um cluster de ativação pós-falha e todas a outra infraestrutura tem foi ideal para-nos."

Desde a migrar para o Azure, CSI Software necessita de uma equipa de operações apenas dois para gerir mais de 600 bases de dados do cliente. A empresa utiliza conjuntos elásticos SQL Database do Azure para mover as bases de dados de cliente com base no tamanho e precisa.

Molina continua, "os nossos clientes felt a alteração imediatamente. Antes de conjuntos elásticos, ocasionalmente tinham tempos limite e outros problemas durante períodos de rajada. Com o Azure conjuntos elásticos, podem impulsar conforme necessário e utilizar o software sem problemas."

Para além de melhorar o desempenho para os clientes, do Azure conjuntos elásticos libertados recursos de CSI Software focar-se no desenvolvimento de novos serviços e funcionalidades, em vez de terem de lidar com operações e gestão. Os recursos de TI ajudou CSI Software melhorar o respetivo software de enterprise oferta SpectrumNG, para ajudar a interagir com os membros de gym, melhorar a eficiência de equipa e atribua os funcionários e membros acesso móvel para tarefas interativas e notificações em tempo real.

Azure ajudou também CSI Software acelerar e melhorar o desenvolvimento e o ciclo de garantia de qualidade (pergunta e resposta), Ativando as opções de automatização. Com a implementação do Azure da empresa, gestores de compilação podem empacotar os componentes com clique do botão para um. Conforme Molina, "como parte do ciclo de lançamento, pergunta e resposta é agora possível implementar a um ambiente de teste no Azure, o que estritamente mimics nosso pilha de produção. Pode implementar compilações imediatamente para o nosso ambiente de desenvolvimento para analise juntamente alterações. É uma grande win para us, porque não foram cumpridos paridade para fins de teste antes dessa."

## <a name="offloading-to-the-cloud"></a>Descarregar para a nuvem
Antes de mover para a nuvem, CSI Software tinha construídos com êxito a própria infraestrutura multi-inquilino no Centro de dados local no Houston. Como a empresa expandido, deparam crescente pains crescentes de compra, aprovisionamento e manter todas o hardware e software necessários para suportar a que os seus clientes. IT staffing para processar operações ficou estrangulamento outro que conduziram a um slowdown no aprovisionamento novos recursos e disponibilizando novos serviços aos clientes.

CSI Software de analisar as opções de nuvem para eliminando esse sobrecarga, para que foi focar-se no respetivo código e, em vez das suas operações. A empresa detetados muitos dos fornecedores de nuvem superior apenas oferecem soluções do infraestrutura-como-um-serviço (IaaS) que ainda requerem uma grande equipa de TI para gerir a pilha de IaaS. No final, CSI Software determinou que a solução de Azure PaaS era a melhor opção para as respetivas necessidades. Explica Molina, "O Azure obtém o software de hardware e sistema horizontalmente de forma, pelo que iremos poder concentrar na nossa ofertas de software, reduzindo o nosso custos gerais de TI."

## <a name="making-the-transition-to-azure"></a>Efetuar a transição para o Azure
Depois de selecionar do Azure para a sua solução de PaaS, o CSI Software começou a migrar a sua infraestrutura de back-end e bases de dados para a nuvem. Antes do Azure, os clientes SpectrumNG são necessários para instalar uma aplicação cliente que comunicou com um serviço Windows Communication Foundation (WCF) no back-end. De acordo com Molina, "Embora alguns clientes alojado tudo nos respetivos centros de dados, criámos terminar o produto ser multi-inquilino. Iremos alojado tudo num centro de dados no Houston, utilizar o SQL Server como o arquivo de dados.

"O nosso produto oferta também incluído um orientado para o membro portal web (escrito no ASP.net), que foi concebido para ser de etiqueta em branco para corresponder a presença na web do cliente e uma API de SOAP para suportar as páginas online e qualquer integração de terceiros."

A migração para a nuvem não entre longa para a arquitetura. De acordo com Molina, "A maioria das esforço resolvida modificar a forma que iremos ler informações de ficheiro de configuração, uma modificação de cadeia de ligação centralizada e automatizar o empacotamento, carregar e a implementação das nossas versões."

Para desenvolver a automatização de compilação, os engenheiros de CSI Software utilizado Azure PowerShell e REST APIs para criar pacotes e carregá-los para um ambiente de teste versão todas as noites.
A transição geral para uma implementação de acesso baseado na nuvem do Azure correu rápida e facilmente para a equipa de TI de Software CSI. Explica Molina, "em todas, iremos tinha um ambiente de beta na nuvem no prazo de três a quatro semanas de colocar no projeto. Que era uma win surprising para-nos."

Depois de configurar e o ambiente de teste, o CSI Software começou a migrar os clientes. Clientes que já utilizam CSI Software de alojamento, a transição foi quase totalmente integrada. Para os clientes a migrar de uma implementação no local, a migração para a nuvem demorou algum tempo adicional, mas foi ainda principalmente tensão livre para os clientes e CSI Software.

Para clientes novos, CSI Software equipa de TI utiliza o seguinte processo para uma-los para o Azure:

1. Scripts do PowerShell do Azure são utilizadas para rotação configurar uma nova base de dados para o cliente; todos os clientes começam a camada de premium para garantir que o suficiente débito inicial para a transição.
2. Sempre que possível, o Software de CSI utiliza o Assistente de migração de SQL do Azure para mover dados existentes para uma instância de SQL Database do Azure.
3. Por fim, o Microsoft SQL Server Integration Services (SSIS) são utilizados para reconciliar quaisquer discrepâncias nos dados de ou para executar qualquer limpeza de dados conforme necessário.

Hoje em dia, cerca de 99 por cento dos clientes de CSI Software estão alojados no Azure, entre quatro regionais dos centros de dados (Norte Central, Sul Central, leste e oeste). Por meio de centros de dados na região geográfica de cada cliente, a latência é mantida a um mínimo.

## <a name="azure-elastic-pools-free-up-it-resources"></a>Azure conjuntos elásticos liberte recursos de TI
Várias funcionalidades do Azure tem ajudou CSI Software shift deixou de ser a infraestrutura e operações concentra-se para a funcionalidade e desenvolvimento concentra-se. Talvez o benefício maior foi de conjuntos elásticos.

CSI Software fornece atualmente 550 sobre bases de dados para os clientes. Antes de conjuntos elásticos, era difícil gerir que muitas bases de dados dentro de uma estrutura de camada. Gestores de OPS tinham que atribuir os escalões de desempenho com base nas necessidades rajada de clientes, que obrigatório significativa IT-recurso dos custos gerais. Com conjuntos elásticos, gestores podem atribuir inquilinos um premium ou um conjunto padrão, conforme adequado e, em seguida, mova os clientes com base no tamanho e precisam. Os clientes felt os efeitos dos conjuntos elásticos quase imediatamente; antes de conjuntos elásticos, os clientes tinham tempos limite e outros problemas durante períodos de utilização de rajada, mas com conjuntos elásticos, os clientes podem sentir atividade bursts conforme necessário e podem continuar a utilizar SpectrumNG sem problemas.

## <a name="azure-active-geo-replication-accelerates-reporting"></a>Azure Active Directory georreplicação acelera Reporting Services
Alguns clientes de CSI Software também são tirar partido do Azure Active Directory georreplicação. Com replicação geográfica activa, até quatro legíveis secundários bases de dados pode ser configurada nas regiões diferentes ou mesmo centro de dados. CSI Software utilizam georreplicação ativa de duas formas: primeiro, as bases de dados secundárias estão disponíveis no caso de uma falha de centro de dados ou a impossibilidade de estabelecer ligação à base de dados primária; e segundo, as bases de dados secundárias são legíveis e podem ser utilizados para a descarga de cargas de trabalho só de leitura como tarefas de relatório. Alguns clientes de CSI Software utilizam desta vantagem para acelerar os fluxos de trabalho de relatórios.

## <a name="csi-software-application-logic-and-architecture"></a>Lógica da aplicação de CSI Software e arquitetura
SpectrumNG utiliza funções da web. Porque a aplicação multi-inquilino, um serviço WCF é utilizado para processar o pedido de ligação inicial de clientes. Como Molina Estados, "o pedido identifica cada cliente, que, em seguida, permite-na criar uma cadeia de ligação enviados para as bases de dados para que temos de fazer."

Para a camada web do seu serviço, CSI Software tira partido do dimensionamento automático do Azure, com base no dia e hora. Recursos disponíveis são automaticamente aumentados para uma utilização superior durante o horário comercial, de acordo com o fuso horário de cada datacenter regional. Recursos estão igualmente configurados serem reduzir verticalmente de semana, quando as necessidades do cliente são inferiores.

![Arquitetura de Daxko/CSI](./media/sql-database-implementation-daxko/figure1.png)

Figura 1. Uma função de trabalho de serviços em nuvem desenha dados estruturados da SQL Database do Azure e semiestruturados dados a partir do armazenamento de tabelas. Utilizadores de SpectrumNG interagem com que dados através de uma nuvem de serviços de função da web.

## <a name="using-web-apps-and-a-web-plan-tier-for-mobile-apps"></a>Utilizar as aplicações web e uma camada web plano para aplicações móveis
Utilizar a base de dados do SQL Azure libertado recursos de CSI Software para ativar iniciativas novo, incluindo uma plataforma móvel completa com base numa API personalizada alojada em aplicações web do Azure. A plataforma permite membros gym e funcionários utilizem os dispositivos móveis para verificar as agendas, livro classes e receber mensagens.

A plataforma utiliza arquitetura orientada para serviços (SOA) para criar um único componente — como um sistema de ponto de venda (POS) ou um sistema de venda — movê-la no momento para outro plano da web e, em seguida, rotação um serviço para suportar este componente, mantendo tudo o resto no plano de web original. Esta capacidade proporciona flexibilidade imenso CSI Software e ajuda a mantenha os custos reduzidos.

## <a name="azure-lets-csi-software-developers-focus-on-apps-and-services"></a>Azure permite CSI Software aos programadores focarem-se no aplicações e serviços
Base de dados SQL do Azure não é apenas um boon aos clientes de SpectrumNG, que desfrutar o serviço rápido e fiável, também é uma grande win para Software de CSI equipa de TI e programadores. Ao descarregar ops para o Azure na nuvem, o Software de CSI reduzido a sobrecarga de infraestrutura e de recursos, significativamente acelerados respetivo ciclos de desenvolvimento e já não tem bases de dados de micromanage para otimizar o desempenho para os seus inquilinos.

## <a name="more-information"></a>Mais informações
* Para obter mais informações sobre conjuntos elásticos do Azure, consulte o artigo [conjuntos elásticos](sql-database-elastic-pool.md).
* Para saber mais sobre as ferramentas de base de dados e dimensionamento elástico, consulte [ferramentas de base de dados elástica e dimensionamento elástico](sql-database-elastic-scale-get-started.md).
* Para obter mais informações sobre como migrar uma base de dados do SQL Server, consulte ver [migrar uma base de dados do SQL Server para o Azure](sql-database-cloud-migrate.md).
* Para saber mais sobre a replicação geográfica activa, consulte [georreplicação ativa](sql-database-geo-replication-overview.md).
* Para obter mais informações sobre funções da Web e funções de trabalho, consulte o artigo [as funções de trabalho](../fundamentals-introduction-to-azure.md#compute).    
* Para saber mais sobre o Service Bus do Azure, consulte o artigo [Service Bus do Azure](https://azure.microsoft.com/services/service-bus/).
* Para saber mais sobre o dimensionamento automático, consulte o artigo [dimensionamento serviços em nuvem](../cloud-services/cloud-services-how-to-scale-portal.md).

