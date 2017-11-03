---
title: Visualizar registos de fluxo de NSG de observador de rede do Azure com ferramentas open source | Microsoft Docs
description: "Esta página descreve como utilizar ferramentas open source para visualizar registos de fluxo NSG."
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: e9b2dcad-4da4-4d6b-aee2-6d0afade0cb8
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 8b313b68be07da1a943748d21da68c169980cfc2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="visualize-azure-network-watcher-nsg-flow-logs-using-open-source-tools"></a>Visualizar registos de fluxo de NSG de observador de rede do Azure com ferramentas open source

Os registos de fluxo do grupo de segurança de rede fornecem informações que podem ser utilizadas compreender o tráfego IP de entrada e de saída nos grupos de segurança de rede. Estes registos de fluxo mostram fluxos de saída e entrados numa base por regra, o NIC o fluxo aplica-se a, 5 cadeias de identificação sobre o fluxo (origem/destino IP, porta de origem/destino, protocolo), e se o tráfego foi permitido ou negado.

Estes registos de fluxo podem ser difícil de analisar e obter conhecimentos aprofundados sobre manualmente. No entanto, existem várias ferramentas open source para que o podem ajudar a visualizar estes dados. Este artigo irá fornecer uma solução para visualizar estes registos utilizando a pilha de elástico, que lhe permitirá rapidamente índice e visualizar o fluxo de registos num Kibana dashboard.

## <a name="scenario"></a>Cenário

Neste artigo, vamos configurar uma solução que irá permitir-lhe visualizar registos de fluxo do grupo de segurança de rede com a pilha de elástico.  Um plug-in de entrada de Logstash irá obter os registos de fluxo diretamente a partir do blob de armazenamento configurado para que contém os registos de fluxo. Em seguida, utilizar a pilha de elástico, os registos de fluxo serão indexados e utilizados para criar um dashboard Kibana para visualizar as informações.

![cenário][scenario]

## <a name="steps"></a>Passos

### <a name="enable-network-security-group-flow-logging"></a>Registo de fluxo de ativar o grupo de segurança de rede
Para este cenário, tem de ter rede segurança grupo fluxo o registo ativado pelo menos um grupo de segurança de rede na sua conta. Para obter instruções sobre como ativar registos de fluxo de segurança de rede, consulte o artigo seguinte [introdução ao registo do fluxo de grupos de segurança de rede](network-watcher-nsg-flow-logging-overview.md).


### <a name="set-up-the-elastic-stack"></a>Configurar a pilha de elástico
Ao ligar os registos de fluxo NSG com a pilha elástico, podemos criar um dashboard Kibana que permite-na procurar, da graph, analisar e derivar insights nossos registos.

#### <a name="install-elasticsearch"></a>Instalar Elasticsearch

1. A pilha elástico da versão 5.0 e posterior requer Java 8. Execute o comando `java -version` para verificar a sua versão. Se não tiver java instalar, consulte a documentação sobre [site da Oracle](http://docs.oracle.com/javase/8/docs/technotes/guides/install/install_overview.html)
1. Transferir o pacote de binário correto para o seu sistema:

    ```
    curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.2.0.deb
    sudo dpkg -i elasticsearch-5.2.0.deb
    sudo /etc/init.d/elasticsearch start
    ```

    Outros métodos de instalação podem ser encontrados em [Elasticsearch instalação](https://www.elastic.co/guide/en/beats/libbeat/5.2/elasticsearch-installation.html)

1. Certifique-se de que o Elasticsearch está em execução com o comando:

    ```
    curl http://127.0.0.1:9200
    ```

    Deverá ver uma resposta semelhante a isto:

    ```
    {
    "name" : "Angela Del Toro",
    "cluster_name" : "elasticsearch",
    "version" : {
        "number" : "5.2.0",
        "build_hash" : "8ff36d139e16f8720f2947ef62c8167a888992fe",
        "build_timestamp" : "2016-01-27T13:32:39Z",
        "build_snapshot" : false,
        "lucene_version" : "6.1.0"
    },
    "tagline" : "You Know, for Search"
    }
    ```

Para obter mais instruções sobre pesquisa elástica instalar, consulte a página [instalação](https://www.elastic.co/guide/en/elasticsearch/reference/5.2/_installation.html)

### <a name="install-logstash"></a>Instalar Logstash

1. Para instalar Logstash execute os seguintes comandos:

    ```
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```
1. Em seguida, é necessário configurar Logstash para aceder e analisar os registos de fluxo. Crie um ficheiro de logstash.conf utilizando:

    ```
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

1. Adicione o seguinte conteúdo para o ficheiro:

  ```
input {
   azureblob
     {
         storage_account_name => "mystorageaccount"
         storage_access_key => "VGhpcyBpcyBhIGZha2Uga2V5Lg=="
         container => "insights-logs-networksecuritygroupflowevent"
         codec => "json"
         # Refer https://docs.microsoft.com/en-us/azure/network-watcher/network-watcher-read-nsg-flow-logs
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

  mutate{
   split => { "[records][resourceId]" => "/"}
   add_field => {"Subscription" => "%{[records][resourceId][2]}"
                 "ResourceGroup" => "%{[records][resourceId][4]}"
                 "NetworkSecurityGroup" => "%{[records][resourceId][8]}"}
   convert => {"Subscription" => "string"}
   convert => {"ResourceGroup" => "string"}
   convert => {"NetworkSecurityGroup" => "string"}
   split => { "[records][properties][flows][flows][flowTuples]" => ","}
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
   convert => {"unixtimestamp" => "integer"}
   convert => {"srcPort" => "integer"}
   convert => {"destPort" => "integer"}        
  }

  date{
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

Para obter mais instruções sobre como instalar Logstash, consulte o [documentação oficial](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html)

### <a name="install-the-logstash-input-plugin-for-azure-blob-storage"></a>Instalar o Logstash entrado Plug-in para o armazenamento de Blobs do Azure

Este plug-in de Logstash permitirá aceda diretamente os registos de fluxo a partir da respetiva conta de armazenamento designada. Execute o comando para instalar este plug-in, do diretório de instalação predefinido Logstash (neste /usr/share/logstash/bin maiúsculas):

```
logstash-plugin install logstash-input-azureblob
```

Para iniciar o Logstash execute o comando:

```
sudo /etc/init.d/logstash start
```

Para mais informações sobre este plug-in, consulte a documentação [aqui](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob)

### <a name="install-kibana"></a>Instalar Kibana

1. Execute os seguintes comandos para instalar Kibana:

  ```
  curl -L -O https://artifacts.elastic.co/downloads/kibana/kibana-5.2.0-linux-x86_64.tar.gz
  tar xzvf kibana-5.2.0-linux-x86_64.tar.gz
  ```

1. Para executar os comandos de utilização de Kibana:

  ```
  cd kibana-5.2.0-linux-x86_64/
  ./bin/kibana
  ```

1. Para ver a sua interface de web Kibana, navegue para`http://localhost:5601`
1. Para este cenário, o padrão de índice utilizado para os registos de fluxo é "registos de fluxo de nsg". Pode alterar o padrão de índice na secção "saída" do ficheiro logstash.conf.

1. Se pretender ver o dashboard de Kibana remotamente, criar uma regra de NSG entrada permitir o acesso à **porta 5601**.

### <a name="create-a-kibana-dashboard"></a>Criar um dashboard Kibana

Para este artigo, fornecemos um dashboard de exemplo para ver as tendências e os detalhes nos alertas.

![figura 1][1]

1. Transfira o ficheiro de dashboard [aqui](https://aka.ms/networkwatchernsgflowlogdashboard), o ficheiro de visualização [aqui](https://aka.ms/networkwatchernsgflowlogvisualizations)e o ficheiro de pesquisa guardada [aqui](https://aka.ms/networkwatchernsgflowlogsearch).

1. Sob o **gestão** separador de Kibana, navegue para **guardado objetos** e importar todos os três ficheiros. Em seguida, do **Dashboard** separador, pode abrir e carregar o dashboard de exemplo.

Também pode criar as suas próprias visualizações e dashboards adaptados para as métricas do seu interesse. Saiba mais sobre a criação de visualizações de Kibana a partir do Kibana [documentação oficial](https://www.elastic.co/guide/en/kibana/current/visualize.html).

### <a name="visualize-nsg-flow-logs"></a>Visualizar registos de fluxo do NSG

O dashboard de exemplo fornece visualizações de vários registos de fluxo:

1. Fluxos decisão/direção ao longo do tempo de - de gráficos de séries de tempo que mostra o número de fluxos durante o período de tempo. Pode editar a unidade de tempo e o intervalo de ambas estas visualizações. Fluxos de decisão mostra a proporção de permitir ou negar as decisões tomadas, enquanto fluxos por direção mostra a proporção do tráfego de entrada e saída. Com estas visuais pode examinar as tendências de tráfego ao longo do tempo e procure quaisquer picos ou invulgares padrões.

  ![figura 2][2]

1. Fluxos por porta de origem/destino – gráficos circulares que discrimina fluxos para as suas respetivas portas. Com esta vista, pode ver as portas utilizadas com mais frequência. Se clicar numa porta específica no gráfico circular, irá filtrar o resto do dashboard para baixo para fluxos dessa porta.

  ![figure3][3]

1. Número de fluxos e o tempo de registo mais antigo – métricas que mostra o número de fluxos registadas e a data do registo mais antigo capturada.

  ![figura 4][4]

1. Fluxos por NSG e regra – um gráfico de barras que mostra a distribuição dos fluxos dentro de cada NSG, bem como a distribuição das regras dentro de cada NSG. Aqui pode ver as regras de NSG e gerados mais tráfego.

  ![figure5][5]

1. Principais 10 origem/destino IPs – gráficos de barras que mostra a origem de 10 principais e de destino IPs. Pode ajustar estas gráficos para mostrar os IPs mais ou menos superior. Aqui pode ver mais frequentemente ocorrer IPs, bem como a decisão de tráfego (permitir ou negar) que está a ser efetuadas para cada IP.

  ![figure6][6]

1. Cadeias de identificação de fluxo – esta tabela mostra as informações contidas no cada tupla de fluxo, bem como correspondente NGS e regra.

  ![figure7][7]

Utilizar a barra de consulta na parte superior do dashboard, pode filtrar para baixo o dashboard com base em quaisquer parâmetros de fluxos, tais como o ID de subscrição, grupos de recursos, regra ou qualquer outra variável de interesse. Para mais informações sobre consultas e os filtros da Kibana, consulte o [documentação oficial](https://www.elastic.co/guide/en/beats/packetbeat/current/kibana-queries-filters.html)

## <a name="conclusion"></a>Conclusão

Ao combinar os registos de fluxo do grupo de segurança de rede com a pilha elástico, ter obtemos uma forma poderosa e personalizável para visualizar o nosso tráfego de rede. Estes dashboards permitem-lhe obter rapidamente e partilhar informações sobre o tráfego de rede, bem como filtro para baixo e investigar em qualquer potenciais anomalias. Utilizar Kibana, pode personalizar estas dashboards e criar visualizações específicas para satisfazer as necessidades de segurança, auditoria e conformidade.

## <a name="next-steps"></a>Passos seguintes

Saiba como visualizar os registos de fluxo NSG com o Power BI, visitando [visualizar NSG flui registos com o Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)


<!--Image references-->

[scenario]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/scenario.png
[1]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure1.png
[2]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure2.png
[3]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure3.png
[4]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure4.png
[5]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure5.png
[6]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure6.png
[7]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure7.png
