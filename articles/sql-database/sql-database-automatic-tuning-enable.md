---
title: "Ativar a otimização automática para a SQL Database do Azure | Microsoft Docs"
description: "Pode ativar automática otimização na base de dados SQL do Azure facilmente."
services: sql-database
documentationcenter: 
author: veljko-msft
manager: drasumic
editor: vvasic
ms.assetid: 
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Inactive
ms.date: 09/19/2016
ms.author: veljko-msft
ms.openlocfilehash: bf8e0203112a42132a80e234964747c550fea284
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2017
---
# <a name="enable-automatic-tuning"></a>Ativar o ajuste automático

Base de dados SQL do Azure é um serviço de dados geridos automaticamente que constantemente monitoriza as suas consultas e identifica a ação que pode efetuar para melhorar o desempenho da sua carga de trabalho. Pode rever as recomendações e manualmente aplicá-las ou permitir que a base de dados do SQL Azure automaticamente aplicar ações corretivas – isto é conhecido como **modo otimização automático**. A otimização automática pode ser ativado ao servidor ou ao nível de base de dados.

## <a name="enable-automatic-tuning-on-server"></a>Ativar a otimização automática no servidor
No nível do servidor pode escolher para herdar a configuração automática de otimização de "Predefinições do Azure" ou não para herdar a configuração. Predefinições do Azure são FORCE_LAST_GOOD_PLAN ativada, CREATE_INDEX ativada e DROP_INDEX desativada.

### <a name="portal"></a>Portal
Para ativar a otimização automática no servidor da SQL Database do Azure, navegue para o servidor no portal do Azure e, em seguida, selecione **otimização automática** no menu. Selecione as opções de otimização automáticas que pretende ativar e selecione **aplicar**:

![Servidor](./media/sql-database-automatic-tuning-enable/server.png)

Opções de otimização automáticas no servidor são aplicadas a todas as bases de dados no servidor. Por predefinição, todas as bases de dados de herdar a configuração do respetivo elemento principal do servidor, mas isto pode ser substituído e especificado individualmente para cada base de dados.

### <a name="rest-api"></a>API REST
[Clique aqui para mais informações sobre como ativar a otimização automática ao nível do servidor através da REST API](https://docs.microsoft.com/rest/api/sql/serverautomatictuning)

## <a name="enable-automatic-tuning-on-database"></a>Ativar a otimização automática na base de dados

A base de dados do SQL do Azure permite-lhe especificar individualmente a configuração de otimização automática em cada base de dados. O nível de base de dados pode escolher para herdar a configuração automática de otimização do servidor de principal, "Azure predefinições" ou não para herdar a configuração. Predefinições do Azure são FORCE_LAST_GOOD_PLAN ativada, CREATE_INDEX ativada e DROP_INDEX desativada.

> [!NOTE]
> A recomendação geral é para gerir a configuração de otimização automática ao nível do servidor para que as mesmas definições de configuração podem ser aplicadas automaticamente em cada base de dados. Configure a otimização automática numa base de dados individuais se a base de dados é diferente que outras pessoas no mesmo servidor.
>

### <a name="portal"></a>Portal

Para ativar a otimização automática numa base de dados único, navegue para a base de dados no portal do Azure e, em seguida e selecione **otimização automática**. Pode configurar uma base de dados individual para herdar as definições do servidor, selecionando a opção ou pode especificar a configuração para uma base de dados individualmente.

![Base de Dados](./media/sql-database-automatic-tuning-enable/database.png)

Assim que tiver selecionado a configuração adequada, clique em **aplicar**.

### <a name="rest-api"></a>REST API
[Clique aqui para mais informações sobre como ativar a otimização automática numa única base de dados através da REST API](https://docs.microsoft.com/rest/api/sql/databaseautomatictuning)

### <a name="t-sql"></a>T-SQL

Para ativar a otimização de numa única base de dados através de T-SQL automática, ligar à base de dados e execute a seguinte consulta:

   ```T-SQL
   ALTER DATABASE current SET AUTOMATIC_TUNING = AUTO | INHERIT | CUSTOM
   ```
   
Definição de otimização automática para AUTO, será aplicada a predefinições do Azure. Defini-la como HERDAR, a configuração automática de otimização será herdada do servidor principal. Escolher PERSONALIZADO, terá de configurar manualmente a otimização automática.

Para configurar opções de otimização automáticas individuais através de T-SQL, ligar à base de dados e executar a consulta, tal como esta:

   ```T-SQL
   ALTER DATABASE current SET AUTOMATIC_TUNING (FORCE_LAST_GOOD_PLAN = ON, CREATE_INDEX = DEFAULT, DROP_INDEX = OFF)
   ```
   
Definir a opção de otimização individuais como ON, irá substituir qualquer definição de base de dados herdada e ative a opção de otimização. Defini-la como OFF, será também substituir qualquer definição de base de dados herdada e desativar a opção de otimização. Opção de otimização automática, para que a predefinição é especificada, será herdar a configuração do nível da base de dados automático de definição de otimização.  

## <a name="disabled-by-the-system"></a>Desativado pelo sistema
A otimização automática está a monitorizar todas as ações que demora na base de dados e, em alguns casos pode determinar que a otimização automática não funciona corretamente na base de dados. Nesta situação, uma opção de otimização será desativada pelo sistema. Na maioria dos casos, isto acontece porque o arquivo de consultas não está ativado ou está no estado só de leitura numa base de dados específica.

## <a name="next-steps"></a>Passos seguintes
* Leia o [artigo otimização automático](sql-database-automatic-tuning.md) para saber mais sobre a otimização automática e como pode ajudar a melhorar o desempenho.
* Consulte [recomendações de desempenho](sql-database-advisor.md) para uma descrição geral das recomendações de desempenho de SQL Database do Azure.
* Consulte [Query Performance Insight](sql-database-query-performance.md) para saber mais sobre como ver o impacto do desempenho das consultas superiores.
