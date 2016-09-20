<properties
   pageTitle="Gestão de contentores do Serviço de Contentor do Azure através da API REST | Microsoft Azure"
   description="Implemente contentores num cluster Mesos do Serviço de Contentor do Azure utilizando a API REST do Marathon."
   services="container-service"
   documentationCenter=""
   authors="neilpeterson"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, Contentores, Microserviços, Mesos, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/13/2016"
   ms.author="nepeters"/>

# Gestão de contentores através da API REST

O DC/OS fornece um ambiente para implementação e dimensionamento de cargas de trabalho em cluster, abstraindo o hardware subjacente. Na parte superior do DC/OS, existe uma estrutura que gere o agendamento e a execução de cargas de trabalho de computação.

Embora as estruturas estejam disponíveis para muitas cargas de trabalho populares, este documento descreve como pode criar e dimensionar implementações de contentores utilizando o Marathon. Antes de avançarmos, necessita de um cluster DC/OS configurado no Serviço de Contentor do Azure. Também necessita de conectividade remota a este cluster. Para obter mais informações sobre estes itens, consulte os artigos seguintes:

- [Implementar um cluster do Serviço de Contentor do Azure](container-service-deployment.md)
- [Ligar a um cluster do Serviço de Contentor do Azure](container-service-connect.md)

Depois de ligar ao cluster do Serviço de Contentor do Azure, pode aceder ao DC/OS e às APIs REST relacionadas através de http://localhost:local-port. Os exemplos neste documento partem do princípio de que está a utilizar um túnel na porta 80. Por exemplo, o ponto final do Marathon pode ser obtido em `http://localhost/marathon/v2/`. Para obter mais informações sobre as diversas APIs, consulte a documentação do Mesosphere da [API do Marathon](https://mesosphere.github.io/marathon/docs/rest-api.html) e da [API do Chronos](https://mesos.github.io/chronos/docs/api.html), bem como a documentação do Apache da [API do Mesos Scheduler](http://mesos.apache.org/documentation/latest/scheduler-http-api/).

## Reunir informações do DC/OS e do Marathon

Antes de implementar os contentores no cluster DC/OS, recolha algumas informações sobre o cluster DC/OS, tais como os nomes e o estado atual dos agentes DC/OS. Para tal, consulte o ponto final `master/slaves` da API REST do DC/OS. Se tudo correr bem, verá uma lista de agentes DC/OS e as diversas propriedades de cada um.

```bash
curl http://localhost/mesos/master/slaves
```

Agora, utilize o ponto final `/apps` do Marathon para verificar a existência de implementações de aplicações atuais no cluster DC/OS. Se for um cluster novo, vê uma matriz de aplicações vazia.

```
curl localhost/marathon/v2/apps

{"apps":[]}
```

## Implementar um contentor formatado para Docker

Os contentores formatados para Docker são implementados através do Marathon utilizando um ficheiro JSON que descreve a implementação pretendida. O exemplo seguinte implementa o contentor Nginx, a porta de enlace 80 do agente DC/OS para a porta 80 do contentor. Note também que a propriedade “acceptedResourceRoles” está definida para “slave_public”. Isto permite implementar o contentor num agente no conjunto de dimensionamento do agente destinado ao público.

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 16.0,
  "instances": 1,
    "acceptedResourceRoles": [
    "slave_public"
  ],
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        { "containerPort": 80, "hostPort": 80, "servicePort": 9000, "protocol": "tcp" }
      ]
    }
  }
}
```

Para poder implementar um contentor formatado para Docker, crie o seu próprio ficheiro JSON ou utilize o exemplo fornecido na [demonstração do Serviço de Contentor do Azure](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/marathon/marathon.json). Armazene-o numa localização acessível. Em seguida, para implementar o contentor, execute o comando seguinte. Especifique o nome do ficheiro JSON.

```
curl -X POST http://localhost/marathon/v2/apps -d @marathon.json -H "Content-type: application/json"
```

O resultado será semelhante ao seguinte:

```json
{"version":"2015-11-20T18:59:00.494Z","deploymentId":"b12f8a73-f56a-4eb1-9375-4ac026d6cdec"}
```

Agora, se consultar o Marathon quanto a aplicações, esta nova aplicação será apresentada no resultado.

```
curl localhost/marathon/v2/apps
```

## Dimensionar os contentores

Também pode utilizar a API do Marathon para aumentar ou reduzir horizontalmente em implementações de aplicações. No exemplo anterior, foi implementada uma instância de uma aplicação. Vamos aumentar horizontalmente para três instâncias de uma aplicação. Para tal, crie um ficheiro JSON utilizando o seguinte texto JSON e armazene-o numa localização acessível.

```json
{ "instances": 3 }
```

Execute o comando seguinte para aumentar horizontalmente a aplicação.

>[AZURE.NOTE] O URI será http://localhost/marathon/v2/apps/ e, em seguida, o ID da aplicação a dimensionar. Se estiver a utilizar o exemplo Nginx facultado aqui, o URI será http://localhost/marathon/v2/apps/nginx.

```json
curl http://localhost/marathon/v2/apps/nginx -H "Content-type: application/json" -X PUT -d @scale.json
```

Por fim, consulte o ponto final do Marathon quanto a aplicações. Verá que existem agora três contentores Nginx.

```
curl localhost/marathon/v2/apps
```

## Utilizar o PowerShell para este exercício: interação da API REST do Marathon com o PowerShell

Pode efetuar estas mesmas ações utilizando comandos do PowerShell num sistema Windows.

Para recolher informações sobre o cluster DC/OS, tais como os nomes de agente e o estado do agente, execute o comando seguinte.

```powershell
Invoke-WebRequest -Uri http://localhost/mesos/master/slaves
```

Os contentores formatados para Docker são implementados através do Marathon utilizando um ficheiro JSON que descreve a implementação pretendida. O exemplo seguinte implementa o contentor Nginx, a porta de enlace 80 do agente DC/OS para a porta 80 do contentor.

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 16.0,
  "instances": 1,
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        { "containerPort": 80, "hostPort": 80, "servicePort": 9000, "protocol": "tcp" }
      ]
    }
  }
}
```

Crie o seu próprio ficheiro JSON ou utilize o exemplo fornecido na [demonstração do Serviço de Contentor do Azure](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/marathon/marathon.json). Armazene-o numa localização acessível. Em seguida, para implementar o contentor, execute o comando seguinte. Especifique o nome do ficheiro JSON.

```powershell
Invoke-WebRequest -Method Post -Uri http://localhost/marathon/v2/apps -ContentType application/json -InFile 'c:\marathon.json'
```

Também pode utilizar a API do Marathon para aumentar ou reduzir horizontalmente em implementações de aplicações. No exemplo anterior, foi implementada uma instância de uma aplicação. Vamos aumentar horizontalmente para três instâncias de uma aplicação. Para tal, crie um ficheiro JSON utilizando o seguinte texto JSON e armazene-o numa localização acessível.

```json
{ "instances": 3 }
```

Execute o comando seguinte para aumentar horizontalmente a aplicação.

> [AZURE.NOTE] O URI será http://localhost/marathon/v2/apps/ e, em seguida, o ID da aplicação a dimensionar. Se estiver a utilizar o exemplo Nginx facultado aqui, o URI será http://localhost/marathon/v2/apps/nginx.

```powershell
Invoke-WebRequest -Method Put -Uri http://localhost/marathon/v2/apps/nginx -ContentType application/json -InFile 'c:\scale.json'
```

## Passos seguintes

- [Saiba mais sobre os pontos finais de HTTP do Mesos]( http://mesos.apache.org/documentation/latest/endpoints/).
- [Saiba mais sobre a API REST do Marathon]( https://mesosphere.github.io/marathon/docs/rest-api.html).



<!--HONumber=sep16_HO2-->


