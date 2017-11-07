---
title: "Gerar recomendações utilizando Mahout e HDInsight (SSH) - Azure | Microsoft Docs"
description: "Saiba como utilizar a Apache Mahout biblioteca de machine learning para gerar recomendações de filmes com o HDInsight (Hadoop)."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: c78ec37c-9a8c-4bb6-9e38-0bdb9e89fbd7
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/06/2017
ms.author: larryfr
ms.openlocfilehash: 65d6dc7bf96666f004038c6dae00d2f4e9ea5d7f
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2017
---
# <a name="generate-movie-recommendations-by-using-apache-mahout-with-linux-based-hadoop-in-hdinsight-ssh"></a>Gerar recomendações de filmes utilizando Mahout do Apache com Hadoop baseado em Linux no HDInsight (SSH)

[!INCLUDE [mahout-selector](../../../includes/hdinsight-selector-mahout.md)]

Saiba como utilizar o [Apache Mahout](http://mahout.apache.org) biblioteca de aprendizagem máquina com o Azure HDInsight para gerar recomendações de filmes.

Mahout é um [machine learning] [ ml] biblioteca para o Apache Hadoop. Mahout contém algoritmos para o processamento de dados, tais como a filtragem de classificação e o clustering. Neste artigo, pode utilizar um motor de recomendação para gerar recomendações de filmes que se baseiem filmes que tem visto seus amigos.

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do HDInsight baseado em Linux. Para obter informações sobre como criar um, consulte [começar a utilizar o Hadoop baseado em Linux no HDInsight][getstarted].

> [!IMPORTANT]
> O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).

* Um cliente SSH. Para obter mais informações, veja o documento [Utilizar SSH com o HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="mahout-versioning"></a>Mahout controlo de versões

Para obter mais informações sobre a versão do Mahout no HDInsight, consulte [HDInsight versões e componentes do Hadoop](../hdinsight-component-versioning.md).

## <a name="recommendations"></a>Recomendações de compreender

Uma das funções que é fornecida pela Mahout é um motor de recomendação. Este motor aceita dados no formato de `userID`, `itemId`, e `prefValue` (a preferência para o item). Mahout, em seguida, pode executar uma análise do occurance conjunta para determinar: *utilizadores que têm uma preferência de um item tem também uma preferência destas outros itens*. Mahout determina, em seguida, os utilizadores com as preferências de item semelhante, que podem ser utilizados para efetuar recomendações.

O seguinte fluxo de trabalho é um exemplo simplificado que utiliza dados de filmes:

* **Occurance conjunta**: Joe, Alice e Bernardo todos os gostou *estrela Wars*, *o Empire Strikes novamente*, e *retorno do Jedi*. Mahout determina que os utilizadores que como qualquer uma destes filmes também como as outras duas.

* **Occurance conjunta**: João e Alice também gostou *o Phantom Menace*, *ataque dos Clones*, e *Revenge do Sith*. Mahout determina que os utilizadores que gostou também os três filmes anteriores, como estes três filmes.

* **Recomendação de semelhança**: Joe porque gostou os três primeiros filmes, Mahout observa filmes que outras pessoas com as preferências semelhantes que gostou, mas Joe não foi observada (gostou/classificados). Neste caso, recomenda Mahout *o Phantom Menace*, *ataque dos Clones*, e *Revenge do Sith*.

### <a name="understanding-the-data"></a>Compreender os dados

Comodamente, [GroupLens Research] [ movielens] fornece dados de classificação de filmes num formato que é compatível com o Mahout. Estes dados ficarem disponíveis no armazenamento de predefinido do cluster em `/HdiSamples/HdiSamples/MahoutMovieData`.

Existem dois ficheiros, `moviedb.txt` e `user-ratings.txt`. O `user-ratings.txt` ficheiro é utilizado durante a análise. O `moviedb.txt` é utilizado para fornecer as informações de texto intuitivo ao visualizar os resultados.

Os dados contidos no utilizador ratings.txt tem uma estrutura de `userID`, `movieID`, `userRating`, e `timestamp`, que indica como altamente cada utilizador classificados um filme. Eis um exemplo dos dados:

    196    242    3    881250949
    186    302    3    891717742
    22    377    1    878887116
    244    51    2    880606923
    166    346    1    886397596

## <a name="run-the-analysis"></a>Executar a análise

A partir de uma ligação SSH para o cluster, utilize o seguinte comando para executar a tarefa de recomendação:

```bash
mahout recommenditembased -s SIMILARITY_COOCCURRENCE -i /HdiSamples/HdiSamples/MahoutMovieData/user-ratings.txt -o /example/data/mahoutout --tempDir /temp/mahouttemp
```

> [!NOTE]
> A tarefa pode demorar vários minutos a concluir e pode executar várias tarefas de MapReduce.

## <a name="view-the-output"></a>Ver o resultado

1. Uma vez concluída a tarefa, utilize o seguinte comando para ver o resultado gerado:

    ```bash
    hdfs dfs -text /example/data/mahoutout/part-r-00000
    ```

    É apresentado o resultado da seguinte forma:

        1    [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
        2    [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
        3    [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
        4    [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

    A primeira coluna é a `userID`. Os valores contidos em ' [' e ']' são `movieId`:`recommendationScore`.

2. Pode utilizar a saída, juntamente com o moviedb.txt, para fornecer mais informações sobre as recomendações. Em primeiro lugar, copie os ficheiros localmente, utilizando os seguintes comandos:

    ```bash
    hdfs dfs -get /example/data/mahoutout/part-r-00000 recommendations.txt
    hdfs dfs -get /HdiSamples/HdiSamples/MahoutMovieData/* .
    ```

    Este comando copia os dados de saída para um ficheiro denominado **recommendations.txt** no diretório atual, juntamente com os ficheiros de dados de filmes.

3. Utilize o seguinte comando para criar um script de Python que procura nomes de filmes para os dados no resultado recomendações:

    ```bash
    nano show_recommendations.py
    ```

    Quando abre o editor, utilize o seguinte texto como o conteúdo do ficheiro:

   ```python
   #!/usr/bin/env python

   import sys

   if len(sys.argv) != 5:
        print "Arguments: userId userDataFilename movieFilename recommendationFilename"
        sys.exit(1)

   userId, userDataFilename, movieFilename, recommendationFilename = sys.argv[1:]

   print "Reading Movies Descriptions"
   movieFile = open(movieFilename)
   movieById = {}
   for line in movieFile:
       tokens = line.split("|")
       movieById[tokens[0]] = tokens[1:]
   movieFile.close()

   print "Reading Rated Movies"
   userDataFile = open(userDataFilename)
   ratedMovieIds = []
   for line in userDataFile:
       tokens = line.split("\t")
       if tokens[0] == userId:
           ratedMovieIds.append((tokens[1],tokens[2]))
   userDataFile.close()

   print "Reading Recommendations"
   recommendationFile = open(recommendationFilename)
   recommendations = []
   for line in recommendationFile:
       tokens = line.split("\t")
       if tokens[0] == userId:
           movieIdAndScores = tokens[1].strip("[]\n").split(",")
           recommendations = [ movieIdAndScore.split(":") for movieIdAndScore in movieIdAndScores ]
           break
   recommendationFile.close()

   print "Rated Movies"
   print "------------------------"
   for movieId, rating in ratedMovieIds:
       print "%s, rating=%s" % (movieById[movieId][0], rating)
   print "------------------------"

   print "Recommended Movies"
   print "------------------------"
   for movieId, score in recommendations:
       print "%s, score=%s" % (movieById[movieId][0], score)
   print "------------------------"
   ```

    Prima **Ctrl-X**, **Y**e, finalmente, **Enter** onde guardar os dados.

4. Execute o script de Python. O comando seguinte assume que está no diretório onde todos os ficheiros foram transferidos:

    ```bash
    python show_recommendations.py 4 user-ratings.txt moviedb.txt recommendations.txt
    ```

    Este comando analisa as recomendações geradas para o utilizador 4 de ID.

    * O **utilizador ratings.txt** ficheiro é utilizado para obter filmes que estejam classificados.

    * O **moviedb.txt** ficheiro é utilizado para obter os nomes de filmes.

    * O **recommendations.txt** é utilizado para obter recomendações de filmes para este utilizador.

     O resultado deste comando é semelhante ao seguinte texto:

        Pontuar sete anos no Tibet (1997), = 5.0 Indiana Jones e o último Crusade (1989), Pontuar = 5.0 Jaws (1975), pontuação = 5.0 sentido e Sensibility (1995), pontuação = 5.0 independência dia (ID4) (1996), pontuação = 5.0 meu melhor amigo Wedding (1997), Pontuar = 5.0 Jerry Maguire (1996) pontuação = 5.0 Scream 2 (1997), pontuação = 5.0 tempo para Kill, (1996), pontuação = 5.0

## <a name="delete-temporary-data"></a>Eliminar dados temporários

Tarefas de mahout não remover os dados temporários que são criados durante o processamento da tarefa. O `--tempDir` parâmetro for especificado na tarefa de exemplo para isolar os ficheiros temporários num caminho específico para eliminação fácil. Para remover os ficheiros temporários, utilize o seguinte comando:

```bash
hdfs dfs -rm -f -r /temp/mahouttemp
```

> [!WARNING]
> Se pretender executar novamente o comando, também tem de eliminar o diretório de saída. Utilize o seguinte procedimento para eliminar este diretório:
>
> `hdfs dfs -rm -f -r /example/data/mahoutout`


## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu como utilizar o Mahout, detete outras formas de trabalhar com dados no HDInsight:

* [O Hive com o HDInsight](hdinsight-use-hive.md)
* [O PIg com o HDInsight](hdinsight-use-pig.md)
* [MapReduce com o HDInsight](hdinsight-use-mapreduce.md)

[build]: http://mahout.apache.org/developers/buildingmahout.html
[movielens]: http://grouplens.org/datasets/movielens/
[100k]: http://files.grouplens.org/datasets/movielens/ml-100k.zip
[getstarted]:apache-hadoop-linux-tutorial-get-started.md
[upload]: hdinsight-upload-data.md
[ml]: http://en.wikipedia.org/wiki/Machine_learning
[forest]: http://en.wikipedia.org/wiki/Random_forest
[enableremote]: ./media/hdinsight-mahout/enableremote.png
[connect]: ./media/hdinsight-mahout/connect.png
[hadoopcli]: ./media/hdinsight-mahout/hadoopcli.png
[tools]: https://github.com/Blackmist/hdinsight-tools
