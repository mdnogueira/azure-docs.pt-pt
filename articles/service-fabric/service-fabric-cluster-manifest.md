---
title: "Configurar o cluster de autónoma do Service Fabric do Azure | Microsoft Docs"
description: "Saiba como configurar o cluster de Service Fabric do Azure autónomo ou no local."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 0c5ec720-8f70-40bd-9f86-cd07b84a219d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: dc17ba7f8cc1326790b0256de277ccb2eaa20949
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2017
---
# <a name="configuration-settings-for-a-standalone-windows-cluster"></a>Definições de configuração para um cluster do Windows autónomo
Este artigo descreve como configurar um cluster do Azure Service Fabric autónoma, utilizando o ficheiro Clusterconfig. Pode utilizar este ficheiro para especificar informações tais como os nós de Service Fabric e os respetivos endereços IP e os diferentes tipos de nós no cluster. Também pode especificar configurações de segurança, bem como a topologia de rede em termos de domínios de falhas/atualização para o seu cluster autónomo.

Quando lhe [transferir o pacote de Service Fabric autónomo](service-fabric-cluster-creation-for-windows-server.md#downloadpackage), alguns exemplos do ficheiro Clusterconfig são transferidos para o seu computador de trabalho. Os exemplos que tenham DevCluster nos respetivos nomes de ajudam a criar um cluster com todos os três nós no mesmo computador, como lógicas nós. Fora em nós, pelo menos um pode estar marcado como um nó principal. Este cluster é útil para um ambiente de desenvolvimento ou teste. Não é suportado como um cluster de produção. Os exemplos que tenham MultiMachine nos respetivos nomes de ajudam a criar um cluster de qualidade de produção, com cada nó num computador separado. O número de nós principais para esses clusters baseia-se no [nível de fiabilidade](#reliability). Versão 5.7 versão da API 05-2017, iremos remover a propriedade de nível de fiabilidade. Em vez disso, o nosso código calcula o nível de fiabilidade mais otimizado para o cluster. Não utilize esta propriedade em versões de código 5.7 e posterior.


* ClusterConfig.Unsecure.DevCluster.JSON e ClusterConfig.Unsecure.MultiMachine.JSON mostram como criar um teste protegida ou o cluster de produção, respetivamente.

* ClusterConfig.Windows.DevCluster.JSON e ClusterConfig.Windows.MultiMachine.JSON mostram como criar clusters de teste ou de produção que estejam protegidos utilizando [segurança do Windows](service-fabric-windows-cluster-windows-security.md).

* ClusterConfig.X509.DevCluster.JSON e ClusterConfig.X509.MultiMachine.JSON mostram como criar clusters de teste ou de produção que estejam protegidos utilizando [X509 segurança baseada em certificado](service-fabric-windows-cluster-x509-security.md).

Agora vamos examinar as diversas secções de um ficheiro de Clusterconfig.

## <a name="general-cluster-configurations"></a>Configurações de cluster geral
Configurações de cluster geral abrangem abrangentes configurações de cluster específicos, conforme ilustrado no fragmento JSON seguinte:

    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "01-2017",

Pode dar um nome amigável ao cluster do Service Fabric atribuindo-lo para a variável de nome. O clusterConfigurationVersion é o número de versão do cluster. Aumente demasiado. sempre que atualizar o seu cluster do Service Fabric. Deixe apiVersion conjunto para o valor predefinido.

    <a id="clusternodes"></a>

## <a name="nodes-on-the-cluster"></a>Nós no cluster
Pode configurar os nós no cluster do Service Fabric, utilizando a secção de nós, como o fragmento seguinte mostra:

    "nodes": [{
        "nodeName": "vm0",
        "iPAddress": "localhost",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r0",
        "upgradeDomain": "UD0"
    }, {
        "nodeName": "vm1",
        "iPAddress": "localhost",
        "nodeTypeRef": "NodeType1",
        "faultDomain": "fd:/dc1/r1",
        "upgradeDomain": "UD1"
    }, {
        "nodeName": "vm2",
        "iPAddress": "localhost",
        "nodeTypeRef": "NodeType2",
        "faultDomain": "fd:/dc1/r2",
        "upgradeDomain": "UD2"
    }],

Um cluster do Service Fabric tem de conter, pelo menos, três nós. Pode adicionar mais nós nesta secção, de acordo com a configuração. A tabela seguinte explica as definições de configuração para cada nó:

| **Configuração do nó** | **Descrição** |
| --- | --- |
| nodeName |Pode dar um nome amigável para o nó. |
| IPAddress |Determinar o endereço IP do seu nó, ao abrir uma janela de comandos e digitar `ipconfig`. Tenha em atenção o endereço IPV4 e atribua-a para a variável de iPAddress. |
| nodeTypeRef |Cada nó pode ser atribuído um tipo de nó diferente. O [tipos de nó](#node-types) são definidas na secção seguinte. |
| faultDomain |Domínios de falhas permitem que os administradores de cluster definir os nós físicos que poderão falhar ao mesmo tempo devido a dependências físicas partilhadas. |
| upgradeDomain |Domínios de atualização descrevem conjuntos de nós que são desligados para atualizações de Service Fabric em sobre o mesmo tempo. Pode escolher quais os nós para atribuir os domínios de atualização, porque estes não são estar limitados pelos requisitos físicos. |

## <a name="cluster-properties"></a>Propriedades do cluster
A secção de propriedades de Clusterconfig é utilizada para configurar o cluster, conforme mostrado:

    <a id="reliability"></a>

### <a name="reliability"></a>Fiabilidade
O conceito de reliabilityLevel define o número de réplicas ou instâncias dos serviços de sistema do Service Fabric que podem ser executadas em nós principais do cluster. Determina a fiabilidade destes serviços e, por conseguinte, o cluster. O valor é calculado pelo sistema no momento de criação e a atualização do cluster.

### <a name="diagnostics"></a>Diagnóstico
Na secção diagnosticsStore, pode configurar os parâmetros para ativar os diagnósticos e resolução de falhas de cluster ou nó, conforme mostrado no seguinte fragmento: 

    "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "FileShare",
        "IsEncrypted": "false",
        "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
    }

Os metadados é uma descrição da sua diagnósticos de cluster e podem ser definido de acordo com a configuração. Estas variáveis de ajudam a recolher registos de rastreio ETW e informações de falhas, bem como os contadores de desempenho. Para obter mais informações sobre os registos de rastreio ETW, consulte [registo de rastreio](https://msdn.microsoft.com/library/windows/hardware/ff552994.aspx) e [rastreio ETW](https://msdn.microsoft.com/library/ms751538.aspx). Todos os registos, incluindo [capturas de falhas](https://blogs.technet.microsoft.com/askperf/2008/01/08/understanding-crash-dump-files/) e [contadores de desempenho](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx), pode ser direcionado para a pasta de connectionString no seu computador. Também pode utilizar AzureStorage para armazenar o diagnóstico. Consulte o seguinte fragmento de exemplo:

    "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "AzureStorage",
        "IsEncrypted": "false",
        "connectionstring": "xstore:DefaultEndpointsProtocol=https;AccountName=[AzureAccountName];AccountKey=[AzureAccountKey]"
    }

### <a name="security"></a>Segurança
A secção de segurança é necessária para um cluster de Service Fabric autónomo segura. O fragmento seguinte mostra uma parte desta secção:

    "security": {
        "metadata": "This cluster is secured using X509 certificates.",
        "ClusterCredentialType": "X509",
        "ServerCredentialType": "X509",
        . . .
    }

Os metadados é uma descrição do seu cluster segura e podem ser definido de acordo com a configuração. O ClusterCredentialType e ServerCredentialType determinam o tipo de segurança que implementam o cluster e os nós. Podem ser definidas para o *X509* para uma segurança baseada em certificado ou *Windows* para segurança baseada no Azure Active Directory. O resto da secção de segurança baseia-se no tipo de segurança. Para obter informações sobre a preencher o resto da secção de segurança, consulte [segurança baseada em certificados num cluster autónomo](service-fabric-windows-cluster-x509-security.md) ou [segurança do Windows num cluster autónomo](service-fabric-windows-cluster-windows-security.md).

    <a id="nodetypes"></a>

### <a name="node-types"></a>Tipos de nó
A secção de nodeTypes descreve o tipo de nós que tenha o seu cluster. Pelo menos um tipo de nó tem de ser especificado para um cluster, conforme mostrado no seguinte fragmento: 

    "nodeTypes": [{
        "name": "NodeType0",
        "clientConnectionEndpointPort": "19000",
        "clusterConnectionEndpointPort": "19001",
        "leaseDriverEndpointPort": "19002"
        "serviceConnectionEndpointPort": "19003",
        "httpGatewayEndpointPort": "19080",
        "reverseProxyEndpointPort": "19081",
        "applicationPorts": {
            "startPort": "20575",
            "endPort": "20605"
        },
        "ephemeralPorts": {
            "startPort": "20606",
            "endPort": "20861"
        },
        "isPrimary": true
    }]

O nome é o nome amigável para este tipo de nó específico. Para criar um nó deste tipo de nó, atribua o nome amigável para a variável de nodeTypeRef desse nó, como [mencionado anteriormente](#nodes-on-the-cluster). Para cada tipo de nó, defina os pontos finais de ligação que são utilizados. Pode escolher qualquer número de porta para estes pontos finais da ligação, desde que não estão em conflito com quaisquer outros pontos finais neste cluster. Num cluster de vários nós, existem um ou mais nós primárias (ou seja, isPrimary está definido como *verdadeiro*), consoante o [reliabilityLevel](#reliability). Para saber mais sobre os tipos de nó primário e nonprimary, consulte [considerações de planeamento de capacidade do cluster de Service Fabric](service-fabric-cluster-capacity.md) para obter informações sobre nodeTypes e reliabilityLevel. 

#### <a name="endpoints-used-to-configure-the-node-types"></a>Pontos finais utilizados para configurar os tipos de nó
* clientConnectionEndpointPort é a porta utilizada pelo cliente para ligar ao cluster quando são utilizadas APIs do cliente. 
* clusterConnectionEndpointPort é a porta em que os nós de comunicarem entre si.
* leaseDriverEndpointPort é a porta utilizada pelo controlador de concessão de cluster para saber se os nós ainda se encontram ativos. 
* serviceConnectionEndpointPort é a porta utilizada pelas aplicações e serviços implementados num nó para comunicar com o cliente de Service Fabric nesse nó específico.
* httpGatewayEndpointPort é a porta utilizada pelo Explorador de recursos de infraestrutura de serviço para ligar ao cluster.
* Substituir ephemeralPorts o [portas dinâmicas utilizadas pelo sistema operativo](https://support.microsoft.com/kb/929851). O Service Fabric utiliza estas portas parte como portas de aplicação e o restante estão disponíveis para o SO. -Também mapeia este intervalo para o intervalo existente presente no sistema operativo, pelo para todos os fins, que pode utilizar os intervalos indicados nos ficheiros de JSON de exemplo. Certifique-se de que a diferença entre o início e as portas de fim é pelo menos de 255. Poderá executar para conflitos se esta diferença está demasiado baixa, porque este intervalo está partilhado com o SO. Para ver o intervalo de porta dinâmica configurada, execute `netsh int ipv4 show dynamicport tcp`.
* applicationPorts são as portas que são utilizadas pelas aplicações de Service Fabric. O intervalo de portas de aplicação deve ser suficientemente grande para cobrir o requisito de ponto final das suas aplicações. Este intervalo deve ser exclusivo do intervalo de porta dinâmica na máquina, ou seja, o intervalo de ephemeralPorts conforme definido na configuração. Service Fabric utiliza estas portas sempre que as novas portas são necessárias e encarrega-se de abrir a firewall para estas portas. 
* reverseProxyEndpointPort é um ponto final opcionais de proxy inverso. Para obter mais informações, consulte [Service Fabric proxy inverso](service-fabric-reverseproxy.md). 

### <a name="log-settings"></a>Definições de registo
Na secção fabricSettings, pode definir os diretórios raiz para os registos e dados do Service Fabric. Pode personalizar estas diretórios apenas durante a criação de cluster inicial. Consulte o seguinte fragmento de exemplo desta secção:

    "fabricSettings": [{
        "name": "Setup",
        "parameters": [{
            "name": "FabricDataRoot",
            "value": "C:\\ProgramData\\SF"
        }, {
            "name": "FabricLogRoot",
            "value": "C:\\ProgramData\\SF\\Log"
    }]

Recomendamos que utilize uma unidade de SO não como a variável FabricDataRoot e FabricLogRoot. Fornece mais fiabilidade no evitando situações quando o SO deixa de responder. Se personalizar apenas a raiz dos dados, a raiz de registo é colocada um nível abaixo da raiz de dados.

### <a name="stateful-reliable-services-settings"></a>Definições de Reliable Services com monitorização de estado
Na secção KtlLogger, pode configurar as definições de configuração global para Reliable Services. Para obter mais informações sobre estas definições, consulte [configurar monitorização de estado Reliable Services](service-fabric-reliable-services-configuration.md). O exemplo seguinte mostra como alterar o registo de transações partilhado que é criado para fazer uma cópia de quaisquer coleções fiáveis para serviços com monitorização de estado:

    "fabricSettings": [{
        "name": "KtlLogger",
        "parameters": [{
            "name": "SharedLogSizeInMB",
            "value": "4096"
        }]
    }]

### <a name="add-on-features"></a>Funcionalidades de suplemento
Para configurar funcionalidades de suplemento, configure o apiVersion como 04 2017 ou superior e configure o addonFeatures conforme mostrado aqui:

    "apiVersion": "04-2017",
    "properties": {
      "addOnFeatures": [
          "DnsService",
          "RepairManager"
      ]
    }

### <a name="container-support"></a>Suporte de contentor
Para ativar o suporte de contentor de contentores do Windows Server e contentores de Hyper-V para clusters autónomos, a funcionalidade de suplemento DnsService tem de estar ativada.


## <a name="next-steps"></a>Passos seguintes
Depois de ter um ficheiro Clusterconfig completo configurado, de acordo com a configuração de cluster autónomo, pode implementar o cluster. Siga os passos no [criar um cluster do Service Fabric autónomo](service-fabric-cluster-creation-for-windows-server.md). Em seguida, avance para [visualizar o cluster com o Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) e siga os passos.

