---
title: "Orientações para o exemplo de aplicação multi-inquilino de base de dados SQL - Wingtip SaaS | Microsoft Docs"
description: "Fornece os passos e as orientações para instalar e executar a aplicação multi-inquilino de exemplo que utiliza o SQL Database do Azure, o exemplo de Wingtip bilhetes SaaS."
keywords: tutorial de base de dados sql
services: sql-database
author: MightyPen
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/20/2017
ms.author: genemi
ms.openlocfilehash: ebf1c4948816746fa02b867d1ea66afc64b882b0
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2017
---
# <a name="general-guidance-for-working-with-wingtip-tickets-sample-saas-apps"></a>Orientações gerais para trabalhar com Wingtip bilhetes de exemplo aplicações SaaS

Este artigo contém orientações gerais para executar as aplicações de SaaS de exemplo Wingtip pedidos que utilizam a SQL Database do Azure. 

## <a name="download-and-unblock-the-wingtip-tickets-saas-scripts"></a>Transferir e os scripts de Wingtip SaaS de pedidos de desbloqueio

Conteúdo executável (scripts, dlls) pode estar bloqueado pelo Windows quando zip ficheiros são transferidos a partir de uma origem externa e extraiu. Quando extrair os scripts a partir de um ficheiro zip **siga os passos abaixo para desbloquear o ficheiro. zip antes de a extrair**. Isto garante que os scripts estão autorizados a executar.

1. Navegue para o repositório do GitHub de SaaS de bilhetes Wingtip para o padrão de inquilinos de base de dados que pretende explorar: 
    - [WingtipTicketsSaaS StandaloneApp](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp)
    - [WingtipTicketsSaaS DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant)
    - [WingtipTicketsSaaS MultiTenantDb](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb)
2. Clique em **clonar ou transferir**.
3. Clique em **transferir zip** e guarde o ficheiro.
4. O ficheiro zip com o botão direito e selecione **propriedades**. O nome do ficheiro zip irá corresponder ao nome do repositório. (ex. _WingtipTicketsSaaS-DbPerTenant-master.zip_)
5. No **geral** separador, selecione **desbloqueio**.
6. Clique em **OK**.
7. Extraia os ficheiros.

Scripts estão localizados no *... \\Learning módulos* pasta.


## <a name="working-with-the-wingtip-tickets-powershell-scripts"></a>Trabalhar com os scripts do PowerShell de bilhetes Wingtip

Para tirar o máximo partido do exemplo tem de aprofundar os scripts fornecidos. Utilizar pontos de interrupção e percorra os scripts, tal como executar e examine a forma como os diferentes padrões de SaaS são implementados. Passo facilmente através de scripts fornecidos e módulos para a melhor compreensão, recomendamos que utilize o [ISE do PowerShell](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise).

### <a name="update-the-configuration-file-for-your-deployment"></a>Atualizar o ficheiro de configuração para a implementação

Editar o **UserConfig.psm1** ficheiro com o valor de utilizador e grupo de recursos que definiu durante a implementação:

1. Abra o *ISE do PowerShell* e carregar... \\Learning módulos\\*UserConfig.psm1* 
2. Atualização *ResourceGroupName* e *nome* com os valores específicos para a sua implementação (em linhas 10 e 11 apenas).
3. Guarde as alterações!

Definir estes valores aqui simplesmente evita que tenha que que atualizar estes valores específicos da implementação em cada script.

### <a name="execute-the-scripts-by-pressing-f5"></a>Executar scripts ao premir F5

Utilizam scripts várias *$PSScriptRoot* para navegar pastas, e *$PSScriptRoot* é avaliada apenas quando scripts são executados ao premir **F5**.  Realce e em execução uma seleção (**F8**) pode resultar em erros, por isso, prima **F5** quando executar scripts.

### <a name="step-through-the-scripts-to-examine-the-implementation"></a>Analisar os scripts para examinar a implementação

É a melhor forma de compreender os scripts pelo próximo através de-los para ver o que fazer. Veja o incluídos **demonstração -** scripts que apresentam uma fácil de seguir o fluxo de trabalho de alto nível. O **demonstração -** scripts mostram os passos necessários para realizar cada tarefa, por isso, configurar pontos de interrupção e desagregar mais aprofundada para as chamadas para ver detalhes de implementação para os diferentes padrões de SaaS individuais.

Sugestões para explorar e avance mediante scripts do PowerShell:

- Abra **demonstração -** scripts no ISE do PowerShell.
- Executar ou continuar com **F5** (utilizando **F8** não é aconselhada porque *$PSScriptRoot* não é avaliada quando em execução seleções de um script).
- Para colocar os pontos de interrupção, clique ou selecione uma linha e prima **F9**.
- Para executar o próximo passo numa chamada de função ou script, utilize **F10**.
- Para avançar para uma chamada de função ou script, utilize **F11**.
- Para ir até ao fim do procedimento da chamada de função ou script atual, utilize **Shift + F11**.


## <a name="explore-database-schema-and-execute-sql-queries-using-ssms"></a>Explorar o esquema de base de dados e executar consultas SQL com o SSMS

Utilize [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) para ligar e procurar a servidores de aplicações e bases de dados.

A implementação foi inicialmente inquilinos e os servidores de base de dados SQL de catálogo para ligar a. A nomenclatura dos servidores depende o padrão de inquilinos de base de dados (consulte abaixo especificações). 

   - **Aplicação autónoma:** servidores para cada inquilino (ex. *contosoconcerthall -&lt;utilizador&gt;*  servidor) e *catálogo-sa -&lt;utilizador&gt;*
   - **Base de dados por inquilino:** *tenants1-dpt -&lt;utilizador&gt;*  e *catálogo-dpt -&lt;utilizador&gt;*  servidores
   - **Base de dados do multi-inquilino:** *tenants1-mt -&lt;utilizador&gt;*  e *catálogo-mt -&lt;utilizador&gt;*  servidores

Para garantir uma ligação de demonstração com êxito, todos os servidores têm um [regra de firewall](sql-database-firewall-configure.md) permitir que todos os IPs através de.


1. Abra *SSMS* e estabelecer ligação com aos inquilinos. O nome do servidor depende o padrão de inquilinos de base de dados que selecionou (consulte abaixo especificações):
    - **Aplicação autónoma:** servidores de inquilinos individuais (ex. *contosoconcerthall -&lt;utilizador&gt;. database.windows.net*) 
    - **Base de dados por inquilino:** *tenants1-dpt -&lt;utilizador&gt;. database.windows.net*
    - **Base de dados do multi-inquilino:** *tenants1-mt -&lt;utilizador&gt;. database.windows.net* 
2. Clique em **Ligar** > **Motor de Base de Dados...**:

   ![servidor de catálogo](media/saas-tenancy-wingtip-app-guidance-tips/connect.png)

3. As credenciais de demonstração são: Início de sessão = *developer*, Palavra-passe = *P@ssword1*

    A imagem abaixo demonstra o início de sessão para o *base de dados por inquilino* padrão. 
    ![ligação](media/saas-tenancy-wingtip-app-guidance-tips/tenants1-connect.png)
    
   

4. Repita os passos 2 a 3 e ligar ao servidor de catálogo (consulte abaixo os nomes de servidor específico com base num padrão de inquilinos da base de dados seleccionado)
    - **Aplicação autónoma:** *catálogo-sa -&lt;utilizador&gt;. database.windows.net*
    - **Base de dados por inquilino:** *catálogo-dpt -&lt;utilizador&gt;. database.windows.net*
    - **Base de dados do multi-inquilino:** *catálogo-mt -&lt;utilizador&gt;. database.windows.net*


Depois de estabelecer ligação com êxito, deve ver todos os servidores. A lista de bases de dados poderão ser diferente, consoante os inquilinos que aprovisionou.

A imagem abaixo demonstra o início de sessão para o *base de dados por inquilino* padrão.

![explorador de objetos](media/saas-tenancy-wingtip-app-guidance-tips/object-explorer.png)



## <a name="next-steps"></a>Passos seguintes
- [Implementar a aplicação de SaaS autónomo Wingtip bilhetes](saas-standaloneapp-get-started-deploy.md)
- [Implementar Wingtip pedidos de suporte de SaaS base de dados por aplicação do inquilino](saas-dbpertenant-get-started-deploy.md)
- [Implementar a aplicação de base de dados do Wingtip bilhetes SaaS multi-inquilino](saas-multitenantdb-get-started-deploy.md)

