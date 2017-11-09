---
title: "Monitorizar serviços do Azure e as aplicações que utilizam Grafana | Microsoft Docs"
description: "Dados de rota Azure Monitor e o Application Insights para que possa vê-los Grafana."
services: monitoring-and-diagnostics
keywords: 
author: rboucher
ms.author: robb
ms.date: 11/06/2017
ms.topic: article
ms.service: monitoring-and-diagnostics
ms.openlocfilehash: 709a98f8bcdb75962f8e41de348ca7a41c677610
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2017
---
# <a name="monitor-your-azure-services-in-grafana"></a>Monitorizar os serviços do Azure no Grafana
Pode agora também é monitorizar serviços do Azure e aplicações de [Grafana](https://grafana.com/) utilizando o [Plug-in da origem de dados de monitorização do Azure](https://grafana.com/plugins/grafana-azure-monitor-datasource). O plug-in recolhe dados de desempenho de aplicações recolhidos pelo Application Insights SDK, bem como os dados de infraestrutura fornecidos pelo Monitor do Azure. Em seguida, pode apresentar estes dados no seu dashboard Grafana.

O plug-in está atualmente em pré-visualização.

Utilize os seguintes passos para configurar um servidor de Grafana do Azure Marketplace e criar dashboards com base nas métricas do Monitor do Azure e o Application Insights.

## <a name="set-up-a-grafana-instance"></a>Configure uma instância de Grafana
1. Vá para o Azure Marketplace e escolha Grafana pelo Grafana Labs.

2. Preencha os nomes e os detalhes. Crie um novo grupo de recursos. Manter o registo dos valores que escolher para a VM nomedeutilizador, palavra-passe VM e palavra-passe de administrador de servidor Grafana.  

3. Escolha o tamanho da VM e uma conta de armazenamento.

4. Configure as definições de configuração de rede.

5. Ver o resumo e selecione **criar** depois de aceitar os termos de utilização.

## <a name="log-in-to-grafana"></a>Inicie sessão no Grafana
1. Depois de concluída a implementação, selecione **aceda ao grupo de recursos**. Pode ver uma lista de recursos criados de novo. 

    ![Objetos de grupo de recursos de Grafana](.\media\monitor-how-to-grafana\grafana1.png) 

    Se selecionar o grupo de segurança de rede (*grafana nsg* neste caso), pode ver que a porta 3000 é utilizada para aceder ao servidor Grafana. 

2. Regresse à lista de recursos e selecione **endereço IP público**. Utilizando os valores encontrados neste ecrã, digite *http://<IP address>: 3000* ou  *<DNSName>: 3000* no seu browser. Deverá ver uma página de início de sessão para o servidor de Grafana que apenas incorporada.
    
    ![Ecrã de início de sessão Grafana](.\media\monitor-how-to-grafana\grafana2.png) 

3. Iniciar sessão com o nome de utilizador como *admin* e a Grafana servidor admin palavra-passe que criou anteriormente. 

## <a name="configure-data-source-plugin"></a>Configurar o plug-in de origem de dados

Depois de iniciado sessão com êxito, deverá ver que o plug-in do Monitor de Azure dados origem já está incluído.

![Grafana mostra Plug-in do Monitor do Azure](.\media\monitor-how-to-grafana\grafana3.png) 

1. Selecione **Adicionar origem de dados** para configurar a monitorização do Azure e o Application Insights. 
    
2. Escolha um nome para a origem de dados e selecione **Azure Monitor** como origem de dados na lista pendente.
    
    
## <a name="create-a-service-principal"></a>Criar um principal de serviço 

Grafana utiliza um principal de serviço do Azure Active Directory para ligar ao Azure Monitor APIs e recolher dados de métricas. Tem de criar um serviço principal para gerir o acesso aos seus recursos do Azure.

1. Consulte [estas instruções](../azure-resource-manager/resource-group-create-service-principal-portal.md) para criar um principal de serviço. Copie e guarde o seu inquilino ID, ID de cliente e um segredo do cliente.

2. Consulte [atribuir a aplicação a função](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-create-service-principal-portal#assign-application-to-role) para atribuir a função de leitor para a aplicação do Azure Active Directory.   

3. Se utilizar o Application Insights, também pode incluir a API do Application Insights e o ID da aplicação para recolher métricas do Application Insights com base. Para obter mais informações, consulte [obter a chave de API e o ID da aplicação](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID).

4. Após introduzir todas estas informações, selecione **guardar** e Grafana testa a API. Deverá ver uma mensagem semelhante ao seguinte.  

    ![Grafana mostra Plug-in do Monitor do Azure](.\media\monitor-how-to-grafana\grafana4.png) 
    
## <a name="build-a-grafana-dashboard"></a>Criar um dashboard Grafana

1. Aceda à home page e selecione **novo Dashboard**.

2. No dashboard do novo, selecione o **gráfico**. Pode experimentar outras opções charting mas este artigo utiliza *gráfico* como exemplo. 

    ![Grafana novo Dashboard](.\media\monitor-how-to-grafana\grafana5.png) 

3. Um gráfico em branco aparece no dashboard. 

4. Clique no título do painel e selecione **editar** introduza os detalhes dos dados que pretende desenhar neste gráfico de gráfico.
    
5. Assim que tiver selecionado a todas as VMs à direita, pode começar a ver as métricas no dashboard. 

Segue-se um dashboard simple com dois gráficos. No lado esquerdo mostra a percentagem de CPU de duas VMs. O gráfico à direita mostra as transações numa conta de armazenamento do Azure, discriminada segundo o tipo de transação de API.
    
![Exemplo de gráficos Grafana dois](.\media\monitor-how-to-grafana\grafana6.png) 
    

## <a name="optional-create-dashboard-playlists"></a>Opcional: Criar listas de reprodução do dashboard

Uma das várias funcionalidades de Grafana é a lista de reprodução do dashboard. Pode criar dashboards vários e adicioná-las para configurar um intervalo para cada dashboard apresentar uma lista de reprodução. Selecione **reproduzir** para ver os dashboards percorra. Poderá querer as pode apresentar um monitor de lateral grandes para fornecer uma "placa de estado" para o seu grupo. 
    
![Exemplo de listas de reprodução de Grafana](.\media\monitor-how-to-grafana\grafana7.png) 


## <a name="optional-monitor-your-custom-metrics-in-the-same-grafana-server"></a>Opcional: Monitorizar as métricas personalizadas no mesmo servidor Grafana

Também pode instalar Telegraf e InfluxDB para recolher e desenhar métricas personalizadas e baseada no agente na mesma instância Grafana. Existem muitos dados origem plug-ins que pode utilizar para reunir estas métricas num dashboard. 
    
Também pode reutilizar esta configurado para incluir as métricas do seu servidor Prometheus. Utilize o plug-in da origem de dados de Prometheus na Galeria de plug-in do Grafana.
    
Seguem-se artigos boa referência sobre como utilizar Telegraf, InfluxDB, Prometheus e Docker
 - [Como monitorizar as métricas de sistema com a pilha de marcas de escala no Ubuntu 16.04](https://www.digitalocean.com/community/tutorials/how-to-monitor-system-metrics-with-the-tick-stack-on-ubuntu-16-04)

 - [Monitorizar as métricas de recurso do Docker com Grafana, InfluxDB e Telegraf](https://blog.vpetkov.net/2016/08/04/monitor-docker-resource-metrics-with-grafana-influxdb-and-telegraf/)

 - [Uma solução de monitorização para os anfitriões de Docker, contentores e de serviços](https://stefanprodan.com/2016/a-monitoring-solution-for-docker-hosts-containers-and-containerized-services/)

Eis uma imagem de um dashboard Grafana completo que tenha as métricas de Monitor do Azure e o Application Insights.
![Exemplo de Grafana métricas](.\media\monitor-how-to-grafana\grafana8.png) 


## <a name="clean-up-resources"></a>Limpar recursos

São-lhe cobrados quando as VMs estão em execução se estiver a utilizá-los ou não. Para evitar incorrer em custos adicionais, limpe o grupo de recursos criado neste artigo. 

1. No menu da esquerda no portal do Azure, clique em **grupos de recursos** e, em seguida, clique em **Grafana**. 
2. Na sua página de grupo de recursos, clique em **eliminar**, tipo **Grafana** na caixa de texto e, em seguida, clique em **eliminar**.

## <a name="next-steps"></a>Passos seguintes
* [Descrição geral das métricas de Monitor do Azure](monitoring-overview-metrics.md)


