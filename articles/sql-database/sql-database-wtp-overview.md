---
title: "SaaS numa caixa (aplicação SaaS de exemplo com a Base de Dados SQL do Azure) | Microsoft Docs"
description: "Criar aplicações SaaS com a Base de Dados SQL"
keywords: tutorial de base de dados sql
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: tutorial
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 05/10/2017
ms.author: billgib; sstein
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: bf5745a788cd9ab6bf2ea8d5d97b8c04f083fc5d
ms.contentlocale: pt-pt
ms.lasthandoff: 05/10/2017


---
# <a name="introduction-to-the-wingtip-tickets-platform-wtp-sample-saas-application"></a>Introdução à aplicação SaaS Wingtip Tickets Platform (WTP) de exemplo

A aplicação SaaS Wingtip Tickets Platform (WTP) é uma aplicação multi-inquilino de exemplo que demonstra as vantagens exclusivas da Base de Dados SQL. A aplicação utiliza uma base de dados por inquilino, o padrão da aplicação SaaS, para servir vários inquilinos. A aplicação WTP foi concebida para demonstrar as funcionalidades da Base de Dados SQL do Azure que permitem cenários SaaS, incluindo padrões de conceção e gestão SaaS. Para começar a trabalhar rapidamente, [a aplicação WTP é implementada em menos de cinco minutos](sql-database-saas-tutorial.md)!

Depois de implementar a aplicação WTP, explore a [coleção de tutoriais](#sql-database-saas-tutorials) que são criados após a implementação inicial. Cada tutorial concentra-se nas tarefas típicas que são implementadas em aplicações SaaS. As tarefas são implementadas segundo os padrões SaaS, que tiram partido das funcionalidades incorporadas da Base de Dados SQL. Os padrões descritos incluem, aprovisionamento de novos clientes, reposição de bases de dados de inquilinos, execução de consultas distribuídas em todos os inquilinos e reposição de alterações de esquema em todas as bases de dados de inquilinos. Cada tutorial inclui scripts reutilizáveis, com explicações detalhadas que simplificam significativamente a compreensão e a implementação dos mesmos padrões de gestão SaaS nas aplicações.

Apesar de a aplicação WTP ser completa e apelativa enquanto aplicação de exemplo, é importante concentrar-se nos padrões SaaS básicos, dado que estão relacionados com a camada de dados. Por outras palavras, concentre-se na camada de dados e não em analisar em excesso a aplicação em si. Compreender a implementação destes padrões SaaS básicos é fundamental para implementar estes padrões nas suas aplicações, tendo em conta as eventuais modificações necessárias para os seus requisitos comerciais específicos.



## <a name="application-architecture"></a>Arquitetura da aplicação

A aplicação WTP utiliza o modelo de base de dados por inquilino e utiliza conjuntos elásticos SQL para maximizar a eficiência.
Utilização de um catálogo de inquilino para o aprovisionamento da gestão e da conectividade.
Alerta e monitorização de base de dados, conjuntos e aplicações integradas (OMS).
Gestão de dados de referência e esquema entre-inquilinos (tarefas da bases de dados elástica).
Consulta entre-inquilinos, análise operacional (consulta elástica).
Utilização de dados de distribuição geográfica para alcance expandido.
Continuidade empresarial, Recuperação de inquilino individual (PITR), DR à escala (georrestauro, georreplicação, DR automática), Gestão personalizada de inquilino (através de APIs de gestão), PITR para recuperar de erros causados pelo utilizador.

A aplicação Wingtip essencial utiliza um conjunto com três inquilinos de exemplo, bem como uma base de dados do catálogo.

![WTP](media/sql-database-wtp-overview/wtp-architecture.png)


## <a name="sql-database-wtp-saas-tutorials"></a>Tutoriais SaaS WTP da Base de Dados SQL

Os tutoriais seguintes são criados após a implementação inicial da [aplicação exemplo SaaS da Wingtip Tickets Platform](sql-database-saas-tutorial.md):

| Área | Descrição | Localização do script |
|:--|:--|:--|
|[Tutorial Aprovisionar e catalogar inquilinos](sql-database-saas-tutorial-provision-and-catalog.md)| Aprovisionar novos inquilinos e registá-los no catálogo | [Scripts no github](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules/Provision%20and%20Catalog) |
|[Tutorial Monitorizar e gerir o desempenho](sql-database-saas-tutorial-performance-monitoring.md)| Monitorizar e gerir o desempenho da base de dados e do conjunto | [Scripts no github](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules/Performance%20Monitoring%20and%20Management) |
|[Tutorial Restaurar um inquilino individual](sql-database-saas-tutorial-restore-single-tenant.md)| Restaurar bases de dados de inquilinos | [Scripts no github](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules/Business%20Continuity%20and%20Disaster%20Recovery/RestoreTenant) |
|[Tutorial Gerir esquema de inquilinos](sql-database-saas-tutorial-schema-management.md)| Executar consultas em todos os inquilinos  | [Scripts no github](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules/Schema%20Management) |
|[Tutorial Executar análises ad hoc](sql-database-saas-tutorial-adhoc-analytics.md) | Criar uma base de dados de análise ad hoc e executar consultas em todos os inquilinos  | [Scripts no github](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules/Operational%20Analytics/Adhoc%20Analytics) |
|[Tutorial Gerir com o Log Analytics (OMS)](sql-database-saas-tutorial-log-analytics.md) | Configurar e explorar o Log Analytics | [Scripts no github](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules/Performance%20Monitoring%20and%20Management/LogAnalytics) |
|[Tutorial Executar análises de inquilinos](sql-database-saas-tutorial-tenant-analytics.md) | Configurar e executar consultas de análises de inquilinos | [Scripts no github](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules/Operational%20Analytics/Tenant%20Analytics) |

## <a name="get-the-wingtip-application-scripts"></a>Obter os scripts da aplicação Wingtip

Os scripts da Wingtip Tickets e o código fonte da aplicação estão disponíveis no repositório do github [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS). Os ficheiros dos scripts estão localizados na pasta [Módulos de Aprendizagem](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules). Transfira a pasta **Módulos de Aprendizagem** para o computador local, mantendo a estrutura das pastas.

## <a name="working-with-the-wtp-powershell-scripts"></a>Trabalhar com os Scripts do PowerShell da WTP

As vantagens de trabalhar com a aplicação de WTP têm a ver com a exploração dos scripts fornecidos e de examinar a forma como os diferentes padrões SaaS são implementados.

Para ver os scripts e módulos fornecidos e para facilitar a sua análise para uma melhor compreensão, utilize o [ISE do Windows PowerShell](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise). Uma vez que a maioria dos scripts com o prefixo *Demo-* contém variáveis que pode modificar antes da execução, a utilização do ISE do PowerShell simplifica o trabalho com estes scripts.

Para cada implementação da aplicação WTP, existe um ficheiro **UserConfig.psm1** que contém dois parâmetros para definir os valores do grupo de recursos e do nome de utilizador que definir durante a implementação. Após concluir a implementação, edite o módulo **UserConfig.psm1** ao definir os parâmetros  _ResourceGroupName_ e _Nome_. Estes valores são utilizados por outros scripts para a execução bem sucedida, por isso, recomenda-se defini-los ao concluir a implementação!



### <a name="execute-scripts-by-pressing-f5"></a>Executar Scripts ao premir F5

Vários scripts utilizam *$PSScriptRoot* para permitir navegar nas pastas e esta variável é avaliada apenas quando o script é executado ao premir **F5**.  Destacar e executar uma seleção (**F8**) pode resultar em erros. Por isso, prima **F5** quando executar scripts WTP.

### <a name="step-through-the-scripts-to-examine-the-implementation"></a>Analisar os scripts para examinar a implementação

Para saber qual o verdadeiro valor da exploração dos scripts, execute-os para ver o que fazem. Veja os scripts _Demo-_ de primeiro nível, que proporcionam um fluxo de trabalho detalhado de leitura fácil que mostra os passos necessários para realizar cada tarefa. Examine a fundo as chamadas individuais para ver os detalhes de implementação dos diferentes padrões de SaaS.

Sugestões para trabalhar e [depurar scripts do PowerShell](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise):

* Abra e configure os scripts demo- no ISE do PowerShell.
* Execute ou continue com **F5**. Utilizar **F8** não é aconselhado, porque *$PSScriptRoot* não é avaliado quando se executam seleções de um script.
* Para colocar os pontos de interrupção, clique ou selecione uma linha e prima **F9**.
* Para executar o próximo passo numa chamada de função ou script, utilize **F10**.
* Para avançar para uma chamada de função ou script, utilize **F11**.
* Para ir até ao fim do procedimento da chamada de função ou script atual, utilize **Shift + F11**.




## <a name="explore-database-schema-and-execute-sql-queries-using-ssms"></a>Explorar o esquema de base de dados e executar consultas SQL com o SSMS

Utilize o [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) para estabelecer ligação e navegar nas bases de dados e servidores da WTP.

A aplicação WTP de exemplo inicialmente tem dois servidores da Base de Dados SQL para ligar ao servidor *tenants1* e ao servidor *catalog*:


1. Abra o *SSMS* e ligue-se ao servidor *tenants1-&lt;Utilizador&gt;.database.windows.net*.
2. Clique em **Ligar** > **Motor de Base de Dados...**:

   ![servidor de catálogo](media/sql-database-wtp-overview/connect.png)

1. As credenciais de demonstração são: Início de sessão = *developer*, Palavra-passe = *P@ssword1*

   ![ligação](media\sql-database-wtp-overview\tenants1-connect.png)

1. Repita os passos 2 e 3 e ligue-se ao servidor *catalog-&lt;Utilizador&gt;.database.windows.net*.

Após estabelecer ligação, deverá poder ver os dois servidores. Poderá ver mais ou menos bases de dados consoante o número de inquilinos que aprovisionou:

![explorador de objetos](media/sql-database-wtp-overview/object-explorer.png)



## <a name="next-steps"></a>Passos seguintes

[Implementar a aplicação SaaS Wingtip Tickets de exemplo](sql-database-saas-tutorial.md)
