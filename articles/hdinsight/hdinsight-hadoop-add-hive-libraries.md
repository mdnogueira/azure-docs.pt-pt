---
title: "Adicionar as bibliotecas Hive durante a criação do cluster do HDInsight - Azure | Microsoft Docs"
description: "Saiba como adicionar as bibliotecas Hive (ficheiros. jar), para um cluster do HDInsight durante a criação do cluster."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 2fd74b8d-c006-45c6-a9e2-72ff5d2d978a
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/04/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 90a1ea99cbba82b49a0ff6712bcaaa5dc814810e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="add-custom-hive-libraries-when-creating-your-hdinsight-cluster"></a>Adicione as bibliotecas Hive personalizadas ao criar o cluster do HDInsight

Saiba como pré-carregar as bibliotecas Hive no HDInsight. Este documento contém informações sobre como utilizar uma ação de Script de pré-carregamento de bibliotecas durante a criação do cluster. Bibliotecas adicionadas através dos passos neste documento são globalmente disponíveis no ramo de registo - não é necessário utilizar [adicionar JAR](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Cli) ao carregá-los.

## <a name="how-it-works"></a>Como funciona

Quando criar um cluster, pode utilizar uma ação de Script para modificar nós de cluster como são criados. O script neste documento aceita um único parâmetro que é a localização das bibliotecas. Esta localização tem de ser uma conta de armazenamento do Azure e as bibliotecas têm de ser armazenadas como ficheiros jar.

Durante a criação do cluster, o script enumera os ficheiros, copia-os para o `/usr/lib/customhivelibs/` diretório em nós head e de trabalho, em seguida, adiciona-os para o `hive.aux.jars.path` propriedade no `core-site.xml` ficheiro. Em clusters baseados em Linux, também atualiza o `hive-env.sh` ficheiro com a localização dos ficheiros.

> [!NOTE]
> Utilizando as ações de script neste artigo torna as bibliotecas disponíveis nos seguintes cenários:
>
> * **HDInsight baseado em Linux** - quando utilizar um cliente do Hive, **WebHCat**, e **HiveServer2**.
> * **HDInsight baseado em Windows** - quando utilizar o cliente do Hive e **WebHCat**.

## <a name="the-script"></a>O script

**Localização do script**

Para **clusters baseados em Linux**: [https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh)

Para **clusters baseados em Windows**: [https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1](https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1)

> [!IMPORTANT]
> O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).

**Requisitos**

* Os scripts devem ser aplicados para ambos os **nós principais** e **nós de trabalho**.

* V7 que pretende instalar deve ser armazenado no armazenamento de Blobs do Azure num **único contentor**.

* A conta de armazenamento que contém a biblioteca de ficheiros jar **tem** ser ligado ao cluster do HDInsight durante a criação. -Tem de estar a conta do storage predefinida ou uma conta adicionados através do __configuração opcional__.

* O caminho WASB para o contentor tem de ser especificado como parâmetro para a ação de Script. Por exemplo, se o v7 é armazenadas num contentor com o nome **libs** numa conta de armazenamento com o nome **mystorage**, o parâmetro seria  **wasb://libs@mystorage.blob.core.windows.net/** .

  > [!NOTE]
  > Este documento assume que já criou uma conta de armazenamento, o contentor de blob e carregado os ficheiros ao mesmo.
  >
  > Se não tiver criado uma conta de armazenamento, pode fazê-lo, por isso, através de [portal do Azure](https://portal.azure.com). Em seguida, pode utilizar um utilitário como [Explorador de armazenamento do Azure](http://storageexplorer.com/) para criar um contentor na conta e carregar ficheiros para a mesma.

## <a name="create-a-cluster-using-the-script"></a>Criar um cluster utilizando o script

> [!NOTE]
> Os seguintes passos criar um cluster do HDInsight baseado em Linux. Para criar um cluster baseado no Windows, selecione **Windows** do SO ao criar o cluster e a utilização do script do Windows (PowerShell) em vez do script de deteção de cluster.
>
> Pode também utilizar o Azure PowerShell ou o SDK .NET do HDInsight para criar um cluster com este script. Para obter mais informações sobre como utilizar estes métodos, consulte [HDInsight personalizar clusters com ações de Script](hdinsight-hadoop-customize-cluster-linux.md).

1. Começar a aprovisionar um cluster, utilizando os passos no [aprovisionar clusters do HDInsight no Linux](hdinsight-hadoop-provision-linux-clusters.md), mas não concluir o aprovisionamento.

2. No **configuração opcional** secção, selecione **ações de Script**e forneça as seguintes informações:

   * **NOME**: introduza um nome amigável para a ação de script.

   * **URI de SCRIPT**: https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh

   * **HEAD**: selecione esta opção.

   * **TRABALHO**: selecione esta opção.

   * **ZOOKEEPER**: deixar isto em branco.

   * **Os parâmetros**: introduza o endereço WASB à conta de armazenamento e de contentor que contém o v7. Por exemplo,  **wasb://libs@mystorage.blob.core.windows.net/** .

3. Na parte inferior do **ações de Script**, utilize o **selecione** botão para guardar a configuração.

4. No **configuração opcional** secção, selecione **contas de armazenamento ligadas** e selecione o **adicionar uma chave de armazenamento** ligação. Selecione a conta de armazenamento que contém o v7. Em seguida, utilize o **selecione** botões para guardar as definições e voltar a **configuração opcional**.

5. Para guardar a configuração opcional, utilize o **selecione** na parte inferior do **configuração opcional** secção.

6. Continuar o aprovisionamento de cluster, conforme descrito em [aprovisionar clusters do HDInsight no Linux](hdinsight-hadoop-provision-linux-clusters.md).

Depois de concluída a criação do cluster, é capazes de utilizar o v7 adicionados através deste script de ramo de registo, sem ter de utilizar o `ADD JAR` instrução.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre como trabalhar com o Hive, consulte [utilizar o Hive com o HDInsight](hdinsight-use-hive.md)
