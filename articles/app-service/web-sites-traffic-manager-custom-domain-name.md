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
ms.openlocfilehash: 5f099201d9018a6f8577cb3daf127d09560fb94b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configuring-a-custom-domain-name-for-a-web-app-in-azure-app-service-using-traffic-manager"></a>Configurar um nome de domínio personalizado para uma aplicação web no serviço de aplicações do Azure utilizando o Gestor de tráfego
[!INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

[!INCLUDE [intro](../../includes/custom-dns-web-site-intro-traffic-manager.md)]

Este artigo fornece instruções genéricas para utilizar um nome de domínio personalizado com o App Service do Azure que utilizam o Gestor de tráfego para balanceamento de carga.

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

Para associar o domínio personalizado com uma aplicação web no App Service do Azure, tem de adicionar uma nova entrada na tabela de DNS para o seu domínio personalizado ao utilizar ferramentas fornecidas pelo entidade de registo do domínio que comprou o nome de domínio do. Utilize os seguintes passos para localizar e utilizar as ferramentas DNS.

1. Inicie sessão na sua conta na sua entidade de registo do domínio e procure uma página para gerir registos DNS. Procure ligações ou áreas do site identificadas como **nome de domínio**, **DNS**, ou **nome do servidor de gestão**. Muitas vezes, é possível encontrar uma ligação a esta página possível visualizar as informações da conta e, em seguida, tais como a procura para uma ligação **meu domínios**.
2. Depois de encontrar a página de gestão para o nome de domínio, procure uma ligação que lhe permite editar os registos DNS. Isto poderá estar listado como um **ficheiro de zona**, **registos DNS**, ou como um **avançadas** a ligação de configuração.
   
   * A página será provavelmente ter alguns registos já criados, tais como uma associação de entrada '**@**'ou'\*' com uma página de 'parking domínio'. Também poderá conter registos de subdomínios comuns, tais como **www**.
   * A página será mencionar **registos CNAME**, ou forneça um lista pendente para selecionar um tipo de registo. -Pode mencionar também outros registos como **registos** e **registos MX**. Em alguns casos, registos CNAME serão chamados por outros nomes, tal como um **registo Alias**.
   * A página também irá ter campos que lhe permitem **mapa** de um **nome de anfitrião** ou **nome de domínio** para outro nome de domínio.
3. Enquanto as especificações de cada entidade de registo podem variar, em geral mapear *de* o nome de domínio personalizado (tais como **contoso.com**,) *para* o nome de domínio do Gestor de tráfego (**contoso.trafficmanager.net**) que é utilizado para a sua aplicação web.
   
   > [!NOTE]
   > Em alternativa, se um registo já está em utilização e tem de vincular preventivamente as suas aplicações à mesma, pode criar um registo CNAME adicional. Por exemplo, para vincular preventivamente **www.contoso.com** à sua aplicação web, crie um registo CNAME de **awverify.www** para **contoso.trafficmanager.net**. Em seguida, pode adicionar "www.contoso.com" à sua aplicação Web sem alterar o registo CNAME "www". Para obter mais informações, consulte [registos DNS criar para uma aplicação web no domínio personalizado de][CREATEDNS].
   > 
   > 
4. Quando terminar de adicionar ou modificar registos DNS na sua entidade de registo, guarde as alterações.

<a name="enabledomain"></a>

## <a name="enable-traffic-manager"></a>Ativar o Gestor de tráfego
[!INCLUDE [modes](../../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações, consulte o [Centro para Programadores do Node.js](/develop/nodejs/).

[!INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[!INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[CREATEDNS]: ../dns/dns-web-sites-custom-domain.md
