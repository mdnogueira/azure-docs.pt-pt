---
title: "Criar um armazém de dados SQL do Azure - Portal do Azure | Microsoft Docs"
description: "Para o Azure SQL Data Warehouse, crie um SQL server, a regra de firewall ao nível do servidor e um armazém de dados no portal do Azure. Em seguida, consultá-lo."
keywords: "tutorial de armazém de dados SQL, criar um SQL data warehouse"
services: sql-database
documentationcenter: 
author: barbkess
manager: jhubbard
editor: 
ms.service: sql-database
ms.custom: mvc,DBs & servers
ms.workload: Active
ms.tgt_pltfrm: portal
ms.devlang: na
ms.topic: quickstart
ms.date: 11/20/2017
ms.author: barbkess
ms.openlocfilehash: 3a3d077aeb705a996ea82fe7b5e390112712182b
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="create-an-azure-sql-data-warehouse-in-the-azure-portal"></a>Criar um Azure SQL data warehouse no portal do Azure

Este tutorial de introdução cria um armazém de dados utilizando o serviço do Azure SQL Data Warehouse e inicializa-lo com os dados de exemplo AdventureWorksDW. Em seguida, ligar ao armazém de dados e executar uma consulta nos dados. O tutorial utiliza o [portal do Azure](https://portal.azure.com) e [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms.md) (SSMS)

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="before-you-begin"></a>Antes de começar

Antes de começar este início rápido, transfira e instale a versão mais recente do [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms.md) (SSMS).

## <a name="log-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [Portal do Azure](https://portal.azure.com/).

## <a name="create-a-data-warehouse"></a>Criar um armazém de dados

Um armazém de dados SQL do Azure é criado com um conjunto definido de [recursos de computação](performance-tiers.md). A base de dados é criado dentro de um [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) e num [servidor lógico SQL do Azure](../sql-database/sql-database-features.md). 

Siga estes passos para criar um SQL data warehouse que contém os dados de exemplo AdventureWorksDW. 

1. Clique em de **novo** botão no canto superior esquerdo do portal do Azure.

2. Selecione **bases de dados** do **novo** página e selecione **SQL Data Warehouse** em **em destaque** no **novo**página.

    ![criar o armazém de dados vazio](media/load-data-from-azure-blob-storage-using-polybase/create-empty-data-warehouse.png)

3. Preencha o formulário de armazém de dados do SQL Server com as seguintes informações:   

    | Definição | Valor sugerido | Descrição | 
    | ------- | --------------- | ----------- | 
    | **Nome da base de dados** | mySampleDataWarehouse | Para nomes de bases de dados válidos, veja [Database Identifiers](/sql/relational-databases/databases/database-identifiers) (Identificadores de Bases de Dados). Tenha em atenção de que um armazém de dados é um tipo de base de dados.| 
    | **Subscrição** | A sua subscrição  | Para obter detalhes sobre as suas subscrições, veja [Subscriptions](https://account.windowsazure.com/Subscriptions) (Subscrições). |
    | **Grupo de recursos** | myResourceGroup | Para nomes de grupo de recursos válidos, veja [Naming rules and restrictions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Atribuição de nomes de regras e restrições). |
    | **Selecione a origem** | Exemplo | Especifica a carregar uma base de dados de exemplo. Tenha em atenção de que o armazém de dados é um tipo de base de dados. |
    | **Selecionar exemplo** | AdventureWorksDW | Especifica a carregar a base de dados de exemplo AdventureWorksDW.  |

    ![criar o armazém de dados](media/create-data-warehouse-portal/select-sample.png)

4. Clique em **Servidor** para criar e configurar um novo servidor para a nova base de dados. Preencha o **novo formulário de servidor** com as seguintes informações: 

    | Definição | Valor sugerido | Descrição | 
    | ------------ | ------------------ | ------------------------------------------------- | 
    | **Nome do servidor** | Qualquer nome globalmente exclusivo | Para nomes de servidores válidos, veja [Naming rules and restrictions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Atribuição de nomes de regras e restrições). | 
    | **Início de sessão de administrador do servidor** | Qualquer nome válido | Para nomes de início de sessão válidos, veja [Database Identifiers](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers) (Identificadores de Bases de Dados).|
    | **Palavra-passe** | Qualquer palavra-passe válida | A palavra-passe tem de ter, pelo menos, oito carateres e tem de conter carateres de três das seguintes categorias: carateres maiúsculos, carateres minúsculos, números e carateres não alfanuméricos. |
    | **Localização** | Nenhuma localização válida | Para obter mais informações sobre regiões, veja [Azure Regions](https://azure.microsoft.com/regions/) (Regiões do Azure). |

    ![criar o servidor de base de dados](media/load-data-from-azure-blob-storage-using-polybase/create-database-server.png)

5. Clique em **Selecionar**.

6. Clique em **camada de desempenho** para especificar se o armazém de dados está otimizado para elasticidade ou de computação e o número de dados do armazém de unidades. 

7. Para este tutorial, selecione o **otimizado para elasticidade** camada de serviço. O controlo de deslize, por predefinição, está definido como **DW400**.  Tente movê-los e reduzir verticalmente para ver como funciona. 

    ![configurar o desempenho](media/load-data-from-azure-blob-storage-using-polybase/configure-performance.png)

8. Clique em **Aplicar**.

9. Agora que concluiu o formulário da Base de Dados SQL do Servidor, clique em **Criar** para aprovisionar a base de dados. O aprovisionamento demora alguns minutos. 

    ![Clique em criar](media/load-data-from-azure-blob-storage-using-polybase/click-create.png)

10. Na barra de ferramentas, clique em **Notificações** para monitorizar o processo de implementação.
    
     ![notificação](media/load-data-from-azure-blob-storage-using-polybase/notification.png)

## <a name="create-a-server-level-firewall-rule"></a>Criar uma regra de firewall ao nível do servidor

O serviço do SQL Data Warehouse cria uma firewall ao nível do servidor-que impede o ligar ao servidor ou bases de dados no servidor de aplicações externas e as ferramentas. Para ativar a conetividade, pode adicionar regras de firewall que permitem uma conectividade de endereços IP específicos.  Siga estes passos para criar um [regra de firewall ao nível do servidor](../sql-database/sql-database-firewall-configure.md) para o endereço IP do cliente. 

> [!NOTE]
> O SQL Data Warehouse comunica através da porta é 1433. Se estiver a tentar ligar a partir de uma rede empresarial, o tráfego de saída através da porta 1433 não poderá permitido pela firewall da sua rede. Se assim for, não poderá ligar ao servidor da Base de Dados SQL do Azure, a menos que o departamento de TI abra a porta 1433.
>

1. Depois de concluída a implementação, clique em **Bases de dados SQL** no menu do lado esquerdo e, em seguida, clique em **mySampleDatabase** na página **Bases de dados SQL**. Abre a página de descrição geral da base de dados, que mostra o nome de servidor completamente qualificado (tais como **mynewserver 20171113.database.windows.net**) e fornece opções para continuar a configuração. 

2. Copie este nome de servidor totalmente qualificado para utilizar para ligar ao seu servidor e às respetivas bases de dados nos seguintes guias de introdução. Em seguida, clique no nome do servidor para abrir as definições do servidor.

   ![localizar o nome do servidor](media/load-data-from-azure-blob-storage-using-polybase/find-server-name.png) 

3. Clique no nome de servidor para abrir as definições do servidor.

   ![definições do servidor](media/load-data-from-azure-blob-storage-using-polybase/server-settings.png) 

5. Clique em **mostrar as definições da firewall**. É aberta a página **Definições da firewall** do servidor da Base de Dados SQL. 

   ![regra de firewall do servidor](media/load-data-from-azure-blob-storage-using-polybase/server-firewall-rule.png) 

4. Clique em **Adicionar IP de cliente**, na barra de ferramentas, para adicionar o seu endereço IP atual a uma nova regra de firewall. Uma regra de firewall consegue abrir a porta 1433 para um único endereço IP ou para um intervalo de endereços IP.

5. Clique em **Guardar**. É criada uma regra de firewall ao nível do servidor para a sua porta de abertura 1433 do endereço IP atual no servidor lógico.

6. Clique em **OK** e, em seguida, feche a página **Definições da firewall**.

Agora pode ligar ao SQL server e o respetivos armazéns de dados com este endereço IP. A ligação funciona do SQL Server Management Studio ou outra ferramenta de sua escolha. Quando se liga, utilize a conta de ServerAdmin que criou anteriormente.  

> [!IMPORTANT]
> Por predefinição, o acesso através da firewall da Base de Dados SQL está ativado para todos os serviços do Azure. Clique em **OFF** nesta página e, em seguida, clique em **guardar** para desativar a firewall para todos os serviços do Azure.

## <a name="get-the-fully-qualified-server-name"></a>Obter o nome de servidor completamente qualificado

Obter o nome de servidor completamente qualificado para o SQL server no portal do Azure. Mais tarde irá utilizar o nome completamente qualificado ao ligar ao servidor.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Selecione **Bases de Dados SQL** a partir do menu do lado esquerdo e clique na sua base de dados na página **Bases de Dados SQL**. 
3. No painel **Essentials** na página do portal do Azure da sua base de dados, localize e, em seguida, copie o **Nome do servidor**. Neste exemplo, o nome completamente qualificado é mynewserver 20171113.database.windows.net. 

    ![informações da ligação](media/load-data-from-azure-blob-storage-using-polybase/find-server-name.png)  

## <a name="connect-to-the-server-as-server-admin"></a>Ligar ao servidor como administrador do servidor

Esta secção utiliza [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms.md) (SSMS) para estabelecer uma ligação ao servidor SQL do Azure.

1. Abra o SQL Server Management Studio.

2. Na caixa de dialogo **Ligar ao Servidor**, introduza as seguintes informações:

   | Definição       | Valor sugerido | Descrição | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | Tipo de servidor | Motor de base de dados | Este valor é necessário |
   | Nome do servidor | O nome de servidor completamente qualificado | O nome deve ser algo semelhante ao seguinte: **mynewserver 20171113.database.windows.net**. |
   | Autenticação | Autenticação do SQL Server | A Autenticação do SQL é o único tipo de autenticação que configurámos neste tutorial. |
   | Iniciar sessão | A conta de administrador do servidor | Esta é a conta que especificou quando criou o servidor. |
   | Palavra-passe | A palavra-passe da sua conta de administrador do servidor | Esta é a palavra-passe que especificou quando criou o servidor. |

    ![ligar ao servidor](media/load-data-from-azure-blob-storage-using-polybase/connect-to-server.png)

4. Clique em **Ligar**. A janela do Object Explorer é aberta no SSMS. 

5. No Object Explorer, expanda **bases de dados**. Em seguida, expanda **mySampleDatabase** para ver os objetos na sua nova base de dados.

    ![Objetos de base de dados](media/create-data-warehouse-portal/connected.png) 

## <a name="run-some-queries"></a>Executar algumas consultas

O SQL Data Warehouse utiliza T-SQL, como o idioma de consulta. Abra uma janela de consulta e executar algumas consultas de T-SQL, utilize os seguintes passos.

1. Clique com botão direito **mySampleDataWarehouse** e selecione **nova consulta**.  É aberta uma nova janela de consulta.
2. Na janela da consulta, introduza o seguinte comando para ver uma lista de bases de dados.

    ```sql
    SELECT * FROM sys.databases
    ```

3. Clique em **Executar**.  Os resultados da consulta mostram duas bases de dados: **mestre** e **mySampleDataWarehouse**.

    ![Bases de dados de consulta](media/create-data-warehouse-portal/query-databases.png)

4. Para ver alguns dados, utilize o seguinte comando para ver o número de clientes com o último nome do Andrade que têm três elementos subordinados em casa. Os resultados lista seis clientes. 

    ```sql
    SELECT LastName, FirstName FROM dbo.dimCustomer
    WHERE LastName = 'Adams' AND NumberChildrenAtHome = 3;
    ```

    ![Dbo.dimCustomer de consulta](media/create-data-warehouse-portal/query-customer.png)

## <a name="clean-up-resources"></a>Limpar recursos

Estão a ser-lhe cobrados para recursos de computação e os dados que é carregado para o armazém de dados. Estes são cobradas separadamente. 

- Se pretender manter os dados no armazenamento, pode interromper a computação quando não estiverem a utilizar o armazém de dados. Ao colocar em pausa computação só será encargos de armazenamento de dados e pode retomar a computação sempre que estiver pronto para trabalhar com os dados.
- Se pretender remover os encargos futuros, é possível eliminar o armazém de dados. 

Siga estes passos para limpar os recursos que desejar.

1. Início de sessão para o [portal do Azure](https://portal.azure.com), clique no seu armazém de dados.

    ![Limpar recursos](media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

1. Para colocar em pausa computação, clique em de **colocar em pausa** botão. Quando o armazém de dados está em pausa, verá um **iniciar** botão.  Para retomar a computação, clique em **iniciar**.

2. Para remover o armazém de dados, de modo que não lhe será cobrado para computação ou o armazenamento, clique em **eliminar**.

3. Para remover o servidor SQL que criou, clique em **mynewserver 20171113.database.windows.net** na imagem anterior e, em seguida, clique em **eliminar**.  Seja cuidadoso com esta opção como eliminar o servidor irá eliminar todas as bases de dados atribuídos ao servidor.

4. Para remover o grupo de recursos, clique em **myResourceGroup**e, em seguida, clique em **eliminar grupo de recursos**.


## <a name="next-steps"></a>Passos seguintes

Agora que tem uma base de dados, pode ligar e consultar com as suas ferramentas favoritas. Saiba mais ao selecionar a sua ferramenta abaixo:

- [SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md?toc=%2fazure%2fsql-data-warehouse%2ftoc.json)
- [Visual Studio Code](../sql-database/sql-database-connect-query-vscode.md?toc=%2fazure%2fsql-data-warehouse%2ftoc.json)
- [.NET](../sql-database/sql-database-connect-query-dotnet.md?toc=%2fazure%2fsql-data-warehouse%2ftoc.json)
- [PHP](../sql-database/sql-database-connect-query-php.md?toc=%2fazure%2fsql-data-warehouse%2ftoc.json)
- [Node.js](../sql-database/sql-database-connect-query-nodejs.md?toc=%2fazure%2fsql-data-warehouse%2ftoc.json)
- [Java](../sql-database/sql-database-connect-query-java.md?toc=%2fazure%2fsql-data-warehouse%2ftoc.json)
- [python](../sql-database/sql-database-connect-query-python.md?toc=%2fazure%2fsql-data-warehouse%2ftoc.json)
- [Ruby](../sql-database/sql-database-connect-query-ruby.md?toc=%2fazure%2fsql-data-warehouse%2ftoc.json)