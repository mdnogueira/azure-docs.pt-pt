---
title: Gerir bases de dados de nuvem de escalamento horizontal | Microsoft Docs
description: "Utilize o serviço de tarefa de base de dados elástica para executar um script entre um grupo de bases de dados."
metakeywords: azure sql database elastic databases
services: sql-database
documentationcenter: 
manager: jhubbard
author: ddove
ms.assetid: 6fa47cf2-1162-4534-a206-6e2d95b78580
ms.service: sql-database
ms.custom: scale out apps
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: ddove
ms.openlocfilehash: f709cd38a690ba666ca290cc029caa2ce4f9dff0
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="managing-scaled-out-cloud-databases"></a>Gerir bases de dados de nuvem de escalamento horizontal
Para gerir bases de dados em partição horizontal de escalamento horizontal, o **tarefas de bases de dados elásticas** funcionalidade (pré-visualização) permite-lhe fiável executar um script de Transact-SQL (T-SQL) entre um grupo de bases de dados, incluindo:

* uma coleção personalizado de bases de dados (explicado abaixo)
* todas as bases de dados num [conjunto elástico](sql-database-elastic-pool.md)
* um conjunto de partições horizontais (criado utilizando [biblioteca de clientes de base de dados elástica](sql-database-elastic-database-client-library.md)). 

## <a name="documentation"></a>Documentação
* [Instalar os componentes de tarefa da base de dados elástica](sql-database-elastic-jobs-service-installation.md). 
* [Começar a utilizar com as tarefas de bases de dados elásticas](sql-database-elastic-jobs-getting-started.md).
* [Criar e gerir tarefas através do PowerShell](sql-database-elastic-jobs-powershell.md).
* [Criar e gerir expandido terminar bases de dados do Azure SQL](sql-database-elastic-jobs-getting-started.md)

**As tarefas de base de dados elásticas** é atualmente um cliente alojado do Azure serviço em nuvem que permite a execução do ad-hoc e agendadas tarefas administrativas, que são chamadas **tarefas**. Com tarefas, pode facilmente e fiável gerir grandes grupos de bases de dados do Azure SQL Server através da execução de scripts de Transact-SQL para efetuar operações administrativas. 

![Serviço de tarefas de base de dados elástica][1]

## <a name="why-use-jobs"></a>Porquê utilizar tarefas?
**Gerir**

Facilmente Efetue alterações de esquema, gestão de credenciais, as atualizações de dados de referência, recolha de dados de desempenho ou a coleção de telemetria do inquilino (cliente).

**Relatórios**

Dados agregados de uma coleção de bases de dados do Azure SQL Server para uma tabela de destino única.

**Reduzir a sobrecarga**

Normalmente, tem de ligar para cada base de dados de forma independente para executar instruções Transact-SQL ou efetuar outras tarefas administrativas. Uma tarefa processa a tarefa de início de sessão em cada base de dados no grupo de destino. Pode também define, manter e manter os scripts de Transact-SQL para ser executada através de um grupo de bases de dados do Azure SQL.

**Gestão de contas**

Tarefas a executar o script e o estado de execução para cada base de dados de registo. Também obter a repetição automática quando ocorrem falhas.

**Flexibilidade**

Definir grupos personalizados de bases de dados do Azure SQL e definir agendas para executar uma tarefa.

> [!NOTE]
> No portal do Azure, apenas um conjunto reduzido de funções limitada para conjuntos elásticos SQL Azure está disponível. Utilize as APIs do PowerShell para aceder ao conjunto completo de funcionalidade atual.
> 
> 

## <a name="applications"></a>Aplicações
* Execute tarefas administrativas, tal como implementar um novo esquema.
* Atualize informações de produto de dados de referência comuns em todas as bases de dados. Ou agendas automático atualiza cada dia da semana, após horas.
* Reconstrua índices para melhorar o desempenho de consulta. A reconstrução pode ser configurado para ser executado através de uma coleção de bases de dados numa base periódica, tal como durante as horas de ponta.
* Recolha os resultados da consulta de um conjunto de bases de dados numa tabela central numa base em curso. Consultas de desempenho podem ser continuamente executadas e configuradas para acionar as tarefas adicionais para ser executada.
* Execute consultas mais de processamento de dados em execução através de um grande conjunto de bases de dados, por exemplo a coleção de telemetria de cliente. Os resultados são recolhidos para uma tabela de destino única para análise adicional.

## <a name="elastic-database-jobs-end-to-end"></a>As tarefas de base de dados elásticas: ponto a ponto
1. Instalar o **tarefas de bases de dados elásticas** componentes. Para obter mais informações, consulte [tarefas de instalação de base de dados elástica](sql-database-elastic-jobs-service-installation.md). Se a instalação falhar, consulte o artigo [como desinstalar](sql-database-elastic-jobs-uninstall.md).
2. Utilize as APIs do PowerShell para aceder às funcionalidades mais, por exemplo, criar coleções de base de dados personalizado, adicionar as agendas de e/ou a recolha de conjuntos de resultados. Utilizar o portal para instalação simple e a criação/de monitorização de tarefas limitada para execução contra um **conjunto elástico**. 
3. Criar credenciais encriptado para execução da tarefa e [adicionar o utilizador (ou função) para cada base de dados no grupo](sql-database-security-overview.md).
4. Crie um script T-SQL que pode ser executado relativamente a cada base de dados no grupo de idempotent. 
5. Siga estes passos para criar tarefas no portal do Azure: [criar e gerir tarefas de bases de dados elásticas](sql-database-elastic-jobs-create-and-manage.md). 
6. Ou utilizar scripts do PowerShell: [criar e gerir tarefas de bases de dados elásticas uma base de dados SQL através do PowerShell (pré-visualização)](sql-database-elastic-jobs-powershell.md).

## <a name="idempotent-scripts"></a>Scripts de Idempotent
Os scripts têm de ser [idempotent](https://en.wikipedia.org/wiki/Idempotence). Em termos simples, "idempotent" significa que, se o script com êxito e for executada novamente, o mesmo resultado ocorre. Um script pode falhar devido a problemas de rede transitórios. Nesse caso, a tarefa seja automaticamente repetida a executar o script de um número de vezes antes de desisting predefinido. Um script de idempotent tem o mesmo resultado, mesmo que tenha sido executada com êxito duas vezes. 

Uma simple tática consiste em testar a existência de um objeto antes de criar.  

    IF NOT EXIST (some_object)
    -- Create the object 
    -- If it exists, drop the object before recreating it.

Da mesma forma, um script tem de poder ser executado com êxito pelo logicamente testar e countering quaisquer condições que encontra.

## <a name="failures-and-logs"></a>Falhas e os registos
Se um script falhar após várias tentativas, a tarefa registará o erro e continua. Após termina uma tarefa (isto é, uma execução em todas as bases de dados no grupo), pode verificar a respetiva lista de tentativas falhadas. Os registos fornecem detalhes para depurar scripts defeituoso. 

## <a name="group-types-and-creation"></a>Tipos de grupo e criação
Existem dois tipos de grupos: 

1. Conjuntos de partições horizontais
2. Grupos personalizados

Grupos de conjunto de partições horizontais são criados utilizando o [ferramentas de base de dados elástica](sql-database-elastic-scale-introduction.md). Quando cria um grupo de conjunto de partições horizontais, bases de dados são adicionados ou removidos do grupo automaticamente. Por exemplo, um ID de partição horizontal novo será automaticamente no grupo quando adiciona o mapa de partições horizontais. Em seguida, pode ser executada uma tarefa em relação ao grupo.

Por outro lado, grupos personalizados, rigidly são definidos. Explicitamente tem de adicionar ou remover bases de dados de grupos personalizados. Se uma base de dados no grupo for removido, a tarefa irá tentar executar o script na base de dados, causando uma eventual falha. Grupos criados no portal do Azure atualmente são grupos personalizados. 

## <a name="components-and-pricing"></a>Os componentes e preços
Os seguintes componentes funcionam em conjunto para criar um serviço de nuvem do Azure que permite a execução do ad-hoc das tarefas administrativas. Os componentes são instalados e configurados automaticamente durante a configuração, na sua subscrição. Pode identificar os serviços que têm o mesmo nome gerado automaticamente. O nome é exclusivo e é composta pelo prefixo "edj" seguido de 21 carateres gerados aleatoriamente.

* **Serviço Cloud do Azure**: tarefas de bases de dados elásticas (pré-visualização) é entregue como um serviço de nuvem do Azure alojada de cliente para efetuar a execução das tarefas pedidas. No portal, o serviço é implementado e alojado na sua subscrição do Microsoft Azure. A predefinição implementado executa de serviço com o mínimo de duas funções de trabalho para elevada disponibilidade. O tamanho predefinido de cada função de trabalho (ElasticDatabaseJobWorker) é executado numa instância A0. Para os preços, consulte [preços de serviços de Cloud](https://azure.microsoft.com/pricing/details/cloud-services/). 
* **Base de dados SQL do Azure**: O serviço utiliza uma base de dados de SQL do Azure conhecido como o **base de dados de controlo** para armazenar todos os metadados de tarefa. A camada de serviço predefinido é um S0. Para os preços, consulte [preços da SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).
* **Service Bus do Azure**: um Azure Service Bus destina-se coordenação de trabalho no âmbito do serviço de nuvem do Azure. Consulte [preços de barramento de serviço](https://azure.microsoft.com/pricing/details/service-bus/).
* **Armazenamento do Azure**: uma conta do Storage Azure é utilizada para armazenar os resultados de diagnóstico registo no caso de um problema requer uma depuração ainda mais (consulte [ativar diagnósticos no Cloud Services do Azure e máquinas virtuais](../cloud-services/cloud-services-dotnet-diagnostics.md)). Para os preços, consulte [preços do Storage do Azure](https://azure.microsoft.com/pricing/details/storage/).

## <a name="how-elastic-database-jobs-work"></a>Como funcionam as tarefas de base de dados elástica
1. Uma base de dados do SQL do Azure é designado uma **base de dados de controlo** que armazena todos os dados e estado de dados de metadados.
2. A base de dados de controlo é acedida pelo **tarefa serviço** para iniciar e controlar as tarefas a executar.
3. Duas funções diferentes comunicam com a base de dados de controlo: 
   * Controlador: Determina quais as tarefas de exigem tarefas a executar o trabalho pedido e as tarefas falhadas tentativas através da criação de novas tarefas.
   * Tarefa a execução da tarefa: Acarreta as tarefas da tarefa.

### <a name="job-task-types"></a>Tipos de tarefas da tarefa
Existem vários tipos de tarefas que realizar a execução de tarefas:

* ShardMapRefresh: O mapa de partições horizontais para determinar todas as bases de dados utilizadas como shards consulta o
* ScriptSplit: Divide o script em instruções 'ACEDA' em lotes
* ExpandJob: Cria subordinado tarefas para cada base de dados de uma tarefa que tenha como destino um grupo de bases de dados
* ScriptExecution: Executa um script em relação a uma determinada base de dados utilizando as credenciais definidas
* Dacpac: Aplica-se um DACPAC para uma determinada base de dados utilizando as credenciais específicas

## <a name="end-to-end-job-execution-work-flow"></a>Fluxo de trabalho do execução tarefa de ponto a ponto
1. Utilizar o Portal ou a API do PowerShell, uma tarefa é inserida o **base de dados de controlo**. A tarefa solicita a execução de um script de Transact-SQL em relação a um grupo de bases de dados utilizando credenciais específicas.
2. O controlador identifica a tarefa de novo. As tarefas da tarefa são criadas e executadas para dividir o script e bases de dados do grupo de atualização. Por último, uma nova tarefa criada e executada para expandir a tarefa e criar novos subordinados onde cada tarefa subordinada é especificada ao executar o script de Transact-SQL em relação a uma base de dados individuais no grupo de tarefas.
3. O controlador identifica as tarefas criadas subordinado. Para cada tarefa, o controlador cria e aciona a tarefa para executar o script em relação a uma base de dados. 
4. Depois de concluir todas as tarefas da tarefa, o controlador atualiza as tarefas para um estado concluído. 
   Em qualquer momento durante a execução da tarefa, a API de PowerShell pode ser utilizada para ver o estado atual da execução da tarefa. Todas as horas devolvidas pelas APIs do PowerShell são representadas em UTC. Se assim o desejar, pode ser iniciado um pedido de cancelamento para parar uma tarefa. 

## <a name="next-steps"></a>Passos seguintes
[Instalar os componentes](sql-database-elastic-jobs-service-installation.md), em seguida, [criar e adicionar um início de sessão para cada base de dados no grupo de bases de dados](sql-database-manage-logins.md). Para compreender melhor tarefa de criação e gestão, consulte [criar e gerir tarefas de bases de dados elásticas](sql-database-elastic-jobs-create-and-manage.md). Consulte também [introdução às tarefas de bases de dados elásticas](sql-database-elastic-jobs-getting-started.md).

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-overview/elastic-jobs.png
<!--anchors-->


