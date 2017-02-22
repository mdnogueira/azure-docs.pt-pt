---
title: "Não suportado na Base de Dados SQL do Azure T-SQL | Microsoft Docs"
description: "Instruções de Transact-SQL que são menos do que totalmente suportadas na Base de Dados SQL do Azure"
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
tags: 
ms.assetid: c05abd9e-28a7-4c97-9bdf-bc60d08fc92e
ms.service: sql-database
ms.custom: overview
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 11/28/2016
ms.author: rickbyh
translationtype: Human Translation
ms.sourcegitcommit: 7db5939c88045ab59d7d9fb4b58f07374d7bca01
ms.openlocfilehash: ebec6ca87cfd5e9d2c4ea726d5d31d20998ef33f


---
# <a name="azure-sql-database-transact-sql-differences"></a>Diferenças da base de dados SQL Transact-SQL Azure   
A maioria das funcionalidades do Transact-SQL que as aplicações dependem são suportadas no Microsoft SQL Server e na Base de Dados SQL do Azure. Por exemplo, os componentes principais do SQL, como funções de tipos de dados, de operadores,de cadeia, de aritmética, lógicas e de cursor, etc., funcionam sem diferenças do SQL Server.

## <a name="why-some-transact-sql-is-not-supported"></a>Por que motivo alguns Transact-SQL não são suportados
A Base de Dados SQL do Azure foi concebida para isolar as funcionalidades de dependências na base de dados mestra e o sistema operativo. Como resultado, muitas atividades ao nível do servidor são inadequadas para a Base de Dados SQL. As instruções do Transact-SQL, normalmente, não estão disponíveis se configurarem as opções ao nível do servidor, componentes do sistema operativo, ou especificarem a configuração do sistema de ficheiros. Quando as funcionalidades que se encontram fora da base de dados do utilizador são necessárias, está disponível, muitas vezes, uma alternativa apropriada de alguma forma a partir da Base de Dados SQL ou a partir de outra funcionalidade ou serviço do Azure. 

Por exemplo, o Always On é substituído pela Georreplicação Ativa. Por esse motivo, quaisquer instruções do Transact-SQL relacionadas com grupos de disponibilidade não são suportadas pela Base de Dados SQL e as vistas de gestão dinâmica relacionadas com o Always On não são suportadas.  

Para obter uma lista das funcionalidades que são suportados e não suportados pela Base de Dados SQL, consulte [Azure SQL Database considerations, guidelines and features (Considerações, diretrizes e funcionalidades sobre a Base de Dados SQL do Azure)](sql-database-features.md).

Geralmente, a sintaxe que foi preterida no SQL Server não é suportada na Base de Dados SQL.

## <a name="transact-sql-syntax-partially-supported-in-sql-database"></a>Sintaxe do Transact-SQL parcialmente suportada na Base de Dados SQL
A Base de Dados SQL suporta alguns, mas nem todos os argumentos que existem nas instruções SQL Server 2016 Transact-SQL correspondente. Por exemplo, a instrução `CREATE PROCEDURE` está disponível, no entanto todas as opções de `CREATE PROCEDURE` não estão disponíveis. Descrever aqui a sintaxe completa seria confuso e redundante. Consulte os tópicos de sintaxe ligados para obter detalhes sobre as áreas suportadas de cada instrução.

- Bases de dados: [CRIAR](https://msdn.microsoft.com/library/dn268335.aspx)/[ALTERAR BASE DE DADOS](https://msdn.microsoft.com/library/ms174269.aspx)   
- Funções: [CRIAR](https://msdn.microsoft.com/library/ms186755.aspx)/[ALTERAR FUNÇÃO](https://msdn.microsoft.com/library/ms186967.aspx)   
- Inícios de sessão: [CRIAR](https://msdn.microsoft.com/library/ms189751.aspx)/[ALTERAR INÍCIO DE SESSÃO](https://msdn.microsoft.com/library/ms189828.aspx)   
- Procedimentos armazenados: [CRIAR](https://msdn.microsoft.com/library/ms187926.aspx)/[ALTERAR PROCEDIMENTO](https://msdn.microsoft.com/library/ms189762.aspx)   
- Tabelas: [CRIAR](https://msdn.microsoft.com/library/dn305849.aspx)/[ALTERAR TABELA](https://msdn.microsoft.com/library/ms190273.aspx)   
- Tipos (personalizado): [CRIAR TIPO](https://msdn.microsoft.com/library/ms175007.aspx)   
- Utilizadores: [CRIAR](https://msdn.microsoft.com/library/ms173463.aspx)/[ALTERAR UTILIZADOR](https://msdn.microsoft.com/library/ms176060.aspx)   
- Vistas: [CRIAR](https://msdn.microsoft.com/library/ms187956.aspx)/[ALTERAR VISTA](https://msdn.microsoft.com/library/ms173846.aspx)   

## <a name="transact-sql-syntax-not-supported-in-sql-database"></a>Sintaxe do Transact-SQL não suportada na Base de Dados SQL   
Para além das instruções do Transact-SQL relacionadas com as funcionalidades não suportadas descritas em [Azure SQL Database considerations, guidelines and features (Considerações, diretrizes e funcionalidades sobre a Base de Dados SQL do Azure)](sql-database-features.md), as seguintes instruções e grupos de instruções não são suportadas.
- Agrupamento de objetos de sistema
- Ligação relacionada: instruções do Endpoint, `ORIGINAL_DB_NAME`. A Base de Dados SQL não suporta a autenticação do Windows, mas suporta a autenticação do Azure Active Directory semelhante. Alguns tipos de autenticação precisam da versão mais recente do SSMS. Para mais informações, consulte [Connecting to SQL Database or SQL Data Warehouse By Using Azure Active Directory Authentication (Ligar à Base de Dados SQL ou SQL Data Warehouse, utilizando a Autenticação do Azure Active Directory)](sql-database-aad-authentication.md).
- Consultas entre bases de dados com três ou quatro nomes de partes. (As consultas entre bases de dados só de leitura são suportadas através da [consulta de base de dados elástica](sql-database-elastic-query-overview.md).)
- Encadeamento de propriedade entre bases de dados, `TRUSTWORTHY` definição
- `DATABASEPROPERTY`Utilize `DATABASEPROPERTYEX` em vez disso.
- `EXECUTE AS LOGIN`Em vez disso, utilize "EXECUTAR COMO UTILIZADOR".
- A encriptação é suportada, exceto para a gestão de chaves extensível
- Eventos: eventos, notificações de eventos, notificações de consulta
- Sintaxe relacionada com a colocação de ficheiros de base de dados, tamanho e ficheiros de base de dados que são geridas automaticamente pelo Microsoft Azure.
- Sintaxe relacionada com disponibilidade elevada, que é gerida através da sua conta do Microsoft Azure. Isto inclui a sintaxe da cópia de segurança, restauro, Always On, espelhamento da base de dados, envio de registos, modos de recuperação.
- Sintaxe que se baseia no leitor de registo, que não está disponível na Base de Dados SQL: Replicação Push, Captura de Dados Alterados. A Base de Dados SQL pode ser um subscritor de um artigo de replicação push.
- Sintaxe que se baseia no Agente do SQL Server ou na base de dados do MSDB: alertas, operadores, servidores de gestão central. Ao invés, utilize o scripting, como o Azure PowerShell.
- Funções: `fn_get_sql`, `fn_virtualfilestats`, `fn_virtualservernodes`
- Tabelas temporárias globais
- Sintaxe relacionada com as definições do servidor relacionadas com o hardware: memória, threads de trabalho, afinidade de CPU, sinalizadores de rastreio, etc. Ao invés, utilize níveis de serviço.
- `HAS_DBACCESS`
- `KILL STATS JOB`
- `OPENQUERY`, `OPENROWSET`, `OPENDATASOURCE`, `BULK INSERT` e nomes de quatro partes
- .NET Framework [integração CLR com o SQL Server](http://msdn.microsoft.com/library/ms254963.aspx)
- Pesquisa semântica
- Credenciais do servidor. Em vez disso, utilize base de dados no âmbito de credenciais.
- Itens ao nível do servidor: funções de servidor, `IS_SRVROLEMEMBER`, `sys.login_token`. `GRANT`, `REVOKE`, e `DENY` das permissões ao nível do servidor não estão disponíveis, apesar de algumas serem substituídas por permissões ao nível da base de dados. Algumas DMVs ao nível do servidor útil têm DMVs equivalentes ao nível da base de dados.
- `SET REMOTE_PROC_TRANSACTIONS`
- `SHUTDOWN`
- `sp_addmessage`
- `sp_configure` opções e `RECONFIGURE`. Algumas opções estão disponíveis com [ALTERAR BASE DE DADOS NO ÂMBITO DA CONFIGURAÇÃO](https://msdn.microsoft.com/library/mt629158.aspx).
- `sp_helpuser`
- `sp_migrate_user_to_contained`
- Auditoria do SQL Server. Ao invés, utilize a auditoria da Base de Dados SQL.
- Rastreio do SQL Server
- Sinalizadores de rastreio. Alguns itens do sinalizador de rastreio foram movidos para modos de compatibilidade.
- Depuração do Transact-SQL
- Acionadores: no âmbito do servidor ou acionadores de início de sessão
- `USE` instrução: para alterar o contexto da base de dados para outra base de dados, tem de efetuar uma nova ligação para a nova base de dados.

## <a name="full-transact-sql-reference"></a>Referência do Transact-SQL completa
Para obter mais informações sobre a gramática, utilização e exemplos do Transact-SQL, consulte [Transact-SQL Reference (Database Engine) (Referência do Transact-SQL (Motor de Base de Dados))](https://msdn.microsoft.com/library/bb510741.aspx) na Documentação Online do Microsoft SQL Server. 

### <a name="about-the-applies-to-tags"></a>Sobre as etiquetas "Aplica-se a"
A referência do Transact-SQL inclui tópicos relacionados com versões do SQL Server 2008 até ao presente. Abaixo do título do tópico existe uma barra de ícones que lista as quatro plataformas do SQL Server e indica a aplicabilidade. Por exemplo, os grupos de disponibilidade foram introduzidos no SQL Server 2012. O tópico [CRIAR GRUPO DE DISPONIBILIDADE](https://msdn.microsoft.com/library/ff878399.aspx) indica que a instrução se aplica ao **SQL Server (a partir de 2012)**. A instrução não é aplicável ao SQL Server 2008, ao SQL Server 2008 R2, à Base de Dados SQL do Azure, ao Azure SQL Data Warehouse ou ao Parallel Data Warehouse.

Em alguns casos, o assunto geral de um tópico pode ser utilizado num produto, mas existem pequenas diferenças entre produtos. As diferenças são indicadas em pontos intermédios no tópico, conforme adequado.



<!--HONumber=Dec16_HO3-->


