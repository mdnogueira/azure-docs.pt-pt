---
title: "Tutorial da Base de Dados SQL: Criar um servidor, uma regra de firewall ao nível do servidor, uma base de dados de exemplo, uma regra de firewall ao nível da base de dados e ligar com o SQL Server Management Studio | Microsoft Docs"
description: "Saiba como configurar um servidor lógico de Base de dados SQL, uma regra de firewall do servidor, uma base de dados SQL e dados de exemplo. Além disso, saiba como ligar-se com ferramentas de cliente, configurar utilizadores e como configurar uma regra de firewall de base de dados."
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
ms.date: 11/23/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: c2252fc81f97019391ca2ba957f8402c4e97a9c2
ms.openlocfilehash: f9b17c1cc77918fb1989b94b5bb359a697ceea7c


---
# <a name="get-started-with-azure-sql-database-servers-databases-and-firewall-rules-by-using-the-azure-portal-and-sql-server-management-studio"></a>Introdução aos servidores, bases de dados e regras de firewall da Base de Dados SQL do Azure através do portal do Azure e do SQL Server Management Studio

Neste tutorial de introdução irá aprender a utilizar o portal do Azure para:

* Criar um novo grupo de recursos do Azure
* Criar um servidor lógico do SQL do Azure
* Ver as propriedades do servidor lógico do SQL do Azure
* Criar uma regra de firewall ao nível do servidor
* Criar a base de dados de exemplo do Adventure Works LT como base de dados única
* Ver as propriedades da base de dados de exemplo do Adventure Works LT no Azure

Neste tutorial também utilizará a versão mais recente do SQL Server Management Studio para:

* Ligar ao servidor lógico e à respetiva base de dados mestra
* Consultar a base de dados mestra
* Ligar à base de dados de exemplo
* Consultar a base de dados de amostra

Quando terminar este tutorial, terá uma base de dados de exemplo e uma base de dados vazia em execução num grupo de recursos do Azure e ligadas a um servidor lógico. Também terá uma regra de firewall ao nível do servidor configurada para permitir que o principal ao nível de servidor inicie sessão no servidor a partir de um endereço IP especificado (ou um intervalo de endereços IP). 

**Estimativa de tempo**: este tutorial irá demorar, aproximadamente, 30 minutos (assumindo que já cumpre os pré-requisitos).

## <a name="prerequisites"></a>Pré-requisitos

* Precisa de uma conta do Azure. Pode [abrir uma conta do Azure gratuita](/pricing/free-trial/?WT.mc_id=A261C142F) ou [Ativar as vantagens de subscritor do Visual Studio](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). 

* Tem de conseguir ligar ao portal do Azure com uma conta que seja membro do proprietário da subscrição ou da função de contribuinte. Para obter mais informações sobre o controlo de acesso baseado em funções (RBAC), veja o artigo [Introdução à gestão de acesso no portal do Azure](../active-directory/role-based-access-control-what-is.md).

> [!TIP]
> Pode realizar estas mesmas tarefas num tutorial de introdução com o [C#](sql-database-get-started-csharp.md) ou o [PowerShell](sql-database-get-started-powershell.md).
>

### <a name="sign-in-by-using-your-existing-account"></a>Iniciar sessão com a conta existente
Com a [subscrição existente](https://account.windowsazure.com/Home/Index), siga estes passos para se ligar ao portal do Azure.

1. Abra o browser da sua preferência e ligue-se ao [Portal do Azure](https://portal.azure.com/).
2. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
3. Quando for apresentada a página **Iniciar sessão**, forneça as credenciais da sua subscrição.
   
   ![Iniciar sessão](./media/sql-database-get-started/login.png)


<a name="create-logical-server-bk"></a>

## <a name="create-a-new-logical-sql-server-in-the-azure-portal"></a>Criar um novo servidor SQL lógico no portal do Azure

1. Clique em **Novo**, escreva **servidor sql** e, em seguida, clique em **ENTER**.

    ![servidor SQL lógico](./media/sql-database-get-started/logical-sql-server.png)
2. Clique em **servidor SQL (servidor lógico)**.
   
    ![criar servidor sql lógico](./media/sql-database-get-started/create-logical-sql-server.png)
3. Clique em **Criar** para abrir o painel do novo SQL Server (servidor lógico).

    ![novo servidor sql lógico](./media/sql-database-get-started/new-logical-sql-server.png)
3. Na caixa de texto do nome do Servidor, forneça um nome válido para o novo servidor lógico. Uma marca de verificação verde indica que forneceu um nome válido.
    
    ![novo nome do servidor](./media/sql-database-get-started/new-server-name.png)

    > [!IMPORTANT]
    > O nome completamente qualificado do novo servidor será <o_seu_nome_de_servidor>.database.windows.net.
    >
    
4. Na caixa de texto de início de sessão de administrador do Servidor, forneça um nome de utilizador para o início de sessão de autenticação SQL para este servidor. Este início de sessão é conhecido como o início de sessão do principal do servidor. Uma marca de verificação verde indica que forneceu um nome válido.
    
    ![início de sessão de administrador SQL](./media/sql-database-get-started/sql-admin-login.png)
5. Nas caixas de texto **Palavra-passe** e **Confirmar palavra-passe**, forneça uma palavra-passe para a conta de início de sessão do principal do servidor. Uma marca de verificação verde indica que forneceu uma palavra-passe válida.
    
    ![palavra-passe de administrador SQL](./media/sql-database-get-started/sql-admin-password.png)
6. Selecione uma subscrição em que tenha permissão para criar objetos.

    ![subscrição](./media/sql-database-get-started/subscription.png)
7. Na caixa de texto Grupo de recursos, selecione **Criar novo** e, em seguida, na caixa de texto do grupo de recursos, forneça um nome válido para o novo grupo de recursos (também pode utilizar um grupo de recursos existente se já tiver criado um para si). Uma marca de verificação verde indica que forneceu um nome válido.

    ![novo grupo de recursos](./media/sql-database-get-started/new-resource-group.png)

8. Na caixa de texto **Localização**, selecione um datacenter adequado à sua localização, como, por exemplo, "Leste da Austrália".
    
    ![localização do servidor](./media/sql-database-get-started/server-location.png)
    
    > [!TIP]
    > A caixa de verificação **Permitir que os serviços do azure acedam ao servidor** não pode ser alterada neste painel. Pode alterar esta definição no painel da firewall do servidor. Para obter mais informações, veja o artigo [Introdução à segurança](sql-database-get-started-security.md).
    >
    
9. Clique em **Criar**.

    ![botão criar](./media/sql-database-get-started/create.png)

## <a name="view-the-logical-sql-server-properties-in-the-azure-portal"></a>Ver as propriedades do SQL Server lógico no portal do Azure

1. No portal do Azure, clique em **Mais serviços**.

    ![mais serviços](./media/sql-database-get-started/more-services.png)
2. Na caixa de texto Filtro, escreva **SQL** e, em seguida, clique na estrela dos servidores SQL para especificar os servidores SQL como um favorito no Azure. 

    ![definir favoritos](./media/sql-database-get-started/favorite.png)
3. No painel predefinido, clique em **servidores SQL** para abrir a lista de servidores SQL na sua subscrição do Azure. 

    ![novo servidor sql](./media/sql-database-get-started/new-sql-server.png)

4. Clique no seu novo servidor SQL para ver as respetivas propriedades no portal do Azure. Os tutoriais subsequentes ajudam-no a compreender as opções que tem disponíveis neste painel.

    ![painel do servidor sql](./media/sql-database-get-started/sql-server-blade.png)
5. Em Definições, clique em **Propriedades** para ver várias propriedades do servidor SQL lógico.

    ![propriedades do servidor sql](./media/sql-database-get-started/sql-server-properties.png)
6. Copie o nome de servidor completamente qualificado para a sua área de transferência para o utilizar um pouco mais tarde neste tutorial.

    ![nome completo do servidor sql](./media/sql-database-get-started/sql-server-full-name.png)

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Criar uma regra de firewall ao nível do servidor no portal do Azure

1. No painel do servidor SQL, em Definições, clique em **Firewall** para abrir o painel Firewall do servidor SQL.

    ![firewall do servidor sql](./media/sql-database-get-started/sql-server-firewall.png)

2. Reveja o endereço IP de cliente apresentado e confirme que o mesmo é o seu endereço IP na Internet através do browser da sua preferência (perguntar "qual é o meu endereço IP"). Ocasionalmente, os endereços IP não correspondem devido a vários motivos.

    ![o seu endereço IP](./media/sql-database-get-started/your-ip-address.png)

3. Partindo do princípio de que os endereços IP correspondem, clique em **Adicionar IP de cliente** na barra de ferramentas.

    ![adicionar IP de cliente](./media/sql-database-get-started/add-client-ip.png)

    > [!NOTE]
    > Pode abrir a firewall da Base de Dados SQL no servidor para um único endereço IP ou um conjunto de endereços. Abrir a firewall permite aos utilizadores e administradores SQL iniciar sessão em qualquer base de dados no servidor para o qual tenham credenciais válidas.
    >

4. Clique em **Guardar** na barra de ferramentas para guardar esta regra de firewall ao nível do servidor e, em seguida, clique em **OK**.

    ![adicionar IP de cliente](./media/sql-database-get-started/save-firewall-rule.png)

## <a name="connect-to-sql-server-using-sql-server-management-studio-ssms"></a>Ligar ao servidor SQL através do SQL Server Management Studio (SSMS)

1. Se ainda não o fez, transfira e instale a versão mais recente do SSMS em [Transferir o SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx). Para se manter atualizado, a versão mais recente do SSMS avisa-o quando existe uma nova versão disponível para transferência.

2. Após a instalação, escreva **Microsoft SQL Server Management Studio** na caixa de pesquisa do Windows e clique em **Enter** para abrir o SSMS:

    ![SQL Server Management Studio](./media/sql-database-get-started/ssms.png)
3. Na caixa de diálogo Ligar ao Servidor, introduza as informações necessárias para ligar ao seu servidor SQL através da Autenticação do SQL Server.

    ![ligar ao servidor](./media/sql-database-get-started/connect-to-server.png)
4. Clique em **Ligar**.

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
    > Para explorar a segurança SQL, veja o artigo [Introdução à segurança SQL](sql-database-get-started-security.md)
    >

## <a name="create-new-database-in-the-azure-portal-using-adventure-works-lt-sample"></a>Criar uma nova base de dados no portal do Azure com um exemplo do Adventure Works LT

1. No portal do Azure, clique em **Bases de dados SQL** no painel predefinido.

    ![bases de dados sql](./media/sql-database-get-started/new-sql-database.png)
2. No painel Bases de Dados SQL, clique em **Adicionar**.

    ![adicionar base de dados SQL](./media/sql-database-get-started/add-sql-database.png)
3. No painel Base de Dados SQL, reveja as informações que preenchemos por si.

    ![painel base de dados sql](./media/sql-database-get-started/sql-database-blade.png)
4. Forneça um nome de base de dados válido.

    ![nome da base de dados sql](./media/sql-database-get-started/sql-database-name.png)
5. Em Selecionar origem, clique em **Exemplo** e, em seguida, em Selecionar exemplo, clique em **AdventureWorksLT [V12]**.
   
    ![adventure works lt](./media/sql-database-get-started/adventureworkslt.png)
6. Em Servidor, forneça o nome de utilizador e a palavra-passe do início de sessão de administrador do servidor.

    ![credenciais do servidor](./media/sql-database-get-started/server-credentials.png)

    > [!NOTE]
    > Quando adiciona uma base de dados a um servidor, a mesma pode ser adicionada como base de dados única (a predefinição) ou adicionada a um conjunto elástico. Para obter mais informações sobre conjuntos elásticos, veja o artigo [Conjuntos elásticos](sql-database-elastic-pool.md).
    >

7. Em Escalão de preço, altere o escalão de preço para **Básico** (pode aumentar o escalão de preço mais tarde se assim o desejar, mas, para efeitos de aprendizagem, recomendamos que utilize o escalão de custo mais baixo).

    ![escalão de preço](./media/sql-database-get-started/pricing-tier.png)
8. Clique em **Criar**.

    ![botão criar](./media/sql-database-get-started/create.png)

## <a name="view-database-properties-in-the-azure-portal"></a>Ver as propriedades da base de dados no portal do Azure

1. No painel das bases de Dados SQL, clique na sua nova base de dados para ver as respetivas propriedades no portal do Azure. Os tutoriais subsequentes ajudam-no a compreender as opções que tem disponíveis neste painel. 

    ![painel da nova base de dados de exemplo](./media/sql-database-get-started/new-sample-db-blade.png)
2. Clique em **Propriedades** para ver informações adicionais sobre a base de dados.

    ![propriedades da nova base de dados de exemplo](./media/sql-database-get-started/new-sample-db-properties.png)

3. Clique em **Mostrar cadeias de ligação da base de dados**.

    ![cadeias de ligação da nova base de dados de exemplo](./media/sql-database-get-started/new-sample-db-connection-strings.png)
4. Clique em **Descrição Geral** e, em seguida, clique no nome do seu servidor no painel Essentials.
    
    ![painel essentials da nova base de dados de exemplo](./media/sql-database-get-started/new-sample-db-essentials-pane.png)
5. No painel Essentials do seu servidor, pode ver a base de dados adicionada recentemente.

    ![nova base de dados de exemplo no painel essentials do servidor](./media/sql-database-get-started/new-sample-db-server-essentials-pane.png)

## <a name="connect-and-query-sample-database-using-sql-server-management-studio"></a>Ligar e consultar a base de dados de exemplo com o SQL Server Management Studio

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

## <a name="create-a-new-blank-database-using-sql-server-management-studio"></a>Criar uma nova base de dados vazia com o SQL Server Management Studio

1. No Object Explorer, clique com o botão direito do rato em **Bases de Dados** e, em seguida, clique em **Nova base de dados**.

    ![nova base de dados vazia com ssms](./media/sql-database-get-started/new-blank-database-ssms.png)

    > [!NOTE]
    > Também pode deixar que o SSMS crie um script de base de dados por si de forma a criar uma nova base de dados através de Transact-SQL.
    >

2. Na caixa de diálogo Nova Base de Dados, forneça um nome de base de dados na caixa de texto Nome da base de dados. 

    ![nome da nova base de dados vazia com ssms](./media/sql-database-get-started/new-blank-database-name-ssms.png)

3. Na caixa de diálogo Nova Base de Dados, clique em **Opções** e, em seguida, altere a Edição para **Básica**.

    ![opções da nova base de dados vazia com ssms](./media/sql-database-get-started/new-blank-database-options-ssms.png)

    > [!TIP]
    > Veja as outras opções que pode modificar nesta caixa de diálogo para uma Base de Dados SQL do Azure. Para obter mais informações sobre estas opções, veja o artigo [Criar Base de Dados](https://msdn.microsoft.com/library/dn268335.aspx).
    >

4. Clique em **OK** para criar a base de dados vazia.
5. Quando terminar, atualize o nó Base de Dados no Object Explorer para ver a base de dados vazia que acabou de criar. 

    ![nova base de dados vazia no object explorer](./media/sql-database-get-started/new-blank-database-object-explorer.png)

> [!TIP]
> Pode eliminar bases de dados que não estiver a utilizar para poupar algum dinheiro enquanto está a aprender. No caso das bases de dados de edição Básica, pode restaurá-las no período de sete dias. No entanto, não elimine um servidor. Se o fizer, não poderá recuperar o servidor nem nenhuma das respetivas bases de dados eliminadas.
>


## <a name="next-steps"></a>Passos seguintes
Agora que concluiu este tutorial, existem alguns tutoriais adicionais que poderá explorar para cimentar o que aprendeu neste tutorial. 

* Se quiser começar a explorar a segurança da Base de Dados SQL do Azure, veja o artigo [Introdução à segurança](sql-database-get-started-security.md).
* Se tem conhecimentos de Excel, saiba como [Ligar a uma base de dados SQL no Azure com o Excel](sql-database-connect-excel.md).
* Se estiver pronto para iniciar a codificação, escolha a linguagem de programação em [Bibliotecas de ligação para Base de Dados SQL e SQL Server](sql-database-libraries.md).
* Se quiser mover as suas bases de dados do SQL Server no local para o Azure, veja o artigo [Migrar uma base de dados para a Base de Dados SQL](sql-database-cloud-migrate.md).
* Se pretender carregar alguns dados para uma nova tabela a partir de um ficheiro CSV utilizando a ferramenta de linha de comandos BCP, veja [Carregar dados para a Base de Dados SQL a partir de um ficheiro CSV com o BCP](sql-database-load-from-csv-with-bcp.md).
* Se quiser começar a criar tabelas e outros objetos, veja o tópico "Para criar uma tabela" no artigo [Criar uma tabela](https://msdn.microsoft.com/library/ms365315.aspx).

## <a name="additional-resources"></a>Recursos adicionais

- Para uma descrição geral técnica veja [O que é a Base de Dados SQL?](sql-database-technical-overview.md).
- Para informações sobre preços, veja [preços Base de Dados SQL do Azure](https://azure.microsoft.com/pricing/details/sql-database/).




<!--HONumber=Dec16_HO3-->


