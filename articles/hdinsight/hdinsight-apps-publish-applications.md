<properties
    pageTitle="Publicar aplicações HDInsight | Microsoft Azure"
    description="Saiba como criar e publicar aplicações HDInsight."
    services="hdinsight"
    documentationCenter=""
    authors="mumian"
    manager="paulettm"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="06/29/2016"
    ms.author="jgao"/>

# Publicar aplicações HDInsight para no Azure Marketplace

Uma aplicação HDInsight é uma aplicação que os utilizadores podem instalar num cluster do HDInsight baseado em Linux. Estas aplicações podem ser desenvolvidas pela Microsoft, por fornecedores independentes de software (ISV) ou por si. Neste artigo, ficará a saber como publicar uma aplicação HDInsight no Azure Marketplace.  Para obter informações gerais sobre a publicação no Azure Marketplace, consulte [publicar uma oferta no Azure Marketplace](../marketplace-publishing/marketplace-publishing-getting-started.md).

As aplicações HDInsight utilizam o modelo *Traga a sua própria licença (BYOL)*, onde o fornecedor da aplicação é responsável por licenciar a aplicação para os utilizadores finais e estes são cobrados pelo Azure apenas pelos recursos que criarem, tais como o cluster do HDInsight e os respetivos nós/VMs. Neste momento, a faturação da própria aplicação não é efetuada através do Azure.

Outro artigo relacionado com a aplicação HDInsight:

- [Instalar aplicações HDInsight](hdinsight-apps-install-applications.md): Saiba como instalar aplicações HDInsight nos clusters.
- [Instalar aplicações HDInsight personalizadas](hdinsight-apps-install-custom-applications.md): Saiba como instalar e testar aplicações HDInsight personalizadas.

 
## Pré-requisitos

Para submeter a sua aplicação personalizada para o mercado, é necessário que tenha criado e testado a sua aplicação personalizada. Consulte os seguintes artigos:

- [Instalar aplicações HDInsight personalizadas](hdinsight-apps-install-custom-applications.md): Saiba como instalar e testar aplicações HDInsight personalizadas.

Tem também de ter registado a sua conta de programador. Consulte [Publicar uma oferta no Azure Marketplace](../marketplace-publishing/marketplace-publishing-getting-started.md) e [Criar uma conta de Programador da Microsoft](../marketplace-publishing/marketplace-publishing-accounts-creation-registration.md).

## Definir a aplicação

Existem dois passos envolvidos para publicar aplicações no Azure Marketplace.  Em primeiro lugar, tem de definir um ficheiro **createUiDef.json** para indicar os clusters com os quais a sua aplicação é compatível; em seguida, publique o modelo a partir do Portal do Azure. Segue-se um ficheiro createUiDef.json de exemplo.

    {
        "handler": "Microsoft.HDInsight",
        "version": "0.0.1-preview",
        "clusterFilters": {
            "types": ["Hadoop", "HBase", "Storm", "Spark"],
            "tiers": ["Standard", "Premium"],
            "versions": ["3.4"]
        }
    }


|Campo  | Descrição   | Valores possíveis|
|-------|---------------|----------------|
|tipos  |Os tipos de cluster com os quais a aplicação é compatível. |Hadoop, HBase, Storm, Spark (ou qualquer combinação destes)|
|escalões  |Os escalões de cluster com os quais a aplicação é compatível. |Standard, Premium (ou ambos)|
|versões|  Os tipos de cluster do HDInsight com os quais a aplicação é compatível.    |3.4|

## Empacotar a aplicação

Crie um ficheiro zip que inclua todos os ficheiros necessários para instalar as suas aplicações HDInsight. Necessitará do ficheiro zip em [Publicar a aplicação](#publish-application).

- [createUiDefinition.json](#define-application).
- mainTemplate.json. Veja um exemplo em [Instalar aplicações HDInsight personalizadas](hdinsight-apps-install-custom-applications.md).

    >[AZURE.IMPORTANT] O nome dos nomes do script de instalação de aplicações têm de ser exclusivos de um determinado cluster com o formato abaixo. Além disso quaisquer ações de scrip de instalar e desinstalar devem ser idempotent, que significa que os scripts de podem ser convocados repetidamente ao produzir o mesmo resultado.
    
    >   nome": "[concat('hue-install-v0','-' ,uniquestring(‘applicationName’)]"
        
    >Tenha em atenção que o nome do script é constituído por três partes:
        
    >   1. Um prefixo do nome do script, que deve incluir ou o nome da aplicação ou um nome relevante para a aplicação.
    >   2. Um "-" para legibilidade.
    >   3. Uma função de cadeia exclusiva com o nome da aplicação como parâmetro.

    >   O exemplo acima acaba por ficar da seguinte forma: hue-install-v0-4wkahss55hlas na lista de ações de script persistentes. Para um payload JSON de exemplo, consulte [https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/Hue/azuredeploy.json](https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/Hue/azuredeploy.json).

- Todos os scripts necessários.

> [AZURE.NOTE] Os ficheiros da aplicação (incluindo os ficheiros da aplicação Web, se existentes) podem estar localizados em qualquer ponto final acessível publicamente.

## Publicar a aplicação

Siga os passos abaixo para publicar uma aplicação HDInsight:

1. Inicie sessão no [portal de Publicação do Azure](https://publish.windowsazure.com/).
2. Clique em **Modelos de solução** para criar um novo modelo de solução.
3. Clique em **Criar conta do Dev Center e aderir ao programa Azure** para registar a sua empresa, se ainda não o tiver feito.  Consulte [Criar uma conta de Programador da Microsoft](../marketplace-publishing/marketplace-publishing-accounts-creation-registration.md).
4. Clique em **Definir algumas topologias para começar**. Um modelo de solução é um "elemento principal" para todas as suas topologias. Pode definir vários topologias num modelo de oferta/solução. Quando é emitida uma oferta para teste, esta é instalada com todas as respetivas topologias. 
5. Adicione uma nova versão.
6. Carregue o ficheiro zip preparado em [Empacotar a aplicação](#package-application).  
7. Clique em **Solicitar Certificação**. A equipa de certificação da Microsoft irá analisar os ficheiros e certificar a topologia.

## Passos seguintes

- [Instalar aplicações HDInsight](hdinsight-apps-install-applications.md): Saiba como instalar aplicações HDInsight nos clusters.
- [Instalar aplicações HDInsight personalizadas](hdinsight-apps-install-custom-applications.md): saiba como implementar uma aplicação HDInsight não publicada no HDInsight.
- [Personalizar clusters do HDInsight baseados em Linux através da Ação de Script](hdinsight-hadoop-customize-cluster-linux.md): saiba como utilizar a Ação de Script para instalar outras aplicações.
- [Crie clusters do Hadoop baseados em Linux no HDInsight utilizando modelos do Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md): saiba como convocar modelos Resource Manager para criar clusters do HDInsight.


<!--HONumber=Aug16_HO1-->


