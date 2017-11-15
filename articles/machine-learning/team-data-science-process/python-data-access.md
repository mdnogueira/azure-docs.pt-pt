---
title: Aceder a conjuntos de dados com a biblioteca de clientes do Machine Learning Python | Microsoft Docs
description: "Instalar e utilizar a biblioteca de clientes do Python para aceder e gerir dados do Azure Machine Learning em segurança a partir de um ambiente Python local."
services: machine-learning
documentationcenter: python
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 9ab42272-c30c-4b7e-8e66-d64eafef22d0
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: huvalo;bradsev
ms.openlocfilehash: 3cffb90baadef570bbb7e4e2d96741cc0420b0a2
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2017
---
# <a name="access-datasets-with-python-using-the-azure-machine-learning-python-client-library"></a>Utilizar a biblioteca de clientes de Python do Azure Machine Learning para aceder a conjuntos de dados com Python
A pré-visualização da biblioteca de clientes do Microsoft Azure Machine Learning Python pode permitir um acesso seguro para os conjuntos de dados do Azure Machine Learning a partir de um ambiente Python local e permite a criação e gestão de conjuntos de dados numa área de trabalho.

Este tópico fornece instruções sobre como:

* instalar a biblioteca de clientes de Machine Learning Python 
* aceder e carregar conjuntos de dados, incluindo instruções sobre como obter autorização para aceder à Azure Machine Learning conjuntos de dados do seu ambiente local do Python
* conjuntos de dados intermédios do acesso a partir das experimentações
* utilizar a biblioteca de cliente do Python para enumerar os conjuntos de dados, acedem a metadados, ler o conteúdo de um conjunto de dados, criar novos conjuntos de dados e atualizar conjuntos de dados existentes

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="prerequisites"></a>Pré-requisitos
A biblioteca de clientes do Python foi testada em seguintes ambientes:

* Windows, Mac e Linux
* Python 2.7, 3.3 e 3.4

Tem uma dependência nos seguintes pacotes:

* pedidos
* Python dateutil
* pandas

Recomendamos que utilize uma distribuição do Python, tais como [Anaconda](http://continuum.io/downloads#all) ou [Canopy](https://store.enthought.com/downloads/), que são fornecidos com o Python, IPython e instalar três pacotes listados acima. Embora não seja estritamente necessária IPython, é um ambiente excelente para manipular e visualizar dados de forma interativa.

### <a name="installation"></a>Como instalar a biblioteca de cliente do Azure Machine Learning Python
A biblioteca de cliente do Azure Machine Learning Python também tem de estar instalada para executar as tarefas descritas neste tópico. Está disponível no [índice de pacote do Python](https://pypi.python.org/pypi/azureml). Para instalá-lo no seu ambiente do Python, execute o seguinte comando do seu ambiente Python local:

    pip install azureml

Em alternativa, pode transferir e instalar das origens de em [github](https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python).

    python setup.py install

Se tiver o git instalado no seu computador, pode utilizar o pip para instalar diretamente a partir do repositório de git:

    pip install git+https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python.git


## <a name="datasetAccess"></a>Utilizar fragmentos de código Studio para aceder a conjuntos de dados
A biblioteca de clientes do Python dá-lhe acesso programático para os conjuntos de dados existentes a partir das experimentações que foram executadas.

Da interface de web Studio, pode gerar fragmentos de código que incluem todas as informações necessárias para transferir e anular a serialização de conjuntos de dados como Pandas DataFrame objetos no seu computador de localização.

### <a name="security"></a>Segurança para acesso a dados
Os fragmentos de código fornecidos pelo Studio para utilizar com a biblioteca de clientes do Python incluem o seu id da área de trabalho e a autorização token. Estes disponibilizar acesso total à sua área de trabalho e tem de estar protegidas, como uma palavra-passe.

Por motivos de segurança, a funcionalidade de fragmento de código só está disponível para utilizadores que tenham a respetiva função definida como **proprietário** para a área de trabalho. A função é apresentada no Azure Machine Learning Studio no **utilizadores** página em **definições**.

![Segurança][security]

Se a sua função não está definida como **proprietário**, pode um pedido para reinvited como um proprietário ou peça ao proprietário da área de trabalho para lhe fornecer o fragmento de código.

Para obter o token de autorização, pode efetuar um dos seguintes:

* Pedir um token de um proprietário. Os proprietários podem aceder os tokens de autorização da página de definições da sua área de trabalho no Studio. Selecione **definições** do painel esquerdo e clique em **TOKENS de autorização** para ver os tokens primários e secundários.  Embora o principal ou os tokens de autorização secundário podem ser utilizados no fragmento de código, recomenda-se que os proprietários de partilham só os tokens de autorização secundário.

![Tokens de autorização](./media/python-data-access/ml-python-access-settings-tokens.png)

* Peça a ser promovido a função de proprietário.  Para tal, um proprietário atual de área de trabalho tem de primeiro remover-a efectuar a área de trabalho, em seguida, volte convidá-lo ao mesmo como um proprietário.

Assim que os programadores obter o id da área de trabalho e a autorização tokens, estes são capazes de aceder à área de trabalho utilizando o fragmento de código, independentemente da respetiva função.

Tokens de autorização são geridos no **TOKENS de autorização** página em **definições**. Pode gerar novamente, mas este procedimento revoga acesso para o token anterior.

### <a name="accessingDatasets"></a>Conjuntos de dados de acesso de uma aplicação local do Python
1. No Machine Learning Studio, clique em **conjuntos de dados** na barra de navegação à esquerda.
2. Selecione o conjunto de dados que pretende aceder. Pode selecionar qualquer um dos conjuntos de dados do **conjuntos de dados os meus** lista ou do **amostras** lista.
3. Na barra de ferramentas inferior, clique em **gerar código de acesso de dados**. Se os dados estão num formato incompatível com a biblioteca de clientes do Python, este botão está desativado.
   
    ![Conjuntos de dados][datasets]
4. Selecione o fragmento de código a partir da janela do que aparece e copie-a para a sua área de transferência.
   
    ![Código de acesso][dataset-access-code]
5. Cole o código no bloco de notas da sua aplicação local do Python.
   
    ![Bloco de notas][ipython-dataset]

## <a name="accessingIntermediateDatasets"></a>Conjuntos de dados intermédios do acesso de experimentações de Machine Learning
Após a execução uma experimentação no Machine Learning Studio, é possível aceder os conjuntos de dados intermédios de nós de módulos de saída. Conjuntos de dados intermédios são dados que foi criados e utilizados para passos intermédios quando uma ferramenta de modelo já foi executada.

Conjuntos de dados intermédios podem ser acedidos, desde que o formato dos dados é compatível com a biblioteca de clientes do Python.

São suportados os seguintes formatos (constantes para estes estão no `azureml.DataTypeIds` classe):

* Texto simples
* GenericCSV
* GenericTSV
* GenericCSVNoHeader
* GenericTSVNoHeader

Pode determinar o formato por cima de um nó de saída do módulo. Será apresentado juntamente com o nome de nó numa descrição.

Alguns dos módulos, tais como o [divisão] [ split] módulo, para um formato com o nome de saída `Dataset`, que não é suportado pela biblioteca de clientes de Python.

![Formato de conjunto de dados][dataset-format]

Tem de utilizar um módulo de conversão, tais como [converter para CSV][convert-to-csv], para obter um resultado para um formato suportado.

![Formato de GenericCSV][csv-format]

Os passos seguintes mostram um exemplo que cria uma experimentação, executa-o e acede ao conjunto de dados intermédio.

1. Crie uma nova experimentação.
2. Inserir uma **conjunto de dados para adultos receitas Census da classificação do binário** módulo.
3. Inserir uma [divisão] [ split] módulo e ligue-se a entrada para a saída do módulo de conjunto de dados.
4. Inserir uma [converter para CSV] [ convert-to-csv] módulo e ligue-se a entrada para um do [divisão] [ split] produz do módulo.
5. Guarde a experimentação, executá-la e aguarde que termine a execução.
6. Clique no nó de saída no [converter para CSV] [ convert-to-csv] módulo.
7. Quando for apresentado o menu de contexto, selecione **gerar código de acesso de dados**.
   
    ![Menu de contexto][experiment]
8. Selecione o fragmento de código e copie-o à sua área de transferência da janela apresentada.
   
    ![Código de acesso][intermediate-dataset-access-code]
9. Cole o código no seu bloco de notas.
   
    ![Bloco de notas][ipython-intermediate-dataset]
10. Pode visualizar os dados utilizando matplotlib. Esta ação apresenta um histograma da coluna de idade:
    
    ![Histograma][ipython-histogram]

## <a name="clientApis"></a>Utilizar a biblioteca de cliente de Machine Learning Python para aceder, ler, criar e gerir conjuntos de dados
### <a name="workspace"></a>Área de trabalho
A área de trabalho é o ponto de entrada para a biblioteca de cliente do Python. Forneça o `Workspace` token de classe com o id da área de trabalho e a autorização para criar uma instância:

    ws = Workspace(workspace_id='4c29e1adeba2e5a7cbeb0e4f4adfb4df',
                   authorization_token='f4f3ade2c6aefdb1afb043cd8bcf3daf')


### <a name="enumerate-datasets"></a>Enumerar os conjuntos de dados
Enumerar todos os conjuntos de dados numa determinada área de trabalho:

    for ds in ws.datasets:
        print(ds.name)

Enumerar apenas criados pelo utilizador conjuntos de dados:

    for ds in ws.user_datasets:
        print(ds.name)

Enumerar apenas os conjuntos de dados de exemplo:

    for ds in ws.example_datasets:
        print(ds.name)

Pode aceder a um conjunto de dados por nome (que tem maiúsculas e minúsculas):

    ds = ws.datasets['my dataset name']

Ou pode aceder ao mesmo pelo índice:

    ds = ws.datasets[0]


### <a name="metadata"></a>Metadados
Conjuntos de dados tem metadados, para além do conteúdo. (Intermédios conjuntos de dados são uma exceção a esta regra e não tem quaisquer metadados.)

Alguns valores de metadados estão atribuídos pelo utilizador no momento de criação:

    print(ds.name)
    print(ds.description)
    print(ds.family_id)
    print(ds.data_type_id)

Outros recursos são valores atribuídos pelo Azure ML:

    print(ds.id)
    print(ds.created_date)
    print(ds.size)

Consulte o `SourceDataset` classe para obter mais informações sobre os metadados disponível.

### <a name="read-contents"></a>Ler o conteúdo
Os fragmentos de código fornecidos pelo Machine Learning Studio automaticamente transferiram e anular a serialização de um objeto de Pandas DataFrame o conjunto de dados. Isto é feito com o `to_dataframe` método:

    frame = ds.to_dataframe()

Se preferir transferir os dados não processados e efetuar a anulação da serialização por si, que é uma opção. De momento, esta é a única opção para formatos, tais como 'ARFF', não é possível anular a serialização da biblioteca de clientes do Python.

Para ler o conteúdo como texto:

    text_data = ds.read_as_text()

Para ler o conteúdo como binário:

    binary_data = ds.read_as_binary()

Pode também simplesmente abrir uma transmissão em fluxo para o conteúdo:

    with ds.open() as file:
        binary_data_chunk = file.read(1000)


### <a name="create-a-new-dataset"></a>Criar um novo conjunto de dados
A biblioteca de clientes do Python permite-lhe carregar conjuntos de dados do seu programa de Python. Estes conjuntos de dados, em seguida, estão disponíveis para utilização na sua área de trabalho.

Se tiver um DataFrame Pandas os dados, utilize o seguinte código:

    from azureml import DataTypeIds

    dataset = ws.datasets.add_from_dataframe(
        dataframe=frame,
        data_type_id=DataTypeIds.GenericCSV,
        name='my new dataset',
        description='my description'
    )

Se os dados já está a ser serializados, pode utilizar:

    from azureml import DataTypeIds

    dataset = ws.datasets.add_from_raw_data(
        raw_data=raw_data,
        data_type_id=DataTypeIds.GenericCSV,
        name='my new dataset',
        description='my description'
    )

A biblioteca de clientes do Python é capaz de serializar um DataFrame Pandas para os seguintes formatos (constantes para estes estão no `azureml.DataTypeIds` classe):

* Texto simples
* GenericCSV
* GenericTSV
* GenericCSVNoHeader
* GenericTSVNoHeader

### <a name="update-an-existing-dataset"></a>Atualizar um conjunto de dados existente
Se tentar carregar um novo conjunto de dados com um nome que corresponde a um conjunto de dados existente, deve obter um erro de conflito.

Para atualizar um conjunto de dados existente, terá primeiro de obter uma referência ao conjunto de dados existente:

    dataset = ws.datasets['existing dataset']

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Em seguida, utilize `update_from_dataframe` para serializar e substitua os conteúdos do conjunto de dados no Azure:

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(frame2)

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Se pretender serializar os dados num formato diferente, especifique um valor para o opcional `data_type_id` parâmetro.

    from azureml import DataTypeIds

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        data_type_id=DataTypeIds.GenericTSV,
    )

    print(dataset.data_type_id) # 'GenericTSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Opcionalmente, pode definir uma nova descrição, especificando um valor para o `description` parâmetro.

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        description='data up to feb 2015',
    )

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to feb 2015'

Opcionalmente, pode definir um novo nome, especificando um valor para o `name` parâmetro. De agora no, irá obter o conjunto de dados utilizando o novo nome. O seguinte código atualiza os dados, o nome e a descrição.

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        name='existing dataset v2',
        description='data up to feb 2015',
    )

    print(dataset.data_type_id)                    # 'GenericCSV'
    print(dataset.name)                            # 'existing dataset v2'
    print(dataset.description)                     # 'data up to feb 2015'

    print(ws.datasets['existing dataset v2'].name) # 'existing dataset v2'
    print(ws.datasets['existing dataset'].name)    # IndexError

O `data_type_id`, `name` e `description` parâmetros são opcionais e predefinida para o respetivo valor anterior. O `dataframe` parâmetro é sempre necessário.

Se os dados já está a ser serializados, utilize `update_from_raw_data` em vez de `update_from_dataframe`. Se passa no `raw_data` em vez de `dataframe`, funciona de forma semelhante.

<!-- Images -->
[security]:./media/python-data-access/security.png
[dataset-format]:./media/python-data-access/dataset-format.png
[csv-format]:./media/python-data-access/csv-format.png
[datasets]:./media/python-data-access/datasets.png
[dataset-access-code]:./media/python-data-access/dataset-access-code.png
[ipython-dataset]:./media/python-data-access/ipython-dataset.png
[experiment]:./media/python-data-access/experiment.png
[intermediate-dataset-access-code]:./media/python-data-access/intermediate-dataset-access-code.png
[ipython-intermediate-dataset]:./media/python-data-access/ipython-intermediate-dataset.png
[ipython-histogram]:./media/python-data-access/ipython-histogram.png


<!-- Module References -->
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/

