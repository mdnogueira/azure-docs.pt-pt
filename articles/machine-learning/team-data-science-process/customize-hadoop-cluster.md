---
title: "Personalizar clusters do Hadoop para o processo de ciência de dados de equipa | Microsoft Docs"
description: "Módulos do Python populares disponibilizados nos clusters do Hadoop do Azure HDInsight personalizadas."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 0c115dca-2565-4e7a-9536-6002af5c786a
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: hangzh;bradsev
ms.openlocfilehash: 522e33b399f2648427464b439bc4405e9e8097cd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="customize-azure-hdinsight-hadoop-clusters-for-the-team-data-science-process"></a>Personalizar clusters do Hadoop no HDInsight do Azure para o Team Data Science Process
Este artigo descreve como personalizar um cluster do HDInsight Hadoop através da instalação de 64 bits Anaconda (Python 2.7) em cada nó quando o cluster seja aprovisionado como um serviço de HDInsight. Também mostra como aceder headnode para submeter tarefas personalizadas para o cluster. Esta personalização torna muitos populares módulos do Python, que estão incluídos no Anaconda, comodamente disponível para utilização num funções definidas pelo utilizador (UDFs) que foram concebidos para processar registos de ramo de registo do cluster. Para obter instruções sobre os procedimentos utilizados neste cenário, consulte [como submeter consultas do Hive](move-hive-tables.md#submit).

As seguintes ligações de menu para tópicos que descrevem como configurar os vários ambientes de ciência de dados utilizados pelo [processo de ciência de dados de equipa (TDSP)](overview.md).

[!INCLUDE [data-science-environment-setup](../../../includes/cap-setup-environments.md)]

## <a name="customize"></a>Personalizar o Cluster de Hadoop do HDInsight do Azure
Para criar um cluster de Hadoop do HDInsight personalizado, comece por registo no [ **portal clássico do Azure**](https://manage.windowsazure.com/), clique em **novo** no canto inferior esquerdo e, em seguida, selecione serviços de dados - > -> HDINSIGHT **criação personalizada** trazer para primeiro a **os detalhes do Cluster** janela. 

![Criar a área de trabalho](./media/customize-hadoop-cluster/customize-cluster-img1.png)

Introduza o nome do cluster ser criado na página de configuração 1 e aceite os valores predefinidos para os outros campos. Clique na seta para ir para a página de configuração seguinte. 

![Criar a área de trabalho](./media/customize-hadoop-cluster/customize-cluster-img1.png)

Na página de configuração 2, introduza o número de **dados nós**, selecione o **região/VIRTUAL NETWORK**e selecione os tamanhos do **nó principal** e **dados NÓ**. Clique na seta para ir para a página de configuração seguinte.

> [!NOTE]
> O **região/VIRTUAL NETWORK** tem de ser o mesmo que a região da conta do storage que vai ser utilizado para o cluster de Hadoop do HDInsight. Caso contrário, na quarta página de configuração, a conta de armazenamento não irão aparecer na lista pendente de **nome da conta**.
> 
> 

![Criar a área de trabalho](./media/customize-hadoop-cluster/customize-cluster-img3.png)

Na página de configuração 3, forneça um nome de utilizador e palavra-passe para o cluster do HDInsight Hadoop. **Não** selecionar o *introduza o Metastore do Hive/Oozie*. Em seguida, clique na seta para ir para a página de configuração seguinte. 

![Criar a área de trabalho](./media/customize-hadoop-cluster/customize-cluster-img4.png)

Na página de configuração 4, especifique o nome de conta de armazenamento, o contentor predefinido do cluster do HDInsight Hadoop. Se selecionar *criar contentor de predefinido* no **contentor predefinido** pendente lista, um contentor com o mesmo nome como o cluster será criado. Clique na seta para ir para a última página de configuração.

![Criar a área de trabalho](./media/customize-hadoop-cluster/customize-cluster-img5.png)

No final **ações de Script** página de configuração, clique em **adicionar ação de script** botão e preencha os campos de texto com os seguintes valores.

* **NOME** -qualquer cadeia como o nome desta ação de script
* **TIPO de nó** - selecione **todos os nós**
* **URI de SCRIPT** - *http://getgoing.blob.core.windows.net/publicscripts/Azure_HDI_Setup_Windows.ps1* 
  * *publicscripts* é um contentor público na conta de armazenamento 
  * *getgoing* utilizamos a partilha de ficheiros de script do PowerShell para facilitar o trabalho dos utilizadores no Azure
* **Os parâmetros** -(deixar em branco)

Por fim, clique na marca de verificação para iniciar a criação do cluster de Hadoop do HDInsight personalizada. 

![Criar a área de trabalho](./media/customize-hadoop-cluster/script-actions.png)

## <a name="headnode"></a>Aceder ao nó principal do Cluster de Hadoop
Tem de ativar o acesso remoto para o cluster de Hadoop no Azure antes de aceder ao nó principal do cluster de Hadoop através de RDP. 

1. Inicie sessão no [ **portal clássico do Azure**](https://manage.windowsazure.com/), selecione **HDInsight** à esquerda, selecione o cluster de Hadoop da lista de clusters, clique em de **configuração**  separador e, em seguida, clique em de **ATIVAR remoto** ícone na parte inferior da página.
   
    ![Criar a área de trabalho](./media/customize-hadoop-cluster/enable-remote-access-1.png)
2. No **configurar o ambiente de trabalho remoto** janela, introduza os campos de nome de utilizador e palavra-passe e selecione a data de expiração de acesso remoto. Em seguida, clique na marca de verificação para permitir o acesso remoto para o nó principal do cluster de Hadoop.
   
    ![Criar a área de trabalho](./media/customize-hadoop-cluster/enable-remote-access-2.png)

> [!NOTE]
> O nome de utilizador e palavra-passe para o acesso remoto não são o nome de utilizador e palavra-passe que utilizam quando criou o cluster do Hadoop. Este é um conjunto separado de credenciais. Além disso, a data de expiração do acesso remoto tem de estar dentro de 7 dias a contar da data atual.
> 
> 

Depois de acesso remoto estiver ativado, clique em **CONNECT** na parte inferior da página para remoto para o nó principal. Inicie sessão no nó principal do cluster de Hadoop ao introduzir as credenciais para o utilizador de acesso remoto que especificou anteriormente.

![Criar a área de trabalho](./media/customize-hadoop-cluster/enable-remote-access-3.png)

Os passos no processo de análise avançadas estão mapeados no [processo de ciência de dados de equipa (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) e podem incluir passos que movem dados para o HDInsight, em seguida, processam e de exemplo que não existe em preparação para aprender os dados com o Aprendizagem do Azure.

Consulte [como submeter consultas do Hive](move-hive-tables.md#submit) para obter instruções sobre como aceder os módulos do Python que estão incluídos no Anaconda do nó principal do cluster no definido pelo utilizador funções (UDFs que são utilizados para processar registos de ramo de registo armazenados no) cluster.

