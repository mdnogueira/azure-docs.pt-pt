---
title: "Passo 3: Criar uma nova experimentação do Machine Learning | Microsoft Docs"
description: "Passo 3 da desenvolver uma solução preditiva instruções: criar uma nova experimentação de preparação no Azure Machine Learning Studio."
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 660e3c27-55ef-4c33-a4e9-dff4d1224630
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.author: garye
ms.openlocfilehash: a8f1764204740a8f5ef757e5e2ad63cfd43af150
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="walkthrough-step-3-create-a-new-azure-machine-learning-experiment"></a>Passo 3 das Instruções: Criar uma experimentação nova do Azure Machine Learning
Este é o terceiro passo de instruções, [desenvolver uma solução de Análise Preditiva no Azure Machine Learning](walkthrough-develop-predictive-solution.md)

1. [Criar uma área de trabalho do Machine Learning](walkthrough-1-create-ml-workspace.md)
2. [Carregar os dados existentes](walkthrough-2-upload-data.md)
3. **Criar uma nova experimentação**
4. [Dar formação e avaliar os modelos](walkthrough-4-train-and-evaluate-models.md)
5. [Implementar o serviço Web](walkthrough-5-publish-web-service.md)
6. [Aceder ao serviço Web](walkthrough-6-access-web-service.md)

- - -
Esta explicação passo a passo, o próximo passo é criar uma experimentação no Machine Learning Studio, que utiliza o conjunto de dados que foi carregadas.  

1. No Studio, clique em **+ novo** na parte inferior da janela.
2. Selecione **experimentação**e, em seguida, selecione "Experimentação em branco". 

    ![Criar uma nova experimentação][0]

2. Selecione o nome predefinido da experimentação na parte superior da tela e mude o nome para algo significativo.

    ![Mudar o nome experimentação][5]
   
   > [!TIP]
   > É uma boa prática preencha **resumo** e **Descrição** para a experimentação no **propriedades** painel. Estas propriedades dão-lhe a oportunidade para documentar a experimentação, para que qualquer pessoa que observa o-lo mais tarde será compreender os seus objetivos e metodologia.
   > 
   > ![Propriedades de experimentação][6]
   > 
3. Na paleta do módulo para a esquerda da tela de experimentação, expanda **conjuntos de dados guardado**.
4. Localizar o conjunto de dados que criou em **conjuntos de dados os meus** e arraste-o para a tela. Também pode encontrar o conjunto de dados ao introduzir o nome no **pesquisa** caixa acima a paleta.  

    ![Adicionar o conjunto de dados para a experimentação][7]

## <a name="prepare-the-data"></a>Preparar os dados
Pode ver as primeiros 100 linhas dos dados e algumas informações estatísticas para todo o conjunto de dados: clique na porta de saída do conjunto de dados (pequeno círculo na parte inferior) e selecione **visualizar**.  

Porque o ficheiro de dados não são fornecidos com cabeçalhos de coluna, Studio forneceu cabeçalhos genéricos (Col1, Col2, *etc.*). Cabeçalhos de boas não são essenciais para criar um modelo, mas tornam mais fácil trabalhar com os dados a experimentação. Além disso, quando, eventualmente, vamos publicar este modelo num serviço web, os cabeçalhos de ajudam a identificar as colunas para o utilizador do serviço.  

Iremos pode adicionar cabeçalhos de coluna a utilizar o [Editar metadados] [ edit-metadata] módulo.
Utilizar o [Editar metadados] [ edit-metadata] módulo para alterar os metadados associados um conjunto de dados. Neste caso, vamos utilizá-lo para fornecer nomes amigáveis mais para cabeçalhos de coluna. 

Para utilizar [Editar metadados][edit-metadata], especificar primeiro as colunas a modificar (neste caso, todos eles.) Em seguida, especificar a ação para ser efetuada nessas colunas (neste caso, a alteração de cabeçalhos de coluna.)

1. Na paleta do módulo, escreva "metadados" o **pesquisa** caixa. O [Editar metadados] [ edit-metadata] aparece na lista de módulos.

2. Clique e arraste o [Editar metadados] [ edit-metadata] módulo para a tela e removê-la abaixo o conjunto de dados que adicionamos anteriormente.

3. Ligar o conjunto de dados para o [Editar metadados][edit-metadata]: clique na porta de saída do conjunto de dados (pequeno círculo na parte inferior do conjunto de dados), arraste para a porta de entrada de [Editar metadados] [ edit-metadata] (o pequeno círculo na parte superior do módulo), em seguida, liberte o botão do rato. O conjunto de dados e o módulo permanecem ligadas, mesmo se mover um em torno da tela.
   
   A experimentação deve agora algo semelhante ao seguinte:  
   
   ![Adicionar metadados de edição][1]
   
   O ponto de exclamação vermelho indica que ainda não definimos as propriedades para este módulo ainda. Iremos vai fazê-lo nesse seguinte.
   
   > [!TIP]
   > Pode adicionar um comentário a um módulo, fazendo duplo clique no módulo e introduzindo o texto. Isto pode ajudá-lo a ver rapidamente o que o módulo está a fazer na sua experimentação. Neste caso, faça duplo clique o [Editar metadados] [ edit-metadata] módulo e escreva os comentário "Adicionar cabeçalhos de coluna". Clique em qualquer lugar pessoa na tela, para fechar a caixa de texto. Para apresentar o comentário, clique na seta para baixo no módulo.
   > 
   > ![Editar metadados do módulo com o comentário adicionado][8]
   > 
4. Selecione [Editar metadados][edit-metadata]e o **propriedades** painel à direita da tela, clique em **Seletor de coluna**.

5. No **selecionar colunas** caixa de diálogo, selecione todas as linhas no **colunas disponíveis** e clique em > ao movê-los para **colunas selecionadas**.
   A caixa de diálogo deve ter o seguinte aspeto:

   ![Seletor de coluna com todas as colunas selecionadas][2]

6. Clique em de **OK** marca de verificação.

7. Volta a **propriedades** painel, procure o **novos nomes de coluna** parâmetro. Neste campo, introduza uma lista de nomes para as 21 colunas no conjunto de dados, separados por vírgulas e pela ordem de coluna. Pode obter os nomes de colunas na documentação do conjunto de dados no site da UCI ou para sua comodidade, pode copiar e colar a lista seguinte:  
   
       Status of checking account, Duration in months, Credit history, Purpose, Credit amount, Savings account/bond, Present employment since, Installment rate in percentage of disposable income, Personal status and sex, Other debtors, Present residence since, Property, Age in years, Other installment plans, Housing, Number of existing credits, Job, Number of people providing maintenance for, Telephone, Foreign worker, Credit risk  
   
   O painel de propriedades tem o seguinte aspeto:
   
   ![Propriedades de metadados de edição][3]

> [!TIP]
> Se pretender verificar os cabeçalhos de coluna, execute a experimentação (clique **executar** abaixo a tela de experimentação). Quando terminar em execução (aparece uma marca de verificação verde [Editar metadados][edit-metadata]), clique na porta de saída do [Editar metadados] [ edit-metadata] módulo, e selecione **visualizar**. Pode ver o resultado de qualquer módulo da mesma forma para ver o progresso dos dados através de experimentação.
> 
> 

## <a name="create-training-and-test-datasets"></a>A criação de formação e teste conjuntos de dados
Temos alguns dados para preparar o modelo e alguns testá-lo.
Para o passo seguinte da experimentação, iremos dividir o conjunto de dados em dois conjuntos de dados separados: um para o nosso modelo e outra para testá-lo de preparação.

Para tal, utilizamos o [dividir dados] [ split] módulo.  

1. Localizar o [dividir dados] [ split] módulo, arraste-o para a tela e ligá-lo para o [Editar metadados] [ edit-metadata] módulo.

2. Por predefinição, o rácio de divisão é 0,5 e **Randomized divisão** parâmetro está definido. Isto significa que um meio aleatório dos dados de saída através de uma porta do [dividir dados] [ split] módulo e metade através de outro. Pode ajustar estes parâmetros, bem como o **seed aleatório** parâmetro, para alterar a divisão entre formação e testar os dados. Neste exemplo, vamos deixá-los como-é.
   
   > [!TIP]
   > A propriedade **fração de linhas no primeiro conjunto de dados de saída** determina a quantidade dos dados de saída através de é o *esquerdo* porta de saída. Por exemplo, se definir o rácio para 0,7, em seguida, 70% dos dados é saída pela porta esquerda e 30% através da porta à direita.  
   > 
   > 

3. Faça duplo clique o [dividir dados] [ split] módulo e introduza o comentário, "dados de formação/teste dividir 50%". 

Podemos utilizar as saídas do [dividir dados] [ split] módulo no entanto, como, mas vamos escolha para utilizar a saída à esquerda, como utilizadores de teste como dados de saída de dados de formação e à direita.  

Tal como mencionado no [passo anterior](walkthrough-2-upload-data.md), o custo de misclassifying um risco de crédito elevada como baixa é cinco vezes superior ao custo de misclassifying um risco de crédito baixa como alta. A conta de isto, iremos gerar um novo conjunto de dados que reflete esta função de custo. No novo conjunto de dados, cada exemplo de alto risco é replicado cinco vezes, enquanto cada exemplo de baixo risco não é replicado.   

Podemos fazer esta replicação com o código do R:  

1. Localize e arraste o [executar Script do R] [ execute-r-script] módulo para a tela de experimentação. 

2. Ligue a porta de saída à esquerda do [dividir dados] [ split] módulo para a primeira porta de entrada ("Dataset1") da [executar Script do R] [ execute-r-script] módulo.

3. Faça duplo clique o [executar Script do R] [ execute-r-script] módulo e introduza o comentário "Definir o custo ajuste".

4. No **propriedades** painel, elimine o texto predefinido a **R Script** parâmetro e introduza este script:
   
       dataset1 <- maml.mapInputPort(1)
       data.set<-dataset1[dataset1[,21]==1,]
       pos<-dataset1[dataset1[,21]==2,]
       for (i in 1:5) data.set<-rbind(data.set,pos)
       maml.mapOutputPort("data.set")

    ![Script do R no módulo executar Script do R][9]

Temos de fazer esta mesma operação de replicação para cada resultado do [dividir dados] [ split] módulo para que os dados de formação e testes tenham a mesma ajuste de custo. A forma mais fácil de fazê-lo é ao duplicar o [executar Script do R] [ execute-r-script] módulo Tornamos apenas e ligar a outros porta de saída a [dividir dados] [ split] módulo.

1. Clique com botão direito do [executar Script do R] [ execute-r-script] módulo e selecione **cópia**.

2. Clique com o botão direito a tela de experimentação e selecione **colar**.

3. Arraste o módulo de novo na posição e, em seguida, ligar-se na porta saída à direita do [dividir dados] [ split] módulo para a primeira porta de entrada deste novo [executar Script do R] [ execute-r-script] módulo. 

4. Na parte inferior da tela, clique em **executar**. 

> [!TIP]
> A cópia do módulo executar Script do R contém o mesmo script, como o módulo original. Quando copiar e colar um módulo na tela, a cópia retém todas as propriedades do original.  
> 
> 

A nossa experiência agora aspeto semelhante ao seguinte:

![Adicionar o módulo de divisão e scripts de R][4]

Para obter mais informações sobre como utilizar os scripts de R nas suas experimentações, consulte [expandir a sua experiência com R](extend-your-experiment-with-r.md).

**Seguinte: [formação e avaliar os modelos](walkthrough-4-train-and-evaluate-models.md)**

[0]: ./media/walkthrough-3-create-new-experiment/create-new-experiment.png
[5]: ./media/walkthrough-3-create-new-experiment/rename-experiment.png
[6]: ./media/walkthrough-3-create-new-experiment/experiment-properties.png
[7]: ./media/walkthrough-3-create-new-experiment/add-dataset-to-experiment.png
[8]: ./media/walkthrough-3-create-new-experiment/edit-metadata-with-comment.png
[9]: ./media/walkthrough-3-create-new-experiment/execute-r-script.png
[1]: ./media/walkthrough-3-create-new-experiment/experiment-with-edit-metadata-module.png
[2]: ./media/walkthrough-3-create-new-experiment/select-columns.png
[3]: ./media/walkthrough-3-create-new-experiment/edit-metadata-properties.png
[4]: ./media/walkthrough-3-create-new-experiment/experiment.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
