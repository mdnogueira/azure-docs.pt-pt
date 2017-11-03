---
title: "Utilizando o .NET SDK para aceder a APIs de serviço do Azure Mobile Engagement"
description: "Descreve como utilizar o SDK .NET do Mobile Engagement para aceder às APIs de serviço do Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: c07728aa-43f2-4238-8b4a-c9eddf9d838b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 6a497189268c5a1b7e269cc57904ebc77c1906fd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="using-net-sdk-to-access-azure-mobile-engagement-service-apis"></a>Utilizando o .NET SDK para aceder a APIs de serviço do Azure Mobile Engagement
O Azure Mobile Engagement expõe um conjunto de APIs para gerir dispositivos, campanhas de alcance/enviar por Push etc. Para interagir com estas APIs, também fornecemos-lhe um [ficheiro Swagger](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-mobileengagement/2014-12-01/swagger/mobile-engagement.json) que pode utilizar com as ferramentas para gerar SDKs para o idioma preferencial. Recomendamos que utilize o [AutoRest](https://github.com/Azure/AutoRest) ferramenta para gerar o SDK do nosso ficheiro Swagger.

> [!NOTE]
> O serviço Azure Mobile Engagement será extinto em março de 2018 e, atualmente, apenas está disponível para os clientes existentes. Para obter mais informações, veja [Mobile Engagement](https://azure.microsoft.com/en-us/services/mobile-engagement/).

Foi criado um SDK .NET de forma semelhante, que lhe permite interagir com estas APIs utilizando um wrapper c# e não tiver a negociação de token de autenticação e atualize si próprio.  

Este exemplo atravessa o conjunto de passos a seguir para utilizar o SDK .NET:

1. Primeiro de tudo, terá de configurar a autenticação para as suas APIs utilizando o Azure Active Directory, conforme descrito [aqui](mobile-engagement-api-authentication.md#authentication). No final destes passos, deve ter um **SubscriptionId**, **TenantId**, **ApplicationId** e **segredo**. 
2. Utilizaremos uma simple aplicação de consola do Windows para demonstrar como trabalhar com o SDK .NET com o cenário de criação de uma campanha de anúncio. Por isso, abra o Visual Studio e crie um **aplicação de consola**.   
3. Em seguida terá de transferir o SDK do .NET que está disponível como **biblioteca de gestão do Microsoft Azure Engagement** na Galeria Nuget [aqui](https://www.nuget.org/packages/Microsoft.Azure.Management.Engagement/).
   Se estiver a instalar o Nuget do Visual Studio, terá de garantir que dispõe verificação marcada a **incluir pré-lançamento** opção ao procurar o pacote:
   
    ![][1]
4. No `Program.cs` ficheiro, adicione os seguintes espaços de nomes:
   
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure.Management.Engagement;
        using Microsoft.Azure.Management.Engagement.Models;
5. Em seguida, é necessário definir as seguintes constantes, que iremos utilizar para autenticação e interagir com a aplicação de Mobile Engagement no qual está a criar a campanha de anúncio:
   
        // For authentication
        const string TENANT_ID = "<Your TenantId>";
        const string CLIENT_ID = "<Your Application Id>";
        const string CLIENT_SECRET = "<Your Secret>";
        const string SUBSCRIPTION_ID = "<Your Subscription Id>";
   
        // This is the Azure Resource group concept for grouping together resources 
        //  see here: https://azure.microsoft.com/en-us/documentation/articles/resource-group-portal/
        const string RESOURCE_GROUP = "";
   
        // For Mobile Engagement operations
        // App Collection Name 
        const string APP_COLLECTION_NAME = "";
        // Application Resource Name - make sure you are using the one as specified in the Azure portal (NOT the App Name)
        const string APP_RESOURCE_NAME = "";
6. Definir o `EngagementManagementClient` variável que iremos utilizar para chamar os métodos de SDK do Mobile Engagement:
   
        static EngagementManagementClient engagementClient; 
7. Adicione o seguinte ao seu `Main` método:
   
        try
            {
                // Initialize the Engagement SDK to call out APIs. 
                InitEngagementClient().Wait();
   
                // Create a Reach campaign
                CreateCampaign().Wait();
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.InnerException.Message);
                throw ex;
            }
8. Definir o método seguinte que trata ao inicializar o `EngagementManagementClient` pelo primeiro a autenticação e, em seguida, associar-se a aplicação de Mobile Engagement no qual pretende criar a campanha de anúncio:
   
        private static async Task InitEngagementClient()
        {
            var credentials = await ApplicationTokenProvider.LoginSilentAsync(TENANT_ID, CLIENT_ID, CLIENT_SECRET);
            engagementClient = new EngagementManagementClient(credentials) { SubscriptionId = SUBSCRIPTION_ID };
   
            // This is the Azure concept of ResourceGroup
            engagementClient.ResourceGroupName = RESOURCE_GROUP;
   
            // This is your Mobile Engagement App Collection & App Resource Name in which you create the campaign
            engagementClient.AppCollection = APP_COLLECTION_NAME;
            engagementClient.AppName = APP_RESOURCE_NAME;
        }
   
   > [!IMPORTANT]
   > Tenha em atenção que tem de utilizar o **nome de recurso de aplicação** conforme definido no portal de gestão do Azure para o parâmetro de AppName. 
   > 
   > 
9. Por último, definir o método de CreateCampaign que tratará de utilizar o EngagementClient anteriormente inicializado para criar uma simples **em qualquer altura** & **apenas de notificação** campanha com um título e a mensagem: 
   
        private async static Task CreateCampaign()
        {
            //  Refer to the Announcement Campaign format from here - 
            //      https://msdn.microsoft.com/en-us/library/azure/mt683751.aspx
            // Make sure you are passing all the non-optional parameters
            Campaign parameters = new Campaign(
                name:"WelcomeCampaign",
                notificationTitle: "Welcome", 
                notificationMessage: "Thank you for installing the app!",
                type:"only_notif",
                deliveryTime:"any"
                );
   
            // Refer to the Campaign Kinds from here - https://msdn.microsoft.com/en-us/library/azure/mt683742.aspx
            CampaignStateResult result = 
                await engagementClient.Campaigns.CreateAsync(CampaignKinds.Announcements, parameters);
            Console.WriteLine("Campaign Id '{0}' was created successfully and it is in '{1}' state", result.Id, result.State);
        }
10. Execute a aplicação de consola, verá o seguinte na criação com êxito da campanha:
    
    **Id de campanha '159' foi criado com êxito e se encontra num estado "Rascunho"**

<!-- Images. -->

[1]: ./media/mobile-engagement-dotnet-sdk-service-api/include-prerelease.png
