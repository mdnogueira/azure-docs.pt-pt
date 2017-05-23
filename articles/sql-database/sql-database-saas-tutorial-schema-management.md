---
title: "Gerir o esquema da Base de Dados SQL numa aplicação multi-inquilino | Microsoft Docs"
description: "Gerir o Esquema para vários inquilinos numa aplicação multi-inquilino que utiliza a Base de Dados SQL do Azure"
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
ms.sourcegitcommit: fc4172b27b93a49c613eb915252895e845b96892
ms.openlocfilehash: 19d02229781186053a0063af1c7e1a3280179f46
ms.contentlocale: pt-pt
ms.lasthandoff: 05/12/2017


---
# <a name="manage-schema-for-multiple-tenants-in-the-wtp-saas-application"></a>Gerir esquema para vários inquilinos na aplicação SaaS WTP

O tutorial Introdução à Aplicação WTP mostra como a aplicação WTP pode aprovisionar uma base de dados de inquilinos com o seu esquema inicial e registá-la no catálogo. Como qualquer aplicação, a aplicação WTP evoluirá ao longo do tempo e exigirá, por vezes, que sejam feitas alterações à base de dados. As alterações podem incluir o esquema novo ou alterado, os dados de referência novos ou alterados e as tarefas de manutenção de rotina da base de dados para garantir o desempenho ideal da aplicação. Com uma aplicação SaaS, estas alterações têm de ser implementadas de forma coordenada através de uma frota potencialmente massiva de bases de dados de inquilinos. As alterações também têm de ser incorporadas no processo de aprovisionamento das bases de dados de inquilinos futuras.

Este tutorial explora dois cenários – implementar atualizações de dados de referência para todos os inquilinos e devolver um índice na tabela que contém os dados de referência. A funcionalidade [Tarefas elásticas](sql-database-elastic-jobs-overview.md) é utilizada para executar estas operações em todos os inquilinos e numa base de dados de inquilinos *dourada* que é utilizada como modelo para as novas bases de dados.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]

> * Criar uma conta de tarefa para consulta em vários inquilinos
> * Atualizar dados em todas as bases de dados de inquilinos
> * Criar um índice numa tabela em todas as bases de dados de inquilinos


Para concluir este tutorial, devem ser cumpridos os seguintes pré-requisitos:

* A aplicação WTP está implementada. Para implementar em menos de cinco minutos, veja [Implementar e explorar a aplicação SaaS WTP](sql-database-saas-tutorial.md)
* O Azure PowerShell está instalado. Para obter mais detalhes, veja [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* Está instalada a versão mais recente do SQL Server Management Studio (SSMS). [Transferir e instalar o SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)

*Este tutorial utiliza funcionalidades do serviço de Base de Dados SQL que estão numa pré-visualização limitada (tarefas da Base de Dados Elástica). Se pretender realizar este tutorial, forneça o ID da subscrição paraSaaSFeedback@microsoft.com com assunto=Elastic Jobs Preview (Pré-Visualização de Tarefas Elásticas). Após receber a confirmação indicando que a sua subscrição foi ativada, deverá [transferir e instalar os cmdlets mais recentes das tarefas da versão de pré-lançamento](https://github.com/jaredmoo/azure-powershell/releases). Como é uma pré-visualização limitada, deve contactar SaaSFeedback@microsoft.com para questões relacionadas ou para suporte.*


## <a name="introduction-to-saas-schema-management-patterns"></a>Introdução aos padrões de Gestão de Esquemas SaaS

O padrão SaaS de inquilino individual por base de dados beneficia em muitos aspetos do isolamento de dados que daí resulta, mas ao mesmo tempo introduz a complexidade adicional de manter e gerir muitas bases de dados. As [Tarefas Elásticas](sql-database-elastic-jobs-overview.md) facilitam a administração e a gestão da camada de dados do SQL. As tarefas permitem-lhe, de forma segura e fiável, executar tarefas (scripts T-SQL) independentemente da interação ou intervenção do utilizador, em relação a um grupo de bases de dados. Este método pode ser utilizado para implementar o esquema e as alterações aos dados de referência comuns em todos os inquilinos numa aplicação. As tarefas elásticas também podem ser utilizadas para manter uma cópia *dourada* da base de dados utilizada para criar novos inquilinos ao assegurar que tem sempre o esquema e os dados de referências mais recentes.

![ecrã](media/sql-database-saas-tutorial-schema-management/schema-management.png)


## <a name="elastic-jobs-limited-preview"></a>Pré-visualização limitada das Tarefas Elásticas

Existe uma nova versão das Tarefas Elásticas, que agora é uma funcionalidade integrada da Base de Dados SQL do Azure (não requer nenhum componente ou serviço adicional). Esta nova versão das Tarefas Elásticas está atualmente em pré-visualização limitada. Esta pré-visualização limitada suporta atualmente o PowerShell para criar contas de tarefa e o T-SQL para criar e gerir tarefas.

> [!NOTE]
> *Este tutorial utiliza funcionalidades do serviço de Base de Dados SQL que estão numa pré-visualização limitada (tarefas da Base de Dados Elástica). Se pretender realizar este tutorial, forneça o ID da subscrição paraSaaSFeedback@microsoft.com com assunto=Elastic Jobs Preview (Pré-Visualização de Tarefas Elásticas). Após receber a confirmação indicando que a sua subscrição foi ativada, deverá [transferir e instalar os cmdlets mais recentes das tarefas da versão de pré-lançamento](https://github.com/jaredmoo/azure-powershell/releases). Como é uma pré-visualização limitada, deve contactar SaaSFeedback@microsoft.com para questões relacionadas ou para suporte.*

## <a name="get-the-wingtip-application-scripts"></a>Obter os scripts da aplicação Wingtip

Os scripts da Wingtip Tickets e o código fonte da aplicação estão disponíveis no repositório do github [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS). Os ficheiros dos scripts estão localizados na pasta [Módulos de Aprendizagem](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules). Transfira a pasta **Módulos de Aprendizagem** para o computador local, mantendo a estrutura das pastas.

## <a name="create-a-job-account-database-and-new-job-account"></a>Criar uma base de dados das contas de tarefa e uma nova conta de tarefa

Este tutorial requer a utilização do PowerShell para criar a base de dados das contas de tarefa e a conta de tarefa. Tal como o MSDB e o Agente SQL, as Tarefas Elásticas utilizam uma Base de Dados SQL do Azure para armazenar as definições de tarefa, o estado da tarefa e o histórico. Depois de criar a conta de tarefa, pode criar e monitorizar tarefas de imediato.

1. Abra ...\\Módulos de Aprendizagem\\Gestão de Esquemas\\*Demo-SchemaManagement.ps1* no **ISE do PowerShell**.
1. Prima **F5** para executar o script.

O script *Demo-SchemaManagement.ps1* chama o script *Deploy-SchemaManagement.ps1* para criar uma base de dados *S2* denominada **jobaccount** no servidor de catálogo. Em seguida, cria a conta de tarefa ao passar a base de dados jobaccount como um parâmetro para a chamada de criação da conta de tarefa.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>Criar uma tarefa para implementar novos dados de referência para todos os inquilinos

Cada base de dados de inquilinos inclui um conjunto de tipos de local que definem o tipo de eventos que são realizados num local. Neste exercício, vai implementar uma atualização para todas as bases de dados de inquilinos para adicionar dois tipos de local adicionais: *Corrida de Motos* e *Clube de Natação*. Estes tipos de local correspondem à imagem de fundo que vê na aplicação de eventos do inquilino.

Clique no menu pendente Tipo de Local e valide que apenas 10 opções de tipo de local estão disponíveis e, especificamente, que “Corrida de Motos” e “Clube de Natação” não estão incluídos na lista.

Agora, vamos criar uma tarefa para atualizar a tabela *VenueTypes* em todas as bases de dados de inquilinos e adicionar os novos tipos de local.

Para criar uma nova tarefa, vamos utilizar um conjunto de procedimentos armazenados do sistema e tarefas, criado na base de dados jobaccount quando foi criada a conta de tarefa.

1. Abra o SSMS e ligue-se ao servidor de catálogo: servidor catalog-\<utilizador\>.database.windows.net
1. Ligue-se também ao servidor de inquilinos: tenants1-\<utilizador\>.database.windows.net
1. Navegue para a base de dados *contosoconcerthall*, no servidor *tenants1*, e consulte a tabela *VenueTypes* para confirmar que *Corrida de Motos* e *Clube de Natação* **não estão** na lista de resultados.
1. Abra o ficheiro ...\\Módulos de Aprendizagem\\Gestão de Esquemas\\DeployReferenceData.sql
1. Modifique o \<utilizador\>, (utilize o nome de utilizador que usou quando implementou a aplicação WTP) em nas três localizações no script
1. Verifique se está ligado à base de dados jobaccount e prima **F5** para executar o script

* **sp\_add\_target\_group** cria o nome do grupo DemoServerGroup de destino. Agora, precisamos adicionar membros de destino.
* **sp\_add\_target\_group\_member** adiciona um tipo de membro destino de *servidor*, que considera todas as bases de dados dentro desse servidor (tenha em atenção que se trata do servidor customer1-&lt;WtpUser&gt; que contém as bases de dados de inquilinos) no momento em que a execução da tarefa deve ser incluída na tarefa. Em seguida, deve adicionar um tipo de membro destino de *base de dados*, especificamente a base de dados “dourada”, baseTenantDB, que reside no servidor catalog-&lt;WtpUser&gt;. E, por último, adicionar outro tipo de membro de grupo de destino de *base de dados* para incluir a base de dados adhocanalytics, utilizada num tutorial posterior.
* **sp\_add\_job** cria uma tarefa denominada “Implementação de Dados de Referência”
* **sp\_add\_jobstep** cria o passo de tarefa que contém o texto do comando T-SQL para atualizar a tabela de referência, VenueTypes
* As vistas restantes no script mostram a existência dos objetos e monitorizam a execução da tarefa. Consulte o valor de estado na coluna **ciclo de vida**. A tarefa foi concluída com êxito em todas as bases de dados de inquilinos e nas duas bases de dados adicionais que contêm a tabela de referência.

1. No SSMS, navegue para a base de dados *contosoconcerthall*, no servidor *tenants1*, e consulte a tabela *VenueTypes* para confirmar que *Corrida de Motos* e *Clube de Natação*  **estão** agora na lista de resultados.


## <a name="create-a-job-to-manage-the-reference-table-index"></a>Criar uma tarefa para gerir o índice de tabela de referência

À semelhança do exercício anterior, este exercício vai criar uma tarefa para criar o índice na chave primária da tabela de referência, uma operação de gestão da base de dados típica que um administrador poderá efetuar após uma elevada carga de dados numa tabela.

Crie uma tarefa com os mesmos procedimentos armazenados no “sistema” das tarefas.

1. Abra o SSMS e ligue-se ao servidor catalog-&lt;WtpUser&gt;.database.windows.net
1. Abra o ficheiro ...\\Módulos de Aprendizagem\\Gestão de Esquemas\\OnlineReindex.sql
1. Clique com o botão direito do rato, selecione Ligação e ligue-se ao servidor catalog-&lt;WtpUser&gt;.database.windows.net, se ainda não estiver ligado
1. Verifique se está ligado à base de dados jobaccount e prima F5 para executar o script

* sp\_add\_job cria uma nova tarefa denominada “Online Reindex PK\_\_VenueTyp\_\_265E44FD7FD4C885”
* sp\_add\_jobstep cria o passo da tarefa que contém texto do comando T-SQL para atualizar o índice




## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]

> * Criar uma conta de tarefa para consulta em vários inquilinos
> * Atualizar dados em todas as bases de dados de inquilinos
> * Criar um índice numa tabela em todas as bases de dados de inquilinos

[Tutorial de análises ad hoc](sql-database-saas-tutorial-adhoc-analytics.md)


## <a name="additional-resources"></a>Recursos adicionais

* [Tutoriais adicionais criados após a implementação inicial da aplicação Wingtip Tickets Platform (WTP)](sql-database-wtp-overview.md#sql-database-wtp-saas-tutorials)
* [Managing scaled-out cloud databases (Gerir bases de dados de escalamento horizontal na cloud)](sql-database-elastic-jobs-overview.md)
* [Create and manage scaled-out cloud databases (Criar e gerir bases de dados de escalamento horizontal na cloud)](sql-database-elastic-jobs-create-and-manage.md)
