---
title: "Tutorial de registo de contentor do Azure – implemente a aplicação web a partir do registo de contentor do Azure"
description: "Implemente uma aplicação web baseado em Linux utilizando uma imagem do contentor de um registo de contentor do Azure georreplicação. Parte dois de uma série de três partes."
services: container-registry
documentationcenter: 
author: mmacy
manager: timlt
editor: neilpeterson
tags: acr, azure-container-registry, geo-replication
keywords: Docker, contentores, registo, Azure
ms.service: container-registry
ms.devlang: 
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/24/2017
ms.author: marsma
ms.custom: 
ms.openlocfilehash: 749a536fea50d0f45b021cfeaffe8e493faa9b3f
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2017
---
# <a name="deploy-web-app-from-azure-container-registry"></a>Implementar a aplicação web a partir do registo de contentor do Azure

Esta é a parte dois numa série tutorial três partes. No [parte um](container-registry-tutorial-prepare-registry.md), foi criado um registo de contentor privado, georreplicação e uma imagem do contentor foi criada a partir de origem e enviada para o registo. Neste artigo, implementa o contentor em duas instâncias de aplicação Web em duas regiões do Azure diferentes para tirar partido do aspeto de fecho de rede do registo georreplicação.

Neste tutorial, parte dois na série de:

> [!div class="checklist"]
> * Implementar uma imagem de contentor em dois *Web Apps para contentores* instâncias
> * Certifique-se a aplicação implementada

Se ainda não criou um registo georreplicação e a imagem da aplicação de exemplo para o registo é feito o Push regressar à anterior tutorial da série [preparar um registo de contentor do Azure georreplicação](container-registry-tutorial-prepare-registry.md).

A parte seguinte da série, a atualizar a aplicação, em seguida, emitir uma nova imagem do contentor para o registo. Por fim, navegar para cada instância de aplicação Web em execução para ver a alteração refletida automaticamente no ambos, que mostra o registo de contentor do Azure georreplicação e webhooks em ação.

## <a name="automatic-deployment-to-web-app-for-containers"></a>Implementação automática para a aplicação Web para contentores

Registo de contentor do Azure fornece suporte para a implementação de aplicações diretamente a [Web Apps para contentores](../app-service/containers/index.yml). Neste tutorial, utilize o portal do Azure para implementar a imagem de contentor criada no tutorial anterior para planos de aplicações web dois localizadas em diferentes regiões do Azure.

Quando implementar uma aplicação web a partir de uma imagem do contentor no seu registo e tem um registo georreplicação na mesma região, o registo de contentor do Azure cria uma implementação de imagem [webhook](container-registry-webhook.md) por si. Quando enviar uma nova imagem para o seu repositório de contentor, o webhook escolherá a alteração e implementa automaticamente a nova imagem do contentor à sua aplicação web.

## <a name="deploy-a-web-app-for-containers-instance"></a>Implementar uma aplicação Web para a instância de contentores

Neste passo, vai criar uma aplicação de Web para a instância de contentores no *EUA oeste* região.

Iniciar sessão para o [portal do Azure](https://portal.azure.com) e navegue para o registo que criou no tutorial anterior.

Selecione **repositórios de** > **acr-helloworld**, em seguida, faça duplo clique no **v1** tag em **etiquetas** e selecione **Implementar no app service**.

![Implementar no app service no portal do Azure][deploy-app-portal-01]

Em **aplicação Web no Linux (pré-visualização)** que é apresentado, especifique os seguintes valores para cada definição:

| Definição | Valor |
|---|---|
| **Nome do site** | Um nome globalmente exclusivo para a aplicação web. Neste exemplo, utilizamos o formato `<acrName>-westus` para identificar facilmente o registo e a região for implementada a partir da aplicação web. |
| **Grupo de Recursos** | **Utilizar existentes** > `myResourceGroup` |
| **Plano de serviço de aplicações/localização** | Criar um novo plano com o nome `plan-westus` no **EUA oeste** região. |
| **Imagem** | `acr-helloworld:v1`

Selecione **criar** para aprovisionar a aplicação web para o *EUA oeste* região.

![Aplicação Web na configuração do Linux no portal do Azure][deploy-app-portal-02]

## <a name="view-the-deployed-web-app"></a>Ver a aplicação web implementada

Quando a implementação estiver concluída, pode ver a aplicação em execução, ao navegar para o URL no browser.

No portal, selecione **serviços aplicacionais**, em seguida, a aplicação web que aprovisionou no passo anterior. Neste exemplo, a aplicação web é denominada *uniqueregistryname westus*.

Selecione o URL da aplicação web com hiperligação na parte superior direita do **descrição geral do serviço de aplicações** para ver a aplicação em execução no seu browser.

![Aplicação Web na configuração do Linux no portal do Azure][deploy-app-portal-04]

Depois da imagem de Docker é implementada a partir do seu registo de contentor georreplicação, o site é apresentado uma imagem que representa a região do Azure que aloja o registo de contentor.

![Aplicação web implementada visualizada num browser][deployed-app-westus]

## <a name="deploy-second-web-app-for-containers-instance"></a>Implementar a aplicação Web segunda instância de contentores

Utilize o procedimento descrito na secção anterior para implementar uma aplicação web segundo para o *EUA Leste* região. Em **aplicação Web no Linux (pré-visualização)**, especifique os seguintes valores:

| Definição | Valor |
|---|---|
| **Nome do site** | Um nome globalmente exclusivo para a aplicação web. Neste exemplo, utilizamos o formato `<acrName>-eastus` para identificar facilmente o registo e a região for implementada a partir da aplicação web. |
| **Grupo de Recursos** | **Utilizar existentes** > `myResourceGroup` |
| **Plano de serviço de aplicações/localização** | Criar um novo plano com o nome `plan-eastus` no **EUA Leste** região. |
| **Imagem** | `acr-helloworld:v1`

Selecione **criar** para aprovisionar a aplicação web para o *EUA Leste* região.

![Aplicação Web na configuração do Linux no portal do Azure][deploy-app-portal-06]

## <a name="view-the-deployed-web-app"></a>Ver a aplicação web implementada

Como anteriormente, pode ver a aplicação em execução, ao navegar para o URL no browser.

No portal, selecione **serviços aplicacionais**, em seguida, a aplicação web que aprovisionou no passo anterior. Neste exemplo, a aplicação web é denominada *uniqueregistryname eastus*.

Selecione o URL da aplicação web com hiperligação na parte superior direita do **descrição geral do serviço de aplicações** para ver a aplicação em execução no seu browser.

![Aplicação Web na configuração do Linux no portal do Azure][deploy-app-portal-07]

Depois da imagem de Docker é implementada a partir do seu registo de contentor georreplicação, o site é apresentado uma imagem que representa a região do Azure que aloja o registo de contentor.

![Aplicação web implementada visualizada num browser][deployed-app-eastus]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, implementou duas aplicações Web para instâncias de contentores de um registo de contentor do Azure georreplicação. Ao seguir os passos neste tutorial, pode:

> [!div class="checklist"]
> * Implementar uma imagem de contentor dois *Web Apps para contentores* instâncias
> * Verificar aplicação implementada

Avançar para o próximo tutorial para atualizar e, em seguida, implementar uma nova imagem do contentor no registo de contentor, em seguida, certifique-se de que as aplicações web em execução em ambas as regiões foram atualizadas automaticamente.

> [!div class="nextstepaction"]
> [Implementar uma atualização para a imagem de contentor georreplicação](./container-registry-tutorial-deploy-update.md)

<!-- IMAGES -->
[deploy-app-portal-01]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-01.png
[deploy-app-portal-02]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-02.png
[deploy-app-portal-03]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-03.png
[deploy-app-portal-04]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-04.png
[deploy-app-portal-05]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-05.png
[deploy-app-portal-06]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-06.png
[deploy-app-portal-07]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-07.png
[deployed-app-westus]: ./media/container-registry-tutorial-deploy-app/deployed-app-westus.png
[deployed-app-eastus]: ./media/container-registry-tutorial-deploy-app/deployed-app-eastus.png