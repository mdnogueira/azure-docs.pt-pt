---
title: "Design serviço altamente disponível a utilizar a SQL Database do Azure | Microsoft Docs"
description: "Saiba mais sobre a estrutura de aplicação para os serviços de elevada disponibilidade utilizando a SQL Database do Azure."
keywords: "nuvem de recuperação após desastre, as soluções de recuperação após desastre, cópia de segurança de dados de aplicação, replicação geográfica, planeamento de continuidade de negócio"
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
editor: monicar
ms.assetid: e8a346ac-dd08-41e7-9685-46cebca04582
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: On Demand
ms.date: 09/08/2017
ms.author: sashan
ms.openlocfilehash: 0fb11ee553685618cc7466d3ad8b07ba01611027
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="designing-highly-available-services-using-azure-sql-database"></a>Conceber serviços de elevada disponibilidade utilizando a SQL Database do Azure

Ao criar e implementar os serviços de elevada disponibilidade no SQL Database do Azure, utilize [ativação pós-falha de grupos e a georreplicação ativa](sql-database-geo-replication-overview.md) para fornecer maior resiliência às falhas regionais e de falhas catastrófica. Também permite uma recuperação rápida para as bases de dados secundárias. Este artigo foca-se nos padrões comuns da aplicação e descreve as vantagens e os compromissos de cada opção. Para obter informações sobre a georreplicação ativa com conjuntos elásticos, consulte [estratégias de recuperação de desastre do conjunto elástico](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).

## <a name="scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime"></a>Cenário 1: Utilizar duas regiões do Azure para a continuidade do negócio com o período de indisponibilidade mínimo
Neste cenário, as aplicações com as seguintes características: 
*   Aplicação está ativa uma região do Azure
*   Todas as sessões de base de dados requerem o acesso de escrita (RW) para dados e de leitura
*   Camada Web e a camada de dados tem de ser colocalizados para reduzir o custo de latência e de tráfego 
*   Fundamentalmente, o período de indisponibilidade é um risco mais elevado do negócio para estas aplicações a perda de dados

Neste caso, a topologia de implementação de aplicação está otimizada para processar perante desastres regionais quando todos os componentes da aplicação têm de ativação pós-falha em conjunto. O diagrama abaixo mostra esta topologia. Para a redundância geográfica, os recursos da aplicação são implementados nos região A e B. No entanto, os recursos na região B não são utilizados até que a região A falha. Um grupo de ativação pós-falha está configurado entre as duas regiões para gerir a conectividade da base de dados, a replicação e ativação pós-falha. O serviço web em ambas as regiões está configurado para aceder à base de dados através do serviço de escuta de leitura e escrita  **&lt;nome do grupo de ativação pós-falha&gt;. database.windows.net** (1). Gestor de tráfego é configurado para utilizar [método de encaminhamento de prioridade](../traffic-manager/traffic-manager-configure-priority-routing-method.md) (2).  

> [!NOTE]
> [Gestor de tráfego do Azure](../traffic-manager/traffic-manager-overview.md) é utilizado em toda este artigo para fins de ilustração. Pode utilizar qualquer solução de balanceamento de carga que suporta o método de encaminhamento de prioridade.    
>

O diagrama seguinte mostra esta configuração antes de uma falha:

![Cenário 1. Configuração antes da interrupção.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario1-a.png)

Após uma falha na região primária, o serviço de base de dados SQL Deteta que a base de dados primária não está acessível e aciona uma ativação pós-falha para a região secundária com base nos parâmetros da política de ativação pós-falha automática (1). Consoante o SLA de aplicação, pode configurar um período de tolerância que controla o tempo entre a deteção da interrupção e a ativação pós-falha de si próprio. É possível que esse gestor de tráfego inicia a ativação pós-falha de ponto final antes do grupo de ativação pós-falha aciona a ativação pós-falha da base de dados. Nesse caso a aplicação web não é possível imediatamente restabeleça a ligação à base de dados. Mas os reconnections serão automaticamente bem-sucedidas, assim que concluir a ativação pós-falha da base de dados. Quando a região de falha é restaurada e novamente online, o principal anterior automaticamente a ligação como um novo secundário. O diagrama abaixo mostra a configuração após a ativação pós-falha.
 
> [!NOTE]
> Todas as transações consolidadas após a ativação pós-falha são perdidas durante o restabelecimento. Uma vez concluída a ativação pós-falha, a aplicação na região B é capaz de voltar a ligar e reinicie a processar os pedidos de utilizador. A aplicação web e a base de dados primária estão agora numa região B e permanecem localizadas conjuntamente. 
n>

![Cenário 1. Configuração após a ativação pós-falha](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario1-b.png)

Se ocorre uma falha na região B, o processo de replicação entre a primária e a base de dados secundária obtém suspenso, mas a ligação entre os dois permanece intacta (1). Tráfego gerido Deteta que a conectividade à região B está quebrada e marca a aplicação web de ponto final 2 como Degraded (2). Desempenho da aplicação não é afetado neste caso, mas a base de dados fica exposto e, por conseguinte, em maior risco de perda de dados em caso de região A falha sucessivamente.

> [!NOTE]
> Recuperação de desastres, recomendamos que a configuração com a implementação de aplicação limitada a duas regiões. Isto acontece porque a maioria as localizações geográficas do Azure tem apenas duas regiões. Esta configuração protege a aplicação contra uma falha catastrófica simultânea de ambas as regiões. Numa improvável eventualidade de uma falha de tal, pode recuperar as bases de dados numa terceira região através de [georrestauro operação](sql-database-disaster-recovery.md#recover-using-geo-restore).
>

 Assim que a interrupção é atenuada, a base de dados secundária resynchronizes automaticamente com o site primário. Durante a sincronização, o desempenho dos principais pode ser afetado. O impacto específico depende da quantidade de dados a nova principal adquirido desde a ativação pós-falha. O diagrama seguinte ilustra uma falha na região secundária:

![Cenário 1. Configuração após uma falha na região secundária.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario1-c.png)

A chave **vantagens** deste padrão de conceção são:

* A mesma aplicação web está implementada para ambas as regiões sem qualquer configuração específico da região e não requer uma lógica adicional para gerir a ativação pós-falha. 
* Desempenho da aplicação não é afetado por ativação pós-falha da aplicação web e são sempre a base de dados localizadas conjuntamente.

O principal **compromisso** é que os recursos de aplicação na região B são subutilizados na maioria das vezes.

## <a name="scenario-2-azure-regions-for-business-continuity-with-maximum-data-preservation"></a>Cenário 2: Regiões do Azure para a continuidade do negócio com a preservação de dados
Esta opção é mais adequada para as aplicações com as seguintes características:

* Perda de dados é o risco de negócio elevada. A ativação pós-falha da base de dados só pode ser utilizada como último recurso, se a interrupção é causada por uma falha catastrófica.
* A aplicação suporta modos só de leitura e de leitura-escrita de operações e pode operar no "modo só de leitura" para um período de tempo.

Neste padrão, a aplicação muda para o modo só de leitura quando as ligações de leitura e escrita comece a obter erros de tempo limite. A aplicação Web está implementado para ambas as regiões e incluem uma ligação ao ponto final do serviço de escuta de leitura / escrita e noutra ligação ao ponto final do serviço de escuta de só de leitura (1). Deve utilizar o perfil do Gestor de tráfego [prioridade encaminhamento](../traffic-manager/traffic-manager-configure-priority-routing-method.md). [Monitorização do ponto final](../traffic-manager/traffic-manager-monitoring.md) devem ser ativados para o ponto final da aplicação em cada região (2).

O diagrama seguinte ilustra esta configuração antes de uma falha:

![Cenário 2. Configuração antes da interrupção.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario2-a.png)

Quando o Gestor de tráfego Deteta uma falha de conectividade à região A, automaticamente muda o tráfego do utilizador para a instância da aplicação na região B. Com este padrão, é importante que defina o período de tolerância a perdas de dados para um valor suficientemente elevado, por exemplo 24 horas. Garante que a perda de dados é impedida se a interrupção mitigada nesse período de tempo. Quando a aplicação Web na região B é ativada as operações de leitura e escrita começam a falhar. Nessa altura, deve mudar para o modo só de leitura (1). Neste modo os pedidos automaticamente são encaminhados para a base de dados secundária. Se a interrupção é causada por uma falha catastrófica, provavelmente, não pode ser atenuado dentro do período de tolerância. Quando expirar os acionadores de grupo de ativação pós-falha de ativação pós-falha. Depois de que o serviço de escuta de leitura e escrita torna-se disponíveis e as ligações ao mesmo parar falhar (2). O diagrama seguinte ilustra as duas fases do processo de recuperação.

> [!NOTE]
> Se a interrupção na região primária é atenuada dentro do período de tolerância, o Gestor de tráfego Deteta o restauro de conectividade na região primária e muda o tráfego de utilizador para a instância da aplicação na região A. Essa instância de aplicação retoma e funciona no modo de leitura e escrita com a base de dados primária na região A conforme ilustrado o diagrama anterior.
>

![Cenário 2. Fases de recuperação após desastre.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario2-b.png)

Se uma falha acontecer na região B, o Gestor de tráfego Deteta a falha de ponto final web-app-2 na região B e marca se degradado (1). Entretanto, o grupo de ativação pós-falha muda o modo só de leitura serviço de escuta à região A (2). Esta falha não afeta a experiência de utilizador final, mas a base de dados primária está exposta durante a interrupção. O diagrama seguinte ilustra uma falha na região secundária:

![Cenário 2. Falha da região secundária.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario2-c.png)

Assim que a interrupção é atenuada, a base de dados secundária imediatamente está sincronizado com o site primário e o serviço de escuta de só de leitura é mudado novamente para a base de dados secundária na região B. Durante a sincronização desempenho dos principais pode ser afetado ligeiramente dependendo da quantidade de dados que devem ser sincronizadas.

Neste padrão de conceção tem vários **vantagens**:

* Evita a perda de dados durante as falhas temporárias.
* Período de indisponibilidade depende apenas rapidez do traffic manager Deteta a falha de conectividade, o que é configurável.

O **compromisso** é que a aplicação tem de ser capaz de funcionar no modo só de leitura.

## <a name="scenario-3-application-relocation-to-a-different-geography-without-data-loss-and-near-zero-downtime"></a>Cenário 3: Reposicionamento de aplicação para um geografia diferentes sem perda de dados e perto períodos de indisponibilidade 
Neste cenário, a aplicação tem as seguintes características: 
* Os utilizadores finais aceder à aplicação a partir de diversas localizações geográficas
* A aplicação inclui as cargas de trabalho só de leitura que não dependam de sincronização completa com as atualizações mais recentes
* Acesso de escrita de dados devem ser suportado na mesma geografia para a maioria dos utilizadores 
* Latência de leitura é essencial para a experiência de utilizador final 


Para cumprir estes requisitos que tem de garantir que o dispositivo do utilizador **sempre** estabelece ligação à aplicação implementada no mesma de geografia para as operações só de leitura, tais como os dados de navegação, análise, etc. Enquanto, as operações de OLTP são processadas na mesma geografia **maioria do tempo**. Por exemplo, durante a hora do dia operações OLTP são processadas na mesma geografia, mas durante as horas desligadas foi processadas numa geografia diferentes. Se a atividade do utilizador final principalmente ocorre durante as horas de trabalho, pode garantir o desempenho ideal para a maior parte dos utilizadores maioria do tempo. O diagrama seguinte mostra esta topologia. 
 
Recursos da aplicação devem ser implementados em cada geografia em que tenha a pedido de utilização considerável. Por exemplo, se a aplicação é utilizada ativamente nos Estados Unidos, União Europeia e Sudeste asiático a aplicação devem ser implementadas todas estas localizações geográficas. A base de dados principal deve ser dinamicamente mudou de um geografia para o seguinte no fim do horário de trabalho. Este método é denominado "seguir o sun". A carga de trabalho OLTP sempre estabelece ligação à base de dados através do serviço de escuta de leitura e escrita  **&lt;nome do grupo de ativação pós-falha&gt;. database.windows.net** (1). A carga de trabalho só de leitura estabelece ligação à base de dados local diretamente com o ponto final de servidor de bases de dados  **&lt;nome do servidor&gt;. database.windows.net** (2). Gestor de tráfego está configurado com o [método de encaminhamento de desempenho](../traffic-manager/traffic-manager-configure-performance-routing-method.md). Assegura que o dispositivo do utilizador final está ligado ao serviço web na região mais próxima. Gestor de tráfego deve ser configurado com a monitorização do ponto final ativado para cada ponto de final de serviço web (3).

> [!NOTE]
> A configuração do grupo de ativação pós-falha define a que região é utilizado para ativação pós-falha. Porque a nova principal está a ser uma geografia diferentes os resultados de ativação pós-falha de latência tempo OLTP e cargas de trabalho só de leitura até que a região afetada esteja novamente online.
>

![Cenário 3. Configuração com o site primário nos EUA Leste.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario3-a.png)

No fim do dia (por exemplo, na hora local do 23: 00), as bases de dados do Active Directory devem ser mudados para a região seguinte (Europa do Norte). Esta tarefa pode ser automatizada totalmente utilizando [do Azure de serviço de agendamento](../scheduler/scheduler-intro.md).  A tarefa envolve os seguintes passos:
* Mudar o servidor primário do grupo de ativação pós-falha para a Europa do Norte amigável ativação pós-falha (1) a utilizar
* Remova o grupo de ativação pós-falha entre EUA leste e Europa do Norte
* Crie um novo grupo de ativação pós-falha com o mesmo nome mas entre Europa do Norte e Ásia Oriental (2). 
* Adicione o site primário na Europa do Norte e secundários na Ásia Oriental a este grupo de ativação pós-falha (3).


O diagrama seguinte ilustra a nova configuração após a ativação pós-falha planeada:

![Cenário 3. Mudar o principal para a Europa do Norte.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario3-b.png)

Se uma falha acontecer na Europa do Norte por exemplo, a ativação pós-falha automática da base de dados é iniciada pelo grupo de ativação pós-falha, o que resulta de forma eficaz mover a aplicação para a região seguinte à frente das agenda (1).  Nesse caso e.u. a leste é a região secundária restantes apenas até Europa do Norte estiver novamente online. As restantes duas regiões servem os clientes em localizações três geográficas todos os pela mudança funções. O agendador do Azure tem de ser ajustado em conformidade. Porque as regiões restantes obter tráfego de utilizador adicionais da Europa, o desempenho da aplicação é afetado não só pela latência adicional, mas também por um aumento do número de ligações de utilizador final. Assim que a interrupção mitigada na Europa do Norte, a base de dados secundária não existe imediatamente está sincronizado com o principal atual. O diagrama seguinte ilustra uma falha na Europa do Norte:

![Cenário 3. Falha na Europa do Norte.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario3-c.png)

> [!NOTE]
> Pode reduzir o tempo quando a experiência do utilizador final na Europa está degradada à latência de comprimento. Para que deve proativamente implemente uma cópia de aplicação e crie bases de dados secundárias noutra região local (Europa Ocidental) como uma substituição da instância da aplicação offline na Europa do Norte. Quando a última opção é novamente online pode decidir se deve continuar a utilizar Europa ocidental ou remova a cópia da aplicação não existe e voltar ao utilizar Europa do Norte,
>

A chave **vantagens** do design desta são:
* A carga de trabalho de aplicações só de leitura acede a dados na região closets permanente. 
* A carga de trabalho de leitura-escrita de aplicações acede aos dados na região mais próxima durante o período da atividade em cada geografia mais elevado
* Porque a aplicação é implementada em várias regiões, pode sobreviver uma perda de uma das regiões sem qualquer período de indisponibilidade significativo. 

Mas existem algumas **fala**:
* Uma falha regional resulta numa geografia para ser afetado por latência superior. Cargas de trabalho de leitura / escrita e só de leitura é fornecido pela aplicação na geografia diferente. 
* As cargas de trabalho só de leitura devem ligar a um ponto final diferente em cada região. 


## <a name="business-continuity-planning-choose-an-application-design-for-cloud-disaster-recovery"></a>Planeamento de continuidade do negócio: Escolha uma estrutura de aplicação para recuperação após desastre de nuvem
A estratégia de recuperação de desastres nuvem específica pode combinar ou expandir estes padrões de conceção para melhor satisfazer as necessidades da sua aplicação.  Conforme mencionado anteriormente, a estratégia de que escolher é com base no SLA que pretende para oferecer os seus clientes e a topologia de implementação de aplicação. Para o orientar a sua decisão, a tabela seguinte compara as escolhas com base no objetivo de ponto de recuperação (RPO) e a hora da recuperação estimado (ERT).

| Padrão | RPO | ERT |
|:--- |:--- |:--- |
| Implementação de ativo-passivo para recuperação após desastre com acesso de base de dados localizadas conjuntamente |Acesso de leitura e escrita < 5 seg |Hora da deteção de falha + TTL do DNS |
| Implementação de ativo-ativo para o balanceamento de carga da aplicação |Acesso de leitura e escrita < 5 seg |Hora da deteção de falha + TTL do DNS |
| Implementação de ativo-passivo para preservação de dados |Acesso só de leitura < 5 seg | Acesso só de leitura = 0 |
||Acesso de leitura e escrita = zero | Acesso de leitura e escrita = hora da deteção de falha + período de tolerância a perdas de dados |
|||

## <a name="next-steps"></a>Passos seguintes
* Para cenários e uma descrição geral de continuidade de negócio, consulte [descrição geral da continuidade de negócio](sql-database-business-continuity.md)
* Para saber mais sobre os grupos de replicação geográfica e ativação pós-falha, consulte [georreplicação ativa](sql-database-geo-replication-overview.md)  
* Para obter informações sobre a georreplicação ativa com conjuntos elásticos, consulte [estratégias de recuperação de desastre do conjunto elástico](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).
