<properties
   pageTitle="Serviço Marathon específico para aplicações ou utilizadores | Microsoft Azure"
   description="Criar um serviço Marathon específico para aplicações ou utilizadores"
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Containers, Marathon, Micro-services, DC/OS, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/12/2016"
   ms.author="rogardle"/>

# Criar um serviço Marathon específico para aplicações ou utilizadores

O Serviço de Contentor do Azure fornece um conjunto de servidores principais no qual podemos pré-configurar o Apache Mesos e o Marathon. Estes podem ser utilizados para orquestrar as aplicações no cluster, mas é melhor não utilizar Modelos de Estrutura Mestres para esta finalidade. Por exemplo, para ajustar a configuração do Marathon, necessita de iniciar sessão nos próprios modelos de estrutura mestres e efetuar as alterações; isto encoraja servidores mestre exclusivos, que são ligeiramente diferentes do padrão e têm de ser tratados e geridos de forma independente. Além disso, a configuração necessária para uma equipa pode não ser a configuração ideal para outra equipa. Neste artigo explicaremos como adicionar um serviço Marathon específico para utilizadores ou aplicações.

Uma vez que este serviço pertencerá a um utilizador único ou equipa, estes são livres de configurá-lo da forma pretendida. Além disso, o Serviço de Contentor do Azure garante que o serviço continua em execução; se o serviço falhar, o Serviço de Contentor do Azure reinicia-o automaticamente. Na maioria das vezes, nem se aperceberá de eventuais períodos de indisponibilidade.

## Pré-requisitos

[Implemente uma instância do Serviço de Contentor do Azure](container-service-deployment.md) com o tipo de orquestrador DCOS [e certifique-se de que o cliente pode ligar ao cluster](container-service-connect.md) e ao [AZURE.INCLUDE [install the DC/OS CLI](../../includes/container-service-install-dcos-cli-include.md)].

## Criar um serviço Marathon específico para aplicações ou utilizadores.

Comece por criar um ficheiro de configuração JSON que define o nome do serviço de aplicação que pretende criar. Aqui, utilizamos `marathon-alice` como o nome da estrutura. Guarde o ficheiro com um nome semelhante a `marathon-alice.json`:

```json
{"marathon": {"framework-name": "marathon-alice" }}
```

Em seguida, utilize o CLI DC/OS para instalar a instância do Marathon com as opções definidas no ficheiro de configuração:

```bash
dcos package install --options=marathon-alice.json marathon
```

Agora, deverá ver o serviço `marathon-alice` em execução no separador de serviços da IU do DC/OS. A IU será `http://<hostname>/service/marathon-alice/` se pretender aceder diretamente à mesma.

## Definir o CLI DC/OS para aceder ao serviço

Opcionalmente, pode configurar o CLI DC/OS para aceder a este novo serviço definindo a propriedade `marathon.url` para apontar para a instância `marathon-alice` da seguinte forma:

```bash
dcos config set marathon.url http://<hostname>/service/marathon-alice/
```

Pode verificar em qual instância do Marathon o CLI está a funcionar com o comando `dcos config show`. Pode voltar a utilizar o serviço Marathon principal com o comando `dcos config unset marathon.url`.


<!--HONumber=Jun16_HO2-->


