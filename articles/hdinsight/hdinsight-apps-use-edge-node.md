---
title: "Utilizar nós edge vazio nos clusters do Hadoop no HDInsight - Azure | Microsoft Docs"
description: "Como adicionar um nó de extremidade vazio para um cluster do HDInsight que pode ser utilizado como um cliente e, em seguida, teste/o anfitrião de aplicações do HDInsight."
services: hdinsight
editor: cgronlun
manager: jhubbard
author: mumian
tags: azure-portal
documentationcenter: 
ms.assetid: cdc7d1b4-15d7-4d4d-a13f-c7d3a694b4fb
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2017
ms.author: jgao
ms.openlocfilehash: e21dabcc6999b1f1047d334e782f723d0c03c2cb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-empty-edge-nodes-on-hadoop-clusters-in-hdinsight"></a>Utilizar nós edge vazio nos clusters do Hadoop no HDInsight

Saiba como adicionar um nó de extremidade vazio para um cluster do HDInsight. Um nó de extremidade vazia é uma máquina virtual do Linux com as mesmo as ferramentas de cliente instalado e configurado como o headnodes, mas com serviços não Hadoop em execução. Pode utilizar o nó de extremidade para aceder ao cluster, teste as aplicações de cliente e que aloja as aplicações de cliente. 

Pode adicionar um nó de extremidade vazio para um cluster do HDInsight existente para um novo cluster ao criar o cluster. Adicionar um nó de extremidade vazio é feito utilizando o modelo Azure Resource Manager.  O exemplo seguinte demonstra como é efetuada através de um modelo:

    "resources": [
        {
            "name": "[concat(parameters('clusterName'),'/', variables('applicationName'))]",
            "type": "Microsoft.HDInsight/clusters/applications",
            "apiVersion": "2015-03-01-preview",
            "dependsOn": [ "[concat('Microsoft.HDInsight/clusters/',parameters('clusterName'))]" ],
            "properties": {
                "marketPlaceIdentifier": "EmptyNode",
                "computeProfile": {
                    "roles": [{
                        "name": "edgenode",
                        "targetInstanceCount": 1,
                        "hardwareProfile": {
                            "vmSize": "Standard_D3"
                        }
                    }]
                },
                "installScriptActions": [{
                    "name": "[concat('emptynode','-' ,uniquestring(variables('applicationName')))]",
                    "uri": "[parameters('installScriptAction')]",
                    "roles": ["edgenode"]
                }],
                "uninstallScriptActions": [],
                "httpsEndpoints": [],
                "applicationType": "CustomApplication"
            }
        }
    ],

Como é mostrado no exemplo, opcionalmente, pode chamar um [ação de script](hdinsight-hadoop-customize-cluster-linux.md) para efetuar uma configuração adicional, como a instalação [Apache Hue](hdinsight-hadoop-hue-linux.md) no nó de extremidade. O script de ação de script tem de ser publicamente acessível na web.  Por exemplo, se o script é armazenado no armazenamento do Azure, utilize contentores públicos ou blobs públicos.

O tamanho de máquina virtual do nó de extremidade têm de cumprir os requisitos de tamanho de vm de nó do HDInsight cluster trabalho. Para os tamanhos de vm de nó de trabalho recomendada, consulte [clusters do Hadoop criar no HDInsight](hdinsight-hadoop-provision-linux-clusters.md#cluster-types).

Depois de ter criado um nó de extremidade, pode ligar ao nó de extremidade utilizando SSH e executar as ferramentas de cliente acedam ao cluster de Hadoop no HDInsight.

> [!WARNING] 
> Utilização de um nó de extremidade vazio com o HDInsight está atualmente em pré-visualização. Componentes personalizados que estão instalados no nó de extremidade recebem comercialmente razoável suporte da Microsoft. Isto pode resultar na resolução de problemas que encontrar. Ou pode ser referido recursos de Comunidade para obter assistência. Seguem-se alguns dos mais sites de Active Directory para obter ajuda da Comunidade:
>
> * [Fórum do MSDN para o HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight)
> * [http://StackOverflow.com](http://stackoverflow.com).
>
> Se estiver a utilizar uma tecnologia do Apache, poderá encontrar assistência através do Apache sites projeto [http://apache.org](http://apache.org), tais como o [Hadoop](http://hadoop.apache.org/) site.

## <a name="add-an-edge-node-to-an-existing-cluster"></a>Adicionar um nó de extremidade para um cluster existente
Nesta secção, utilize um modelo do Resource Manager para adicionar um nó de extremidade para um cluster do HDInsight existente.  O modelo do Resource Manager pode ser encontrado na [GitHub](https://azure.microsoft.com/en-us/resources/templates/101-hdinsight-linux-add-edge-node/). O modelo do Resource Manager chama uma ação de script, localizada em https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-add-edge-node/scripts/EmptyNodeSetup.sh. O script não realizar qualquer ação.  É para demonstrar a ação de script chamada a partir de um modelo do Resource Manager.

**Para adicionar um nó de extremidade vazio para um cluster existente**

1. Se ainda não tiver uma, crie um cluster do HDInsight.  Consulte [tutorial do Hadoop: começar a utilizar o Hadoop no HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md).
2. Clique na imagem seguinte para iniciar sessão no Azure e abrir o modelo Azure Resource Manager no portal do Azure. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-add-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/deploy-to-azure.png" alt="Deploy to Azure"></a>
3. Configure as seguintes propriedades:
   
   * **Subscrição**: selecione uma subscrição do Azure utilizada para criar o cluster.
   * **Grupo de recursos**: selecione o grupo de recursos utilizado para o cluster do HDInsight existente.
   * **Localização**: selecionar a localização do cluster do HDInsight existente.
   * **Nome do cluster**: introduza o nome do cluster do HDInsight existente.
   * **Tamanho de nó de contorno**: selecione uma dos tamanhos VM. O tamanho da vm tem de cumprir os requisitos de tamanho de vm de nó de trabalho. Para os tamanhos de vm de nó de trabalho recomendada, consulte [clusters do Hadoop criar no HDInsight](hdinsight-hadoop-provision-linux-clusters.md#cluster-types).
   * **Contorno nó prefixo**: O valor predefinido é **novo**.  Utilizar o valor predefinido, o nome de nó de extremidade é **novo edgenode**.  Pode personalizar o prefixo do portal. Também pode personalizar o nome completo do modelo.

4. Verifique **concordo com os termos e condições indicadas acima**e, em seguida, clique em **Compra** para criar o nó de extremidade.

>[!IMPORTANT]
> Certifique-se de que seleciona o grupo de recursos do Azure para o cluster do HDInsight existente.  Caso contrário, receberá a mensagem de erro "não é possível executar a operação pedida no recurso aninhado. Principais recursos '&lt;ClusterName >' não foi encontrada. "

## <a name="add-an-edge-node-when-creating-a-cluster"></a>Adicionar um nó de extremidade ao criar um cluster
Nesta secção, utilize um modelo do Resource Manager para criar o cluster do HDInsight com um nó de extremidade.  O modelo do Resource Manager pode ser encontrado no [Galeria de modelos de início rápido do Azure](https://azure.microsoft.com/documentation/templates/101-hdinsight-linux-with-edge-node/). O modelo do Resource Manager chama uma ação de script, localizada em https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-with-edge-node/scripts/EmptyNodeSetup.sh. O script não realizar qualquer ação.  É para demonstrar a ação de script chamada a partir de um modelo do Resource Manager.

**Para adicionar um nó de extremidade vazio para um cluster existente**

1. Se ainda não tiver uma, crie um cluster do HDInsight.  Consulte [começar a utilizar o Hadoop no HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md).
2. Clique na imagem seguinte para iniciar sessão no Azure e abrir o modelo Azure Resource Manager no portal do Azure. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/deploy-to-azure.png" alt="Deploy to Azure"></a>
3. Configure as seguintes propriedades:
   
   * **Subscrição**: selecione uma subscrição do Azure utilizada para criar o cluster.
   * **Grupo de recursos**: criar um novo grupo de recursos utilizado para o cluster.
   * **Localização**: Selecione uma localização para o grupo de recursos.
   * **Nome do cluster**: introduza um nome para o novo cluster criar.
   * **Nome de utilizador de início de sessão do cluster**: introduza o nome de utilizador de HTTP do Hadoop.  O nome predefinido é **admin**.
   * **Palavra-passe de início de sessão do cluster**: introduza a palavra-passe de utilizador do Hadoop HTTP.
   * **SSH nome de utilizador**: introduza o nome de utilizador SSH. O nome predefinido é **sshuser**.
   * **SSH palavra-passe**: introduza a palavra-passe de utilizador do SSH.
   * **Instalar a ação de Script**: mantenha o valor predefinido para percorrer neste tutorial.
     
     Foram codificado no modelo de algumas propriedades: tipo de Cluster, o número de nós de trabalho de Cluster, tamanho de nó de limite e o nome de nó de extremidade.
4. Verifique **concordo com os termos e condições indicadas acima**e, em seguida, clique em **Compra** para criar o cluster com o nó de extremidade.

## <a name="access-an-edge-node"></a>Aceder a um nó de extremidade
O nó de extremidade ssh ponto final está &lt;EdgeNodeName >.&lt; ClusterName >-ssh.azurehdinsight.net:22.  Por exemplo, novas-edgenode.myedgenode0914-ssh.azurehdinsight.net:22.

O nó de extremidade aparece como uma aplicação no portal do Azure.  O portal dá-lhe as informações para aceder ao nó de extremidade utilizando SSH.

**Para verificar o ponto final de SSH do nó de extremidade**

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Abra o cluster do HDInsight com um nó de extremidade.
3. Clique em **aplicações** do painel do cluster. Deverá ver o nó de extremidade.  O nome predefinido é **novo edgenode**.
4. Clique no nó de extremidade. Deverá ver o ponto final SSH.

**Para utilizar o Hive no nó de extremidade**

1. Utilize o SSH para ligar ao nó de extremidade. Para obter informações, veja [Use SSH with HDInsight (Utilizar SSH com o HDInsight)](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Depois de ligar ao nó de extremidade através do SSH, utilize o seguinte comando para abrir a consola do ramo de registo:
   
        hive
3. Execute o seguinte comando para mostrar as tabelas do Hive no cluster:
   
        show tables;

## <a name="delete-an-edge-node"></a>Eliminar um nó de extremidade
Pode eliminar um nó de extremidade do portal do Azure.

**Para aceder a um nó de extremidade**

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Abra o cluster do HDInsight com um nó de extremidade.
3. Clique em **aplicações** do painel do cluster. Deverá ver uma lista de nós de limite.  
4. Clique no nó de extremidade que pretende eliminar e, em seguida, clique em **eliminar**.
5. Clique em **Sim** para confirmar.

## <a name="next-steps"></a>Passos seguintes
Neste artigo, aprendeu como adicionar um nó de extremidade e como aceder ao nó de extremidade. Para obter mais informações, consulte os artigos seguintes:

* [Instalar aplicações HDInsight](hdinsight-apps-install-applications.md): Saiba como instalar aplicações HDInsight nos clusters.
* [Instalar aplicações HDInsight personalizadas](hdinsight-apps-install-custom-applications.md): Saiba como implementar uma aplicação HDInsight não publicada no HDInsight.
* [Publicar aplicações do HDInsight](hdinsight-apps-publish-applications.md): Saiba como publicar as suas aplicações do HDInsight personalizadas no Azure Marketplace.
* [MSDN: Instalar uma aplicação do HDInsight](https://msdn.microsoft.com/library/mt706515.aspx): Saiba como definir aplicações do HDInsight.
* [Personalizar clusters do HDInsight baseados em Linux através da Ação de Script](hdinsight-hadoop-customize-cluster-linux.md): saiba como utilizar a Ação de Script para instalar outras aplicações.
* [Create Linux-based Hadoop clusters in HDInsight using Resource Manager templates (Criar clusters Hadoop baseados em Linux no HDInsight com modelos do Resource Manager)](hdinsight-hadoop-create-linux-clusters-arm-templates.md): saiba como chamar modelos do Resource Manager para criar clusters HDInsight.

