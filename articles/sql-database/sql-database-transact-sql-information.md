---
title: "Resolver a base de dados SQL T-SQL Azure de migração de diferenças | Microsoft Docs"
description: "Instruções de Transact-SQL que são menos do que totalmente suportadas na Base de Dados SQL do Azure"
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
tags: 
ms.assetid: c05abd9e-28a7-4c97-9bdf-bc60d08fc92e
ms.service: sql-database
ms.custom: migrate
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: On Demand
ms.date: 10/23/2017
ms.author: rickbyh
ms.openlocfilehash: 5d9cfce0453bb32bf3512b5b8e3ed25c9c2fdbdf
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="resolving-transact-sql-differences-during-migration-to-sql-database"></a>Resolver diferenças Transact-SQL durante a migração para a base de dados SQL   
Quando [migrar a base de dados](sql-database-cloud-migrate.md) do SQL Server para o Azure SQL Server, poderá descobrir que a base de dados requer alguns novamente engenharia antes do SQL Server podem ser migrado. Este artigo fornece orientação para o ajudar a efetuar esta engenharia novamente e compreender os motivos pelos quais subjacentes por que motivo é necessário o engenharia novamente. Para detetar incompatibilidades, utilize o [através do Assistente de dados de migração (DMA)](https://www.microsoft.com/download/details.aspx?id=53595).

## <a name="overview"></a>Descrição geral
A maioria das funcionalidades de Transact-SQL que utilizam as aplicações são totalmente suportadas no Microsoft SQL Server e SQL Database do Azure. Por exemplo, os componentes do SQL Server core, tais como tipos de dados, os operadores, string, aritmética, lógica e funções de cursor, forma idêntica funcionam no SQL Server e base de dados SQL. Existem, no entanto, algumas diferenças de T-SQL no DDL (linguagem de definição de dados) e os elementos DML (idioma de manipulação de dados), resultando em instruções T-SQL e consultas que são suportadas apenas parcialmente (que discutimos neste artigo).

Além disso, existem algumas funcionalidades e a sintaxe que não é suportada em todos os porque a SQL Database do Azure foi concebido para isolar as funcionalidades de dependências na base de dados mestra e o sistema operativo. Como tal, a maioria das atividades de nível de servidor são inadequada para a base de dados SQL. Instruções T-SQL e as opções não estão disponíveis se possam configurar opções ao nível do servidor de componentes do sistema operativo, ou especificar a configuração do sistema de ficheiros. Quando estas capacidades são necessárias, uma alternativa adequada, muitas vezes, está disponível de outro modo da base de dados do SQL Server ou do Azure outro funcionalidade ou serviço. 

Por exemplo, a elevada disponibilidade está incorporada no Azure, para que configurar o Always On não é necessário (embora poderá querer configurar a georreplicação ativa para a recuperação mais rápida no caso de desastre). Por isso, as instruções de T-SQL relacionados com grupos de disponibilidade não são suportadas pela base de dados do SQL Server e as vistas de gestão dinâmica relacionados com o Always On não também são suportadas.

Para obter uma lista das funcionalidades que são suportados e não suportados pela base de dados do SQL Server, consulte [comparação de funcionalidades da SQL Database do Azure](sql-database-features.md). A lista nesta página complementa esse artigo diretrizes e as funcionalidades e concentra-se nas instruções Transact-SQL.

## <a name="transact-sql-syntax-statements-with-partial-differences"></a>Instruções de sintaxe Transact-SQL com diferenças parciais
As instruções DDL (linguagem de definição de dados) de núcleos estão disponíveis, mas algumas instruções DDL tem extensões relacionadas com a colocação de disco e não suportados funcionalidades. 

- Instruções de criar e alterar a base de dados de mais dúzia de três maneiras. As instruções incluem a colocação de ficheiros, FILESTREAM e opções de Mediador de serviço que só se aplicam ao SQL Server. Isto poderá não importa se criar bases de dados antes de migrar, mas se estiver a migrar o código de T-SQL que cria as bases de dados deve comparar [CREATE DATABASE (base de dados do Azure SQL)](https://msdn.microsoft.com/library/dn268335.aspx) com a sintaxe do SQL Server em [criar Base de dados (SQL Server Transact-SQL)](https://msdn.microsoft.com/library/ms176061.aspx) para se certificar de que todas as opções que utilizar são suportadas. Criar base de dados do SQL Database do Azure também tem o objetivo de serviço e as opções de dimensionamento elástico que se aplicam apenas a base de dados SQL.
- As instruções para criar e ALTER TABLE tem FileTable opções que não podem ser utilizadas na base de dados do SQL Server porque FILESTREAM não é suportado.
- CRIAR e alterar as instruções de início de sessão são suportadas mas a base de dados do SQL Server não fornece todas as opções. Para tornar a sua base de dados mais portátil, a base de dados SQL encoraja utilizando os utilizadores de base de dados contida, em vez de inícios de sessão sempre que possível. Para obter mais informações, consulte [CREATE/ALTER início de sessão](https://msdn.microsoft.com/library/ms189828.aspx) e [Controlling e conceder acesso de base de dados](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins).

## <a name="transact-sql-syntax-not-supported-in-azure-sql-database"></a>Sintaxe de Transact-SQL não suportado na SQL Database do Azure   
Para além das instruções Transact-SQL relacionadas com as funcionalidades não suportadas, descritas em [comparação de funcionalidades da SQL Database do Azure](sql-database-features.md), as seguintes declarações e grupos de declarações, não são suportadas. Como tal, se a sua base de dados a serem migradas utilizar qualquer uma das seguintes funcionalidades, criar novamente o T-SQL para eliminar estas funcionalidades T-SQL e as instruções.

- Agrupamento de objetos de sistema
- Ligação relacionadas com: instruções de ponto final. A Base de Dados SQL não suporta a autenticação do Windows, mas suporta a autenticação do Azure Active Directory semelhante. Alguns tipos de autenticação precisam da versão mais recente do SSMS. Para mais informações, consulte [Connecting to SQL Database or SQL Data Warehouse By Using Azure Active Directory Authentication (Ligar à Base de Dados SQL ou SQL Data Warehouse, utilizando a Autenticação do Azure Active Directory)](sql-database-aad-authentication.md).
- Consultas entre bases de dados com três ou quatro nomes de partes. (As consultas entre bases de dados só de leitura são suportadas através da [consulta de base de dados elástica](sql-database-elastic-query-overview.md).)
- Encadeamento de propriedade entre bases de dados, `TRUSTWORTHY` definição
- `EXECUTE AS LOGIN`Em vez disso, utilize "EXECUTAR COMO UTILIZADOR".
- A encriptação é suportada, exceto para a gestão de chaves extensível
- Eventos CCM: Eventos, as notificações de eventos, as notificações de consulta
- Colocação de ficheiros: sintaxe relacionadas com a colocação de ficheiros de base de dados, tamanho e ficheiros de base de dados que são geridos automaticamente pelo Microsoft Azure.
- Elevada disponibilidade: relacionadas com a sintaxe para elevada disponibilidade, o que é gerida através da sua conta do Microsoft Azure. Isto inclui a sintaxe da cópia de segurança, restauro, Always On, espelhamento da base de dados, envio de registos, modos de recuperação.
- Leitor de registo: sintaxe que depende o leitor do registo, que não está disponível na base de dados do SQL Server: Push replicação, captura de dados alterados. A Base de Dados SQL pode ser um subscritor de um artigo de replicação push.
- Funções: `fn_get_sql`, `fn_virtualfilestats`, `fn_virtualservernodes`
- Hardware: Sintaxe relacionadas com as definições relacionadas com o hardware de servidor: como a memória de threads de trabalho, afinidade da CPU, sinalizadores de rastreio. Ao invés, utilize níveis de serviço.
- `KILL STATS JOB`
- `OPENQUERY`, `OPENROWSET`, `OPENDATASOURCE`e os nomes de quatro partes
- .NET framework: Integração do CLR com o SQL Server
- Pesquisa semântica
- As credenciais do servidor: Utilize [credenciais no âmbito de base de dados](https://msdn.microsoft.com/library/mt270260.aspx) em vez disso.
- Itens de ao nível do servidor: funções de servidor, `sys.login_token`. `GRANT`, `REVOKE`, e `DENY` das permissões ao nível do servidor não estão disponíveis, apesar de algumas serem substituídas por permissões ao nível da base de dados. Algumas DMVs ao nível do servidor útil têm DMVs equivalentes ao nível da base de dados.
- `SET REMOTE_PROC_TRANSACTIONS`
- `SHUTDOWN`
- `sp_addmessage`
- `sp_configure` opções e `RECONFIGURE`. Algumas opções estão disponíveis com [ALTERAR BASE DE DADOS NO ÂMBITO DA CONFIGURAÇÃO](https://msdn.microsoft.com/library/mt629158.aspx).
- `sp_helpuser`
- `sp_migrate_user_to_contained`
- Agente do SQL Server: Sintaxe que depende o agente do SQL Server ou a base de dados MSDB: alertas, operadores, servidores de gestão central. Ao invés, utilize o scripting, como o Azure PowerShell.
- Auditoria de SQL Server: auditoria de base de dados de SQL de utilização em vez disso.
- Rastreio do SQL Server
- Sinalizadores de rastreio: alguns itens do sinalizador de rastreio foi movidos para modos de compatibilidade.
- Depuração do Transact-SQL
- Acionadores: no âmbito do servidor ou acionadores de início de sessão
- `USE` instrução: para alterar o contexto da base de dados para outra base de dados, tem de efetuar uma nova ligação para a nova base de dados.

## <a name="full-transact-sql-reference"></a>Referência do Transact-SQL completa
Para obter mais informações sobre a gramática, utilização e exemplos do Transact-SQL, consulte [Transact-SQL Reference (Database Engine) (Referência do Transact-SQL (Motor de Base de Dados))](https://msdn.microsoft.com/library/bb510741.aspx) na Documentação Online do Microsoft SQL Server. 

### <a name="about-the-applies-to-tags"></a>Sobre as etiquetas "Aplica-se a"
A referência de Transact-SQL inclui artigos relacionados com versões do SQL Server 2008 para a presente. Abaixo do título do artigo não existe um ícone de barra, lista as quatro plataformas do SQL Server e que indica a aplicabilidade. Por exemplo, os grupos de disponibilidade foram introduzidos no SQL Server 2012. O [criar grupo AVAILABILTY](https://msdn.microsoft.com/library/ff878399.aspx) artigo indica que a instrução aplica-se a **do SQL Server (começando com o 2012)**. A instrução não é aplicável ao SQL Server 2008, ao SQL Server 2008 R2, à Base de Dados SQL do Azure, ao Azure SQL Data Warehouse ou ao Parallel Data Warehouse.

Em alguns casos, o assunto geral de um artigo pode ser utilizado num produto, mas existem pequenas diferenças entre os produtos. As diferenças são indicadas em midpoints no artigo conforme apropriado. Em alguns casos, o assunto geral de um artigo pode ser utilizado num produto, mas existem pequenas diferenças entre os produtos. As diferenças são indicadas em midpoints no artigo conforme apropriado. Por exemplo o artigo de CREATE TRIGGER está disponível na base de dados do SQL Server. Mas o **ALL SERVER** opção para acionadores ao nível do servidor, indica que os acionadores ao nível do servidor não podem ser utilizados na base de dados do SQL Server. Em alternativa, utilize acionadores ao nível da base de dados.

## <a name="next-steps"></a>Passos seguintes

Para obter uma lista das funcionalidades que são suportados e não suportados pela base de dados do SQL Server, consulte [comparação de funcionalidades da SQL Database do Azure](sql-database-features.md). A lista nesta página complementa esse artigo diretrizes e as funcionalidades e concentra-se nas instruções Transact-SQL.

