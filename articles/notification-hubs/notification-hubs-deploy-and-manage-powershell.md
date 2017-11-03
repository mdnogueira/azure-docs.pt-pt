---
title: "Implementar e Gerir Hubs de Notificação com o PowerShell"
description: "Como criar e gerir os Hubs de notificação através do PowerShell para automatização"
services: notification-hubs
documentationcenter: 
author: ysxu
manager: erikre
editor: 
ms.assetid: 7c58f2c8-0399-42bc-9e1e-a7f073426451
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: powershell
ms.devlang: na
ms.topic: article
ms.date: 06/29/2016
ms.author: yuaxu
ms.openlocfilehash: 4db058e4bd91dc287b14e887abc6c378c65c4a2b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-and-manage-notification-hubs-using-powershell"></a>Implementar e Gerir Hubs de Notificação com o PowerShell
## <a name="overview"></a>Descrição geral
Este artigo mostra como utilizar a criar e gerir Notification Hubs do Azure com o PowerShell. As seguintes tarefas de automatização comuns são apresentadas neste tópico.

* Criar um Hub de notificação
* Definir credenciais

Se também tem de criar um novo service bus espaço de nomes para os hubs de notificação, consulte o artigo [gerir o Service Bus com o PowerShell](../service-bus-messaging/service-bus-powershell-how-to-provision.md).

Hubs de notificações de gestão não é suportado diretamente pelos cmdlets incluídos com o Azure PowerShell. A abordagem das melhores a partir do PowerShell está a referenciar a assemblagem de Microsoft.Azure.NotificationHubs.dll. A assemblagem é distribuída com o [pacote NuGet do Microsoft Azure Notification Hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este artigo, tem de ter o seguinte:

* Uma subscrição do Azure. Azure é uma plataforma baseado na subscrição. Para obter mais informações sobre a obtenção de uma subscrição, consulte [opções de compra], [ofertas de membros], ou [avaliação gratuita].
* Um computador com o Azure PowerShell. Para obter instruções, consulte [instalar e configurar o Azure PowerShell].
* Uma compreensão geral de scripts do PowerShell, os pacotes de NuGet e o .NET Framework.

## <a name="including-a-reference-to-the-net-assembly-for-service-bus"></a>Inclusão de uma referência à assemblagem .NET do Service Bus
Gestão de Notification Hubs do Azure ainda não está incluído com os cmdlets do PowerShell do Azure PowerShell. Para aprovisionar os notification hubs, pode utilizar o cliente do .NET fornecido a [pacote NuGet do Microsoft Azure Notification Hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

Em primeiro lugar, certifique-se o script pode localizar o **Microsoft.Azure.NotificationHubs.dll** assemblagem, o que é instalada como um pacote NuGet num projeto Visual Studio. Para poder estar flexível, o script efetua estes passos:

1. Determina o caminho no qual foi invocado.
2. Atravessa o caminho até encontrar uma pasta denominada `packages`. Esta pasta é criada quando instala os pacotes de NuGet para projetos do Visual Studio.
3. Pesquisas recursivamente o `packages` pasta para uma assemblagem com o nome **Microsoft.Azure.NotificationHubs.dll**.
4. Referencia a assemblagem para que os tipos estão disponíveis para utilização posterior.

Eis como estes passos são implementados num script do PowerShell:

``` powershell

try
{
    # WARNING: Make sure to reference the latest version of Microsoft.Azure.NotificationHubs.dll
    Write-Output "Adding the [Microsoft.Azure.NotificationHubs.dll] assembly to the script..."
    $scriptPath = Split-Path (Get-Variable MyInvocation -Scope 0).Value.MyCommand.Path
    $packagesFolder = (Split-Path $scriptPath -Parent) + "\packages"
    $assembly = Get-ChildItem $packagesFolder -Include "Microsoft.Azure.NotificationHubs.dll" -Recurse
    Add-Type -Path $assembly.FullName

    Write-Output "The [Microsoft.Azure.NotificationHubs.dll] assembly has been successfully added to the script."
}

catch [System.Exception]
{
    Write-Error("Could not add the Microsoft.Azure.NotificationHubs.dll assembly to the script. Make sure you build the solution before running the provisioning script.")
}
```

## <a name="create-the-namespacemanager-class"></a>Criar a classe de NamespaceManager
Para aprovisionar os Notification Hubs, crie uma instância do [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.azure.notificationhubs.namespacemanager.aspx) classe do SDK. 

Pode utilizar o [Get-AzureSBAuthorizationRule] cmdlet incluído com o Azure PowerShell para obter uma regra de autorização que é utilizada para fornecer uma cadeia de ligação. Iremos guardar uma referência para o `NamespaceManager` instância no `$NamespaceManager` variável. Utilizaremos `$NamespaceManager` para aprovisionar um notification hub.

``` powershell
$sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
# Create the NamespaceManager object to create the hub
Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
$NamespaceManager=[Microsoft.Azure.NotificationHubs.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."
```


## <a name="provisioning-a-new-notification-hub"></a>Aprovisionamento de um novo Notification Hub
Para aprovisionar um novo notification hub, utilize o [API .NET para os Notification Hubs].

Nesta parte do script configurou quatro variáveis locais. 

1. `$Namespace`: Defina esta opção para o nome do espaço de nomes onde pretende criar um hub de notificação.
2. `$Path`: Defina este caminho para o nome do novo hub de notificação.  Por exemplo, "MyHub".    
3. `$WnsPackageSid`: Defina esta opção para o SID do pacote para a aplicação do Windows do [Windows Dev Center](http://go.microsoft.com/fwlink/p/?linkid=266582&clcid=0x409).
4. `$WnsSecretkey`: Defina esta opção para a chave secreta para a aplicação do Windows do [Windows Dev Center](http://go.microsoft.com/fwlink/p/?linkid=266582&clcid=0x409).

Estas variáveis são utilizadas para ligar ao seu espaço de nomes e criar um novo Hub de notificação configurado para processar as notificações de serviços de notificação do Windows (WNS) com as credenciais do WNS para uma aplicação do Windows. Para obter informações sobre como obter o pacote SID e consulte chave secreta, o [introdução aos Notification Hubs](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) tutorial. 

* O fragmento de script utiliza o `NamespaceManager` objeto para verificar se o Hub de notificação identificado por `$Path` existe.
* Se não existir, o script irá criar um `NotificationHubDescription` com WNS credenciais e passar que para o `NamespaceManager` classe `CreateNotificationHub` método.

``` powershell

$Namespace = "<Enter your namespace>"
$Path  = "<Enter a name for your notification hub>"
$WnsPackageSid = "<your package sid>"
$WnsSecretkey = "<enter your secret key>"

$WnsCredential = New-Object -TypeName Microsoft.Azure.NotificationHubs.WnsCredential -ArgumentList $WnsPackageSid,$WnsSecretkey

# Query the namespace
$CurrentNamespace = Get-AzureSBNamespace -Name $Namespace

# Check if the namespace already exists
if ($CurrentNamespace)
{
    Write-Output "The namespace [$Namespace] in the [$($CurrentNamespace.Region)] region was found."

    # Create the NamespaceManager object used to create a new notification hub
    $sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
    Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
    $NamespaceManager = [Microsoft.Azure.NotificationHubs.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
    Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."

    # Check to see if the Notification Hub already exists
    if ($NamespaceManager.NotificationHubExists($Path))
    {
        Write-Output "The [$Path] notification hub already exists in the [$Namespace] namespace."  
    }
    else
    {
        Write-Output "Creating the [$Path] notification hub in the [$Namespace] namespace."
        $NHDescription = New-Object -TypeName Microsoft.Azure.NotificationHubs.NotificationHubDescription -ArgumentList $Path;
        $NHDescription.WnsCredential = $WnsCredential;
        $NamespaceManager.CreateNotificationHub($NHDescription);
        Write-Output "The [$Path] notification hub was created in the [$Namespace] namespace."
    }
}
else
{
    Write-Host "The [$Namespace] namespace does not exist."
}
```




## <a name="additional-resources"></a>Recursos Adicionais
* [Gerir o barramento de serviço com o PowerShell](../service-bus-messaging/service-bus-powershell-how-to-provision.md)
* [Como criar filas do Service Bus, tópicos e subscrições através de um script do PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
* [Como criar um espaço de nomes de barramento de serviço e um Hub de eventos utilizando um script do PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)

Alguns scripts pronta a utilizar também estão disponíveis para transferência:

* [Scripts do PowerShell de Service Bus](https://code.msdn.microsoft.com/windowsazure/Service-Bus-PowerShell-a46b7059)

[opções de compra]: http://azure.microsoft.com/pricing/purchase-options/
[ofertas de membros]: http://azure.microsoft.com/pricing/member-offers/
[avaliação gratuita]: http://azure.microsoft.com/pricing/free-trial/
[instalar e configurar o Azure PowerShell]: /powershell/azureps-cmdlets-docs.
[API .NET para os Notification Hubs]: https://msdn.microsoft.com/library/azure/mt414893.aspx
[Get-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495122.aspx
[New-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495165.aspx
[Get-AzureSBAuthorizationRule]: https://msdn.microsoft.com/library/azure/dn495113.aspx

