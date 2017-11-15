---
title: "Implementação de aplicação de Service Fabric do Azure | Microsoft Docs"
description: "Como implementar e remover aplicações no Service Fabric com o PowerShell."
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
ms.openlocfilehash: 5a1279ba9626ece30491c8fc899054873f6359e2
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2017
---
# <a name="deploy-and-remove-applications-using-powershell"></a>Implementar e remover aplicações utilizando o PowerShell
> [!div class="op_single_selector"]
> * [Resource Manager](service-fabric-application-arm-resource.md)
> * [PowerShell](service-fabric-deploy-remove-applications.md)
> * [CLI do Service Fabric](service-fabric-application-lifecycle-sfctl.md)
> * [APIs FabricClient](service-fabric-deploy-remove-applications-fabricclient.md)

<br/>

Uma vez um [tipo de aplicação foram empacotado][10], está pronto para a implementação para um cluster do Service Fabric do Azure. Implementação envolve os seguintes três passos:

1. Carregar o pacote de aplicação para o arquivo de imagens
2. Registar o tipo de aplicação
3. Criar a instância da aplicação

Depois de uma aplicação é implementada e está em execução uma instância no cluster, pode eliminar a instância da aplicação e o respetivo tipo de aplicação. Para remover completamente uma aplicação do cluster envolve os seguintes passos:

1. Remover (ou eliminar) a execução instância da aplicação
2. O registo do tipo de aplicação já não é necessário
3. Remova o pacote de aplicações do arquivo de imagens

Se utilizar o Visual Studio para implementar e depurar aplicações no seu cluster de desenvolvimento local, todos os passos anteriores são processados automaticamente através de um script do PowerShell.  Este script está localizado no *Scripts* pasta do projeto de aplicação. Este artigo fornece em segundo plano que esse script está a fazer para que possam executar as mesmas operações fora do Visual Studio. 
 
## <a name="connect-to-the-cluster"></a>Ligar ao cluster
Antes de executar os comandos do PowerShell neste artigo, inicie sempre utilizando [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) para ligar ao cluster do Service Fabric. Para ligar ao cluster de desenvolvimento local, execute o seguinte:

```powershell
PS C:\>Connect-ServiceFabricCluster
```

Para obter exemplos de ligar a um cluster remoto ou o cluster protegido com o Azure Active Directory, X509 certificados, ou consulte Windows Active Directory [ligar a um cluster seguro](service-fabric-connect-to-secure-cluster.md).

## <a name="upload-the-application-package"></a>Carregar o pacote de aplicação
Carregar o pacote de aplicações, coloca-o numa localização que esteja acessível aos componentes internos do Service Fabric.
Se pretende verificar o pacote de aplicação localmente, utilize o [teste ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps) cmdlet.

O [cópia ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) comando carrega o pacote de aplicação para o arquivo de imagens do cluster.

Suponha que criar e uma aplicação com o nome do pacote *MinhaAplicação* no Visual Studio 2015. Por predefinição, o nome do tipo de aplicação listado no ApplicationManifest.xml é "MyApplicationType".  O pacote de aplicações, que contém o manifesto da aplicação necessários, os manifestos de serviço e os pacotes de código/configuração/dados, está localizado na *C:\Users\<username\>\Documents\Visual Studio 2015\Projects\MyApplication\MyApplication\pkg\Debug*. 

O comando seguinte lista os conteúdos do pacote de aplicação:

```powershell
PS C:\> $path = 'C:\Users\<user\>\Documents\Visual Studio 2015\Projects\MyApplication\MyApplication\pkg\Debug'
PS C:\> tree /f $path
Folder PATH listing for volume OSDisk
Volume serial number is 0459-2393
C:\USERS\USER\DOCUMENTS\VISUAL STUDIO 2015\PROJECTS\MYAPPLICATION\MYAPPLICATION\PKG\DEBUG
│   ApplicationManifest.xml
│
└───Stateless1Pkg
    │   ServiceManifest.xml
    │
    ├───Code
    │       Microsoft.ServiceFabric.Data.dll
    │       Microsoft.ServiceFabric.Data.Interfaces.dll
    │       Microsoft.ServiceFabric.Internal.dll
    │       Microsoft.ServiceFabric.Internal.Strings.dll
    │       Microsoft.ServiceFabric.Services.dll
    │       ServiceFabricServiceModel.dll
    │       Stateless1.exe
    │       Stateless1.exe.config
    │       Stateless1.pdb
    │       System.Fabric.dll
    │       System.Fabric.Strings.dll
    │
    └───Config
            Settings.xml
```

Se o pacote de aplicação é grande e/ou tem vários ficheiros, pode [comprimi-los](service-fabric-package-apps.md#compress-a-package). A compressão reduz o tamanho e o número de ficheiros.
O efeito é que ao registar e anular o registo do tipo de aplicação são mais rápidas. Tempo de carregamento pode ser mais lento atualmente, especialmente se incluir o tempo para comprimir o pacote. 

Para comprimir um pacote, utilize o mesmo [cópia ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) comando. A compressão pode ser efetuada separado do carregamento, utilizando o `SkipCopy` sinalizador, ou em conjunto com a operação de carregamento. Aplicar compressão num pacote comprimido é sem operações.
Para descomprimir um pacote comprimido, utilize o mesmo [cópia ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) comando com o `UncompressPackage` mudar.

O seguinte cmdlet comprime o pacote sem copiar para o arquivo de imagens. O pacote inclui agora zipped ficheiros para o `Code` e `Config` pacotes. A aplicação e os manifestos de serviço não são zipados, porque são necessários para muitas operações internas (como a partilha, application nome e versão extração do tipo para determinados validações do pacote). Zipping manifestos iria tornar estas operações ineficaz.

```
PS C:\> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -CompressPackage -SkipCopy
PS C:\> tree /f $path
Folder PATH listing for volume OSDisk
Volume serial number is 0459-2393
C:\USERS\USER\DOCUMENTS\VISUAL STUDIO 2015\PROJECTS\MYAPPLICATION\MYAPPLICATION\PKG\DEBUG
|   ApplicationManifest.xml
|
└───Stateless1Pkg
       Code.zip
       Config.zip
       ServiceManifest.xml
```

Para pacotes de aplicações de grande, a compressão demora tempo. Para obter os melhores resultados, utilize uma unidade SSD rápida. Os tempos de compressão e o tamanho do pacote comprimido também diferem consoante o conteúdo do pacote.
Por exemplo, eis estatísticas de compressão de alguns pacotes que mostram o inicial e o tamanho do pacote comprimida, com a hora de compressão.

|Tamanho inicial (MB)|Contagem de ficheiros|Tempo de compressão|Tamanho do pacote comprimida (MB)|
|----------------:|---------:|---------------:|---------------------------:|
|100|100|00:00:03.3547592|60|
|512|100|00:00:16.3850303|307|
|1024|500|00:00:32.5907950|615|
|2048|1000|00:01:04.3775554|1231|
|5012|100|00:02:45.2951288|3074|

Depois de um pacote está comprimido, este pode ser também carregada para um ou vários clusters de Service Fabric conforme necessário. O mecanismo de implementação é a mesmo para pacotes comprimidos e descomprimidos. Se o pacote está comprimido, é armazenada como tal no arquivo de imagem do cluster e descomprimido no nó antes da aplicação é executada.


O exemplo seguinte carrega o pacote para o arquivo de imagens, para uma pasta denominada "MyApplicationV1":

```powershell
PS C:\> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -ApplicationPackagePathInImageStore MyApplicationV1 -TimeoutSec 1800
```

Se não especificar o *- ApplicationPackagePathInImageStore* parâmetro, o pacote de aplicação é copiado para a pasta "Depuração" no arquivo de imagem.

>[!NOTE]
>**Cópia ServiceFabricApplicationPackage** irá detetar automaticamente a cadeia de ligação do arquivo de imagem apropriada se a sessão do PowerShell está ligada a um cluster do Service Fabric. Para versões de Service Fabric anteriores 5.6, o **- ImageStoreConnectionString** argumento tem de ser explicitamente fornecido.
>
>```powershell
>PS C:\> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -ApplicationPackagePathInImageStore MyApplicationV1 -ImageStoreConnectionString (Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)) -TimeoutSec 1800
>```
>
>O **Get-ImageStoreConnectionStringFromClusterManifest** cmdlet, o qual faz parte do módulo do PowerShell de SDK do Service Fabric, é utilizado para obter a cadeia de ligação do arquivo de imagem.  Para importar o módulo do SDK, execute:
>
>```powershell
>Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
>```
>
>Consulte [compreender a cadeia de ligação do arquivo de imagens](service-fabric-image-store-connection-string.md) para informações suplementares sobre o arquivo de imagens e imagem armazenar a cadeia de ligação.
>
>
>

O tempo que demora a carregar um pacote difere consoante vários fatores. Alguns destes fatores são o número de ficheiros no pacote, o tamanho do pacote e os tamanhos de ficheiro. A velocidade da rede entre a máquina de origem e o cluster do Service Fabric também tem impacto sobre o tempo de carregamento. O tempo limite predefinido para [cópia ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) é 30 minutos.
Dependendo de fatores descritos, poderá ter de aumentar o limite de tempo. Se estiver a compressão o pacote na chamada de cópia, terá de considerar a hora de compressão.



## <a name="register-the-application-package"></a>Registar o pacote de aplicações
O tipo de aplicação e a versão declarados no manifesto da aplicação ficam disponível para utilização quando o pacote de aplicações está registado. O sistema lê o pacote carregado no passo anterior, verifica o pacote, processa o conteúdo do pacote e copia o pacote processado para uma localização de sistema interno.  

Execute o [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) cmdlet para registar o tipo de aplicação no cluster e disponibilizá-lo para a implementação:

```powershell
PS C:\> Register-ServiceFabricApplicationType MyApplicationV1
Register application type succeeded
```

"MyApplicationV1" é a pasta onde o pacote de aplicações está localizado o arquivo de imagens. O tipo de aplicação com o nome "MyApplicationType" e a versão "1.0.0" (ambos encontram-se no manifesto da aplicação) está agora registado no cluster.

O [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) comando devolve apenas depois do sistema registou com êxito o pacote de aplicações. Quanto registo demora depende do tamanho e conteúdos do pacote de aplicação. Se for necessário, o **- TimeoutSec** parâmetro pode ser utilizado para fornecer um tempo limite maior (o tempo limite predefinido é de 60 segundos).

Se tiver uma aplicação grande pacote ou se ocorrerem tempos limite, utilize o **- Async** parâmetro. O comando devolve quando o cluster aceita o comando de registo e o processamento continua conforme necessário.
O [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) comando apresenta uma lista de todas as versões de tipo de aplicação registada com êxito e o respetivo estado de registo. Pode utilizar este comando para determinar quando o registo foi efetuado.

```powershell
PS C:\> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

## <a name="remove-an-application-package-from-the-image-store"></a>Remover um pacote de aplicação de arquivo de imagens
Recomenda-se que remova o pacote de aplicações após a aplicação for registada com êxito.  A eliminação de pacotes de aplicação da loja de imagem liberta recursos do sistema.  Manter os pacotes de aplicações não utilizadas consome armazenamento em disco e leva a problemas de desempenho da aplicação.

```powershell
PS C:\>Remove-ServiceFabricApplicationPackage -ApplicationPackagePathInImageStore MyApplicationV1
```

## <a name="create-the-application"></a>Criar a aplicação
Pode instanciar uma aplicação a partir de qualquer versão do tipo de aplicação que foi registado com êxito utilizando a [New-ServiceFabricApplication](/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps) cmdlet. O nome de cada aplicação tem de começar com o *"fabric:"* esquema e tem de ser exclusivo para cada instância da aplicação. Também são criados quaisquer serviços predefinido definidos no manifesto da aplicação do tipo de aplicação de destino.

```powershell
PS C:\> New-ServiceFabricApplication fabric:/MyApp MyApplicationType 1.0.0

ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
ApplicationParameters  : {}
```
Várias instâncias de aplicações podem ser criadas para qualquer versão especificada de um tipo de aplicação registada. Cada instância da aplicação é executado em isolamento, com o seu próprio processo e o diretório de trabalho.

Para ver qual denominada aplicações e serviços estão em execução no cluster, execute o [Get-ServiceFabricApplication](/powershell/servicefabric/vlatest/get-servicefabricapplication) e [Get-ServiceFabricService](/powershell/module/servicefabric/get-servicefabricservice?view=azureservicefabricps) cmdlets:

```powershell
PS C:\> Get-ServiceFabricApplication  

ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : {}

PS C:\> Get-ServiceFabricApplication | Get-ServiceFabricService

ServiceName            : fabric:/MyApp/Stateless1
ServiceKind            : Stateless
ServiceTypeName        : Stateless1Type
IsServiceGroup         : False
ServiceManifestVersion : 1.0.0
ServiceStatus          : Active
HealthState            : Ok
```

## <a name="remove-an-application"></a>Remover uma aplicação
Quando já não é necessária uma instância de aplicação, pode removê-lo permanentemente com o nome de [remover ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication?view=azureservicefabricps) cmdlet. [Remover ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication?view=azureservicefabricps) automaticamente remove todos os serviços que pertencem à aplicação bem, permanentemente remover todos os Estados de serviço. 

> [!WARNING]
> Esta operação não é possível ser invertida e não é possível recuperar o estado da aplicação.

```powershell
PS C:\> Remove-ServiceFabricApplication fabric:/MyApp

Confirm
Continue with this operation?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
Remove application instance succeeded

PS C:\> Get-ServiceFabricApplication
```

## <a name="unregister-an-application-type"></a>Anular o registo de um tipo de aplicação
Quando já não é necessária uma versão específica de um tipo de aplicação, deve anular o registo do tipo de aplicação utilizando o [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) cmdlet. A anular registo aplicações não utilizadas tipos versões espaço de armazenamento utilizado pelo arquivo de imagens removendo os binários da aplicação. A anulação do registo um tipo de aplicação não remove o pacote de aplicações. Um tipo de aplicação pode ser anulado desde que não existem aplicações são instanciadas nele e não pendentes aplicação atualizações estão a referenciá-lo.

Executar [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) para ver os tipos de aplicação atualmente registados no cluster:

```powershell
PS C:\> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

Executar [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) ao anular o registo de um tipo de aplicação específica:

```powershell
PS C:\> Unregister-ServiceFabricApplicationType MyApplicationType 1.0.0
```

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
Problema: [cópia ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) exceder o tempo limite para um pacote de aplicações grandes (ordem de GB).
Tente:
- Especifique um tempo limite para [cópia ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) comando com `TimeoutSec` parâmetro. Por predefinição, o tempo limite é de 30 minutos.
- Verifique a ligação de rede entre a máquina de origem e o cluster. Se a ligação estiver lenta, considere a utilização de uma máquina com uma ligação de rede melhor.
Se o computador cliente for noutra região que o cluster, considere utilizar uma máquina cliente numa região quanto mais próximo ou mesma que o cluster.
- Verifique se são atingir a limitação externo. Por exemplo, quando o arquivo de imagens está configurado para utilizar o armazenamento do azure, carregamento poderá ser limitado.

Problema: Pacote de carregamento foi concluída com êxito, mas [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) exceder o tempo limite. Tente:
- [Comprimir o pacote](service-fabric-package-apps.md#compress-a-package) antes de copiar para o arquivo de imagens.
A compressão reduz o tamanho e o número de ficheiros, que por sua vez reduz a quantidade de tráfego e funciona esse Service Fabric tem de efetuar. A operação de carregamento poderá ser mais lenta (especialmente se incluir o tempo de compressão), mas o registo e anular o registo do tipo de aplicação são mais rápidas.
- Especifique um tempo limite para [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) com `TimeoutSec` parâmetro.
- Especifique `Async` mudar para [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps). O comando devolve quando o cluster aceita o comando e o registo do tipo de aplicação continua no modo assíncrono. Por este motivo, não é necessário especificar um tempo limite superior neste caso. O [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) comando apresenta uma lista de todas as versões de tipo de aplicação registada com êxito e o respetivo estado de registo. Pode utilizar este comando para determinar quando o registo foi efetuado.

```powershell
PS C:\> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

### <a name="deploy-application-package-with-many-files"></a>Implementar o pacote de aplicações com muitos ficheiros
Problema: [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) exceder o tempo limite para um pacote de aplicação com muitos ficheiros (ordem de milhares).
Tente:
- [Comprimir o pacote](service-fabric-package-apps.md#compress-a-package) antes de copiar para o arquivo de imagens. A compressão reduz o número de ficheiros.
- Especifique um tempo limite para [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) com `TimeoutSec` parâmetro.
- Especifique `Async` mudar para [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps). O comando devolve quando o cluster aceita o comando e o registo do tipo de aplicação continua no modo assíncrono.
Por este motivo, não é necessário especificar um tempo limite superior neste caso. O [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) comando apresenta uma lista de todas as versões de tipo de aplicação registada com êxito e o respetivo estado de registo. Pode utilizar este comando para determinar quando o registo foi efetuado.

```powershell
PS C:\> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

## <a name="next-steps"></a>Passos seguintes
[Atualização da aplicação de Service Fabric](service-fabric-application-upgrade.md)

[Introdução de estado de funcionamento do Service Fabric](service-fabric-health-introduction.md)

[Diagnosticar e resolver problemas de um serviço do Service Fabric](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Modelar uma aplicação no Service Fabric](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-application-model.md
[11]: service-fabric-application-upgrade.md
