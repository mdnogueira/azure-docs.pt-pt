---
title: "As notificações push de perímetros geográficos e os Hubs de Notificação do Azure e Dados Geográficos do Bing | Microsoft Docs"
description: "Neste tutorial, irá aprender como entregar notificações push com base na localização com Notification Hubs do Azure e Dados Geográficos do Bing."
services: notification-hubs
documentationcenter: windows
keywords: "notificação push, notificação push"
author: dend
manager: yuaxu
editor: dend
ms.assetid: f41beea1-0d62-4418-9ffc-c9d70607a1b7
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 09/15/2017
ms.author: dendeli
ms.openlocfilehash: a416edaded8aa04c3229a5788d648de0a6afe2b6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="geo-fenced-push-notifications-with-azure-notification-hubs-and-bing-spatial-data"></a>Notificações push de perímetro geográfico com Notification Hubs do Azure e Dados Geográficos do Bing
> [!NOTE]
> Para concluir este tutorial, tem de ter uma conta ativa do Azure. Se não tiver uma conta, pode criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02).
> 
> 

Neste tutorial, vai aprender a entrega de notificações push com base na localização com Notification Hubs do Azure e Dados Geográficos do Bing, alavancada no seio da aplicação Plataforma Universal do Windows.

## <a name="prerequisites"></a>Pré-requisitos
Primeiro e mais importante, confirme que tem todos os pré-requisitos de software e de serviço:

* [Visual Studio 2015 Update 1](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx) ou posterior (a [Edição Community](https://go.microsoft.com/fwlink/?LinkId=691978&clcid=0x409) também serve). 
* Versão mais recente do [Azure SDK](https://azure.microsoft.com/downloads/). 
* [Conta do Dev Center do Bing Maps](https://www.bingmapsportal.com/) (pode criar uma gratuitamente e associá-la à conta Microsoft). 

## <a name="getting-started"></a>Introdução
Vamos começar por criar o projeto. No Visual Studio, inicie um novo projeto do tipo **Aplicação em Branco (Universal Windows)**.

![](./media/notification-hubs-geofence/notification-hubs-create-blank-app.png)

Assim que a criação de projeto estiver concluída, deverá ter o escudo para a aplicação em si. Vamos agora configurar tudo para a infraestrutura de perímetro geográfico. Uma vez que vamos utilizar serviços Bing para isto, existe um ponto de final público da API REST que nos permite molduras consultar molduras de localização específicas:

    http://spatial.virtualearth.net/REST/v1/data/

Para a pôr a funcionar, tem de especificar os parâmetros seguintes:

* **ID da Origem de Dados** e **Nome da Origem de Dados** – na API Bing Maps, origens de dados contêm vários metadados agrupados, como localizações e horas de expediente da operação. Pode ler mais acerca deles aqui. 
* **Nome da Entidade** – a entidade que pretende utilizar como um ponto de referência para a notificação. 
* **Chave de API do Bing Maps** – esta é a chave que obteve anteriormente quando criou a conta do Dev Center do Bing.

Vamos fazer uma descrição profunda da configuração de cada um dos elementos acima.

## <a name="setting-up-the-data-source"></a>Configurar a origem de dados
Pode configurar a origem de dados no Bing Maps Dev Center. Na barra de navegação superior, escolha **Origens de dados** > **Gerir Origens de dados**.

![](./media/notification-hubs-geofence/bing-maps-manage-data.png)

Se nunca tiver trabalhado com a API Bing Maps, não terá, provavelmente, nenhuma origem de dados presente, pelo que pode criar simplesmente uma nova ao escolher **Origens de Dados** > **Carregar dados**. Certifique-se de que preenche todos os campos obrigatórios:

![](./media/notification-hubs-geofence/bing-maps-create-data.png)

Poderá estar a perguntar-se – o que é o ficheiro de dados e o que deve estar carregar? Para efeitos deste teste, podemos utilizar simplesmente amostra baseada em pipe que enquadra uma área da frente marítima de São Francisco:

    Bing Spatial Data Services, 1.0, TestBoundaries
    EntityID(Edm.String,primaryKey)|Name(Edm.String)|Longitude(Edm.Double)|Latitude(Edm.Double)|Boundary(Edm.Geography)
    1|SanFranciscoPier|||POLYGON ((-122.389825 37.776598,-122.389438 37.773087,-122.381885 37.771849,-122.382186 37.777022,-122.389825 37.776598))

A expressão acima representa esta entidade:

![](./media/notification-hubs-geofence/bing-maps-geofence.png)

Copie e cole a cadeia acima num novo ficheiro, guarde-o como **NotificationHubsGeofence.pipe** e carregue-o no Bing Dev Center.

> [!NOTE]
> Poderá ser-lhe pedido que especifique uma nova chave para a **Chave Mestra** diferente da **Chave de Consulta**. Crie simplesmente uma nova chave através do dashboard e atualize a página de carregamento da origem de dados.
> 
> 

Uma vez carregado o ficheiro de dados, terá de se certificar de que pode publicar a origem de dados. 

Aceda a **Gerir Origens de Dados**, como fizemos acima, localize na lista a sua origem de dados e escolha **Publicar**, na coluna **Ações**. Daí a pouco, deverá ver a sua origem de dados no separador **Origens dos Dados Publicados**:

![](./media/notification-hubs-geofence/bing-maps-published-data.png)

Se escolher **Editar**, poderá ver (de relance) as localizações que introduziu:

![](./media/notification-hubs-geofence/bing-maps-data-details.png)

Nesta fase, o portal não mostra os limites do perímetro geográfico que criou – só precisa de uma confirmação de que a localização especificada está na vizinhança certa.

Tem, agora, todos os requisitos para a origem de dados. Para obter os detalhes acerca do URL do pedido para a chamada de API, no Bing Maps Dev Center, escolha **Origens de dados** e selecione **Informações das Origens de Dados**.

![](./media/notification-hubs-geofence/bing-maps-data-info.png)

O que procuramos aqui é o **URL da Consulta**. Este é o ponto final relativamente ao qual podemos executar consultas para verificar se o dispositivo está ou não atualmente dentro dos limites de uma localização. Para executar esta verificação, basta simplesmente executar uma chamada GET no URL de consulta, com os parâmetros seguintes anexados:

    ?spatialFilter=intersects(%27POINT%20LONGITUDE%20LATITUDE)%27)&$format=json&key=QUERY_KEY

Estará, assim, a especificar um ponto de destino que obtemos do dispositivo e o Bing Maps efetuará automaticamente os cálculos para ver se está dentro do perímetro geográfico. Uma vez executado o pedido por meio de um browser (ou cURL), obterá uma resposta JSON padrão:

![](./media/notification-hubs-geofence/bing-maps-json.png)

Esta resposta só aparece quando o ponto está, de facto, dentro dos limites designados. Se não estiver, obterá um registo de **resultados** vazio:

![](./media/notification-hubs-geofence/bing-maps-nores.png)

## <a name="setting-up-the-uwp-application"></a>Configurar a aplicação de UWP
Agora que já temos a origem de dados pronta, podemos começar a trabalhar na aplicação UWP de que fizemos anteriormente o arranque.

Primeiro e mais importante, temos de ativar os serviços de localização para a nossa aplicação. Para tal, faça abra o ficheiro `Package.appxmanifest` no **Explorador de Soluções**.

![](./media/notification-hubs-geofence/vs-package-manifest.png)

No separador de propriedades do pacote que acabou de abrir, escolha **Capacidades** e confirme que seleciona **Localização**:

![](./media/notification-hubs-geofence/vs-package-location.png)

Como a capacidade de localização é declarada, crie uma nova pasta na solução com o nome `Core` e adicione um novo ficheiro dentro da mesma, denominado `LocationHelper.cs`:

![](./media/notification-hubs-geofence/vs-location-helper.png)

A classe `LocationHelper` em si é bastante básica nesta fase – tudo o que faz é permitir-nos obter a localização do utilizador através da API do sistema:

    using System;
    using System.Threading.Tasks;
    using Windows.Devices.Geolocation;

    namespace NotificationHubs.Geofence.Core
    {
        public class LocationHelper
        {
            private static readonly uint AppDesiredAccuracyInMeters = 10;

            public async static Task<Geoposition> GetCurrentLocation()
            {
                var accessStatus = await Geolocator.RequestAccessAsync();
                switch (accessStatus)
                {
                    case GeolocationAccessStatus.Allowed:
                        {
                            Geolocator geolocator = new Geolocator { DesiredAccuracyInMeters = AppDesiredAccuracyInMeters };

                            return await geolocator.GetGeopositionAsync();
                        }
                    default:
                        {
                            return null;
                        }
                }
            }

        }
    }

Pode ler mais sobre como obter a localização do utilizador em aplicações UWP no [Documento MSDN](https://msdn.microsoft.com/library/windows/apps/mt219698.aspx) oficial.

Para verificar se a aquisição de localização está, de facto, a funcionar, abra o lado de código da sua página principal (`MainPage.xaml.cs`). Crie um processador de eventos para o evento `Loaded` no construtor `MainPage`:

    public MainPage()
    {
        this.InitializeComponent();
        this.Loaded += MainPage_Loaded;
    }

A implementação do processador de eventos é feita do seguinte modo:

    private async void MainPage_Loaded(object sender, RoutedEventArgs e)
    {
        var location = await LocationHelper.GetCurrentLocation();

        if (location != null)
        {
            Debug.WriteLine(string.Concat(location.Coordinate.Longitude,
                " ", location.Coordinate.Latitude));
        }
    }

Repare que declarámos o processador como assíncrono porque `GetCurrentLocation` é passível de espera e necessita, por conseguinte, ser executada num contexto assíncrono. Além disso, como em determinadas circunstâncias poderá ficar com uma localização nula (por exemplo, os serviços de localização estão desativados ou foram negadas permissões à aplicação para aceder à localização), temos de certificar-nos de que a mesma é corretamente processada com uma verificação nula.

Execute a aplicação. Certifique-se de que permite o acesso de localização:

![](./media/notification-hubs-geofence/notification-hubs-location-access.png)

Uma vez iniciada a aplicação, deverá conseguir ver as coordenadas na janela **Saída**:

![](./media/notification-hubs-geofence/notification-hubs-location-output.png)

Agora que já sabe que a aquisição de localização funciona, pode remover à vontade o processador de eventos de teste Carregado, uma vez que não voltará a utilizá-lo.

O passo seguinte é capturar as alterações de localização. Para isso, vamos voltar para à classe `LocationHelper` e adicionar o processador de eventos para `PositionChanged`:

    geolocator.PositionChanged += Geolocator_PositionChanged;

A implementação apresenta as coordenadas de localização na janela **Saída**:

    private static async void Geolocator_PositionChanged(Geolocator sender, PositionChangedEventArgs args)
    {
        await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
        {
            Debug.WriteLine(string.Concat(args.Position.Coordinate.Longitude, " ", args.Position.Coordinate.Latitude));
        });
    }

## <a name="setting-up-the-backend"></a>Configurar o back-end
Transfira o [Exemplo de Back-end do .NET a partir do GitHub](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/NotifyUsers). Uma vez concluída a transferência, abra a pasta `NotifyUsers` e, subsequentemente, o ficheiro `NotifyUsers.sln`.

Defina o projeto `AppBackend` como **Projeto de Arranque** e inicie-o.

![](./media/notification-hubs-geofence/vs-startup-project.png)

O projeto já está configurado para enviar notificações push para os dispositivos de destino, sendo assim vamos precisar de fazer apenas duas coisas – trocar a cadeia de ligação correta para o Notification Hub e adicionar identificação de limites para enviar a notificação apenas quando o utilizador estiver dentro do perímetro geográfico.

Para configurar a cadeia de ligação, na pasta `Models` abra `Notifications.cs`. A função `NotificationHubClient.CreateClientFromConnectionString` deve conter as informações acerca do Notification Hub que pode obter no [Portal do Azure](https://portal.azure.com) (procure dentro do painel **Políticas de Acesso** em **Definições**). Guarde a configuração de ficheiro atualizada.

Temos, agora, de criar um modelo para o resultado da API do Bing Maps. A forma mais fácil de o fazer é abrir a pasta `Models` e escolher **Adicionar** > **Classe**. Dê-lhe o nome `GeofenceBoundary.cs`. Quando tiver terminado, copie o JSON da resposta da API que discutimos na primeira secção e, no Visual Studio, utilize **Editar** > **Colar Especial** > **Colar JSON como Classes**. 

Desta forma, garantimos que será anulada a serialização do objeto exatamente como se projetou. O conjunto de classes resultante deve assemelhar-se a isto:

    namespace AppBackend.Models
    {
        public class Rootobject
        {
            public D d { get; set; }
        }

        public class D
        {
            public string __copyright { get; set; }
            public Result[] results { get; set; }
        }

        public class Result
        {
            public __Metadata __metadata { get; set; }
            public string EntityID { get; set; }
            public string Name { get; set; }
            public float Longitude { get; set; }
            public float Latitude { get; set; }
            public string Boundary { get; set; }
            public string Confidence { get; set; }
            public string Locality { get; set; }
            public string AddressLine { get; set; }
            public string AdminDistrict { get; set; }
            public string CountryRegion { get; set; }
            public string PostalCode { get; set; }
        }

        public class __Metadata
        {
            public string uri { get; set; }
        }
    }

Em seguida, abra `Controllers` > `NotificationsController.cs`. É necessário otimizar a chamada Publicar para ter em conta a latitude e longitude do destino. Para o fazer, adicione simplesmente duas cadeias à assinatura da função – `latitude` e `longitude`.

    public async Task<HttpResponseMessage> Post(string pns, [FromBody]string message, string to_tag, string latitude, string longitude)

Crie uma nova classe no projeto denominada `ApiHelper.cs` – utilizá-la-emos para ligar ao Bing para verificar intersecções de limites do ponto. Implemente uma função `IsPointWithinBounds`, como esta:

    public class ApiHelper
    {
        public static readonly string ApiEndpoint = "{YOUR_QUERY_ENDPOINT}?spatialFilter=intersects(%27POINT%20({0}%20{1})%27)&$format=json&key={2}";
        public static readonly string ApiKey = "{YOUR_API_KEY}";

        public static bool IsPointWithinBounds(string longitude,string latitude)
        {
            var json = new WebClient().DownloadString(string.Format(ApiEndpoint, longitude, latitude, ApiKey));
            var result = JsonConvert.DeserializeObject<Rootobject>(json);
            if (result.d.results != null && result.d.results.Count() > 0)
            {
                return true;
            }
            else
            {
                return false;
            }
        }
    }

> [!NOTE]
> Não se esqueça de substituir o Ponto Final de API com o URL de consulta que obteve anteriormente no Dev Center do Bing (o mesmo se aplica à chave de API). 
> 
> 

Se não existirem resultados da consulta, isso significa que o ponto especificado está dentro dos limites do perímetro geográfico, voltamos pois a `true`. Se não existirem resultados, o Bing estará a informar-nos que o ponto está fora do intervalo de pesquisa, logo inserimos `false`.

De volta a `NotificationsController.cs`, crie uma verificação mesmo antes da instrução switch:

    if (ApiHelper.IsPointWithinBounds(longitude, latitude))
    {
        switch (pns.ToLower())
        {
            case "wns":
                //// Windows 8.1 / Windows Phone 8.1
                var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
                            "From " + user + ": " + message + "</text></binding></visual></toast>";
                outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

                // Windows 10 specific Action Center support
                toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
                            "From " + user + ": " + message + "</text></binding></visual></toast>";
                outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

                break;
        }
    }

Deste modo, a notificação é apenas enviada quando o ponto estiver dentro dos limites.

## <a name="testing-push-notifications-in-the-uwp-app"></a>Testar notificações push na aplicação UWP
De volta à aplicação UWP, já deveremos poder testar as notificações. N classe `LocationHelper`, crie uma nova função – `SendLocationToBackend`:

    public static async Task SendLocationToBackend(string pns, string userTag, string message, string latitude, string longitude)
    {
        var POST_URL = "http://localhost:8741/api/notifications?pns=" +
            pns + "&to_tag=" + userTag + "&latitude=" + latitude + "&longitude=" + longitude;

        using (var httpClient = new HttpClient())
        {
            try
            {
                await httpClient.PostAsync(POST_URL, new StringContent("\"" + message + "\"",
                    System.Text.Encoding.UTF8, "application/json"));
            }
            catch (Exception ex)
            {
                Debug.WriteLine(ex.Message);
            }
        }
    }

> [!NOTE]
> Troque o `POST_URL` pela localização da aplicação Web implementada que criámos na secção anterior. Para já, é possível executá-la localmente, mas à medida que for trabalhando na implementação de uma versão pública, será necessário alojá-la num fornecedor externo.
> 
> 

Vamos agora garantir que registamos a aplicação UWP para notificações push. No Visual Studio, escolha **Projeto** > **Armazenar** > **Associar a aplicação à loja**.

![](./media/notification-hubs-geofence/vs-associate-with-store.png)

Depois de iniciar sessão na conta de programador, certifique-se de que seleciona uma aplicação existente ou cria uma nova e associe-lhe o pacote. 

Aceda ao Dev Center e abra a aplicação que acabou de criar. Escolha **Serviços** > **Notificações Push** > **Site dos Serviços Live**.

![](./media/notification-hubs-geofence/ms-live-services.png)

No site, tome nota do **Segredo da Aplicação** e do **SID do Pacote**. Vai precisar de ambos no Portal do Azure. Abra o seu hub de notificação, escolha **Definições** > **Serviços de Notificação** > **Windows (WNS)** e introduza as informações nos campos obrigatórios.

![](./media/notification-hubs-geofence/notification-hubs-wns.png)

Escolha **Guardar**.

Abra **Referências**, no **Explorador de Soluções**, e selecione **Gerir Pacotes NuGet**. Precisamos de adicionar uma referência à **Biblioteca gerida do Service Bus do Microsoft Azure**. Para tal, basta procurar `WindowsAzure.Messaging.Managed` e adicioná-la ao projeto.

![](./media/notification-hubs-geofence/vs-nuget.png)

Para fins de teste, podemos criar novamente o processador de eventos `MainPage_Loaded` e adicionar-lhe este fragmento de código:

    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    var hub = new NotificationHub("HUB_NAME", "HUB_LISTEN_CONNECTION_STRING");
    var result = await hub.RegisterNativeAsync(channel.Uri);

    // Displays the registration ID so you know it was successful
    if (result.RegistrationId != null)
    {
        Debug.WriteLine("Reg successful.");
    }

O procedimento acima regista a aplicação com o Notification Hub. Está pronto para funcionar! 

Em `LocationHelper`, no processador `Geolocator_PositionChanged`, pode adicionar um fragmento de código de teste que coloca forçadamente a localização dentro do perímetro geográfico:

    await LocationHelper.SendLocationToBackend("wns", "TEST_USER", "TEST", "37.7746", "-122.3858");

Como não estamos a transmitir as coordenadas reais (que podem não estar de momento dentro dos limites) e estamos a utilizar valores de teste predefinidos, veremos aparecer uma notificação na atualização:

![](./media/notification-hubs-geofence/notification-hubs-test-notification.png)

## <a name="next-steps"></a>Passos seguintes
Existem alguns passos que poderá ter de seguir para além dos acima descritos para se certificar de que a solução está pronta para ser produzida.

Primeiro e mais importante, poderá ter de garantir que os perímetros geográficos são dinâmicos. Isto irá necessitar de algum trabalho adicional com a API do Bing para que seja possível carregar novos limites no seio da origem de dados existente. Consulte a [Documentação da API dos Serviços de Dados Geográficos do Bing](https://msdn.microsoft.com/library/ff701734.aspx) para obter mais detalhes sobre o assunto.

Segundo, à medida que vai trabalhando para garantir que a entrega é efetuada aos participantes corretos, poderá querer segmentá-los através de [etiquetagem](notification-hubs-tags-segment-push-message.md).

A solução mostrada acima descreve um cenário em que poderá ter uma grande variedade de plataformas de destino, por isso não limitámos o perímetro geográfico para as capacidades específicas do sistema. Dito isto, a Plataforma Universal do Windows oferece capacidades para [detetar perímetros geográficos logo na primeira execução](https://msdn.microsoft.com/windows/uwp/maps-and-location/set-up-a-geofence).

Para obter mais detalhes sobre as capacidades dos Notification Hubs, consulte o nosso [portal de documentação](https://azure.microsoft.com/documentation/services/notification-hubs/).

