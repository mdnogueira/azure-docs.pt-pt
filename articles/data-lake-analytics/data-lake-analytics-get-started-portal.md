---
title: "Introdução ao Azure Data Lake Analytics com o portal do Azure | Microsoft Docs"
description: 'Saiba como utilizar o portal do Azure para criar uma conta do Data Lake Analytics, criar uma tarefa do Data Lake Analytics com U-SQL e submeter a tarefa. '
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: b1584d16-e0d2-4019-ad1f-f04be8c5b430
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/21/2017
ms.author: edmaca
ms.translationtype: Human Translation
ms.sourcegitcommit: ef1e603ea7759af76db595d95171cdbe1c995598
ms.openlocfilehash: b004310a3b2667134c9ad890bd907dc8ae439dd9
ms.contentlocale: pt-pt
ms.lasthandoff: 06/16/2017


---
<a id="get-started-with-azure-data-lake-analytics-using-azure-portal" class="xliff"></a>

# Introdução ao Azure Data Lake Analytics com o Portal do Azure
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Saiba como utilizar o portal do Azure para criar contas do Azure Data Lake Analytics, definir tarefas em [U-SQL](data-lake-analytics-u-sql-get-started.md) e submeter tarefas para o serviço Data Lake Analytics. Para mais informações sobre a Data Lake Analytics, consulte [Descrição geral da Análise do Azure Data Lake](data-lake-analytics-overview.md).

<a id="prerequisites" class="xliff"></a>

## Pré-requisitos

Antes de começar este tutorial, tem de ter uma **subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

<a id="create-a-data-lake-analytics-account" class="xliff"></a>

## Criar uma conta de Data Lake Analytics

Agora, irá criar em simultâneo uma conta do Data Lake Analytics e uma conta do Data Lake Store.  Este passo é simples e leva apenas cerca de 60 segundos para concluir.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Clique em **Novo** >  **Intelligence + análise** > **Data Lake Analytics**.
3. Selecione os valores para os seguintes itens:
   * **Nome**: nome da conta do Data Lake Analytics (Apenas são permitidas letras minúsculas e números).
   * **Subscrição**: selecione a subscrição do Azure utilizada para a conta da Análise.
   * **Grupo de Recursos**. Selecione um Grupo de Recursos do Azure existente ou crie um novo.
   * **Localização**. Selecione um centro de dados do Azure para a conta do Data Lake Analytics.
   * **Data Lake Store**: siga as instruções para criar uma nova conta do Data Lake Store ou selecione uma conta existente. 
4. Opcionalmente, selecione o escalão de preços para a sua conta do Data Lake Analytics.
5. Clique em **Criar**. 


<a id="your-first-u-sql-script" class="xliff"></a>

## O seu primeiro script U-SQL

O texto apresentado em seguida é um script U-SQL muito simples. Tudo o que faz é definir um pequeno conjunto de dados dentro do script e, em seguida, gravar esse conjunto de dados no Data Lake Store predefinido como um ficheiro denominado `/data.csv`.

```
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS 
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
```

<a id="submit-a-u-sql-job" class="xliff"></a>

## Submeter uma tarefa de U-SQL

1. Na conta do Data Lake Analytics, clique em **Nova Tarefa**.
2. Cole o texto do script U-SQL mostrado acima. 
3. Clique em **Submeter Tarefa**.   
4. Aguarde até que o estado da tarefa seja alterado para **Com êxito**.
5. Caso a tarefa tenha falhado, consulte [Monitor and troubleshoot Data Lake Analytics jobs](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md) (Monitorizar e resolver problemas de tarefas do Data Lake Analytics).
6. Clique no separador **Saída** e, em seguida, clique em `SearchLog-from-Data-Lake.csv`. 

<a id="see-also" class="xliff"></a>

## Consultar também

* Para começar a desenvolver aplicações U-SQL, consulte [Desenvolver scripts SQL-U, utilizando as Ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
* Para saber mais sobre U-SQL, consulte [Introdução à linguagem U-SQL da Análise do Azure Data Lake](data-lake-analytics-u-sql-get-started.md).
* Para tarefas de gestão, veja [Manage Azure Data Lake Analytics using Azure portal (Gerir o Azure Data Lake Analytics com o Portal do Azure)](data-lake-analytics-manage-use-portal.md).

