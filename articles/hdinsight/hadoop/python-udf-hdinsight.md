---
title: Python UDF com Apache Hive e o Pig - o Azure HDInsight | Microsoft Docs
description: "Saiba como utilizar o Python utilizador definidas funções (UDF) do Hive e do Pig no HDInsight, a pilha de tecnologia de Hadoop no Azure."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: c44d6606-28cd-429b-b535-235e8f34a664
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 10/06/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 822293da48f14dc3fe29e7e95e7a30faaadbfea4
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="use-python-user-defined-functions-udf-with-hive-and-pig-in-hdinsight"></a>Utilize Python funções definidas pelo utilizador (UDF) com o Hive e Pig no HDInsight

Saiba como utilizar funções definidas pelo utilizador Python (UDF) com Apache Hive e do Pig no Hadoop no Azure HDInsight.

## <a name="python"></a>Python no HDInsight

Python2.7 é instalado por predefinição no HDInsight 3.0 e versões posteriores. Apache Hive pode ser utilizado com esta versão do Python para o processamento de fluxo. Processamento de fluxo utiliza STDOUT e STDIN para transmitir dados entre o ramo de registo e UDF.

HDInsight também inclui Jython, que é uma implementação de Python escrita em Java. Jython é executado diretamente na máquina Virtual de Java e não utiliza a transmissão em fluxo. Jython é o interpretador Python recomendado quando utiliza o Python com o Pig.

> [!WARNING]
> Os passos neste documento efetuar os seguintes pressupostos: 
>
> * Criar os scripts Python no seu ambiente de desenvolvimento local.
> * Carregar os scripts para o HDInsight utilizando o `scp` comando a partir de uma sessão de Bash local ou do script do PowerShell fornecido.
>
> Se pretender utilizar o [Shell de nuvem do Azure (bash)](https://docs.microsoft.com/azure/cloud-shell/overview) pré-visualização para trabalhar com o HDInsight, em seguida, tem de:
>
> * Crie os scripts dentro do ambiente de shell na nuvem.
> * Utilize `scp` para carregar os ficheiros a partir da shell de nuvem no HDInsight.
> * Utilize `ssh` partir da shell de nuvem para estabelecer ligação ao HDInsight e execute os exemplos.

## <a name="hivepython"></a>Ramo de registo UDF

Python pode ser utilizado como um UDF do Hive através de HiveQL `TRANSFORM` instrução. Por exemplo, o HiveQL seguinte invoca o `hiveudf.py` ficheiros armazenados na conta de armazenamento do Azure predefinido para o cluster.

**HDInsight baseado em Linux**

```hiveql
add file wasb:///hiveudf.py;

SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'python hiveudf.py' AS
    (clientid string, phoneLable string, phoneHash string)
FROM hivesampletable
ORDER BY clientid LIMIT 50;
```

**HDInsight baseado em Windows**

```hiveql
add file wasb:///hiveudf.py;

SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'D:\Python27\python.exe hiveudf.py' AS
    (clientid string, phoneLable string, phoneHash string)
FROM hivesampletable
ORDER BY clientid LIMIT 50;
```

> [!NOTE]
> Nos clusters do HDInsight baseados em Windows, o `USING` cláusula tem de especificar o caminho completo para python.exe.

Eis o que faz este exemplo:

1. O `add file` instrução no início do ficheiro adiciona o `hiveudf.py` ficheiros à cache distribuída, pelo que pode ser acedida por todos os nós do cluster.
2. O `SELECT TRANSFORM ... USING` instrução seleciona os dados a partir de `hivesampletable`. Estes passam também os valores clientid, devicemake e devicemodel para o `hiveudf.py` script.
3. O `AS` cláusula descreve os campos devolvidos de `hiveudf.py`.

<a name="streamingpy"></a>

### <a name="create-the-hiveudfpy-file"></a>Criar o ficheiro hiveudf.py


No seu ambiente de desenvolvimento, crie um ficheiro de texto com o nome `hiveudf.py`. Utilize o seguinte código como o conteúdo do ficheiro:

```python
#!/usr/bin/env python
import sys
import string
import hashlib

while True:
    line = sys.stdin.readline()
    if not line:
        break

    line = string.strip(line, "\n ")
    clientid, devicemake, devicemodel = string.split(line, "\t")
    phone_label = devicemake + ' ' + devicemodel
    print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])
```

Este script executa as seguintes ações:

1. Ler uma linha de dados de STDIN.
2. O caráter de nova linha à direita é removido utilizando `string.strip(line, "\n ")`.
3. Ao efetuar o processamento da transmissão, uma única linha contém todos os valores com um caráter de separador entre cada valor. Por isso, `string.split(line, "\t")` pode ser utilizado para dividir a entrada em cada separador, apenas os campos a devolver.
4. Quando o processamento estiver concluído, o resultado deve ser escrito para STDOUT como uma única linha, com um separador entre cada campo. Por exemplo, `print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])`.
5. O `while` ciclo repete até não `line` é de leitura.

O resultado do script é uma concatenação dos valores introduzidos para `devicemake` e `devicemodel`e um hash do valor concatenado.

Consulte [executar os exemplos](#running) como executar este exemplo no cluster do HDInsight.

## <a name="pigpython"></a>PIg UDF

Um script de Python pode ser utilizado como um UDF do Pig através de `GENERATE` instrução. Pode executar o script utilizar Jython ou C Python.

* Jython é executado a JVM e nativamente pode ser chamado de Pig.
* C Python é um processo externo, para que os dados do Pig nas JVM são enviados para o script em execução num processo Python. O resultado do script de Python é enviado para o Pig.

Para especificar o interpretador Python, utilize `register` quando referenciar o script de Python. Os exemplos seguintes registar scripts Pig como `myfuncs`:

* **Para utilizar Jython**:`register '/path/to/pigudf.py' using jython as myfuncs;`
* **Para utilizar o C Python**:`register '/path/to/pigudf.py' using streaming_python as myfuncs;`

> [!IMPORTANT]
> Quando utilizar Jython, o caminho para o ficheiro pig_jython pode ser um caminho local ou um WASB: / / caminho. No entanto, ao utilizar o C Python, tem de referenciar um ficheiro no sistema de ficheiros local do nó que está a utilizar para submeter a tarefa Pig.

Uma vez passado registo, o Pig Latin para este exemplo é o mesmo para ambas:

```pig
LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);
LOG = FILTER LOGS by LINE is not null;
DETAILS = FOREACH LOG GENERATE myfuncs.create_structure(LINE);
DUMP DETAILS;
```

Eis o que faz este exemplo:

1. A primeira linha carrega o ficheiro de dados de exemplo, `sample.log` para `LOGS`. Também define cada registo como um `chararray`.
2. A linha seguinte filtra quaisquer valores nulos, o resultado da operação para o armazenamento `LOG`.
3. Em seguida, itera sobre os registos no `LOG` e utiliza `GENERATE` para invocar a `create_structure` método contido no script de Python/Jython carregadas como `myfuncs`. `LINE`é utilizado para transmitir o registo atual para a função.
4. Por fim, as saídas são capturar para STDOUT utilizando o `DUMP` comando. Este comando apresenta os resultados após a conclusão da operação.

### <a name="create-the-pigudfpy-file"></a>Criar o ficheiro pigudf.py

No seu ambiente de desenvolvimento, crie um ficheiro de texto com o nome `pigudf.py`. Utilize o seguinte código como o conteúdo do ficheiro:

<a name="streamingpy"></a>

```python
# Uncomment the following if using C Python
#from pig_util import outputSchema

@outputSchema("log: {(date:chararray, time:chararray, classname:chararray, level:chararray, detail:chararray)}")
def create_structure(input):
    if (input.startswith('java.lang.Exception')):
        input = input[21:len(input)] + ' - java.lang.Exception'
    date, time, classname, level, detail = input.split(' ', 4)
    return date, time, classname, level, detail
```

No exemplo Pig Latin, que definimos os `LINE` de entrada como um chararray porque não há nenhum esquema consistente para a entrada. O script de Python transforma os dados para um esquema consistente para saída.

1. O `@outputSchema` instrução define o formato dos dados que são devolvidos ao Pig. Neste caso, tem um **matriz dados**, que é um tipo de dados do Pig. A matriz contém os seguintes campos, as quais são chararray (cadeias):

   * data - a data que a entrada de registo foi criada
   * hora - o que a entrada de registo foi criada
   * ClassName - o nome da classe a entrada foi criado para
   * nível - o nível de registo
   * detalhe - detalhes verboso para a entrada de registo

2. Em seguida, o `def create_structure(input)` define a função que Pig transfere a linha de itens a.

3. Os dados de exemplo, `sample.log`, principalmente está em conformidade com a data, hora, classname, nível e queremos para devolver do esquema de detalhe. No entanto, contém algumas linhas que começam com `*java.lang.Exception*`. Estas linhas têm de ser modificadas para fazer corresponder o esquema. O `if` instrução verifica os, em seguida, massages os dados de entrada para mover o `*java.lang.Exception*` cadeia até ao fim, colocar a dados em linha com o nosso esquema de saída esperado.

4. Em seguida, o `split` comando é utilizado para dividir os dados em carateres de espaço quatro em primeiro. O resultado é atribuído para `date`, `time`, `classname`, `level`, e `detail`.

5. Por fim, os valores são devolvidos para Pig.

Quando os dados, são devolvidos para o Pig, tem um esquema consistente tal como definido no `@outputSchema` instrução.

## <a name="running"></a>Carregar e executar os exemplos

> [!IMPORTANT]
> O **SSH** passos só funcionam com um cluster do HDInsight baseado em Linux. O **PowerShell** passos trabalhar com um Linux ou o HDInsight baseado em Windows cluster, mas necessitar de um cliente Windows.

### <a name="ssh"></a>SSH

Para obter mais informações sobre como utilizar o SSH, consulte [utilizar o SSH com o HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Utilize `scp` para copiar os ficheiros para o cluster do HDInsight. Por exemplo, o comando seguinte copia os ficheiros para um cluster com o nome **mycluster**.

    ```bash
    scp hiveudf.py pigudf.py myuser@mycluster-ssh.azurehdinsight.net:
    ```

2. Utilize o SSH para ligar ao cluster.

    ```bash
    ssh myuser@mycluster-ssh.azurehdinsight.net
    ```

    Para obter mais informações, veja o documento [Utilizar SSH com o HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

3. A partir de sessão SSH, adicione os ficheiros de python carregados anteriormente para o armazenamento WASB para o cluster.

    ```bash
    hdfs dfs -put hiveudf.py /hiveudf.py
    hdfs dfs -put pigudf.py /pigudf.py
    ```

Depois de carregar os ficheiros, utilize os seguintes passos para executar as tarefas do Hive e do Pig.

#### <a name="use-the-hive-udf"></a>Utilizar o Hive UDF

1. Para ligar ao ramo de registo, utilize o seguinte comando:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

    Este comando inicia o cliente Beeline.

2. Introduza a seguinte consulta no `0: jdbc:hive2://headnodehost:10001/>` linha:

   ```hive
   add file wasb:///hiveudf.py;
   SELECT TRANSFORM (clientid, devicemake, devicemodel)
       USING 'python hiveudf.py' AS
       (clientid string, phoneLabel string, phoneHash string)
   FROM hivesampletable
   ORDER BY clientid LIMIT 50;
   ```

3. Depois de introduzir a última linha, deve iniciar a tarefa. Uma vez concluída a tarefa,-devolve o resultado semelhante ao seguinte exemplo:

        100041    RIM 9650    d476f3687700442549a83fac4560c51c
        100041    RIM 9650    d476f3687700442549a83fac4560c51c
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9

4. Para sair Beeline, utilize o seguinte comando:

    ```hive
    !q
    ```

#### <a name="use-the-pig-udf"></a>Utilizar o Pig UDF

1. Para ligar a pig, utilize o seguinte comando:

    ```bash
    pig
    ```

2. Introduza as seguintes declarações no `grunt>` linha:

   ```pig
   Register wasb:///pigudf.py using jython as myfuncs;
   LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

3. Depois de introduzir a seguinte linha, deve iniciar a tarefa. Assim que a tarefa é concluída, devolve resultados semelhantes aos seguintes dados:

        ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
        ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
        ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
        ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
        ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

4. Utilizar `quit` para sair da shell Grunt e, em seguida, utilize o seguinte para editar o ficheiro de pigudf.py no sistema de ficheiros locais:

    ```bash
    nano pigudf.py
    ```

5. Uma vez no editor, anule os comentários a seguinte linha ao remover o `#` caráter a partir do início da linha:

    ```bash
    #from pig_util import outputSchema
    ```

    Isto modifica o script do Python para trabalhar com o Python C em vez de Jython. Assim que a alteração foi efetuada, utilize **Ctrl + X** para sair do editor. Selecione **Y**e, em seguida, **Enter** para guardar as alterações.

6. Utilize o `pig` comando para iniciar a shell de novo. Assim que estiver no `grunt>` linha de comandos, utilize o seguinte para executar o script de Python com o interpretador C Python.

   ```pig
   Register 'pigudf.py' using streaming_python as myfuncs;
   LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

    Assim que esta tarefa é concluída, deverá ver o mesmo resultado como quando executou anteriormente o script utilizando Jython.

### <a name="powershell-upload-the-files"></a>PowerShell: Carregar os ficheiros

Pode utilizar o PowerShell para carregar os ficheiros para o servidor do HDInsight. Utilize o seguinte script para carregar os ficheiros de Python:

> [!IMPORTANT] 
> Os passos nesta secção utilizam o Azure PowerShell. Para obter mais informações sobre como utilizar o Azure PowerShell, consulte [como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=5-41)]

> [!IMPORTANT]
> Alterar o `C:\path\to` valor para o caminho para os ficheiros no seu ambiente de desenvolvimento.

Este script obtém informações de cluster do HDInsight, em seguida, extrai a conta e chave para a conta do storage predefinida e carrega os ficheiros para a raiz do contentor.

> [!NOTE]
> Para obter mais informações sobre o carregamento de ficheiros, consulte o [carregar dados para tarefas do Hadoop no HDInsight](../hdinsight-upload-data.md) documento.

#### <a name="powershell-use-the-hive-udf"></a>PowerShell: O ramo do UDF utilizar o

Também pode ser utilizado o PowerShell remotamente executar consultas do Hive. Utilize o seguinte script do PowerShell para executar uma consulta de Hive que utiliza **hiveudf.py** script:

> [!IMPORTANT]
> Antes de executar, o script pede-lhe as informações de conta de administrador/HTTPs para o seu cluster HDInsight.

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=45-94)]

O resultado para o **Hive** tarefa deve ser semelhante ao seguinte exemplo:

    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9

#### <a name="pig-jython"></a>PIg (Jython)

Também pode ser utilizado o PowerShell para executar tarefas do Pig Latin. Para executar uma tarefa de Pig Latin que utiliza o **pigudf.py** script, utilize o seguinte script do PowerShell:

> [!NOTE]
> Quando remotamente submeter uma tarefa com o PowerShell, não é possível utilizar o C Python como o interpretador.

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=98-144)]

O resultado para o **Pig** tarefa deve ser semelhante para os seguintes dados:

    ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
    ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
    ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
    ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
    ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

## <a name="troubleshooting"></a>Resolução de Problemas

### <a name="errors-when-running-jobs"></a>Erros de tarefas em execução

Ao executar a tarefa do hive, pode encontrar um erro semelhante para o seguinte texto:

    Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: [Error 20001]: An error occurred while reading or writing to your custom script. It may have crashed with an error.

Este problema pode ser causado por endings a linha no ficheiro de Python. Muitos predefinido de editores de Windows para utilizar com CRLF como de fim de linha, mas as aplicações de Linux, normalmente, espera LF.

Pode utilizar as seguintes declarações de PowerShell para remover os carateres CR antes de carregar o ficheiro para o HDInsight:

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=148-150)]

### <a name="powershell-scripts"></a>Scripts do PowerShell

Ambos os scripts do PowerShell de exemplo utilizados para executar os exemplos contém uma linha Comentada que apresenta a saída de erro para a tarefa. Se não está a ver o resultado esperado para a tarefa, anule os comentários a seguinte linha e ver se as informações de erro indica um problema.

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=135-139)]

As informações de erro (STDERR) e o resultado da tarefa (STDOUT), também são registadas para o armazenamento do HDInsight.

| Para esta tarefa... | Observe estes ficheiros no contentor do blob |
| --- | --- |
| Hive |/ HivePython/stderr<p>/ HivePython/stdout |
| Pig |/ PigPython/stderr<p>/ PigPython/stdout |

## <a name="next"></a>Passos seguintes

Se precisar de carregar módulos do Python, que não são fornecidos por predefinição, consulte [como implementar um módulo do Azure HDInsight](http://blogs.msdn.com/b/benjguin/archive/2014/03/03/how-to-deploy-a-python-module-to-windows-azure-hdinsight.aspx).

Para outras formas de utilizar o Pig, Hive e para saber mais sobre como utilizar o MapReduce, consulte os seguintes documentos:

* [Utilizar o Hive com o HDInsight](hdinsight-use-hive.md)
* [Utilizar o Pig com o HDInsight](hdinsight-use-pig.md)
* [Utilizar o MapReduce com o HDInsight](hdinsight-use-mapreduce.md)
