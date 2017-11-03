---
title: "Base de dados SQL do Azure do Azure caso prático - Snelstart | Microsoft Docs"
description: "Saiba mais sobre como SnelStart utiliza a base de dados SQL para expandido rapidamente os seus serviços empresariais uma taxa de 1.000 novo Azure bases de dados SQL por mês"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: fab506b2-439d-4f1a-bdc5-d1d25c80d267
ms.service: sql-database
ms.custom: reference
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: Inactive
ms.date: 01/10/2017
ms.author: carlrab
ms.openlocfilehash: 4fa21cf9cbd1680ddd855189f50af50e1068ccd5
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="with-azure-snelstart-has-rapidly-expanded-its-business-services-at-a-rate-of-1000-new-azure-sql-databases-per-month"></a>Com o Azure, SnelStart expandiu rapidamente os serviços empresariais uma taxa de 1.000 novo Azure bases de dados SQL por mês
![SnelStartLogo](./media/sql-database-implementation-snelstart/snelstartlogo.png)

SnelStart torna software popular financeiros-empresarial-gestão e para empresas pequena e média (PMEs) no países baixos. Que os seus 55,000 clientes são servidos por uma equipa de pessoal de 110 funcionários, incluindo uma equipa de TI de 35. Ao mover o software de ambiente de trabalho para um software-como-um-serviço (SaaS) oferta incorporadas no Azure, SnelStart efetuada a maioria dos serviços incorporados, automatizar a gestão através do ambiente familiar em c# e otimizar o desempenho e escalabilidade por nenhuma das empresas de ativação pós-falha ou em-aprovisionamento de utilizam conjuntos elásticos. Utilizar o Azure fornece SnelStart fluidity dos clientes mover entre no local e na nuvem.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-Case-Study-SnelStart/player]
> 
> 

## <a name="why-snelstart-extended-services-from-the-desktop-to-the-cloud"></a>Por que motivo SnelStart expandido serviços no ambiente de trabalho para a nuvem
> "A trabalhar com o Azure significa que pode fornecer software mais rápida, rapidamente reagir a pedidos de cliente e dimensionar soluções quando exigências aumentam."
> 
> — Henry foi, Arquitetos de Software
> 
> 

SnelStart foi executada uma empresa de software com êxito durante anos, com um modelo de programação tradicionais: code, o pacote, são enviados e repita. Ao longo do tempo, o ritmo de alteração queria mais rápido e mais rapidamente. Normas são alterados frequentemente e os clientes necessárias formas mais fácil para processar registos financeiros e colaborar com os respetivos accountants e government manter essas alterações.

"O envio de software aos clientes foi dispendiosa e restritiva,", de acordo com Henry foi, arquitetos de software. "Produção, empacotamento e os custos de envio limitada frequência foi Lançamos software. Tivemos às atualizações de pacote do shipments periódicas, mas que efetuadas disco rígido satisfazer as necessidades de alteração dos nossos clientes. Iremos foi nunca ter a certeza de que os nossos clientes atualizados para a versão mais recente do produto. Por conseguinte, tivemos que suportar várias versões, efetuar a tarefa de suporte muito difícil bem."

Foi adiciona, "queremos uma forma de atualizações e a versão do programa numa na melhoria ritmo, pelo que iremos foi inovar mais rapidamente e criar novos serviços para os nossos clientes. Também queremos uma forma de automatizar processos mais para simplificar as necessidades de negócio administração os nossos clientes."

Para SnelStart, a solução era expandir os respetivos serviços por se tornar um fornecedor de SaaS baseado na nuvem. A plataforma de SQL Database do Azure ajudou SnelStart chegar lá sem incorrer nos custos de gerais de TI principais que necessitaria uma solução de infraestrutura-como-um-serviço (IaaS).

O modelo de nuvem também permite SnelStart corrigir os erros e fornecer novas funcionalidades rapidamente, sem necessidade de transferir e atualizar o software de clientes. Em conformidade com foi, "Using cloud services do Azure permite-na agir rapidamente alterar os requisitos de terceiros. Em vez de ter que enviar uma nova versão para milhares de clientes, iremos pode adaptar informações enviadas pelo nossa aplicação de ambiente de trabalho para novas formatos de terceiros."

## <a name="a-modern-company-with-traditional-roots"></a>Uma empresa moderna com raízes tradicionais
SnelStart é uma empresa moderna, seja ágil, alta raízes humble dating para 1964, quando os founders iniciado a empresa como um fabricante de peças instrumento musical. O centro das empresas de software SnelStart realmente iniciado beating no 1980s, durante a proliferação do computador pessoal. A empresa necessárias uma alternativa melhor para o software de gestão de contas disponível na altura, pelo demorou é importante as suas próprias mãos. 1982, a empresa criar a base do que seria deixar de software de gestão de contas SnelStart. Desde o início, o software foi admired para sua simplicidade e velocidade —, tanto que, para que a empresa eventualmente alteradas foco e reinvented próprio para uma empresa de software.

Em 1995, SnelStart lançadas a sua primeira aplicação de bookkeeping para Windows. A aplicação, incorporada no Microsoft Visual Basic 1.0 e do Microsoft Access bases de dados ajudou a aumentar o cliente base de mais de 5000 utilizadores.

Hoje, SnelStart é um fornecedor de principais de uma linha de negócio (LOB) e a aplicação de negócio administração direcionada para PMEs neerlandês e entrepreneurs self employed. Como Carlo Kuip, arquiteto IT, diz: "o nosso objetivo é fornecer automatização de 100-por cento dos serviços de administração de negócio para os nossos clientes."

## <a name="optimizing-performance-and-cost-with-elastic-pools"></a>Otimizar o desempenho e o custo com conjuntos elásticos
SnelStart era um adopter antecipado em grande escala de conjuntos elásticos. Conjuntos elásticos ajudam a empresa limitar os custos e gerir requisitos de desempenho de forma mais eficiente. Em conformidade com foi, "ao utilizar conjuntos elásticos, mas pode otimizar o desempenho com base nas necessidades dos nossos clientes, sem sobre-aprovisionar. Se tivemos aprovisionar com base no pico de carga, seria possível bastante dispendiosa. Em vez disso, a opção para partilhar recursos entre várias bases de dados de utilização baixo nos permite criar uma solução que efetua bem e também é rentável. "

## <a name="azure-sql-databases-help-containerize-data-for-isolation-and-security"></a>Bases de dados SQL do Azure ajuda containerize dados para segurança e isolamento
Base de dados SQL do Azure permite SnelStart facilmente e transparente mover dos clientes no local dados de negócio administração no Azure. A base de dados do SQL do Azure é um contentor conveniente que fornece o isolamento, um limite para autenticação, autorização e fácil capacidades de cópia de segurança e restauro. Bases de dados fornecem um modelo conceptual adequado para a administração de negócio. De acordo com Carlo Kuip, arquiteto IT, "itens este limite do contentor contêm dados confidenciais que são fundamentais para uma empresa e armazenar esses itens numa base de dados isolado mantém-na bem protegida. Iremos pode gerir autorização ao nível da base de dados e mesmo automatizar a gestão e ampliação destas bases de dados sem necessidade dos administradores de base de dados (DBAs) em equipa."

O Azure SQL Data Warehouse também desempenha um papel na história SnelStart de segurança e gestão por ajudar a recolher dados de telemetria, tais como a deteção de intrusões, o registo de atividade do utilizador e a conectividade de empresa.

## <a name="azure-removes-overhead-so-that-developers-can-spend-more-time-delivering-value"></a>Azure remove sobrecarga para que os programadores podem demora mais tempo a entrega de valor
O modelo de plataforma do Azure removido custos de infraestrutura e ativada SnelStart automatizar a implementações com bibliotecas de gestão do c#. Como Kuip Estados, "foi possível crescer nosso atuais operações com uma equipa de pessoal muito pequena aumentando simultaneamente as opções de recuperação após desastre, velocidade e escalabilidade para os nossos clientes. Shift para desenvolvimento de serviços libertado recursos para se focarem em novos serviços e funcionalidades, em vez de apenas atualizar código existente para manter-se com as normas novo ou códigos dedução dos impostos." Adiciona, "ao automatizar a gestão e utilizar o SaaS oferta, mas é possível fornecer o valor para os nossos clientes sem ter de tomar grande investimentos em equipa operacional." Por exemplo, ao utilizar conjuntos do Azure e elásticos, SnelStart conseguiu adicionar uma variedade de novas funcionalidades, incluindo a integração de dados de cliente mais robusta com bancos, de faturação novos serviços, verificações pequenas empresas e serviços de e-mail.

> "Apenas os primeiro alguns meses de 2016, vamos expandido nosso implementações de SQL Database do Azure de 5,500 prestes a mais do que 12 000 e estamos a adicionar atualmente 1000 bases de dados por mês."
> 
> — Henry foi, Arquitetos de Software
> 
> 

Gestão de base de dados é ainda mais automatizado utilizando a funcionalidade das tarefas elásticas. Como Kuip Estados, "altamente Agradecemos a deteção automática de bases de dados numa instância de base de dados SQL [server]." Isto permite SnelStart executar operações de gestão em todos os respetivos clientes dinamicamente crescente base sem custos adicionais.

SnelStart também está a desenvolver uma API que age como um mediador entre os dados de cliente e as aplicações criadas pelo parceiros de software de terceiros. Como Estados Kuip, "Esta API irá ativar outros fornecedores adicionar a funcionalidade com o nosso software, por exemplo, eliminando a entrada de dados de faturas e outros documentos." Os clientes já não tem de escrever manualmente faturas no seu software de gestão de contas de pequenas empresas; o software de SnelStart será trocar as informações necessárias diretamente. Isto permite que os clientes associar as respetivas tarefas de administração de negócio com a ecossistema de informações é emergentes de transformação digital da indústria.  

## <a name="how-azure-services-enable-saas-for-snelstart"></a>A forma como os serviços do Azure ativar SaaS para SnelStart
Ao utilizar o Azure, SnelStart pode servir os clientes e os respetivos accountants mais totalmente integrada na nuvem. Por exemplo, os clientes e accountants pode partilhar informações ao aceder diretamente a API de cliente do SnelStart, alojado no Azure. Kuip Estados "estes serviços reutilizáveis estão disponíveis para web apps do nosso orientado para o cliente e que fornecem a infraestrutura e funções para permitir o acesso a administração de negócio para os clientes e para software de terceiros utilizados pelos nossos clientes comuns. Os exemplos incluem fornecer capacidades de configuração do produto, a gestão de regras de firewall e gerir os processos de execução longa, como as cópias de segurança."

> O nosso objetivo é fornecer automatização de 100-por cento dos serviços de administração de negócio para os nossos clientes". 
> 
> — Carlo Kuip, Arquiteto IT
> 
> 

Além disso, serviços web de SnelStart permitem que os clientes e accountants aceder facilmente aos dados em conjuntos elásticos SQL Database do Azure. Este modelo de SaaS, conjugado com a elasticidade da base de dados e o Azure Resource Manager, fornece SnelStart com funcionalidades de escalabilidade complementam todas as implementações do Azure. A implementação é totalmente automatizada utilizando bibliotecas de gestão do c#.

![Arquitetura de SnelStart](./media/sql-database-implementation-snelstart/figure1.png)

Figura 1. A partir de Junho de 2016, SnelStart tem mais de 50 conjuntos elásticos e bases de dados mais do que 11,000

## <a name="simplicity-from-the-cloud"></a>Simplicidade da nuvem
Desde a mover uma solução baseada na nuvem do Azure, SnelStart foi capaz de suportar o crescimento rápido ao cliente durante a oferta de serviços e funcionalidades inovadoras. Em conformidade com foi, "com o Azure, que pode fornecer quase contínua atualizações para os nossos clientes, sem a expandir o nosso pessoal de operações. E obtemos todas as outras excelente Azure funcionalidades — como a recuperação após desastre e escalabilidade — incluídas na. "

> "Quando foi realmente através em Redmond... Recebi uma chamada de um programador regresso de países baixos, phoning-me sobre um problema específico. Foi possível obter o Microsoft para fornecer uma alteração no respetivo ambiente de produção no prazo de 48 horas para resolver o nosso problema."
> 
> — Henry foi, Arquitetos de Software
> 
> 

SnelStart também appreciates parceria de forte que tem desenvolvidas com a equipa de BD SQL do Microsoft Azure. Como Kuip Estados, "temos discussões sobre funcionalidades e como utilizar a tecnologia, o que é algo appreciated em ambos os lados."
O objetivo de imediato para SnelStart é continuam a crescer respetivo cliente satisfeito base. Como foi Estados, "Sem as limitações de recursos e técnica que tivemos como um ISV, não há nenhum limite para até que ponto cresça."

## <a name="more-information"></a>Mais informações
* Para obter mais informações sobre conjuntos elásticos do Azure, consulte o artigo [conjuntos elásticos](sql-database-elastic-pool.md).
* Para obter mais informações sobre funções da Web e funções de trabalho, consulte o artigo [as funções de trabalho](../fundamentals-introduction-to-azure.md#compute).    
* Para saber mais sobre o Azure SQL Data Warehouse, consulte o artigo [SQL Data Warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/)
* Para saber mais sobre SnelStart, consulte o artigo [SnelStart](http://www.snelstart.nl).

