---
title: Ferramentas do Azure HDInsight - configurar o ambiente interativa PySpark para Visual Studio Code | Microsoft Docs
description: Saiba como utilizar as ferramentas do Azure HDInsight para Visual Studio Code para criar, submeter consultas e scripts.
Keywords: VScode, as ferramentas do HDInsight do Azure, do Hive, Python, PySpark, Spark, HDInsight, Hadoop, LLAP, interativo do Hive, consulta interativa
services: HDInsight
documentationcenter: 
author: jejiang
manager: 
editor: 
tags: azure-portal
ms.assetid: 
ms.service: HDInsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/27/2017
ms.author: jejiang
ms.openlocfilehash: 24839aadaee07b98ac5a6e6cfd14e44de54e7e7e
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="set-up-pyspark-interactive-environment-for-visual-studio-code"></a>Configurar o ambiente interativa PySpark para Visual Studio Code

Os passos seguintes mostram como instalar pacotes de python, quando executada **HDInsight: PySpark interativa**.


## <a name="set-up-pyspark-interactive-environment-on-macos-and-linux"></a>Configurar o ambiente interativa do PySpark no MacOS e Linux
É necessário utilizar o comando **pip3** para os passos seguintes, se for **python 3**.
1. Certifique-se de que o **Python** e **pip** instalado.
 
    ![versão do Python pip](./media/set-up-pyspark-interactive-environment/check-python-pip-version.png)

2.  Instalar o Jupyter
    ```
    sudo pip install jupyter
    ```
    +  Talvez a seguinte mensagem de erro dá origem a no Linux e MacOS:

        ![error1](./media/set-up-pyspark-interactive-environment/error1.png)
        ```Resolve:
        sudo pip uninstall asyncio
        sudo pip install trollies
        ```

    + Instalar libkrb5 dev(For Linux only), talvez apresentar a seguinte mensagem de erro:

        ![error2](./media/set-up-pyspark-interactive-environment/error2.png)
        ```Resolve:
        sudo apt-get install libkrb5-dev 
        ```

3. Instalar sparkmagic
   ```
   sudo pip install sparkmagic
   ```

4. Certifique-se de que ipywidgets está corretamente instalado, executando a aplicação:
   ```
   sudo jupyter nbextension enable --py --sys-prefix widgetsnbextension
   ```
   ![Instalar os kernels wrapper](./media/set-up-pyspark-interactive-environment/ipywidget-enable.png)
 

5. Instale os kernels wrapper. Executar **pip Mostrar sparkmagic** e mostra o caminho que sparkmagic está instalado. 

    ![localização de sparkmagic](./media/set-up-pyspark-interactive-environment/sparkmagic-location.png)
   
6. Navegue para a localização e execute:

   ```Python2
   sudo jupyter-kernelspec install sparkmagic/kernels/pysparkkernel   
   ```
   ```Python3
   sudo jupyter-kernelspec install sparkmagic/kernels/pyspark3kernel
   ```

   ![instalação de kernelspec do jupyter](./media/set-up-pyspark-interactive-environment/jupyter-kernelspec-install.png)
7. Verifique o estado da instalação: 

    ```
    jupyter-kernelspec list
    ```
    ![lista de kernelspec do jupyter](./media/set-up-pyspark-interactive-environment/jupyter-kernelspec-list.png)

    Para kernels disponíveis: **python2** e **pysparkkernel** correspondem ao **python 2**, **python3** e ** pyspark3kernel** correspondem ao **python 3**. 

8. Reinicie VScode e de volta para o editor de scripts a executar **HDInsight: PySpark interativa**.

## <a name="next-steps"></a>Passos seguintes

### <a name="demo"></a>Demonstração
* HDInsight para VScode: [vídeo](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Ferramentas e extensões
* [Utilizar a ferramenta do Azure HDInsight para Visual Studio Code](hdinsight-for-vscode.md)
* [Utilize o Toolkit do Azure para o IntelliJ para criar e submeter aplicações do Spark Scala](spark/apache-spark-intellij-tool-plugin.md)
* [Utilize o Toolkit do Azure para o IntelliJ para depurar aplicações do Spark remotamente através de SSH](spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Utilize o Toolkit do Azure para o IntelliJ para depurar aplicações do Spark remotamente através da VPN](spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Utilizar as ferramentas do HDInsight na Azure Toolkit para Eclipse para criar Spark aplicações](spark/apache-spark-eclipse-tool-plugin.md)
* [Utilize as ferramentas do HDInsight para o IntelliJ com Hortonworks Sandbox](hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Utilizar blocos de notas do Zeppelin com um cluster do Spark no HDInsight](spark/apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o bloco de notas do Jupyter no cluster do Spark para o HDInsight](spark/apache-spark-jupyter-notebook-kernels.md)
* [Utilizar pacotes externos com blocos de notas do Jupyter](spark/apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter no computador e ligar a um cluster do Spark do HDInsight](spark/apache-spark-jupyter-notebook-install-locally.md)
* [Visualizar dados do Hive com o Microsoft Power BI no Azure HDInsight](hadoop/apache-hadoop-connect-hive-power-bi.md).
* [Utilize Zeppelin para executar consultas do Hive no Azure HDInsight](hdinsight-connect-hive-zeppelin.md)
