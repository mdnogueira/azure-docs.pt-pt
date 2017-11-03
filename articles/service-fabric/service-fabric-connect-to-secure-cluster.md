---
title: Ligar de forma segura a um cluster de Service Fabric do Azure | Microsoft Docs
description: "Descreve como autenticar o acesso de cliente para um cluster do Service Fabric e como proteger a comunicação entre clientes e um cluster."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 759a539e-e5e6-4055-bff5-d38804656e10
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/29/2017
ms.author: ryanwi
ms.openlocfilehash: 3f46d743b85b1133f64309f01074cbc3b430183f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="connect-to-a-secure-cluster"></a>Ligar a um cluster seguro

Quando um cliente liga a um nó de cluster do Service Fabric, o cliente pode ser autenticada e segura comunicação estabelecida com o Azure Active Directory (AAD) ou de segurança do certificado. Esta autenticação garante que apenas utilizadores autorizados podem aceder ao cluster e aplicações implementadas e efetuar tarefas de gestão.  Certificado ou de segurança do AAD tem de ter sido anteriormente ativada no cluster quando o cluster foi criado.  Para obter mais informações sobre os cenários de segurança do cluster, consulte [Cluster segurança](service-fabric-cluster-security.md). Se estiver a ligar a um cluster protegido com certificados, [configurar o certificado de cliente](service-fabric-connect-to-secure-cluster.md#connectsecureclustersetupclientcert) no computador que estabelece ligação ao cluster. 

<a id="connectsecureclustercli"></a> 

## <a name="connect-to-a-secure-cluster-using-azure-service-fabric-cli-sfctl"></a>Ligar a um cluster seguro utilizando a CLI do Azure Service Fabric (sfctl)

Existem várias formas para ligar a um cluster seguro utilizando a CLI de recursos de infraestrutura de serviço (sfctl). Quando utilizar um certificado de cliente para autenticação, os detalhes do certificado têm de coincidir com um certificado implementado nos nós do cluster. Se o certificado tem de autoridades de certificação (ACs), terá de além disso, especificar o ACs fidedignas.

Pode ligar a um cluster utilizando o `sfctl cluster select` comando.

Certificados de cliente podem ser especificados em dois fashions diferentes, como um par de certificado e chave, ou como um ficheiro pem único. Palavra-passe protegida `pem` ficheiros, ser-lhe-á solicitado automaticamente para introduzir a palavra-passe.

Para especificar o certificado de cliente como um ficheiro pem, especifique o caminho de ficheiro no `--pem` argumento. Por exemplo:

```azurecli
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem
```

Palavra-passe protegida pem ficheiros solicita a palavra-passe antes de executar qualquer comando.

Para especificar um certificado, chave par utilizar o `--cert` e `--key` argumentos para especificar os caminhos de ficheiro para cada ficheiro correspondentes.

```azurecli
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --cert ./client.crt --key ./keyfile.key
```

Por vezes, certificados utilizados para proteger clusters de teste ou desenvolvimento falharem a validação de certificado. Para ignorar a verificação de certificado, especifique o `--no-verify` opção. Por exemplo:

> [!WARNING]
> Não utilize o `no-verify` opção ao ligar a clusters do Service Fabric de produção.

```azurecli
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem --no-verify
```

Além disso, pode especificar caminhos para os diretórios de certificados de AC fidedignos ou certificados individuais. Para especificar estes caminhos, utilize o `--ca` argumento. Por exemplo:

```azurecli
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem --ca ./trusted_ca
```

Depois de ligar, deverá conseguir [executar outros comandos sfctl](service-fabric-cli.md) para interagir com o cluster.

<a id="connectsecurecluster"></a>

## <a name="connect-to-a-cluster-using-powershell"></a>Ligar a um cluster com o PowerShell
Antes de executar operações num cluster através do PowerShell, primeiro estabelecer uma ligação ao cluster. A ligação de cluster é utilizada para todos os comandos subsequentes na sessão do PowerShell especificado.

### <a name="connect-to-an-unsecure-cluster"></a>Ligar a um cluster não seguro

Para ligar a um cluster não seguro, forneça o endereço de ponto final de cluster para o **Connect-ServiceFabricCluster** comando:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 
```

### <a name="connect-to-a-secure-cluster-using-azure-active-directory"></a>Ligar a um cluster seguro com o Azure Active Directory

Para ligar a um cluster seguro que utiliza o Azure Active Directory para autorizar o acesso de administrador do cluster, forneça o thumbprint do certificado de cluster e utilizar o *AzureActiveDirectory* sinalizador.  

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
-ServerCertThumbprint <Server Certificate Thumbprint> `
-AzureActiveDirectory
```

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Ligar a um cluster seguro utilizando um certificado de cliente
Execute o seguinte comando do PowerShell para ligar a um cluster seguro que utiliza certificados de cliente para autorizar o acesso de administrador. Forneça o thumbprint do certificado de cluster e o thumbprint do certificado de cliente que foram concedido permissões para gestão de clusters. Os detalhes do certificado devem coincidir com um certificado em nós de cluster.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint <Certificate Thumbprint> `
          -FindType FindByThumbprint -FindValue <Certificate Thumbprint> `
          -StoreLocation CurrentUser -StoreName My
```

*ServerCertThumbprint* é o thumbprint do certificado de servidor instalado em nós de cluster. *FindValue* é o thumbprint do certificado de cliente de admin.
Quando os parâmetros são preenchidos, o comando procura semelhante ao seguinte exemplo: 

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint clustername.westus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint A8136758F4AB8962AF2BF3F27921BE1DF67F4326 `
          -FindType FindByThumbprint -FindValue 71DE04467C9ED0544D021098BCD44C71E183414E `
          -StoreLocation CurrentUser -StoreName My
```

### <a name="connect-to-a-secure-cluster-using-windows-active-directory"></a>Ligar a um cluster seguro, através do Active Directory do Windows
Se o cluster autónomo é implementado através de segurança do AD, ligar-se ao cluster, acrescentando o comutador "WindowsCredential".

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
          -WindowsCredential
```

<a id="connectsecureclusterfabricclient"></a>

## <a name="connect-to-a-cluster-using-the-fabricclient-apis"></a>Ligar a um cluster com as APIs de FabricClient
O SDK de Service Fabric fornece o [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) classe para gestão de clusters. Para utilizar as APIs de FabricClient, obter o pacote Microsoft.ServiceFabric NuGet.

### <a name="connect-to-an-unsecure-cluster"></a>Ligar a um cluster não seguro

Para ligar a um cluster protegido remoto, crie uma instância de FabricClient e forneça o endereço de cluster:

```csharp
FabricClient fabricClient = new FabricClient("clustername.westus.cloudapp.azure.com:19000");
```

Para o código que for executada a partir de um cluster, por exemplo, um serviço fiável, criar um FabricClient *sem* especificação do endereço de cluster. FabricClient liga-se o gateway de gestão local no nó que de código está em execução, evitando um salto de rede adicionais.

```csharp
FabricClient fabricClient = new FabricClient();
```

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Ligar a um cluster seguro utilizando um certificado de cliente

Os nós do cluster tem de ter certificados válidos cujo nome comum ou nome DNS SAN aparece no [RemoteCommonNames propriedade](https://docs.microsoft.com/dotnet/api/system.fabric.x509credentials#System_Fabric_X509Credentials_RemoteCommonNames) definido no [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient). Este processo permite a autenticação mútua entre o cliente e os nós do cluster.

```csharp
using System.Fabric;
using System.Security.Cryptography.X509Certificates;

string clientCertThumb = "71DE04467C9ED0544D021098BCD44C71E183414E";
string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string CommonName = "www.clustername.westus.azure.com";
string connection = "clustername.westus.cloudapp.azure.com:19000";

var xc = GetCredentials(clientCertThumb, serverCertThumb, CommonName);
var fc = new FabricClient(xc, connection);

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}

static X509Credentials GetCredentials(string clientCertThumb, string serverCertThumb, string name)
{
    X509Credentials xc = new X509Credentials();
    xc.StoreLocation = StoreLocation.CurrentUser;
    xc.StoreName = "My";
    xc.FindType = X509FindType.FindByThumbprint;
    xc.FindValue = clientCertThumb;
    xc.RemoteCommonNames.Add(name);
    xc.RemoteCertThumbprints.Add(serverCertThumb);
    xc.ProtectionLevel = ProtectionLevel.EncryptAndSign;
    return xc;
}
```

### <a name="connect-to-a-secure-cluster-interactively-using-azure-active-directory"></a>Ligar a um cluster seguro interativamente utilizando o Azure Active Directory

O exemplo seguinte utiliza o Azure Active Directory para o certificado de identidade e o servidor de cliente para a identidade do servidor.

Uma janela de caixa de diálogo aparece automaticamente para o início de sessão interativo ao estabelecer a ligação ao cluster.

```csharp
string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string connection = "clustername.westus.cloudapp.azure.com:19000";

var claimsCredentials = new ClaimsCredentials();
claimsCredentials.ServerThumbprints.Add(serverCertThumb);

var fc = new FabricClient(claimsCredentials, connection);

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}
```

### <a name="connect-to-a-secure-cluster-non-interactively-using-azure-active-directory"></a>Ligar a um cluster seguro de forma não interativa com o Azure Active Directory

O exemplo seguinte depende Microsoft.IdentityModel.Clients.ActiveDirectory, versão: 2.19.208020213.

Para obter mais informações sobre a aquisição de token do AAD, consulte [Microsoft.IdentityModel.Clients.ActiveDirectory](https://msdn.microsoft.com/library/microsoft.identitymodel.clients.activedirectory.aspx).

```csharp
string tenantId = "C15CFCEA-02C1-40DC-8466-FBD0EE0B05D2";
string clientApplicationId = "118473C2-7619-46E3-A8E4-6DA8D5F56E12";
string webApplicationId = "53E6948C-0897-4DA6-B26A-EE2A38A690B4";

string token = GetAccessToken(
    tenantId,
    webApplicationId,
    clientApplicationId,
    "urn:ietf:wg:oauth:2.0:oob");

string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string connection = "clustername.westus.cloudapp.azure.com:19000";

var claimsCredentials = new ClaimsCredentials();
claimsCredentials.ServerThumbprints.Add(serverCertThumb);
claimsCredentials.LocalClaims = token;

var fc = new FabricClient(claimsCredentials, connection);

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}

...

static string GetAccessToken(
    string tenantId,
    string resource,
    string clientId,
    string redirectUri)
{
    string authorityFormat = @"https://login.microsoftonline.com/{0}";
    string authority = string.Format(CultureInfo.InvariantCulture, authorityFormat, tenantId);
    var authContext = new AuthenticationContext(authority);

    var authResult = authContext.AcquireToken(
        resource,
        clientId,
        new UserCredential("TestAdmin@clustenametenant.onmicrosoft.com", "TestPassword"));
    return authResult.AccessToken;
}

```

### <a name="connect-to-a-secure-cluster-without-prior-metadata-knowledge-using-azure-active-directory"></a>Ligar a um cluster de seguro sem o conhecimento anterior de metadados com o Azure Active Directory

O exemplo seguinte utiliza a aquisição de token não interativa, mas a mesma abordagem que pode ser utilizada para criar uma experiência de aquisição de token interativa personalizado. Os metadados do Azure Active Directory necessários para a aquisição do token é lido a partir da configuração de cluster.

```csharp
string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string connection = "clustername.westus.cloudapp.azure.com:19000";

var claimsCredentials = new ClaimsCredentials();
claimsCredentials.ServerThumbprints.Add(serverCertThumb);

var fc = new FabricClient(claimsCredentials, connection);

fc.ClaimsRetrieval += (o, e) =>
{
    return GetAccessToken(e.AzureActiveDirectoryMetadata);
};

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}

...

static string GetAccessToken(AzureActiveDirectoryMetadata aad)
{
    var authContext = new AuthenticationContext(aad.Authority);

    var authResult = authContext.AcquireToken(
        aad.ClusterApplication,
        aad.ClientApplication,
        new UserCredential("TestAdmin@clustenametenant.onmicrosoft.com", "TestPassword"));
    return authResult.AccessToken;
}

```

<a id="connectsecureclustersfx"></a>

## <a name="connect-to-a-secure-cluster-using-service-fabric-explorer"></a>Ligar a um cluster seguro com o Service Fabric Explorer
Para alcançar [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) para um determinado cluster, aponte o browser para:

`http://<your-cluster-endpoint>:19080/Explorer`

O URL completo também está disponível no painel de essentials de cluster do portal do Azure.

Para ligar a um cluster seguro no Windows ou OS X, utilizando um browser, pode importar o certificado de cliente e o browser irá pedir o certificado a utilizar para ligar ao cluster.  Em máquinas Linux, o certificado tem de ser importado utilizando as definições do browser avançado (cada browser tem diferentes mecanismos) e apontar para a localização do certificado tehe no disco.

### <a name="connect-to-a-secure-cluster-using-azure-active-directory"></a>Ligar a um cluster seguro com o Azure Active Directory

Para ligar a um cluster que está protegido com o AAD, aponte o browser para:

`https://<your-cluster-endpoint>:19080/Explorer`

São automaticamente terá de iniciar sessão com o AAD.

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Ligar a um cluster seguro utilizando um certificado de cliente

Para ligar a um cluster que está protegido com certificados, o browser para apontar:

`https://<your-cluster-endpoint>:19080/Explorer`

São automaticamente terá de selecionar um certificado de cliente.

<a id="connectsecureclustersetupclientcert"></a>
## <a name="set-up-a-client-certificate-on-the-remote-computer"></a>Configurar um certificado de cliente no computador remoto
Devem ser utilizados, pelo menos, dois certificados para proteger o cluster, uma para o certificado de cluster e o servidor e outra para acesso de cliente.  Recomendamos que utilize também certificados secundários adicionais e certificados de acesso de cliente.  Para proteger a comunicação entre um cliente e um nó de cluster através de segurança do certificado, terá primeiro de obter e instalar o certificado de cliente. O certificado pode ser instalado para o (os meus) arquivo pessoal do computador local ou o utilizador atual.  Também precisa do thumbprint do certificado de servidor para que o cliente pode autenticar o cluster.

Execute o seguinte cmdlet do PowerShell para configurar o certificado de cliente no computador a partir da qual pode aceder ao cluster.

```powershell
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
        -Password (ConvertTo-SecureString -String test -AsPlainText -Force)
```

Se se tratar de um certificado autoassinado, terá de importá-lo ao arquivo de "pessoas fidedignas" do seu computador antes de poder utilizar este certificado para ligar a um cluster seguro.

```powershell
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople `
-FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
-Password (ConvertTo-SecureString -String test -AsPlainText -Force)
```

## <a name="next-steps"></a>Passos seguintes

* [Processo de atualização de Cluster do Service Fabric e as expectativas do utilizador](service-fabric-cluster-upgrade.md)
* [Gerir as aplicações de Service Fabric no Visual Studio](service-fabric-manage-application-in-visual-studio.md)
* [Introdução de modelo de estado de funcionamento de recursos de infraestrutura de serviço](service-fabric-health-introduction.md)
* [Segurança da aplicação e RunAs](service-fabric-application-runas-security.md)
* [Introdução à CLI do Service Fabric](service-fabric-cli.md)
