---
title: "Início rápido: A sua primeira Base de Dados SQL do Azure | Microsoft Docs"
description: "Saiba como criar um servidor lógico da Base de Dados SQL, regras de firewall ao nível do servidor e bases de dados no portal do Azure. Também vai aprender a utilizar o SQL Server Management Studio com a Base de Dados SQL do Azure."
keywords: tutorial de base de dados sql, criar uma base de dados sql
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: aeb8c4c3-6ae2-45f7-b2c3-fa13e3752eed
ms.service: sql-database
ms.custom: single databases
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 02/17/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 166a9d7032bb75188a790bea1724aefd194dcefa
ms.openlocfilehash: 36afd5c8bccb080ae3aaf1b4975d317b9087a3b3


---
# <a name="create-connect-to-and-query-your-first-azure-sql-databases-in-the-azure-portal-and-using-ssms"></a>Criar, ligar e consultar as suas primeiras bases de dados SQL do Azure no portal do Azure e com o SSMS

Neste tutorial, vai aprender a criar, ligar e consultar bases de dados SQL do Azure no portal do Azure e com o SQL Server Management Studio. Depois de concluir este tutorial:

* Terá criado um grupo de recursos que contém um servidor lógico, uma regra de firewall ao nível do servidor e duas bases de dados.
* Saberá como ver propriedades de servidores e bases de dados no portal do Azure e com o SQL Server Management Studio.
* Saberá como consultar uma base de dados no portal do Azure e com o SQL Server Management Studio.

**Estimativa de tempo**: este tutorial demora, aproximadamente, 30 minutos (assumindo que já cumpre os pré-requisitos).

> [!TIP]
> Também pode aprender a criar, ligar e consultar bases de dados SQL do Azure com o [PowerShell](sql-database-get-started-powershell.md) ou com [C#](sql-database-get-started-csharp.md).
>

> [!NOTE]
> Este tutorial ajuda-o a aprender os conteúdos dos tópicos de aprendizagem seguintes: [Descrição geral do servidor da Base de Dados SQL](sql-database-server-overview.md), [Descrição geral da Base de Dados SQL](sql-database-overview.md) e [Descrição geral das regras de firewall da Base de Dados SQL do Azure](sql-database-firewall-configure.md). Para obter uma descrição geral do serviço Base de Dados SQL, veja [O que é a Base de Dados SQL?](sql-database-technical-overview.md).
>  

## <a name="prerequisites"></a>Pré-requisitos

* **Uma conta do Azure**. Pode [abrir uma conta do Azure gratuita](https://azure.microsoft.com/free/) ou [Ativar as vantagens de subscritor do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/). 

* **Permissões de criação do Azure**. Tem de conseguir ligar ao portal do Azure com uma conta que seja membro do proprietário da subscrição ou que tenha a função de contribuinte. Para obter mais informações sobre o controlo de acesso baseado em funções (RBAC), veja o artigo [Introdução à gestão de acesso no portal do Azure](../active-directory/role-based-access-control-what-is.md).

* **SQL Server Management Studio**. Pode transferir e instalar a versão mais recente do SQL Server Management Studio (SSMS) em [Download SQL Server Management Studio (Transferir o SQL Server Management Studio)](https://msdn.microsoft.com/library/mt238290.aspx). Utilize sempre a versão mais recente do SSMS quando ligar à Base de Dados SQL do Azure, uma vez que são continuamente lançadas capacidades novas.

### <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Os passos deste procedimento mostram-lhe como ligar ao portal do Azure com a sua [conta do Azure](https://account.windowsazure.com/Home/Index).

1. Abra o browser da sua preferência e ligue-se ao [Portal do Azure](https://portal.azure.com/).
2. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
3. Quando for apresentada a página **Iniciar sessão**, forneça as credenciais da sua subscrição.
   
   ![Iniciar sessão](./media/sql-database-get-started/login.png)


<a name="create-logical-server-bk"></a>

## <a name="create-a-new-logical-sql-server"></a>Criar um servidor SQL lógico novo

Os passos deste procedimento mostram-lhe como criar um servidor lógico no portal do Azure na região que escolher. Um servidor lógico é o objeto no qual cria as suas bases de dados SQL e o objeto no qual cria regras de firewall para permitir aos utilizadores ligarem-se através da firewall da Base de Dados SQL do Azure. 

1. Clique em **Novo**, escreva **servidor sql** e, em seguida, clique em **ENTER**.

    ![servidor SQL lógico](./media/sql-database-get-started/logical-sql-server.png)
2. Clique em **servidor SQL (servidor lógico)**.
   
    ![criar servidor sql lógico](./media/sql-database-get-started/create-logical-sql-server.png)
3. Clique em **Criar** para abrir o painel do novo SQL Server (servidor lógico) apenas.

    ![novo servidor sql lógico](./media/sql-database-get-started/new-logical-sql-server.png)
3. Na caixa de texto **Nome do servidor**, indique um nome válido para o novo servidor lógico. Uma marca de verificação verde indica que forneceu um nome válido.
    
    ![novo nome do servidor](./media/sql-database-get-started/new-server-name.png)

    > [!IMPORTANT]
    > O nome completamente qualificado do novo servidor tem de ser globalmente exclusivo e estar no formato: **<o_nome_do_seu_servidor>.database.windows.net**. Vai utilizar este nome de servidor completamente qualificado mais à frente no tutorial, para ligar ao servidor e às bases de dados.
    >
    
4. Na caixa de texto **Início de sessão de administrador do servidor**, indique um nome de utilizador para o início de sessão de autenticação SQL para este servidor. Este início de sessão é conhecido como o início de sessão do principal do servidor. Uma marca de verificação verde indica que forneceu um nome válido.
    
    ![início de sessão de administrador SQL](./media/sql-database-get-started/sql-admin-login.png)
5. Nas caixas de texto **Palavra-passe** e **Confirmar palavra-passe**, forneça uma palavra-passe para a conta de início de sessão do principal do servidor. Uma marca de verificação verde indica que forneceu uma palavra-passe válida.
    
    ![palavra-passe de administrador SQL](./media/sql-database-get-started/sql-admin-password.png)
6. Na caixa pendente **Subscrição**, selecione uma subscrição na qual tenha permissão para criar objetos.

    ![subscrição](./media/sql-database-get-started/subscription.png)
7. Na caixa de texto **Grupo de recursos**, selecione **Criar novo** e indique um nome válido para o grupo de recursos novo. Uma marca de verificação verde indica que forneceu um nome válido.

    ![novo grupo de recursos](./media/sql-database-get-started/new-resource-group.png)

8. Na caixa de texto **Localização**, selecione um datacenter no qual criar o servidor lógico.
    
    ![localização do servidor](./media/sql-database-get-started/server-location.png)
    
    > [!TIP]
    > A caixa de verificação **Permitir que os serviços do azure acedam ao servidor** não pode ser alterada neste painel. Pode alterar esta definição no painel da firewall do servidor. Para obter mais informações, veja o artigo [Introdução à segurança](sql-database-control-access-sql-authentication-get-started.md).
    >
    
9. Selecione a caixa de verificação **Afixar ao dashboard**.

10. Clique em **Criar** para implementar este script no Azure e criar o servidor lógico.

    ![botão criar](./media/sql-database-get-started/create.png)

11. Quando o servidor estiver criado, reveja as propriedades que são apresentadas por predefinição. 

    ![painel do servidor sql](./media/sql-database-get-started/sql-server-blade.png)
12. Clique em **Propriedades** para ver propriedades adicionais do servidor lógico do SQL.

    ![propriedades do servidor sql](./media/sql-database-get-started/sql-server-properties.png)
13. Copie o nome de servidor completamente qualificado para a sua área de transferência para o utilizar um pouco mais tarde neste tutorial.

    ![nome completo do servidor sql](./media/sql-database-get-started/sql-server-full-name.png)

## <a name="create-a-server-level-firewall-rule"></a>Criar uma regra de firewall ao nível do servidor

Os passos deste procedimento mostram-lhe como criar uma regra de firewall ao nível do servidor no portal do Azure. Por predefinição, as firewalls da Base de Dados SQL do Azure impedem a conectividade externa ao seu servidor lógico e às respetivas bases de dados. Para permitir-lhe ligar-se ao seu servidor, tem de criar uma regra de firewall para o endereço IP do computador a partir do qual se vai ligar, no próximo procedimento. Para obter mais informações, veja [Descrição geral das regras de firewall da Base de Dados SQL do Azure](sql-database-firewall-configure.md).

1. No painel do servidor SQL, clique em **Firewall** para abrir o painel Firewall do servidor. Repare que o endereço IP é apresentado para o seu computador cliente.

    ![firewall do servidor sql](./media/sql-database-get-started/sql-server-firewall.png)

2. Clique em **Adicionar IP de cliente**, na barra de ferramentas, para criar uma regra de firewall para o seu endereço IP atual.

    ![adicionar IP de cliente](./media/sql-database-get-started/add-client-ip.png)

    > [!NOTE]
    > Pode criar uma regra de firewall para endereços IP individuais ou para intervalos de endereços. Abrir a firewall permite aos utilizadores e administradores do SQL iniciar sessão em qualquer base de dados no servidor para o qual tenham credenciais válidas.
    >

4. Clique em **Guardar**, na barra de ferramentas, para guardar esta regra de firewall ao nível do servidor e, em seguida, clique em **OK** para fechar a caixa de diálogo Êxito.

    ![exito](./media/sql-database-get-started/save-firewall-rule.png)

## <a name="connect-to-the-server-with-ssms"></a>Ligar ao servidor com o SSMS

Os passos deste procedimento mostram-lhe como ligar-se ao servidor lógico SQL com o SQL Server Management Studio. O SSMS é a ferramenta principal que os administradores de bases de dados utilizam para gerir servidores e bases de dados SQL.

1. Abra o SQL Server Management Studio (escreva **Microsoft SQL Server Management Studio**, na caixa d pesquisa Windows, e clique em **Enter** para abrir o SSMS).

    ![SQL Server Management Studio](./media/sql-database-get-started/ssms.png)
3. Na caixa de diálogo **Ligar ao servidor**, introduza o nome do servidor completamente qualificado do procedimento anterior, selecione Autenticação SQL Server e indique o início de sessão e a palavra-passe especificados durante o aprovisionamento do servidor.

    ![ligar ao servidor](./media/sql-database-get-started/connect-to-server.png)
4. Clique em **Ligar** para iniciar a ligação e abrir o Object Explorer no SSMS.

    ![ligado ao servidor](./media/sql-database-get-started/connected-to-server.png)
5. No Object Explorer, expanda a opção **Bases de Dados**, a opção **Bases de Dados do Sistema** e a opção **Mestra** para ver os objetos na base de dados mestra.

    ![base de dados mestra](./media/sql-database-get-started/master-database.png)
6. Clique com o botão direito do rato em **mestra** e, em seguida, clique em **Nova Consulta**.

    ![consultar base de dados mestra](./media/sql-database-get-started/query-master-database.png)

8. Na janela da consulta, escreva a seguinte consulta:

   ```select * from sys.objects```

9.  Na barra de ferramentas, clique em **Executar** para devolver uma lista de todos os objetos de sistema na base de dados mestra.

    ![consultar objetos de sistema da base de dados mestra](./media/sql-database-get-started/query-master-database-system-objects.png)

    > [!NOTE]
    > Para obter uma introdução sobre a segurança do SQL, veja [Introdução à autenticação SQL](sql-database-control-access-sql-authentication-get-started.md)
    >

## <a name="create-a-database-with-sample-data"></a>Criar uma base de dados com dados de exemplo

Os passos deste procedimento mostram-lhe como criar uma base de dados com dados de exemplo no portal do Azure associada ao servidor lógico que criou anteriormente. 

1. No portal do Azure, clique em **Bases de dados SQL** no painel predefinido.

    ![bases de dados sql](./media/sql-database-get-started/new-sql-database.png)
2. No painel Bases de Dados SQL, clique em **Adicionar**. 

    ![adicionar base de dados SQL](./media/sql-database-get-started/add-sql-database.png)

    ![painel base de dados sql](./media/sql-database-get-started/sql-database-blade.png)
3. Na caixa de texto **Nome da base de dados**, indique um nome válido para a base de dados.

    ![nome da base de dados sql](./media/sql-database-get-started/sql-database-name.png)
4. Em **Selecionar origem**, selecione **Sample (AdventureWorksLT)**.
   
    ![adventure works lt](./media/sql-database-get-started/adventureworkslt.png)
5. Em **Servidor**, confirme que o seu servidor está selecionado. Repare também que, quando adiciona uma base de dados a um servidor, esta pode ser adicionada como uma base de dados individual (a predefinição) ou adicionada a um conjunto elástico. Para obter mais informações sobre conjuntos elásticos, veja o artigo [Conjuntos elásticos](sql-database-elastic-pool.md).

6. Em **Escalão de preço**, altere o escalão de preço para **Básico** e clique em **Selecionar**. Pode aumentar o escalão de preço mais tarde se assim o desejar, mas, para efeitos de aprendizagem, recomendamos que utilize o escalão de custo mais baixo.

    ![escalão de preço](./media/sql-database-get-started/pricing-tier.png)
7. Selecione a caixa de verificação **Afixar ao dashboard** e clique em **Criar**.

    ![botão criar](./media/sql-database-get-started/create.png)

8. Quando a base de dados estiver criada, veja as propriedades da mesma no portal do Azure. Os tutoriais subsequentes ajudam-no a compreender as opções que tem disponíveis neste painel. 

    ![painel da nova base de dados de exemplo](./media/sql-database-get-started/new-sample-db-blade.png)

## <a name="query-the-database-in-the-azure-portal"></a>Consultar a base de dados no portal do Azure

Os passos deste procedimento mostram-lhe como consultar a base de dados diretamente no portal do Azure. 

1. No painel Bases de Dados SQL, clique em **Ferramentas**, na barra de ferramentas.

    ![ferramentas](./media/sql-database-get-started/tools.png)
2. No painel Ferramentas, clique em **Editor de consultas (pré-visualização)**.

    ![editor de consultas](./media/sql-database-get-started/query-editor.png)
3. Clique na caixa de verificação para confirmar que o editor de consultas é uma funcionalidade de pré-visualização e clique em **OK**.
4. No painel **Editor de consultas**, clique em **Iniciar Sessão**.

    ![painel “editor de consultas”](./media/sql-database-get-started/query-editor-blade.png)
5. Reveja o tipo de Autorização e o Início de sessão e indique a palavra-passe deste. 

    ![início de sessão no editor de consultas](./media/sql-database-get-started/query-editor-login.png)
6. Clique em **OK** para tentar iniciar sessão.
7. Quando estiver autenticado, escreva a consulta seguinte na janela de consultas e clique em **Executar**.

   ```select * from sys.objects```

    ![consultar editor de consultas](./media/sql-database-get-started/query-editor-query.png)

8. Veja os resultados da consulta no painel **Resultados**.

    ![Resultados do editor de consultas](./media/sql-database-get-started/query-editor-results.png)

## <a name="query-the-database-with-ssms"></a>Consultar a base de dados com o SSMS

Os passos deste procedimento mostram-lhe como ligar à base de dados com o SQL Server Management Studio e, em seguida, consultar os dados de exemplo para ver os objetos na base de dados.

1. Mude para o SQL Server Management Studio e, no Object Explorer, clique em **Bases de Dados** e, em seguida, clique em **Atualizar** na barra de ferramentas para ver a base de dados de exemplo.

    ![nova base de dados de exemplo com ssms](./media/sql-database-get-started/new-sample-db-ssms.png)
2. No Object Explorer, expanda a sua nova base de dados para ver os respetivos objetos.

    ![objetos da nova base de dados de exemplo com ssms](./media/sql-database-get-started/new-sample-db-objects-ssms.png)
3. Clique com o botão direito do rato na base de dados de exemplo e, em seguida, clique em **Nova Consulta**.

    ![consulta da nova base de dados de exemplo com ssms](./media/sql-database-get-started/new-sample-db-query-ssms.png)
4. Na janela da consulta, escreva a seguinte consulta:

   ```select * from sys.objects```
   
9.  Na barra de ferramentas, clique em **Executar** para devolver uma lista de todos os objetos de sistema na base de dados de exemplo.

    ![objetos de sistema da consulta da nova base de dados de exemplo com ssms](./media/sql-database-get-started/new-sample-db-query-objects-ssms.png)

## <a name="create-a-blank-database-with-ssms"></a>Criar uma base de dados vazia com o SSMS

Os passos deste procedimento mostram-lhe como criar uma base de dados nova com o SQL Server Management Studio.

1. No Object Explorer, clique com o botão direito do rato em **Bases de Dados** e, em seguida, clique em **Nova base de dados**.

    ![nova base de dados vazia com ssms](./media/sql-database-get-started/new-blank-database-ssms.png)

2. Na caixa de diálogo **Nova Base de Dados**, indique um nome para a base de dados na caixa de texto Nome da base de dados. 

    ![nome da nova base de dados vazia com ssms](./media/sql-database-get-started/new-blank-database-name-ssms.png)

3. Na caixa de diálogo Nova Base de Dados, clique em **Opções** e, em seguida, altere a Edição para **Básica**.

    ![opções da nova base de dados vazia com ssms](./media/sql-database-get-started/new-blank-database-options-ssms.png)

    > [!TIP]
    > Veja as outras opções que pode modificar nesta caixa de diálogo para uma Base de Dados SQL do Azure. Para obter mais informações sobre estas opções, veja o artigo [Criar Base de Dados](https://msdn.microsoft.com/library/dn268335.aspx).
    >

4. Clique em **OK** para criar a base de dados vazia.
5. Quando terminar, atualize o nó Base de Dados no Object Explorer para ver a base de dados vazia que acabou de criar. 

    ![nova base de dados vazia no object explorer](./media/sql-database-get-started/new-blank-database-object-explorer.png)

## <a name="troubleshoot-connectivity"></a>Resolver problemas de conectividade

Recebe mensagens de erro quando a ligação à base de dados SQL do Azure falha. Os problemas de ligação podem ser provocados pela reconfiguração da base de dados SQL do Azure, por definições de firewall, por tempo limite das ligações excedido ou por informações de início de sessão incorretas. Para obter uma ferramenta de resolução de problemas de conectividade, veja [Troubleshooting connectivity issues with Microsoft Azure SQL Database (Resolver problemas de conectividade com a base de Dados SQL do Microsoft Azure)](https://support.microsoft.com/help/10085/troubleshooting-connectivity-issues-with-microsoft-azure-sql-database).

## <a name="delete-a-single-database-in-the-azure-portal"></a>Eliminar uma base de dados individual no portal do Azure

Os passos deste procedimento mostram-lhe como eliminar uma base de dados individual no portal do Azure.

1. No painel Bases de dados SQL, no portal do Azure, clique na base de dados que pretende eliminar. 
2.  Na sua base de dados SQL, clique em **Eliminar**.

    ![eliminar base de dados](./media/sql-database-get-started/delete-database.png)
2. Clique em **Sim** para confirmar que pretende eliminar a base de dados permanentemente.

    ![eliminar base de dado sim](./media/sql-database-get-started/delete-database-yes.png)

> [!TIP]
> Durante o período de retenção da base de dados, pode restaurá-la a partir das cópias de segurança automáticas iniciadas pelo serviço (desde que não elimine o próprio servidor). No caso das bases de dados de edição Básica, pode restaurá-las no período de sete dias. Em todas as outras edições, pode restaurá-las num período de 35 dias. Se eliminar o próprio servidor, não pode recuperá-lo nem a nenhuma das bases de dados eliminadas do mesmo. Para obter mais informações sobre as cópias de segurança da base de dados, veja [Learn about SQL Database backups (Saber mais sobre as cópias de segurança da Base de Dados SQL)](sql-database-automated-backups.md) e sobre o restauro de bases de dados a partir de cópias de segurança, veja [Recuperação de bases de dados](sql-database-recovery-using-backups.md). Para obter um artigo que mostra como restaurar bases de dados eliminadas, veja [Restore a deleted Azure SQL database - Azure portal (Restaurar bases de dados SQL do Azure eliminadas - portal do Azure)](sql-database-restore-deleted-database-portal.md).
>


## <a name="next-steps"></a>Passos seguintes
Agora que concluiu este tutorial, existem alguns tutoriais adicionais que poderá explorar para cimentar o que aprendeu neste tutorial. 

- Para obter um tutorial de introdução à autenticação do SQL Server, veja [SQL authentication and authorization (Autenticação e autorização do SQL)](sql-database-control-access-sql-authentication-get-started.md)
- Para obter um tutorial de introdução à autenticação do Azure Active Directory, veja [AAD authentication and authorization (Autenticação e autorização do AAD)](sql-database-control-access-aad-authentication-get-started.md).
* Se quiser consultar a base de dados de exemplo no portal do Azure, veja [Public preview: Interactive query experience for SQL databases (Pré-visualização pública: Experiência de consultas interativas para bases de dados SQL)](https://azure.microsoft.com/updates/azure-sql-database-public-preview-t-sql-editor/)
* Se tem conhecimentos de Excel, saiba como [Ligar a uma base de dados SQL no Azure com o Excel](sql-database-connect-excel.md).
* Se estiver pronto para iniciar a codificação, escolha a linguagem de programação em [Bibliotecas de ligação para Base de Dados SQL e SQL Server](sql-database-libraries.md).
* Se quiser mover as suas bases de dados do SQL Server no local para o Azure, veja o artigo [Migrar uma base de dados para a Base de Dados SQL](sql-database-cloud-migrate.md).
* Se quiser carregar alguns dados para uma tabela nova a partir de um ficheiro CSV com a ferramenta de linha de comandos do BCP, veja [Carregar dados para a Base de Dados SQL a partir de um ficheiro CSV com o BCP](sql-database-load-from-csv-with-bcp.md).
* Se quiser começar a criar tabelas e outros objetos, veja o tópico "Para criar uma tabela" no artigo [Criar uma tabela](https://msdn.microsoft.com/library/ms365315.aspx).

## <a name="additional-resources"></a>Recursos adicionais

- Para obter uma descrição geral técnica, veja [O que é a Base de Dados SQL?](sql-database-technical-overview.md)
- Para informações sobre preços, veja [preços Base de Dados SQL do Azure](https://azure.microsoft.com/pricing/details/sql-database/).




<!--HONumber=Feb17_HO3-->


