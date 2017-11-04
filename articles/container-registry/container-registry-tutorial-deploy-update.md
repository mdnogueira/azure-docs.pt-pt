---
title: "Tutorial de registo de contentor do Azure – enviar por Push uma imagem atualizada para implementações regionais"
description: "Emita uma imagem de Docker modificada para o seu Azure georreplicação contém registo e ver as alterações implementadas automaticamente em aplicações web em execução em várias regiões. Parte três de uma série de três partes."
services: container-registry
documentationcenter: 
author: mmacy
manager: timlt
editor: mmacy
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
ms.openlocfilehash: 76e6e1b826f37bfea7a8463808566191753e4f2d
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2017
---
# <a name="push-an-updated-image-to-regional-deployments"></a>Emita uma imagem atualizada para implementações regionais

Esta é a parte três numa série tutorial três partes. No [tutorial anterior](container-registry-tutorial-deploy-app.md), georreplicação foi configurada para duas implementações de aplicação Web regionais diferentes. Neste tutorial, primeiro modificar a aplicação, em seguida, criar uma nova imagem de contentor e enviá-lo para o registo de georreplicação. Por fim, ver a alteração, implementada automaticamente pelo registo de contentor do Azure webhooks em ambas as instâncias da aplicação Web.

Neste tutorial, a parte final na série de:

> [!div class="checklist"]
> * Modificar a aplicação web HTML
> * Criar e a etiqueta da imagem do Docker
> * Push a alteração ao registo de contentor do Azure
> * Ver a aplicação atualizada em duas regiões diferentes

Se ainda não configurou as duas *aplicação Web para contentores* regionais implementações, devolver o tutorial da série anterior [aplicação web de implementar a partir do registo de contentor do Azure](container-registry-tutorial-deploy-app.md).

## <a name="modify-the-web-application"></a>Modificar a aplicação Web

Neste passo, efetue uma alteração à aplicação web que estarão visível elevada disponibilidade depois de emitir a imagem do contentor atualizadas para o registo de contentor do Azure.

Localizar o `AcrHelloworld/Views/Home/Index.cshtml` ficheiro da origem de aplicação [clonado a partir do GitHub](container-registry-tutorial-prepare-registry.md#get-application-code) um tutorial anterior e abri-lo no seu editor de texto favorito. Adicione a seguinte linha acima o `<img>` linha:

```html
<h1>MODIFIED</h1>
```

A modificação `Index.cshtml` deve ter um aspeto semelhante a:

```html
@{
    ViewData["Title"] = "Azure Container Registry :: Geo-replication";
}
<h1>MODIFIED</h1>
<img width="700" src="~/images/@ViewData["MAPIMAGE"]" />
<ul>
<li>Registry URL: @ViewData["REGISTRYURL"]</li>
<li>Registry IP: @ViewData["REGISTRYIP"]</li>
<li>HostEntry: @ViewData["HOSTENTRY"]</li>
<li>Region: @ViewData["REGION"]</li>
<li>Map: @ViewData["MAPIMAGE"]</li>
</ul>
```

## <a name="rebuild-the-image"></a>Recriar a imagem

Agora que atualizou a aplicação web, recriar a imagem de contentor. Como anteriormente, utilize o nome de imagem completamente qualificado, incluindo o URL do servidor de início de sessão, para a etiqueta:

```bash
docker build . -f ./AcrHelloworld/Dockerfile -t <acrName>.azurecr.io/acr-helloworld:v1
```

## <a name="run-the-container-locally"></a>Executar o contentor localmente

Antes de implementar no registo de contentor do Azure, execute localmente para verificar que a compilação foi bem-sucedida.

```bash
docker run -d -p 8080:80 <acrName>.azurecr.io/acr-helloworld:v1
```

Navegue para http://localhost:8080 no seu browser para confirmar que o contentor está a funcionar e é apresentada a modificação.

![IMAGEM DO CONTENTOR DE LOCAIS][local-container-01]

## <a name="push-image-to-azure-container-registry"></a>Imagem de push para o registo de contentor do Azure

Agora, push a atualização *acr olámundo* imagem de contentor para o registo de georreplicação. Aqui, está a executar um único `docker push` comando para implementar a imagem atualizada para as réplicas de registo em ambos os *EUA oeste* e *EUA Leste* regiões.

```bash
docker push <acrName>.azurecr.io/acr-helloworld:v1
```

## <a name="view-the-webhook-logs"></a>Ver os registos do webhook

Enquanto está a ser replicada a imagem, pode ver os webhooks de registo de contentor do Azure que está a ser acionados.

Para ver os webhooks regionais que foram criados quando implementou o contentor para *Web Apps para contentores* um tutorial anterior, navegue para o registo de contentor no portal do Azure, em seguida, selecione **Webhooks**em **serviços**.

![Registo de contentor Webhooks no portal do Azure][tutorial-portal-01]

Selecione cada Webhook para ver o histórico dos seus chamadas e as respostas. Deverá ver uma linha para o **push** ação nos registos de ambos os Webhooks. Aqui, o registo para o Webhook localizado no *EUA oeste* região mostra o **push** ação acionada pelo `docker push` no passo anterior:

![Registo de Webhook de registo de contentor no portal do Azure (EUA oeste)][tutorial-portal-02]

## <a name="view-the-updated-web-app"></a>Ver a aplicação web atualizadas

Os Webhooks notificar as Web Apps do que uma nova imagem tiver sido feito o push para o registo, que implementa o contentor atualizadas automaticamente para as aplicações regional web dois.

Certifique-se de que a aplicação foi atualizada em ambas as implementações, navegando para ambas as implementações de aplicação Web regionais no seu browser. Como um lembrete, pode encontrar o URL para a aplicação web implementada na parte superior direita do cada separador de descrição geral do serviço de aplicações.

![Descrição geral do serviço de aplicações no portal do Azure][tutorial-portal-03]

Para ver a aplicação atualizada, selecione a hiperligação na descrição geral do serviço de aplicações. Eis uma vista de exemplo de aplicação em execução no *EUA oeste*:

![Vista de browser da aplicação web modificadas em execução na região EUA oeste][deployed-app-westus-modified]

Certifique-se de que a imagem do contentor atualizadas também foi implementada para o *EUA Leste* implementação ao visualizá-lo no seu browser.

![Vista de browser da aplicação web modificadas em execução na região EUA leste][deployed-app-eastus-modified]

Com um único `docker push`, atualizou ambas as implementações de aplicação Web regionais e registo de contentor do Azure servida as imagens de contentor do repositórios de fecho de rede.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, atualizada e instalada uma versão nova do contentor de aplicação web no seu registo de georreplicação. Webhooks no registo de contentor do Azure notificado serviços de aplicação da atualização, o que acionou uma solicitação de local de registos replicadas.

Deste tutorial final da série:

> [!div class="checklist"]
> * Atualizar a aplicação web de HTML
> * Incorporada e etiquetados a imagem do Docker
> * Instalada a alteração no registo de contentor do Azure
> * Visualizar a aplicação atualizada em duas regiões diferentes

<!-- IMAGES -->
[deployed-app-eastus-modified]: ./media/container-registry-tutorial-deploy-update/deployed-app-eastus-modified.png
[deployed-app-westus-modified]: ./media/container-registry-tutorial-deploy-update/deployed-app-westus-modified.png
[local-container-01]: ./media/container-registry-tutorial-deploy-update/local-container-01.png
[tutorial-portal-01]: ./media/container-registry-tutorial-deploy-update/tutorial-portal-01.png
[tutorial-portal-02]: ./media/container-registry-tutorial-deploy-update/tutorial-portal-02.png
[tutorial-portal-03]: ./media/container-registry-tutorial-deploy-update/tutorial-portal-03.png