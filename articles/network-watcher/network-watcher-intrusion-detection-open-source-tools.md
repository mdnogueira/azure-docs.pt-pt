---
title: "Executar a deteção de intrusão de rede com o observador de rede do Azure e ferramentas open source | Microsoft Docs"
description: "Este artigo descreve como utilizar o observador de rede do Azure e abrir ferramentas de origem para efetuar a deteção de intrusão de rede"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 0f043f08-19e1-4125-98b0-3e335ba69681
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: aff1b5f9e8860d3b8dc09b37684bb8a4ac2bf134
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="perform-network-intrusion-detection-with-network-watcher-and-open-source-tools"></a>Executar a deteção de intrusão de rede com o observador de rede e ferramentas open source

Capturas de pacotes são uma componente essencial para implementar sistemas de deteção de intrusão de rede (IDS) e efetuar a monitorização de segurança de rede (NSM). Existem várias ferramentas de IDS de open source para processam capturas de pacotes e procurarem as assinaturas do intrusions possíveis de rede e a atividade maliciosa. Utilizar o pacote captura fornecida pelo observador de rede, pode analisar a rede para qualquer intrusions prejudiciais ou vulnerabilidades.

Uma ferramenta deste tipo open source para é Suricata, um motor de IDS, que utiliza rulesets para monitorizar o tráfego de rede e aciona alertas sempre que ocorrer eventos suspeitos. Suricata oferece um motor de vários threads, o que significa que pode efetuar a análise de tráfego de rede com maior velocidade e eficiência. Para obter mais detalhes sobre Suricata e as respetivas capacidades, visite o seu site em https://suricata-ids.org/.

## <a name="scenario"></a>Cenário

Este artigo explica como configurar o ambiente para executar a deteção de intrusão de rede utilizando o observador de rede, Suricata e a pilha de elástico. Observador de rede fornece-lhe as capturas de pacotes utilizadas para executar a deteção de intrusão de rede. Suricata processa as capturas de pacotes e alertas de Acionador com base nos pacotes que corresponde ao respetivo ruleset indicado de ameaças. Estes alertas são armazenados num ficheiro de registo no seu computador local. Utilizar a pilha de elástico, os registos gerados pelo Suricata podem ser indexados e utilizados para criar um dashboard Kibana, fornecendo uma representação visual de um meio para obter rapidamente informações para potenciais vulnerabilidades de rede e os registos.  

![cenário de aplicação web simples][1]

Ambas as ferramentas de open source podem ser configuradas numa VM do Azure, permitindo-lhe executar esta análise na seu próprio ambiente de rede do Azure.

## <a name="steps"></a>Passos

### <a name="install-suricata"></a>Instalar Suricata

Para todos os outros métodos de instalação, visite http://suricata.readthedocs.io/en/latest/install.html

1. No terminal da linha de comandos da sua VM, execute os seguintes comandos:

    ```
    sudo add-apt-repository ppa:oisf/suricata-stable
    sudo apt-get update
    sudo sudo apt-get install suricata
    ```

1. Para verificar a instalação, execute o comando `suricata -h` para ver a lista completa dos comandos.

### <a name="download-the-emerging-threats-ruleset"></a>Transferir o ruleset ameaças emergentes

Nesta fase, não temos quaisquer regras para Suricata ser executada. Pode criar as suas próprias regras se existirem ameaças específicas para a sua rede que pretende detetar, ou pode também utilizar desenvolvida conjuntos de regras de um número de fornecedores, tais como ameaças emergentes ou regras VRT de Snort. Utilizamos o ruleset ameaças emergentes livremente acessível aqui:

Transferir o conjunto de regras e copie-os para o diretório:

```
wget http://rules.emergingthreats.net/open/suricata/emerging.rules.tar.gz
tar zxf emerging.rules.tar.gz
sudo cp -r rules /etc/suricata/
```

### <a name="process-packet-captures-with-suricata"></a>Capturas de pacotes de processo com Suricata

Ao processar o pacote captura utilizando Suricata, execute o seguinte comando:

```
sudo suricata -c /etc/suricata/suricata.yaml -r <location_of_pcapfile>
```
Para verificar os alertas resultantes, ler o ficheiro fast.log:
```
tail -f /var/log/suricata/fast.log
```

### <a name="set-up-the-elastic-stack"></a>Configurar a pilha de elástico

Enquanto os registos que produz Suricata contém informações importantes sobre o que acontece na nossa rede, estes ficheiros de registo não são o facilitar a leitura e compreender. Ao ligar Suricata com a pilha elástico, podemos criar um dashboard Kibana que permite-na procurar, da graph, analisar e derivar insights nossos registos.

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
1. Em seguida, é necessário configurar Logstash ler a partir da saída do ficheiro de eve.json. Crie um ficheiro de logstash.conf utilizando:

    ```
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

1. Adicione o seguinte conteúdo para o ficheiro (Certifique-se de que o caminho para o ficheiro eve.json correto):

    ```ruby
    input {
    file {
        path => ["/var/log/suricata/eve.json"]
        codec =>  "json"
        type => "SuricataIDPS"
    }

    }

    filter {
    if [type] == "SuricataIDPS" {
        date {
        match => [ "timestamp", "ISO8601" ]
        }
        ruby {
        code => "
            if event.get('[event_type]') == 'fileinfo'
            event.set('[fileinfo][type]', event.get('[fileinfo][magic]').to_s.split(',')[0])
            end
        "
        }

        ruby{
        code => "
            if event.get('[event_type]') == 'alert'
            sp = event.get('[alert][signature]').to_s.split(' group ')
            if (sp.length == 2) and /\A\d+\z/.match(sp[1])
                event.set('[alert][signature]', sp[0])
            end
            end
            "
        }
    }

    if [src_ip]  {
        geoip {
        source => "src_ip"
        target => "geoip"
        #database => "/opt/logstash/vendor/geoip/GeoLiteCity.dat"
        add_field => [ "[geoip][coordinates]", "%{[geoip][longitude]}" ]
        add_field => [ "[geoip][coordinates]", "%{[geoip][latitude]}"  ]
        }
        mutate {
        convert => [ "[geoip][coordinates]", "float" ]
        }
        if ![geoip.ip] {
        if [dest_ip]  {
            geoip {
            source => "dest_ip"
            target => "geoip"
            #database => "/opt/logstash/vendor/geoip/GeoLiteCity.dat"
            add_field => [ "[geoip][coordinates]", "%{[geoip][longitude]}" ]
            add_field => [ "[geoip][coordinates]", "%{[geoip][latitude]}"  ]
            }
            mutate {
            convert => [ "[geoip][coordinates]", "float" ]
            }
        }
        }
    }
    }

    output {
    elasticsearch {
        hosts => "localhost"
    }
    }
    ```

1. Certifique-se conceder as permissões corretas para o ficheiro eve.json para que Logstash pode ingerir o ficheiro.
    
    ```
    sudo chmod 775 /var/log/suricata/eve.json
    ```

1. Para iniciar o Logstash execute o comando:

    ```
    sudo /etc/init.d/logstash start
    ```

Para obter mais instruções sobre como instalar Logstash, consulte o [documentação oficial](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html)

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
1. Para este cenário, o padrão de índice utilizado para os registos de Suricata é "logstash-*"

1. Se pretender ver o dashboard de Kibana remotamente, criar uma regra de NSG entrada permitir o acesso à **porta 5601**.

### <a name="create-a-kibana-dashboard"></a>Criar um dashboard Kibana

Para este artigo, fornecemos um dashboard de exemplo para ver as tendências e os detalhes nos alertas.

1. Transfira o ficheiro de dashboard [aqui](https://aka.ms/networkwatchersuricatadashboard), o ficheiro de visualização [aqui](https://aka.ms/networkwatchersuricatavisualization)e o ficheiro de pesquisa guardada [aqui](https://aka.ms/networkwatchersuricatasavedsearch).

1. Sob o **gestão** separador de Kibana, navegue para **guardado objetos** e importar todos os três ficheiros. Em seguida, do **Dashboard** separador, pode abrir e carregar o dashboard de exemplo.

Também pode criar as suas próprias visualizações e dashboards adaptados para as métricas do seu interesse. Saiba mais sobre a criação de visualizações de Kibana a partir do Kibana [documentação oficial](https://www.elastic.co/guide/en/kibana/current/visualize.html).

![dashboard de kibana][2]

### <a name="visualize-ids-alert-logs"></a>Visualizar registos de alerta de IDS

O dashboard de exemplo fornece visualizações de vários registos de alerta Suricata:

1. Alertas por GeoIP – um mapa que mostra a distribuição de alertas pelo respetivo país de origem com base na localização geográfica (determinada por IP)

    ![georreplicação ip][3]

1. Alertas de 10 principais – um resumo dos 10 mais frequentes acionada alertas e a respetiva descrição. Ao clicar num alerta individual filtra para baixo o dashboard para as informações relativas a esse alerta específica.

    ![imagem 4][4]

1. Número de alertas – a contagem total de alertas acionados pelo ruleset

    ![imagem 5][5]

1. Principais 20 origem/destino IPs/portas - gráficos circulares que mostra os principais 20 IPs e as portas que alertas foram acionadas no. Pode filtrar estão a ser acionado baixo em IPs/portas específicas para ver o número e os tipos de alertas.

    ![imagem 6][6]

1. Resumo de alerta – de uma tabela resumir detalhes específicos de cada alerta individual. Pode personalizar esta tabela para mostrar os outros parâmetros de interesse para cada alerta.

    ![imagem 7][7]

Para obter mais documentação sobre a criação de visualizações personalizadas e dashboards, consulte [documentação oficial do Kibana](https://www.elastic.co/guide/en/kibana/current/introduction.html).

## <a name="conclusion"></a>Conclusão

Ao combinar o pacote captura fornecida pelo observador de rede e ferramentas de IDS de open source como Suricata, pode efetuar a deteção de intrusão de rede para uma vasta gama de ameaças. Estes dashboards permitem-lhe detetar rapidamente as tendências e anomalias dentro da sua rede, como dig bem para os dados para detetar a causa raiz de alertas, como agentes de utilizador mal intencionado ou portas vulneráveis. Com estes dados extraídos, possa tomar decisões informadas sobre a reagir a proteger a sua rede de quaisquer tentativas de intrusões prejudicial e criar regras para impedir futuras intrusions à sua rede.

## <a name="next-steps"></a>Passos seguintes

Saiba como acionar capturas de pacotes com base em alertas, visitando [utilizar a captura de pacotes para fazer a monitorização de rede proativa com as funções do Azure](network-watcher-alert-triggered-packet-capture.md)

Saiba como visualizar os registos de fluxo NSG com o Power BI, visitando [visualizar NSG flui registos com o Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)



<!-- images -->
[1]: ./media/network-watcher-intrusion-detection-open-source-tools/figure1.png
[2]: ./media/network-watcher-intrusion-detection-open-source-tools/figure2.png
[3]: ./media/network-watcher-intrusion-detection-open-source-tools/figure3.png
[4]: ./media/network-watcher-intrusion-detection-open-source-tools/figure4.png
[5]: ./media/network-watcher-intrusion-detection-open-source-tools/figure5.png
[6]: ./media/network-watcher-intrusion-detection-open-source-tools/figure6.png
[7]: ./media/network-watcher-intrusion-detection-open-source-tools/figure7.png
