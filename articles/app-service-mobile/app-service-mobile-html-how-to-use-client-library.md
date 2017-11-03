---
title: Como utilizar o SDK de JavaScript para Mobile Apps do Azure
description: Como utilizar v para Mobile Apps do Azure
services: app-service\mobile
documentationcenter: javascript
author: ggailey777
manager: syntaxc4
editor: 
ms.assetid: 53b78965-caa3-4b22-bb67-5bd5c19d03c4
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: html
ms.devlang: javascript
ms.topic: article
ms.date: 10/30/2016
ms.author: glenga
ms.openlocfilehash: 0c4b4de560d70592f5bbdee28b56a7686b5689f4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-the-javascript-client-library-for-azure-mobile-apps"></a>Como utilizar a biblioteca de clientes JavaScript para Mobile Apps do Azure
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

Este guia informa efetuar cenários comuns utilizando a versão mais recente [JavaScript SDK de Mobile Apps do Azure]. Se estiver familiarizado com aplicações móveis do Azure, primeiro conclua [Azure Mobile Apps início rápido] para criar um back-end e criar uma tabela. Neste guia, iremos focar-se sobre como utilizar o back-end móvel em aplicações Web HTML/JavaScript.

## <a name="supported-platforms"></a>Plataformas suportadas
Limitamos browser com o suporte para as versões atuais e o apelido dos principais browsers: Google Chrome, o Microsoft Edge, o Microsoft Internet Explorer e o Mozilla Firefox.  Esperamos que o SDK para a função com qualquer browser relativamente moderna.

O pacote é distribuído como um módulo de JavaScript Universal, para que suporta globais, AMD, e CommonJS formatos.

## <a name="Setup"></a>A configuração e pré-requisitos
Este guia pressupõe que criou um back-end com uma tabela. Este guia pressupõe que a tabela tem o mesmo esquema como as tabelas nestes tutoriais.

Instalar o Azure Mobile Apps JavaScript SDK pode ser efetuado através de `npm` comando:

```
npm install azure-mobile-apps-client --save
```

A biblioteca também pode ser utilizada como um módulo de ES2015, dentro de ambientes de CommonJS como Browserify e Webpack e como uma biblioteca AMD.  Por exemplo:

```
# For ECMAScript 5.1 CommonJS
var WindowsAzure = require('azure-mobile-apps-client');
# For ES2015 modules
import * as WindowsAzure from 'azure-mobile-apps-client';
```

Também pode utilizar uma versão do SDK pré-criadas transferindo-a diretamente a partir do nosso CDN:

```html
<script src="https://zumo.blob.core.windows.net/sdk/azure-mobile-apps-client.min.js"></script>
```

[!INCLUDE [app-service-mobile-html-js-library](../../includes/app-service-mobile-html-js-library.md)]

## <a name="auth"></a>Como: autenticar os utilizadores
App Service do Azure suporta autenticar e autorizar utilizadores de aplicações através de vários fornecedores de identidade externas: Facebook, Google, Account Microsoft e Twitter. Pode definir as permissões em tabelas para restringir o acesso de operações específicas para apenas os utilizadores autenticados. Também pode utilizar a identidade de utilizadores autenticados para implementar as regras de autorização em scripts de servidor. Para obter mais informações, consulte o [introdução à autenticação] tutorial.

São suportados dois fluxos de autenticação: um fluxo de servidor e um fluxo de cliente.  O fluxo de servidor fornece a experiência de autenticação mais simples, como baseia-se na interface de autenticação de web do fornecedor. O fluxo de cliente permite para uma integração mais profunda com as capacidades específicas do dispositivo, tal como single-sign-on como depende SDKs específica do fornecedor.

[!INCLUDE [app-service-mobile-html-js-auth-library](../../includes/app-service-mobile-html-js-auth-library.md)]

### <a name="configure-external-redirect-urls"></a>Como: configurar o serviço de aplicações móveis para os URLs de redirecionamento externo.
Vários tipos de aplicações de JavaScript utilizam uma capacidade de loopback para processar fluxos de IU de OAuth.  Estas funcionalidades incluem:

* Executar o serviço localmente
* Utilizar recarregar em direto com o Framework Ionic
* Redirecionar para o App Service para autenticação.

Executar localmente pode causar problemas, porque, por predefinição, a autenticação do serviço de aplicações só está configurada para permitir o acesso de back-end da aplicação móvel. Utilize os seguintes passos para alterar as definições de serviço de aplicações para ativar a autenticação ao executar o servidor localmente:

1. Inicie sessão no [portal do Azure]
2. Navegue para o back-end da aplicação móvel.
3. Selecione **Explorador de recursos** no **ferramentas de desenvolvimento** menu.
4. Clique em **aceda** para abrir o Explorador de recursos de back-end da aplicação móvel num novo separador ou janela.
5. Expanda o **configuração** > **authsettings** nós para a sua aplicação.
6. Clique em de **editar** botão para activar a edição do recurso.
7. Localizar o **allowedExternalRedirectUrls** elemento, que deve ser nulo. Adicione os URLs de uma matriz:

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    Substitua os URLs na matriz com os URLs do seu serviço, que, neste exemplo, é `http://localhost:3000` para o serviço de exemplo de Node.js local. Pode também utilizar `http://localhost:4400` para o serviço de Ripple ou outro URL, dependendo de como a aplicação está configurada.
8. Na parte superior da página, clique em **leitura/escrita**, em seguida, clique em **colocar** para guardar as atualizações.

Também terá de adicionar os mesmos URLs de loopback para as definições CORS da lista branca:

1. Navegue de volta para o [portal do Azure].
2. Navegue para o back-end da aplicação móvel.
3. Clique em **CORS** no **API** menu.
4. Introduza cada URL vazia **origens permitidas** caixa de texto.  É criada uma nova caixa de texto.
5. Clique em **guardar**

Após o back-end de atualizações, poderá utilizar os novos URLs de loopback na sua aplicação.

<!-- URLs. -->
[Azure Mobile Apps início rápido]: app-service-mobile-cordova-get-started.md
[introdução à autenticação]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[portal do Azure]: https://portal.azure.com/
[JavaScript SDK de Mobile Apps do Azure]: https://www.npmjs.com/package/azure-mobile-apps-client
[Query object documentation]: https://msdn.microsoft.com/en-us/library/azure/jj613353.aspx
