---
title: "Como dimensionar um cluster do serviço de contentor do Azure Machine Learning | Microsoft Docs"
description: "Dimensionamento de um cluster de ACS - dimensionamento automático e dimensionamento estático; dimensionamento do número de nós no cluster"
services: machine-learning
author: raymondl
ms.author: raymondl
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 10/04/2017
ms.openlocfilehash: 44aa167375355433851453010cebe5b49ef56ebd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="scaling-the-cluster-to-manage-web-service-throughput"></a>Dimensionar o cluster para gerir o débito de serviço web

## <a name="why-scale-the-cluster"></a>Dimensionar por que razão o cluster?

Dimensionar o cluster do serviço de contentor do Azure (ACS) é uma forma eficaz para otimizar o débito de serviço, mantendo o tamanho do cluster a um mínimo de reduzir o custo. 

Para compreender melhor o dimensionamento automático, considere o seguinte exemplo de um cluster com os três serviços web:

![Exemplo: Três serviços num cluster](media/how-to-scale-clusters/three-services.png)

Os serviços têm vários pedidos de pico: serviço 1 (azul linha) necessita de 40 pods em picos de procura, 38 no pico necessita de serviço 2 (linha laranja) e 3 de serviço (cinzenta linha) exige que 50 das horas de ponta. Se a capacidade de reserva de pico necessários para cada serviço individualmente, este cluster precisaria de, pelo menos, 40 + 38 + 50 = 128 pods no total.

Mas, considere a utilização real pod em qualquer ponto no tempo, representado pela linha tracejada preta no gráfico. Neste caso, o *número mais elevado de pods utilizadas num dado momento* é 64, que ocorre no 20:00 quando Service 3 é das horas de ponta. Neste momento, Service 3 utiliza 50 pods, mas Service 2 utiliza apenas 9 pods e Service 1 utiliza apenas 5. Lembre-se de que este é *utilização máxima* para este cluster. Isto significa que nenhum momento cluster utiliza mais do que 64 pods – meio o requisito calculado de 128 pods para os três serviços dimensionados de forma independente para utilização máxima.

Através da reatribuição pods de cluster – isto é, por rescaling – para satisfazer a procura atual de cada serviço, em vez de exigir simplesmente recursos suficientes para os picos de procura de todos os serviços, pode diminuir o tamanho de cluster. Neste exemplo simples, o dimensionamento automático diminui o número necessário de pods de 128 para 64, ao reduzir o tamanho de cluster necessários na metade.

Dimensionamento do número de pods é uma operação relativamente rápida, que necessitam de menos de um minuto, pelo que a capacidade de resposta do serviço é importante que não é afetada.

> [!NOTE]
> Dimensionamento de um cluster não ajuda com problemas de latência de pedido. Para efeitos de operationalization, o como aumentar verticalmente deve aumentar o número de êxitos e diminuir erros serviço indisponível. 

## <a name="how-to-scale-web-services-on-your-acs-cluster"></a>Como serviços web de escala no seu cluster de ACS

A opção de configuração de cluster de gestão de modelo CLI por predefinição configura dois agentes e um pod no seu ambiente. Instala também o CLI Kubernetes.

Pode dimensionar os serviços web que implementou para ACS ao ajustar:

* O número de nós de agente do cluster
* O número de réplicas de pod Kubernetes em execução em nós de agente

### <a name="scaling-the-number-of-nodes-in-the-cluster"></a>Dimensionamento do número de nós no cluster

O comando seguinte define o número de nós de agente no cluster:

```
az acs scale -g <resource group> -n <cluster name> --new-agent-count <new scale>
```

Esta ação irá demorar alguns minutos a concluir. Para obter mais informações sobre como aumentar o número de nós no cluster, consulte [dimensionar a nós de agente de um cluster do serviço de contentor](https://docs.microsoft.com/en-us/azure/container-service/container-service-scale).

### <a name="scaling-the-number-of-kubernetes-pod-replicas-in-a-cluster"></a>Dimensionamento do número de réplicas de pod Kubernetes num cluster
 
Pode dimensionar o número de réplicas pod atribuído ao cluster utilizando a CLI do Azure Machine Learning ou [Kubernetes dashboard] (https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/).

Para obter mais informações sobre Kubernetes pods de réplica, consulte o [Kubernetes Pods](https://kubernetes.io/docs/concepts/workloads/pods/pod/) documentação.

#### <a name="scaling-a-cluster-with-the-azure-machine-learning-cli"></a>Dimensionamento de um cluster com a CLI do Azure Machine Learning

Existem duas formas de um cluster utilizando a CLI de escala:

- Dimensionamento Automático
- Escala estática

Dimensionamento automático está ativo por predefinição, quando o serviço é criado e, na maioria das situações é o método preferencial de dimensionamento.

##### <a name="autoscale"></a>Dimensionamento Automático

O seguinte comando permite o dimensionamento automático e define o número mínimo e máximo de réplicas para o serviço.

```
az ml service update realtime -i <service id> --autoscale-enabled true --autoscale-min-replicas <positive number> --autoscale-max-replicas <positive number>
```

Por exemplo, definir `autoscale-min-replicas` 5 criará cinco réplicas. Para chegam a um número ideal para o serviço web, defina o número de valores, tais como 10 e monitorizar o número de 503 mensagens de erro. Em seguida, ajuste o número em conformidade.


| Nome do parâmetro | Tipo | Descrição |
|--------------------|--------------------|--------------------|
| `autoscale-enabled` | Valor booleano | Especifica se o dimensionamento automático está ativado. Predefinição: VERDADEIRO |
| `autoscale-min-replicas` | número inteiro | Especifica o número mínimo de pods. Tem de ser maior ou igual a 0. Predefinição: 1 |
| `autoscale-max-replicas` | número inteiro | Especifica o número máximo de pods. Tem de ser 1 ou superior. Se réplicas de máx. de dimensionamento automático é inferior ao mínimo-réplicas de dimensionamento automático, serão ignoradas réplicas de máx. de dimensionamento automático. Predefinição: 10 |
| `autoscale-refresh-period-seconds` | número inteiro | Especifica a duração em segundos, entre as atualizações de dimensionamento automático. Predefinição: 1 |
| `autoscale-target-utilization` | número inteiro | Especifica a percentagem de utilização que destinos de dimensionamento automático, entre 1 e 100. Predefinição: 70 |

Dimensionamento automático funciona para garantir que as duas condições seguintes:

1. A utilização de destino é cumprida
2. Dimensionamento nunca excede as definições de mínimas e máxima

Serviços de um cluster competem ter para os recursos do cluster. Um serviço dimensionado automaticamente irá aumentar a sua utilização de recursos de cluster como respetivos pedidos por segundo (RPS) aumenta e lentamente será libertar recursos como os diminuições RPS. Serão possível adquirir os recursos do cluster a pedido, desde que esses recursos existem para o serviço ao adquirir.

Para obter mais informações sobre como utilizar os parâmetros de dimensionamento automático, consulte o [referência de Interface de linha de comandos de gestão de modelo](model-management-cli-reference.md) documentação.

##### <a name="static-scale"></a>Escala estática

Em geral, dimensionamento estático deve ser evitada, uma vez que não permite a redução do tamanho de cluster de dimensionamento automático. Ainda assim, em algumas situações dimensionamento estático pode ser aconselhado. Por exemplo, quando um cluster se encontra dedicado para um único serviço, o dimensionamento automático não fornece existir nenhuma vantagem; todos os recursos de cluster devem ser atribuídos a esse serviço.

Para um cluster de escala estaticamente, dimensionamento automático deve estar desativado. Desative o dimensionamento automático com o seguinte comando:

```
az ml service update realtime -i <service id> --autoscale-enabled false
```

Depois de desativar o dimensionamento automático, o seguinte comando diretamente ajusta o número de réplicas para um serviço.

```
az ml service update realtime -i <service id> -z <replica count>
```
 
Para obter mais informações sobre como aumentar o número de nós no cluster, consulte nós de agente de dimensionamento de um cluster do serviço de contentor.

#### <a name="scaling-number-of-replicas-using-the-kubernetes-dashboard"></a>Número de dimensionamento de réplicas utilizando o dashboard Kubernetes

Na linha de comandos, introduza:

```
kubectl proxy
```

No Windows, a localização de instalação Kubernetes não é automaticamente adicionada ao caminho. Primeiro, navegue para a pasta de instalação:

```
c:\users\<user name>\bin
```

Depois de executar o comando, deverá ver a seguinte mensagem informativa:

```
Starting to serve on 127.0.0.1:8001
```

Se a porta já está a ser utilizado, verá uma mensagem semelhante ao seguinte exemplo:

```
F0612 21:49:22.459111   59621 proxy.go:137] listen tcp 127.0.0.1:8001: bind: address already in use
```

Pode especificar uma porta alternativa número utilizando o *-porta* parâmetro.

```
kubectl proxy --port=8010
Starting to serve on 127.0.0.1:8010
```

Depois de iniciar o servidor de dashboard, abra um browser e introduza o seguinte URL:

```
127.0.0.1:<port number>/ui
```

No ecrã principal do dashboard, clique em **implementações** na barra de navegação esquerdo. Se não for apresentado o painel de navegação, selecione este ícone ![Menu consiste em três linhas horizontais curtas](media/how-to-scale-clusters/icon-hamburger.png) no canto superior esquerdo.

Localize a implementação para modificar e clique neste ícone ![ícone de Menu consiste em três pontos verticais](media/how-to-scale-clusters/icon-kebab.png) à direita e, em seguida, clique em **vista/edi YAML**.

No ecrã de implementação de edição, localize o *spec* nó, modifique o *réplicas* valor e clique em **atualização**.
