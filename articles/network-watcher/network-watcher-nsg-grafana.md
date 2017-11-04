---
title: "Gerir a rede grupo fluxo registos de segurança utilizando o observador de rede e Grafana | Microsoft Docs"
description: "Gerir e analisar rede grupo fluxo registos de segurança no Azure utilizando o observador de rede e Grafana."
services: network-watcher
documentationcenter: na
author: kumudD
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2017
ms.author: kumud
ms.openlocfilehash: 44cf074223c88b8fa539144c0d948e68ae6cbd13
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-and-analyze-network-security-group-flow-logs-using-network-watcher-and-grafana"></a>Gerir e analisar os registos de fluxo de grupo de segurança de rede utilizando o observador de rede e Grafana

[Os registos de fluxo do grupo de segurança (NSG) de rede](network-watcher-nsg-flow-logging-overview.md) fornecem informações que podem ser utilizadas para compreender o tráfego IP de entrada e de saída em interfaces de rede. Estes registos de fluxo mostram fluxos de saída e entrados num por base de regras do NSG, a NIC o fluxo aplica-se a informações de 5 cadeias de identificação sobre o fluxo (origem/destino IP, porta de origem/destino, protocolo), e se o tráfego foi permitido ou negado.

Pode ter vários NSGs na sua rede com o registo de fluxo ativado. Esta quantidade de dados de registo torna complexa para analisar e obter conhecimentos aprofundados sobre os seus registos. Este artigo fornece uma solução para gerir centralmente estes registos de fluxo NSG Grafana, um código aberto graphing ferramenta, ElasticSearch, uma pesquisa distribuída e o motor de análise e Logstash, que é um pipeline de processamento de dados do lado do servidor de open source para a utilizar.  

## <a name="scenario"></a>Cenário

Os registos de fluxo NSG estão ativados utilizando o observador de rede e são armazenados no blob storage do Azure. Um plug-in de Logstash é utilizado para ligar e processar registos de fluxo a partir do blob storage e enviá-los para ElasticSearch.  Depois dos registos de fluxo são armazenados nos ElasticSearch, podem ser analisados e visualizados em dashboards personalizados no Grafana.

![Grafana de observador de rede do NSG](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig1.png)

## <a name="installation-steps"></a>Passos de instalação

### <a name="enable-network-security-group-flow-logging"></a>Registo de fluxo de ativar o grupo de segurança de rede

Para este cenário, tem de ter rede segurança grupo fluxo o registo ativado pelo menos um grupo de segurança de rede na sua conta. Para obter instruções sobre como ativar registos de fluxo de segurança de rede, consulte o artigo seguinte [introdução ao registo do fluxo de grupos de segurança de rede](network-watcher-nsg-flow-logging-overview.md).

### <a name="setup-considerations"></a>Considerações sobre o programa de configuração

Neste exemplo Grafana, ElasticSearch e Logstash estão configurados num servidor de LTS 16.04 Ubuntu implementado no Azure. Esta configuração mínima é utilizada para executar todos os três componentes – estejam a ser executadas na mesma VM. Esta configuração só deve ser utilizada para cargas de trabalho de teste e não críticas. Logstash, Elasticsearch e Grafana podem todos ser criado dimensionar de forma independente em várias instâncias. Para obter mais informações, consulte a documentação para cada um destes componentes.

### <a name="install-logstash"></a>Instalar Logstash

Pode utilizar Logstash para aplanar os registos de fluxo de formatação JSON para um nível de cadeia de identificação de fluxo.

1. Para instalar Logstash, execute os seguintes comandos:

    ```bash
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```

2. Configure Logstash para analisar os registos de fluxo e enviá-los para ElasticSearch. Crie um ficheiro de Logstash.conf utilizando:

    ```bash
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

3. Adicione o seguinte conteúdo para o ficheiro. Altere a chave de acesso e o nome conta de armazenamento para refletir os detalhes de conta de armazenamento:

    ```bash
    input {
      azureblob
      {
        storage_account_name => "mystorageaccount"
        storage_access_key => "VGhpcyBpcyBhIGZha2Uga2V5Lg=="
        container => "insights-logs-networksecuritygroupflowevent"
        codec => "json"
        # Refer https://docs.microsoft.com/azure/network-watcher/network-watcher-read-nsg-flow-logs
        # Typical numbers could be 21/9 or 12/2 depends on the nsg log file types
        file_head_bytes => 12
        file_tail_bytes => 2
        # Enable / tweak these settings when event is too big for codec to handle.
        # break_json_down_policy => "with_head_tail"
        # break_json_batch_count => 2
      }
    }
    filter {
      split { field => "[records]" }
      split { field => "[records][properties][flows]"}
      split { field => "[records][properties][flows][flows]"}
      split { field => "[records][properties][flows][flows][flowTuples]"}

      mutate {
        split => { "[records][resourceId]" => "/"}
        add_field => { "Subscription" => "%{[records][resourceId][2]}"
          "ResourceGroup" => "%{[records][resourceId][4]}"
          "NetworkSecurityGroup" => "%{[records][resourceId][8]}" 
        }
        convert => {"Subscription" => "string"}
        convert => {"ResourceGroup" => "string"}
        convert => {"NetworkSecurityGroup" => "string"}
        split => { "[records][properties][flows][flows][flowTuples]" => "," }
        add_field => {
          "unixtimestamp" => "%{[records][properties][flows][flows][flowTuples][0]}"
          "srcIp" => "%{[records][properties][flows][flows][flowTuples][1]}"
          "destIp" => "%{[records][properties][flows][flows][flowTuples][2]}"
          "srcPort" => "%{[records][properties][flows][flows][flowTuples][3]}"
          "destPort" => "%{[records][properties][flows][flows][flowTuples][4]}"
          "protocol" => "%{[records][properties][flows][flows][flowTuples][5]}"
          "trafficflow" => "%{[records][properties][flows][flows][flowTuples][6]}"
          "traffic" => "%{[records][properties][flows][flows][flowTuples][7]}"
        }
        add_field => {
          "time" => "%{[records][time]}"
          "systemId" => "%{[records][systemId]}"
          "category" => "%{[records][category]}"
          "resourceId" => "%{[records][resourceId]}"
          "operationName" => "%{[records][operationName}}"
          "Version" => "%{[records][properties][Version}}"
          "rule" => "%{[records][properties][flows][rule]}"
          "mac" => "%{[records][properties][flows][flows][mac]}"
        }
        convert => {"unixtimestamp" => "integer"}
        convert => {"srcPort" => "integer"}
        convert => {"destPort" => "integer"}
        add_field => { "message" => "%{Message}" }        
      }
 
      date {
        match => ["unixtimestamp" , "UNIX"]
      }
    }
    output {
      stdout { codec => rubydebug }
      elasticsearch {
        hosts => "localhost"
        index => "nsg-flow-logs"
      }
    }
    ```

O ficheiro de configuração de Logstash fornecido é composto por três partes: a entrada, o filtro e a saída. A secção de entrada designa a origem de entrada de registos que irá processar Logstash – neste caso, vamos utilizar uma "azureblob" entrada Plug-in (instalada nos passos) que irão permitir-nos aceder os ficheiros de registo fluxo do NSG JSON armazenados no blob storage. 

A secção de filtro flattens, em seguida, cada ficheiro de registo de fluxo, para que cada tupla de fluxo individuais e as respetivas propriedades associadas torna-se um evento de Logstash separado.

Por fim, a secção de saída reencaminha cada evento Logstash para o servidor de ElasticSearch. Pode modificar o ficheiro de configuração Logstash de acordo com as suas necessidades específicas.

### <a name="install-the-logstash-input-plugin-for-azure-blob-storage"></a>Instalar o Logstash entrado Plug-in para o Blob storage do Azure

Este plug-in de Logstash permite-lhe aceder diretamente os registos de fluxo a partir da respetiva conta de armazenamento de BLOBs designado. Para instalar este plug, a partir do diretório de instalação de Logstash predefinido (neste /usr/share/logstash/bin maiúsculas) execute o comando:

```bash
cd /usr/share/logstash/bin
sudo ./logstash-plugin install logstash-input-azureblob
```

Para obter mais informações sobre este plug no, consulte [Logstash Plug-in entrada para o Blobs Storage do Azure](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob).

### <a name="install-elasticsearch"></a>Instalar ElasticSearch

Pode utilizar o seguinte script para instalar ElasticSearch. Para obter informações sobre a instalação ElasticSearch, consulte [pilha elástico](https://www.elastic.co/guide/en/elastic-stack/current/index.html).

```bash
apt-get install apt-transport-https openjdk-8-jre-headless uuid-runtime pwgen -y
wget -qO - https://packages.elastic.co/GPG-KEY-elasticsearch | apt-key add -
echo "deb https://packages.elastic.co/elasticsearch/5.x/debian stable main" | tee -a /etc/apt/sources.list.d/elasticsearch-5.x.list
apt-get update && apt-get install elasticsearch
sed -i s/#cluster.name:.*/cluster.name:\ grafana/ /etc/elasticsearch/elasticsearch.yml
systemctl daemon-reload
systemctl enable elasticsearch.service
systemctl start elasticsearch.service
```

### <a name="install-grafana"></a>Instalar Grafana

Para instalar e executar Grafana, execute os seguintes comandos:

```bash
wget https://s3-us-west-2.amazonaws.com/grafana-releases/release/grafana_4.5.1_amd64.deb
sudo apt-get install -y adduser libfontconfig
sudo dpkg -i grafana_4.5.1_amd64.deb
sudo service grafana-server start
```

Para informações de instalação adicionais, consulte [instalar Debian / Ubuntu](http://docs.grafana.org/installation/debian/).

#### <a name="add-the-elasticsearch-server-as-a-data-source"></a>Adicione o servidor de ElasticSearch como uma origem de dados

Em seguida, terá de adicionar o índice de ElasticSearch que contém registos de fluxo como uma origem de dados. Pode adicionar uma origem de dados selecionando **Adicionar origem de dados** e concluir o formulário com as informações relevantes. Um exemplo desta configuração pode ser encontrado na captura de ecrã seguinte:

![Adicionar origem de dados](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig2.png)

#### <a name="create-a-dashboard"></a>Criar um dashboard

Agora que configurou com êxito Grafana ler desde o índice de ElasticSearch que contém os registos de fluxo NSG, pode criar e personalizar os dashboards. Para criar um novo dashboard, selecione **criar o dashboard do primeiro**. A configuração de gráfico de exemplo seguinte mostra fluxos segmentados por regras NSG:

![Gráfico do dashboard](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig3.png)

Captura de ecrã seguinte ilustra um gráfico e o gráfico que mostra os fluxos superiores e a respetiva frequência. Fluxos também são mostrados por regras NSG e as decisões fluxos pelo. Grafana é altamente personalizável, pelo que é recomendado que crie dashboards para se adequarem às suas necessidades de monitorização específicas. O exemplo seguinte mostra um dashboard típico:

![Gráfico do dashboard](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig4.png)

## <a name="conclusion"></a>Conclusão

Ao integrar o observador de rede com ElasticSearch e Grafana, tem agora uma forma prática e centralizada para gerir e visualizar registos de fluxo NSG, bem como outros dados. Grafana tem um número de outras funcionalidades graphing poderosas que também podem ser utilizadas para gerir mais registos de fluxo e compreender melhor o tráfego de rede. Agora que tem uma instância de Grafana configurada e ligada para o Azure, pode continuar a explorar a funcionalidade que oferece.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre como utilizar [observador de rede](network-watcher-monitoring-overview.md).

