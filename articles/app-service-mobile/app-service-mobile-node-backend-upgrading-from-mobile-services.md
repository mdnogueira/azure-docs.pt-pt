---
title: Atualizar dos Mobile Services para o App Service do Azure - Node.js
description: "Saiba como atualizar facilmente a sua aplicação de Mobile Services a uma aplicação de Mobile do serviço de aplicações"
services: app-service\mobile
documentationcenter: 
author: ggailey777
manager: yochayk
editor: 
ms.assetid: c58f6df0-5aad-40a3-bddc-319c378218e3
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile
ms.devlang: node
ms.topic: article
ms.date: 10/01/2016
ms.author: glenga
ms.openlocfilehash: 5fc61fed674f0d2fc64bc29c064e7e872b4f2e68
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="upgrade-your-existing-nodejs-azure-mobile-service-to-app-service"></a>Atualizar o seu serviço móvel do Azure de Node.js atual para o App Service
Serviço de aplicações móveis é uma nova forma de criar aplicações móveis com o Microsoft Azure. Para obter mais informações, consulte [que são Mobile Apps?].

Este tópico descreve como atualizar uma aplicação de back-end Node.js existente de Mobile Services do Azure para um novo Mobile Apps do App Service. Enquanto executa esta atualização, a aplicação de Mobile Services existente pode continuar a funcionar.  Se precisar de atualizar uma aplicação de back-end Node.js, consulte [atualizar os Mobile Services do .NET](app-service-mobile-net-upgrading-from-mobile-services.md).

Quando um back-end móvel é atualizado para o App Service do Azure, tem acesso a todas as funcionalidades do serviço de aplicações e são cobrados de acordo com [preços do serviço de aplicações], não Mobile Services preços.

## <a name="migrate-vs-upgrade"></a>Migrar vs atualização
[!INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

> [!TIP]
> É recomendado que [efetuar uma migração](app-service-mobile-migrating-from-mobile-services.md) antes de avançar através de uma atualização. Desta forma, pode colocar em pausa ambas as versões da aplicação do mesmo plano do App Service e implicar sem custos adicionais.
>
>

### <a name="improvements-in-mobile-apps-nodejs-server-sdk"></a>Melhoramentos no servidor de Mobile Apps Node.js SDK
Atualizar para o novo [SDK de aplicações móveis](https://www.npmjs.com/package/azure-mobile-apps) fornece muitas melhorias, incluindo:

* Com base no [Express framework](http://expressjs.com/en/index.html), o novo SDK do nó é claro ponderação e concebida para manter-se novas versões de nó como entram. Pode personalizar o comportamento da aplicação com o Express middleware.
* Melhorias de desempenho significativas em comparação comparadas o SDK dos Mobile Services.
* Agora pode alojar um Web site, juntamente com o seu back-end móvel; da mesma forma, é fácil adicionar o SDK do Azure Mobile para qualquer aplicação express.v4 existente.
* Incorporado para o desenvolvimento de plataforma e local, o SDK de aplicações móveis podem ser desenvolvido e executar localmente em plataformas Windows, Linux e no OSX. Agora é fácil de utilizar técnicas de programação de nó comuns, como em execução [Mocha](https://mochajs.org/) testes antes da implementação.

## <a name="overview"></a>Descrição geral básica de atualização
Para ajudar a atualizar um back-end do Node.js, o App Service do Azure tiver fornecido um pacote de compatibilidade.  Após a atualização, terá de um site de niew que pode ser implementado para um novo site do serviço de aplicações.

O cliente de Mobile Services SDKs são **não** compatível com o novo servidor de aplicações móveis do SDK. Para fornecer a continuidade do serviço para a sua aplicação, não deve publicar as alterações a um site publicados clientes atualmente a funcionar. Em vez disso, deve criar uma nova aplicação móvel que funciona como um duplicado. Pode colocar esta aplicação no mesmo plano de serviço de aplicações para evitar incorrer em custos financeiros adicionais.

Em seguida, terá de duas versões da aplicação: um que permanece igual e serve aplicações publicadas no caráter e outro que pode, em seguida, atualizar e de destino com uma nova versão de cliente. Pode mover e testar o seu código ao seu ritmo, mas, deve certificar-se de que quaisquer correções de erros que efetuar ser aplicadas para ambos. Uma vez sentir que um número pretendido de aplicações no difundidas tem atualizado para a versão mais recente do cliente, é possível eliminar a aplicação migrada original se pretendidos ao nível. -Não implicar quaisquer custos de monetários adicionais, se alojados no mesmo plano de serviço de aplicações que a sua aplicação móvel.

O contorno completo para o processo de atualização é o seguinte:

1. Transferir o serviço de móvel do Azure (migrados) existente.
2. Converta o projeto para uma aplicação de Mobile do Azure utilizando o pacote de compatibilidade.
3. Corrija quaisquer diferenças (tais como definições de autenticação).
4. Implemente o projeto de aplicação móvel do Azure convertido para um novo serviço de aplicações.
5. Uma nova versão da aplicação cliente que utilizam a nova aplicação móvel de versão.
6. (Opcional) Elimine a aplicação de serviço móvel migrado original.

A eliminação pode ocorrer quando não vir qualquer tráfego no seu serviço móvel migrado original.

## <a name="install-npm-package"></a>Instalar as pré-requisitos
Deve instalar o [nó] no seu computador local.  Deve também instalar o pacote de compatibilidade.  Após instalação do nó, pode executar o seguinte comando a partir de um cmd nova ou a linha de comandos PowerShell:

```npm i -g azure-mobile-apps-compatibility```

## <a name="obtain-ams-scripts"></a>Obter os Scripts de Mobile Services do Azure
* Inicie sessão no [Portal do Azure].
* Utilizar **todos os recursos** ou **serviços aplicacionais**, localizar o site de Mobile Services.
* No site, clique em **ferramentas** -> **Kudu** -> **aceda** para abrir o site do Kudu.
* Clique em **consola de depuração** -> **PowerShell** para abrir a consola de depuração.
* Navegue para `site/wwwroot/App_Data/config` clicando em cada diretório por sua vez
* Clique no ícone de transferência junto a `scripts` diretório.

Isto irá transferir os scripts no formato ZIP.  Criar um novo diretório no seu computador local e Descompacte o `scripts.ZIP` ficheiro no diretório.  Esta ação irá criar um `scripts` diretório.

## <a name="scaffold-app"></a>Estruturar o novo back-end do Mobile Apps do Azure
Execute o seguinte comando a partir do diretório que contém o diretório de scripts:

```scaffold-mobile-app scripts out```

Esta ação irá criar um estruturado back-end de Mobile Apps do Azure no `out` diretório.  Apesar de não ser obrigatório, é uma boa ideia confirmar o `out` diretório para um repositório de código de origem à sua escolha.

## <a name="deploy-ama-app"></a>Implementar o seu back-end do Mobile Apps do Azure
Durante a implementação, terá de fazer o seguinte:

1. Criar uma nova aplicação móvel no [Portal do Azure].
2. Execute o `createViews.sql` script na base de dados ligada.
3. Ligar a base de dados que está ligada ao seu serviço móvel para o novo serviço de aplicações.
4. Ligação quaisquer outros recursos (por exemplo, os Hubs de notificação) para o novo serviço de aplicações.
5. Implemente o código gerado para o novo site.

### <a name="create-a-new-mobile-app"></a>Criar uma nova aplicação móvel
1. Inicie sessão no [Portal do Azure].
2. Clique em **+NOVO** > **Web + Móvel** > **Aplicação Móvel** e indique um nome para o back-end da Aplicação Móvel.
3. Para o **Grupo de Recursos**, selecione um grupo de recursos existente ou crie um novo (utilizando o mesmo nome que a sua aplicação).

    Pode selecionar outro plano do Serviço de Aplicações ou criar um novo. Para mais informações sobre serviços aplicacionais planos e como criar um novo plano num preço diferente camada e na localização pretendida, consulte [descrição geral dos planos do App Service do Azure](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).
4. Para o **Plano do Serviço de Aplicações**, encontra-se selecionado o plano predefinido (no [Escalão Standard](https://azure.microsoft.com/pricing/details/app-service/)). Pode ainda selecionar um plano diferente ou [criar um novo](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md#create-an-app-service-plan). As definições do plano do Serviço de Aplicações determinam [a localização, as funcionalidades, o custo e os recursos de computação](https://azure.microsoft.com/pricing/details/app-service/) associados à aplicação.

    Depois de decidir o plano, clique em **Criar**. Esta ação cria o back-end da Aplicação Móvel.

### <a name="run-createviewssql"></a>Executar CreateViews.SQL
A aplicação estruturada contém um ficheiro chamado `createViews.sql`.  Este script tem de ser executado na base de dados de destino.  A cadeia de ligação da base de dados de destino pode ser obtida a partir do seu serviço móvel migrado do **definições** painel em **cadeias de ligação**.  É denominado `MS_TableConnectionString`.

Pode executar este script do SQL Server Management Studio ou Visual Studio.

### <a name="link-the-database-to-your-app-service"></a>Ligar a base de dados para o serviço de aplicações
Ligar a base de dados existente para o serviço de aplicações:

* No [Portal do Azure], abra o serviço de aplicações.
* Selecione **todas as definições** -> **ligações de dados**.
* Clique em **+ adicionar**.
* Na lista pendente, selecione **base de dados SQL**
* Em **base de dados SQL**, selecione a base de dados existente, em seguida, clique em **selecione**.
* Em **cadeia de ligação**, introduza o nome de utilizador e palavra-passe para a base de dados, em seguida, clique em **OK**.
* No **adicione ligações de dados** painel, clique em **OK**.

O nome de utilizador e palavra-passe podem ser encontradas visualizando a cadeia de ligação da base de dados de destino no seu serviço móvel migrados.

### <a name="set-up-authentication"></a>Configurar autenticação
Mobile Apps do Azure permite-lhe configurar o Azure Active Directory, Facebook, Google, Microsoft e Twitter autenticação no serviço.  Autenticação personalizado tem de ser desenvolvido separadamente.  Consulte o [conceitos de autenticação] documentação e [início rápido de autenticação] documentação para obter mais informações.  

## <a name="updating-clients"></a>Atualizar clientes móveis
Depois de ter um back-end de aplicação móvel operacional, pode trabalhar numa nova versão da aplicação cliente que consome-lo. As Mobile Apps também inclui uma nova versão do cliente SDKs e semelhante para a atualização do servidor acima, terá de remover todas as referências para os SDKs de serviços móveis antes de instalar as versões de aplicações móveis.

Uma das principais alterações entre as versões é que os construtores já não necessitam de uma chave de aplicação.
Agora simplesmente passa o URL da sua aplicação móvel. Por exemplo, nos clientes .NET, o `MobileServiceClient` construtor é agora:

        public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://contoso.azurewebsites.net" // URL of the Mobile App
        );

Pode ler sobre como instalar os novos SDKs e utilizando a estrutura nova através de ligações abaixo:

* [Versão Android 2.2 ou posterior](app-service-mobile-android-how-to-use-client-library.md)
* [versão 3.0.0 iOS ou posterior](app-service-mobile-ios-how-to-use-client-library.md)
* [O .NET (Windows/Xamarin) versão 2.0.0 ou posterior](app-service-mobile-dotnet-how-to-use-client-library.md)
* [Apache Cordova versão 2.0 ou posterior](app-service-mobile-cordova-how-to-use-client-library.md)

Se a aplicação faz com que a utilização de notificações push, anote as instruções de registo específicos para cada plataforma, como foram algumas alterações existe bem.

Quando tiver a nova versão de cliente pronta, experimente contra o projeto de servidor atualizado. Depois de confirmar que funciona, pode de versão uma nova versão da aplicação para clientes. Eventualmente, depois dos seus clientes tenham tido oportunidade para receber estas atualizações, pode eliminar a versão de Mobile Services da sua aplicação. Neste momento, completamente tiver atualizado para uma aplicação móvel do App Service utilizando o servidor de aplicações móveis SDK mais recente.

<!-- URLs. -->

[Portal do Azure]: https://portal.azure.com/
[Azure classic portal]: https://manage.windowsazure.com/
[que são Mobile Apps?]: app-service-mobile-value-prop.md
[I already use web sites and mobile services – how does App Service help me?]: /en-us/documentation/articles/app-service-mobile-value-prop-migration-from-mobile-services
[Mobile App Server SDK]: https://www.npmjs.com/package/azure-mobile-apps
[Create a Mobile App]: app-service-mobile-xamarin-ios-get-started.md
[Add push notifications to your mobile app]: app-service-mobile-xamarin-ios-get-started-push.md
[Add authentication to your mobile app]: app-service-mobile-xamarin-ios-get-started-users.md
[Azure Scheduler]: /en-us/documentation/services/scheduler/
[Web Job]: https://github.com/Azure/azure-webjobs-sdk/wiki
[How to use the .NET server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Migrate from Mobile Services to an App Service Mobile App]: app-service-mobile-migrating-from-mobile-services.md
[Migrate your existing Mobile Service to App Service]: app-service-mobile-migrating-from-mobile-services.md
[preços do serviço de aplicações]: https://azure.microsoft.com/en-us/pricing/details/app-service/
[.NET server SDK overview]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[conceitos de autenticação]: ../app-service/app-service-authentication-overview.md
[início rápido de autenticação]: app-service-mobile-auth.md

[Portal do Azure]: https://portal.azure.com/
[OData]: http://www.odata.org
[Promise]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise
[basicapp sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/basic-app
[todo sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/todo
[samples directory on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples
[static-schema sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/static-schema
[QueryJS]: https://github.com/Azure/queryjs
[Node.js Tools 1.1 for Visual Studio]: https://github.com/Microsoft/nodejstools/releases/tag/v1.1-RC.2.1
[mssql Node.js package]: https://www.npmjs.com/package/mssql
[Microsoft SQL Server 2014 Express]: http://www.microsoft.com/en-us/server-cloud/Products/sql-server-editions/sql-server-express.aspx
[ExpressJS Middleware]: http://expressjs.com/guide/using-middleware.html
[Winston]: https://github.com/winstonjs/winston
