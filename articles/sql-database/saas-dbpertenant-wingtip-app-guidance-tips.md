---
title: "Orientações para o exemplo de aplicação multi-inquilino de base de dados SQL - Wingtip SaaS | Microsoft Docs"
description: "Fornece os passos e as orientações para instalar e executar a aplicação multi-inquilino de exemplo que utiliza o SQL Database do Azure, o exemplo de Wingtip SaaS."
keywords: tutorial de base de dados sql
services: sql-database
author: ajlam
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/12/2017
ms.author: andrela
ms.openlocfilehash: f7ba0569a0ede80a6579ccf3f30efb1997d4ad90
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="guidance-and-tips-for-azure-sql-database-multi-tenant-saas-app-example"></a>Orientações e sugestões de exemplo de aplicação SaaS do SQL Database do Azure multi-inquilino


## <a name="download-and-unblock-the-wingtip-tickets-saas-database-per-tenant-scripts"></a>Transferir e desbloquear Wingtip pedidos de suporte de SaaS base de dados por scripts de inquilino

Conteúdo executável (scripts, dlls) pode estar bloqueado pelo Windows quando zip ficheiros são transferidos a partir de uma origem externa e extraiu. Quando extrair os scripts a partir de um ficheiro zip ***siga os passos abaixo para desbloquear o ficheiro. zip antes de a extrair***. Isto garante que os scripts estão autorizados a executar.

1. Navegue até à [Wingtip pedidos de suporte de SaaS base de dados por repositório do GitHub inquilino](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant).
2. Clique em **clonar ou transferir**.
3. Clique em **transferir ZIP** e guarde o ficheiro.
4. Clique com botão direito do **WingtipTicketsSaaS-DbPerTenant-master.zip** do ficheiro e selecione **propriedades**.
5. No **geral** separador, selecione **desbloqueio**.
6. Clique em **OK**.
7. Extraia os ficheiros.

Scripts estão localizados no *... \\Learning módulos* pasta.


## <a name="working-with-the-wingtip-tickets-saas-database-per-tenant-powershell-scripts"></a>Trabalhar com Wingtip bilhetes SaaS base de dados por inquilino Scripts do PowerShell

Para tirar o máximo partido do exemplo tem de aprofundar os scripts fornecidos. Utilizar pontos de interrupção e percorra os scripts, examinando os detalhes de como os diferentes padrões de SaaS são implementados. Passo facilmente através de scripts fornecidos e módulos para a melhor compreensão, recomendamos que utilize o [ISE do PowerShell](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise).

### <a name="update-the-configuration-file-for-your-deployment"></a>Atualizar o ficheiro de configuração para a implementação

Editar o **UserConfig.psm1** ficheiro com o valor de utilizador e grupo de recursos que definiu durante a implementação:

1. Abra o *ISE do PowerShell* e carregar... \\Learning módulos\\*UserConfig.psm1* 
2. Atualização *ResourceGroupName* e *nome* com os valores específicos para a sua implementação (em linhas 10 e 11 apenas).
3. Guarde as alterações!

Definir estes valores aqui simplesmente evita que tenha que que atualizar estes valores específicos da implementação em cada script.

### <a name="execute-scripts-by-pressing-f5"></a>Executar Scripts ao premir F5

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

A implementação inicialmente tem dois servidores de base de dados do SQL Server para ligar ao - o *tenants1-dpt -&lt;utilizador&gt;*  servidor e o *catálogo-dpt -&lt;utilizador&gt;* servidor. Para garantir uma ligação de demonstração com êxito, ambos os servidores têm um [regra de firewall](sql-database-firewall-configure.md) permitir que todos os IPs através de.


1. Abra *SSMS* e estabelecer ligação com o *tenants1-dpt -&lt;utilizador&gt;. database.windows.net* servidor.
2. Clique em **Ligar** > **Motor de Base de Dados...**:

   ![servidor de catálogo](media/saas-dbpertenant-wingtip-app-guidance-tips/connect.png)

3. As credenciais de demonstração são: Início de sessão = *developer*, Palavra-passe = *P@ssword1*

   ![ligação](media/saas-dbpertenant-wingtip-app-guidance-tips/tenants1-connect.png)

4. Repita os passos 2 a 3 e estabelecer ligação com o *catálogo-dpt -&lt;utilizador&gt;. database.windows.net* servidor.


Após estabelecer ligação, deverá poder ver os dois servidores. A lista de bases de dados poderão ser diferente, consoante os inquilinos que aprovisionou.

![explorador de objetos](media/saas-dbpertenant-wingtip-app-guidance-tips/object-explorer.png)



## <a name="next-steps"></a>Passos seguintes

[Implementar Wingtip pedidos de suporte de SaaS base de dados por aplicação do inquilino](saas-dbpertenant-get-started-deploy.md)

