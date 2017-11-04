---
title: "Autenticação de utilização do Azure AD para aceder à API de serviços de suporte de dados do Azure com o .NET | Microsoft Docs"
description: "Este tópico mostra como utilizar a autenticação do Azure Active Directory (Azure AD) para aceder à API de serviços de suporte de dados do Azure (AMS) com o .NET."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/17/2017
ms.author: juliako
ms.openlocfilehash: a9355200a05a3aa1b494b76977d38ddc42bfe179
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-ad-authentication-to-access-azure-media-services-api-with-net"></a>Utilize a autenticação do Azure AD para aceder à API de serviços de suporte de dados do Azure com o .NET

A partir do windowsazure.mediaservices 4.0.0.4, Media Services do Azure suporta a autenticação com base no Azure Active Directory (Azure AD). Este tópico mostra como utilizar a autenticação do Azure AD para aceder à API de serviços de suporte de dados do Azure com o Microsoft .NET.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure. Para obter mais detalhes, veja [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Uma conta dos Media Services. Para obter mais informações, consulte [criar uma conta de Media Services do Azure no portal do Azure](media-services-portal-create-account.md).
- A versão mais recente [NuGet](https://www.nuget.org/packages/windowsazure.mediaservices) pacote.
- Familiaridade com o tópico [aceder à API do Azure suporte de dados de serviços com descrição geral da autenticação do AAD](media-services-use-aad-auth-to-access-ams-api.md). 

Quando estiver a utilizar a autenticação do Azure AD com Media Services do Azure, pode autenticar-se de uma das seguintes formas:

- **Autenticação de utilizador** efetua a autenticação de uma pessoa que está a utilizar a aplicação para interagir com recursos de Media Services do Azure. A aplicação interativa do primeiro deve solicitar credenciais ao utilizador. Um exemplo é uma aplicação de consola de gestão que é utilizada por utilizadores autorizados para monitorizar as tarefas de codificação ou transmissão em direto. 
- **Autenticação principal de serviço** autentica um serviço. As aplicações que utilizam frequentemente este método de autenticação são aplicações que executem serviços de daemon, serviços de camada média ou tarefas agendadas, tais como as web apps, aplicações de função, as logic apps, APIs ou micro-serviços.

>[!IMPORTANT]
>Atualmente, o serviço de multimédia do Azure suporta um modelo de autenticação do serviço de controlo de acesso do Azure. No entanto, a autorização de controlo de acesso é vai preterido no dia 1 de Junho de 2018. Recomendamos a migração para um modelo de autenticação do Azure Active Directory logo que possível.

## <a name="get-an-azure-ad-access-token"></a>Obtenha o token de acesso do Azure AD

Para ligar a API de serviços de suporte de dados do Azure com a autenticação do Azure AD, a aplicação de cliente tem de pedir um token de acesso do Azure AD. Quando utiliza o SDK do cliente do .NET dos Media Services, muitos dos detalhes sobre como adquirir um token de acesso do Azure AD são encapsulados e simplificados para si no [AzureAdTokenProvider](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.Authentication/AzureAdTokenProvider.cs) e [AzureAdTokenCredentials](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.Authentication/AzureAdTokenCredentials.cs)classes. 

Por exemplo, não precisa de fornecer a autoridade do Azure AD, o URI do recurso dos Media Services ou o nativo detalhes da aplicação do Azure AD. Estes são os valores conhecidos que já estão configurados por classe de fornecedor de tokens de acesso do Azure AD. 

Se não estiver a utilizar o SDK de .NET do serviço de suporte de dados do Azure, recomendamos que utilize o [Azure AD Authentication Library](../active-directory/develop/active-directory-authentication-libraries.md). Para obter os valores para os parâmetros que precisa para utilizar com o Azure AD Authentication Library, consulte [utilizar o portal do Azure para aceder às definições de autenticação do Azure AD](media-services-portal-get-started-with-aad.md).

Também tem a opção de substituir a implementação predefinida do **AzureAdTokenProvider** com a sua própria implementação.

## <a name="install-and-configure-azure-media-services-net-sdk"></a>Instalar e configurar o SDK .NET do Azure Media Services

>[!NOTE] 
>Para utilizar autenticação do Azure AD com o SDK de .NET de Media Services, tem de ter a versão mais recente [NuGet](https://www.nuget.org/packages/windowsazure.mediaservices) pacote. Além disso, adicione uma referência para o **Microsoft.IdentityModel.Clients.ActiveDirectory** assemblagem. Se estiver a utilizar uma aplicação existente, incluem o **Microsoft.WindowsAzure.MediaServices.Client.Common.Authentication.dll** assemblagem. 

1. Crie uma nova aplicação de consola do c# no Visual Studio.
2. Utilize o [windowsazure.mediaservices](https://www.nuget.org/packages/windowsazure.mediaservices) pacote NuGet para instalar **SDK .NET do Azure Media Services**. 

    Para adicionar referências através do NuGet, siga os passos seguintes: no **Explorador de soluções**, faça duplo clique no nome do projeto e, em seguida, selecione **pacotes NuGet gerir**. Em seguida, procure **windowsazure.mediaservices** e selecione **instalar**.
    
    -ou-

    Execute o seguinte comando **consola do Gestor de pacotes** no Visual Studio.

        Install-Package windowsazure.mediaservices -Version 4.0.0.4

3. Adicionar **utilizando** ao código de origem.

        using Microsoft.WindowsAzure.MediaServices.Client; 

## <a name="use-user-authentication"></a>Utilize a autenticação de utilizador

Para ligar a API de serviços de suporte de dados do Azure com a opção de autenticação de utilizador, a aplicação de cliente precisa de pedir um token do Azure AD utilizando os seguintes parâmetros:  

- Azure AD ponto final de inquilino. As informações de inquilino podem ser obtidas a partir do portal do Azure. Coloque o cursor sobre o utilizador com sessão iniciada no canto superior direito.
- URI do recurso dos Media Services.
- ID de cliente de serviços de suporte de dados (nativo) de aplicação. 
- URI de redirecionamento da aplicação de Media Services (nativo). 

Os valores para estes parâmetros podem ser encontrados na **AzureEnvironments.AzureCloudEnvironment**. O **AzureEnvironments.AzureCloudEnvironment** constante é um programa auxiliar no SDK do .NET para obter o ambiente direita variável definições para um centro de dados do Azure público. 

Contém definições de ambiente de pré-definido para aceder aos serviços de suporte de dados só de centros de dados público. Para sovereign ou government regiões de nuvem, pode utilizar **AzureChinaCloudEnvironment**, **AzureUsGovernmentEnvrionment**, ou **AzureGermanCloudEnvironment** respetivamente.

Exemplo de código seguinte cria um token:
    
    var tokenCredentials = new AzureAdTokenCredentials("microsoft.onmicrosoft.com", AzureEnvironments.AzureCloudEnvironment);
    var tokenProvider = new AzureAdTokenProvider(tokenCredentials);
  
Para iniciar a programação com os Media Services, terá de criar um **CloudMediaContext** instância que representa o contexto de servidor. O **CloudMediaContext** inclui as referências a coleções importantes, incluindo tarefas, recursos, ficheiros, as políticas de acesso e localizadores. 

Também tem de passar o **URI para serviços de REST do suporte de dados do recurso** para o **CloudMediaContext** construtor. Para obter o URI do recurso de REST de Media Services, iniciar sessão no portal do Azure, selecione a sua conta de Media Services do Azure, selecione **acesso à API**e, em seguida, selecione **ligar aos serviços de suporte de dados do Azure com a autenticação de utilizador**. 

O exemplo de código seguinte cria um **CloudMediaContext** instância:

    CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);

O exemplo seguinte mostra como criar o token do Azure AD e o contexto:

    namespace AADAuthSample
    {
        class Program
        {
            static void Main(string[] args)
            {
                // Specify your Azure AD tenant domain, for example "microsoft.onmicrosoft.com".
                var tokenCredentials = new AzureAdTokenCredentials("{YOUR AAD TENANT DOMAIN HERE}", AzureEnvironments.AzureCloudEnvironment);
    
                var tokenProvider = new AzureAdTokenProvider(tokenCredentials);
    
                // Specify your REST API endpoint, for example "https://accountname.restv2.westcentralus.media.azure.net/API".
                CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);
    
                var assets = context.Assets;
                foreach (var a in assets)
                {
                    Console.WriteLine(a.Name);
                }
            }
    
        }
    }

>[!NOTE]
>Se obtiver uma exceção que diz "o servidor remoto devolveu um erro: não autorizado (401)," consulte o [controlo de acesso](media-services-use-aad-auth-to-access-ams-api.md#access-control) secção de aceder a API de serviços de suporte de dados do Azure com a descrição geral da autenticação do Azure AD.

## <a name="use-service-principal-authentication"></a>Utilizar a autenticação principal de serviço
    
Para ligar à API de serviços de suporte de dados do Azure com a opção de principal do serviço, a sua aplicação de camada média necessidades (web API ou a aplicação web) para pedidos de um token do Azure AD com os seguintes parâmetros:  

- Azure AD ponto final de inquilino. As informações de inquilino podem ser obtidas a partir do portal do Azure. Coloque o cursor sobre o utilizador com sessão iniciada no canto superior direito.
- URI do recurso dos Media Services.
- Os valores de aplicação do Azure AD: o **ID de cliente** e **segredo do cliente**.

Os valores para o **ID de cliente** e **segredo do cliente** parâmetros podem ser encontrados no portal do Azure. Para obter mais informações, consulte [introdução à autenticação do Azure AD através do portal do Azure](media-services-portal-get-started-with-aad.md).

Exemplo de código seguinte cria um token ao utilizar o **AzureAdTokenCredentials** construtor que assuma **AzureAdClientSymmetricKey** como um parâmetro: 
    
    var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}", 
                                new AzureAdClientSymmetricKey("{YOUR CLIENT ID HERE}", "{YOUR CLIENT SECRET}"), 
                                AzureEnvironments.AzureCloudEnvironment);

    var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

Também pode especificar o **AzureAdTokenCredentials** construtor que assuma **AzureAdClientCertificate** como parâmetro. 

Para obter instruções sobre como criar e configurar um certificado num formulário que pode ser utilizado pelo Azure AD, consulte [autenticar para o Azure AD nas aplicações de daemon com certificados - passos de configuração manual](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential/blob/master/Manual-Configuration-Steps.md).

    var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}", 
                                new AzureAdClientCertificate("{YOUR CLIENT ID HERE}", "{YOUR CLIENT CERTIFICATE THUMBPRINT}"), 
                                AzureEnvironments.AzureCloudEnvironment);

Para iniciar a programação com os Media Services, terá de criar um **CloudMediaContext** instância que representa o contexto de servidor. Também tem de passar o **URI para serviços de REST do suporte de dados do recurso** para o **CloudMediaContext** construtor. Pode obter o **URI para serviços de REST do suporte de dados do recurso** valor do portal do Azure, bem como.

O exemplo de código seguinte cria um **CloudMediaContext** instância:

    CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);
    
O exemplo seguinte mostra como criar o token do Azure AD e o contexto:

    namespace AADAuthSample
    {
    
        class Program
        {
            static void Main(string[] args)
            {
                var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}", 
                                            new AzureAdClientSymmetricKey("{YOUR CLIENT ID HERE}", "{YOUR CLIENT SECRET}"), 
                                            AzureEnvironments.AzureCloudEnvironment);
            
                var tokenProvider = new AzureAdTokenProvider(tokenCredentials);
    
                // Specify your REST API endpoint, for example "https://accountname.restv2.westcentralus.media.azure.net/API".      
                CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);
    
                var assets = context.Assets;
                foreach (var a in assets)
                {
                    Console.WriteLine(a.Name);
                }
    
                Console.ReadLine();
            }
    
        }
    }

## <a name="next-steps"></a>Passos seguintes

Introdução ao [carregar ficheiros para a conta](media-services-dotnet-upload-files.md).
