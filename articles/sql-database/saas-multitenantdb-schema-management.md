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
ms.date: 11/14/2017
ms.author: billgib
ms.openlocfilehash: 346177be29ec196464f4f441858222ac5d5eb8c3
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="manage-schema-for-multiple-tenants-in-a-multi-tenant-application-that-uses-azure-sql-database"></a>Gerir o esquema para vários inquilinos numa aplicação multi-inquilino que utiliza a SQL Database do Azure

O [primeiro tutorial de base de dados do Wingtip bilhetes SaaS multi-inquilino](saas-multitenantdb-get-started-deploy.md) mostra como a aplicação pode aprovisionar uma base de dados em partição horizontal do multi-inquilino e registá-lo no catálogo. Como qualquer aplicação, a aplicação SaaS de bilhetes Wingtip será evoluir ao longo do tempo e, por vezes necessitarão alterações à base de dados. As alterações podem incluir o esquema novo ou alterado, os dados de referência novos ou alterados e as tarefas de manutenção de rotina da base de dados para garantir o desempenho ideal da aplicação. Com uma aplicação SaaS, estas alterações têm de ser implementadas de forma coordenada através de uma frota potencialmente massiva de bases de dados de inquilinos. Para estas alterações no futuro estar bases de dados do inquilino, necessitam de ser incorporadas no processo de aprovisionamento.

Este tutorial explora dois cenários – implementar atualizações de dados de referência para todos os inquilinos e devolver um índice na tabela que contém os dados de referência. O [as tarefas elásticas](sql-database-elastic-jobs-overview.md) funcionalidade é utilizada para executar estas operações em bases de dados do inquilino e o *dourada* base de dados do inquilino que é utilizado como um modelo para novas bases de dados.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]

> * Criar uma conta de tarefa
> * Consulta em vários inquilinos
> * Atualizar dados em todas as bases de dados de inquilinos
> * Criar um índice numa tabela em todas as bases de dados de inquilinos


Para concluir este tutorial, devem ser cumpridos os seguintes pré-requisitos:

* A aplicação de base de dados do Wingtip bilhetes SaaS multi-inquilino for implementada. Para implementar em menos de cinco minutos, consulte [implementar e explorar a aplicação de base de dados do Wingtip bilhetes SaaS multi-inquilino](saas-multitenantdb-get-started-deploy.md)
* O Azure PowerShell está instalado. Para obter mais detalhes, veja [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* Está instalada a versão mais recente do SQL Server Management Studio (SSMS). [Transferir e instalar o SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)

> [!NOTE]
> *Este tutorial utiliza funcionalidades do serviço de Base de Dados SQL que estão numa pré-visualização limitada (tarefas da Base de Dados Elástica). Se pretender realizar este tutorial, forneça o ID da subscrição paraSaaSFeedback@microsoft.com com assunto=Elastic Jobs Preview (Pré-Visualização de Tarefas Elásticas). Após receber a confirmação indicando que a sua subscrição foi ativada, deverá [transferir e instalar os cmdlets mais recentes das tarefas da versão de pré-lançamento](https://github.com/jaredmoo/azure-powershell/releases). Esta pré-visualização é limitada, por isso, contacte SaaSFeedback@microsoft.com para suporte ou questões relacionados.*


## <a name="introduction-to-saas-schema-management-patterns"></a>Introdução aos padrões de Gestão de Esquemas SaaS

O modelo da base de dados do multi-inquilino utilizado neste exemplo permite que uma base de dados de inquilinos conter qualquer número de inquilinos. Este exemplo explicar o potencial de utilizar uma combinação de um multi-inquilino e de inquilino único bases de dados, permitindo um modelo de gestão do inquilino 'híbrida'. Manter e a gestão destas bases de dados é mais complicada. As [Tarefas Elásticas](sql-database-elastic-jobs-overview.md) facilitam a administração e a gestão da camada de dados do SQL. As tarefas permitem-lhe, de forma segura e fiável, executar tarefas (scripts T-SQL) independentemente da interação ou intervenção do utilizador, em relação a um grupo de bases de dados. Este método pode ser utilizado para implementar o esquema e as alterações aos dados de referência comuns em todos os inquilinos numa aplicação. As tarefas elásticas também podem ser utilizadas para manter uma cópia *dourada* da base de dados utilizada para criar novos inquilinos ao assegurar que tem sempre o esquema e os dados de referências mais recentes.

![ecrã](media/saas-multitenantdb-schema-management/schema-management.png)

## <a name="elastic-jobs-limited-preview"></a>Pré-visualização limitada das Tarefas Elásticas

Existe uma nova versão das Tarefas Elásticas, que agora é uma funcionalidade integrada da Base de Dados SQL do Azure (não requer nenhum componente ou serviço adicional). Esta nova versão das Tarefas Elásticas está atualmente em pré-visualização limitada. Esta pré-visualização limitada suporta atualmente o PowerShell para criar contas de tarefa e o T-SQL para criar e gerir tarefas.

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-scripts"></a>Obter os scripts de aplicação de base de dados do Wingtip bilhetes SaaS multi-inquilino

Os scripts de base de dados do Wingtip bilhetes SaaS multi-inquilino e o código fonte da aplicação, estão disponíveis no [WingtipTicketsSaaS MultiTenantDB](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB) repositório do GitHub. <!-- [Steps to download the Wingtip Tickets SaaS Multi-tenant Database scripts](saas-multitenantdb-wingtip-app-guidance-tips.md#download-and-unblock-the-wingtip-saas-scripts)-->

## <a name="create-a-job-account-database-and-new-job-account"></a>Criar uma base de dados das contas de tarefa e uma nova conta de tarefa

Este tutorial requer a utilização do PowerShell para criar a base de dados das contas de tarefa e a conta de tarefa. Tal como o MSDB e o Agente SQL, as Tarefas Elásticas utilizam uma Base de Dados SQL do Azure para armazenar as definições de tarefa, o estado da tarefa e o histórico. Depois de criar a conta de tarefa, pode criar e monitorizar tarefas de imediato.

1. No **ISE do PowerShell**, abra *... \\Learning módulos\\esquema gestão\\demonstração SchemaManagement.ps1*.
1. Prima **F5** para executar o script.

O script *Demo-SchemaManagement.ps1* chama o script *Deploy-SchemaManagement.ps1* para criar uma base de dados *S2* denominada **jobaccount** no servidor de catálogo. Em seguida, cria a conta de tarefa ao passar a base de dados jobaccount como um parâmetro para a chamada de criação da conta de tarefa.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>Criar uma tarefa para implementar novos dados de referência para todos os inquilinos

Cada base de dados de inquilinos inclui um conjunto de tipos de venue na tabela **VenueTypes** que define o tipo de eventos que estão alojados num venue. Neste exercício, implementar uma atualização para todas as bases de dados para adicionar dois tipos de venue adicionais: *da sua motocicleta Racing* e *Swimming Club*. Estes tipos de local correspondem à imagem de fundo que vê na aplicação de eventos do inquilino.

Clique no menu pendente Tipo de Local e valide que apenas 10 opções de tipo de local estão disponíveis e, especificamente, que “Corrida de Motos” e “Clube de Natação” não estão incluídos na lista.

Agora vamos criar uma tarefa para atualizar o **VenueTypes** uma tabela em todos os inquilinos bases de dados e adicionar os novos tipos de venue.

Para criar uma nova tarefa, vamos utilizar um conjunto de procedimentos armazenados do sistema e tarefas, criado na base de dados jobaccount quando foi criada a conta de tarefa.

1. No SSMS, ligue ao servidor do inquilino: tenants1-mt -\<utilizador\>. database.windows.net
2. Navegue para o *tenants1* da base de dados no *tenants1-mt -\<utilizador\>. database.windows.net* servidor e consulta o *VenueTypes* tabela Confirme que *da sua motocicleta Racing* e *Swimming Club* são **não** na lista de resultados.
3. Ligar ao servidor de catálogo: catálogo-mt -\<utilizador\>. database.windows.net
4. Ligar à base de dados jobaccount no servidor de catálogo.
5. No SSMS, abra o ficheiro... \\Learning módulos\\esquema gestão\\DeployReferenceData.sql
6. Modificar a instrução: definir @User = &lt;utilizador&gt; e substitua o valor de utilizador utilizado quando implementou a aplicação de base de dados do Wingtip bilhetes SaaS multi-inquilino.
7. Prima **F5** para executar o script.

    * **SP\_adicionar\_destino\_grupo** cria o grupo de destino agora nome DemoServerGroup, adicionar os membros de destino ao grupo.
    * **SP\_adicionar\_destino\_grupo\_membro** adiciona um *servidor* tipo de membro, considere todas as bases de dados dentro desse servidor de destino (tenha em atenção de que este é o tenants1 - mt - &lt;utilizador&gt; servidor que contém a base de dados de inquilinos) no momento de execução da tarefa a ser incluído na tarefa, um *base de dados* como alvo o tipo de membro, 'dourada' da base de dados (basetenantdb) que reside no catálogo-mt -&lt;utilizador&gt; servidor e, por último um *base de dados* como alvo o tipo de membro para incluir a base de dados de adhocreporting que é utilizado um tutorial posterior.
    * **SP\_adicionar\_tarefa** cria uma tarefa denominada "Implementação de dados de referência".
    * **SP\_adicionar\_passo** cria o passo de tarefa que contém o texto do comando T-SQL ao atualizar a tabela de referência, VenueTypes.
    * As vistas restantes no script mostram a existência dos objetos e monitorizam a execução da tarefa. Utilize estas consultas para consultar o valor de estado no **ciclo de vida** coluna para determinar quando a tarefa tem foi concluído com êxito na base de dados de inquilinos e duas bases de dados adicionais que contém a tabela de referência.

1. No SSMS, navegue para a base de dados do inquilino no *tenants1-mt -&lt;utilizador&gt;*  servidor e consulta o *VenueTypes* tabela para confirmar que *da sua motocicleta Racing* e *Swimming Club* estão agora **adicionado* à tabela.


## <a name="create-a-job-to-manage-the-reference-table-index"></a>Criar uma tarefa para gerir o índice de tabela de referência

À semelhança do exercício anterior, este exercício vai criar uma tarefa para criar o índice na chave primária da tabela de referência, uma operação de gestão da base de dados típica que um administrador poderá efetuar após uma elevada carga de dados numa tabela.


1. No SSMS, estabeleça ligação à base de dados de jobaccount no catálogo-mt -&lt;utilizador&gt;. database.windows.net servidor.
2. No SSMS, abra... \\Learning módulos\\esquema gestão\\OnlineReindex.sql.
3. Prima **F5** para executar o script

    * **SP\_adicionar\_tarefa** cria uma nova tarefa denominada "Online PK reindexar\_\_VenueTyp\_\_265E44FD7FD4C885".
    * **SP\_adicionar\_passo** cria o passo de tarefa que contém o texto de comando T-SQL para atualizar o índice.
    * As vistas restantes no script de monitorizar a execução da tarefa. Utilize estas consultas para consultar o valor de estado no **ciclo de vida** coluna para determinar quando a tarefa tem foi concluído com êxito em todos os membros do grupo de destino.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]

> * Criar uma conta de tarefa para consulta em vários inquilinos
> * Atualizar dados em todas as bases de dados de inquilinos
> * Criar um índice numa tabela em todas as bases de dados de inquilinos

[Tutorial de análises ad hoc](saas-multitenantdb-adhoc-reporting.md)


## <a name="additional-resources"></a>Recursos adicionais

<!--* Additional tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application deployment (*Tutorial link to come*)
(saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
* [Managing scaled-out cloud databases (Gerir bases de dados de escalamento horizontal na cloud)](sql-database-elastic-jobs-overview.md)
* [Create and manage scaled-out cloud databases (Criar e gerir bases de dados de escalamento horizontal na cloud)](sql-database-elastic-jobs-create-and-manage.md)
