---
title: "Descrição geral do Microsoft Azure Data Lake Analytics | Microsoft Docs"
description: "O Data Lake Analytics é um serviço de computação do Azure Big Data que lhe permite utilizar dados para orientar a sua empresa com as informações obtidas a partir dos dados na nuvem, independentemente de onde se encontram e do respetivo tamanho. O Data Lake Analytics permite isto da forma mais simples, escalável e económica possível. "
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: 1e1d443a-48a2-47fb-bc00-bf88274222de
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/16/2016
ms.author: edmaca
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: a6d2d6365afaecab57a100867a72b9aa2def3745


---
# <a name="overview-of-microsoft-azure-data-lake-analytics"></a>Descrição geral do Microsoft Azure Data Lake Analytics
## <a name="what-is-azure-data-lake-analytics"></a>O que é o Azure Data Lake Analytics?
O Azure Data Lake Analytics é um novo serviço incorporado para facilitar a análise de macrodados. Este serviço permite-lhe concentrar-se na escrita, execução e gestão de tarefas, em vez de na infraestrutura distribuída em funcionamento. Em vez de implementar, configurar e otimizar hardware, escreve consultas para transformar os dados e extrair informações valiosas. O serviço de análise pode processar tarefas de qualquer dimensionamento instantaneamente definindo simplesmente a quantidade de potência necessária. Apenas paga a tarefa quando estiver a ser executada, tornando-a económica. O serviço de análise suporta o Azure Active Directory permitindo-lhe gerir simplesmente o acesso e as funções e está integrado no sistema de identidades no local. Também inclui U-SQL, uma linguagem que unifica os benefícios do SQL com o poder expressivo do código de utilizador. O runtime escalável distribuído de U-SQL permite-lhe analisar eficazmente os dados no arquivo e nos SQL Servers no Azure, Base de Dados SQL do Azure e Azure SQL Data Warehouse.

## <a name="key-capabilities"></a>Principais capacidades
* **Dimensionamento dinâmico**
  
    O Data Lake Analytics é criado de raiz para dimensionamento e desempenho da nuvem.  Aprovisiona dinamicamente os recursos e permite-lhe fazer análises sobre terabytes ou até mesmo exabytes de dados. Quando a tarefa é concluída, reduz os recursos automaticamente e paga apenas a potência de processamento utilizada. À medida que aumenta ou diminui o tamanho dos dados armazenados ou a quantidade de computação utilizada, não tem de reescrever o código. Isto permite concentrar-se apenas na lógica de negócio e não em como processar e armazenar grandes conjuntos de dados.
* **Desenvolver mais rapidamente, depurar e otimizar de forma mais inteligente com ferramentas familiares**
  
    O Data Lake Analytics tem uma profunda integração com o Visual Studio, pelo que pode utilizar ferramentas familiares para executar, depurar e otimizar o seu código. As visualizações das tarefas U-SQL permitem-lhe ver o modo de execução do código relativamente ao dimensionamento, para que possa identificar facilmente estrangulamentos de desempenho e otimizar os custos.
* **U-SQL: simples e familiar, poderosa e extensível**
  
    O Data Lake Analytics inclui U-SQL, uma linguagem de consulta que expande a natureza familiar, simples e declarativa do SQL Server com o poder expressivo do C#. A linguagem U-SQL foi criada com base no mesmo runtime distribuído que alimenta os sistemas de macrodados na Microsoft. Milhões de programadores SQL e .NET podem agora processar e analisar todos os seus dados com as capacidades que já têm.
* **Integração total com os seus investimentos de TI**
  
    O Data Lake Analytics pode utilizar os investimentos de TI existentes para identidade, gestão, segurança e armazenamento de dados. Isto simplifica a governação de dados e facilita a expansão das aplicações de dados atuais. O Data Lake Analytics está integrada no Active Directory para gestão de utilizadores e permissões e é fornecida com monitorização e auditoria incorporadas.
* **Económica e acessível**
  
    O Data Lake Analytics é uma solução económica para executar cargas de trabalho de macrodados. Paga por tarefa quando os dados são processados. Não é necessário nenhum hardware, licenças ou contratos de suporte específicos do serviço. O sistema é aumentado ou reduzido verticalmente de forma automática à medida que a tarefa é iniciada e concluída, o que significa que nunca paga mais do que aquilo que precisa.
* **Funciona com todos os Dados do Azure**
  
    O Data Lake Analytics pode trabalhar inúmeras origens de dados do Azure: armazenamento de Blobs do Azure, base de dados SQL do Azure e, obviamente, a Data Lake Analytics está otimizada especialmente para funcionar com o Azure Data Lake Store, fornecendo o nível mais elevado de desempenho, débito e paralelização para as suas cargas de trabalho de macrodados.

## <a name="see-also"></a>Consultar também
* Introdução
  
  * [Introdução ao Data Lake Analytics com o Portal do Azure](data-lake-analytics-get-started-portal.md)
  * [Introdução ao Data Lake Analytics com o Azure PowerShell](data-lake-analytics-get-started-powershell.md)
  * [Introdução ao Data Lake Analytics com o SDK .NET do Azure](data-lake-analytics-get-started-net-sdk.md)
  * [Desenvolver scripts U-SQL com as Ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
  * [Introdução à linguagem U-SQL do Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md)
* U-SQL e desenvolvimento
  
  * [Introdução à linguagem U-SQL do Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md)
  * [Utilizar as funções de janela U-SQL para tarefas do Azure Data Lake Analytics](data-lake-analytics-use-window-functions.md)
  * [Desenvolver operadores U-SQL definidos pelo utilizador para tarefas do Data Lake Analytics](data-lake-analytics-u-sql-develop-user-defined-operators.md)
* Gestão
  
  * [Gerir o Azure Data Lake Analytics com o Portal do Azure](data-lake-analytics-manage-use-portal.md)
  * [Gerir o Azure Data Lake Analytics com o Azure PowerShell](data-lake-analytics-manage-use-powershell.md)
  * [Monitorizar e resolver problemas das tarefas de Azure Data Lake Analytics com o Portal do Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
  * [Accessing Diagnostics logs for Azure Data Lake Analytics (Aceder aos registos de diagnóstico do Azure Data Lake Analytics)](data-lake-analytics-diagnostic-logs.md)
* Tutorial ponto a ponto
  
  * [Utilizar tutoriais interativos do Azure Data Lake Analytics](data-lake-analytics-use-interactive-tutorials.md)
  * [Analyze Website logs using Azure Data Lake Analytics (Analisar registos de sites com o Azure Data Lake Analytics)](data-lake-analytics-analyze-weblogs.md)
* Dê-nos a sua opinião
  
  <!-- Fixing broken links for Azure content migration from ACOM to DOCS. I can't find a suitable substitute for what appears to be a link that is no longer available. I am commenting out for now. The author can investigate in the future. Hyperlink text: Comment on our documentation backlog. Referenced file: data-lake-analytics-documentation-backlog.md -->
  * [Submeter um pedido de funcionalidade](http://aka.ms/adlafeedback)
  * [Obter ajuda nos fóruns](http://aka.ms/adlaforums)




<!--HONumber=Nov16_HO2-->


