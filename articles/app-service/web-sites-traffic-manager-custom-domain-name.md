---
title: "Configure um nome de domínio personalizado para uma aplicação web no App Service do Azure utiliza o Gestor de tráfego para balanceamento de carga."
description: "Utilize um nome de domínio personalizado para um uma aplicação web no App Service do Azure inclui o Gestor de tráfego para o balanceamento de carga."
services: app-service\web
documentationcenter: 
author: cephalin
manager: cfowler
editor: 
ms.assetid: 0f96c0e7-0901-489b-a95a-e3b66ca0a1c2
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2016
ms.author: cephalin
ms.openlocfilehash: 69c7984d0620b4a0fd40252129023093c09d6e56
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2017
---
# <a name="configuring-a-custom-domain-name-for-a-web-app-in-azure-app-service-using-traffic-manager"></a>Configurar um nome de domínio personalizado para uma aplicação web no serviço de aplicações do Azure utilizando o Gestor de tráfego
[!INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

[!INCLUDE [intro](../../includes/custom-dns-web-site-intro-traffic-manager.md)]

Este artigo fornece instruções genéricas para utilizar um nome de domínio personalizado com um [do serviço de aplicações](app-service-web-overview.md) aplicação que está integrada [Gestor de tráfego](../traffic-manager/traffic-manager-overview.md) para balanceamento de carga.

[!INCLUDE [tmwebsitefooter](../../includes/custom-dns-web-site-traffic-manager-notes.md)]

[!INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]

<a name="understanding-records"></a>

## <a name="understanding-dns-records"></a>Compreender os registos DNS
[!INCLUDE [understandingdns](../../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

<a name="bkmk_configsharedmode"></a>

## <a name="configure-your-web-apps-for-standard-mode"></a>Configurar as suas aplicações web para o modo padrão
[!INCLUDE [modes](../../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a>

## <a name="add-a-dns-record-for-your-custom-domain"></a>Adicionar um registo DNS para o domínio personalizado
> [!NOTE]
> Se tiver adquirido domínio através da Web Apps do Azure App Service, em seguida, ignorar os passos seguintes e consulte o passo final [comprar o domínio para aplicações Web](custom-dns-web-site-buydomains-web-app.md) artigo.
> 
> 

Para associar o domínio personalizado com uma aplicação web no App Service do Azure, tem de adicionar uma nova entrada na tabela de DNS para o seu domínio personalizado. Fazê-lo ao utilizar as ferramentas de gestão através do seu fornecedor de domínio.

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

Enquanto as especificações de cada fornecedor de domínio podem variar, mapear *de* o nome de domínio personalizado (tais como **contoso.com**) *para* o nome de domínio do Gestor de tráfego ( **contoso.trafficmanager.NET**) que está integrado com a sua aplicação web.
   
> [!NOTE]
> Se um registo já está em utilização e tem de vincular pre-emptively as suas aplicações à mesma, pode criar um registo CNAME adicional. Por exemplo, para vincular pre-emptively **www.contoso.com** à sua aplicação web, crie um registo CNAME de **awverify.www** para **contoso.trafficmanager.net**. Em seguida, pode adicionar "www.contoso.com" à sua aplicação Web sem alterar o registo CNAME "www". Para obter mais informações, consulte [registos DNS criar para uma aplicação web no domínio personalizado de][CREATEDNS].
> 
> 

Quando terminar de adicionar ou modificar registos DNS no seu fornecedor de domínio, guarde as alterações.

<a name="enabledomain"></a>

## <a name="enable-traffic-manager"></a>Ativar o Gestor de tráfego
[!INCLUDE [modes](../../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações, consulte o [Centro para Programadores do Node.js](/develop/nodejs/).

[!INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[CREATEDNS]: ../dns/dns-web-sites-custom-domain.md
