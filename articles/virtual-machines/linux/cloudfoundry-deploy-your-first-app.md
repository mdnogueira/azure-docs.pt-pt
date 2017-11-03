---
title: "Implementar a sua primeira aplicação em Foundry de nuvem no Microsoft Azure | Microsoft Docs"
description: "Implementar uma aplicação Foundry de nuvem no Azure"
services: virtual-machines-linux
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 8fa04a58-56ad-4e6c-bef4-d02c80d4b60f
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/14/2017
ms.author: seanmck
ms.openlocfilehash: b617127fc0a3f8dcae293e356ea669edcfa5deff
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-your-first-app-to-cloud-foundry-on-microsoft-azure"></a>Implementar a sua primeira aplicação em Foundry de nuvem no Microsoft Azure

[Nuvem Foundry](http://cloudfoundry.org) é uma plataforma de aplicação de open source popular disponíveis no Microsoft Azure. Neste artigo, vamos mostrar como implementar e gerir uma aplicação na nuvem Foundry num ambiente do Azure.

## <a name="create-a-cloud-foundry-environment"></a>Criar um ambiente de nuvem Foundry

Existem várias opções para criar um ambiente de nuvem Foundry no Azure:

- Utilize o [oferta Pivotal nuvem Foundry] [ pcf-azuremarketplace] no Azure Marketplace para criar um ambiente padrão que inclui PCF Ops Manager e o Mediador de serviço do Azure. Pode encontrar [concluir as instruções] [ pcf-azuremarketplace-pivotaldocs] para implementar o marketplace oferecem na documentação do Pivotal.
- Criar um ambiente personalizado por [implementar manualmente Pivotal nuvem Foundry][pcf-custom].
- [Implementar os pacotes de nuvem Foundry open source diretamente] [ oss-cf-bosh] ao configurar um [BOSH](http://bosh.io) director, uma VM que coordena a implementação do ambiente de nuvem Foundry.

> [!IMPORTANT] 
> Se estiver a implementar PCF no Azure Marketplace, anote o SYSTEMDOMAINURL e as credenciais de administrador necessárias para aceder ao Gestor de aplicações Pivotal, que são descritos no guia de implementação do marketplace. Estes são necessárias para concluir este tutorial. Para implementações do marketplace, o SYSTEMDOMAINURL está a ser https://system o formulário. *endereço ip*. cf.pcfazure.com.

## <a name="connect-to-the-cloud-controller"></a>Ligar ao controlador de nuvem

O controlador de nuvem é o ponto de entrada principal para um ambiente de nuvem Foundry para implementação e gestão de aplicações. As principais API de controlador de nuvem (CCAPI) é uma API REST, mas está acessível através de várias ferramentas. Neste caso, iremos interagir com ele através de [nuvem Foundry CLI][cf-cli]. Pode instalar a CLI no Linux, MacOS ou Windows, mas se preferir não instalá-lo de todo, fica disponível pré-instalado no [Shell de nuvem do Azure][cloudshell-docs].

Para iniciar sessão, preceder `api` para SYSTEMDOMAINURL que obteve da implementação do marketplace. Uma vez que a implementação predefinida utiliza um certificado autoassinado, também deve incluir o `skip-ssl-validation` mudar.

```bash
cf login -a https://api.SYSTEMDOMAINURL --skip-ssl-validation
```

Lhe for pedido que inicie sessão no controlador de nuvem. Utilize as credenciais de conta de administrador que obteve dos passos de implementação do marketplace.

Nuvem Foundry fornece *organizações do* e *espaços* como espaços de nomes para isolar as equipas e ambientes dentro de uma implementação partilhada. A implementação de marketplace PCF inclui a predefinição *sistema* org e um conjunto de espaços criado para conter os componentes base, como o serviço de dimensionamento automático e o Mediador de serviço do Azure. Por agora, escolha o *sistema* espaço.


## <a name="create-an-org-and-space"></a>Criar uma organização e o espaço

Se escrever `cf apps`, verá um conjunto de aplicações do sistema que foram implementadas no espaço de sistema dentro de org. de sistema 

Deve manter a *sistema* org reservado para as aplicações de sistema, crie um org e espaço para alojam a nossa aplicação de exemplo.

```bash
cf create-org myorg
cf create-space dev -o myorg
```

Utilize o comando de destino para mudar para a nova organização e o espaço:

```bash
cf target -o testorg -s dev
```

Agora, quando implementar uma aplicação, for automaticamente criada na nova organização e espaço. Para confirmar que estão atualmente não existem aplicações no org/espaço novo, escreva `cf apps` novamente.

> [!NOTE] 
> Para obter mais informações sobre as organizações do e espaços e como podem ser utilizadas para controlo de acesso baseado em funções (RBAC), consulte o [documentação de nuvem Foundry][cf-orgs-spaces-docs].

## <a name="deploy-an-application"></a>Implementar uma aplicação

Vamos utilizar um exemplo de aplicação de nuvem Foundry chamado Hello a nuvem, o que é escrita em Java e com base no [mola Framework](http://spring.io) e [mola arranque](http://projects.spring.io/spring-boot/).

### <a name="clone-the-hello-spring-cloud-repository"></a>Clone o repositório de nuvem Hello mola

A aplicação de exemplo de Olá mola em nuvem está disponível no GitHub. Clone-o para o seu ambiente e altere para o novo diretório:

```bash
git clone https://github.com/cloudfoundry-samples/hello-spring-cloud
cd hello-spring-cloud
```

### <a name="build-the-application"></a>Criar a aplicação

A aplicação através de compilação [Apache Maven](http://maven.apache.org).

```bash
mvn clean package
```

### <a name="deploy-the-application-with-cf-push"></a>Implementar a aplicação com cf push

Pode implementar a maioria das aplicações para a utilização de nuvem Foundry o `push` comando:

```bash
cf push
```

Quando lhe *push* uma aplicação, nuvem Foundry Deteta o tipo de aplicação (neste caso, uma aplicação Java) e identifica as respetivas dependências (neste caso, a arquitetura de mola). -Lo, em seguida, os pacotes tudo necessários para executar o código para uma imagem de contentor autónomo, conhecida como um *droplet*. Por fim, nuvem Foundry agendas a aplicação das máquinas disponíveis no seu ambiente e cria um URL onde pode aceder, que está disponível na saída do comando.

![Saída do comando de push cf][cf-push-output]

Para ver a aplicação Olá-mola-cloud, abra o URL fornecido no seu browser:

![Predefinição da IU para a nuvem de Olá mola][hello-spring-cloud-basic]

> [!NOTE] 
> Para obter mais informações sobre o que acontece durante `cf push`, consulte [como as aplicações são testados] [ cf-push-docs] na documentação do Foundry de nuvem.

## <a name="view-application-logs"></a>Ver registos de aplicações

Pode utilizar a CLI de Foundry da nuvem para ver os registos para uma aplicação pelo respetivo nome:

```bash
cf logs hello-spring-cloud
```

Por predefinição, os registos de comando utiliza *seguimento*, que mostra os novos registos como estas são escritas. Para ver os novos registos são apresentados, atualize a aplicação Olá-mola-cloud no browser.

Para ver registos que já foi escritos, adicione o `recent` comutador:

```bash
cf logs --recent hello-spring-cloud
```

## <a name="scale-the-application"></a>Dimensionar a aplicação

Por predefinição, `cf push` apenas cria uma única instância da sua aplicação. Para garantir a elevada disponibilidade e ativar de escalamento horizontal para maior débito, geralmente, pretende executar mais do que uma instância das suas aplicações. Pode ampliar facilmente aplicações já implementadas utilizando o `scale` comando:

```bash
cf scale -i 2 hello-spring-cloud
```

Executar o `cf app` comando na aplicação apresenta Foundry de nuvem está a criar outra instância da aplicação. Assim que a aplicação foi iniciada, o Cloud Foundry inicia automaticamente para o tráfego de balanceamento de carga.


## <a name="next-steps"></a>Passos seguintes

- [Leia a documentação de nuvem Foundry][cloudfoundry-docs]
- [Configurar o plug-in do Visual Studio Team Services para a nuvem Foundry][vsts-plugin]
- [Configurar o Nozzle de análise do registo da Microsoft para nuvem Foundry][loganalytics-nozzle]

<!-- LINKS -->

[pcf-azuremarketplace]: https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry
[pcf-custom]: https://docs.pivotal.io/pivotalcf/1-10/customizing/azure.html
[oss-cf-bosh]: https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs
[pcf-azuremarketplace-pivotaldocs]: https://docs.pivotal.io/pivotalcf/customizing/pcf_azure.html
[cf-cli]: https://github.com/cloudfoundry/cli
[cloudshell-docs]: https://docs.microsoft.com/azure/cloud-shell/overview
[cf-orgs-spaces-docs]: https://docs.cloudfoundry.org/concepts/roles.html
[spring-boot]: https://projects.spring.io/spring-boot/
[spring-framework]: http://spring.io
[cf-push-docs]: https://docs.cloudfoundry.org/concepts/how-applications-are-staged.html
[cloudfoundry-docs]: https://docs.cloudfoundry.org
[vsts-plugin]: https://github.com/Microsoft/vsts-cloudfoundry
[loganalytics-nozzle]: https://github.com/Azure/oms-log-analytics-firehose-nozzle

<!-- IMAGES -->
[cf-push-output]: ./media/cloudfoundry-deploy-your-first-app/cf-push-output.png
[hello-spring-cloud-basic]: ./media/cloudfoundry-deploy-your-first-app/hello-spring-cloud-basic.png