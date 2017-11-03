---
title: "Ligar a uma origem de dados em coleções de área de trabalho do Power BI | Microsoft Docs"
description: "Saiba como ligar a uma origem de dados dentro de coleções de área de trabalho do Power BI."
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ROBOTS: NOINDEX
ms.assetid: 2a4caeb3-255d-4215-9554-0ca8e3568c13
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: asaxton
ms.openlocfilehash: 24600c4343e3bfebe14f25020c5a7ba02d15af64
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="connect-to-a-data-source"></a>Ligar a uma origem de dados

Com **coleções de área de trabalho do Power BI**, pode incorporar relatórios para a própria aplicação. Quando incorporar um relatório do Power BI na aplicação, o relatório liga-se aos dados subjacentes por **importar** uma cópia dos dados ou pelo **ligar diretamente** para a origem de dados utilizando **DirectQuery** .

> [!IMPORTANT]
> As Coleções de Áreas de Trabalho do Power BI foram preteridas e estão disponíveis até junho de 2018 ou até quando indicar o contrato. Recomendamos que planeie a migração para o Power BI Embedded para evitar interrupções na sua aplicação. Para obter informações sobre como migrar os dados para o Power BI Embedded, veja [How to migrate Power BI Workspace Collections content to Power BI Embedded (Como migrar o conteúdo das Coleções de Áreas de Trabalho do Power BI para o Power BI Embedded)](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Estas são as diferenças entre a utilização de **Importar** e **DirectQuery**.

| Importar | DirectQuery |
| --- | --- |
| Tabelas, colunas *e dados* são importadas ou copiadas para o conjunto de dados do relatório. Para ver as alterações que ocorreram nos dados subjacentes, é necessário atualizar ou importar novamente um dataset completo e atual. |Apenas *tabelas e colunas* são importadas ou copiadas para o conjunto de dados do relatório. É sempre a ver os dados mais recentes. |

Com coleções de área de trabalho do Power BI, que pode utilizar o DirectQuery com origens de dados de nuvem e não no local origens de dados neste momento.

> [!NOTE]
> O Gateway de dados no local não é suportado com coleções de área de trabalho do Power BI neste momento. Isto significa que não é possível utilizar DirectQuery com origens de dados no local.

## <a name="supported-data-sources"></a>Origens de dados suportadas

**DirectQuery**
* Base de dados SQL do Azure
* Azure SQL Data Warehouse

**Importar**

Pode importar utilizando todas as origens de dados disponíveis no Power BI Desktop. Irá **não** conseguir atualizar os dados dentro de coleções de área de trabalho do Power BI. Tem de carregar as alterações ao seu ficheiro PBIX para coleções de área de trabalho do Power BI. Isto acontece porque nenhum gateway disponível. 

## <a name="benefits-of-using-directquery"></a>Vantagens da utilização de DirectQuery

Existem duas vantagens principais quando utilizar **DirectQuery**:

* **DirectQuery** permite-lhe criar visualizações através de grandes conjuntos de dados, onde caso contrário, seria possível unfeasible a importar primeiro todos os dados.
* Subjacente alterações de dados pode necessitar de uma atualização de dados e, para alguns relatórios, a necessidade para apresentar dados atuais pode exigir transferências de dados de grandes dimensões, tornando voltar a importar dados unfeasible. Por outro lado, **DirectQuery** relatórios utilizem sempre dados atuais.

## <a name="limitations-of-directquery"></a>Limitações de DirectQuery

Existem algumas limitações da utilização **DirectQuery**:

* Todas as tabelas têm provenientes de uma base de dados.
* Se a consulta é demasiado complexa, ocorre um erro. Para resolver o erro deve refatorar a consulta de modo a que tenha menos complexa. Se a consulta tem de ser complexa, terá de importar os dados em vez de utilizar **DirectQuery**.
* Filtragem de relação está limitado a uma única direção, em vez de ambas as direções.
* Não é possível alterar o tipo de dados de uma coluna.
* Por predefinição, as limitações são colocadas em expressões DAX permitidas em medidas. Consulte [DirectQuery e as medidas](#measures).

<a name="measures"/>

## <a name="directquery-and-measures"></a>DirectQuery e medidas
Para garantir que as consultas enviadas para a origem de dados subjacente ter um desempenho aceitável, limitações são impostas em medidas. Quando utilizar **Power BI Desktop**, avançadas os utilizadores podem optar por ignorar esta limitação escolhendo **ficheiro > Opções e definições > opções**. No **opções** caixa de diálogo, escolha **DirectQuery**e selecione a opção **permitir sem restrições medidas no modo DirectQuery**. Quando esta opção está selecionada, pode ser utilizada uma expressão DAX que é válida para uma medida. Os utilizadores deverão ter conhecimento; No entanto, se algumas expressões que efetuar bem quando os dados são importados visualizem lenta consultas para o back-end de origem quando no **DirectQuery** modo. 

## <a name="see-also"></a>Veja Também

* [Começar a utilizar coleções de área de trabalho do Microsoft Power BI](get-started.md)
* [Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)

Mais perguntas? [Tente a Comunidade do Power BI](http://community.powerbi.com/)

