---
title: "Criar soluções de recuperação de desastre - SQL Database do Azure | Microsoft Docs"
description: "Saiba como conceber a sua solução de nuvem de recuperação após desastre ao escolher o padrão de ativação pós-falha à direita."
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
editor: monicar
ms.assetid: 2db99057-0c79-4fb0-a7f1-d1c057ec787f
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: Inactive
ms.date: 04/07/2017
ms.author: sashan;carlrab
ms.openlocfilehash: 03bc991d5c1f644b439e9ebfa0d750cbf0c56764
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="disaster-recovery-strategies-for-applications-using-sql-database-elastic-pools"></a>Estratégias de recuperação de desastre para aplicações que utilizam conjuntos elásticos da base de dados SQL
Ao longo dos anos podemos ter aprendidas que serviços em nuvem não são foolproof e ocorrem de incidentes catastrófica. Base de dados do SQL Server fornece várias capacidades para fornecer a continuidade de negócio da sua aplicação quando ocorrem estes incidentes. [Conjuntos elásticos](sql-database-elastic-pool.md) e bases de dados individuais suportam o mesmo tipo de funcionalidades de recuperação após desastre. Este artigo descreve várias estratégias de DR para conjuntos elásticos que tiram partido destas funcionalidades de continuidade empresarial da base de dados do SQL Server.

Este artigo utiliza o padrão de aplicação SaaS ISV canónico seguinte:

<i>Uma aplicação web baseados na nuvem modernas Aprovisiona uma base de dados do SQL Server para cada utilizador final. O ISV tem muitos clientes e, por conseguinte, utiliza muitas bases de dados, conhecidos como bases de dados do inquilino. Porque as bases de dados do inquilino têm, normalmente, os padrões de atividade imprevisíveis, o ISV utiliza um conjunto elástico para tornar a base de dados custo muito previsível ao longo de períodos de tempo prolongados. O conjunto elástico também simplifica a gestão de desempenho quando picos de atividade do utilizador. Para além das bases de dados do inquilino a aplicação também utiliza várias bases de dados para gerir perfis de utilizador, segurança, recolher padrões de utilização etc. Disponibilidade de inquilinos individuais não afeta a disponibilidade da aplicação como todo. No entanto, a disponibilidade e o desempenho das bases de dados de gestão é essencial para a função da aplicação e se as bases de dados de gestão estão offline a aplicação completa está offline.</i>  

Este artigo aborda as estratégias de DR que abrangem uma gama de cenários de aplicações de arranque confidenciais de custos para aqueles com requisitos de disponibilidade rigorosos.

## <a name="scenario-1-cost-sensitive-startup"></a>Cenário 1. Custo arranque confidencial
<i>Posso estou uma empresa de arranque e estou de custo extremamente confidencial.  Pretendo simplificar a implementação e gestão da aplicação e pode tiver um SLA limitado de clientes individuais. Mas quero garantir a aplicação como um todo nunca está offline.</i>

Para satisfazer o requisito de simplicidade, implementar todas as bases de dados do inquilino para um conjunto elástico na região do Azure à sua escolha e implementar as bases de dados de gestão como georreplicação bases de dados individuais. Para a recuperação após desastre de inquilinos, utilize georrestauro, vem sem custos adicionais. Para garantir a disponibilidade das bases de dados de gestão, georreplicação replicá-los noutra região utilizando uma ativação pós-falha automática de grupo (no-pré-visualização) (passo 1). O custo em curso da configuração de recuperação após desastre, este cenário é igual ao custo total das bases de dados secundárias. Esta configuração é ilustrada no diagrama seguinte.

![Figura 1](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-1.png)

Se ocorrer uma falha na região primária, os passos de recuperação para colocar a sua aplicação online são ilustrados pelo diagrama seguinte.

* O grupo de ativação pós-falha inicia a ativação pós-falha automática da base de dados de gestão para a região DR. A aplicação automaticamente é novamente ligada para as novas contas de principais e todos os novos e bases de dados de inquilino são criados na região DR. Os clientes existentes veem os dados temporariamente indisponíveis.
* Crie o conjunto elástico com a mesma configuração de que o conjunto original (2).
* Utilize georrestauro para criar cópias do inquilino bases de dados (3). Pode considerar a acionar os restauros individuais, as ligações de utilizador final ou utilize outra esquema de prioridade específicas da aplicação.


Neste momento a aplicação estiver novamente online na região DR, mas alguns clientes experiência atraso quando aceder aos respetivos dados.

![Figura 2](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-2.png)

Se a interrupção temporária, é possível que a região primária é recuperada do Azure antes de todos os restauros de base de dados completos na região DR. Neste caso, orquestrar mover a aplicação para a região primária. O processo executa os passos ilustrados no diagrama seguinte.

* Cancele a todos os pedidos de georrestauro pendentes.   
* Ativação pós-falha as bases de dados de gestão para a região primária (5). Após a recuperação a região, os antigos primaries automaticamente ficou secundárias. Agora, mudar funções novamente. 
* Altere a cadeia de ligação da aplicação para apontar de volta para a região primária. Agora todas as novas contas e bases de dados de inquilino são criados na região primária. Alguns clientes existentes veem os dados temporariamente indisponíveis.   
* Defina todas as bases de dados no agrupamento de DR para só de leitura para garantir que não podem ser modificadas na região DR (6). 
* Para cada base de dados no agrupamento de DR que tenha sido alterado desde a recuperação, mude o nome ou eliminar as bases de dados correspondentes no conjunto principal (7). 
* Copie as bases de dados atualizadas do conjunto de DR para o conjunto principal (8). 
* Eliminar o conjunto de DR (9)

Neste momento a sua aplicação está online na região primária com todos os inquilinos bases de dados disponível no agrupamento de primário.

![Figura 3](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-3.png)

A chave **beneficiar** desta estratégia é baixo custo em curso para a redundância de camada de dados. São efetuadas cópias de segurança automaticamente pelo serviço de base de dados SQL com nenhuma aplicação reescrever e sem custos adicionais.  O custo quando apenas quando as bases de dados elásticas estão restauradas. O **compromisso** é que a recuperação completa de todas as bases de dados do inquilino demora muito tempo. O período de tempo depende o total geral tamanho das bases de dados de inquilino e número de restauros iniciar na região DR. Mesmo que atribuir prioridades restauros dos inquilinos, alguns através de outros utilizadores, são competir com todos os outros restauros que sejam iniciados na mesma região que o serviço arbitrates e acelera para minimizar o impacto geral em bases de dados dos clientes existentes. Além disso, a recuperação de bases de dados do inquilino não consegue iniciar até que o novo conjunto elástico na região DR é criado.

## <a name="scenario-2-mature-application-with-tiered-service"></a>Cenário 2. Aplicação madura com o serviço em camadas
<i>Sou uma aplicação SaaS madura com ofertas de serviço em camadas e SLAs diferentes para os clientes de avaliação e pagar com clientes. Para os clientes de avaliação, tenho de reduzir o custo quanto possível. Clientes avaliação podem demorar um período de indisponibilidade, mas pretender reduzem a probabilidade. Para os clientes pagar, qualquer período de inatividade é um risco de voo. Para pretendo certificar-se de que pagar os clientes conseguem sempre aceder aos respetivos dados.</i> 

Para suportar este cenário, separe os inquilinos avaliação de inquilinos pagos colocando-los em conjuntos elásticos separados. Os clientes avaliação tem inferior eDTU por inquilino e inferior SLA com mais tempo de recuperação. Os clientes de pagar estão num conjunto com superior eDTU por inquilino e um SLA mais elevado. Para garantir o menor tempo de recuperação, inquilino as bases de dados os clientes de pagar são georreplicação. Esta configuração é ilustrada no diagrama seguinte. 

![Figura 4](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-4.png)

Como o primeiro cenário, as bases de dados de gestão são bastante Active Directory para utilizar uma única base de dados georreplicação para o mesmo (1). Isto garante o desempenho previsível para subscrições de cliente novo, atualizações de perfil e outras operações de gestão. A região na qual residem primaries das bases de dados de gestão é a região primária e a região na qual residem as bases de dados secundárias das bases de dados de gestão é a região DR.

Inquilino as bases de dados os clientes de pagar tiverem bases de dados ativas no agrupamento "pago" aprovisionado na região primária. Aprovisione um conjunto com o mesmo nome na região DR secundário. Cada inquilino é georreplicação ao agrupamento de secundário (2). Isto permite uma recuperação rápida de todas as bases de dados inquilino através de ativação pós-falha. 

Se ocorrer uma falha na região primária, os passos de recuperação para colocar a sua aplicação online são ilustrados no diagrama seguinte:

![figura 5](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-5.png)

* Efetuar imediatamente a ativação pós-falha as bases de dados de gestão para a região DR (3).
* Altere a cadeia de ligação da aplicação para apontar para a região DR. Agora todas as novas contas e bases de dados de inquilino são criados na região DR. Os clientes existentes de avaliação, consulte os respetivos dados temporariamente indisponíveis.
* Ativação pós-falha as bases de dados do inquilino paga do agrupamento na região DR para restaurar imediatamente a respetiva disponibilidade (4). Uma vez que a ativação pós-falha de uma alteração do nível de metadados rápido, considere uma otimização onde as ativações pós-falha individuais são acionadas a pedido através de ligações de utilizador final. 
* Se o tamanho de eDTU do conjunto secundária foi inferior ao principal porque as bases de dados secundárias necessário apenas a capacidade para processar os registos de alteração enquanto estavam réplicas secundárias, imediatamente aumentar a capacidade de agrupamento agora para acomodar a carga de trabalho completa de todos os inquilinos ( 5). 
* Crie novo conjunto elástico com o mesmo nome e a mesma configuração na região DR para bases de dados dos clientes de avaliação (6). 
* Assim que for criado o conjunto de avaliação dos clientes, utilize georrestauro para restaurar as bases de dados individuais de inquilino de avaliação para o novo agrupamento (7). Considere a acionar os restauros individuais, as ligações de utilizador final ou utilize outra esquema de prioridade específicas da aplicação.

Neste momento a sua aplicação estiver novamente online na região DR. Todos os clientes de pagar tem acesso aos respetivos dados enquanto os clientes avaliação experiência atraso quando aceder aos respetivos dados.

Quando a região primária é recuperada Azure *depois* restaurou a aplicação na região DR pode continuar a executar a aplicação nessa região ou pode optar por efetuar novamente a região primária. Se a região primária for recuperada *antes* concluído o processo de ativação pós-falha, considere a falhar back imediato. A reativação pós-falha executa os passos ilustrados no diagrama seguinte: 

![figura 6](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-6.png)

* Cancele a todos os pedidos de georrestauro pendentes.   
* Ativação pós-falha as bases de dados de gestão (8). Após a recuperação a região, o principal anterior tornar automaticamente-se secundário. Agora, torna-se o principal novamente.  
* Ativação pós-falha as bases de dados do inquilino paga (9). De igual modo, após a recuperação a região, os antigos primaries automaticamente tornar-se as bases de dados secundárias. Agora que fiquem os primaries novamente. 
* Conjunto de bases de dados de avaliação restauradas que tenham sido alterados na região DR só de leitura (10).
* Para cada base de dados no agrupamento de avaliação clientes DR que foram alterados desde a recuperação, mude o nome ou eliminar a base de dados correspondente no conjunto de principais de avaliação clientes (11). 
* Copie as bases de dados atualizadas do conjunto de DR para o conjunto principal (12). 
* Eliminar o conjunto de DR (13) 

> [!NOTE]
> A operação de ativação pós-falha é assíncrona. Para minimizar o tempo de recuperação é importante que executar o comando de ativação pós-falha do inquilino bases de dados em lotes de, pelo menos, 20 bases de dados. 
> 
> 

A chave **beneficiar** desta estratégia é que fornece o SLA mais elevado para os clientes de pagar. Esta ação garante também que o novo avaliações estiverem desbloqueadas, assim que o conjunto de DR avaliação é criado. O **compromisso** é que esta configuração aumenta o custo total de bases de dados do inquilino, o custo do conjunto de DR secundário para paga clientes. Além disso, se o agrupamento de secundário não tem um tamanho diferente, os clientes de pagar experiência de desempenho inferior após a ativação pós-falha até que a atualização do agrupamento na região DR esteja concluída. 

## <a name="scenario-3-geographically-distributed-application-with-tiered-service"></a>Cenário 3. Aplicação distribuída geograficamente com o serviço em camadas
<i>Tenho uma aplicação SaaS madura com ofertas de serviço em camadas. Pretende oferecer um SLA muito agressiva aos meus clientes pagas e minimizar o risco de impacto quando ocorrem falhas porque interrupção breve mesmo pode causar insatisfação de cliente. É fundamental que os clientes de pagar sempre podem aceder aos respetivos dados. As avaliações são gratuitas e um SLA não é oferecido durante o período de avaliação.</i> 

Para suportar este cenário, utilize três conjuntos elásticos separados. Aprovisione dois conjuntos de tamanho igual com elevada eDTUs por base de dados em duas regiões diferentes para conter inquilino as bases de dados os clientes paga. O terceiro conjunto que contém os inquilinos avaliação pode ter inferior eDTUs por base de dados e aprovisionamento de uma das duas regiões.

Para garantir o menor tempo de recuperação durante a falhas, inquilino as bases de dados os clientes de pagar são georreplicação com 50% das bases de dados primárias em cada uma das duas regiões. Da mesma forma, cada região tem 50% das bases de dados secundárias. Desta forma, se uma região estiver offline, os apenas 50% das bases de dados dos clientes paga são afetados e tem de efetuar a ativação pós-falha. As outras bases de dados permanecem intactas. Esta configuração é ilustrada no diagrama seguinte:

![Figura 4](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-7.png)

Tal como nos cenários anteriores, as bases de dados de gestão são bastante Active Directory, por isso, configurá-las como único georreplicação bases de dados (1). Isto garante o desempenho previsível do cliente nova subscrições, as atualizações de perfil e outras operações de gestão. Região A região primária para as bases de dados de gestão e a região B é utilizada para recuperação das bases de dados de gestão.

Inquilino as bases de dados os clientes de pagar são também georreplicação mas com primaries e bases de dados secundárias divididas entre região A e região B (2). Desta forma, os inquilino principal bases de dados afetados pela falha podem efetuar a ativação pós-falha para a região e fique disponíveis. A outra metade das bases de dados do inquilino não são ser afetado de todo. 

O diagrama seguinte ilustra os passos de recuperação a tomar se ocorrer uma falha na região A.

![figura 5](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-8.png)

* Efetuar imediatamente a ativação pós-falha de gestão bases de dados à região B (3).
* Altere a cadeia de ligação da aplicação para apontar para as bases de dados de gestão na região B. Modifique as bases de dados de gestão para se certificar de que as novas contas e bases de dados de inquilino são criados na região B e as bases de dados existente do inquilino encontram-se existir, bem como. Os clientes existentes de avaliação, consulte os respetivos dados temporariamente indisponíveis.
* Efetuar a ativação pós-falha de bases de dados do inquilino paga ao agrupamento 2 na região B imediatamente restaurar a respetiva disponibilidade (4). Uma vez que a ativação pós-falha de uma alteração do nível de metadados rápido, pode considerar otimização onde as ativações pós-falha individuais são acionadas a pedido através de ligações de utilizador final. 
* Desde agora conjunto 2 contém apenas primários bases de dados, a carga de trabalho total no aumenta conjunto e imediatamente pode aumentar o tamanho de eDTU (5). 
* Crie novo conjunto elástico com o mesmo nome e a mesma configuração na região B para bases de dados dos clientes de avaliação (6). 
* Depois do conjunto for criado utilize georrestauro para restaurar a base de dados individuais de inquilino de avaliação para o conjunto (7). Pode considerar a acionar os restauros individuais, as ligações de utilizador final ou utilize outra esquema de prioridade específicas da aplicação.

> [!NOTE]
> A operação de ativação pós-falha é assíncrona. Para minimizar o tempo de recuperação, é importante que executar o comando de ativação pós-falha do inquilino bases de dados em lotes de, pelo menos, 20 bases de dados. 
> 

Neste momento a aplicação estiver novamente online na região B. Todos os clientes de pagar tem acesso aos respetivos dados enquanto os clientes avaliação experiência atraso quando aceder aos respetivos dados.

Quando a região A recuperação terá de decidir se pretende utilizar região B para clientes de avaliação ou reativação pós-falha para utilizar o agrupamento de avaliação de clientes na região A. Um critério foi possível a % de bases de dados do inquilino de avaliação modificada uma vez que a recuperação. Independentemente desse decisão, terá de voltar a equilibras os inquilinos pagos entre dois conjuntos. o diagrama seguinte ilustra o processo quando as bases de dados do inquilino de avaliação não voltar a região A.  

![figura 6](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-9.png)

* Cancele a todos os pedidos pendentes georrestauro ao agrupamento de DR avaliação.   
* Ativação pós-falha da base de dados de gestão (8). Após a recuperação a região, o principal anterior ficou automaticamente secundário. Agora, torna-se o principal novamente.  
* Selecione o inquilino pago bases de dados falharem novamente para o conjunto 1 e iniciar a ativação pós-falha para as respetivas bases de dados secundárias (9). Após a recuperação a região, todas as bases de dados no conjunto 1 automaticamente deixou de estar bases de dados secundárias. Agora a 50% deles ficar primaries novamente. 
* Reduza o tamanho do conjunto 2 para a eDTU original (10).
* Conjunto de todos os restaurados avaliação bases de dados na região B para só de leitura (11).
* Para cada base de dados no conjunto de DR avaliação que tenha sido alterado desde a recuperação, mude o nome ou eliminar a base de dados correspondente no conjunto principal de avaliação (12). 
* Copie as bases de dados atualizadas do conjunto de DR para o conjunto principal (13). 
* Eliminar o conjunto de DR (14) 

A chave **vantagens** desta estratégia são:

* Suporta o SLA mais agressiva para os clientes de pagar porque garante que uma falha não pode afetar mais de 50% das bases de dados do inquilino. 
* Garante que o novo avaliações estiverem desbloqueadas, assim que o conjunto de DR de registo é criado durante a recuperação. 
* Permite uma utilização mais eficiente da capacidade de agrupamento como 50% das bases de dados secundárias no conjunto 1 e conjunto 2 garantidos ser menos ativas que as bases de dados principais.

O principal **compromissos** são:

* As operações CRUD contra as bases de dados de gestão têm latência inferior dos utilizadores finais à região A que para os utilizadores finais à região B, que são executadas contra o principal das bases de dados de gestão.
* Requer mais complexa estrutura da base de dados de gestão. Por exemplo, cada registo de inquilino tem uma tag de localização que tem de ser alterada durante a ativação pós-falha e a reativação pós-falha.  
* Os clientes de pagar podem ter um desempenho inferior do que o habitual até que a atualização do agrupamento na região B esteja concluída. 

## <a name="summary"></a>Resumo
Este artigo incida nas estratégias de recuperação de desastres para a camada de base de dados utilizado por uma aplicação de multi-inquilino de SaaS ISV. A estratégia de que escolher baseia-se nas necessidades da aplicação, tais como o modelo de negócio, o SLA pretende oferecer aos seus clientes, budget restrição etc. Cada estratégia descrita descreve as vantagens e compromisso, pelo que pode tomar uma decisão informada. Além disso, a aplicação específica provável inclui outros componentes do Azure. Assim, reveja as orientações de continuidade de negócio e orquestrar com a recuperação da camada de base de dados com os mesmos. Para saber mais sobre a gestão de recuperação de aplicações de base de dados no Azure, consulte [estruturar soluções de nuvem de recuperação após desastre](sql-database-designing-cloud-solutions-for-disaster-recovery.md).  

## <a name="next-steps"></a>Passos seguintes
* Para saber mais sobre o SQL do Azure, base de dados automática de cópias de segurança, consulte [cópias de segurança automatizadas de base de dados SQL](sql-database-automated-backups.md).
* Para cenários e uma descrição geral de continuidade de negócio, consulte [descrição geral da continuidade do negócio](sql-database-business-continuity.md).
* Para saber mais sobre a utilização de cópias de segurança automatizadas para recuperação, consulte [restaurar uma base de dados de cópias de segurança iniciou o serviço](sql-database-recovery-using-backups.md).
* Para saber mais sobre as opções de recuperação mais rápidas, consulte [georreplicação ativa](sql-database-geo-replication-overview.md).
* Para saber mais sobre a utilização de cópias de segurança automatizadas para arquivar, consulte [cópia da base de dados](sql-database-copy.md).

