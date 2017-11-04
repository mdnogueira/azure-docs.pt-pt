---
title: "Azure Service Fabric Docker Compose pré-visualização de implementação"
description: "Azure Service Fabric aceita formato Docker Compose para tornar mais fácil orquestrar a contentores existentes utilizando o Service Fabric. Este suporte está atualmente em pré-visualização."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/25/2017
ms.author: subramar
ms.openlocfilehash: 92d1951de8c8c80f7b47033dc751cd65a63c43f6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="docker-compose-deployment-support-in-azure-service-fabric-preview"></a>Suporte de implementação do docker Compose no Service Fabric do Azure (pré-visualização)

Docker utiliza o [docker-Compose.yml](https://docs.docker.com/compose) ficheiro para definir o contentor várias aplicações. Para que seja mais fácil para os clientes familiarizado com o Docker para orquestrar as aplicações existentes do contentor no Azure Service Fabric, incluímos suporte de pré-visualização para o Docker Compose implementação nativamente na plataforma. Service Fabric pode aceitar a versão 3 e posterior do `docker-compose.yml` ficheiros. 

Porque este suporte está em pré-visualização, apenas um subconjunto de diretivas de Compose é suportado. Por exemplo, as atualizações de aplicações não são suportadas. No entanto, pode sempre remover e implementar aplicações em vez de atualização-los.

Para utilizar esta pré-visualização, crie o cluster com a versão 5.7 ou superior do tempo de execução do Service Fabric através do portal do Azure, juntamente com o SDK correspondente. 

> [!NOTE]
> Esta funcionalidade está em pré-visualização e não é suportada na produção.
> Os exemplos abaixo são baseados no tempo de execução versão 6.0 e SDK versão 2.8.

## <a name="deploy-a-docker-compose-file-on-service-fabric"></a>Implementar um ficheiro de Docker Compose no Service Fabric

Os comandos seguintes criam uma aplicação de Service Fabric (com o nome `fabric:/TestContainerApp`), que pode monitorizar e gerir como qualquer outra aplicação de Service Fabric. Pode utilizar o nome de aplicação especificada para consultas de estado de funcionamento.
Service Fabric reconhece "DeploymentName" como o identificador da implementação Compose.

### <a name="use-powershell"></a>Utilizar o PowerShell

Crie uma implementação compor de recursos de infraestrutura de serviço a partir de um ficheiro docker-Compose.yml executando o seguinte comando do PowerShell:

```powershell
New-ServiceFabricComposeDeployment -DeploymentName TestContainerApp -Compose docker-compose.yml [-RegistryUserName <>] [-RegistryPassword <>] [-PasswordEncrypted]
```

`RegistryUserName`e `RegistryPassword` palavra-passe e do nome de utilizador de registo de contentor. Após concluir a implementação, pode verificar o estado do mesmo utilizando o seguinte comando:

```powershell
Get-ServiceFabricComposeDeploymentStatus -DeploymentName TestContainerApp
```

Para eliminar a implementação de Compose através do PowerShell, utilize o seguinte comando:

```powershell
Remove-ServiceFabricComposeDeployment  -DeploymentName TestContainerApp
```

Para iniciar uma atualização da implementação de Compose através do PowerShell, utilize o seguinte comando:

```powershell
Start-ServiceFabricComposeDeploymentUpgrade -DeploymentName TestContainerApp -Compose docker-compose-v2.yml -Monitored -FailureAction Rollback
```

Após a atualização é aceite, o progresso da atualização pode ser controlado utilizando o seguinte comando:

```powershell
Get-ServiceFabricComposeDeploymentUpgrade -Deployment TestContainerApp
```

### <a name="use-azure-service-fabric-cli-sfctl"></a>Utilizar a CLI do Azure Service Fabric (sfctl)

Em alternativa, pode utilizar o seguinte comando da CLI de recursos de infraestrutura de serviço:

```azurecli
sfctl compose create --deployment-name TestContainerApp --file-path docker-compose.yml [ [ --user --encrypted-pass ] | [ --user --has-pass ] ] [ --timeout ]
```

Depois de criar a implementação, pode verificar o estado do mesmo utilizando o seguinte comando:

```azurecli
sfctl compose status --deployment-name TestContainerApp [ --timeout ]
```

Para eliminar a implementação de compose, utilize o seguinte comando:

```azurecli
sfctl compose remove  --deployment-name TestContainerApp [ --timeout ]
```

Para iniciar uma atualização da implementação de Compose, utilize o seguinte comando:

```powershell
sfctl compose upgrade --deployment-name TestContainerApp --file-path docker-compose-v2.yml [ [ --user --encrypted-pass ] | [ --user --has-pass ] ] [--upgrade-mode Monitored] [--failure-action Rollback] [ --timeout ]
```

Após a atualização é aceite, o progresso da atualização pode ser controlado utilizando o seguinte comando:

```powershell
sfctl compose upgrade-status --deployment-name TestContainerApp
```

## <a name="supported-compose-directives"></a>Diretivas de Compose suportadas

Esta pré-visualização suporta um subconjunto das opções de configuração de formato de versão 3 Compose, incluindo as primitivas seguintes:

* Serviços > Implementar > réplicas
* Serviços > Implementar > colocação > restrições
* Serviços > Implementar > recursos > limites
    * -cpu partilhas
    * -memória
    * -memória-troca
* Serviços > comandos
* Serviços > ambiente
* Serviços > portas
* Serviços > imagem
* Serviços > isolamento (apenas para o Windows)
* Serviços > registo > controladores
* Serviços > registo > controladores > Opções
* Volume & implementar > Volume

Configurar o cluster para impor limites de recursos, conforme descrito em [governação de recursos do Service Fabric](service-fabric-resource-governance.md). Todos os outras diretivas Docker Compose não são suportadas para esta pré-visualização.

## <a name="servicednsname-computation"></a>Cálculo ServiceDnsName

Se o nome do serviço que especificou num ficheiro Compose é um nome de domínio completamente qualificado (ou seja, contém um ponto [.]), o nome DNS registado pelo Service Fabric é `<ServiceName>` (incluindo o ponto). Caso contrário, cada segmento de caminho no nome da aplicação passa a ser uma etiqueta de domínio no nome DNS do serviço, com o primeiro segmento de caminho tornar-se a etiqueta de domínio de nível superior.

Por exemplo, se o nome de aplicação especificada é `fabric:/SampleApp/MyComposeApp`, `<ServiceName>.MyComposeApp.SampleApp` seria o nome DNS registado.

## <a name="compose-deployment-instance-definition-versus-service-fabric-app-model-type-definition"></a>Componha implementação (definição de instância) em comparação com o modelo de aplicação de Service Fabric (definição de tipo)

Um ficheiro docker-Compose.yml descreve um conjunto implementável de contentores, incluindo as respetivas propriedades e configurações.
Por exemplo, o ficheiro pode conter variáveis de ambiente e portas. Também pode especificar parâmetros de implementação, tais como restrições de posicionamento, limites de recursos e nomes DNS no ficheiro docker-Compose.yml.

O [modelo de aplicação de Service Fabric](service-fabric-application-model.md) utiliza tipos e tipos de aplicações, onde pode ter várias instâncias da aplicação do mesmo tipo de serviço. Por exemplo, pode ter uma instância da aplicação por cliente. Este modelo baseado no tipo suporta várias versões do mesmo tipo de aplicação, registado com o tempo de execução.

Por exemplo, o cliente um pode ter uma aplicação com instâncias com tipo 1.0 do AppTypeA e o cliente B pode ter instanciada com o mesmo tipo e versão de outra aplicação. Definir os tipos de aplicação nos manifestos de aplicação e especifique os parâmetros de nome e a implementação de aplicação ao criar a aplicação.

Embora este modelo oferece flexibilidade, iremos estiver a planear também para suportar um modelo de implementação mais simples, instância baseada em onde tipos são implícitos do ficheiro de manifesto. Neste modelo, cada aplicação obtém o seu próprio manifesto independente. Iremos são pré-visualização deste esforço adicionando suporte para docker-Compose.yml, que é um formato de implementação com base na instância.

## <a name="next-steps"></a>Passos seguintes

* Ler sobre o [modelo de aplicação de Service Fabric](service-fabric-application-model.md)
* [Introdução à CLI do Service Fabric](service-fabric-cli.md)
