<properties
    pageTitle="Instalar aplicações do Hadoop no HDInsight | Microsoft Azure"
    description="Saiba como instalar aplicações do HDInsight em aplicações do HDInsight."
    services="hdinsight"
    documentationCenter=""
    authors="mumian"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="09/14/2016"
    ms.author="jgao"/>


# Instalar aplicações do HDInsight personalizadas

Uma aplicação HDInsight é uma aplicação que os utilizadores podem instalar num cluster do HDInsight baseado em Linux.  Estas aplicações podem ser desenvolvidas pela Microsoft, por fornecedores independentes de software (ISV) ou por si. Neste artigo, ficará a saber como instalar uma aplicação do HDInsight que não tenha sido publicada ao Portal do Azure no HDInsight. A aplicação que irá instalar é a [Hue](http://gethue.com/). 

Outros artigos relacionados:

- [Instalar aplicações HDInsight](hdinsight-apps-install-applications.md): Saiba como instalar aplicações HDInsight nos clusters.
- [Publicar aplicações do HDInsight](hdinsight-apps-publish-applications.md): Saiba como publicar as suas aplicações do HDInsight personalizadas no Azure Marketplace.
- [MSDN: Instalar uma aplicação do HDInsight](https://msdn.microsoft.com/library/mt706515.aspx): Saiba como definir aplicações do HDInsight.

 
## Pré-requisitos

Se pretender instalar aplicações do HDInsight num cluster do HDInsight existente, tem de ter um cluster do HDInsight. Para criar um, consulte [Criar clusters](hdinsight-hadoop-linux-tutorial-get-started.md#create-cluster). Também pode instalar aplicações do HDInsight ao criar um cluster do HDInsight.


## Instalar aplicações do HDInsight

As aplicações do HDInsight podem ser instaladas quando cria um cluster ou num cluster do HDInsight existente. Para definir modelos do Azure Resource Manager, consulte [MSDN: Instalar uma aplicação do HDInsight](https://msdn.microsoft.com/library/mt706515.aspx).

Ficheiros necessários para implementar esta aplicação (Hue):

- [azuredeploy.json](https://github.com/hdinsight/Iaas-Applications/blob/master/Hue/azuredeploy.json): modelo do Azure Resource Manager para a instalação da aplicação do HDInsight. Consulte [MSDN: instalar uma aplicação do HDInsight](https://msdn.microsoft.com/library/mt706515.aspx) para desenvolver o seu próprio modelo do Azure Resource Manager.
- [hue install_v0.sh](https://github.com/hdinsight/Iaas-Applications/blob/master/Hue/scripts/Hue-install_v0.sh): a ação de Script a ser chamada pelo modelo do Azure Resource Manager para a configuração do nó de extremidade. 
- [hue binaries.tgz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/hue-binaries-14-04.tgz): O ficheiro binário da Hue a ser chamado a partir de hui install_v0.sh. 
- [hue-binaries-14-04.tgz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/hue-binaries-14-04.tgz): O ficheiro binário da Hue a ser chamado a partir de hui-install_v0.sh. 
- [webwasb tomcat.tar.gz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/webwasb-tomcat.tar.gz): Uma aplicação Web de exemplo (Tomcat) a ser chamada a partir de hui install_v0.sh.

**Para instalar a Hue num cluster do HDInsight existente**

1. Clique na imagem seguinte para iniciar sessão no Azure e abrir o modelo do Azure Resource Manager no Portal do Azure. 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2FIaas-Applications%2Fmaster%2FHue%2Fazuredeploy.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

    Este botão abre um modelo do Azure Resource Manager no Portal do Azure.  O modelo do Azure Resource Manager está localizado em [https://github.com/hdinsight/Iaas-Applications/tree/master/Hue](https://github.com/hdinsight/Iaas-Applications/tree/master/Hue).  Para obter informações sobre como escrever este modelo do Azure Resource Manager, consulte [MSDN: Instalar uma aplicação do HDInsight](https://msdn.microsoft.com/library/mt706515.aspx).
    
2. No painel **Parâmetros**, introduza o seguinte:

    - **ClusterName**: Introduza o nome do cluster onde pretende instalar a aplicação. Este cluster tem de ser um cluster existente.
    
3. Clique em **OK** para guardar os parâmetros.
4. No painel **Implementação personalizada**, introduza **Grupo de recursos**.  O grupo de recursos é um contentor que agrupa o cluster, a conta do Storage dependente e outros recursos. É necessário utilizar o mesmo grupo de recursos do cluster.
5. Clique em **Termos legais** e em **Criar**.
6. Verifique se a caixa de verificação **Afixar ao dashboard** está selecionada e, em seguida, clique em **Criar**. Pode ver o estado da instalação no mosaico afixado ao dashboard do portal e a notificação do portal (clique no ícone de sino na parte superior do portal).  A instalação da aplicação demora cerca de 10 minutos.

**Para instalar a Hue ao criar um cluster**

1. Clique na imagem seguinte para iniciar sessão no Azure e abrir o modelo do Azure Resource Manager no Portal do Azure. 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhdinsightapps%2Fcreate-linux-based-hadoop-cluster-in-hdinsight.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

    Este botão abre um modelo do Azure Resource Manager no Portal do Azure.  O modelo do Azure Resource Manager está localizado em [https://hditutorialdata.blob.core.windows.net/hdinsightapps/create-linux-based-hadoop-cluster-in-hdinsight.json](https://hditutorialdata.blob.core.windows.net/hdinsightapps/create-linux-based-hadoop-cluster-in-hdinsight.json).  Para obter informações sobre como escrever este modelo do Azure Resource Manager, consulte [MSDN: Instalar uma aplicação do HDInsight](https://msdn.microsoft.com/library/mt706515.aspx).

2. Siga a instrução para criar o cluster e instalar a Hue. Para obter mais informações sobre a criação de clusters do HDInsight, consulte [Criar clusters do Hadoop baseados em Linux no HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

Além de Portal do Azure, também pode utilizar o [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-arm-templates.md#deploy-with-powershell) e a [CLI do Azure](hdinsight-hadoop-create-linux-clusters-arm-templates.md#deploy-with-azure-cli) para chamar modelos do Azure Resource Manager.

## Validar a instalação

Pode verificar o estado da aplicação no Portal do Azure para validar a instalação da aplicação. Além disso, também pode validar se todos os pontos finais de HTTP surgiram conforme esperado e a página Web se for o caso:

**Para abrir o portal da Hue**

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Clique em **Clusters do HDInsight** no menu à esquerda.  Se o mesmo não for apresentado, clique em **Procurar** e, em seguida, em **Clusters do HDInsight**.
3. Clique no cluster onde instalou a aplicação.
4. No painel **Definições**, clique em **aplicações** na categoria **Geral**. Deverá ver **hue** indicada no painel **Aplicações Instaladas**.
5. Clique em **hue** na lista para ver as propriedades.  
6. Clique na ligação da página Web para validar o Web Site; abra o ponto final de HTTP num browser para validar a IU da Web da Hue, abra o ponto final de SSH com [PuTTY](hdinsight-hadoop-linux-use-ssh-windows.md) ou outros [clientes SSH](hdinsight-hadoop-linux-use-ssh-unix.md).
 
## Resolver problemas relacionados com a instalação

Pode verificar o estado da instalação da aplicação na notificação do portal (clique no ícone de sino na parte superior do portal). 


Em caso de falha na instalação de uma aplicação, pode ver as mensagens de erro e as informações de depuração em 3 locais:

- Aplicações do HDInsight: informações de erro gerais.

    Abra o cluster a partir do portal e clique em Aplicações no painel Definições:

    ![aplicações do hdinsight erro de instalação da aplicação](./media/hdinsight-apps-install-applications/hdinsight-apps-error.png)

- Ação de script do HDInsight: Se a mensagem de erro das Aplicações do HDInsight indicar uma falha de ação de script, serão apresentados mais detalhes sobre a falha de script no painel de ações de script.

    Clique em Ação de Script no painel Definições. O histórico de ações de script apresenta as mensagens de erro

    ![aplicações do hdinsight erro de ação de script](./media/hdinsight-apps-install-applications/hdinsight-apps-script-action-error.png)
    
- IU da Web do Ambari: Se o script de instalação tiver sido a causa da falha, utilize a IU da Web do Ambari para verificar os registos completos sobre os scripts de instalação.

    Para obter mais informações, consulte [Resolução de problemas](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting).

## Remover aplicações do HDInsight

Existem várias formas de eliminar aplicações de HDInsight.

### Utilizar o portal

**Para remover uma aplicação através do portal**

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Clique em **Clusters do HDInsight** no menu à esquerda.  Se o mesmo não for apresentado, clique em **Procurar** e, em seguida, em **Clusters do HDInsight**.
3. Clique no cluster onde instalou a aplicação.
4. No painel **Definições**, clique em **aplicações** na categoria **Geral**. Deverá ver uma lista da aplicação instalada. Para este tutorial, a **hue** está indicada no painel **Aplicações Instaladas**.
5. Clique com o botão direito do rato na aplicação que pretende remover e, em seguida, clique em **Eliminar**.
6. Clique em **Sim** para confirmar.

No portal, também pode eliminar o cluster ou eliminar o grupo de recursos que contém a aplicação.

### Utilizar o Azure PowerShell

Através do Azure PowerShell, pode eliminar o cluster ou o grupo de recursos. Consulte [Eliminar clusters ao utilizar o Azure PowerShell](hdinsight-administer-use-powershell.md#delete-clusters).

### Utilizar a CLI do Azure

Com a CLI do Azure, pode eliminar o cluster ou o grupo de recursos. Consulte [Eliminar clusters ao utilizar a CLI do Azure](hdinsight-administer-use-command-line.md#delete-clusters).


## Passos seguintes

- [MSDN: instalar uma aplicação do HDInsight](https://msdn.microsoft.com/library/mt706515.aspx): saiba como desenvolver modelos do Azure Resource Manager para a implementação de aplicações do HDInsight.
- [Instalar aplicações HDInsight](hdinsight-apps-install-applications.md): Saiba como instalar aplicações HDInsight nos clusters.
- [Publicar aplicações do HDInsight](hdinsight-apps-publish-applications.md): Saiba como publicar as suas aplicações do HDInsight personalizadas no Azure Marketplace.
- [Personalizar clusters do HDInsight baseados em Linux através da Ação de Script](hdinsight-hadoop-customize-cluster-linux.md): saiba como utilizar a Ação de Script para instalar outras aplicações.
- [Create Linux-based Hadoop clusters in HDInsight using Resource Manager templates (Criar clusters Hadoop baseados em Linux no HDInsight com modelos do Resource Manager)](hdinsight-hadoop-create-linux-clusters-arm-templates.md): saiba como chamar modelos do Resource Manager para criar clusters HDInsight.
- [Utilizar nós de extremidade vazios no HDInsight](hdinsight-apps-use-edge-node.md): saiba como utilizar um nó de extremidade vazio para aceder ao cluster do HDInsight, testar aplicações do HDInsight e alojar aplicações do HDInsight.



<!--HONumber=Sep16_HO3-->


