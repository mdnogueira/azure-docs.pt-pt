---
title: Como utilizar o plug-in do Apache Cordova para Mobile Apps do Azure
description: Como utilizar o plug-in do Apache Cordova para Mobile Apps do Azure
services: app-service\mobile
documentationcenter: javascript
author: ggailey777
manager: syntaxc4
editor: 
ms.assetid: a56a1ce4-de0c-4f3c-8763-66252c52aa59
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 10/30/2016
ms.author: glenga
ms.openlocfilehash: ebf0e911eeada0e529f908dd3e3430c94edae763
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-apache-cordova-client-library-for-azure-mobile-apps"></a>Como utilizar a biblioteca de clientes do Apache Cordova para Mobile Apps do Azure
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

Este guia informa efetuar cenários comuns utilizando a versão mais recente [Plug-in do Apache Cordova para Mobile Apps do Azure]. Se estiver familiarizado com aplicações móveis do Azure, primeiro conclua [Azure Mobile Apps início rápido] para criar um back-end, criar uma tabela e transferir um projeto pré-criadas do Apache Cordova. Neste guia, iremos focar-se no plug-in do lado do cliente Apache Cordova.

## <a name="supported-platforms"></a>Plataformas suportadas
Este SDK suporta v6.0.0 Apache Cordova e mais tarde no iOS, Android e Windows dispositivos.  O suporte de plataforma é o seguinte:

* API do Android 19-24 (KitKat através de Nougat).
* iOS versões 8.0 e posteriores.
* Windows Phone 8.1.
* Plataforma universal do Windows.

## <a name="Setup"></a>A configuração e pré-requisitos
Este guia pressupõe que criou um back-end com uma tabela. Este guia pressupõe que a tabela tem o mesmo esquema como as tabelas nestes tutoriais. Este guia também parte do princípio de que adicionou o plug-in do Apache Cordova para o seu código.  Se não o tiver feito, pode adicionar o plug-in do Apache Cordova ao seu projeto na linha de comandos:

```
cordova plugin add cordova-plugin-ms-azure-mobile-apps
```

Para obter mais informações sobre como criar [sua primeira aplicação Apache Cordova], consulte a respetiva documentação.

## <a name="ionic"></a>Configurar uma aplicação Ionic v2

Para configurar corretamente um projeto de Ionic v2, criar uma aplicação básica e adicione o plug-in Cordova:

```
ionic start projectName --v2
cd projectName
ionic plugin add cordova-plugin-ms-azure-mobile-apps
```

Adicione as seguintes linhas ao `app.component.ts` para criar o objeto de cliente:

```
declare var WindowsAzure: any;
var client = new WindowsAzure.MobileServiceClient("https://yoursite.azurewebsites.net");
```

Agora pode compilar e executar o projeto no browser:

```
ionic platform add browser
ionic run browser
```

O plug-in do Azure Mobile Apps Cordova suporta ambas as aplicações v1 e v2 Ionic.  Apenas as aplicações de Ionic v2 requerem a declaração adicional para o `WindowsAzure` objeto.

[!INCLUDE [app-service-mobile-html-js-library.md](../../includes/app-service-mobile-html-js-library.md)]

## <a name="auth"></a>Como: autenticar os utilizadores
App Service do Azure suporta autenticar e autorizar utilizadores de aplicações através de vários fornecedores de identidade externas: Facebook, Google, Account Microsoft e Twitter. Pode definir as permissões em tabelas para restringir o acesso de operações específicas para apenas os utilizadores autenticados. Também pode utilizar a identidade de utilizadores autenticados para implementar as regras de autorização em scripts de servidor. Para obter mais informações, consulte o [introdução à autenticação] tutorial.

Quando utilizar a autenticação numa aplicação Apache Cordova, o plug-ins de Cordova seguintes têm de estar disponíveis:

* [dispositivo de plug-in cordova]
* [cordova-Plug-in-inappbrowser]

São suportados dois fluxos de autenticação: um fluxo de servidor e um fluxo de cliente.  O fluxo de servidor fornece a experiência de autenticação mais simples, como baseia-se na interface de autenticação de web do fornecedor. O fluxo de cliente permite para uma integração mais profunda com as capacidades específicas do dispositivo, tal como single-sign-on como depende específica do fornecedor específicos do dispositivo SDKs.

[!INCLUDE [app-service-mobile-html-js-auth-library.md](../../includes/app-service-mobile-html-js-auth-library.md)]

### <a name="configure-external-redirect-urls"></a>Como: configurar o serviço de aplicações móveis para os URLs de redirecionamento externo.
Vários tipos de aplicações do Apache Cordova utilizam uma capacidade de loopback para processarem fluxos de IU de OAuth.  Fluxos de IU de OAuth no localhost causam problemas, uma vez que o serviço de autenticação apenas sabe como utilizar o seu serviço por predefinição.  Exemplos de fluxos de IU de OAuth problemáticos incluem:

* O emulador de Ripple.
* Em direto recarregar com Ionic.
* Executar localmente o back-end móvel
* Executar o back-end móvel num serviço de aplicações do Azure diferente que a autenticação de fornecer um.

Siga estas instruções para adicionar as suas definições locais para a configuração:

1. Inicie sessão no [Portal do Azure]
2. Selecione **todos os recursos** ou **serviços aplicacionais** , em seguida, clique no nome da sua aplicação móvel.
3. Clique em **ferramentas**
4. Clique em **Explorador de recursos** no OBSERVE menu, em seguida, clique em **aceda**.  Abre uma nova janela ou separador.
5. Expanda o **configuração**, **authsettings** nós para o seu site no painel de navegação esquerdo.
6. Clique em **editar**
7. Procure o elemento "allowedExternalRedirectUrls".  Pode ser definida como nula ou como uma matriz de valores.  Altere o valor para o seguinte valor:

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    Substitua os URLs com os URLs do seu serviço.  Os exemplos incluem "http://localhost:3000" (para o serviço de exemplo de Node.js) ou "http://localhost:4400" (para o serviço de Ripple).  No entanto, estes URLs são exemplos - sua situação, incluindo para os serviços mencionados nos exemplos, poderão ser diferentes.
8. Clique em de **leitura/escrita** botão no canto superior direito do ecrã.
9. Clique na verde **colocar** botão.

As definições são guardadas neste momento.  Não feche a janela do browser enquanto não concluir as definições de guardar.
Adicione também estes URLs de loopback para as definições CORS para o serviço de aplicações:

1. Inicie sessão no [Portal do Azure]
2. Selecione **todos os recursos** ou **serviços aplicacionais** , em seguida, clique no nome da sua aplicação móvel.
3. Abre o painel de definições automaticamente.  Se não, clique **todas as definições**.
4. Clique em **CORS** sob o menu de API.
5. Introduza o URL que pretende adicionar na caixa fornecida e prima Enter.
6. Introdução de URLs adicionais conforme necessário.
7. Clique em **guardar** para guardar as definições.

Demora, aproximadamente, 10 a 15 segundos para as novas definições surtam efeito.

## <a name="register-for-push"></a>Como: registar-se para notificações push
Instalar o [phonegap-Plug-in-push] para processar as notificações push.  Este plug-in pode ser facilmente adicionada utilizando o `cordova plugin add` comando na linha de comandos ou através do instalador de plug-in do Git no Visual Studio.  O seguinte código na sua aplicação Apache Cordova regista o dispositivo para notificações push:

```
var pushOptions = {
    android: {
        senderId: '<from-gcm-console>'
    },
    ios: {
        alert: true,
        badge: true,
        sound: true
    },
    windows: {
    }
};
pushHandler = PushNotification.init(pushOptions);

pushHandler.on('registration', function (data) {
    registrationId = data.registrationId;
    // For cross-platform, you can use the device plugin to determine the device
    // Best is to use device.platform
    var name = 'gcm'; // For android - default
    if (device.platform.toLowerCase() === 'ios')
        name = 'apns';
    if (device.platform.toLowerCase().substring(0, 3) === 'win')
        name = 'wns';
    client.push.register(name, registrationId);
});

pushHandler.on('notification', function (data) {
    // data is an object and is whatever is sent by the PNS - check the format
    // for your particular PNS
});

pushHandler.on('error', function (error) {
    // Handle errors
});
```

Utilize o SDK dos Notification Hubs para enviar notificações push a partir do servidor.  Nunca envie notificações push diretamente a partir de clientes. Se o fizer, pode ser utilizado para acionar um ataque de recusa de serviço contra os Hubs de notificação ou o PNS.  O PNS foi ban tráfego como resultado esses ataques.

## <a name="more-information"></a>Mais informações

Pode encontrar detalhes de API de detalhado no nosso [documentação da API](http://azure.github.io/azure-mobile-apps-js-client/).

<!-- URLs. -->
[Portal do Azure]: https://portal.azure.com
[Azure Mobile Apps início rápido]: app-service-mobile-cordova-get-started.md
[introdução à autenticação]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[Plug-in do Apache Cordova para Mobile Apps do Azure]: https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-apps
[sua primeira aplicação Apache Cordova]: http://cordova.apache.org/#getstarted
[phonegap-facebook-plugin]: https://github.com/wizcorp/phonegap-facebook-plugin
[phonegap-Plug-in-push]: https://www.npmjs.com/package/phonegap-plugin-push
[dispositivo de plug-in cordova]: https://www.npmjs.com/package/cordova-plugin-device
[cordova-Plug-in-inappbrowser]: https://www.npmjs.com/package/cordova-plugin-inappbrowser
[Query object documentation]: https://msdn.microsoft.com/en-us/library/azure/jj613353.aspx
