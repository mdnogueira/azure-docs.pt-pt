---
title: Migrar o servidor de SQL Server DB para SQL Database do Azure | Microsoft Docs
description: Saiba como migrar a base de dados do SQL Server a SQL Database do Azure.
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: mvc,migrate
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: Active
ms.date: 09/01/2017
ms.author: carlrab
ms.openlocfilehash: 526222944974c08f92aec2a8418e9b42401bc4d3
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="migrate-your-sql-server-database-to-azure-sql-database"></a>Migrar a base de dados do SQL Server a SQL Database do Azure

Mover o SQL Server a base de dados para a SQL Database do Azure é tão simple como criar uma base de dados do SQL Server em branco no Azure e, em seguida, utilizar o [Assistente de migração de dados](https://www.microsoft.com/download/details.aspx?id=53595) (DMA) para importar a base de dados para o Azure. Neste tutorial, saiba como:

> [!div class="checklist"]
> * Criar uma base de dados SQL do Azure vazio no portal do Azure (utilizar um servidor de base de dados do Azure SQL novo ou existente)
> * Criar uma firewall ao nível do servidor no portal do Azure (se não anteriormente criado)
> * Utilize o [Assistente de migração de dados](https://www.microsoft.com/download/details.aspx?id=53595) (DMA) para importar a base de dados do SQL Server para a base de dados SQL do Azure em branco 
> * Utilize [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) para alterar as propriedades de base de dados.

Se não tiver uma subscrição do Azure, [criar uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, confirme que conclui os pré-requisitos seguintes:

- Instalou a versão mais recente do [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).  
- Instalou a versão mais recente do [Assistente de migração de dados](https://www.microsoft.com/download/details.aspx?id=53595) (DMA).
- Identificou e tem acesso a uma base de dados para migrar. Este tutorial utiliza o [do SQL Server 2008-R2 base de dados AdventureWorks OLTP](https://msftdbprodsamples.codeplex.com/releases/view/59211) numa instância do SQL Server 2008-R2 ou mais recente, mas pode utilizar qualquer base de dados da sua preferência. Para corrigir problemas de compatibilidade, utilize [SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)

## <a name="log-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [Portal do Azure](https://portal.azure.com/).

## <a name="create-a-blank-sql-database"></a>Criar uma base de dados do SQL Server em branco

É criada uma base de dados SQL do Azure com um conjunto definido de [recursos de armazenamento e computação](sql-database-service-tiers.md). A base de dados é criada num [Grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) e num [servidor lógico da Base de Dados SQL do Azure](sql-database-features.md). 

Siga estes passos para criar uma base de dados do SQL Server em branco. 

1. Clique no botão **Novo** localizado no canto superior esquerdo do portal do Azure.

2. Selecione **Bases de Dados** na página **Novo** e selecione **Criar na Base de Dados SQL** em **Base de Dados SQL** na página **Novo**.

   ![Criar base de dados vazio](./media/sql-database-design-first-database/create-empty-database.png)

3. Preencha o formulário da Base de Dados SQL com as informações seguintes, conforme mostrado na imagem anterior:   

   | Definição       | Valor sugerido | Descrição | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **Nome da base de dados** | mySampleDatabase | Para nomes de bases de dados válidos, veja [Database Identifiers](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers) (Identificadores de Bases de Dados). | 
   | **Subscrição** | A sua subscrição  | Para obter detalhes sobre as suas subscrições, veja [Subscriptions](https://account.windowsazure.com/Subscriptions) (Subscrições). |
   | **Grupo de recursos** | myResourceGroup | Para nomes de grupo de recursos válidos, veja [Naming rules and restrictions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Atribuição de nomes de regras e restrições). |
   | **Selecione a origem** | Base de dados em branco | Especifica que deve ser criada uma base de dados em branco. |

4. Clique em **Servidor** para criar e configurar um novo servidor para a nova base de dados. Preencha o **novo formulário de servidor** com as seguintes informações: 

   | Definição       | Valor sugerido | Descrição | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **Nome do servidor** | Qualquer nome globalmente exclusivo | Para nomes de servidores válidos, veja [Naming rules and restrictions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Atribuição de nomes de regras e restrições). | 
   | **Início de sessão de administrador do servidor** | Qualquer nome válido | Para nomes de início de sessão válidos, veja [Database Identifiers](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers) (Identificadores de Bases de Dados).|
   | **Palavra-passe** | Qualquer palavra-passe válida | A palavra-passe tem de ter, pelo menos, oito carateres e tem de conter carateres de três das seguintes categorias: carateres maiúsculos, carateres minúsculos, números e carateres não alfanuméricos. |
   | **Localização** | Nenhuma localização válida | Para obter mais informações sobre regiões, veja [Azure Regions](https://azure.microsoft.com/regions/) (Regiões do Azure). |

   ![criar servidor de base de dados](./media/sql-database-design-first-database/create-database-server.png)

5. Clique em **Selecionar**.

6. Clique em **Escalão de preço** para especificar a camada de serviço, o número de DTUs e a quantidade de armazenamento. Explore as opções para o número de DTUs e de armazenamento que está disponível para si para cada camada de serviço. 

7. Para este tutorial, selecione o **padrão** camada de serviço e, em seguida, utilize o controlo de deslize para selecionar **100 DTUs (S3)** e **400** GB de armazenamento.

   ![criar base de dados-s1](./media/sql-database-design-first-database/create-empty-database-pricing-tier.png)

8. Aceitd os termos de pré-visualização para utilizar a opção **Adicionar ao Armazenamento**. 

   > [!IMPORTANT]
   > \*Tamanhos de armazenamento maiores do que a quantidade de armazenamento incluído estão em pré-visualização e são aplicados custos adicionais. Para detalhes, ver os preços da [Base de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/). 
   >
   >\* No escalão Premium, mais de 1 TB de armazenamento estão atualmente disponível nas seguintes regiões: E.U.A. Leste 2, E.U.A. Oeste, Gov (US) - Virginia, Europa Ocidental, Alemanha Central, Sudeste Asiático, Leste do Japão, Leste da Austrália, Canadá Central e Leste do Canadá. Ver [Limitações Atuais P11-P15](sql-database-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  
   > 

9. Depois de selecionar o escalão de servidor, o número de DTUs e a quantidade de armazenamento, clique em **Aplicar**.  

10. Selecione um **agrupamento** para a base de dados em branco (para este tutorial, utilize o valor predefinido). Para obter mais informações sobre agrupamentos, consulte [agrupamentos](https://docs.microsoft.com/sql/t-sql/statements/collations)

11. Agora que concluiu o formulário da Base de Dados SQL do Servidor, clique em **Criar** para aprovisionar a base de dados. O aprovisionamento demora alguns minutos. 

12. Na barra de ferramentas, clique em **Notificações** para monitorizar o processo de implementação.
    
     ![notificação](./media/sql-database-get-started-portal/notification.png)

## <a name="create-a-server-level-firewall-rule"></a>Criar uma regra de firewall ao nível do servidor

O serviço da Base de Dados SQL cria uma firewall ao nível do servidor, impedindo que as aplicações e ferramentas externas estabeleçam uma ligação ao servidor ou a quaisquer bases de dados no servidor, a menos que seja criada uma regra de firewall para abrir a firewall para endereços IP específicos. Siga estes passos para criar uma [regra de firewall ao nível do servidor da Base de Dados SQL](sql-database-firewall-configure.md) para o endereço IP do seu cliente, para permitir a conectividade externa através da firewall da Base de Dados SQL apenas para o seu endereço IP. 

> [!NOTE]
> A Base de Dados SQL comunica através da porta 1433. Se estiver a tentar ligar a partir de uma rede empresarial, o tráfego de saída através da porta 1433 poderá não ser permitido pela firewall da rede. Se assim for, não poderá ligar ao servidor da Base de Dados SQL do Azure, a menos que o departamento de TI abra a porta 1433.
>

1. Depois de concluída a implementação, clique em **Bases de dados SQL** no menu do lado esquerdo e, em seguida, clique em **mySampleDatabase** na página **Bases de dados SQL**. É aberta uma página de descrição geral para a base de dados que mostra o nome de servidor completamente qualificado (como **mynewserver-20170824.database.windows.net**) e oferece opções para configuração adicional. 

2. Copie este nome de servidor totalmente qualificado para utilizar para ligar ao seu servidor e às respetivas bases de dados nos seguintes guias de introdução. 

   ![nome do servidor](./media/sql-database-get-started-portal/server-name.png) 

3. Clique em **definir a firewall do servidor** na barra de ferramentas. É aberta a página **Definições da firewall** do servidor da Base de Dados SQL. 

   ![regra de firewall do servidor](./media/sql-database-get-started-portal/server-firewall-rule.png) 

4. Clique em **Adicionar IP de cliente**, na barra de ferramentas, para adicionar o seu endereço IP atual a uma nova regra de firewall. Uma regra de firewall consegue abrir a porta 1433 para um único endereço IP ou para um intervalo de endereços IP.

5. Clique em **Guardar**. É criada uma regra de firewall ao nível do servidor para a sua porta de abertura 1433 do endereço IP atual no servidor lógico.

6. Clique em **OK** e, em seguida, feche a página **Definições da firewall**.

Pode agora ligar para o servidor de base de dados do SQL Server e respetivas bases de dados utilizando o SQL Server Management Studio, Assistente de migração de dados ou outra ferramenta de sua escolha deste endereço IP utilizando a conta de administrador de servidor criada no procedimento anterior.

> [!IMPORTANT]
> Por predefinição, o acesso através da firewall da Base de Dados SQL está ativado para todos os serviços do Azure. Clique em **DESATIVAR** nesta página para desativar todos os serviços do Azure.

## <a name="sql-server-connection-information"></a>Informações de ligação do servidor SQL

Obtenha o nome de servidor totalmente qualificado para o servidor da Base de Dados SQL do Azure no portal do Azure. Utilize o nome de servidor completamente qualificado para ligar ao seu servidor de SQL do Azure com ferramentas de cliente, incluindo o SQL Server Management Studio e assistência de migração de dados.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Selecione **Bases de Dados SQL** a partir do menu do lado esquerdo e clique na sua base de dados na página **Bases de Dados SQL**. 
3. No painel **Essentials** na página do portal do Azure da sua base de dados, localize e, em seguida, copie o **Nome do servidor**.

   ![informações da ligação](./media/sql-database-get-started-portal/server-name.png)

## <a name="migrate-your-database"></a>Migrar a base de dados

Siga estes passos para utilizar o  **[Assistente de migração de dados](https://www.microsoft.com/download/details.aspx?id=53595)**  para avaliar a preparação da base de dados para migração para a SQL Database do Azure e para concluir a migração.

1. Abra o **Assistente de migração de dados**. Pode executar DMA em qualquer computador com conectividade à internet e de conectividade para a instância do SQL Server que contém a base de dados que pretende migrar. Não é necessário instalá-lo no computador que aloja a instância do SQL Server que está a migrar. Tem de ser a regra de firewall que criou num procedimento anterior para o computador no qual está a executar o Assistente de migração de dados.

     ![Abra o Assistente de migração de dados](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-open.png)

2. No menu da esquerda, clique em **+ novo** para criar um **avaliação** projeto. Preencha os valores de pedido e, em seguida, clique em **criar**:

   | Definição      | Valor sugerido | Descrição | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | Tipo de projeto | Migração | Escolha avaliar a sua base de dados para a migração ou escolha avaliar e migração como parte do mesmo fluxo de trabalho |
   |Nome do projeto|Tutorial de migração| Um nome descritivo |
   |Tipo de servidor de origem| SQL Server | Esta é a única origem atualmente suportada |
   |Tipo de servidor de destino| Base de Dados SQL do Azure| As opções incluem: base de dados SQL do Azure, SQL Server, SQL Server em virtual machines do Azure |
   |Âmbito da migração| Esquema e de dados| As opções incluem: dados esquema e de dados, apenas, o esquema |
   
   ![novo projeto de Assistente de migração de dados](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-new-project.png)

3.  No **selecionar origem** página, preencha os valores de pedido e, em seguida, clique em **Connect**:

    | Definição      | Valor sugerido | Descrição | 
    | ------------ | ------------------ | ------------------------------------------------- | 
    | Nome do servidor | O nome do servidor ou endereço IP | O nome do servidor ou endereço IP |
    | Tipo de autenticação | O tipo de autenticação preferencial| As escolhas: Do Active Directory do Windows Authentication, autenticação de servidor do SQL Server, integrado autenticação, autenticação de palavra-passe do Active Directory |
    | Nome de utilizador | O nome de início de sessão | O início de sessão tem de ter **controlo servidor** permissões |
    | Palavra-passe| a palavra-passe | a palavra-passe |
    | Propriedades de ligação| Selecione **encriptar ligação** e **certificado de servidor de confiança** conforme adequado para o seu ambiente. | Escolha as propriedades adequadas para ligar ao seu servidor |

    ![novos dados migração selecionar origem](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-source.png)

5. Selecione uma base de dados do servidor de origem para migrar para a SQL Database do Azure e, em seguida, clique em **seguinte**. Para este tutorial, é apenas uma base de dados.

6. No **destino selecione** página, preencha os valores de pedido e, em seguida, clique em **Connect**:

    | Definição      | Valor sugerido | Descrição | 
    | ------------ | ------------------ | ------------------------------------------------- | 
    | Nome do servidor | O nome completamente qualificado do servidor de base de dados do Azure | O nome completamente qualificado do servidor de base de dados do Azure do procedimento anterior |
    | Tipo de autenticação | Autenticação do SQL Server | Autenticação do SQL Server é a única opção este tutorial foi escrito, mas a autenticação integrada do Active Directory e autenticação de palavra-passe do Active Directory também são suportadas pelo SQL Database do Azure |
    | Nome de utilizador | O nome de início de sessão | O início de sessão tem de ter **base de dados de controlo** permissões para a base de dados de origem |
    | Palavra-passe| a palavra-passe | a palavra-passe |
    | Propriedades de ligação| Selecione **encriptar ligação** e **certificado de servidor de confiança** conforme adequado para o seu ambiente. | Escolha as propriedades adequadas para ligar ao seu servidor |

    ![destino de Selecione novos dados migração](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-target.png)

7. Selecione a base de dados do servidor de destino que criou num procedimento anterior e, em seguida, clique em **seguinte** para iniciar o processo de avaliação de esquema de base de dados de origem. Para este tutorial, é apenas uma base de dados. Tenha em atenção que o nível de compatibilidade desta base de dados está definido como 140, que é o nível de compatibilidade predefinido para todas as novas bases de dados na base de dados do Azure SQL.

   > [!IMPORTANT] 
   > Depois de migrar a base de dados para a SQL Database do Azure, pode optar por operar a base de dados a um nível de compatibilidade especificado para efeitos de compatibilidade com versões anteriores. Para obter mais informações sobre as implicações e as opções para o funcionamento uma base de dados a um nível de compatibilidade específicos, consulte [nível de compatibilidade de base de dados ALTER](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level). Consulte também [configuração de um âmbito de base de dados de falha de ALTER](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) para obter informações sobre definições de nível de base de dados adicionais relacionados com níveis de compatibilidade.
   >

8. No **selecionar objetos** conclusão da página, após o processo de avaliação do esquema da base de dados de origem, reveja os objetos selecionados para migração e reveja os objetos que contém os problemas. Por exemplo, analise o **dbo.uspSearchCandidateResumes** do objeto para **SERVERPROPERTY('LCID')** alterações de comportamento e o **HumanResourcesJobCandidate** do objeto para Alterações de pesquisa em texto completo. 

   > [!IMPORTANT] 
   > Consoante o design da base de dados e design da sua aplicação, ao migrar a base de dados de origem, poderá ser necessário modificar qualquer uma ou ambas a base de dados ou a aplicação após a migração (e, em alguns casos, antes da migração). Para obter informações sobre as diferenças Transact-SQL que pode afetar a migração, consulte [diferenças Transact-SQL resolver durante a migração para a base de dados SQL](sql-database-transact-sql-information.md).

     ![nova migração assessment e o objeto de seleção de dados](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-assessment-results.png)

9. Clique em **script SQL gerar** para os objetos de esquema na base de dados de origem do script. 
10. Reveja o script gerado e, em seguida, clique em **junto emitir** conforme necessário para rever as recomendações e os problemas de avaliação identificados. Por exemplo, para pesquisa em texto completo, a recomendação quando atualizar é testar as suas aplicações tirar partido as funcionalidades de texto completo. Pode guardar ou copie o script se desejar.

     ![script de migração gerada novos dados](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-generated-script.png)

11. Clique em **implementar esquema** e veja o processo de migração de esquema.

     ![migração do esquema de migração de dados nova](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-schema-migration.png)

12. Quando concluir a migração de esquema, reveja os resultados de erros e, em seguida, pressupondo que existirem none, clique em **migrar dados**.
13. No **selecionar tabelas** página, reveja as tabelas selecionadas para a migração e, em seguida, clique em **começar a migração de dados**.

     ![migração de dados de migração de dados nova](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-data-migration.png)

14. Veja o processo de migração.

     ![novo processo de migração de dados a migração de dados](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-data-migration-process.png)

## <a name="connect-to-the-database-with-ssms"></a>Ligar à base de dados com o SSMS

Utilize [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) estabelecer uma ligação ao servidor da SQL Database do Azure.

1. Abra o SQL Server Management Studio.

2. Na caixa de dialogo **Ligar ao Servidor**, introduza as seguintes informações:

   | Definição       | Valor sugerido | Descrição | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | Tipo de servidor | Motor de base de dados | Este valor é necessário |
   | Nome do servidor | O nome de servidor completamente qualificado | O nome deve ser algo semelhante ao seguinte: **mynewserver20170824.database.windows.net**. |
   | Autenticação | Autenticação do SQL Server | A Autenticação do SQL é o único tipo de autenticação que configurámos neste tutorial. |
   | Iniciar sessão | A conta de administrador do servidor | Esta é a conta que especificou quando criou o servidor. |
   | Palavra-passe | A palavra-passe da sua conta de administrador do servidor | Esta é a palavra-passe que especificou quando criou o servidor. |

   ![ligar ao servidor](./media/sql-database-connect-query-ssms/connect.png)

3. Clique em **Opções** na caixa de diálogo **Ligar ao servidor**. Na secção **Ligar à base de dados**, introduza **mySampleDatabase** para ligar a esta base de dados.

   ![ligar à base de dados no servidor](./media/sql-database-connect-query-ssms/options-connect-to-db.png)  

4. Clique em **Ligar**. A janela do Object Explorer é aberta no SSMS. 

5. No Object Explorer, expanda as **Bases de Dados** e, em seguida, expanda **mySampleDatabase**, para ver os objetos na base de dados de exemplo.

   ![Objetos de base de dados](./media/sql-database-connect-query-ssms/connected.png)  

## <a name="change-database-properties"></a>Alterar propriedades de base de dados

Pode alterar o escalão de serviço, o nível de desempenho e o nível de compatibilidade com o SQL Server Management Studio. Durante a fase de importação, recomendamos que importa um maior desempenho escalão base de dados para um melhor desempenho, mas que reduzir verticalmente após a importação estiver concluída poupar dinheiro até estar pronto a utilizar ativamente a base de dados importado. Alterar o nível de compatibilidade poderá produzir um melhor desempenho e o acesso às funcionalidades mais recentes do serviço SQL Database do Azure. Quando migra uma base de dados mais antigo, o nível de compatibilidade de base de dados é mantida ao nível mais baixa suportada que é compatível com a base de dados que está a ser importado. Para obter mais informações, consulte [melhorada desempenho das consultas com 130 de nível de compatibilidade na SQL Database do Azure](sql-database-compatibility-level-query-performance-130.md).

1. No Object Explorer, clique com botão direito **mySampleDatabase** e, em seguida, clique em **nova consulta**. Uma janela de consulta abre-se à sua base de dados.

2. Execute o seguinte comando para definir a camada de serviço para **padrão** e o nível de desempenho para **S1**.

    ```sql
    ALTER DATABASE mySampleDatabase 
    MODIFY 
        (
        EDITION = 'Standard'
        , MAXSIZE = 250 GB
        , SERVICE_OBJECTIVE = 'S1'
    );
    ```

## <a name="next-steps"></a>Passos seguintes 
Neste tutorial, que aprendeu a:

> * Criar uma base de dados SQL do Azure vazio no portal do Azure 
> * Criar uma firewall ao nível do servidor no portal do Azure 
> * Utilize o [Assistente de migração de dados](https://www.microsoft.com/download/details.aspx?id=53595) (DMA) para importar a base de dados do SQL Server para a base de dados SQL do Azure em branco 
> * Utilize [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) para alterar as propriedades de base de dados.

Avançar para o próximo tutorial para saber como proteger a base de dados.

> [!div class="nextstepaction"]
> [Proteger a base de dados SQL do Azure](sql-database-security-tutorial.md).


