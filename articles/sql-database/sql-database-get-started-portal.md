---
title: "Portal do Azure: Criar uma única base de dados SQL | Microsoft Docs"
description: "Crie um servidor lógico da Base de Dados SQL, regras de firewall ao nível do servidor e uma base de dados no portal do Azure e realize consultas à mesma."
keywords: tutorial de base de dados sql, criar uma base de dados sql
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: aeb8c4c3-6ae2-45f7-b2c3-fa13e3752eed
ms.service: sql-database
ms.custom: mvc,DBs & servers
ms.workload: data-management
ms.tgt_pltfrm: portal
ms.devlang: na
ms.topic: quickstart
ms.date: 08/25/2017
ms.author: carlrab
ms.openlocfilehash: a97ff8cbd05eb3cd94b557b4dbeb63634a94ccf8
ms.sourcegitcommit: 1131386137462a8a959abb0f8822d1b329a4e474
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2017
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

2. Selecione **Bases de Dados** na página **Novo** e selecione **Criar na Base de Dados SQL** em **Base de Dados SQL** na página **Novo**.

   ![criar base de dados-1](./media/sql-database-get-started-portal/create-database-1.png)

3. Preencha o formulário da Base de Dados SQL com as informações seguintes, conforme mostrado na imagem anterior:   

   | Definição       | Valor sugerido | Descrição | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **Nome da base de dados** | mySampleDatabase | Para nomes de bases de dados válidos, veja [Database Identifiers](https://docs.microsoft.com/en-us/sql/relational-databases/databases/database-identifiers) (Identificadores de Bases de Dados). | 
   | **Subscrição** | A sua subscrição  | Para obter detalhes sobre as suas subscrições, veja [Subscriptions](https://account.windowsazure.com/Subscriptions) (Subscrições). |
   | **Grupo de recursos**  | myResourceGroup | Para nomes de grupo de recursos válidos, veja [Naming rules and restrictions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Atribuição de nomes de regras e restrições). |
   | **Origem** | Exemplo (AdventureWorksLT) | Carrega o esquema e os dados AdventureWorksLT na nova base de dados |

   > [!IMPORTANT]
   > Tem de selecionar a base de dados de exemplo neste formulário porque é utilizada no resto deste guia de introdução.
   > 

4. No **Servidor**, clique em **Configurar definições necessárias** e preencha o formulário do servidor SQL (servidor lógico) com as informações seguintes, conforme mostrado na imagem anterior:   

   | Definição       | Valor sugerido | Descrição | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **Nome do servidor** | Qualquer nome globalmente exclusivo | Para nomes de servidores válidos, veja [Naming rules and restrictions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Atribuição de nomes de regras e restrições). | 
   | **Início de sessão de administrador do servidor** | Qualquer nome válido | Para nomes de início de sessão válidos, veja [Database Identifiers](https://docs.microsoft.com/en-us/sql/relational-databases/databases/database-identifiers) (Identificadores de Bases de Dados). |
   | **Palavra-passe** | Qualquer palavra-passe válida | A sua palavra-passe deve ter, pelo menos, oito carateres e deve conter carateres de três das seguintes categorias: carateres maiúsculos, carateres minúsculos, números e carateres não alfanuméricos. |
   | **Subscrição** | A sua subscrição | Para obter detalhes sobre as suas subscrições, veja [Subscriptions](https://account.windowsazure.com/Subscriptions) (Subscrições). |
   | **Grupo de recursos** | myResourceGroup | Para nomes de grupo de recursos válidos, veja [Naming rules and restrictions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Atribuição de nomes de regras e restrições). |
   | **Localização** | Nenhuma localização válida | Para obter mais informações sobre regiões, veja [Azure Regions](https://azure.microsoft.com/regions/) (Regiões do Azure). |

   > [!IMPORTANT]
   > O início de sessão e a palavra-passe de administrador de servidor que especificar aqui serão necessários para iniciar sessão no servidor e nas respetivas bases de dados mais tarde neste guia de introdução. Lembre-se ou grave estas informações para utilização posterior. 
   >  

   ![criar servidor de base de dados](./media/sql-database-get-started-portal/create-database-server.png)

5. Quando concluir o formulário, clique em **Selecionar**.

6. Clique em **Escalão de preço** para especificar a camada de serviço, o número de DTUs e a quantidade de armazenamento. Explore as opções para a quantidade de DTUs e de armazenamento que tem disponível para cada camada de serviço. 

   > [!IMPORTANT]
   > \*Tamanhos de armazenamento maiores do que a quantidade de armazenamento incluído estão em pré-visualização e são aplicados custos adicionais. Para detalhes, ver os preços da [Base de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/). 
   >
   >\* No escalão Premium, mais de 1 TB de armazenamento estão atualmente disponível nas seguintes regiões: E.U.A. Leste 2, E.U.A. Oeste, Gov (US) - Virginia, Europa Ocidental, Alemanha Central, Sudeste Asiático, Leste do Japão, Leste da Austrália, Canadá Central e Leste do Canadá. Ver [Limitações Atuais P11-P15](sql-database-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  
   > 

7. Para este tutorial de início rápido, selecione o escalão de preço **Standard** e, em seguida, utilize o controlo de deslize para selecionar **100 DTUs (S3)** e **400** GB de armazenamento.

   ![criar base de dados-s1](./media/sql-database-get-started-portal/create-database-s1.png)

8. Aceitd os termos de pré-visualização para utilizar a opção **Adicionar ao Armazenamento**. 

   > [!IMPORTANT]
   > \*Tamanhos de armazenamento maiores do que a quantidade de armazenamento incluído estão em pré-visualização e são aplicados custos adicionais. Para detalhes, ver os preços da [Base de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/). 
   >
   >\* No escalão Premium, mais de 1 TB de armazenamento estão atualmente disponível nas seguintes regiões: E.U.A. Leste 2, E.U.A. Oeste, Gov (US) - Virginia, Europa Ocidental, Alemanha Central, Sudeste Asiático, Leste do Japão, Leste da Austrália, Canadá Central e Leste do Canadá. Ver [Limitações Atuais P11-P15](sql-database-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  
   > 

9. Depois de selecionar o escalão de servidor, o número de DTUs e a quantidade de armazenamento, clique em **Aplicar**.  

10. Agora que concluiu o formulário da Base de Dados SQL do Servidor, clique em **Criar** para aprovisionar a base de dados. O aprovisionamento demora alguns minutos. 

11. Na barra de ferramentas, clique em **Notificações** para monitorizar o processo de implementação.
    
     ![notificação](./media/sql-database-get-started-portal/notification.png)

## <a name="create-a-server-level-firewall-rule"></a>Criar uma regra de firewall ao nível do servidor

O serviço da Base de Dados SQL cria uma firewall ao nível do servidor, impedindo que as aplicações e ferramentas externas estabeleçam uma ligação ao servidor ou a quaisquer bases de dados no servidor, a menos que seja criada uma regra de firewall para abrir a firewall para endereços IP específicos. Siga estes passos para criar uma [regra de firewall ao nível do servidor da Base de Dados SQL](sql-database-firewall-configure.md) para o endereço IP do seu cliente, para permitir a conectividade externa através da firewall da Base de Dados SQL apenas para o seu endereço IP. 

> [!NOTE]
> A Base de Dados SQL comunica através da porta 1433. Se estiver a tentar ligar a partir de uma rede empresarial, o tráfego de saída através da porta 1433 poderá não ser permitido pela firewall da rede. Se assim for, não poderá ligar ao servidor da Base de Dados SQL do Azure, a menos que o departamento de TI abra a porta 1433.
>

1. Depois de concluída a implementação, clique em **Bases de dados SQL** no menu do lado esquerdo e, em seguida, clique em **mySampleDatabase** na página **Bases de dados SQL**. É aberta uma página de descrição geral para a base de dados que mostra o nome de servidor completamente qualificado (como **mynewserver-20170824.database.windows.net**) e oferece opções para configuração adicional. 

2. Copie este nome de servidor totalmente qualificado para utilizar para ligar ao seu servidor e às respetivas bases de dados nos seguintes guias de introdução. 

   ![nome do servidor](./media/sql-database-get-started-portal/server-name.png) 

3. Clique em **Configurar firewall do servidor** na barra de ferramentas, conforme mostrado na imagem anterior. É aberta a página **Definições da firewall** do servidor da Base de Dados SQL. 

   ![regra de firewall do servidor](./media/sql-database-get-started-portal/server-firewall-rule.png) 

4. Clique em **Adicionar IP de cliente**, na barra de ferramentas, para adicionar o seu endereço IP atual a uma nova regra de firewall. Uma regra de firewall consegue abrir a porta 1433 para um único endereço IP ou para um intervalo de endereços IP.

5. Clique em **Guardar**. É criada uma regra de firewall ao nível do servidor para a sua porta de abertura 1433 do endereço IP atual no servidor lógico.

6. Clique em **OK** e, em seguida, feche a página **Definições da firewall**.

Pode agora ligar ao servidor da Base de Dados SQL e às respetivas bases de dados com o SQL Server Management Studio ou outra ferramenta à sua escolha a partir deste endereço IP com a conta de administrador de servidor criada anteriormente.

> [!IMPORTANT]
> Por predefinição, o acesso através da firewall da Base de Dados SQL está ativado para todos os serviços do Azure. Clique em **DESATIVAR** nesta página para desativar todos os serviços do Azure.
>

## <a name="query-the-sql-database"></a>Consultar a base de dados SQL

Agora que criou uma base de dados de exemplo no Azure, vamos utilizar a ferramenta de consulta incorporada no portal do Azure para confirmar que se consegue ligar à base de dados e consultar os dados. 

1. Na página Base de Dados SQL da sua base de dados, clique em **Ferramentas** na barra de ferramentas e, em seguida, clique em **Editor de consultas (pré-visualização)**.

   ![menu ferramentas](./media/sql-database-get-started-portal/tools-menu.png) 

2. Clique a caixa de verificação **Termos de pré-visualização** e, em seguida, clique em **OK**. É aberta a página Editor de consultas.

3. Clique em **Iniciar sessão**, reveja as informações de início de sessão e, em seguida, clique em **OK** para iniciar sessão com a autenticação do SQL server com o início de sessão de administrador de servidor e a palavra-passe que criou anteriormente.

   ![início de sessão](./media/sql-database-get-started-portal/login.png) 

4. Clique em **OK** para iniciar sessão.

5. Quando estiver autenticado como **ServerAdmin**, escreva a consulta seguinte no painel do editor de consultas.

   ```sql
   SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
   FROM SalesLT.ProductCategory pc
   JOIN SalesLT.Product p
   ON pc.productcategoryid = p.productcategoryid;
   ```

6. Clique em **Executar** e, em seguida, reveja os resultados da consulta no painel **Resultados**.

   ![Resultados do editor de consultas](./media/sql-database-get-started-portal/query-editor-results.png)

7. Feche a página **Editor de consultas**, clique em **OK** para eliminar as edições não guardadas e, em seguida, feche a página **ferramentas**.

## <a name="clean-up-resources"></a>Limpar recursos

Guarde estes recursos se pretender aceder aos [Próximos passos](#next-steps) e saiba como ligar e consultar a base de dados utilizando um número de métodos diferentes. Se, no entanto, pretender eliminar os recursos que criou neste início rápido, utilize os seguintes passos. 


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
