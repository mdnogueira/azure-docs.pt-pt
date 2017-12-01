---
title: Ferramentas do Azure HDInsight - configurar o ambiente interativa PySpark para Visual Studio Code | Microsoft Docs
description: Saiba como utilizar as ferramentas do Azure HDInsight para Visual Studio Code para criar e submeter consultas e scripts.
Keywords: VScode,Azure HDInsight Tools,Hive,Python,PySpark,Spark,HDInsight,Hadoop,LLAP,Interactive Hive,Interactive Query
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
ms.openlocfilehash: 5a64023df813262c461b9d772b722ebd613369ed
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2017
---
# <a name="set-up-the-pyspark-interactive-environment-for-visual-studio-code"></a>Configurar o ambiente de interativo PySpark para Visual Studio Code

Os passos seguintes mostram como instalar pacotes de Python executando **HDInsight: PySpark interativa**.


## <a name="set-up-the-pyspark-interactive-environment-on-macos-and-linux"></a>Configurar o ambiente interativo PySpark no macOS e Linux
Se estiver a utilizar **python 3**, tem de utilizar o comando **pip3** para os seguintes passos:

1. Certifique-se **Python** e **pip** estão instalados.
 
    ![versão do Python pip](./media/set-up-pyspark-interactive-environment/check-python-pip-version.png)

2.  Instale o Jupyter.
    ```
    sudo pip install jupyter
    ```
   Poderá ver a seguinte mensagem de erro no Linux e macOS:

   ![Erro 1](./media/set-up-pyspark-interactive-environment/error1.png)

   ```Resolve:
    sudo pip uninstall asyncio
    sudo pip install trollies
    ```

3. Instalar **libkrb5 dev** (para Linux). Poderá ver a seguinte mensagem de erro:

   ![Erro de 2](./media/set-up-pyspark-interactive-environment/error2.png)
       
   ```Resolve:
   sudo apt-get install libkrb5-dev 
   ```

3. Instalar **sparkmagic**.
   ```
   sudo pip install sparkmagic
   ```

4. Certifique-se de que **ipywidgets** se encontra corretamente instalado, executando o seguinte:
   ```
   sudo jupyter nbextension enable --py --sys-prefix widgetsnbextension
   ```
   ![Instalar os kernels wrapper](./media/set-up-pyspark-interactive-environment/ipywidget-enable.png)
 

5. Instale os kernels wrapper. Executar **pip Mostrar sparkmagic**. O resultado mostra o caminho para o **sparkmagic** instalação. 

    ![localização de sparkmagic](./media/set-up-pyspark-interactive-environment/sparkmagic-location.png)
   
6. Vá para a localização e, em seguida, execute:

   ```Python2
   sudo jupyter-kernelspec install sparkmagic/kernels/pysparkkernel   
   ```
   ```Python3
   sudo jupyter-kernelspec install sparkmagic/kernels/pyspark3kernel
   ```

   ![instalação de kernelspec do jupyter](./media/set-up-pyspark-interactive-environment/jupyter-kernelspec-install.png)
7. Verifique o estado de instalação.

    ```
    jupyter-kernelspec list
    ```
    ![lista de kernelspec do jupyter](./media/set-up-pyspark-interactive-environment/jupyter-kernelspec-list.png)

    Para kernels disponíveis: 
    - **python2** e **pysparkkernel** correspondem ao **python 2**. 
    - **python3** e **pyspark3kernel** correspondem ao **python 3**. 

8. Reinicie o VS Code e, em seguida, volte atrás para o editor de scripts está em execução **HDInsight: PySpark interativa**.

## <a name="next-steps"></a>Passos seguintes

### <a name="demo"></a>Demonstração
* HDInsight para o código de VS: [vídeo](https://go.microsoft.com/fwlink/?linkid=858706)

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
* [Visualizar dados do Hive com o Microsoft Power BI no Azure HDInsight](hadoop/apache-hadoop-connect-hive-power-bi.md)
* [Utilize Zeppelin para executar consultas do Hive no Azure HDInsight](hdinsight-connect-hive-zeppelin.md)
