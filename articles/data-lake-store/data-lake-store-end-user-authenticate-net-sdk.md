---
title: "Autenticação de utilizador final: SDK do .NET Data Lake Store utilizando o Azure Active Directory | Microsoft Docs"
description: "Aprenda a alcançar a autenticação de utilizador final com o Data Lake Store utilizando o Azure Active Directory com o .NET SDK"
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
ms.openlocfilehash: 43cb2225d7fbf2fc7f8d93c78415f0f2c1a3fa25
ms.sourcegitcommit: d03907a25fb7f22bec6a33c9c91b877897e96197
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/12/2017
---
# <a name="end-user-authentication-with-data-lake-store-using-net-sdk"></a>Autenticação de utilizador final com o Data Lake Store com .NET SDK
> [!div class="op_single_selector"]
> * [Utilizar o Java](data-lake-store-end-user-authenticate-java-sdk.md)
> * [Com o .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Utilizar o Python](data-lake-store-end-user-authenticate-python.md)
> * [Utilizar a API REST](data-lake-store-end-user-authenticate-rest-api.md)
> 
>  

Neste artigo, pode saber mais sobre como utilizar o SDK .NET para fazer a autenticação de utilizador final com o Azure Data Lake Store. Para a autenticação de serviço de serviço com o Data Lake Store com .NET SDK, consulte [autenticação do serviço de serviço com o Data Lake Store com .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md).

## <a name="prerequisites"></a>Pré-requisitos
* **Visual Studio 2013, 2015 ou 2017**. As instruções abaixo utilizam o Visual Studio 2017.

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Criar uma aplicação "Nativas" do Azure Active Directory**. Tem de ter concluído os passos em [autenticação de utilizador final com o Data Lake Store utilizando o Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

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
                
        using Microsoft.Rest;
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure.Management.DataLake.Store;
        using Microsoft.Azure.Management.DataLake.Store.Models;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        

## <a name="end-user-authentication"></a>Autenticação de utilizador final
Adicione este fragmento na sua aplicação de cliente do .NET. Substitua os valores de marcador de posição pelos valores obtidos a partir de uma aplicação nativa do Azure AD (listada como pré-requisito). Este fragmento permite-lhe autenticar a sua aplicação **interativamente** com o Data Lake Store, que significa que lhe for pedido para introduzir as suas credenciais do Azure.

Facilidade de utilização, o fragmento seguinte utiliza os valores predefinidos para o ID de cliente e URI que são válidas para qualquer subscrição do Azure de redirecionamento. O fragmento seguinte, apenas terá de fornecer o valor para o ID do inquilino. Pode obter o ID de inquilino utilizando as instruções fornecidas na [obter o ID de inquilino](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-tenant-id).
    
    private static void Main(string[] args)
    {
        //User login via interactive popup
        string TENANT = "<AAD-directory-domain>";
        string CLIENTID = "1950a258-227b-4e31-a9cf-717495945fc2";
        System.Uri ARM_TOKEN_AUDIENCE = new System.Uri(@"https://management.core.windows.net/");
        System.Uri ADL_TOKEN_AUDIENCE = new System.Uri(@"https://datalake.azure.net/");
        string MY_DOCUMENTS = System.Environment.GetFolderPath(System.Environment.SpecialFolder.MyDocuments);
        string TOKEN_CACHE_PATH = System.IO.Path.Combine(MY_DOCUMENTS, "my.tokencache");
        var tokenCache = GetTokenCache(TOKEN_CACHE_PATH);
        var armCreds = GetCreds_User_Popup(TENANT, ARM_TOKEN_AUDIENCE, CLIENTID, tokenCache);
        var adlCreds = GetCreds_User_Popup(TENANT, ADL_TOKEN_AUDIENCE, CLIENTID, tokenCache);
    }

Alguns aspetos a conhecer o fragmento anterior:

* O fragmento de que precede utiliza funções de programa auxiliar `GetTokenCache` e `GetCreds_User_Popup`. O código para estas funções de programa auxiliar está disponível [aqui no Github](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#gettokencache).
* Para ajudar a agilizar a conclusão do tutorial, o fragmento utiliza um ID de cliente da aplicação nativa que está disponível por predefinição para todas as subscrições do Azure. Por isso, pode **utilizar este fragmento tal como está na sua aplicação**.
* No entanto, se pretender utilizar o seu próprio domínio do Azure AD e o ID de cliente de aplicação, tem de criar uma aplicação nativa do Azure AD e, depois, utilizar o ID de inquilino do Azure AD, o ID de cliente e o URI de redirecionamento da aplicação que criou. Veja [Criar uma Aplicação do Active Directory para autenticação de utilizador final com o Data Lake Store](data-lake-store-end-user-authenticate-using-active-directory.md) para obter instruções.

  
## <a name="next-steps"></a>Passos seguintes
Neste artigo, aprendeu a utilizar a autenticação de utilizador final para autenticar com o Azure Data Lake Store com .NET SDK. Agora pode ver os seguintes artigos falar sobre como utilizar o SDK .NET para trabalhar com o Azure Data Lake Store.

* [Operações de gestão de contas no Data Lake Store com o SDK .NET](data-lake-store-get-started-net-sdk.md)
* [Operações de dados no Data Lake Store com .NET SDK](data-lake-store-data-operations-net-sdk.md)

