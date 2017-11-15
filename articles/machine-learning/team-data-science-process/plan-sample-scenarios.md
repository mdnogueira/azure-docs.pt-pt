---
title: "Identificar os cenários de análise avançadas para o Azure Machine Learning | Microsoft Docs"
description: "Selecione os cenários adequados para fazê-lo avançadas de Análise Preditiva com o processo de ciência de dados de equipa."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 53aecc1e-5089-42cf-8d44-77678653f92d
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: bradsev
ms.openlocfilehash: 3b6a92f4f4615954902124c59adca25560182de6
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2017
---
# <a name="scenarios-for-advanced-analytics-in-azure-machine-learning"></a>Cenários de análises avançadas no Azure Machine Learning
Este artigo descreve a variedade de origens de dados de exemplo e cenários de destino que podem ser processados pelo [processo de ciência de dados de equipa (TDSP)](overview.md). O TDSP fornece uma abordagem systematic para as equipas de colaborar em compilar aplicações inteligentes. Os cenários aqui apresentados mostram as opções disponíveis no fluxo de trabalho de processamento de dados que dependem as características dos dados, as localizações de origem e repositórios de destino no Azure.

O **árvore de decisões** para selecionar os cenários de exemplo que é adequado para os seus dados e o objetivo é apresentado na última secção.

> [!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]
> 
> 

Cada uma das seguintes secções apresenta um cenário de exemplo. Para cada cenário, um ciência de dados ou análise avançada fluxo e o suporte de recursos do Azure são listados.

> [!NOTE]
> **Para todos os cenários seguintes, tem de:**
> <br/>
> 
> * [Criar uma conta de armazenamento](../../storage/common/storage-create-storage-account.md)
>   <br/>
> * [Criar uma área de trabalho do Azure Machine Learning](../studio/create-workspace.md)
> 
> 

## <a name="smalllocal"></a>Cenário \#1: pequeno para o conjunto de dados em tabela médio nos ficheiros de locais
![Pequenas e médias ficheiros locais][1]

#### <a name="additional-azure-resources-none"></a>Recursos do Azure adicionais: nenhuma
1. Iniciar sessão para o [do Azure Machine Learning Studio](https://studio.azureml.net/).
2. Carregar um conjunto de dados.
3. Crie um fluxo de experimentação do Azure Machine Learning a partir dos DataSets carregados.

## <a name="smalllocalprocess"></a>Cenário \#2: pequena a médio conjunto de dados de ficheiros locais que necessitam de processamento
![Pequenas e médias ficheiros locais com o processamento][2]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>Recursos do Azure adicionais: Máquina Virtual do Azure (servidor IPython bloco de notas)
1. Crie uma máquina de Virtual do Azure em execução IPython bloco de notas.
2. Carregar dados para um contentor de armazenamento do Azure.
3. Pré-processar e apagar dados em IPython bloco de notas, aceder aos dados a partir do contentor de armazenamento do Azure.
4. Transforme dados em limpos formato tabular.
5. Guarde os dados transformados em blobs do Azure.
6. Iniciar sessão para o [do Azure Machine Learning Studio](https://studio.azureml.net/).
7. Ler os dados de blobs do Azure utilizando o [importar dados] [ import-data] módulo.
8. Crie um fluxo de experimentação do Azure Machine Learning a partir dos DataSets transmissões em.

## <a name="largelocal"></a>Cenário \#3: grande conjunto de dados de ficheiros locais, a filtragem de Blobs do Azure
![Ficheiros de locais de grande][3]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>Recursos do Azure adicionais: Máquina Virtual do Azure (servidor IPython bloco de notas)
1. Crie uma máquina de Virtual do Azure em execução IPython bloco de notas.
2. Carregar dados para um contentor de armazenamento do Azure.
3. Pré-processar e apagar dados em IPython bloco de notas, aceder aos dados de blobs do Azure.
4. Transforme dados em limpos formato tabular, se necessário.
5. Explore os dados e criar funcionalidades conforme necessário.
6. Extrair um exemplo de dados de pequena a média.
7. Guarde as amostras de dados em blobs do Azure.
8. Iniciar sessão para o [do Azure Machine Learning Studio](https://studio.azureml.net/).
9. Ler os dados de blobs do Azure utilizando o [importar dados] [ import-data] módulo.
10. Crie o fluxo de experimentação do Azure Machine Learning a partir dos DataSets transmissões em.

## <a name="smalllocaltodb"></a>Cenário \#4: pequenas a médio conjunto de dados de ficheiros locais, direcionada para uma Máquina Virtual no Azure do SQL Server
![Pequenas e médias ficheiros locais BD SQL no Azure][4]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Recursos do Azure adicionais: Máquina Virtual do Azure (SQL Server / servidor IPython bloco de notas)
1. Crie uma máquina de Virtual do Azure a executar o SQL Server + IPython bloco de notas.
2. Carregar dados para um contentor de armazenamento do Azure.
3. Pré-processar e limpar dados no contentor de armazenamento do Azure utilizando o bloco de notas do IPython.
4. Transforme dados em limpos formato tabular, se necessário.
5. Guardar os dados para os ficheiros da VM local (bloco de notas do IPython está em execução numa VM, consulte unidades locais em unidades de VM).
6. Carregar dados para a base de dados do SQL Server em execução numa VM do Azure.
   
   Opção \#1: utilizar SQL Server Management Studio.
   
   * Início de sessão a VM do SQL Server
   * Execute o SQL Server Management Studio.
   * Crie tabelas de base de dados e de destino.
   * Utilize uma em volume importar métodos para carregar os dados a partir dos ficheiros de locais de VM.
   
   Opção \#2: utilizar IPython bloco de notas – não recomendado para conjuntos de dados de médios e grandes
   
   <!-- -->    
   * Utilize a cadeia de ligação de ODBC para aceder ao SQL Server numa VM.
   * Crie tabelas de base de dados e de destino.
   * Utilize uma em volume importar métodos para carregar os dados a partir dos ficheiros de locais de VM.
7. Explore os dados, criar funcionalidades conforme necessário. Tenha em atenção que as funcionalidades não precisam de ser materializada nas tabelas de base de dados. Apenas tenha em atenção a consulta necessária para criá-los.
8. Opte por utilizar um tamanho de amostra de dados, se necessário e/ou assim o desejar.
9. Iniciar sessão para o [do Azure Machine Learning Studio](https://studio.azureml.net/).
10. Ler os dados diretamente a partir do SQL Server utilizando o [importar dados] [ import-data] módulo. Cole a consulta necessária que extrai os campos, cria as funcionalidades e dados de exemplo, se for necessário diretamente no [importar dados] [ import-data] consulta.
11. Crie o fluxo de experimentação do Azure Machine Learning a partir dos DataSets transmissões em.

## <a name="largelocaltodb"></a>Cenário \#5: conjunto de dados grande nos ficheiros de locais, SQL Server numa VM do Azure de destino
![Ficheiros de locais grandes para a base de dados do SQL no Azure][5]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Recursos do Azure adicionais: Máquina Virtual do Azure (SQL Server / servidor IPython bloco de notas)
1. Crie uma máquina de Virtual do Azure a executar o SQL Server e servidor IPython bloco de notas.
2. Carregar dados para um contentor de armazenamento do Azure.
3. (Opcional) Pré-processar e limpar dados.
   
   a.  Pré-processar e apagar dados em IPython bloco de notas, aceder aos dados a partir do Azure
   
       blobs.
   
   b.  Transforme dados em limpos formato tabular, se necessário.
   
   c.  Guardar os dados para os ficheiros da VM local (bloco de notas do IPython está em execução numa VM, consulte unidades locais em unidades de VM).
4. Carregar dados para a base de dados do SQL Server em execução numa VM do Azure.
   
   a.  Início de sessão a VM do SQL Server.
   
   b.  Se não guardar os dados já, transferir os ficheiros de dados do Azure
   
       storage container to local-VM folder.
   
   c.  Execute o SQL Server Management Studio.
   
   d.  Crie tabelas de base de dados e de destino.
   
   e.  Utilize uma em volume importar métodos para carregar os dados.
   
   f.  Se forem necessárias associações de tabelas, crie índices a fim de acelerar associações.
   
   > [!NOTE]
   > Para carregar mais rápida de tamanhos de dados de grandes dimensões, recomenda-se que criar tabelas particionadas e em massa importar os dados em paralelo. Para obter mais informações, consulte [Parallel importar dados a tabelas Particionadas do SQL Server](parallel-load-sql-partitioned-tables.md).
   > 
   > 
5. Explore os dados, criar funcionalidades conforme necessário. Tenha em atenção que as funcionalidades não precisam de ser materializada nas tabelas de base de dados. Apenas tenha em atenção a consulta necessária para criá-los.
6. Opte por utilizar um tamanho de amostra de dados, se necessário e/ou assim o desejar.
7. Iniciar sessão para o [do Azure Machine Learning Studio](https://studio.azureml.net/).
8. Ler os dados diretamente a partir do SQL Server utilizando o [importar dados] [ import-data] módulo. Cole a consulta necessária que extrai os campos, cria as funcionalidades e dados de exemplo, se for necessário diretamente no [importar dados] [ import-data] consulta.
9. Fluxo de experimentação do Azure Machine Learning Simple começando pelo conjunto de dados carregado

## <a name="largedbtodb"></a>Cenário \#6: grande conjunto de dados um SQL Server da base de dados no local, direcionada para uma Máquina Virtual no Azure do SQL Server
![Grande SQL DB no local à base de dados do SQL Server no Azure][6]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Recursos do Azure adicionais: Máquina Virtual do Azure (SQL Server / servidor IPython bloco de notas)
1. Crie uma máquina de Virtual do Azure a executar o SQL Server e servidor IPython bloco de notas.
2. Utilize um dos dados exportar métodos para exportar os dados do SQL Server para ficheiros de informação.
   
   > [!NOTE]
   > Se optar por mover todos os dados da base de dados no local, um método alternativo (rápido) para mover a base de dados completo para a instância do SQL Server no Azure. Ignore os passos para exportar dados, criar a base de dados e carga/importar dados para a base de dados de destino e siga o método alternativo.
   > 
   > 
3. Carregar ficheiros de informação para o contentor de armazenamento do Azure.
4. Carregar os dados para uma base de dados do SQL Server em execução de uma Máquina Virtual no Azure.
   
   a.  Início de sessão para a VM do SQL Server.
   
   b.  Transferir ficheiros de dados a partir de um contentor de armazenamento do Azure para a pasta local-VM.
   
   c.  Execute o SQL Server Management Studio.
   
   d.  Crie tabelas de base de dados e de destino.
   
   e.  Utilize uma em volume importar métodos para carregar os dados.
   
   f.  Se forem necessárias associações de tabelas, crie índices a fim de acelerar associações.
   
   > [!NOTE]
   > Para carregar mais rápida de tamanhos de dados de grandes dimensões, criar tabelas particionadas em massa importe os dados em paralelo. Para obter mais informações, consulte [Parallel importar dados a tabelas Particionadas do SQL Server](parallel-load-sql-partitioned-tables.md).
   > 
   > 
5. Explore os dados, criar funcionalidades conforme necessário. Tenha em atenção que as funcionalidades não precisam de ser materializada nas tabelas de base de dados. Apenas tenha em atenção a consulta necessária para criá-los.
6. Opte por utilizar um tamanho de amostra de dados, se necessário e/ou assim o desejar.
7. Iniciar sessão para o [do Azure Machine Learning Studio](https://studio.azureml.net/).
8. Ler os dados diretamente a partir do SQL Server utilizando o [importar dados] [ import-data] módulo. Cole a consulta necessária que extrai os campos, cria as funcionalidades e dados de exemplo, se for necessário diretamente no [importar dados] [ import-data] consulta.
9. Fluxo de experimentação do Simple do Azure Machine Learning começando pelo conjunto de dados carregado.

### <a name="alternate-method-to-copy-a-full-database-from-an-on-premises--sql-server-to-azure-sql-database"></a>Método alternativo para copiar uma base de dados completo de um servidor de SQL no local para a SQL Database do Azure
![Desligar a base de dados local e ligue à BD do SQL Server no Azure][7]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Recursos do Azure adicionais: Máquina Virtual do Azure (SQL Server / servidor IPython bloco de notas)
Para replicar a base de dados do SQL Server toda a VM do SQL Server, deve copiar uma base de dados de uma localização/servidor para outro, partindo do princípio de que a base de dados pode ficar temporariamente offline. Fazê-lo no SQL Server Management Studio Object Explorer, ou utilizando os comandos equivalentes do Transact-SQL.

1. Desligar a base de dados na localização de origem. Para obter mais informações, consulte [desligar uma base de dados](https://technet.microsoft.com/library/ms191491\(v=sql.110\).aspx).
2. Na janela do Explorador do Windows ou a linha de comandos do Windows, copie o ficheiro de base de dados desanexada ou ficheiros e ficheiro de registo ou os ficheiros para a localização de destino na VM do servidor SQL no Azure.
3. Ligar os ficheiros copiados para a instância do SQL Server de destino. Para obter mais informações, consulte [anexar uma base de dados](https://technet.microsoft.com/library/ms190209\(v=sql.110\).aspx).

[Mover uma base de dados utilizando desanexar e anexar (Transact-SQL)](https://technet.microsoft.com/library/ms187858\(v=sql.110\).aspx)

## <a name="largedbtohive"></a>Cenário \#7: base de dados de ramo de registo no Azure HDInsight Hadoop clusters de destino de macrodados nos ficheiros de locais
![Macrodados no destino local do Hive][9]

#### <a name="additional-azure-resources-azure-hdinsight-hadoop-cluster-and-azure-virtual-machine-ipython-notebook-server"></a>Recursos do Azure adicionais: Cluster de Hadoop do Azure HDInsight e a Máquina Virtual do Azure (servidor IPython bloco de notas)
1. Crie uma máquina de Virtual do Azure com o bloco de notas IPython server.
2. Crie um cluster de Hadoop do Azure HDInsight.
3. (Opcional) Pré-processar e limpar dados.
   
   a.  Pré-processar e apagar dados em IPython bloco de notas, aceder aos dados a partir do Azure
   
       blobs.
   
   b.  Transforme dados em limpos formato tabular, se necessário.
   
   c.  Guardar os dados para os ficheiros da VM local (bloco de notas do IPython está em execução numa VM, consulte unidades locais em unidades de VM).
4. Carregar dados para o contentor predefinido do cluster de Hadoop selecionado no passo 2.
5. Carregar dados para a base de dados do Hive num cluster de Hadoop do Azure HDInsight.
   
   a.  Inicie sessão no nó principal do cluster de Hadoop
   
   b.  Abra a linha de comandos do Hadoop.
   
   c.  Introduza o diretório de raiz do ramo de registo pelo comando `cd %hive_home%\bin` na linha de comandos do Hadoop.
   
   d.  Executar consultas do Hive para criar a base de dados e tabelas e carregar dados do armazenamento de BLOBs para as tabelas do Hive.
   
   > [!NOTE]
   > Se os dados são grandes, os utilizadores podem criar a tabela do Hive com partições. Em seguida, os utilizadores podem utilizar um `for` ciclo no Hadoop linha de comandos no nó principal para carregar dados para a tabela de Hive particionada por partição.
   > 
   > 
6. Explore os dados e criar funcionalidades conforme necessário na linha de comandos do Hadoop. Tenha em atenção que as funcionalidades não precisam de ser materializada nas tabelas de base de dados. Apenas tenha em atenção a consulta necessária para criá-los.
   
   a.  Inicie sessão no nó principal do cluster de Hadoop
   
   b.  Abra a linha de comandos do Hadoop.
   
   c.  Introduza o diretório de raiz do ramo de registo pelo comando `cd %hive_home%\bin` na linha de comandos do Hadoop.
   
   d.  Execute consultas do Hive na linha de comandos do Hadoop no nó principal do cluster de Hadoop para explorar os dados e criar funcionalidades conforme necessário.
7. Se for necessário e/ou assim o desejar, os dados para caber no Azure Machine Learning Studio de exemplo.
8. Iniciar sessão para o [do Azure Machine Learning Studio](https://studio.azureml.net/).
9. Ler os dados diretamente a partir de `Hive Queries` utilizando o [importar dados] [ import-data] módulo. Cole a consulta necessária que extrai os campos, cria as funcionalidades e dados de exemplo, se for necessário diretamente no [importar dados] [ import-data] consulta.
10. Fluxo de experimentação do Simple do Azure Machine Learning começando pelo conjunto de dados carregado.

## <a name="decisiontree"></a>Árvore de decisões de seleção do cenário
- - -
O diagrama a seguir resume os cenários descritos acima e o processo de análise avançada e as escolhas de tecnologia efetuadas que leva-o para cada um dos cenários itemized. Tenha em atenção que o processamento de dados, exploração, engenharia da funcionalidade e a amostragem podem demorar coloque num ou mais método/ambiente – na origem, intermédia, e/ou ambientes de destino – e pode continuar iteratively conforme necessário. O diagrama só funciona como uma ilustração de alguns dos possíveis fluxos e não fornece uma enumeração exaustiva.

![Cenários de instruções de processo de DS de exemplo][8]

### <a name="advanced-analytics-in-action-examples"></a>Análise avançada na ação exemplos
Para instruções de Azure Machine Learning ponto-a-ponto que utilizam o processo de análise avançada e tecnologia de conjuntos de dados públicos a utilizar, consulte:

* [O processo de ciência de dados de equipa em ação: utilizar o SQL Server](sql-walkthrough.md).
* [O processo de ciência de dados de equipa em ação: com clusters do HDInsight Hadoop](hive-walkthrough.md).

[1]: ./media/plan-sample-scenarios/dsp-plan-small-in-aml.png
[2]: ./media/plan-sample-scenarios/dsp-plan-local-with-processing.png
[3]: ./media/plan-sample-scenarios/dsp-plan-local-large.png
[4]: ./media/plan-sample-scenarios/dsp-plan-local-to-db.png
[5]: ./media/plan-sample-scenarios/dsp-plan-large-to-db.png
[6]: ./media/plan-sample-scenarios/dsp-plan-db-to-db.png
[7]: ./media/plan-sample-scenarios/dsp-plan-attach-db.png
[8]: ./media/plan-sample-scenarios/dsp-plan-sample-scenarios.png
[9]: ./media/plan-sample-scenarios/dsp-plan-local-to-hive.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
