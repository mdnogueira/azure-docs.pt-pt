---
title: "Implementação de aplicação de Service Fabric do Azure | Microsoft Docs"
description: "Utilize as APIs de FabricClient para implementar e remover aplicações no Service Fabric."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: b120ffbf-f1e3-4b26-a492-347c29f8f66b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/05/2017
ms.author: ryanwi
ms.openlocfilehash: 480f574640d4a9ccd4da97a98adc8b284d373855
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-and-remove-applications-using-fabricclient"></a>Implementar e remover aplicações utilizando FabricClient
> [!div class="op_single_selector"]
> * [PowerShell](service-fabric-deploy-remove-applications.md)
> * [Visual Studio](service-fabric-publish-app-remote-cluster.md)
> * [APIs FabricClient](service-fabric-deploy-remove-applications-fabricclient.md)
> 
> 

<br/>

Uma vez um [tipo de aplicação foram empacotado][10], está pronto para a implementação para um cluster do Service Fabric do Azure. Implementação envolve os seguintes três passos:

1. Carregar o pacote de aplicação para o arquivo de imagens
2. Registar o tipo de aplicação
3. Remova o pacote de aplicações do arquivo de imagens
4. Criar a instância da aplicação

Depois de uma aplicação é implementada e está em execução uma instância no cluster, pode eliminar a instância da aplicação e o respetivo tipo de aplicação. Para remover completamente uma aplicação do cluster envolve os seguintes passos:

1. Remover (ou eliminar) a execução instância da aplicação
2. O registo do tipo de aplicação já não é necessário

Se utilizar [Visual Studio para implementar e depurar aplicações](service-fabric-publish-app-remote-cluster.md) no seu cluster de desenvolvimento local, todos os passos anteriores são processados automaticamente através de um script do PowerShell.  Este script está localizado no *Scripts* pasta do projeto de aplicação. Este artigo fornece em segundo plano que esse script está a fazer para que possam executar as mesmas operações fora do Visual Studio. 
 
## <a name="connect-to-the-cluster"></a>Ligar ao cluster
Ligar ao cluster através da criação de um [FabricClient](/dotnet/api/system.fabric.fabricclient) instância antes de executar qualquer um dos exemplos de código neste artigo. Para obter exemplos de ligar a um cluster de desenvolvimento local ou um cluster remoto ou o cluster protegido com o Azure Active Directory, X509 certificados, ou consulte Windows Active Directory [ligar a um cluster seguro](service-fabric-connect-to-secure-cluster.md#connect-to-a-cluster-using-the-fabricclient-apis). Para ligar ao cluster de desenvolvimento local, execute o seguinte:

```csharp
// Connect to the local cluster.
FabricClient fabricClient = new FabricClient();
```

## <a name="upload-the-application-package"></a>Carregar o pacote de aplicação
Suponha que criar e uma aplicação com o nome do pacote *MinhaAplicação* no Visual Studio. Por predefinição, o nome do tipo de aplicação listado no ApplicationManifest.xml é "MyApplicationType".  O pacote de aplicações, que contém o manifesto da aplicação necessários, os manifestos de serviço e os pacotes de código/configuração/dados, está localizado na *C:\Users\&lt; nome de utilizador&gt;\Documents\Visual Studio 2017\Projects\MyApplication\MyApplication\pkg\Debug*.

Carregar o pacote de aplicações, coloca-o numa localização que seja acessível pelos componentes do Service Fabric internos. Recursos de infraestrutura de serviço verifica o pacote de aplicação durante o registo do pacote de aplicação. No entanto, se quiser verificar localmente o pacote de aplicação (ou seja, antes de carregar), utilize o [teste ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps) cmdlet.

O [CopyApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) API carrega o pacote de aplicação para o arquivo de imagens do cluster. 

Se o pacote de aplicação é grande e/ou tem vários ficheiros, pode [comprimi-los](service-fabric-package-apps.md#compress-a-package) e copie-a para o arquivo de imagens com o PowerShell. A compressão reduz o tamanho e o número de ficheiros.

Consulte [compreender a cadeia de ligação do arquivo de imagens](service-fabric-image-store-connection-string.md) para informações suplementares sobre o arquivo de imagens e imagem armazenar a cadeia de ligação.

## <a name="register-the-application-package"></a>Registar o pacote de aplicações
O tipo de aplicação e a versão declarados no manifesto da aplicação ficam disponível para utilização quando o pacote de aplicações está registado. O sistema lê o pacote carregado no passo anterior, verifica o pacote, processa o conteúdo do pacote e copia o pacote processado para uma localização de sistema interno.  

O [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) regista de API a aplicação escreva no cluster e disponibilizá-lo para a implementação.

O [GetApplicationTypeListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getapplicationtypelistasync) API fornece informações sobre todos os tipos de aplicação registada com êxito. Pode utilizar esta API para determinar quando o registo foi efetuado.

## <a name="remove-an-application-package-from-the-image-store"></a>Remover um pacote de aplicação de arquivo de imagens
Recomenda-se que remova o pacote de aplicações após a aplicação for registada com êxito.  A eliminação de pacotes de aplicação da loja de imagem liberta recursos do sistema.  Manter os pacotes de aplicações não utilizadas consome armazenamento em disco e leva a problemas de desempenho da aplicação. Eliminar o pacote de aplicação do imagem loja com o [RemoveApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.removeapplicationpackage) API.

## <a name="create-an-application-instance"></a>Criar uma instância de aplicação
Pode instanciar uma aplicação a partir de qualquer tipo de aplicação que foi registado com êxito utilizando a [CreateApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.createapplicationasync) API. O nome de cada aplicação tem de começar com o *"fabric:"* esquema e tem de ser exclusivo para cada instância da aplicação (dentro de um cluster). Também são criados quaisquer serviços predefinido definidos no manifesto da aplicação do tipo de aplicação de destino.

Várias instâncias de aplicações podem ser criadas para qualquer versão especificada de um tipo de aplicação registada. Cada instância da aplicação é executado em isolamento, com o seu próprio diretório de trabalho e o conjunto de processos.

Para ver qual denominada aplicações e serviços estão em execução no cluster, execute o [GetApplicationListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getapplicationlistasync) e [GetServiceListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getservicelistasync) APIs.

## <a name="create-a-service-instance"></a>Criar uma instância de serviço
Pode instanciar um serviço a partir de um tipo de serviço através de [CreateServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) API.  Se o serviço está declarado como um serviço predefinido no manifesto da aplicação, o serviço é instanciado quando a aplicação é instanciada.  Chamar o [CreateServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) API para um serviço que já está a ser instanciado irá devolver uma exceção do tipo FabricException contendo um código de erro com um valor de FabricErrorCode.ServiceAlreadyExists.

## <a name="remove-a-service-instance"></a>Remover uma instância de serviço
Quando já não é necessária uma instância de serviço, pode removê-lo a execução instância da aplicação ao chamar o [DeleteServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.deleteserviceasync) API.  

> [!WARNING]
> Esta operação não é possível ser invertida e não é possível recuperar o estado do serviço.

## <a name="remove-an-application-instance"></a>Remover uma instância de aplicação
Quando já não é necessária uma instância de aplicação, pode removê-lo permanentemente com o nome de [DeleteApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.deleteapplicationasync) API. [DeleteApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.deleteapplicationasync) automaticamente remove todos os serviços que pertencem à aplicação bem, permanentemente remover todos os Estados de serviço.

> [!WARNING]
> Esta operação não é possível ser invertida e não é possível recuperar o estado da aplicação.

## <a name="unregister-an-application-type"></a>Anular o registo de um tipo de aplicação
Quando já não é necessária uma versão específica de um tipo de aplicação, deverá anular o registo essa versão específica do tipo de aplicação utilizando o [Unregister-ServiceFabricApplicationType](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.unprovisionapplicationasync) API. A anulação do registo não utilizadas versões dos tipos de aplicação liberta espaço de armazenamento utilizado pelo arquivo de imagens. Uma versão de um tipo de aplicação pode ser anulada desde que não existem aplicações são instanciadas em relação a que a versão do tipo de aplicação e não existem atualizações de aplicação pendente estão a referenciar essa versão do tipo de aplicação.

## <a name="troubleshooting"></a>Resolução de problemas
### <a name="copy-servicefabricapplicationpackage-asks-for-an-imagestoreconnectionstring"></a>Cópia ServiceFabricApplicationPackage pede-lhe um ImageStoreConnectionString
O ambiente do SDK de Service Fabric já deve ter as predefinições corretas configurado. Mas, se necessário, ImageStoreConnectionString para todos os comandos deve corresponder ao valor que o cluster do Service Fabric está a utilizar. Pode encontrar o ImageStoreConnectionString no manifesto do cluster, obtido utilizando o [Get-ServiceFabricClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest?view=azureservicefabricps) e Get-ImageStoreConnectionStringFromClusterManifest comandos:

```powershell
PS C:\> Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)
```

O **Get-ImageStoreConnectionStringFromClusterManifest** cmdlet, o qual faz parte do módulo do PowerShell de SDK do Service Fabric, é utilizado para obter a cadeia de ligação do arquivo de imagem.  Para importar o módulo do SDK, execute:

```powershell
Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
```


O ImageStoreConnectionString encontra-se no manifesto do cluster:

```xml
<ClusterManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Name="Server-Default-SingleNode" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

    [...]

    <Section Name="Management">
      <Parameter Name="ImageStoreConnectionString" Value="file:D:\ServiceFabric\Data\ImageStore" />
    </Section>

    [...]
```

Consulte [compreender a cadeia de ligação do arquivo de imagens](service-fabric-image-store-connection-string.md) para informações suplementares sobre o arquivo de imagens e imagem armazenar a cadeia de ligação.

### <a name="deploy-large-application-package"></a>Implementar o pacote de aplicações grandes
Problema: [CopyApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) API exceder o tempo limite para um pacote de aplicações grandes (ordem de GB).
Tente:
- Especifique um tempo limite para [CopyApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) método, com `timeout` parâmetro. Por predefinição, o tempo limite é de 30 minutos.
- Verifique a ligação de rede entre a máquina de origem e o cluster. Se a ligação estiver lenta, considere a utilização de uma máquina com uma ligação de rede melhor.
Se o computador cliente for noutra região que o cluster, considere utilizar uma máquina cliente numa região quanto mais próximo ou mesma que o cluster.
- Verifique se são atingir a limitação externo. Por exemplo, quando o arquivo de imagens está configurado para utilizar o armazenamento do azure, carregamento poderá ser limitado.

Problema: Pacote de carregamento foi concluída com êxito, mas [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) API exceder o tempo limite. Tente:
- [Comprimir o pacote](service-fabric-package-apps.md#compress-a-package) antes de copiar para o arquivo de imagens.
A compressão reduz o tamanho e o número de ficheiros, que por sua vez reduz a quantidade de tráfego e funciona esse Service Fabric tem de efetuar. A operação de carregamento poderá ser mais lenta (especialmente se incluir o tempo de compressão), mas o registo e anular o registo do tipo de aplicação são mais rápidas.
- Especifique um tempo limite para [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) API com `timeout` parâmetro.

### <a name="deploy-application-package-with-many-files"></a>Implementar o pacote de aplicações com muitos ficheiros
Problema: [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) exceder o tempo limite para um pacote de aplicação com muitos ficheiros (ordem de milhares).
Tente:
- [Comprimir o pacote](service-fabric-package-apps.md#compress-a-package) antes de copiar para o arquivo de imagens. A compressão reduz o número de ficheiros.
- Especifique um tempo limite para [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) com `timeout` parâmetro.

## <a name="code-example"></a>Exemplo de código
O exemplo seguinte copia um pacote de aplicação para o arquivo de imagens, Aprovisiona o tipo de aplicação, cria uma instância de aplicação, cria uma instância de serviço, remove a instância da aplicação, anular Aprovisiona o tipo de aplicação e elimina o pacote de aplicações da loja de imagem.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Reflection;
using System.Threading.Tasks;

using System.Fabric;
using System.Fabric.Description;
using System.Threading;

namespace ServiceFabricAppLifecycle
{
class Program
{
static void Main(string[] args)
{

    string clusterConnection = "localhost:19000";
    string appName = "fabric:/MyApplication";
    string appType = "MyApplicationType";
    string appVersion = "1.0.0";
    string serviceName = "fabric:/MyApplication/Stateless1";
    string imageStoreConnectionString = "file:C:\\SfDevCluster\\Data\\ImageStoreShare";
    string packagePathInImageStore = "MyApplication";
    string packagePath = "C:\\Users\\username\\Documents\\Visual Studio 2017\\Projects\\MyApplication\\MyApplication\\pkg\\Debug";
    string serviceType = "Stateless1Type";

    // Connect to the cluster.
    FabricClient fabricClient = new FabricClient(clusterConnection);

    // Copy the application package to a location in the image store
    try
    {
        fabricClient.ApplicationManager.CopyApplicationPackage(imageStoreConnectionString, packagePath, packagePathInImageStore);
        Console.WriteLine("Application package copied to {0}", packagePathInImageStore);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("Application package copy to Image Store failed: ");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Provision the application.  "MyApplicationV1" is the folder in the image store where the application package is located. 
    // The application type with name "MyApplicationType" and version "1.0.0" (both are found in the application manifest) 
    // is now registered in the cluster.            
    try
    {
        fabricClient.ApplicationManager.ProvisionApplicationAsync(packagePathInImageStore).Wait();

        Console.WriteLine("Provisioned application type {0}", packagePathInImageStore);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("Provision Application Type failed:");

        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Delete the application package from a location in the image store.
    try
    {
        fabricClient.ApplicationManager.RemoveApplicationPackage(imageStoreConnectionString, packagePathInImageStore);
        Console.WriteLine("Application package removed from {0}", packagePathInImageStore);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("Application package removal from Image Store failed: ");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    //  Create the application instance.
    try
    {
        ApplicationDescription appDesc = new ApplicationDescription(new Uri(appName), appType, appVersion);
        fabricClient.ApplicationManager.CreateApplicationAsync(appDesc).Wait();
        Console.WriteLine("Created application instance of type {0}, version {1}", appType, appVersion);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("CreateApplication failed.");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Create the stateless service description.  For stateful services, use a StatefulServiceDescription object.
    StatelessServiceDescription serviceDescription = new StatelessServiceDescription();
    serviceDescription.ApplicationName = new Uri(appName);
    serviceDescription.InstanceCount = 1;
    serviceDescription.PartitionSchemeDescription = new SingletonPartitionSchemeDescription();
    serviceDescription.ServiceName = new Uri(serviceName);
    serviceDescription.ServiceTypeName = serviceType;

    // Create the service instance.  If the service is declared as a default service in the ApplicationManifest.xml,
    // the service instance is already running and this call will fail.
    try
    {
        fabricClient.ServiceManager.CreateServiceAsync(serviceDescription).Wait();
        Console.WriteLine("Created service instance {0}", serviceName);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("CreateService failed.");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Delete a service instance.
    try
    {
        DeleteServiceDescription deleteServiceDescription = new DeleteServiceDescription(new Uri(serviceName));

        fabricClient.ServiceManager.DeleteServiceAsync(deleteServiceDescription);
        Console.WriteLine("Deleted service instance {0}", serviceName);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("DeleteService failed.");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Delete an application instance from the application type.
    try
    {
        DeleteApplicationDescription deleteApplicationDescription = new DeleteApplicationDescription(new Uri(appName));

        fabricClient.ApplicationManager.DeleteApplicationAsync(deleteApplicationDescription).Wait();
        Console.WriteLine("Deleted application instance {0}", appName);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("DeleteApplication failed.");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Un-provision the application type.
    try
    {
        fabricClient.ApplicationManager.UnprovisionApplicationAsync(appType, appVersion).Wait();
        Console.WriteLine("Un-provisioned application type {0}, version {1}", appType, appVersion);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("Un-provision application type failed: ");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    Console.WriteLine("Hit enter...");
    Console.Read();
}        
}
}

```

## <a name="next-steps"></a>Passos seguintes
[Atualização da aplicação de Service Fabric](service-fabric-application-upgrade.md)

[Introdução de estado de funcionamento do Service Fabric](service-fabric-health-introduction.md)

[Diagnosticar e resolver problemas de um serviço do Service Fabric](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Modelar uma aplicação no Service Fabric](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-application-model.md
[11]: service-fabric-application-upgrade.md
