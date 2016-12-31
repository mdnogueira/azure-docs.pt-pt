---
title: "Continuidade empresarial na nuvem - recuperação de base de dados - Base de Dados SQL | Microsoft Docs"
description: "Saiba como a Base de Dados SQL do Azure suporta a continuidade empresarial na nuvem e a recuperação de base de dados, e ajuda a manter as aplicações fundamentais na nuvem em execução."
keywords: "continuidade empresarial, continuidade empresarial na nuvem, recuperação de base de dados após desastre, recuperação de base de dados"
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
editor: 
ms.assetid: 18e5d3f1-bfe5-4089-b6fd-76988ab29822
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/13/2016
ms.author: carlrab;sashan
translationtype: Human Translation
ms.sourcegitcommit: 747f6ca642a33c4ce9bcaacad4976e8eaed8fa44
ms.openlocfilehash: f642cfade2369f5c758ab45994c7cf3f37b6d4c5


---
# <a name="overview-of-business-continuity-with-azure-sql-database"></a>Descrição geral da continuidade empresarial com a Base de Dados SQL do Azure
Esta descrição geral descreve as capacidades que a Base de Dados SQL do Azure fornece para a continuidade empresarial e a recuperação após desastre. Fornece opções, recomendações e tutoriais para recuperar de eventos problemáticos que podem causar perda de dados ou fazer com que a base de dados e a aplicação fiquem indisponíveis. O debate inclui as medidas a tomar quando o erro de um utilizador ou aplicação afetar a integridade dos dados, uma região do Azure ficar indisponível ou a aplicação requerer manutenção. 

## <a name="sql-database-features-that-you-can-use-to-provide-business-continuity"></a>Funcionalidades da Base de Dados SQL que pode utilizar para proporcionar continuidade empresarial
A Base de Dados SQL fornece várias funcionalidades de continuidade empresarial, incluindo cópias de segurança automáticas e a replicação opcional da base de dados. Cada uma possui características diferentes para o tempo de recuperação estimado (ERT) e a potencial perda de dados de transações recentes. Assim que compreender estas opções, pode escolher entre elas - e, na maioria dos cenários, utilizá-las em conjunto para cenários diferentes. À medida que elabora o seu plano de continuidade empresarial, tem de saber qual o tempo máximo aceitável antes de a aplicação recuperar totalmente após o evento problemático – este é o objetivo de tempo de recuperação (RTO). Também tem de saber a quantidade máxima de atualizações recentes de dados (intervalo de tempo) que a aplicação pode tolerar perder ao recuperar após o evento problemático - o objetivo de ponto de recuperação (RPO). 

A tabela seguinte compara o ERT e o RPO para os três cenários mais comuns.

| Capacidade | Escalão Basic | Escalão Standard | Escalão Premium |
| --- | --- | --- | --- |
| Restauro para um Ponto Anterior no Tempo a partir de cópia de segurança |Qualquer ponto de restauro num período de 7 dias |Qualquer ponto de restauro num período de 35 dias |Qualquer ponto de restauro num período de 35 dias |
| Georrestauro a partir de cópias de segurança georreplicadas |ERT < 12h, RPO < 1h |ERT < 12h, RPO < 1h |ERT < 12h, RPO < 1h |
| Restaurar a partir do Azure Backup Vault |ERT < 12h, RPO < 1 sem |ERT < 12h, RPO < 1 sem |ERT < 12h, RPO < 1 sem |
| Georreplicação Ativa |ERT < 30s, RPO < 5s |ERT < 30s, RPO < 5s |ERT < 30s, RPO < 5s |

### <a name="use-database-backups-to-recover-a-database"></a>Utilizar cópias de segurança da base de dados para recuperar uma base de dados
A Base de Dados SQL efetua automaticamente uma combinação de cópias de segurança completas de bases de dados semanalmente, cópias de segurança diferenciais de bases de dados de hora a hora e cópias de segurança do registo de transações a cada cinco minutos para proteger a sua empresa contra perda de dados. Estas cópias de segurança são armazenadas no armazenamento localmente redundante durante 35 dias para bases de dados nas camadas de serviços Standard e Premium e durante sete dias para bases de dados na camada de serviços Basic - veja [camadas de serviços](sql-database-service-tiers.md) para obter mais detalhes sobre as camadas de serviços. Se o período de retenção da camada de serviços não for de encontro aos seus requisitos empresariais, pode aumentar o período de retenção ao [alterar a camada de serviços](sql-database-scale-up.md). As cópias de segurança completas e diferenciais da base de dados também são replicadas para um [centro de dados emparelhado](../best-practices-availability-paired-regions.md), para proteção contra indisponibilidade do centro de dados. Veja [cópias de segurança automáticas da base de dados](sql-database-automated-backups.md) para obter mais detalhes.

Se o período de retenção incorporado não for suficiente para a sua aplicação, pode expandi-lo ao configurar a política de retenção de longa duração para as bases de dados. Para obter mais informações, veja [Retenção de longa duração](sql-database-long-term-retention.md). 

Pode utilizar estas cópias de segurança automáticas da base de dados para recuperar uma base de dados após vários eventos problemáticos, tanto no seu centro de dados como para outro centro de dados. Ao utilizar cópias de segurança automáticas de bases de dados, o tempo estimado de recuperação depende de vários fatores, incluindo o número total de bases de dados a recuperar na mesma região ao mesmo tempo, o tamanho da base de dados, o tamanho do registo de transações e a largura de banda de rede. Na maioria dos casos, o tempo de recuperação é inferior a 12 horas. Ao recuperar para outra região de dados, a potencial perda de dados está limitada a 1 hora pelo armazenamento georredundante de cópias de segurança de bases de dados diferenciais de hora a hora. 

> [!IMPORTANT]
> Para recuperar através de cópias de segurança automáticas, tem de ser um membro da função de Contribuinte do SQL Server ou o proprietário da subscrição - veja [RBAC: funções incorporadas](../active-directory/role-based-access-built-in-roles.md). Pode recuperar através do portal do Azure, do PowerShell ou da API REST. Não é possível utilizar Transact-SQL.
> 
> 

Utilize cópias de segurança automáticas como o mecanismo de continuidade empresarial e de recuperação se a aplicação:

* Não for considerada fundamental para a atividade crítica.
* Não tiver um SLA de enlace, implicando que o período de indisponibilidade de 24 horas ou mais não resultará em encargos financeiros.
* Tiver uma taxa reduzida de alteração de dados (poucas transações por hora) e perder até uma hora de alterações é uma perda de dados aceitável. 
* For sensível aos custos. 

Se precisar de recuperação mais rápida, utilize a [Georreplicação Ativa](sql-database-geo-replication-overview.md) (abordada a seguir). Se precisar de recuperar dados de um período mais antigo do que 35 dias, considere arquivar a base de dados regularmente num ficheiro BACPAC (um ficheiro comprimido que contém o esquema da base de dados e os dados associados) armazenado no armazenamento de blobs do Azure ou noutra localização da sua preferência. Para obter mais informações sobre como criar um arquivo de bases de dados consistente ao nível das transações, veja [criar uma cópia da base de dados](sql-database-copy.md) e [exportar a cópia da base de dados](sql-database-export.md). 

### <a name="use-active-geo-replication-to-reduce-recovery-time-and-limit-data-loss-associated-with-a-recovery"></a>Utilizar a Georreplicação Ativa para reduzir o tempo de recuperação e limitar a perda de dados associada a uma recuperação
Além de utilizar cópias de segurança da base de dados para recuperação da base de dados em caso de uma interrupção do negócio, pode utilizar a [Georreplicação Ativa](sql-database-geo-replication-overview.md) para configurar uma base de dados com até quatro bases de dados secundárias legíveis em regiões da sua preferência. Estas bases de dados secundárias são mantidas sincronizadas com a base de dados primária através de um mecanismo de replicação assíncrona. Esta funcionalidade é utilizada para proteção contra interrupção do negócio, em caso de uma indisponibilidade do centro de dados ou durante uma atualização da aplicação. A Georreplicação Ativa também pode ser utilizada para fornecer um melhor desempenho de consulta para consultas só de leitura aos utilizadores geograficamente dispersos.

Se a base de dados primária ficar offline inesperadamente ou se tiver de colocá-la offline para atividades de manutenção, pode promover rapidamente uma base de dados secundária para primária (também chamado de ativação pós-falha) e configurar as aplicações para ligarem à base de dados primária recentemente promovida. Com uma ativação pós-falha planeada, não existe nenhuma perda de dados. Com uma ativação pós-falha não planeada, poderá existir uma pequena quantidade de perda de dados para transações muito recentes devido à natureza da replicação assíncrona. Após uma ativação pós-falha, pode fazer a reativação pós-falha posteriormente - de acordo com um plano ou quando o centro de dados voltar a ficar online. Em todos os casos, os utilizadores passam por um pequeno período de indisponibilidade e têm de restabelecer a ligação. 

> [!IMPORTANT]
> Para utilizar a Georreplicação Ativa, tem de ser o proprietário da subscrição ou ter permissões administrativas no SQL Server. Pode configurar e efetuar a ativação pós-falha com o portal do Azure, o PowerShell ou a API REST através de permissões na subscrição ou através de Transact-SQL com permissões no SQL Server.
> 
> 

Utilize a Georreplicação Ativa se a sua aplicação cumprir qualquer um dos seguintes critérios:

* É fundamental para a atividade crítica.
* Tem um contrato de nível de serviço (SLA) que não permite 24 horas ou mais de períodos de indisponibilidade.
* O período de indisponibilidade irá resultar em encargos financeiros.
* Tem uma taxa elevada de alteração de dados e perder uma hora não é aceitável.
* O custo adicional da georreplicação ativa é inferior aos potenciais encargos financeiros e perda empresarial associada.

## <a name="recover-a-database-after-a-user-or-application-error"></a>Recuperar uma base de dados após um erro de utilizador ou aplicação
* Ninguém é perfeito! Um utilizador poderá acidentalmente eliminar alguns dados, inadvertidamente remover uma tabela importante ou até mesmo remover uma base de dados completa. Ou uma aplicação poderá substituir acidentalmente dados corretos por dados incorretos devido a um defeito da aplicação. 

Neste cenário, estas são as opções de recuperação.

### <a name="perform-a-point-in-time-restore"></a>Efetuar um restauro para um ponto anterior no tempo
Pode utilizar as cópias de segurança automáticas para recuperar uma cópia da base de dados para um ponto anterior no tempo válido e conhecido, desde que esse ponto esteja dentro do período de retenção da base de dados. Depois de restaurar a base de dados, pode substituir a base de dados original pela base de dados restaurada ou copiar os dados necessários a partir dos dados restaurados para a base de dados original. Se a base de dados utilizar a Georreplicação Ativa, é recomendável copiar os dados necessários da cópia restaurada para a base de dados original. Se substituir a base de dados original pela base de dados restaurada, terá de reconfigurar e ressincronizar a Georreplicação Ativa (o que pode demorar bastante tempo para uma base de dados grande). 

Para obter mais informações e passos detalhados para restaurar uma base de dados para um ponto anterior no tempo através do portal do Azure ou através do PowerShell, veja [restauro para um ponto anterior no tempo](sql-database-recovery-using-backups.md#point-in-time-restore). Não é possível recuperar com o Transact-SQL.

### <a name="restore-a-deleted-database"></a>Restaurar uma base de dados eliminada
Se a base de dados tiver sido eliminada, mas o servidor lógico não tiver sido eliminado, pode restaurar a base de dados eliminada para o ponto anterior no tempo em que foi eliminada. Isto restaura uma cópia de segurança da base de dados para o mesmo servidor SQL lógico do qual foi eliminada. Pode restaurá-la com o nome original ou fornecer um novo nome para a base de dados restaurada.

Para obter mais informações e passos detalhados para restaurar uma base de dados eliminada através do portal do Azure ou através do PowerShell, veja [restaurar uma base de dados eliminada](sql-database-recovery-using-backups.md#deleted-database-restore). Não é possível restaurar com o Transact-SQL.

> [!IMPORTANT]
> Se o servidor lógico tiver sido eliminado, não é possível recuperar uma base de dados eliminada. 
> 
> 

### <a name="restore-from-azure-backup-vault"></a>Restaurar a partir do Azure Backup Vault
Se a perda de dados ocorreu fora do período de retenção atual para cópias de segurança automáticas e a base de dados estiver configurada para uma retenção de longa duração, pode restaurar a partir de uma cópia de segurança semanal no Azure Backup Vault para uma nova base de dados. Nesse momento, pode substituir a base de dados original pela base de dados restaurada ou copiar os dados necessários da base de dados restaurada para a base de dados original. Se precisar de obter uma versão antiga da base de dados antes de uma atualização principal da aplicação, responder a um pedido de auditores ou uma ordem legal, pode criar uma nova base de dados com uma cópia de segurança completa guardada no Azure Backup Vault.  Para obter mais informações, veja [Retenção de longa duração](sql-database-long-term-retention.md).

## <a name="recover-a-database-to-another-region-from-an-azure-regional-data-center-outage"></a>Recuperar uma base de dados para outra região a partir de uma indisponibilidade do centro de dados regional do Azure
<!-- Explain this scenario -->

Embora seja raro, um centro de dados do Azure pode ficar indisponível. Quando ocorre uma indisponibilidade, esta causa uma interrupção do negócio que poderá demorar apenas alguns minutos ou pode durar horas. 

* Uma opção é aguardar que a base de dados volte a ficar online quando a indisponibilidade do centro de dados terminar. Isto funciona para as aplicações que podem dar-se ao luxo de ter a base de dados offline. Por exemplo, um projeto de desenvolvimento ou uma versão de avaliação gratuita nos quais não tem de trabalhar constantemente. Quando um centro de dados fica indisponível, não sabe quanto tempo irá durar a indisponibilidade, pelo que esta opção só funciona se não precisar da base de dados durante algum tempo.
* Outra opção consiste em fazer a ativação pós-falha para outra região de dados se estiver a utilizar a Georreplicação Ativa ou em recuperar através de cópias de segurança da base de dados georredundantes (Georrestauro). A ativação pós-falha demora apenas alguns segundos, enquanto a recuperação a partir de cópias de segurança demora horas.

Decidir quando é necessário tomar medidas, o tempo que demora a recuperar e a quantidade de perda de dados incorrida na eventualidade de uma indisponibilidade do centro de dados depende de como utiliza as funcionalidades de continuidade empresarial descritas acima na sua aplicação. De facto, poderá optar por utilizar uma combinação de cópias de segurança da base de dados e a Georreplicação Ativa, consoante os requisitos da aplicação. Para um debate das considerações de estrutura de aplicações para bases de dados autónomas e para conjuntos elásticos com estas funcionalidades de continuidade empresarial, veja [Estruturar uma aplicação para recuperação após desastre na nuvem](sql-database-designing-cloud-solutions-for-disaster-recovery.md) e [Estratégias de recuperação após desastre do Conjunto Elástico](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).

As secções abaixo fornecem uma descrição geral dos passos para recuperar ao utilizar cópias de segurança da base de dados ou a Georreplicação Ativa. Para obter passos detalhados, incluindo os requisitos de planeamento, passos após recuperação e informações sobre como simular uma indisponibilidade para efetuar um teste de recuperação após desastre, veja [Recuperar uma Base de Dados SQL de uma indisponibilidade](sql-database-disaster-recovery.md).

### <a name="prepare-for-an-outage"></a>Preparar para uma indisponibilidade
Independentemente da funcionalidade de continuidade empresarial utilizada, tem de:

* Identificar e preparar o servidor de destino, incluindo as regras de firewall ao nível do servidor, os inícios de sessão e as permissões ao nível da base de dados mestra.
* Determinar como redirecionar os clientes e as aplicações de cliente para o novo servidor
* Documentar outras dependências, tais como definições e alertas de auditoria 

Se não planear e preparar corretamente, colocar as aplicações online após uma ativação pós-falha ou uma recuperação demora mais tempo e, provavelmente, irá também requerer a resolução de problemas numa altura tensa - uma má combinação.

### <a name="failover-to-a-geo-replicated-secondary-database"></a>Ativação pós-falha para uma base de dados georreplicada secundária
Se estiver a utilizar a Georreplicação Ativa como mecanismo de recuperação, [force uma ativação pós-falha para uma base de dados secundária georreplicada](sql-database-disaster-recovery.md#failover-to-geo-replicated-secondary-database). Em apenas alguns segundos, a base de dados secundária é promovida a primária e fica pronta para registar novas transações e responder a consultas - com apenas alguns segundos de perda de dados para os dados que ainda não tinham sido replicados. Para obter informações sobre como automatizar o processo de ativação pós-falha, veja [estruturar uma aplicação para recuperação após desastre na nuvem](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

> [!NOTE]
> Quando o centro de dados voltar a ficar online, pode fazer a reativação pós-falha para a base de dados primária original (ou não).
> 
> 

### <a name="perform-a-geo-restore"></a>Efetuar um Georrestauro
Se estiver a utilizar cópias de segurança automáticas com a replicação de armazenamento georredundante como mecanismo de recuperação, [inicie uma recuperação de base de dados com o Georrestauro](sql-database-disaster-recovery.md#recover-using-geo-restore). A recuperação normalmente decorre no prazo de 12 horas - com perda de dados até uma hora, determinada por quando a última cópia de segurança diferencial de hora a hora foi efetuada e replicada. Até concluir a recuperação, a base de dados não consegue registar quaisquer transações nem responder a consultas. 

> [!NOTE]
> Se o centro de dados voltar a ficar online antes de mudar a aplicação para a base de dados recuperada, pode simplesmente cancelar a recuperação.  
> 
> 

### <a name="perform-post-failover--recovery-tasks"></a>Efetuar a ativação pós-falha/tarefas de recuperação
Após a recuperação a partir de qualquer mecanismo de recuperação, tem de efetuar as seguintes tarefas adicionais antes de os seus utilizadores e aplicações ficarem funcionais:

* Redirecionar os clientes e as aplicações de cliente para o novo servidor e base de dados restaurada
* Certificar-se de que estão implementadas regras de firewall ao nível do servidor adequadas para que os utilizadores liguem (ou utilizar [firewalls ao nível da base de dados](sql-database-firewall-configure.md#creating-database-level-firewall-rules))
* Certificar-se de que estão implementados inícios de sessão e permissões ao nível da base de dados mestra no local adequados (ou utilizar [utilizadores contidos](https://msdn.microsoft.com/library/ff929188.aspx))
* Configurar auditorias, conforme adequado
* Configurar alertas, conforme adequado

## <a name="upgrade-an-application-with-minimal-downtime"></a>Atualizar uma aplicação com um período de indisponibilidade mínimo
Por vezes, uma aplicação tem de ser colocada offline devido à manutenção planeada, como uma atualização da aplicação. [Gerir atualizações de aplicações](sql-database-manage-application-rolling-upgrade.md) descreve como utilizar a Georreplicação Ativa para permitir a implementação de atualizações da sua aplicação na nuvem para minimizar o período de indisponibilidade durante as atualizações e fornecer um caminho de recuperação no caso de algo correr mal. Este artigo visa dois métodos diferentes para orquestrar o processo de atualização e descreve as vantagens e os compromissos de cada opção.

## <a name="next-steps"></a>Passos seguintes
Para um debate das considerações de estrutura de aplicações para bases de dados autónomas e conjuntos elásticos, veja [Estruturar uma aplicação para recuperação após desastre na nuvem](sql-database-designing-cloud-solutions-for-disaster-recovery.md) e [Estratégias de recuperação após desastre do Conjunto Elástico](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).




<!--HONumber=Dec16_HO2-->


