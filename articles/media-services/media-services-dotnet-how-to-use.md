---
title: "Como configurar o computador para o desenvolvimento de serviços de suporte de dados com o .NET"
description: "Saiba mais sobre os pré-requisitos para os Media Services utilizando o SDK de Media Services para .NET. Além disso, saiba como criar uma aplicação do Visual Studio."
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: ec2804c7-c656-4fbf-b3e4-3f0f78599a7f
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/23/2017
ms.author: juliako
ms.openlocfilehash: 024b4cbb13001d67e7c1f0b86a84dfb43478c49d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="media-services-development-with-net"></a>Desenvolvimento de Media Services com .NET
[!INCLUDE [media-services-selector-setup](../../includes/media-services-selector-setup.md)]

Este tópico descreve como começar a desenvolver aplicações de Media Services utilizando o .NET.

O **SDK .NET do Azure Media Services** biblioteca permite-lhe programar nos serviços de suporte de dados através do .NET. Para facilitar o mesmo desenvolver com o .NET, o **extensões do SDK .NET do Azure suporte de dados de serviços** biblioteca é fornecida. Esta biblioteca contém um conjunto de métodos de extensão e funções de programa auxiliar que simplificam o código de .NET. Ambas as bibliotecas estão disponíveis através de **NuGet** e **GitHub**.

## <a name="prerequisites"></a>Pré-requisitos
* Uma conta dos Media Services numa subscrição Azure nova ou existente. Consulte o tópico [Como Criar uma Conta dos Media Services](media-services-portal-create-account.md).
* Sistemas operativos: Windows 10, Windows 7, Windows 2008 R2 ou Windows 8.
* .NET framework 4.5.
* Visual Studio.

## <a name="create-and-configure-a-visual-studio-project"></a>Criar e configurar um projeto de Visual Studio
Esta secção mostra como criar um projeto no Visual Studio e defina-o para o desenvolvimento de Media Services.  Neste caso, o projeto é uma aplicação de consola c# Windows, mas os mesmos passos de configuração mostrados aqui aplicam-se para outros tipos de projetos, que pode criar para aplicações de serviços de suporte de dados (por exemplo, uma aplicação do Windows Forms ou uma aplicação Web de ASP.NET).

Esta secção mostra como utilizar **NuGet** adicionar extensões do SDK .NET dos Media Services e outras bibliotecas dependentes.

Em alternativa, pode obter os bits do SDK .NET dos Media Services mais recentes a partir do GitHub ([github.com/Azure/azure-sdk-for-media-services](https://github.com/Azure/azure-sdk-for-media-services) ou [github.com/Azure/azure-sdk-for-media-services-extensions](https://github.com/Azure/azure-sdk-for-media-services-extensions)), compilar a solução e adicione as referências para o projeto de cliente. Todas as dependências necessárias obterem transferiu e extraiu automaticamente.

1. Crie uma nova Aplicação de Consola C# no Visual Studio. Introduza o **nome**, **localização**, e **nome da solução**e, em seguida, clique em OK.
2. Compilar a solução.
3. Utilize **NuGet** para instalar e adicionar **extensões do SDK .NET do Azure suporte de dados de serviços** (**windowsazure.mediaservices.extensions**). Ao instalar este pacote, também é instalado o **SDK do .NET dos Media Services** e são adicionadas todas as outras dependências necessárias.
   
    Certifique-se de que tem a versão mais recente do NuGet instalado. Para obter mais instruções de instalação e informações, consulte [NuGet](http://nuget.codeplex.com/).

    1. No Explorador de soluções, faça duplo clique o nome do projeto e escolha **gerir pacotes NuGet**.

    2. É apresentada a caixa de diálogo Gerir pacotes NuGet.

    3. Na Galeria Online, procure as extensões de MediaServices do Azure, escolha **extensões do SDK .NET do Azure suporte de dados de serviços** (**windowsazure.mediaservices.extensions**) e, em seguida, clique em de  **Instalar** botão.
   
    4. O projeto é modificado e são adicionadas referências para as extensões de SDK do .NET de serviços de suporte de dados, o SDK .NET dos Media Services e outras assemblagens dependentes.
4. Para promover um limpeza ambiente de desenvolvimento, considere ativar restauro do pacote NuGet. Para obter mais informações, consulte [restauro do pacote NuGet "](http://docs.nuget.org/consume/package-restore).
5. Adicione uma referência a **System** assemblagem. Esta assemblagem contém o System. **ConfigurationManager** classe que é utilizado para aceder aos ficheiros de configuração (por exemplo, App. config).
   
    1. Para adicionar referências utilizando a caixa de diálogo Gerir referências, faça duplo clique no nome do projeto no Explorador de soluções. Em seguida, clique em **adicionar**, em seguida, clique em **referência...** .
   
    2. É apresentada a caixa de diálogo Gerir referências.
    3. Em assemblagens do .NET framework, localize e selecione a assemblagem System e prima **OK**.
6. Abra o ficheiro App. config e adicione um **appSettings** secção para o ficheiro.     
   
    Defina os valores que são necessárias para estabelecer a ligação para a API de serviços de suporte de dados. Para obter mais informações, consulte [aceder à API de serviços de suporte de dados do Azure com a autenticação do Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

    Se estiver a utilizar [autenticação de utilizador](media-services-use-aad-auth-to-access-ams-api.md#types-of-authentication) o ficheiro de configuração provavelmente terão valores para o domínio de inquilino do Azure AD e o ponto final de API de REST de AMS.
    
    >[!Important]
    >A maioria dos exemplos de código na documentação do Media Services do Azure definido, utilize um tipo (interativo) do utilizador da autenticação para ligar à API do AMS. Este método de autenticação irá funcionar bem para gestão ou de monitorização de aplicações nativas: as aplicações móveis, as aplicações do Windows e aplicações de consola. Este método de autenticação não é adequado para o servidor, serviços web, tipo APIs de aplicações.  Para obter mais informações, consulte [aceder à API do AMS com a autenticação do Azure AD](media-services-use-aad-auth-to-access-ams-api.md).

        <configuration>
        ...
            <appSettings>
              <add key="AADTenantDomain" value="YourAADTenantDomain" />
              <add key="MediaServiceRESTAPIEndpoint" value="YourRESTAPIEndpoint" />
            </appSettings>

        </configuration>

7. Substitua as declarações **using** existentes no início do ficheiro Program.cs pelo seguinte código.
           
        using System;
        using System.Configuration;
        using System.IO;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using System.Threading;
        using System.Collections.Generic;
        using System.Linq;

Neste momento, está pronto para começar a desenvolver uma aplicação dos Media Services.    

## <a name="example"></a>Exemplo

Eis um exemplo de pequeno que se liga à API do AMS e apresenta uma lista de todos os processadores de suporte de dados disponíveis.
    
    class Program
    {
        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["MediaServiceRESTAPIEndpoint"];
    
        private static CloudMediaContext _context = null;
        static void Main(string[] args)
        {
            var tokenCredentials = new AzureAdTokenCredentials(_AADTenantDomain, AzureEnvironments.AzureCloudEnvironment);
            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);
    
            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);
    
            // List all available Media Processors
            foreach (var mp in _context.MediaProcessors)
            {
                Console.WriteLine(mp.Name);
            }
    
        }

##<a name="next-steps"></a>Passos seguintes

Agora [pode ligar à API do AMS](media-services-use-aad-auth-to-access-ams-api.md) e iniciar [desenvolver](media-services-dotnet-get-started.md).


## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

