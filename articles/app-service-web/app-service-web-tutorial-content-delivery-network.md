---
title: "Ligar uma Aplicação Web a uma Rede de Entrega de Conteúdos | Microsoft Docs"
description: "Ligue uma Aplicação Web a uma Rede de Entrega de Conteúdos para entregar os seus ficheiros estáticos a partir de nós de extremidade."
services: app-service
author: syntaxc4
ms.author: cfowler
ms.date: 04/03/2017
ms.topic: hero-article
ms.service: app-service-web
manager: erikre
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 8194e669f943a6c47f02ae0d2a55e0e720420489
ms.lasthandoff: 04/18/2017

---
# <a name="connect-a-web-app-to-a-content-delivery-network"></a>Ligar uma Aplicação Web a uma Rede de Entrega de Conteúdos

Neste tutorial, vai criar um Perfil e um Ponto Final da CDN do Azure para enviar os ficheiros estáticos da sua Aplicação Web através das localizações pop da CDN do Azure.

> [!TIP]
> Reveja a lista atualizada de [Azure CDN pop locations (Localizações pop da CDN do Azure)](https://docs.microsoft.com/en-us/azure/cdn/cdn-pop-locations).
>

## <a name="step-1---login-to-azure-portal"></a>Passo 1 - Iniciar sessão no Portal do Azure

Em primeiro lugar, abra o seu browser favorito e navegue para o [Portal](https://portal.azure.com) do Azure.

## <a name="step-2---create-a-cdn-profile"></a>Passo 2 - Criar um Perfil da CDN

Clique no botão **+ Novo** na navegação do lado esquerdo e clique em **Web + Móvel**. Na categoria Web + Móvel, selecione **CDN**.

Especifique o **Nome**, a **Localização**, o **Grupo de recursos**, o **Escalão de preços** e clique em **Criar**.

Abra o concentrador de grupos de recursos na navegação do lado esquerdo e selecione **myResourceGroup**. A partir da lista de recursos, selecione **myCDNProfile**.

![azure-cdn-profile-created](media/app-service-web-tutorial-content-delivery-network/azure-cdn-profile-created.png)

## <a name="step-3---create-a-cdn-endpoint"></a>Passo 3 - Criar um Ponto Final da CDN

Clique em **+ Ponto Final** nos comandos ao lado da caixa de pesquisa, o que abre o painel Criação de ponto final.

Especifique o **Nome**, o **Tipo de origem**, o **Nome de anfitrião da origem**, e clique em **Adicionar**.

O Ponto Final da Rede de Entrega de Conteúdos é criado e o estado atualizado para **em execução**.

![azure-cdn-endpoint-created](media/app-service-web-tutorial-content-delivery-network/azure-cdn-endpoint-created.png)

## <a name="step-4---leveraging-cdn"></a>Passo 4 - Tirar partido da CDN

Agora que o ponto final está em execução, vamos confirmar que o conteúdo está disponível no servidor pop, navegando para um ficheiro estático no servidor Web e alterando o nome de anfitrião de `http://<app_name>.azurewebsites.net/path/to-static-file` para `http://<endpoint_name>.azureedge.net/path/to-static-file`.

![app-service-web-url-to-cdn-endpoint-url](media/app-service-web-tutorial-content-delivery-network/app-service-web-url-to-cdn-endpoint-url.png)

## <a name="next-steps"></a>Passos Seguintes


