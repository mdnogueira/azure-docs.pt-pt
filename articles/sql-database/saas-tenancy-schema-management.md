---
title: "Gerir o esquema da Base de Dados SQL numa aplicação multi-inquilino | Microsoft Docs"
description: "Gerir o Esquema para vários inquilinos numa aplicação multi-inquilino que utiliza a Base de Dados SQL do Azure"
keywords: tutorial de base de dados sql
services: sql-database
documentationcenter: 
author: stevestein
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/28/2017
ms.author: billgib; sstein
ms.openlocfilehash: c3eaa4d490b61b746e427d2fe2640ae5cdd6032c
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="manage-schema-for-multiple-tenants-in-a-multi-tenant-application-that-uses-azure-sql-database"></a>Gerir o esquema para vários inquilinos numa aplicação multi-inquilino que utiliza a SQL Database do Azure

O [primeiro tutorial Wingtip bilhetes SaaS da base de dados por inquilino](saas-dbpertenant-get-started-deploy.md) mostra como a aplicação pode aprovisionar uma base de dados do inquilino e registá-lo no catálogo. Como qualquer aplicação, a aplicação de Wingtip bilhetes SaaS da base de dados por inquilino serão evoluir ao longo do tempo e, por vezes necessitarão alterações à base de dados. As alterações podem incluir o esquema novo ou alterado, os dados de referência novos ou alterados e as tarefas de manutenção de rotina da base de dados para garantir o desempenho ideal da aplicação. Com uma aplicação SaaS, estas alterações têm de ser implementadas de forma coordenada através de uma frota potencialmente massiva de bases de dados de inquilinos. Para estas alterações no futuro estar bases de dados do inquilino, necessitam de ser incorporadas no processo de aprovisionamento.

Este tutorial explora dois cenários – implementar atualizações de dados de referência para todos os inquilinos e devolver um índice na tabela que contém os dados de referência. O [as tarefas elásticas](sql-database-elastic-jobs-overview.md) funcionalidade é utilizada para executar estas operações em todos os inquilinos e o *dourada* base de dados do inquilino que é utilizado como um modelo para novas bases de dados.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]

> * Criar uma conta de tarefa
> * Consulta em vários inquilinos
> * Atualizar dados em todas as bases de dados de inquilinos
> * Criar um índice numa tabela em todas as bases de dados de inquilinos


Para concluir este tutorial, devem ser cumpridos os seguintes pré-requisitos:

* A aplicação de Wingtip bilhetes SaaS da base de dados por inquilino é implementada. Para implementar em menos de cinco minutos, consulte [implementar e explorar a aplicação de Wingtip bilhetes SaaS da base de dados por inquilino](saas-dbpertenant-get-started-deploy.md)
* O Azure PowerShell está instalado. Para obter mais detalhes, veja [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* Está instalada a versão mais recente do SQL Server Management Studio (SSMS). [Transferir e instalar o SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)

> [!NOTE]
> Este tutorial utiliza funcionalidades do serviço base de dados do SQL Server que estão numa (tarefas de bases de dados elásticas) de pré-visualização limitada. Se pretender realizar este tutorial, forneça o ID da subscrição paraSaaSFeedback@microsoft.com com assunto=Elastic Jobs Preview (Pré-Visualização de Tarefas Elásticas). Após receber a confirmação indicando que a sua subscrição foi ativada, deverá [transferir e instalar os cmdlets mais recentes das tarefas da versão de pré-lançamento](https://github.com/jaredmoo/azure-powershell/releases). Esta pré-visualização é limitada, por isso, contacte SaaSFeedback@microsoft.com para suporte ou questões relacionados.

## <a name="introduction-to-saas-schema-management-patterns"></a>Introdução aos padrões de gestão do esquema de SaaS

O padrão SaaS de inquilino individual por base de dados beneficia em muitos aspetos do isolamento de dados que daí resulta, mas ao mesmo tempo introduz a complexidade adicional de manter e gerir muitas bases de dados. As [Tarefas Elásticas](sql-database-elastic-jobs-overview.md) facilitam a administração e a gestão da camada de dados do SQL. As tarefas permitem-lhe, de forma segura e fiável, executar tarefas (scripts T-SQL) independentemente da interação ou intervenção do utilizador, em relação a um grupo de bases de dados. Este método pode ser utilizado para implementar o esquema e as alterações aos dados de referência comuns em todos os inquilinos numa aplicação. As tarefas elásticas também podem ser utilizadas para manter uma cópia *dourada* da base de dados utilizada para criar novos inquilinos ao assegurar que tem sempre o esquema e os dados de referências mais recentes.

![ecrã](media/saas-tenancy-schema-management/schema-management.png)


## <a name="elastic-jobs-limited-preview"></a>Pré-visualização limitada das Tarefas Elásticas

Existe uma nova versão das Tarefas Elásticas, que agora é uma funcionalidade integrada da Base de Dados SQL do Azure (não requer nenhum componente ou serviço adicional). Esta nova versão das Tarefas Elásticas está atualmente em pré-visualização limitada. Esta pré-visualização limitada suporta atualmente o PowerShell para criar contas de tarefa e o T-SQL para criar e gerir tarefas.

> [!NOTE]
> Este tutorial utiliza funcionalidades do serviço base de dados do SQL Server que estão numa (tarefas de bases de dados elásticas) de pré-visualização limitada. Se pretender realizar este tutorial, forneça o ID da subscrição paraSaaSFeedback@microsoft.com com assunto=Elastic Jobs Preview (Pré-Visualização de Tarefas Elásticas). Após receber a confirmação indicando que a sua subscrição foi ativada, deverá [transferir e instalar os cmdlets mais recentes das tarefas da versão de pré-lançamento](https://github.com/jaredmoo/azure-powershell/releases). Esta pré-visualização é limitada, por isso, contacte SaaSFeedback@microsoft.com para suporte ou questões relacionados.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Obter os scripts de aplicação Wingtip bilhetes SaaS da base de dados por inquilino

Os scripts de base de dados do Wingtip bilhetes SaaS multi-inquilino e o código fonte da aplicação, estão disponíveis no [WingtipTicketsSaaS DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) repositório do GitHub. Veja o [orientações gerais](saas-tenancy-wingtip-app-guidance-tips.md) para obter os passos transferir e os scripts de Wingtip SaaS de pedidos de desbloqueio.

## <a name="create-a-job-account-database-and-new-job-account"></a>Criar uma base de dados das contas de tarefa e uma nova conta de tarefa

Este tutorial requer a utilização do PowerShell para criar a base de dados das contas de tarefa e a conta de tarefa. Tal como o MSDB e o Agente SQL, as Tarefas Elásticas utilizam uma Base de Dados SQL do Azure para armazenar as definições de tarefa, o estado da tarefa e o histórico. Depois de criar a conta de tarefa, pode criar e monitorizar tarefas de imediato.

1. **No ISE do PowerShell**, abra... \\Learning módulos\\esquema gestão\\*demonstração SchemaManagement.ps1*.
1. Prima **F5** para executar o script.

O script *Demo-SchemaManagement.ps1* chama o script *Deploy-SchemaManagement.ps1* para criar uma base de dados *S2* denominada **jobaccount** no servidor de catálogo. Em seguida, cria a conta de tarefa ao passar a base de dados jobaccount como um parâmetro para a chamada de criação da conta de tarefa.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>Criar uma tarefa para implementar novos dados de referência para todos os inquilinos

Cada base de dados de inquilinos inclui um conjunto de tipos de local que definem o tipo de eventos que são realizados num local. Neste exercício, vai implementar uma atualização para todas as bases de dados de inquilinos para adicionar dois tipos de local adicionais: *Corrida de Motos* e *Clube de Natação*. Estes tipos de local correspondem à imagem de fundo que vê na aplicação de eventos do inquilino.

Clique no menu pendente Tipo de Local e valide que apenas 10 opções de tipo de local estão disponíveis e, especificamente, que “Corrida de Motos” e “Clube de Natação” não estão incluídos na lista.

Agora, vamos criar uma tarefa para atualizar a tabela *VenueTypes* em todas as bases de dados de inquilinos e adicionar os novos tipos de local.

Para criar uma nova tarefa, vamos utilizar um conjunto de procedimentos armazenados do sistema e tarefas, criado na base de dados jobaccount quando foi criada a conta de tarefa.

1. Abra o SSMS e ligar ao servidor de catálogo: catálogo-dpt -\<utilizador\>. database.windows.net servidor
1. Também ligar ao servidor do inquilino: tenants1-dpt -\<utilizador\>. database.windows.net
1. Navegue para o *contosoconcerthall* da base de dados no *tenants1-dpt -\<utilizador\>*  servidor e consulta o *VenueTypes* tabela para confirmar que *Da sua motocicleta Racing* e *Swimming Club* **não são** na lista de resultados.
1. No SSMS, abra o ficheiro... \\Learning módulos\\esquema gestão\\DeployReferenceData.sql
1. Modificar a instrução: definir @wtpUser = &lt;utilizador&gt; e substitua o valor de utilizador utilizado quando implementou a aplicação de Wingtip bilhetes SaaS da base de dados por inquilino
1. Verifique se está ligado à base de dados jobaccount e prima **F5** para executar o script

Observar as seguintes o *DeployReferenceData.sql* script:
* **sp\_add\_target\_group** cria o nome do grupo DemoServerGroup de destino. Agora, precisamos adicionar membros de destino.
* **SP\_adicionar\_destino\_grupo\_membro** adiciona um *servidor* tipo de membro, considere todas as bases de dados dentro desse servidor de destino (tenha em atenção de que este é o tenants1 - dpt - &lt;Utilizador&gt; servidor que contém as bases de dados do inquilino) no momento da tarefa de execução deve ser incluída na tarefa, o segundo é adicionar uma *base de dados* tipo de membro, especificamente o 'dourada' de destino base de dados (basetenantdb) que reside no catálogo-dpt -&lt;utilizador&gt; servidor e por último outro *base de dados* tipo de membro de grupo para incluir a base de dados de adhocanalytics que é utilizada na posterior de destino Tutorial.
* **sp\_add\_job** cria uma tarefa denominada “Implementação de Dados de Referência”
* **SP\_adicionar\_passo** cria o passo de tarefa que contém o texto do comando T-SQL ao atualizar a tabela de referência, VenueTypes
* As vistas restantes no script mostram a existência dos objetos e monitorizam a execução da tarefa. Utilize estas consultas para consultar o valor de estado no **ciclo de vida** coluna para determinar quando a tarefa tem foi concluído com êxito em todas as bases de dados do inquilino e duas bases de dados adicionais que contém a tabela de referência.

No SSMS, navegue para o *contosoconcerthall* da base de dados no *tenants1-dpt -\<utilizador\>*  servidor e consulta o *VenueTypes* tabela Confirme que *da sua motocicleta Racing* e *Swimming Club* **são** agora na lista de resultados.


## <a name="create-a-job-to-manage-the-reference-table-index"></a>Criar uma tarefa para gerir o índice de tabela de referência

À semelhança do exercício anterior, este exercício vai criar uma tarefa para criar o índice na chave primária da tabela de referência, uma operação de gestão da base de dados típica que um administrador poderá efetuar após uma elevada carga de dados numa tabela.

Crie uma tarefa com os mesmos procedimentos armazenados no “sistema” das tarefas.

1. Abra o SSMS e ligar ao catálogo de-dpt -&lt;utilizador&gt;. database.windows.net servidor
1. Abra o ficheiro ...\\Módulos de Aprendizagem\\Gestão de Esquemas\\OnlineReindex.sql
1. Clique com o botão direito, selecione a ligação e ligar ao catálogo de-dpt -&lt;utilizador&gt;. database.windows.net servidor, se ainda não estiver ligado
1. Verifique se está ligado à base de dados jobaccount e prima F5 para executar o script

Observar as seguintes o *OnlineReindex.sql* script:
* **SP\_adicionar\_tarefa** cria uma nova tarefa denominada "Online PK reindexar\_\_VenueTyp\_\_265E44FD7FD4C885"
* **SP\_adicionar\_passo** cria o passo de tarefa que contém o texto de comando T-SQL para atualizar o índice
* As vistas restantes no script de monitorizar a execução da tarefa. Utilize estas consultas para consultar o valor de estado no **ciclo de vida** coluna para determinar quando a tarefa tem foi concluído com êxito em todos os membros do grupo de destino.



## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]

> * Criar uma conta de tarefa para consulta em vários inquilinos
> * Atualizar dados em todas as bases de dados de inquilinos
> * Criar um índice numa tabela em todas as bases de dados de inquilinos

Em seguida, tente o [tutorial relatórios Ad-hoc](saas-tenancy-adhoc-analytics.md).


## <a name="additional-resources"></a>Recursos adicionais

* [Tutoriais adicionais que criar após a implementação da aplicação Wingtip bilhetes SaaS da base de dados por inquilino](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Managing scaled-out cloud databases (Gerir bases de dados de escalamento horizontal na cloud)](sql-database-elastic-jobs-overview.md)
* [Create and manage scaled-out cloud databases (Criar e gerir bases de dados de escalamento horizontal na cloud)](sql-database-elastic-jobs-create-and-manage.md)