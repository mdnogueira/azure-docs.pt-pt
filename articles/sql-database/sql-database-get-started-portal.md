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
ms.date: 05/03/2017
ms.author: carlrab
ms.translationtype: Human Translation
ms.sourcegitcommit: 7c4d5e161c9f7af33609be53e7b82f156bb0e33f
ms.openlocfilehash: dbed7fe1f6ffdb40d445a66890b63baaf7a57f54
ms.contentlocale: pt-pt
ms.lasthandoff: 05/04/2017


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

   > [!IMPORTANT]
   > Tem de selecionar a base de dados de exemplo neste formulário porque é utilizada no resto deste guia de introdução.
   > 

4. Clique em **Servidor** e preencha o **Novo formulário do servidor** especificando um nome de servidor globalmente único, forneça um nome para o início de sessão de administrador do servidor e, em seguida, especifique a palavra-passe da sua preferência. 

   > [!IMPORTANT]
   > O início de sessão e a palavra-passe de administrador de servidor que especificar aqui serão necessários para iniciar sessão no servidor e nas respetivas bases de dados mais tarde neste guia de introdução. Lembre-se ou grave estas informações para utilização posterior. 
   >  

    ![criar servidor de base de dados](./media/sql-database-get-started-portal/create-database-server.png)
5. Quando concluir o formulário, clique em **Selecionar**.

6. Clique em **Escalão de preço** para especificar o escalão de serviço e o nível de desempenho para a nova base de dados. Utilize o controlo de deslize para selecionar **20 DTUs** e **250** GB de armazenamento. Para obter mais informações sobre DTUs, consulte [O que é um DTU?](sql-database-what-is-a-dtu.md).

    ![criar base de dados-s1](./media/sql-database-get-started-portal/create-database-s1.png)

7. Depois de selecionar a quantidade de DTUs, clique em **Aplicar**.  

8. Agora que concluiu o formulário da Base de Dados SQL do Servidor, clique em **Criar** para aprovisionar a base de dados. O aprovisionamento demora alguns minutos. 

9. Na barra de ferramentas, clique em **Notificações** para monitorizar o processo de implementação.

    ![notificação](./media/sql-database-get-started-portal/notification.png)


## <a name="create-a-server-level-firewall-rule"></a>Criar uma regra de firewall ao nível do servidor

O serviço da Base de Dados SQL cria uma firewall ao nível do servidor, impedindo que as aplicações e ferramentas externas estabeleçam uma ligação ao servidor ou a quaisquer bases de dados no servidor, a menos que seja criada uma regra de firewall para abrir a firewall para endereços IP específicos. Siga estes passos para criar uma [regra de firewall ao nível do servidor da Base de Dados SQL](sql-database-firewall-configure.md) para o endereço IP do seu cliente, para permitir a conectividade externa através da firewall da Base de Dados SQL apenas para o seu endereço IP. 

> [!NOTE]
> A Base de Dados SQL comunica através da porta 1433. Se estiver a tentar ligar a partir de uma rede empresarial, o tráfego de saída através da porta 1433 poderá não ser permitido pela firewall da rede. Se assim for, não poderá ligar ao seu servidor de Base de Dados SQL do Azure, a menos que o departamento de TI abra a porta 1433.
>

1. Depois de concluída a implementação, clique em **Bases de dados SQL** no menu do lado esquerdo e, em seguida, clique em **mySampleDatabase** na página de bases de dados SQL. É aberta uma página de descrição geral para a base de dados que mostra o nome de servidor completamente qualificado (como **mynewserver20170411.database.windows.net**) e fornece opções para configuração adicional.

   > [!IMPORTANT]
   > Irá precisar deste nome de servidor totalmente qualificado para ligar ao seu servidor e às respetivas bases de dados nos seguintes guias de introdução.
   > 

      ![nome do servidor](./media/sql-database-get-started-portal/server-name.png) 

2. Clique em **Configurar firewall do servidor** na barra de ferramentas, conforme mostrado na imagem anterior. É aberta a página **Definições da firewall** do servidor da Base de Dados SQL. 

      ![regra de firewall do servidor](./media/sql-database-get-started-portal/server-firewall-rule.png) 


3. Clique em **Adicionar IP de cliente**, na barra de ferramentas, para adicionar o seu endereço IP atual a uma nova regra de firewall. Uma regra de firewall consegue abrir a porta 1433 para um único endereço IP ou para um intervalo de endereços IP.

4. Clique em **Guardar**. É criada uma regra de firewall ao nível do servidor para a sua porta de abertura 1433 do endereço IP atual no servidor lógico.

      ![configurar regra de firewall do servidor](./media/sql-database-get-started-portal/server-firewall-rule-set.png) 

4. Clique em **OK** e, em seguida, feche a página **Definições da firewall**.

Pode agora ligar ao servidor da Base de Dados SQL e às respetivas bases de dados com o SQL Server Management Studio ou outra ferramenta à sua escolha a partir deste endereço IP com a conta de administrador de servidor criada anteriormente.

> [!IMPORTANT]
> Por predefinição, o acesso através da firewall da Base de Dados SQL está ativado para todos os serviços do Azure. Clique em **DESATIVAR** nesta página para desativar todos os serviços do Azure.

## <a name="query-the-sql-database"></a>Consultar a base de dados SQL

Agora que criou uma base de dados de exemplo no Azure, vamos utilizar a ferramenta de consulta incorporada no portal do Azure para confirmar que se consegue ligar à base de dados e consultar os dados. 

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

Se não precisa destes recursos para outro início rápido/tutorial (consulte [Passos seguintes](#next-steps)), pode eliminá-los ao realizar o seguinte procedimento:


1. No menu do lado esquerdo no portal do Azure, clique em **Grupos de recursos** e, em seguida, clique em **myResourceGroup**. 
2. Na página do grupo de recursos, clique em **Eliminar**, escreva **myResourceGroup** na caixa de texto e, em seguida, clique em **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

Agora que tem uma base de dados, pode ligar e consultar com as suas ferramentas favoritas. Saiba mais ao selecionar a sua ferramenta abaixo:

- [SQL Server Management Studio](sql-database-connect-query-ssms.md)
- [Visual Studio Code](sql-database-connect-query-vscode.md)
- [.NET](sql-database-connect-query-dotnet.md)
- [PHP](sql-database-connect-query-php.md)
- [Node.js](sql-database-connect-query-nodejs.md)
- [Java](sql-database-connect-query-java.md)
- [python](sql-database-connect-query-python.md)
- [Ruby](sql-database-connect-query-ruby.md)

