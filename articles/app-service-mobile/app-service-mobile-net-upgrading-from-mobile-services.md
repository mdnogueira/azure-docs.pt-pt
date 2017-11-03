---
title: Atualizar dos Mobile Services para o App Service do Azure
description: "Saiba como atualizar facilmente a sua aplicação de Mobile Services a uma aplicação de Mobile do serviço de aplicações"
services: app-service\mobile
documentationcenter: 
author: ggailey777
manager: syntaxc4
editor: 
ms.assetid: 9c0ac353-afb6-462b-ab94-d91b8247322f
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: glenga
ms.openlocfilehash: 81c8ba6245565368eab4cdaca297ff7656180605
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="upgrade-your-existing-net-azure-mobile-service-to-app-service"></a>Atualizar o seu serviço móvel do Azure de .NET atual para o App Service
Serviço de aplicações móveis é uma nova forma de criar aplicações móveis com o Microsoft Azure. Para obter mais informações, consulte [que são Mobile Apps?].

Este tópico descreve como atualizar uma aplicação existente de back-end .NET dos Mobile Services do Azure para um novo Mobile Apps do App Service. Enquanto executa esta atualização, a aplicação de Mobile Services existente pode continuar a funcionar.   Se precisar de atualizar uma aplicação de back-end Node.js, consulte [atualizar os Mobile Services do Node.js](app-service-mobile-node-backend-upgrading-from-mobile-services.md).

Quando um back-end móvel é atualizado para o App Service do Azure, tem acesso a todas as funcionalidades do serviço de aplicações e são cobrados de acordo com [preços do serviço de aplicações], não Mobile Services preços.

## <a name="migrate-vs-upgrade"></a>Migrar vs atualização
[!INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

> [!TIP]
> É recomendado que [efetuar uma migração](app-service-mobile-migrating-from-mobile-services.md) antes de avançar através de uma atualização. Desta forma, pode colocar em pausa ambas as versões da aplicação do mesmo plano do App Service e implicar sem custos adicionais.
>
>

### <a name="improvements-in-mobile-apps-net-server-sdk"></a>Melhoramentos no servidor de aplicações .NET dos Mobile SDK
Atualizar para o novo [SDK de aplicações móveis](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) fornece as seguintes vantagens:

* Mais flexibilidade na NuGet dependências. O ambiente de alojamento já não fornece as suas próprias versões dos pacotes de NuGet, pelo que pode utilizar versões compatíveis alternativas. No entanto, se existirem novas bugfixes críticos ou atualizações de segurança para o servidor SDK dos Mobile ou dependências, tem de atualizar manualmente o serviço.
* Mais flexibilidade no SDK móvel. Explicitamente pode controlar as funcionalidades e as rotas estão configuradas, como a autenticação, APIs de tabela e o ponto final de registo de push. Para obter mais informações, consulte [como utilizar o SDK do servidor .NET para Mobile Apps do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).
* Suporte para outros tipos de projeto do ASP.NET e as rotas. Pode agora alojar controladores do MVC e Web API no mesmo projecto como o projeto de back-end móvel.
* Suporte para novas funcionalidades de autenticação do serviço de aplicações, que permitem-lhe utilizar uma configuração de autenticação comuns nas suas web apps e móveis.

## <a name="overview"></a>Descrição geral básica de atualização
Em muitos casos, a atualização será tão simple como mudar para o novo servidor de aplicações móveis SDK e republishing o código para uma nova instância de aplicação móvel. Não existem, no entanto alguns cenários que irão exigir algumas configurações adicionais, como cenários de autenticação avançadas e trabalhar com tarefas agendadas. Cada um destes é descrita nas secções posteriores.

> [!TIP]
> Recomenda-se que leia e compreenda o resto deste tópico completamente antes de iniciar uma atualização. Tome nota de quaisquer funcionalidades utilizar indicadas abaixo.
>
>

O cliente de Mobile Services SDKs são **não** compatível com o novo servidor de aplicações móveis do SDK. Para fornecer a continuidade do serviço para a sua aplicação, não deve publicar as alterações a um site publicados clientes atualmente a funcionar. Em vez disso, deve criar uma nova aplicação móvel que funciona como um duplicado. Pode colocar esta aplicação no mesmo plano de serviço de aplicações para evitar incorrer em custos financeiros adicionais.

Em seguida, terá de duas versões da aplicação: um que permanece igual e serve aplicações publicadas no caráter e outro que pode, em seguida, atualizar e de destino com uma nova versão de cliente. Pode mover e testar o seu código ao seu ritmo, mas, deve certificar-se de que quaisquer correções de erros que efetuar ser aplicadas para ambos. Uma vez sentir que um número pretendido de aplicações no difundidas tem atualizado para a versão mais recente do cliente, é possível eliminar a aplicação migrada original se pretendidos ao nível.

O contorno completo para o processo de atualização é o seguinte:

1. Criar uma nova aplicação móvel
2. Atualizar o projeto para utilizar os novos SDKs de servidor
3. Uma nova versão da sua aplicação de cliente da versão
4. (Opcional) Eliminar a instância original do migrados

## <a name="mobile-app-version"></a>Criar uma segunda instância de aplicação
Atualizar o primeiro passo consiste em criar o recurso de aplicação móvel que irá alojar a nova versão da aplicação. Se já tiver migrado um serviço móvel, irá querer criar esta versão no mesmo plano de alojamento. Abra o [portal do Azure] e navegue para a aplicação migrada. Anote o plano do App Service que é executada.

Em seguida, crie a segunda instância de aplicação, seguindo o [instruções de criação de back-end .NET](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#create-app). Quando lhe for pedido para selecionar de o plano do App Service ou "plano de alojamento" Escolher o plano da sua aplicação migrada.

Será provavelmente pretende utilizar a mesma base de dados e o Hub de notificação, tal como fez nos Mobile Services. Pode copiar estes valores, abrindo [portal do Azure] e navegar para a aplicação original, em seguida, clique em **definições** > **definições da aplicação**. Em **cadeias de ligação**, cópia `MS_NotificationHubConnectionString` e `MS_TableConnectionString`. Navegue para o novo site de atualização e cole-os no, substituindo os valores existentes. Repita este processo para quaisquer outras definições de aplicação às suas necessidades de aplicação. Se não utilizar um serviço migrado, pode ler as cadeias de ligação e as definições de aplicação do **configurar** separador da secção de Mobile Services a [portal clássico do Azure].

Faça uma cópia do projeto ASP.NET para a sua aplicação e publicá-lo no seu novo site. Utilizando uma cópia da sua aplicação de cliente atualizada com o novo URL, confirme que tudo funciona conforme esperado.

## <a name="updating-the-server-project"></a>Atualizar o projeto de servidor
As Mobile Apps fornece uma nova [do SDK da aplicação do Mobile] que fornece muito a mesma funcionalidade que o tempo de execução de Mobile Services. Em primeiro lugar, deve remover todas as referências aos pacotes dos Mobile Services. No Gestor de pacotes NuGet, procure `WindowsAzure.MobileServices.Backend`. A maioria das aplicações verá vários pacotes aqui, incluindo `WindowsAzure.MobileServices.Backend.Tables` e `WindowsAzure.MobileServices.Backend.Entity`. Nesse caso, começa com o pacote mais baixo na árvore da dependência, tais como `Entity`e removê-lo. Quando lhe for pedido, não remova todos os pacotes dependentes. Repita este processo até ter removido `WindowsAzure.MobileServices.Backend` próprio.

Neste momento, terá um projeto que já não referencia Mobile Services SDKs.

Em seguida irá adicionar as referências do SDK de aplicações móveis. Para esta atualização, a maior parte dos programadores será útil transferir e instalar o `Microsoft.Azure.Mobile.Server.Quickstart` do pacote, como isto irá solicitar no conjunto de todo necessário.

Será bastante alguns erros do compilador resultantes de diferenças entre os SDKs, mas estes são fáceis de endereço e são abordadas no resto nesta secção.

### <a name="base-configuration"></a>Configuração base
Em seguida, no WebApiConfig.cs, pode substituir:

        // Use this class to set configuration options for your mobile service
        ConfigOptions options = new ConfigOptions();

        // Use this class to set WebAPI configuration options
        HttpConfiguration config = ServiceConfig.Initialize(new ConfigBuilder(options));

com o

        HttpConfiguration config = new HttpConfiguration();
        new MobileAppConfiguration()
            .UseDefaultConfiguration()
        .ApplyTo(config);

> [!NOTE]
> Se pretender saber mais sobre o novo servidor de .NET SDK e como adicionar/remover funcionalidades da sua aplicação, volte a ver o [como utilizar o SDK do servidor .NET] tópico.
>
>

Se a aplicação permite utilizar as funcionalidades de autenticação, também terá de registar um middleware OWIN. Neste caso, deve mover o código de configuração acima para uma nova classe de arranque da OWIN.

1. Adicione o pacote NuGet `Microsoft.Owin.Host.SystemWeb` se este já não está incluído no seu projeto.
2. No Visual Studio, clique no seu projeto e selecione **adicionar** -> **Novo Item**. Selecione **Web** -> **geral** -> **classe de Startup da OWIN**.
3. Mover o código acima para MobileAppConfiguration de `WebApiConfig.Register()` para o `Configuration()` método da sua nova classe de arranque.

Certifique-se de que o `Configuration()` método termina com:

        app.UseWebApi(config)
        app.UseAppServiceAuthentication(config);

Existem alterações adicionais relacionadas com autenticação abordados na secção autenticação completa abaixo.

### <a name="working-with-data"></a>Trabalhar com dados
Nos Mobile Services, o nome de aplicação móvel servidos como o nome de esquema predefinido na configuração do Entity Framework.

Para se certificar de que tem o mesmo esquema referenciado como anteriormente, utilize o seguinte para definir o esquema em DbContext para a sua aplicação:

        string schema = System.Configuration.ConfigurationManager.AppSettings.Get("MS_MobileServiceName");

Certifique-se que tiver MS_MobileServiceName definido se o fizer acima. Também pode fornecer outro nome de esquema se a aplicação personalizado isto anteriormente.

### <a name="system-properties"></a>Propriedades do sistema
#### <a name="naming"></a>Atribuição de nomes
No servidor SDK de Mobile Services do Azure, as propriedades do sistema sempre contêm um caráter de sublinhado duplo (`__`) prefixo para as propriedades:

* __createdAt
* __updatedAt
* __deleted
* __version

O cliente de Mobile Services SDKs tem lógica especial para analisar as propriedades do sistema neste formato.

Mobile Apps do Azure, as propriedades do sistema já não tem um formato especial em tem os seguintes nomes:

* createdAt
* updatedAt
* eliminar
* Versão

O cliente de Mobile Apps SDKs utilizar novos nomes de propriedades do sistema, pelo que não são necessárias para o código de cliente alterações. No entanto, se estiver a efetuar diretamente chamadas REST ao seu serviço, em seguida, deve alterar as suas consultas em conformidade.

#### <a name="local-store"></a>Arquivo local
As alterações aos nomes das propriedades do sistema significam que uma base de dados local para serviços móveis sincronização offline não é compatível com as aplicações móveis. Se for possível, deve evitar a atualização de cliente foram enviadas para o servidor de aplicações de Mobile Services para Mobile Apps até depois das alterações pendentes. Em seguida, a aplicação atualizada deve utilizar um novo nome de ficheiro de base de dados.

Se uma aplicação de cliente é atualizada de Mobile Services para aplicações móveis enquanto existirem offline alterações pendentes na fila de operação, em seguida, a base de dados do sistema deve ser atualizada para utilizar novos nomes de coluna. No iOS, isto pode ser conseguido utilizando migrações simples para alterar os nomes das colunas. No Android e o cliente gerido .NET, deve escrever SQL personalizada para mudar o nome as colunas para as suas tabelas de objeto de dados.

No iOS, deve alterar o esquema de dados de núcleos para as entidades de dados fazer corresponder o seguinte. Tenha em atenção que as propriedades `createdAt`, `updatedAt` e `version` deixará de ter um `ms_` prefixo:

| Atributo | Tipo | Nota |
| --- | --- | --- |
| ID |Cadeia, marcada como necessária |chave primária no arquivo remoto |
| createdAt |Data |(opcional) é mapeado para a propriedade de sistema createdAt |
| updatedAt |Data |(opcional) é mapeado para a propriedade de sistema updatedAt |
| Versão |Cadeia |(opcional) utilizado para detetar conflitos, é mapeado para a versão |

#### <a name="querying-system-properties"></a>Consultar as propriedades do sistema
Nos Mobile Services do Azure, as propriedades do sistema não são enviadas por predefinição, mas apenas quando forem necessários utilizando a cadeia de consulta `__systemProperties`. Em contrapartida, no sistema de Mobile Apps do Azure propriedades são **sempre selecionado** , uma vez que fazem parte do modelo de objeto do SDK de servidor.

Esta alteração afeta principalmente implementações personalizadas de gestores de domínio, por exemplo, as extensões de `MappedEntityDomainManager`. Nos Mobile Services, se um cliente solicita nunca quaisquer propriedades do sistema, é possível utilizar um `MappedEntityDomainManager` que, na verdade, não é mapeado todas as propriedades. No entanto, nas Mobile Apps do Azure, estas propriedades não mapeadas fará com que um erro em consultas GET.

A forma mais fácil para resolver o problema está a modificar a sua DTOs para que estes herdam `ITableData` em vez de `EntityData`. Em seguida, adicione o `[NotMapped]` atributo para os campos que devem ser omitidos.

Por exemplo, o seguinte define `TodoItem` sem propriedades de sistema:

    using System.ComponentModel.DataAnnotations.Schema;

    public class TodoItem : ITableData
    {
        public string Text { get; set; }

        public bool Complete { get; set; }

        public string Id { get; set; }

        [NotMapped]
        public DateTimeOffset? CreatedAt { get; set; }

        [NotMapped]
        public DateTimeOffset? UpdatedAt { get; set; }

        [NotMapped]
        public bool Deleted { get; set; }

        [NotMapped]
        public byte[] Version { get; set; }
    }

Nota: Se obtiver erros no `NotMapped`, adicione uma referência à assemblagem `System.ComponentModel.DataAnnotations`.

### <a name="cors"></a>CORS
Mobile Services incluídos algumas suporte para CORS encapsulamento a solução de CORS do ASP.NET. Esta camada de encapsulamento de aplicações foi removida para o programador mais controlo, pelo que pode aproveitar diretamente [suporte de CORS do ASP.NET](http://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api).

As principais áreas de preocupação se utilizando a CORS são que os `eTag` e `Location` cabeçalhos tem de ser permitidos por ordem para o cliente SDKs para funcionar corretamente.

### <a name="push-notifications"></a>Notificações Push
Para enviar por push, o item principal que pode encontrar em falta do SDK do servidor é a classe de PushRegistrationHandler. Registos são processados de forma ligeiramente diferente em Mobile Apps e tagless registos estão ativados por predefinição. Gerir as etiquetas pode ser realizada com APIs personalizadas. Consulte o [etiquetas a registar](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#tags) instruções para obter mais informações.

### <a name="scheduled-jobs"></a>Tarefas agendadas
Tarefas agendadas não são incorporadas em Mobile Apps, pelo que quaisquer tarefas existentes na sua back-end de .NET, terá de ser atualizado individualmente. Uma das alternativas consiste em criar um agendada [Web Job] no site de código de aplicação móvel. Também pode configurar um controlador que contém o código de tarefa e configurar o [agendador do Azure] para aceder esse ponto final na agenda esperada.

### <a name="miscellaneous-changes"></a>Diversas alterações
Todos os ApiControllers que serão consumidas por um cliente para dispositivos móveis agora tem de ter o `[MobileAppController]` atributo. Já não é incluído por predefinição, para que outros ApiControllers ir afetada pelo móvel ao mesmo tempo.

O `ApiServices` objeto já não faz parte do SDK. Para aceder às definições de aplicação móvel, pode utilizar o seguinte:

    MobileAppSettingsDictionary settings = this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();

Da mesma forma, o registo agora é feito utilizando a escrita de rastreio ASP.NET padrão:

    ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
    traceWriter.Info("Hello, World");  

## <a name="authentication"></a>Considerações de autenticação
Os componentes de autenticação de Mobile Services agora tem sido movidos para a funcionalidade de autenticação/autorização do App Service. Pode saber sobre a ativação desta para o seu site ao ler o [adicionar autenticação à sua aplicação móvel](app-service-mobile-ios-get-started-users.md) tópico.

Para alguns fornecedores, como o AAD, Facebook e Google, devem ser capazes de tirar partido de registo existente da sua aplicação de cópia. Basta tem navegue até ao portal do fornecedor de identidade e adicionar um novo URL de redirecionamento para o registo. Em seguida, configure a aplicação serviço de autenticação/autorização com o ID de cliente e o segredo.

### <a name="controller-action-authorization"></a>Autorização de ação de controlador
As instâncias do `[AuthorizeLevel(AuthorizationLevel.User)]` atributo agora tem de ser alterado para utilizar o ASP.NET padrão `[Authorize]` atributo. Além disso, os controladores são agora anónimo por predefinição, tal como em outras aplicações de ASP.NET.
Se estava a utilizar uma das outras opções de AuthorizeLevel, tais como administrador ou de aplicação, tenha em atenção que estes são removidos. Em vez disso, pode configurar AuthorizationFilters para segredos partilhados ou configurar um Principal de serviço do AAD para ativar as chamadas de serviços em segurança.

### <a name="getting-additional-user-information"></a>Obter informações adicionais de utilizadores
Pode obter informações de utilizador adicionais, incluindo os tokens de acesso através de `GetAppServiceIdentityAsync()` método:

        FacebookCredentials creds = await this.User.GetAppServiceIdentityAsync<FacebookCredentials>();

Além disso, se a sua aplicação demora dependências no utilizador IDs, por exemplo, armazene-os numa base de dados, é importante ter em atenção que os IDs de utilizador entre os Mobile Services e as Mobile Apps do App Service são diferentes. Ainda pode obter o ID de utilizador do Mobile Services, embora. Todas as subclasses de ProviderCredentials tem uma propriedade de ID de utilizador. Por isso, continuar do exemplo antes de:

        string mobileServicesUserId = creds.Provider + ":" + creds.UserId;

Se a sua aplicação demorar quaisquer dependências IDs de utilizador, é importante que que utilize o mesmo registo com um fornecedor de identidade se for possível. Os IDs de utilizador normalmente passam para o registo de aplicação que foi utilizado para que introduzir um novo registo foi possível criar a problemas com correspondência de utilizadores aos respetivos dados.

### <a name="custom-authentication"></a>Autenticação personalizado
Se a aplicação estiver a utilizar uma solução de autenticação personalizado, deverá certificar-se de que o site atualizado tem acesso ao sistema. Siga as instruções para a autenticação personalizada numa nova o [descrição geral do SDK do .NET servidor] para integrar a sua solução. Tenha em atenção que os componentes de autenticação personalizado ainda estão em pré-visualização.

## <a name="updating-clients"></a>Atualizar clientes
Depois de ter um back-end de aplicação móvel operacional, pode trabalhar numa nova versão da aplicação cliente que consome-lo. As Mobile Apps também inclui uma nova versão do cliente SDKs e semelhante para a atualização do servidor acima, terá de remover todas as referências para os SDKs de serviços móveis antes de instalar as versões de aplicações móveis.

Uma das principais alterações entre as versões é que os construtores já não necessitam de uma chave de aplicação. Agora simplesmente passa o URL da sua aplicação móvel. Por exemplo, nos clientes .NET, o `MobileServiceClient` construtor é agora:

        public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://contoso.azurewebsites.net", // URL of the Mobile App
        );

Pode ler sobre como instalar os novos SDKs e utilizando a estrutura nova através de ligações abaixo:

* [versão 3.0.0 iOS ou posterior](app-service-mobile-ios-how-to-use-client-library.md)
* [O .NET (Windows/Xamarin) versão 2.0.0 ou posterior](app-service-mobile-dotnet-how-to-use-client-library.md)

Se a aplicação faz com que a utilização de notificações push, anote as instruções de registo específicos para cada plataforma, como foram algumas alterações existe bem.

Quando tiver a nova versão de cliente pronta, experimente contra o projeto de servidor atualizado. Depois de confirmar que funciona, pode de versão uma nova versão da aplicação para clientes. Eventualmente, depois dos seus clientes tenham tido oportunidade para receber estas atualizações, pode eliminar a versão de Mobile Services da sua aplicação. Neste momento, completamente tiver atualizado para uma aplicação móvel do App Service utilizando o servidor de aplicações móveis SDK mais recente.

<!-- URLs. -->

[portal do Azure]: https://portal.azure.com/
[portal clássico do Azure]: https://manage.windowsazure.com/
[que são Mobile Apps?]: app-service-mobile-value-prop.md
[I already use web sites and mobile services – how does App Service help me?]: /en-us/documentation/articles/app-service-mobile-value-prop-migration-from-mobile-services
[do SDK da aplicação do Mobile]: http://www.nuget.org/packages/microsoft.azure.mobile.server
[Create a Mobile App]: app-service-mobile-xamarin-ios-get-started.md
[Add push notifications to your mobile app]: app-service-mobile-xamarin-ios-get-started-push.md
[Add authentication to your mobile app]: app-service-mobile-xamarin-ios-get-started-users.md
[agendador do Azure]: /en-us/documentation/services/scheduler/
[Web Job]: https://github.com/Azure/azure-webjobs-sdk/wiki
[como utilizar o SDK do servidor .NET]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Migrate from Mobile Services to an App Service Mobile App]: app-service-mobile-migrating-from-mobile-services.md
[Migrate your existing Mobile Service to App Service]: app-service-mobile-migrating-from-mobile-services.md
[preços do serviço de aplicações]: https://azure.microsoft.com/en-us/pricing/details/app-service/
[descrição geral do SDK do .NET servidor]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
