---
title: "Configurar a segurança de SQL Database do Azure para recuperação após desastre | Microsoft Docs"
description: "Saiba as considerações de segurança para configurar e gerir a segurança depois de restaurar uma base de dados ou uma ativação pós-falha para um servidor secundário."
services: sql-database
documentationcenter: na
author: anosov1960
manager: jhubbard
editor: monicar
ms.assetid: c7c898c9-69d4-4e16-8b7e-720bbb3353dd
ms.service: sql-database
ms.custom: business continuity
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Inactive
ms.date: 10/13/2016
ms.author: sashan
ms.openlocfilehash: 5bdcdba4956a12b54559b8accf822a4f41656045
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="configure-and-manage-azure-sql-database-security-for-geo-restore-or-failover"></a>Configurar e gerir a segurança de SQL Database do Azure para georrestauro ou de ativação pós-falha 

Este tópico descreve os requisitos de autenticação para configurar e controlar [georreplicação ativa](sql-database-geo-replication-overview.md) e os passos necessários para configurar o acesso de utilizador para a base de dados secundária. Também descreve como ativar o acesso à base de dados recuperada depois de utilizar [georrestauro](sql-database-recovery-using-backups.md#geo-restore). Para obter mais informações sobre as opções de recuperação, consulte [descrição geral da continuidade do negócio](sql-database-business-continuity.md).

> [!NOTE]
> [Replicação geográfica activa](sql-database-geo-replication-overview.md) está agora disponível para todas as bases de dados em todos os escalões de serviço.
>  

## <a name="disaster-recovery-with-contained-users"></a>Recuperação após desastre com utilizadores contidos
Ao contrário dos utilizadores tradicionais, o que devem ser mapeados para inícios de sessão na base de dados mestra, um utilizador contido é completamente gerido pela base de dados. Isto tem duas vantagens. Num cenário de recuperação após desastre, os utilizadores podem continuar a estabelecer ligação com nova base de dados primária ou a base de dados recuperada utilizando georrestauro sem qualquer configuração adicional, porque a base de dados gere os utilizadores. Também existem potencial escalabilidade e os benefícios de desempenho desta configuração de uma perspetiva de início de sessão. Para obter mais informações, veja [Contained Database Users - Making Your Database Portable (Utilizadores de Base de Dados Contidos - Tornar a Sua Base de Dados Portátil)](https://msdn.microsoft.com/library/ff929188.aspx). 

O compromisso principal está a gerir o processo de recuperação após desastre em escala mais difícil. Quando tiver várias bases de dados que utilizam o início de sessão mesmo, manter as credenciais através de utilizadores contidos em várias bases de dados poderá negate as vantagens de utilizadores contidos. Por exemplo, a política de rotação de palavra-passe requer que efetuadas alterações a ser consistentemente várias bases de dados em vez de alterar a palavra-passe para início de sessão uma vez na base de dados mestra. Por este motivo, se tiver várias bases de dados que utilizam o mesmo nome de utilizador e palavra-passe, através de utilizadores contidos não é recomendado. 

## <a name="how-to-configure-logins-and-users"></a>Como configurar utilizadores e os inícios de sessão
Se estiver a utilizar inícios de sessão e os utilizadores (em vez de utilizadores contidos), tem de efetuar passos adicionais para assegurar que os inícios de sessão mesmos existem na base de dados mestra. As secções seguintes descrevem as considerações de passos envolvidos e adicionais.

### <a name="set-up-user-access-to-a-secondary-or-recovered-database"></a>Configurar o acesso de utilizador para uma base de dados secundária ou recuperado
Ordem para a base de dados secundária para ser utilizado como uma base de dados secundária só de leitura e para garantir um acesso adequado para a nova base de dados primária ou a base de dados recuperada utilizando georrestauro, base de dados mestra do servidor de destino têm de ter a configuração de segurança adequados antes da recuperação.

As permissões específicas para cada passo são descritas mais à frente neste tópico.

Preparar o acesso de utilizador para uma secundária georreplicação deve ser efetuada como parte de configurar a georreplicação. Preparar o acesso de utilizador para as bases de dados de georreplicação-restaurada deve ser efetuada em qualquer altura quando o servidor original estiver online (por exemplo, como parte integrante a simulação).

> [!NOTE]
> Se efetuar a ativação pós-falha ou georrestauro para um servidor que não tenha inícios de sessão está corretamente configurados, acesso ao mesmo será limitado para a conta de administrador do servidor.
> 
> 

Configurar os inícios de sessão no servidor de destino envolve três passos descritos abaixo:

#### <a name="1-determine-logins-with-access-to-the-primary-database"></a>1. Determine os inícios de sessão com acesso à base de dados primária:
O primeiro passo do processo consiste em determinar os inícios de sessão tem de estar duplicados no servidor de destino. Isto é conseguido com um par de instruções SELECT, na base de dados mestra lógica no servidor de origem e na base de dados primária em si.

Apenas o administrador de servidor ou membro do **LoginManager** função de servidor pode determinar os inícios de sessão no servidor de origem com a seguinte instrução SELECT. 

    SELECT [name], [sid] 
    FROM [sys].[sql_logins] 
    WHERE [type_desc] = 'SQL_Login'

Apenas um membro da função de base de dados db_owner, o dbo de utilizador ou o administrador do servidor, pode determinar todos os principais de utilizador de base de dados na base de dados primária.

    SELECT [name], [sid]
    FROM [sys].[database_principals]
    WHERE [type_desc] = 'SQL_USER'

#### <a name="2-find-the-sid-for-the-logins-identified-in-step-1"></a>2. Localize o SID de inícios de sessão identificados no passo 1:
Ao comparar o resultado das consultas da secção anterior e o SID de correspondência, pode mapear o início de sessão do utilizador de base de dados. Inícios de sessão com um utilizador de base de dados com um SID correspondente tem acesso de utilizador para a base de dados do utilizador da base de dados principal. 

A seguinte consulta pode ser utilizada para ver todos os principais de utilizador e os respetivos SIDs numa base de dados. Apenas um membro do administrador de função ou o servidor da base de dados de db_owner pode executar esta consulta.

    SELECT [name], [sid]
    FROM [sys].[database_principals]
    WHERE [type_desc] = 'SQL_USER'

> [!NOTE]
> O **INFORMATION_SCHEMA** e **sys** os utilizadores têm *nulo* SIDs e o **convidado** SID é **0x00**. O **dbo** SID pode começar a utilizar *0x01060000000001648000000000048454*, se o criador de base de dados foi o administrador de servidor em vez de um membro do **DbManager**.
> 
> 

#### <a name="3-create-the-logins-on-the-target-server"></a>3. Crie inícios de sessão no servidor de destino:
O último passo consiste em Ir para o servidor de destino ou os servidores e gerar os inícios de sessão com os SIDs adequado. Segue-se a sintaxe básica.

    CREATE LOGIN [<login name>]
    WITH PASSWORD = <login password>,
    SID = <desired login SID>

> [!NOTE]
> Se pretende conceder acesso de utilizador para o secundário, mas não para o servidor primário, pode fazer isto, modificando o início de sessão do utilizador no servidor primário, utilizando a seguinte sintaxe.
> 
> INÍCIO DE SESSÃO DE ALTER <login name> DESATIVAR
> 
> DESATIVAR não irá alterar a palavra-passe, pelo que pode sempre ativar-se necessário.
> 
> 

## <a name="next-steps"></a>Passos seguintes
* Para obter mais informações sobre a gestão de acesso de base de dados e inícios de sessão, consulte [segurança da base de dados SQL: Gerir a segurança da base de dados de início de sessão e acesso](sql-database-manage-logins.md).
* Para obter mais informações sobre os utilizadores de base de dados contida, consulte [contidos base de dados de utilizadores - tornar a base de dados portátil](https://msdn.microsoft.com/library/ff929188.aspx).
* Para obter informações sobre como utilizar e configurar a replicação geográfica activa, consulte [georreplicação ativa](sql-database-geo-replication-overview.md)
* Para obter informações sobre como utilizar georrestauro, consulte [georrestauro](sql-database-recovery-using-backups.md#geo-restore)

