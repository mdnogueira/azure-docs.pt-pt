---
title: Adicionar contas de armazenamento do Azure adicionais ao HDInsight | Microsoft Docs
description: Saiba como adicionar as contas do storage do Azure adicionais para um cluster do HDInsight existente.
services: hdinsight
documentationCenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/24/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 68229487f45c3246500875303105b0ebcc8079cb
ms.sourcegitcommit: 4d90200f49cc60d63015bada2f3fc4445b34d4cb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2017
---
# <a name="add-additional-storage-accounts-to-hdinsight"></a>Adicionar contas de armazenamento adicional para o HDInsight

Saiba como utilizar as ações de script para adicionar contas de armazenamento do Azure adicionais para o HDInsight. Os passos neste documento adicionar uma conta de armazenamento num cluster do HDInsight baseado em Linux existente.

> [!IMPORTANT]
> As informações neste documento são sobre como adicionar armazenamento adicional para um cluster depois de terem sido criadas. Para obter informações sobre como adicionar contas de armazenamento durante a criação do cluster, consulte [configurar clusters no HDInsight com o Hadoop, Spark, Kafka e muito mais](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="how-it-works"></a>Como funciona

Este script utiliza os seguintes parâmetros:

* __Nome da conta de armazenamento do Azure__: O nome da conta do storage para adicionar ao cluster do HDInsight. Depois de executar o script, HDInsight pode ler e escrever dados armazenados nesta conta de armazenamento.

* __Chave de conta de armazenamento do Azure__: uma chave que concede acesso à conta de armazenamento.

* __-p__ (opcional): se for especificado, a chave não está encriptada e é armazenada no ficheiro Core-site.XML como texto simples.

Durante o processamento, o script executa as seguintes ações:

* Se a conta de armazenamento já existe na configuração Core-site.XML para o cluster, o script sair e não existem mais ações são efetuadas.

* Verifica se a conta de armazenamento existe e que pode ser acedida utilizando a chave.

* Encripta a chave utilizando as credenciais de cluster.

* Adiciona a conta de armazenamento para o ficheiro Core-site.XML.

* Para e reinicia os serviços do Oozie, YARN, MapReduce2 e HDFS. Parar e iniciar estes serviços permitem-lhe utilizar a nova conta de armazenamento.

> [!WARNING]
> Não é suportada a utilização de uma conta de armazenamento numa localização diferente que o cluster do HDInsight.

## <a name="the-script"></a>O script

__Localização do script__: [https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh)

__Requisitos__:

* O script tem de ser aplicado a __nós principais__.

## <a name="to-use-the-script"></a>Para utilizar o script

Este script pode ser utilizado a partir do portal do Azure, Azure PowerShell ou a CLI do Azure 1.0. Para obter mais informações, consulte o [clusters do HDInsight baseado em Linux personalizar através da ação de script](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster) documento.

> [!IMPORTANT]
> Quando utilizar os passos fornecidos no documento de personalização, utilize as seguintes informações para aplicar este script:
>
> * Substitua qualquer ação de script de exemplo URI com o URI para este script (https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh).
> * Substitua quaisquer parâmetros de exemplo com o nome da conta de armazenamento do Azure e a chave da conta do storage para ser adicionado ao cluster. Se utilizar o portal do Azure, estes parâmetros têm de ser separados por um espaço.
> * Não é necessário marcar este script como __persistentes__, como diretamente atualizará a configuração do Ambari para o cluster.

## <a name="known-issues"></a>Problemas conhecidos

### <a name="storage-accounts-not-displayed-in-azure-portal-or-tools"></a>Não é apresentadas no portal do Azure ou ferramentas de contas de armazenamento

Ao visualizar o cluster do HDInsight no portal do Azure, selecionando o __contas do Storage__ entrada em __propriedades__ não apresenta as contas do storage adicionadas através desta ação de script. Azure PowerShell e a CLI do Azure não apresentam a conta de armazenamento adicional ou.

As informações de armazenamento não estão apresentadas porque o script apenas modifica a configuração de Core-site.XML para o cluster. Estas informações não são utilizadas ao obter as informações de cluster através de APIs de gestão do Azure.

Para ver informações de conta de armazenamento adicionadas ao cluster com este script, utilize a API de REST do Ambari. Utilize os seguintes comandos para obter estas informações para o cluster:

```PowerShell
$creds = Get-Credential -UserName "admin" -Message "Enter the cluster login credentials"
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.configurations.properties."fs.azure.account.key.$storageAccountName.blob.core.windows.net"
```

> [!NOTE]
> Definir `$clusterName` com o nome do cluster do HDInsight. Definir `$storageAccountName` para o nome da conta de armazenamento. Quando lhe for pedido, introduza o início de sessão do cluster (admin) e a palavra-passe.

```Bash
curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.azure.account.key.$STORAGEACCOUNTNAME.blob.core.windows.net"] | select(. != null)'
```

> [!NOTE]
> Definir `$PASSWORD` para a palavra-passe da conta de início de sessão (administrador) do cluster. Definir `$CLUSTERNAME` com o nome do cluster do HDInsight. Definir `$STORAGEACCOUNTNAME` para o nome da conta de armazenamento.
>
> Este exemplo utiliza [curl (http://curl.haxx.se/)](http://curl.haxx.se/) e [jq (https://stedolan.github.io/jq/)](https://stedolan.github.io/jq/) para obter e analisar dados JSON.

Quando utilizar este comando, substitua __CLUSTERNAME__ com o nome do cluster do HDInsight. Substitua __palavra-passe__ com a palavra-passe de início de sessão HTTP para o cluster. Substitua __STORAGEACCOUNT__ com o nome da conta do storage adicionado através da ação de script. As informações devolvidas a partir deste comando parece ser semelhantes para o seguinte texto:

    "MIIB+gYJKoZIhvcNAQcDoIIB6zCCAecCAQAxggFaMIIBVgIBADA+MCoxKDAmBgNVBAMTH2RiZW5jcnlwdGlvbi5henVyZWhkaW5zaWdodC5uZXQCEA6GDZMW1oiESKFHFOOEgjcwDQYJKoZIhvcNAQEBBQAEggEATIuO8MJ45KEQAYBQld7WaRkJOWqaCLwFub9zNpscrquA2f3o0emy9Vr6vu5cD3GTt7PmaAF0pvssbKVMf/Z8yRpHmeezSco2y7e9Qd7xJKRLYtRHm80fsjiBHSW9CYkQwxHaOqdR7DBhZyhnj+DHhODsIO2FGM8MxWk4fgBRVO6CZ5eTmZ6KVR8wYbFLi8YZXb7GkUEeSn2PsjrKGiQjtpXw1RAyanCagr5vlg8CicZg1HuhCHWf/RYFWM3EBbVz+uFZPR3BqTgbvBhWYXRJaISwssvxotppe0ikevnEgaBYrflB2P+PVrwPTZ7f36HQcn4ifY1WRJQ4qRaUxdYEfzCBgwYJKoZIhvcNAQcBMBQGCCqGSIb3DQMHBAhRdscgRV3wmYBg3j/T1aEnO3wLWCRpgZa16MWqmfQPuansKHjLwbZjTpeirqUAQpZVyXdK/w4gKlK+t1heNsNo1Wwqu+Y47bSAX1k9Ud7+Ed2oETDI7724IJ213YeGxvu4Ngcf2eHW+FRK"

Este texto é um exemplo de uma chave encriptada, o que é utilizado para aceder à conta de armazenamento.

### <a name="unable-to-access-storage-after-changing-key"></a>Não é possível aceder ao armazenamento depois de alterar a chave

Se alterar a chave para uma conta de armazenamento, o HDInsight já não pode aceder a conta de armazenamento. Para o cluster, o HDInsight utiliza uma cópia em cache da chave no Core-site.XML. Esta cópia em cache deve ser atualizada para corresponderem à nova chave.

Executar novamente a ação de script __não__ atualizar a chave, como o script verifica se já existe uma entrada para a conta de armazenamento. Se já existir uma entrada, faça quaisquer alterações.

Para contornar este problema, tem de remover a entrada existente para a conta de armazenamento. Utilize os seguintes passos para remover a entrada existente:

1. Num browser, abra a IU da Web do Ambari para o cluster do HDInsight. O URI é https://CLUSTERNAME.azurehdinsight.net. Substitua __CLUSTERNAME__ pelo nome do cluster.

    Quando lhe for pedido, introduza o utilizador de início de sessão HTTP e a palavra-passe para o cluster.

2. Na lista de serviços no lado esquerdo da página, selecione __HDFS__. Em seguida, selecione o __folhas__ separador no centro da página.

3. No __filtro...__  campo, introduza um valor de __fs.azure.account__. Esta ação devolve entradas para as contas de armazenamento adicional que foram adicionadas ao cluster. Existem dois tipos de entradas; __keyprovider__ e __chave__. Ambos contêm o nome da conta de armazenamento como parte do nome da chave.

    Seguem-se entradas de exemplo para uma conta de armazenamento com o nome __mystorage__:

        fs.azure.account.keyprovider.mystorage.blob.core.windows.net
        fs.azure.account.key.mystorage.blob.core.windows.net

4. Depois de ter identificado as chaves para a conta de armazenamento tem de remover, utilize a vermelho '-' ícone para a direita da entrada de eliminá-la. Em seguida, utilize o __guardar__ botão para guardar as alterações.

5. Depois das alterações foram guardadas, utilize a ação de script para adicionar a conta de armazenamento e o novo valor de chave para o cluster.

### <a name="poor-performance"></a>Fraco desempenho

Se a conta de armazenamento numa região diferente que o cluster do HDInsight, pode deparar-se fraco desempenho. Aceder aos dados numa região diferente envia o tráfego de rede fora do Datacenter do Azure regional e através da internet pública, que pode dar origem a latência.

> [!WARNING]
> Não é suportada a utilização de uma conta de armazenamento numa região diferente que o cluster do HDInsight.

### <a name="additional-charges"></a>Custos adicionais

Se a conta de armazenamento numa região diferente que o cluster do HDInsight, poderá reparar encargos associados à saída adicionais na sua faturação do Azure. Um custo de saída é aplicado quando deixa de dados de um centro de dados regionais. Este custo é aplicado, mesmo que o tráfego é destinado a outro Datacenter do Azure numa região diferente.

> [!WARNING]
> Não é suportada a utilização de uma conta de armazenamento numa região diferente que o cluster do HDInsight.

## <a name="next-steps"></a>Passos seguintes

Aprendeu como adicionar contas de armazenamento adicional para um cluster do HDInsight existente. Para obter mais informações sobre ações de script, consulte [clusters do HDInsight baseado em Linux personalizar através da ação de script](hdinsight-hadoop-customize-cluster-linux.md)
