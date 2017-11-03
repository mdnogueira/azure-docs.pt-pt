---
title: "Publicar aplicações do Azure HDInsight | Microsoft Docs"
description: "Saiba como criar uma aplicação do HDInsight e, em seguida, publicá-lo no Azure Marketplace."
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 14aef891-7a37-4cf1-8f7d-ca923565c783
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/21/2017
ms.author: jgao
ms.openlocfilehash: 34550ed33cd81bcbf5b405a5e5c09d25adf5e6ac
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="publish-an-hdinsight-application-in-the-azure-marketplace"></a>Publicar uma aplicação HDInsight no Azure Marketplace
Pode instalar uma aplicação do Azure HDInsight num cluster do HDInsight baseado em Linux. Neste artigo, saiba como publicar uma aplicação HDInsight no Azure Marketplace. Para obter informações gerais sobre a publicação no Azure Marketplace, consulte [publicar uma oferta no Azure Marketplace](../marketplace-publishing/marketplace-publishing-getting-started.md).

As aplicações HDInsight utilizam o *traga a sua própria licença (BYOL)* modelo. Num cenário BYOL, um fornecedor de aplicação é responsável por licenciar a aplicação para utilizadores de aplicações. Utilizadores de aplicações são-lhe cobrados apenas para os recursos do Azure que criarem, como o cluster do HDInsight e o cluster VMs e os nós. Atualmente, a faturação da própria aplicação não ocorre no Azure.

Para obter mais informações, consulte estes artigos relacionados com a aplicação do HDInsight:

* [Instalar aplicações HDInsight](hdinsight-apps-install-applications.md). Saiba como instalar aplicações HDInsight nos clusters.
* [Instalar aplicações HDInsight personalizadas](hdinsight-apps-install-custom-applications.md). Saiba como instalar e testar aplicações HDInsight personalizadas.

## <a name="prerequisites"></a>Pré-requisitos
Ao submeter a sua aplicação personalizada no Marketplace, em primeiro lugar, [criar e testar a sua aplicação personalizada](hdinsight-apps-install-custom-applications.md).

Também tem de registar a conta de programador. Para obter mais informações, consulte [publicar uma oferta no Azure Marketplace](../marketplace-publishing/marketplace-publishing-getting-started.md) e [criar uma conta Microsoft Developer](../marketplace-publishing/marketplace-publishing-accounts-creation-registration.md).

## <a name="define-the-application"></a>Definir a aplicação
Dois passos envolvidos na publicação de aplicações no mercado. Em primeiro lugar, definir uma *Createuidef* ficheiro. O ficheiro Createuidef indica que os clusters da aplicação é compatível com. Em seguida, publica o modelo a partir do portal do Azure. Segue-se um ficheiro Createuidef de exemplo:

```json
{
    "handler": "Microsoft.HDInsight",
    "version": "0.0.1-preview",
    "clusterFilters": {
        "types": ["Hadoop", "HBase", "Storm", "Spark"],
        "tiers": ["Standard", "Premium"],
        "versions": ["3.4"]
    }
}
```

| Campo | Descrição | Valores possíveis |
| --- | --- | --- |
| tipos |Os tipos de cluster com os quais a aplicação é compatível. |Hadoop, HBase, Storm, Spark (ou qualquer combinação destes) |
| escalões |Os escalões de cluster com os quais a aplicação é compatível. |Standard, Premium (ou ambos) |
| versões |Os tipos de cluster do HDInsight com os quais a aplicação é compatível. |3.4 |

## <a name="application-installation-script"></a>Script de instalação da aplicação
Quando uma aplicação é instalada num cluster (à um cluster existente ou com uma nova), é criado um nó de extremidade. O script de instalação da aplicação é executada no nó de extremidade.

  > [!IMPORTANT]
  > O nome do script de instalação de aplicações tem de ser exclusivo de um cluster específico. O nome do script tem de ter o seguinte formato:
  > 
  > "nome": "[concat ('hue-install-v0 ','-', uniquestring]"
  > 
  > O nome do script tem três partes:
  > 
  > * Um prefixo de nome script, que tem de incluir o nome da aplicação ou um nome relevante para a aplicação.
  > * Um hífen, para facilitar a leitura.
  > * Uma função de cadeia exclusiva, com o nome da aplicação como parâmetro.
  > 
  > Na lista de ação de script persistentes, o exemplo anterior é apresentado como **hue-install-v0-4wkahss55hlas**. Consulte uma [payload JSON de exemplo](https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/Hue/azuredeploy.json).
  > 

O script de instalação tem de ter as seguintes características:
* O script é idempotent. Várias chamadas para o script produzem o mesmo resultado.
* O script é corretamente com a versão. Utilize uma localização diferente para o script quando estiver a atualizar ou testar alterações. Isto garante que os clientes que estiver a instalar a aplicação não são afetados pelas atualizações ou teste. 
* O script tem registo adequado em cada ponto. Normalmente, os registos de script são a única forma de depurar problemas de instalação da aplicação.
* As chamadas para recursos ou serviços externos têm tentativas adequadas, para que a instalação não é afetada por problemas de rede transitórios.
* Se o script for iniciado serviços em nós, os serviços são monitorizados e configurados para iniciar automaticamente se ocorrer um reinício do nó.

## <a name="package-the-application"></a>Pacote de aplicação
Crie um ficheiro. zip que contém todos os ficheiros que são necessários para instalar a aplicação do HDInsight. Utilize o ficheiro. zip para [publicar a aplicação](#publish-application). O ficheiro. zip inclui os seguintes ficheiros:

* [createUiDefinition.json](#define-application)
* mainTemplate.json (para um exemplo, consulte [instalar aplicações HDInsight personalizadas](hdinsight-apps-install-custom-applications.md).)
* Scripts todos requeridos

> [!NOTE]
> Pode alojar os ficheiros da aplicação (incluindo os ficheiros de aplicação web) em qualquer ponto final acessível publicamente.
> 

## <a name="publish-the-application"></a>Publicar a aplicação
Para publicar uma aplicação HDInsight:

1. Inicie sessão no [publicação do Azure](https://publish.windowsazure.com/).
2. No menu à esquerda, selecione **modelos de solução**.
3. Introduza um título e, em seguida, selecione **criar um novo modelo de solução**.
4. Se ainda não registou sua organização, selecione **conta criar Dev Center e aderir ao programa Azure**.  Para obter mais informações, consulte [criar uma conta Microsoft Developer](../marketplace-publishing/marketplace-publishing-accounts-creation-registration.md).
5. Selecione **definir algumas topologias para começar a utilizar**. Um modelo de solução é um "elemento principal" para todas as respetivas topologias. Pode definir vários topologias num modelo de oferta ou solução um. Quando é emitida uma oferta para teste, esta é instalada com todas as respetivas topologias. 
6. Introduza um nome de topologia e, em seguida, selecione  **+** .
7. Introduza uma nova versão e, em seguida, selecione  **+** .
8. Carregue o ficheiro. zip que criou quando a [empacotada a aplicação](#package-application).  
9. Selecione **solicitar certificação**. A equipa de certificação da Microsoft analisa os ficheiros e se certifica a topologia.

## <a name="next-steps"></a>Passos seguintes
* Saiba como [instalar aplicações HDInsight](hdinsight-apps-install-applications.md) nos seus clusters.
* Saiba como [instalar aplicações HDInsight personalizadas](hdinsight-apps-install-custom-applications.md) e implementar uma aplicação HDInsight não publicada no HDInsight.
* Saiba como [utilizar a ação de Script ao personalizar clusters do HDInsight baseado em Linux](hdinsight-hadoop-customize-cluster-linux.md) e adicionar mais aplicações. 
* Saiba como [criar clusters do Hadoop baseados em Linux no HDInsight com modelos Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).
* Saiba como [utilizar um nó de extremidade vazio no HDInsight](hdinsight-apps-use-edge-node.md) para aceder a clusters do HDInsight, testar aplicações HDInsight e alojar aplicações do HDInsight.

