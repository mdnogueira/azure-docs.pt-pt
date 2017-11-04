---
title: "Utilizar o Cofre de chaves do Azure de uma aplicação Web | Microsoft Docs"
description: "Utilize este tutorial para o ajudar a saber como utilizar o Cofre de chaves do Azure a partir de uma aplicação web."
services: key-vault
author: adhurwit
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 9b7d065e-1979-4397-8298-eeba3aec4792
ms.service: key-vault
ms.workload: identity
ms.topic: article
ms.date: 09/15/2017
ms.author: adhurwit
ms.openlocfilehash: 1846305e6834145046cf9903714c68e9a6fd4f7d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-key-vault-from-a-web-application"></a>Utilizar o Cofre de chaves do Azure de uma aplicação Web

## <a name="introduction"></a>Introdução

Utilize este tutorial para o ajudar a saber como utilizar o Cofre de chaves do Azure a partir de uma aplicação web no Azure. -Explica o processo de aceder a um segredo a partir de um cofre de chaves do Azure para que possa ser utilizado na sua aplicação web.

**Tempo estimado para concluir:** 15 minutos

Para obter informações gerais sobre o Cofre de Chaves do Azure, consulte o artigo [O que é o Cofre de Chaves do Azure?](key-vault-whatis.md)

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, deve ter o seguinte:

* Um URI para um segredo num cofre de chaves do Azure
* Um ID de cliente e um segredo de cliente para uma aplicação web registado no Azure Active Directory que tem acesso ao seu Cofre de chaves
* Uma aplicação web. Iremos irá ser mostrar os passos para uma aplicação ASP.NET MVC implementado no Azure como uma aplicação Web.

>[!IMPORTANT]
>* Este exemplo depende de uma forma mais antiga do aprovisionamento manualmente as identidades do AAD. Atualmente, não há uma nova funcionalidade de pré-visualização chamado [identidade de serviço geridas (MSI)](https://docs.microsoft.com/azure/active-directory/msi-overview), que pode aprovisionar automaticamente identidades do AAD. Consulte o exemplo seguinte no [github](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet/) para obter mais detalhes.

> [!NOTE]
>* É essencial que concluiu os passos apresentados na [introdução ao Cofre de chaves do Azure](key-vault-get-started.md) para este tutorial, de modo a que tem o URI para um segredo e o ID de cliente e o segredo do cliente para uma aplicação web.


A aplicação web que irão aceder ao Cofre de chaves é aquele que está registado no Azure Active Directory e tiver sido dado acesso ao seu Cofre de chaves. Se não for este o caso, volte atrás para registar uma aplicação no tutorial de introdução e repita os passos apresentados.

Este tutorial foi concebido para programadores de web que compreender as noções básicas de criação de aplicações web no Azure. Para obter mais informações sobre as aplicações Web do Azure, consulte [descrição geral das aplicações Web](../app-service/app-service-web-overview.md).

## <a id="packages"></a>Adicionar pacotes Nuget

Existem dois pacotes que a aplicação web tem de ter instalado.

* Biblioteca de autenticação do Active Directory - contém métodos para interagir com o Azure Active Directory e gerir a identidade do utilizador
* Biblioteca de Cofre de chaves do Azure - contém métodos para interagir com o Cofre de chaves do Azure

Ambos estes pacotes podem ser instalados através da consola do Gestor de pacote utilizando o comando do Install-Package.

```
// this is currently the latest stable version of ADAL
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.16.204221202
Install-Package Microsoft.Azure.KeyVault
```

## <a id="webconfig"></a>Modificar Web. config

Existem três definições de aplicação que precisem de ser adicionado ao ficheiro Web. config da seguinte forma.

```
    <!-- ClientId and ClientSecret refer to the web application registration with Azure Active Directory -->
    <add key="ClientId" value="clientid" />
    <add key="ClientSecret" value="clientsecret" />

    <!-- SecretUri is the URI for the secret in Azure Key Vault -->
    <add key="SecretUri" value="secreturi" />
```

Se não pretender alojar a aplicação como uma aplicação Web do Azure, deve adicionar os valores reais de ClientId, o segredo do cliente e o segredo do URI para o Web. config. Caso contrário, deixe estes valores fictícias porque iremos adicionar os valores reais no portal do Azure para um nível adicional de segurança.

## <a id="gettoken"></a>Adicionar método ao obter o Token de acesso

Para utilizar a API do Cofre de chaves tem um token de acesso. O cliente do Cofre de chave processa chamadas à API do Cofre de chave, mas terá de fornecer com uma função que obtém o token de acesso.  

Segue-se o código para obter acesso token do Azure Active Directory. Este código pode aceder em qualquer lugar na sua aplicação. Incluindo a adicionar uma classe Utils ou EncryptionHelper.  

```cs
//add these using statements
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Threading.Tasks;
using System.Web.Configuration;

//this is an optional property to hold the secret after it is retrieved
public static string EncryptSecret { get; set; }

//the method that will be provided to the KeyVaultClient
public static async Task<string> GetToken(string authority, string resource, string scope)
{
    var authContext = new AuthenticationContext(authority);
    ClientCredential clientCred = new ClientCredential(WebConfigurationManager.AppSettings["ClientId"],
                WebConfigurationManager.AppSettings["ClientSecret"]);
    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

    if (result == null)
        throw new InvalidOperationException("Failed to obtain the JWT token");

    return result.AccessToken;
}
```

> [!NOTE]
>* Atualmente, a funcionalidade nova Managed Service Identity (MSI) é a forma mais fácil de se autenticar. Para obter mais detalhes, veja a ligação seguinte para o exemplo ao utilizar [Key Vault with MSI in an application in .NET](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet/) (Key Vault com MSI numa aplicação em .NET) e o [tutorial MSI with App Service and Functions tutorial](https://docs.microsoft.com/en-us/azure/app-service/app-service-managed-service-identity) (MSI com o Serviço de Aplicações e as Funções). 
>* A utilização de ID de cliente e o segredo de cliente é outra forma de autenticar uma aplicação do Azure AD. E utilizá-lo na sua aplicação web permite uma separação de deveres e mais controlo sobre a gestão de chaves. Mas dependem de colocar o segredo do cliente nas suas definições de configuração, que, para algumas, podem ser como arriscadas como colocar o segredo que pretende proteger nas suas definições de configuração. Consulte o seguinte procedimento para ver um debate sobre como utilizar um ID de cliente e o certificado em vez de ID de cliente e o segredo de cliente para autenticar a aplicação do Azure AD.

## <a id="appstart"></a>Obter o segredo em início da aplicação

Agora precisamos de código para chamar a API do Cofre de chaves e obter o segredo. O seguinte código pode ser colocado em qualquer lugar, desde que é denominado antes que seja necessário utilizá-lo. Ter coloque este código no evento do pré-carregamento da aplicação no global. asax para que é executada uma vez no início e disponibiliza o segredo da aplicação.

```cs
//add these using statements
using Microsoft.Azure.KeyVault;
using System.Web.Configuration;

// I put my GetToken method in a Utils class. Change for wherever you placed your method.
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));
var sec = await kv.GetSecretAsync(WebConfigurationManager.AppSettings["SecretUri"]);

//I put a variable in a Utils class to hold the secret for general  application use.
Utils.EncryptSecret = sec.Value;
```

## <a id="portalsettings"></a>Adicionar as definições de aplicação no portal do Azure (opcional)

Se tiver uma aplicação Web do Azure, agora pode adicionar os valores reais para AppSettings no portal do Azure. Ao fazê-lo, os valores reais não estarão disponíveis na Web. config, mas protegidos através do Portal em que tenha as capacidades de controlo de acesso separado. Estes valores serão substituídos pelos valores que introduziu no seu Web. config. Certifique-se de que os nomes são os mesmos.

![Definições da aplicação apresentadas no portal do Azure][1]

## <a name="authenticate-with-a-certificate-instead-of-a-client-secret"></a>Autenticar com um certificado em vez de um segredo do cliente

Outra forma de autenticar uma aplicação do Azure AD é através de um ID de cliente e um certificado em vez de um ID de cliente e o segredo do cliente. Seguem-se os passos para utilizar um certificado numa aplicação Web do Azure:

1. Obter ou criar um certificado
2. Associar o certificado com uma aplicação do Azure AD
3. Adicione código à sua aplicação Web para utilizar o certificado
4. Adicionar um certificado para a sua aplicação Web

### <a name="get-or-create-a-certificate"></a>Obter ou criar um certificado

Para efeitos de nosso, iremos faz com que um certificado de teste. Eis alguns comandos que pode utilizar na linha de comandos do programador para criar um certificado. Altere o diretório para onde pretende que os ficheiros de certificado criados.  Além disso, para o início e fim de data do certificado, utilize a data atual mais a 1 ano.

```
makecert -sv mykey.pvk -n "cn=KVWebApp" KVWebApp.cer -b 03/07/2017 -e 03/07/2018 -r
pvk2pfx -pvk mykey.pvk -spc KVWebApp.cer -pfx KVWebApp.pfx -po test123
```

Tome nota da data de fim e a palavra-passe de. pfx (neste exemplo: 31/07/2016 e test123). Vai precisar deles abaixo.

Para obter mais informações sobre como criar um certificado de teste, consulte [como: criar seu próprio teste certificado](https://msdn.microsoft.com/library/ff699202.aspx)

### <a name="associate-the-certificate-with-an-azure-ad-application"></a>Associar o certificado com uma aplicação do Azure AD

Agora que tem um certificado, tem de associar a uma aplicação do Azure AD. Atualmente, o portal do Azure não suporta este fluxo de trabalho; Isto pode ser efetuado através do PowerShell. Execute os seguintes comandos para assoicate o certificado com a aplicação do Azure AD:

```ps
$x509 = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
$x509.Import("C:\data\KVWebApp.cer")
$credValue = [System.Convert]::ToBase64String($x509.GetRawCertData())


$adapp = New-AzureRmADApplication -DisplayName "KVWebApp" -HomePage "http://kvwebapp" -IdentifierUris "http://kvwebapp" -CertValue $credValue -StartDate $x509.NotBefore -EndDate $x509.NotAfter


$sp = New-AzureRmADServicePrincipal -ApplicationId $adapp.ApplicationId


Set-AzureRmKeyVaultAccessPolicy -VaultName 'contosokv' -ServicePrincipalName "http://kvwebapp" -PermissionsToSecrets all -ResourceGroupName 'contosorg'

# get the thumbprint to use in your app settings
$x509.Thumbprint
```

Depois de executar estes comandos, pode ver a aplicação no Azure AD. Quando pesquisar, certifique-se de que selecionar "Aplicações minha empresa detém" em vez de "Aplicações minha empresa utiliza" na caixa de diálogo de pesquisa.

Para obter mais informações sobre objetos de aplicação do Azure AD e objetos de ServicePrincipal, consulte [objectos da aplicação e objetos de principais de serviço](../active-directory/active-directory-application-objects.md).

### <a name="add-code-to-your-web-app-to-use-the-certificate"></a>Adicione código à sua aplicação Web para utilizar o certificado

Iremos agora adicionar código para a sua aplicação Web para aceder a certificação e utilizá-lo para autenticação.

Não há primeiro código para o certificado de acesso.

```cs
public static class CertificateHelper
{
    public static X509Certificate2 FindCertificateByThumbprint(string findValue)
    {
        X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
        try
        {
            store.Open(OpenFlags.ReadOnly);
            X509Certificate2Collection col = store.Certificates.Find(X509FindType.FindByThumbprint,
                findValue, false); // Don't validate certs, since the test root isn't installed.
            if (col == null || col.Count == 0)
                return null;
            return col[0];
        }
        finally
        {
            store.Close();
        }
    }
}
```

Tenha em atenção que o StoreLocation CurrentUser em vez de LocalMachine. E que iremos são fornecendo 'false' para o método de localizar porque está a utilizar um certificado de teste.

Em seguida, é código que utiliza o CertificateHelper e cria um ClientAssertionCertificate, necessária para a autenticação.

```cs
public static ClientAssertionCertificate AssertionCert { get; set; }

public static void GetCert()
{
    var clientAssertionCertPfx = CertificateHelper.FindCertificateByThumbprint(WebConfigurationManager.AppSettings["thumbprint"]);
    AssertionCert = new ClientAssertionCertificate(WebConfigurationManager.AppSettings["clientid"], clientAssertionCertPfx);
}
```

Eis o novo código para obter o token de acesso. Esta ação substitui o método GetToken no exemplo anterior. Posso atribuiu-um nome diferente para sua comodidade.

```cs
public static async Task<string> GetAccessToken(string authority, string resource, string scope)
{
    var context = new AuthenticationContext(authority, TokenCache.DefaultShared);
    var result = await context.AcquireTokenAsync(resource, AssertionCert);
    return result.AccessToken;
}
```

Ter colocar todos os este código na classe de Utils meu projeto de aplicação Web para facilidade de utilização.

É a última alteração de código no método Application_Start. Primeiro é preciso chamar o método GetCert() ao carregar o ClientAssertionCertificate. E, em seguida, vamos alterar o método de chamada de retorno que podemos fornecer ao criar um novo KeyVaultClient. Tenha em atenção que esta ação substitui o código que tivemos no exemplo anterior.

```cs
Utils.GetCert();
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetAccessToken));
```

### <a name="add-a-certificate-to-your-web-app-through-the-azure-portal"></a>Adicionar um certificado para a sua aplicação Web através do portal do Azure

A adição de um certificado para a sua aplicação Web é um processo de dois passos simples. Em primeiro lugar, aceda ao portal do Azure e navegue até à sua aplicação Web. No painel de definições da sua aplicação Web, clique na entrada para "domínios personalizados e SSL". No painel que abre que conseguirá carregar o certificado que criou no exemplo anterior, KVWebApp.pfx, certifique-se de que, lembre-se a palavra-passe pfx.

![Adicionar um certificado para uma aplicação Web no portal do Azure][2]

A última coisa que precisa de fazer consiste em Adicionar uma definição de aplicação para a sua aplicação Web que tem o nome de site\_carga\_certificados e um valor de *. Isto irá garantir que todos os certificados estão carregados. Se quer carregar apenas os certificados que carregou, em seguida, pode introduzir uma lista separada por vírgulas de thumbprints do respetivos.

Para obter mais informações sobre como adicionar um certificado para uma aplicação Web, consulte [utilizando certificados em aplicações de Web sites do Azure](https://azure.microsoft.com/blog/2014/10/27/using-certificates-in-azure-websites-applications/)

### <a name="add-a-certificate-to-key-vault-as-a-secret"></a>Adicionar um certificado ao Cofre de chaves como um segredo

Em vez de diretamente a carregar o certificado para o serviço de aplicação Web, pode armazenar no Cofre de chaves como um segredo e implementá-la a partir daí. Este é um processo de dois passos que é descrito na mensagem de blogue seguinte, [implementar Azure certificado da aplicação Web através do Cofre de chaves](https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/)

## <a id="next"></a>Passos seguintes

Para as referências de programação, consulte [Azure chave de cofre c# cliente referência da API](https://msdn.microsoft.com/library/azure/dn903628.aspx).

<!--Image references-->
[1]: ./media/key-vault-use-from-web-application/PortalAppSettings.png
[2]: ./media/key-vault-use-from-web-application/PortalAddCertificate.png
