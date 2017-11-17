---
title: "Gerir aplicações de Service Fabric do Azure com a CLI do Azure Service Fabric"
description: "Saiba como implementar e remover aplicações a partir de um cluster do Service Fabric do Azure ao utilizar a CLI do Azure Service Fabric"
services: service-fabric
author: samedder
manager: timlt
ms.service: service-fabric
ms.topic: article
ms.date: 08/22/2017
ms.author: edwardsa
ms.openlocfilehash: 6c8501e8c863e1240a277bf8064118a2b113a81b
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2017
---
# <a name="manage-an-azure-service-fabric-application-by-using-azure-service-fabric-cli"></a>Gerir uma aplicação de Service Fabric do Azure ao utilizar a CLI do Azure Service Fabric

Saiba como criar e eliminar as aplicações que estão a executar um cluster do Service Fabric do Azure.

## <a name="prerequisites"></a>Pré-requisitos

* Instale a CLI do Service Fabric. Em seguida, selecione o cluster do Service Fabric. Para obter mais informações, consulte [começar com o serviço de recursos de infraestrutura CLI](service-fabric-cli.md).

* Tem um pacote de aplicação de Service Fabric pronto para ser implementada. Para obter mais informações sobre como criar e pacote de uma aplicação, leia sobre o [modelo de aplicação de Service Fabric](service-fabric-application-model.md).

## <a name="overview"></a>Descrição geral

Para implementar uma nova aplicação, conclua estes passos:

1. Carregar um pacote de aplicação para o arquivo de imagens do Service Fabric.
2. Aprovisione um tipo de aplicação.
3. Elimine o arquivo de conteúdo da imagem.
4. Especificar e criar uma aplicação.
5. Especifique e criação de serviços.

Para remover uma aplicação existente, conclua estes passos:

1. Elimine a aplicação.
2. Anular o provisionamento o tipo de aplicação associada.

## <a name="deploy-a-new-application"></a>Implementar uma nova aplicação

Para implementar uma nova aplicação, conclua as seguintes tarefas:

### <a name="upload-a-new-application-package-to-the-image-store"></a>Carregue um novo pacote de aplicação para o arquivo de imagens

Antes de criar uma aplicação, carregar o pacote de aplicação para o arquivo de imagens do Service Fabric.

Por exemplo, se o pacote de aplicação está a ser o `app_package_dir` diretório, utilize os seguintes comandos para carregar o diretório:

```azurecli
sfctl application upload --path ~/app_package_dir
```

Para pacotes de aplicações grandes, pode especificar o `--show-progress` opção para apresentar o progresso do carregamento.

### <a name="provision-the-application-type"></a>Aprovisionar o tipo de aplicação

Quando o carregamento estiver concluído, Aprovisione a aplicação. Para aprovisionar a aplicação, utilize o seguinte comando:

```azurecli
sfctl application provision --application-type-build-path app_package_dir
```

O valor para `application-type-build-path` é o nome do diretório onde o seu pacote de aplicação que carregou.

### <a name="delete-the-application-package"></a>Eliminar o pacote de aplicação

Recomenda-se que remova o pacote de aplicações após a aplicação for registada com êxito.  A eliminação de pacotes de aplicação da loja de imagem liberta recursos do sistema.  Manter os pacotes de aplicações não utilizadas consome armazenamento em disco e leva a problemas de desempenho da aplicação. 

Para eliminar o pacote de aplicações da loja de imagem, utilize o seguinte comando:

```azurecli
sfctl store delete --content-path app_package_dir
```

`content-path`tem de ser o nome do diretório que carregou quando criou a aplicação.

### <a name="create-an-application-from-an-application-type"></a>Criar uma aplicação a partir de um tipo de aplicação

Depois de aprovisionar a aplicação, utilize o seguinte comando para o nome e criar a sua aplicação:

```azurecli
sfctl application create --app-name fabric:/TestApp --app-type TestAppType --app-version 1.0
```

`app-name`é o nome que pretende utilizar para a instância de aplicação. Pode obter os parâmetros adicionais do manifesto da aplicação aprovisionados anteriormente.

O nome da aplicação tem de começar com o prefixo `fabric:/`.

### <a name="create-services-for-the-new-application"></a>Criar serviços para a nova aplicação

Depois de criar uma aplicação, crie serviços da aplicação. No exemplo seguinte, vamos criar um novo serviço sem monitorização de estado da nossa aplicação. Os serviços que pode criar a partir de uma aplicação estão definidos num manifesto de serviço no pacote de aplicação aprovisionados anteriormente.

```azurecli
sfctl service create --app-id TestApp --name fabric:/TestApp/TestSvc --service-type TestServiceType \
--stateless --instance-count 1 --singleton-scheme
```

## <a name="verify-application-deployment-and-health"></a>Verifique o estado de funcionamento e implementação de aplicação

Para verificar que tudo está em bom estado, utilize os seguintes comandos do Estado de funcionamento:

```azurecli
sfctl application list
sfctl service list --application-id TestApp
```

Para verificar que o serviço está em bom estado, utilize comandos semelhantes para obter o estado de funcionamento do serviço e a aplicação:

```azurecli
sfctl application health --application-id TestApp
sfctl service health --service-id TestApp/TestSvc
```

Bom estado de funcionamento dos serviços e aplicações têm um `HealthState` valor `Ok`.

## <a name="remove-an-existing-application"></a>Remover uma aplicação existente

Para remover uma aplicação, conclua as seguintes tarefas:

### <a name="delete-the-application"></a>Eliminar a aplicação

Para eliminar a aplicação, utilize o seguinte comando:

```azurecli
sfctl application delete --application-id TestEdApp
```

### <a name="unprovision-the-application-type"></a>Anular o aprovisionamento do tipo de aplicação

Depois de eliminar a aplicação, pode desaprovisionou o tipo de aplicação, se já não precisar dele. Ao anular o provisionamento o tipo de aplicação, utilize o seguinte comando:

```azurecli
sfctl application unprovision --application-type-name TestAppType --application-type-version 1.0
```

O nome do tipo e a versão de tipo têm de corresponder o nome e versão no manifesto da aplicação aprovisionados anteriormente.

## <a name="upgrade-application"></a>Atualizar a aplicação

Depois de criar a sua aplicação, pode repetir o mesmo conjunto de passos para Aprovisionar uma segunda versão da aplicação. Em seguida, com uma atualização da aplicação de Service Fabric pode efetuar a transição para a segunda versão da aplicação a ser executado. Para obter mais informações, consulte a documentação sobre [as atualizações de aplicações de Service Fabric](service-fabric-application-upgrade.md).

Para efetuar uma atualização, primeiro aprovisionar a próxima versão da aplicação utilizando os mesmos comandos como anteriormente:

```azurecli
sfctl application upload --path ~/app_package_dir_2
sfctl application provision --application-type-build-path app_package_dir_2
sfctl store delete --content-path app_package_dir_2
```

Recomenda-se, em seguida, para efetuar uma atualização automática monitorizada, iniciar a atualização, executando o seguinte comando:

```azurecli
sfctl application upgrade --app-id TestApp --app-version 2.0.0 --parameters "{\"test\":\"value\"}" --mode Monitored
```

Atualizações substituam parâmetros existentes com qualquer conjunto especificado. Parâmetros da aplicação devem ser transmitidos como argumentos para o comando de atualização, se necessário. Parâmetros da aplicação devem ser codificados como um objeto JSON.

Para obter os parâmetros especificados anteriormente, pode utilizar o `sfctl application info` comando.

Quando uma atualização da aplicação está em curso, o estado pode ser obtido utilizando o `sfctl application upgrade-status` comando.

Por fim, se uma atualização está em progresso e tem de ser cancelado, pode utilizar o `sfctl application upgrade-rollback` para reverter a atualização.

## <a name="next-steps"></a>Passos seguintes

* [Noções básicas do Service Fabric CLI](service-fabric-cli.md)
* [Introdução ao Service Fabric no Linux](service-fabric-get-started-linux.md)
* [Iniciar uma atualização da aplicação de Service Fabric](service-fabric-application-upgrade.md)
