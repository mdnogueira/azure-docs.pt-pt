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

# Instalar aplicações do HDInsight

Uma aplicação HDInsight é uma aplicação que os utilizadores podem instalar num cluster do HDInsight baseado em Linux. Estas aplicações podem ser desenvolvidas pela Microsoft, por fornecedores independentes de software (ISV) ou por si. Neste artigo, vai ficar a saber como instalar uma aplicação publicada. Para instalar a sua própria aplicação, veja [Instalar aplicações do HDInsight personalizadas](hdinsight-apps-install-custom-applications.md). 

Atualmente, existe uma aplicação publicada:

- **Datameer**: o [Datameer](http://www.datameer.com/documentation/display/DAS50/Home?ls=Partners&lsd=Microsoft&c=Partners&cd=Microsoft) oferece aos analistas uma forma interativa de detetar, analisar e visualizar os resultados de Macrodados. Extraia origens de dados adicionais facilmente para detetar novas relações e obter depressa as respostas de que precisa.

>[AZURE.NOTE] Atualmente, o Datameer só é suportado em clusters da versão 3.2 do Azure HDInsight.

As instruções fornecidas neste artigo utilizam o portal do Azure. Também pode exportar o modelo do Azure Resource Manager a partir do portal ou obter uma cópia do modelo do Resouce Manager junto de fornecedores e utilizar o Azure PowerShell e a CLI do Azure para implementar o modelo.  Veja [Create Linux-based Hadoop clusters in HDInsight using Resource Manager templates (Criar clusters Hadoop baseados em Linux no HDInsight com modelos do Resource Manager)](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

## Pré-requisitos

Se pretender instalar aplicações do HDInsight num cluster do HDInsight existente, tem de ter um cluster do HDInsight. Para criar um, consulte [Criar clusters](hdinsight-hadoop-linux-tutorial-get-started.md#create-cluster). Também pode instalar aplicações do HDInsight ao criar um cluster do HDInsight.

## Instalar aplicações em clusters existentes

O procedimento seguinte mostra-lhe como instalar aplicações do HDInsight num cluster HDInsight existente.

**Para instalar uma aplicação do HDInsight**

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Clique em **Clusters do HDInsight** no menu à esquerda.  Se o mesmo não for apresentado, clique em **Procurar** e, em seguida, em **Clusters do HDInsight**.
3. Clique num cluster HDInsight.  Se não tiver um, tem de criá-lo.  Veja [Create clusters (Criar clusters)](hdinsight-hadoop-linux-tutorial-get-started.md#create-cluster).
4. No painel **Definições**, clique em **aplicações** na categoria **Geral**. O painel **Aplicações Instaladas** apresenta todas as aplicações instaladas. 

    ![menu do portal das aplicações do hdinsight](./media/hdinsight-apps-install-applications/hdinsight-apps-portal-menu.png)

5. Clique em **Adicionar** no menu do painel. 

    ![aplicações instaladas do hdinsight](./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps.png)

    Deverá ver uma lista de aplicações do HDInsight existentes.

    ![aplicações do hdinsight disponíveis](./media/hdinsight-apps-install-applications/hdinsight-apps-list.png)

6. Clique numa das aplicações, aceite os termos legais e clique em **Selecionar**.

Pode verificar o estado da instalação nas notificações do portal (clique no ícone de sino na parte superior do portal). Depois de instalada, a aplicação aparece no painel Aplicações Instaladas.

## Instalar aplicações durante a criação do cluster

Tem a opção de instalar aplicações do HDInsight quando cria um cluster. Durante o processo, as aplicações do HDInsight são instaladas depois de o cluster ser criado e estar no estado Em Execução. O procedimento seguinte mostra-lhe como instalar aplicações do HDInsight quando cria um cluster.

**Para instalar uma aplicação do HDInsight**

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Clique em **NOVA**, clique em **Dados + Análises** e clique em **HDInsight**.
3. Introduza o **Nome do Cluster**: este nome tem de ser globalmente exclusivo.
4. Clique em **Subscrição** para selecionar a subscrição do Azure que será utilizada no cluster.
5. Clique em **Selecionar tipo de cluster** e, em seguida, selecione:

    - **Tipo de Cluster**: se não sabe o que escolher, selecione **Hadoop**. É o tipo de cluster mais popular.
    - **Sistema Operativo**: selecione **Linux**.
    - **Versão**: utilize a versão predefinida se não souber o que escolher. Para obter mais informações, veja [HDInsight cluster versions (Versões de clusters HDInsight)](hdinsight-component-versioning.md).
    - **Escalão do Cluster**: o Azure HDInsight disponibiliza as ofertas de nuvem de macrodados em duas categorias -- escalão Standard e escalão Premium. Para obter mais informações, veja [Escalões de Clusters](hdinsight-hadoop-provision-linux-clusters.md#cluster-tiers).
6. Clique em **Aplicações**, clique numa das aplicações publicadas e, em seguida, clique em **Selecionar**.
6. Clique em **Credenciais** e, em seguida, introduza uma palavra-passe para o utilizador administrador. Também tem de introduzir um **Nome de Utilizador de SSH** e uma **PALAVRA-PASSE** ou uma **CHAVE PÚBLICA**, que será utilizada para autenticar o utilizador do SSH. A abordagem recomendada é utilizar uma chave pública. Clique em **Selecionar**, na parte inferior, para guardar a configuração das credenciais.
8. Clique em **Origem de Dados**, selecione uma das contas de armazenamento existentes ou crie uma nova que vai ser utilizada como a conta de armazenamento predefinida do cluster.
9. Clique em **Grupo de Recursos** para ver um grupo de recursos existente ou clique em **Novo** para criar um novo.

10. No painel **Novo Cluster HDInsight**, confirme que **Afixar ao Startboard** está selecionado e, em seguida, clique em **Criar**. 

## Apresentar as aplicações do HDInsight instaladas e propriedades

O portal mostra uma lista das aplicações do HDInsight instaladas relativas a um cluster e as propriedades de cada uma dessas aplicações.

**Para apresentar as aplicações do HDInsight e as respetivas propriedades**

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Clique em **Clusters do HDInsight** no menu à esquerda.  Se o mesmo não for apresentado, clique em **Procurar** e, em seguida, em **Clusters do HDInsight**.
3. Clique num cluster HDInsight.
4. No painel **Definições**, clique em **aplicações** na categoria **Geral**. O painel Aplicações Instaladas apresenta uma lista de todas as aplicações instaladas. 

    ![aplicações instaladas do hdinsight](./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps-with-apps.png)

5. Clique numa das aplicações instaladas para mostrar a propriedade. O painel de propriedades mostra:

    - Nome da aplicação: o nome da aplicação.
    - Estado: o estado da aplicação. 
    - Página Web: o URL da aplicação Web que implementou no nó de extremidade, caso haja algum. A credencial é a mesma que as credenciais de utilizador HTTP que configurou para o cluster.
    - Ponto final de HTTP: a credencial é a mesma que as credenciais de utilizador HTTP que configurou para o cluster. 
    - Ponto final de SSH: pode utilizar o [SSH](hdinsight-hadoop-linux-use-ssh-unix.md) para ligar ao nó de extremidade. As credenciais de SSH são as mesmas que as credenciais de utilizador de SSH que configurou para o cluster.

6. Para eliminar uma aplicação, clique com o botão direito do rato na mesma e, em seguida, clique em **Eliminar**, no menu de contexto.

## Ligar ao nó de extremidade

Pode ligar ao nó de extremidade com HTTP e SSH. Pode encontrar as informações relativas ao ponto final no [portal](#list-installed-hdinsight-apps-and-properties). Para obter mais informações sobre como utilizar o SSH, veja [Utilizar o SSH com o Hadoop baseado em Linux no HDInsight a partir do Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md). 

As credenciais do ponto final de HTTP são as credenciais de utilizador HTTP que configurou para o cluster HDInsight; as credenciais do ponto final de SSH são as credenciais de SSH que configurou para o cluster HDInsight.

## Resolução de problemas

Veja [Troubleshoot the installation (Resolver problemas de instalação)](hdinsight-apps-install-custom-applications.md#troubleshoot-the-installation).

## Passos seguintes

- [Instalar aplicações HDInsight personalizadas](hdinsight-apps-install-custom-applications.md): saiba como implementar uma aplicação HDInsight não publicada no HDInsight.
- [Publicar aplicações do HDInsight](hdinsight-apps-publish-applications.md): Saiba como publicar as suas aplicações do HDInsight personalizadas no Azure Marketplace.
- [MSDN: Instalar uma aplicação do HDInsight](https://msdn.microsoft.com/library/mt706515.aspx): Saiba como definir aplicações do HDInsight.
- [Personalizar clusters do HDInsight baseados em Linux através da Ação de Script](hdinsight-hadoop-customize-cluster-linux.md): saiba como utilizar a Ação de Script para instalar outras aplicações.
- [Create Linux-based Hadoop clusters in HDInsight using Resource Manager templates (Criar clusters Hadoop baseados em Linux no HDInsight com modelos do Resource Manager)](hdinsight-hadoop-create-linux-clusters-arm-templates.md): saiba como chamar modelos do Resource Manager para criar clusters HDInsight.
- [Utilizar nós de extremidade vazios no HDInsight](hdinsight-apps-use-edge-node.md): saiba como utilizar um nó de extremidade vazio para aceder ao cluster do HDInsight, testar aplicações do HDInsight e alojar aplicações do HDInsight.




<!--HONumber=sep16_HO2-->


