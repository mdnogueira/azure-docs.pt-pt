---
title: "Analisar registos de fluxo de grupo de segurança de rede do Azure - Graylog | Microsoft Docs"
description: "Saiba como gerir e analisar registos da fluxo de grupo de segurança de rede no Azure utilizando o observador de rede e Graylog."
services: network-watcher
documentationcenter: na
author: mareat
manager: vitinnan
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2017
ms.author: mareat
ms.openlocfilehash: eaee4e1ed213d0834d959d862feffd4bca57cd9f
ms.sourcegitcommit: a036a565bca3e47187eefcaf3cc54e3b5af5b369
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2017
---
# <a name="manage-and-analyze-network-security-group-flow-logs-in-azure-using-network-watcher-and-graylog"></a>Gerir e analisar registos da fluxo de grupo de segurança de rede no Azure utilizando o observador de rede e Graylog

[Registos de fluxo de grupo de segurança de rede](network-watcher-nsg-flow-logging-overview.md) fornecem informações que pode utilizar para compreender o tráfego IP de entrada e de saída para interfaces de rede do Azure. Registos de fluxo mostram fluxos de saída e entrados num por base de regra de grupo de segurança de rede, a interface de rede o fluxo aplica-se a informações de 5 cadeias de identificação (IP de origem/destino, porta de origem/destino, protocolo) sobre o fluxo, e se o tráfego foi permitido ou negado .

Pode ter vários grupos de segurança de rede na sua rede com o registo de fluxo ativado. Vários grupos de segurança de rede com o registo de fluxo ativado podem torná-lo complexa para analisar e obter conhecimentos aprofundados sobre os seus registos. Este artigo fornece uma solução para gerir centralmente estes registos de fluxo do grupo do rede segurança utilizando Graylog, uma gestão do registo de código aberto e ferramenta de análise e Logstash, um pipeline de processamento de dados do lado do servidor de origem aberta.

## <a name="scenario"></a>Cenário

Registos de fluxo de grupo de segurança de rede são ativados utilizando o observador de rede. Fluxo registos fluxo no blob storage do Azure. Um plug-in de Logstash é utilizado para ligar e processar registos de fluxo a partir do blob storage e enviá-los para Graylog. Depois dos registos de fluxo são armazenados nos Graylog, podem ser analisados e visualizados em dashboards personalizados.

! [Graylog fluxo de trabalho]] (. / media/network-watcher-analyze-nsg-flow-logs-graylog/workflow.png)

## <a name="installation-steps"></a>Passos de Instalação

### <a name="enable-network-security-group-flow-logging"></a>Ativar o registo de fluxo de grupo de segurança de rede

Para este cenário, tem de ter rede segurança grupo fluxo registo ativados no grupo de segurança de pelo menos uma rede na sua conta. Para obter instruções sobre como ativar registos de fluxo de grupo de segurança de rede, consulte o artigo seguinte [introdução ao registo do fluxo de grupos de segurança de rede](network-watcher-nsg-flow-logging-overview.md).

### <a name="setting-up-graylog"></a>Configurar Graylog

Neste exemplo, Graylog e Logstash estão configurados num servidor Ubuntu 14.04, implementado no Azure.

- Consulte o [documentação](http://docs.graylog.org/en/2.2/pages/installation/os/ubuntu.html) de Graylog, para instruções passo a passo sobre como instalar o numa Ubuntu.
- Certifique-se também de configurar a interface de web Graylog seguindo o [documentação](http://docs.graylog.org/en/2.2/pages/configuration/web_interface.html#configuring-webif).

Este exemplo utiliza o programa de configuração de Graylog mínimo (revertidos uma instância de um Graylog), mas pode ser criado Graylog de dimensionamento na recursos consoante o sistema e produção necessidades. Para mais informações sobre considerações sobre a arquitetura ou um guia de profundidade da arquitetura, consulte o artigo do Graylog [documentação](http://docs.graylog.org/en/2.2/pages/architecture.html) e [guia da arquitetura](https://www.slideshare.net/Graylog/graylog-engineering-design-your-architecture).

Graylog pode ser instalado em várias formas, consoante a plataforma e preferências. Para obter uma lista completa dos métodos de instalação possíveis, consulte oficial do Graylog [documentação](http://docs.graylog.org/en/2.2/pages/installation.html). A aplicação de servidor Graylog é executado nas distribuições do Linux e tem os seguintes pré-requisitos:

-   Oracle zar Java 8 ou posterior – [documentação de instalação do Oracle](http://docs.oracle.com/javase/8/docs/technotes/guides/install/install_overview.html)
-   Elástico procurar 2 (2.1.0 ou posterior)- [Elasticsearch documentação de instalação](https://www.elastic.co/guide/en/elasticsearch/reference/2.4/_installation.html)
-   MongoDB 2.4 ou posterior – [documentação de instalação do MongoDB](https://docs.mongodb.com/manual/administration/install-on-linux/)

### <a name="install-logstash"></a>Instalar Logstash

Logstash é utilizada para aplanar os registos de fluxo de formatação JSON para um nível de cadeia de identificação de fluxo. Flattening os registos de fluxo facilita os registos organizar e procurar Graylog.

1.  Para instalar Logstash, execute os seguintes comandos:

    ```bash
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```

2.  Configure Logstash para analisar os registos de fluxo e enviá-los para Graylog. Crie um ficheiro de Logstash.conf:

    ```bash
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

3.  Adicione o seguinte conteúdo para o ficheiro. Altere os valores realçados para refletir os detalhes de conta de armazenamento:

    ```
    input {
        azureblob
        {
            storage_account_name => "mystorageaccount"
            storage_access_key => "NrUZmx7pJSKaRJzvQbeiZWi5nBRWOTr7Wwr9DrvK7YtDBrADYxT1y0oEExtSlkDnGRt7qcRiZzEBCCyRYND8SxSt"
            container => "insights-logs-networksecuritygroupflowevent"
            registry_create_policy => "start_over"
            codec => "json"
            file_head_bytes => 21
            file_tail_bytes => 9
            # Possible options: `do_not_break`, `with_head_tail`, `without_head_tail`
            break_json_down_policy  => 'with_head_tail'
            break_json_batch_count => 2
            interval => 5
        }
    }
    
    filter {
        split { field => "[records]" }
        split { field => "[records][properties][flows]"}
        split { field => "[records][properties][flows][flows]"}
        split { field => "[records][properties][flows][flows][flowTuples]"
    }
    
     mutate {
        split => { "[records][resourceId]" => "/"}
        add_field =>{
                    "Subscription" => "%{[records][resourceId][2]}"
                    "ResourceGroup" => "%{[records][resourceId][4]}"
                    "NetworkSecurityGroup" => "%{[records][resourceId][8]}"
        }
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
        udp {
            host => "127.0.0.1"
            port => 12201
        }
    }
    ```
O ficheiro de configuração de Logstash fornecido é composto por três partes: a entrada, o filtro e a saída. A secção de entrada designa a origem de entrada de registos que irá processar Logstash – neste caso, vamos utilizar um blogue do Azure entrada Plug-in (instalada nos passos) que permite-nos para o fluxo de grupo de segurança de rede de acesso aos ficheiros JSON armazenados no blob storage de registo.

A secção de filtro flattens, em seguida, cada ficheiro de registo de fluxo, para que cada tupla de fluxo individuais e as respetivas propriedades associadas torna-se um evento de Logstash separado.

Por fim, a secção de saída reencaminha cada evento Logstash para o servidor de Graylog. De acordo com a sua específica precisa, modificar o ficheiro de configuração Logstash, conforme necessário.

    > [!NOTE]
    > The previous config file assumes that the Graylog server has been configured on the local host loopback IP address 127.0.0.1. If not, be sure to change the host parameter in the output section to the correct IP address.

Para obter mais instruções sobre como instalar Logstash, consulte o Logstash [documentação](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html).

### <a name="install-the-logstash-input-plug-in-for-azure-blob-storage"></a>Instalar a entrada de Logstash Plug-in para o armazenamento de Blobs do Azure

O plug-in de Logstash permite-lhe aceder diretamente os registos de fluxo a partir da respetiva conta de armazenamento de BLOBs designado. Para instalar o plug-in do diretório de instalação predefinido Logstash (neste cenário /usr/share/logstash/bin), execute o seguinte comando:

```bash
cd /usr/share/logstash/bin
sudo ./logstash-plugin install logstash-input-azureblob
```

Para obter mais informações sobre este plug no, consulte o [documentação](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob).

### <a name="set-up-connection-from-logstash-to-graylog"></a>Configurar ligação do Logstash Graylog

Agora que estabeleceram uma ligação para os registos de fluxo com Logstash e configurar o servidor de Graylog, é necessário configurar Graylog para aceitar os ficheiros de registo de entrada.

1.  Navegue para a interface de web Graylog servidor utilizando o URL configurado para ele. Pode aceder a interface ao instruir o browser`http://<graylog-server-ip>:9000/`

2.  Para navegar para a página de configuração, selecione o **sistema** menu pendente na parte superior da navegação barra à direita e, em seguida, clique em **entradas**.
    Em alternativa, navegue para`http://<graylog-server-ip>:9000/system/inputs`

    ![Introdução](./media/network-watcher-analyze-nsg-flow-logs-graylog/getting-started.png)

3.  Para iniciar a nova entrada, selecione *GELF UDP* no **selecione entrada** pendente e, em seguida, preencha o formulário. GELF representa Graylog formato de registo expandido. O formato GELF é desenvolvido pela Graylog. Para saber mais sobre as vantagens, consulte o Graylog [documentação](http://docs.graylog.org/en/2.2/pages/gelf.html).

    Certifique-se vincular a entrada para o IP configurou o seu servidor Graylog no. O endereço IP deve corresponder a **anfitrião** campo de saída do ficheiro de configuração Logstash UDP. A porta predefinida deve ser *12201*. Certifique-se de que a porta corresponde a **porta** campo no UDP de saída designado no ficheiro de configuração de Logstash.

    ![Entradas](./media/network-watcher-analyze-nsg-flow-logs-graylog/inputs.png)

    Depois de iniciar a entrada, deverá ver que são apresentados no **entradas Local** secção, conforme mostrado na imagem seguinte:

    ![](./media/network-watcher-analyze-nsg-flow-logs-graylog/local-inputs.png)

    Para saber mais sobre as entradas de mensagem Graylog, consulte o [documentação](http://docs.graylog.org/en/2.2/pages/sending_data.html#what-are-graylog-message-inputs).

4.  Após tem sido feitas estas configurações, pode começar a Logstash para começar a ler nos registos de fluxo com o seguinte comando:

    `sudo systemctl start logstash.service`

### <a name="search-through-graylog-messages"></a>Procurar Graylog mensagens

Depois, permitindo algum tempo para o servidor de Graylog recolher mensagens, conseguir procurar as mensagens. Para verificar as mensagens a ser enviadas ao servidor Graylog, pela **entradas** configuração página, clique em de "**Mostrar recebidas mensagens**" botão do GELF UDP de entrada que criou. O utilizador é direcionado para um ecrã semelhante para a imagem seguinte: 

![Histograma](./media/network-watcher-analyze-nsg-flow-logs-graylog/histogram.png)

Ao clicar na ligação azul "% {Message}" expande a cada mensagem para mostrar os parâmetros de cadeia de identificação cada fluxo, conforme mostrado na imagem seguinte:

![Mensagens](./media/network-watcher-analyze-nsg-flow-logs-graylog/messages.png)

Por predefinição, todos os campos de mensagem estão incluídos na pesquisa se não selecionar um campo de mensagem específico para procurar. Se pretender procurar mensagens específicas (revertidos – cadeias de identificação de fluxo de específica IP de origem) pode utilizar o idioma de consulta de pesquisa Graylog como [documentados](http://docs.graylog.org/en/2.2/pages/queries.html)


## <a name="analyze-network-security-group-flow-logs-using-graylog"></a>Analisar utilização Graylog dos registos de fluxo de grupo para segurança rede

Agora que Graylog defini-lo como cópia de segurança em execução, podemos utilizar algumas das respetivas funcionalidades para compreender melhor os nossos dados de registo do fluxo. Uma forma de tal é através da utilização de dashboards para criar vistas específicas dos nossos dados.

### <a name="create-a-dashboard"></a>Criar um dashboard

1.  Na barra de navegação superior, selecione **Dashboards** ou navegue para`http://<graylog-server-ip>:9000/dashboards/`

2.  A partir daí, clique na verde **criar dashboard** botão e preencher o formulário com o título e a descrição do seu dashboard curto. Atingiu o **guardar** botão para criar o dashboard novo. Ver um dashboard semelhante para a imagem seguinte:

    ![Dashboards](./media/network-watcher-analyze-nsg-flow-logs-graylog/dashboards.png)

### <a name="add-widgets"></a>Adicionar widgets

Pode clicar o título do dashboard para vê-lo, mas o botão direito do rato agora de vazia, uma vez que vamos não adicionou quaisquer widgets. Um widget de tipo de fácil e útil para adicionar ao dashboard são **valores rápida** gráficos, que apresentam uma lista de valores do campo seleccionado e a respetiva distribuição.

1.  Navegue de volta para os resultados da pesquisa da entrada UDP que está a receber os registos de fluxo selecionando **pesquisa** na barra de navegação superior.

2.  Sob o **resultados de pesquisa** painel no lado esquerdo do ecrã, localizar o **campos** separador, que lista os vários campos de cada mensagem de cadeia de identificação de fluxo recebida.

3.  Selecione qualquer parâmetro pretendido na qual pretende visualizar (neste exemplo selecionamos o IP de origem). Para mostrar a lista de possíveis widgets, clique na seta pendente azul para a esquerda do campo, em seguida, selecione **valores Rápidos** para gerar o widget. Deverá ver algo semelhante para a imagem seguinte:

    ![IP de origem](./media/network-watcher-analyze-nsg-flow-logs-graylog/srcip.png)

4.  A partir daí, pode selecionar o **adicionar ao dashboard** botão no canto superior direito o widget e selecione o dashboard correspondente a adicionar.

5.  Navegue de volta para o dashboard para ver a miniaplicação que acabou de adicionar.

    Pode adicionar uma variedade de outras widgets, tais como histogramas e contagens ao seu dashboard para controlar métricas importantes, tais como o dashboard de exemplo mostrado na imagem seguinte:

    ![Dashboard de Flowlogs](./media/network-watcher-analyze-nsg-flow-logs-graylog/flowlogs-dashboard.png)

    Para obter uma explicação mais nos dashboards e os outros tipos de widgets, consulte do Graylog [documentação](http://docs.graylog.org/en/2.2/pages/dashboards.html).

Ao integrar o observador de rede com Graylog, temos agora uma forma prática e centralizada para gerir e visualizar registos de fluxo de grupo de segurança de rede. Graylog tem um número de outras funcionalidades poderosas como fluxos e alertas também podem ser utilizados para gerir mais registos de fluxo e compreender melhor o tráfego de rede. Agora que tem Graylog configurada e ligada para o Azure, pode continuar a explorar a funcionalidade que oferece.

## <a name="next-steps"></a>Passos seguintes

Saiba como visualizar os registos da fluxo de grupo de segurança de rede com o Power BI, visitando [flui de grupo de segurança de rede de visualizar registos com o Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md).
