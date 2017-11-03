---
title: "Solução de monitorização do contentor no Log Analytics do Azure | Microsoft Docs"
description: "A solução de monitorização do contentor de mensagens em fila na análise de registos ajuda-o a ver e gerir os seus Docker e o Windows anfitriões contentor numa única localização."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: e1e4b52b-92d5-4bfa-8a09-ff8c6b5a9f78
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/20/2017
ms.author: magoedte;banders
ms.openlocfilehash: 562a7a73e2d440c0c3e3e8ab9e94ffd6c1fba7d9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="container-monitoring-solution-in-log-analytics"></a>Solução de monitorização do contentor no Log Analytics

![Símbolo de contentores](./media/log-analytics-containers/containers-symbol.png)

Este artigo descreve como configurar e utilizar a solução de monitorização do contentor na análise de registos, que ajuda a ver e gerir os seus Docker e o Windows anfitriões contentor numa única localização. Docker é um sistema de Virtualização do software utilizado para criar contentores de automatizar a implementação de software para a respetiva infraestrutura de TI.

A solução mostra os contentores estão em execução, que imagem de contentor estiver a executar e, em que os contentores estão em execução. Pode ver informações detalhadas de auditoria que mostra comandos utilizados com contentores. Além disso, pode resolver contentores ao visualizar e pesquisar registos centralizados sem ter de ver remotamente os anfitriões de Docker ou Windows. Pode encontrar os contentores que podem ser inúteis e consumo recursos em excesso num anfitrião. E, pode ver centralizada de CPU, memória, armazenamento e informações de utilização e desempenho de rede para contentores. Em computadores com Windows, pode centralizar e comparar os registos do Windows Server, Hyper-V e contentores de Docker. A solução suporta os seguintes orchestrators do contentor:

- Docker Swarm
- DC/OS
- Utilizar o Kubernetes
- Service Fabric
- Red Hat OpenShift


O diagrama seguinte mostra as relações entre vários anfitriões do contentor e os agentes com o OMS.

![Diagrama de contentores](./media/log-analytics-containers/containers-diagram.png)

## <a name="system-requirements-and-supported-platforms"></a>Requisitos de sistema e as plataformas suportadas

Antes de começar, reveja os detalhes seguintes para verificar que cumpre os pré-requisitos.

### <a name="container-monitoring-solution-support-for-docker-orchestrator-and-os-platform"></a>Suporta a solução de monitorização do contentor para a plataforma do Docker Orchestrator e SO
A tabela seguinte descreve a orquestração de Docker e o sistema operativo de suporte de inventário de contentor, desempenho e registos de análise de registos de monitorização.   

| | ACS | Linux | Windows | Contentor<br>Inventário | Imagem<br>Inventário | Nó<br>Inventário | Contentor<br>Desempenho | Contentor<br>Evento | Evento<br>Registar | Contentor<br>Registar |
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
| Utilizar o Kubernetes | &#8226; | &#8226; | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; |
| Mesosphere<br>DC/OS | &#8226; | &#8226; | | &#8226; | &#8226; | &#8226; | &#8226;| &#8226; | &#8226; | &#8226; |
| Docker<br>Swarm | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |
| Serviço<br>Recursos de infraestrutura | | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; |
| Red Hat aberta<br>SHIFT | | &#8226; | | &#8226; | &#8226;| &#8226; | &#8226; | &#8226; | | &#8226; |
| Windows Server<br>(autónomo) | | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |
| Servidor Linux<br>(autónomo) | | &#8226; | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |


### <a name="docker-versions-supported-on-linux"></a>Versões de docker suportadas no Linux

- Docker 1.11 para 1.13
- Docker CE e EE v17.06

### <a name="x64-linux-distributions-supported-as-container-hosts"></a>x64 distribuições Linux suportadas como anfitriões de contentor

- Ubuntu 14.04 LTS e 16.04 LTS
- CoreOS(stable)
- Amazon Linux 2016.09.0
- openSUSE 13.2
- openSUSE LEAP 42.2
- CentOS 7.2 e 7.3
- SLES 12
- RHEL 7.2 e 7.3
- Plataforma de contentor do Red Hat OpenShift (OCP) 3.4 e 3.5
- ACS Mesosphere DC/SO 1.7.3 para 1.8.8
- ACS Kubernetes 1.4.5 para 1.6
    - Eventos de Kubernetes, Kubernetes inventário e processos de contentor só são suportados o com a versão 1.4.1-45 e mais tarde do agente do OMS a para Linux
- ACS Docker Swarm

### <a name="supported-windows-operating-system"></a>Sistema operativo Windows suportado

- Windows Server 2016
- Windows 10 Anniversary Edition (Professional ou Enterprise)

### <a name="docker-versions-supported-on-windows"></a>Versões de docker suportadas no Windows

- Docker 1.12 e 1.13
- Docker 17.03.0 e posterior

## <a name="installing-and-configuring-the-solution"></a>Instalar e configurar a solução
Utilize as seguintes informações para instalar e configurar a solução.

1. Adicionar a solução de monitorização do contentor à sua área de trabalho do OMS de [do Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ContainersOMS?tab=Overview) ou utilizando o processo descrito no [soluções de análise de registos adicionar da galeria do soluções](log-analytics-add-solutions.md).

2. Instalar e utilizar o Docker com um agente do OMS. Com base no seu sistema operativo e Docker orchestrator, pode utilizar os métodos seguintes para configurar o agente.
  - Para anfitriões autónomos:
    - Em sistemas operativos Linux suportados, instalar e executar Docker e, em seguida, instalar e configurar o [agente do OMS para Linux](log-analytics-agent-linux.md).  
    - No CoreOS, não é possível executar o agente do OMS para Linux. Em vez disso, execute uma versão do agente do OMS para Linux. Reveja [anfitriões de contentor do Linux, incluindo CoreOS](#for-all-linux-container-hosts-including-coreos) ou [anfitriões de contentor do Azure Government Linux, incluindo CoreOS](#for-all-azure-government-linux-container-hosts-including-coreos) se estiver a trabalhar com contentores na nuvem do Azure Government.
    - No Windows Server 2016 e Windows 10, instalar o cliente e o motor de Docker, em seguida, ligar um agente para recolher informações e envia-as à análise de registos. Reveja [instalar e configurar os anfitriões de contentor do Windows](#install-and-configure-windows-container-hosts) se tiver um ambiente do Windows.
  - De orquestração do Docker multi anfitrião:
    - Se tiver um ambiente do Red Hat OpenShift, reveja [configurar um agente do OMS para Red Hat OpenShift](#configure-an-oms-agent-for-red-hat-openshift).
    - Se tiver um cluster de Kubernetes utilizando o serviço de contentor do Azure, reveja [configurar um agente do OMS para Kubernetes](#configure-an-oms-agent-for-kubernetes).
    - Se tiver um cluster do serviço de contentor do Azure DC/OS, saiba mais em [monitorizar um cluster DC/OS do serviço de contentor do Azure com o Operations Management Suite](../container-service/dcos-swarm/container-service-monitoring-oms.md).
    - Se tiver um ambiente de modo Docker Swarm, saiba mais em [configurar um agente do OMS para Docker Swarm](#configure-an-oms-agent-for-docker-swarm).
    - Se utilizar contentores com o Service Fabric, saiba mais em [descrição geral do Azure Service Fabric](../service-fabric/service-fabric-overview.md).

Reveja o [motor de Docker no Windows](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon) artigo para obter informações adicionais sobre como instalar e configurar os motores de Docker nos computadores com Windows.

> [!IMPORTANT]
> Tem de executar o docker **antes** instalar o [agente do OMS para Linux](log-analytics-agent-linux.md) nos anfitriões contentor. Se já tiver instalado o agente antes de instalar o Docker, terá de reinstalar o agente do OMS para Linux. Para obter mais informações sobre o Docker, consulte o [Web site do Docker](https://www.docker.com).


### <a name="install-and-configure-linux-container-hosts"></a>Instalar e configurar os anfitriões de contentor do Linux

Depois de instalar Docker, utilize as seguintes definições para o anfitrião de contentor para configurar o agente para utilização com o Docker. Primeiro terá do ID da área de trabalho OMS e a chave, o que pode encontrar no portal do Azure. Na área de trabalho, clique em **início rápido** > **computadores** para ver o **ID da área de trabalho** e **chave primária**.  Copie e cole-o no seu editor favorito.

**Para todos os anfitriões de contentor de Linux, exceto CoreOS:**

- Para obter mais informações e os passos sobre como instalar o agente do OMS para Linux, consulte [ligar os computadores com Linux para o Operations Management Suite (OMS)](log-analytics-agent-linux.md).

**Para todos os Linux contentor anfitriões, incluindo CoreOS:**

Inicie o contentor do OMS que pretende monitorizar. Modificar e utilize o seguinte exemplo:

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -e WSID="your workspace id" -e KEY="your key" -h=`hostname` -p 127.0.0.1:25225:25225 --name="omsagent" --restart=always microsoft/oms
```

**Para todos os Azure Government Linux contentor anfitriões, incluindo CoreOS:**

Inicie o contentor do OMS que pretende monitorizar. Modificar e utilize o seguinte exemplo:

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -v /var/log:/var/log -e WSID="your workspace id" -e KEY="your key" -e DOMAIN="opinsights.azure.us" -p 127.0.0.1:25225:25225 -p 127.0.0.1:25224:25224/udp --name="omsagent" -h=`hostname` --restart=always microsoft/oms
```

**Mudar de utilizar um agente Linux instalado para um num contentor**

Se utilizar o agente diretamente instalados anteriormente e pretende utilizar em vez disso, um agente em execução num contentor, tem primeiro de remover o agente do OMS Linux. Consulte [desinstalar o agente do OMS para Linux](log-analytics-agent-linux.md#uninstalling-the-oms-agent-for-linux) para compreender como desinstalar com êxito o agente.  

#### <a name="configure-an-oms-agent-for-docker-swarm"></a>Configurar um agente do OMS para Docker Swarm

Pode executar o agente do OMS como um serviço global no Docker Swarm. Utilize as seguintes informações para criar um serviço de agente do OMS. Tem de inserir o seu ID da área de trabalho OMS e a chave primária.

- Execute o seguinte no nó principal.

    ```
    sudo docker service create  --name omsagent --mode global  --mount type=bind,source=/var/run/docker.sock,destination=/var/run/docker.sock  -e WSID="<WORKSPACE ID>" -e KEY="<PRIMARY KEY>" -p 25225:25225 -p 25224:25224/udp  --restart-condition=on-failure microsoft/oms
    ```

##### <a name="secure-secrets-for-docker-swarm"></a>Proteger os segredos para o Docker Swarm

Para o Docker Swarm, assim que for criado o segredo para ID da área de trabalho e a chave primária, utilize as seguintes informações para criar as suas informações secretas.

1. Execute o seguinte no nó principal.

    ```
    echo "WSID" | docker secret create WSID -
    echo "KEY" | docker secret create KEY -
    ```

2. Certifique-se de que os segredos foram criados corretamente.

    ```
    keiko@swarmm-master-13957614-0:/run# sudo docker secret ls
    ```

    ```
    ID                          NAME                CREATED             UPDATED
    j2fj153zxy91j8zbcitnjxjiv   WSID                43 minutes ago      43 minutes ago
    l9rh3n987g9c45zffuxdxetd9   KEY                 38 minutes ago      38 minutes ago
    ```

3. Execute o seguinte comando para montar os segredos de agente do OMS.

    ```
    sudo docker service create  --name omsagent --mode global  --mount type=bind,source=/var/run/docker.sock,destination=/var/run/docker.sock --secret source=WSID,target=WSID --secret source=KEY,target=KEY  -p 25225:25225 -p 25224:25224/udp --restart-condition=on-failure microsoft/oms
    ```

#### <a name="configure-an-oms-agent-for-red-hat-openshift"></a>Configurar um agente do OMS para OpenShift Red Hat
Existem três maneiras de adicionar o agente do OMS para Red Hat OpenShift para iniciar a recolha de dados de monitorização do contentor.

* [Instalar o agente do OMS para Linux](log-analytics-agent-linux.md) diretamente em cada nó de OpenShift  
* [Ativar a extensão de VM de análise do registo](log-analytics-azure-vm-extension.md) em cada nó de OpenShift que reside no Azure  
* Instalar o agente do OMS como um conjunto de daemon de OpenShift  

Nesta secção, iremos abrangem os passos necessários para instalar o agente do OMS como um conjunto de daemon OpenShift.  

1. Início de sessão para o nó mestre OpenShift e copie o ficheiro yaml [ocp-omsagent.yaml](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-omsagent.yaml) a partir do GitHub para o nó mestre e modifique o valor com o ID da área de trabalho OMS e com a chave primária.
2. Execute os seguintes comandos para criar um projeto para OMS e definir a conta de utilizador.

    ```
    oadm new-project omslogging --node-selector='zone=default'
    oc project omslogging  
    oc create serviceaccount omsagent  
    oadm policy add-cluster-role-to-user cluster-reader   system:serviceaccount:omslogging:omsagent  
    oadm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent  
    ```

4. Para implementar o conjunto de daemon, execute o seguinte:

    `oc create -f ocp-omsagent.yaml`

5. Certifique-se de que está configurado e a funcionar corretamente, escreva o seguinte:

    `oc describe daemonset omsagent`  

    e a saída deve assemelhar-se:

    ```
    [ocpadmin@khm-0 ~]$ oc describe ds oms  
    Name:           oms  
    Image(s):       microsoft/oms  
    Selector:       name=omsagent  
    Node-Selector:  zone=default  
    Labels:         agentVersion=1.4.0-12  
                    dockerProviderVersion=10.0.0-25  
                    name=omsagent  
    Desired Number of Nodes Scheduled: 3  
    Current Number of Nodes Scheduled: 3  
    Number of Nodes Misscheduled: 0  
    Pods Status:    3 Running / 0 Waiting / 0 Succeeded / 0 Failed  
    No events.  
    ```

Se pretender utilizar segredos para proteger o seu ID da área de trabalho OMS e a chave primária quando utilizar o ficheiro de conjunto de daemon yaml de agente do OMS, execute os seguintes passos.

1. Início de sessão para o nó mestre OpenShift e copie o ficheiro yaml [ocp-ds-omsagent.yaml](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-ds-omsagent.yaml) e o segredo do script de gerar [ocp-secretgen.sh](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-secretgen.sh) a partir do GitHub.  Este script irá gerar o ficheiro de yaml segredos para o ID da área de trabalho OMS e a chave primária proteger a sua secrete informações.  
2. Execute os seguintes comandos para criar um projeto para OMS e definir a conta de utilizador. O segredo gerar o script pede-lhe o ID da área de trabalho OMS <WSID> e a chave primária <KEY> e após a conclusão, cria o ficheiro ocp-secret.yaml.  

    ```
    oadm new-project omslogging --node-selector='zone=default'  
    oc project omslogging  
    oc create serviceaccount omsagent  
    oadm policy add-cluster-role-to-user cluster-reader   system:serviceaccount:omslogging:omsagent  
    oadm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent  
    ```

4. Implemente o ficheiro secreto executando o seguinte:

    `oc create -f ocp-secret.yaml`

5. Certifique-se a implementação, executando o seguinte:

    `oc describe secret omsagent-secret`  

    e a saída deve assemelhar-se:  

    ```
    [ocpadmin@khocp-master-0 ~]$ oc describe ds oms  
    Name:           oms  
    Image(s):       microsoft/oms  
    Selector:       name=omsagent  
    Node-Selector:  zone=default  
    Labels:         agentVersion=1.4.0-12  
                    dockerProviderVersion=10.0.0-25  
                    name=omsagent  
    Desired Number of Nodes Scheduled: 3  
    Current Number of Nodes Scheduled: 3  
    Number of Nodes Misscheduled: 0  
    Pods Status:    3 Running / 0 Waiting / 0 Succeeded / 0 Failed  
    No events.  
    ```

6. Implemente o ficheiro de conjunto de daemon yaml de agente do OMS, executando o seguinte:

    `oc create -f ocp-ds-omsagent.yaml`  

7. Certifique-se a implementação, executando o seguinte:

    `oc describe ds oms`

    e a saída deve assemelhar-se:

    ```
    [ocpadmin@khocp-master-0 ~]$ oc describe secret omsagent-secret  
    Name:           omsagent-secret  
    Namespace:      omslogging  
    Labels:         <none>  
    Annotations:    <none>  

    Type:   Opaque  

     Data  
     ====  
     KEY:    89 bytes  
     WSID:   37 bytes  
    ```

#### <a name="configure-an-oms-linux-agent-for-kubernetes"></a>Configurar um agente do OMS Linux para Kubernetes

Para Kubernetes, utilizar um script para gerar o ficheiro de yaml segredos para o seu ID da área de trabalho e a chave primária instalar o agente do OMS para Linux. Na [OMS Docker Kubernetes GitHub](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes) página, existem ficheiros que pode utilizar com ou sem as informações secretas.

- O agente do OMS predefinido para DaemonSet de Linux não tem informações secretas (omsagent.yaml)
- O agente do OMS para Linux DaemonSet yaml ficheiro utiliza informações secretas (omsagent-ds-secrets.yaml) com scripts de geração secreta para gerar o ficheiro de yaml (omsagentsecret.yaml) segredos.

Pode optar por criar omsagent DaemonSets com ou sem segredos.

**Ficheiro de yaml OMSagent DaemonSet predefinido sem segredos**

- Para o ficheiro de yaml do predefinido DaemonSet de agente do OMS, substitua o `<WSID>` e `<KEY>` aos seus WSID e a chave. Copie o ficheiro para o nó principal e execute o seguinte:

    ```
    sudo kubectl create -f omsagent.yaml
    ```

**Ficheiro de yaml OMSagent DaemonSet predefinido com segredos**

1. Para utilizar DaemonSet de agente do OMS utilizando as informações secretas, crie primeiro os segredos.
    1. Copie o script e o ficheiro de modelo secreta e certifique-se de que estão no mesmo diretório.
        - segredo gerar script - gen.sh segredo
        - modelo secreto - template.yaml segredo
    2. Execute o script, semelhante ao seguinte exemplo. O script pede-lhe o ID da área de trabalho OMS e a chave primária e depois de introduzi-los, o script cria um ficheiro de yaml secreta pelo pode executá-lo.   

        ```
        #> sudo bash ./secret-gen.sh
        ```

    3. Crie o pod segredos executando o seguinte:
        ```
        sudo kubectl create -f omsagentsecret.yaml
        ```

    4. Para verificar, execute o seguinte:

        ```
        keiko@ubuntu16-13db:~# sudo kubectl get secrets
        ```

        Saída deve assemelhar-se:

        ```
        NAME                  TYPE                                  DATA      AGE
        default-token-gvl91   kubernetes.io/service-account-token   3         50d
        omsagent-secret       Opaque                                2         1d
        ```

        ```
        keiko@ubuntu16-13db:~# sudo kubectl describe secrets omsagent-secret
        ```

        Saída deve assemelhar-se:

        ```
        Name:           omsagent-secret
        Namespace:      default
        Labels:         <none>
        Annotations:    <none>

        Type:   Opaque

        Data
        ====
        WSID:   36 bytes
        KEY:    88 bytes
        ```

    5. Criar a sua omsagent daemon-set executando``` sudo kubectl create -f omsagent-ds-secrets.yaml ```

2. Certifique-se de que o DaemonSet de agente do OMS está em execução, semelhante ao seguinte:

    ```
    keiko@ubuntu16-13db:~# sudo kubectl get ds omsagent
    ```

    ```
    NAME       DESIRED   CURRENT   NODE-SELECTOR   AGE
    omsagent   3         3         <none>          1h
    ```


Para Kubernetes, utilize um script para gerar o ficheiro de yaml segredos para o ID da área de trabalho e a chave primária para o agente do OMS para Linux. Utilize as seguintes informações de exemplo com o [omsagent yaml ficheiro](https://github.com/Microsoft/OMS-docker/blob/master/Kubernetes/omsagent.yaml) para proteger as informações secretas.

```
keiko@ubuntu16-13db:~# sudo kubectl describe secrets omsagent-secret
Name:           omsagent-secret
Namespace:      default
Labels:         <none>
Annotations:    <none>

Type:   Opaque

Data
====
WSID:   36 bytes
KEY:    88 bytes
```

#### <a name="configure-an-oms-agent-for-windows-kubernetes"></a>Configurar um agente do OMS para Kubernetes do Windows
Para Windows Kubernetes, utilizar um script para gerar o ficheiro de yaml segredos para o seu ID da área de trabalho e a chave primária instalar o agente do OMS. Na [OMS Docker Kubernetes GitHub](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes/windows) página, existem ficheiros que pode utilizar com as informações secretas.  Tem de instalar o agente do OMS separadamente para os nós mestre e o agente.  

1. Para utilizar DaemonSet de agente do OMS utilizando as informações secretas no mestre de nó, inicie sessão e crie primeiro os segredos.
    1. Copie o script e o ficheiro de modelo secreta e certifique-se de que estão no mesmo diretório.
        - segredo gerar script - gen.sh segredo
        - modelo secreto - template.yaml segredo

    2. Execute o script, semelhante ao seguinte exemplo. O script pede-lhe o ID da área de trabalho OMS e a chave primária e depois de introduzi-los, o script cria um ficheiro de yaml secreta pelo pode executá-lo.   

        ```
        #> sudo bash ./secret-gen.sh
        ```
    3. Criar a sua omsagent daemon-set executando``` kubectl create -f omsagentsecret.yaml ```
    4. Para verificar, execute o seguinte:
    
        ``` 
        root@ubuntu16-13db:~# kubectl get secrets
        ```

        Saída deve assemelhar-se:

        ```
        NAME                  TYPE                                  DATA      AGE
        default-token-gvl91   kubernetes.io/service-account-token   3         50d
        omsagent-secret       Opaque                                2         1d
        root@ubuntu16-13db:~# kubectl describe secrets omsagent-secret
        Name:           omsagent-secret
        Namespace:      default
        Labels:         <none>
        Annotations:    <none>
    
        Type:   Opaque
    
        Data
        ====
        WSID:   36 bytes
        KEY:    88 bytes 
        ```

    5. Criar a sua omsagent daemon-set executando```kubectl create -f ws-omsagent-de-secrets.yaml```

2. Certifique-se de que o DaemonSet de agente do OMS está em execução, semelhante ao seguinte:

    ```
    root@ubuntu16-13db:~# kubectl get deployment omsagent
    NAME       DESIRED   CURRENT   NODE-SELECTOR   AGE
    omsagent   1         1         <none>          1h
    ```

3. Para instalar o agente no nó de trabalho, que estão a executar o Windows, siga os passos na secção [instalar e configurar os anfitriões de contentor do Windows](#install-and-configure-windows-container-hosts). 

#### <a name="use-helm-to-deploy-oms-agent-on-linux-kubernetes"></a>Utilizar Helm para implementar o agente do OMS no Linux Kubernetes 
Para utilizar helm para implementar o agente do OMS no seu ambiente Linux Kubernetes, execute os seguintes passos.

1. Criar a sua omsagent daemon-set executando```helm install --name omsagent --set omsagent.secret.wsid=<WSID>,omsagent.secret.key=<KEY> stable/msoms```
2. Os resultados terá um aspeto semelhantes ao seguinte:

    ```
    NAME:   omsagent
    LAST DEPLOYED: Tue Sep 19 20:37:46 2017
    NAMESPACE: default
    STATUS: DEPLOYED

    RESOURCES:
    ==> v1/Secret
    NAME            TYPE    DATA  AGE
    omsagent-msoms  Opaque  3     3s

    ==> v1beta1/DaemonSet
    NAME            DESIRED  CURRENT  READY  UP-TO-DATE  AVAILABLE  NODE-SELECTOR  AGE
    omsagent-msoms  3        3        3      3           3          <none>         3s
    ```
3. Pode verificar o estado do omsagent executando: ```helm status "omsagent"``` e o resultado será semelhante ao seguinte:

    ```
    keiko@k8s-master-3814F33-0:~$ helm status omsagent
    LAST DEPLOYED: Tue Sep 19 20:37:46 2017
    NAMESPACE: default
    STATUS: DEPLOYED
 
    RESOURCES:
    ==> v1/Secret
    NAME            TYPE    DATA  AGE
    omsagent-msoms  Opaque  3     17m
 
    ==> v1beta1/DaemonSet
    NAME            DESIRED  CURRENT  READY  UP-TO-DATE  AVAILABLE  NODE-SELECTOR  AGE
    omsagent-msoms  3        3        3      3           3          <none>         17m
    ```
Para obter mais informações, visite [contentor solução Helm gráfico](https://aka.ms/omscontainerhelm).

### <a name="install-and-configure-windows-container-hosts"></a>Instalar e configurar os anfitriões de contentor do Windows

Utilize as informações na secção para instalar e configurar os anfitriões de contentor do Windows.

#### <a name="preparation-before-installing-windows-agents"></a>Preparação antes de instalar os agentes do Windows

Antes de instalar os agentes em computadores com Windows, terá de configurar o serviço de Docker. A configuração permite que o agente do Windows ou a extensão de máquina virtual de análise de registos para utilizar o socket de Docker TCP para que os agentes possam aceder remotamente o daemon de Docker e para capturar os dados para a monitorização.

##### <a name="to-start-docker-and-verify-its-configuration"></a>Para iniciar o Docker e verificar a respetiva configuração

Existem passos necessários para configurar o TCP pipe nomeado para o Windows Server:

1. No Windows PowerShell, ative o TCP pipe e pipe nomeado.

    ```
    Stop-Service docker
    dockerd --unregister-service
    dockerd --register-service -H npipe:// -H 0.0.0.0:2375  
    Start-Service docker
    ```

2. Configurar Docker com o ficheiro de configuração para o TCP pipe e pipe nomeado. O ficheiro de configuração está localizado em C:\ProgramData\docker\config\daemon.json.

    No ficheiro daemon.json, irá precisar do seguinte:

    ```
    {
    "hosts": ["tcp://0.0.0.0:2375", "npipe://"]
    }
    ```

Para obter mais informações sobre a configuração de daemon de Docker utilizada com contentores do Windows, consulte [motor de Docker no Windows](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon).


#### <a name="install-windows-agents"></a>Instalar agentes do Windows

Para ativar a monitorização de contentor do Windows e Hyper-V, instale o Microsoft Monitoring Agent (MMA) em computadores com Windows que os anfitriões de contentor. Para computadores com Windows no seu ambiente no local, consulte [computadores Windows ligar ao Log Analytics](log-analytics-windows-agents.md). Para máquinas virtuais em execução no Azure, ligue-a utilização de análise de registos a [extensão da máquina virtual](log-analytics-azure-vm-extension.md).

Pode monitorizar os contentores de Windows em execução no Service Fabric. No entanto, apenas [máquinas virtuais em execução no Azure](log-analytics-azure-vm-extension.md) e [computadores com Windows no seu ambiente no local](log-analytics-windows-agents.md) atualmente são suportadas para o Service Fabric.

Pode verificar que a solução de monitorização do contentor está corretamente definida para o Windows. Para verificar se o pacote de gestão foi transferência corretamente, procure *ContainerManagement.xxx*. Os ficheiros devem estar na pasta C:\Program Files\Microsoft monitorização Agent\Agent\Health serviço State\Management pacotes.


## <a name="solution-components"></a>Componentes da solução

Se estiver a utilizar o agentes do Windows, o seguinte pacote de gestão é instalado em cada computador com um agente quando adicionar esta solução. Nenhuma configuração ou a manutenção é necessária para o pacote de gestão.

- *ContainerManagement.xxx* instalado em C:\Program Files\Microsoft monitorização Agent\Agent\Health serviço State\Management pacotes

## <a name="container-data-collection-details"></a>Detalhes de recolha de dados do contentor
A solução de monitorização do contentor recolhe diversos dados de métricas e registo de desempenho dos anfitriões de contentor e contentores utilizando os agentes que ativar.

Dados são recolhidos a cada três minutos pelos seguintes tipos de agente.

- [Agente do OMS para Linux](log-analytics-linux-agents.md)
- [Agente do Windows](log-analytics-windows-agents.md)
- [Extensão de VM de análise do registo](log-analytics-azure-vm-extension.md)


### <a name="container-records"></a>Registos do contentor

A tabela seguinte mostra exemplos de registos recolhidos pela solução de monitorização do contentor e os tipos de dados que aparecem nos resultados de pesquisa de registo.

| Tipo de dados | Tipo de dados na pesquisa de registo | Campos |
| --- | --- | --- |
| Desempenho para os anfitriões e contentores | `Type=Perf` | Computador, ObjectName, CounterName &#40; de % de tempo do processador, MB de leituras de disco, disco escreve MB, MB de utilização de memória, rede receber Bytes, rede enviar Bytes, o processador de seg de utilização, rede &#41; CounterValue, TimeGenerated, CounterPath, SourceSystem |
| Inventário de contentor | `Type=ContainerInventory` | TimeGenerated, computador, nome do contentor, ContainerHostname, imagem, ImageTag, ContainerState, ExitCode, EnvironmentVar, comando, CreatedTime, StartedTime, FinishedTime, SourceSystem, ID do contentor, ImageID |
| Inventário de imagem do contentor | `Type=ContainerImageInventory` | TimeGenerated, computador, imagem, ImageTag, ImageSize, VirtualSize, em execução, em pausa, parar, falha, SourceSystem, ImageID, TotalContainer |
| Registo de contentor | `Type=ContainerLog` | TimeGenerated, o computador, o ID de imagem, o nome do contentor, LogEntrySource, LogEntry, SourceSystem, ID do contentor |
| Registo do serviço de contentor | `Type=ContainerServiceLog`  | TimeGenerated, computador, TimeOfCommand, imagem, comando, SourceSystem, ID do contentor |
| Inventário de nó do contentor | `Type=ContainerNodeInventory_CL`| TimeGenerated, computador, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Kubernetes inventário | `Type=KubePodInventory_CL` | TimeGenerated, computador, PodLabel_deployment_s, PodLabel_deploymentconfig_s, PodLabel_docker_registry_s, Name_s, Namespace_s, PodStatus_s, PodIp_s, PodUid_g, PodCreationTimeStamp_t, SourceSystem |
| Processo de contentor | `Type=ContainerProcess_CL` | TimeGenerated, computador, Pod_s, Namespace_s, ClassName_s, InstanceID_s, Uid_s, PID_s, PPID_s, C_s, STIME_s, Tty_s, TIME_s, Cmd_s, Id_s, Name_s, SourceSystem |
| Eventos de Kubernetes | `Type=KubeEvents_CL` | TimeGenerated, computador, Name_s, ObjectKind_s, Namespace_s, Reason_s, Type_s SourceComponent_s, SourceSystem, mensagem |

As etiquetas acrescentado ao *PodLabel* tipos de dados são as suas próprias etiquetas personalizadas. As etiquetas PodLabel anexadas mostradas na tabela são exemplos. Por isso, `PodLabel_deployment_s`, `PodLabel_deploymentconfig_s`, `PodLabel_docker_registry_s` irá diferem no conjunto de dados do seu ambiente e genericamente assemelhar-se `PodLabel_yourlabel_s`.


## <a name="monitor-containers"></a>Contentores de monitor
Depois de ter a solução ativada no portal do OMS, a **contentores** mosaico mostra informações de resumo sobre os anfitriões de contentor e os contentores em execução em anfitriões.

![Mosaico de contentores](./media/log-analytics-containers/containers-title.png)

O mosaico mostra uma descrição geral dos contentores quantos tiver o ambiente e se estiver a falha, em execução ou parado.

### <a name="using-the-containers-dashboard"></a>Utilizar o dashboard de contentores
Clique em de **contentores** mosaico. A partir daí, irá ver vistas organizadas por:

- **Eventos de contentor** -mostra computadores com contentores falha e estado do contentor.
- **Os registos do contentor** -mostra um gráfico dos ficheiros de registo do contentor gerados ao longo do tempo e uma lista de computadores com o maior número de ficheiros de registo.
- **Eventos de Kubernetes** -mostra um gráfico dos eventos de Kubernetes gerados ao longo do tempo e uma lista dos motivos por que motivo pods gerado os eventos. *Este conjunto de dados é utilizado apenas em ambientes de Linux.*
- **Inventário de espaço de nomes de Kubernetes** - mostra o número de espaços de nomes e pods e mostra a respetiva hierarquia. *Este conjunto de dados é utilizado apenas em ambientes de Linux.*
- **Inventário de nó do contentor** -mostra o número de tipos de orquestração utilizados no contentor nós/anfitriões. O computador nós/aloja também é listado pelo número de contentores. *Este conjunto de dados é utilizado apenas em ambientes de Linux.*
- **Inventário de imagens do contentor** -mostra o número total de imagens do contentor utilizado e o número de tipos de imagem. O número de imagens também é listado pela tag de imagem.
- **Estado de contentores** -mostra o número total do contentor de computadores de nós/anfitriões que têm de executar contentores. Computadores também são listados pelo número de anfitriões a executar.
- **Processo de contentor** -mostra um gráfico de linhas de processos de contentor com o ao longo do tempo. Contentores são também apresentados executando o comando/processe dentro de contentores. *Este conjunto de dados é utilizado apenas em ambientes de Linux.*
- **Desempenho de CPU do contentor** -mostra um gráfico de linhas de utilização da CPU média ao longo do tempo para computador nós/anfitriões. Também apresenta uma lista de nós/anfitriões do computador com base em média de utilização da CPU.
- **Desempenho de memória do contentor** -mostra um gráfico de linhas de utilização de memória ao longo do tempo. Também apresenta uma lista utilização com base no nome de instância de memória do computador.
- **Desempenho do computador** -mostra os gráficos de linhas de percentagem de desempenho da CPU ao longo do tempo, percentagem de utilização de memória ao longo do tempo e a quantidade de megabytes de espaço livre em disco ao longo do tempo. Pode paira o rato sobre qualquer linha de um gráfico para ver mais detalhes.


Cada área do dashboard é uma representação visual de uma pesquisa que é executada nos dados recolhidos.

![Dashboard de contentores](./media/log-analytics-containers/containers-dash01.png)

![Dashboard de contentores](./media/log-analytics-containers/containers-dash02.png)

No **contentor estado** área, clique na área superior, como mostrado abaixo.

![Estado de contentores](./media/log-analytics-containers/containers-status.png)

Abre a pesquisa de registo, que apresenta informações sobre o estado dos seus contentores.

![Pesquisa de registo para contentores](./media/log-analytics-containers/containers-log-search.png)

Aqui, pode editar a consulta de pesquisa para modificá-la para encontrar as informações específicas que estiver interessado em. Para obter mais informações sobre pesquisas de registo, consulte [pesquisas de registo na análise de registos](log-analytics-log-searches.md).

## <a name="troubleshoot-by-finding-a-failed-container"></a>Resolver problemas ao localizar um contentor com falhas

Análise de registos marca um contentor como **falha** se saiu com um código de saída diferente de zero. Pode ver uma descrição geral dos erros e falhas no ambiente no **falha contentores** área.

### <a name="to-find-failed-containers"></a>Para localizar falha contentores
1. Clique em de **contentor estado** área.  
   ![Estado de contentores](./media/log-analytics-containers/containers-status.png)
2. Pesquisa de registo abre e apresenta o estado dos seus contentores, semelhantes ao seguinte.  
   ![Estado de contentores](./media/log-analytics-containers/containers-log-search.png)
3. Em seguida, clique no valor de agregados de contentores com falhas para ver informações adicionais. Expanda **mostrar mais** para ver o ID de imagem.  
   ![Falha de contentores](./media/log-analytics-containers/containers-state-failed.png)  
4. Em seguida, escreva o seguinte na consulta de pesquisa. `Type=ContainerInventory <ImageID>`Para ver detalhes sobre a imagem, tais como o tamanho da imagem e o número de imagens parados e falha.  
   ![Falha de contentores](./media/log-analytics-containers/containers-failed04.png)

## <a name="search-logs-for-container-data"></a>Registos de pesquisa para dados de contentor
Quando estiver a resolver problemas com um erro específico, pode ajudar a ver onde está a ocorrer no seu ambiente. Os seguintes tipos de registo irão ajudá-lo a criar consultas para devolver as informações que pretende.


- **ContainerImageInventory** – utilizar este tipo quando está a tentar encontrar informações de organizados por imagem e para ver as informações da imagem como imagem IDs ou tamanhos.
- **ContainerInventory** – utilizar este tipo quando pretender obter informações sobre a localização do contentor, quais são os respetivos nomes e que as imagens que estiver a executar.
- **ContainerLog** – utilizar este tipo quando pretende localizar informações de registo de erro específico e entradas.
- **ContainerNodeInventory_CL** utilizar este tipo quando pretender que as informações sobre o nó/anfitrião onde contentores são que reside. Fornece a versão do Docker, tipo de orquestração, armazenamento e informações de rede.
- **ContainerProcess_CL** Utilize este tipo para ver rapidamente o processo em execução no contentor.
- **ContainerServiceLog** – utilizar este tipo quando está a tentar encontrar as informações de registo de auditoria para o daemon de Docker, tais como iniciar, parar, eliminar ou comandos de extração.
- **KubeEvents_CL** utilizar este tipo para ver os eventos de Kubernetes.
- **KubePodInventory_CL** utilizar este tipo quando pretende compreender as informações da hierarquia de cluster.


### <a name="to-search-logs-for-container-data"></a>Para procurar os registos de dados de contentor
* Escolha uma imagem que sabe que falhou recentemente e localize os registos de erros para a mesma. Comece por localizar um nome de contentor que está a executar essa imagem com um **ContainerInventory** pesquisa. Por exemplo, procure`Type=ContainerInventory ubuntu Failed`  
    ![Procurar contentores Ubuntu](./media/log-analytics-containers/search-ubuntu.png)

  O nome do contentor junto a **nome**e procure esses registos. Neste exemplo, é `Type=ContainerLog cranky_stonebreaker`.

**Ver informações de desempenho**

Quando estiver a começar a construir consultas, pode ajudar a ver o que é possível primeiro. Por exemplo, para ver todos os dados de desempenho, experimente uma consulta abrangente, escrevendo a seguinte consulta de pesquisa.

```
Type=Perf
```

![desempenho de contentores](./media/log-analytics-containers/containers-perf01.png)

Pode analisar os dados de desempenho que está a ver para um contentor específico, escrevendo o nome do mesmo para a direita da sua consulta.

```
Type=Perf <containerName>
```

Que mostra a lista de métricas de desempenho que são recolhidas para um contentor individuais.

![desempenho de contentores](./media/log-analytics-containers/containers-perf03.png)

## <a name="example-log-search-queries"></a>Consultas de pesquisa de registo de exemplo
Muitas vezes, é útil criar consultas começando com um exemplo ou dois e, em seguida, modificá-los para que se adeque ao seu ambiente. Como um ponto de partida, pode experimentar o **consultas de exemplo** área para o ajudar a criar consultas mais avançadas.

[!include[log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

![Consultas de contentores](./media/log-analytics-containers/containers-queries.png)


## <a name="saving-log-search-queries"></a>Guardar registo de consultas de pesquisa
Guardar consultas é uma funcionalidade padrão na análise de registos. Ao guardá-las, terá de os que encontrou útil útil para utilização futura.

Depois de criar uma consulta que úteis, guarde-o ao clicar em **Favoritos** na parte superior da página pesquisa de registo. Em seguida, pode aceder facilmente mais tarde a partir de **Dashboard os meus** página.

## <a name="next-steps"></a>Passos seguintes
* [Pesquisar registos](log-analytics-log-searches.md) para ver registos de dados do contentor de detalhado.
