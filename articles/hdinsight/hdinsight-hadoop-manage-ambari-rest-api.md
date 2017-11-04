---
title: Monitorizar e gerir Hadoop com a API de REST do Ambari - Azure HDInsight | Microsoft Docs
description: "Saiba como utilizar Ambari para monitorizar e gerir clusters do Hadoop no Azure HDInsight. Neste documento, irá aprender a utilizar a API de REST do Ambari incluído com clusters do HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 2400530f-92b3-47b7-aa48-875f028765ff
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/07/2017
ms.author: larryfr
ms.openlocfilehash: 7960d83bce22d4f671d61e9aaf55561bc24308f8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-hdinsight-clusters-by-using-the-ambari-rest-api"></a>Gerir clusters do HDInsight utilizando a API de REST do Ambari

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Saiba como utilizar a API de REST do Ambari para gerir e monitorizar clusters do Hadoop no Azure HDInsight.

Apache Ambari simplifica a gestão e monitorização de um cluster de Hadoop, fornecendo uma fácil de utilizar a IU da web e REST API. Ambari está incluído nos clusters do HDInsight que utilizam o sistema operativo Linux. Pode utilizar Ambari para monitorizar o cluster e efetuar alterações de configuração.

## <a id="whatis"></a>O que é o Ambari

[Apache Ambari](http://ambari.apache.org) fornece web IU que pode ser utilizada para aprovisionar, gerir e monitorizar clusters do Hadoop. Os programadores podem integrar estas capacidades nas suas aplicações utilizando o [APIs REST do Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

Ambari é fornecido por predefinição com clusters do HDInsight baseado em Linux.

## <a name="how-to-use-the-ambari-rest-api"></a>Como utilizar a API de REST do Ambari

> [!IMPORTANT]
> As informações e exemplos neste documento exigem um cluster do HDInsight que utiliza o sistema operativo Linux. Para obter mais informações, consulte [introdução ao HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md).

Os exemplos neste documento são fornecidos para a shell de Bourne (bash) e o PowerShell. Bash exemplos foram testados com GNU bash 4.3.11, mas devem funcionar com outros shells de Unix. Os exemplos do PowerShell foram testados com PowerShell 5.0, mas devem funcionar com o PowerShell 3.0 ou superior.

Se utilizar o __Bourne shell__ (Bash), tem de ter o seguinte instalado:

* [cURL](http://curl.haxx.se/): cURL é um utilitário que pode ser utilizado para trabalhar com as APIs REST na linha de comandos. Neste documento, é utilizado para comunicar com a API de REST do Ambari.

Se utilizar Bash ou PowerShell, também tem de ter [jq](https://stedolan.github.io/jq/) instalado. Jq é um utilitário para trabalhar com documentos JSON. É utilizado em **todos os** os exemplos de Bash, e **um** dos exemplos do PowerShell.

### <a name="base-uri-for-ambari-rest-api"></a>Base de URI para Ambari Rest API

O URI base para a API de REST do Ambari no HDInsight é https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME, onde **CLUSTERNAME** é o nome do cluster.

> [!IMPORTANT]
> Enquanto o nome do cluster na parte de nome (FQDN) de domínio completamente qualificado do URI (CLUSTERNAME.azurehdinsight.net) é sensível, outras ocorrências no URI de diferenciam maiúsculas de minúsculas. Por exemplo, se o cluster é designado `MyCluster`, seguem-se URIs válido:
> 
> `https://mycluster.azurehdinsight.net/api/v1/clusters/MyCluster`
>
> `https://MyCluster.azurehdinsight.net/api/v1/clusters/MyCluster`
> 
> O URI seguinte devolver um erro, porque a segunda ocorrência do nome não está a maiúsculas e minúsculas corretas.
> 
> `https://mycluster.azurehdinsight.net/api/v1/clusters/mycluster`
>
> `https://MyCluster.azurehdinsight.net/api/v1/clusters/mycluster`

### <a name="authentication"></a>Autenticação

A ligação a Ambari no HDInsight requer HTTPS. Utilize o nome de conta de administrador (a predefinição é **admin**) e palavra-passe que forneceu durante a criação do cluster.

## <a name="examples-authentication-and-parsing-json"></a>Exemplos: Autenticação e análise JSON

Os exemplos seguintes demonstram como efetuar um pedido GET relativamente a API de REST do Ambari base:

```bash
curl -u admin:$PASSWORD -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME"
```

> [!IMPORTANT]
> Os exemplos de Bash neste documento efetuar os seguintes pressupostos:
>
> * O nome de início de sessão para o cluster é o valor predefinido de `admin`.
> * `$PASSWORD`contém a palavra-passe para o comando de início de sessão do HDInsight. Pode definir este valor utilizando `PASSWORD='mypassword'`.
> * `$CLUSTERNAME`contém o nome do cluster. Pode definir este valor utilizando`set CLUSTERNAME='clustername'`

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
    -Credential $creds
$resp.Content
```

> [!IMPORTANT]
> Os exemplos do PowerShell neste documento efetuar os seguintes pressupostos:
>
> * `$creds`é um objeto de credencial que contém o início de sessão de administrador e a palavra-passe para o cluster. Pode definir este valor utilizando `$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"` e fornecer as credenciais quando lhe for pedido.
> * `$clusterName`é uma cadeia que contém o nome do cluster. Pode definir este valor utilizando `$clusterName="clustername"`.

Ambos os exemplos de devolver um documento JSON que começa com informações semelhantes ao seguinte exemplo:

```json
{
"href" : "http://10.0.0.10:8080/api/v1/clusters/CLUSTERNAME",
"Clusters" : {
    "cluster_id" : 2,
    "cluster_name" : "CLUSTERNAME",
    "health_report" : {
    "Host/stale_config" : 0,
    "Host/maintenance_state" : 0,
    "Host/host_state/HEALTHY" : 7,
    "Host/host_state/UNHEALTHY" : 0,
    "Host/host_state/HEARTBEAT_LOST" : 0,
    "Host/host_state/INIT" : 0,
    "Host/host_status/HEALTHY" : 7,
    "Host/host_status/UNHEALTHY" : 0,
    "Host/host_status/UNKNOWN" : 0,
    "Host/host_status/ALERT" : 0
    ...
```

### <a name="parsing-json-data"></a>Analisar dados JSON

O exemplo seguinte utiliza `jq` para analisar o documento de resposta JSON e apresentar apenas o `health_report` informações dos resultados.

```bash
curl -u admin:$PASSWORD -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME" \
| jq '.Clusters.health_report'
```

PowerShell 3.0 e superior fornece o `ConvertFrom-Json` cmdlet, que converte o documento JSON um objeto que seja mais fácil trabalhar com a partir do PowerShell. O exemplo seguinte utiliza `ConvertFrom-Json` para apresentar apenas o `health_report` informações dos resultados.

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
$respObj.Clusters.health_report
```

> [!NOTE]
> Enquanto a maioria dos exemplos de utilização deste documento `ConvertFrom-Json` para apresentar os elementos do documento resposta, a [atualização Ambari configuração](#example-update-ambari-configuration) exemplo utiliza jq. Jq é utilizada neste exemplo, para construir um novo modelo do documento de resposta JSON.

Para uma referência completa da REST API, consulte [V1 de referência de API do Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

## <a name="example-get-the-fqdn-of-cluster-nodes"></a>Exemplo: Obter o FQDN de nós de cluster

Ao trabalhar com o HDInsight, poderá ter de saber o nome de domínio completamente qualificado (FQDN) de um nó de cluster. Pode obter facilmente o FQDN para os vários nós no cluster utilizando os exemplos seguintes:

* **Todos os nós**

    ```bash
    curl -u admin:$PASSWORD -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/hosts" \
    | jq '.items[].Hosts.host_name'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.Hosts.host_name
    ```

* **Nós de cabeçalho**

    ```bash
    curl -u admin:$PASSWORD -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/HDFS/components/NAMENODE" \
    | jq '.host_components[].HostRoles.host_name'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/NAMENODE" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.host_components.HostRoles.host_name
    ```

* **Nós de trabalho**

    ```bash
    curl -u admin:PASSWORD -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/DATANODE" \
    | jq '.host_components[].HostRoles.host_name'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/DATANODE" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.host_components.HostRoles.host_name
    ```

* **Nós de zookeeper**

    ```bash
    curl -u admin:PASSWORD -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" \
    | jq '.host_components[].HostRoles.host_name'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.host_components.HostRoles.host_name
    ```

## <a name="example-get-the-internal-ip-address-of-cluster-nodes"></a>Exemplo: Obter o endereço IP interno de nós de cluster

> [!IMPORTANT]
> Os endereços IP devolvidos pelos exemplos nesta secção não estão diretamente acessíveis através da internet. Só estão acessíveis dentro da rede Virtual do Azure que contém o cluster do HDInsight.
>
> Para obter mais informações sobre como trabalhar com o HDInsight e redes virtuais, consulte [capacidades de expandir HDInsight utilizando uma rede Virtual do Azure personalizado](hdinsight-extend-hadoop-virtual-network.md).

Para localizar o endereço IP, tem de saber o nome de domínio completamente qualificado (FQDN) interna de nós do cluster. Assim que tiver o FQDN, em seguida, pode obter o endereço IP do anfitrião. Os exemplos seguintes primeiro consultar Ambari para o FQDN de todos os nós de anfitrião, em seguida, consultar Ambari para o endereço IP de cada anfitrião.

```bash
for HOSTNAME in $(curl -u admin:$PASSWORD -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/hosts" | jq -r '.items[].Hosts.host_name')
do
    IP=$(curl -u admin:$PASSWORD -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/hosts/$HOSTNAME" | jq -r '.Hosts.ip')
  echo "$HOSTNAME <--> $IP"
done
```

```powershell
$uri = "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts"
$resp = Invoke-WebRequest -Uri $uri -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
foreach($item in $respObj.items) {
    $hostName = [string]$item.Hosts.host_name
    $hostInfoResp = Invoke-WebRequest -Uri "$uri/$hostName" `
        -Credential $creds
    $hostInfoObj = ConvertFrom-Json $hostInfoResp 
    $hostIp = $hostInfoObj.Hosts.ip
    "$hostName <--> $hostIp"
}
```

## <a name="example-get-the-default-storage"></a>Exemplo: Obter o armazenamento de predefinido

Quando cria um cluster do HDInsight, tem de utilizar uma conta de armazenamento do Azure ou a Data Lake Store como armazenamento de predefinido para o cluster. Pode utilizar Ambari para obter estas informações depois do cluster foi criado. Por exemplo, se pretender leitura/escrita dados para o contentor fora do HDInsight.

Os exemplos seguintes obter a configuração de armazenamento predefinida do cluster:

```bash
curl -u admin:$PASSWORD -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
| jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.configurations.properties.'fs.defaultFS'
```

> [!IMPORTANT]
> Estes exemplos devolvem a primeira configuração aplicada ao servidor (`service_config_version=1`) que contém esta informação. Se é possível obter um valor que tenha sido modificado após a criação do cluster, terá de lista as versões de configuração e obter um mais recente.

O valor de retorno é semelhante a uma das seguintes exemplos:

* `wasb://CONTAINER@ACCOUNTNAME.blob.core.windows.net`-Este valor indica se o cluster está a utilizar uma conta de armazenamento do Azure para armazenamento de predefinido. O `ACCOUNTNAME` valor é o nome da conta de armazenamento. O `CONTAINER` parte é o nome do contentor do blob na conta de armazenamento. O contentor é a raiz de armazenamento HDFS compatível para o cluster.

* `adl://home`-Este valor indica se o cluster está a utilizar um Azure Data Lake Store para armazenamento de predefinido.

    Para localizar o nome de conta do Data Lake Store, utilize os seguintes exemplos:

    ```bash
    curl -u admin:$PASSWORD -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
    | jq '.items[].configurations[].properties["dfs.adls.home.hostname"] | select(. != null)'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.configurations.properties.'dfs.adls.home.hostname'
    ```

    O valor de retorno é semelhante à `ACCOUNTNAME.azuredatalakestore.net`, onde `ACCOUNTNAME` é o nome da conta do Data Lake Store.

    Para localizar o diretório dentro do Data Lake Store que contém o armazenamento para o cluster, utilize os seguintes exemplos:

    ```bash
    curl -u admin:$PASSWORD -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
    | jq '.items[].configurations[].properties["dfs.adls.home.mountpoint"] | select(. != null)'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.configurations.properties.'dfs.adls.home.mountpoint'
    ```

    O valor de retorno é semelhante à `/clusters/CLUSTERNAME/`. Este valor é um caminho dentro da conta do Data Lake Store. Este caminho é a raiz do sistema de ficheiros compatível HDFS para o cluster. 

> [!NOTE]
> O `Get-AzureRmHDInsightCluster` cmdlet fornecida pelo [Azure PowerShell](/powershell/azure/overview) também devolve as informações do armazenamento para o cluster.


## <a name="example-get-configuration"></a>Exemplo: Configuração do Get

1. Obter as configurações que estão disponíveis para o cluster.

    ```bash
    curl -u admin:$PASSWORD -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME?fields=Clusters/desired_configs"
    ```

    ```powershell
    $respObj = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_configs" `
        -Credential $creds
    $respObj.Content
    ```

    Neste exemplo devolve um documento JSON que contém a configuração atual (identificado pelo *tag* valor) para os componentes instalados no cluster. O exemplo seguinte é um excerpt os dados devolvidos por um tipo de cluster do Spark.
   
   ```json
   "spark-metrics-properties" : {
       "tag" : "INITIAL",
       "user" : "admin",
       "version" : 1
   },
   "spark-thrift-fairscheduler" : {
       "tag" : "INITIAL",
       "user" : "admin",
       "version" : 1
   },
   "spark-thrift-sparkconf" : {
       "tag" : "INITIAL",
       "user" : "admin",
       "version" : 1
   }
   ```

2. Obter a configuração para o componente que está interessado em. No exemplo seguinte, substitua `INITIAL` com o valor da etiqueta devolvido do pedido anterior.

    ```bash
    curl -u admin:$PASSWORD -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/configurations?type=core-site&tag=INITIAL"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=core-site&tag=INITIAL" `
        -Credential $creds
    $resp.Content
    ```

    Neste exemplo devolve um documento JSON que contém a configuração atual para o `core-site` componente.

## <a name="example-update-configuration"></a>Exemplo: Configuração de atualização

1. Obter a configuração atual, que armazena Ambari como "configuração desejada":

    ```bash
    curl -u admin:$PASSWORD -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME?fields=Clusters/desired_configs"
    ```

    ```powershell
    Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_configs" `
        -Credential $creds
    ```

    Neste exemplo devolve um documento JSON que contém a configuração atual (identificado pelo *tag* valor) para os componentes instalados no cluster. O exemplo seguinte é um excerpt os dados devolvidos por um tipo de cluster do Spark.
   
    ```json
    "spark-metrics-properties" : {
        "tag" : "INITIAL",
        "user" : "admin",
        "version" : 1
    },
    "spark-thrift-fairscheduler" : {
        "tag" : "INITIAL",
        "user" : "admin",
        "version" : 1
    },
    "spark-thrift-sparkconf" : {
        "tag" : "INITIAL",
        "user" : "admin",
        "version" : 1
    }
    ```
   
    Nesta lista, terá de copiar o nome do componente (por exemplo, **spark\_thrift\_sparkconf** e **tag** valor.

2. Obter a configuração para o componente e a etiqueta utilizando os seguintes comandos:
   
    ```bash
    curl -u admin:$PASSWORD -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/configurations?type=spark-thrift-sparkconf&tag=INITIAL" \
    | jq --arg newtag $(echo version$(date +%s%N)) '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
    ```

    ```powershell
    $epoch = Get-Date -Year 1970 -Month 1 -Day 1 -Hour 0 -Minute 0 -Second 0
    $now = Get-Date
    $unixTimeStamp = [math]::truncate($now.ToUniversalTime().Subtract($epoch).TotalMilliSeconds)
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=spark-thrift-sparkconf&tag=INITIAL" `
        -Credential $creds
    $resp.Content | jq --arg newtag "version$unixTimeStamp" '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
    ```

    > [!NOTE]
    > Substitua **sparkconf de thrift de spark** e **inicial** com o componente e a etiqueta que pretende obter a configuração.
   
    Jq é utilizado para ativar os dados obtidos a partir do HDInsight para um novo modelo de configuração. Especificamente, estes exemplos de efetuar as seguintes ações:
   
    * Cria um valor exclusivo que contém a cadeia "versão" e a data, o que é armazenada no `newtag`.

    * Cria um documento de raiz para a nova configuração pretendida.

    * Obtém o conteúdo do `.items[]` matriz e adiciona-sob o **desired_config** elemento.

    * Elimina o `href`, `version`, e `Config` elementos, como estes elementos não são necessários para submeter uma nova configuração.

    * Adiciona um `tag` elemento com um valor de `version#################`. A parte numérica baseia-se a data atual. Cada configuração tem de ter uma etiqueta exclusiva.
     
    Por fim, os dados são guardados para a `newconfig.json` documento. A estrutura de documento deve ser semelhante ao seguinte exemplo:
     
     ```json
    {
        "Clusters": {
            "desired_config": {
            "tag": "version1459260185774265400",
            "type": "spark-thrift-sparkconf",
            "properties": {
                ....
            },
            "properties_attributes": {
                ....
            }
        }
    }
    ```

3. Abra o `newconfig.json` documento e modificar/adicionar valores a `properties` objeto. O exemplo seguinte altera o valor da `"spark.yarn.am.memory"` de `"1g"` para `"3g"`. Também adiciona `"spark.kryoserializer.buffer.max"` com um valor de `"256m"`.
   
        "spark.yarn.am.memory": "3g",
        "spark.kyroserializer.buffer.max": "256m",
   
    Guarde o ficheiro depois de terminar de efetuar as modificações.

4. Utilize os seguintes comandos para submeter a configuração atualizada ao Ambari.
   
    ```bash
    curl -u admin:$PASSWORD -sS -H "X-Requested-By: ambari" -X PUT -d @newconfig.json "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME"
    ```

    ```powershell
    $newConfig = Get-Content .\newconfig.json
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
        -Credential $creds `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body $newConfig
    $resp.Content
    ```
   
    Estes comandos submeter os conteúdos do **newconfig.json** ficheiro para o cluster como a nova configuração pretendida. O pedido devolve um documento JSON. O **versionTag** elemento neste documento deve corresponder à versão submetido, e o **folhas** objeto contém as alterações de configuração pretendida.

### <a name="example-restart-a-service-component"></a>Exemplo: Reiniciar um componente de serviço

Neste momento, se observar a IU da web do Ambari, o serviço de Spark indica que necessita de ser reiniciado antes da nova configuração tenham efeito. Utilize os seguintes passos para reiniciar o serviço.

1. Utilize o seguinte para ativar o modo de manutenção para o serviço de Spark:

    ```bash
    curl -u admin:$PASSWORD -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo": {"context": "turning on maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}' \
    "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/SPARK"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK" `
        -Credential $creds `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo": {"context": "turning on maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}'
    $resp.Content
    ```
   
    Estes comandos enviarem um documento JSON para o servidor que ativa o modo de manutenção. Pode verificar que o serviço está agora em modo de manutenção utilizando o seguinte pedido:
   
    ```bash
    curl -u admin:$PASSWORD -sS -H "X-Requested-By: ambari" \
    "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/SPARK" \
    | jq .ServiceInfo.maintenance_state
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.ServiceInfo.maintenance_state
    ```
   
    O valor de retorno é `ON`.

2. Em seguida, utilize o seguinte para desativar o serviço:

    ```bash
    curl -u admin:$PASSWORD -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"INSTALLED"}}}' \
    "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/SPARK"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK" `
        -Credential $creds `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"INSTALLED"}}}'
    $resp.Content
    ```
    
    A resposta é semelhante ao seguinte exemplo:
   
    ```json
    {
        "href" : "http://10.0.0.18:8080/api/v1/clusters/CLUSTERNAME/requests/29",
        "Requests" : {
            "id" : 29,
            "status" : "Accepted"
        }
    }
    ```
    
    > [!IMPORTANT]
    > O `href` valor devolvido por este URI está a utilizar o endereço IP interno do nó de cluster. Para utilizar a fora do cluster, substitua a parte '10.0.0.18:8080' com o FQDN do cluster. 
    
    Os seguintes comandos obtêm o estado do pedido:

    ```bash
    curl -u admin:$PASSWORD -sS -H "X-Requested-By: ambari" \
    "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/requests/29" \
    | jq .Requests.request_status
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/requests/29" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.Requests.request_status
    ```

    Uma resposta de `COMPLETED` indica que o pedido foi concluído.

3. Depois de ter concluído o pedido anterior, utilize o seguinte para iniciar o serviço.
   
    ```bash
    curl -u admin:$PASSWORD -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}' \
    "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/SPARK"
    ```
   
    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK" `
        -Credential $creds `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}'
    ```
    O serviço está agora a utilizar a nova configuração.

4. Por fim, utilize o seguinte para desativar o modo de manutenção.
   
    ```bash
    curl -u admin:$PASSWORD -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo": {"context": "turning off maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}' \
    "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/SPARK"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK" `
        -Credential $creds `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo": {"context": "turning off maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}'
    ```

## <a name="next-steps"></a>Passos seguintes

Para uma referência completa da REST API, consulte [V1 de referência de API do Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

