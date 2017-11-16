---
title: "Ligar um serviço em nuvem para um controlador de domínio personalizado | Microsoft Docs"
description: "Saiba como ligar o seu web/funções de trabalho a um domínio personalizado do AD com o PowerShell e a extensão de domínio do AD"
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: 1e2d7c87-d254-4e7a-a832-67f84411ec95
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: adegeo
ms.openlocfilehash: e2aadf6a103e92a4fbb11223a449280a36dea6b4
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2017
---
# <a name="connecting-azure-cloud-services-roles-to-a-custom-ad-domain-controller-hosted-in-azure"></a>Ligar a funções de serviços na nuvem do Azure como um controlador de domínio do AD alojado no Azure de personalizado
Vamos primeiro configurar uma rede Virtual (VNet) no Azure. Em seguida, iremos adicionar um controlador de domínio do Active Directory (alojado uma Máquina Virtual no Azure) para a VNet. Em seguida, iremos irá adicionar funções de serviço existente na nuvem para a VNet criada previamente, em seguida, ligue-o para o controlador de domínio.

Antes de Vamos começar a utilizar, alguns dos aspetos a ter em mente:

1. Este tutorial utiliza o PowerShell, por isso certifique-se de que tem o Azure PowerShell instalado e pronto para começar. Para obter ajuda para configurar o Azure PowerShell, consulte o artigo [como instalar e configurar o Azure PowerShell](/powershell/azure/overview).
2. As instâncias de controlador de domínio do AD e a função da Web/trabalho têm de ser na VNet.

Siga este guia passo a passo e caso se depare com problemas, deixe-num comentário no final do artigo. Alguém aceder novamente para que possa (Sim, iremos ler comentários).

A rede que é referenciada pelo serviço em nuvem tem de ser um **rede virtual clássica**.

## <a name="create-a-virtual-network"></a>Criar uma rede Virtual
Pode criar uma rede Virtual no Azure utilizando o portal do Azure ou PowerShell. Para este tutorial, iremos irá utilizar o PowerShell. Para criar uma rede Virtual com o portal do Azure, consulte [criar rede Virtual](../virtual-network/virtual-networks-create-vnet-arm-pportal.md).

```powershell
#Create Virtual Network

$vnetStr =
@"<?xml version="1.0" encoding="utf-8"?>
<NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
    <VirtualNetworkConfiguration>
    <VirtualNetworkSites>
        <VirtualNetworkSite name="[your-vnet-name]" Location="West US">
        <AddressSpace>
            <AddressPrefix>[your-address-prefix]</AddressPrefix>
        </AddressSpace>
        <Subnets>
            <Subnet name="[your-subnet-name]">
            <AddressPrefix>[your-subnet-range]</AddressPrefix>
            </Subnet>
        </Subnets>
        </VirtualNetworkSite>
    </VirtualNetworkSites>
    </VirtualNetworkConfiguration>
</NetworkConfiguration>
"@;

$vnetConfigPath = "<path-to-vnet-config>"
Set-AzureVNetConfig -ConfigurationPath $vnetConfigPath
```

## <a name="create-a-virtual-machine"></a>Criar uma Máquina Virtual
Depois de concluir ao configurar a rede Virtual, terá de criar um controlador de domínio do AD. Para este tutorial, iremos será possível configurar um controlador de domínio do AD uma Máquina Virtual no Azure.

Para tal, crie uma máquina virtual através do PowerShell através dos seguintes comandos:

```powershell
# Initialize variables
# VNet and subnet must be classic virtual network resources, not Azure Resource Manager resources.

$vnetname = '<your-vnet-name>'
$subnetname = '<your-subnet-name>'
$vmsvc1 = '<your-hosted-service>'
$vm1 = '<your-vm-name>'
$username = '<your-username>'
$password = '<your-password>'
$affgrp = '<your- affgrp>'

# Create a VM and add it to the Virtual Network

New-AzureQuickVM -Windows -ServiceName $vmsvc1 -Name $vm1 -ImageName $imgname -AdminUsername $username -Password $password -AffinityGroup $affgrp -SubnetNames $subnetname -VNetName $vnetname
```

## <a name="promote-your-virtual-machine-to-a-domain-controller"></a>Promover a Máquina Virtual para um controlador de domínio
Para configurar a Máquina Virtual como um controlador de domínio do AD, terá de iniciar sessão na VM e configurá-lo.

Para iniciar sessão VM, pode obter o ficheiro RDP através do PowerShell, utilize os seguintes comandos:

```powershell
# Get RDP file
Get-AzureRemoteDesktopFile -ServiceName $vmsvc1 -Name $vm1 -LocalPath <rdp-file-path>
```

Assim que tem sessão iniciada para a VM, configure a Máquina Virtual como um controlador de domínio do AD ao seguir o guia passo a passo [como configurar o seu controlador de domínio do AD de cliente](http://social.technet.microsoft.com/wiki/contents/articles/12370.windows-server-2012-set-up-your-first-domain-controller-step-by-step.aspx).

## <a name="add-your-cloud-service-to-the-virtual-network"></a>Adicionar o seu serviço em nuvem para a rede Virtual
Em seguida, terá de adicionar a implementação de serviço em nuvem para a nova VNet. Para tal, modifique o cscfg do serviço de nuvem, adicionando as secções relevantes para o cscfg utilizando o Visual Studio ou o editor à sua escolha.

```xml
<ServiceConfiguration serviceName="[hosted-service-name]" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="[os-family]" osVersion="*">
    <Role name="[role-name]">
    <Instances count="[number-of-instances]" />
    </Role>
    <NetworkConfiguration>

    <!--optional-->
    <Dns>
        <DnsServers><DnsServer name="[dns-server-name]" IPAddress="[ip-address]" /></DnsServers>
    </Dns>
    <!--optional-->

    <!--VNet settings
        VNet and subnet must be classic virtual network resources, not Azure Resource Manager resources.-->
    <VirtualNetworkSite name="[virtual-network-name]" />
    <AddressAssignments>
        <InstanceAddress roleName="[role-name]">
        <Subnets>
            <Subnet name="[subnet-name]" />
        </Subnets>
        </InstanceAddress>
    </AddressAssignments>
    <!--VNet settings-->

    </NetworkConfiguration>
</ServiceConfiguration>
```

Em seguida, criar o projeto de serviços de nuvem e implementá-la para o Azure. Para obter ajuda com a implementação do pacote de serviços em nuvem para o Azure, consulte [como criar e implementar um serviço em nuvem](cloud-services-how-to-create-deploy-portal.md)

## <a name="connect-your-webworker-roles-to-the-domain"></a>Ligar as funções da web/trabalho ao domínio
Depois do projeto de serviço de nuvem é implementado no Azure, ligar as instâncias de função para o domínio personalizado de AD utilizando a extensão de domínio do AD. Para adicionar a extensão de domínio do AD para a implementação de serviços em nuvem existente e associar o domínio personalizado, execute os seguintes comandos do PowerShell:

```powershell
# Initialize domain variables

$domain = '<your-domain-name>'
$dmuser = '$domain\<your-username>'
$dmpswd = '<your-domain-password>'
$dmspwd = ConvertTo-SecureString $dmpswd -AsPlainText -Force
$dmcred = New-Object System.Management.Automation.PSCredential ($dmuser, $dmspwd)

# Add AD Domain Extension to the cloud service roles

Set-AzureServiceADDomainExtension -Service <your-cloud-service-hosted-service-name> -Role <your-role-name> -Slot <staging-or-production> -DomainName $domain -Credential $dmcred -JoinOption 35
```

E já está.

Os serviços de nuvem devem ser associados ao seu controlador de domínio personalizado. Se gostaria de saber mais sobre as diferentes opções disponíveis para saber como configurar a extensão de domínio do AD, utilize a ajuda do PowerShell. Alguns exemplos seguir:

```powershell
help Set-AzureServiceADDomainExtension
help New-AzureServiceADDomainExtensionConfig
```
