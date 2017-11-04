---
title: "Autenticação do serviço de serviço: SDK do .NET Data Lake Store utilizando o Azure Active Directory | Microsoft Docs"
description: "Aprenda a alcançar a autenticação do serviço de serviço com o Data Lake Store utilizando o Azure Active Directory utilizando o .NET SDK"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/11/2017
ms.author: nitinme
ms.openlocfilehash: c336cda6f3af4e2a4647371458b2db3e97917105
ms.sourcegitcommit: d03907a25fb7f22bec6a33c9c91b877897e96197
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/12/2017
---
# <a name="service-to-service-authentication-with-data-lake-store-using-net-sdk"></a>Autenticação do serviço de serviço com o Data Lake Store com .NET SDK
> [!div class="op_single_selector"]
> * [Utilizar o Java](data-lake-store-service-to-service-authenticate-java.md)
> * [Com o .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Utilizar o Python](data-lake-store-service-to-service-authenticate-python.md)
> * [Utilizar a API REST](data-lake-store-service-to-service-authenticate-rest-api.md)
> 
>  

Neste artigo, pode saber mais sobre como utilizar o SDK .NET para fazer a autenticação de serviços do Azure Data Lake Store. Para a autenticação de utilizador final com o Data Lake Store com .NET SDK, consulte [autenticação de utilizador final com o Data Lake Store com .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md).


## <a name="prerequisites"></a>Pré-requisitos
* **Visual Studio 2013, 2015 ou 2017**. As instruções abaixo utilizam o Visual Studio 2017.

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Criar uma aplicação de "Web" do Azure Active Directory**. Tem de ter concluído os passos em [autenticação do serviço de serviço com o Data Lake Store utilizando o Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md).

## <a name="create-a-net-application"></a>Criar uma aplicação .NET
1. Abra o Visual Studio e crie uma aplicação de consola.
2. No menu **Ficheiro**, clique em **Novo** e, em seguida, clique em **Projeto**.
3. A partir de **Novo Projeto**, escreva ou selecione os seguintes valores:

   | Propriedade | Valor |
   | --- | --- |
   | Categoria |Templates/Visual C#/Windows |
   | Modelo |Aplicação de Consola |
   | Nome |CreateADLApplication |
4. Clique em **OK** para criar o projeto.

5. Adicione os pacotes NuGet ao seu projeto.

   1. Clique com o botão direito do rato no nome do projeto no Explorador de Soluções e clique em **Gerir Pacotes NuGet**.
   2. No separador **Gestor de Pacotes NuGet**, certifique-se de que a **Origem do pacote** está definida como **nuget.org** e que a caixa de verificação **Incluir pré-lançamento** está selecionada.
   3. Procure e instale os seguintes pacotes NuGet:

      * `Microsoft.Azure.Management.DataLake.Store` - Este tutorial utiliza a v2.1.3-preview.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` - Este tutorial utiliza a v2.2.12.

        ![Adicionar uma origem NuGet](./media/data-lake-store-get-started-net-sdk/data-lake-store-install-nuget-package.png "Criar uma nova conta do Azure Data Lake")
   4. Feche o **Gestor de Pacotes NuGet**.

6. Abra **Program.cs**, elimine o código existente e, em seguida, inclua as seguintes instruções para adicionar referências aos espaços de nomes.

        using System;
        using System.IO;
        using System.Linq;
        using System.Text;
        using System.Threading;
        using System.Collections.Generic;
        using System.Security.Cryptography.X509Certificates; // Required only if you are using an Azure AD application created with certificates
                
        using Microsoft.Rest;
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure.Management.DataLake.Store;
        using Microsoft.Azure.Management.DataLake.Store.Models;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;

## <a name="service-to-service-authentication-with-client-secret"></a>Autenticação do serviço de serviço com o segredo do cliente
Adicione este fragmento na sua aplicação de cliente do .NET. Substitua os valores de marcador de posição pelos valores obtidos a partir de uma aplicação de web do Azure AD (listada como pré-requisito).  Este fragmento permite-lhe autenticar a sua aplicação **forma não interativa** com o Data Lake Store utilizando a chave/segredo de cliente para o Azure AD de aplicação web. 

    private static void Main(string[] args)
    {    
        // Service principal / appplication authentication with client secret / key
        // Use the client ID of an existing AAD "Web App" application.
        string TENANT = "<AAD-directory-domain>";
        string CLIENTID = "<AAD_WEB_APP_CLIENT_ID>";
        System.Uri ARM_TOKEN_AUDIENCE = new System.Uri(@"https://management.core.windows.net/");
        System.Uri ADL_TOKEN_AUDIENCE = new System.Uri(@"https://datalake.azure.net/");
        string secret_key = "<AAD_WEB_APP_SECRET_KEY>";
        var armCreds = GetCreds_SPI_SecretKey(TENANT, ARM_TOKEN_AUDIENCE, CLIENTID, secret_key);
        var adlCreds = GetCreds_SPI_SecretKey(TENANT, ADL_TOKEN_AUDIENCE, CLIENTID, secret_key);
    }

O fragmento de que precede utiliza uma função de programa auxiliar `GetCreds_SPI_SecretKey`. O código para esta função auxiliar está disponível [aqui no Github](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#getcreds_spi_secretkey).

## <a name="service-to-service-authentication-with-certificate"></a>Autenticação de serviços com certificado

Adicione este fragmento na sua aplicação de cliente do .NET. Substitua os valores de marcador de posição pelos valores obtidos a partir de uma aplicação de web do Azure AD (listada como pré-requisito). Este fragmento permite-lhe autenticar a sua aplicação **forma não interativa** com o Data Lake Store utilizando o certificado para um Azure AD de aplicação web. Para obter instruções sobre como criar uma aplicação do Azure AD, consulte [criar serviço principal com certificados](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-self-signed-certificate).

    
    private static void Main(string[] args)
    {
        // Service principal / application authentication with certificate
        // Use the client ID and certificate of an existing AAD "Web App" application.
        string TENANT = "<AAD-directory-domain>";
        string CLIENTID = "<AAD_WEB_APP_CLIENT_ID>";
        System.Uri ARM_TOKEN_AUDIENCE = new System.Uri(@"https://management.core.windows.net/");
        System.Uri ADL_TOKEN_AUDIENCE = new System.Uri(@"https://datalake.azure.net/");
        var cert = new X509Certificate2(@"d:\cert.pfx", "<certpassword>");
        var armCreds = GetCreds_SPI_Cert(TENANT, ARM_TOKEN_AUDIENCE, CLIENTID, cert);
        var adlCreds = GetCreds_SPI_Cert(TENANT, ADL_TOKEN_AUDIENCE, CLIENTID, cert);
    }

O fragmento de que precede utiliza uma função de programa auxiliar `GetCreds_SPI_Cert`. O código para esta função auxiliar está disponível [aqui no Github](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#getcreds_spi_cert).

## <a name="next-steps"></a>Passos seguintes
Neste artigo, aprendeu a utilizar a autenticação de serviço a serviço para autenticar com o Azure Data Lake Store com .NET SDK. Agora pode ver os seguintes artigos falar sobre como utilizar o SDK .NET para trabalhar com o Azure Data Lake Store.

* [Operações de gestão de contas no Data Lake Store com o SDK .NET](data-lake-store-get-started-net-sdk.md)
* [Operações de dados no Data Lake Store com .NET SDK](data-lake-store-data-operations-net-sdk.md)


