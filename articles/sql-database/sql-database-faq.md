---
title: Base de dados SQL do Azure FAQ | Microsoft Docs
description: "Colocar as respostas aos clientes de perguntas comuns sobre o bases de dados de nuvem e SQL Database do Azure, sistema de gestão da Microsoft base de dados relacional (RDBMS) e da base de dados como um serviço na nuvem."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 1da12abc-0646-43ba-b564-e3b049a6487f
ms.service: sql-database
ms.custom: reference
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: On Demand
ms.date: 02/07/2017
ms.author: sashan;carlrab
ms.openlocfilehash: da463bcaf91321b65c8ad1067e457b88c8dcd58f
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="sql-database-faq"></a>FAQ da Base de Dados SQL

## <a name="what-is-the-current-version-of-sql-database"></a>O que é a versão atual da base de dados SQL?
A versão atual da base de dados do SQL Server é V12. Versão V11 foi extinguido.

## <a name="what-is-the-sla-for-sql-database"></a>O que é o SLA para a base de dados SQL?
Garantimos que os clientes terão conectividade entre a Base de Dados SQL Basic, Standard ou Premium única ou elástica do Microsoft Azure e o nosso gateway de Internet durante, pelo menos, 99,99% do tempo. Para obter mais informações, consulte [SLA](http://azure.microsoft.com/support/legal/sla/).

## <a name="how-do-i-reset-the-password-for-the-server-admin"></a>Como a reposição de palavra-passe do administrador do servidor?
No [portal do Azure](https://portal.azure.com) clique **SQL Servers**, selecione o servidor da lista e, em seguida, clique em **Repor palavra-passe**.

## <a name="how-do-i-manage-databases-and-logins"></a>Como gerir bases de dados e inícios de sessão?
Consulte [gerir bases de dados e inícios de sessão](sql-database-manage-logins.md).

## <a name="how-do-i-make-sure-only-authorized-ip-addresses-are-allowed-to-access-a-server"></a>Como torno se apenas endereços IP autorizados estão autorizados a aceder a um servidor?
Consulte [como: configurar as definições da firewall da SQL Database](sql-database-configure-firewall-settings.md).

## <a name="how-does-the-usage-of-sql-database-show-up-on-my-bill"></a>Como a utilização da base de dados SQL apareçam na minha fatura?
Faturas de base de dados SQL numa taxa por hora previsível, com base na camada de serviço + nível de desempenho de bases de dados individuais ou de eDTUs por conjunto elástico. Utilização real é calculada e calculada hora a hora, pelo que a fatura poderá mostrar frações de uma hora. Por exemplo, se existir uma base de dados para 12 horas num mês, a fatura mostra a utilização de 0.5 dias. Além disso, escalões de serviço + de nível de desempenho e de eDTUs por conjunto serão descriminados na factura para tornar mais fácil ver o número de dias de base de dados utilizadas para cada um num único mês.

## <a name="what-if-a-single-database-is-active-for-less-than-an-hour-or-uses-a-higher-service-tier-for-less-than-an-hour"></a>E se uma base de dados está ativo para menos de uma hora ou utiliza uma camada de serviço mais elevada para menos de uma hora?
É-lhe faturado para cada hora, existe uma base de dados utilizando a camada de serviço mais elevada + o nível de desempenho que aplicadas durante essa hora, independentemente da utilização ou a base de dados estava ativo para menos de uma hora. Por exemplo, se criar uma base de dados e eliminá-lo mais tarde cinco minutos a fatura reflete cobrada uma taxa por hora de uma base de dados. 

Exemplos

* Se criar uma base de dados básica e, em seguida, atualizá-lo imediatamente Standard S1, são cobrado à taxa Standard S1 para a primeira hora.
* Se a atualizar uma base de dados do básica para o Premium às 10:00 e a conclusão da atualização à 1:35:00. no dia seguinte, são-lhe cobrados a taxa de Premium começando em 1:00:00. 
* Se mudar uma base de dados de Premium básico às 11:00 e terminar em 2: 00h 15, em seguida, a base de dados é cobrado à taxa Premium até 3: 00h, após o qual é cobrada às taxas de básicas.

## <a name="how-does-elastic-pool-usage-show-up-on-my-bill-and-what-happens-when-i-change-edtus-per-pool"></a>Como utilização do conjunto elástico apareçam na minha fatura e o que acontece quando altero os eDTUs por conjunto?
Agrupamento elástico cobra Mostrar cópias de segurança na sua factura como DTUs elásticas (eDTUs) em incrementos apresentados em eDTUs por conjunto no [a página de preços](https://azure.microsoft.com/pricing/details/sql-database/). Não há sem qualquer encargo por base de dados para conjuntos elásticos. É-lhe faturado para cada hora, que existe um conjunto, a eDTU máxima, independentemente da utilização ou o conjunto estava ativo para menos de uma hora. 

Exemplos

* Se criar um conjunto elástico Standard com 200 eDTUs às 11:18 a.m., bases de dados de cinco a adicionar ao agrupamento, são-lhe cobrados para 200 eDTUs para a hora de toda, começando na 11 a.m. através do resto do dia.
* No dia 2, em 5:05 a.m., base de dados 1 começa a consumir 50 eDTUs e de que detém gradual através do dia. Bases de dados 2 a 5 variam entre 0 e eDTUs de 80. Durante o dia, adicione cinco outras bases de dados que consomem eDTUs variando ao longo do dia. Dia 2 é um dia completo cobrado às 200 eDTU. 
* No dia 3, no 5 a.m. Adicione outro 15 bases de dados. Utilização de base de dados aumenta ao longo do dia para o ponto em que optar por aumentar os eDTUs de conjunto de 200 400 às 20:05 Os encargos ao nível da 200 eDTU foram em vigor até 8 pm e aumenta a 400 eDTUs para as restantes quatro horas. 

## <a name="elastic-pool-billing-and-pricing-information"></a>Obter informações sobre preços e de faturação do conjunto elástico
Conjuntos elásticos são faturados pelas seguintes características:

* Um conjunto elástico é faturado após a respetiva criação, mesmo quando existem bases de dados no conjunto.
* Um conjunto elástico é faturado de hora a hora. Esta é a mesma frequência de medição para níveis de desempenho de bases de dados individuais.
* Se um conjunto elástico é redimensionado para um novo número de edtus que, em seguida, o conjunto não é faturado, de acordo com a quantidade de nova edtus até concluir a operação de redimensionamento. Este acompanha o mesmo padrão como alterar o nível de desempenho de bases de dados individuais.
* O preço do conjunto elástico baseia-se no número de eDTUs do conjunto. O preço do conjunto elástico é independente do número e a utilização das bases de dados elásticas dentro da mesma.
* Preço é calculado pelo (número de eDTUs de conjunto) x (unitário por eDTU).

O preço de eDTU de unidade de um conjunto elástico é superior ao unitário DTU para uma base de dados da mesma camada de serviço. Para detalhes, ver os preços da [Base de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/). 

Para compreender as camadas de eDTUs e o serviço, consulte [opções de base de dados SQL e desempenho](sql-database-service-tiers.md).

## <a name="how-does-the-use-of-active-geo-replication-in-an-elastic-pool-show-up-on-my-bill"></a>Como funciona a utilização de georreplicação ativa num conjunto elástico são apresentados na minha fatura?
Ao contrário das bases de dados individuais, utilizando [georreplicação ativa](sql-database-geo-replication-overview.md) com bases de dados elásticas não tem um impacto direto de faturação.  Apenas são cobrados para as eDTUs aprovisionadas para cada um dos agrupamentos (conjunto principal e secundário agrupamento)

## <a name="how-does-the-use-of-the-auditing-feature-impact-my-bill"></a>De que forma é que a utilização da funcionalidade de auditoria se reflete na fatura?
Auditoria baseia-se para o serviço de base de dados SQL em nenhum extra de custos e está disponível para as bases de dados básicas, Standard, Premium e Premium RS. No entanto, para armazenar os registos de auditoria, as utilizações de funcionalidade de auditoria uma conta de armazenamento do Azure e as taxas de tabelas e filas no armazenamento do Azure aplicam-se com base no tamanho do seu registo de auditoria.

## <a name="how-do-i-find-the-right-service-tier-and-performance-level-for-single-databases-and-elastic-pools"></a>Como se encontrar o nível de desempenho e o escalão de serviço à direita para bases de dados individuais e conjuntos elásticos?
Existem algumas ferramentas disponíveis para si. 

* Para bases de dados no local, utilizar o [advisor de dimensionamento de DTU](http://dtucalculator.azurewebsites.net/) recomendamos as bases de dados e das dtus mínimas necessárias e avaliar várias bases de dados para conjuntos elásticos.
* Se uma base de dados iria beneficiar está a ser um conjunto, o motor inteligente do Azure recomenda um conjunto elástico se vê-lo um padrão de histórico de utilização warrants-lo. Consulte [monitorizar e gerir um conjunto elástico com o portal do Azure](sql-database-elastic-pool-manage-portal.md). Para obter mais informações sobre como realizar os cálculos, consulte [considerações sobre preço e desempenho de um conjunto elástico](sql-database-elastic-pool.md)
* Para ver se é preciso uma base de dados de aumentar ou reduzir verticalmente, consulte [guia de desempenho das bases de dados](sql-database-performance-guidance.md).

## <a name="how-often-can-i-change-the-service-tier-or-performance-level-of-a-single-database"></a>Com que frequência posso alterar o nível de desempenho ou camada de serviço de uma base de dados?
Pode alterar a camada de serviço (entre básicas, Standard, Premium e Premium RS) ou o nível de desempenho dentro de uma camada de serviço (por exemplo, S1 para S2) as vezes que pretender. Para bases de dados de versão anteriores, pode alterar o nível de desempenho ou camada de serviço um total de quatro vezes num período de 24 horas.

## <a name="how-often-can-i-adjust-the-edtus-per-pool"></a>Como muitas vezes, pode ajustar os eDTUs por conjunto?
As vezes que pretender.

## <a name="how-long-does-it-take-to-change-the-service-tier-or-performance-level-of-a-single-database-or-move-a-database-in-and-out-of-an-elastic-pool"></a>Quanto tempo demora para alterar o nível de desempenho ou camada de serviço de uma base de dados ou mover uma base de dados dentro ou fora do conjunto elástico?
Alterar o escalão de serviço de uma base de dados e mover dentro e fora de um agrupamento requerem a base de dados para ser copiado na plataforma como operação em segundo plano. A alteração da camada de serviço pode demorar de alguns minutos ou várias horas, consoante o tamanho das bases de dados. Em ambos os casos, as bases de dados permanecerem online e disponíveis durante a mudança. Para obter detalhes sobre a alteração de bases de dados individuais, consulte [alterar o escalão de serviço de uma base de dados](sql-database-service-tiers.md). 

## <a name="when-should-i-use-a-single-database-vs-elastic-databases"></a>Quando devo utilizar uma base de dados vs bases de dados elásticas?
Em geral, os conjuntos elásticos foram concebidos para típica [padrão de aplicação do software-como-um-serviço (SaaS)](sql-database-design-patterns-multi-tenancy-saas-applications.md), onde existe uma base de dados por inquilino ou cliente. Na maioria dos casos, comprar bases de dados individuais e aprovisionar em excesso para satisfazer os picos de procura para cada base de dados não é rentável. Com agrupamentos, gerir o desempenho coletivo do conjunto e as bases de dados e reduzir verticalmente Dimensionar automaticamente. Motor inteligente do Azure recomenda um conjunto de bases de dados quando um padrão de utilização warrants-lo. Para obter mais informações, consulte [orientações do conjunto elástico](sql-database-elastic-pool.md).

## <a name="what-does-it-mean-to-have-up-to-200-of-your-maximum-provisioned-database-storage-for-backup-storage"></a>O que significa ter até 200 % do armazenamento máximo de base de dados aprovisionada para o armazenamento de cópias de segurança?
Armazenamento de cópia de segurança é armazenamento associado à sua cópias de segurança automática da base de dados que são utilizadas para [ponto-na-tempo-restauro](sql-database-recovery-using-backups.md#point-in-time-restore) e [georrestauro](sql-database-recovery-using-backups.md#geo-restore). A Base de Dados SQL do Azure proporciona até 200 % do armazenamento máximo de base de dados aprovisionada para armazenamento de cópias de segurança sem custos adicionais. Por exemplo, se tiver uma instância de base de dados Standard com um tamanho de base de dados de aprovisionamento de 250 GB, são fornecidos com 500 GB de armazenamento de cópia de segurança, sem encargos adicionais. Se a base de dados exceder o armazenamento de cópia de segurança fornecido, pode optar por reduzir o período de retenção contactando o suporte do Azure ou paga para o armazenamento de cópia de segurança extra cobrado a taxa de acesso de leitura geograficamente armazenamento redundantes (RA-GRS) padrão. Para obter mais informações sobre faturação RA-GRS, consulte os detalhes de preços de armazenamento.

## <a name="im-moving-from-webbusiness-to-the-new-service-tiers-what-do-i-need-to-know"></a>Posso estou a mover de negócio/Web para os novos escalões de serviço, o que é necessário saber?
Bases de dados do Azure SQL Server Web e Business foram extinguidas agora. Os escalões básico, Standard, Premium, Premium RS e elástico substituem as retiring bases de dados Web e Business. 

## <a name="what-is-an-expected-replication-lag-when-geo-replicating-a-database-between-two-regions-within-the-same-azure-geography"></a>O que é um atraso de replicação esperado ao replicar de georreplicação uma base de dados entre duas regiões dentro da mesma geografia do Azure?
Estamos atualmente são suportar um RPO de cinco segundos e o desfasamento foi inferior ao que quando o secundário georreplicação está alojado no Azure recomendado emparelhado região e na mesma camada de serviço.

## <a name="what-is-an-expected-replication-lag-when-geo-secondary-is-created-in-the-same-region-as-the-primary-database"></a>O que é um atraso de replicação esperado quando georreplicação secundário é criado na mesma região que a base de dados primária?
Com base nos dados empirical, não há demasiado diferença entre intra região e o desfasamento entre região quando o Azure recomendado região emparelhado é utilizado. 

## <a name="if-there-is-a-network-failure-between-two-regions-how-does-the-retry-logic-work-when-geo-replication-is-set-up"></a>Se existir uma falha de rede entre duas regiões, como a lógica de repetição funciona quando configurar a georreplicação?
Se houver um desligar, iremos repetir a cada 10 segundos para voltar a estabelecer ligações.

## <a name="what-can-i-do-to-guarantee-that-a-critical-change-on-the-primary-database-is-replicated"></a>O que posso fazer para garantir que é replicada uma alteração fundamental na base de dados primária?
Georreplicação-secundário é uma réplica async e estamos a tentar guardá-lo em sincronização completa com o site primário. Mas fornecemos um método para forçar a sincronização para garantir que a replicação das alterações crítico (por exemplo, atualizações de palavra-passe). Sincronização forçada tem impacto no desempenho porque bloqueia o thread que efectua até que todas as transações consolidadas são replicadas. Para obter mais informações, consulte [sp_wait_for_database_copy_sync](https://msdn.microsoft.com/library/dn467644.aspx). 

## <a name="what-tools-are-available-to-monitor-the-replication-lag-between-the-primary-database-and-geo-secondary"></a>As ferramentas estão disponíveis para monitorizar o desfasamento entre a base de dados primária e secundária georreplicação?
Expomos o desfasamento em tempo real entre a base de dados primária e a georreplicação-secundárias através de um DMV. Para obter mais informações, consulte [sys.dm_geo_replication_link_status](https://msdn.microsoft.com/library/mt575504.aspx).

## <a name="to-move-a-database-to-a-different-server-in-the-same-subscription"></a>Para mover uma base de dados para um servidor diferente na mesma subscrição
* No [portal do Azure](https://portal.azure.com), clique em **bases de dados SQL**, selecione uma base de dados da lista e, em seguida, clique em **cópia**. Consulte [copiar uma base de dados SQL do Azure](sql-database-copy.md) para obter mais detalhes.

## <a name="to-move-a-database-between-subscriptions"></a>Para mover uma base de dados entre subscrições
* No [portal do Azure](https://portal.azure.com), clique em **servidores SQL** e, em seguida, selecione o servidor que aloja a base de dados da lista. Clique em **mover**e, em seguida, escolha os recursos para mover e mover para a subscrição.
