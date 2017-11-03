---
title: "Gestão de registo"
description: "Este tópico explica como registar dispositivos com os hubs de notificação para receber notificações push."
services: notification-hubs
documentationcenter: .net
author: ysxu
manager: erikre
editor: 
ms.assetid: fd0ee230-132c-4143-b4f9-65cef7f463a1
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 06/29/2016
ms.author: yuaxu
ms.openlocfilehash: a1a349150ef4c7837932706f0c4fcc8d022ec7ab
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="registration-management"></a>Gestão de registos
## <a name="overview"></a>Descrição geral
Este tópico explica como registar dispositivos com os hubs de notificação para receber notificações push. O tópico descreve os registos a um nível elevado, em seguida, apresenta dois padrões de principais para registar dispositivos: registar o dispositivo diretamente para o hub de notificação e registo através de um back-end da aplicação. 

## <a name="what-is-device-registration"></a>O que é o registo de dispositivos
Registo de dispositivos com um Hub de notificação é realizado através de um **registo** ou **instalação**.

#### <a name="registrations"></a>Registos
Um registo associa o identificador do serviço de notificação de plataforma (PNS) para um dispositivo com as etiquetas e possivelmente um modelo. O identificador PNS pode ser um ChannelURI, o token do dispositivo ou o id de registo do GCM. As etiquetas são utilizadas para encaminhar as notificações para o conjunto correto de identificadores do dispositivo. Para obter mais informações, consulte [de encaminhamento e expressões de etiqueta](notification-hubs-tags-segment-push-message.md). Os modelos são utilizados para implementar transformação por registo. Para obter mais informações, consulte [modelos](notification-hubs-templates-cross-platform-push-messages.md).

#### <a name="installations"></a>Instalações
Uma instalação é um avançada as propriedades relacionadas com registo que inclui uma matriz de push. É a abordagem melhor e mais recente para registar os seus dispositivos. No entanto, não é suportado pelo .NET SDK do lado do cliente ([SDK dos Notification Hub para operações de back-end](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)) chegou.  Isto significa que se está a registar do próprio dispositivo cliente, terá de utilizar o [API REST dos Notification Hubs](https://msdn.microsoft.com/library/mt621153.aspx) abordagem para suportar instalações. Se estiver a utilizar um serviço de back-end, deverá conseguir utilizar [SDK dos Notification Hub para operações de back-end](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

Seguem-se algumas vantagens fundamentais para a utilização de instalações:

* Criar ou atualizar uma instalação totalmente é idempotent. Por isso, pode repetir sem as preocupações registos duplicados.
* O modelo de instalação torna mais fácil fazer individuais pushes - direcionada para dispositivos específicos. Uma tag de sistema **"$InstallationId: [installationId]"** é adicionado automaticamente com cada registo de instalação com base. Por isso, pode ligar a uma atividade send para esta etiqueta para um dispositivo específico de destino sem ser necessária codificação adicionais.
* Utilizar as instalações também permite atualizações do registo parcial. A atualização parcial de uma instalação é solicitada com um método de PATCH utilizando o [padrão de Patch de JSON](https://tools.ietf.org/html/rfc6902). Isto é particularmente útil quando pretende atualizar as etiquetas no registo. Não tem de importar o registo de todo e, em seguida, novamente a reenvie todas as etiquetas anteriores.

Uma instalação pode conter as seguintes propriedades. Para uma lista completa de Consulte de propriedades de instalação, [criar ou substituir uma instalação com a REST API](https://msdn.microsoft.com/library/azure/mt621153.aspx) ou [as propriedades de instalação](https://msdn.microsoft.com/library/azure/microsoft.azure.notificationhubs.installation_properties.aspx) para o.

    // Example installation format to show some supported properties
    {
        installationId: "",
        expirationTime: "",
        tags: [],
        platform: "",
        pushChannel: "",
        ………
        templates: {
            "templateName1" : {
                body: "",
                tags: [] },
            "templateName2" : {
                body: "",
                // Headers are for Windows Store only
                headers: {
                    "X-WNS-Type": "wns/tile" }
                tags: [] }
        },
        secondaryTiles: {
            "tileId1": {
                pushChannel: "",
                tags: [],
                templates: {
                    "otherTemplate": {
                        bodyTemplate: "",
                        headers: {
                            ... }
                        tags: [] }
                }
            }
        }
    }



É importante ter em atenção que as instalações por predefinição e registos já não está a expirar.

As instalações e registos tem de conter um identificador PNS válido para cada dispositivo/canal. Porque apenas podem ser obtidos identificadores PNS numa aplicação cliente no dispositivo, um padrão, é registado diretamente no que o dispositivo com a aplicação de cliente. Por outro lado, as considerações de segurança e a lógica de negócio relacionadas com as etiquetas podem implicar a gerir o registo de dispositivos na aplicação back-end. 

#### <a name="templates"></a>Modelos
Se pretender utilizar [modelos](notification-hubs-templates-cross-platform-push-messages.md), a instalação do dispositivo também conter todos os modelos associados com que o dispositivo num JSON formatar (consulte exemplo acima). Nomes dos modelos ajudam a modelos diferentes de destino para o mesmo dispositivo.

Tenha em atenção que cada nome de modelo mapeia para um corpo do modelo e um conjunto opcional de etiquetas. Além disso, cada plataforma pode ter propriedades do modelo adicionais. Para a loja Windows (WNS utilizando) e Windows Phone 8 (utilizando MPNS), um conjunto de cabeçalhos adicionais pode ser parte do modelo. No caso do APNs, pode definir uma propriedade de expiração para uma constante ou para uma expressão de modelo. Para uma lista completa de Consulte de propriedades de instalação, [criar ou substituir uma instalação com REST](https://msdn.microsoft.com/library/azure/mt621153.aspx) tópico.

#### <a name="secondary-tiles-for-windows-store-apps"></a>Secundários mosaicos para aplicações da loja Windows
Para aplicações de cliente da loja Windows, enviar notificações para mosaicos secundários é igual a enviar para um primário. Isto também é suportado em instalações. Tenha em atenção que os mosaicos secundários tem um ChannelUri diferentes, o que o SDK na sua aplicação de cliente processa de forma transparente.

O dicionário de SecondaryTiles utiliza o mesmo TileId que é utilizado para criar o objeto de SecondaryTiles na sua aplicação da loja Windows.
Tal como acontece com ChannelUri primário, pode alterar ChannelUris de mosaicos secundários em qualquer momento. Para manter as instalações no hub de notificação atualizado, o dispositivo deve atualizá-los com o ChannelUris atual dos mosaicos secundários.

## <a name="registration-management-from-the-device"></a>Gestão de registo do dispositivo
Quando a gerir o registo de dispositivos de aplicações de cliente, o back-end só é responsável pelo envio de notificações. As aplicações cliente mantêm identificadores PNS atualizados e registe as etiquetas. A imagem seguinte ilustra este padrão.

![](./media/notification-hubs-registration-management/notification-hubs-registering-on-device.png)

O dispositivo primeiro obtém o identificador PNS do PNS, em seguida, regista diretamente com o notification hub. Após o registo for bem sucedido, o back-end de aplicação pode enviar uma notificação de filtragem desse registo. Para obter mais informações sobre como enviar notificações, consulte [de encaminhamento e expressões de etiqueta](notification-hubs-tags-segment-push-message.md).
Tenha em atenção que neste caso, irá utilizar apenas escutar direitos de acesso os hubs de notificação do dispositivo. Para obter mais informações, consulte [segurança](notification-hubs-push-notification-security.md).

Registo do dispositivo é o método mais simples, mas tem algumas desvantagens.
A primeiro desvantagem é que as respetivas etiquetas só pode atualizar uma aplicação de cliente quando a aplicação está ativa. Por exemplo, se um utilizador tiver dois dispositivos registar etiquetas relacionados com a equipas de uma porta, quando o primeiro dispositivo regista uma tag adicional (por exemplo, Seahawks), o segundo dispositivo não irá receber as notificações sobre o Seahawks até que a aplicação no dispositivo segundo é executada uma segunda vez. Mais geralmente, quando as etiquetas são afetadas por vários dispositivos, gerir etiquetas de back-end é uma opção de desejados.
A segundo desvantagem da gestão do registo da aplicação de cliente é que, uma vez que as aplicações podem ser vítima de acesso ilícito, proteger o registo para etiquetas específicas requer cuidado extra, conforme explicado na secção "segurança de nível de etiqueta".

#### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-an-installation"></a>Código de exemplo para registar um hub de notificação de um dispositivo com uma instalação
Neste momento, isto só é suportado utilizando a [API REST dos Notification Hubs](https://msdn.microsoft.com/library/mt621153.aspx).

Também pode utilizar o método de PATCH utilizando o [padrão de Patch de JSON](https://tools.ietf.org/html/rfc6902) para atualizar a instalação.

    class DeviceInstallation
    {
        public string installationId { get; set; }
        public string platform { get; set; }
        public string pushChannel { get; set; }
        public string[] tags { get; set; }
    }

    private async Task<HttpStatusCode> CreateOrUpdateInstallationAsync(DeviceInstallation deviceInstallation,
         string hubName, string listenConnectionString)
    {
        if (deviceInstallation.installationId == null)
            return HttpStatusCode.BadRequest;

        // Parse connection string (https://msdn.microsoft.com/library/azure/dn495627.aspx)
        ConnectionStringUtility connectionSaSUtil = new ConnectionStringUtility(listenConnectionString);
        string hubResource = "installations/" + deviceInstallation.installationId + "?";
        string apiVersion = "api-version=2015-04";

        // Determine the targetUri that we will sign
        string uri = connectionSaSUtil.Endpoint + hubName + "/" + hubResource + apiVersion;

        //=== Generate SaS Security Token for Authorization header ===
        // See, https://msdn.microsoft.com/library/azure/dn495627.aspx
        string SasToken = connectionSaSUtil.getSaSToken(uri, 60);

        using (var httpClient = new HttpClient())
        {
            string json = JsonConvert.SerializeObject(deviceInstallation);

            httpClient.DefaultRequestHeaders.Add("Authorization", SasToken);

            var response = await httpClient.PutAsync(uri, new StringContent(json, System.Text.Encoding.UTF8, "application/json"));
            return response.StatusCode;
        }
    }

    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    string installationId = null;
    var settings = ApplicationData.Current.LocalSettings.Values;

    // If we have not stored a installation id in application data, create and store as application data.
    if (!settings.ContainsKey("__NHInstallationId"))
    {
        installationId = Guid.NewGuid().ToString();
        settings.Add("__NHInstallationId", installationId);
    }

    installationId = (string)settings["__NHInstallationId"];

    var deviceInstallation = new DeviceInstallation
    {
        installationId = installationId,
        platform = "wns",
        pushChannel = channel.Uri,
        //tags = tags.ToArray<string>()
    };

    var statusCode = await CreateOrUpdateInstallationAsync(deviceInstallation, 
                        "<HUBNAME>", "<SHARED LISTEN CONNECTION STRING>");

    if (statusCode != HttpStatusCode.Accepted)
    {
        var dialog = new MessageDialog(statusCode.ToString(), "Registration failed. Installation Id : " + installationId);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }
    else
    {
        var dialog = new MessageDialog("Registration successful using installation Id : " + installationId);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }



#### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-a-registration"></a>Código de exemplo para registar um hub de notificação de um dispositivo com um registo
Estes métodos criar ou atualizar um registo para o dispositivo no qual são denominados. Isto significa que para atualizar o identificador ou as etiquetas, tem de substituir o registo de todo. Lembre-se de que os registos são transitórios, pelo que deve ter sempre um arquivo fiável com as etiquetas atuais que necessita de um dispositivo específico.

    // Initialize the Notification Hub
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(listenConnString, hubName);

    // The Device id from the PNS
    var pushChannel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    // If you are registering from the client itself, then store this registration id in device
    // storage. Then when the app starts, you can check if a registration id already exists or not before
    // creating.
    var settings = ApplicationData.Current.LocalSettings.Values;

    // If we have not stored a registration id in application data, store in application data.
    if (!settings.ContainsKey("__NHRegistrationId"))
    {
        // make sure there are no existing registrations for this push handle (used for iOS and Android)    
        string newRegistrationId = null;
        var registrations = await hub.GetRegistrationsByChannelAsync(pushChannel.Uri, 100);
        foreach (RegistrationDescription registration in registrations)
        {
            if (newRegistrationId == null)
            {
                newRegistrationId = registration.RegistrationId;
            }
            else
            {
                await hub.DeleteRegistrationAsync(registration);
            }
        }

        newRegistrationId = await hub.CreateRegistrationIdAsync();

        settings.Add("__NHRegistrationId", newRegistrationId);
    }

    string regId = (string)settings["__NHRegistrationId"];

    RegistrationDescription registration = new WindowsRegistrationDescription(pushChannel.Uri);
    registration.RegistrationId = regId;
    registration.Tags = new HashSet<string>(YourTags);

    try
    {
        await hub.CreateOrUpdateRegistrationAsync(registration);
    }
    catch (Microsoft.WindowsAzure.Messaging.RegistrationGoneException e)
    {
        settings.Remove("__NHRegistrationId");
    }


## <a name="registration-management-from-a-backend"></a>Gestão de registo de um back-end
Gerir registos de back-end requer a escrever código adicional. A aplicação do dispositivo tem de fornecer o PNS atualizado processar para o back-end, sempre que a aplicação for iniciada (juntamente com as etiquetas e modelos) e o back-end tem de atualizar este identificador no hub de notificação. A imagem seguinte ilustra esta estrutura.

![](./media/notification-hubs-registration-management/notification-hubs-registering-on-backend.png)

As vantagens de gerir registos de back-end incluem a capacidade para modificar etiquetas para registos, mesmo quando a aplicação correspondente no dispositivo está inativa e para autenticar a aplicação de cliente antes de adicionar uma etiqueta para o respetivo registo.

#### <a name="example-code-to-register-with-a-notification-hub-from-a-backend-using-an-installation"></a>Código de exemplo para registar um hub de notificação de um back-end através de uma instalação
O dispositivo de cliente ainda obtém o respetivo identificador PNS e propriedades de instalação relevantes como antes e de que chama uma API personalizada no back-end que pode efetuar o registo e autorizar etiquetas etc. O back-end pode tirar partido de [SDK dos Notification Hub para operações de back-end](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

Também pode utilizar o método de PATCH utilizando o [padrão de Patch de JSON](https://tools.ietf.org/html/rfc6902) para atualizar a instalação.

    // Initialize the Notification Hub
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(listenConnString, hubName);

    // Custom API on the backend
    public async Task<HttpResponseMessage> Put(DeviceInstallation deviceUpdate)
    {

        Installation installation = new Installation();
        installation.InstallationId = deviceUpdate.InstallationId;
        installation.PushChannel = deviceUpdate.Handle;
        installation.Tags = deviceUpdate.Tags;

        switch (deviceUpdate.Platform)
        {
            case "mpns":
                installation.Platform = NotificationPlatform.Mpns;
                break;
            case "wns":
                installation.Platform = NotificationPlatform.Wns;
                break;
            case "apns":
                installation.Platform = NotificationPlatform.Apns;
                break;
            case "gcm":
                installation.Platform = NotificationPlatform.Gcm;
                break;
            default:
                throw new HttpResponseException(HttpStatusCode.BadRequest);
        }


        // In the backend we can control if a user is allowed to add tags
        //installation.Tags = new List<string>(deviceUpdate.Tags);
        //installation.Tags.Add("username:" + username);

        await hub.CreateOrUpdateInstallationAsync(installation);

        return Request.CreateResponse(HttpStatusCode.OK);
    }


#### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-a-registration-id"></a>Código de exemplo para registar um hub de notificação de um dispositivo utilizando um id de registo
Do seu back-end da aplicação, pode executar operações básicas de CRUDS registarem. Por exemplo:

    var hub = NotificationHubClient.CreateClientFromConnectionString("{connectionString}", "hubName");

    // create a registration description object of the correct type, e.g.
    var reg = new WindowsRegistrationDescription(channelUri, tags);

    // Create
    await hub.CreateRegistrationAsync(reg);

    // Get by id
    var r = await hub.GetRegistrationAsync<RegistrationDescription>("id");

    // update
    r.Tags.Add("myTag");

    // update on hub
    await hub.UpdateRegistrationAsync(r);

    // delete
    await hub.DeleteRegistrationAsync(r);


O back-end deve processar simultaneidade entre as atualizações do registo. Barramento de serviço oferece controlo de simultaneidade otimista para a gestão de registo. O nível de HTTP, isto é implementado com a utilização de ETag no operações de gestão do registo. Esta funcionalidade é transparente utilizada pelo Microsoft SDKs, que acionar uma excepção se uma atualização foi rejeitada por motivos de concorrência. O back-end de aplicação é responsável por processar estas exceções e repetir a atualização, se necessário.

