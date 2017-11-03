---
title: Gerir bases de dados no Azure SQL Data Warehouse | Microsoft Docs
description: "Descrição geral da gestão de bases de dados do armazém de dados do SQL Server. Inclui ferramentas de gestão, as DWUs e o desempenho de escalamento horizontal, resolução de problemas de desempenho das consultas, estabelecer políticas boa segurança e restaurar uma base de dados de Corrupção de dados ou a partir de uma falha regional."
services: sql-data-warehouse
documentationcenter: NA
author: kevinvngo
manager: jhubbard
editor: 
ms.assetid: 8576fdb3-71fe-4b3b-a4e0-5e8a7f148acf
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 10/31/2016
ms.author: kevin;barbkess
ms.openlocfilehash: b14d0aad5a1f50c225391dbab27ec6240423a65a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-databases-in-azure-sql-data-warehouse"></a>Gerir bases de dados no Azure SQL Data Warehouse
O SQL Data Warehouse automatiza vários aspetos da gestão de bases de dados. Por exemplo, para aumentar o desempenho apenas terá de ajustar e paga para o nível adequado de recursos de computação e, em seguida, permita que o SQL Data Warehouse fazer todo o trabalho de ampliar e dimensionamento novamente.

Será undoubtedly pretende monitorizar a carga de trabalho para identificar as necessidades de desempenho, bem como resolver problemas relacionados com consultas de execução longa. Também terá de efetuar algumas tarefas de segurança para gerir permissões para utilizadores e os inícios de sessão.

Esta descrição geral inclui estes aspetos da gestão do armazém de dados do SQL Server.

* Ferramentas de gestão
* Dimensionar a computação
* Colocar em pausa e retomar
* Melhores práticas do desempenho
* Monitorização de consulta
* Segurança
* Cópia de segurança e restauro

## <a name="management-tools"></a>Ferramentas de gestão
Pode utilizar uma variedade de ferramentas para gerir bases de dados no armazém de dados do SQL Server. Como gerir bases de dados, irá desenvolver as preferências de ferramenta para cada tipo de tarefa que tem de efetuar.

### <a name="azure-portal"></a>Portal do Azure
O [portal do Azure] [ Azure portal] é um portal baseado na web, onde pode criar, atualizar e eliminar as bases de dados-las e monitorizar os recursos de base de dados. Esta ferramenta é uma grande é se estiver a começar com o Azure, gerir um pequeno número de bases de dados, ou se precisa de fazer algo de rapidamente.

Para começar a utilizar com o portal do Azure, consulte o artigo [criar um SQL Data Warehouse (portal do Azure)][Create a SQL Data Warehouse (Azure portal)].

### <a name="sql-server-data-tools-in-visual-studio"></a>Ferramentas de dados do SQL Server no Visual Studio
[SQL Server Data Tools] [ SQL Server Data Tools] (SSDT) no Visual Studio permite-lhe ligar, gerir e desenvolver as bases de dados. Se tiver um programador de aplicações familiarizado com o Visual Studio ou outros ambientes de desenvolvimento integrado (IDEs), experimente utilizar SSDT no Visual Studio.

SSDT inclui o SQL Server Object Explorer, que permite-lhe visualizar, ligar e executar scripts em bases de dados do armazém de dados do SQL Server. Para ligar-se rapidamente ao SQL Data Warehouse, pode simplesmente clicar o **abrir no Visual Studio** botão no comando barra quando visualizar a base de dados os detalhes no Portal clássico do Azure.  

Para começar a utilizar com SSDT no Visual Studio, consulte o artigo [consultar o Azure SQL Data Warehouse com o Visual Studio][Query Azure SQL Data Warehouse with Visual Studio].

### <a name="command-line-tools"></a>Ferramentas de linha de comandos
Ferramentas de linha de comandos são ideais para automatizar as cargas de trabalho.  PowerShell sqlcmd, sendo excelente formas para automatizar os processos.  Recomendamos que estas ferramentas para gerir um grande número de servidores lógicos e implementar as alterações de recursos num ambiente de produção, como as tarefas de necessárias podem ser convertidos em script e, em seguida, automatizadas.

### <a name="dynamic-management-views"></a>Vistas de gestão dinâmica
DMVs são butter de and bread da gestão do armazém de dados do SQL Server. Quase todas as informações que analisa no portal do depende DMVs. Para ver uma lista de DMVs de armazém de dados do SQL Server, consulte [vistas de sistema do SQL Data Warehouse][SQL Data Warehouse system views].

Para começar a utilizar, consulte o artigo [ligar e consultar com sqlcmd][Connect and query with sqlcmd], e [criar uma base de dados (PowerShell)][Create a database (PowerShell)].

## <a name="scale-compute"></a>Dimensionar a computação
No SQL Data Warehouse, pode dimensionar rapidamente o desempenho out ou retroceder por aumente ou diminua a recursos de computação de CPU, memória e largura de banda de e/s. Para aumentar o desempenho, tudo o que precisa de fazer é ajustar o número de unidades do data warehouse (DWUs) que o SQL Data Warehouse atribui à base de dados. O SQL Data Warehouse rapidamente faz com que a alteração e processa todas as alterações para o hardware ou software subjacentes.

Para obter mais informações sobre dimensionamento DWUs, consulte o artigo [Dimensionar desempenho].

## <a name="pause-and-resume"></a>Colocar em pausa e retomar
Para reduzir os custos, pode colocar em pausa e retomar a computação recursos a pedido. Por exemplo, se não utilizar a base de dados durante a noite e no fim de semana, pode colocar em pausa-lo durante essas horas e retomá-lo durante o dia. Não lhe será cobrado dwus enquanto a base de dados está em pausa.

Para obter mais informações, consulte [colocar em pausa computação][Pause compute], e [retomar a computação][Resume compute].

## <a name="performance-best-practices"></a>Melhores práticas do desempenho
Quando introdução com uma nova tecnologia, detetar as sugestões e truques que funcionam melhor direito desde o início e pode poupar muitos de tempo.  Encontrará as melhores práticas ao longo de muitos dos nossos tópicos.

Para ver muitas um resumo das considerações mais importantes quando desenvolver a sua carga de trabalho, consulte [melhores práticas do SQL Data Warehouse][SQL Data Warehouse Best Practices].

## <a name="query-monitoring"></a>Monitorização de consulta
Por vezes, uma consulta está a ser executado demasiado tempo, mas não se do qual é o culprit. O SQL Data Warehouse tem vistas de gestão dinâmica (DMVs) que pode utilizar para descobrir qual consulta está a demorar demasiado tempo.

Para consultas de longa execução, consulte [monitorizar a carga de trabalho com DMVs][Monitor your workload using DMVs].

## <a name="security"></a>Segurança
Para manter um sistema seguro, tem de estar num alerta e proteger contra qualquer tipo de acesso não autorizado. Tem de certificar-se de que as regras de firewall estão assegurados autorizado para apenas endereços IP podem ligar-se um sistema de segurança. Necessita de uma autenticação adequada das credenciais do utilizador. Depois de um utilizador estabeleceu ligação à base de dados, o utilizador só deve ter permissões para efetuar um número mínimo de ações. Para proteger dados, pode utilizar encriptação. Também é importante que tenha de auditoria e controlo para pode retrace eventos se não houver qualquer atividade suspeita.

Para saber mais sobre a gestão de segurança, aceda ao [descrição geral de segurança][Security overview].

## <a name="backup-and-restore"></a>Cópia de segurança e restauro
Ter backps fiável dos seus dados é uma parte essencial dos qualquer base de dados de produção. Armazém de dados do SQL Server mantém os seus dados seguros por automaticamente fazer cópias de segurança das bases de dados do Active Directory em intervalos regulares. Estas cópias de segurança permitem-lhe recuperar a partir de cenários em que tiver danificado os dados ou acidentalmente ignorados os dados ou a base de dados.  Para a agenda de cópia de segurança de dados, política de retenção e restaurar uma base de dados, consulte [restaurar a partir do instantâneo][Restore from snapshot].

## <a name="next-steps"></a>Passos seguintes
Utilizando a estrutura de base de dados boa princípios tornarão mais fácil de gerir as bases de dados no armazém de dados do SQL Server. Para obter mais informações, aceda ao [descrição geral do desenvolvimento][Development overview].

<!--Image references-->

<!--Article references-->
[Create a SQL Data Warehouse (Azure Portal)]: sql-data-warehouse-get-started-provision.md
[Create a database (PowerShell)]: sql-data-warehouse-get-started-provision-powershell.md
[connection]: sql-data-warehouse-develop-connections.md
[Query Azure SQL Data Warehouse with Visual Studio]: sql-data-warehouse-query-visual-studio.md
[Connect and query with sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md
[Development overview]: sql-data-warehouse-overview-develop.md
[Monitor your workload using DMVs]: sql-data-warehouse-manage-monitor.md
[Pause compute]: sql-data-warehouse-manage-compute-overview.md#pause-compute-bk
[Restore from snapshot]: sql-data-warehouse-restore-database-overview.md
[Resume compute]: sql-data-warehouse-manage-compute-overview.md#resume-compute-bk
[Dimensionar desempenho]: sql-data-warehouse-manage-compute-overview.md#scale-compute
[Security overview]: sql-data-warehouse-overview-manage-security.md
[SQL Data Warehouse Best Practices]: sql-data-warehouse-best-practices.md
[SQL Data Warehouse system views]: sql-data-warehouse-reference-tsql-system-views.md

<!--MSDN references-->
[SQL Server Data Tools]: https://msdn.microsoft.com/library/mt204009.aspx

<!--Other web references-->
[Azure portal]: http://portal.azure.com/
