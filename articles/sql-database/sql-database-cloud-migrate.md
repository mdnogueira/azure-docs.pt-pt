---
title: "Migração de bases de dados do SQL Server para a Base de Dados SQL do Azure | Microsoft Docs"
description: "Saiba mais sobre a migração de bases de dados do SQL Server no local para a Base de Dados SQL do Azure na cloud. Utilize ferramentas de migração de base de dados para testar a compatibilidade antes da migração da base de dados."
keywords: "migração de base de dados, migração de base de dados do sql server, ferramentas de migração de base de dados, migrar base de dados, migrar base de dados sql"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 9cf09000-87fc-4589-8543-a89175151bc2
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 11/08/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 86bc7d89bb5725add8ba05b6f0978467147fd3ca
ms.openlocfilehash: 61fb027dfdd5830d87fe4fcfff57f685db71475e


---
# <a name="sql-server-database-migration-to-sql-database-in-the-cloud"></a>Migração de bases de dados do SQL Server para a Base de Dados SQL na cloud
Neste artigo, saiba como migrar um SQL Server 2005 no local ou posterior para a Base de Dados SQL do Azure. Neste processo de migração de bases de dados, pode migrar o esquema e os seus dados da base de dados do SQL Server no seu ambiente atual na Base de Dados SQL. Para ter êxito, a base de dados existente tem de passar primeiro por um teste de compatibilidade. Com a Base de Dados SQL v12, estamos a aproximar-nos da [paridade de funcionalidades](sql-database-features.md), em vez de em problemas relacionados com operações ao nível do servidor e entre bases de dados. Bases de dados e aplicações que dependem de [funções parcialmente ou não suportadas](sql-database-transact-sql-information.md) precisam de alguma reengenharia para corrigir estas incompatibilidades antes que as bases de dados do SQL Server possam ser migradas.

Os passos para migrar são os seguintes:

* **Teste de Compatibilidade**: valide a compatibilidade das bases de dados com a Base de Dados SQL. 
* **Corrigir Problemas de Compatibilidade, se existirem**: se a validação falhar, tem de corrigir os erros de validação.  
* **Fazer a migração** assim que a base de dados for compatível, pode utilizar um ou vários métodos para fazer a migração. 

O SQL Server proporciona vários métodos para realizar cada uma destas tarefas. Este artigo disponibiliza uma descrição geral dos métodos disponíveis para cada tarefa. O diagrama seguinte ilustra os passos e os métodos.

  ![Diagrama de migração de VSSSDT](./media/sql-database-cloud-migrate/03VSSSDTDiagram.png)

> [!NOTE]
> Para migrar uma base de dados não SQL Server, incluindo Microsoft Access, Sybase, MySQL Oracle e DB2 para a Base de Dados SQL do Azure, veja [SQL Server Migration Assistant (Assistente de Migração do SQL Server)](http://blogs.msdn.com/b/ssma/).
> 
> 

## <a name="database-migration-tools-test-sql-server-database-compatibility-with-sql-database"></a>As ferramentas de migração de bases de dados testam a compatibilidade das bases de dados do SQL Server com a Base de Dados SQL
Para testar problemas de compatibilidade da Base de Dados SQL antes de iniciar o processo de migração de bases de dados, utilize um dos seguintes métodos:

> [!div class="op_single_selector"]
> * [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
> * [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
> * [SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md)
> * [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)
> 
> 

* [SQL Server Data Tools para Visual Studio ("SSDT")](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md): SSDT utiliza as regras de compatibilidade mais recentes para detetar incompatibilidades da Base de Dados SQL V12. Se forem detetadas incompatibilidades, pode corrigir os problemas detetados diretamente nesta ferramenta. Este é o método recomendado para testar e resolver problemas de compatibilidade da Base de Dados SQL V12. 
* [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md): SqlPackage é um utilitário de linha de comandos que testa problemas de compatibilidade e gera um relatório que contém os problemas detetados. Se utilizar esta ferramenta, certifique-se de que utiliza a versão mais recente para se servir das regras de compatibilidade mais recentes. Se forem detetados, tem de utilizar outra ferramenta para corrigir quaisquer problemas de compatibilidade detetados - SSDT é recomendado.  
* [O assistente da aplicação Export Data Tier (Exportar Camada de Dados) no SQL Server Management Studio](sql-database-cloud-migrate-determine-compatibility-ssms.md): este assistente deteta e reporta erros no ecrã. Se não forem detetados erros, pode continuar e concluir a migração para a Base de Dados SQL. Se forem detetados, tem de utilizar outra ferramenta para corrigir quaisquer problemas de compatibilidade detetados - SSDT é recomendado.
* [SQL Azure Migration Wizard (Assistente de Migração do SQL Azure, "SAMW")](sql-database-cloud-migrate-fix-compatibility-issues.md): SAMW é uma ferramenta de codeplex que utiliza as regras de compatibilidade da Base de Dados SQL V11 do Azure para detetar incompatibilidades na Base de Dados SQL V12 do Azure. Se forem detetadas incompatibilidades, alguns problemas podem ser resolvidos diretamente nesta ferramenta. Esta ferramenta pode encontrar incompatibilidades que não precisam de ser corrigidas. Foi a primeira ferramenta de assistência de migração da Base de Dados SQL do Azure disponível e é ativamente suportada pela comunidade do SQL Server. Esta ferramenta também pode concluir a migração a partir de si própria. 

## <a name="fix-database-migration-compatibility-issues"></a>Corrigir problemas de compatibilidade de migração de bases de dados
Se forem detetados problemas de compatibilidade, tem de corrigi-los antes de continuar com a migração das bases de dados do SQL Server. Existe uma ampla variedade de problemas de compatibilidade que pode encontrar, dependendo da versão do SQL Server na base de dados de origem e da complexidade da base de dados que está a migrar. As versões mais antigas do SQL Server têm mais problemas de compatibilidade. Utilize os seguintes recursos, para além de uma pesquisa na Internet direcionada com o seu motor de busca preferido:

* [Funcionalidades da base de dados do SQL Server não suportadas na Base de Dados SQL do Azure](sql-database-transact-sql-information.md)
* [Funcionalidade do Motor da Base de Dados Descontinuada no SQL Server 2016](https://msdn.microsoft.com/library/ms144262%28v=sql.130%29)
* [Funcionalidade do Motor da Base de Dados Descontinuada no SQL Server 2014](https://msdn.microsoft.com/library/ms144262%28v=sql.120%29)
* [Funcionalidade do Motor da Base de Dados Descontinuada no SQL Server 2012](https://msdn.microsoft.com/library/ms144262%28v=sql.110%29)
* [Funcionalidade do Motor da Base de Dados Descontinuada no SQL Server 2008 R2](https://msdn.microsoft.com/library/ms144262%28v=sql.105%29)
* [Funcionalidade do Motor da Base de Dados Descontinuada no SQL Server 2005](https://msdn.microsoft.com/library/ms144262%28v=sql.90%29)

Para além de procurar na Internet e utilizar estes recursos, utilize os [fóruns da comunidade do SQL Server do MSDN](https://social.msdn.microsoft.com/Forums/sqlserver/home?category=sqlserver) ou o [StackOverflow](http://stackoverflow.com/).

Utilize uma das seguintes ferramentas de migração de bases de dados para corrigir os problemas detetados:

> [!div class="op_single_selector"]
> * [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
> * [SSMS](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)
> * [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)
> 
> 

* Utilize [SQL Server Data Tools para Visual Studio ("SSDT")](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md): para utilizar SSDT, tem de importar o esquema da base de dados para o SQL Server Data Tools para Visual Studio "SSDT") e criar o projeto para uma implementação de Base de Dados SQL V12. Em seguida, pode corrigir todos os problemas de compatibilidade detetados no SSDT. Quando terminar, sincronize as alterações novamente com a base de dados de origem (ou uma cópia desta). SSDT é, atualmente, o método recomendado para testar e resolver problemas de compatibilidade da Base de Dados SQL V12. Siga a ligação para ver uma [introdução à utilização de SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md).
* Utilize o [SQL Server Management Studio ("SSMS")](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md): para utilizar o SSMS, execute comandos de Transact-SQL para corrigir os erros detetados com outra ferramenta. Este método destina-se principalmente a permitir que os utilizadores avançados modifiquem o esquema da base de dados diretamente na base de dados de origem. 
* Utilize o [SQL Azure Migration Wizard ("SAMW")](sql-database-cloud-migrate-fix-compatibility-issues.md): para utilizar o SAMW, crie um script de Transact-SQL da base de dados de origem. O assistente transforma o script, sempre que possível, para tornar o esquema compatível com a Base de Dados SQL V12. Quando terminar, o SAMW pode ligar-se à Base de Dados SQL V12 para executar o script. Esta ferramenta também analisa os ficheiros de rastreio para determinar os problemas de compatibilidade. O script pode ser gerado apenas com o esquema ou pode incluir dados no formato BCP.

## <a name="migrate-a-compatible-sql-server-database-to-sql-database"></a>Migrar uma base de dados do SQL Server compatível para a Base de Dados SQL
Para migrar uma base de dados do SQL Server compatível, a Microsoft oferece vários métodos de migração para diversos cenários. O método que escolher depende da sua tolerância para períodos de inatividade, do tamanho e complexidade da sua base de dados do SQL Server e da conectividade à cloud do Microsoft Azure.  

> [!div class="op_single_selector"]
> * [SSMS Migration Wizard (Assistente de Migração SSMS)](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
> * [Exportar para ficheiro BACPAC](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
> * [Importar a partir de ficheiro BACPAC](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
> * [Replicação Transacional](sql-database-cloud-migrate-compatible-using-transactional-replication.md)
> 
> 

Para escolher o seu método de migração, a primeira pergunta a fazer é se pode colocar a base de dados fora de produção durante a migração. Migrar uma base de dados enquanto estão a ocorrer transações ativas pode resultar em inconsistências da base de dados e na possível danificação da mesma. Existem vários métodos para silenciar uma base de dados, desde desativar a conectividade de cliente a criar um [instantâneo de base de dados](https://msdn.microsoft.com/library/ms175876.aspx).

Para migrar com tempo de inatividade mínimo, utilize a [replicação de transações do SQL Server](sql-database-cloud-migrate-compatible-using-transactional-replication.md), se a sua base de dados cumprir os requisitos para replicação transacional. Se conseguir sustentar algum período de inatividade ou estiver a fazer uma migração de teste de uma base de dados de produção para migração posterior, considere um dos três métodos seguintes:

* [SSMS Migration Wizard](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md): para bases de dados pequenas e médias, migrar um SQL Server 2005 compatível ou posterior é tão simples como executar [Deploy Database to Microsoft Azure Database Wizard (Assistente para Implementar a Base de Dados na Base de Dados do Microsoft Azure)](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md), no SQL Server Management Studio.
* [Exportar para Ficheiro BACPAC](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md) e, em seguida, [Importar a partir de Ficheiro BACPAC](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md): se tiver desafios de conectividade (sem conectividade, largura de banda baixa ou problemas de tempo limite) e para bases de dados médias e grandes, utilize um ficheiro [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4). Com este método, são exportados o esquema do SQL Server e os dados para um ficheiro BACPAC. Em seguida, é importado o ficheiro BACPAC para a Base de Dados SQL com o Assistente da Aplicação Export Data TIer no SQL Server Management Studio ou o utilitário de linha de comando [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx).
* Utilizar BACPAC e BCP em conjunto: utilize um ficheiro [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) e [BCP](https://msdn.microsoft.com/library/ms162802.aspx) para bases de dados muito maiores para alcançar maior paralelização e aumentar o desempenho, embora com complexidade maior. Com este método, migre o esquema e os dados separadamente.
  
  * [Exporte o esquema apenas para um ficheiro BACPAC](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md).
  * [Importe o esquema apenas a partir do Ficheiro BACPAC](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md) para a Base de Dados SQL.
  * Utilize [BCP](https://msdn.microsoft.com/library/ms162802.aspx) para extrair os dados para ficheiros simples e, em seguida, [carregue em paralelo](https://technet.microsoft.com/library/dd425070.aspx) estes ficheiros para a Base de Dados SQL do Azure.
    
     ![Migração de base de dados do SQL Server - migrar a base de dados SQL para a cloud.](./media/sql-database-cloud-migrate/01SSMSDiagram_new.png)

## <a name="next-steps"></a>Passos seguintes
* [Newest version of SSDT (Versão mais recente do SSDT)](https://msdn.microsoft.com/library/mt204009.aspx)
* [Newest version of SQL Server Management Studio (Versão mais recente do SQL Server Management Studio)](https://msdn.microsoft.com/library/mt238290.aspx)

## <a name="additional-resources"></a>Recursos adicionais
* [SQL Database feaures](sql-database-features.md)
  [Transact-SQL partially or unsupported functions (Funcionalidades da Base de Dados SQL: funções de Transact-SQL parcialmente ou não suportadas)](sql-database-transact-sql-information.md)
* [Migrate non-SQL Server databases using SQL Server Migration Assistant (Migrar bases de dados não SQL Server com o SQL Server Migration Assistant [Assistente de Migração do SQL Server])](http://blogs.msdn.com/b/ssma/)




<!--HONumber=Jan17_HO1-->


