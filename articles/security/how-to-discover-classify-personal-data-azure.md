---
title: Detetar, identificar e classificar os dados pessoais no Microsoft Azure | Microsoft Docs
description: Saiba mais sobre a procurar, classificar, detetar e identificar os dados
services: security
documentationcenter: na
author: barclayn
manager: MBaldwin
editor: TShinder
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: barclayn
ms.custom: 
ms.openlocfilehash: d60a5ca6229939e5d368c216688e4964261f85c7
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="discover-identify-and-classify-personal-data-in-microsoft-azure"></a>Detetar, identificar e classificar os dados pessoais no Microsoft Azure

Este artigo fornece orientação sobre como detetar, identificar e classificar os dados pessoais nas várias ferramentas do Azure e serviços, incluindo a utilização de base de dados SQL do Azure Active Directory do catálogo de dados do Azure, Power Query para clusters do Hadoop no HDInsight do Azure, Azure Proteção de informações da Azure Search, consultas e SQL Server para a base de dados do Azure Cosmos.

## <a name="scenario-problem-statement-and-goal"></a>Cenário, declaração de problema e objetivos

Uma empresa com base em E.U.A. desporto recolhe uma variedade de dados pessoais e outros. Isto inclui dados de funcionários e clientes. A empresa mantém-na várias bases de dados e armazena-os em várias localizações diferentes no seu ambiente do Azure. Para além de vender equipamento desporto, estes também alojarem e gerir o registo de eventos athletic elite em todo o mundo, incluindo no EU, e em alguns casos, os dados de clientes que possam recolher incluem informações médicas.

Uma vez que a empresa aloja muitos tours bicycling internacionais todos os anos e tiver uma equipa contingent em localizações em torno de todo o mundo, alguns dos conjuntos de dados são bastante grande. A empresa também tem aplicações incorporadas do programador, que são utilizadas por clientes e empregados.

A empresa pretende resolver os problemas seguintes:

- Dados pessoais ao cliente e o empregado tem de ser classificados/único os outros dados da empresa recolhe para garantir a segurança e de acesso corretos.
- O administrador de dados tem de detetar facilmente a localização dos dados pessoais do cliente em várias áreas do ambiente do Azure.
- Dados pessoais ao cliente e o empregado que aparece no partilhada documentos e devem ser assinalada como comunicações por e-mail para ajudar a garantir que é mantida segura.
- Os programadores de aplicações da empresa precisam de uma forma de procurar facilmente os dados pessoais ao cliente e do empregado na respetiva web apps e móveis.
- Os programadores também tem de consultar os seus dados pessoais da base de dados do documento.

### <a name="company-goals"></a>Objetivos da empresa

- Todos os dados pessoais de cliente e o empregado tem de ser etiquetados/anotados no catálogo de dados do Azure para que podem ser encontrados facilmente. Idealmente, cliente e o empregado dados pessoais são etiquetados/anotado separadamente.
- Dados pessoais dos perfis de utilizador do cliente e dos empregados e informações de trabalho que reside no Azure Active Directory tem de ser facilmente localizados.
- Dados pessoais que residem em várias bases de dados do SQL Server tem de ser consultados facilmente. 
- Alguns dos grandes conjuntos de dados da empresa são geridos através do Azure HDInsight e armazenados no Hadoop. Estes tem de ser importados para o Excel para que podem ser consultadas para dados pessoais.
- Dados pessoais partilhados em documentos e comunicações por e-mail devem ser classificados, etiqueta e mantidos seguros com o Azure Information Protection.
- Os programadores de aplicações da empresa tem de ser capazes de detetar o cliente e o empregado dados pessoais nas aplicações que compilou, podem fazer com o Azure Search.
- Os programadores devem ser capazes de localizar dados pessoais na respetiva base de dados de documento.

## <a name="azure-active-directory-data-discovery"></a>Do Azure Active Directory: Deteção de dados

[Azure Active Directory](https://azure.microsoft.com/services/active-directory/) é baseado na nuvem, o multi-inquilino diretório e identidade do serviço de gestão da Microsoft. Pode localizar os perfis de utilizador do cliente e dos empregados e informações de trabalho de utilizador que contenham dados pessoais no seu [do Azure Active Directory](https://azure.microsoft.com/services/active-directory/) ambiente (AAD), utilizando o [portal do Azure](https://portal.azure.com/).

Isto é particularmente útil se pretender localizar ou alterar os dados pessoais para um utilizador específico. Também pode adicionar ou alterar o perfil de utilizador e informação de trabalho. Tem de iniciar sessão com uma conta que seja um administrador global do diretório.

### <a name="how-do-i-locate-or-view-user-profile-and-work-information"></a>Como localizar ou ver o perfil de utilizador e a informação de trabalho?

1. Iniciar sessão para o [portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global do diretório.

2. Selecione **mais serviços**, introduza **utilizadores e grupos** na caixa de texto e, em seguida, selecione **Enter**.

   ![como localizar o perfil de utilizador e informação de trabalho](media/how-to-discover-classify-personal-data-azure/user-profile.png)

3. No **utilizadores e grupos** painel, selecione **utilizadores**.

  ![Abrir utilizadores e grupo](media/how-to-discover-classify-personal-data-azure/users-groups.png)

4. No **utilizadores e grupos - os utilizadores** painel, selecione um utilizador na lista e, em seguida, no painel de para o utilizador selecionado, selecione **perfil** para ver informações de perfil de utilizador que poderão conter dados pessoais.

  ![Selecionar utilizador](media/how-to-discover-classify-personal-data-azure/select-user.png)

5. Se precisar de adicionar ou alterar as informações do perfil de utilizador, pode fazê-lo e, em seguida, na barra de comandos, selecione **guardar.**
6. No painel de para o utilizador selecionado, selecione **trabalhar informações** para ver informações de trabalho de utilizador que poderão conter dados pessoais.

 ![visualizar informações de trabalho](media/how-to-discover-classify-personal-data-azure/work-info.png)

7. Se precisar de adicionar ou alterar as informações de trabalho do utilizador, pode fazê-lo e, em seguida, na barra de comandos, selecione **guardar.**

## <a name="azure-sql-database-data-discovery"></a>Base de dados SQL do Azure: Deteção de dados

[Base de dados SQL do Azure](https://azure.microsoft.com/services/sql-database/?v=16.50) é uma base de dados de nuvem que ajuda os programadores criar e manter as aplicações. Dados pessoais podem ser encontrados na [SQL Database do Azure](https://azure.microsoft.com/services/sql-database/?v=16.50) através de consultas de SQL Server padrão. Consulta elástica do Azure SQL (pré-visualização) permite aos utilizadores efetuar consultas entre bases de dados.

Um detalhadas [base de dados SQL](../sql-database/sql-database-technical-overview.md) tutorial explica muitos aspetos da utilização de uma base de dados do SQL Server, incluindo como criar uma e como executar consultas de dados. Segue-se um resumo das informações disponíveis no tutorial com ligações para secções específicas.

### <a name="how-do-i-build-a-sql-database"></a>Como posso criar uma base de dados do SQL Server?

Existem três formas de fazê-lo:

- Uma base de dados SQL do Azure pode ser criada no [portal do Azure](https://portal.azure.com/). O tutorial, irá utilizar um conjunto específico de recursos de armazenamento e computação dentro de um grupo de recursos e o servidor lógico. Irá utilizar dados de exemplo de uma empresa fictícia denominada AdventureWorks. Também pode criar uma regra de firewall ao nível do servidor. Para saber como fazê-lo, visite o [criar uma base de dados SQL do Azure no portal do Azure](../sql-database/sql-database-get-started-portal.md) tutorial.

  ![Criar base de dados SQL do Azure](media/how-to-discover-classify-personal-data-azure/create-database.png)
- Também é possível criar uma base de dados do SQL Server no [Shell de nuvem do Azure](https://azure.microsoft.com/features/cloud-shell/) CLI, uma ferramenta de linha de comandos baseada no browser. A ferramenta está disponível no portal do Azure e pode ser executada diretamente a partir daí. Neste tutorial, inicie a ferramenta, definir as variáveis do script, crie um grupo de recursos e o servidor lógico e configurar uma regra de firewall do servidor. Em seguida, crie uma base de dados com dados de exemplo. Para saber como criar a base de dados desta forma, visite o [criar uma base de dados SQL do Azure único utilizando a CLI do Azure](../sql-database/sql-database-get-started-cli.md) tutorial.

  ![Tutorial CLIT](media/how-to-discover-classify-personal-data-azure/cli-tutorial.png)

>[!NOTE]
CLI do Azure costuma é utilizada por administradores de Linux e os programadores. Alguns utilizadores encontrarão-lo mais fácil e mais intuitiva de PowerShell, que é a terceira opção.

- Por fim, pode criar uma base de dados do SQL Server com o PowerShell, que é uma ferramenta de linha de comandos/script utilizada para criar e gerir o Azure e outros recursos. Neste tutorial, inicie a ferramenta, definir as variáveis do script, crie um grupo de recursos e o servidor lógico e configurar uma regra de firewall do servidor. Em seguida, irá criar uma base de dados com dados de exemplo.

O tutorial requer o Azure PowerShell versão 4.0 ou posterior do módulo. Execute Get-Module - ListAvailable AzureRM para localizar a versão. Se precisar de instalar ou atualizar, consulte o módulo de instalar o Azure PowerShell.

```PowerShell
New-AzureRmSQLDatabase -ResourceGroupName $resourcegroupname `
-ServerName $servername `
-DatabaseName $databasename `
-RequestedServiceObjectiveName "s0"
```

Para saber como criar a base de dados desta forma, visite o [criar uma base de dados SQL do Azure único através do Powershell](../sql-database/sql-database-get-started-powershell.md) tutorial.

>[!Note]
Administradores de Windows tendem a utilizar o PowerShell, mas algumas delas preferem CLI do Azure.

### <a name="how-do-i-search-for-personal-data-in-sql-database-in-the-azure-portal"></a>Como procurar para os dados pessoais na base de dados do SQL no portal do Azure? * *

Pode utilizar a ferramenta de editor de consulta incorporada dentro do portal do Azure para procurar dados pessoais. Irá iniciar sessão para a ferramenta utilizando o seu início de sessão de administrador de servidor SQL e a palavra-passe e, em seguida, introduza uma consulta.

  ![SQL Server de pesquisa através do portal](media/how-to-discover-classify-personal-data-azure/search-sql-portal.png)

Passo 5 do tutorial mostra um exemplo de consulta no painel do editor de consulta, mas não focar-se nas informações pessoais ou confidenciais (também combina os dados de duas tabelas e cria os aliases para a coluna de origem no conjunto de dados que está a ser devolvido). A seguinte captura de ecrã mostra a consulta do passo 5, bem como o painel de resultados devolvido:

  ![editor de consultas](media/how-to-discover-classify-personal-data-azure/query-editor.png)

Se a base de dados foi chamado MyTable, uma consulta de exemplo para obter informações pessoais poderão incluir o nome, o número de Segurança Social e o número de ID e iria ter este aspeto:

"SELECIONAR nome, SSN, ID número de MyTable"

Pretende executar a consulta e, em seguida, ver os resultados de **resultados** painel.

Para obter mais informações sobre como consultar uma base de dados do SQL Server no portal do Azure, visite o [consultar a base de dados do SQL Server](../sql-database/sql-database-get-started-portal.md) secção do tutorial.

### <a name="how-do-i-search-for-data-across-multiple-databases"></a>Como posso procurar dados em várias bases de dados?

Consulta SQL elástica (pré-visualização) permite-lhe efetuar entre bases de dados e várias consultas de base de dados e devolver um resultado único. O [descrição geral do tutorial](../sql-database/sql-database-elastic-query-overview.md) inclui uma descrição detalhada dos cenários e explica a diferença entre a criação de partições vertical e horizontal da base de dados. Criação de partições horizontais é chamada "fragmentação."

  ![Criação de partições vertical](media/how-to-discover-classify-personal-data-azure/vertical-partition.png)

  ![criação de partições horizontais](media/how-to-discover-classify-personal-data-azure/horizontal.png)

Para começar, visite o [descrição geral da consulta elástico de SQL Database do Azure (pré-visualização)](../sql-database/sql-database-elastic-query-overview.md) página.

#### <a name="power-query-for-importing-azure-hdinsight-hadoop-clusters-data-discovery-for-large-data-sets"></a>Power Query (para importar do Azure HDInsight Hadoop clusters): deteção de dados de grandes conjuntos de dados

Hadoop é um serviço de armazenamento e processamento do Apache para grandes conjuntos de dados, que são analisados e armazenadas nos clusters do Hadoop de open source. [O Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) permite aos utilizadores trabalhar com clusters do Hadoop no Azure. Power Query é um suplemento do Excel que, entre outras coisas, ajuda os utilizadores a detetar os dados de diferentes origens.

Dados pessoais associados com clusters do Hadoop no Azure HDInsight podem ser importados para o Excel com o Power Query. Depois dos dados no Excel pode utilizar uma consulta para identificá-lo.

#### <a name="how-do-i-use-excel-power-query-to-import-hadoop-clusters-in-azure-hdinsight-into-excel"></a>Como utilizar o Excel Power Query para importar os clusters do Hadoop no Azure HDInsight para o Excel?

Um tutorial do HDInsight irá guiá-lo este processo completo. Explica os pré-requisitos e inclui uma ligação para uma [introdução ao Azure HDInsight](../hdinsight/hadoop/apache-hadoop-linux-tutorial-get-started.md) tutorial. Instruções abrangem Excel 2016, bem como 2013 e 2010 (passos são ligeiramente diferentes para as versões mais antigas do Excel). Se não tiver o suplemento do Excel Power Query, o tutorial mostra como a obter. O tutorial começar no Excel e tem de ter uma conta de armazenamento de Blobs do Azure associada ao seu cluster.

  ![A consulta no Excel](media/how-to-discover-classify-personal-data-azure/excel.png)

Para saber como fazê-lo, visite o [ligar o Excel ao Hadoop com o Power Query](../hdinsight/hadoop/apache-hadoop-connect-excel-power-query.md) tutorial.

Origem: [Connect Excel ao Hadoop com o Power Query](../hdinsight/hadoop/apache-hadoop-connect-excel-power-query.md)

## <a name="azure-information-protection-personal-data-classification-for-documents-and-email"></a>Do Azure Information Protection: classificação de dados pessoais para e-mail e documentos

[O Azure Information Protection](https://www.microsoft.com/cloud-platform/azure-information-protection) pode ajuda os clientes do Azure aplicar etiquetas para classificar e proteger documentos partilhados internamente ou externamente e comunicações de e-mail. Alguns destes itens podem conter informações pessoais dos funcionários ou cliente. Regras e as condições podem ser definidas automaticamente ou manualmente, por administradores ou pelos utilizadores. Por exemplo, se um utilizador está a guardar um documento que inclui informações de cartão de crédito, ele ou ela teria Consulte uma recomendação de etiqueta configurada pelo administrador.

### <a name="how-do-i-try-it"></a>Como tente-lo?

Se quiser dar Azure Information Protection tentar ver se pode ser uma opção para a sua organização, visite o [tutorial de início rápido](https://docs.microsoft.com/information-protection/get-started/infoprotect-quick-start-tutorial). -Orienta cinco passos básicos — da instalação para configurar a política para ver a classificação, etiquetagem e partilha em ação — e que devem demorar menos de meia hora.

### <a name="how-do-i-deploy-it"></a>Como implementá-la?

Se gostaria de implementar o Azure Information Protection na sua organização, visite o [plano de implementação para classificação, etiquetagem e proteção](https://docs.microsoft.com/information-protection/plan-design/deployment-roadmap).

### <a name="is-there-anything-else-i-should-know"></a>É tudo o resto que deve sei?

Para obter informações complementares que irão ajudar a analisar como configurá-lo, aceda a [preparada, configurar, proteger!](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/21/azure-information-protection-ready-set-protect/)
mensagem de blogue. E verifique a Saiba mais ligações listadas abaixo para obter mais informações sobre o Azure Information Protection.

## <a name="azure-search-data-discovery-for-developer-apps"></a>Pesquisa do Azure: deteção de dados para programadores de aplicações

[A pesquisa do Azure](https://azure.microsoft.com/services/search/) é uma solução de pesquisa em nuvem para programadores e fornece uma experiência de pesquisa avançada de dados para as suas aplicações. A pesquisa do Azure permite-lhe localizar dados em índices definido pelo utilizador, tenham origem a partir do Azure Cosmo DB, SQL Database do Azure, Blob Storage do Azure, Table storage do Azure ou dados JSON de cliente personalizado. Também pode estrutura consultas Lucene utilizando a API REST da Azure Search para procurar tipos de dados pessoais ou os dados pessoais do indivíduos específicos. As funcionalidades incluem a pesquisa em texto completo, sintaxe de consulta simples e consulta lucene. 

## <a name="how-do-i-use-sql-to-query-data"></a>Como utilizar o SQL Server para consultar dados?

Lugar as noções básicas, visite o [BD do Azure CosmosD: como consultar o SQL Server a utilizar](../cosmos-db/tutorial-query-documentdb.md) tutorial. O tutorial fornece um documento de exemplo e dois exemplos de consultas SQL e os resultados.

Para obter mais orientações aprofundadas sobre a criação de consultas SQL, visite [as consultas SQL para a API de base de dados de documento do Azure Cosmos DB.](../cosmos-db/documentdb-sql-query.md)

Se estiver familiarizado com a base de dados do Azure Cosmos e gostaria de saber como criar uma base de dados, adicionar uma coleção e adicionar dados, visite o [BD do Azure Cosmos: criar uma aplicação de web API do DocumentDB](../cosmos-db/create-documentdb-dotnet.md) tutorial de início rápido. Se gostaria de fazê-lo num idioma diferente do .NET, tais como o Java ou Python, escolha o idioma preferencial da depois de obter ao site.

## <a name="next-steps"></a>Passos seguintes

[Base de Dados SQL do Azure](https://azure.microsoft.com/services/sql-database/?v=16.50)

[O que é a Base de Dados SQL?](../sql-database/sql-database-technical-overview.md)

[SQL Server da base de dados Editor de consultas disponíveis no portal do Azure] (https://azure.microsoft.com/blog/t-sql-query-editor-in-browser-azure-portal/)

[O que é o Azure Information Protection?](https://docs.microsoft.com/information-protection/understand-explore/what-is-information-protection)

[O que é o Azure Rights Management?](https://docs.microsoft.com/information-protection/understand-explore/what-is-azure-rms)

[O Azure Information Protection: Pronto, definir, proteger!](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/21/azure-information-protection-ready-set-protect/)
