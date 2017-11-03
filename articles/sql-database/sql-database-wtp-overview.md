---
title: "Exemplo de aplicação multi-inquilino de base de dados SQL do Azure - Wingtip SaaS | Microsoft Docs"
description: "Aprender utilizando um exemplo de aplicação de multi-inquilino que utiliza o SQL Database do Azure, o exemplo de Wingtip SaaS"
keywords: tutorial de base de dados sql
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: sstein
ms.openlocfilehash: 46c9a3eadc2c23959b4d08649c6c0215d44b493e
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="introduction-to-a-sql-database-multi-tenant-saas-app-example"></a>Introdução a um exemplo de aplicação de SaaS de multi-inquilino de base de dados SQL

O *Wingtip SaaS* aplicação é uma aplicação de multi-inquilino de exemplo que demonstra vantagens exclusivas da base de dados SQL. A aplicação utiliza uma base de dados por inquilino, o padrão da aplicação SaaS, para servir vários inquilinos. A aplicação foi concebida para demonstram as funcionalidades da SQL Database do Azure que ativar cenários de SaaS, incluindo vários padrões de conceção e gestão de SaaS. Para obter rapidamente e em execução, a aplicação Wingtip SaaS implementa em menos de cinco minutos!

Scripts de gestão e código de origem de aplicação estão disponíveis no [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS) repositório do github. Para executar os scripts, [transferir a pasta de módulos de aprendizagem](#download-and-unblock-the-wingtip-saas-scripts) para o seu computador local.

## <a name="sql-database-wingtip-saas-tutorials"></a>Tutoriais do SQL Server da base de dados Wingtip SaaS

Depois de implementar a aplicação, explore os tutoriais seguintes que criar após a implementação inicial. Estes tutoriais explorar padrões comuns da SaaS que tirar partido das funcionalidades integradas de base de dados SQL, o SQL Data Warehouse e outros serviços do Azure. Tutoriais incluem scripts do PowerShell, com explicações detalhadas que simplificar significativamente a compreender e implementar os padrões de gestão de SaaS mesmos nas suas aplicações.


| Tutorial | Descrição |
|:--|:--|
|[Implementar e explorar a aplicação Wingtip SaaS](sql-database-saas-tutorial.md)| **COMECE POR AQUI!** Implementar e explorar a aplicação Wingtip SaaS à sua subscrição do Azure. |
|[Aprovisionamento e o catálogo de inquilinos](sql-database-saas-tutorial-provision-and-catalog.md)| Saiba como a aplicação se liga ao utilizar uma base de dados do catálogo de inquilinos e como o catálogo de mapas de inquilinos para os seus dados. |
|[Monitorizar e gerir o desempenho](sql-database-saas-tutorial-performance-monitoring.md)| Saiba como utilizar funcionalidades de monitorização da base de dados do SQL Server e como configurar alertas quando os limiares de desempenho for excedidos. |
|[Monitor com a análise de registos (OMS)](sql-database-saas-tutorial-log-analytics.md) | Saiba como utilizar [Log Analytics](../log-analytics/log-analytics-overview.md) monitorizar grandes quantidades de recursos, entre vários conjuntos. |
|[Restaurar um inquilino único](sql-database-saas-tutorial-restore-single-tenant.md)| Saiba como restaurar uma base de dados do inquilino para um ponto anterior no tempo. Passos para restaurar uma base de dados paralelas, deixando a base de dados existente do inquilino online, também são incluídos. |
|[Gerir o esquema de inquilino](sql-database-saas-tutorial-schema-management.md)| Saiba como atualizar o esquema e atualizar os dados de referência, em todos os inquilinos Wingtip SaaS. |
|[Executar a análise de ad-hoc](sql-database-saas-tutorial-adhoc-analytics.md) | Criar uma base de dados de análise do ad-hoc e executar consultas distribuídas em tempo real em todos os inquilinos.  |
|[Executar a análise de inquilino](sql-database-saas-tutorial-tenant-analytics.md) | Extrair dados do inquilino para um armazém de dados ou base de dados de análise para executar consultas de análise offline. |



## <a name="application-architecture"></a>Arquitetura da aplicação

A aplicação Wingtip SaaS utiliza o modelo de base de dados por inquilino e utiliza conjuntos elásticos SQL para maximizar a eficiência. Para o aprovisionamento e os inquilinos de mapeamento para os seus dados, é utilizada uma base de dados do catálogo. As principais aplicação Wingtip SaaS, utiliza um conjunto com três inquilinos de exemplo, para além da base de dados do catálogo. Concluir muitas do SaaS Wingtip tutoriais resultam em suplementos para a implementação iniciais existentes, introduzindo as bases de dados de análise, entre bases de dados gestão de esquema, etc.


![Arquitetura de SaaS Wingtip](media/sql-database-wtp-overview/app-architecture.png)


Ao percorrer os tutoriais e trabalhar com a aplicação, é importante focar-se nos padrões de SaaS, como se relacionam com a camada de dados. Por outras palavras, concentre-se na camada de dados e não em analisar em excesso a aplicação em si. Noções sobre a implementação destas SaaS padrões é a chave para implementar estes padrões nas suas aplicações, ao considerar quaisquer modificações necessárias para necessidades comerciais específicas.

## <a name="download-and-unblock-the-wingtip-saas-scripts"></a>Transferir e desbloquear os scripts de Wingtip SaaS

Conteúdo executável (scripts, dlls) pode estar bloqueado pelo Windows quando zip ficheiros são transferidos a partir de uma origem externa e extraiu. Quando extrair os scripts a partir de um ficheiro zip ***siga os passos abaixo para desbloquear o ficheiro. zip antes de a extrair***. Isto garante que os scripts estão autorizados a executar.

1. Navegue até à [o repositório do github Wingtip SaaS](https://github.com/Microsoft/WingtipSaaS).
1. Clique em **clonar ou transferir**.
1. Clique em **transferir ZIP** e guarde o ficheiro.
1. Clique com botão direito do **WingtipSaaS master.zip** do ficheiro e selecione **propriedades**.
1. No **geral** separador, selecione **desbloqueio**.
1. Clique em **OK**.
1. Extraia os ficheiros.

Scripts estão localizados no *... \\WingtipSaaS-master\\Learning módulos* pasta.


## <a name="working-with-the-wingtip-saas-powershell-scripts"></a>Trabalhar com Scripts do PowerShell de SaaS Wingtip

Para tirar o máximo partido do exemplo tem de aprofundar os scripts fornecidos. Utilizar pontos de interrupção e percorra os scripts, examinando os detalhes de como os diferentes padrões de SaaS são implementados. Passo facilmente através de scripts fornecidos e módulos para a melhor compreensão, recomendamos que utilize o [ISE do PowerShell](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise).

### <a name="update-the-configuration-file-for-your-deployment"></a>Atualizar o ficheiro de configuração para a implementação

Editar o **UserConfig.psm1** ficheiro com o valor de utilizador e grupo de recursos que definiu durante a implementação:

1. Abra o *ISE do PowerShell* e carregar... \\Learning módulos\\*UserConfig.psm1* 
1. Atualização *ResourceGroupName* e *nome* com os valores específicos para a sua implementação (em linhas 10 e 11 apenas).
1. Guarde as alterações!

Definir estes valores aqui simplesmente evita que tenha que que atualizar estes valores específicos da implementação em cada script.

### <a name="execute-scripts-by-pressing-f5"></a>Executar Scripts ao premir F5

Utilizam scripts várias *$PSScriptRoot* para navegar pastas, e *$PSScriptRoot* é avaliada apenas quando scripts são executados ao premir **F5**.  Realce e em execução uma seleção (**F8**) pode resultar em erros, por isso, prima **F5** quando executar scripts.

### <a name="step-through-the-scripts-to-examine-the-implementation"></a>Analisar os scripts para examinar a implementação

É a melhor forma de compreender os scripts pelo próximo através de-los para ver o que fazer. Veja o incluídos **demonstração -** scripts que apresentam uma fácil de seguir o fluxo de trabalho de alto nível. O **demonstração -** scripts mostram os passos necessários para realizar cada tarefa, por isso, configurar pontos de interrupção e desagregar mais aprofundada para as chamadas para ver detalhes de implementação para os diferentes padrões de SaaS individuais.

Sugestões para explorar e avance mediante scripts do PowerShell:

* Abra **demonstração -** scripts no ISE do PowerShell.
* Executar ou continuar com **F5** (utilizando **F8** não é aconselhada porque *$PSScriptRoot* não é avaliada quando em execução seleções de um script).
* Para colocar os pontos de interrupção, clique ou selecione uma linha e prima **F9**.
* Para executar o próximo passo numa chamada de função ou script, utilize **F10**.
* Para avançar para uma chamada de função ou script, utilize **F11**.
* Para ir até ao fim do procedimento da chamada de função ou script atual, utilize **Shift + F11**.


## <a name="explore-database-schema-and-execute-sql-queries-using-ssms"></a>Explorar o esquema de base de dados e executar consultas SQL com o SSMS

Utilize [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) para ligar e procurar a servidores de aplicações e bases de dados.

A implementação inicialmente tem dois servidores de base de dados do SQL Server para ligar ao - o *tenants1 -&lt;utilizador&gt;*  servidor e o *catálogo -&lt;utilizador&gt;*  servidor. Para garantir uma ligação de demonstração com êxito, ambos os servidores têm um [regra de firewall](sql-database-firewall-configure.md) permitir que todos os IPs através de.


1. Abra o *SSMS* e ligue-se ao servidor *tenants1-&lt;Utilizador&gt;.database.windows.net*.
1. Clique em **Ligar** > **Motor de Base de Dados...**:

   ![servidor de catálogo](media/sql-database-wtp-overview/connect.png)

1. As credenciais de demonstração são: Início de sessão = *developer*, Palavra-passe = *P@ssword1*

   ![ligação](media\sql-database-wtp-overview\tenants1-connect.png)

1. Repita os passos 2 e 3 e ligue-se ao servidor *catalog-&lt;Utilizador&gt;.database.windows.net*.

Após estabelecer ligação, deverá poder ver os dois servidores. A lista de bases de dados poderá ser diferente, consoante os inquilinos que já aprovisionada:

![explorador de objetos](media/sql-database-wtp-overview/object-explorer.png)



## <a name="next-steps"></a>Passos seguintes

[Implementar a aplicação Wingtip SaaS](sql-database-saas-tutorial.md)
