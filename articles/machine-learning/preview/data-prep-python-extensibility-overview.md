---
title: Utilizar extensibilidade do Python com o Azure Machine Learning dados preparativos | Microsoft Docs
description: "Este documento fornece uma descrição geral e alguns exemplos de detalhado de como utilizar o código do Python para expandir a funcionalidade de preparação de dados"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/07/2017
ms.openlocfilehash: 53771c407fedc53f27a38ec3fe9b381d6b8c0dad
ms.sourcegitcommit: 2d1153d625a7318d7b12a6493f5a2122a16052e0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2017
---
# <a name="data-preparations-python-extensions"></a>Extensões de Python preparativos de dados
Como uma forma de preencher os intervalos de funcionalidade entre funções incorporadas, o Azure Machine Learning dados preparativos inclui extensibilidade em vários níveis. Neste documento, vamos descrevem a extensibilidade através do script do Python. 

## <a name="custom-code-steps"></a>Passos de código personalizado 
Dados preparativos tem os seguintes passos personalizados onde os utilizadores podem escrever código:

* Leitor de ficheiro *
* Escritor *
* Adicionar a coluna
* Filtragem avançadas
* Transformar o fluxo de dados
* Partição de transformação

* Estes passos não são atualmente suportados na execução de Spark.

## <a name="code-block-types"></a>Tipos de bloco de código 
Para cada um destes passos, suportamos dois tipos de bloco de código. Em primeiro lugar, suportamos uma expressão de Python bare, que é executado como está. Segundo, suportamos um módulo de Python onde chamamos a uma função específica com uma assinatura conhecida no código que fornece.

Por exemplo, pode adicionar uma nova coluna que calcula o registo de outra coluna seguintes duas formas:

expressão 

```python    
    math.log(row["Score"])
```

Módulo 
    
```python
def newvalue(row): 
        return math.log(row["Score"])
```


A transformação de adicionar a coluna no modo de módulo espera encontrar uma função de chamada `newvalue` que aceita uma variável de linha e devolve o valor da coluna. Este módulo pode incluir qualquer quantidade de código do Python com outras funções, importa, etc.

Os detalhes de cada ponto de extensão são abordados nas secções seguintes. 

## <a name="imports"></a>Importa 
Se utilizar o tipo de bloco de expressão, pode ainda adicionar **importar** instruções para o seu código. Todos eles têm de estar agrupados nas linhas principais do seu código.

Corrigir 

```python
import math 
import numpy 
math.log(row["Score"])
```
 

Erro  

```python
import math  
math.log(row["Score"])  
import numpy
```
 
 
Se utilizar o tipo de bloco de módulo, pode seguir todas as regras de Python normais para utilizar o **importar** instrução. 

## <a name="default-imports"></a>Importa predefinido
As seguintes importações são sempre incluídos e utilizável no seu código. Não precisa de reimportá-los. 

```python
import math  
import numbers  
import datetime  
import re  
import pandas as pd  
import numpy as np  
import scipy as sp
```
  

## <a name="install-new-packages"></a>Instalar pacotes de novo
Para utilizar um pacote que não está instalado por predefinição, primeiro tem de instalá-lo em ambientes que utiliza dados preparações. Esta instalação necessita de ser efetuada no seu computador local e em quaisquer elementos de computação que pretende executar.

Para instalar os pacotes de um destino de computação, tem de modificar o ficheiro de conda_dependencies.yml localizado na pasta aml_config na raiz do projeto.

### <a name="windows"></a>Windows 
Para localizar a localização no Windows, localize a instalação de específico da aplicação de Python e o diretório de scripts. A localização predefinida é:  

`C:\Users\<user>\AppData\Local\AmlWorkbench\Python\Scripts.` 

Em seguida, execute um dos seguintes comandos: 

`conda install <libraryname>` 

ou 

`pip install <libraryname> `

### <a name="mac"></a>Mac 
Para localizar a localização no Mac, localize a instalação de específico da aplicação de Python e o diretório de scripts. A localização predefinida é: 

`/Users/<user>/Library/Caches/AmlWorkbench>/Python/bin` 

Em seguida, execute um dos seguintes comandos: 

`./conda install <libraryname>`

ou 

`./pip install <libraryname>`

## <a name="column-data"></a>Dados da coluna 
Dados da coluna podem ser acedidos a partir de uma linha utilizando a notação de pontos ou notação de chave-valor. Nomes de colunas que contêm espaços ou carateres especiais não podem ser acedidos utilizando a notação de pontos. O `row` variável sempre deve ser definida em ambos os modos de extensões do Python (módulo e expressão). 

Exemplos 

```python
    row.ColumnA + row.ColumnB  
    row["ColumnA"] + row["ColumnB"]
```

## <a name="file-reader"></a>Leitor de ficheiro 
### <a name="purpose"></a>Objetivo 
O ponto de extensão de ficheiro leitor permite-lhe completamente o processo de leitura de um ficheiro para um fluxo de dados de controlo. O sistema chama o seu código e transmite na lista de ficheiros que deve processar. O código tem de criar e devolver um dataframe Pandas. 

>[!NOTE]
>Este ponto de extensão não funciona no Spark. 


### <a name="how-to-use"></a>Como utilizar 
Aceder a este ponto de extensão do **Open Source de dados** assistente. Escolha **ficheiro** na primeira página e, em seguida, escolha a localização do ficheiro. No **Escolher ficheiro parâmetros** na página de **tipo de ficheiro** pendente lista, escolha **ficheiros personalizada (Script)**. 

O código é dado um dataframe Pandas com o nome "df" que contém informações sobre os ficheiros que têm de ler. Se optar por abrir um diretório que contém vários ficheiros, o dataframe contém mais de uma linha.  

Este dataframe tem as seguintes colunas:

- Caminho: O ficheiro ser lido.
- PathHint: Indica onde o ficheiro está localizado. Valores: Local, AzureBlobStorage e AzureDataLakeStorage.
- AuthenticationType: O tipo de autenticação utilizado para aceder ao ficheiro. Valores: None, SasToken e OAuthToken.
- AuthenticationValue: Contém None ou o token para ser utilizado.

### <a name="syntax"></a>Sintaxe 
expressão 

```python
    paths = df['Path'].tolist()  
    df = pd.read_csv(paths[0])
```


Módulo  
```python
PathHint = Local  
def read(df):  
    paths = df['Path'].tolist()  
    filedf = pd.read_csv(paths[0])  
    return filedf  
```
 

## <a name="writer"></a>Escritor 
### <a name="purpose"></a>Objetivo 
O ponto de extensão de escritor permite-lhe completamente o processo de escrita de dados de um fluxo de dados de controlo. O sistema chama o seu código e passa num dataframe. O código pode utilizar o dataframe para escrever dados no entanto, o que pretende. 

>[!NOTE]
>O ponto de extensão do escritor não funciona no Spark.


### <a name="how-to-use"></a>Como utilizar 
Pode adicionar este ponto de extensão utilizando o bloco de escrever o fluxo de dados (Script). Está disponível de nível superior **transformações** menu.

### <a name="syntax"></a>Sintaxe 
expressão

```python
    df.to_csv('c:\\temp\\output.csv')
```

Módulo

```python
def write(df):  
    df.to_csv('c:\\temp\\output.csv')  
    return df
```
 
 
Este bloco de escrita personalizado pode existir no meio de uma lista dos passos. Se utilizar um módulo, a função de escrita tem de devolver dataframe que é a entrada para o passo seguinte. 

## <a name="add-column"></a>Adicionar a coluna 
### <a name="purpose"></a>Objetivo
O ponto de extensão Add Column permite-lhe escrever Python para calcular uma nova coluna. O código de escrita tem acesso para a linha completo. Tem de devolver um novo valor de coluna para cada linha. 

### <a name="how-to-use"></a>Como utilizar
Pode adicionar este ponto de extensão utilizando o bloco de Add Column (Script). Está disponível de nível superior **transformações** menu, bem como no **coluna** menu de contexto. 

### <a name="syntax"></a>Sintaxe
expressão

```python
    math.log(row["Score"])
```

Módulo 

```python
def newvalue(row):  
     return math.log(row["Score"])
```
 

## <a name="advanced-filter"></a>Filtragem avançadas
### <a name="purpose"></a>Objetivo 
O ponto de extensão de filtro avançada permite-lhe escrever um filtro personalizado. Tem acesso a toda a linha e o código tem de devolver VERDADEIRO (inclua a linha) ou FALSO (excluir a linha). 

### <a name="how-to-use"></a>Como utilizar
Pode adicionar este ponto de extensão utilizando o bloco de filtro avançadas (Script). Está disponível de nível superior **transformações** menu. 

### <a name="syntax"></a>Sintaxe

expressão

```python
    row["Score"] > 95
```

Módulo  

```python
def includerow(row):  
    return row["Score"] > 95
```
 

## <a name="transform-dataflow"></a>Transformar o fluxo de dados
### <a name="purpose"></a>Objetivo 
O ponto de extensão do fluxo de dados de transformação permite-lhe transformar completamente o fluxo de dados. Tem acesso a um dataframe Pandas que contém todas as colunas e linhas que está a processar. O código tem de devolver um dataframe Pandas com os novos dados. 

>[!NOTE]
>Nas Python, todos os dados a ser carregada na memória está a ser um dataframe Pandas se esta extensão é utilizada. 
>
>No Spark, todos os dados são recolhidos para um nó de trabalho único. Se os dados são muito grandes, uma função de trabalho pode ficar com memória esgotada. Utilize cuidadosamente.

### <a name="how-to-use"></a>Como utilizar 
Pode adicionar este ponto de extensão utilizando o bloco de fluxo de transformação dados (Script). Está disponível de nível superior **transformações** menu. 
### <a name="syntax"></a>Sintaxe 

expressão

```python
    df['index-column'] = range(1, len(df) + 1)  
    df = df.reset_index()
```
 

Módulo 

```python
def transform(df):  
    df['index-column'] = range(1, len(df) + 1)  
    df = df.reset_index()  
    return df
```
  

## <a name="transform-partition"></a>Partição de transformação  
### <a name="purpose"></a>Objetivo 
O ponto de extensão de transformação partição permite-lhe transformar uma partição do fluxo de dados. Tem acesso a um dataframe Pandas que contém todas as colunas e linhas para esse partição. O código tem de devolver um dataframe Pandas com os novos dados. 

>[!NOTE]
>No Python, poderá acaba por ficar com uma partição única ou várias partições, dependendo do tamanho dos dados. Spark, que está a trabalhar com um dataframe que contém os dados para uma partição num nó de trabalho especificado. Em ambos os casos, não é possível assumem que tem acesso para o conjunto completo de dados. 


### <a name="how-to-use"></a>Como utilizar
Pode adicionar este ponto de extensão utilizando o bloco de partição de transformação (Script). Está disponível de nível superior **transformações** menu. 

### <a name="syntax"></a>Sintaxe 

expressão 

```python
    df['partition-id'] = index  
    df['index-column'] = range(1, len(df) + 1)  
    df = df.reset_index()
```
 

Módulo 

```python
def transform(df, index):
    df['partition-id'] = index
    df['index-column'] = range(1, len(df) + 1)
    df = df.reset_index()
    return df
```


## <a name="datapreperror"></a>DataPrepError  
### <a name="error-values"></a>Valores de erro  
Dados preparativos, existe o conceito de valores de erro. 

É possível encontrar os valores de erro no código Python personalizado. São instâncias de uma classe de Python chamado `DataPrepError`. Esta classe encapsula num wrapper uma exceção de Python e tem duas propriedades. As propriedades contêm informações sobre o erro que ocorreram quando o valor original foi processado, bem como o valor original. 


### <a name="datapreperror-class-definition"></a>Definição de classe DataPrepError
```python 
class DataPrepError(Exception): 
    def __bool__(self): 
        return False 
``` 
A criação de um DataPrepError no framework dados preparativos Python, geralmente, este aspeto: 
```python 
DataPrepError({ 
   'message':'Cannot convert to numeric value', 
   'originalValue': value, 
   'exceptionMessage': e.args[0], 
   '__errorCode__':'Microsoft.DPrep.ErrorValues.InvalidNumericType' 
}) 
``` 
#### <a name="how-to-use"></a>Como utilizar 
É possível quando o Python executa um ponto de extensão para gerar DataPrepErrors como valores de retorno utilizando o método de criação de anterior. É muito mais provável que DataPrepErrors forem encontrados quando os dados são processados um ponto de extensão. Neste momento, o código de Python personalizado tem de processar um DataPrepError como um tipo de dados válido.

#### <a name="syntax"></a>Sintaxe 
expressão 
```python 
    if (isinstance(row["Score"], DataPrepError)): 
        row["Score"].originalValue 
    else: 
        row["Score"] 
``` 
```python 
    if (hasattr(row["Score"], "originalValue")): 
        row["Score"].originalValue 
    else: 
        row["Score"] 
``` 
Módulo 
```python 
def newvalue(row): 
    if (isinstance(row["Score"], DataPrepError)): 
        return row["Score"].originalValue 
    else: 
        return row["Score"] 
``` 
```python 
def newvalue(row): 
    if (hasattr(row["Score"], "originalValue")): 
        return row["Score"].originalValue 
    else: 
        return row["Score"] 
```  
