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
ms.openlocfilehash: 82db5996c1ba1f224593e4eaa5b3b0067755db49
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="enable-automatic-tuning"></a>Ativar o ajuste automático

Base de dados SQL do Azure é um serviço de dados geridos automaticamente que constantemente monitoriza as suas consultas e identifica a ação que pode efetuar para melhorar o desempenho da sua carga de trabalho. Pode rever as recomendações e manualmente aplicá-las ou permitir que a base de dados do SQL Azure automaticamente aplicar ações corretivas – isto é conhecido como **modo otimização automático**. A otimização automática pode ser ativado ao servidor ou ao nível de base de dados.

## <a name="enable-automatic-tuning-on-server"></a>Ativar a otimização automática no servidor

Para ativar a otimização automática no servidor da SQL Database do Azure, navegue para o servidor no portal do Azure e, em seguida, selecione **otimização automática** no menu. Selecione as opções de otimização automáticas que pretende ativar e selecione **aplicar**:

![Servidor](./media/sql-database-automatic-tuning-enable/server.png)

Opções de otimização automáticas no servidor são aplicadas a todas as bases de dados no servidor. Por predefinição, todas as bases de dados de herdar a configuração do respetivo elemento principal do servidor, mas isto pode ser substituído e especificado individualmente para cada base de dados.

## <a name="configure-automatic-tuning-on-database"></a>Configurar a otimização automática na base de dados

O portal do Azure permite-lhe especificar individualmente a configuração de otimização automática em cada base de dados.

> [!NOTE]
> A recomendação geral é para gerir a configuração de otimização automática ao nível do servidor para que as mesmas definições de configuração podem ser aplicadas automaticamente em cada base de dados. Configure a otimização automática numa base de dados individuais se a base de dados é diferente que outras pessoas no mesmo servidor.
>

Para ativar a otimização automática numa base de dados único, navegue para a base de dados no portal do Azure e, em seguida e selecione **otimização automática**. Pode configurar uma base de dados individual para herdar as definições do servidor, selecionando a opção ou pode especificar a configuração para uma base de dados individualmente.

![Base de Dados](./media/sql-database-automatic-tuning-enable/database.png)

Assim que tiver selecionado a configuração adequada, clique em **aplicar**.

## <a name="next-steps"></a>Passos seguintes
* Leia o [artigo otimização automático](sql-database-automatic-tuning.md) para saber mais sobre a otimização automática e como pode ajudar a melhorar o desempenho.
* Consulte [recomendações de desempenho](sql-database-advisor.md) para uma descrição geral das recomendações de desempenho de SQL Database do Azure.
* Consulte [Query Performance Insight](sql-database-query-performance.md) para saber mais sobre como ver o impacto do desempenho das consultas superiores.
