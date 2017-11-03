---
title: "Combinar as colunas por utilizar o Azure Machine Learning Workbench de transformação de exemplo"
description: "O documento de referência para a transformação 'Combinar colunas por exemplo'"
services: machine-learning
author: ranvijaykumar
ms.author: ranku
manager: mwinkle
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, reference
ms.topic: article
ms.date: 09/14/2017
ms.openlocfilehash: 3ca1710c969b9bc5a1f56dc53f52c706e1ed07cd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="combine-columns-by-example-transformation"></a>Combinar as colunas por transformação de exemplo
Esta transformação permite ao utilizador adicionar uma nova coluna através da combinação de valores de várias colunas. Utilizador pode especificar um separador ou fornecem exemplos de valores combinados para efetuar esta transformação. Quando o utilizador fornece exemplos de combinação, a transformação é processada pelo mesmo **por exemplo** motor que é utilizado no **derivar coluna, por exemplo** transformação.

## <a name="how-to-perform-this-transformation"></a>Como efetuar esta transformação

Para efetuar esta transformação, siga estes passos:
1. Selecione os dois ou mais colunas que pretende combinar numa coluna. 
2. Selecione **combinar colunas, por exemplo** do **transforma** menu. Em alternativa, clique no cabeçalho de qualquer uma das colunas selecionadas e selecione **combinar colunas, por exemplo** no menu de contexto. Abre o Editor de transformação, e uma nova coluna é adicionada junto a coluna da direita mais selecionada. A nova coluna contém valores combinados separados por um separador de predefinição. Colunas selecionadas podem ser identificadas pelas caixas de verificação, os cabeçalhos de coluna. Adição e remoção das colunas da seleção podem ser feitos utilizando as caixas de verificação.
3. Pode atualizar o valor combinado na coluna recentemente criado. O valor atualizado é utilizado como um exemplo para saber a transformação.
4. Clique em **OK** para aceitar a transformação.

### <a name="transform-editor-advanced-mode"></a>Transformar o editor: modo avançado

Modo avançado fornece uma experiência mais rica para combinar as colunas. 

Selecionar **separador** em **combinar colunas por** permite que o utilizador especificar cadeias no **separador** caixa de texto. Separador enviados a partir de **separador** caixa de texto para pré-visualizar os resultados no gird a dados. Prima **OK** para consolidar a transformação.

Selecionar **exemplos** em **combinar colunas por** permite que o utilizador a fornecer exemplos de valores combinados. Para promover uma linha por exemplo, faça duplo clique nas linhas na grelha. Escreva o resultado esperado na caixa de texto em relação a linha promovida. Separador enviados a partir de **separador** caixa de texto para pré-visualizar os resultados no gird a dados. Prima **OK** para consolidar a transformação. 

Utilizador pode alternar entre o **modo básico** e **modo avançado** clicando nas ligações do Editor de transformação.

### <a name="editing-existing-transformation"></a>Editar transformação existente

Um utilizador pode editar uma existente **combinar coluna por exemplo** transformar selecionando **editar** opção do passo de transformação. Clicar no **editar** abre o Editor de transformação de mensagens em fila no **modo básico**. Utilizador pode introduzir o **modo avançado** clicando na ligação no cabeçalho. Todos os exemplos que foram fornecidos durante a criação de transformação são mostrados aqui.

## <a name="example-using-separators"></a>Exemplo utilizando separadores

Uma vírgula seguida por um espaço é utilizada como um separador neste exemplo combinar o *Rua*, *Cidade*, *estado*, e *ZIP* colunas.

|Rua|Cidade|Estado|ZIP|Coluna|
|:----|:----|:----|:----|:----|
|16011 N.E. 36th forma|REDMOND|WA|98052|16011 N.E. 36th forma, REDMOND, WA, 98052|
|16021 N.E. 36th forma|REDMOND|WA|98052|16021 N.E. 36th forma, REDMOND, WA, 98052|
|16031 N.E. 36th forma|REDMOND|WA|98052|16031 N.E. 36th forma, REDMOND, WA, 98052|
|16041 N.E. 36th forma|REDMOND|WA|98052|16041 N.E. 36th forma, REDMOND, WA, 98052|
|16051 N.E. 36th forma|REDMOND|WA|98052|16051 N.E. 36th forma, REDMOND, WA, 98052|
|16061 N.E. 36th forma|REDMOND|WA|98052|16061 N.E. 36th forma, REDMOND, WA, 98052|
|3460 157th compromissos NE|REDMOND|WA|98052|NE de compromissos da 157th 3460, REDMOND, WA, 98052|
|3350 157th Ave N.E.|REDMOND|WA|98052|N.E. de Ave da 157th 3350, REDMOND, WA, 98052|
|3240 157th compromissos N.E.|REDMOND|WA|98052|N.E. de compromissos da 157th 3240, REDMOND, WA, 98052|

## <a name="example-using-by-example"></a>Exemplo através de exemplo

O valor no **negrito** foi fornecido como um exemplo.

|Data|Mês|ano|Hora|Minuto|Segundo|Coluna combinada|
|:----|:----|:----|:----|:----|:----|:----|
|13|FPO|2016|15|01|23|**13-FPO-2016 15:01:23 PDT**|
|16|FPO|2016|16|22|33|16-FPO-2016 15:01:33 PDT|
|17|FPO|2016|12|43|12|17-FPO-2016 15:01:12 PDT|
|12|Novembro|2016|14|22|44|12-Novembro de 2016 15:01:44 PDT|
|23|Novembro|2016|01|52|45|23-Novembro de 2016 15:01:45 PDT|
|16|Janeiro|2017|22|34|56|16-Janeiro de 2016 15:01:56 PDT|
|23|Mar|2017|01|55|25|23-Mar-2016 15:01:25 PDT|
|16|Apr|2017|11|34|36|16-Apr-2016 15:01:36 PDT|

