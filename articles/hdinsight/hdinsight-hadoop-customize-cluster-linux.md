---
title: "Personalizar clusters do HDInsight utilizando ações de script - Azure | Microsoft Docs"
description: "Adicione componentes personalizados para os clusters do HDInsight baseado em Linux utilizando as ações de Script. Ações de script são scripts de Bash que podem ser utilizadas para personalizar a configuração de cluster ou adicionar serviços adicionais e utilitários como Hue, Solr ou R."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 48e85f53-87c1-474f-b767-ca772238cc13
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2017
ms.author: larryfr
ms.openlocfilehash: 0c5d00b6cb9f68a1a0e474f81c969eb1b5654c67
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="customize-linux-based-hdinsight-clusters-using-script-action"></a>Personalizar clusters do HDInsight baseado em Linux através da ação de Script

O HDInsight fornece uma opção de configuração denominada **ação de Script** que invoca scripts personalizados que personalizam o cluster. Estes scripts são utilizados para instalar componentes adicionais e alterar as definições de configuração. Ações de script podem ser utilizadas durante ou após a criação do cluster.

> [!IMPORTANT]
> A capacidade de utilizar as ações de script num cluster já em execução só está disponível para clusters do HDInsight baseado em Linux.
>
> O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).

Ações de script também podem ser publicadas para o Azure Marketplace como uma aplicação do HDInsight. Alguns exemplos neste documento mostram como instalar uma aplicação do HDInsight utilizando os comandos de ação de script do PowerShell e o SDK .NET. Para obter mais informações sobre as aplicações do HDInsight, consulte [aplicações do HDInsight publicar no Azure Marketplace](hdinsight-apps-publish-applications.md).

## <a name="permissions"></a>Permissões

Se estiver a utilizar um cluster do HDInsight associados a um domínio, existem dois Ambari as permissões necessárias ao utilizar as ações de script com o cluster:

* **AMBARI. EXECUTAR\_personalizada\_comando**: função de administrador do Ambari o tenha esta permissão, por predefinição.
* **CLUSTER. EXECUTAR\_personalizada\_comando**: tanto o administrador de Cluster do HDInsight e administrador de Ambari tem esta permissão, por predefinição.

Para obter mais informações sobre como trabalhar com permissões com o HDInsight associados a um domínio, consulte [gerir clusters do HDInsight associados a um domínio](hdinsight-domain-joined-manage.md).

## <a name="access-control"></a>Controlo de acesso

Se não for o proprietário/administrador da sua subscrição do Azure, a conta tem de ter, pelo menos, **contribuinte** acesso ao grupo de recursos que contém o cluster do HDInsight.

Além disso, se estiver a criar, pelo menos, um cluster do HDInsight, alguém com **contribuinte** acesso à subscrição do Azure tem tiver registado anteriormente o fornecedor para o HDInsight. O registo do fornecedor acontece quando um utilizador com o acesso Contribuidor para a subscrição cria um recurso na mesma pela primeira vez. Também pode ser feito sem a criação de um recurso, ao [registar o fornecedor com REST](https://msdn.microsoft.com/library/azure/dn790548.aspx).

Para obter mais informações sobre como trabalhar com a gestão do acesso, veja os documentos seguintes:

* [Get started with access management in the Azure portal (Introdução à gestão do acesso no portal do Azure)](../active-directory/role-based-access-control-what-is.md)
* [Utilize atribuições de funções para gerir o acesso aos recursos de subscrição do Azure](../active-directory/role-based-access-control-configure.md)

## <a name="understanding-script-actions"></a>Noções sobre ações de Script

Uma ação de Script é simplesmente um script de deteção que fornece um URI para e os parâmetros para. O script é executado em nós de cluster do HDInsight. Seguem-se as características e as funcionalidades de ações de script.

* Deve ser armazenado no URI que é acessível a partir do cluster do HDInsight. Seguem-se as localizações de armazenamento possíveis:

    * Um **Azure Data Lake Store** conta que seja acessível para o cluster do HDInsight. Para obter informações sobre como utilizar o Azure Data Lake Store com o HDInsight, consulte [criar um cluster do HDInsight com o Data Lake Store](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

        Quando utilizar um script armazenado no Data Lake Store, o formato URI é `adl://DATALAKESTOREACCOUNTNAME.azuredatalakestore.net/path_to_file`.

        > [!NOTE]
        > O HDInsight utiliza para aceder ao arquivo Data Lake do principal de serviço tem de ter acesso de leitura para o script.

    * Um blob num **conta de armazenamento do Azure** que é o principal ou adicionais conta de armazenamento para o cluster do HDInsight. HDInsight é concedido acesso a ambos os tipos de contas de armazenamento durante a criação do cluster.

    * Um ficheiro pública partilha serviço como o Blob do Azure, o GitHub, OneDrive, Dropbox, etc.

        Por exemplo URIs, consulte o [scripts de ação de script de exemplo](#example-script-action-scripts) secção.

        > [!WARNING]
        > HDInsight suporta apenas __para fins gerais__ contas de armazenamento do Azure. Não suporta atualmente o __armazenamento de BLOBs__ tipo de conta.

* Pode ser restringida a **em apenas determinados tipos de nó**para nós principais de exemplo ou nós de trabalho.

  > [!NOTE]
  > Quando utilizado com o HDInsight Premium, pode especificar que o script deve ser utilizado no nó de extremidade.

* Pode ser **persistente** ou **ad hoc**.

    **Persistente** scripts são aplicados a nós de trabalho adicionados ao cluster depois do script é executado. Por exemplo, quando o cluster de dimensionamento.

    Um script persistente também podem ser aplicadas alterações a outro tipo de nó, tais como um nó principal.

  > [!IMPORTANT]
  > Ações de script persistentes tem de ter um nome exclusivo.

    **Ad hoc** scripts não são mantidos. Não são aplicadas a nós de trabalho adicionados ao cluster depois do script foi executado. Pode, subsequentemente, promover um script para um script persistente ad hoc ou despromover um script persistente para um script ad hoc.

  > [!IMPORTANT]
  > Ações de script utilizadas durante a criação do cluster automaticamente são mantidas.
  >
  > Scripts que não são falhar persistem, mesmo se especificamente a indicar que devem ser.

* Pode aceitar **parâmetros** que são utilizados pelo script durante a execução.
* Executar com **privilégios de nível de raiz** em nós de cluster.
* Pode ser utilizada através do **portal do Azure**, **Azure PowerShell**, **CLI do Azure**, ou **SDK .NET do HDInsight**

O cluster mantém um histórico de todos os scripts que tenha sido executado. O histórico é útil quando tem de localizar o ID de um script para operações de promoção ou despromoção.

> [!IMPORTANT]
> Não é uma forma automática de anular as alterações efetuadas por uma ação de script. Manualmente reverter as alterações ou fornecer um script que inverte-los.


### <a name="script-action-in-the-cluster-creation-process"></a>Ação de script no processo de criação de cluster

Ações de script utilizadas durante a criação do cluster são ligeiramente diferentes do script foi executadas ações num cluster existente:

* O script é **automaticamente persistente**.
* A **falha** no script pode provocar a falha no processo de criação de cluster.

O diagrama seguinte ilustra quando a ação de Script é executada durante o processo de criação:

![Personalização de cluster do HDInsight e fases durante a criação do cluster][img-hdi-cluster-states]

O script é executado enquanto HDInsight está a ser configurado. Nesta fase, o script é executado em paralelo em todos os nós no cluster especificados e é executado com privilégios de raiz em nós.

> [!NOTE]
> Porque o script é executado com privilégios de nível de raiz em nós de cluster, pode efetuar operações como parar e iniciar serviços, incluindo os serviços relacionados com o Hadoop. Se parar os serviços, certifique-se de que o serviço do Ambari e outros serviços relacionados com o Hadoop estão em execução antes do script é executado concluída. Estes serviços são necessários para determinar com êxito o estado de funcionamento e o estado do cluster enquanto está a ser criada.


Durante a criação do cluster, pode utilizar várias ações de script em simultâneo. Estes scripts estão invocados pela ordem em que foram especificadas.

> [!IMPORTANT]
> Ações de script tem de concluir dentro de 60 minutos ou tempo limite. Durante o aprovisionamento de cluster, o script é executado simultaneamente com outros processos de instalação e configuração. Disputa pelos recursos, tais como a largura de banda de CPU, tempo ou de rede pode fazer com que o script a demorar mais tempo a concluir do que-lo no seu ambiente de desenvolvimento.
>
> Para minimizar o tempo que demora a executar o script, evite tarefas, tais como transferir e compilar aplicações a partir da origem. Pré-compilar aplicações e armazenar o binário no armazenamento do Azure.


### <a name="script-action-on-a-running-cluster"></a>Ação de script num cluster em execução

Ao contrário das ações utilizadas durante a criação do cluster, uma falha num script são executadas num cluster já em execução do script não automaticamente fazer com que o cluster alterar para um estado falhado. Após a conclusão do script, deverá devolver o cluster para um Estado "em execução".

> [!IMPORTANT]
> Mesmo que o cluster tem um Estado 'em execução', o script falhou pode quebraram coisas. Por exemplo, um script foi possível eliminar ficheiros necessários para o cluster.
>
> Ações de scripts executam com privilégios de raiz, pelo que deve certificar-se de que compreende o que faz um script antes de aplicá-la para o cluster.

Ao aplicar um script para um cluster, o estado do cluster é alterado para de **em execução** para **aceites**, em seguida, **HDInsight configuração**e, finalmente, criar uma cópia de **executar** para scripts com êxito. O estado de script é registado no histórico de ações de script e pode utilizar estas informações para determinar se o script com êxito ou falha. Por exemplo, o `Get-AzureRmHDInsightScriptActionHistory` cmdlet do PowerShell pode ser utilizado para ver o estado de um script. Devolve informações semelhante para o seguinte texto:

    ScriptExecutionId : 635918532516474303
    StartTime         : 8/14/2017 7:40:55 PM
    EndTime           : 8/14/2017 7:41:05 PM
    Status            : Succeeded

> [!NOTE]
> Se tiver alterado a palavra-passe de utilizador (administrador) do cluster depois do cluster foi criado, o script ações executaram este cluster poderá falhar. Se tiver quaisquer ações de script persistentes que nós de trabalho de destino, estes scripts podem falhar ao dimensionar o cluster.

## <a name="example-script-action-scripts"></a>Scripts de ação de Script de exemplo

Scripts de ação de script podem ser utilizadas através de utilitários do seguintes:

* Portal do Azure
* Azure PowerShell
* CLI do Azure
* SDK de .NET do HDInsight

O HDInsight fornece scripts para instalar os seguintes componentes nos clusters do HDInsight:

| Nome | Script |
| --- | --- |
| **Adicionar uma conta de armazenamento do Azure** |https://hdiconfigactions.blob.Core.Windows.NET/linuxaddstorageaccountv01/Add-Storage-Account-v01.SH. Consulte [adicionar armazenamento adicional para um cluster do HDInsight](hdinsight-hadoop-add-storage.md). |
| **Instalar a Hue** |https://hdiconfigactions.blob.Core.Windows.NET/linuxhueconfigactionv02/Install-hue-uber-v02.SH. Consulte [instalar e utilizar Hue no HDInsight clusters](hdinsight-hadoop-hue-linux.md). |
| **Instalar Presto** |https://RAW.githubusercontent.com/hdinsight/presto-hdinsight/Master/installpresto.SH. Consulte [instalar e utilizar Presto no HDInsight clusters](hdinsight-hadoop-install-presto.md). |
| **Instalar Solr** |https://hdiconfigactions.blob.Core.Windows.NET/linuxsolrconfigactionv01/solr-Installer-v01.SH. Consulte [instalar e utilizar Solr no HDInsight clusters](hdinsight-hadoop-solr-install-linux.md). |
| **Instalar Giraph** |https://hdiconfigactions.blob.Core.Windows.NET/linuxgiraphconfigactionv01/giraph-Installer-v01.SH. Consulte [instalar e utilizar Giraph no HDInsight clusters](hdinsight-hadoop-giraph-install-linux.md). |
| **Pré-carregar as bibliotecas Hive** |https://hdiconfigactions.blob.Core.Windows.NET/linuxsetupcustomhivelibsv01/Setup-customhivelibs-v01.SH. Consulte [bibliotecas adicionar Hive nos clusters do HDInsight](hdinsight-hadoop-add-hive-libraries.md). |
| **Instalar ou atualizar o Mono** | https://hdiconfigactions.blob.Core.Windows.NET/Install-mono/Install-mono.bash. Consulte [instalação ou atualização Mono no HDInsight](hdinsight-hadoop-install-mono.md). |

## <a name="use-a-script-action-during-cluster-creation"></a>Utilizar uma ação de Script durante a criação do cluster

Esta secção fornece exemplos sobre as diferentes formas em que pode utilizar ações de script ao criar um cluster do HDInsight.

### <a name="use-a-script-action-during-cluster-creation-from-the-azure-portal"></a>Utilizar uma ação de Script durante a criação do cluster do portal do Azure

1. Começar a criar um cluster, conforme descrito em [clusters do Hadoop criar no HDInsight](hdinsight-hadoop-provision-linux-clusters.md). Pare quando chegar a __resumo do Cluster__ secção.

2. Do __resumo do Cluster__ secção, selecione o __editar__ hiperligação para __definições avançadas__.

    ![Definições avançadas de ligação](./media/hdinsight-hadoop-customize-cluster-linux/advanced-settings-link.png)

3. Do __definições avançadas__ secção, selecione __ações de Script__. Do __ações de Script__ secção, selecione __+ submeter novos__

    ![Submeter uma nova ação de script](./media/hdinsight-hadoop-customize-cluster-linux/add-script-action.png)

4. Utilize o __selecionar um script__ entrada para selecionar um script pré-cópia efetuado. Para utilizar um script personalizado, selecione __personalizado__ e, em seguida, forneça o __nome__ e __Bash script URI__ para o script.

    ![Adicionar um script no formulário do script selecione](./media/hdinsight-hadoop-customize-cluster-linux/select-script.png)

    A tabela seguinte descreve os elementos no formulário:

    | Propriedade | Valor |
    | --- | --- |
    | Selecione um script | Para utilizar o seu próprio script, selecione __personalizada__. Caso contrário, selecione um dos scripts fornecidos. |
    | Nome |Especifique um nome para a ação de script. |
    | Scripts de bash URI |Especifique o URI para o script que é invocado para personalizar o cluster. |
    | Trabalho/HEAD/Zookeeper |Especifique os nós (**Head**, **trabalho**, ou **ZooKeeper**) no qual é executado o script de personalização. |
    | Parâmetros |Especifique os parâmetros, se necessário, o script. |

    Utilize o __manter esta ação de script__ entrada para se certificar de que o script é aplicado durante operações de dimensionamento.

5. Selecione __criar__ ao guardar o script. Em seguida, pode utilizar __+ submeter novo__ para adicionar outro script.

    ![Várias ações de script](./media/hdinsight-hadoop-customize-cluster-linux/multiple-scripts.png)

    Quando tiver concluído a adição de scripts, utilize o __selecione__ botão e, em seguida, o __seguinte__ botão para regressar ao __resumo do Cluster__ secção.

3. Para criar o cluster, selecione __criar__ do __resumo do Cluster__ seleção.

### <a name="use-a-script-action-from-azure-resource-manager-templates"></a>Utilizar uma ação de Script a partir de modelos Azure Resource Manager

Os exemplos nesta secção demonstram como utilizar as ações de script com modelos Azure Resource Manager.

#### <a name="before-you-begin"></a>Antes de começar

* Para obter informações sobre como configurar uma estação de trabalho para executar os cmdlets do Powershell do HDInsight, consulte [instalar e configurar o Azure PowerShell](/powershell/azure/overview).
* Para obter instruções sobre como criar modelos, consulte [modelos Authoring Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).
* Se tiver não utilizado anteriormente Azure PowerShell com o Resource Manager, consulte o artigo [utilizar o Azure PowerShell com o Azure Resource Manager](../azure-resource-manager/powershell-azure-resource-manager.md).

#### <a name="create-clusters-using-script-action"></a>Criar clusters através da ação de Script

1. Copie o seguinte modelo para uma localização no seu computador. Este modelo instala Giraph em nós headnodes e de trabalho no cluster. Também pode verificar se o modelo JSON é válido. Cole o conteúdo do modelo [JSONLint](http://jsonlint.com/), uma ferramenta de validação JSON online.

            {
            "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
                "clusterLocation": {
                    "type": "string",
                    "defaultValue": "West US",
                    "allowedValues": [ "West US" ]
                },
                "clusterName": {
                    "type": "string"
                },
                "clusterUserName": {
                    "type": "string",
                    "defaultValue": "admin"
                },
                "clusterUserPassword": {
                    "type": "securestring"
                },
                "sshUserName": {
                    "type": "string",
                    "defaultValue": "username"
                },
                "sshPassword": {
                    "type": "securestring"
                },
                "clusterStorageAccountName": {
                    "type": "string"
                },
                "clusterStorageAccountResourceGroup": {
                    "type": "string"
                },
                "clusterStorageType": {
                    "type": "string",
                    "defaultValue": "Standard_LRS",
                    "allowedValues": [
                        "Standard_LRS",
                        "Standard_GRS",
                        "Standard_ZRS"
                    ]
                },
                "clusterStorageAccountContainer": {
                    "type": "string"
                },
                "clusterHeadNodeCount": {
                    "type": "int",
                    "defaultValue": 1
                },
                "clusterWorkerNodeCount": {
                    "type": "int",
                    "defaultValue": 2
                }
            },
            "variables": {
            },
            "resources": [
                {
                    "name": "[parameters('clusterStorageAccountName')]",
                    "type": "Microsoft.Storage/storageAccounts",
                    "location": "[parameters('clusterLocation')]",
                    "apiVersion": "2015-05-01-preview",
                    "dependsOn": [ ],
                    "tags": { },
                    "properties": {
                        "accountType": "[parameters('clusterStorageType')]"
                    }
                },
                {
                    "name": "[parameters('clusterName')]",
                    "type": "Microsoft.HDInsight/clusters",
                    "location": "[parameters('clusterLocation')]",
                    "apiVersion": "2015-03-01-preview",
                    "dependsOn": [
                        "[concat('Microsoft.Storage/storageAccounts/', parameters('clusterStorageAccountName'))]"
                    ],
                    "tags": { },
                    "properties": {
                        "clusterVersion": "3.2",
                        "osType": "Linux",
                        "clusterDefinition": {
                            "kind": "hadoop",
                            "configurations": {
                                "gateway": {
                                    "restAuthCredential.isEnabled": true,
                                    "restAuthCredential.username": "[parameters('clusterUserName')]",
                                    "restAuthCredential.password": "[parameters('clusterUserPassword')]"
                                }
                            }
                        },
                        "storageProfile": {
                            "storageaccounts": [
                                {
                                    "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.windows.net')]",
                                    "isDefault": true,
                                    "container": "[parameters('clusterStorageAccountContainer')]",
                                    "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), '2015-05-01-preview').key1]"
                                }
                            ]
                        },
                        "computeProfile": {
                            "roles": [
                                {
                                    "name": "headnode",
                                    "targetInstanceCount": "[parameters('clusterHeadNodeCount')]",
                                    "hardwareProfile": {
                                        "vmSize": "Large"
                                    },
                                    "osProfile": {
                                        "linuxOperatingSystemProfile": {
                                            "username": "[parameters('sshUserName')]",
                                            "password": "[parameters('sshPassword')]"
                                        }
                                    },
                                    "scriptActions": [
                                        {
                                            "name": "installGiraph",
                                            "uri": "https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh",
                                            "parameters": ""
                                        }
                                    ]
                                },
                                {
                                    "name": "workernode",
                                    "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
                                    "hardwareProfile": {
                                        "vmSize": "Large"
                                    },
                                    "osProfile": {
                                        "linuxOperatingSystemProfile": {
                                            "username": "[parameters('sshUserName')]",
                                            "password": "[parameters('sshPassword')]"
                                        }
                                    },
                                    "scriptActions": [
                                        {
                                            "name": "installR",
                                            "uri": "https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh",
                                            "parameters": ""
                                        }
                                    ]
                                }
                            ]
                        }
                    }
                }
            ],
            "outputs": {
                "cluster":{
                    "type" : "object",
                    "value" : "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
                }
            }
        }
2. Inicie o Azure PowerShell e inicie sessão na sua conta do Azure. Depois de fornecer as suas credenciais, o comando devolve informações sobre a sua conta.

        Add-AzureRmAccount

        Id                             Type       ...
        --                             ----
        someone@example.com            User       ...
3. Se tiver várias subscrições, forneça o ID de subscrição que pretende utilizar para a implementação.

        Select-AzureRmSubscription -SubscriptionID <YourSubscriptionId>

    > [!NOTE]
    > Pode utilizar `Get-AzureRmSubscription` para obter uma lista de todas as subscrições associadas à sua conta, que inclui o ID de subscrição para cada um deles.

4. Se não tiver um grupo de recursos existente, crie um grupo de recursos. Forneça o nome do grupo de recursos e localização que precisa para a sua solução. É devolvido um resumo do novo grupo de recursos.

        New-AzureRmResourceGroup -Name myresourcegroup -Location "West US"

        ResourceGroupName : myresourcegroup
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
                            Actions  NotActions
                            =======  ==========
                            *
        ResourceId        : /subscriptions/######/resourceGroups/ExampleResourceGroup

5. Para criar uma implementação para o grupo de recursos, execute o **New-AzureRmResourceGroupDeployment** de comandos e forneça os parâmetros necessários. Os parâmetros incluem os seguintes dados:

    * Um nome para a implementação
    * O nome do grupo de recursos
    * O caminho ou URL para o modelo que criou.

  Se o seu modelo necessita de quaisquer parâmetros, tem de passar esses parâmetros bem. Neste caso, a ação de script para instalar o R no cluster não requer quaisquer parâmetros.

        New-AzureRmResourceGroupDeployment -Name mydeployment -ResourceGroupName myresourcegroup -TemplateFile <PathOrLinkToTemplate>

    Lhe for pedido para fornecer valores para os parâmetros definidos no modelo.

1. Quando o grupo de recursos foi implementado, é apresentado um resumo da implementação.

          DeploymentName    : mydeployment
          ResourceGroupName : myresourcegroup
          ProvisioningState : Succeeded
          Timestamp         : 8/14/2017 7:00:27 PM
          Mode              : Incremental
          ...

2. Se a implementação falhar, pode utilizar os seguintes cmdlets para obter informações sobre as falhas.

        Get-AzureRmResourceGroupDeployment -ResourceGroupName myresourcegroup -ProvisioningState Failed

### <a name="use-a-script-action-during-cluster-creation-from-azure-powershell"></a>Utilizar uma ação de Script durante a criação do cluster a partir do Azure PowerShell

Nesta secção, vamos utilizar o [adicionar AzureRmHDInsightScriptAction](https://msdn.microsoft.com/library/mt603527.aspx) cmdlet invocar scripts ao utilizar a ação de Script para personalizar um cluster. Antes de continuar, certifique-se de que tem instalado e configurado o Azure PowerShell. Para obter informações sobre como configurar uma estação de trabalho para executar os cmdlets do PowerShell do HDInsight, consulte [instalar e configurar o Azure PowerShell](/powershell/azure/overview).

O script seguinte demonstra como aplicar uma ação de script ao criar um cluster com o PowerShell:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=5-90)]

Pode demorar vários minutos antes do cluster for criado.

### <a name="use-a-script-action-during-cluster-creation-from-the-hdinsight-net-sdk"></a>Utilizar uma ação de Script durante a criação do cluster do HDInsight .NET SDK

O SDK .NET do HDInsight fornece bibliotecas de cliente que torna mais fácil trabalhar com o HDInsight a partir de uma aplicação .NET. Para um exemplo de código, consulte [baseado em Linux criar clusters HDInsight utilizando o SDK .NET](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-script-action).

## <a name="apply-a-script-action-to-a-running-cluster"></a>Aplicar uma ação de Script para um cluster em execução

Nesta secção, saiba como aplicar ações de script para um cluster em execução.

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-portal"></a>Aplicar uma ação de Script para um cluster em execução a partir do portal do Azure

1. Do [portal do Azure](https://portal.azure.com), selecione o cluster do HDInsight.

2. A partir da descrição de geral do cluster de HDInsight, selecione o **ações de Script** mosaico.

    ![Mosaico de ações de script](./media/hdinsight-hadoop-customize-cluster-linux/scriptactionstile.png)

   > [!NOTE]
   > Também pode selecionar **todas as definições** e, em seguida, selecione **ações de Script** da secção de definições.

3. Na parte superior da secção ações de Script, selecione **submeter novo**.

    ![Adicionar um script para um cluster em execução](./media/hdinsight-hadoop-customize-cluster-linux/add-script-running-cluster.png)

4. Utilize o __selecionar um script__ entrada para selecionar um script pré-cópia efetuado. Para utilizar um script personalizado, selecione __personalizado__ e, em seguida, forneça o __nome__ e __Bash script URI__ para o script.

    ![Adicionar um script no formulário do script selecione](./media/hdinsight-hadoop-customize-cluster-linux/select-script.png)

    A tabela seguinte descreve os elementos no formulário:

    | Propriedade | Valor |
    | --- | --- |
    | Selecione um script | Para utilizar o seu próprio script, selecione __personalizado__. Caso contrário, selecione um script fornecidos. |
    | Nome |Especifique um nome para a ação de script. |
    | Scripts de bash URI |Especifique o URI para o script que é invocado para personalizar o cluster. |
    | Trabalho/HEAD/Zookeeper |Especifique os nós (**Head**, **trabalho**, ou **ZooKeeper**) no qual é executado o script de personalização. |
    | Parâmetros |Especifique os parâmetros, se necessário, o script. |

    Utilize o __manter esta ação de script__ entrada para se certificar de que o script é aplicado durante operações de dimensionamento.

5. Por último, utilize o **criar** botão para aplicar o script para o cluster.

### <a name="apply-a-script-action-to-a-running-cluster-from-azure-powershell"></a>Aplicar uma ação de Script para um cluster em execução a partir do Azure PowerShell

Antes de continuar, certifique-se de que tem instalado e configurado o Azure PowerShell. Para obter informações sobre como configurar uma estação de trabalho para executar os cmdlets do PowerShell do HDInsight, consulte [instalar e configurar o Azure PowerShell](/powershell/azure/overview).

O exemplo seguinte demonstra como aplicar uma ação de script para um cluster em execução:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=105-117)]

Uma vez concluída a operação, receberá informações semelhantes para o seguinte texto:

    OperationState  : Succeeded
    ErrorMessage    :
    Name            : Giraph
    Uri             : https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
    Parameters      :
    NodeTypes       : {HeadNode, WorkerNode}

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-cli"></a>Aplicar uma ação de Script para um cluster em execução a partir da CLI do Azure

Antes de continuar, certifique-se de que tem instalado e configurado a CLI do Azure. Para obter mais informações, consulte [instalar a CLI do Azure](../cli-install-nodejs.md).

[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

1. Para mudar para modo Azure Resource Manager, utilize o seguinte comando na linha de comandos:

        azure config mode arm

2. Utilize o seguinte para autenticar a sua subscrição do Azure.

        azure login

3. Utilize o seguinte comando para aplicar uma ação de script para um cluster em execução

        azure hdinsight script-action create <clustername> -g <resourcegroupname> -n <scriptname> -u <scriptURI> -t <nodetypes>

    Se omitir parâmetros para este comando, é-lhe pedida-los. Se o script especificar com `-u` aceite parâmetros, pode especificá-los utilizando o `-p` parâmetro.

    Os tipos de nó válido são `headnode`, `workernode`, e `zookeeper`. Se o script deve ser aplicado a vários tipos de nó, especificar os tipos separados por um ';'. Por exemplo, `-n headnode;workernode`.

    Para manter o script, adicione o `--persistOnSuccess`. Pode também manter o script posterior utilizando `azure hdinsight script-action persisted set`.

    Uma vez concluída a tarefa, recebe o resultado semelhante para o seguinte texto:

        info:    Executing command hdinsight script-action create
        + Executing Script Action on HDInsight cluster
        data:    Operation Info
        data:    ---------------
        data:    Operation status:
        data:    Operation ID:  b707b10e-e633-45c0-baa9-8aed3d348c13
        info:    hdinsight script-action create command OK

### <a name="apply-a-script-action-to-a-running-cluster-using-rest-api"></a>Aplicar uma ação de Script para um cluster em execução com a API REST

Consulte [executar ações de Script num cluster em execução](https://msdn.microsoft.com/library/azure/mt668441.aspx).

### <a name="apply-a-script-action-to-a-running-cluster-from-the-hdinsight-net-sdk"></a>Aplicar uma ação de Script para um cluster em execução a partir do SDK .NET do HDInsight

Para obter um exemplo utilizando o SDK .NET para aplicar scripts para um cluster, consulte [https://github.com/Azure-Samples/hdinsight-dotnet-script-action](https://github.com/Azure-Samples/hdinsight-dotnet-script-action).

## <a name="view-history-promote-and-demote-script-actions"></a>Ver histórico, promover e despromover ações de Script

### <a name="using-the-azure-portal"></a>Utilizar o portal do Azure

1. Do [portal do Azure](https://portal.azure.com), selecione o cluster do HDInsight.

2. A partir da descrição de geral do cluster de HDInsight, selecione o **ações de Script** mosaico.

    ![Mosaico de ações de script](./media/hdinsight-hadoop-customize-cluster-linux/scriptactionstile.png)

   > [!NOTE]
   > Também pode selecionar **todas as definições** e, em seguida, selecione **ações de Script** da secção de definições.

4. Um histórico de scripts para este cluster é apresentado na secção ações de Script. Estas informações incluem uma lista de scripts persistentes. Na captura de ecrã abaixo, pode ver que Solr script foi executada neste cluster. A captura de ecrã mostra quaisquer scripts persistentes.

    ![Secção de ações de script](./media/hdinsight-hadoop-customize-cluster-linux/script-action-history.png)

5. A seleção de um script do histórico mostra a secção de propriedades para este script. Na parte superior do ecrã, pode voltar a executar o script ou promovê-lo.

    ![Propriedades de ações de script](./media/hdinsight-hadoop-customize-cluster-linux/promote-script-actions.png)

6. Também pode utilizar o **...**  à direita de entradas na secção ações de Script para efetuar ações.

    ![Ações de script... utilização](./media/hdinsight-hadoop-customize-cluster-linux/deletepromoted.png)

### <a name="using-azure-powershell"></a>Utilizar o Azure PowerShell

| Utilize o seguinte... | Para... |
| --- | --- |
| Get-AzureRmHDInsightPersistedScriptAction |Obter informações sobre ações de script persistentes |
| Get-AzureRmHDInsightScriptActionHistory |Obter um histórico das ações de script aplicado para o cluster ou os detalhes para um script específico |
| Set-AzureRmHDInsightPersistedScriptAction |Promove uma ação de script ad hoc para uma ação de script persistentes |
| Remove-AzureRmHDInsightPersistedScriptAction |Despromove uma ação de script persistentes para uma ação ad hoc |

> [!IMPORTANT]
> Utilizar `Remove-AzureRmHDInsightPersistedScriptAction` não anular as ações executadas por um script. Este cmdlet apenas remove o sinalizador persistente.

O script de exemplo seguinte demonstra a utilizar os cmdlets para promover, despromover um script.

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=123-140)]

### <a name="using-the-azure-cli"></a>Com a CLI do Azure

| Utilize o seguinte... | Para... |
| --- | --- |
| `azure hdinsight script-action persisted list <clustername>` |Obter uma lista de ações de script persistentes |
| `azure hdinsight script-action persisted show <clustername> <scriptname>` |Obter informações sobre uma ação de script persistentes específico |
| `azure hdinsight script-action history list <clustername>` |Obter um histórico das ações de script aplicada ao cluster |
| `azure hdinsight script-action history show <clustername> <scriptname>` |Obter informações sobre uma ação de script específico |
| `azure hdinsight script action persisted set <clustername> <scriptexecutionid>` |Promove uma ação de script ad hoc para uma ação de script persistentes |
| `azure hdinsight script-action persisted delete <clustername> <scriptname>` |Despromove uma ação de script persistentes para uma ação ad hoc |

> [!IMPORTANT]
> Utilizar `azure hdinsight script-action persisted delete` não anular as ações executadas por um script. Este cmdlet apenas remove o sinalizador persistente.

### <a name="using-the-hdinsight-net-sdk"></a>Utilizando o SDK .NET do HDInsight

Para obter um exemplo utilizando o SDK .NET para obter o histórico de script a partir de um cluster, promover ou despromover scripts, consulte [https://github.com/Azure-Samples/hdinsight-dotnet-script-action](https://github.com/Azure-Samples/hdinsight-dotnet-script-action).

> [!NOTE]
> Este exemplo também demonstra como instalar uma aplicação do HDInsight utilizando o SDK .NET.

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>Suporte para o software de open source utilizados nos clusters do HDInsight

O serviço do Microsoft Azure HDInsight utiliza um ecossistema de tecnologias de open source formado em torno do Hadoop. Microsoft Azure fornece um nível geral de suporte para tecnologias de open source. Para obter mais informações, consulte o **suporte âmbito** secção o [site FAQ de suporte do Azure](https://azure.microsoft.com/support/faq/). O serviço de HDInsight fornece um nível adicional de suporte para componentes incorporados.

Existem dois tipos de componentes de open source que estão disponíveis no serviço do HDInsight:

* **Componentes incorporados** -estes componentes são previamente instalados nos clusters do HDInsight e fornecer a funcionalidade principal do cluster. Por exemplo, YARN ResourceManager, o idioma de consulta do Hive (HiveQL) e a biblioteca de Mahout pertencem a esta categoria. Uma lista completa dos componentes de cluster está disponível no [Novidades nas versões de cluster do Hadoop fornecidas pelo HDInsight](hdinsight-component-versioning.md).
* **Componentes personalizados** -, como um utilizador do cluster, pode instalar ou utilizar na sua carga de trabalho qualquer componente disponível na Comunidade ou criado por si.

> [!WARNING]
> Componentes fornecidos com o cluster do HDInsight são totalmente suportados. Microsoft Support ajuda a isolar e resolver problemas relacionados com estes componentes.
>
> Componentes personalizados recebem suporte comercialmente razoável para ajudar a resolver o problema. Suporte da Microsoft poderá resolver o problema ou poderão pedir-lhe que participem canais disponíveis para as tecnologias de open source para onde se encontra profundo conhecimentos para que a tecnologia. Por exemplo, existem vários sites de Comunidade que podem ser utilizadas, como: [fórum do MSDN para o HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). Também projetos do Apache tem sites de projeto no [http://apache.org](http://apache.org), por exemplo: [Hadoop](http://hadoop.apache.org/).

O serviço de HDInsight fornece várias formas de utilizar componentes personalizados. Se aplica o mesmo nível de suporte, independentemente da forma como um componente utilizado ou não está instalado no cluster. A lista seguinte descreve as mais comuns formas que componentes personalizados podem ser utilizados nos clusters do HDInsight:

1. Submissão de tarefa - Hadoop ou outros tipos de tarefas que executarem ou utilizam componentes personalizados pode ser submetido para o cluster.

2. Personalização de cluster - durante a criação do cluster, pode especificar definições adicionais e componentes personalizados que estão instalados em nós do cluster.

3. Amostras - para componentes personalizados populares, a Microsoft e outras pessoas podem fornecer exemplos de como estes componentes podem ser utilizados nos clusters do HDInsight. Estes exemplos são fornecidos sem suporte.

## <a name="troubleshooting"></a>Resolução de problemas

Pode utilizar a IU da web do Ambari para ver informações registadas pelas ações de script. Se o script falhar durante a criação do cluster, os registos também estão disponíveis na conta do storage predefinida associada com o cluster. Esta secção fornece informações sobre como obter os registos utilizando ambas estas opções.

### <a name="using-the-ambari-web-ui"></a>Utilizando a IU da Web do Ambari

1. No seu browser, navegue para https://CLUSTERNAME.azurehdinsight.net. Substitua CLUSTERNAME com o nome do cluster do HDInsight.

    Quando lhe for pedido, introduza o nome da conta de administrador (administrador) e a palavra-passe para o cluster. Poderá ter de reintroduzir as credenciais de administrador num formulário web.

2. Na barra na parte superior da página, selecione o **ops** entrada. É apresentada uma lista de atuais e anteriores operações executadas no cluster através do Ambari.

    ![Barra de IU do Ambari web com ops selecionado](./media/hdinsight-hadoop-customize-cluster-linux/ambari-nav.png)

3. Localize as entradas que tenham **executar\_customscriptaction** no **operações** coluna. Estas entradas são criadas quando executar as ações de Script.

    ![Captura de ecrã de operações](./media/hdinsight-hadoop-customize-cluster-linux/ambariscriptaction.png)

    Para ver o resultado STDOUT e STDERR, selecione a entrada de run\customscriptaction e desagregar através de ligações. Este resultado é gerado quando o script é executado e pode conter informações úteis.

### <a name="access-logs-from-the-default-storage-account"></a>Registos de acesso da conta do storage predefinida

Se a criação do cluster falhar devido a um erro de ação de script, os registos podem ser acedidos da conta de armazenamento do cluster.

* Os registos de armazenamento estão disponíveis em `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE`.

    ![Captura de ecrã de operações](./media/hdinsight-hadoop-customize-cluster-linux/script_action_logs_in_storage.png)

    Neste diretório, os registos são organizados em separado para headnode, workernode e nós de zookeeper. Alguns exemplos incluem:

    * **Headnode** - `<uniqueidentifier>AmbariDb-hn0-<generated_value>.cloudapp.net`

    * **Nó de trabalho** - `<uniqueidentifier>AmbariDb-wn0-<generated_value>.cloudapp.net`

    * **Nós de zookeeper** - `<uniqueidentifier>AmbariDb-zk0-<generated_value>.cloudapp.net`

* Todos os stdout e stderr do anfitrião correspondente é carregado para a conta de armazenamento. Há um **resultado -\*. txt** e **erros -\*. txt** para cada ação de script. O ficheiro de saída *.txt contém informações sobre o URI de script que foi executado no anfitrião. Por exemplo

        'Start downloading script locally: ', u'https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh'

* É possível criar repetidamente um cluster de ação de script com o mesmo nome. Esse caso, pode distinguir os registos relevantes com base no nome da pasta data. Por exemplo, a estrutura da pasta para um cluster (mycluster) criado em datas diferentes parece ser semelhante para as seguintes entradas de registo:

    `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-04` `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-05`

* Se criar um cluster de ação de script com o mesmo nome no mesmo dia, pode utilizar o prefixo exclusivo para identificar os ficheiros de registo relevantes.

* Se criar um cluster quase 12:00:00 (meia-noite), é possível que os ficheiros de registo span em dois dias. Nestes casos, pode ver duas pastas data diferente para o mesmo cluster.

* Carregar ficheiros de registo para o contentor predefinido poderá demorar até 5 minutos, especialmente para grandes clusters. Por isso, se pretender aceder os registos, não deverá imediatamente eliminar o cluster se falhar de uma ação de script.

### <a name="ambari-watchdog"></a>Watchdog do Ambari

> [!WARNING]
> Não altere a palavra-passe para watchdog do Ambari (hdinsightwatchdog) no cluster do HDInsight baseado em Linux. Alterar a palavra-passe para esta conta interrompe a capacidade para executar novas ações de script no cluster do HDInsight.

### <a name="cant-import-name-blobservice"></a>Não é possível importar o nome BlobService

__Sintomas__: A falha de ação de script. Texto semelhante ao seguinte erro é apresentado quando o utilizador vê a operação no Ambari:

```
Traceback (most recent call list):
  File "/var/lib/ambari-agent/cache/custom_actions/scripts/run_customscriptaction.py", line 21, in <module>
    from azure.storage.blob import BlobService
ImportError: cannot import name BlobService
```

__Causa__: Este erro ocorre se atualizar o cliente do Storage do Python do Azure que está incluído no cluster do HDInsight. HDInsight espera que o cliente de armazenamento do Azure 0.20.0.

__Resolução__: para resolver este erro, manualmente ligar a cada nó de cluster utilizando `ssh` e utilize o seguinte comando para reinstalar a versão de cliente de armazenamento correta:

```
sudo pip install azure-storage==0.20.0
```

Para obter informações sobre a ligação ao cluster com SSH, consulte [utilizar o SSH com o HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

### <a name="history-doesnt-show-scripts-used-during-cluster-creation"></a>Histórico não mostra scripts utilizados durante a criação do cluster

Se o cluster foi criado antes de 15 de Março de 2016, não poderá ver uma entrada no histórico de ação de Script. Se redimensionar o cluster após 15 de Março de 2016, os scripts a utilizar durante a criação do cluster são apresentados no histórico de como estas são aplicadas a novos nós no cluster como parte da operação de redimensionamento.

Existem duas exceções:

* Se o cluster foi criado antes de 1 de Setembro de 2015. Esta data é quando foram introduzidas ações de Script. Qualquer cluster criado antes desta data não foi ter utilizar ações de Script para a criação do cluster.

* Se utilizar várias ações de Script durante a criação do cluster e utilizado o mesmo nome de vários scripts, ou o mesmo nome, mesmo URI, mas parâmetros diferentes de vários scripts. Nestes casos, receberá o seguinte erro:

    Não existe nenhum script de nova ações podem ser executadas neste cluster devido a nomes de script em conflito em scripts existentes. Nomes do script fornecidos no cluster criar tem de ser exclusivos. Os scripts existentes são executadas no redimensionamento.

## <a name="next-steps"></a>Passos seguintes

* [Desenvolver scripts de ação de Script para o HDInsight](hdinsight-hadoop-script-actions-linux.md)
* [Instalar e utilizar Solr nos clusters do HDInsight](hdinsight-hadoop-solr-install-linux.md)
* [Instalar e utilizar Giraph nos clusters do HDInsight](hdinsight-hadoop-giraph-install-linux.md)
* [Adicionar armazenamento adicional para um cluster do HDInsight](hdinsight-hadoop-add-storage.md)

[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-linux/HDI-Cluster-state.png "Fases durante a criação do cluster"
