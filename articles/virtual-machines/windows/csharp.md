---
title: "Criar e gerir uma Máquina Virtual do Azure com c# | Microsoft Docs"
description: "Utilize c# e do Azure Resource Manager para implementar uma máquina virtual e todos os respetivos recursos de suporte."
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 87524373-5f52-4f4b-94af-50bf7b65c277
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: davidmu
ms.openlocfilehash: 5d9021c2f65b70e36d5ea82992c9fb9d2d6d394a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-manage-windows-vms-in-azure-using-c"></a>Criar e gerir VMs do Windows no Azure com c# #

Um [Máquina Virtual do Azure](overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (VM) tem vários recursos do Azure de suporte. Este artigo abrange a criar, gerir e eliminar recursos VM através de c#. Saiba como:

> [!div class="checklist"]
> * Criar um projeto do Visual Studio
> * Instalar o pacote
> * Criar as credenciais
> * Criar recursos
> * Executar tarefas de gestão
> * Eliminar recursos
> * Executar a aplicação

Demora cerca de 20 minutos para executar estes passos.

## <a name="create-a-visual-studio-project"></a>Criar um projeto do Visual Studio

1. Se ainda não o fez, instale [Visual Studio](https://docs.microsoft.com/visualstudio/install/install-visual-studio). Selecione **desenvolvimento de ambiente de trabalho .NET** na página de cargas de trabalho e, em seguida, clique em **instalar**. Em resumo, pode ver que **ferramentas de desenvolvimento do .NET Framework 4 4.6** é selecionado automaticamente para si. Se já tiver instalado o Visual Studio, pode adicionar a carga de trabalho do .NET utilizando o Iniciador do Studio Visual.
2. No Visual Studio, clique em **ficheiro** > **novo** > **projeto**.
3. No **modelos** > **Visual c#**, selecione **aplicação de consola (.NET Framework)**, introduza *myDotnetProject* para o nome do projeto, selecione a localização do projeto e, em seguida, clique em **OK**.

## <a name="install-the-package"></a>Instalar o pacote

Os pacotes de NuGet são a forma mais fácil de instalar as bibliotecas que precisa para concluir estes passos. Para obter as bibliotecas que necessita no Visual Studio, execute estes passos:

1. Clique em **ferramentas** > **Gestor de pacotes Nuget**e, em seguida, clique em **consola do Gestor de pacotes**.
2. Escreva este comando na consola:

    ```
    Install-Package Microsoft.Azure.Management.Fluent
    ```

## <a name="create-credentials"></a>Criar as credenciais

Antes de começar este passo, certifique-se de que tem acesso a um [principal de serviço do Active Directory](../../azure-resource-manager/resource-group-create-service-principal-portal.md). Também deve registar o ID da aplicação, a chave de autenticação e o ID do inquilino que precisa num passo posterior.

### <a name="create-the-authorization-file"></a>Criar o ficheiro de autorização

1. No Explorador de soluções, faça duplo clique *myDotnetProject* > **adicionar** > **Novo Item**e, em seguida, selecione **doficheirodetexto** no *Visual c# itens*. Nome de ficheiro *azureauth.properties*e, em seguida, clique em **adicionar**.
2. Adicione estas propriedades de autorização:

    ```
    subscription=<subscription-id>
    client=<application-id>
    key=<authentication-key>
    tenant=<tenant-id>
    managementURI=https://management.core.windows.net/
    baseURL=https://management.azure.com/
    authURL=https://login.windows.net/
    graphURL=https://graph.windows.net/
    ```

    Substitua  **&lt;id de subscrição&gt;**  com o identificador de subscrição,  **&lt;id da aplicação&gt;**  com a aplicação do Active Directory Identificador,  **&lt;chave de autenticação&gt;**  com a chave de aplicação, e  **&lt;id do inquilino&gt;**  com o identificador de inquilino.

3. Guarde o ficheiro azureauth.properties. 
4. Defina uma variável de ambiente do Windows com o nome AZURE_AUTH_LOCATION com o caminho completo do ficheiro de autorização que criou. Por exemplo, pode ser utilizado o seguinte comando do PowerShell:

    ```
    [Environment]::SetEnvironmentVariable("AZURE_AUTH_LOCATION", "C:\Visual Studio 2017\Projects\myDotnetProject\myDotnetProject\azureauth.properties", "User")
    ```

### <a name="create-the-management-client"></a>Criar o cliente de gestão

1. Abra o ficheiro Program.cs para o projeto que criou e, em seguida, adicione-os através de instruções para as instruções existentes na parte superior do ficheiro:

    ```
    using Microsoft.Azure.Management.Compute.Fluent;
    using Microsoft.Azure.Management.Compute.Fluent.Models;
    using Microsoft.Azure.Management.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent.Core;
    ```

2. Para criar o cliente de gestão, adicione este código ao método principal:

    ```
    var credentials = SdkContext.AzureCredentialsFactory
        .FromFile(Environment.GetEnvironmentVariable("AZURE_AUTH_LOCATION"));

    var azure = Azure
        .Configure()
        .WithLogLevel(HttpLoggingDelegatingHandler.Level.Basic)
        .Authenticate(credentials)
        .WithDefaultSubscription();
    ```

## <a name="create-resources"></a>Criar recursos

### <a name="create-the-resource-group"></a>Criar o grupo de recursos

Todos os recursos tem de estar contidos num [grupo de recursos](../../azure-resource-manager/resource-group-overview.md).

Para especificar valores para a aplicação e criar o grupo de recursos, adicione este código ao método principal:

```
var groupName = "myResourceGroup";
var vmName = "myVM";
var location = Region.USWest;
    
Console.WriteLine("Creating resource group...");
var resourceGroup = azure.ResourceGroups.Define(groupName)
    .WithRegion(location)
    .Create();
```

### <a name="create-the-availability-set"></a>Criar o conjunto de disponibilidade

[Conjuntos de disponibilidade](tutorial-availability-sets.md) tornar mais fácil para si manter as máquinas virtuais utilizadas pela sua aplicação.

Para criar o conjunto de disponibilidade, adicione este código ao método principal:

```
Console.WriteLine("Creating availability set...");
var availabilitySet = azure.AvailabilitySets.Define("myAVSet")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithSku(AvailabilitySetSkuTypes.Managed)
    .Create();
```

### <a name="create-the-public-ip-address"></a>Criar o endereço IP público

A [endereço IP público](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) é necessária para comunicar com a máquina virtual.

Para criar o endereço IP público para a máquina virtual, adicione este código ao método principal:
   
```
Console.WriteLine("Creating public IP address...");
var publicIPAddress = azure.PublicIPAddresses.Define("myPublicIP")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithDynamicIP()
    .Create();
```

### <a name="create-the-virtual-network"></a>Criar a rede virtual

Uma máquina virtual tem de ser uma sub-rede de um [rede Virtual](../../virtual-network/virtual-networks-overview.md).

Para criar uma sub-rede e uma rede virtual, adicione este código ao método principal:

```
Console.WriteLine("Creating virtual network...");
var network = azure.Networks.Define("myVNet")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithAddressSpace("10.0.0.0/16")
    .WithSubnet("mySubnet", "10.0.0.0/24")
    .Create();
```

### <a name="create-the-network-interface"></a>Criar a interface de rede

Uma máquina virtual necessita de uma interface de rede para comunicar na rede virtual.

Para criar uma interface de rede, adicione este código ao método principal:

```
Console.WriteLine("Creating network interface...");
var networkInterface = azure.NetworkInterfaces.Define("myNIC")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithExistingPrimaryNetwork(network)
    .WithSubnet("mySubnet")
    .WithPrimaryPrivateIPAddressDynamic()
    .WithExistingPrimaryPublicIPAddress(publicIPAddress)
    .Create();
 ```

### <a name="create-the-virtual-machine"></a>Criar a máquina virtual

Agora que criou todos os recursos de suporte, pode criar uma máquina virtual.

Para criar a máquina virtual, adicione este código ao método principal:

```
Console.WriteLine("Creating virtual machine...");
azure.VirtualMachines.Define(vmName)
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithExistingPrimaryNetworkInterface(networkInterface)
    .WithLatestWindowsImage("MicrosoftWindowsServer", "WindowsServer", "2012-R2-Datacenter")
    .WithAdminUsername("azureuser")
    .WithAdminPassword("Azure12345678")
    .WithComputerName(vmName)
    .WithExistingAvailabilitySet(availabilitySet)
    .WithSize(VirtualMachineSizeTypes.StandardDS1)
    .Create();
```

> [!NOTE]
> Este tutorial cria uma máquina virtual com uma versão do sistema operativo Windows Server. Para obter mais informações sobre como selecionar outras imagens, consulte [navegar e selecionar imagens da máquina virtual do Azure com o Windows PowerShell e a CLI do Azure](../linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
>

Se pretender utilizar um disco existente, em vez de uma imagem do marketplace, utilize este código:

```
var managedDisk = azure.Disks.Define("myosdisk")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithWindowsFromVhd("https://mystorage.blob.core.windows.net/vhds/myosdisk.vhd")
    .WithSizeInGB(128)
    .WithSku(DiskSkuTypes.PremiumLRS)
    .Create();

azure.VirtualMachines.Define("myVM")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithExistingPrimaryNetworkInterface(networkInterface)
    .WithSpecializedOSDisk(managedDisk, OperatingSystemTypes.Windows)
    .WithExistingAvailabilitySet(availabilitySet)
    .WithSize(VirtualMachineSizeTypes.StandardDS1)
    .Create();
```

## <a name="perform-management-tasks"></a>Executar tarefas de gestão

Durante o ciclo de vida de uma máquina virtual, poderá querer executar tarefas de gestão, tais como iniciar, parar ou eliminar uma máquina virtual. Além disso, pode querer criar código para automatizar tarefas repetitivas ou complexas.

Quando precisar de fazer nada, com a VM, terá de obter uma instância do mesmo:

```
var vm = azure.VirtualMachines.GetByResourceGroup(groupName, vmName);
```

### <a name="get-information-about-the-vm"></a>Obter informações sobre a VM

Para obter informações sobre a máquina virtual, adicione este código ao método principal:

```
Console.WriteLine("Getting information about the virtual machine...");
Console.WriteLine("hardwareProfile");
Console.WriteLine("   vmSize: " + vm.Size);
Console.WriteLine("storageProfile");
Console.WriteLine("  imageReference");
Console.WriteLine("    publisher: " + vm.StorageProfile.ImageReference.Publisher);
Console.WriteLine("    offer: " + vm.StorageProfile.ImageReference.Offer);
Console.WriteLine("    sku: " + vm.StorageProfile.ImageReference.Sku);
Console.WriteLine("    version: " + vm.StorageProfile.ImageReference.Version);
Console.WriteLine("  osDisk");
Console.WriteLine("    osType: " + vm.StorageProfile.OsDisk.OsType);
Console.WriteLine("    name: " + vm.StorageProfile.OsDisk.Name);
Console.WriteLine("    createOption: " + vm.StorageProfile.OsDisk.CreateOption);
Console.WriteLine("    caching: " + vm.StorageProfile.OsDisk.Caching);
Console.WriteLine("osProfile");
Console.WriteLine("  computerName: " + vm.OSProfile.ComputerName);
Console.WriteLine("  adminUsername: " + vm.OSProfile.AdminUsername);
Console.WriteLine("  provisionVMAgent: " + vm.OSProfile.WindowsConfiguration.ProvisionVMAgent.Value);
Console.WriteLine("  enableAutomaticUpdates: " + vm.OSProfile.WindowsConfiguration.EnableAutomaticUpdates.Value);
Console.WriteLine("networkProfile");
foreach (string nicId in vm.NetworkInterfaceIds)
{
    Console.WriteLine("  networkInterface id: " + nicId);
}
Console.WriteLine("vmAgent");
Console.WriteLine("  vmAgentVersion" + vm.InstanceView.VmAgent.VmAgentVersion);
Console.WriteLine("    statuses");
foreach (InstanceViewStatus stat in vm.InstanceView.VmAgent.Statuses)
{
    Console.WriteLine("    code: " + stat.Code);
    Console.WriteLine("    level: " + stat.Level);
    Console.WriteLine("    displayStatus: " + stat.DisplayStatus);
    Console.WriteLine("    message: " + stat.Message);
    Console.WriteLine("    time: " + stat.Time);
}
Console.WriteLine("disks");
foreach (DiskInstanceView disk in vm.InstanceView.Disks)
{
    Console.WriteLine("  name: " + disk.Name);
    Console.WriteLine("  statuses");
    foreach (InstanceViewStatus stat in disk.Statuses)
    {
        Console.WriteLine("    code: " + stat.Code);
        Console.WriteLine("    level: " + stat.Level);
        Console.WriteLine("    displayStatus: " + stat.DisplayStatus);
        Console.WriteLine("    time: " + stat.Time);
    }
}
Console.WriteLine("VM general status");
Console.WriteLine("  provisioningStatus: " + vm.ProvisioningState);
Console.WriteLine("  id: " + vm.Id);
Console.WriteLine("  name: " + vm.Name);
Console.WriteLine("  type: " + vm.Type);
Console.WriteLine("  location: " + vm.Region);
Console.WriteLine("VM instance status");
foreach (InstanceViewStatus stat in vm.InstanceView.Statuses)
{
    Console.WriteLine("  code: " + stat.Code);
    Console.WriteLine("  level: " + stat.Level);
    Console.WriteLine("  displayStatus: " + stat.DisplayStatus);
}
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

### <a name="stop-the-vm"></a>Parar a VM

Pode parar uma máquina virtual e manter todas as respetivas definições, mas continuam a ser-lhe cobrados-lo, ou pode parar uma máquina virtual e desalocá-lo. Quando uma máquina virtual está a ser desalocada, todos os recursos associados à mesma também são extremidades desalocadas e faturação para o mesmo.

Para parar a máquina virtual sem Desalocação-lo, adicione este código ao método principal:

```
Console.WriteLine("Stopping vm...");
vm.PowerOff();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

Se pretender desalocar a máquina virtual, altere a chamada de desligado para este código:

```
vm.Deallocate();
```

### <a name="start-the-vm"></a>Iniciar a VM

Para iniciar a máquina virtual, adicione este código ao método principal:

```
Console.WriteLine("Starting vm...");
vm.Start();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

### <a name="resize-the-vm"></a>Redimensionar a VM

Muitos aspetos de implementação devem ser considerados ao decidir sobre um tamanho para a máquina virtual. Para obter mais informações, consulte [tamanhos de VM](sizes.md).  

Para alterar o tamanho da máquina virtual, adicione este código ao método principal:

```
Console.WriteLine("Resizing vm...");
vm.Update()
    .WithSize(VirtualMachineSizeTypes.StandardDS2) 
    .Apply();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

### <a name="add-a-data-disk-to-the-vm"></a>Adicionar um disco de dados para a VM

Para adicionar um disco de dados para a máquina virtual, adicione este código para o método Main para adicionar um disco de dados é de 2 GB de tamanho, han um LUN de 0 e um tipo de colocação em cache de ReadWrite:

```
Console.WriteLine("Adding data disk to vm...");
vm.Update()
    .WithNewDataDisk(2, 0, CachingTypes.ReadWrite) 
    .Apply();
Console.WriteLine("Press enter to delete resources...");
Console.ReadLine();
```

## <a name="delete-resources"></a>Eliminar recursos

Porque os lhe é cobrados os recursos utilizados no Azure, é sempre boa prática para eliminar os recursos que já não são necessárias. Se pretender eliminar as máquinas virtuais e todos os recursos de suporte, tudo o que precisa de executar é eliminar o grupo de recursos.

Para eliminar o grupo de recursos, adicione este código ao método principal:

```
azure.ResourceGroups.DeleteByName(groupName);
```

## <a name="run-the-application"></a>Executar a aplicação

Deve demorar cerca de cinco minutos para esta aplicação de consola executar totalmente do início seja concluída. 

1. Para executar a aplicação de consola, clique em **iniciar**.

2. Antes de premir **Enter** para iniciar a eliminação de recursos, pode demorar alguns minutos para verificar a criação dos recursos no portal do Azure. Clique no estado de implementação para ver informações sobre a implementação.

## <a name="next-steps"></a>Passos seguintes
* Tirar partido da utilização de um modelo para criar uma máquina virtual, utilizando as informações no [implementar uma máquina de Virtual do Azure com c# e um modelo do Resource Manager](csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Saiba mais sobre como utilizar o [bibliotecas do Azure para .NET](https://docs.microsoft.com/dotnet/azure/?view=azure-dotnet).

