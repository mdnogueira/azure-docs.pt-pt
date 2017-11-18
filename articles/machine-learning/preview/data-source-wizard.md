---
title: Assistente do Azure Machine Learning da origem de dados do Azure | Microsoft Docs
description: Explica o Assistente de origem de dados do AML workbench
author: cforbe
ms.author: cforbe
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/07/2017
ms.openlocfilehash: 6a0b014fe57f3e9d2ceeac94f3570353a9a07b27
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2017
---
# <a name="data-source-wizard"></a>Assistente de origem de dados #

O Assistente de origem de dados é uma forma rápida e fácil para assegurar a um conjunto de dados do Azure ML Workbench sem código. É onde pode selecionar também uma estratégia de exemplo para o conjunto de dados. 

## <a name="step-1-trigger-the-data-source-wizard"></a>Passo 1: Acionar o Assistente de origem de dados ## 

Para colocar os dados para um projeto com o Assistente de origem de dados. Selecione o  **+**  botão junto à caixa de pesquisa na vista de dados e escolha Adicionar origem de dados. 

![Adicionar origem de dados](media/data-source-wizard/add-data-source.png)

## <a name="step-2-select-where-data-is-stored"></a>Passo 2: Selecione de onde os dados são armazenados ##
Em primeiro lugar, especificar como os dados atualmente nos. Foi possível armazenar num ficheiro simples ou um diretório, um ficheiro de parquet, um ficheiro do Excel ou uma base de dados. Consulte [origens de dados suportada](data-prep-appendix2-supported-data-sources.md) para obter mais informações.

![Passo 1](media/data-source-wizard/step1.png)

## <a name="step-3-select-data-file"></a>Passo 3: Selecione o ficheiro de dados ##
Para um ficheiro ou directório, especifique o caminho do ficheiro. Escolha, na lista pendente, a localização dos dados – poderá ser um caminho de ficheiro local ou o Blob Storage do Azure. 

Especifique o caminho, escrevendo-lo na ou clicando no **procurar...** botão Procurar para o mesmo. Pode navegar para um diretório ou um ou mais ficheiros.

Clique em **concluir** para aceitar as predefinições para o resto dos passos ou em seguida, prossiga para o passo seguinte.


![Passo 4](media/data-source-wizard/step2.png)

## <a name="step-4-choose-file-parameters"></a>Passo 4: Escolher parâmetros do ficheiro ##

O Assistente de origem de dados pode detetar automaticamente o ficheiro de tipo, separadores e codificação. Seria mostram também um exemplo de dados serão aspeto. Também pode alterar qualquer um destes parâmetros manualmente. 

![Passo 5](media/data-source-wizard/step3.png)

## <a name="step-5-set-data-types-for-columns"></a>Passo 5: Definir os tipos de dados em colunas ##

O Assistente de origem de dados Deteta automaticamente os tipos de dados das colunas do conjunto de dados. Se esta pedidos sem êxito um ou pretende impor um tipo de dados, pode alterar o tipo de dados manualmente. O **dados de saída de exemplo** coluna mostra exemplos de dados serão aspeto.

![Passo 6](media/data-source-wizard/step4.png)

## <a name="step-6-choose-sampling-strategy-for-data"></a>Passo 6: Escolher a estratégia de amostragem de dados ##

Pode especificar um ou mais estratégias de amostragem para o conjunto de dados e escolha uma como a estratégia de Active Directory. A predefinição é ao carregar as linhas de Top 10000. Pode editar este exemplo, clicando no **editar** botão na barra de ferramentas ou adicione uma estratégia de novo ao clicar no + novo. São apresentadas as estratégias que suportamos atualmente

-     Número de principal de linhas
-     Número aleatório de linhas
-     Percentagem aleatória de linhas
-     Ficheiro completo

Pode especificar quantas estratégias de amostragem, como quiser, mas não existe apenas um que pode ser definida como ativa quando preparar os dados. Pode definir qualquer estratégia para ser o ativo, selecionando a estratégia e clique em definir como o Active Directory na barra de ferramentas.

Dependendo de onde os dados vêm, algumas estratégias de exemplo não podem ser suportadas. Para obter mais informações sobre a amostragem, a secção de amostragem em [neste documento](data-prep-user-guide.md) 

![Passo 6](media/data-source-wizard/step5.png)

## <a name="step-7-path-column-handling"></a>Passo 7: Processamento de coluna de caminho ##

Se o caminho do ficheiro inclui dados importantes, pode optar por inclui-lo como a primeira coluna no conjunto de dados. Isto seria útil se colocarem em múltiplos ficheiros. Caso contrário, pode optar por não incluí-la.

![Passo 7](media/data-source-wizard/step6.png)

Depois de clicar em Concluir, será adicionada uma nova origem de dados para o projeto. Pode encontrá-lo sob o grupo de origens de dados na vista de dados, ou como um ficheiro .dsource o **vista do ficheiro**.
