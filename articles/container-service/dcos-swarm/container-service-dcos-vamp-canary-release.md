---
title: "Versão canary com Vamp no cluster DC/OS do Azure | Microsoft Docs"
description: "Como utilizar Vamp versão canary serviços e aplicar tráfego inteligente filtragem num cluster do serviço de contentor do Azure DC/OS"
services: container-service
author: gggina
manager: rasquill
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 04/17/2017
ms.author: rasquill
ms.custom: mvc
ms.openlocfilehash: 4a20091b59f2643ea71cce99c159a5075706e35d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="canary-release-microservices-with-vamp-on-an-azure-container-service-dcos-cluster"></a>Versão canary micro-serviços com Vamp num cluster do serviço de contentor do Azure DC/OS

Nestas instruções, iremos configurar Vamp no serviço de contentor do Azure com um cluster DC/OS. Iremos canary liberte o serviço de demonstração Vamp "sava" e, em seguida, resolver uma incompatibilidade de serviço com o Firefox ao aplicar a filtragem de tráfego inteligente. 

> [!TIP] 
> Esta explicação passo a passo Vamp é executado num cluster DC/OS, mas também pode utilizar Vamp com Kubernetes como o orchestrator.
>

## <a name="about-canary-releases-and-vamp"></a>Sobre versões canary e Vamp


[Libertar canary](https://martinfowler.com/bliki/CanaryRelease.html) é uma estratégia de implementação avançada adotado uma larga maioria por organizações inovadoras como Netflix, Facebook e o Spotify. É uma abordagem que faz sentido, porque esta reduz os problemas, introduz segurança redes e aumenta inovação. Por isso, por que motivo não estão todas as empresas utilizá-la? Expandir um pipeline de CI/CD para incluir estratégias canary adiciona a complexidade e requer o conhecimento de devops extensas e experiência. Que é suficiente para bloquear mais pequenas empresas e as empresas igual antes de poderem mesmo começar a utilizar. 

[Vamp](http://vamp.io/) é um sistema de open source concebido para facilitar esta transição e colocar canary libertar funcionalidades para o seu agendador contentor preferencial. Funcionalidade canary do vamp vai mais além implementação baseada em percentagem. Tráfego pode ser filtrado e dividir numa vasta gama de condições, por exemplo, para utilizadores específicos de destino, intervalos de IP ou dispositivos. Vamp controla e analisa as métricas de desempenho, permitindo automatização com base nos dados do mundo real. Pode configurar a reversão automática sobre os erros ou variantes serviço individual com base na carga ou a latência de escala.

## <a name="set-up-azure-container-service-with-dcos"></a>Configurar o serviço de contentor do Azure com o DC/OS



1. [Implementar um cluster DC/SO](container-service-deployment.md) com um nó principal e dois agentes do tamanho predefinido. 

2. [Criar um túnel SSH](../container-service-connect.md) para ligar ao cluster do DC/OS. Este artigo pressupõe túnel para o cluster na porta 80 do local.


## <a name="set-up-vamp"></a>Configurar Vamp

Agora que tem um cluster DC/SO em execução, poderá instalar Vamp a partir da IU do DC/OS (http://localhost:80). 

![IU do DC/OS](./media/container-service-dcos-vamp-canary-release/01_set_up_vamp.png)

A instalação for efetuada em duas fases:

1. **Implementar Elasticsearch**.

2. Em seguida, **implementar Vamp** ao instalar o pacote de universo Vamp DC/OS.

### <a name="deploy-elasticsearch"></a>Implementar Elasticsearch

Vamp requer Elasticsearch para a coleção de métricas e a agregação. Pode utilizar o [imagens de Docker magneticio](https://hub.docker.com/r/magneticio/elastic/) para implementar uma pilha de Vamp Elasticsearch compatível.

1. Na IU do DC/OS, aceda a **serviços** e clique em **implementar serviço**.

2. Selecione **modo JSON** do **implementar novo serviço** pop-up.

  ![Selecione o modo JSON](./media/container-service-dcos-vamp-canary-release/02_deploy_service_json_mode.png)

3. Cole no seguinte JSON. Esta configuração é executado o contentor com 1 GB de RAM e uma verificação de estado de funcionamento básico na porta Elasticsearch.
  
  ```JSON
  {
    "id": "elasticsearch",
    "instances": 1,
    "cpus": 0.2,
    "mem": 1024.0,
    "container": {
      "docker": {
        "image": "magneticio/elastic:2.2",
        "network": "HOST",
        "forcePullImage": true
      }
    },
    "healthChecks": [
      {
        "protocol": "TCP",
        "gracePeriodSeconds": 30,
        "intervalSeconds": 10,
        "timeoutSeconds": 5,
        "port": 9200,
        "maxConsecutiveFailures": 0
      }
    ]
  }
  ```
  

3. Clique em **implementar**.

  DC/SO implementa o contentor de Elasticsearch. Pode controlar o progresso no **serviços** página.  

  ![implementar i? Elasticsearch](./media/container-service-dcos-vamp-canary-release/03_deply_elasticsearch.png)

### <a name="deploy-vamp"></a>Implementar Vamp

Depois de Elasticsearch relatórios como **executar**, pode adicionar o pacote do universo Vamp de DC/OS. 

1. Aceda a **universo** e procure **vamp**. 
  ![Vamp no universo de DC/OS](./media/container-service-dcos-vamp-canary-release/04_universe_deploy_vamp.png)

2. Clique em **instalar** junto a vamp do pacote e escolha **avançadas instalação**.

3. Desloque para baixo e introduza o seguinte url de elasticsearch: `http://elasticsearch.marathon.mesos:9200`. 

  ![Introduza o URL de Elasticsearch](./media/container-service-dcos-vamp-canary-release/05_universe_elasticsearch_url.png)

4. Clique em **rever e instalar**, em seguida, clique em **instalar** para iniciar a implementação.  

  DC/SO implementa componentes de Vamp todos requeridos. Pode controlar o progresso no **serviços** página.
  
  ![Implementar Vamp como pacote do universo](./media/container-service-dcos-vamp-canary-release/06_deploy_vamp.png)
  
5. Depois de concluída a implementação, pode aceder a IU Vamp:

  ![Serviço de vamp no DC/OS](./media/container-service-dcos-vamp-canary-release/07_deploy_vamp_complete.png)
  
  ![Vamp da IU](./media/container-service-dcos-vamp-canary-release/08_vamp_ui.png)


## <a name="deploy-your-first-service"></a>Implementar o primeiro serviço

Agora que Vamp está a funcionar, implemente um serviço a partir de um blueprint. 

Na sua forma mais simples, uma [Vamp blueprint](http://vamp.io/documentation/using-vamp/blueprints/) descreve os pontos finais (gateways), clusters e implementar os serviços. Vamp utiliza clusters para agrupar variantes diferentes do mesmo serviço em grupos lógicos para libertar canary ou um teste a / B.  

Este cenário utiliza um exemplo de aplicação monolithic chamado [ **sava**](https://github.com/magneticio/sava), que se encontra na versão 1.0. O monolith é empacotada num contentor de Docker, que está no Hub de Docker sob magneticio/sava:1.0.0. Normalmente, a aplicação é executada na porta 8080, mas quiser expô-la na porta 9050 neste caso. Implemente a aplicação através de Vamp utilizando um blueprint simple.

1. Aceda a **implementações**.

2. Clique em **Adicionar**.

3. Cole o seguinte blueprint YAML. Este blueprint contém um cluster com a variante de apenas um serviço, que iremos alterar num passo posterior:

  ```YAML
  name: sava                        # deployment name
  gateways:
    9050: sava_cluster/webport      # stable endpoint
  clusters:
    sava_cluster:               # cluster to create
     services:
        -
          breed:
            name: sava:1.0.0        # service variant name
            deployable: magneticio/sava:1.0.0
            ports:
              webport: 8080/http # cluster endpoint, used for canary releasing
  ```

4. Clique em **Guardar**. Vamp inicia a implementação.

A implementação está listada no **implementações** página. Clique na monitorizar o estado da implementação.

![Vamp IU - implementar sava](./media/container-service-dcos-vamp-canary-release/09_sava100.png)

![serviço de Sava na IU Vamp](./media/container-service-dcos-vamp-canary-release/09a_sava100.png)

São criados dois gateways, que estão listadas no **Gateways** página:

* um ponto final estável para aceder ao serviço em execução (porta 9050) 
* um geridos Vamp interno gateway (mais informações sobre este gateway mais tarde). 

![Vamp IU - sava gateways](./media/container-service-dcos-vamp-canary-release/10_vamp_sava_gateways.png)

O serviço de sava implementou agora, mas não é possível aceder-lhe externamente porque não sabe o Balanceador de carga do Azure para encaminhar o tráfego ao mesmo ainda. Para aceder ao serviço, atualize a configuração de rede do Azure.


## <a name="update-the-azure-network-configuration"></a>Atualizar a configuração de rede do Azure

Vamp implementar o serviço de sava em nós de agente DC/OS, um ponto final estável na porta 9050 a exposição. Para aceder ao serviço de fora do cluster DC/OS, efetue as seguintes alterações à configuração de rede do Azure na sua implementação de cluster: 

1. **Configurar o Balanceador de carga do Azure** para os agentes (o recurso com o nome **dcos-agente-lb-xxxx**) com uma sonda do Estado de funcionamento e uma regra para encaminhar o tráfego na porta 9050 para as instâncias de sava. 

2. **Atualizar o grupo de segurança de rede** para os agentes públicos (o recurso com o nome **XXXX-agente-público-nsg-XXXX**) para permitir tráfego na porta 9050.

Para obter passos detalhados concluir estas tarefas no portal do Azure, consulte [ativar o acesso público para uma aplicação de serviço de contentor Azure](container-service-enable-public-access.md). Especifique a porta 9050 para todas as definições de porta.


Quando tiver sido criado tudo, vá para o **descrição geral** painel do Balanceador de carga do agente DC/OS (o recurso com o nome **dcos-agente-lb-xxxx**). Localizar o **endereço IP público**e utilize o endereço para aceder à sava na porta 9050.

![Portal do Azure – endereço IP público do get](./media/container-service-dcos-vamp-canary-release/18_public_ip_address.png)

![Sava](./media/container-service-dcos-vamp-canary-release/19_sava100.png)


## <a name="run-a-canary-release"></a>Executar uma versão canary

Suponha que tem uma versão nova da aplicação que pretende que a versão canary em produção. O tiver de como magneticio/sava:1.1.0 e estiver pronto para começar. Vamp permite-lhe facilmente adicionar novos serviços para a implementação em execução. Estes serviços "intercalados" são implementados em conjunto com os serviços existentes no cluster e atribuir uma ponderação de 0%. Nenhum tráfego é encaminhado para um serviço recentemente intercalado até ajustar a distribuição de tráfego. O controlo de deslize de ponderação na IU do Vamp dá-lhe controlo total sobre a distribuição, permitindo ajustes incrementais (versão canary) ou uma reversão instantânea.

### <a name="merge-a-new-service-variant"></a>Intercalar uma variante de serviço novo

Intercalar o novo serviço sava 1.1 com a implementação em execução:

1. Na IU do Vamp, clique em **esquemas**.

2. Clique em **adicionar** e cole o seguinte blueprint YAML: este blueprint descreve uma variante de serviço novo (sava: 1.1.0) para implementar dentro do cluster existente (sava_cluster).

  ```YAML
  name: sava:1.1.0      # blueprint name
  clusters:
    sava_cluster:       # cluster to update
      services:
        -
          breed:
            name: sava:1.1.0    # service variant name
            deployable: magneticio/sava:1.1.0    
            ports:
              webport: 8080/http # cluster endpoint to update
  ```
  
3. Clique em **Guardar**. O blueprint é armazenado e apresentado no **esquemas** página.

4. Abra o menu ação o blueprint sava: 1.1 e clique em **intercalar com**.

  ![Vamp IU - esquemas](./media/container-service-dcos-vamp-canary-release/20_sava110_mergeto.png)

5. Selecione o **sava** implementação e clique em **intercalar**.

  ![Vamp IU - blueprint de intercalação para implementação](./media/container-service-dcos-vamp-canary-release/21_sava110_merge.png)

Vamp implementa a variante de serviço novo sava: 1.1.0 descrita blueprint juntamente com sava: 1.0.0 no **sava_cluster** da implementação em execução. 

![Vamp IU - implementação sava atualizada](./media/container-service-dcos-vamp-canary-release/22_sava_cluster.png)

O **sava_cluster/sava/webport** gateway (o ponto final de cluster) também é atualizada, adicionar uma rota para o sava: 1.1.0 recentemente implementado. Neste momento, não o tráfego é encaminhado aqui (o **ponderação** está definido como 0%).

![Vamp IU – o gateway de cluster](./media/container-service-dcos-vamp-canary-release/23_sava_cluster_webport.png)

### <a name="canary-release"></a>Versão canary

Com ambas as versões do sava implementadas no mesmo cluster, ajuste a distribuição de tráfego entre eles, movendo a **ponderação** controlo de deslize.

1. Clique em ![Vamp IU - editar](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) junto a **ponderação**.

2. Defina a distribuição da ponderação como 50% 50% e clique em **guardar**.

  ![Vamp IU - controlo de deslize de peso de gateway](./media/container-service-dcos-vamp-canary-release/24_sava_cluster_webport_weight.png)

3. Voltar para o browser e atualize a página de sava alguns mais vezes. A aplicação sava agora muda entre uma página sava: 1.0 e uma página sava: 1.1.

  ![alternar serviços sava1.0 e sava1.1](./media/container-service-dcos-vamp-canary-release/25_sava_100_101.png)


  > [!NOTE]
  > Este alternação da página funciona melhor com o "Incognito" ou "Anónimo" modo do seu browser devido a colocação em cache de elementos estáticos.
  >

### <a name="filter-traffic"></a>Filtrar o tráfego

Suponha que após a implementação que detetar uma incompatibilidade em sava: 1.1.0 que faz com que mostra os problemas nos Firefox browsers. Pode definir Vamp para filtrar o tráfego de entrada e direcionar que todos os utilizadores Firefox de volta para o conhecidos sava: 1.0.0 estável. Este filtro resolve de forma instantânea de interrupção para utilizadores Firefox, enquanto outro everybody continua a Desfrute mais benefícios 1.1.0 o melhorada sava:.

Vamp utiliza **condições** para filtrar o tráfego entre as rotas de um gateway. Tráfego de primeiro é filtrado e direcionado, de acordo com as condições aplicadas a cada rota. Todo o restante tráfego é distribuído, de acordo com a definição de peso de gateway.

Pode criar uma condição para filtrar todos os utilizadores Firefox e direcioná-los para o sava: 1.0.0 antigo:

1. No sava/sava_cluster/webport **Gateways** página, clique em ![Vamp IU - editar](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) para adicionar um **condição** para sava/sava_cluster/sava:1.0.0/webport a rota. 

2. Introduza a condição **agente de utilizador = = Firefox** e clique em ![Vamp IU - guardar](./media/container-service-dcos-vamp-canary-release/vamp_ui_save.png).

  Vamp adiciona a condição força uma predefinição de 0%. Para começar a filtragem de tráfego, terá de ajustar a força da condição.

3. Clique em ![Vamp IU - editar](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) para alterar o **força** aplicada à condição.
 
4. Definir o **força** 100%, clique em ![Vamp IU - guardar](./media/container-service-dcos-vamp-canary-release/vamp_ui_save.png) para guardar.

  Vamp agora envia todo o tráfego que correspondem à condição (todos os utilizadores Firefox) para sava: 1.0.0.

  ![Vamp IU - aplicar condição ao gateway](./media/container-service-dcos-vamp-canary-release/26_apply_condition.png)

5. Por fim, ajuste o peso do gateway para enviar todo o restante tráfego (todos os utilizadores não Firefox) para o novo sava: 1.1.0. Clique em ![Vamp IU - editar](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) junto a **ponderação** e defina a distribuição da ponderação para 100% é direcionado para o sava/sava_cluster/sava:1.1.0/webport rota.

  Todo o tráfego não filtrado pela condição agora é direcionado para o novo sava: 1.1.0.

6. Para ver o filtro em ação, abra dois diferentes browsers (um Firefox e um browser outro) e aceder ao serviço de sava a partir de ambos. Todos os pedidos de Firefox são enviados para sava: 1.0.0, enquanto todos os outros browsers são direcionados para sava: 1.1.0.

  ![Vamp IU - filtrar o tráfego](./media/container-service-dcos-vamp-canary-release/27_filter_traffic.png)

## <a name="summing-up"></a>Summing cópias de segurança

Este artigo foi uma introdução rápida aos Vamp num cluster DC/OS. Por starters, obteve Vamp cópias de segurança e em execução no seu Azure contentor serviço DC/OS clusters, implementar um serviço com um blueprint Vamp e acedeu ao mesmo o ponto de final expostas (gateway).

Podemos também touched algumas funcionalidades poderosas de Vamp: intercalação de uma nova variante de serviço para a implementação em execução e introdução de forma incremental, em seguida, filtragem de tráfego para resolver uma incompatibilidade conhecida.


## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre a gestão de ações de Vamp através de [Vamp API de REST](http://vamp.io/documentation/api/api-reference/).

* Criar scripts de automatização Vamp no Node.js e executá-los como [Vamp fluxos de trabalho](http://vamp.io/documentation/tutorials/create-a-workflow/).

* Consulte adicional [VAMP tutoriais](http://vamp.io/documentation/tutorials/overview/).

