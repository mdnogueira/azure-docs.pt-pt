---
title: "Portal do Azure: Criar uma única base de dados SQL | Microsoft Docs"
description: "Saiba como criar um servidor lógico da Base de Dados SQL, regras de firewall ao nível do servidor e bases de dados no portal do Azure. Saiba também como consultar uma base de dados SQL do Azure através do portal do Azure."
keywords: tutorial de base de dados sql, criar uma base de dados sql
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: aeb8c4c3-6ae2-45f7-b2c3-fa13e3752eed
ms.service: sql-database
ms.custom: quick start create
ms.workload: data-management
ms.tgt_pltfrm: portal
ms.devlang: na
ms.topic: hero-article
ms.date: 04/03/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 58af25d90b419b3ddb986118a8c9ba3b42aa95a6
ms.lasthandoff: 04/12/2017


---
# <a name="create-an-azure-sql-database-in-the-azure-portal"></a>Criar uma base de dados SQL do Azure no portal do Azure

Este guia de introdução explica como criar uma base de dados SQL no Azure. A Base de Dados SQL do Azure é uma oferta de "Base de dados como Serviço" que lhe permite executar e dimensionar bases de dados do SQL Server altamente disponíveis na cloud. Este guia de introdução mostra-lhe como começar a criar uma base de dados SQL através do portal do Azure.

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="log-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [Portal do Azure](https://portal.azure.com/).

## <a name="create-a-sql-database"></a>Criar uma base de dados SQL

É criada uma base de dados SQL do Azure com um conjunto definido de [recursos de armazenamento e computação](sql-database-service-tiers.md). A base de dados é criada num [Grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) e num [servidor lógico da Base de Dados SQL do Azure](sql-database-features.md). 

Siga estes passos para criar uma base de dados SQL com os dados de exemplo da Adventure Works LT. 

1. Clique no botão **Novo** localizado no canto superior esquerdo do portal do Azure.

2. Selecione **Bases de Dados** na página **Novo** e selecione **Base de Dados SQL** na página **Bases de Dados**.

    ![criar base de dados-1](./media/sql-database-get-started-portal/create-database-1.png)

3. Preencha o formulário da Base de Dados SQL com as informações seguintes, conforme mostrado na imagem anterior:     
   - Nome da base de dados: **mySampleDatabase**
   - Grupo de recursos: **myResourceGroup**
   - Origem: **Exemplo (AdventureWorksLT)**

4. Clique em **Servidor** para criar e configurar o servidor para a nova base de dados. Preencha o **Novo formulário do servidor** especificando um nome de servidor globalmente único, forneça um nome para o início de sessão de administrador do Servidor e, em seguida, especifique a palavra-passe da sua preferência. 

    ![criar servidor de base de dados](./media/sql-database-get-started-portal/create-database-server.png)
5. Clique em **Selecionar**.

6. Clique em **Escalão de preço** para especificar o escalão de serviço e o nível de desempenho para a nova base de dados. Para este guia de introdução, selecione **20 DTUs** e **250** GB de armazenamento

    ![criar base de dados-s1](./media/sql-database-get-started-portal/create-database-s1.png)

7. Clique em **Aplicar**.  

8. Clique em **Criar** para aprovisionar a base de dados. O aprovisionamento demora alguns minutos. 

9. Na barra de ferramentas, clique em **Notificações** para monitorizar o processo de implementação.

    ![notificação](./media/sql-database-get-started-portal/notification.png)


## <a name="create-a-server-level-firewall-rule"></a>Criar uma regra de firewall ao nível do servidor

O serviço da Base de Dados SQL cria uma firewall ao nível do servidor, impedindo que as aplicações e ferramentas externas estabeleçam uma ligação ao servidor ou a quaisquer bases de dados no servidor, a menos que seja criada uma regra de firewall para abrir a firewall para endereços IP específicos. Siga estes passos para criar uma [regra de firewall ao nível do servidor da Base de Dados SQL](sql-database-firewall-configure.md) para o endereço IP do seu cliente, para permitir a conectividade externa através da firewall da Base de Dados SQL apenas para o seu endereço IP. 

1. Depois de concluída a implementação, clique em **Bases de dados SQL** no menu do lado esquerdo e clique na sua base de dados, na página **bases de dados SQL**. É aberta uma página de descrição geral para a base de dados que mostra o nome de servidor completamente qualificado (como **mynewserver20170327.database.windows.net**) e fornece opções para configuração adicional.

      ![regra de firewall do servidor](./media/sql-database-get-started-portal/server-firewall-rule.png) 

2. Clique em **Configurar firewall do servidor** na barra de ferramentas, conforme mostrado na imagem anterior. É aberta a página **Definições da firewall** do servidor da Base de Dados SQL. 

3. Clique em **Adicionar IP de cliente** na barra de ferramentas e, em seguida, clique em **Guardar**. É criada uma regra de firewall ao nível do servidor para o seu endereço IP atual.

      ![configurar regra de firewall do servidor](./media/sql-database-get-started-portal/server-firewall-rule-set.png) 

4. Clique em **OK** e, em seguida, feche a página **Definições da firewall**.

Pode agora ligar ao servidor da Base de Dados SQL e às respetivas bases de dados com o SQL Server Management Studio ou outra ferramenta à sua escolha a partir deste endereço IP com a conta de administrador de Servidor criada anteriormente.

> [!NOTE]
> A Base de Dados SQL comunica através da porta 1433. Se estiver a tentar ligar a partir de uma rede empresarial, o tráfego de saída através da porta 1433 poderá não ser permitido pela firewall da rede. Se assim for, não poderá ligar ao seu servidor de Base de Dados SQL do Azure, a menos que o departamento de TI abra a porta 1433.
>

## <a name="query-the-sql-database"></a>Consultar a base de dados SQL

Quando criámos a nossa base de dados SQL, preenchemos com a base de dados de exemplo **AdventureWorksLT** (uma das opções que selecionámos na IU de Criação anterior neste guia de introdução). Vamos utilizar agora a ferramenta de consulta incorporada no portal do Azure para consultar os dados. 

1. Na página Base de Dados SQL da sua base de dados, clique em **Ferramentas** na barra de ferramentas. É aberta a página **Ferramentas**.

     ![menu ferramentas](./media/sql-database-get-started-portal/tools-menu.png) 

2. Clique em **Editor de consultas (pré-visualização)**, clique na caixa de verificação **Termos de pré-visualização** e, em seguida, clique em **OK**. É aberta a página Editor de consultas.

3. Clique em **Iniciar sessão** e, em seguida, quando lhe for pedido, selecione **Autenticação do SQL Server** e forneça o início de sessão e a palavra-passe de administrador do servidor que criou anteriormente.

    ![início de sessão](./media/sql-database-get-started-portal/login.png) 

4. Clique em **OK** para iniciar sessão.

5. Quando estiver autenticado, escreva a consulta seguinte no painel do editor de consultas.

   ```
   SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
   FROM SalesLT.ProductCategory pc
   JOIN SalesLT.Product p
   ON pc.productcategoryid = p.productcategoryid;
   ```

6. Clique em **Executar** e, em seguida, reveja os resultados da consulta no painel **Resultados**.

    ![Resultados do editor de consultas](./media/sql-database-get-started-portal/query-editor-results.png)

7. Feche a página **Editor de consultas** e a página **Ferramentas**.

## <a name="clean-up-resources"></a>Limpar recursos

Outros guias de introdução nesta coleção são criados através deste guia de introdução. Se pretender continuar a trabalhar com guias de introdução subsequentes, não limpe os recursos criados neste guia de introdução. Se não quiser continuar, utilize os passos seguintes para eliminar todos os recursos criados por este guia de introdução no portal do Azure.

1. No menu do lado esquerdo no portal do Azure, clique em **Grupos de recursos** e, em seguida, clique em **myResourceGroup**. 
2. Na página do grupo de recursos, clique em **Eliminar**, escreva **myResourceGroup** na caixa de texto e, em seguida, clique em **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

- Para ligar e consultar com o SQL Server Management Studio, veja [Ligar e consultar com SSMS](sql-database-connect-query-ssms.md)
- Para ligar e consultar com o Visual Studio Code, veja [Connect and query with Visual Studio Code (Ligar e consultar com o Visual Studio Code)](sql-database-connect-query-vscode.md).
- Para ligar e consultar com .NET, consulte [Connect and query with .NET (Ligar e consultar com .NET)](sql-database-connect-query-dotnet.md).
- Para ligar e consultar com PHP, consulte [Connect and query with PHP (Ligar e consultar com PHP)](sql-database-connect-query-php.md).
- Para ligar e consultar com Node.js, consulte [Connect and query with Node.js (Ligar e consultar com Node.js)](sql-database-connect-query-nodejs.md).
- Para ligar e consultar com Java, consulte [Connect and query with Java (Ligar e consultar com Java)](sql-database-connect-query-java.md).
- Para ligar e consultar com Python, consulte [Connect and query with Python (Ligar e consultar com Python)](sql-database-connect-query-python.md).
- Para ligar e consultar com Ruby, consulte [Connect and query with Ruby (Ligar e consultar com Ruby)](sql-database-connect-query-ruby.md).

